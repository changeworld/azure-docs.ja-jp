---
title: Azure Load Balancer の Azure セキュリティ ベースライン
description: Azure Load Balancer セキュリティ ベースラインでは、Azure セキュリティ ベンチマークで指定されているセキュリティに関する推奨事項を実装するための手順のガイダンスとリソースが提供されます。
author: msmbaldwin
ms.service: load-balancer
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 64e0a8cfcaf00c55038fbe5d1cdc7423b681b690
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589263"
---
# <a name="azure-security-baseline-for-azure-load-balancer"></a>Azure Load Balancer の Azure セキュリティ ベースライン

このセキュリティ ベースラインでは、[Azure セキュリティ ベンチマーク バージョン 1.0](../security/benchmarks/overview-v1.md) のガイダンスが Microsoft Azure Load Balancer に適用されます。 Azure セキュリティ ベンチマークには、Azure 上のクラウド ソリューションをセキュリティで保護する方法に関する推奨事項がまとめてあります。
内容は、**セキュリティ制御** によってグループ化されています。これは、Azure セキュリティ ベンチマークと、Azure Load Balancer に適用できる関連ガイダンスによって定義されています。 Azure Load Balancer に適用できない **制御** は、除外されています。

 
Azure Load Balancer を完全に Azure セキュリティ ベンチマークにマップする方法については、[完全な Azure Load Balancer セキュリティ ベースライン マッピング ファイル](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)を参照してください。

## <a name="network-security"></a>ネットワークのセキュリティ

*詳細については、[Azure セキュリティ ベンチマークの「ネットワークのセキュリティ](../security/benchmarks/security-control-network-security.md)」を参照してください。*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1:仮想ネットワーク内の Azure リソースを保護する

**ガイダンス**:内蔵 Azure ロードバランサーを使用して、インターネットに公開することなく、特定の仮想ネットワークまたはピアリングされた仮想ネットワーク内からバックエンド リソースへのトラフィックのみを許可します。 送信元ネットワークアドレス変換 (SNAT) を使用して外部ロード バランサーを実装し、

インターネットに直接公開されることから保護するためにバックエンド リソースの IP アドレスをマスカレードします。

Azure には、Standard と Basic の 2 つのロード バランサーが用意されています。 すべての運用環境のワークロードに Standard Load Balancer を使用します。 ネットワーク セキュリティ グループを実装し、アプリケーションの信頼されたポートと IP アドレス範囲へのアクセスのみを許可します。 バックエンド サブネットまたはバックエンド仮想マシンの NIC にネットワーク セキュリティ グループが割り当てられていない場合、トラフィックはロード バランサーからのこれらのリソースには許可されません。 Standard Load Balancer では、ネットワーク セキュリティ グループを使用して送信 NAT を定義するアウトバウンド規則を提供します。 送信接続の動作を調整するには、これらのアウトバウンド規則を確認します。

運用環境のワークロードには Standard Load Balancer を使用することをお勧めします。通常、基本的な Load Balancer はテストにのみ使用します。基本の種類は既定でインターネットからの接続に対して開かれ、操作のためにネットワーク セキュリティ グループを必要としないためです。

- [Azure の送信接続](load-balancer-outbound-connections.md)

- [Azure Public Load Balancer をアップグレードする](upgrade-basic-standard.md)

**責任**: Customer

**Azure Security Center の監視**: [Azure セキュリティ ベンチマーク](/azure/governance/policy/samples/azure-security-benchmark)は、Security Center の既定のポリシー イニシアチブであり、[Security Center の推奨事項](/azure/security-center/security-center-recommendations)の基礎となります。 このコントロールに関連する Azure Policy 定義は、Security Center によって自動的に有効になります。 このコントロールに関連するアラートでは、関連するサービスのために [Azure Defender](/azure/security-center/azure-defender) プランが必要になる場合があります。

**Azure Policy 組み込み定義 - Microsoft.Network**:

