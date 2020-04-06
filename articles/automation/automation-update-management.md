---
title: Azure の Update Management ソリューション
description: この記事では、Azure Update Management ソリューションを使用して Windows マシンと Linux マシンの更新プログラムを管理する方法について説明します。
services: automation
ms.subservice: update-management
ms.date: 02/27/2020
ms.topic: conceptual
ms.openlocfilehash: c76b14e4f08ec930159498da4a35fdad0341929e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "79235479"
---
# <a name="update-management-solution-in-azure"></a>Azure の Update Management ソリューション

Azure Automation の Update Management ソリューションを使用すると、Azure、オンプレミス環境、およびその他のクラウド環境で、Azure での Windows マシンと Linux マシンに対するオペレーティング システムの更新プログラムを管理できます。 すべてのエージェント マシンで利用可能な更新プログラムの状態をすばやく評価し、サーバーに必要な更新プログラムをインストールするプロセスを管理できます。

次の方法を使用して、仮想マシン (VM) に対する Update Management を有効にすることができます。

- 1 台以上の Azure マシンに対しては、お使いの [Azure Automation](automation-onboard-solutions-from-automation-account.md) アカウントから。Azure 以外のマシンに対しては手動で。

- 単一の Azure VM に対しては、Azure portal の [仮想マシン] ページから。 このシナリオは、[Linux](../virtual-machines/linux/tutorial-config-management.md#enable-update-management) VM 用と [Windows](../virtual-machines/windows/tutorial-config-management.md#enable-update-management) VM 用があります。

- [複数の Azure VM](manage-update-multi.md) に対しては、Azure portal の **[仮想マシン]** ページからそれらを選択することで。 

> [!NOTE]
> Update Management ソリューションでは、Log Analytics ワークスペースを Automation アカウントにリンクする必要があります。 サポートされているリージョンの確定的な一覧については、[Azure でのワークスペースのマッピング](./how-to/region-mappings.md)に関する記事をご覧ください。 リージョン マッピングは、Automation アカウントとは別のリージョンの VM を管理する機能には影響しません。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

お使いのサブスクリプション内の 新しいまたは既存の Automation アカウントと Log Analytics ワークスペースに Update Management ソリューションをデプロイできる Azure [Resource Manager テンプレート](automation-update-management-deploy-template.md)を使用できます。

## <a name="solution-overview"></a>ソリューションの概要

Update Management で管理されるマシンでは、評価の実行とデプロイの更新に次の構成が使用されます。

* Windows または Linux 用の Log Analytics エージェント
* PowerShell Desired State Configuration (DSC) (Linux の場合)
* Automation Hybrid Runbook Worker
* Microsoft Update または Windows Server Update Services (WSUS) (Windows マシンの場合)

次の図に、ワークスペース内の接続されたすべての Windows Server および Linux マシンに対する、このソリューションによる評価の実行方法とセキュリティ更新プログラムの適用方法を示します。

![Update Management プロセスのフロー](./media/automation-update-management/update-mgmt-updateworkflow.png)

Update Management を使用して、同じテナント内の複数のサブスクリプションにマシンをネイティブにオンボードできます。

パッケージがリリースされた後、Linux マシンの評価用に修正プログラムが表示されるまで 2 時間から 3 時間かかります。 Windows マシンの場合、リリースされてから評価用に修正プログラムが表示されるまで 12 時間から 15 時間かかります。

マシンで更新プログラムのコンプライアンスを確認するためのスキャンが完了すると、エージェントによって情報が Azure Monitor ログに一括転送されます。 Windows マシンでは、コンプライアンス スキャンは既定で 12 時間ごとに実行されます。

このスキャン スケジュールに加えて、Log Analytics エージェントの再起動後 15 分以内、更新プログラムのインストール前、および更新プログラムのインストール後に、更新プログラムのコンプライアンスを確認するためのスキャンが開始されます。

Linux マシンでは、コンプライアンス スキャンは既定で 1 時間ごとに実行されます。 Log Analytics エージェントを再起動した場合、コンプライアンス スキャンは 15 分以内に開始されます。

このソリューションでは、同期先として構成されたソースに基づいて、マシンがどの程度最新の状態であるかがレポートされます。 WSUS にレポートするように Windows マシンが構成されている場合、WSUS の Microsoft Update との最後の同期のタイミングによっては、Microsoft Updates で示されるものと結果が一致しないことがあります。 この動作は、パブリック リポジトリではなくローカル リポジトリにレポートするように構成されている Linux マシンでも同様です。

> [!NOTE]
> サービスに正しく報告するためには、Update Management で、特定の URL とポートを有効にする必要があります。 これらの要件の詳細については、[Hybrid Worker のためのネットワーク計画](automation-hybrid-runbook-worker.md#network-planning)に関する記事を参照してください。

スケジュールされたデプロイを作成することで、更新が必要なマシンへのソフトウェア更新プログラムのデプロイとインストールを実行できます。 Windows マシンの場合、"*オプション*" に分類されている更新プログラムはデプロイの範囲に含まれません。 デプロイの範囲には、必須の更新プログラムのみが含まれています。

スケジュールされたデプロイでは、適用可能な更新プログラムを受け取るターゲット マシンが定義されます。 これを行うには、特定のマシンを明示的に指定するか、特定のマシン セットのログ検索 (または指定した条件に基づいて動的に Azure VM を選択する [Azure クエリ](automation-update-management-query-logs.md)) に基づいて[コンピューター グループ](../azure-monitor/platform/computer-groups.md)を選択します。 これらのグループは[スコープ構成](../azure-monitor/insights/solution-targeting.md)とは異なります。これは、ソリューションを有効にする管理パックを取得するマシンを決定するためにのみ使用されます。

また、スケジュールを指定するときは、更新プログラムのインストールを許可する期間を承認し、設定します。 この期間は、メンテナンス期間と呼ばれます。 再起動が必要な場合、適切な再起動オプションを選択していれば、再起動のために 20 分間のメンテナンス期間が予約されます。 パッチ適用に予想よりも時間がかかり、メンテナンス期間の残りが 20 分を切った場合、再起動は行われません。

更新プログラムは、Azure Automation の Runbook によってインストールされます。 これらの Runbook は表示できません。また、これらは構成不要です。 更新プログラムのデプロイを作成すると、対象に含めたマシンに対して、指定した時間にマスター更新 Runbook を開始するスケジュールが作成されます。 このマスター Runbook は、必須の更新プログラムをインストールする子 Runbook を各エージェントで開始します。

更新プログラムのデプロイで指定された日時に、ターゲット マシンでデプロイが並列で実行されます。 まず、スキャンが実行され、その更新プログラムが依然として必須であることが確認されてからインストールされます。 WSUS クライアント マシンの場合、更新プログラムが WSUS で承認されていないと、更新プログラムのデプロイは失敗します。

1 つのマシンを複数の Log Analytics ワークスペースに Update Management 用に登録すること (マルチホームとも言われます) は、サポートされていません。

## <a name="clients"></a>クライアント

### <a name="supported-client-types"></a>サポートされているクライアントの種類

次の表は、更新プログラムの評価でサポートされているオペレーティング システムの一覧です。 修正プログラムを適用するには、Hybrid Runbook Worker が必要です。 Hybrid Runbook Worker の要件の詳細については、[Windows Hybrid Runbook Worker](automation-windows-hrw-install.md) のインストールと [Linux Hybrid Runbook Worker](automation-linux-hrw-install.md#installing-a-linux-hybrid-runbook-worker) のインストールに関するインストール ガイドを参照してください。

|オペレーティング システム  |Notes  |
|---------|---------|
|Windows Server 2019 (Datacenter、Datacenter Core、Standard)<br><br>Windows Server 2016 (Datacenter、Datacenter Core、Standard)<br><br>Windows Server 2012 R2 (Datacenter、Standard)<br><br>Windows Server 2012 || 
|Windows Server 2008 R2 (RTM および SP1 Standard)| Update Management は、このオペレーティング システムの評価のみをサポートしています。[Hybrid Runbook Worker](automation-windows-hrw-install.md) は Windows Server 2008 R2 ではサポートされていないため、修正プログラムの適用はサポートされていません。 |
|CentOS 6 (x86/x64) および 7 (x64)      | Linux エージェントでは、更新リポジトリへのアクセスが必要です。 分類に基づく修正プログラムでは、CentOS の RTM リリースには含まれていないセキュリティ データを返すための `yum` が必須です。 分類に基づく CentOS への修正プログラムの適用の詳細については、[Linux の更新プログラムの分類](automation-view-update-assessments.md#linux-2)に関する記事を参照してください。          |
|Red Hat Enterprise 6 (x86/x64) および 7 (x64)     | Linux エージェントでは、更新リポジトリへのアクセスが必要です。        |
|SUSE Linux Enterprise Server 11 (x86/x64) および 12 (x64)     | Linux エージェントでは、更新リポジトリへのアクセスが必要です。        |
|Ubuntu 14.04 LTS、16.04 LTS、18.04 (x86/x64)      |Linux エージェントでは、更新リポジトリへのアクセスが必要です。         |

> [!NOTE]
> Azure 仮想マシン スケール セットは、Update Management を使用して管理できます。 Update Management は、基本イメージではなくインスタンス自体で動作します。 すべての VM インスタンスを一度に更新しないように、段階的に更新をスケジュールする必要があります。
> 仮想マシン スケールセットのノードを追加するには、「[Azure 以外のマシンの配布準備](automation-tutorial-installed-software.md#onboard-a-non-azure-machine)」の手順に従ってください。

### <a name="unsupported-client-types"></a>サポートされていないクライアントの種類

次の表は、サポートされていないオペレーティング システムの一覧です。

|オペレーティング システム  |Notes  |
|---------|---------|
|Windows クライアント     | クライアント オペレーティング システム (Windows 7 や Windows 10 など) はサポートされません。        |
|Windows Server 2016 Nano Server     | サポートされていません。       |
|Azure Kubernetes Service ノード | サポートされていません。 「[Azure Kubernetes Service (AKS) の Linux ノードにセキュリティとカーネルの更新を適用する](../aks/node-updates-kured.md)」で説明されている修正プログラム適用プロセスを使用します。|

### <a name="client-requirements"></a>クライアントの要件

OS 固有のクライアント要件について以下に説明します。 追加のガイダンスについては、「[ネットワークの計画](#ports)」を参照してください。

#### <a name="windows"></a>Windows

WSUS サーバーと通信するように Windows エージェントを構成するか、Microsoft Update へのアクセスが必要です。

Configuration Manager で Azure Update Management を使用できます。 統合シナリオの詳細については、[Configuration Manager と Update Management を統合する](oms-solution-updatemgmt-sccmintegration.md#configuration)方法に関する記事を参照してください。 [Log Analytics Windows エージェント](../azure-monitor/platform/agent-windows.md)が必要です。 Azure VM をオンボードする場合、このエージェントは自動的にインストールされます。

既定では、Azure Marketplace からデプロイされた Windows VM は、Windows Update Service から自動更新を受信するように設定されています。 このソリューションまたは Windows VM をワークスペースに追加しても、この動作は変わりません。 このソリューションで更新プログラムを能動的に管理しない場合は、既定の動作 (更新プログラムが自動的に適用される) が適用されます。

> [!NOTE]
> ユーザーは、グループ ポリシーを変更して、システムではなくユーザーだけがマシンの再起動を実行できるようにすることができます。 Update Management にユーザーによる手動操作なしでマシンを再起動する権限がない場合、管理対象のマシンが停止する可能性があります。
>
> 詳しくは、「[自動更新のグループ ポリシー設定を構成する](https://docs.microsoft.com/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates)」をご覧ください。

#### <a name="linux"></a>Linux

Linux マシンでは、更新リポジトリへのアクセスが必要です。 プライベートまたはパブリックの更新リポジトリが使用できます。 Update Management と対話するには、TLS 1.1 または TLS 1.2 が必要です。 このソリューションでは、Linux 用 Log Analytics エージェントが複数の Azure Log Analytics ワークスペースにレポートする構成はサポートされていません。 マシンには Python 2.x もインストールされている必要があります。

Linux 用 Log Analytics エージェントをインストールして最新バージョンをダウンロードする方法の詳細については、[Linux 用 Log Analytics エージェント](../azure-monitor/platform/agent-linux.md)に関するページを参照してください。 Windows 用 Log Analytics エージェントをインストールする方法については、「[Windows コンピューターを Azure Monitor に接続する](../log-analytics/log-analytics-windows-agent.md)」を参照してください。

Azure Marketplace から入手できるオンデマンドの Red Hat Enterprise Linux (RHEL) イメージから作成した VM は、Azure にデプロイされた [Red Hat Update Infrastructure (RHUI)](../virtual-machines/workloads/redhat/redhat-rhui.md) にアクセスするよう登録されています。 その他の Linux ディストリビューションは、そのディストリビューションのサポートされている方法を使用して、ディストリビューションのオンライン ファイル リポジトリから更新する必要があります。

## <a name="permissions"></a>アクセス許可

更新プログラムのデプロイを作成および管理するには、特定のアクセス許可が必要です。 これらのアクセス許可の詳細については、[Update Management へのロールベースのアクセス](automation-role-based-access-control.md#update-management)に関するページをご覧ください。

## <a name="solution-components"></a>ソリューションのコンポーネント

このソリューションは、次のリソースで構成されています。 ソリューションを有効にすると、これらのリソースが Automation アカウントに自動的に追加されます。 

### <a name="hybrid-worker-groups"></a>ハイブリッド worker グループ

このソリューションを有効にすると、ソリューションに含まれている Runbook をサポートするために、Log Analytics ワークスペースに直接接続された Windows マシンが自動的に Hybrid Runbook Worker として構成されます。

ソリューションで管理されている各 Windows マシンが、Automation アカウントの **[システム ハイブリッド worker グループ]** として、 **[ハイブリッド Worker グループ]** ペインに表示されます。 ソリューションでは、*Hostname FQDN_GUID* の名前付け規則を使用します。 アカウントの Runbook でこれらのグループを対象として指定することはできません。 指定しようとすると、失敗します。 これらのグループは、この管理ソリューションをサポートすることのみを目的としています。

このソリューションと Hybrid Runbook Worker グループ メンバーシップの両方に同じアカウントを使用すると、Windows マシンを Automation アカウントの Hybrid Runbook Worker グループに追加して Automation Runbook をサポートすることができます。 この機能は、Hybrid Runbook Worker のバージョン 7.2.12024.0 で追加されました。

### <a name="management-packs"></a>管理パック

System Center Operations Manager 管理グループが [Log Analytics ワークスペースに接続](../azure-monitor/platform/om-agents.md)されている場合は、以下の管理パックが Operations Manager にインストールされます。 これらの管理パックは、このソリューションの追加後に、直接接続された Windows マシンにもインストールされます。 これらの管理パックを構成または管理する必要はありません。

* Microsoft System Center Advisor 更新プログラム評価インテリジェンス パック (Microsoft.IntelligencePacks.UpdateAssessment)
* Microsoft.IntelligencePack.UpdateAssessment.Configuration (Microsoft.IntelligencePack.UpdateAssessment.Configuration)
* 更新プログラムの展開 MP

> [!NOTE]
> ログ データを収集するように管理グループに構成されたエージェントを使用して Log Analytics ワークスペースに接続される Operations Manager 1807 または 2019 管理グループがある場合、それらを Update Management で管理するためには、次の規則を上書きする必要があります。**Microsoft.IntelligencePacks.AzureAutomation.HybridAgent.Init** 規則の **IsAutoRegistrationEnabled** パラメーターを上書きして **True** に設定します。

ソリューション管理パックの更新方法の詳細については、[Azure Monitor ログへの Operations Manager の接続](../azure-monitor/platform/om-agents.md)に関するページを参照してください。

> [!NOTE]
> Operations Manger エージェントを使用しているマシンを Update Management で完全に管理するには、エージェントを Windows または Linux 用の Log Analytics エージェントに更新する必要があります。 エージェントを更新する方法については、[Operations Manager エージェントのアップグレード方法](https://docs.microsoft.com/system-center/scom/deploy-upgrade-agents)に関する記事を参照してください。 Operations Manager を使用する環境では、System Center Operations Manager 2012 R2 UR 14 以降を実行している必要があります。

## <a name="data-collection"></a>データ コレクション

### <a name="supported-agents"></a>サポートされているエージェント

次の表では、このソリューションでサポートされている接続先ソースについて説明します。

| 接続先ソース | サポートされています | 説明 |
| --- | --- | --- |
| Windows エージェント |はい |ソリューションは、Windows エージェントからシステムの更新プログラムに関する情報を収集し、必要な更新プログラムのインストールを開始します。 |
| Linux エージェント |はい |ソリューションは、Linux エージェントからシステムの更新プログラムに関する情報を収集し、サポート対象のディストリビューションに対して必要な更新プログラムのインストールを開始します。 |
| Operations Manager 管理グループ |はい |ソリューションは、接続された管理グループ内のエージェントからシステムの更新プログラムに関する情報を収集します。<br/><br/>Operations Manager エージェントから Azure Monitor ログへの直接接続は必要ありません。 データは管理グループから Log Analytics ワークスペースに転送されます。 |

### <a name="collection-frequency"></a>収集の頻度

管理対象の各 Windows マシンでは、1 日 2 回スキャンが実行されます。 15 分ごとに Windows API が呼び出され、最後の更新時間を照会することで、状態が変化したかどうかが確認されます。 状態が変更された場合、コンプライアンス スキャンが開始されます。

管理対象の各 Linux マシンでは、1 時間ごとにスキャンが実行されます。

管理対象のマシンの更新されたデータがダッシュボードに表示されるまでに、30 分から 6 時間かかる可能性があります。

Update Management を使用しているマシンでの Azure Monitor ログによる平均データ使用量は、1 か月あたり約 25 メガバイト (MB) です。 この値は概数にすぎず、環境によって異なることがあります。 正確な使用量を把握するために、環境を監視することをお勧めします。 データ使用状況の分析の詳細については、「[使用量とコストの管理](../azure-monitor/platform/manage-cost-storage.md)」を参照してください。

## <a name="network-planning"></a><a name="ports"></a>ネットワークの計画

Update Management には次のアドレスが明示的に必要です。 このアドレスへの通信は、ポート 443 を使用して行われます。

|Azure Public  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com    | *.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|*.blob.core.windows.net | *.blob.core.usgovcloudapi.net|
|*.azure-automation.net | *.azure-automation.us|

Windows マシンでは、Windows Update で必要なすべてのエンドポイントへのトラフィックも許可する必要があります。 必要なエンドポイントの更新された一覧は、「[HTTP またはプロキシに関連する問題](/windows/deployment/update/windows-update-troubleshooting#issues-related-to-httpproxy)」で確認できます。 ローカル環境に [Windows Update サーバー](/windows-server/administration/windows-server-update-services/plan/plan-your-wsus-deployment)がある場合は、[WSUS キー](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry)で指定されているサーバーへのトラフィックも許可する必要があります。

Red Hat Linux マシンで必要なエンドポイントについては、「[RHUI コンテンツ配信サーバーの IP アドレス](../virtual-machines/workloads/redhat/redhat-rhui.md#the-ips-for-the-rhui-content-delivery-servers)」をご覧ください。 他の Linux ディストリビューションについては、プロバイダーのドキュメントをご覧ください。

Hybrid Runbook Worker で必要なポートの詳細については、[ハイブリッド worker ロールのポート](automation-hybrid-runbook-worker.md#hybrid-worker-role)に関するページをご覧ください。

例外を定義するときは、一覧に示されているアドレスを使用することをお勧めします。 [Microsoft Azure データセンターの IP 範囲](https://www.microsoft.com/download/details.aspx?id=41653)の IP アドレスをダウンロードできます。 このファイルは毎週更新され、現在デプロイされている範囲と今後変更される IP 範囲が反映されます。

[インターネットにアクセスできないコンピューターの接続](../azure-monitor/platform/gateway.md)に関するページの手順に従って、インターネットにアクセスできないマシンを構成します。

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

### <a name="linux"></a><a name="linux-2"></a>Linux

|分類  |説明  |
|---------|---------|
|緊急更新プログラムとセキュリティ更新プログラム     | 特定の問題または製品固有のセキュリティに関連する問題に対する更新プログラムです。         |
|他の更新プログラム     | 本質的に重要ではない、またはセキュリティ更新プログラムではない、他のすべての更新プログラムです。        |

Linux の場合、クラウドでのデータ エンリッチメントにより、Update Management は、評価データを表示しながら、クラウド内で重要な更新プログラムとセキュリティ更新プログラムを識別できます。 修正プログラムの場合、Update Management はマシン上にある分類データを使用します。 他のディストリビューションとは異なり、RTM バージョンの CentOS ではこの情報は使用できません。 CentOS マシンで、次のコマンドに対してセキュリティ データを返すように構成されている場合、Update Management は分類に基づいて修正プログラムを適用できます。

```bash
sudo yum -q --security check-update
```

CentOS 上でネイティブ分類データを使用できるようにするためのサポートされている方法は現在ありません。 現時点では、お客様がこれを自分で使用可能にした場合には、できる範囲内のサポートのみを提供しています。 

Red Hat Enterprise バージョン 6 の更新プログラムを分類するには、yum-security プラグインをインストールする必要があります。 Red Hat Enterprise Linux 7 では、プラグインは既に yum 自体の一部であるため、何もインストールする必要はありません。 詳細については、次の Red Hat の[ナレッジ記事](https://access.redhat.com/solutions/10021)を参照してください。

## <a name="integrate-with-configuration-manager"></a>Configuration Manager との統合

PC、サーバー、モバイル デバイスを管理するためにMicrosoft Endpoint Configuration Manager に投資してきたお客様は、Configuration Manager の強みと成熟度を活用してソフトウェア更新プログラムを管理できます。 Configuration Manager は、そのソフトウェア更新プログラムの管理 (SUM) サイクルの一部です。

管理ソリューションを Configuration Manager と統合する方法については、[Configuration Manager と Update Management を統合する](oms-solution-updatemgmt-sccmintegration.md)方法に関する記事を参照してください。

### <a name="third-party-updates-on-windows"></a>Windows でのサードパーティの更新プログラム

Update Management では、サポート対象の Windows システムへの更新プログラムの適用を、ローカルに構成された更新リポジトリに依存しています。 これは、WSUS または Windows Update のいずれかです。 [System Center Updates Publisher](https://docs.microsoft.com/configmgr/sum/tools/updates-publisher) (Updates Publisher) などのツールを使用して、WSUS でのカスタム更新プログラムのインポートと発行を実行できます。 このシナリオでは、サードパーティ製ソフトウェアで Configuration Manager を更新リポジトリとして使用するマシンに、Update Management で更新プログラムを適用できます。 Updates Publisher を構成する方法については、「[Install Updates Publisher](https://docs.microsoft.com/configmgr/sum/tools/install-updates-publisher)」(Updates Publisher のインストール) を参照してください。

## <a name="enable-update-management"></a><a name="onboard"></a>Update Management の有効化

システムへの更新プログラムの適用を開始するには、Update Management ソリューションを有効にする必要があります。 ソリューションをオンボードするために推奨およびサポートされている方法を以下に示します。

- [仮想マシンから](automation-onboard-solutions-from-vm.md)

- [複数のマシンを参照することから](automation-onboard-solutions-from-browse.md)

- [お使いの Automation アカウントから](automation-onboard-solutions-from-automation-account.md)

- [Azure Automation Runbook によって](automation-onboard-solutions.md)

- [Azure Resource Manager テンプレートの使用](automation-update-management-deploy-template.md)

## <a name="next-steps"></a>次のステップ

Azure Automation [FAQ](automation-faq.md) を見直して、このソリューションに関してよく寄せられる質問を確認します。
