---
title: Azure の Update Management ソリューション
description: この記事では、Azure Update Management ソリューションを使用して Windows および Linux コンピューターの更新プログラムを管理する方法について説明します。
services: automation
ms.service: automation
ms.component: update-management
author: georgewallace
ms.author: gwallace
ms.date: 06/19/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 8013a221f818b2aca8163e5f5195cae17c8a6a95
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/19/2018
ms.locfileid: "36232145"
---
# <a name="update-management-solution-in-azure"></a>Azure の Update Management ソリューション

Azure Automation の Update Management ソリューションを使用すると、Azure、オンプレミスの環境、またはその他のクラウド プロバイダーにデプロイされた Windows コンピューターと Linux コンピューターに関して、オペレーティング システムの更新プログラムを管理できます。 すべてのエージェント コンピューターで利用可能な更新プログラムの状態をすばやく評価し、サーバーに必要な更新プログラムをインストールするプロセスを管理できます。

仮想マシンの Update Management は、Azure Automation アカウントから直接有効にすることができます。 Automation アカウントから仮想マシンの Update Management を有効にする方法については、[複数の仮想マシンの更新管理](manage-update-multi.md)に関するページを参照してください。 また、Azure Portal の仮想マシン ページから単一の仮想マシンの Update Management を有効にすることもできます。 このシナリオは、[Linux](../virtual-machines/linux/tutorial-monitoring.md#enable-update-management) および [Windows](../virtual-machines/windows/tutorial-monitoring.md#enable-update-management) の仮想マシンに対して使用できます。

## <a name="solution-overview"></a>ソリューションの概要

Update Management で管理されるコンピューターでは、評価と更新プログラムの展開に次の構成を使用します。

* Windows または Linux 用の Microsoft Monitoring Agent (MMA)
* PowerShell Desired State Configuration (DSC) (Linux の場合)
* Automation Hybrid Runbook Worker
* Microsoft Update または Windows Server Update Services (WSUS) (Windows コンピューターの場合)

次の図は、動作とデータ フローの概念図です。ワークスペース内の接続された Windows Server と Linux コンピューターすべてがこのソリューションによってどのように評価され、セキュリティ更新プログラムが適用されるかを示しています。

![Update Management プロセスのフロー](media/automation-update-management/update-mgmt-updateworkflow.png)

コンピューターが更新プログラムの対応状態を確認するためにスキャンを実行した後、エージェントによって情報が Azure Log Analytics に一括転送されます。 Windows コンピューターでは、対応スキャンは既定で 12 時間ごとに実行されます。 

このスキャン スケジュールに加えて、Microsoft Monitoring Agent (MMA) の再起動後 15 分以内、更新プログラムのインストール前、および更新プログラムのインストール後に、更新プログラムの対応状態を確認するためのスキャンが開始されます。 

Linux コンピューターでは、対応スキャンは既定では 3 時間ごとに実行されます。 MMA エージェントを再起動した場合は、対応スキャンは 15 分以内に開始されます。

このソリューションは、同期先として構成されたソースに基づいて、コンピューターがどの程度最新の状態であるかをレポートします。 Windows コンピューターが WSUS にレポートするよう構成されている場合、WSUS が Microsoft Update と最後に同期したタイミングによっては、更新の結果が Microsoft Updates の示す内容と一致しない場合があります。 Linux コンピューターでも、レポート先がローカル リポジトリに構成されているか、あるいはパブリック リポジトリに構成されているかで更新結果が異なる場合があります。

> [!NOTE]
> サービスに正しく報告するためには、Update Management で、特定の URL とポートを有効にする必要があります。 これらの要件の詳細については、[Hybrid Worker のためのネットワーク計画](automation-hybrid-runbook-worker.md#network-planning)に関する記事を参照してください。

更新が必要なコンピューターへのソフトウェア更新プログラムのデプロイとインストールに、スケジュールされたデプロイを使用できます。 Windows コンピューターの場合、デプロイの範囲に含まれるのは、必須の更新プログラムのみで、*オプション*に分類されている更新プログラムは含まれません。 デプロイの範囲には、必要な更新プログラムのみが含まれています。 

スケジュールされた展開では、適用可能な更新プログラムを受け取る対象コンピューターを定義する際に、コンピューターを明示的に指定するか、特定のコンピューター セットのログ検索に基づいて[コンピューター グループ](../log-analytics/log-analytics-computer-groups.md)を選択します。 また、スケジュールを指定する際は、更新プログラムのインストールを許可する期間を承認し、指定します。 

更新プログラムは、Azure Automation の Runbook によってインストールされます。 これらの Runbook は表示できません。また、これらは構成不要です。 更新プログラムの展開を作成すると、対象に含めたコンピューターに対して、指定した時間にマスター更新 Runbook を開始するスケジュールが作成されます。 このマスター Runbook は、必要な更新プログラムのインストールを実行する子 Runbook を各エージェントで開始します。

更新プログラムの展開で指定した日時に、対象のコンピューターでデプロイが並行して実行されます。 まず、スキャンが実行され、その更新プログラムが必要であることが確認されてからインストールされます。 WSUS クライアント コンピューターの場合、更新プログラムが WSUS で承認されていないと更新プログラムの展開は失敗します。

## <a name="clients"></a>クライアント

### <a name="supported-client-types"></a>サポートされているクライアントの種類

次の表は、サポートされているオペレーティング システムの一覧です。

|オペレーティング システム  |メモ  |
|---------|---------|
|Windows Server 2008、Windows Server 2008 R2 RTM    | 更新プログラムの評価のみをサポートします。         |
|Windows Server 2008 R2 SP1 以降     |.NET Framework 4.5 以降が必要です。 ([.NET Framework のダウンロード](/dotnet/framework/install/guide-for-developers))<br/> Windows PowerShell 4.0 以降が必要です。 ([WMF 4.0 のダウンロード](https://www.microsoft.com/download/details.aspx?id=40855))<br/> より高い信頼性を保証するには Windows PowerShell 5.1 を使用することをお勧めします。  ([WMF 5.1 のダウンロード](https://www.microsoft.com/download/details.aspx?id=54616))        |
|CentOS 6 (x86/x64) および 7 (x64)      | Linux エージェントは、更新リポジトリへのアクセスが必要です。 分類に基づく修正プログラムでは、CentOS に既定では設定されていない、セキュリティ データを返すための 'yum' が必要です。         |
|Red Hat Enterprise 6 (x86/x64) および 7 (x64)     | Linux エージェントは、更新リポジトリへのアクセスが必要です。        |
|SUSE Linux Enterprise Server 11 (x86/x64) および 12 (x64)     | Linux エージェントは、更新リポジトリへのアクセスが必要です。        |
|Ubuntu 14.04 LTS および 16.04 LTS (x86/x64)      |Linux エージェントは、更新リポジトリへのアクセスが必要です。         |

### <a name="unsupported-client-types"></a>サポートされていないクライアントの種類

次の表は、サポートされていないオペレーティング システムの一覧です。

|オペレーティング システム  |メモ  |
|---------|---------|
|Windows クライアント     | (Windows 7 および Windows 10) などのクライアント オペレーティング システムはサポートされません。        |
|Windows Server 2016 Nano Server     | サポートされていません。       |

### <a name="client-requirements"></a>クライアントの要件

#### <a name="windows"></a>Windows

Windows エージェントは、Windows Server Update Services (WSUS) サーバーと通信するか Microsoft Update にアクセスできるように構成する必要があります。 System Center Configuration Manager と Update Management を統合して使用できます。 統合シナリオの詳細については、「[System Center Configuration Manager と Update Management の統合](oms-solution-updatemgmt-sccmintegration.md#configuration)」を参照してください。 [Windows エージェント](../log-analytics/log-analytics-agent-windows.md)が必要です。 Azure 仮想マシンのオンボードを実行すると、このエージェントは自動的にインストールされます。

#### <a name="linux"></a>Linux

Linux では、更新リポジトリへのアクセスが必要です。 プライベートまたはパブリックの更新リポジトリが使用できます。 このソリューションでは、Operations Management Suite (OMS) for Linux エージェントが複数の Log Analytics ワークスペースにレポートする構成はサポートされていません。

OMS エージェント for Linux をインストールして最新バージョンをダウンロードする方法の詳細については、[Operations Management Suite エージェント for Linux](https://github.com/microsoft/oms-agent-for-linux) に関するページを参照してください。 Windows 用 OMS エージェントをインストールする方法の詳細については、[Windows 用 Operations Management Suite エージェント](../log-analytics/log-analytics-windows-agent.md)に関するページを参照してください。

## <a name="permissions"></a>アクセス許可

更新プログラムの展開を作成および管理するには、特定のアクセス許可が必要です。 これらのアクセス許可の詳細については、[Update Management へのロールベースのアクセス](automation-role-based-access-control.md#update-management)に関するページをご覧ください。

## <a name="solution-components"></a>ソリューションのコンポーネント

このソリューションは、次のリソースで構成されています。 リソースは、Automation アカウントに追加されます。 これらは、直接接続しているエージェントであるか、または Operations Manager に接続された管理グループの中に存在します。

### <a name="hybrid-worker-groups"></a>ハイブリッド worker グループ

このソリューションを有効にすると、ソリューションに含まれている Runbook をサポートするために、Log Analytics ワークスペースに直接接続された Windows コンピューターが自動的に Hybrid Runbook Worker として構成されます。

ソリューションで管理されている各 Windows コンピューターは、Automation アカウントの **[システム ハイブリッド worker グループ]** として、**[ハイブリッド Worker グループ]** ページに表示されます。 ソリューションは、*Hostname FQDN_GUID* の名前付け規則を使用します。 アカウントの Runbook でこれらのグループを対象として指定することはできません。 指定すると失敗します。 これらのグループは、管理ソリューションをサポートすることのみを目的としています。

このソリューションと Hybrid Runbook Worker グループ メンバーシップの両方に同じアカウントを使用すると、Windows コンピューターを Automation アカウントの Hybrid Runbook Worker に追加して Automation Runbook をサポートすることができます。 この機能は、Hybrid Runbook Worker のバージョン 7.2.12024.0 で追加されました。

### <a name="management-packs"></a>管理パック

System Center Operations Manager 管理グループが Log Analytics ワークスペースに接続されている場合は、以下の管理パックが Operations Manager にインストールされます。 これらの管理パックは、このソリューションを追加した後、直接接続された Windows コンピューターにもインストールされます。 これらの管理パックを構成または管理する必要はありません。

* Microsoft System Center Advisor 更新プログラム評価インテリジェンス パック (Microsoft.IntelligencePacks.UpdateAssessment)
* Microsoft.IntelligencePack.UpdateAssessment.Configuration (Microsoft.IntelligencePack.UpdateAssessment.Configuration)
* 更新プログラムの展開 MP

ソリューション管理パックの更新方法の詳細については、「[Operations Manager を Log Analytics に接続する](../log-analytics/log-analytics-om-agents.md)」を参照してください。

### <a name="confirm-that-non-azure-machines-are-onboarded"></a>Azure 以外のマシンが配布準備済みであることを確認する

直接接続されたマシンが Log Analytics と通信していることを確認するには、数分経ってから、次のログ検索を実行します。

#### <a name="linux"></a>Linux

```
Heartbeat
| where OSType == "Linux" | summarize arg_max(TimeGenerated, *) by SourceComputerId | top 500000 by Computer asc | render table
```

#### <a name="windows"></a>Windows

```
Heartbeat
| where OSType == "Windows" | summarize arg_max(TimeGenerated, *) by SourceComputerId | top 500000 by Computer asc | render table
```

Windows コンピューターでは、次の情報を調べて、Log Analytics とエージェントの接続を確認できます。

1. [コントロール パネル] から **[Microsoft Monitoring Agent]** を開きます。 **[Azure Log Analytics]** タブで、エージェントに "**Microsoft Monitoring Agent は Log Analytics に正常に接続しました**" というメッセージが表示されていることを確認します。
2. Windows イベント ログを開きます。 **アプリケーションとサービス ログ\Operations Manager** に移動して、ソースの **[サービス コネクタ]** でイベント ID 3000 および 5002 を検索します。 これらのイベントは、コンピューターが Log Analytics ワークスペースに登録され、構成を受信していることを示しています。

エージェントが Log Analytics と通信できず、ファイアウォールまたはプロキシ サーバーを介してインターネットと通信するよう構成されている場合は、ファイアウォールまたはプロキシ サーバーが正しく構成されていることを確認します。 ファイアウォールまたはプロキシ サーバーが正しく構成されていることを確認する方法については、[Windows エージェントのネットワーク構成](../log-analytics/log-analytics-agent-windows.md)または [Linux エージェントのネットワーク構成](../log-analytics/log-analytics-agent-linux.md)に関する記事を参照してください。

> [!NOTE]
> Linux システムがプロキシまたは OMS ゲートウェイと通信するよう構成されており、このソリューションの配布準備を行っている場合は、次のコマンドを実行し、ファイルに対する読み取り権限を omiuser グループに付与するよう、*proxy.conf* のアクセス許可を更新してください。
>
> `sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/proxy.conf`
> `sudo chmod 644 /etc/opt/microsoft/omsagent/proxy.conf`

新しく追加された Linux エージェントは、評価が完了した後、状態が "**更新済み**" と表示されます。 このプロセスには最大で 6 時間かかります。

Operations Manager 管理グループが Log Analytics と通信していることを確認する方法については、[Operations Manager と Log Analytics の統合の検証](../log-analytics/log-analytics-om-agents.md#validate-operations-manager-integration-with-log-analytics)に関する記事を参照してください。

## <a name="data-collection"></a>データ収集

### <a name="supported-agents"></a>サポートされているエージェント

次の表では、このソリューションでサポートされている接続先ソースについて説明します。

| 接続先ソース | サポートされています | 説明 |
| --- | --- | --- |
| Windows エージェント |[はい] |ソリューションは、Windows エージェントからシステムの更新プログラムに関する情報を収集し、必要な更新プログラムのインストールを開始します。 |
| Linux エージェント |[はい] |ソリューションは、Linux エージェントからシステムの更新プログラムに関する情報を収集し、サポート対象のディストリビューションに対して必要な更新プログラムのインストールを開始します。 |
| Operations Manager 管理グループ |[はい] |ソリューションは、接続された管理グループ内のエージェントからシステムの更新プログラムに関する情報を収集します。<br/>Operations Manager エージェントから Log Analytics への直接接続は必要ありません。 データは管理グループから Log Analytics ワークスペースに転送されます。 |

### <a name="collection-frequency"></a>収集の頻度

管理対象の各 Windows コンピューターでは、1 日 2 回スキャンが実行されます。 15 分ごとに Windows API が呼び出され、最後の更新時間を照会することで、状態が変化したかどうかが確認されます。 状態が変更された場合、対応スキャンが開始されます。 

管理対象の各 Linux コンピューターでは、3 時間ごとにスキャンが実行されます。

ダッシュボードに管理対象コンピューターの更新されたデータが表示されるまでに、30 分～ 6 時間かかる場合があります。

## <a name="viewing-update-assessments"></a>更新の評価を表示する

Automation アカウントで **[Update Management]** をクリックすると、使用しているマシンの状態が表示されます。

このビューには、ご利用のマシン、不足している更新プログラム、更新プログラムの展開、およびスケジュールされている更新プログラムの展開に関する情報が表示されます。 **[対応]** 列では、マシンが評価された最終時刻を確認できます。 **[エージェントの更新の準備]** 列では、更新エージェントの正常性を確認できます。 問題がある場合は、問題を解決するために必要な手順が記載されたトラブルシューティング ドキュメントへのリンクを選択してください。

マシン、更新プログラム、または展開に関する情報を返すログ検索を実行するには、一覧から項目を選択します。 これにより、項目のクエリが選択された状態で **[ログ検索]** ページが開きます。

![Update Management の既定のビュー](media/automation-update-management/update-management-view.png)

## <a name="install-updates"></a>更新プログラムをインストールする

ご利用のワークスペースにある Linux コンピューターと Windows コンピューターのすべてで更新プログラムが評価されたら、*更新プログラムの展開*を作成して、必要な更新プログラムをインストールできます。 更新プログラムの展開とは、1 台以上のコンピューターに対して、必要な更新プログラムをスケジュールに従ってインストールすることです。 展開の範囲に含めるコンピューターまたはコンピューター グループと、展開の日時を指定します。 コンピューター グループの詳細については、[Log Analytics のコンピューター グループ](../log-analytics/log-analytics-computer-groups.md)に関するページを参照してください。

 更新プログラムの展開にコンピューター グループを含めると、スケジュールの作成時にグループ メンバーシップが 1 回だけ評価されます。 その後で加えられたグループへの変更は反映されません。 この問題を回避するには、スケジュールされた更新プログラムの展開を削除して、もう一度作成します。

> [!NOTE]
> Azure Marketplace からデプロイされた Windows 仮想マシンは、既定で Windows Update Service から自動更新を受信するように設定されています。 このソリューションまたは Windows 仮想マシンをワークスペースに追加しても、この動作は変わりません。 このソリューションで更新プログラムを能動的に管理しない場合は、既定の動作 (更新プログラムが自動的に適用される) が適用されます。

Ubuntu でメンテナンス期間外に更新プログラムが適用されないようにするには、無人アップグレード パッケージを再構成して自動更新を無効にします。 パッケージの構成方法については、[Ubuntu サーバー ガイドの自動更新に関するトピック](https://help.ubuntu.com/lts/serverguide/automatic-updates.html)をご覧ください。

Azure Marketplace から利用できるオンデマンドの Red Hat Enterprise Linux (RHEL) イメージから作成した仮想マシンは、Azure にデプロイされた [Red Hat Update Infrastructure (RHUI)](../virtual-machines/virtual-machines-linux-update-infrastructure-redhat.md) にアクセスするよう登録されています。 その他の Linux ディストリビューションは、サポートされている方法に従って、ディストリビューション オンライン ファイル リポジトリから更新する必要があります。

## <a name="view-missing-updates"></a>不足している更新プログラムを表示する

**[不足している更新プログラム]** をクリックすると、ご利用のマシンで不足している更新プログラムの一覧が表示されます。 各更新プログラムが表示され、選択することができます。 更新プログラムを必要とするマシンの数やオペレーティング システムに関する情報、および詳細情報にアクセスするためのリンクが表示されます。 **[ログ検索]** ウィンドウには更新プログラムに関する詳細情報が表示されます。

## <a name="view-update-deployments"></a>更新プログラムの展開を表示する

**[更新プログラムの展開]** タブをクリックすると、既存の更新プログラムの展開の一覧が表示されます。 表で更新プログラムの展開のいずれかをクリックすると、その更新プログラムの展開の **[更新プログラムの展開の実行]** ページが開きます。

![更新プログラムの展開結果の概要](./media/automation-update-management/update-deployment-run.png)

## <a name="create-or-edit-an-update-deployment"></a>更新プログラムを作成または編集します。

新しい更新プログラムの展開を作成するには、**[更新プログラムの展開のスケジュール]** を選択します。 **[新しい更新プログラムの展開]** ウィンドウが開きます。 次の表で説明されているプロパティの値を入力します。

| プロパティ | 説明 |
| --- | --- |
|Name |更新プログラムの展開を識別する一意の名前。 |
|オペレーティング システム| **Linux** か **Windows** を選択します。|
|更新するマシン |保存した検索条件を選択するか、ドロップダウンから **[マシン]** を選択し、個別のマシンを選択します |
|更新プログラムの分類|必要な更新プログラムの分類をすべて選択します。 CentOS ではこれは既定ではサポートされていません。|
|除外する更新プログラム|除外する更新プログラムを入力します。 Windows の場合は、KB 記事を **KB** プレフィックスを付けないで入力します。 Linux の場合は、パッケージ名を入力するか、ワイルドカード文字を使用します。  |
|スケジュール設定|開始する時刻を選択し、繰り返しの設定として、**[1 回]** または **[定期的]** のいずれかを選択します|| メンテナンス期間 |更新プログラムに対して設定された分数です。 30 分未満、あるいは 6 時間を超える値を指定することはできません。 |

## <a name="update-classifications"></a>更新プログラムの分類

次の表は、Update Management の更新プログラムの分類と、各分類の定義を示します。

### <a name="windows"></a>Windows

|分類  |説明  |
|---------|---------|
|緊急更新プログラム     | セキュリティに関連しない重大なバグを修正する、特定の問題に対する更新プログラムです。        |
|セキュリティ更新プログラム     | 製品固有のセキュリティに関連する問題に対する更新プログラムです。        |
|更新プログラムのロールアップ     | 容易なデプロイのためにパッケージにまとめられた修正プログラムの累積セットです。        |
|Feature Pack     | 製品リリース外で配布される製品の新機能です。        |
|Service Pack     | アプリケーションに適用される修正プログラムの累積セットです。        |
|定義の更新     | ウイルスまたは他の定義ファイルに対する更新プログラムです。        |
|ツール     | 1 つまたは複数のタスクを完了するのに役立つユーティリティまたは機能です。        |
|更新プログラム     | 現在インストールされているアプリケーションまたはファイルに対する更新プログラムです。        |

### <a name="linux"></a>Linux

|分類  |説明  |
|---------|---------|
|緊急更新プログラムとセキュリティ更新プログラム     | 特定の問題または製品固有のセキュリティに関連する問題に対する更新プログラムです。         |
|他の更新プログラム     | 本質的に重要ではない、またはセキュリティ更新プログラムではない、他のすべての更新プログラムです。        |

Linux の場合、クラウドでのデータ エンリッチメントにより、Update Management は、評価データを表示しながら、重要な更新プログラムとセキュリティ更新プログラムを識別できます。 修正プログラムの場合、Update Management はマシン上にある分類データを使用します。 他のディストリビューションとは異なり CentOS では、既定ではこの情報は使用できません。 CentOS マシンで、次のコマンドに対しセキュリティ データを返すように構成されている場合、Update Management は分類に基づいて修正プログラムを適用できます。

```bash
sudo yum -q --security check-update
```

CentOS 上でネイティブ分類データを使用できるようにするためのサポートされている方法はありません。 現時点では、独自の方法で分類データを使用するお客様には、できる範囲内のサポートのみを提供しています。

## <a name="ports"></a>ポート

Update Management には次のアドレスが明示的に必要です。 このアドレスへの通信は、ポート 443 を使用して行われます。

|Azure Public  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com     |*.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|*.blob.core.windows.net|*.blob.core.usgovcloudapi.net|

Hybrid Runbook Worker で必要なポートの詳細については、[ハイブリッド worker ロールのポート](automation-hybrid-runbook-worker.md#hybrid-worker-role)に関するページをご覧ください。

## <a name="search-logs"></a>検索ログ

Azure Portal で提供されている詳細情報に加え、ログに対する検索を実行できます。 **[変更の追跡]** ウィンドウで、**[Log Analytics]** を選択します。 **[ログ検索]** ウィンドウが開きます。

### <a name="sample-queries"></a>サンプル クエリ

次の表は、このソリューションによって収集された更新レコードを探すログ検索の例です。

| クエリ | 説明 |
| --- | --- |
|アップデート</br>&#124; where UpdateState == "Needed" and Optional == false</br>&#124; project Computer, Title, KBID, Classification, PublishedDate |更新プログラムがインストールされていないすべてのコンピューター</br>次のいずれかを追加して、OS を限定します。</br>OSType != "Linux"</br>OSType == "Linux" |
| アップデート</br>&#124; where UpdateState == "Needed" and Optional == false</br>&#124; where Computer == "ContosoVM1.contoso.com"</br>&#124; project Computer, Title, KBID, Product, PublishedDate |特定のコンピューターにインストールされていない更新プログラム (コンピューター名は実際の名前に置き換えてください)|
| Event</br>&#124; where EventLevelName == "error" and Computer in ((Update &#124; where (Classification == "Security Updates" or Classification == "Critical Updates")</br>&#124; where UpdateState == "Needed" and Optional == false </br>&#124; distinct Computer)) |Error events for machines that have missing critical or security required updates (必要とされている緊急更新プログラムまたはセキュリティ更新プログラムがインストールされていないコンピューターのエラー イベント) |
| アップデート</br>&#124; where UpdateState == "Needed" and Optional == false</br>&#124; distinct Title |全コンピューターのインストールされていない個別の更新プログラム |
| UpdateRunProgress</br>&#124; where InstallationStatus == "failed" </br>&#124; summarize AggregatedValue = count() by Computer, Title, UpdateRunName |更新の実行に失敗した更新プログラムがあるコンピューター</br>次のいずれかを追加して、OS を限定します。</br>OSType = "Windows"</br>OSType == "Linux" |
| アップデート</br>&#124; where OSType == "Linux"</br>&#124; where UpdateState != "Not needed" and (Classification == "Critical Updates" or Classification == "Security Updates")</br>&#124; summarize AggregatedValue = count() by Computer |重大な脆弱性またはセキュリティの脆弱性に対処するパッケージ更新プログラムが使用可能な Linux マシンの一覧 |
| UpdateRunProgress</br>&#124; where UpdateRunName == "DeploymentName"</br>&#124; summarize AggregatedValue = count() by Computer|この更新実行で更新されたコンピューター (更新プログラムの展開名は実際の名前に置き換えてください) |

## <a name="integrate-with-system-center-configuration-manager"></a>System Center Configuration Manager との統合

PC、サーバー、モバイル デバイスを管理するために System Center Configuration Manager に投資してきたお客様は、Configuration Manager の強みと成熟度を活用してソフトウェア更新プログラムを管理できます。 Configuration Manager は、そのソフトウェア更新プログラムの管理 (SUM) サイクルの一部です。

管理ソリューションを System Center Configuration Manager と統合する方法については、「[System Center Configuration Manager と Update Management の統合](oms-solution-updatemgmt-sccmintegration.md)」を参照してください。

## <a name="patch-linux-machines"></a>Linux マシンのパッチ

次のセクションでは、Linux のパッチ適用に関する潜在的な問題について説明します。

### <a name="unexpected-os-level-upgrades"></a>予期しない OS レベルのアップグレード

Red Hat Enterprise Linux など、Linux バリエーションの中には、パッケージによって OS レベルのアップグレードが発生することがあります。 これにより、OS バージョン番号の変更を伴う Update Management が実行される可能性があります。 Update Management がパッケージを更新するときに使用する方法は、管理者がローカルで Linux コンピューターに使用する方法と同じであるため、この動作は意図的なものです。

Update Management の実行による OS バージョンの更新を回避するには、**除外**機能を使用します。

Red Hat Enterprise Linux で、除外するパッケージ名は redhat-release-server.x86_64 です

![Linux の除外するパッケージ](./media/automation-update-management/linuxpatches.png)

### <a name="critical--security-patches-arent-applied"></a>重要なパッチまたはセキュリティ パッチが適用されない

更新プログラムを Linux マシンに展開する場合、更新プログラムの分類を選択できます。 これにより、更新プログラムは、指定した条件を満たすマシンにのみ適用されるようにフィルター処理されます。 このフィルターは、更新プログラムが展開されるときに、マシンに対してローカルに適用されます。

Update Management は更新プログラムの強化をクラウドで実行するため、Update Management では、一部の更新プログラムに "セキュリティへの影響あり" のフラグが設定されることがありますが、ローカル マシンにはその情報がありません。 このため、重大な更新プログラムを Linux マシンに適用する場合、そのマシンの "セキュリティへの影響あり" とマークされていない更新プログラムが存在する可能性があり、これらは適用されません。

ただし、Update Management には関連更新プログラムに関する追加情報があるため、そのマシンは引き続き "非対応" として報告されることがあります。

更新プログラムの分類ごとの更新プログラムの展開は CentOS では既定では動作しません。 SUSE で '他の更新プログラム' *のみ*を分類として選択すると、zypper (パッケージ マネージャー) に関連するセキュリティ更新プログラムもインストールされるか、またはその依存関係がまず必要になる場合があります。 これは、zypper の制限です。 場合によっては、更新プログラムのログを確認するため、更新プログラムの展開を再実行する必要があります。

## <a name="troubleshooting"></a>トラブルシューティング

このセクションでは、Update Management ソリューションに関する問題のトラブルシューティングに役立つ情報について説明します。

### <a name="windows"></a>Windows

ソリューションまたは仮想マシンを配布準備しようとして問題が発生した場合は、ローカル コンピューター上の**アプリケーションとサービス ログ\Operations Manager** イベント ログで、イベント ID 4502 のイベントと **Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent** を含むイベント メッセージを確認してください。 次の表に、具体的なエラー メッセージと、考えられる個別の解決策を示します。

| メッセージ | 理由 | 解決策 |
|----------|----------|----------|
| Unable to Register Machine for Patch Management, (更新プログラムの管理用のマシンを登録できません。)<br/>Registration Failed with Exception (登録は次の例外で失敗しました)<br/>System.InvalidOperationException: {"Message":"Machine is already<br/>registered to a different account. "} (System.InvalidOperationException: {"メッセージ": "マシンは既に別のアカウントに登録されています。) | マシンが既に Update Management 用の別のワークスペースに配布準備されています。 | [Hybrid Runbook グループを削除する](automation-hybrid-runbook-worker.md#remove-a-hybrid-worker-group)ことにより、古いアーティファクトのクリーンアップを実行します。|
| Unable to Register Machine for Patch Management, Registration Failed with Exception (更新プログラムの管理用のマシンを登録できません。登録は次の例外で失敗しました。)<br/>System.Net.Http.HttpRequestException: この要求の送信中にエラーが発生しました。 ---><br/>System.Net.WebException: 基になる接続が<br/>閉じられました。受信時に予期しないエラーが<br/>発生しました。 ---> System.ComponentModel.Win32Exception:<br/>クライアントとサーバーは共通のアルゴリズムを保持していないため<br/>通信できません。 | プロキシ/ゲートウェイ/ファイアウォールが通信をブロックしています。 | [ネットワークの要件を確認します](automation-hybrid-runbook-worker.md#network-planning)。|
| Unable to Register Machine for Patch Management, (更新プログラムの管理用のマシンを登録できません。)<br/>Registration Failed with Exception (登録は次の例外で失敗しました)<br/>Newtonsoft.Json.JsonReaderException: Error parsing positive infinity value. (Newtonsoft.Json.JsonReaderException: 正の無限大の値の解析エラー。) | プロキシ/ゲートウェイ/ファイアウォールが通信をブロックしています。 | [ネットワークの要件を確認します](automation-hybrid-runbook-worker.md#network-planning)。|
| サービス \<wsid\>.oms.opinsights.azure.com によって提示された証明書は、<br/>Microsoft サービスで使用する証明機関が<br/>発行したものではありませんでした。 連絡先<br/>TLS/SSL 通信を遮断するプロキシが実行されているかどうかを<br/>確認してください。 |プロキシ/ゲートウェイ/ファイアウォールが通信をブロックしています。 | [ネットワークの要件を確認します](automation-hybrid-runbook-worker.md#network-planning)。|
| Unable to Register Machine for Patch Management, (更新プログラムの管理用のマシンを登録できません。)<br/>Registration Failed with Exception (登録は次の例外で失敗しました)<br/>AgentService.HybridRegistration。<br/>PowerShell.Certificates.CertificateCreationException:<br/>自己署名証明書を作成できませんでした。 ---><br/>System.UnauthorizedAccessException: アクセスが拒否されました。 | 自己署名証明書の生成に失敗しました。 | システム アカウントが次のフォルダーに対する<br/>読み取りアクセスを持っていることを確認します。<br/>**C:\ProgramData\Microsoft\**<br/>** Crypto\RSA**|

### <a name="linux"></a>Linux

Linux コンピューターで更新プログラムの実行の開始が失敗した場合は、次のログ ファイルのコピーを作成し、トラブルシューティングのために保存します。

```
/var/opt/microsoft/omsagent/run/automationworker/worker.log
```

Linux 上で正常に開始した後に更新プログラムの実行中にエラーが発生した場合は、実行で影響を受けるマシンからの出力を確認します。 コンピューターのパッケージ マネージャーからの特定のエラー メッセージが見つかれば、調査して対処することができます。 Update Management で更新プログラムを展開するには、パッケージ マネージャーが正常である必要があります。

場合によっては、Update Management とパッケージの更新プログラムが干渉して、更新プログラムの展開が完了されていないことがあります。 この場合、これらのパッケージを今後の更新プログラムの実行から除外するか、それらを手動でインストールする必要があります。

修正プログラムの問題を解決できない場合は、次のログ ファイルをコピーし、トラブルシューティングのために、次の更新プログラムの展開が開始される**前に**保存します。

```
/var/opt/microsoft/omsagent/run/automationworker/omsupdatemgmt.log
```

## <a name="next-steps"></a>次の手順

使用している Windows 仮想マシンの更新プログラムの管理方法を学習するためのチュートリアルに進みます。

> [!div class="nextstepaction"]
> [Azure Windows VM の更新プログラムとパッチの管理](automation-tutorial-update-management.md)

* [Log Analytics](../log-analytics/log-analytics-log-searches.md) でログ検索を使用して、詳細な更新プログラム データを確認します。
* 緊急更新プログラムがコンピューターにインストールされていないと検出された場合、またはコンピューターで自動更新が無効になっている場合、[アラートを作成](../log-analytics/log-analytics-alerts.md)します。