[!INCLUDE [Resource Policy for Microsoft.Network 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2:仮想ネットワーク、サブネット、NIC の構成とトラフィックを監視してログに記録する

**ガイダンス**:ロード バランサーは、バックエンド リソースに適用されるネットワーク セキュリティ グループの規則と、インターネット アクセスを制御するために構成されたアウトバウンド規則に依存するため、パススルー サービスです。

お使いのロード バランサーの [アウトバウンド規則] ブレードと、暗黙のアウトバウンド規則が有効になっている可能性がある [負荷分散規則] ブレードを使用して、Standard Load Balancer 用に構成されたアウトバウンド規則を確認します。

送信接続の数を監視して、リソースがインターネットに到達する頻度を追跡します。 

セキュリティ センターを使用し、ネットワークの保護に関する推奨事項に従って、Azure のネットワーク リソースを確保します。

バックエンド リソースのセキュリティ推奨事項に従い、ネットワーク セキュリティ グループのフロー ログを有効にし、監査のためにログを Azure Storage アカウントに送信します。

また、フロー ログを Log Analytics ワークスペースに送信してから、Traffic Analytics を使用して Azure クラウド内のトラフィック フローに関する分析情報を提供することもできます。 Traffic Analytics のいくつかの利点として、ネットワーク アクティビティを視覚化したり、ホット スポットやセキュリティの脅威を識別したり、トラフィック フロー パターンを把握したり、ネットワークの誤った構成の正確な場所を特定したりする機能が挙げられます。

- [ネットワーク セキュリティ グループのフローのログを有効にする方法](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Traffic Analytics を有効にして使用する方法](../network-watcher/traffic-analytics.md)

- [Azure Security Center によって提供されるネットワークのセキュリティについて](../security-center/security-center-network-recommendations.md)

- [送信接続の統計情報を確認する方法](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics#how-do-i-check-my-outbound-connection-statistics)

**責任**: Customer

**Azure Security Center の監視**: [Azure セキュリティ ベンチマーク](/azure/governance/policy/samples/azure-security-benchmark)は、Security Center の既定のポリシー イニシアチブであり、[Security Center の推奨事項](/azure/security-center/security-center-recommendations)の基礎となります。 このコントロールに関連する Azure Policy 定義は、Security Center によって自動的に有効になります。 このコントロールに関連するアラートでは、関連するサービスのために [Azure Defender](/azure/security-center/azure-defender) プランが必要になる場合があります。

**Azure Policy 組み込み定義 - Microsoft.Network**:

[!INCLUDE [Resource Policy for Microsoft.Network 1.2](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-2.md)]

### <a name="13-protect-critical-web-applications"></a>1.3:重要な Web アプリケーションを保護する

**ガイダンス**:Web アプリケーションを保護するために Microsoft の脅威インテリジェンスを使用するには、アウトバウンド規則とネットワーク セキュリティ グループを通じて、インターネット接続と有効なソース IP を明示的に定義します。

- [Azure Firewall を統合します。](../firewall/integrate-lb.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4:既知の悪意のある IP アドレスとの通信を拒否する

**ガイダンス**:DDoS 攻撃から保護するには、Azure 仮想ネットワークで Azure DDoS (Azure 分散型サービス拒否) Standard 保護を有効にします。 

有効化され、悪意のあるネットワーク トラフィックに対して "アラートおよび拒否" するように構成された脅威インテリジェンスベースのフィルター処理とともに、組織の各ネットワーク境界に Azure Firewall をデプロイします。

 

セキュリティ センターの脅威保護を使用して、既知悪意ある IP アドレスとの通信を検出します。 

Standard Load Balancer は、既定でセキュリティで保護され、プライベートであり分離された仮想ネットワークの一部として設計されています。 許可されたトラフィックを明示的に許可し、既知の悪意のある IP アドレスを許可しないように、ネットワーク セキュリティ グループによって開かれている場合を除き、受信フローに閉じられます。 サブネットまたは仮想マシン リソースの NIC のネットワーク セキュリティ グループがロード バランサーの背後に存在しない場合、トラフィックはこのリソースに接続できません。 

有効化され、悪意のあるネットワーク トラフィックに対して "アラートおよび拒否" するように構成された脅威インテリジェンス ベースのフィルター処理とともに、組織の各ネットワーク境界に Azure Firewall をデプロイし、悪意ある IP アドレスからの攻撃を防ぎます。 

Azure Firewall とロード バランサーを統合する方法についての説明。

セキュリティ センターの脅威保護機能を使用して、既知の悪意ある IP アドレスとの通信を検出します。 

セキュリティ センター (Standard Tier) では、ジャストインタイムの仮想マシン アクセスが提供され、許可されているソース IP アドレスを構成して、承認された IP アドレス/範囲からのアクセスのみを許可します。
 

また、実際のトラフィックと脅威インテリジェンスに基づいてポートとソース IP を制限するネットワーク セキュリティ グループ構成を推奨するセキュリティ センターのアダプティブ ネットワーク強化機能を使用します。
 

- [Azure portal を使用した Azure DDoS Protection Standard の管理](../ddos-protection/manage-ddos-protection.md)

- [Azure Firewall の脅威インテリジェンスベースのフィルター処理](../firewall/threat-intel.md)

- [Azure Security Center での脅威の防止](../security-center/azure-defender.md)

- [Just-In-Time アクセスを使用して管理ポートをセキュリティで保護する](../security-center/security-center-just-in-time.md)

- [Azure Security Center でのアダプティブ ネットワークのセキュリティ強化機能](../security-center/security-center-adaptive-network-hardening.md)

- [Azure Firewall とロード バランサーを統合する](../firewall/overview.md)

**責任**: Customer

**Azure Security Center の監視**: [Azure セキュリティ ベンチマーク](/azure/governance/policy/samples/azure-security-benchmark)は、Security Center の既定のポリシー イニシアチブであり、[Security Center の推奨事項](/azure/security-center/security-center-recommendations)の基礎となります。 このコントロールに関連する Azure Policy 定義は、Security Center によって自動的に有効になります。 このコントロールに関連するアラートでは、関連するサービスのために [Azure Defender](/azure/security-center/azure-defender) プランが必要になる場合があります。

**Azure Policy 組み込み定義 - Microsoft.Network**:

[!INCLUDE [Resource Policy for Microsoft.Network 1.4](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-4.md)]

### <a name="15-record-network-packets"></a>1.5:ネットワーク パケットを記録する

**ガイダンス**:異常なアクティビティを調査する場合は、Network Watcher パケット キャプチャを有効にします。

- [Azure Network Watcher のインスタンスを作成する方法](../network-watcher/network-watcher-create.md)

**責任**: Customer

**Azure Security Center の監視**: [Azure セキュリティ ベンチマーク](/azure/governance/policy/samples/azure-security-benchmark)は、Security Center の既定のポリシー イニシアチブであり、[Security Center の推奨事項](/azure/security-center/security-center-recommendations)の基礎となります。 このコントロールに関連する Azure Policy 定義は、Security Center によって自動的に有効になります。 このコントロールに関連するアラートでは、関連するサービスのために [Azure Defender](/azure/security-center/azure-defender) プランが必要になる場合があります。

**Azure Policy 組み込み定義 - Microsoft.Network**:

[!INCLUDE [Resource Policy for Microsoft.Network 1.5](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-5.md)]

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6:ネットワーク ベースの侵入検出/侵入防止システム (IDS/IPS) をデプロイする

**ガイダンス**:ロード バランサーの環境に対するペイロード検査能力を備えた IDS または IPS 機能をサポートする Azure Marketplace からの提供内容を実装します。 

ペイロード検査が要件でない場合は、Azure Firewall の脅威インテリジェンスを使用します。 Azure Firewall の脅威インテリジェンス ベースのフィルター処理は、既知の悪意のある IP アドレスおよびドメインとの間のトラフィックに対してアラートを送信したりブロックしたりするために使用されます。 この IP アドレスとドメインのソースは、Microsoft の脅威インテリジェンス フィードです。

悪意のあるトラフィックを検出およびブロックできるように、組織の各ネットワーク境界に任意のファイアウォール ソリューションをデプロイします。

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

- [Azure Firewall をデプロイする方法l](../firewall/tutorial-firewall-deploy-portal.md)

- [Azure Firewall でアラートを構成する方法](../firewall/threat-intel.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="17-manage-traffic-to-web-applications"></a>1.7:Web アプリケーションへのトラフィックを管理する

**ガイダンス**:Web アプリケーションを保護するために Microsoft の脅威インテリジェンス機能を使用するには、アウトバウンド規則とネットワーク セキュリティ グループを通じて、インターネット接続と有効なソース IP を明示的に定義します。

- [Azure Firewall を統合します。](../firewall/integrate-lb.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8:ネットワーク セキュリティ規則の複雑さと管理オーバーヘッドを最小限に抑える

**ガイダンス**:セキュリティ規則を作成するときは、特定の IP アドレスの代わりにサービス タグを使うことができます。 規則のソースまたはターゲット フィールドでサービス タグ名を指定することにより、対応するサービスのトラフィックを許可または拒否できます。 

サービス タグに含まれるアドレス プレフィックスの管理は Microsoft が行い、アドレスが変化するとサービス タグは自動的に更新されます。 

既定では、正常性プローブのトラフィックを許可するためのサービス タグ AzureLoadBalancer がすべてのネットワーク セキュリティ グループに含まれています。 

ネットワーク セキュリティ グループの規則で使用できるすべてのサービス タグについての Azure ドキュメントを参照します。

- [利用可能なサービス タグ](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9:ネットワーク デバイスの標準的なセキュリティ構成を維持する

**ガイダンス**: Azure Policy を使用して、ネットワーク リソースの標準的なセキュリティ構成を定義し、実装します。

Azure Blueprints を使用して、Azure Resource Manager テンプレート、Azure RBAC コントロール、ポリシーなどの主要な環境アーティファクトを単一のブループリント定義にパッケージ化することで、大規模な Azure デプロイを簡略化することもできます。 

ブループリントを新しいサブスクリプションに適用し、バージョン管理によって制御と管理を容易に微調整できます。

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

- [ネットワークに関する Azure Policy のサンプル](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network)

- [Azure Blueprint を作成する方法](../governance/blueprints/create-blueprint-portal.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="110-document-traffic-configuration-rules"></a>1.10:トラフィック構成規則を文書化する

**ガイダンス**:ネットワーク セキュリティおよびトラフィック フローに関連したネットワーク セキュリティ グループやその他のリソースに対して、リソース タグを使用してください。 

個々のネットワーク セキュリティ グループの規則については、[Description (説明)] フィールドを使用して、ネットワークとの間で送受信されるトラフィックを許可する規則を文書化します。

すべてのリソースが確実にタグ付きで作成され、既存のタグ付けされていないリソースがユーザーに通知されるようにするには、タグ付けに関連したいずれかの組み込みの Azure Policy 定義 (「タグとその値が必要」など) を実装します。

Azure PowerShell または Azure CLI を選択して、タグに基づいたリソースの検索やアクションを実行します。

- [タグを作成して使用する方法](../azure-resource-manager/management/tag-resources.md)

- [Azure 仮想ネットワークの作成方法](../virtual-network/quick-create-portal.md)

- [ネットワーク セキュリティ グループの規則を使用してネットワーク トラフィックをフィルター処理する方法](../virtual-network/tutorial-filter-network-traffic.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11:自動化ツールを使用してネットワーク リソース構成を監視し、変更を検出する

**ガイダンス**:Azure アクティビティ ログを使用してリソース構成を監視し、Azure リソースに対する変更を検出します。 

Azure Monitor でアラートを作成して、重要なリソースへが変更されたときに通知します。

- [Azure アクティビティ ログ イベントを表示して取得する方法](https://docs.microsoft.com/azure/azure-monitor/essentials/activity-log#view-the-activity-log)

- [Azure Monitor でアラートを作成する方法](../azure-monitor/alerts/alerts-activity-log.md)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="logging-and-monitoring"></a>ログ記録と監視

*詳細については、[Azure セキュリティ ベンチマークの「ログ記録と監視](../security/benchmarks/security-control-logging-monitoring.md)」を参照してください。*

### <a name="22-configure-central-security-log-management"></a>2.2:セキュリティ ログの一元管理を構成する

**ガイダンス**:サブスクリプションのアクティビティ ログを表示して、ロード バランサーのアウトバウンド規則とネットワーク セキュリティ グループへの変更を確認します。 

内部ホスト ログを表示して、バックエンド リソースがセキュリティで保護されていることを確認します。

これらのログを Log Analytics または別のストレージ プラットフォームにエクスポートします。 Azure Monitor で Log Analytics ワークスペースを使用してクエリを発行し、分析を実行して、長期のアーカイブ ストレージには Azure Storage アカウントを使用します。

組織のビジネス要件に基づいて、Azure Sentinel またはサードパーティの SIEM に対してこのデータを有効にしてオンボードします。

- [Azure Sentinel をオンボードする方法](../sentinel/quickstart-onboard.md)

- [Azure Monitor でプラットフォーム ログとメトリックを収集する方法](../azure-monitor/essentials/diagnostic-settings.md)

- [Azure Monitor で Azure 仮想マシンの内部ホスト ログを収集する方法](../azure-monitor/vm/quick-collect-azurevm.md)

- [Azure Monitor とサードパーティの SIEM 統合を開始する方法](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

- [プラットフォーム アクティビティ ログ](../azure-monitor/essentials/activity-log.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3:Azure リソースの監査ログ記録を有効にする

**ガイダンス**:Basic Load Balancer のアクティビティ ログでキャプチャされたコントロールと管理プレーンのログ記録と監査情報を確認します。 これらのキャプチャ設定は既定で有効になっています。 

アクティビティ ログを使用してリソースに対するアクションを監視し、すべてのアクティビティとそれらの状態を表示します。 

アクティビティ ログを使用して、サブスクリプション内のリソースに対して実行された操作を特定します。 

- 操作を開始したユーザー
- 操作が発生した時間
- 操作の状態

- 操作を調査するために役立つ可能性のあるその他のプロパティの値

Azure PowerShell、Azure コマンド ライン インターフェイス (CLI)、Azure REST API、または Azure portal を使用して、アクティビティ ログから情報を取得します。 

Standard Load Balancer 構成用に、Azure Monitor を介して多次元診断機能を実装します。  これには、ソース ネットワーク アドレス変換 (SNAT) 接続、ポートに関する情報など、セキュリティの利用可能なメトリックスが含まれます。 SYN (同期) パケットおよびパケット カウンターに関する追加のメトリックスも利用できます。 

API を使用してプログラムから多次元メトリックスを取得し、[All Metrics (すべてのメトリックス)] オプションを使用してストレージ アカウントに書き込みます。

Microsoft Power BI を備えた Azure 監査ログ コンテンツ パックを使用して、構成済みのダッシュボードでデータを分析するか、要件に合わせてビューをカスタマイズします。

ログは、イベント ハブまたは Log Analytics ワークスペースにストリーム配信します。 どのログも Azure Blob Storage から抽出し、Excel や Power BI などのさまざまなツールで表示できます。 

組織のビジネス要件に基づいて、Azure Sentinel またはサードパーティの SIEM に対してデータを有効にしてオンボードします。

- [「Audit operations with Resource Manager (リソース マネージャーの監査操作)」に説明されている各メソッドについては、この記事の手順を確認してください。](../azure-resource-manager/management/view-activity-logs.md)

- [パブリック Basic ロード バランサーの Azure Monitor ログ](load-balancer-monitor-log.md)

- [リソースのアクションを監視するアクティビティ ログの表示](../azure-resource-manager/management/view-activity-logs.md)

- [API を使用してプログラムで多次元メトリックを取得する](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics#retrieve-multi-dimensional-metrics-programmatically-via-apis)

- [Azure Monitor とサードパーティの SIEM 統合を開始する方法](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="25-configure-security-log-storage-retention"></a>2.5:セキュリティ ログのストレージ保持を構成する

**ガイダンス**:アクティビティ ログは、既定で有効になっており、Azure のイベントログ ストアに 90 間保持されます。 Azure Monitor 内の組織のコンプライアンス規則に従って Log Analytics ワークスペースの保持期間を設定します。 長期およびアーカイブ ストレージには Azure Storage アカウントを使用します。

- [リソース記事においてアクションを監視するアクティビティ ログを表示します。](../azure-resource-manager/management/view-activity-logs.md)

- [Log Analytics でデータ保持期間を変更する](https://docs.microsoft.com/azure/azure-monitor/logs/manage-cost-storage#change-the-data-retention-period)

- [Azure Storage アカウント ログの保持ポリシーを構成する方法](https://docs.microsoft.com/azure/storage/common/manage-storage-analytics-logs#configure-logging)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="26-monitor-and-review-logs"></a>2.6:ログを監視して確認する

**ガイダンス**:Azure portal の [ロード バランサー] ページと Azure Monitor 下の [Resource Health (リソース正常性)] ページを使用して、Standard Load Balancer リソースを監視、管理、トラブルシューティングします。 これには、ソース ネットワーク アドレス変換 (SNAT) 接続、ポートに関する情報など、セキュリティについて利用可能です。 SYN (同期) パケットおよびパケット カウンターに関する追加のメトリックスも利用できます。 

Azure Monitor を使用して、標準、外部、内部、ロード バランサーの多次元メトリックスでエンドポイント正常性プローブの状態を確認します。 API を使用してプログラムからこれらのメトリックスを取得し、[All Metrics (すべてのメトリックス)] オプションを使用してストレージ アカウントに書き込みます。

Azure Monitor ログは内部の Basic Load Balancer では使用できません。 

Azure Monitor を使用して、正常性プローブの障害によりロード バランサーからの要求を受信していないバックエンド プール内のインスタンスの数など、Basic 外部ロード バランサーのバックエンド プールごとに概要が表示される正常性プローブの状態を確認します。 

Azure Operational Insights とともにログ記録を実装し、ロード バランサーの正常性状態に関する統計情報を提供します。 

アクティビティ ログを使用して、Azure サブスクリプションのリソースとその状態に関するアラートを表示し、アクションを監視します。 アクティビティ ログは既定で有効になっており、Azure portal で表示できます。 

Microsoft Power BI を Azure 監査ログコンテンツ パックと共に使用して、事前に構成されたダッシュボードでデータを分析します。 ビジネス ニーズに基づいて、ニーズに合わせてビューをカスタマイズします。 

ログは、イベント ハブまたは Log Analytics ワークスペースにストリーム配信します。 どのログも Azure Blob Storage から抽出し、Excel や Power BI などのさまざまなツールで表示できます。 Azure Sentinel またはサード パーティの SIEM に対してデータを有効にしてオンボードできます。

- [Load Balancer の正常性プローブ](load-balancer-custom-probe-overview.md)

- [Azure 監視 REST API](/rest/api/monitor)

- [REST API を使用してメトリックスを取得する方法](/rest/api/monitor/metrics/list)

- [メトリックス、アラート、およびリソース正常性を使用した Standard Load Balancer の診断](load-balancer-standard-diagnostics.md)

- [パブリック Basic ロード バランサーの Azure Monitor ログ](load-balancer-monitor-log.md)

- [Azure Portal でロード バランサーのメトリックを表示する](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics#view-your-load-balancer-metrics-in-the-azure-portal)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7:異常なアクティビティについてのアラートを有効にする

**ガイダンス**:Log Analytics ワークスペースと共にセキュリティ センターを使用し、セキュリティ ログやイベントでロード バランサーに関連する異常なアクティビティに対する監視とアラートを行います。

Azure Sentinel またはサード パーティの SIEM ツールに対してデータを有効にしてオンボードできます。

- [Azure Sentinel をオンボードする方法](../sentinel/quickstart-onboard.md)

- [Azure Security Center でアラートを管理する方法](../security-center/security-center-managing-and-responding-alerts.md)

- [Log Analytics のログ データに関するアラートを送信する方法](../azure-monitor/alerts/tutorial-response.md)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="identity-and-access-control"></a>ID およびアクセス制御

*詳細については、[Azure セキュリティ ベンチマークの「ID およびアクセス制御](../security/benchmarks/security-control-identity-access-control.md)」を参照してください。*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: 管理アカウントのインベントリを維持する

**ガイダンス**:Azure のロールベースのアクセス制御 (Azure RBAC) を使用すると、ロールの割り当てを通じてロード バランサーなどの Azure リソースへのアクセスを管理できます。 これらのロールを、ユーザー、グループ、サービス プリンシパル、マネージド ID に割り当てます。

特定のリソースに対して、Azure CLI、Azure PowerShell、Azure portal などのツールを使用してインベントリが事前定義された組み込みロール。

- [PowerShell を使用して Azure Active Directory (Azure AD) でディレクトリ ロールを取得する方法](/powershell/module/azuread/get-azureaddirectoryrole)

- [PowerShell を使用して Azure AD でディレクトリ ロールのメンバーを取得する方法](/powershell/module/azuread/get-azureaddirectoryrolemember)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: すべての Azure Active Directory ベースのアクセスに多要素認証を使用する

**ガイダンス**: Azure Active Directory (Azure AD) 多要素認証を有効にし、Security Center の ID およびアクセス管理の推奨事項に従います。

- [Azure で多要素認証を有効にする方法](../active-directory/authentication/howto-mfa-getstarted.md) 

- [Azure Security Center で ID とアクセスを監視する方法](../security-center/security-center-identity-access.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: すべての管理タスクに専用マシン (特権アクセス ワークステーション) を使用する

**ガイダンス**: 多要素認証が構成された特権アクセス ワークステーション (PAW) を使用して Azure ネットワーク リソースの管理とアクセスを行います。 

- [特権アクセス ワークステーションについて](/security/compass/privileged-access-devices)

- [Azure で多要素認証を有効にする方法](../active-directory/authentication/howto-mfa-getstarted.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3.8:承認された場所からのみ Azure リソースを管理する

**ガイダンス**: 条件付きアクセスのネームド ロケーションを使用して、IP アドレス範囲または国、地域の特定の論理グループからのアクセスのみを許可します。

- [Azure でネームド ロケーションを構成する方法](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="39-use-azure-active-directory"></a>3.9: Azure Active Directory を使用する

**ガイダンス**:Azure Active Directory (Azure AD) を自社サービスの中央認証および承認システムとして使用します。 Azure AD では、データの保存時と転送時に強力な暗号化を使用してデータが保護され、さらに、ユーザーの資格情報がソルト化およびハッシュされ、安全に保存されます。  

- [Azure AD インスタンスを作成して構成する方法](../active-directory-domain-services/tutorial-create-instance.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: ユーザー アクセスを定期的に確認して調整する

**ガイダンス**:Azure Active Directory (Azure AD) を使用して、古いアカウントの検出に役立つログが提供されます。 

Azure ID アクセス レビューは、グループ メンバーシップ、エンタープライズ アプリケーションへのアクセス、ロールの割り当てを効率的に管理するために実行できます。 アクティブなユーザーのみが継続的なアクセス権を持っていることを確認するために、ユーザー アクセスを定期的に確認する必要があります。

- [Azure AD のレポートの概要](/azure/active-directory/reports-monitoring/)

- [Azure ID アクセス レビューの使用方法](../active-directory/governance/access-reviews-overview.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: 非アクティブ化された資格情報へのアクセスの試行を監視する

**ガイダンス**:Azure Active Directory (Azure AD) のサインイン アクティビティ、監査、リスク イベントのログ ソースを、自分のアクセスに基づいて任意の SIEM または監視ツールと統合します。

このプロセスを効率化するには、Azure AD ユーザー アカウントの診断設定を作成し、監査ログとサインイン ログを Log Analytics ワークスペースに送信します。 Log Analytics ワークスペース内で任意のアラートを構成できます。

- [Azure アクティビティ ログを Azure Monitor に統合する方法](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: アカウント ログイン動作の偏差に関するアラートを生成する

**ガイダンス**: Azure Active Directory (Azure AD) のリスクおよび Identity Protection 機能を使用して、ユーザー ID に関連して検出された疑わしいアクションに対する自動応答を構成します。 さらに詳しく調査するために、Azure Sentinel にデータを取り込みます。

- [Azure AD の危険なサインインを表示する方法](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Identity Protection のリスク ポリシーを構成して有効にする方法](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Azure Sentinel をオンボードする方法](../sentinel/quickstart-onboard.md)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="data-protection"></a>データ保護

*詳細については、[Azure セキュリティ ベンチマークの「データ保護](../security/benchmarks/security-control-data-protection.md)」を参照してください。*

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4:転送中のすべての機密情報を暗号化する

**ガイダンス**: Azure リソースに接続しているすべてのクライアントが TLS 1.2 以上を確実にネゴシエートできるようにします。

該当する場合、保存時の暗号化と転送中の暗号化に関する Azure Security Center の推奨事項に従います。

- [Azure での転送中の暗号化の概要](https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4.6:Azure RBAC を使用してリソースへのアクセスを制御する

**ガイダンス**:Azure RBAC を使用してロード バランサー リソースへのアクセスを制御します。

- [Azure RBAC を構成する方法](../role-based-access-control/role-assignments-portal.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7:ホストベースのデータ損失防止を使用してアクセス制御を実施する

**ガイダンス**:ロード バランサーは、顧客データを格納しないパススルー サービスです。 これは、Microsoft によって管理される、基盤となるプラットフォームの一部です。 

Microsoft では、顧客のすべてのコンテンツを機密として扱い、顧客データを損失や漏洩から保護するためにあらゆる手段を尽くしています。 

Azure 内の顧客データが確実にセキュリティで保護されるように、Microsoft では一連の堅牢なデータ保護制御および機能を実装して管理しています。 

- [Azure での顧客データの保護について](../security/fundamentals/protection-customer-data.md)

**責任**: 共有

**Azure Security Center の監視**: なし

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9:重要な Azure リソースへの変更に関するログとアラート

**ガイダンス**:Azure アクティビティ ログで Azure Monitor を使用して、重要な運用ワークロードに使用するロード バランサーなど、重要な Azure リソースに変更が加えられたときのアラートを作成します。

- [Azure アクティビティ ログ イベントのアラートを作成する方法](../azure-monitor/alerts/alerts-activity-log.md)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="inventory-and-asset-management"></a>インベントリと資産の管理

*詳細については、[Azure セキュリティ ベンチマークの「インベントリと資産の管理](../security/benchmarks/security-control-inventory-asset-management.md)」を参照してください。*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1:自動化された資産検出ソリューションを使用する

**ガイダンス**:Azure Resource Graph を使用して、サブスクリプション内のすべてのリソース (コンピューティング、ストレージ、ネットワーク、ポート、プロトコルなど) のクエリおよび検出を行います。 現在のリソースを作成して使用するには、Azure Resource Manager をお勧めします。

テナントで適切な (読み取り) アクセス許可を確認し、サブスクリプション内のリソースだけでなく、すべての Azure サブスクリプションを列挙します。

- [Azure Resource Graph Explorer を使用してクエリを作成する方法](../governance/resource-graph/first-query-portal.md)

- [Azure サブスクリプションを表示する方法](/powershell/module/az.accounts/get-azsubscription)

- [Azure RBAC について](../role-based-access-control/overview.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="62-maintain-asset-metadata"></a>6.2:資産メタデータを保持する

**ガイダンス**:メタデータを使用して Azure リソースにタグを適用し、それらを各分類に従って論理的に整理します。

- [タグを作成して使用する方法](/azure/azure-resource-manager/resource-group-using-tags)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="63-delete-unauthorized-azure-resources"></a>6.3:承認されていない Azure リソースを削除する

**ガイダンス**: 必要に応じて、タグ付け、管理グループ、および個別のサブスクリプションを使用して、資産の整理と追跡を行います。 

定期的にインベントリを調整し、承認されていないリソースがサブスクリプションから適切なタイミングで削除されるようにします。

- [追加の Azure サブスクリプションを作成する方法](../cost-management-billing/manage/create-subscription.md)

- [管理グループを作成する方法](../governance/management-groups/create-management-group-portal.md)

- [タグを作成して使用する方法](../azure-resource-manager/management/tag-resources.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4:承認された Azure リソースのインベントリを定義および管理する

**ガイダンス**: 許可リストのメカニズムとして利用できる組織のニーズに応じて、承認された Azure リソースの一覧を作成します。 これにより、組織は、組織の一般的なセキュリティ評価プロセスによって正式にレビューおよび承認された後に、新しく利用可能な Azure サービスをオンボードすることができます。

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5:承認されていない Azure リソースを監視する

**ガイダンス**: Azure Policy を使用して、サブスクリプションで作成できるリソースの種類に制限を設けます。

Azure Resource Graph を使用して、所有するサブスクリプション内の Azure Resource Graphのクエリおよび検出を行います。 

環境に存在するすべての Azure リソースが承認されていることを確認します。

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

- [Azure Resource Graph Explorer を使用してクエリを作成する方法](../governance/resource-graph/first-query-portal.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="69-use-only-approved-azure-services"></a>6.9:承認された Azure サービスのみを使用する

**ガイダンス**: 次の組み込みのポリシー定義を使用して、顧客のサブスクリプション内に作成できるリソースの種類に制限を適用するには、Azure Policy を使用します。
- 許可されないリソースの種類
- 許可されるリソースの種類

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy を使用して特定のリソースの種類を拒否する方法](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

- [仮想ネットワークに対する Azure Policy のサンプルの組み込み](/azure/virtual-network/policy-samples)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11:Azure Resource Manager を操作するユーザーの機能を制限する

**ガイダンス**: "Microsoft Azure Management" アプリに対して [アクセスのブロック] を構成することによって、Azure Resource Manager を操作するユーザーの機能を制限するには、Azure Active Directory (Azure AD) 条件付きアクセスを使用します。

- [条件付きアクセスを構成して Azure Resource Manager へのアクセスをブロックする方法](../role-based-access-control/conditional-access-azure-management.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13:リスクの高いアプリケーションを物理的または論理的に分離する

**ガイダンス**:業務に必要であっても、組織のリスクが高くなる可能性があるソフトウェアは、独自の仮想マシンや仮想ネットワーク内に隔離し、Azure Firewall またはネットワーク セキュリティ グループで十分に保護する必要があります。

- [仮想ネットワークを作成する方法](../virtual-network/quick-create-portal.md)

- [セキュリティ構成を使用してネットワーク セキュリティ グループを作成する方法: ](../virtual-network/tutorial-filter-network-traffic.md)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="secure-configuration"></a>セキュリティで保護された構成

*詳細については、[Azure セキュリティ ベンチマークの「セキュリティで保護された構成](../security/benchmarks/security-control-secure-configuration.md)」を参照してください。*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1:すべての Azure リソースに対してセキュリティで保護された構成を確立する

**ガイダンス**: ご利用の Azure リソースの構成を監査または適用するには、Azure Policy エイリアスを使用してカスタム ポリシーを作成します。 組み込みの Azure Policy 定義を使用します。

Azure Resource Manager には、JavaScript Object Notation (JSON) でテンプレートをエクスポートする機能があり、構成が確実に組織のセキュリティ要件を満たすように確認する必要があります。

Azure Resource Manager テンプレートを JavaScript Object Notation (JSON) 形式にエクスポートし、定期的に確認して、構成が組織のセキュリティ要件を満たしていることを確認します。

Azure リソースの安全な構成基準として Security Center からの推奨事項を実装します。

- [使用可能な Azure Policy エイリアスを表示する方法](/powershell/module/az.resources/get-azpolicyalias)

- [チュートリアル:コンプライアンスを強制するポリシーの作成と管理](../governance/policy/tutorials/create-and-manage.md)

- [Azure portal のテンプレートへの単一および複数リソースのエクスポート](../azure-resource-manager/templates/export-template-portal.md)

- [セキュリティの推奨事項 - リファレンス ガイド](../security-center/recommendations-reference.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3:セキュリティで保護された Azure リソースの構成を維持する

**ガイダンス**: Azure リソース全体にセキュリティで保護された設定を適用するには、Azure Policy の [deny] と [deploy if not exist] を使用します。  また、Azure Resource Manager テンプレートを使用して、組織に必要な Azure リソースのセキュリティ構成を維持できます。 

- [Azure Policy の効果について](../governance/policy/concepts/effects.md)

- [コンプライアンスを強制するポリシーの作成と管理](../governance/policy/tutorials/create-and-manage.md)

- [Azure Resource Manager テンプレートの概要](../azure-resource-manager/templates/overview.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5:Azure リソースの構成を安全に格納する

**ガイダンス**:カスタム Azure Policy 定義、Azure Resource Manager テンプレート、Desired State Configuration スクリプトなどのコードを安全に格納して管理するには、Azure DevOps を使用します。

Azure DevOps と統合されている場合は Azure Active Directory (Azure AD)、または TFS と統合されている場合は Azure AD で定義されている特定のユーザー、組み込みのセキュリティ グループ、またはグループにアクセス許可を付与または拒否できます。

- [Azure DevOps でコードを格納する方法](/azure/devops/repos/git/gitworkflow)

- [Azure DevOps でのアクセス許可とグループについて](/azure/devops/organizations/security/about-permissions)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7:Azure リソース用の構成管理ツールをデプロイする

**ガイダンス**:Azure Policy を使用して、Azure リソースの標準的なセキュリティ構成を定義して実装します。  ご利用の Azure リソースのネットワーク構成を監査または適用するには、Azure Policy エイリアスを使用してカスタム ポリシーを作成します。 また、特定の Azure Load Balancer リソースに関連する組み込みのポリシー定義を実装します。  また、Azure Automation を使用して、構成の変更をデプロイすることもできます。 Azure Load Balancer のリソースに対して

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

- [エイリアスを使用する方法](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9:Azure リソースの自動構成監視を実装する

**ガイダンス**:セキュリティ センターを使用して、Azure リソースおよび Azure ポリシーのベースラインスキャンを実行し、リソース構成をアラートおよび監査します。

- [Azure Security Center の推奨事項を修復する方法](../security-center/security-center-remediate-recommendations.md)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="incident-response"></a>インシデント対応

*詳細については、[Azure セキュリティ ベンチマークの「インシデント対応](../security/benchmarks/security-control-incident-response.md)」を参照してください。*

### <a name="101-create-an-incident-response-guide"></a>10.1:インシデント対応ガイドを作成する

**ガイダンス**: 組織のインシデント対応ガイドを作成します。 要員のすべてのロールを定義するインシデント対応計画が記述されていることと、検出からインシデント後のレビューまでのインシデント対応/管理のフェーズがあることを確認します。 

- [Azure Security Center 内でワークフロー自動化を構成する方法](../security-center/security-center-planning-and-operations-guide.md) 

- [独自のセキュリティ インシデント対応プロセスを構築するためのガイダンス](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 

- [Microsoft Security Response Center のインシデントの構造](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 

- [お客様は、独自のインシデント対応計画の作成に役立つ NIST の「コンピューター セキュリティ インシデント対応ガイド」を利用することもできます](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2:インシデントのスコアリングと優先順位付けの手順を作成する

**ガイダンス**: Security Center によって各アラートに重大度が割り当てられるため、最初に調査する必要があるアラートの優先順位付けに役立ちます。 

重要度は、アラートの発行に使用された Security Center の信頼度と、アラートの原因となったアクティビティの背後に悪意のある意図があったかどうかの信頼レベルに基づいて決まります。

タグを使用してサブスクリプションをマークし、Azure リソース (特に、機密データを処理するもの) を識別して分類するための命名システムを作成します。  

インシデントが発生した Azure リソースと環境の重要度に基づいて、アラートの修復に優先順位を付けることは、お客様の責任です。

- [Security alerts in Azure Security Center](../security-center/security-center-alerts-overview.md)

- [タグを使用した Azure リソースの整理](../azure-resource-manager/management/tag-resources.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="103-test-security-response-procedures"></a>10.3:セキュリティ対応手順のテスト

**ガイダンス**: ご利用のシステムのインシデント対応機能を定期的にテストする演習を実施することで、ご利用の Azure リソースの保護を支援できます。 弱点やギャップを特定し、必要に応じて対応計画を見直します。 

- [NIST の出版物 -- IT 計画と機能に関するテスト、トレーニング、および演習プログラムのガイド](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4:セキュリティ インシデントの連絡先の詳細を指定し、セキュリティ インシデントのアラート通知を構成します

**ガイダンス**:セキュリティ インシデントの連絡先情報は、Microsoft Security Response Center (MSRC) でユーザーのデータが違法または権限のないユーザーによってアクセスされたことが検出された場合に、Microsoft からの連絡先として使用されます。 事後にインシデントをレビューして、問題が解決されていることを確認します。 

- [Azure Security Center のセキュリティ連絡先を設定する方法](../security-center/security-center-provide-security-contact-details.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5:インシデント対応システムにセキュリティ アラートを組み込む

**ガイダンス**:連続エクスポート機能を使用して セキュリティ センターのアラートと推奨事項をエクスポートすると、Azure リソースに対するリスクを特定できます。 

セキュリティ センサーの連続エクスポート機能を使用すると、アラートと推奨事項を手動で、または継続した連続的な方法でエクスポートできます。 

Security Center データ コネクタを使用してアラートを Azure Sentinel にストリーミングできます。

- [連続エクスポートを構成する方法](../security-center/continuous-export.md)

- [Azure Sentinel にアラートをストリーミングする方法](../sentinel/connect-azure-security-center.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="106-automate-the-response-to-security-alerts"></a>10.6:セキュリティ アラートへの対応を自動化する

**ガイダンス**:セキュリティ センターのワークフロー自動化機能を使用すると、セキュリティのアラートと推奨事項に対して自動的に応答をトリガーし、Azure リソースを保護できます。

- [セキュリティ センターでワークフロー自動化を構成する方法](../security-center/workflow-automation.md)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="penetration-tests-and-red-team-exercises"></a>侵入テストとレッド チーム演習

*詳細については、[Azure セキュリティ ベンチマークの「侵入テストとレッド チーム演習](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)」を参照してください。*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1:Azure リソースの通常の侵入テストを実施し、セキュリティに関する重大な調査結果がすべて、確実に修復されるようにする

**ガイダンス**: お客様の侵入テストが Microsoft のポリシーに違反しないように、Microsoft クラウド侵入テストの実施ルールに従ってください。 Microsoft が管理しているクラウド インフラストラクチャ、サービス、アプリケーションに対する Red Teaming およびライブ サイト侵入テストに関する Microsoft の戦略と実施を活用してください。 

- [侵入テストの実施ルール](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="next-steps"></a>次のステップ

- 「[Azure セキュリティ ベンチマーク V2 の概要](/azure/security/benchmarks/overview)」を参照してください。
- [Azure セキュリティ ベースライン](/azure/security/benchmarks/security-baselines-overview)の詳細について学習する
