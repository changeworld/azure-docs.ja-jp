---
title: Azure Cognitive Search の Azure セキュリティ ベースライン
description: Azure Cognitive Search のセキュリティ ベースラインでは、Azure セキュリティ ベンチマークで指定されているセキュリティに関する推奨事項を実装するための手順のガイダンスとリソースが提供されます。
author: msmbaldwin
ms.service: search
ms.topic: conceptual
ms.date: 09/25/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 4f54eab9616aa014e6f3a59b5c79e268450ecfce
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/02/2021
ms.locfileid: "101668352"
---
# <a name="azure-security-baseline-for-azure-cognitive-search"></a>Azure Cognitive Search の Azure セキュリティ ベースライン

このセキュリティ ベースラインにより、[Azure セキュリティ ベンチマーク バージョン 1.0](../security/benchmarks/overview.md) のガイダンスが Azure Cognitive Search に適用されます。 Azure セキュリティ ベンチマークには、Azure 上のクラウド ソリューションをセキュリティで保護する方法に関する推奨事項がまとめてあります。 内容は、**セキュリティ制御** によってグループ化されています。これは、Azure セキュリティ ベンチマークと、Azure Cognitive Search に適用できる関連ガイダンスによって定義されています。 Azure Cognitive Search にもお客様にも当てはまらない **制御** は除外されました。

Azure Cognitive Search を完全に Azure セキュリティ ベンチマークにマップする方法については、[完全な Azure Cognitive Search セキュリティ ベースライン マッピング ファイル](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)を参照してください。

## <a name="network-security"></a>ネットワークのセキュリティ

*詳細については、[Azure セキュリティ ベンチマークの「ネットワークのセキュリティ](../security/benchmarks/security-control-network-security.md)」を参照してください。*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1:仮想ネットワーク内の Azure リソースを保護する

**ガイダンス**:すべての Microsoft Azure Virtual Network サブネット デプロイに、"最小特権" アクセス スキームを実装するための規則が適用されたネットワーク セキュリティ グループがあることを確認します。 アプリケーションの信頼されたポートと IP アドレスの範囲へのアクセスのみを許可します。 可能な場合は、Azure プライベート エンドポイントを使用して Azure Cognitive Search をデプロイすることにより、使用している仮想ネットワークからサービスへのプライベート アクセスを有効にします。

Cognitive Search では、ネットワーク アクセス制御リストを管理するための追加のネットワーク セキュリティ機能もサポートされています。 信頼されたソースとの通信のみを許可するように検索サービスを構成するために、ファイアウォール機能を使用して特定のパブリック IP アドレスの範囲からのアクセスを制限します。

- [Azure Cognitive Search のプライベート エンドポイントを構成する方法](./service-create-private-endpoint.md)

