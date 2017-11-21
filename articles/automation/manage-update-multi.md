---
title: "複数の Azure 仮想マシンの更新を管理する | Microsoft Docs"
description: "このトピックでは、Azure 仮想マシンの更新プログラムを管理する方法について説明します。"
services: automation
documentationcenter: 
author: eslesar
manager: carmonm
editor: 
ms.assetid: 
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/31/2017
ms.author: magoedte;eslesar
ms.openlocfilehash: f97b28d1588e959728163f7ab16d2550a79f610e
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2017
---
# <a name="manage-updates-for-multiple-machines"></a>複数のマシンの更新プログラムの管理

更新管理では、Windows マシンおよび Linux マシンの更新プログラムとパッチを管理できます。
[Azure Automation](automation-offering-get-started.md) アカウントから、マシンをすばやく更新の対象に追加し、利用可能な更新プログラムの状態を評価し、必要な更新プログラムのインストール スケジュールを設定し、デプロイの結果から更新管理が有効になっているすべての仮想マシンに更新プログラムが正常に適用されたことを確認できます。

## <a name="prerequisites"></a>前提条件

更新管理を使用するには、以下が必要です。

* Azure Automation アカウント。 Azure Automation 実行アカウントの作成手順については、「[Azure Automation の概要](automation-offering-get-started.md)」を参照してください。

* サポートされているオペレーティング システムのいずれかがインストールされている仮想マシンまたはコンピューター。

## <a name="supported-operating-systems"></a>サポートされているオペレーティング システム

更新管理は、次のオペレーティング システムでサポートされています。

### <a name="windows"></a>Windows

* Windows Server 2008 以降、および Windows Server 2008 R2 SP1 以降に対する更新プログラムのデプロイ。  Server Core と Nano Server のインストール オプションはサポートされていません。

    > [!NOTE]
    > Windows Server 2008 R2 SP1 に更新プログラムをデプロイするためには、.NET Framework 4.5 および WMF 5.0 以降が必要です。
    > 
* Windows クライアント オペレーティング システムはサポートされていません。

Windows エージェントは、Windows Server Update Services (WSUS) サーバーと通信するか Microsoft Update にアクセスできるように構成する必要があります。

> [!NOTE]
> Windows エージェントは、System Center Configuration Manager で同時に管理することはできません。
>

### <a name="linux"></a>Linux

* CentOS 6 (x86/x64) および 7 (x64)  
* Red Hat Enterprise 6 (x86/x64) および 7 (x64)  
* SUSE Linux Enterprise Server 11 (x86/x64) および 12 (x64)  
* Ubuntu 12.04 LTS 以降 (x86/x64)   

