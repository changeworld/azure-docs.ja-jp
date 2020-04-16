---
title: '[グループ ポリシーのローカル ユーザーとグループ] ポリシーの適用中に仮想マシンが応答しない'
description: この記事では、Azure Virtual Machine (VM) で起動中にポリシーを適用しているときに、読み込み画面が停止する問題を解決する手順について説明します。
services: virtual-machines-windows
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: ff113268-f5bf-4e6a-986e-63b9b0ceff20
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 04/02/2020
ms.author: v-mibufo
ms.openlocfilehash: 085880122e9a80e976cfe59686748b58aeba1922
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/02/2020
ms.locfileid: "80620811"
---
# <a name="virtual-machine-is-unresponsive-while-applying-group-policy-local-users--groups-policy"></a>[グループ ポリシーのローカル ユーザーとグループ] ポリシーの適用中に仮想マシンが応答しない

この記事では、Azure Virtual Machine (VM) で起動中にポリシーを適用しているときに、読み込み画面が停止する問題を解決する手順について説明します。

## <a name="symptom"></a>症状

[ブート診断](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics)を使用して VM のスクリーンショットを表示すると、次のメッセージが表示され、読み込み中に画面が停止していることがわかります。"*グループ ポリシーのローカル ユーザーとグループ ポリシーの適用中*"。

![代替テキスト:"グループ ポリシーのローカル ユーザーとグループ ポリシーの適用中" の読み込みが表示された画面 (Windows Server 2012)。](media/troubleshoot-vm-unresponsive-group-policy-local-users/1.png)

Windows Server 2012

![代替テキスト:"グループ ポリシーのローカル ユーザーとグループ ポリシーの適用中" の読み込みが表示された画面 (Windows Server 2012 R2)。](media/troubleshoot-vm-unresponsive-group-policy-local-users/2.png)

Windows Server 2012 R2

## <a name="cause"></a>原因

このフリーズの症状は、Windows Profile Service ダイナミックリンク ライブラリ (*profsvc.dll*) のコードの不具合が原因です。

> [!NOTE]
> この不具合は、Windows Server 2012 および Windows Server 2012 R2 にのみ適用されます。

### <a name="the-policy-in-question"></a>問題のポリシー

プロセスが完了しない適用されているポリシーを次に示します。

* *[コンピューターの構成]\[ポリシー]\[管理用テンプレート]\[システム/ユーザー プロファイル]\[システム再起動時に指定した日数を経過しているユーザー プロファイルを削除する]*

このポリシーは、次の 6 つの条件に該当する場合にのみ停止します。

* *[システム再起動時に指定した日数を経過しているユーザー プロファイルを削除する]* ポリシーが有効です。
* クリーンアップが必要な期間の要件を満たしているプロファイルがあります。
* プロファイルの削除通知用に登録されているコンポーネントがあります。
* コンポーネントから、サービスの開始、停止、情報のクエリなど、Windows のサービス コントロール マネージャー (SCM) コンポーネントからデータを取得する必要がある呼び出しを (直接または間接) 行っています。
* *[自動]* として開始するように構成されたサービスがあります。
* このサービスは、(ローカル システムなどの組み込みアカウントを使用するのではなく) ドメイン アカウントのコンテキストで実行するように設定されています。

### <a name="the-code-defect"></a>コードの不具合

このコードの不具合は、サービス コントロール マネージャー (SCM) サービスとプロファイル サービスがロックを同時に適用しようとしたことが原因です。 複数のサービスが同時に同じデータに変更を加えることによる破損を防ぐために、ロックが存在します。 通常、複数のロック要求で問題が発生することはありません。 ただし、これは起動中に発生し、どちらのサービスも相互に待機しているため、プロセスを完了できません。

### <a name="os-bug-5880648---service-control-manager-deadlocks-with-the-delete-user-profiles-on-restart-policy"></a>OS バグ 5880648 - [Delete user profiles on restart]\(再起動時にユーザー プロファイルを削除する\) ポリシーでサービス コントロール マネージャーがデッドロックする

重複する 2 つのアクションがあり、次のようになります。

* アクション 1 はプロファイルのロックを獲得していますが、まだ SCM のロックを獲得していません。

  **AND**

* アクション 2 は SCM のロックを獲得していますが、まだプロファイルのロックを獲得していません。

このデッドロックが発生すると、2 つ目の必要なロックを獲得しようとすると、アクションがハングします。

### <a name="action-1---old-profile-deletion-notification-has-profile-lock-needs-scm-lock"></a>アクション 1 - 古いプロファイルの削除通知 (**プロファイルのロック**あり、**SCM のロック**が必要)

1. まず、古いプロファイルを削除するように設定されたポリシーによって、内部プロファイル サービス ロックが獲得されます。

   * "*削除操作*" の進行中に 2 つのスレッドがプロファイルと対話しないようにするためにこのロックが存在します。

