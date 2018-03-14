---
title: "Azure の Update Management ソリューション | Microsoft Docs"
description: "この記事の目的は、このソリューションを使用して Windows コンピューターと Linux コンピューターの更新プログラムを管理する方法の理解を助けることです。"
services: automation
documentationcenter: 
author: georgewallace
manager: carmonm
editor: 
ms.assetid: e33ce6f9-d9b0-4a03-b94e-8ddedcc595d2
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2018
ms.author: gwallace
ms.openlocfilehash: 9280925cdd5cccf8d1d2f2b33a7de8523a07cd14
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2018
---
# <a name="update-management-solution-in-azure"></a>Azure の Update Management ソリューション

Azure Automation の Update Management ソリューションを使用すると、Azure、オンプレミスの環境、またはその他のクラウド プロバイダーにデプロイされた Windows コンピューターと Linux コンピューターに関して、オペレーティング システムのセキュリティ更新プログラムを管理できます。 すべてのエージェント コンピューターで利用可能な更新プログラムの状態をすばやく評価し、サーバーに必要な更新プログラムをインストールするプロセスを管理できます。

仮想マシンの更新管理は、[Azure Automation](automation-offering-get-started.md) アカウントから直接有効にすることができます。
Automation アカウントから仮想マシンの更新管理を有効にする方法については、[多数の仮想マシンの更新管理](manage-update-multi.md)に関するページを参照してください。

## <a name="solution-overview"></a>ソリューションの概要

Update Management で管理されるコンピューターでは、評価と更新プログラムの展開に次の構成を使用します。

* Windows または Linux 用の Microsoft Monitoring Agent
* PowerShell Desired State Configuration (DSC) (Linux の場合)
* Automation Hybrid Runbook Worker
* Microsoft Update または Windows Server Update Services (Windows コンピューターの場合)

次の図は、動作とデータ フローの概念図です。ワークスペース内の接続された Windows Server と Linux コンピューターすべてがこのソリューションによってどのように評価され、セキュリティ更新プログラムが適用されるかを示しています。    

![Update Management プロセスのフロー](media/automation-update-management/update-mgmt-updateworkflow.png)

コンピューターが更新プログラムの対応状態を確認するためにスキャンを実行した後、エージェントによって情報が Log Analytics に一括して転送されます。 Windows コンピューターでは、対応スキャンは既定で 12 時間ごとに実行されます。 このスキャン スケジュールに加えて、Microsoft Monitoring Agent (MMA) の再起動後 15 分以内、更新プログラムのインストール前、および更新プログラムのインストール後に、更新プログラムの対応状態を確認するためのスキャンが開始されます。 Linux コンピューターでは、対応スキャンは既定で 3 時間ごとに実行されます。また、MMA エージェントが再起動された場合も 15 分以内に対応スキャンが開始されます。

このソリューションは、同期先として構成されたソースに基づいて、コンピューターがどの程度最新の状態であるかをレポートします。 Windows コンピューターが WSUS にレポートするよう構成されている場合、WSUS が Microsoft Update と最後に同期したタイミングによっては、更新の結果が Microsoft Updates の示す内容と一致しない場合があります。 Linux コンピューターでも、レポート先の構成がローカル リポジトリとパブリック リポジトリとで更新結果が異なる場合があります。

更新が必要なコンピューターへのソフトウェア更新プログラムのデプロイとインストールに、スケジュールされたデプロイを使用できます。 Windows コンピューターの場合、デプロイの範囲に含まれるのは、必須の更新プログラムのみで、"*オプション*" に分類されている更新プログラムは含まれません。 スケジュールされた展開では、適用可能な更新プログラムを受け取る対象コンピューターを定義する際に、コンピューターを明示的に指定するか、特定のコンピューター セットのログ検索に基づく[コンピューター グループ](../log-analytics/log-analytics-computer-groups.md)を選択します。 また、スケジュールを指定する際は、更新プログラムのインストールを許可する期間を承認し、指定します。 更新プログラムは、Azure Automation の Runbook によってインストールされます。 これらの Runbook は表示できません。また、これらは構成不要です。 "更新プログラムの展開" を作成すると、対象に含めたコンピューターに対して、指定した時間にマスター更新 Runbook を開始するスケジュールが作成されます。 このマスター Runbook は、必要な更新プログラムのインストールを実行する子 Runbook を各エージェントで開始します。

