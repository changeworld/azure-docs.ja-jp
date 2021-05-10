---
title: Synapse Analytics 用の Azure セキュリティ ベースライン
description: Synapse Analytics セキュリティ ベースラインでは、Azure セキュリティ ベンチマークで指定されているセキュリティに関する推奨事項を実装するための手順のガイダンスとリソースが提供されます。
author: msmbaldwin
ms.service: synapse-analytics
ms.subservice: security
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 1a426a86ddb25733b8425c5887d2c0522f915e2d
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/17/2021
ms.locfileid: "107587716"
---
# <a name="azure-security-baseline-for-synapse-analytics"></a>Synapse Analytics 用の Azure セキュリティ ベースライン

このセキュリティ ベースラインにより、[Azure セキュリティ ベンチマーク バージョン 1.0](../security/benchmarks/overview-v1.md) のガイダンスが Synapse Analytics に適用されます。 Azure セキュリティ ベンチマークには、Azure 上のクラウド ソリューションをセキュリティで保護する方法に関する推奨事項がまとめてあります。
その内容は、Azure セキュリティ ベンチマークによって、および Synapse Analytics に適用できる関連ガイダンスによって定義される **セキュリティ制御** によってグループ化されています。 Synapse Analytics に適用できない **制御** は、除外されています。

 
Synapse Analytics を Azure セキュリティ ベンチマークに完全にマップする方法については、[Synapse Analytics セキュリティ ベースライン マッピング ファイル完全版](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)を参照してください。

## <a name="network-security"></a>ネットワークのセキュリティ

*詳細については、[Azure セキュリティ ベンチマークの「ネットワークのセキュリティ](../security/benchmarks/security-control-network-security.md)」を参照してください。*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1:仮想ネットワーク内の Azure リソースを保護する

**ガイダンス**: Private Link を使用することで仮想ネットワークに対して Azure SQL Server をセキュリティで保護します。 Azure Private Link を使用すると、仮想ネットワーク内のプライベート エンドポイント経由で、Azure PaaS サービスにアクセスできます。 仮想ネットワークとサービスの間のトラフィックは、Microsoft のバックボーン ネットワークを通ります。

または、Synapse SQL プールに接続するときに、ネットワーク セキュリティ グループを使用して、SQL データベースへの送信接続の範囲を絞り込みます。 [Allow Azure Services]\(Azure サービスを許可する\) を [オフ] に設定して、パブリック エンドポイント経由で SQL Database へのすべての Azure サービス トラフィックを無効にします。 ファイアウォール規則でパブリック IP アドレスが許可されていないことを確認します。

- [Azure Private Link について](../private-link/private-link-overview.md)

- [Azure Synapse SQL に対する Private Link について](../azure-sql/database/private-endpoint-overview.md)

- [仮想ネットワークを作成する方法](../virtual-network/quick-create-portal.md)

- [セキュリティ構成を使用して NSG を作成する方法](../virtual-network/tutorial-filter-network-traffic.md)

**責任**: Customer

**Azure Security Center の監視**: [Azure セキュリティ ベンチマーク](/azure/governance/policy/samples/azure-security-benchmark)は、Security Center の既定のポリシー イニシアチブであり、[Security Center の推奨事項](/azure/security-center/security-center-recommendations)の基礎となります。 このコントロールに関連する Azure Policy 定義は、Security Center によって自動的に有効になります。 この制御に関連するアラートでは、関連するサービスのために [Azure Defender](/azure/security-center/azure-defender) プランが必要になる場合があります。

**Azure Policy 組み込み定義 - Microsoft.Sql**:

