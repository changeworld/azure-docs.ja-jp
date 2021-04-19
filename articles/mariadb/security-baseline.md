---
title: Azure Database for MariaDB 用の Azure セキュリティ ベースライン
description: Azure Database for MariaDB セキュリティ ベースラインでは、Azure セキュリティ ベンチマークで指定されているセキュリティに関する推奨事項を実装するための手順のガイダンスとリソースが提供されます。
author: msmbaldwin
ms.author: mbaldwin
ms.date: 03/29/2021
ms.service: mariadb
ms.topic: conceptual
ms.custom: subject-security-benchmark
ms.openlocfilehash: fd169738c138d56932dbab87047b89e373b9c6ec
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/09/2021
ms.locfileid: "107227672"
---
# <a name="azure-security-baseline-for-azure-database-for-mariadb"></a>Azure Database for MariaDB 用の Azure セキュリティ ベースライン

このセキュリティ ベースラインにより、[Azure セキュリティ ベンチマーク バージョン 1.0](../security/benchmarks/overview-v1.md) のガイダンスが Azure Database for MariaDB に適用されます。 Azure セキュリティ ベンチマークには、Azure 上のクラウド ソリューションをセキュリティで保護する方法に関する推奨事項がまとめてあります。 内容は、Azure セキュリティ ベンチマークおよび Azure Database for MariaDB に適用できる関連ガイダンスによって定義されている **セキュリティ制御** でグループ化されています。 Azure Database for MariaDB に適用されない、または Microsoft の責任である **コントロール** は除外されています。

Azure Database for MariaDB を完全に Azure セキュリティ ベンチマークにマップする方法については、[完全な Azure Database for MariaDB セキュリティ ベースライン マッピング ファイル](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)を参照してください。

## <a name="network-security"></a>ネットワークのセキュリティ

*詳細については、[Azure セキュリティ ベンチマークの「ネットワークのセキュリティ](../security/benchmarks/security-control-network-security.md)」を参照してください。*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1:仮想ネットワーク内の Azure リソースを保護する

**ガイダンス**:プライベート エンドポイントを使用して、Azure Database for MariaDB 用に Private Link を構成します。 Private Link を使用すると、プライベート エンドポイントを経由して Azure 内のさまざまな PaaS サービスに接続できます。 Azure Private Link は基本的に、プライベート仮想ネットワーク (VNet) 内に Azure サービスを提供します。 仮想ネットワークと MariaDB インスタンスの間のトラフィックは、Microsoft のバックボーン ネットワークを経由します。

また、仮想ネットワーク サービス エンドポイントを使用して、Azure Database for MariaDB の実装へのネットワーク アクセスを保護および制限することもできます。 仮想ネットワーク規則は、仮想ネットワーク内の特定のサブネットから送信される通信を Azure Database for MariaDB で許可するかどうかを制御する 1 つのファイアウォール セキュリティ機能です。

ファイアウォール規則を使用して、Azure Database for MariaDB をセキュリティで保護することもできます。 サーバー ファイアウォールでは、どのコンピューターに権限を持たせるかを指定するまで、データベース サーバーへのすべてのアクセスを防ぎます。 ファイアウォールを構成するには、受け入れ可能な IP アドレスの範囲を指定するファイアウォール規則を作成します。 ファイアウォール規則はサーバー レベルで作成できます。

- [Azure Database for MariaDB 用に Private Link を構成する方法](howto-configure-privatelink-portal.md)

- [Azure Database for MariaDB サーバーで VNet サービス エンドポイントおよび VNet 規則を作成して管理する方法](howto-manage-vnet-portal.md)

- [Azure Database for MariaDB のファイアウォール規則を構成する方法](concepts-firewall-rules.md)

**責任**: Customer

**Azure Security Center の監視**: [Azure セキュリティ ベンチマーク](/azure/governance/policy/samples/azure-security-benchmark)は、Security Center の既定のポリシー イニシアチブであり、[Security Center の推奨事項](/azure/security-center/security-center-recommendations)の基礎となります。 このコントロールに関連する Azure Policy 定義は、Security Center によって自動的に有効になります。 この制御に関連するアラートでは、関連するサービスのために [Azure Defender](/azure/security-center/azure-defender) プランが必要になる場合があります。

**Azure Policy 組み込み定義 - Microsoft.DBforMariaDB**:

[!INCLUDE [Resource Policy for Microsoft.DBforMariaDB 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.dbformariadb-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2:仮想ネットワーク、サブネット、ネットワーク インターフェイスの構成とトラフィックを監視してログに記録する

**ガイダンス**: Azure Database for MariaDB サーバーがプライベート エンドポイントに対してセキュリティで保護されている場合は、同じ仮想ネットワークに仮想マシンをデプロイできます。 ネットワーク セキュリティ グループ (NSG) を使用して、データ流出のリスクを軽減することができます。 NSG フロー ログを有効にし、トラフィック監査のためにログをストレージ アカウントに送信します。 また、NSG フロー ログを Log Analytics ワークスペースに送信し、Traffic Analytics を使用して Azure クラウド内のトラフィック フローに関する分析情報を提供することもできます。 Traffic Analytics のいくつかの利点として、ネットワーク アクティビティを視覚化してホット スポットを特定したり、セキュリティの脅威を識別したり、トラフィック フロー パターンを把握したり、ネットワークの誤った構成の正確な場所を特定したりする機能が挙げられます。

- [Azure Database for MariaDB 用に Private Link を構成する方法](howto-configure-privatelink-portal.md)

- [NSG フロー ログを有効にする方法](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Traffic Analytics を有効にして使用する方法](../network-watcher/traffic-analytics.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4:既知の悪意のある IP アドレスとの通信を拒否する

**ガイダンス**: Azure Database for MariaDB 用に Advanced Threat Protection を使用します。 Advanced Threat Protection では、データベースにアクセスしたり、データベースを悪用したりしようとする、通常とは異なる、害を及ぼす可能性のある試行を示す異常なアクティビティを検出します。

Azure Database for MariaDB インスタンスに関連付けられている仮想ネットワークで DDoS Protection Standard を有効にして、DDoS 攻撃から保護します。 Azure Security Center の統合された脅威インテリジェンスを使用して、既知の悪意のある、または未使用のインターネット IP アドレスとの通信を拒否します。

- [Azure Database for MariaDB 用に Advanced Threat Protection を構成する方法](howto-database-threat-protection-portal.md)

- [DDoS 保護を構成する方法](/azure/virtual-network/manage-ddos-protection)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="15-record-network-packets"></a>1.5:ネットワーク パケットを記録する

**ガイダンス**: Azure Database for MariaDB サーバーがプライベート エンドポイントに対してセキュリティで保護されている場合は、同じ仮想ネットワークに仮想マシンをデプロイできます。 その後、データ流出のリスクを軽減するために、ネットワーク セキュリティ グループ (NSG) を構成することができます。 NSG フロー ログを有効にし、トラフィック監査のためにログをストレージ アカウントに送信します。 また、NSG フロー ログを Log Analytics ワークスペースに送信し、Traffic Analytics を使用して Azure クラウド内のトラフィック フローに関する分析情報を提供することもできます。 Traffic Analytics のいくつかの利点として、ネットワーク アクティビティを視覚化してホット スポットを特定したり、セキュリティの脅威を識別したり、トラフィック フロー パターンを把握したり、ネットワークの誤った構成の正確な場所を特定したりする機能が挙げられます。

- [NSG フロー ログを有効にする方法](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Traffic Analytics を有効にして使用する方法](../network-watcher/traffic-analytics.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6:ネットワーク ベースの侵入検出/侵入防止システム (IDS/IPS) をデプロイする

**ガイダンス**: Azure Database for MariaDB 用に Advanced Threat Protection を使用します。 Advanced Threat Protection では、データベースにアクセスしたり、データベースを悪用したりしようとする、通常とは異なる、害を及ぼす可能性のある試行を示す異常なアクティビティを検出します。

- [Azure Database for MariaDB 用に Advanced Threat Protection を構成する方法](howto-database-threat-protection-portal.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8:ネットワーク セキュリティ規則の複雑さと管理オーバーヘッドを最小限に抑える

**ガイダンス**: Azure Database for MariaDB インスタンスへのアクセスを必要とするリソースについては、仮想ネットワーク サービス タグを使用して、ネットワーク セキュリティ グループまたは Azure Firewall に対するネットワーク アクセス制御を定義します。 セキュリティ規則を作成するときは、特定の IP アドレスの代わりにサービス タグを使うことができます。 規則の適切なソースまたはターゲット フィールドにサービス タグ名 (SQL.WestUs など) を指定することにより、対応するサービスのトラフィックを許可または拒否できます。 サービス タグに含まれるアドレス プレフィックスの管理は Microsoft が行い、アドレスが変化するとサービス タグは自動的に更新されます。

Azure Database for MariaDB では、"Microsoft.Sql" サービス タグが使用されます。

- [サービス タグの使用に関する詳細](../virtual-network/service-tags-overview.md)

- [Azure Database for MariaDB のサービス タグの使用方法について](https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-vnet#terminology-and-description)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9:ネットワーク デバイスの標準的なセキュリティ構成を維持する

**ガイダンス**:Azure Policy を使用して、Azure Database for MariaDB インスタンスに関連付けられているネットワーク設定とネットワーク リソースの標準的なセキュリティ構成を定義して実装します。 Azure Database for MariaDB インスタンスのネットワーク構成を監査または適用するためのカスタム ポリシーを作成するには、"Microsoft.DBforMariaDB" および "Microsoft.Network" 名前空間で Azure Policy エイリアスを使用します。 次のように、ネットワークまたは Azure Database for MariaDB インスタンスに関連する組み込みのポリシー定義を使用することもできます。

- DDoS Protection Standard を有効にする必要がある

- MariaDB サーバーに対してプライベート エンドポイントを有効にする必要がある

- MariaDB サーバーで仮想ネットワーク サービス エンドポイントを使用する必要がある

リファレンス ドキュメント:

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

- [ネットワークに関する Azure Policy のサンプル](../governance/policy/samples/built-in-policies.md)

- [Azure Blueprint を作成する方法](../governance/blueprints/create-blueprint-portal.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="110-document-traffic-configuration-rules"></a>1.10:トラフィック構成規則を文書化する

**ガイダンス**: MariaDB インスタンスのネットワーク セキュリティとトラフィック フローに関連するリソースにタグを使用すると、メタデータで論理的にリソースを整理できます。

すべてのリソースが確実にタグ付きで作成され、既存のタグ付けされていないリソースがユーザーに通知されるようにするには、タグ付けに関連したいずれかの組み込みの Azure Policy 定義 ([タグとその値が必要] など) を使用します。

Azure PowerShell または Azure CLI を使用して、タグに基づいてリソースを検索したり、リソースに対するアクションを実行したりすることもできます。

- [タグを作成して使用する方法](/azure/azure-resource-manager/resource-group-using-tags)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11:自動化ツールを使用してネットワーク リソース構成を監視し、変更を検出する

**ガイダンス**: ネットワーク リソース構成を監視し、Azure Database for MariaDB インスタンスに関連したネットワーク リソースの変更を検出するには、Azure アクティビティ ログを使用します。 重要なネットワーク リソースへの変更が発生するとトリガーされる Azure Monitor 内のアラートを作成します。

- [Azure アクティビティ ログ イベントを表示して取得する方法](/azure/azure-monitor/platform/activity-log-view)

- [Azure Monitor でアラートを作成する方法](/azure/azure-monitor/platform/alerts-activity-log)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="logging-and-monitoring"></a>ログ記録と監視

*詳細については、[Azure セキュリティ ベンチマークの「ログ記録と監視](../security/benchmarks/security-control-logging-monitoring.md)」を参照してください。*

### <a name="22-configure-central-security-log-management"></a>2.2:セキュリティ ログの一元管理を構成する

**ガイダンス**: 診断設定とサーバー ログを有効にし、ログを取り込み、Azure Database for MariaDB インスタンスによって生成されるセキュリティ データを集約します。 Azure Monitor 内で Log Analytics ワークスペースを使用してクエリを発行し、分析を実行して、長期/アーカイブ ストレージには Azure Storage アカウントを使用します。 または、Azure Sentinel またはサードパーティの SIEM に対してデータを有効にしてオンボードすることもできます。

- [Azure Database for MariaDB のサーバー ログの構成およびアクセス方法](concepts-server-logs.md)

- [Azure Database for MariaDB の監査ログの構成およびアクセス方法](howto-configure-audit-logs-portal.md)

- [Azure Sentinel をオンボードする方法](../sentinel/quickstart-onboard.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3:Azure リソースの監査ログ記録を有効にする

**ガイダンス**: 監査、セキュリティ、および診断ログにアクセスするために、Azure Database for MariaDB インスタンスに関する診断設定を有効にします。 MariaDB 監査ログが明示的に有効になっていることを確認します。 自動的に使用できるアクティビティ ログには、イベント ソース、日付、ユーザー、タイムスタンプ、送信元アドレス、送信先アドレス、その他の役立つ要素が含まれています。 また、Azure アクティビティ ログの診断設定を有効にし、同じ Log Analytics ワークスペースまたはストレージ アカウントにログを送信することもできます。

- [Azure Database for MariaDB のサーバー ログの構成およびアクセス方法](concepts-server-logs.md)

- [Azure Database for MariaDB の監査ログの構成およびアクセス方法](howto-configure-audit-logs-portal.md)

- [Azure アクティビティ ログ用に診断設定を構成する方法](/azure/azure-monitor/platform/diagnostic-settings-legacy)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="25-configure-security-log-storage-retention"></a>2.5:セキュリティ ログのストレージ保持を構成する

**ガイダンス**: Azure Monitor 内で、Azure Database for MariaDB ログを保持するために使用される Log Analytics ワークスペースについて、組織のコンプライアンス規則に従って保有期間を設定します。 長期/アーカイブ ストレージには Azure Storage アカウントを使用します。

- [Log Analytics ワークスペースのログ保持パラメーターを設定する方法](/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

- [ Azure ストレージ アカウントでのリソース ログの格納](/azure/azure-monitor/platform/resource-logs#send-to-azure-storage)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="26-monitor-and-review-logs"></a>2.6:ログを監視して確認する

**ガイダンス**: MariaDB インスタンスからのログを分析および監視して、異常な動作がないか確認します。 Azure Monitor の Log Analytics ワークスペースを使用してログを確認し、ログ データに対してクエリを実行します。 または、Azure Sentinel またはサード パーティの SIEM に対してデータを有効にしてオンボードすることもできます。

- [Azure Sentinel をオンボードする方法](../sentinel/quickstart-onboard.md)

- [Log Analytics ワークスペースの詳細について](/azure/azure-monitor/log-query/get-started-portal)

- [Azure Monitor でカスタム クエリを実行する方法](/azure/azure-monitor/log-query/get-started-queries)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7:異常なアクティビティについてのアラートを有効にする

**ガイダンス**: MariaDB 用に Advanced Threat Protection を有効にします。 Azure Database for MariaDB 用の Advanced Threat Protection は、データベースにアクセスしたりそれを悪用したりしようとする、通常とは異なり、かつ有害なおそれのある試みを示す異常なアクティビティを検出します。

さらに、MariaDB のサーバー ログと診断設定を有効にし、Log Analytics ワークスペースにログを送信することもできます。 Log Analytics ワークスペースを Azure Sentinel にオンボードします。これは、セキュリティ オーケストレーション自動応答 (SOAR) ソリューションが提供されるためです。 これにより、プレイブック (自動化されたソリューション) を作成して、セキュリティの問題を修復するために使用できます。

- [MariaDB 用に Advanced Threat Protection を有効にする方法](howto-database-threat-protection-portal.md)

- [MariDB のサーバー ログの構成およびアクセス方法](concepts-server-logs.md)

- [MariaDB の監査ログの構成およびアクセス方法](howto-configure-audit-logs-portal.md)

- [Azure Sentinel をオンボードする方法](../sentinel/quickstart-onboard.md)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="identity-and-access-control"></a>ID およびアクセス制御

*詳細については、[Azure セキュリティ ベンチマークの「ID およびアクセス制御](../security/benchmarks/security-control-identity-access-control.md)」を参照してください。*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: 管理アカウントのインベントリを維持する

**ガイダンス**: MariaDB インスタンスの管理プレーン (Azure portal または Azure Resource Manager) への管理アクセス権を持つユーザー アカウントのインベントリを保持します。 さらに、MariaDB インスタンスのデータ プレーンへのアクセス権を持つ管理者アカウントのインベントリを保持します。 (MariaDB サーバーを作成する場合は、管理者ユーザーの資格情報を指定します。 この管理者は、追加の MariaDB ユーザーを作成するために使用できます。)

- [MariaDB のアクセス管理について](https://docs.microsoft.com/azure/mariadb/concepts-security#access-management)

- [Azure サブスクリプションの Azure 組み込みロールについて](../role-based-access-control/built-in-roles.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="32-change-default-passwords-where-applicable"></a>3.2: 既定のパスワードを変更する (該当する場合)

**ガイダンス**: Azure Active Directory (Azure AD) には、既定のパスワードの概念がありません。

MariaDB リソース自体の作成時に、Azure では強力なパスワードを使用する管理ユーザーの作成が強制されます。 ただし、MariaDB インスタンスが作成されたら、アカウントを作成した最初のサーバー管理者アカウントを使用して、追加のユーザーを作成し、それらのユーザーに管理アクセス権を付与することができます。 これらのアカウントを作成するときは、必ず、アカウントごとに異なる強力なパスワードを構成してください。

- [MariaDB の追加アカウントを作成する方法](howto-create-users.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: 専用管理者アカウントを使用する

**ガイダンス**: MariaDB インスタンスにアクセスできる専用管理者アカウントの使用に関する標準的な操作手順を作成します。 Azure Security Center ID とアクセス管理を使用して、管理者アカウントの数を監視します。

- [Azure Security Center ID とアクセスについて](../security-center/security-center-identity-access.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Azure Active Directory シングル サインオン (SSO) を使用する

**ガイダンス**: MariaDB へのデータ プレーン アクセスは、データベース内に格納されている ID によって制御され、SSO はサポートされていません。 MariaDB のコントロール プレーン アクセスは、REST API 経由で利用でき、SSO がサポートされます。 認証を行うには、要求の Authorization ヘッダーを、Azure Active Directory (Azure AD) から取得した JSON Web トークンに設定します。

- [Azure Database for MariaDB REST API について](/rest/api/mariadb/)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: すべての Azure Active Directory ベースのアクセスに多要素認証を使用する

**ガイダンス**: Azure Active Directory (Azure AD) の多要素認証を有効にし、Azure Security Center の ID およびアクセス管理の推奨事項に従います。

- [Azure で多要素認証を有効にする方法](../active-directory/authentication/howto-mfa-getstarted.md)

- [Azure Security Center で ID とアクセスを監視する方法](../security-center/security-center-identity-access.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3.6: セキュリティで保護された Azure マネージド ワークステーションを管理タスクに使用する

**ガイダンス**: 多要素認証が構成された PAW (特権アクセス ワークステーション) を使用して Azure リソースにログインし、そのリソースを構成します。 

- [特権アクセス ワークステーションについて](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Azure で多要素認証を有効にする方法](../active-directory/authentication/howto-mfa-getstarted.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: 管理者アカウントからの疑わしいアクティビティに関するログとアラート

**ガイダンス**:MariaDB 用に Advanced Threat Protection を有効にして、疑わしいアクティビティに関するアラートを生成します。

さらに、Azure Active Directory (Azure AD) Privileged Identity Management (PIM) を使用して、環境内で疑わしいまたは安全でないアクティビティが発生したときにログとアラートを生成できます。 Azure AD のリスク検出を使用して、危険なユーザーの行動に関するアラートとレポートを表示します。

- [MariaDB 用に Advanced Threat Protection を設定する方法](howto-database-threat-protection-portal.md)

- [Privileged Identity Management (PIM) をデプロイする方法](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Azure AD のリスク検出の概要](/azure/active-directory/reports-monitoring/concept-risk-events)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8:承認された場所からのみ Azure リソースを管理する

**ガイダンス**: 条件付きアクセスのネームド ロケーションを使用して、特定の論理グループ (IP アドレス範囲または国や地域) のみからのアクセスを許可し、MariaDB などの Azure リソースへのアクセスを制限します。

- [Azure でネームド ロケーションを構成する方法](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="39-use-azure-active-directory"></a>3.9: Azure Active Directory を使用する

**ガイダンス**: Azure Active Directory (Azure AD) を中央認証と承認システムとして使用します。 Azure AD でデータを保護するには、保存データと転送中のデータに強力な暗号化を使用します。 また、Azure AD では、ユーザーの資格情報がソルト化およびハッシュされ、安全に格納されます。

MariaDB データ プレーンへの直接アクセスに Azure AD 認証を使用することはできません。ただし、MariaDB 管理者アカウントを制御するために、Azure AD の資格情報を (Azure portal などの) 管理プレーン レベルで管理に使用できます。

- [MariaDB の管理者パスワードを更新する方法](https://docs.microsoft.com/azure/mariadb/howto-create-manage-server-portal#update-admin-password)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: ユーザー アクセスを定期的に確認して調整する

**ガイダンス**: Azure Active Directory (Azure AD) ログを確認します。これは、古いアカウント (MariaDB 管理ロールを持つアカウントが含まれる場合がある) を検出するのに役立ちます。 また、Azure ID アクセス レビューを使用して、グループ メンバーシップ、MariaDB へのアクセスに使用される可能性のあるエンタープライズ アプリケーションへのアクセス、およびロールの割り当てを効率的に管理します。 ユーザー アクセスを定期的 (たとえば、90 日ごと) に確認し、適切なユーザーだけが継続してアクセスできるようにする必要があります。

- [Azure AD のレポートについて](/azure/active-directory/reports-monitoring/)

- [Azure ID アクセス レビューの使用方法](../active-directory/governance/access-reviews-overview.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: 非アクティブ化された資格情報へのアクセスの試行を監視する

**ガイダンス**: MariaDB および Azure Active Directory (Azure AD) の診断設定を有効にし、すべてのログを Log Analytics ワークスペースに送信します。 Log Analytics ワークスペース内で、必要なアラート (認証試行の失敗など) を構成します。

- [MariaDB のサーバー ログの構成およびアクセス方法](concepts-server-logs.md)

- [MariaDB の監査ログの構成およびアクセス方法](howto-configure-audit-logs-portal.md)

- [Azure アクティビティ ログを Azure Monitor に統合する方法](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: アカウント サインイン動作の偏差に関するアラートを生成する

**ガイダンス**: MariaDB 用に Advanced Threat Protection を有効にします。 Azure Database for MariaDB 用の Advanced Threat Protection は、データベースにアクセスしたりそれを悪用したりしようとする、通常とは異なり、かつ有害なおそれのある試みを示す異常なアクティビティを検出します。

検出された疑わしいアクションに対する自動応答を構成するには、Azure Active Directory (Azure AD) の Identity Protection とリスク検出機能を使用します。 Azure Sentinel で自動応答を有効にして、組織のセキュリティ対応を実装することができます。

- [MariaDB 用に Advanced Threat Protection を有効にする方法](howto-database-threat-protection-portal.md)

- [Identity Protection のリスク ポリシーを構成して有効にする方法](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Azure AD の危険なサインインを表示する方法](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Azure Sentinel をオンボードする方法](../sentinel/quickstart-onboard.md)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="data-protection"></a>データ保護

*詳細については、[Azure セキュリティ ベンチマークの「データ保護](../security/benchmarks/security-control-data-protection.md)」を参照してください。*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: 機密情報のインベントリを維持する

**ガイダンス**:タグを使用すると、機密情報を格納または処理する Azure Database for MariaDB インスタンスや関連リソースの追跡に役立ちます。

- [タグを作成して使用する方法](/azure/azure-resource-manager/resource-group-using-tags)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2:機密情報を格納または処理するシステムを分離する

**ガイダンス**:開発、テスト、および運用で別々のサブスクリプションまたは管理グループ、あるいはその両方を実装します。 Private Link、サービスエンド ポイント、または MariaDB ファイアウォール規則の組み合わせを使用して、MariaDB インスタンスへのネットワーク アクセスを分離して制限します。

- [追加の Azure サブスクリプションを作成する方法](/azure/billing/billing-create-subscription)

- [管理グループを作成する方法](/azure/governance/management-groups/create)

- [Azure Database for MariaDB 用に Private Link を構成する方法](concepts-data-access-security-private-link.md)

- [Azure Database for MariaDB のサービス エンドポイントの構成方法](howto-manage-vnet-portal.md)

- [Azure Database for MariaDB のファイアウォール規則の構成方法](concepts-firewall-rules.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3:機密情報の承認されていない転送を監視してブロックする

**ガイダンス**:Azure VM を使用して MariaDB インスタンスにアクセスする場合は、Private Link、MariaDB ネットワーク構成、ネットワーク セキュリティ グループ、およびサービス タグを利用して、データ流出の可能性を軽減します。

Microsoft では、MariaDB 用の基になるインフラストラクチャを管理し、顧客データの損失や漏洩を防ぐための厳格な管理を実施してきました。

- [Azure Database for MariaDB のデータ流出を軽減する方法](concepts-data-access-security-private-link.md)

- [Azure での顧客データの保護について](../security/fundamentals/protection-customer-data.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4:転送中のすべての機密情報を暗号化する

**ガイダンス**: Azure Database for MariaDB では、以前は Secure Sockets Layer (SSL) と呼ばれていたトランスポート層セキュリティ (TLS) を使用した、Azure Database for MariaDB サーバーのクライアント アプリケーションへの接続がサポートされます。 お使いのデータベース サーバーとクライアント アプリケーション間に TLS 接続を強制すると、サーバーとアプリケーション間のデータ ストリームが暗号化され、中間者攻撃から保護するのに役立ちます。 Azure portal で、すべての MariaDB インスタンスに対して **[SSL 接続を強制する]** が有効になっていることを確認します。

- [MariaDB 用に転送中の暗号化を構成する方法](howto-configure-ssl.md)

**責任**: 共有

**Azure Security Center の監視**: なし

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5:アクティブ検出ツールを使用して機密データを特定する

**ガイダンス**: データの識別、分類、および損失防止機能は、Azure Database for MariaDB ではまだ使用できません。 コンプライアンスのために必要な場合は、サードパーティ ソリューションを実装します。

Microsoft によって管理される基になるプラットフォームの場合、Microsoft は顧客のすべてのコンテンツを機密として扱い、顧客データを損失や漏洩から保護するためにあらゆる手段を尽くします。 Azure 内の顧客データが確実にセキュリティで保護されるように、Microsoft では一連の堅牢なデータ保護制御および機能を実装して管理しています。

- [Azure での顧客データの保護について](../security/fundamentals/protection-customer-data.md)

**責任**: 共有

**Azure Security Center の監視**: なし

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6:ロールベースのアクセス制御を使用してリソースへのアクセスを制御する

**ガイダンス**: Azure ロールベースのアクセス制御 (Azure RBAC) を使用して、Azure Database for MariaDB 管理プレーン (Azure portal または Azure Resource Manager) へのアクセスを管理します。 (データベース自体内の) データ プレーン アクセスの場合は、SQL クエリを使用してユーザーを作成し、ユーザーのアクセス許可を構成します。

- [Azure RBAC を構成する方法](../role-based-access-control/role-assignments-portal.md)

- [SQL を使用して MariaDB のユーザー アクセスを構成する方法](howto-create-users.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9:重要な Azure リソースへの変更に関するログとアラート

**ガイダンス**: Azure Monitor と Azure アクティビティ ログを使用して、Azure Database for MariaDB の運用インスタンスやその他の重要なリソースまたは関連リソースに対して変更が行われたときのアラートを作成します。

- [Azure アクティビティ ログ イベントのアラートを作成する方法](/azure/azure-monitor/platform/alerts-activity-log)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="vulnerability-management"></a>脆弱性の管理

*詳細については、[Azure セキュリティ ベンチマークの「脆弱性の管理](../security/benchmarks/security-control-vulnerability-management.md)」を参照してください。*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1:自動化された脆弱性スキャン ツールを実行する

**ガイダンス**: 現在使用できません。Azure Security Center では、Azure Database for MariaDB サーバーの脆弱性評価はまだサポートされていません。

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="inventory-and-asset-management"></a>インベントリと資産の管理

*詳細については、[Azure セキュリティ ベンチマークの「インベントリと資産の管理](../security/benchmarks/security-control-inventory-asset-management.md)」を参照してください。*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1:自動化された資産検出ソリューションを使用する

**ガイダンス**: Azure Resource Graph を使用して、サブスクリプション内のすべてのリソース (Azure Database for MariaDB サーバーを含む) のクエリや検出を行います。 テナント内の適切な (読み取り) アクセス許可を持っており、サブスクリプション内のリソースだけでなく、すべての Azure サブスクリプションを列挙できることを確認します。

- [Azure Resource Graph を使用してクエリを作成する方法](../governance/resource-graph/first-query-portal.md)

- [Azure サブスクリプションを表示する方法](/powershell/module/az.accounts/get-azsubscription)

- [Azure RBAC について](../role-based-access-control/overview.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="62-maintain-asset-metadata"></a>6.2:資産メタデータを保持する

**ガイダンス**: Azure Database for MariaDB サーバーやその他の関連リソースにタグを適用してメタデータを提供し、リソースを論理的に分類して整理します。

- [タグを作成して使用する方法](/azure/azure-resource-manager/resource-group-using-tags)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="63-delete-unauthorized-azure-resources"></a>6.3:承認されていない Azure リソースを削除する

**ガイダンス**: 必要に応じて、タグ付け、管理グループ、および個別のサブスクリプションを使用して、Azure Database for MariaDB サーバーと関連リソースを整理し、追跡します。 定期的にインベントリを調整し、承認されていないリソースがサブスクリプションから適切なタイミングで削除されるようにします。

- [追加の Azure サブスクリプションを作成する方法](/azure/billing/billing-create-subscription)

- [管理グループを作成する方法](/azure/governance/management-groups/create)

- [タグを作成して使用する方法](/azure/azure-resource-manager/resource-group-using-tags)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4:承認された Azure リソースのインベントリを定義および管理する

**ガイダンス**:適用できません。この推奨事項は、コンピューティング リソースと Azure 全体を対象にしています。

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5:承認されていない Azure リソースを監視する

**ガイダンス**: 次の組み込みのポリシー定義を使用して、顧客のサブスクリプション内に作成できるリソースの種類に制限を適用するには、Azure Policy を使用します。

- 許可されないリソースの種類

- 許可されるリソースの種類

また、Azure Resource Graph を使用すると、サブスクリプション内のリソースのクエリまたは検出を行えます。

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

- [Azure Graph を使用してクエリを作成する方法](../governance/resource-graph/first-query-portal.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="69-use-only-approved-azure-services"></a>6.9:承認された Azure サービスのみを使用する

**ガイダンス**: 次の組み込みのポリシー定義を使用して、顧客のサブスクリプション内に作成できるリソースの種類に制限を適用するには、Azure Policy を使用します。

- 許可されないリソースの種類

- 許可されるリソースの種類

詳細については、次のリファレンスを参照してください。

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy を使用して特定のリソースの種類を拒否する方法](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11:Azure Resource Manager を操作するユーザーの機能を制限する

**ガイダンス**: Azure Conditional Access を使用して Azure Resource Manager を操作するユーザーの権限を制限するには、"Microsoft Azure 管理" アプリに対して [アクセスのブロック] を構成します。 これにより、高セキュリティ環境内で機密情報を含む Azure Database for MariaDB サーバーなどのリソースの作成と変更を防ぐことができます。

- [Azure Resource Manager へのアクセスをブロックするように条件付きアクセスを構成する方法](../role-based-access-control/conditional-access-azure-management.md)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="secure-configuration"></a>セキュリティで保護された構成

*詳細については、[Azure セキュリティ ベンチマークの「セキュリティで保護された構成](../security/benchmarks/security-control-secure-configuration.md)」を参照してください。*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1:すべての Azure リソースに対してセキュリティで保護された構成を確立する

**ガイダンス**: Azure Policy を使用して、Azure Database for MariaDB インスタンスの標準的なセキュリティ構成を定義して実装します。 Azure Database for MariaDB サーバーのネットワーク構成を監査または適用するためのカスタム ポリシーを作成するには、"Microsoft.DBforMariaDB" 名前空間の Azure Policy エイリアスを使用します。 次のように、Azure Database for MariaDB サーバーに関連する組み込みのポリシー定義を使用することもできます。

- Azure Database for MariaDB の geo 冗長バックアップを有効にする必要がある

詳細については、次のリファレンスを参照してください。

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

**ガイダンス**: Azure Database for MariaDB サーバーと関連リソースにカスタム Azure Policy 定義を使用する場合は、Azure Repos を使ってコードを安全に格納および管理します。

- [Azure DevOps でコードを格納する方法](/azure/devops/repos/git/gitworkflow)

- [Azure Repos のドキュメント](/azure/devops/repos/)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7:Azure リソース用の構成管理ツールをデプロイする

**ガイダンス**: "Microsoft.DBforMariaDB" 名前空間の Azure Policy エイリアスを使用して、システム構成のアラート通知、監査、適用を行うためのカスタム ポリシーを作成します。 さらに、ポリシー例外を管理するためのプロセスとパイプラインを作成します。

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9:Azure リソースの自動構成監視を実装する

**ガイダンス**: "Microsoft.DBforMariaDB" 名前空間の Azure Policy エイリアスを使用して、システム構成のアラート通知、監査、適用を行うためのカスタム ポリシーを作成します。 Azure Policy の [audit]、[deny]、[deploy if not exist] を使用して、Azure Database for MariaDB インスタンスおよび関連リソースの構成を自動的に適用します。

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="711-manage-azure-secrets-securely"></a>7.11:Azure シークレットを安全に管理する

**ガイダンス**: Azure Database for MariaDB サーバーへのアクセスに使用される Azure App Service で実行されている Azure Virtual Machines または Web アプリケーションの場合は、マネージド サービス ID を Azure Key Vault と組み合わせて使用して、Azure Database for MariaDB サーバー シークレットの管理を簡素化およびセキュリティで保護します。 Key Vault の論理的な削除が有効になっていることを確認します。

- [Azure マネージド ID と統合する方法](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [キー コンテナーを作成する方法](../key-vault/general/quick-create-portal.md)

- [Key Vault に対して認証を行う方法](../key-vault/general/authentication.md)

- [Key Vault のアクセス ポリシーを割り当てる方法](../key-vault/general/assign-access-policy-portal.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="712-manage-identities-securely-and-automatically"></a>7.12:ID を安全かつ自動的に管理する

**ガイダンス**:Azure Database for MariaDB サーバーでは、現在、データベースにアクセスするための Azure Active Directory (Azure AD) 認証はサポートされていません。 Azure Database for MariaDB サーバーを作成するときに、管理者ユーザーの資格情報を指定します。 この管理者は、追加の MariaDB ユーザーを作成するために使用できます。

Azure Database for MariaDB サーバーへのアクセスに使用される Azure App Service で実行されている Azure Virtual Machines または Web アプリケーションの場合は、マネージド ID を Azure Key Vault と組み合わせて使用して、Azure Database for MariaDB サーバーの資格情報を格納および取得します。 Key Vault の論理的な削除が有効になっていることを確認します。

マネージド ID を使用して、Azure AD で自動的に管理される ID を Azure サービスに提供します。 マネージド ID を使用すると、コードに資格情報を追加しなくても、Azure AD の認証をサポートするさまざまなサービス (Key Vault を含む) に対して認証を行うことができます。

- [マネージド ID を構成する方法](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

- [Azure マネージド ID と統合する方法](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13:意図しない資格情報の公開を排除する

**ガイダンス**: コード内で資格情報を特定する資格情報スキャナーを実装します。 また、資格情報スキャナーを使うと、検出された資格情報を、Azure Key Vault などのより安全な場所に移動しやすくなります。 

- [資格情報スキャナーを設定する方法](https://secdevtools.azurewebsites.net/helpcredscan.html)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="malware-defense"></a>マルウェアからの防御

*詳細については、[Azure セキュリティ ベンチマークの「マルウェアからの防御](../security/benchmarks/security-control-malware-defense.md)」を参照してください。*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2:非コンピューティング Azure リソースにアップロードするファイルを事前にスキャンする

**ガイダンス**: Microsoft のマルウェア対策は、Azure サービス (Azure Database for MariaDB サーバーなど) をサポートする基になるホストで有効になっていますが、お客様のコンテンツに対しては実行されません。

App Service、Data Lake Storage、Blob Storage、Azure Database for MariaDB サーバーなどの Azure 非コンピューティング リソースにアップロードされるコンテンツはすべて、事前にスキャンしてください。Microsoft は、これらのインスタンス内のデータにアクセスできません。

**責任**: 共有

**Azure Security Center の監視**: なし

## <a name="data-recovery"></a>データの復旧

*詳細については、[Azure セキュリティ ベンチマークの「データの復旧](../security/benchmarks/security-control-data-recovery.md)」を参照してください。*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1:定期的な自動バックアップを保証する

**ガイダンス**: Azure Database for MariaDB では、完全バックアップ、差分バックアップ、およびトランザクション ログ バックアップが作成されます。  Azure Database for MariaDB は、サーバーのバックアップを自動的に作成し、ユーザーが構成したローカル冗長または geo 冗長ストレージに保存します。 バックアップを使用すると、サーバーを特定の時点に復元できます。 不慮の破損または削除からデータを保護するバックアップと復元は、ビジネス継続性戦略の最も重要な部分です。  バックアップの既定のリテンション期間は 7 日です。 これは、必要に応じて、最大 35 日に設定できます。 すべてのバックアップが、AES 256 ビット暗号化を使用して暗号化されます。

- [MariaDB のバックアップについて](concepts-backup.md)

- [MariaDB の初期構成について](tutorial-design-database-using-portal.md)

**責任**: 共有

**Azure Security Center の監視**: [Azure セキュリティ ベンチマーク](/azure/governance/policy/samples/azure-security-benchmark)は、Security Center の既定のポリシー イニシアチブであり、[Security Center の推奨事項](/azure/security-center/security-center-recommendations)の基礎となります。 このコントロールに関連する Azure Policy 定義は、Security Center によって自動的に有効になります。 この制御に関連するアラートでは、関連するサービスのために [Azure Defender](/azure/security-center/azure-defender) プランが必要になる場合があります。

**Azure Policy 組み込み定義 - Microsoft.DBforMariaDB**:

[!INCLUDE [Resource Policy for Microsoft.DBforMariaDB 9.1](../../includes/policy/standards/asb/rp-controls/microsoft.dbformariadb-9-1.md)]

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: システムの完全バックアップを実行し、すべてのカスタマー マネージド キーをバックアップする

**ガイダンス**: Azure Database for MariaDB は、サーバーのバックアップを自動的に作成し、ユーザーが構成したローカル冗長または geo 冗長ストレージに保存します。 バックアップを使用すると、サーバーを特定の時点に復元できます。  不慮の破損または削除からデータを保護するバックアップと復元は、ビジネス継続性戦略の最も重要な部分です。

MariaDB サーバーに格納されているデータに対するクライアント側のデータ暗号化のために Key Vault を使用する場合は、キーの定期的な自動バックアップが確実に行われるようにします。

- [MariaDB のバックアップについて](concepts-backup.md)

- [Key Vault のキーをバックアップする方法](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

**責任**: 共有

**Azure Security Center の監視**: [Azure セキュリティ ベンチマーク](/azure/governance/policy/samples/azure-security-benchmark)は、Security Center の既定のポリシー イニシアチブであり、[Security Center の推奨事項](/azure/security-center/security-center-recommendations)の基礎となります。 このコントロールに関連する Azure Policy 定義は、Security Center によって自動的に有効になります。 この制御に関連するアラートでは、関連するサービスのために [Azure Defender](/azure/security-center/azure-defender) プランが必要になる場合があります。

**Azure Policy 組み込み定義 - Microsoft.DBforMariaDB**:

[!INCLUDE [Resource Policy for Microsoft.DBforMariaDB 9.2](../../includes/policy/standards/asb/rp-controls/microsoft.dbformariadb-9-2.md)]

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3:カスタマー マネージド キーを含むすべてのバックアップを検証する

**ガイダンス**: Azure Database for MariaDB で、バックアップを定期的にテストするために元のサーバーのバックアップから復元を実行します。 使用できる復元には 2 つの種類があります。ポイントインタイム リストアと geo リストアです。 ポイントインタイム リストアは、いずれのバックアップ冗長オプションでも使用でき、元のサーバーと同じリージョンに新しいサーバーが作成されます。 geo リストアは、サーバーを geo 冗長ストレージ用に構成した場合にのみ使用でき、ご利用のサーバーを別のリージョンに復元できます。

復旧の推定所要時間は、データベースのサイズ、トランザクション ログのサイズ、ネットワーク帯域幅、同じリージョン内で同時に復旧するデータベースの合計数など、複数の要因によって異なります。 通常は 12 時間もかかりません。

- [Azure Database for MariaDB でのバックアップと復元について](https://docs.microsoft.com/azure/mariadb/concepts-backup#restore)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: バックアップとカスタマー マネージド キーの保護を保証する

**ガイダンス**: Azure Database for MariaDB では、完全バックアップ、差分バックアップ、およびトランザクション ログ バックアップが作成されます。 これらのバックアップを使用すると、サーバーを、バックアップの構成済みリテンション期間内の任意の時点に復元できます。 バックアップの既定のリテンション期間は 7 日です。 これは、必要に応じて、最大 35 日に設定できます。 すべてのバックアップが、AES 256 ビット暗号化を使用して暗号化されます。

- [Azure Database for MariaDB でのバックアップと復元について](concepts-backup.md)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="incident-response"></a>インシデント対応

*詳細については、[Azure セキュリティ ベンチマークの「インシデント対応](../security/benchmarks/security-control-incident-response.md)」を参照してください。*

### <a name="101-create-an-incident-response-guide"></a>10.1:インシデント対応ガイドを作成する

**ガイダンス**: 組織のインシデント対応ガイドを作成します。 要員のすべてのロールを定義するインシデント対応計画が記述されていることと、検出からインシデント後のレビューまでのインシデント対応/管理のフェーズがあることを確認します。

- [独自のセキュリティ インシデント対応プロセスを構築するためのガイダンス](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft Security Response Center のインシデントの構造](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [お客様は、独自のインシデント対応計画の作成に役立つ NIST の「コンピューター セキュリティ インシデント対応ガイド」を利用することもできます](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2:インシデントのスコアリングと優先順位付けの手順を作成する

**ガイダンス**: Azure Security Center によって各アラートに重大度が割り当てられるため、最初に調査する必要があるアラートの優先順位付けに役立ちます。 重要度は、アラートの発行に使用された調査結果またはメトリックを Security Center がどの程度信頼しているかということと、アラートの原因となったアクティビティの背後に悪意のある意図があったことについての確信の度合いに基づくものです。

さらに、タグを使用してサブスクリプションを明確にマークし、Azure リソース (特に、機密データを処理するもの) を明確に識別して分類するための命名システムを作成します。 インシデントが発生した Azure リソースと環境の重要度に基づいて、アラートの修復に優先順位を付けることは、お客様の責任です。

- [Security alerts in Azure Security Center](../security-center/security-center-alerts-overview.md)

- [タグを使用した Azure リソースの整理](/azure/azure-resource-manager/resource-group-using-tags)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="103-test-security-response-procedures"></a>10.3:セキュリティ対応手順のテスト

**ガイダンス**: ご利用のシステムのインシデント対応機能を定期的にテストする演習を実施することで、ご利用の Azure リソースの保護を支援できます。 弱点やギャップを特定し、必要に応じて計画を見直します。

- [NIST の出版物「IT 計画と機能に関するテスト、トレーニング、演習プログラムのガイド」を参照してください。](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4:セキュリティ インシデントの連絡先の詳細を指定し、セキュリティ インシデントのアラート通知を構成します

**ガイダンス**:セキュリティ インシデントの連絡先情報は、Microsoft Security Response Center (MSRC) でユーザーのデータが違法または権限のないユーザーによってアクセスされたことが検出された場合に、Microsoft からの連絡先として使用されます。 事後にインシデントをレビューして、問題が解決されていることを確認します。

- [Azure Security Center のセキュリティ連絡先を設定する方法](../security-center/security-center-provide-security-contact-details.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5:インシデント対応システムにセキュリティ アラートを組み込む

**ガイダンス**: 連続エクスポート機能を使用して Azure Security Center のアラートと推奨事項をエクスポートすると、Azure リソースへのリスクを特定できます。 連続エクスポートを使用すると、アラートと推奨事項を手動で、または継続した連続的な方法でエクスポートできます。 Azure Security Center データ コネクタを使用してアラートを Azure Sentinel にストリーミングできます。

- [連続エクスポートを構成する方法](../security-center/continuous-export.md)

- [Azure Sentinel にアラートをストリーミングする方法](../sentinel/connect-azure-security-center.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="106-automate-the-response-to-security-alerts"></a>10.6:セキュリティ アラートへの対応を自動化する

**ガイダンス**: Azure Security Center のワークフロー自動化機能を使用すると、セキュリティのアラートと推奨事項に対して "Logic Apps" で自動的に応答をトリガーし、Azure リソースを保護できます。
    

- [ワークフローの自動化と Logic Apps を構成する方法](../security-center/workflow-automation.md)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="penetration-tests-and-red-team-exercises"></a>侵入テストとレッド チーム演習

*詳細については、[Azure セキュリティ ベンチマークの「侵入テストとレッド チーム演習](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)」を参照してください。*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1:Azure リソースの通常の侵入テストを実施し、セキュリティに関する重大な調査結果がすべて、確実に修復されるようにする

**ガイダンス**: お客様の侵入テストが Microsoft のポリシーに違反しないように、Microsoft クラウド侵入テストの実施ルールに従ってください。 Microsoft が管理しているクラウド インフラストラクチャ、サービス、アプリケーションに対する Red Teaming およびライブ サイト侵入テストに関する Microsoft の戦略と実施を活用してください。 

- [侵入テストの実施ルール](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**責任**: 共有

**Azure Security Center の監視**: なし

## <a name="next-steps"></a>次のステップ

- 「[Azure セキュリティ ベンチマーク V2 の概要](/azure/security/benchmarks/overview)」を参照してください。
- [Azure セキュリティ ベースライン](/azure/security/benchmarks/security-baselines-overview)の詳細について学習する