> [!NOTE]  
> Ubuntu でメンテナンス期間外に更新プログラムが適用されないようにするには、無人アップグレード パッケージを再構成して自動更新を無効にします。 構成方法については、[Ubuntu サーバー ガイドの自動更新に関するトピック](https://help.ubuntu.com/lts/serverguide/automatic-updates.html)をご覧ください。

Linux エージェントは、更新リポジトリへのアクセスが必要です。

> [!NOTE]
> このソリューションでは、OMS Agent for Linux が複数の OMS ワークスペースにレポートする構成はサポートされていません。  
>

## <a name="enable-update-management-for-azure-virtual-machines"></a>Azure 仮想マシンの更新管理を有効にする

1. Azure Portal で Automation アカウントを開きます。
2. 画面左側の **[更新管理]** を選択します。
3. 画面上部の **[Azure VM の追加]** をクリックします。
    ![VM の追加](./media/manage-update-multi/update-onboard-vm.png)
4. 利用を開始する仮想マシンを選択します。 **[更新管理の有効化]** 画面が開きます。
5. **[有効]**をクリックします。

   ![更新管理を有効にする](./media/manage-update-multi/update-enable.png)

仮想マシンに対する更新管理が有効になります。

## <a name="enable-update-management-for-non-azure-virtual-machines-and-computers"></a>Azure 以外の仮想マシンおよびコンピューターの更新管理を有効にする

Azure 以外の Windows 仮想マシンとコンピューターの更新管理を有効にする方法については、「[Windows コンピューターを Azure の Log Analytics サービスに接続する](../log-analytics/log-analytics-windows-agents.md)」を参照してください。

Azure 以外の Linux 仮想マシンとコンピューターの更新管理を有効にする方法については、「[Linux コンピューターを Operations Management Suite (OMS) に接続する](../log-analytics/log-analytics-agent-linux.md)」を参照してください。

## <a name="view-update-assessment"></a>更新の評価を表示する

**更新管理**が有効になると、**[更新管理]** 画面が表示されます。 **[不足している更新プログラム]** タブに、デプロイされていない更新プログラムの一覧が表示されます。

## <a name="data-collection"></a>データ収集

仮想マシンおよびコンピューターにインストールされたエージェントは、更新プログラムに関するデータを収集し、Azure の更新管理に送信します。

### <a name="supported-agents"></a>サポートされているエージェント

次の表は、このソリューションの接続先としてサポートされているソースとその説明です。

| 接続されているソース | サポートの有無 | 説明 |
| --- | --- | --- |
| Windows エージェント |あり |更新管理は、Windows エージェントからシステムの更新プログラムに関する情報を収集し、必要な更新プログラムのインストールを開始します。 |
| Linux エージェント |あり |更新管理は、Linux エージェントからシステムの更新プログラムに関する情報を収集し、サポート対象のディストリビューションに対して必要な更新プログラムのインストールを開始します。 |
| Operations Manager 管理グループ |あり |更新管理は、接続された管理グループ内のエージェントからシステムの更新プログラムに関する情報を収集します。 |
| Azure ストレージ アカウント |なし |Azure Storage には、システムの更新プログラムに関する情報が含まれていません。 |

### <a name="collection-frequency"></a>収集の頻度

管理対象の各 Windows コンピューターでは、1 日 2 回スキャンが実行されます。 Windows API が 15 分ごとに呼び出され、最後の更新時間のクエリによって状態が変更されたかどうかが確認されます。更新されている場合は対応スキャンが開始されます。  管理対象の各 Linux コンピューターでは、3 時間ごとにスキャンが実行されます。

ダッシュボードが管理対象コンピューターの更新されたデータを表示するのに、30 分～ 6 時間かかります。

## <a name="schedule-an-update-deployment"></a>更新プログラムのデプロイをスケジュールする

更新プログラムをインストールするには、リリース スケジュールとサービス期間に従ってデプロイをスケジュールします。
デプロイに含める更新の種類を選択できます。 たとえば、緊急更新プログラムやセキュリティ更新プログラムを追加し、更新プログラムのロールアップを除外できます。

**[更新管理]** 画面の上部にある **[更新プログラムの展開のスケジュール]** をクリックして、1 台以上の仮想マシンの新しい更新プログラムのデプロイをスケジュールします。 **[新しい更新プログラムの展開]** 画面で、次の情報を指定します。

* **名前**: 更新プログラムのデプロイを識別する一意の名前を指定します。
* **OS の種類**: [Windows] または [Linux] を選択します。
* **更新するコンピューター**: 更新する仮想マシンを選択します。

  ![更新する仮想マシンを選択する](./media/manage-update-multi/update-select-computers.png)

* **更新プログラムの分類**: 更新プログラムのデプロイによってデプロイに追加されるソフトウェアの種類を選択します。 分類の種類は次のとおりです。
  * 緊急更新プログラム
  * セキュリティ更新プログラム
  * 更新プログラムのロールアップ
  * Feature Pack
  * Service Pack
  * 定義の更新
  * ツール
  * 更新プログラム
* **スケジュール設定**: 既定の日時 (現在の時刻から 30 分後) を使用するか、別の日時を指定します。
   デプロイを 1 回行うか、定期的なスケジュールを設定するかを指定することもできます。 定期的なスケジュールを設定するには、[繰り返し] の下の [繰り返し] オプションをクリックします。

   ![更新プログラムのスケジュール設定画面](./media/manage-update-multi/update-set-schedule.png)

* **メンテナンス期間 (分)**: 更新プログラムをデプロイする期間を指定します。  これにより、定義したサービス期間内に変更が確実に実行されます。

スケジュールの構成が完了したら、**[作成]** ボタンをクリックして、状態ダッシュボードに戻ります。
**[スケジュール済み]** の表に、作成したデプロイ スケジュールが表示されていることを確認してください。

> [!WARNING]
> 再起動が必要な更新では、仮想マシンは自動的に再起動されます。

## <a name="view-results-of-an-update-deployment"></a>更新プログラムのデプロイの結果を表示する

スケジュールされたデプロイの開始後、**[更新管理]** 画面の **[更新プログラムの展開]** タブに、デプロイの状態が表示されます。
実行中の場合、状態は **[処理中]** と表示されます。 正常に完了すると、状態は **[成功]** に変わります。
デプロイ時に 1 つ以上の更新プログラムでエラーが発生した場合、状態は **[部分的に失敗]** になります。

![更新プログラムのデプロイの状態 ](./media/manage-update-multi/update-view-results.png)

完了した更新プログラムのデプロイをクリックすると、その更新プログラムのデプロイ用のダッシュボードが表示されます。

**[更新プログラムを実行した結果]** タイルに表示されるのは、仮想マシンに対する更新プログラムの合計数とデプロイ結果の概要です。
右側の表に、各更新プログラムとインストールの結果の詳細が示されます。これは、次の値のいずれかになります。

* 試行されていません: メンテナンス期間として定義した時間が十分ではなかったため、更新プログラムがインストールされませんでした。
* 成功: 更新できました。
* 失敗: 更新できませんでした。

デプロイで作成されたすべてのログ エントリを確認するには、**[すべてのログ]** をクリックします。

ターゲットの仮想マシンでの更新プログラムのデプロイを管理する Runbook のジョブ ストリームを確認するには、**[出力]** タイルをクリックします。

デプロイで発生したエラーの詳細情報を確認するには、**[エラー]** をクリックします。

ログ、出力、およびエラー情報の詳細については、[Update Management](../operations-management-suite/oms-solution-update-management.md) に関するページをご覧ください。

## <a name="next-steps"></a>次のステップ

* 更新管理の詳細については、[更新管理](../operations-management-suite/oms-solution-update-management.md)に関する記事を参照してください。