[!INCLUDE [Resource Policy for Microsoft.Sql 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.sql-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2:仮想ネットワーク、サブネット、ネットワーク インターフェイスの構成とトラフィックを監視してログに記録する

**ガイダンス**:専用 SQL プールに接続するとき、ネットワーク セキュリティ グループ (NSG) フロー ログを有効にしてある場合は、トラフィックの監査のために Azure ストレージ アカウントにログが送信されます。

また、NSG フロー ログを Log Analytics ワークスペースに送信し、Traffic Analytics を使用して Azure クラウド内のトラフィック フローに関する分析情報を提供することもできます。 Traffic Analytics のいくつかの利点として、ネットワーク アクティビティを視覚化してホット スポットを特定したり、セキュリティの脅威を識別したり、トラフィック フロー パターンを把握したり、ネットワークの誤った構成の正確な場所を特定したりする機能が挙げられます。

- [NSG フロー ログを有効にする方法](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Azure Security Center によって提供されるネットワークのセキュリティについて](../security-center/security-center-network-recommendations.md)

- [Traffic Analytics を有効にして使用する方法](../network-watcher/traffic-analytics.md)

- [Azure Security Center によって提供されるネットワークのセキュリティについて](../security-center/security-center-network-recommendations.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4:既知の悪意のある IP アドレスとの通信を拒否する

**ガイダンス**: Azure Synapse SQL に対して Advanced Threat Protection (ATP) を使用します。 ATP では、データベースへのアクセスやデータベースの悪用を試みる、害を及ぼす可能性のある異常なアクティビティが検出され、"SQL インジェクションの可能性" や "通常とは異なる場所からのアクセス" などのさまざまなアラートをトリガーできます。 ATP は、Advanced Data Security (ADS) オファリングの一部であり、中央の SQL ADS ポータルからアクセスして管理することができます。

分散型サービス拒否攻撃からの保護のために、Azure Synapse SQL に関連付けられている仮想ネットワークで DDoS Protection Standard を有効にします。 Azure Security Center の統合された脅威インテリジェンスを使用して、既知の悪意のある、または未使用のインターネット IP アドレスとの通信を拒否します。

- [Azure Synapse SQL 用の ATP について](../azure-sql/database/threat-detection-overview.md)

- [Azure SQL Database 向け Advanced Data Security を有効にする方法](../azure-sql/database/azure-defender-for-sql.md)

- [ADS の概要](../azure-sql/database/azure-defender-for-sql.md)

- [DDoS 保護を構成する方法](../ddos-protection/manage-ddos-protection.md)

- [Azure Security Center の統合された脅威インテリジェンスについて](../security-center/azure-defender.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="15-record-network-packets"></a>1.5:ネットワーク パケットを記録する

**ガイダンス**:専用 SQL プールに接続するとき、ネットワーク セキュリティ グループ (NSG) フロー ログを有効にしてある場合は、トラフィックの監査のためにログが Azure ストレージ アカウントに送信されます。 フロー ログは、Log Analytics ワークスペースに送信したり、Event Hubs にストリーミングしたりすることもできます。  異常なアクティビティを調査する必要がある場合は、Network Watcher パケット キャプチャを有効にします。

- [NSG フロー ログを有効にする方法](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Network Watcher を有効にする方法](../network-watcher/network-watcher-create.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6:ネットワーク ベースの侵入検出/侵入防止システム (IDS/IPS) をデプロイする

**ガイダンス**: Azure Synapse SQL に対して Advanced Threat Protection (ATP) を使用します。 ATP では、データベースへのアクセスやデータベースの悪用を試みる、害を及ぼす可能性のある異常なアクティビティが検出され、"SQL インジェクションの可能性" や "通常とは異なる場所からのアクセス" などのさまざまなアラートをトリガーできます。 ATP は、Advanced Data Security (ADS) オファリングの一部であり、中央の SQL ADS ポータルからアクセスして管理することができます。 ATP ではまた、アラートも Azure Security Center と統合されます。

- [Azure Synapse SQL 用の ATP について](../azure-sql/database/threat-detection-overview.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8:ネットワーク セキュリティ規則の複雑さと管理オーバーヘッドを最小限に抑える

**ガイダンス**: ネットワーク セキュリティ グループまたは Azure Firewall でのネットワーク アクセス制御を定義するには、仮想ネットワーク サービス タグを使用します。 セキュリティ規則を作成するときは、特定の IP アドレスの代わりにサービス タグを使うことができます。 規則の適切なソースまたは宛先フィールドにサービス タグ名 (ApiManagement など) を指定することにより、対応するサービスのトラフィックを許可または拒否できます。 サービス タグに含まれるアドレス プレフィックスの管理は Microsoft が行い、アドレスが変化するとサービス タグは自動的に更新されます。

専用 SQL プールに対してサービス エンドポイントを使用するときは、Azure SQL データベースのパブリック IP アドレスへの送信が必要です。ネットワーク セキュリティ グループ (NSG) は、接続を許可するために Azure SQL Database IP に対して開かれている必要があります。 これは、Azure SQL Database 用の NSG サービス タグを使用して行うことができます。

- [Azure SQL Database 用のサービス エンドポイントのサービス タグの概要](https://docs.microsoft.com/azure/azure-sql/database/vnet-service-endpoint-rule-overview#limitations)

- [サービス タグとその使用方法の概要](../virtual-network/service-tags-overview.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9:ネットワーク デバイスの標準的なセキュリティ構成を維持する

**ガイダンス**:Azure Policy を使用して、専用 SQL プールに関連するリソースに対するネットワーク セキュリティ構成を定義して実装します。 "Microsoft.Sql" 名前空間を使用してカスタム ポリシー定義を定義するか、または Azure SQL Database およびサーバーのネットワーク保護のために設計されたいずれかの組み込みのポリシー定義を使用できます。 Azure SQL Database サーバーに適用できる組み込みのネットワーク セキュリティ ポリシーの例を次に示します。"SQL Server は仮想ネットワーク サービス エンドポイントを使用する必要がある"。

Azure Blueprints を使用して、Azure Resource Management テンプレート、Azure ロールベースのアクセス制御 (Azure RBAC)、ポリシーなどの主要な環境成果物を単一のブループリント定義にパッケージ化することによって大規模な Azure デプロイを簡略化します。 ブループリントを新しいサブスクリプションと環境に簡単に適用し、バージョン管理によって制御と管理を微調整します。

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

- [Azure Blueprint を作成する方法](../governance/blueprints/create-blueprint-portal.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="110-document-traffic-configuration-rules"></a>1.10:トラフィック構成規則を文書化する

**ガイダンス**: ネットワーク セキュリティおよびトラフィック フローに関連したネットワーク セキュリティ グループ (NSG) やその他のリソースにタグを使用します。 個々の NSG 規則については、[説明] フィールドを使用して、ネットワークとの間のトラフィックを許可する規則のビジネス ニーズや期間 (など) を指定します。

すべてのリソースが確実にタグ付きで作成され、既存のタグ付けされていないリソースがユーザーに通知されるようにするには、タグ付けに関連したいずれかの組み込みの Azure Policy 定義 ("タグとその値が必要" など) を使用します。

Azure PowerShell または Azure CLI を使用して、タグに基づいてリソースを検索したり、リソースに対するアクションを実行したりすることもできます。

- [タグを作成して使用する方法](../azure-resource-manager/management/tag-resources.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11:自動化ツールを使用してネットワーク リソース構成を監視し、変更を検出する

**ガイダンス**:ネットワーク リソース構成を監視し、専用 SQL プールに関連したネットワーク リソースの変更を検出するには、Azure アクティビティ ログを使用します。 重要なネットワーク リソースへの変更が発生するとトリガーされる Azure Monitor 内のアラートを作成します。

- [Azure アクティビティ ログ イベントを表示して取得する方法](https://docs.microsoft.com/azure/azure-monitor/essentials/activity-log#view-the-activity-log)

- [Azure Monitor でアラートを作成する方法](../azure-monitor/alerts/alerts-activity-log.md)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="logging-and-monitoring"></a>ログ記録と監視

*詳細については、[Azure セキュリティ ベンチマークの「ログ記録と監視](../security/benchmarks/security-control-logging-monitoring.md)」を参照してください。*

### <a name="22-configure-central-security-log-management"></a>2.2:セキュリティ ログの一元管理を構成する

**ガイダンス**:特定のデータベースに対して、または (Azure Synapse をホストする) Azure の既定のサーバー ポリシーとして、監査ポリシーを定義できます。 サーバー ポリシーがサーバー上にある既存と新規作成のすべてのデータベースに適用されます。

サーバー監査が有効な場合は、常にデータベースに適用されます。 データベース監査設定に関係なく、データベースが監査されます。

監査を有効にすると、Azure ストレージ アカウント、Log Analytics ワークスペース、または Event Hubs の監査ログにそれを書き込むことができます。

または、Azure Sentinel またはサードパーティの SIEM に対してデータを有効にしてオンボードすることもできます。

- [Azure SQL リソースの監査を設定する方法](https://docs.microsoft.com/azure/azure-sql/database/auditing-overview#server-vs-database-level)

- [Azure Sentinel をオンボードする方法](../sentinel/quickstart-onboard.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3:Azure リソースの監査ログ記録を有効にする

**ガイダンス**:専用 SQL プールに対して Azure SQL サーバーレベルで監査を有効にし、監査ログの保存場所 (Azure Storage、Log Analytics、または Event Hubs) を選択します。

監査は、データベース レベルとサーバー レベルの両方で有効にすることができ、特定のデータベースに対して個別のデータ シンクまたは保有期間を構成する必要がある場合を除き、サーバーレベルでのみ有効にすることをお勧めします。

- [Azure SQL Database の監査を有効にする方法](../azure-sql/database/auditing-overview.md)

- [サーバーの監査を有効にする方法](https://docs.microsoft.com/azure/azure-sql/database/auditing-overview#setup-auditing)

- [サーバーレベルとデータベース レベルの監査ポリシーの違い](https://docs.microsoft.com/azure/azure-sql/database/auditing-overview#server-vs-database-level)

**責任**: Customer

**Azure Security Center の監視**: [Azure セキュリティ ベンチマーク](/azure/governance/policy/samples/azure-security-benchmark)は、Security Center の既定のポリシー イニシアチブであり、[Security Center の推奨事項](/azure/security-center/security-center-recommendations)の基礎となります。 このコントロールに関連する Azure Policy 定義は、Security Center によって自動的に有効になります。 この制御に関連するアラートでは、関連するサービスのために [Azure Defender](/azure/security-center/azure-defender) プランが必要になる場合があります。

**Azure Policy 組み込み定義 - Microsoft.Sql**:

[!INCLUDE [Resource Policy for Microsoft.Sql 2.3](../../includes/policy/standards/asb/rp-controls/microsoft.sql-2-3.md)]

### <a name="25-configure-security-log-storage-retention"></a>2.5:セキュリティ ログのストレージ保持を構成する

**ガイダンス**:専用 SQL プールに関連するログをストレージ アカウント、Log Analytics ワークスペース、またはイベント ハブに格納する場合は、組織のコンプライアンス規則に従ってログの保有期間を設定します。

- [Azure Blob Storage のライフサイクルの管理](../storage/blobs/storage-lifecycle-management-concepts.md)

- [Log Analytics ワークスペースのログ保持パラメーターを設定する方法](https://docs.microsoft.com/azure/azure-monitor/logs/manage-cost-storage#change-the-data-retention-period)

- [Event Hubs でストリーミング イベントをキャプチャする](../event-hubs/event-hubs-capture-overview.md)

**責任**: Customer

**Azure Security Center の監視**: [Azure セキュリティ ベンチマーク](/azure/governance/policy/samples/azure-security-benchmark)は、Security Center の既定のポリシー イニシアチブであり、[Security Center の推奨事項](/azure/security-center/security-center-recommendations)の基礎となります。 このコントロールに関連する Azure Policy 定義は、Security Center によって自動的に有効になります。 この制御に関連するアラートでは、関連するサービスのために [Azure Defender](/azure/security-center/azure-defender) プランが必要になる場合があります。

**Azure Policy 組み込み定義 - Microsoft.Sql**:

[!INCLUDE [Resource Policy for Microsoft.Sql 2.5](../../includes/policy/standards/asb/rp-controls/microsoft.sql-2-5.md)]

### <a name="26-monitor-and-review-logs"></a>2.6:ログを監視して確認する

**ガイダンス**: 異常な動作がないかどうかログを分析および監視し、結果を定期的に確認します。 SQL Database に関連した異常なアクティビティに対してアラートを生成するには、Azure Security Center と共に Azure SQL Database 用の Advanced Threat Protection を使用します。 または、SQL Database に関連したメトリック値または Azure アクティビティ ログ エントリに基づいてアラートを構成します。

または、Azure Sentinel またはサードパーティの SIEM に対してデータを有効にしてオンボードすることもできます。

- [Advanced Threat Protection および Azure SQL Database に関するアラートの概要](../azure-sql/database/threat-detection-overview.md)

- [Azure SQL Database 向け Advanced Data Security を有効にする方法](../azure-sql/database/azure-defender-for-sql.md)

- [Azure SQL Database に関するカスタム アラートを構成する方法](../azure-sql/database/alerts-insights-configure-portal.md)

- [Azure Sentinel をオンボードする方法](../sentinel/quickstart-onboard.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7:異常なアクティビティについてのアラートを有効にする

**ガイダンス**: 異常なアクティビティを監視してアラートを生成するには、Azure Security Center と共に Azure SQL Database 用の Advanced Threat Protection (ATP) を使用します。 ATP は、Advanced Data Security (ADS) オファリングの一部であり、ポータルの中央 SQL ADS からアクセスして管理することができます。 ADS には、機密データの検出と分類、データベースの潜在的な脆弱性の検出と軽減、データベースへの脅威を示す可能性がある異常なアクティビティの検出を行う機能が含まれています。

または、Azure Sentinel に対してデータを有効にしてオンボードすることもできます。

- [Advanced Threat Protection および Azure SQL Database に関するアラートの概要](../azure-sql/database/threat-detection-overview.md)

- [Azure SQL Database 向け Advanced Data Security を有効にする方法](../azure-sql/database/azure-defender-for-sql.md)

- [Azure Security Center でアラートを管理する方法](../security-center/security-center-managing-and-responding-alerts.md)

- [Azure Sentinel をオンボードする方法](../sentinel/quickstart-onboard.md)

**責任**: Customer

**Azure Security Center の監視**: [Azure セキュリティ ベンチマーク](/azure/governance/policy/samples/azure-security-benchmark)は、Security Center の既定のポリシー イニシアチブであり、[Security Center の推奨事項](/azure/security-center/security-center-recommendations)の基礎となります。 このコントロールに関連する Azure Policy 定義は、Security Center によって自動的に有効になります。 この制御に関連するアラートでは、関連するサービスのために [Azure Defender](/azure/security-center/azure-defender) プランが必要になる場合があります。

**Azure Policy 組み込み定義 - Microsoft.Sql**:

[!INCLUDE [Resource Policy for Microsoft.Sql 2.7](../../includes/policy/standards/asb/rp-controls/microsoft.sql-2-7.md)]

## <a name="identity-and-access-control"></a>ID およびアクセス制御

*詳細については、[Azure セキュリティ ベンチマークの「ID およびアクセス制御](../security/benchmarks/security-control-identity-access-control.md)」を参照してください。*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: 管理アカウントのインベントリを維持する

**ガイダンス**: ユーザーは、Azure Active Directory (Azure AD) または SQL 認証のいずれかを使用して認証されます。

Azure SQL を初めてデプロイするときに、管理者ログインと、そのログインに関連付けられているパスワードを指定します。 この管理者アカウントは、サーバー管理者と呼ばれます。Azure portal を開き、サーバーやマネージド インスタンスの [プロパティ] タブに移動することで、データベースの管理者アカウントを確認できます。 また、完全な管理アクセス許可を持つ Azure AD 管理者アカウントを構成することもできます。これは、Azure AD 認証を有効にする場合に必要です。

管理操作には、明示的に割り当てる必要がある Azure の組み込みロールを使用します。 Azure AD PowerShell モジュールを使用してアドホック クエリを実行し、管理グループのメンバーであるアカウントを検出します。

- [SQL Database の認証](https://docs.microsoft.com/azure/azure-sql/database/security-overview#authentication)

- [管理者以外のユーザーのアカウントを作成する](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage#create-accounts-for-non-administrator-users)

- [認証用に Azure AD アカウントを使用する](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage#create-additional-logins-and-users-having-administrative-permissions)

- [PowerShell を使用して Azure AD でディレクトリ ロールを取得する方法](/powershell/module/azuread/get-azureaddirectoryrole)

- [PowerShell を使用して Azure AD でディレクトリ ロールのメンバーを取得する方法](/powershell/module/azuread/get-azureaddirectoryrolemember)

- [Azure SQL で既存のログインと管理者アカウントを管理する方法](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage#existing-logins-and-user-accounts-after-creating-a-new-database)

- [Azure 組み込みロール](../role-based-access-control/built-in-roles.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="32-change-default-passwords-where-applicable"></a>3.2: 既定のパスワードを変更する (該当する場合)

**ガイダンス**: Azure Active Directory (Azure AD) には、既定のパスワードの概念がありません。 専用 SQL プールをプロビジョニングしている場合は、認証と Azure AD を統合することをお勧めします。 この認証方法では、ユーザーは、ユーザー アカウント名の送信を行い、Azure AD に格納されている資格情報がサービスで使用されるように要求します。

- [Azure SQL での Azure AD 認証を構成して管理する方法](../azure-sql/database/authentication-aad-configure.md)

- [Azure SQL での認証について](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage#existing-logins-and-user-accounts-after-creating-a-new-database)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: 専用管理者アカウントを使用する

**ガイダンス**: 専用管理者アカウントの使用に関するポリシーと手順を作成します。 Azure Security Center ID とアクセス管理を使用して、Azure Active Directory (Azure AD) 経由でサインインする管理者アカウントの数を監視します。

データベースの管理者アカウントを確認するには、Azure portal を開き、お使いのサーバーやマネージド インスタンスの [プロパティ] タブに移動します。

- [Azure Security Center ID とアクセスについて](../security-center/security-center-identity-access.md)

- [Azure SQL で既存のログインと管理者アカウントを管理する方法](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage#existing-logins-and-user-accounts-after-creating-a-new-database)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Azure Active Directory シングル サインオン (SSO) を使用する

**ガイダンス**: Azure アプリの登録 (サービス プリンシパル) を使用して、API 呼び出しを介してコントロール プレーン (Azure portal) のデータ ウェアハウスとやりとりするために使用できるトークンを取得します。

- [Azure REST API を呼び出す方法](/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)

- [クライアント アプリケーション (サービス プリンシパル) を Azure Active Directory (Azure AD) に登録する方法](/rest/api/azure/#register-your-client-application-with-azure-ad)

- [Azure Synapse SQL REST API の情報](sql-data-warehouse/sql-data-warehouse-manage-compute-rest-api.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: すべての Azure Active Directory ベースのアクセスに多要素認証を使用する

**ガイダンス**: Azure Active Directory (Azure AD) の多要素認証を有効にし、Azure Security Center の ID およびアクセス管理の推奨事項に従います。

- [Azure で多要素認証を有効にする方法](../active-directory/authentication/howto-mfa-getstarted.md)

- [Azure Security Center で ID とアクセスを監視する方法](../security-center/security-center-identity-access.md)

- [Azure SQL での多要素認証について](../azure-sql/database/authentication-mfa-ssms-overview.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3.6: セキュリティで保護された Azure マネージド ワークステーションを管理タスクに使用する

**ガイダンス**: 多要素認証が構成された特権アクセス ワークステーション (PAW) を使用して Azure リソースにログインし、そのリソースを構成します。

- [特権アクセス ワークステーションについて](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Azure で多要素認証を有効にする方法](../active-directory/authentication/howto-mfa-getstarted.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: 管理者アカウントからの疑わしいアクティビティに関するログとアラート

**ガイダンス**: 環境内で疑わしいアクティビティまたは安全でないアクティビティが発生したときに、Azure Active Directory (Azure AD) セキュリティ レポートを使用して、ログおよびアラートを生成します。

データベースへのアクセスや悪用のための異常で、かつ有害なおそれのある試みを示す異常なアクティビティを検出してアラートを生成するには、Azure Security Center と共に Azure SQL Database 用の Advanced Threat Protection を使用します。

SQL Server 監査では、サーバー監査を作成できます。これには、サーバー レベルのイベント用のサーバー監査の仕様と、データベース レベルのイベント用のデータベース監査仕様を含めることができます。 監査イベントは、イベント ログまたは監査ファイルへ書き込むことができます。

- [危険なアクティビティのフラグが設定された Azure AD ユーザーを識別する方法](../active-directory/identity-protection/overview-identity-protection.md)

- [Azure Security Center でユーザーの ID およびアクセス アクティビティを監視する方法](../security-center/security-center-identity-access.md)

- [Advanced Threat Protection および潜在的なアラートを確認する](https://docs.microsoft.com/azure/azure-sql/database/threat-detection-overview#alerts)

- [Azure SQL でのログインとユーザー アカウントについて](../azure-sql/database/logins-create-manage.md)

- [SQL Server の監査について](/sql/relational-databases/security/auditing/sql-server-audit-database-engine)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8:承認された場所からのみ Azure リソースを管理する

**ガイダンス**: ポータルや Azure Resource Management での IP アドレス範囲または国/地域の特定の論理グループからのアクセスのみを許可するには、条件付きアクセスのネームド ロケーションを使用します。

- [Azure でネームド ロケーションを構成する方法](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="39-use-azure-active-directory"></a>3.9: Azure Active Directory を使用する

**ガイダンス**: 専用 SQL プール内にある Azure SQL Database サーバーの Azure Active Directory (Azure AD) 管理者を作成します。

- [Azure SQL での Azure AD 認証を構成して管理する方法](../azure-sql/database/authentication-aad-configure.md)

- [Azure AD インスタンスを作成して構成する方法](../active-directory-domain-services/tutorial-create-instance.md)

**責任**: Customer

**Azure Security Center の監視**: [Azure セキュリティ ベンチマーク](/azure/governance/policy/samples/azure-security-benchmark)は、Security Center の既定のポリシー イニシアチブであり、[Security Center の推奨事項](/azure/security-center/security-center-recommendations)の基礎となります。 このコントロールに関連する Azure Policy 定義は、Security Center によって自動的に有効になります。 この制御に関連するアラートでは、関連するサービスのために [Azure Defender](/azure/security-center/azure-defender) プランが必要になる場合があります。

**Azure Policy 組み込み定義 - Microsoft.Sql**:

[!INCLUDE [Resource Policy for Microsoft.Sql 3.9](../../includes/policy/standards/asb/rp-controls/microsoft.sql-3-9.md)]

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: ユーザー アクセスを定期的に確認して調整する

**ガイダンス**: Azure Active Directory (Azure AD) では、古いアカウントの検出に役立つログが提供されます。 また、Azure AD アクセス レビューを使用して、グループ メンバーシップ、エンタープライズ アプリケーションへのアクセス、およびロールの割り当てを効率的に管理します。 ユーザーのアクセスを定期的にレビューして、適切なユーザーのみが継続的なアクセス権を持っていることを確認できます。

SQL 認証の使用時は、データベース内に包含データベース ユーザーを作成します。 1 人以上のデータベース ユーザーを、そのユーザー グループにとって適切な特定のアクセス許可を持つカスタム データベース ロールに必ず配置します。

- [アクセス レビューを使用する方法](../active-directory/governance/access-reviews-overview.md)

- [Azure SQL でのログインとユーザー アカウントについて](../azure-sql/database/logins-create-manage.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: 非アクティブ化された資格情報へのアクセスの試行を監視する

**ガイダンス**: Azure SQL で Azure Active Directory (Azure AD) 認証を構成し、Azure AD ユーザー アカウントの診断設定と Log Analytics ワークスペースへの監査ログとサインイン ログの送信を有効にします。 Log Analytics 内に目的のアラートを構成します。

SQL 認証の使用時は、データベース内に包含データベース ユーザーを作成します。 1 人以上のデータベース ユーザーを、そのユーザー グループにとって適切な特定のアクセス許可を持つカスタム データベース ロールに必ず配置します。

- [アクセス レビューを使用する方法](../active-directory/governance/access-reviews-overview.md)

- [Azure SQL Database で Azure AD 認証を構成して管理する方法](../azure-sql/database/authentication-aad-configure.md)

- [Azure アクティビティ ログを Azure Monitor に統合する方法](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Azure SQL でのログインとユーザー アカウントについて](../azure-sql/database/logins-create-manage.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: アカウント サインイン動作の偏差に関するアラートを生成する

**ガイダンス**:ユーザー ID に関連する検出された疑わしいアクションへの自動応答を構成するには、Azure Active Directory (Azure AD) の Identity Protection 機能とリスク検出機能を使用します。 また、さらに詳しく調査するために、Azure Sentinel にデータをオンボードして取り込むこともできます。

SQL 認証の使用時は、データベース内に包含データベース ユーザーを作成します。 1 人以上のデータベース ユーザーを、そのユーザー グループにとって適切な特定のアクセス許可を持つカスタム データベース ロールに必ず配置します。

- [Azure AD の危険なサインインを表示する方法](../active-directory/identity-protection/overview-identity-protection.md)

- [Identity Protection のリスク ポリシーを構成して有効にする方法](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Azure Sentinel をオンボードする方法](../sentinel/connect-data-sources.md)

- [Azure SQL でのログインとユーザー アカウントについて](../azure-sql/database/logins-create-manage.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: サポート シナリオで関連する顧客データに Microsoft がアクセスできるようにする

**ガイダンス**:Microsoft が専用 SQL プール内の Azure SQL Database に関連するデータにアクセスする必要があるサポート シナリオで、Azure カスタマー ロックボックスのインターフェイスを使用して、データへのアクセス要求を確認し、承認または拒否することができます。

- [カスタマー ロックボックスについて](../security/fundamentals/customer-lockbox-overview.md)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="data-protection"></a>データ保護

*詳細については、[Azure セキュリティ ベンチマークの「データ保護](../security/benchmarks/security-control-data-protection.md)」を参照してください。*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: 機密情報のインベントリを維持する

**ガイダンス**: 機密情報を格納または処理する Azure リソースを追跡しやすくするには、タグを使用します。

データの検出と分類は Azure Synapse SQL に組み込まれています。 これにより、データベース内の機密データの検出、分類、ラベル付け、およびレポート作成を行うための高度な機能が提供されます。

- [タグを作成して使用する方法](../azure-resource-manager/management/tag-resources.md)

- [データの検出と分類について](../azure-sql/database/data-discovery-and-classification-overview.md)

**責任**: Customer

**Azure Security Center の監視**: [Azure セキュリティ ベンチマーク](/azure/governance/policy/samples/azure-security-benchmark)は、Security Center の既定のポリシー イニシアチブであり、[Security Center の推奨事項](/azure/security-center/security-center-recommendations)の基礎となります。 このコントロールに関連する Azure Policy 定義は、Security Center によって自動的に有効になります。 この制御に関連するアラートでは、関連するサービスのために [Azure Defender](/azure/security-center/azure-defender) プランが必要になる場合があります。

**Azure Policy 組み込み定義 - Microsoft.Sql**:

[!INCLUDE [Resource Policy for Microsoft.Sql 4.1](../../includes/policy/standards/asb/rp-controls/microsoft.sql-4-1.md)]

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2:機密情報を格納または処理するシステムを分離する

**ガイダンス**:開発、テスト、および運用で別々のサブスクリプションまたは管理グループ、あるいはその両方を実装します。 リソースは、仮想ネットワークまたはサブネットで分離し、適切にタグ付けし、さらにネットワーク セキュリティ グループまたは Azure Firewall 内でセキュリティ保護する必要があります。 機密データを格納または処理するリソースは分離されている必要があります。 Private Link を使用します。仮想ネットワーク内に Azure SQL Server をデプロイし、Private Link を使用して安全に接続します。

- [追加の Azure サブスクリプションを作成する方法](../cost-management-billing/manage/create-subscription.md)

- [管理グループを作成する方法](../governance/management-groups/create-management-group-portal.md)

- [タグを作成して使用する方法](../azure-resource-manager/management/tag-resources.md)

- [Azure SQL Database 用の Private Link を設定する方法](../azure-sql/database/private-endpoint-overview.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3:機密情報の承認されていない転送を監視してブロックする

**ガイダンス**:機密情報を格納または処理する専用 SQL プール内の Azure SQL Database の場合は、タグを使用してデータベースと関連リソースを機密とマークします。 機密情報の窃盗を防止するために、Azure SQL Database インスタンスで Private Link とネットワーク セキュリティ グループ (NSG) のサービス タグを構成します。

さらに、Azure SQL Database、Azure SQL Managed Instance、Azure Synapse の Advanced Threat Protection では、データベースへのアクセスやデータベースの悪用を試みる、害を及ぼす可能性のある異常なアクティビティが検出されます。

Microsoft によって管理される基になるプラットフォームの場合、Microsoft は顧客のすべてのコンテンツを機密として扱い、顧客データを損失や漏洩から保護するためにあらゆる手段を尽くします。 Azure 内の顧客データが確実にセキュリティで保護されるように、Microsoft では一連の堅牢なデータ保護制御および機能を実装して管理しています。

- [Azure SQL Database インスタンスでデータの窃盗を防止するように Private Link と NSG を構成する方法](../azure-sql/database/private-endpoint-overview.md)

- [Azure SQL Database 用の Advanced Threat Protection について](../azure-sql/database/threat-detection-overview.md)

- [Azure での顧客データの保護について](../security/fundamentals/protection-customer-data.md)

**責任**: 共有

**Azure Security Center の監視**: なし

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5:アクティブ検出ツールを使用して機密データを特定する

**ガイダンス**: Azure Synapse SQL のデータの検出と分類機能を使用します。 データの検出と分類では、データベース内の機密データを検出、分類、ラベル付け、保護するために Azure SQL Database に組み込まれた高度な機能が提供されます。

データの検出と分類は、高度な SQL セキュリティ機能の統合パッケージである Advanced Data Security オファリングの一部です。 データの検出と分類は、SQL ADS ポータルを使って一元的にアクセスおよび管理できます。

また、Azure portal で動的データ マスク (DDM) ポリシーを設定することもできます。 DDM の推奨エンジンでは、データベースの特定のフィールドに「機密データの可能性あり」の注意が付けられます。この注意を参考にマスク候補を選択できます。

- [Azure SQL Server に対してデータの検出と分類を使用する方法](../azure-sql/database/data-discovery-and-classification-overview.md)

- [Azure Synapse SQL に対する動的データ マスクについて](../azure-sql/database/dynamic-data-masking-overview.md)

**責任**: Customer

**Azure Security Center の監視**: [Azure セキュリティ ベンチマーク](/azure/governance/policy/samples/azure-security-benchmark)は、Security Center の既定のポリシー イニシアチブであり、[Security Center の推奨事項](/azure/security-center/security-center-recommendations)の基礎となります。 このコントロールに関連する Azure Policy 定義は、Security Center によって自動的に有効になります。 この制御に関連するアラートでは、関連するサービスのために [Azure Defender](/azure/security-center/azure-defender) プランが必要になる場合があります。

**Azure Policy 組み込み定義 - Microsoft.Sql**:

[!INCLUDE [Resource Policy for Microsoft.Sql 4.5](../../includes/policy/standards/asb/rp-controls/microsoft.sql-4-5.md)]

### <a name="46-use-azure-rbac-access-control-to-control-access-to-resources"></a>4.6: Azure RBAC アクセス制御を使用してリソースへのアクセスを制御する 

**ガイダンス**:Azure のロールベースのアクセス制御 (Azure RBAC) を使用して、専用 SQL プール内の Azure SQL データベースに対するアクセスを管理します。

ユーザー アカウントのデータベースのロール メンバーシップとオブジェクト レベルのアクセス許可によって制御されます。 ベスト プラクティスとして、必要最低限の特権をユーザーに付与することをお勧めします。

- [認証用に Azure SQL Server と Azure Active Directory (Azure AD) を統合する方法](../azure-sql/database/authentication-aad-overview.md)

- [Azure SQL Server でアクセスを制御する方法](../azure-sql/database/logins-create-manage.md)

- [Azure SQL での承認と認証の概要](../azure-sql/database/logins-create-manage.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8:機密情報を保存時に暗号化する

**ガイダンス**: Transparent Data Encryption (TDE) を使用すると、保存データを暗号化することにより、悪意のあるオフライン アクティビティの脅威から Azure Synapse SQL を保護できます。 データベース、関連付けられているバックアップ、保管されているトランザクション ログ ファイルの暗号化と暗号化解除をリアルタイムで実行することにより、アプリケーションに変更を加えずに暗号化を行うことができます。 Azure では、TDE の既定の設定は、組み込みのサーバー証明書によって保護される DEK です。 または、カスタマー マネージド TDE (TDE に対する Bring Your Own Key (BYOK) サポートとも呼ばれます) を使用することもできます。 このシナリオでは、DEK を暗号化する TDE 保護機能はユーザーによって管理される非対象キーであり、それはユーザーが所有して管理している Azure Key Vault (Azure のクラウドベースの外部キー管理システム) 内に格納され、そのキー コンテナーから出ることはありません。

- [サービス マネージド Transparent Data Encryption の概要](../azure-sql/database/transparent-data-encryption-tde-overview.md)

- [カスタマー マネージド Transparent Data Encryption の概要](https://docs.microsoft.com/azure/azure-sql/database/transparent-data-encryption-tde-overview#customer-managed-transparent-data-encryption---bring-your-own-key)

- [独自のキーを使用して TDE を有効にする方法](../azure-sql/database/transparent-data-encryption-byok-configure.md)

**責任**: 共有

**Azure Security Center の監視**: [Azure セキュリティ ベンチマーク](/azure/governance/policy/samples/azure-security-benchmark)は、Security Center の既定のポリシー イニシアチブであり、[Security Center の推奨事項](/azure/security-center/security-center-recommendations)の基礎となります。 このコントロールに関連する Azure Policy 定義は、Security Center によって自動的に有効になります。 この制御に関連するアラートでは、関連するサービスのために [Azure Defender](/azure/security-center/azure-defender) プランが必要になる場合があります。

**Azure Policy 組み込み定義 - Microsoft.Sql**:

[!INCLUDE [Resource Policy for Microsoft.Sql 4.8](../../includes/policy/standards/asb/rp-controls/microsoft.sql-4-8.md)]

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9:重要な Azure リソースへの変更に関するログとアラート

**ガイダンス**: Synapse SQL プールの運用インスタンスやその他の重要なリソースまたは関連リソースへの変更がいつ発生したかに関するアラートを作成するには、Azure Monitor と Azure アクティビティ ログを使用します。

また、Azure portal を使用して、SQL Synapse プールのデータベースに対するアラートを設定することもできます。 あるメトリック (データベース サイズや CPU 使用率など) がしきい値に達したら、アラートはユーザーに電子メールを送信するか、または Web フックを呼び出すことができます。

- [Azure アクティビティ ログ イベントのアラートを作成する方法](../azure-monitor/alerts/alerts-activity-log.md)

- [Azure SQL Synapse のアラートを作成する方法](../azure-sql/database/alerts-insights-configure-portal.md)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="vulnerability-management"></a>脆弱性の管理

*詳細については、[Azure セキュリティ ベンチマークの「脆弱性の管理](../security/benchmarks/security-control-vulnerability-management.md)」を参照してください。*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1:自動化された脆弱性スキャン ツールを実行する

**ガイダンス**: Advanced Data Security を有効にして、Azure SQL Database での脆弱性評価の実行に関する Azure Security Center の推奨事項に従います。

- [Azure SQL Database で脆弱性評価を実行する方法](../azure-sql/database/sql-vulnerability-assessment.md)

- [Advanced Data Security を有効にする方法](../azure-sql/database/azure-defender-for-sql.md)

- [Azure Security Center の脆弱性評価の推奨事項を実装する方法](../security-center/deploy-vulnerability-assessment-vm.md)

**責任**: Customer

**Azure Security Center の監視**: [Azure セキュリティ ベンチマーク](/azure/governance/policy/samples/azure-security-benchmark)は、Security Center の既定のポリシー イニシアチブであり、[Security Center の推奨事項](/azure/security-center/security-center-recommendations)の基礎となります。 このコントロールに関連する Azure Policy 定義は、Security Center によって自動的に有効になります。 この制御に関連するアラートでは、関連するサービスのために [Azure Defender](/azure/security-center/azure-defender) プランが必要になる場合があります。

**Azure Policy 組み込み定義 - Microsoft.Sql**:

[!INCLUDE [Resource Policy for Microsoft.Sql 5.1](../../includes/policy/standards/asb/rp-controls/microsoft.sql-5-1.md)]

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4:バックツーバックの脆弱性スキャンを比較する

**ガイダンス**: 脆弱性評価は、Azure Synapse SQL に組み込まれているスキャン サービスです。 このサービスでは、セキュリティの脆弱性にフラグを付ける規則のナレッジ ベースが採用されています。 これは、誤った設定、過剰なアクセス許可、保護されていない機密データなど、ベスト プラクティスからの逸脱を明らかにします。  脆弱性評価は、SQL Advanced Data Security (ADS) ポータルを使って一元的にアクセスおよび管理できます。

- [SQL ADS ポータルでの脆弱性評価スキャンの管理とエクスポート](../azure-sql/database/sql-vulnerability-assessment.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5:リスク評価プロセスを使用して、検出された脆弱性の修復に優先順位を付ける

**ガイダンス**: Azure Security Center によって提供される既定のリスク評価 (セキュリティ スコア) を使用します。

データの検出と分類は Azure Synapse SQL に組み込まれています。 これにより、データベース内の機密データの検出、分類、ラベル付け、およびレポート作成を行うための高度な機能が提供されます。

- [Azure Security Center のセキュリティ スコアについて](../security-center/secure-score-security-controls.md)

- [データの検出と分類について](../azure-sql/database/data-discovery-and-classification-overview.md)

**責任**: Customer

**Azure Security Center の監視**: [Azure セキュリティ ベンチマーク](/azure/governance/policy/samples/azure-security-benchmark)は、Security Center の既定のポリシー イニシアチブであり、[Security Center の推奨事項](/azure/security-center/security-center-recommendations)の基礎となります。 このコントロールに関連する Azure Policy 定義は、Security Center によって自動的に有効になります。 この制御に関連するアラートでは、関連するサービスのために [Azure Defender](/azure/security-center/azure-defender) プランが必要になる場合があります。

**Azure Policy 組み込み定義 - Microsoft.Sql**:

[!INCLUDE [Resource Policy for Microsoft.Sql 5.5](../../includes/policy/standards/asb/rp-controls/microsoft.sql-5-5.md)]

## <a name="inventory-and-asset-management"></a>インベントリと資産の管理

*詳細については、[Azure セキュリティ ベンチマークの「インベントリと資産の管理](../security/benchmarks/security-control-inventory-asset-management.md)」を参照してください。*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1:自動化された資産検出ソリューションを使用する

**ガイダンス**:Azure Resource Graph を使用して、サブスクリプション内の専用 SQL プールに関連するすべてのリソースをクエリして検出します。 テナント内の適切な (読み取り) アクセス許可を持っており、サブスクリプション内のリソースだけでなく、すべての Azure サブスクリプションを列挙できることを確認します。

従来の Azure リソースは Azure Resource Graph で検出できますが、今後は Azure Resource Manager リソースを作成して使用することを強くお勧めします。

- [Azure Resource Graph を使用してクエリを作成する方法](../governance/resource-graph/first-query-portal.md)

- [Azure サブスクリプションを表示する方法](/powershell/module/az.accounts/get-azsubscription)

- [Azure RBAC について](../role-based-access-control/overview.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="62-maintain-asset-metadata"></a>6.2:資産メタデータを保持する

**ガイダンス**:メタデータを提供する Azure リソースにタグを適用すると、それらのリソースが各分類に論理的に整理されます。

- [タグを作成して使用する方法](../azure-resource-manager/management/tag-resources.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="63-delete-unauthorized-azure-resources"></a>6.3:承認されていない Azure リソースを削除する

**ガイダンス**:必要に応じて、タグ付け、管理グループ、および個別のサブスクリプションを使用して、資産の整理と追跡を行います。 定期的にインベントリを調整し、承認されていないリソースがサブスクリプションから適切なタイミングで削除されるようにします。

- [追加の Azure サブスクリプションを作成する方法](../cost-management-billing/manage/create-subscription.md)

- [管理グループを作成する方法](../governance/management-groups/create-management-group-portal.md)

- [タグを作成して使用する方法](../azure-resource-manager/management/tag-resources.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4:承認された Azure リソースのインベントリを定義および管理する

**ガイダンス**:専用 SQL プールに関連する承認済みの Azure リソースの一覧を定義します。

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5:承認されていない Azure リソースを監視する

**ガイダンス**: 次の組み込みのポリシー定義を使用して、顧客のサブスクリプション内に作成できるリソースの種類に制限を適用するには、Azure Policy を使用します。

- 許可されないリソースの種類

- 許可されるリソースの種類

サブスクリプション内のリソースのクエリまたは検出を行うには、Azure Resource Graph を使用します。 環境に存在するすべての Azure リソースが承認されていることを確認します。

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

- [Azure Resource Graph を使用してクエリを作成する方法](../governance/resource-graph/first-query-portal.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="69-use-only-approved-azure-services"></a>6.9:承認された Azure サービスのみを使用する

**ガイダンス**: 次の組み込みのポリシー定義を使用して、顧客のサブスクリプション内に作成できるリソースの種類に制限を適用するには、Azure Policy を使用します。
- 許可されないリソースの種類
- 許可されるリソースの種類

サブスクリプション内のリソースのクエリまたは検出を行うには、Azure Resource Graph を使用します。 環境に存在するすべての Azure リソースが承認されていることを確認します。

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy を使用して特定のリソースの種類を拒否する方法](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11:Azure Resource Manager を操作するユーザーの機能を制限する

**ガイダンス**:"Microsoft Azure Management" アプリに対して [アクセスのブロック] を構成することによって、Azure Resource Manager を操作するユーザーの機能を制限するには、Azure 条件付きアクセスを使用します。

- [Azure Resource Manager へのアクセスをブロックするように条件付きアクセスを構成する方法](../role-based-access-control/conditional-access-azure-management.md)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="secure-configuration"></a>セキュリティで保護された構成

*詳細については、[Azure セキュリティ ベンチマークの「セキュリティで保護された構成](../security/benchmarks/security-control-secure-configuration.md)」を参照してください。*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1:すべての Azure リソースに対してセキュリティで保護された構成を確立する

**ガイダンス**:専用 SQL プールに関連するリソースの構成を監査または適用するためのカスタム ポリシーを作成するには、"Microsoft.Sql" 名前空間で Azure Policy エイリアスを使用します。 また、次のように、Azure データベースや Azure サーバーを対象とした組み込みのポリシー定義を使用することもできます。

- SQL サーバーでの脅威検出のデプロイ
- SQL Server は仮想ネットワーク サービス エンドポイントを使用する必要がある

- [使用可能な Azure Policy エイリアスを表示する方法](/powershell/module/az.resources/get-azpolicyalias)

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3:セキュリティで保護された Azure リソースの構成を維持する

**ガイダンス**:Azure リソース全体にセキュリティで保護された設定を適用するには、Azure Policy の [deny] と [deploy if not exist] を使用します。

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy の効果について](../governance/policy/concepts/effects.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5:Azure リソースの構成を安全に格納する

**ガイダンス**: カスタムの Azure Policy 定義を使用する場合は、Azure DevOps または Azure Repos を使用して、コードを安全に格納して管理します。

- [Azure DevOps でコードを格納する方法](/azure/devops/repos/git/gitworkflow)

- [Azure Repos のドキュメント](/azure/devops/repos/)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7:Azure リソース用の構成管理ツールをデプロイする

**ガイダンス**: 適用できません。Azure Synapse SQL には、構成可能なセキュリティ設定はありません。

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9:Azure リソースの自動構成監視を実装する

**ガイダンス**:Azure Security Center を利用して、専用 SQL プールに関連するリソースのベースライン スキャンを実行します。

- [Azure Security Center の推奨事項を修復する方法](../security-center/security-center-remediate-recommendations.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="711-manage-azure-secrets-securely"></a>7.11:Azure シークレットを安全に管理する

**ガイダンス**: Azure Key Vault 内のカスタマー マネージド キーと Transparent Data Encryption (TDE) を併用すると、TDE 保護機能と呼ばれる非対称のカスタマー マネージド キーを使用して、自動的に生成されたデータベース暗号化キー (DEK) を暗号化することができます。 これは、一般に、Transparent Data Encryption に対する Bring Your Own Key (BYOK) のサポートとも呼ばれます。 BYOK シナリオでは、TDE 保護機能は、ユーザーが所有および管理する Azure Key Vault 内に格納されます。 さらに、Azure Key Vault で論理的な削除が有効になっていることを確認します。

- [Azure Key Vault のカスタマー マネージド キーを使用して TDE を有効にする方法](../azure-sql/database/transparent-data-encryption-byok-configure.md)

- [Azure Key Vault で論理的な削除を有効にする方法](../key-vault/general/key-vault-recovery.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="712-manage-identities-securely-and-automatically"></a>7.12:ID を安全かつ自動的に管理する

**ガイダンス**: マネージド ID を使用して、Azure Active Directory (Azure AD) で自動的に管理される ID を Azure サービスに提供します。 マネージド ID を使用すると、コード内に資格情報を記述することなく、Azure AD 認証をサポートする任意のサービス (Azure Key Vault を含む) に対して認証できます。

- [チュートリアル:Windows VM のシステム割り当てマネージド ID を使用して Azure SQL にアクセスする](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-sql.md)

- [マネージド ID を構成する方法](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13:意図しない資格情報の公開を排除する

**ガイダンス**: コード内の資格情報を識別するための資格情報スキャナーを実装します。 また、資格情報スキャナーを使うと、検出された資格情報を、Azure Key Vault などのより安全な場所に移動しやすくなります。

- [資格情報スキャナーを設定する方法](https://secdevtools.azurewebsites.net/helpcredscan.html)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="malware-defense"></a>マルウェアからの防御

*詳細については、[Azure セキュリティ ベンチマークの「マルウェアからの防御](../security/benchmarks/security-control-malware-defense.md)」を参照してください。*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2:非コンピューティング Azure リソースにアップロードするファイルを事前にスキャンする

**ガイダンス**: Microsoft のマルウェア対策は、Azure サービス (Azure Synapse SQL など) をサポートしている基になるホストで有効になっていますが、顧客のコンテンツに対しては実行されません。

App Service、Data lake Storage、Blob Storage、Azure SQL Server などの非コンピューティング Azure リソースにアップロードされるコンテンツはすべて、事前にスキャンしてください。Microsoft は、これらのインスタンス内のデータにアクセスできません。

- [Azure Cloud Services および Virtual Machines 向けの Microsoft Antimalware について](../security/fundamentals/antimalware.md)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="data-recovery"></a>データの復旧

*詳細については、[Azure セキュリティ ベンチマークの「データの復旧](../security/benchmarks/security-control-data-recovery.md)」を参照してください。*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1:定期的な自動バックアップを保証する

**ガイダンス**:専用 SQL プールのスナップショットは 1 日を通して自動的に取得され、7 日間利用できる復元ポイントが作成されます。 この保持期間は変更できません。 専用 SQL プールでは、8 時間の RPO (回復ポイントの目標) がサポートされています。 プライマリ リージョンのデータ ウェアハウスを、過去 7 日間に作成されたいずれかのスナップショットから復元することができます。 必要に応じて、手動でスナップショットをトリガーできることに注意してください。

- [専用 SQL プールでのバックアップと復元](sql-data-warehouse/backup-and-restore.md)

**責任**: 共有

**Azure Security Center の監視**: [Azure セキュリティ ベンチマーク](/azure/governance/policy/samples/azure-security-benchmark)は、Security Center の既定のポリシー イニシアチブであり、[Security Center の推奨事項](/azure/security-center/security-center-recommendations)の基礎となります。 このコントロールに関連する Azure Policy 定義は、Security Center によって自動的に有効になります。 この制御に関連するアラートでは、関連するサービスのために [Azure Defender](/azure/security-center/azure-defender) プランが必要になる場合があります。

**Azure Policy 組み込み定義 - Microsoft.Sql**:

[!INCLUDE [Resource Policy for Microsoft.Sql 9.1](../../includes/policy/standards/asb/rp-controls/microsoft.sql-9-1.md)]

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: システムの完全バックアップを実行し、すべてのカスタマー マネージド キーをバックアップする

**ガイダンス**:データ ウェアハウスのスナップショットは 1 日を通して自動的に取得され、7 日間利用できる復元ポイントが作成されます。 この保持期間は変更できません。 専用 SQL プールでは、8 時間の RPO (回復ポイントの目標) がサポートされています。 プライマリ リージョンのデータ ウェアハウスを、過去 7 日間に作成されたいずれかのスナップショットから復元することができます。 必要に応じて、手動でスナップショットをトリガーできることに注意してください。

カスタマー マネージド キーを使用してデータベース暗号化キーを暗号化している場合は、キーがバックアップされていることを確認します。

- [専用 SQL プールでのバックアップと復元](sql-data-warehouse/backup-and-restore.md)

- [Azure Key Vault のキーをバックアップする方法](/powershell/module/az.keyvault/backup-azkeyvaultkey)

**責任**: 共有

**Azure Security Center の監視**: [Azure セキュリティ ベンチマーク](/azure/governance/policy/samples/azure-security-benchmark)は、Security Center の既定のポリシー イニシアチブであり、[Security Center の推奨事項](/azure/security-center/security-center-recommendations)の基礎となります。 このコントロールに関連する Azure Policy 定義は、Security Center によって自動的に有効になります。 この制御に関連するアラートでは、関連するサービスのために [Azure Defender](/azure/security-center/azure-defender) プランが必要になる場合があります。

**Azure Policy 組み込み定義 - Microsoft.Sql**:

[!INCLUDE [Resource Policy for Microsoft.Sql 9.2](../../includes/policy/standards/asb/rp-controls/microsoft.sql-9-2.md)]

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3:カスタマー マネージド キーを含むすべてのバックアップを検証する

**ガイダンス**: 復元ポイントを定期的にテストして、スナップショットが有効であることを確認します。 復元ポイントから既存の専用 SQL プールを復元するには、Azure portal または PowerShell を使用できます。 バックアップされたカスタマー マネージド キーの復元をテストします。

- [Azure Key Vault のキーを復元する方法](/powershell/module/az.keyvault/restore-azkeyvaultkey)

- [専用 SQL プールでのバックアップと復元](sql-data-warehouse/backup-and-restore.md)

- [既存の専用 SQL プールを復元する方法](sql-data-warehouse/sql-data-warehouse-restore-active-paused-dw.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: バックアップとカスタマー マネージド キーの保護を保証する

**ガイダンス**: Azure SQL Database では、長期的なバックアップ保有期間ポリシー (LTR) を使用して、単一またはプールされたデータベースを構成し、別々の Azure Blob Storage コンテナーに最大 10 年間自動的にデータベースのバックアップを保持することができます。 Azure Storage 内のデータは、利用可能な最強のブロック暗号の 1 つである 256 ビット AES 暗号化を使って透過的に暗号化および暗号化解除され、FIPS 140-2 に準拠しています。

既定では、ストレージ アカウント内のデータは Microsoft マネージド キーで暗号化されます。 Microsoft マネージド キーを利用してデータを暗号化することも、独自のキーで暗号化を管理することもできます。 Key Vault で独自のキーを管理している場合は、論理的な削除が有効になっていることを確認します。

- [Azure SQL Database の長期的なバックアップ保有期間を管理する](../azure-sql/database/long-term-backup-retention-configure.md)

- [保存データに対する Azure Storage 暗号化](../storage/common/storage-service-encryption.md)

- [Key Vault で論理的な削除を有効にする方法](../storage/blobs/soft-delete-blob-overview.md)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="incident-response"></a>インシデント対応

*詳細については、[Azure セキュリティ ベンチマークの「インシデント対応](../security/benchmarks/security-control-incident-response.md)」を参照してください。*

### <a name="101-create-an-incident-response-guide"></a>10.1:インシデント対応ガイドを作成する

**ガイダンス**: 担当者の役割を定義している記述されたインシデント対応計画があること、およびインシデント処理/管理のフェーズが存在することを確認します。

- [Azure Security Center 内でワークフロー自動化を構成する方法](../security-center/security-center-planning-and-operations-guide.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2:インシデントのスコアリングと優先順位付けの手順を作成する

**ガイダンス**:Security Center は、アラートに重要度を割り当て、各アラートに対処する優先順位を付けることができます。これにより、リソースが侵害されたときに、すぐにアクセスできるようになります。 重要度は、アラートの発行に使用された調査結果またはメトリックを Security Center がどの程度信頼しているかということと、アラートの原因となったアクティビティの背後に悪意のある意図があったことについての確信の度合いに基づくものです。

- [Security alerts in Azure Security Center](../security-center/security-center-alerts-overview.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="103-test-security-response-procedures"></a>10.3:セキュリティ対応手順のテスト

**ガイダンス**: 定期的にシステムのインシデント対応機能をテストする演習を実施します。 弱点やギャップを特定し、必要に応じて計画を見直します。

- [NIST の出版物「IT 計画と機能に関するテスト、トレーニング、演習プログラムのガイド」を参照してください。](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4:セキュリティ インシデントの連絡先の詳細を指定し、セキュリティ インシデントのアラート通知を構成します

**ガイダンス**: セキュリティ インシデントの連絡先情報は、Microsoft Security Response Center (MSRC) でユーザーのデータが違法または承認されていないパーティによってアクセスされたことが検出された場合はユーザーに連絡するために、Microsoft によって使用されます。

- [Azure Security Center のセキュリティ連絡先を設定する方法](../security-center/security-center-provide-security-contact-details.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5:インシデント対応システムにセキュリティ アラートを組み込む

**ガイダンス**:連続エクスポート機能を使用して Azure Security Center のアラートと推奨事項をエクスポートします。 連続エクスポートを使用すると、アラートと推奨事項を手動で、または継続した連続的な方法でエクスポートできます。 Azure Security Center データ コネクタを使用してアラートを Azure Sentinel にストリーミングできます。

- [連続エクスポートを構成する方法](../security-center/continuous-export.md)

- [Azure Sentinel にアラートをストリーミングする方法](../sentinel/connect-azure-security-center.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="106-automate-the-response-to-security-alerts"></a>10.6:セキュリティ アラートへの対応を自動化する

**ガイダンス**:セキュリティ アラートやセキュリティに関する推奨事項に対して "Logic Apps" 経由で応答を自動的にトリガーするには、Azure Security Center のワークフローの自動化機能を使用します。

- [ワークフローの自動化と Logic Apps を構成する方法](../security-center/workflow-automation.md)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="penetration-tests-and-red-team-exercises"></a>侵入テストとレッド チーム演習

*詳細については、[Azure セキュリティ ベンチマークの「侵入テストとレッド チーム演習](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)」を参照してください。*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1:Azure リソースの通常の侵入テストを実施し、セキュリティに関する重大な調査結果がすべて、確実に修復されるようにする

**ガイダンス**: 侵入テストが Microsoft のポリシーに違反しないようにするために、Microsoft の活動規則 (https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1 ) に従ってください。

- [Microsoft が管理するクラウド インフラストラクチャ、サービス、アプリケーションに対する Microsoft の戦略および Red Teaming とライブ サイト侵入テストの実行に関する詳細については、こちら](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)を参照してください

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="next-steps"></a>次のステップ

- 「[Azure セキュリティ ベンチマーク V2 の概要](/azure/security/benchmarks/overview)」を参照してください。
- [Azure セキュリティ ベースライン](/azure/security/benchmarks/security-baselines-overview)の詳細について学習する