更新プログラムの展開で指定した日時に、対象のコンピューターでデプロイが並行して実行されます。 まず、スキャンが実行され、その更新プログラムが必須であることが確認されてからインストールされます。 WSUS クライアント コンピューターの場合、更新プログラムが WSUS で承認されていないと更新プログラムの展開は失敗します。

## <a name="clients"></a>クライアント

### <a name="supported-client-types"></a>サポートされているクライアントの種類

次の表は、サポートされているオペレーティング システムの一覧です。 

|オペレーティング システム  |メモ  |
|---------|---------|
|Windows Server 2008、Windows Server 2008 R2 RTM    | 更新プログラムの評価のみをサポートします         |
|Windows Server 2008 R2 SP1 以上     |Windows Server 2008 R2 SP1 については、.NET Framework 4.5 および WMF 5.0 以降が必要です        |
|CentOS 6 (x86/x64) および 7 (x64)      | Linux エージェントは、更新リポジトリへのアクセスが必要です。        |
|Red Hat Enterprise 6 (x86/x64) および 7 (x64)     | Linux エージェントは、更新リポジトリへのアクセスが必要です。        |
|SUSE Linux Enterprise Server 11 (x86/x64) および 12 (x64)     | Linux エージェントは、更新リポジトリへのアクセスが必要です。        |
|Ubuntu 12.04 LTS 以降 (x86/x64)       |Linux エージェントは、更新リポジトリへのアクセスが必要です。         |

### <a name="unsupported-client-types"></a>サポートされていないクライアントの種類

次の表は、サポートされていないオペレーティング システムの一覧です。

|オペレーティング システム  |メモ  |
|---------|---------|
|Windows クライアント     | クライアント オペレーティング システム (Windows 7、Windows 10 など) はサポートされていません。        |
|Windows Server 2016 Nano Server     | サポートされていません       |

### <a name="client-requirements"></a>クライアントの要件

#### <a name="windows"></a>Windows

Windows エージェントは、Windows Server Update Services (WSUS) サーバーと通信するか Microsoft Update にアクセスできるように構成する必要があります。 また、Windows エージェントは、System Center Configuration Manager で同時に管理することはできません。 [Windows エージェント](../log-analytics/log-analytics-agent-windows.md)が必要です。 Azure VM の配布準備を行う場合、このエージェントは自動的にインストールされます。

#### <a name="linux"></a>Linux

Linux の場合、マシンは、プライベートまたはパブリックの更新リポジトリにアクセスする必要があります。 このソリューションでは、OMS エージェント for Linux が複数の Log Analytics ワークスペースにレポートする構成はサポートされていません。

