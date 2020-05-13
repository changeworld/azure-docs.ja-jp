---
title: Azure Databricks 用の Azure セキュリティ ベースライン
description: Azure Databricks 用の Azure セキュリティ ベースライン
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 7d417f412592d87dec751b9d98a26bf786c719e5
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/05/2020
ms.locfileid: "82793919"
---
# <a name="azure-security-baseline-for-azure-databricks"></a>Azure Databricks 用の Azure セキュリティ ベースライン

Azure Databricks 用の Azure セキュリティ ベースラインには、ご自身のデプロイのセキュリティ体制を改善するために役立つ推奨事項が含まれています。

このサービス用のベースラインは、ベスト プラクティス ガイダンスを使用して Azure 上のクラウド ソリューションをセキュリティで保護する方法について推奨事項を提供する [Azure セキュリティ ベンチマーク バージョン 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview) に基づいて作成されています。

詳細については、「[Azure セキュリティ ベースラインの概要](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)」を参照してください。

## <a name="network-security"></a>ネットワークのセキュリティ

*詳細については、「[セキュリティ コントロール: ネットワークのセキュリティ](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security)」を参照してください。*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1:Virtual Network でネットワーク セキュリティ グループまたは Azure Firewall を使用してリソースを保護する

**ガイダンス**: ご自分の Azure 仮想ネットワーク (VNet) に Azure Databricks をデプロイします。 Azure Databricks の既定のデプロイは、Azure 上のフル マネージド サービスです。すべてのクラスターが関連付けられる VNet を含むすべてのデータ プレーン リソースは、ロックされたリソース グループにデプロイされます。 ただし、ネットワークのカスタマイズが必要な場合は、Azure Databricks データ プレーン リソースをご自分の仮想ネットワークにデプロイして (VNet インジェクション)、カスタム ネットワーク構成を実装できます。 ご自分のネットワーク セキュリティ グループ (NSG) とカスタム ルールを特定のエグレス トラフィック制限に適用できます。

さらに、NSG ルールを構成して、Azure Databricks インスタンスがデプロイされているサブネットのエグレス トラフィック制限を指定することもできます。 Azure Firewall は、VNET が挿入されたワークスペース用に構成できます。

* [ご自分の Virtual Network に Azure Databricks をデプロイする方法](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)

