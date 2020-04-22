---
title: Azure Arc for servers (プレビュー) の概要
description: Azure Arc for servers を使用して、Azure の外部でホストされているマシンを Azure リソースと同じように管理する方法について説明します。
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
keywords: azure automation, DSC, powershell, 望ましい状態の構成, 更新管理, 変更追跡, インベントリ, Runbook, Python, グラフィカル, ハイブリッド
ms.date: 03/24/2020
ms.topic: overview
ms.openlocfilehash: 5fa39028f1041a063bab295adabf8145a8b46ae4
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/14/2020
ms.locfileid: "81308784"
---
# <a name="what-is-azure-arc-for-servers-preview"></a>Azure Arc for servers (プレビュー) とは

Azure Arc for servers (プレビュー) を使用すると、Azure の外部 (企業ネットワークや Azure 以外のクラウド プロバイダー) でホストされている Windows や Linux のマシンを、ネイティブの Azure 仮想マシンと同じように管理することができます。 ハイブリッド マシンは、Azure に接続されると接続済みマシンになり、Azure 内のリソースとして扱われます。 接続済みマシンにはそれぞれリソース ID があり、サブスクリプション内のリソース グループの一部として管理されます。また、Azure Policy やタグの適用などの標準的な Azure のコンストラクトの利点も活用できます。

Azure の外部でホストされているハイブリッド マシンでこのエクスペリエンスを実現するには、Azure への接続を予定している各マシンに Azure Connected Machine エージェントがインストールされている必要があります。 このエージェントは他の機能をまったく持たず、Azure [Log Analytics エージェント](../../azure-monitor/platform/log-analytics-agent.md)に代わるものでもありません。 マシン上で実行されている OS とワークロードをプロアクティブに監視したい場合、それを Automation Runbook やソリューション (Update Management など) を使用して管理したい場合、または他の Azure サービス ([Azure Security Center](../../security-center/security-center-intro.md) など) を使用したい場合は、Windows 用および Linux 用の Log Analytics エージェントが必要となります。

>[!NOTE]
>このプレビュー リリースは評価を目的としたものであり、運用環境の重要なマシンの管理には使用しないことをお勧めします。
>

## <a name="supported-scenarios"></a>サポートされるシナリオ

Azure Arc for servers (プレビュー) は、接続済みのマシンに関して次のシナリオをサポートします。

