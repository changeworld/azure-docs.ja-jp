---
title: Azure の Update Management ソリューション
description: この記事では、Azure Update Management ソリューションを使用して Windows および Linux コンピューターの更新プログラムを管理する方法について説明します。
services: automation
ms.service: automation
ms.component: update-management
author: georgewallace
ms.author: gwallace
ms.date: 06/28/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: d12ce7b3416fac5de5cba9543f3bed5bcedf2012
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2018
ms.locfileid: "39115675"
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
|Windows Server 2008 R2 SP1 以降     |.NET Framework 4.5 以降が必要です。 ([.NET Framework のダウンロード](/dotnet/framework/install/guide-for-developers))<br/> Windows PowerShell 4.0 以降が必要です。 ([WMF 4.0 のダウンロード](https://www.microsoft.com/download/details.aspx?id=40855))。<br/> より高い信頼性を確保するには Windows PowerShell 5.1 を使用することをお勧めします   ([WMF 5.1 のダウンロード](https://www.microsoft.com/download/details.aspx?id=54616))。        |
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

Linux では、更新リポジトリへのアクセスが必要です。 プライベートまたはパブリックの更新リポジトリが使用できます。 Update Management と対話するには、TLS 1.1 または TLS 1.2 が必要です。 このソリューションでは、Operations Management Suite (OMS) for Linux エージェントが複数の Log Analytics ワークスペースにレポートする構成はサポートされていません。

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

> [!NOTE]
> Operations Manger エージェントを使用しているシステムを Update Management によって完全に管理できるようにするには、エージェントを Microsoft Monitoring Agent に更新する必要があります。 エージェントを更新する方法については、[Operations Manager エージェントのアップグレード方法](/system-center/scom/deploy-upgrade-agents.md)に関する記事を参照してください。

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

新しい更新プログラムの展開を作成するには、**[更新プログラムの展開のスケジュール]** を選択します。 **[新しい更新プログラムの展開]** ウィンドウが開きます。 次の表で説明されているプロパティの値を入力し、**[作成]** をクリックします。

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

例外を定義するときは、一覧に示されているアドレスを使用することをお勧めします。 [Microsoft Azure データセンターの IP 範囲](https://www.microsoft.com/download/details.aspx?id=41653)の IP アドレスをダウンロードできます。 このファイルは毎週更新され、現在デプロイされている範囲と今後変更される IP 範囲が反映されます。

## <a name="search-logs"></a>検索ログ

Azure Portal で提供されている詳細情報に加え、ログに対する検索を実行できます。 ソリューション ページで **[Log Analytics]** を選択します。 **[ログ検索]** ウィンドウが開きます。

また、[Log Analytics の検索 API のドキュメント](
https://dev.loganalytics.io/)を参照して、クエリをカスタマイズし、さまざまなクライアントから使用する方法などを学ぶこともできます。

### <a name="sample-queries"></a>サンプル クエリ

以下のセクションは、このソリューションによって収集された更新レコードのログ クエリの例です。

#### <a name="single-azure-vm-assessment-queries-windows"></a>単一の Azure VM 評価クエリ (Windows)

VMUUID 値を、クエリの対象である仮想マシンの VM GUID に置き換えます。 Log Analytics で次のクエリを実行することで、使用する必要がある VMUUID を確認できます。`Update | where Computer == "<machine name>" | summarize by Computer, VMUUID`

##### <a name="missing-updates-summary"></a>不足している更新プログラムの概要

```
Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and VMUUID=~"b08d5afa-1471-4b52-bd95-a44fea6e4ca8"
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| summarize by UpdateID, Classification
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security"
```

##### <a name="missing-updates-list"></a>不足している更新プログラム一覧

```
Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and VMUUID=~"8bf1ccc6-b6d3-4a0b-a643-23f346dfdf82"
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Title, KBID, PublishedDate, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| project-away UpdateState, Approved, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), displayName=any(Title), publishedDate=min(PublishedDate), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(UpdateID, "_", KBID), classification=Classification, InformationId=strcat("KB", KBID), InformationUrl=iff(isnotempty(KBID), strcat("https://support.microsoft.com/kb/", KBID), ""), osType=2
| sort by ClassificationWeight desc, computersCount desc, displayName asc
| extend informationLink=(iff(isnotempty(InformationId) and isnotempty(InformationUrl), toobject(strcat('{ "uri": "', InformationUrl, '", "text": "', InformationId, '", "target": "blank" }')), toobject('')))
| project-away ClassificationWeight, InformationId, InformationUrl
```

#### <a name="single-azure-vm-assessment-queries-linux"></a>単一の Azure VM 評価クエリ (Linux)

Linux のディストリビューションによっては、Azure Resource Manager に由来する VMUUID 値と、Log Analytics に格納されている値との間で[エンディアン](https://en.wikipedia.org/wiki/Endianness)が一致しない場合があります。 次のクエリは、いずれかのエンディアンでの一致をチェックします。 結果を適切に返すために、VMUUID 値を GUID のビッグエンディアン形式とリトルエンディアン形式に置き換えます。 Log Analytics で次のクエリを実行することで、使用する必要がある VMUUID を確認できます。`Update | where Computer == "<machine name>"
| summarize by Computer, VMUUID`

##### <a name="missing-updates-summary"></a>不足している更新プログラムの概要

```
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and (VMUUID=~"625686a0-6d08-4810-aae9-a089e68d4911" or VMUUID=~"a0865662-086d-1048-aae9-a089e68d4911")
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification) by Computer, SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| summarize by Product, ProductArch, Classification
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security")
```

##### <a name="missing-updates-list"></a>不足している更新プログラム一覧

```
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and (VMUUID=~"625686a0-6d08-4810-aae9-a089e68d4911" or VMUUID=~"a0865662-086d-1048-aae9-a089e68d4911")
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, BulletinUrl, BulletinID) by Computer, SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| project-away UpdateState, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(Product, "_", ProductArch), displayName=Product, productArch=ProductArch, classification=Classification, InformationId=BulletinID, InformationUrl=tostring(split(BulletinUrl, ";", 0)[0]), osType=1
| sort by ClassificationWeight desc, computersCount desc, displayName asc
| extend informationLink=(iff(isnotempty(InformationId) and isnotempty(InformationUrl), toobject(strcat('{ "uri": "', InformationUrl, '", "text": "', InformationId, '", "target": "blank" }')), toobject('')))
| project-away ClassificationWeight, InformationId, InformationUrl

```

#### <a name="multi-vm-assessment-queries"></a>マルチ VM の評価クエリ

##### <a name="computers-summary"></a>コンピューターの概要

```
Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(14h) and OSType!="Linux"
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Approved, Optional, Classification) by SourceComputerId, UpdateID
    | distinct SourceComputerId, Classification, UpdateState, Approved, Optional
    | summarize WorstMissingUpdateSeverity=max(iff(UpdateState=~"Needed" and (Optional==false or Classification has "Critical" or Classification has "Security") and Approved!=false, iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1)), 0)) by SourceComputerId
)
on SourceComputerId
| extend WorstMissingUpdateSeverity=coalesce(WorstMissingUpdateSeverity, -1)
| summarize computersBySeverity=count() by WorstMissingUpdateSeverity
| union (Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(5h) and OSType=="Linux"
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification) by SourceComputerId, Product, ProductArch
    | distinct SourceComputerId, Classification, UpdateState
    | summarize WorstMissingUpdateSeverity=max(iff(UpdateState=~"Needed", iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1)), 0)) by SourceComputerId
)
on SourceComputerId
| extend WorstMissingUpdateSeverity=coalesce(WorstMissingUpdateSeverity, -1)
| summarize computersBySeverity=count() by WorstMissingUpdateSeverity)
| summarize assessedComputersCount=sumif(computersBySeverity, WorstMissingUpdateSeverity>-1), notAssessedComputersCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==-1), computersNeedCriticalUpdatesCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==4), computersNeedSecurityUpdatesCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==2), computersNeeedOtherUpdatesCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==1), upToDateComputersCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==0)
| summarize assessedComputersCount=sum(assessedComputersCount), computersNeedCriticalUpdatesCount=sum(computersNeedCriticalUpdatesCount),  computersNeedSecurityUpdatesCount=sum(computersNeedSecurityUpdatesCount), computersNeeedOtherUpdatesCount=sum(computersNeeedOtherUpdatesCount), upToDateComputersCount=sum(upToDateComputersCount), notAssessedComputersCount=sum(notAssessedComputersCount)
| extend allComputersCount=assessedComputersCount+notAssessedComputersCount