2. このポリシーにより、削除できる古さのプロファイルが検索されます。
3. プロファイルの削除の一環として、プロファイルの削除の通知用に登録されたコンポーネントにより、**サービスの開始**が試行されます。
4. サービスを開始する前に、サービス コントロール マネージャー (SCM) では、**アクション 2** のスレッドによって保持されている**内部 SCM のロック**を獲得する必要があります。

### <a name="action-2---profile-loadcreation-for-user-specific-data-has-scm-lock-needs-profile-lock"></a>アクション 2 - ユーザー固有データのプロファイルの読み込みおよび作成 (**SCM のロック**あり、**プロファイルのロック**が必要)

1. 起動時に、SCM では、すべての "*自動開始*" サービスをグループごとに並べ替え、それらのサービスが依存しているサービスもすべて並べ替える必要があります。

2. SCM では、サービスを並べ替えるときに、サービスの開始、停止、または構成を行うアクセス権を制御するために使用される**内部 SCM ロックを獲得します**。

3. サービスが順番に実行されると、SCM によって各サービスがループされ、開始されます。

4. サービスがドメイン アカウントのコンテキストで実行されている場合、ユーザー固有のデータを格納できるように、ドメイン アカウント用にプロファイルを読み込むか作成する必要があります。

5. この要求は、**プロファイル サービス**に送信されます。

6. プロファイル サービスから、**アクション 1** で獲得した**内部ロック**にアクセスする必要があります。

## <a name="solution"></a>解決策

### <a name="process-overview"></a>プロセスの概要

1. 修復 VM を作成してアクセスする
2. シリアル コンソールとメモリ ダンプの収集を有効にする
3. VM を再構築する
4. メモリ ダンプ ファイルを収集する

   > [!NOTE]
   > このブート エラーが発生する場合、ゲスト OS は動作しなくなります。 この問題を解決するには、オフライン モードでトラブルシューティングを行います。

### <a name="create-and-access-a-repair-vm"></a>修復 VM を作成してアクセスする

1. [VM 修復コマンドの手順 1 から 3](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) を使用して、修復 VM を準備します。
2. リモート デスクトップ接続を使用し、修復 VM に接続します。

### <a name="enable-serial-console-and-memory-dump-collection"></a>シリアル コンソールとメモリ ダンプの収集を有効にする

メモリ ダンプ コレクションとシリアル コンソールを有効にするには、次のスクリプトを実行します。

1. 管理者特権でコマンド プロンプト セッション (管理者として実行) を開きます。
2. 次のコマンドを実行します。

   * シリアル コンソールを有効にする:

     `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON`

     `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

3. OS ディスクの空き領域が、VM のメモリ サイズ (RAM) の容量と同じであることを確認します。

   * OS ディスクに十分な領域がない場合、メモリ ダンプ ファイルが作成される場所を変更し、その場所で十分な空き領域がある VM に接続された任意のデータ ディスクを参照する必要があります。 場所を変更するには、以下のコマンドで `%SystemRoot%` をデータ ディスクのドライブ文字 ("F:" など) に置き換えます。

#### <a name="suggested-configuration-to-enable-os-dump"></a>OS ダンプを有効にするための推奨構成

**破損した OS ディスクのロード:**

`REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM`

**ControlSet001 での有効化:**

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f`

**ControlSet002 での有効化:**

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f`

### <a name="rebuild-the-vm"></a>VM を再構築する

[VM 修復コマンドの手順 5](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) を使用して、VM を再構成します。

### <a name="collect-the-memory-dump-file"></a>メモリ ダンプ ファイルを収集する

この問題を解決するには、まずクラッシュに関するメモリ ダンプ ファイルを収集し、サポートに連絡してメモリ ダンプ ファイルを渡す必要があります。 ダンプ ファイルを収集するには、次の手順を行います。

#### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>新しい修復 VM に OS ディスクを接続する

1. [VM 修復コマンドの手順 1 から 3](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) を使用して、新しい修復 VM を準備します。

2. リモート デスクトップ接続を使用し、修復 VM に接続します。

#### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>ダンプ ファイルを探してサポート チケットを送信する

1. 復旧 VM で、接続されている OS ディスクの Windows フォルダーに移動します。 接続されている OS ディスクに割り当てられているドライブ文字が F の場合は、F:\Windows に移動する必要があります。

2. memory.dmp ファイルを検索し、メモリ ダンプ ファイルと共に[サポート チケットを送信](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)します。

3. memory.dmp ファイルが見つからない場合は、代わりに[シリアル コンソールでマスク不可能割り込み (NMI) 呼び出し](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-windows#use-the-serial-console-for-nmi-calls)を使用することをお勧めします。 このガイドに従い、NMI 呼び出しを使用して[カーネルまたは完全なクラッシュ ダンプ](https://docs.microsoft.com/windows/client-management/generate-kernel-or-complete-crash-dump) ファイルを生成できます。