- Azure 仮想マシンのポリシーの割り当てと同じエクスペリエンスを使用して [Azure Policy のゲスト構成](../../governance/policy/concepts/guest-configuration.md)を割り当てます。
- Log Analytics エージェントによって収集され、マシンの登録先である Log Analytics ワークスペースに格納されているログ データには、そのマシン固有のプロパティ (リソース ID など) が含まれるようになりました。そのプロパティを使用すると、[リソース コンテキスト](../../azure-monitor/platform/design-logs-deployment.md#access-mode) ログ アクセスをサポートすることができます。

## <a name="supported-regions"></a>サポートされているリージョン

Azure Arc for servers (プレビュー) では、特定のリージョンのみがサポートされています。

- WestUS2
- 西ヨーロッパ
- WestAsia

ほとんどの場合、インストール スクリプトを作成するときに選択する場所は、マシンの場所に最も近い Azure リージョンにする必要があります。 保存データは、指定したリージョンを含む Azure geography 内に格納されます。データ所在地に関する要件がある場合は、これがリージョンの選択に影響する可能性もあります。 マシンが接続されている Azure リージョンが障害の影響を受ける場合、接続されたマシンは影響を受けませんが、Azure を使用した管理操作を完了できない可能性があります。 リージョンの障害が発生した場合の回復力のために、地理的に冗長なサービスを提供する複数の場所がある場合は、各場所のマシンを別の Azure リージョンに接続することをお勧めします。

## <a name="prerequisites"></a>前提条件

### <a name="supported-operating-systems"></a>サポートされるオペレーティング システム

Azure Connected Machine エージェントでは、次のバージョンの Windows および Linux オペレーティング システムが正式にサポートされています。 

- Windows Server 2012 R2 以上 (Windows Server Core を含む)
- Ubuntu 16.04 および 18.04
- CentOS Linux 7
- SUSE Linux Enterprise Server (SLES) 15
- Red Hat Enterprise Linux (RHEL) 7
- Amazon Linux 2

>[!NOTE]
>このプレビュー リリースの Windows 用 Connected Machine エージェントでサポートされるのは、英語を使用するように構成された Windows Server だけです。
>

### <a name="required-permissions"></a>必要なアクセス許可

- マシンをオンボードするには、**Azure Connected Machine のオンボード** ロールのメンバーである必要があります。

- マシンの読み取り、変更、再オンボード、および削除を行うには、**Azure Connected Machine のリソース管理者** ロールのメンバーである必要があります。 

### <a name="azure-subscription-and-service-limits"></a>Azure サブスクリプションとサービスの制限

Azure Arc for servers (プレビュー) を使用してマシンを構成する前に、Azure Resource Manager の[サブスクリプションの制限](../../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits)と[リソース グループの制限](../../azure-resource-manager/management/azure-subscription-service-limits.md#resource-group-limits)を確認して、接続するマシンの数を計画する必要があります。

## <a name="tls-12-protocol"></a>TLS 1.2 プロトコル

Azure に転送中のデータのセキュリティを確保するには、トランスポート層セキュリティ (TLS) 1.2 を使用するようにマシンを構成することを強くお勧めします。 以前のバージョンの TLS/SSL (Secure Sockets Layer) は脆弱であることが確認されています。現在、これらは下位互換性を維持するために使用可能ですが、**推奨されていません**。 

|プラットフォーム/言語 | サポート | 詳細情報 |
| --- | --- | --- |
|Linux | Linux ディストリビューションでは、TLS 1.2 のサポートに関して [OpenSSL](https://www.openssl.org) に依存する傾向があります。 | [OpenSSL の Changelog](https://www.openssl.org/news/changelog.html) を参照して、使用している OpenSSL のバージョンがサポートされていることを確認してください。|
| Windows Server 2012 R2 以降 | サポートされています。既定で有効になっています。 | [既定の設定](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings)を使用していることを確認するには。|

### <a name="networking-configuration"></a>ネットワーク構成

Linux と Windows 用の Connected Machine エージェントは、TCP ポート 443 を介して安全に Azure Arc へのアウトバウンド通信を行います。 インターネット経由で通信するためにマシンがファイアウォールやプロキシ サーバーを介して接続する場合、以下の要件を確認してネットワーク構成の要件を把握してください。

アウトバウンド接続がファイアウォールやプロキシ サーバーによって制限されている場合は、以下に示す URL がブロックされていないことを確認してください。 エージェントに必要な IP 範囲またはドメイン名のみにサービスとの通信を許可する場合は、次のサービス タグおよび URL へのアクセスも許可する必要があります。

サービス タグ:

- AzureActiveDirectory
- AzureTrafficManager

URL:

| エージェントのリソース | 説明 |
|---------|---------|
|management.azure.com|Azure Resource Manager|
|login.windows.net|Azure Active Directory|
|dc.services.visualstudio.com|Application Insights|
|agentserviceapi.azure-automation.net|ゲスト構成|
|*-agentservice-prod-1.azure-automation.net|ゲスト構成|
|*.his.hybridcompute.azure-automation.net|ハイブリッド ID サービス|

各サービス タグ/リージョンの IP アドレスの一覧については、「[Azure IP 範囲とサービス タグ – パブリック クラウド](https://www.microsoft.com/download/details.aspx?id=56519)」という JSON ファイルを参照してください。 Microsoft では、各 Azure サービスとそれが使用する IP 範囲を含む更新プログラムを毎週発行しています。 詳細については、「[サービス タグ](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags)」を参照してください。

前の表に記載した URL は、サービス タグの IP アドレス範囲情報とは別に必要となります。現在、ほとんどのサービスにはサービス タグの登録がないためです。 結果として IP アドレスが変更される可能性があります。 ファイアウォール構成に IP アドレス範囲が必要な場合は、**AzureCloud** サービス タグを使用して、すべての Azure サービスへのアクセスを許可してください。 これらの URL のセキュリティ監視または検査を無効にせず、他のインターネット トラフィックと同様に許可してください。

### <a name="register-azure-resource-providers"></a>Azure リソースプロバイダーを登録する

Azure Arc for servers (プレビュー) は、このサービスを使用するために、サブスクリプション内の次の Azure リソースプロバイダーに依存します。

- **Microsoft.HybridCompute**
- **Microsoft.GuestConfiguration**

これらが登録されていない場合は、次のコマンドを使って登録できます。

Azure PowerShell:

```azurepowershell-interactive
Login-AzAccount
Set-AzContext -SubscriptionId [subscription you want to onboard]
Register-AzResourceProvider -ProviderNamespace Microsoft.HybridCompute
Register-AzResourceProvider -ProviderNamespace Microsoft.GuestConfiguration
```

Azure CLI:

```azurecli-interactive
az account set --subscription "{Your Subscription Name}"
az provider register --namespace 'Microsoft.HybridCompute'
az provider register --namespace 'Microsoft.GuestConfiguration'
```

「[Azure portal](../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)」の手順に従って、Azure portal でリソースプロバイダーを登録することもできます。

## <a name="connected-machine-agent"></a>Connected Machine エージェント

Windows および Linux 用の Azure Connected Machine エージェント パッケージは、以下の場所からダウンロードできます。

- Microsoft ダウンロード センターから [Windows エージェント Windows インストーラー パッケージ](https://aka.ms/AzureConnectedMachineAgent)。
- Linux エージェント パッケージは、Microsoft の[パッケージ リポジトリ](https://packages.microsoft.com/)から、ディストリビューションに適切なパッケージ形式 (.RPM または .DEB) を使用して配布されます。

>[!NOTE]
>本プレビュー中は、Ubuntu 16.04 または 18.04 に適したパッケージが 1 つだけリリースされました。

Windows および Linux 用の Azure Connected Machine エージェントは、要件に応じて、手動または自動で最新リリースにアップグレードできます。 詳細については、[このページ](manage-agent.md)を参照してください。

### <a name="agent-status"></a>エージェントの状態

Connected Machine エージェントは、5 分間隔でハートビート メッセージをサービスに送信します。 これらのハートビート メッセージをサービスがマシンから受信できなくなると、そのマシンはオフラインと見なされ、15 分から 30 分以内にポータルでの状態が自動的に **[切断]** に変更されます。 その後、Connected Machine エージェントからハートビート メッセージを受信すると、状態は自動的に **[接続]** に変更されます。

## <a name="install-and-configure-agent"></a>エージェントをインストールして構成する

要件に応じたさまざまな方法を使用して、ハイブリッド環境内のマシンを直接 Azure に接続することができます。 次の表は、どの方法が組織にとって最も効果的であるかを判断するために各方法について説明しています。

| Method | 説明 |
|--------|-------------|
| 対話型 | [Azure portal からマシンを接続する方法](onboard-portal.md)に関するページの手順に従って、1 台のマシンまたは少数のマシンにエージェントを手動でインストールします。<br> Azure portal からスクリプトを生成し、マシン上で実行することによって、エージェントのインストールおよび構成手順を自動化することができます。|
| 大規模 | [サービス プリンシパルを使用したマシンの接続](onboard-service-principal.md)に関するページに従って、複数のマシン用にエージェントをインストールして構成します。<br> この方法では、非対話形式でマシンを接続するためのサービス プリンシパルが作成されます。|
| 大規模 | [Windows PowerShell DSC の使用](onboard-dsc.md)方法に関するページの説明に従って複数のマシンにエージェントをインストールして構成します。<br> この方法では、サービス プリンシパルを使用し、PowerShell DSC から非対話形式でマシンを接続します。 |

## <a name="next-steps"></a>次のステップ

- Azure Arc for servers (プレビュー) の評価を始めるには、「[Azure portal からハイブリッド マシンを Azure に接続する](onboard-portal.md)」に従ってください。 