- [Azure Cognitive Search ファイアウォールを構成する方法](./service-configure-firewall.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2:仮想ネットワーク、サブネット、NIC の構成とトラフィックを監視してログに記録する

**ガイダンス**:Cognitive Search を仮想ネットワークに直接デプロイすることはできません。 ただし、クライアント アプリケーションまたはデータ ソースが仮想ネットワーク内にある場合は、それらのネットワーク内コンポーネントのトラフィック (クラウド内の検索サービスに送信された要求を含む) を監視し、ログに記録することができます。 標準的な推奨事項には、ネットワーク セキュリティ グループ フロー ログを有効にすることと、Azure Storage または Log Analytics ワークスペースのいずれかにログを送信することがあります。 必要に応じて Traffic Analytics を使用して、トラフィック パターンに関する分析情報を得ることもできます。

- [ネットワーク セキュリティ グループのフローのログを有効にする方法](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Traffic Analytics を有効にして使用する方法](../network-watcher/traffic-analytics.md)

- [Azure Security Center によって提供されるネットワークのセキュリティについて](../security-center/security-center-network-recommendations.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="13-protect-critical-web-applications"></a>1.3:重要な Web アプリケーションを保護する

**ガイダンス**:Cognitive Search には適用されません。 この推奨事項は、Azure App Service またはコンピューティング リソース上で実行されている Web アプリケーションを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4:既知の悪意のある IP アドレスとの通信を拒否する

**ガイダンス**:Cognitive Search には、分散型サービス拒否攻撃に対処するための特定の機能は用意されていませんが、Cognitive Search サービスに関連付けられた仮想ネットワークで DDoS Protection Standard を有効にすることで一般的な保護を行うことができます。

- [DDoS 保護を構成する方法](../ddos-protection/manage-ddos-protection.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="15-record-network-packets"></a>1.5:ネットワーク パケットを記録する

**ガイダンス**:Cognitive Search サービスに接続する Azure Virtual Machines (VM) を保護するネットワーク セキュリティ グループについてネットワーク セキュリティ グループ フロー ログを有効にします。 ログは、トラフィック監査用の Azure ストレージ アカウントに送信します。 

異常なアクティビティを調査するために必要な場合は、Network Watcher パケット キャプチャを有効にします。

- [NSG フロー ログを有効にする方法](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Network Watcher を有効にする方法](../network-watcher/network-watcher-create.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6:ネットワーク ベースの侵入検出または侵入防止システム (IDS または IPS) をデプロイする

**ガイダンス**:Cognitive Search でネットワーク侵入検出はサポートされていませんが、侵入の軽減策として、ファイアウォール規則を構成して Cognitive Search サービスが受け入れる IP アドレスを指定することができます。 検索トラフィックをパブリック インターネットから離すようにプライベート エンドポイントを構成します。

- [データの暗号化用にカスタマー マネージド キーを構成する方法](./search-security-manage-encryption-keys.md)

- [インデックスとシノニム マップからカスタマー マネージド キーの情報を取得する方法](./search-security-get-encryption-keys.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="17-manage-traffic-to-web-applications"></a>1.7:Web アプリケーションへのトラフィックを管理する

**ガイダンス**:Cognitive Search には適用されません。 この推奨事項は、Azure App Service またはコンピューティング リソース上で実行されている Web アプリケーションを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8:ネットワーク セキュリティ規則の複雑さと管理オーバーヘッドを最小限に抑える

**ガイダンス**:Cognitive Search でインデクサーとスキルセットを利用している場合は、外部リソースに接続するアクセス許可を持つ IP アドレスの範囲を表現するためにサービス タグを使用します。 

規則の該当するソースまたはターゲット フィールドにサービス タグ名 (たとえば AzureCognitiveSearch) を指定すれば、リソースへのトラフィックを許可または拒否できます。 

- [仮想ネットワーク サービス タグ](../virtual-network/service-tags-overview.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9:ネットワーク デバイスの標準的なセキュリティ構成を維持する

**ガイダンス**:Cognitive Search はネットワーク リソースを持つこともそれに依存することもありません。これは意図的なものです。 検索アプリケーションに関連するクライアント アプリとデータ ソースが仮想ネットワーク上に存在する場合がありますが、検索サービス自体はネットワークにデプロイされません。 

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="110-document-traffic-configuration-rules"></a>1.10:トラフィック構成規則を文書化する

**ガイダンス**:Azure プライベート エンドポイントを使用して Cognitive Search を構成すると、検索サービスを仮想ネットワークに統合することができます。  ネットワーク セキュリティおよびトラフィック フローに関連したネットワーク セキュリティ グループやその他のリソースに対して、リソース タグを使用してください。 個々のネットワーク セキュリティ グループの規則については、"説明" フィールドを使用して、ネットワークとの間で送受信されるトラフィックを許可する規則を文書化します。 

すべてのリソースが確実にタグ付きで作成され、タグ付けされていない既存のリソースについては通知を受けられるようにするには、タグ付けに関連したいずれかの組み込みの Azure Policy 定義 ("タグとその値が必要" 効果など) を使用します。 

Azure PowerShell または Azure CLI を使用すると、タグに基づいたリソースの検索やアクション実行ができます。 

- [Cognitive Search のプライベート エンドポイントを作成する方法](./service-create-private-endpoint.md)

- [タグを作成して使用する方法](../azure-resource-manager/management/tag-resources.md)

- [Azure 仮想ネットワークの作成方法](../virtual-network/quick-create-portal.md)

- [ネットワーク セキュリティ グループの規則を使用してネットワーク トラフィックをフィルター処理する方法](../virtual-network/tutorial-filter-network-traffic.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11:自動化ツールを使用してネットワーク リソース構成を監視し、変更を検出する

**ガイダンス**:Cognitive Search はネットワーク コンポーネントを持つことも、それに依存することもないため、これらのリソースの構成を監視することはできません。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

## <a name="logging-and-monitoring"></a>ログ記録と監視

*詳しくは、「[Azure Security ベンチマーク:ログ記録と監視](../security/benchmarks/security-control-logging-monitoring.md)」を参照してください。*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1:承認された時刻同期ソースを使用する

**ガイダンス**:Cognitive Search では、独自の時刻同期ソースの構成はサポートされていません。 検索サービスは Microsoft の時刻同期ソースに依存しており、構成のために顧客に公開されてはいません。

**Azure Security Center の監視**: 適用なし

**責任**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2:セキュリティ ログの一元管理を構成する

**ガイダンス**:Cognitive Search に関連するログを Azure Monitor を介して取り込み、エンドポイント デバイス、ネットワーク リソース、その他のセキュリティ システムによって生成されたセキュリティ データを集計します。 Azure Monitor で Log Analytics ワークスペースを使用してクエリを発行し、分析を実行して、長期のアーカイブ ストレージには Azure Storage アカウントを使用します。
または、Azure Sentinel かサード パーティの SIEM に対してこのデータを有効にしてオンボードすることもできます。

- [Azure Monitor とサードパーティの SIEM 統合を開始する方法](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

- [Azure Monitor でプラットフォーム ログとメトリックを収集する方法](../azure-monitor/essentials/diagnostic-settings.md)

- [Azure Sentinel をオンボードする方法](../sentinel/quickstart-onboard.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3:Azure リソースの監査ログ記録を有効にする

**ガイダンス**:診断および操作ログでは Cognitive Search の詳細な操作に関する分析情報が提供され、サービスと、サービスにアクセスするワークロードの監視に役立ちます。  診断データをキャプチャするには、ログ情報を格納する場所を指定してログ記録を有効にします。

- [Azure Cognitive Search のログ データを収集して分析する方法](./search-monitor-logs.md)

- [Azure Monitor でプラットフォーム ログとメトリックを収集する方法](../azure-monitor/essentials/diagnostic-settings.md) 

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4:オペレーティング システムからセキュリティ ログを収集する

**ガイダンス**:Cognitive Search には適用されません。 この推奨事項は、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="25-configure-security-log-storage-retention"></a>2.5:セキュリティ ログのストレージ保持を構成する

**ガイダンス**:診断メトリックにフィードされる履歴データは、既定では Cognitive Search によって 30 日間保持されます。 保持期間を延長する場合は、ログに記録されたイベントとメトリックを保持するためのストレージ オプションを指定する設定を必ず有効にしてください。

Azure Monitor 内で、組織のコンプライアンス規則に従って Log Analytics ワークスペースの保持期間を設定します。 長期およびアーカイブ ストレージには Azure Storage アカウントを使用します。 

- [Log Analytics でデータ保持期間を変更する](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

- [Azure Storage アカウント ログの保持ポリシーを構成する方法](../storage/common/manage-storage-analytics-logs.md#configure-logging)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="26-monitor-and-review-logs"></a>2.6:ログを監視して確認する

**ガイダンス**:異常な動作について Cognitive Search サービスからのログを分析し、監視します。 ログを確認し、ログ データに対してクエリを実行するには、Azure Monitor の Log Analytics を使用します。 または、Azure Sentinel またはサード パーティの SIEM に対してデータを有効にしてオンボードすることもできます。

- [Cognitive Search のログ データを収集して分析する方法](./search-monitor-logs.md)

- [Power BI で検索ログのデータを視覚化する方法](./search-monitor-logs-powerbi.md)

- [Azure Sentinel をオンボードする方法](../sentinel/quickstart-onboard.md)

- [Log Analytics の詳細情報](../azure-monitor/logs/log-analytics-tutorial.md)

- [Azure Monitor でカスタム クエリを実行する方法](../azure-monitor/logs/get-started-queries.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7:異常なアクティビティについてのアラートを有効にする

**ガイダンス**:Log Analytics ワークスペースと共に Security Center を使用し、セキュリティ ログやイベントで検出される異常なアクティビティに対する監視とアラートの送信を行います。 または、Azure Sentinel に対してデータを有効にしてオンボードすることもできます。

- [Azure Sentinel をオンボードする方法](../sentinel/quickstart-onboard.md)

- [Azure Security Center でアラートを管理する方法](../security-center/security-center-managing-and-responding-alerts.md)

- [Log Analytics のログ データに関するアラートを送信する方法](../azure-monitor/alerts/tutorial-response.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="28-centralize-anti-malware-logging"></a>2.8:マルウェア対策のログ記録を一元管理する

**ガイダンス**:Cognitive Search には適用されません。 基盤となるプラットフォームのマルウェア対策ソリューションは Microsoft によって管理されます。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="29-enable-dns-query-logging"></a>2.9:DNS クエリのログ記録を有効にする

**ガイダンス**:Cognitive Search には適用されません。 これによって DNS ログが生成されたり、使用されたりすることはありません。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="210-enable-command-line-audit-logging"></a>2.10:コマンドライン監査ログ記録を有効にする

**ガイダンス**:Cognitive Search には適用されません。 Cognitive Search では、コマンドラインによる監査は使用できません。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

## <a name="identity-and-access-control"></a>ID とアクセスの制御

*詳細については、[Azure セキュリティ ベンチマークの「ID およびアクセス制御](../security/benchmarks/security-control-identity-access-control.md)」を参照してください。*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: 管理アカウントのインベントリを維持する

**ガイダンス**: Azure のロールベースのアクセス制御 (Azure RBAC) を使用すると、ロールの割り当てを通じて Azure リソースへのアクセスを管理できます。 これらのロールを、ユーザー、グループ サービス プリンシパル、およびマネージド ID に割り当てることができます。 特定のリソースに対して定義済みの組み込みロールがあります。これらのロールは、Azure CLI、Azure PowerShell、Azure portal などのツールを使用してインベントリまたは照会できます。

Cognitive Search のロールは、サービス レベル管理タスクをサポートするアクセス許可に関連付けられています。  これらのロールによって、サービス エンドポイントへのアクセス権が付与されることはありません。 エンドポイントに対する操作 (インデックス管理、インデックスの作成、検索データに対するクエリなど) にアクセスして、API キーを使用して要求を認証します。

- [Azure Cognitive Search への管理アクセス用のロールを設定する](./search-security-rbac.md)

- [Azure Cognitive Search サービスの管理者 API キーを作成する](./search-security-api-keys.md)

- [PowerShell を使用して Azure AD でディレクトリ ロールを取得する方法](/powershell/module/azuread/get-azureaddirectoryrole)
- [PowerShell を使用して Azure AD でディレクトリ ロールのメンバーを取得する方法](/powershell/module/azuread/get-azureaddirectoryrolemember)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="32-change-default-passwords-where-applicable"></a>3.2: 既定のパスワードを変更する (該当する場合)

**ガイダンス**:Cognitive Search には適用されません。 これに、既定のパスワードという概念はありません。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: 専用管理者アカウントを使用する

**ガイダンス**:Cognitive Search には、インデックスと操作の管理に使用できるローカル レベルまたは Azure Active Directory (Azure AD) の管理者アカウントという概念はありません。 

管理操作には、明示的に割り当てる必要がある Azure AD 組み込みロールを使用します。 Azure AD PowerShell モジュールを呼び出してアドホック クエリを実行し、管理グループのメンバーであるアカウントを検出します。

- [Cognitive Search で管理アクセス用のロールを使用する方法](./search-security-rbac.md)

- [PowerShell を使用して Azure AD でディレクトリ ロールを取得する方法](/powershell/module/azuread/get-azureaddirectoryrole)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: シングル サインオン (SSO) と Azure Active Directory を統合する

**ガイダンス**:Azure Resource Manager を通してサポートされる管理操作については、Azure Active Directory (Azure AD) で SSO 認証を使用して検索サービス情報にアクセスします。 

組織の既存の ID でのサービスへの SSO を有効にするプロセスを確立することで、ID と資格情報の数を減らします。

- [Azure AD を使用した SSO の概要](../active-directory/manage-apps/what-is-single-sign-on.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: すべての Azure Active Directory ベースのアクセスに多要素認証を使用する

**ガイダンス**:Azure Active Directory (Azure AD) 多要素認証 (MFA) 機能を有効にし、Security Center の ID とアクセスに関する推奨事項に従います。

- [Azure で MFA を有効にする方法](../active-directory/authentication/howto-mfa-getstarted.md) 

- [Azure Security Center で ID とアクセスを監視する方法](../security-center/security-center-identity-access.md) 

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: すべての管理タスクに専用マシン (特権アクセス ワークステーション) を使用する

**ガイダンス**:多要素認証 (MFA) が構成された特権アクセス ワークステーション (PAW) を使用してログインし、Azure リソースにアクセスします。

- [セキュリティで保護された Azure マネージド ワークステーションを理解する](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)
 

- [Azure AD MFA を有効にする方法](../active-directory/authentication/howto-mfa-getstarted.md)
 

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: 管理者アカウントからの疑わしいアクティビティに関するログとアラート

**ガイダンス**:Azure Active Directory (Azure AD) のセキュリティ レポートと監視を使用して、環境内で疑わしいアクティビティや安全でないアクティビティが発生したときに検出します。 Security Center を使用して ID とアクセスのアクティビティを監視します。

- [危険なアクティビティのフラグが設定された Azure AD ユーザーを識別する方法](../active-directory/identity-protection/overview-identity-protection.md)

- [Azure Security Center でユーザーの ID およびアクセス アクティビティを監視する方法](../security-center/security-center-identity-access.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3.8:承認された場所からのみ Azure リソースを管理する

**ガイダンス**:Cognitive Search には適用されません。 承認された場所をアクセスの条件として使用することはサポートされていません。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="39-use-azure-active-directory"></a>3.9: Azure Active Directory を使用する

**ガイダンス**:Azure Cognitive Search におけるサービス レベル管理タスク用の中央認証および認可システムとして Azure Active Directory (Azure AD) を使用します。 Azure AD ID によって検索サービス エンドポイントへのアクセス権が付与されることはありません。  インデックスの管理、インデックスの作成、検索データのクエリなどの操作へのアクセスは、API キーによって利用可能になります。

- [Azure AD インスタンスを作成して構成する方法](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Azure Cognitive Search サービスの管理者 API キーを作成する](./search-security-api-keys.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: ユーザー アクセスを定期的に確認して調整する

**ガイダンス**: Azure Active Directory (Azure AD) では、古いアカウントの検出に役立つログが提供されます。 Azure AD の ID およびアクセス レビューを使用して、グループ メンバーシップ、エンタープライズ アプリケーションへのアクセス、ロールの割り当てを効率的に管理します。 ユーザー アクセスを定期的にレビューし、適切なユーザーのみが継続的なアクセス権を持っていることを確認できます。 

インデックス管理、インデックスの作成、クエリなど、検索サービス エンドポイントでのアクティビティについては Cognitive Search の診断ログを確認します。

- [Azure AD のレポートの概要](../active-directory/reports-monitoring/index.yml)

- [Azure AD の ID およびアクセス レビューの使用方法](../active-directory/governance/access-reviews-overview.md)

- [Azure Cognitive Search の操作とアクティビティを監視する](./search-monitor-usage.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: 非アクティブ化された資格情報へのアクセスの試行を監視する

**ガイダンス**:Azure Active Directory (Azure AD) のサインイン アクティビティ、監査、リスク イベントのログ ソースにアクセスすることにより、任意の SIEM または監視ツールとの統合が可能になります。

このプロセスを効率化するには、Azure AD ユーザー アカウントの診断設定を作成し、監査ログとサインイン ログを Log Analytics ワークスペースに送信します。 Log Analytics ワークスペース内で目的のアラートを構成します。

- [Azure アクティビティ ログを Azure Monitor と統合する方法](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) 

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: アカウント ログイン動作の偏差に関するアラートを生成する

**ガイダンス**: Azure Active Directory (Azure AD) Identity Protection 機能を使用して、ユーザー ID に関連して検出された疑わしいアクションに対する自動応答を構成します。 必要に応じて、さらに詳しく調査するために Azure Sentinel にデータを取り込みます。

- [Azure AD の危険なサインインを表示する方法](../active-directory/identity-protection/overview-identity-protection.md) 

- [Identity Protection のリスク ポリシーを構成して有効にする方法](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md) 

- [Azure Sentinel をオンボードする方法](../sentinel/quickstart-onboard.md) 

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: サポート シナリオで関連する顧客データに Microsoft がアクセスできるようにする

**ガイダンス**:Cognitive Search には適用されません。 カスタマー ロックボックスでは Cognitive Search がサポートされていません。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

## <a name="data-protection"></a>データ保護

*詳細については、[Azure セキュリティ ベンチマークの「データ保護](../security/benchmarks/security-control-data-protection.md)」を参照してください。*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: 機密情報のインベントリを維持する

**ガイダンス**: 機密情報を格納または処理する Azure リソースを追跡しやすくするには、タグを使用します。

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2:機密情報を格納または処理するシステムを分離する

**ガイダンス**:開発、テスト、および運用で別々のサブスクリプションまたは管理グループ、あるいはその両方を実装します。 リソースは、仮想ネットワークまたはサブネットで分離し、適切にタグ付けし、さらにネットワーク セキュリティ グループまたは Azure Firewall 内でセキュリティ保護する必要があります。 機密データを格納または処理するリソースは分離されている必要があります。 プライベート リンクを使用して Cognitive Search に対するプライベート エンドポイントを構成します。

- [追加の Azure サブスクリプションを作成する方法](../cost-management-billing/manage/create-subscription.md) 

- [タグを作成して使用する方法](../azure-resource-manager/management/tag-resources.md)

- [Cognitive Search のプライベート エンドポイントを作成する方法](./service-create-private-endpoint.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3:機密情報の承認されていない転送を監視してブロックする

**ガイダンス**: Azure Marketplace からのサードパーティ ソリューションをネットワーク境界で使用して、機密情報の承認されていない転送を監視して、情報セキュリティ担当者にアラートを送信すると同時に、そのような転送をブロックします。

Microsoft では、基になるプラットフォームを管理し、顧客のすべてのコンテンツを機密として扱い、顧客データを損失や漏洩から保護します。 Azure 内の顧客データが確実にセキュリティで保護されるように、Microsoft では一連の堅牢なデータ保護制御および機能を実装して管理しています。

- [Azure での顧客データの保護について](../security/fundamentals/protection-customer-data.md) 

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4:転送中のすべての機密情報を暗号化する

**ガイダンス**:Cognitive Search では、トランスポート層セキュリティ 1.2 を使用して転送中のデータが暗号化され、すべての接続に対して常に暗号化 (SSL/TLS) が適用されます。 これにより、クライアントとサービス間ですべてのデータが "転送中" に暗号化されます。

- [Azure での転送中の暗号化の概要](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit) 

**Azure Security Center の監視**: 適用なし

**責任**: Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5:アクティブ検出ツールを使用して機密データを特定する

**ガイダンス**:Cognitive Search では、データの識別、分類、損失防止の各機能はまだ使用できません。 コンプライアンスのために必要な場合は、サードパーティ ソリューションを実装します。 

Microsoft では、基になるプラットフォームを管理し、顧客のすべてのコンテンツを機密として扱い、顧客データを損失や漏洩から保護します。 Azure 内の顧客データが確実にセキュリティで保護されるように、Microsoft では一連の堅牢なデータ保護制御および機能を実装して管理しています。

- [Azure での顧客データの保護について](../security/fundamentals/protection-customer-data.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4.6:Azure RBAC を使用してリソースへのアクセスを制御する

**ガイダンス**:サービスの管理については、Azure のロールベースのアクセス制御 (Azure RBAC) を使用してキーと構成へのアクセスを管理します。 インデックス作成やクエリなどのコンテンツ操作については、Cognitive Search では ID ベースのアクセス制御モデルの代わりにキーを使用します。 Azure RBAC を使用してキーへのアクセスを制御します。
- [Azure RBAC を構成する方法](../role-based-access-control/role-assignments-portal.md) 

 
- [Cognitive Search に対する管理アクセス用のロールを使用する方法](./search-security-rbac.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7:ホストベースのデータ損失防止を使用してアクセス制御を実施する

**ガイダンス**:Cognitive Search には適用されません。 このガイドラインは、コンピューティング リソースを対象にしています。 

Microsoft では、Cognitive Search 用の基になるインフラストラクチャを管理し、顧客データの損失や漏洩を防ぐための厳格な管理を実施してきました。

- [Azure での顧客データの保護について](../security/fundamentals/protection-customer-data.md)

**Azure Security Center の監視**: 適用なし

**責任**: Microsoft

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8:機密情報を保存時に暗号化する

**ガイダンス**:Cognitive Search では、インデックス付きコンテンツが保存時に Microsoft マネージド キーを使用して自動的に暗号化されます。 保護を強化する必要がある場合は、Azure Key Vault 内で作成して管理するキーを使用して、既定の暗号化を第 2 の暗号化レイヤーで補完できます。

- [Azure Cognitive Search のデータ暗号化のためにカスタマー マネージド キーを構成する](./search-security-manage-encryption-keys.md)

- [Azure での保存時の暗号化の概要](../security/fundamentals/encryption-atrest.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: 共有

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9:重要な Azure リソースへの変更に関するログとアラート

**ガイダンス**:Cognitive Search の運用インスタンスやその他の重要または関連リソースに変更が発生したときにアラートを作成するには、Azure Monitor と Azure アクティビティ ログを使用します。

- [Azure アクティビティ ログ イベントのアラートを作成する方法](../azure-monitor/alerts/alerts-activity-log.md)

- [Cognitive Search アクティビティのアラートを作成する方法](./search-monitor-logs.md)

**Azure Security Center の監視**: はい

**責任**: Customer

## <a name="vulnerability-management"></a>脆弱性の管理

*詳細については、[Azure セキュリティ ベンチマークの「脆弱性の管理](../security/benchmarks/security-control-vulnerability-management.md)」を参照してください。*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1:自動化された脆弱性スキャン ツールを実行する

**ガイダンス**:現在、Cognitive Search では使用できません。  検索サービスのコンテンツを格納するクラスターについては、Microsoft がそれらのクラスターの脆弱性管理を行います。

**Azure Security Center の監視**: 現在は使用できません

**責任**: Microsoft

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2:自動化されたオペレーティング システム修正プログラム管理ソリューションを展開する

**ガイダンス**:Cognitive Search には適用されません。 この推奨事項は、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="53-deploy-an-automated-patch-management-solution-for-third-party-software-titles"></a>5.3:サード パーティ ソフトウェア タイトル用の自動化された修正プログラム管理ソリューションをデプロイする

**ガイダンス**:Cognitive Search には適用されません。 この推奨事項は、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4:バックツーバックの脆弱性スキャンを比較する

**ガイダンス**:Cognitive Search には適用されません。 Microsoft では、Cognitive Search サービスをサポートしている基になるシステムで脆弱性の管理を行います。

**Azure Security Center の監視**: 適用なし

**責任**: Microsoft

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5:リスク評価プロセスを使用して、検出された脆弱性の修復に優先順位を付ける

**ガイダンス**:Cognitive Search には適用されません。 これには、脆弱性スキャンの結果用の標準のリスク評価またはスコアリング システムは用意されていません。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

## <a name="inventory-and-asset-management"></a>インベントリと資産の管理

*詳細については、[Azure セキュリティ ベンチマークの「インベントリと資産の管理](../security/benchmarks/security-control-inventory-asset-management.md)」を参照してください。*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1:自動化された資産検出ソリューションを使用する

**ガイダンス**:Azure Resource Graph を使用して、サブスクリプション内のすべてのリソース (コンピューティング、ストレージ、ネットワーク、ポート、プロトコルなど) のクエリおよび検出を行います。  

テナントで適切な (読み取り) アクセス許可を確認し、サブスクリプション内のリソースだけでなく、すべての Azure サブスクリプションを列挙します。  

- [Azure Resource Graph Explorer を使用してクエリを作成する方法](../governance/resource-graph/first-query-portal.md) 

- [Azure サブスクリプションを表示する方法](/powershell/module/az.accounts/get-azsubscription) 

- [Azure RBAC について](../role-based-access-control/overview.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="62-maintain-asset-metadata"></a>6.2:資産メタデータを保持する

**ガイダンス**:メタデータを使用して Azure リソースにタグを適用し、それらを各分類に論理的に整理します。

- [タグを作成して使用する方法](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="63-delete-unauthorized-azure-resources"></a>6.3:承認されていない Azure リソースを削除する

**ガイダンス**: 必要に応じて、タグ付け、管理グループ、および個別のサブスクリプションを使用して、資産の整理と追跡を行います。 定期的にインベントリを調整し、承認されていないリソースがサブスクリプションから適切なタイミングで削除されるようにします。
- [追加の Azure サブスクリプションを作成する方法](../cost-management-billing/manage/create-subscription.md) 

- [管理グループを作成する方法](../governance/management-groups/create-management-group-portal.md) 

- [タグを作成して使用する方法](../azure-resource-manager/management/tag-resources.md) 

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4:承認された Azure リソースのインベントリを定義および管理する

**ガイダンス**:Cognitive Search でのインデックス作成とスキルセット処理に関連する承認済みの Azure リソースの一覧を定義します。

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5:承認されていない Azure リソースを監視する

**ガイダンス**:組織のポリシーと標準に従って使用を承認する Azure リソースのインベントリを事前に定義してから、Azure Policy または Azure Resource Graph を使用して未承認の Azure リソースを監視することをお勧めします。

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md) 

- [Azure Graph を使用してクエリを作成する方法](../governance/resource-graph/first-query-portal.md) 

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6:コンピューティング リソース内の承認されていないソフトウェア アプリケーションを監視する

**ガイダンス**:Cognitive Search には適用されません。 このガイダンスは、コンピューティング リソースを対象にしています。

組織のポリシーとセキュリティ標準に従って承認されたソフトウェア アプリケーションのインベントリを作成し、Azure コンピューティング リソースにインストールされている未承認のソフトウェア タイトルを監視することをお勧めします。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7:承認されていない Azure リソースとソフトウェア アプリケーションを削除する

**ガイダンス**:Cognitive Search には適用されません。 この推奨事項は、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="68-use-only-approved-applications"></a>6.8:承認されたアプリケーションのみを使用する

**ガイダンス**:Cognitive Search には適用されません。 これによってコンピューティング リソースが公開されることはなく、そのリソースのいずれかにソフトウェア アプリケーションをインストールすることが許可されることもありません。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="69-use-only-approved-azure-services"></a>6.9:承認された Azure サービスのみを使用する

**ガイダンス**:次の組み込みのポリシー定義を使用して顧客のサブスクリプション内に作成できるリソースの種類に制限を適用するには、Azure Policy を使用します。

- 許可されないリソースの種類
- 許可されるリソースの種類

サブスクリプション内のリソースのクエリまたは検出を行うには、Azure Resource Graph を使用します。 環境に存在するすべての Azure リソースが承認されていることを確認します。

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md) 

- [Azure Policy を使用して特定のリソースの種類を拒否する方法](../governance/policy/samples/index.md) 

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10:承認されたソフトウェア タイトルのインベントリを管理する

**ガイダンス**:Cognitive Search には適用されません。 この推奨事項は、コンピューティング リソース上で実行されているアプリケーションを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11:Azure Resource Manager を操作するユーザーの機能を制限する

**ガイダンス**:サービス管理については、Azure 条件付きアクセスを使用して "Microsoft Azure Management" アプリに "アクセスのブロック" を構成することによって、Azure Resource Manager を操作するユーザーの機能を制限します。 

他のすべての操作 (特に、Cognitive Search のコンテンツに関連する操作) については、要求を認証するために使用されるキーへのアクセスを制御します。

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6.12:コンピューティング リソースでスクリプトを実行するユーザーの機能を制限する

**ガイダンス**:Cognitive Search には適用されません。 この推奨事項は、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13:リスクの高いアプリケーションを物理的または論理的に分離する

**ガイダンス**:Cognitive Search には適用されません。 この推奨事項は、Azure App Service またはコンピューティング リソース上で実行されている Web アプリケーションを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

## <a name="secure-configuration"></a>セキュリティで保護された構成

*詳細については、[Azure セキュリティ ベンチマークの「セキュリティで保護された構成](../security/benchmarks/security-control-secure-configuration.md)」を参照してください。*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1:すべての Azure リソースに対してセキュリティで保護された構成を確立する

**ガイダンス**:Azure Cognitive Search リソースの構成を監査または適用するためのカスタム ポリシーを作成するには、"Microsoft.Search" 名前空間で Azure Policy エイリアスを使用します。 Cognitive Search サービスに次のような組み込みの Azure Policy 定義を使用することもできます。

- Azure リソースの監査ログ記録を有効にする

Azure Resource Manager には、JavaScript Object Notation (JSON) でテンプレートをエクスポートする機能があり、構成が確実に組織のセキュリティ要件を満たすように確認する必要があります。 

また、ご利用の Azure リソース用の安全な構成基準として Azure Security Center からのレコメンデーションを使用することもできます。 

- [Azure Cognitive Search 用の Azure Policy 規制コンプライアンス コントロール](./security-controls-policy.md)

- [使用可能な Azure Policy エイリアスを表示する方法](/powershell/module/az.resources/get-azpolicyalias)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="72-establish-secure-operating-system-configurations"></a>7.2:セキュリティで保護されたオペレーティング システムの構成を確立する

**ガイダンス**:Cognitive Search には適用されません。 この推奨事項は、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3:セキュリティで保護された Azure リソースの構成を維持する

**ガイダンス**:Cognitive Search サービス リソース全体にセキュリティで保護された設定を適用するには、Azure Policy の [deny] および [deploy if not exist] 効果を使用します。 

Azure Resource Manager テンプレートを使用して、組織に必要な Azure リソースのセキュリティ構成を維持できます。 

- [Azure Policy の効果について](../governance/policy/concepts/effects.md)

- [Azure Cognitive Search 用の Azure Policy 規制コンプライアンス コントロール](./security-controls-policy.md)

- [コンプライアンスを強制するポリシーの作成と管理](../governance/policy/tutorials/create-and-manage.md)

- [Azure Resource Manager テンプレートの概要](../azure-resource-manager/templates/overview.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4:セキュリティで保護されたオペレーティング システムの構成を維持する

**ガイダンス**:Cognitive Search には適用されません。 この推奨事項は、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5:Azure リソースの構成を安全に格納する

**ガイダンス**: カスタムの Azure Policy 定義を使用する場合は、Azure DevOps または Azure Repos を使用して、コードを安全に格納して管理します。

- [Azure DevOps でコードを格納する方法](/azure/devops/repos/git/gitworkflow)

- [Azure Repos のドキュメント](/azure/devops/repos/index)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="76-securely-store-custom-operating-system-images"></a>7.6:カスタム オペレーティング システム イメージを安全に格納する

**ガイダンス**:Cognitive Search には適用されません。 この推奨事項は、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7:Azure リソース用の構成管理ツールをデプロイする

**ガイダンス**:Azure Policy を使用して、Cognitive Search サービス リソースの標準的なセキュリティ構成を定義して実装します。 

ネットワーク構成を監査または適用するには、エイリアスを使用してカスタム ポリシーを作成します。 また、特定のリソースに関連する組み込みのポリシー定義を使用することもできます。 

さらに、Azure Automation を使用して、構成の変更をデプロイしたりポリシーの例外を管理したりすることもできます。 

- [Azure Cognitive Search 用の Azure Policy 規制コンプライアンス コントロール](./security-controls-policy.md)

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8:オペレーティング システム用の構成管理ツールをデプロイする

**ガイダンス**:Cognitive Search には適用されません。 この推奨事項は、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9:Azure リソースの自動構成監視を実装する

**ガイダンス**:Cognitive Search サービス リソースのベースライン スキャンを実行するには、Security Center を使用します。  さらに、Azure Policy を使用してご使用のリソース構成をアラート送信および監査します。 

- [Azure Security Center の推奨事項を修復する方法](../security-center/security-center-remediate-recommendations.md)

- [Azure Cognitive Search 用の Azure Policy 規制コンプライアンス コントロール](./security-controls-policy.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10:オペレーティング システムの自動構成監視を実装する

**ガイダンス**:Cognitive Search には適用されません。 この推奨事項は、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="711-manage-azure-secrets-securely"></a>7.11:Azure シークレットを安全に管理する

**ガイダンス**:Azure マネージド ID を Azure Key Vault と組み合わせて使用して、クラウド アプリケーションのシークレット管理を簡素化します。
- [Azure リソースに対してマネージド ID を使用する方法](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md) 
- [キー コンテナーを作成する方法](../key-vault/general/quick-create-portal.md) 

- [マネージド ID で Key Vault の認証を提供する方法](../key-vault/general/assign-access-policy-portal.md) 

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="712-manage-identities-securely-and-automatically"></a>7.12:ID を安全かつ自動的に管理する

**ガイダンス**:Azure マネージド ID を使用すると、Azure Active Directory (Azure AD) で自動的に管理されている ID を使用して、Key Vault などの他の Azure サービスやインデクサー データ ソースへのアクセスを Cognitive Search に提供できます。 マネージド ID を使用すると、コード内に資格情報を記述することなく、Azure AD 認証をサポートする任意のサービス (Azure Key Vault を含む) に対して認証することができます。 

- [マネージド ID を使用してデータ ソースへのインデクサー接続を設定する](./search-howto-managed-identities-data-sources.md)

- [マネージド ID を使用してデータ暗号化のためにカスタマー マネージド キーを構成する](./search-security-manage-encryption-keys.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13:意図しない資格情報の公開を排除する

**ガイダンス**:Cognitive Search には適用されません。 これによってコードがホストされることはなく、識別される資格情報もありません。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

## <a name="malware-defense"></a>マルウェアからの防御

*詳細については、[Azure セキュリティ ベンチマークの「マルウェアからの防御](../security/benchmarks/security-control-malware-defense.md)」を参照してください。*

### <a name="81-use-centrally-managed-antimalware-software"></a>8.1:一元管理されるマルウェア対策ソフトウェアを使用する

**ガイダンス**:Cognitive Search には適用されません。 この推奨事項は、コンピューティング リソースを対象にしています。

Microsoft のマルウェア対策が、Azure サービス (Azure Cognitive Search など) をサポートする基になるホストで有効にされます。ただし、顧客のコンテンツに対しては実行されません。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2:非コンピューティング Azure リソースにアップロードするファイルを事前にスキャンする

**ガイダンス**:Cognitive Search、Blob Storage、Azure SQL Database などの非コンピューティング Azure リソースにアップロードされるコンテンツはすべて、事前にスキャンしてください。 

非コンピューティング Azure リソースにアップロードされている任意のコンテンツを事前にスキャンするのは、お客様の責任となります。 Microsoft は、お客様のデータにアクセスできないため、お客様に代わってお客様のコンテンツのマルウェア対策スキャンを実行することはできません。

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="83-ensure-antimalware-software-and-signatures-are-updated"></a>手順 8.3:マルウェア対策ソフトウェアと署名が確実に更新されるようにする

**ガイダンス**:Cognitive Search には適用されません。 そのリソースにマルウェア対策ソリューションをインストールすることはできません。 基になるプラットフォームでは、Microsoft によりマルウェア対策ソフトウェアとシグネチャの更新が処理されます。 

お客様の組織がコンピューティング リソースを所有していて、それを検索ソリューションに使用している場合は、Security Center のコンピューティングとアプリの推奨事項に従ってすべてのエンドポイントが最新のシグネチャに更新されていることを確認します。 Linux の場合は、サード パーティのマルウェア対策ソリューションを使用します。

**Azure Security Center の監視**: 適用なし

**責任**: 共有

## <a name="data-recovery"></a>データの復旧

*詳細については、[Azure セキュリティ ベンチマークの「データの復旧](../security/benchmarks/security-control-data-recovery.md)」を参照してください。*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1:定期的に自動バックアップを行う

**ガイダンス**:検索サービスに格納されているコンテンツを Azure Backup またはその他の組み込みメカニズムを使用してバックアップすることはできませんが、アプリケーションのソース コードとプライマリ データ ソースからインデックスを再構築したり、インデックス付きのコンテンツを取得して格納するためのカスタム ツールを作成したりすることができます。

- [GitHub にあるインデックスのバックアップと復元のサンプル](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/index-backup-restore)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: システムの完全バックアップを実行し、すべてのカスタマー マネージド キーをバックアップする

**ガイダンス**:Cognitive Search では現在、検索サービスのデータの自動バックアップがサポートされていないため、手動プロセスを使用してバックアップする必要があります。  また、Azure Key Vault にカスタマー マネージド キーをバックアップすることもできます。 

- [Azure Cognitive Search インデックスのバックアップと復元](/samples/azure-samples/azure-search-dotnet-samples/azure-search-backup-restore-index/)

- [Azure 上でキー コンテナーのキーをバックアップする方法](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3:カスタマー マネージド キーを含むすべてのバックアップを検証する

**ガイダンス**:Cognitive Search では現在、検索サービスのデータの自動バックアップがサポートされていないため、手動プロセスを使用してバックアップと復元を行う必要があります。  バックアップ プロセスのエンド ツー エンドの整合性を確認するために、手動でバックアップしたコンテンツのデータ復元を定期的に実行します。

- [Azure Cognitive Search インデックスのバックアップと復元](/samples/azure-samples/azure-search-dotnet-samples/azure-search-backup-restore-index/)

- [Azure でキー コンテナーのキーを復元する方法](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: バックアップとカスタマー マネージド キーの保護を保証する

**ガイダンス**:Cognitive Search では現在、検索サービスのデータの自動バックアップがサポートされていないため、手動プロセスを使用してバックアップする必要があります。  また、Azure Key Vault にカスタマー マネージド キーをバックアップすることもできます。 

Key Vault で論理的な削除と消去保護を有効にして、偶発的または悪意のある削除からキーを保護します。 Azure Storage を使用して手動でバックアップを格納する場合は、論理的な削除機能を有効にすれば、BLOB または BLOB のスナップショットが削除されたときにデータを保存し、復旧することができます。 

- [Azure Cognitive Search インデックスのバックアップと復元](/samples/azure-samples/azure-search-dotnet-samples/azure-search-backup-restore-index/)

- [Key Vault で論理的な削除と消去保護を有効にする方法](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

- [Azure Blob Storage の論理的な削除](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

## <a name="incident-response"></a>インシデント対応

*詳細については、[Azure セキュリティ ベンチマークの「インシデント対応](../security/benchmarks/security-control-incident-response.md)」を参照してください。*

### <a name="101-create-an-incident-response-guide"></a>10.1:インシデント対応ガイドを作成する

**ガイダンス**: 組織のインシデント対応ガイドを作成します。 要員のすべてのロールと、検出からインシデント後のレビューまでのインシデント対応と管理のフェーズを定義する、書面によるインシデント対応計画があることを確認します。

- [独自のセキュリティ インシデント対応プロセスを構築するためのガイダンス](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft Security Response Center のインシデントの構造](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [お客様は、独自のインシデント対応計画の作成に役立つ NIST の「コンピューター セキュリティ インシデント対応ガイド」を利用することもできます](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2:インシデントのスコアリングと優先順位付けの手順を作成する

**ガイダンス**: Security Center によって各アラートに重大度が割り当てられるため、最初に調査する必要があるアラートの優先順位付けに役立ちます。 重要度は、アラートの発行に使用された Security Center の信頼度と、アラートの原因となったアクティビティの背後に悪意のある意図があったかどうかの信頼レベルに基づいて決まります。

さらに、タグを使用してサブスクリプションをマークし、Azure リソース (特に、機密データを処理するもの) を識別して分類するための命名システムを作成します。 インシデントが発生した Azure リソースと環境の重要度に基づいてアラートの修復に優先順位を付けることは、お客様の責任です。

- [タグを使用した Azure リソースの整理](../azure-resource-manager/management/tag-resources.md)

- [Security alerts in Azure Security Center](../security-center/security-center-alerts-overview.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="103-test-security-response-procedures"></a>10.3:セキュリティ対応手順のテスト

**ガイダンス**:定期的にシステムのインシデント対応機能をテストする演習を実施します。 弱点やギャップを特定し、必要に応じて計画を見直します。

- [NIST の出版物『IT 計画と機能に関するテスト、トレーニング、および演習プログラムのガイド』をご覧ください](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4:セキュリティ インシデントの連絡先の詳細を指定し、セキュリティ インシデントのアラート通知を構成します

**ガイダンス**:セキュリティ インシデントの連絡先情報は、Microsoft Security Response Center (MSRC) でユーザーのデータが違法または権限のないユーザーによってアクセスされたことが検出された場合に、Microsoft からの連絡先として使用されます。 事後にインシデントをレビューして、問題が解決されていることを確認します。

- [Azure Security Center のセキュリティ連絡先を設定する方法](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5:インシデント対応システムにセキュリティ アラートを組み込む

**ガイダンス**: 連続エクスポート機能を使用して Security Center のアラートと推奨事項をエクスポートします。 連続エクスポートを使用すると、アラートと推奨事項を手動で、または連続的な方法でエクスポートできます。 Security Center データ コネクタを使用してアラートを Azure Sentinel にストリーミングできます。

- [連続エクスポートを構成する方法](../security-center/continuous-export.md)

- [Azure Sentinel にアラートをストリーミングする方法](../sentinel/connect-azure-security-center.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="106-automate-the-response-to-security-alerts"></a>10.6:セキュリティ アラートへの対応を自動化する

**ガイダンス**:セキュリティ アラートやセキュリティに関する推奨事項に対して "Logic Apps" 経由で応答を自動的にトリガーするには、Azure Security Center のワークフローの自動化機能を使用します。

- [ワークフローの自動化と Logic Apps を構成する方法](../security-center/workflow-automation.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

## <a name="penetration-tests-and-red-team-exercises"></a>侵入テストとレッド チーム演習

*詳細については、[Azure セキュリティ ベンチマークの「侵入テストとレッド チーム演習](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)」を参照してください。*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1:Azure リソースの通常の侵入テストを実施し、セキュリティに関する重大な調査結果がすべて、確実に修復されるようにする

**ガイダンス**: お客様の侵入テストが Microsoft のポリシーに違反しないように、Microsoft クラウド侵入テストの実施ルールに従ってください。 Microsoft が管理しているクラウド インフラストラクチャ、サービス、アプリケーションに対する Red Teaming およびライブ サイト侵入テストに関する Microsoft の戦略と実施を活用してください。
- [侵入テストの実施ルール](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)
- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center の監視**: 適用なし

**責任**: 共有

## <a name="next-steps"></a>次のステップ

- [Azure セキュリティ ベンチマーク](../security/benchmarks/overview.md)に関するページを参照する
- [Azure セキュリティ ベースライン](../security/benchmarks/security-baselines-overview.md)の詳細について学習する