OMS エージェント for Linux をインストールして最新バージョンをダウンロードする方法の詳細については、[Operations Management Suite エージェント for Linux](https://github.com/microsoft/oms-agent-for-linux) に関するページを参照してください。 Windows 用 OMS エージェントをインストールする方法の詳細については、[Windows 用 Operations Management Suite エージェント](../log-analytics/log-analytics-windows-agent.md)に関するページを参照してください。  

## <a name="permissions"></a>アクセス許可
更新プログラムの展開を作成および管理するには、特定のアクセス許可が必要です。 これらのアクセス許可の詳細については、[ロールベースのアクセス - Update Management](automation-role-based-access-control.md#update-management) に関するページをご覧ください 

## <a name="solution-components"></a>ソリューションのコンポーネント
このソリューションは以下のリソースで構成されています。これらのリソースは、Automation アカウントに追加され、エージェントに直接接続されるか、Operations Manager に接続された管理グループに接続されます。

### <a name="hybrid-worker-groups"></a>ハイブリッド worker グループ
このソリューションを有効にすると、ソリューションに含まれている Runbook をサポートするために、Log Analytics ワークスペースに直接接続された Windows コンピューターが自動的に Hybrid Runbook Worker として構成されます。 ソリューションで管理されている各 Windows コンピューターは、<*ホスト名 FQDN_GUID*> という命名規則に従って、[ハイブリッド worker グループ] ページに、Automation アカウントのシステム ハイブリッド worker グループとして表示されます。 アカウントの Runbook でこれらのグループを対象として指定することはできません。指定すると失敗します。 これらのグループは、管理ソリューションをサポートすることのみを目的としています。

ただし、このソリューションと Hybrid Runbook Worker グループ メンバーシップの両方に同じアカウントを使用すれば、Windows コンピューターを Automation アカウントの Hybrid Runbook Worker に追加して Automation Runbook をサポートすることができます。 この機能は、Hybrid Runbook Worker のバージョン 7.2.12024.0 に追加されました。

### <a name="management-packs"></a>管理パック

System Center Operations Manager 管理グループが Log Analytics ワークスペースに接続されている場合は、以下の管理パックが Operations Manager にインストールされます。 これらの管理パックは、このソリューションを追加した後、直接接続された Windows コンピューターにもインストールされます。 管理パックに伴って構成や管理が必要となるものはありません。

* Microsoft System Center Advisor 更新プログラム評価インテリジェンス パック (Microsoft.IntelligencePacks.UpdateAssessment)
* Microsoft.IntelligencePack.UpdateAssessment.Configuration (Microsoft.IntelligencePack.UpdateAssessment.Configuration)
* 更新プログラムの展開 MP

ソリューション管理パックの更新方法の詳細については、「 [Operations Manager を Log Analytics に接続する](../log-analytics/log-analytics-om-agents.md)」を参照してください。

### <a name="confirm-non-azure-machines-are-onboarded"></a>Azure 以外のマシンが配布準備済みであることを確認する

直接接続されたマシンが Log Analytics と通信していることを確認するには、数分経ってから、次のログ検索を実行します。

#### <a name="linux"></a>Linux

```
Heartbeat
| where OSType == "Linux" | summarize arg_max(TimeGenerated, *) by SourceComputerId | top 500000 by Computer asc | render table
```

#### <a name="windows"></a>Windows

```
Heartbeat
| where OSType == "Windows" | summarize arg_max(TimeGenerated, *) by SourceComputerId | top 500000 by Computer asc | render table`
```

Windows コンピューターでは、次の内容を調べて、Log Analytics とエージェントの接続を確認できます。

1.  コントロール パネルで [Microsoft Monitoring Agent] を開き、**[Azure Log Analytics (OMS)]** タブで、エージェントに "**Microsoft Monitoring Agent は Microsoft Operations Management Suite サービスに正常に接続しました**" というメッセージが表示されていることを確認します。   
2.  Windows イベント ログを開き、**アプリケーションとサービス ログ\Operations Manager** に移動して、ソースのサービス コネクタでイベント ID 3000 および 5002 を検索します。 これらのイベントは、コンピューターが Log Analytics ワークスペースに登録され、構成を受信していることを示しています。  

エージェントが Log Analytics と通信できない場合、ファイアウォールまたはプロキシ サーバーを介してインターネットと通信するよう構成されているのであれば、[Windows エージェントのネットワーク構成](../log-analytics/log-analytics-agent-windows.md)または [Linux エージェントのネットワーク構成](../log-analytics/log-analytics-agent-linux.md)に関するページを参照して、ファイアウォールまたはプロキシ サーバーが正しく構成されていることを確認します。

> [!NOTE]
> Linux システムがプロキシまたは OMS ゲートウェイと通信するよう構成されており、このソリューションの配布準備を行っている場合は、次のコマンドを実行し、ファイルに対する読み取り権限を omiuser グループに付与するよう、*proxy.conf* のアクセス許可を更新してください。  
> `sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/proxy.conf`  
> `sudo chmod 644 /etc/opt/microsoft/omsagent/proxy.conf`

新しく追加された Linux エージェントは、評価が完了した後、状態が "**更新済み**" と表示されます。 このプロセスには最大で 6 時間かかります。

Operations Manager 管理グループが Log Analytics と通信していることを確認する方法については、「[Operations Manager と OMS の統合を検証する](../log-analytics/log-analytics-om-agents.md#validate-operations-manager-integration-with-oms)」を参照してください。

## <a name="data-collection"></a>データ収集

### <a name="supported-agents"></a>サポートされているエージェント
次の表は、このソリューションの接続先としてサポートされているソースとその説明です。

| 接続先ソース | サポートされています | [説明] |
| --- | --- | --- |
| Windows エージェント |[はい] |ソリューションは、Windows エージェントからシステムの更新プログラムに関する情報を収集し、必要な更新プログラムのインストールを開始します。 |
| Linux エージェント |[はい] |ソリューションは、Linux エージェントからシステムの更新プログラムに関する情報を収集し、サポート対象のディストリビューションに対して必要な更新プログラムのインストールを開始します。 |
| Operations Manager 管理グループ |[はい] |ソリューションは、接続された管理グループ内のエージェントからシステムの更新プログラムに関する情報を収集します。<br>Operations Manager エージェントから Log Analytics への直接接続は必要ありません。 データは管理グループから Log Analytics ワークスペースに転送されます。 |

### <a name="collection-frequency"></a>収集の頻度
管理対象の各 Windows コンピューターでは、1 日 2 回スキャンが実行されます。 Windows API が 15 分ごとに呼び出され、最後の更新時間のクエリによって状態が変更されたかどうかが確認されます。更新されている場合は対応スキャンが開始されます。 管理対象の各 Linux コンピューターでは、3 時間ごとにスキャンが実行されます。

ダッシュボードが管理対象コンピューターの更新されたデータを表示するのに、30 分～ 6 時間かかります。   

## <a name="viewing-update-assessments"></a>更新プログラムの評価の表示
Automation アカウントで **[更新の管理]** をクリックすると、使用しているマシンの状態が表示されます。

このビューには、ご利用のマシン、不足している更新プログラム、更新プログラムの展開、およびスケジュールされている更新プログラムの展開に関する情報が表示されます。

マシン、更新プログラム、または展開に関する情報を返すログ検索を実行するには、一覧から項目を選択します。 これにより、項目のクエリが選択された状態で **[ログ検索]** ページが開きます。

## <a name="installing-updates"></a>更新プログラムのインストール

ご利用のワークスペースにある Linux コンピューターと Windows コンピューターのすべてで更新プログラムが評価されたら、"*更新プログラムの展開*" を作成して、必要な更新プログラムがインストールされるようにすることができます。 "更新プログラムの展開" とは、1 台以上のコンピューターに対して、必要な更新プログラムをスケジュールに従ってインストールすることです。 デプロイの範囲に含めるコンピューターまたはコンピューター グループと、デプロイの日時を指定します。 コンピューター グループの詳細については、[Log Analytics のコンピューター グループ](../log-analytics/log-analytics-computer-groups.md)に関するページを参照してください。 更新プログラムの展開にコンピューター グループを含めると、スケジュールの作成時にグループ メンバーシップが 1 回だけ評価されます。 その後で加えられたグループへの変更は反映されません。 この問題を回避するには、スケジュールされた更新プログラムの展開を削除して、もう一度作成します。

> [!NOTE]
> Azure Marketplace からデプロイされた Windows VM は、既定で Windows Update Service から自動更新を受信するように設定されています。 このソリューションまたは Windows VM をワークスペースに追加した後も、この動作は変更されません。 このソリューションで更新プログラムを能動的に管理しない場合は、既定の動作 (更新プログラムが自動的に適用される) が適用されます。

Ubuntu でメンテナンス期間外に更新プログラムが適用されないようにするには、無人アップグレード パッケージを再構成して自動更新を無効にします。 構成方法については、[Ubuntu サーバー ガイドの自動更新に関するトピック](https://help.ubuntu.com/lts/serverguide/automatic-updates.html)をご覧ください。

Azure Marketplace から利用できるオンデマンドの Red Hat Enterprise Linux (RHEL) イメージから作成した仮想マシンは、Azure にデプロイされた [Red Hat Update Infrastructure (RHUI)](../virtual-machines/virtual-machines-linux-update-infrastructure-redhat.md) にアクセスするよう登録されています。 その他の Linux ディストリビューションは、サポートされている方式に従ったディストリビューション オンライン ファイル リポジトリから更新する必要があります。  

## <a name="viewing-missing-updates"></a>不足している更新プログラムの表示

**[不足している更新プログラム]** をクリックすると、ご利用のマシンに不足している更新プログラムの一覧が表示されます。 各更新プログラムが表示され、その更新プログラムを必要とするマシンの数やオペレーティング システムに関する情報、および詳細情報にアクセスするためのリンクがそれぞれに表示されます。 各更新プログラムを選択でき、**[ログ検索]** ページには、更新プログラムの詳細が表示されます。

## <a name="viewing-update-deployments"></a>更新プログラムの展開の表示

**[更新プログラムの展開]** をクリックすると、既存の更新プログラムの展開の一覧が表示されます。 一覧で更新プログラムの展開のいずれかをクリックすると、その更新プログラムの展開の **[更新プログラムの展開の実行]** ページが開きます。

![更新プログラムの展開結果の概要](./media/automation-update-management/update-deployment-run.png)

## <a name="creating-an-update-deployment"></a>更新プログラムの展開の作成

新しい更新プログラムの展開を作成するには、画面上部にある **[更新プログラムの展開のスケジュール]** をクリックして、**[新しい更新プログラムの展開]** ページを開きます。 指定する必要があるプロパティの値を次の表に示します。

| プロパティ | [説明] |
| --- | --- |
| Name |更新プログラムの展開を識別する一意の名前。 |
|オペレーティング システム| Linux または Windows|
| 更新するマシン |[保存した検索条件] を選択するか、ドロップダウンから [マシン] を選択し、個別のマシンを選択します |
|更新プログラムの分類|必要な更新プログラムの分類すべてを選択します|
|除外する更新プログラム|除外するすべての KB を、"KB" プレフィックスを付けないで入力します|
|スケジュール設定|開始する時刻を選択し、繰り返しの設定として、[1 回] または [定期的] のいずれかを選択します|
| メンテナンス期間 |更新プログラムに対して設定された分数です。 30 分未満の値を指定することはできません。また、6 時間を超えることはできません |

## <a name="search-logs"></a>検索ログ

ポータルで提供されている詳細のほか、ログに対して検索を実行できます。 **[Change Tracking]\(変更の追跡\)** ページを開いた状態で、**[Log Analytics]** をクリックします。これにより、**[ログ検索]** ページが開きます

### <a name="sample-queries"></a>サンプル クエリ

次の表は、このソリューションによって収集された更新レコードを探すログ検索の例です。

| クエリ | [説明] |
| --- | --- |
|プライマリの<br>&#124; where UpdateState == "Needed" and Optional == false<br>&#124; project Computer, Title, KBID, Classification, PublishedDate |更新プログラムがインストールされていないすべてのコンピューター<br>次のいずれかを追加して、OS を限定します。<br>OSType = "Windows"<br>OSType == "Linux" |
| プライマリの<br>&#124; where UpdateState == "Needed" and Optional == false<br>&#124; where Computer == "ContosoVM1.contoso.com"<br>&#124; project Computer, Title, KBID, Product, PublishedDate |特定のコンピューターにインストールされていない更新プログラム (コンピューター名は実際の名前に置き換えてください)|
| Event<br>&#124; where EventLevelName == "error" and Computer in ((Update &#124; where (Classification == "Security Updates" or Classification == "Critical Updates")<br>&#124; where UpdateState == "Needed" and Optional == false <br>&#124; distinct Computer)) |Error events for machines that have missing critical or security required updates (必要とされている緊急更新プログラムまたはセキュリティ更新プログラムがインストールされていないコンピューターのエラー イベント) |
| プライマリの<br>&#124; where UpdateState == "Needed" and Optional == false<br>&#124; distinct Title |全コンピューターのインストールされていない個別の更新プログラム | 
| UpdateRunProgress<br>&#124; where InstallationStatus == "failed" <br>&#124; summarize AggregatedValue = count() by Computer, Title, UpdateRunName |更新の実行に失敗した更新プログラムがあるコンピューター<br>次のいずれかを追加して、OS を限定します。<br>OSType = "Windows"<br>OSType == "Linux" | 
| プライマリの<br>&#124; where OSType == "Linux"<br>&#124; where UpdateState != "Not needed" and (Classification == "Critical Updates" or Classification == "Security Updates")<br>&#124; summarize AggregatedValue = count() by Computer |重大な脆弱性またはセキュリティの脆弱性に対処するパッケージ更新プログラムが使用可能な Linux マシンの一覧 | 
| UpdateRunProgress<br>&#124; where UpdateRunName == "DeploymentName"<br>&#124; summarize AggregatedValue = count() by Computer|この更新実行で更新されたコンピューター (更新プログラムの展開名は実際の名前に置き換えてください) | 

## <a name="integrate-with-system-center-configuration-manager"></a>System Center Configuration Manager との統合

PC、サーバー、モバイル デバイスを管理するために System Center Configuration Manager に投資してきたお客様は、ソフトウェア更新管理 (SUM) サイクルの一環として、ソフトウェア更新プログラムの管理でもその強さと成熟度を活用できます。

OMS Update Management ソリューションを System Center Configuration Manager と統合する方法については、「[System Center Configuration Manager と OMS Update Management の統合](oms-solution-updatemgmt-sccmintegration.md)」を参照してください。

## <a name="patching-linux-machines"></a>Linux マシンへのパッチ適用

次のセクションでは、Linux のパッチ適用に関する潜在的な問題について説明します。

### <a name="package-exclusion"></a>パッケージの除外

Red Hat Enterprise Linux など、Linux バリエーションの中には、パッケージによって OS レベルのアップグレードが発生することがあります。 これにより、OS バージョン番号の変更を伴う Update Management が実行される可能性があります。 Update Management がパッケージを更新するときに使用する方法は、管理者がローカルで Linux コンピューターに使用する方法と同じであるため、この動作は意図的なものです。

Update Management の実行による OS バージョンの更新を回避するには、**除外**機能を使用します。

Red Hat Enterprise Linux で、除外するパッケージ名は redhat-release-server.x86_64 です

![Linux の除外するパッケージ](./media/automation-update-management/linuxpatches.png)

### <a name="security-patches-not-being-applied"></a>セキュリティ パッチが適用されていない

更新プログラムを Linux マシンに展開するとき、更新プログラムの分類を選択できます。 これにより、更新プログラムは、指定した条件を満たすマシンにのみ適用されるようにフィルター処理されます。 このフィルターは、更新プログラムが展開されるときに、マシンに対してローカルに適用されます。 Update Management は更新プログラムの強化をクラウドで実行するため、Update Management では、一部の更新プログラムに "セキュリティへの影響あり" のフラグが設定されることがありますが、ローカル マシンにはその情報がありません。 このため、重大な更新プログラムを Linux マシンに適用する場合、そのマシンの "セキュリティへの影響あり" とマークされていない更新プログラムが存在する可能性があり、これらは適用しません。 ただし、Update Management には関連更新プログラムに関する追加情報があるため、そのマシンは引き続き "非準拠" として報告されることがあります。

使用されているパッチ適用モデルが異なるため、openSUSE では、更新プログラムの分類による更新プログラムの展開が機能しない可能性があります。

## <a name="troubleshooting"></a>トラブルシューティング

このセクションでは、更新管理ソリューションに関する問題のトラブルシューティングに役立つ情報について説明します。

ソリューションまたは仮想マシンをオンボードしようとして問題が発生した場合は、**アプリケーションとサービス ログ\Operations Manager** イベント ログで、イベント ID 4502 のイベントと **Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent** を含むイベント メッセージを確認してください。 次の表に、具体的なエラー メッセージと、考えられる個別の解決策を示します。  

| メッセージ | 理由 | 解決策 |   
|----------|----------|----------|  
| Unable to Register Machine for Patch Management, (更新プログラムの管理用のマシンを登録できません。)<br>Registration Failed with Exception (登録は次の例外で失敗しました)<br>System.InvalidOperationException: {"Message":"Machine is already<br>registered to a different account. "} (System.InvalidOperationException: {"メッセージ": "マシンは既に別のアカウントに登録されています。) | マシンは既に Update Management 用の別のワークスペースにオンボードされています。 | [Hybrid Runbook グループを削除する](automation-hybrid-runbook-worker.md#remove-hybrid-worker-groups)ことにより、古いアーティファクトのクリーンアップを実行します。|  
| Unable to Register Machine for Patch Management, (更新プログラムの管理用のマシンを登録できません。)<br>Registration Failed with Exception (登録は次の例外で失敗しました)<br>System.Net.Http.HttpRequestException: この要求の送信中にエラーが発生しました。 ---><br>System.Net.WebException: 基になる接続が<br>閉じられました。受信時に予期しないエラーが<br>発生しました。 ---> System.ComponentModel.Win32Exception:<br>クライアントとサーバーは共通のアルゴリズムを保持していないため<br>通信できません。 | プロキシ/ゲートウェイ/ファイアウォールが通信をブロックしています。 | [ネットワークの要件を確認します](automation-offering-get-started.md#network-planning)。|  
| Unable to Register Machine for Patch Management, (更新プログラムの管理用のマシンを登録できません。)<br>Registration Failed with Exception (登録は次の例外で失敗しました)<br>Newtonsoft.Json.JsonReaderException: Error parsing positive infinity value. (Newtonsoft.Json.JsonReaderException: 正の無限大の値の解析エラー。) | プロキシ/ゲートウェイ/ファイアウォールが通信をブロックしています。 | [ネットワークの要件を確認します](automation-offering-get-started.md#network-planning)。| 
| サービス <wsid>.oms.opinsights.azure.com によって提示された証明書は、<br>Microsoft サービスで使用する証明機関が<br>発行したものではありませんでした。 連絡先<br>TLS/SSL 通信を遮断するプロキシが実行されているかどうかを<br>確認してください。 |プロキシ/ゲートウェイ/ファイアウォールが通信をブロックしています。 | [ネットワークの要件を確認します](automation-offering-get-started.md#network-planning)。|  
| Unable to Register Machine for Patch Management, (更新プログラムの管理用のマシンを登録できません。)<br>Registration Failed with Exception (登録は次の例外で失敗しました)<br>AgentService.HybridRegistration。<br>PowerShell.Certificates.CertificateCreationException:<br>自己署名証明書を作成できませんでした。 ---><br>System.UnauthorizedAccessException: アクセスが拒否されました。 | 自己署名証明書の生成に失敗しました。 | システム アカウントが次のフォルダーに対する<br>読み取りアクセスを持っていることを確認します。<br>**C:\ProgramData\Microsoft\**<br>**Crypto\RSA**|  

## <a name="next-steps"></a>次の手順

使用している Windows VM の更新プログラムの管理方法を学習するためのチュートリアルに進みます。

> [!div class="nextstepaction"]
> [Azure Windows VM の更新プログラムとパッチの管理](automation-tutorial-troubleshoot-changes.md)

* [Log Analytics](../log-analytics/log-analytics-log-searches.md) でログ検索を使用して、詳細な更新プログラムデータを確認します。
* 緊急更新プログラムがコンピューターにインストールされていないと検出された場合またはコンピューターで自動更新が無効になっている場合、[アラートを作成](../log-analytics/log-analytics-alerts.md)します。
