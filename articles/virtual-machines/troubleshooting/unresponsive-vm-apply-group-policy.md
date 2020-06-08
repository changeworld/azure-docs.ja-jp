---
title: ポリシーの適用中に Azure 仮想マシンが応答しない
description: この記事では、Azure VM での起動中にポリシーを適用しているときに、読み込み画面が停止する問題を解決する手順について説明します。
services: virtual-machines-windows
documentationcenter: ''
author: TobyTu
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: a97393c3-351d-4324-867d-9329e31b5628
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 05/07/2020
ms.author: v-mibufo
ms.openlocfilehash: 30f833bc49f92dcabfc75f0a1507c6f540bdea24
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/21/2020
ms.locfileid: "83748731"
---
# <a name="vm-becomes-unresponsive-while-applying-group-policy-local-users--groups-policy"></a>[グループ ポリシーのローカル ユーザーとグループ] ポリシーの適用中に VM が応答しない

この記事では、Azure VM での起動中にポリシーを適用しているときに、読み込み画面が停止する問題を解決する手順について説明します。

## <a name="symptoms"></a>現象

[ブート診断](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics)を使用して VM のスクリーンショットを表示すると、読み込み中に画面が停止して次のメッセージが表示されます: ’*グループ ポリシーのローカル ユーザーとグループ ポリシーの適用中*’。

:::image type="content" source="media//unresponsive-vm-apply-group-policy/applying-group-policy-1.png" alt-text=""グループ ポリシーのローカル ユーザーとグループ ポリシーの適用中" の読み込みの画面 (Windows Server 2012 R2)。":::

:::image type="content" source="media/unresponsive-vm-apply-group-policy/applying-group-policy-2.png" alt-text=""グループ ポリシーのローカル ユーザーとグループ ポリシーの適用中" の読み込みの画面 (Windows Server 2012)。":::

## <a name="cause"></a>原因

ポリシーによって古いユーザー プロファイルのクリーンアップが行われるときに、ロックの競合が発生しています。

> [!NOTE]
> これは、Windows Server 2012 および Windows Server 2012 R2 にのみ当てはまります。

問題のあるポリシーを次に示します。

`Computer Configuration\Policies\Administrative Templates\System/User Profiles\Delete user profiles older than a specified number of days on system restart`

## <a name="resolution"></a>解像度

### <a name="process-overview"></a>プロセスの概要

1. [修復 VM を作成してアクセスする](#step-1-create-and-access-a-repair-vm)
2. [ポリシーを無効にする](#step-2-disable-the-policy)
3. [シリアル コンソールとメモリ ダンプの収集を有効にする](#step-3-enable-serial-console-and-memory-dump-collection)
4. [VM を再構築する](#step-4-rebuild-the-vm)

> [!NOTE]
> このブート エラーが発生すると、ゲスト OS は動作しなくなります。 オフライン モードでトラブルシューティングを行う必要があります。

### <a name="step-1-create-and-access-a-repair-vm"></a>手順 1:修復 VM を作成してアクセスする

1. [仮想マシンの修復コマンドの手順 1 から 3](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) を使用して、修復 VM を準備します。
2. リモート デスクトップ接続を使用して、修復 VM に接続します。

### <a name="step-2-disable-the-policy"></a>手順 2:ポリシーを無効にする

1. 修復 VM で、レジストリ エディターを開きます。
2. **HKEY_LOCAL_MACHINE** キーを見つけ、メニューから **[ファイル]**  >  **[ハイブの読み込み]** の順に選択します

    :::image type="content" source="media/unresponsive-vm-apply-group-policy/registry.png" alt-text="強調表示された HKEY_LOCAL_MACHINE と [ハイブの読み込み] が表示されているメニューのスクリーンショット。":::

    - [ハイブの読み込み] を実行すると、オフライン システムからレジストリ キーを読み込むことができます。この場合は、修復 VM に接続されている破損したディスクです。
    - システム全体の設定は `HKEY_LOCAL_MACHINE` に格納され、"HKLM" と略記できます。
3. 接続されたディスクで、`\windows\system32\config\SOFTWARE` ファイルにアクセスして開きます。

    1. 名前を入力するように求められます。 「BROKENSOFTWARE」と入力します。<br/>
    2. BROKENSOFTWARE が読み込まれたことを確認するには、**HKEY_LOCAL_MACHINE** を展開し、追加した BROKENSOFTWARE キーを探します。
4. BROKENSOFTWARE に移動し、読み込まれたハイブに CleanupProfile キーが存在するかどうかを確認します。

    1. キーが存在する場合は、CleanupProfile ポリシーが設定されています。その値は保持ポリシーを日数で表します。 キーの削除を続行します。<br/>
    2. キーが存在しない場合、CleanupProfile ポリシーは設定されていません。 接続されている OS ディスクの Windows ディレクトリにある memory.dmp ファイルを含めて、[サポート チケットを送信します](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。

5. 次のコマンドを使用して、CleanupProfiles キーを削除します。

    ```
    reg delete "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows\System" /v CleanupProfiles /f
    ```
6.  次のコマンドを使用して、BROKENSOFTWARE ハイブをアンロードします。

    ```
    reg unload HKLM\BROKENSOFTWARE
    ```

### <a name="step-3-enable-serial-console-and-memory-dump-collection"></a>手順 3:シリアル コンソールとメモリ ダンプの収集を有効にする

メモリ ダンプの収集とシリアル コンソールを有効にするには、このスクリプトを実行します。

1. 管理者特権でコマンド プロンプト セッション (管理者として実行) を開きます。
2. 次の各コマンドを実行します。

    **シリアル コンソールを有効にする**: 
    
    ```
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
    ```

    ```
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200 
    ```
3. OS ディスクの空き領域が、VM のメモリ サイズ (RAM) 以上であることを確認します。

    OS ディスクに十分な空き領域がない場合には、メモリ ダンプの場所を変更し、接続されている十分な空き容量があるデータ ディスクを参照するようにしてください。 場所を変更するには、"%SystemRoot%" をデータ ディスクのドライブ文字 (たとえば、"F:") に、次のコマンドで置き換えます。

    **OS ダンプを有効にするための推奨構成**

    Load Broken OS Disk: (破損した OS ディスクを読み込む)

    ```
    REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM
    ```

    Enable on ControlSet001: (ControlSet001 で有効にする)
    
    ```
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f 
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f 
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f 
    ```

    Enable on ControlSet002: (ControlSet002 で有効にする)
    
    ```
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f 
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f 
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f 
    ```
    
    Unload broken OS disk: (破損した OS ディスクをアンロードする)
    
    ```
    REG UNLOAD HKLM\BROKENSYSTEM
    ```

### <a name="step-4-rebuild-the-vm"></a>手順 4:VM を再構築する

[VM 修復コマンドの手順 5](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) を使用して、VM を再構成します。

問題が解決された場合、ポリシーはローカルで無効になっています。 永続的な解決策としては、VM で CleanupProfiles ポリシーを使用しないでください。 別の方法を使用してプロファイルのクリーンアップを実行してください。

このポリシーは使用しないでください:

`Machine\Admin Templates\System\User Profiles\Delete user profiles older than a specified number of days on system restart`

## <a name="next-steps"></a>次のステップ

Windows Update の適用時に問題が発生した場合は、「[Windows Update の適用時に、Azure VM が C01A001D エラーで応答しません。](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/unresponsive-vm-apply-windows-update)」を参照してください。