```

##### <a name="missing-updates-summary"></a>不足している更新プログラムの概要

```
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification) by Computer, SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| summarize by Product, ProductArch, Classification
| union (Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| summarize by UpdateID, Classification )
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security"
```

##### <a name="computers-list"></a>コンピューター一覧

```
Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions, Computer, ResourceId, ComputerEnvironment, VMUUID) by SourceComputerId
| where Solutions has "updates"
| extend vmuuId=VMUUID, azureResourceId=ResourceId, osType=1, environment=iff(ComputerEnvironment=~"Azure", 1, 2), scopedToUpdatesSolution=true, lastUpdateAgentSeenTime=""
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(5h) and OSType=="Linux" and SourceComputerId in ((Heartbeat
    | where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
    | summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
    | where Solutions has "updates"
    | distinct SourceComputerId))
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Product, Computer, ComputerEnvironment) by SourceComputerId, Product, ProductArch
    | summarize Computer=any(Computer), ComputerEnvironment=any(ComputerEnvironment), missingCriticalUpdatesCount=countif(Classification has "Critical" and UpdateState=~"Needed"), missingSecurityUpdatesCount=countif(Classification has "Security" and UpdateState=~"Needed"), missingOtherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security" and UpdateState=~"Needed"), lastAssessedTime=max(TimeGenerated), lastUpdateAgentSeenTime="" by SourceComputerId
    | extend compliance=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0, 2, 1)
    | extend ComplianceOrder=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0 or missingOtherUpdatesCount > 0, 1, 3)
)
on SourceComputerId
| project id=SourceComputerId, displayName=Computer, sourceComputerId=SourceComputerId, scopedToUpdatesSolution=true, missingCriticalUpdatesCount=coalesce(missingCriticalUpdatesCount, -1), missingSecurityUpdatesCount=coalesce(missingSecurityUpdatesCount, -1), missingOtherUpdatesCount=coalesce(missingOtherUpdatesCount, -1), compliance=coalesce(compliance, 4), lastAssessedTime, lastUpdateAgentSeenTime, osType=1, environment=iff(ComputerEnvironment=~"Azure", 1, 2), ComplianceOrder=coalesce(ComplianceOrder, 2)
| union(Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions, Computer, ResourceId, ComputerEnvironment, VMUUID) by SourceComputerId
| where Solutions has "updates"
| extend vmuuId=VMUUID, azureResourceId=ResourceId, osType=2, environment=iff(ComputerEnvironment=~"Azure", 1, 2), scopedToUpdatesSolution=true, lastUpdateAgentSeenTime=""
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(14h) and OSType!="Linux" and SourceComputerId in ((Heartbeat
    | where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
    | summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
    | where Solutions has "updates"
    | distinct SourceComputerId))
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Title, Optional, Approved, Computer, ComputerEnvironment) by Computer, SourceComputerId, UpdateID
    | summarize Computer=any(Computer), ComputerEnvironment=any(ComputerEnvironment), missingCriticalUpdatesCount=countif(Classification has "Critical" and UpdateState=~"Needed" and Approved!=false), missingSecurityUpdatesCount=countif(Classification has "Security" and UpdateState=~"Needed" and Approved!=false), missingOtherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security" and UpdateState=~"Needed" and Optional==false and Approved!=false), lastAssessedTime=max(TimeGenerated), lastUpdateAgentSeenTime="" by SourceComputerId
    | extend compliance=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0, 2, 1)
    | extend ComplianceOrder=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0 or missingOtherUpdatesCount > 0, 1, 3)
)
on SourceComputerId
| project id=SourceComputerId, displayName=Computer, sourceComputerId=SourceComputerId, scopedToUpdatesSolution=true, missingCriticalUpdatesCount=coalesce(missingCriticalUpdatesCount, -1), missingSecurityUpdatesCount=coalesce(missingSecurityUpdatesCount, -1), missingOtherUpdatesCount=coalesce(missingOtherUpdatesCount, -1), compliance=coalesce(compliance, 4), lastAssessedTime, lastUpdateAgentSeenTime, osType=2, environment=iff(ComputerEnvironment=~"Azure", 1, 2), ComplianceOrder=coalesce(ComplianceOrder, 2) )
| order by ComplianceOrder asc, missingCriticalUpdatesCount desc, missingSecurityUpdatesCount desc, missingOtherUpdatesCount desc, displayName asc
| project-away ComplianceOrder
```

##### <a name="missing-updates-list"></a>不足している更新プログラム一覧

```
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, BulletinUrl, BulletinID) by SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| project-away UpdateState, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(Product, "_", ProductArch), displayName=Product, productArch=ProductArch, classification=Classification, InformationId=BulletinID, InformationUrl=tostring(split(BulletinUrl, ";", 0)[0]), osType=1
| union(Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Title, KBID, PublishedDate, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| project-away UpdateState, Approved, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), displayName=any(Title), publishedDate=min(PublishedDate), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(UpdateID, "_", KBID), classification=Classification, InformationId=strcat("KB", KBID), InformationUrl=iff(isnotempty(KBID), strcat("https://support.microsoft.com/kb/", KBID), ""), osType=2)
| sort by ClassificationWeight desc, computersCount desc, displayName asc
| extend informationLink=(iff(isnotempty(InformationId) and isnotempty(InformationUrl), toobject(strcat('{ "uri": "', InformationUrl, '", "text": "', InformationId, '", "target": "blank" }')), toobject('')))
| project-away ClassificationWeight, InformationId, InformationUrl
```

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

## <a name="troubleshoot"></a>トラブルシューティング

Update Management のトラブルシューティング方法については、[Update Management のトラブルシューティング](troubleshoot/update-management.md)に関するページを参照してください。

## <a name="next-steps"></a>次の手順

使用している Windows 仮想マシンの更新プログラムの管理方法を学習するためのチュートリアルに進みます。

> [!div class="nextstepaction"]
> [Azure Windows VM の更新プログラムとパッチの管理](automation-tutorial-update-management.md)

* [Log Analytics](../log-analytics/log-analytics-log-searches.md) でログ検索を使用して、詳細な更新プログラム データを確認します。
* 緊急更新プログラムがコンピューターにインストールされていないと検出された場合、またはコンピューターで自動更新が無効になっている場合、[アラートを作成](../log-analytics/log-analytics-alerts.md)します。