* [NSG を管理する方法](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2:Vnet、サブネット、NIC の構成とトラフィックを監視してログに記録する

**ガイダンス**: ご自分の Azure 仮想ネットワーク (VNet) に Azure Databricks をデプロイします。 ネットワーク セキュリティ グループ (NSG) は自動的に作成されません。 既定の Azure ルールのみを使用してベースライン NSG を作成する必要があります。 ワークスペースをデプロイするときに、Databricks に必要なルールが追加されます。 空の NSG から始めることもできます。適切なルールが自動的に追加されます。 NSG フロー ログを有効にし、トラフィック監査のためにログをストレージ アカウントに送信します。 また、NSG フロー ログを Log Analytics ワークスペースに送信し、Traffic Analytics を使用して Azure クラウド内のトラフィック フローに関する分析情報を提供することもできます。 Traffic Analytics のいくつかの利点として、ネットワーク アクティビティを視覚化してホット スポットを特定したり、セキュリティの脅威を識別したり、トラフィック フロー パターンを把握したり、ネットワークの誤った構成の正確な場所を特定したりする機能が挙げられます。

* [ご自分の Virtual Network に Azure Databricks をデプロイする方法](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)

* [NSG フロー ログを有効にする方法](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

* [Traffic Analytics を有効にして使用する方法](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

* [Network Watcher を有効にする方法](https://docs.microsoft.com/azure/network-watcher/network-watcher-create)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="13-protect-critical-web-applications"></a>1.3:重要な Web アプリケーションを保護する

**ガイダンス**: 適用できません。この推奨事項は、Azure App Service またはコンピューティング リソース上で実行されている Web アプリケーションを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4:既知の悪意のある IP アドレスとの通信を拒否する

**ガイダンス**: 分散型サービス拒否攻撃からの保護のために、Azure Databricks インスタンスに関連付けられている Azure 仮想ネットワーク上で Azure DDoS Protection Standard を有効にします。 Azure Security Center の統合された脅威インテリジェンスを使用して、既知の悪意のある、または未使用のパブリック IP アドレスとの通信を拒否します。

* [Azure portal を使用した Azure DDoS Protection Standard の管理](https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection)

* [Azure Security Center での Azure ネットワーク レイヤーに対する脅威の防止の概要](https://docs.microsoft.com/azure/security-center/threat-protection#threat-protection-for-azure-network-layer-)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="15-record-network-packets-and-flow-logs"></a>1.5:ネットワーク パケットとフロー ログを記録する

**ガイダンス**: ご自分の Azure 仮想ネットワーク (VNet) に Azure Databricks をデプロイします。 ネットワーク セキュリティ グループ (NSG) は自動的に作成されません。 既定の Azure ルールのみを使用してベースライン NSG を作成する必要があります。 ワークスペースをデプロイするときに、Databricks に必要なルールが追加されます。 NSG フロー ログを有効にし、トラフィック監査のためにログをストレージ アカウントに送信します。 また、NSG フロー ログを Log Analytics ワークスペースに送信し、Traffic Analytics を使用して Azure クラウド内のトラフィック フローに関する分析情報を提供することもできます。 Traffic Analytics のいくつかの利点として、ネットワーク アクティビティを視覚化してホット スポットを特定したり、セキュリティの脅威を識別したり、トラフィック フロー パターンを把握したり、ネットワークの誤った構成の正確な場所を特定したりする機能が挙げられます。

* [ご自分の Virtual Network に Azure Databricks をデプロイする方法](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)

* [NSG フロー ログを有効にする方法](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

* [Traffic Analytics を有効にして使用する方法](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

* [Network Watcher を有効にする方法](https://docs.microsoft.com/azure/network-watcher/network-watcher-create)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6:ネットワーク ベースの侵入検出/侵入防止システム (IDS/IPS) をデプロイする

**ガイダンス**: Azure Marketplace から IDP または IPS 対応のネットワーク仮想アプライアンス (NVA) を実装して、すべての Azure Databricks クラスターが 1 つの IP 送信アドレスを持つ仮想ネットワーク統合ワークスペースを作成します。 この 1 つの IP アドレスは、特定の IP アドレスに基づいてアクセスを許可する他の Azure サービスとアプリケーションで追加のセキュリティ層として使用できます。 Azure ファイアウォール、または NVA などの他のサード パーティ製ツールを使用して、イングレス トラフィックとエグレス トラフィックを管理できます。

ペイロード検査に基づく侵入検出または侵入防止が要件でない場合は、脅威インテリジェンスを備えた Azure Firewall を使用できます。 Azure Firewall の脅威インテリジェンス ベースのフィルター処理では、既知の悪意のある IP アドレスおよびドメインとの間のトラフィックを警告処理して拒否できます。 この IP アドレスとドメインのソースは、Microsoft の脅威インテリジェンス フィードです。

* [Azure Firewall を使用して VNet 組み込みのワークスペースに対して 1 つのパブリック IP を割り当てる方法](https://docs.microsoft.com/azure/databricks/kb/cloud/azure-vnet-single-ip)

* [NVA を作成する方法](https://docs.microsoft.com/azure/virtual-network/tutorial-create-route-table-portal)

* [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="17-manage-traffic-to-web-applications"></a>1.7:Web アプリケーションへのトラフィックを管理する

**ガイダンス**: 適用できません。この推奨事項は、Azure App Service またはコンピューティング リソース上で実行されている Web アプリケーションを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8:ネットワーク セキュリティ規則の複雑さと管理オーバーヘッドを最小限に抑える

**ガイダンス**: Azure Databricks インスタンスが関連付けられているサブネットに接続されているネットワーク セキュリティ グループに対して、サービス タグを使用してネットワーク アクセス コントロールを定義します。 セキュリティ規則を作成するときは、特定の IP アドレスの代わりにサービス タグを使うことができます。 規則の適切なソースまたは宛先フィールドにサービス タグ名 (ApiManagement など) を指定することにより、対応するサービスのトラフィックを許可または拒否できます。 サービス タグに含まれるアドレス プレフィックスの管理は Microsoft が行い、アドレスが変化するとサービス タグは自動的に更新されます。 サービス タグは、挿入されていない VNET ワークスペースではサポートされていません。

* [サービス タグの概要](https://docs.microsoft.com/azure/virtual-network/service-tags-overview)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9:ネットワーク デバイスの標準的なセキュリティ構成を維持する

**ガイダンス**: Azure Policy を使用して、Azure Databricks インスタンスのネットワーク セキュリティ構成を定義および実装します。 "Microsoft.Databricks" 名前空間の Azure Policy エイリアスを使用して、カスタム ポリシー定義を定義できます。 ポリシーは、マネージド リソース グループ内のリソースには適用されません。

また、Azure Blueprints を使用して、Azure Resource Management テンプレート、ロールベースのアクセス制御 (RBAC)、ポリシーなどの主要な環境成果物を単一のブループリント定義にパッケージ化することによって大規模な Azure デプロイを簡略化することもできます。 ブループリントを新しいサブスクリプションと環境に簡単に適用し、バージョン管理によって制御と管理を微調整します。

* [Azure Policy を構成して管理する方法](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Azure Policy エイリアスと定義構造の概要](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure)

* [Azure Blueprint を作成する方法](https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="110-document-traffic-configuration-rules"></a>1.10:トラフィック構成規則を文書化する

**ガイダンス**: Azure Databricks インスタンスに関連付けられているネットワーク セキュリティとトラフィック フローに関連する NSG とその他のリソースのタグを使用します。 個々の NSG 規則については、[説明] フィールドを使用して、ネットワークとの間のトラフィックを許可する規則のビジネス ニーズや期間 (など) を指定します。

* [タグを作成して使用する方法](httphttps://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11:自動化ツールを使用してネットワーク リソース構成を監視し、変更を検出する

**ガイダンス**: Azure アクティビティ ログを使用して、ネットワーク リソース構成を監視し、Azure Databricks インスタンスに関連するネットワーク リソースの変更を検出します。 重要なネットワーク リソースへの変更が発生するとトリガーされる Azure Monitor 内のアラートを作成します。

* [Azure アクティビティ ログ イベントを表示して取得する方法](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)

* [Azure Monitor でアラートを作成する方法](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="logging-and-monitoring"></a>ログ記録と監視

*詳細については、「[セキュリティ コントロール: ログ記録と監視](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring)」を参照してください。*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1:承認された時刻同期ソースを使用する

**ガイダンス**: Microsoft は Azure リソースのためにタイム ソースを管理していますが、コンピューティング リソースのために時刻同期設定を管理するオプションが用意されています。 Azure Databricks は PaaS サービスであるため、Azure Databricks クラスター内の VM に直接アクセスすることはできません。また、時刻の同期設定を設定することはできません。

**Azure Security Center の監視**: 適用なし

**責任**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2:セキュリティ ログの一元管理を構成する

**ガイダンス**: Azure Monitor を介してログを取り込み、Azure Databricks によって生成されたセキュリティ データを集計します。 Azure Monitor 内で、Databricks ログと診断ログを受信するように構成されている Log Analytics ワークスペースに対してクエリを実行できます。 他のシステムにデータをエクスポートするには、長期およびアーカイブのログ ストレージまたはイベント ハブに Azure Storage アカウントを使用します。 または、Azure Sentinel またはサードパーティのセキュリティ インシデントおよびイベント管理 (SIEM) に対してデータを有効にしてオンボードすることもできます。

注:Azure Databricks の診断ログには Azure Databricks Premium プランが必要です

* [診断設定を構成する方法](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings#create-diagnostic-settings-in-azure-portal)

* [Azure Sentinel をオンボードする方法](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

* [Azure Monitor とサードパーティの SIEM 統合を開始する方法](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3:Azure リソースの監査ログ記録を有効にする

**ガイダンス**: Azure アクティビティ ログの診断設定を有効にして、Log Analytics ワークスペース、Azure イベント ハブ、または Azure ストレージア カウントにログを送信してアーカイブします。 Azure アクティビティ ログのデータを使用すると、Azure リソースのコントロール プレーン レベルで実行された書き込み操作 (PUT、POST、DELETE) について、"いつだれが何を" 行ったのかを特定することができます。

監査ログについては、Azure Databricks ユーザーが実行したアクティビティの包括的なエンドツーエンドの診断ログが Azure Databricks に用意されているため、企業は詳細な Azure Databricks の使用パターンを監視できます。

注:Azure Databricks の診断ログには Azure Databricks Premium プランが必要です

* [Azure アクティビティ ログの診断設定を有効にする方法](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy))

* [Azure Databricks の診断設定を有効にする方法](https://docs.microsoft.com/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4:オペレーティング システムからセキュリティ ログを収集する

**ガイダンス**: Azure Databricks ユーザーが実行したアクティビティの包括的なエンドツーエンドの診断ログが Azure Databricks に用意されているため、企業は詳細な Azure Databricks の使用パターンを監視できます。

注:Azure Databricks の診断ログには Azure Databricks Premium プランが必要です。 OS セキュリティ ログは使用できません。

* [Azure アクティビティ ログの診断設定を有効にする方法](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

* [Azure Databricks の診断設定を有効にする方法](https://docs.microsoft.com/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="25-configure-security-log-storage-retention"></a>2.5:セキュリティ ログのストレージ保持を構成する

**ガイダンス**: Azure Databricks の診断設定を有効にします。 Log Analytics ワークスペースにログを保存することを選択した場合は、組織のコンプライアンス規則に従って Log Analytics ワークスペースの保持期間を設定します。 長期/アーカイブ ストレージには Azure Storage アカウントを使用します。 セキュリティ関連のアクティビティは、Databricks 監査ログで追跡されます。

注:Azure Databricks の診断ログには Azure Databricks Premium プランが必要です

* [Azure Databricks の診断設定を有効にする方法](https://docs.microsoft.com/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs)

* [Log Analytics ワークスペースのログ保持パラメーターを設定する方法](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="26-monitor-and-review-logs"></a>2.6:ログを監視して確認する

**ガイダンス**: Azure Databricks の診断設定を有効にし、Log Analytics ワークスペースにログを送信します。 Log Analytics ワークスペースを使用して、Azure Databricks ログを分析して異常な動作を監視し、定期的に結果を確認します。

または、Azure Sentinel またはサード パーティの SIEM に対してデータを有効にしてオンボードすることもできます。

注:Azure Databricks の診断ログには Azure Databricks Premium プランが必要です

* [Azure Databricks の診断設定を有効にする方法](https://docs.microsoft.com/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs)

* [Log Analytics ワークスペースに送信された Azure Databricks ログに対してクエリを実行する方法](https://docs.microsoft.com/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs#analyze-diagnostic-logs)

* [Azure Sentinel をオンボードする方法](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7:異常なアクティビティについてのアラートを有効にする

**ガイダンス**: Azure Databricks インスタンスの診断設定を構成し、Log Analytics ワークスペースにログを送信します。 Log Analytics ワークスペース内で、事前に定義された一連の条件が発生したときに、アラートが発生するように構成します。

または、Azure Sentinel またはサード パーティの SIEM に対してデータを有効にしてオンボードすることもできます。

注:Azure Databricks の診断ログには Azure Databricks Premium プランが必要です

* [Azure Databricks ログを Log Analytics ワークスペースに送信する方法](https://docs.microsoft.com/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs#configure-diagnostic-log-delivery))

* [Log Analytics ワークスペースでアラートを構成する方法](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="28-centralize-anti-malware-logging"></a>2.8:マルウェア対策のログ記録を一元管理する

**ガイダンス**: 適用不可。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="29-enable-dns-query-logging"></a>2.9:DNS クエリのログ記録を有効にする

**ガイダンス**: 適用できません。Azure Databricks では、ユーザーがアクセスできる DNS 関連のログが処理または生成されません。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="210-enable-command-line-audit-logging"></a>2.10:コマンドライン監査ログ記録を有効にする

**ガイダンス**: 適用できません。このガイドラインは、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

## <a name="identity-and-access-control"></a>ID とアクセスの制御

*詳細については、「[セキュリティ コントロール: ID およびアクセス制御](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control)」を参照してください。*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: 管理アカウントのインベントリを維持する

**ガイダンス**: Azure Databricks SCIM API を使用して Azure Databricks ワークスペース内のユーザーを管理し、指定したユーザーに管理者特権を付与できます。 また、Azure Databricks UI を使用してそれらを管理することもできます。

* [SCIM API の使用方法](https://docs.microsoft.com/azure/databricks/dev-tools/api/latest/scim/)

* [Azure Databricks でユーザーを追加および削除する方法](https://docs.microsoft.com/azure/databricks/administration-guide/users-groups/users)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="32-change-default-passwords-where-applicable"></a>3.2: 既定のパスワードを変更する (該当する場合)

**ガイダンス**: Azure Databricks では、Azure portal と Azure Databricks 管理コンソールへのアクセス権を付与するために、Azure Active Directory (AD) が使用されています。 Azure AD には既定のパスワードという概念はありませんが、カスタム アプリケーションまたはサードパーティ製アプリケーションの既定のパスワードは、お客様が変更または禁止する必要があります。

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: 専用管理者アカウントを使用する

**ガイダンス**: Azure Databricks SCIM API を使用して、Azure Databricks に管理者特権を持つユーザーを追加できます。 また、Azure Databricks UI を使用してそれらを管理することもできます。

* [SCIM API の使用方法](https://docs.microsoft.com/azure/databricks/dev-tools/api/latest/scim/)

* [Azure Databricks でユーザーを追加および削除する方法](https://docs.microsoft.com/azure/databricks/administration-guide/users-groups/users)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: シングル サインオン (SSO) と Azure Active Directory を統合する

**ガイダンス**: Azure Databricks は Azure Active Directory シングル サインオンを使用してユーザーを認証するように自動的に設定されています。 組織外のユーザーがシングル サインオンで Azure Databricks にログインするには、招待プロセスを完了し、Active Directory テナントに追加される必要があります。 ワークスペースからのユーザーのプロビジョニングとプロビジョニング解除を自動化するには、SCIM を実装します。

* [SCIM API の使用方法](https://docs.microsoft.com/azure/databricks/dev-tools/api/latest/scim/)

* [Azure Databricks のシングル サインオンの概要](https://docs.microsoft.com/azure/databricks/administration-guide/users-groups/single-sign-on/)

* [Azure AD テナントにユーザーを招待する方法](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-external-users)

**Azure Security Center の監視**: 適用なし

**責任**: Microsoft

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: すべての Azure Active Directory ベースのアクセスに多要素認証を使用する

**ガイダンス**: Azure AD MFA を有効にして、Azure Security Center ID とアクセス管理の推奨事項に従います。

* [Azure で MFA を有効にする方法](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

* [Azure Security Center で ID とアクセスを監視する方法](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: すべての管理タスクに専用マシン (特権アクセス ワークステーション) を使用する

**ガイダンス**: MFA が構成されている PAW (特権アクセス ワークステーション) を使用して Azure リソースにログインし、構成します。

* [特権アクセス ワークステーションについて](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)

* [Azure で MFA を有効にする方法](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: 管理者アカウントからの疑わしいアクティビティを記録してアラートを生成する

**ガイダンス**: 環境内で疑わしいアクティビティまたは安全でないアクティビティが環境で発生したときに、Azure Active Directory セキュリティ レポートを使用して、ログおよびアラートを生成します。 Azure Security Center を使用して ID およびアクセス アクティビティを監視します。 また、Azure Databricks 診断ログを利用することもできます。

* [危険なアクティビティのフラグが設定された Azure AD ユーザーを識別する方法](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk)

* [Azure Security Center でユーザーの ID およびアクセス アクティビティを監視する方法](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8:承認された場所からのみ Azure リソースを管理する

**ガイダンス**: 条件付きアクセスのネームド ロケーションを使用して、IP アドレス範囲または国/地域の特定の論理グループからのアクセスのみを許可します。

* [Azure でネームド ロケーションを構成する方法](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="39-use-azure-active-directory"></a>3.9: Azure Active Directory を使用する

**ガイダンス**: Azure Databricks は Azure Active Directory シングル サインオンを使用してユーザーを認証するように自動的に設定されています。 組織外のユーザーがシングル サインオンで Azure Databricks にログインするには、招待プロセスを完了し、Active Directory テナントに追加される必要があります。

* [Azure Databricks のシングル サインオンの概要](https://docs.microsoft.com/azure/databricks/administration-guide/users-groups/single-sign-on/)

* [Azure AD テナントにユーザーを招待する方法](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-external-users)

**Azure Security Center の監視**: 適用なし

**責任**: Microsoft

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: ユーザー アクセスを定期的に確認して調整する

**ガイダンス**: Azure AD には、古いアカウントの検出に役立つログが用意されています。 また、Azure ID アクセス レビューを使用して、グループ メンバーシップ、エンタープライズ アプリケーションへのアクセス、およびロールの割り当てを効率的に管理します。 ユーザー アクセスを定期的にレビューし、適切なユーザーのみが継続的なアクセス権を持っていることを確認できます。 SCIM API と Azure Databricks 診断ログを実装して、ユーザー アクセスを確認することもできます。 SCIM API と Azure Databricks 診断ログを使用して、ユーザー アクセスを確認することもできます。

さらに、Azure Databricks 管理コンソール内でユーザー アクセスを定期的に確認および管理します。

* [Azure AD レポート](https://docs.microsoft.com/azure/active-directory/reports-monitoring/)

* [Azure ID アクセス レビューの使用方法](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)

* [Azure Databricks 管理コンソール内でユーザー アクセスを管理する方法](https://docs.microsoft.com/azure/databricks/administration-guide/users-groups/users)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: 非アクティブ化されたアカウントへのアクセス試行を監視する

**ガイダンス**: Azure AD サインイン アクティビティ、監査、およびリスク イベント ログのソースにアクセスできるため、任意の SIEM/監視ツールとの統合が可能です。 また、Azure Databricks 診断ログを使用して、ユーザー アクセス アクティビティを確認することもできます。

このプロセスを効率化するには、Azure Active Directory ユーザー アカウントの診断設定を作成し、監査ログとサインイン ログを Log Analytics ワークスペースに送信します。 Log Analytics ワークスペースで必要なアラートを構成できます。

* [SCIM API の使用方法](https://docs.microsoft.com/azure/databricks/dev-tools/api/latest/scim/)

* [Azure アクティビティ ログを Azure Monitor に統合する方法](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: アカウント ログイン動作の偏差に関するアラートを生成する

**ガイダンス**: Azure AD のリスクおよび ID Protectoin 機能を使用して、ユーザー ID に関連して検出された疑わしいアクションに対する自動応答を構成します。 Azure Sentinel にデータを取り込んで、さらに詳しく調査することもできます。 また、Azure Databricks 診断ログを使用して、ユーザー アクセス アクティビティを確認することもできます。

* [Azure AD の危険なサインインを表示する方法](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

* [Identity Protection のリスク ポリシーを構成して有効にする方法](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies)

* [Azure Sentinel をオンボードする方法](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: サポート シナリオで関連する顧客データに Microsoft がアクセスできるようにする

**ガイダンス**: サポート チケットを開いている場合、カスタマー サービスおよびサポート エンジニアから、関連する顧客データへのアクセスに同意が求められます。

**Azure Security Center の監視**: 現在は使用できません

**責任**: 現在は使用できません

## <a name="data-protection"></a>データ保護

*詳細については、「[セキュリティ コントロール: データ保護](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection)」を参照してください。*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: 機密情報のインベントリを維持する

**ガイダンス**: タグを使用すると、機密情報を処理する Azure Databricks インスタンスの追跡に役立ちます。

* [タグを作成して使用する方法](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2:機密情報を格納または処理するシステムを分離する

**ガイダンス**:開発、テスト、および運用で別々のサブスクリプションまたは管理グループ、あるいはその両方を実装します。 Azure Databricks の既定のデプロイは、独自の仮想ネットワーク内にデプロイされるフル マネージド サービスです。 ネットワークのカスタマイズが必要な場合は、ご自分の仮想ネットワークに Azure Databricks をデプロイできます。 ベスト プラクティスとして、ビジネス チームや部門ごとに異なる Azure Databricks ワークスペースを作成することをお勧めします。

* [ご自分の Virtual Network に Azure Databricks をデプロイする方法](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)

* [追加の Azure サブスクリプションを作成する方法](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [管理グループを作成する方法](https://docs.microsoft.com/azure/governance/management-groups/create)

* [仮想ネットワークを作成する方法](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3:機密情報の承認されていない転送を監視してブロックする

**ガイダンス**: Databricks の窃盗防止アーキテクチャに従ってデータ窃盗の可能性を軽減します。

* [Azure Databricks を使用したデータ窃盗からの保護](https://databricks.com/blog/2020/03/27/data-exfiltration-protection-with-azure-databricks.html)

Microsoft では、Azure Databricks の基盤となるインフラストラクチャを管理し、顧客データの損失や漏洩を防ぐための厳格な管理を実施してきました。

* [Azure での顧客データの保護について](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure Security Center の監視**: 現在は使用できません

**責任**: 現在は使用できません

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4:転送中のすべての機密情報を暗号化する

**ガイダンス**: Microsoft では、Azure portal または Azure Databricks コンソールを使用して Azure Databricks インスタンスを管理するときに、既定で TLS 1.2 をネゴシエートします。 Databricks Web アプリは TLS 1.3 をサポートしています。

**Azure Security Center の監視**: 適用なし

**責任**: Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5:アクティブ検出ツールを使用して機密データを特定する

**ガイダンス**: 現在は使用できません。現在、Azure Databricks では、データの識別、分類、損失防止機能を使用できません。 そのため、機密情報を処理している可能性のある Azure Databricks インスタンスと関連リソースにタグを付け、コンプライアンスの目的で必要な場合はサードパーティ製ソリューションを実装します。

Databricks プラットフォームはコンピューティング専用であり、すべてのデータは他の Azure データ サービスに格納されます。 Microsoft によって管理される基になるプラットフォームの場合、Microsoft は顧客のすべてのコンテンツを機密として扱い、顧客データを損失や漏洩から保護するためにあらゆる手段を尽くします。 Azure 内の顧客データが確実にセキュリティで保護されるように、Microsoft では一連の堅牢なデータ保護制御および機能を実装して管理しています。

* [Azure での顧客データの保護について](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure Security Center の監視**: 現在は使用できません

**責任**: 現在は使用できません

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6:Azure RBAC を使用してリソースへのアクセスを制御する

**ガイダンス**: Azure Databricks では、アクセス制御リスト (ACL) を使用して、データ テーブル、クラスター、プール、ジョブと、ノートブック、実験、フォルダーなどのワークスペース オブジェクトにアクセスするための権限を構成できます。 アクセス制御リストを管理できるのは、すべての管理者ユーザーと、アクセス制御リストを管理する権限を委任されたユーザーです。 Azure RBAC を使用して、Azure Databricks ワークスペースにアクセス許可を設定できます。

注:テーブル、クラスター、プール、ジョブ、ワークスペースのアクセス制御は、Azure Databricks Premium プランでのみ使用できます。

* [Azure Databricks でアクセス制御を管理する方法](https://docs.microsoft.com/azure/databricks/administration-guide/access-control/)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7:ホスト ベースのデータ損失防止を使用してアクセス制御を適用する

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

Microsoft では、Azure Databricks の基盤となるインフラストラクチャを管理し、顧客データの損失や漏洩を防ぐための厳格な管理を実施してきました。

* [Azure での顧客データの保護について](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8:機密情報を保存時に暗号化する

**ガイダンス**: Azure Databricks ワークスペースは、Azure Databricks のマネージド仮想ネットワークでホストされる管理プレーンと、カスタマー マネージド仮想ネットワークにデプロイされるデータ プレーンで構成されます。 コントロール プレーンによって、すべてのユーザーのノートブックと関連するノートブックの結果がデータベースに格納されます。 既定では、すべてのノートブックと結果は保存時に別の暗号化キーを使用して暗号化されます。

Databricks ファイル システム (DBFS) は、Azure Databricks ワークスペースにマウントされ、Azure Databricks クラスター上で使用できる分散ファイル システムです。 DBFS は、Azure Databricks ワークスペースのマネージド リソース グループのストレージ アカウントとして実装されます。 既定では、ストレージ アカウントは Microsoft マネージド キーを使用して暗号化されます。 お客様のデータは所有する Azure データ サービスに格納されます。また、暗号化することもできます。 運用データの格納に DBFS を使用することは推奨されません

注:これらの機能は、すべての Azure Databricks サブスクリプションに使用できるわけではありません。 アクセスを要求するには、Microsoft または Databricks のアカウント担当者にお問い合わせください。

* [Azure Databricks ノートブックのカスタマー マネージド キーを有効にする方法](https://docs.microsoft.com/azure/databricks/security/customer-managed-key-notebook)

* [Azure Databricks ファイル システムのカスタマー マネージド キーを有効にする方法](https://docs.microsoft.com/azure/databricks/security/customer-managed-keys-dbfs)

**Azure Security Center の監視**: 適用なし

**責任**: 共有

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9:重要な Azure リソースへの変更に関するログとアラート

**ガイダンス**: Azure アクティビティ ログで Azure Monitor を使用して、重要な Azure Databricks ワークスペースに変更が加えられたときのアラートを作成します。

* [Azure アクティビティ ログ イベントのアラートを作成する方法](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="vulnerability-management"></a>脆弱性の管理

*詳細については、「[セキュリティ コントロール: 脆弱性の管理](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management)」を参照してください。*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1:自動化された脆弱性スキャン ツールを実行する

**ガイダンス**:サードパーティの脆弱性管理ソリューションを実装します。

脆弱性管理プラットフォームのサブスクリプションをお持ちの場合は、Azure Databricks 初期化スクリプトを使用して、Azure Databricks クラスター ノードに脆弱性評価エージェントをインストールし、それぞれのポータルを使用してノードを管理できます。 サードパーティ製ソリューションの動作はそれぞれ異なることに注意してください。

* [Rapid7 エージェントを手動でインストールする方法](https://insightagent.help.rapid7.com/docs/install)

* [Qualys エージェントを手動でインストールする方法](https://www.qualys.com/docs/qualys-cloud-agent-linux-install-guide.pdf)

* [Azure Databricks 初期化スクリプト](https://docs.microsoft.com/azure/databricks/clusters/init-scripts)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2:自動化されたオペレーティング システム修正プログラム管理ソリューションを展開する

**ガイダンス**: Microsoft では Azure Databricks クラスター ノードのベース イメージを保守していますが、クラスター ノードに常にパッチが適用されていることは、お客様が確認する必要があります。 実行中の既存のクラスターにメンテナンス更新プログラムを追加するには、クラスターを再起動する必要があります。

* [Azure Databricks のランタイム メンテナンス更新プログラムの概要](https://docs.microsoft.com/azure/databricks/release-notes/runtime/maintenance-updates)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3:サードパーティ製ソフトウェアの自動パッチ管理ソリューションをデプロイする

**ガイダンス**: Microsoft では Azure Databricks クラスター ノードのベース イメージを保守していますが、インストールするサードパーティ製アプリケーションに常にパッチが適用されていることは、お客様が確認する必要があります。

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4:バックツーバックの脆弱性スキャンを比較する

**ガイダンス**: 時間の経過に伴って脆弱性スキャンを比較できるサードパーティの脆弱性管理ソリューションを実装します。 脆弱性管理サブスクリプションをお持ちの場合は、そのベンダーのポータルを使用して、連続する脆弱性スキャンを表示および比較できます。 サードパーティ製ソリューションの動作はそれぞれ異なることに注意してください。

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5:リスク評価プロセスを使用して、検出された脆弱性の修復に優先順位を付ける。

**ガイダンス**: 一般的なリスク スコアリング プログラム (Common Vulnerability Scoring System など)、またはサードパーティのスキャン ツールによって提供される既定のリスク評価を使用します。

**Azure Security Center の監視**: 該当なし

**責任**: Customer

## <a name="inventory-and-asset-management"></a>インベントリと資産の管理

*詳細については、「[セキュリティ コントロール: インベントリと資産の管理](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management)」を参照してください。*

### <a name="61-use-azure-asset-discovery"></a>6.1:Azure Asset Discovery を使用する

**ガイダンス**: Azure Resource Graph を使用して、サブスクリプション内のすべてのリソース (コンピューティング、ストレージ、ネットワーク、ポート、プロトコルなど) のクエリまたは検出を行います。 テナントに適切な (読み取り) アクセス許可が存在することを確認し、サブスクリプション内のリソースだけでなく、すべての Azure サブスクリプションを列挙します。

従来の Azure リソースは Resource Graph で検出できますが、今後は Azure Resource Manager リソースを作成して使用することを強くお勧めします。

* [Azure Resource Graph を使用してクエリを作成する方法](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

* [Azure サブスクリプションを表示する方法](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

* [Azure RBAC の概要](https://docs.microsoft.com/azure/role-based-access-control/overview)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="62-maintain-asset-metadata"></a>6.2:資産メタデータを保持する

**ガイダンス**:メタデータを提供する Azure リソースにタグを適用すると、それらのリソースが各分類に論理的に整理されます。

* [タグを作成して使用する方法](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="63-delete-unauthorized-azure-resources"></a>6.3:承認されていない Azure リソースを削除する

**ガイダンス**: Azure リソースを整理および追跡するには、必要に応じて、タグ付け、管理グループ、個別のサブスクリプションを使用します。 定期的にインベントリを調整し、承認されていないリソースがサブスクリプションから適切なタイミングで削除されるようにします。

さらに、次の組み込みのポリシー定義を使用して、顧客のサブスクリプション内に作成できるリソースの種類に制限を適用するには、Azure Policy を使用します。
- 許可されないリソースの種類
- 許可されるリソースの種類

* [追加の Azure サブスクリプションを作成する方法](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [管理グループを作成する方法](https://docs.microsoft.com/azure/governance/management-groups/create)

* [タグを作成して使用する方法](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="64-maintain-inventory-of-approved-azure-resources-and-software-titles"></a>6.4:承認された Azure リソースとソフトウェア タイトルのインベントリを保持する

**ガイダンス**: コンピューティング リソース用に承認された Azure リソースと承認されたソフトウェアを定義します。

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5:承認されていない Azure リソースを監視する

**ガイダンス**:次の組み込みのポリシー定義を使用して、顧客のサブスクリプション内に作成できるリソースの種類に制限を適用するには、Azure Policy を使用します。
- 許可されないリソースの種類
- 許可されるリソースの種類

Azure Resource Graph を使用して、サブスクリプション内のリソースのクエリまたは検出を行います。 環境に存在するすべての Azure リソースが承認されていることを確認します。

* [Azure Policy を構成して管理する方法](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Azure Graph を使用してクエリを作成する方法](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6:コンピューティング リソース内の承認されていないソフトウェア アプリケーションを監視する

**ガイダンス**: 適用できません。Azure Databricks は PaaS サービスであり、お客様は Azure Databricks クラスター内の VM に直接アクセスすることはできません。

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7:承認されていない Azure リソースとソフトウェア アプリケーションを削除する

**ガイダンス**: Azure Resource Graph を使用して、Azure Databricks インスタンスを含めて、サブスクリプション内のすべてのリソース (コンピューティング、ストレージ、ネットワーク、ポート、プロトコルなど) のクエリまたは検出を行います。 検出された承認されていない Azure リソースを削除します。 Azure Databricks クラスター ノードの場合は、承認されていないソフトウェアの削除またはアラート通知を行うためのサードパーティ ソリューションを実装します。

* [Azure Graph を使用してクエリを作成する方法](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="68-use-only-approved-applications"></a>6.8:承認されたアプリケーションのみを使用する

**ガイダンス**: 適用できません。Azure Databricks は PaaS サービスであり、お客様は Azure Databricks クラスター内の VM に直接アクセスすることはできません。

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="69-use-only-approved-azure-services"></a>6.9:承認された Azure サービスのみを使用する

**ガイダンス**: 次の組み込みのポリシー定義を使用して、顧客のサブスクリプション内に作成できるリソースの種類に制限を適用するには、Azure Policy を使用します。
- 許可されないリソースの種類
- 許可されるリソースの種類

* [Azure Policy を構成して管理する方法](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Azure Policy を使用して特定のリソースの種類を拒否する方法](https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="610-implement-approved-application-list"></a>6.10:承認されたアプリケーションの一覧を実装する

**ガイダンス**: 適用できません。Azure Databricks は PaaS サービスであり、お客様は Azure Databricks クラスター内の VM に直接アクセスすることはできません。

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="611-divlimit-users-ability-to-interact-with-azure-resource-manager-via-scriptsdiv"></a>6.11: <div>ユーザーがスクリプトを使用して Azure Resource Manager と対話する機能を制限する</div>

**ガイダンス**: "Microsoft Azure Management" アプリに対して [アクセスのブロック] を構成することによって、Azure Resource Manager を操作するユーザーの機能を制限するには、Azure 条件付きアクセスを使用します。

* [Azure Resource Manager へのアクセスをブロックするように条件付きアクセスを構成する方法](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12:コンピューティング リソース内でスクリプトを実行するユーザーの機能を制限する

**ガイダンス**: 適用できません。クラスターのユーザー (管理者以外) は、ジョブを実行するために個々のノードへのアクセスを必要としないため、これは Azure Databricks には適用できません。 クラスター管理者には、既定ですべてのクラスター ノードへのルート アクセス権があります。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13:リスクの高いアプリケーションを物理的または論理的に分離する

**ガイダンス**: 適用なし。ベンチマークでは、Web アプリケーションをホストしている Azure アプリ サービスまたはコンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

## <a name="secure-configuration"></a>セキュリティで保護された構成

*詳細については、「[セキュリティ コントロール: セキュリティで保護された構成](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration)」を参照してください。*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1:すべての Azure リソースに対してセキュリティで保護された構成を確立する

**ガイダンス**: Azure Policy を使用して、Azure Databricks インスタンスの標準のセキュリティ構成を定義および実装します。 Azure Databricks インスタンスの構成を監査または適用するためのカスタム ポリシーを作成するには、"Microsoft.Databricks" 名前空間で Azure Policy エイリアスを使用します。 ポリシーを適用しても、Databricks マネージド リソース グループでは機能しないことに注意してください。

* [使用可能な Azure Policy エイリアスを表示する方法](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [Azure Policy を構成して管理する方法](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="72-establish-secure-operating-system-configurations"></a>7.2:セキュリティで保護されたオペレーティング システムの構成を確立する

**ガイダンス**: 適用できません。Azure Databricks は PaaS サービスであり、お客様は Azure Databricks クラスター内の VM に直接アクセスすることはできません。

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3:セキュリティで保護された Azure リソースの構成を維持する

**ガイダンス**: Azure Policy を使用して、Azure Databricks インスタンスの標準のセキュリティ構成を定義および実装します。 Azure Databricks インスタンスの構成を監査または適用するためのカスタム ポリシーを作成するには、"Microsoft.Databricks" 名前空間で Azure Policy エイリアスを使用します。 Azure リソース全体にセキュリティで保護された設定を適用するには、Azure ポリシー [拒否] と [存在する場合はデプロイする] を使用します。

* [Azure Policy を構成して管理する方法](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Azure Policy の効果について](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4:セキュリティで保護されたオペレーティング システムの構成を維持する

**ガイダンス**: Azure Databricks オペレーティング システム イメージは、Microsoft が管理および保守します。 OS レベルの状態構成を実装する責任はユーザーが負います。

**Azure Security Center の監視**: 適用なし

**責任**: 共有

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5:Azure リソースの構成を安全に格納する

**ガイダンス**:カスタム Azure ポリシー定義を使用する場合は、Azure DevOps または Azure Repos を使ってコードを安全に格納して管理します。

* [Azure DevOps でコードを格納する方法](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Azure Repos のドキュメント](https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="76-securely-store-custom-operating-system-images"></a>7.6:カスタム オペレーティング システム イメージを安全に格納する

**ガイダンス**: Azure Databricks クラスター ノードにカスタム イメージを使用する場合は、カスタム ベース イメージを Azure Container Registry (ACR) などの Docker レジストリにプッシュします。

* [Databricks Container Services を使用してコンテナーをカスタマイズする方法](https://docs.microsoft.com/azure/databricks/clusters/custom-containers)

* [Azure Container Registry の概要](https://docs.microsoft.com/azure/container-registry/container-registry-intro)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="77-deploy-system-configuration-management-tools"></a>7.7:システム構成管理ツールをデプロイする

**ガイダンス**: Azure Policy を使用して、Azure Databricks インスタンスの標準のセキュリティ構成を定義および実装します。 Azure Databricks インスタンスの構成を監査または適用するためのカスタム ポリシーを作成するには、"Microsoft.Databricks" 名前空間で Azure Policy エイリアスを使用します。

* [Azure Policy を構成して管理する方法](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [使用可能な Azure Policy エイリアスを表示する方法](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [Azure Policy を構成して管理する方法](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8:オペレーティング システム用のシステム構成管理ツールをデプロイする

**ガイダンス**: 適用できません。Azure Databricks は PaaS サービスであり、お客様は Azure Databricks クラスター内の VM に直接アクセスすることはできません。

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9:Azure サービスの自動構成監視を実装する

**ガイダンス**: Azure Policy を使用して、Azure Databricks インスタンスの標準のセキュリティ構成を定義および実装します。 Azure Databricks インスタンスの構成を監査または適用するためのカスタム ポリシーを作成するには、"Microsoft.Databricks" 名前空間で Azure Policy エイリアスを使用します。

* [使用可能な Azure Policy エイリアスを表示する方法](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [Azure Policy を構成して管理する方法](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10:オペレーティング システムの自動構成監視を実装する

**ガイダンス**: Azure Databricks クラスター ノードのオペレーティング システムの状態を監視するサードパーティ製ソリューションを実装します。

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="711-manage-azure-secrets-securely"></a>7.11:Azure シークレットを安全に管理する

**ガイダンス**: Azure Key Vault と Azure Databricks シークレット スコープを使用して、シークレットを安全に管理および使用します。

* [Azure Key Vault と Azure Databricks を使用する方法](https://docs.microsoft.com/azure/azure-databricks/store-secrets-azure-key-vault)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="712-manage-identities-securely-and-automatically"></a>7.12:ID を安全かつ自動的に管理する

**ガイダンス**: 現在は使用できません。Azure Databricks では、マネージド ID は現在使用できません

* [Azure リソースのマネージド ID をサポートするサービス](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities)

**Azure Security Center の監視**: 現在は使用できません

**責任**: 現在は使用できません

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13:意図しない資格情報の公開を排除する

**ガイダンス**: コード内で資格情報を特定する資格情報スキャナーを実装します。 また、資格情報スキャナーを使うと、検出された資格情報を、Azure Key Vault などのより安全な場所に移動しやすくなります。

* [資格情報スキャナーを設定する方法](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="malware-defense"></a>マルウェアからの防御

*詳細については、「[セキュリティ コントロール: マルウェアからの防御](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense)」を参照してください。*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1:一元管理されるマルウェア対策ソフトウェアを使用する

**ガイダンス**: 脆弱性管理プラットフォームのサブスクリプションをお持ちの場合は、Azure Databricks 初期化スクリプトを使用して、Azure Databricks クラスター ノードに脆弱性評価エージェントをインストールし、それぞれのポータルを使用してノードを管理できます。 サードパーティ製ソリューションの動作はそれぞれ異なることに注意してください。

* [Rapid7 エージェントを手動でインストールする方法](https://insightagent.help.rapid7.com/docs/install)

* [Qualys エージェントを手動でインストールする方法](https://www.qualys.com/docs/qualys-cloud-agent-linux-install-guide.pdf)

* [Azure Databricks 初期化スクリプト](https://docs.microsoft.com/azure/databricks/clusters/init-scripts)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2:非コンピューティング Azure リソースにアップロードするファイルを事前にスキャンする

**ガイダンス**: Microsoft Antimalware は、Azure のサービス (例: Azure App Service) をサポートする、基になっているホストに対して有効になっていますが、コンテンツに対しては実行されません。

Azure Databricks クラスター ノードまたは関連リソースにアップロードされているファイルを事前にスキャンします。

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>手順 8.3:マルウェア対策ソフトウェアと署名が確実に更新されるようにする

**ガイダンス**: 脆弱性管理プラットフォームのサブスクリプションをお持ちの場合は、Azure Databricks 初期化スクリプトを使用して、Azure Databricks クラスター ノードに脆弱性評価エージェントをインストールし、それぞれのポータルを使用してノードを管理できます。 サードパーティ製ソリューションの動作はそれぞれ異なることに注意してください。

* [Rapid7 エージェントを手動でインストールする方法](https://insightagent.help.rapid7.com/docs/install)

* [Qualys エージェントを手動でインストールする方法](https://www.qualys.com/docs/qualys-cloud-agent-linux-install-guide.pdf)

* [Azure Databricks 初期化スクリプト](https://docs.microsoft.com/azure/databricks/clusters/init-scripts)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="data-recovery"></a>データの復旧

*詳細については、「[セキュリティ コントロール: データの復旧](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery)」を参照してください。*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1:定期的に自動バックアップを行う

**ガイダンス**: Azure Databricks データ ソースについて、ユース ケースに適したレベルのデータ冗長性を構成していることを確認します。 たとえば、Azure Databricks データ ストアに Azure Storage アカウントを使用している場合は、適切な冗長性オプション (LRS、ZRS、GRS、RA-GRS) を選択します。

* [Azure Databricks のデータ ソース](https://docs.microsoft.com/azure/azure-databricks/databricks-connect-to-data-sources#data-sources-for-azure-databricks)

* [Azure Databricks クラスターに対するリージョンのディザスター リカバリー](https://docs.microsoft.com/azure/azure-databricks/howto-regional-disaster-recovery)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: システムの完全バックアップを実行し、カスタマー マネージド キーをバックアップする

**ガイダンス**: Azure Key Vault 内の Azure Databricks 実装に関連するユーザー マネージド キーをバックアップします。 REST API と CLI を使用して、Databricks 構成の毎日のバックアップを作成することもできます。 REST API と CLI を使用して、Databricks 構成の毎日のバックアップを作成することもできます。

* [Azure 上でキー コンテナーのキーをバックアップする方法](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3:カスタマー マネージド キーを含むすべてのバックアップを検証する

**ガイダンス**: Azure Databricks の実装に関連する、バックアップされたユーザー マネージド キーの復元をテストします。

* [Azure でキー コンテナーのキーを復元する方法](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: バックアップとカスタマー マネージド キーの保護を確保する

**ガイダンス**: Key Vault で論理的な削除を有効にして、偶発的または悪意のある削除からキーを保護します。

* [Key Vault で論理的な削除を有効にする方法](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

**Azure Security Center の監視**: はい

**責任**: Customer

## <a name="incident-response"></a>インシデント対応

*詳細については、「[セキュリティ コントロール: インシデント対応](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response)」を参照してください。*

### <a name="101-create-an-incident-response-guide"></a>10.1:インシデント対応ガイドを作成する

**ガイダンス**: 組織のインシデント対応ガイドを作成します。 要員のすべてのロールを定義するインシデント対応計画が記述されていることと、検出からインシデント後のレビューまでのインシデント対応/管理のフェーズがあることを確認します。

* [Azure Security Center 内でワークフロー自動化を構成する方法](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)

* [独自のセキュリティ インシデント対応プロセスを構築するためのガイダンス](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Microsoft Security Response Center のインシデントの構造](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [お客様は、独自のインシデント対応計画の作成に役立つ NIST の「コンピューター セキュリティ インシデント対応ガイド」を利用することもできます](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2:インシデントのスコアリングと優先順位付けの手順を作成する

**ガイダンス**: Security Center によって各アラートに重大度が割り当てられるため、最初に調査する必要があるアラートの優先順位付けに役立ちます。 重要度は、アラートの発行に使用された Security Center の信頼度と、アラートの原因となったアクティビティの背後に悪意のある意図があったかどうかの信頼レベルに基づいて決まります。

また、サブスクリプション ( 稼働、非稼働など) を明確にマークし、Azure リソースを明確に識別および分類するための命名システムを作成します。

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="103-test-security-response-procedures"></a>10.3:セキュリティ対応手順のテスト

**ガイダンス**:定期的にシステムのインシデント対応機能をテストする演習を実施します。 弱点やギャップを特定し、必要に応じて計画を見直します。

* [NIST の出版物「IT 計画と機能に関するテスト、トレーニング、演習プログラムのガイド」を参照してください。](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4:セキュリティ インシデントの連絡先の詳細を指定し、セキュリティ インシデントのアラート通知を構成します

**ガイダンス**: セキュリティ インシデントの連絡先情報は、Microsoft Security Response Center (MSRC) で、不正なユーザーまたは権限のないユーザーによるお客様のデータへのアクセスが検出された場合に、Microsoft からの連絡先として使用されます。 事後にインシデントをレビューして、問題が解決されていることを確認します。

* [Azure Security Center のセキュリティ連絡先を設定する方法](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5:インシデント対応システムにセキュリティ アラートを組み込む

**ガイダンス**:連続エクスポート機能を使用して Azure Security Center のアラートと推奨事項をエクスポートします。 連続エクスポートを使用すると、アラートと推奨事項を手動で、または継続した連続的な方法でエクスポートできます。 Azure Security Center データ コネクタを使用して、アラートの Sentinel のストリーミングを実行できます。

* [連続エクスポートを構成する方法](https://docs.microsoft.com/azure/security-center/continuous-export)

* [Azure Sentinel にアラートをストリーミングする方法](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="106-automate-the-response-to-security-alerts"></a>10.6:セキュリティ アラートへの対応を自動化する

**ガイダンス**:セキュリティ アラートやセキュリティに関する推奨事項に対して "Logic Apps" 経由で応答を自動的にトリガーするには、Azure Security Center のワークフローの自動化機能を使用します。

* [ワークフローの自動化と Logic Apps を構成する方法](https://docs.microsoft.com/azure/security-center/workflow-automation)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="penetration-tests-and-red-team-exercises"></a>侵入テストとレッド チーム演習

*詳細については、「[セキュリティ コントロール: 侵入テストとレッド チーム演習](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises)」を参照してください。*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1:Azure リソースの通常の侵入テストを実施し、セキュリティに関する重大な調査結果がすべて、60 日以内に確実に修復されるようにします。

**ガイダンス**: * [侵入テストが Microsoft のポリシーに違反していないことを確保するために、Microsoft の実施ルールに従ってください。](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

* [Microsoft が管理しているクラウド インフラストラクチャ、サービス、アプリケーションに対する Red Teaming およびライブ サイト侵入テストに関する Microsoft の戦略と実施の詳細については、こちらを参照してください。](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center の監視**: 適用なし

**責任**: 共有

## <a name="next-steps"></a>次のステップ

- [Azure セキュリティ ベンチマーク](https://docs.microsoft.com/azure/security/benchmarks/overview)に関するページを参照する
- [Azure セキュリティ ベースライン](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)の詳細について学習する
