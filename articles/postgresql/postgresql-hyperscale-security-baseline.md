---
title: Azure Database for PostgreSQL の Azure セキュリティ ベースライン - Hyperscale
description: Azure Database for PostgreSQL - Hyperscale セキュリティ ベースラインにより、Azure セキュリティ ベンチマークで指定されているセキュリティに関する推奨事項を実装するための手順のガイダンスとリソースが提供されます。
author: msmbaldwin
ms.service: postgresql
ms.topic: conceptual
ms.date: 04/08/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 256b061e11ac77af9d229e3bd8f96b033e925f6d
ms.sourcegitcommit: c6a2d9a44a5a2c13abddab932d16c295a7207d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/09/2021
ms.locfileid: "107284512"
---
# <a name="azure-security-baseline-for-azure-database-for-postgresql---hyperscale"></a>Azure Database for PostgreSQL の Azure セキュリティ ベースライン - Hyperscale

このセキュリティ ベースラインにより、[Azure セキュリティ ベンチマーク バージョン 1.0](../security/benchmarks/overview-v1.md) のガイダンスが Azure Database for PostgreSQL - Hyperscale に適用されます。 Azure セキュリティ ベンチマークには、Azure 上のクラウド ソリューションをセキュリティで保護する方法に関する推奨事項がまとめてあります。 この内容は、Azure セキュリティ ベンチマーク、および Azure Database for PostgreSQL - Hyperscale に適用できる関連ガイダンスによって定義されている **セキュリティ コントロール** でグループ化されています。 

> [!NOTE]
> Azure Database for PostgreSQL - Hyperscale に適用されない、または Microsoft の責任である **コントロール** は除外されています。 Azure Database for PostgreSQL - Hyperscale を完全に Azure セキュリティ ベンチマークにマップする方法については、 **[完全な Azure Database for PostgreSQL - Hyperscale セキュリティ ベースライン マッピング ファイル](https://github.com/MicrosoftDocs/SecurityBenchmarks/raw/master/Azure%20Offer%20Security%20Baselines/1.1/azure-database-for-postgresql-hyperscale-security-baseline-v1.1.xlsx)** を参照してください。

## <a name="network-security"></a>ネットワークのセキュリティ

*詳細については、[Azure セキュリティ ベンチマークの「ネットワークのセキュリティ](../security/benchmarks/security-control-network-security.md)」を参照してください。*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1:仮想ネットワーク内の Azure リソースを保護する

**ガイダンス**: Azure Database for PostgreSQL サーバーのファイアウォールは、どのコンピューターに権限を持たせるかを指定するまで、Hyperscale (Citus) コーディネーター ノードへのすべてのアクセスを遮断します。 ファイアウォールは、各要求の送信元 IP アドレスに基づいてサーバーへのアクセス権を付与します。 ファイアウォールを構成するには、受け入れ可能な IP アドレスの範囲を指定するファイアウォール規則を作成します。 ファイアウォール規則はサーバー レベルで作成できます。

- [Azure Database for PostgreSQL - Hyperscale (Citus) でのファイアウォール規則の構成方法](concepts-hyperscale-firewall-rules.md)

**責任**: Customer

**Azure Security Center の監視**: [Azure セキュリティ ベンチマーク](/azure/governance/policy/samples/azure-security-benchmark)は、Security Center の既定のポリシー イニシアチブであり、[Security Center の推奨事項](/azure/security-center/security-center-recommendations)の基礎となります。 このコントロールに関連する Azure Policy 定義は、Security Center によって自動的に有効になります。 この制御に関連するアラートでは、関連するサービスのために [Azure Defender](/azure/security-center/azure-defender) プランが必要になる場合があります。

**Azure Policy 組み込み定義 - Microsoft.DBforPostgreSQL**:

[!INCLUDE [Resource Policy for Microsoft.DBforPostgreSQL 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.dbforpostgresql-1-1.md)]

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9:ネットワーク デバイスの標準的なセキュリティ構成を維持する

**ガイダンス**: Azure Policy を使用して、Azure Database for PostgreSQL インスタンスに関連付けられているネットワーク設定とネットワーク リソースの標準的なセキュリティ構成を定義して実装します。 Azure Database for PostgreSQL インスタンスのネットワーク構成を監査または適用するためのカスタム ポリシーを作成するには、"Microsoft.Network" 名前空間で Azure Policy エイリアスを使用します。

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

- [ネットワークに関する Azure Policy のサンプル](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network)

- [Azure Blueprint を作成する方法](../governance/blueprints/create-blueprint-portal.md)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="logging-and-monitoring"></a>ログ記録と監視

*詳細については、[Azure セキュリティ ベンチマークの「ログ記録と監視](../security/benchmarks/security-control-logging-monitoring.md)」を参照してください。*

### <a name="22-configure-central-security-log-management"></a>2.2:セキュリティ ログの一元管理を構成する

**ガイダンス**: コントロール プレーンの監査ログ記録については、Azure アクティビティ ログの診断設定を有効にして、Log Analytics ワークスペース、Azure イベント ハブ、または Azure ストレージ アカウントにログを送信してアーカイブします。 Azure アクティビティ ログのデータを使用すると、Azure リソースのコントロール プレーン レベルで実行された書き込み操作 (PUT、POST、DELETE) について、"いつだれが何を" 行ったのかを特定することができます。

また、Azure Monitor を介してログを取り込み、Hyperscale (Citus) によって生成されたセキュリティ データを集計します。 Azure Monitor 内で Log Analytics ワークスペースを使用してクエリを発行し、分析を実行して、長期/アーカイブ ストレージにはストレージ アカウントを使用します。 または、Azure Sentinel またはサードパーティのセキュリティ インシデントおよびイベント管理 (SIEM) に対してデータを有効にしてオンボードすることもできます。 

- [Azure アクティビティ ログの診断設定を有効にする方法](../azure-monitor/essentials/activity-log.md)

- [Hyperscale (Citus) のメトリック](concepts-hyperscale-monitoring.md)

- [Azure Sentinel をオンボードする方法](../sentinel/quickstart-onboard.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3:Azure リソースの監査ログ記録を有効にする

**ガイダンス**: Hyperscale (Citus) には、サーバー グループ内の各ノード用のメトリックが用意されています。 メトリックを使うと、サポート リソースの動作を分析できます。 各メトリックは 1 分間隔で出力されます。履歴は最大 30 日分です。

コントロール プレーンの監査ログ記録については、Azure アクティビティ ログの診断設定を有効にして、Log Analytics ワークスペース、Azure イベント ハブ、または Azure ストレージ アカウントにログを送信してアーカイブします。 Azure アクティビティ ログのデータを使用すると、Azure リソースのコントロール プレーン レベルで実行された書き込み操作 (PUT、POST、DELETE) について、"いつだれが何を" 行ったのかを特定することができます。

また、Azure Monitor を介してログを取り込み、Hyperscale (Citus) によって生成されたセキュリティ データを集計します。 Azure Monitor 内で Log Analytics ワークスペースを使用してクエリを発行し、分析を実行して、長期/アーカイブ ストレージにはストレージ アカウントを使用します。 または、Azure Sentinel またはサードパーティのセキュリティ インシデントおよびイベント管理 (SIEM) に対してデータを有効にしてオンボードすることもできます。 

- [Hyperscale (Citus) のメトリック](concepts-hyperscale-monitoring.md)

- [Azure アクティビティ ログの診断設定を有効にする方法](../azure-monitor/essentials/activity-log.md)

- [Azure Sentinel をオンボードする方法](../sentinel/quickstart-onboard.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="25-configure-security-log-storage-retention"></a>2.5:セキュリティ ログのストレージ保持を構成する

**ガイダンス**: Azure Monitor 内で、Hyperscale (Citus) ログを保持するために使用される Log Analytics ワークスペースに対して、組織のコンプライアンス規則に従って保持期間を設定します。 長期/アーカイブ ストレージには Azure Storage アカウントを使用します。

- [Log Analytics ワークスペースのログ保持パラメーターを設定する方法](https://docs.microsoft.com/azure/azure-monitor/logs/manage-cost-storage#change-the-data-retention-period)

- [ Azure ストレージ アカウントでのリソース ログの格納](https://docs.microsoft.com/azure/azure-monitor/essentials/resource-logs#send-to-azure-storage)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="26-monitor-and-review-logs"></a>2.6:ログを監視して確認する

**ガイダンス**: Hyperscale (Citus) インスタンスからのログを分析および監視して、異常な動作がないか確認します。 ログを確認し、ログ データに対してクエリを実行するには、Azure Monitor の Log Analytics を使用します。 または、Azure Sentinel またはサードパーティの SIEM に対してデータを有効にしてオンボードすることもできます。

- [Azure Sentinel をオンボードする方法](../sentinel/quickstart-onboard.md)

- [Log Analytics に関する詳細](../azure-monitor/logs/log-analytics-tutorial.md)

- [Azure Monitor でカスタム クエリを実行する方法](../azure-monitor/logs/get-started-queries.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7:異常なアクティビティについてのアラートを有効にする

**ガイダンス**: Hyperscale (Citus) の診断設定を有効にし、Log Analytics ワークスペースにログを送信することができます。 お使いの Azure のサービスの監視メトリックに基づいて、アラートを構成して受信することができます。 ログを確認し、ログ データに対してクエリを実行するには、Azure Monitor の Log Analytics を使用します。 または、Azure Sentinel またはサードパーティの SIEM に対してデータを有効にしてオンボードすることもできます。

Log Analytics ワークスペースを Azure Sentinel にオンボードします。セキュリティ オーケストレーション自動応答 (SOAR) ソリューションが提供されます。 これにより、プレイブック (自動化されたソリューション) を作成して、セキュリティの問題を修復するために使用できます。

- [Hyperscale (Citus) のメトリック](howto-hyperscale-alert-on-metric.md)

- [Azure アクティビティ ログ用に診断設定を構成する方法](../azure-monitor/essentials/activity-log.md)

- [Azure Sentinel をオンボードする方法](../sentinel/quickstart-onboard.md)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="identity-and-access-control"></a>ID およびアクセス制御

*詳細については、[Azure セキュリティ ベンチマークの「ID およびアクセス制御](../security/benchmarks/security-control-identity-access-control.md)」を参照してください。*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: 管理アカウントのインベントリを維持する

**ガイダンス**: Hyperscale (Citus) インスタンスのコントロール プレーン (Azure portal など) への管理アクセス権を持つユーザー アカウントのインベントリを管理します。 さらに、Hyperscale (Citus) インスタンスの (データベース自体内の) データ プレーンへのアクセス権を持つ管理アカウントのインベントリを管理します。

Hyperscale (Citus) では、組み込みのロールベースのアクセス制御はサポートされませんが、特定のリソース プロバイダーの操作に基づいてカスタム ロールを作成することはできます。

さらに、PostgreSQL エンジンはロールを使用してデータベース オブジェクトへのアクセスを制御し、新しく作成された Hyperscale (Citus) サーバー グループにはいくつかのロールが事前に定義されています。 ユーザーの特権を変更するには、PgAdmin や psql などのツールを使用して、標準的な PostgreSQL コマンドを使用します。

- [Azure サブスクリプションのカスタム ロールについて](../role-based-access-control/custom-roles.md) 

- [Azure Database for PostgreSQL リソース プロバイダーの操作について](https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations#microsoftdbforpostgresql) 

- [Azure Database for PostgreSQL のアクセス管理について](https://docs.microsoft.com/azure/postgresql/concepts-security#access-management)

- [Azure Database for PostgreSQL - Hyperscale (Citus) でユーザーを作成する方法](howto-hyperscale-create-users.md)

- [psql を使用して PostgreSQL - Hyperscale (Citus) に接続する方法](https://docs.microsoft.com/azure/postgresql/quickstart-create-hyperscale-portal#connect-to-the-database-using-psql)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="32-change-default-passwords-where-applicable"></a>3.2: 既定のパスワードを変更する (該当する場合)

**ガイダンス**: Azure Active Directory (Azure AD) には、既定のパスワードの概念がありません。 パスワードを必要とする他の Azure リソースでは、パスワードが強制的に作成されます。これには複雑な要件と、サービスによって異なるパスワードの最小文字数が適用されます。 既定のパスワードが使用される可能性があるサードパーティ製のアプリケーションとマーケットプレース サービスについては、お客様が責任を負うものとします。

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: 専用管理者アカウントを使用する

**ガイダンス**: Hyperscale (Citus) インスタンスへのアクセスに使用される専用管理者アカウントの使用に関する標準的な操作手順を作成します。 Azure リソースを管理する管理者アカウントは、Azure Active Directory (Azure AD) に関連付けられています。また、データベースのアクセス許可を管理するための、Hyperscale (Citus) サーバー グループ内に存在するローカル サーバー管理者アカウントもあります。 Azure Security Center ID とアクセス管理を使用して、Azure AD 内の管理アカウントの数を監視します。

- [Azure Security Center ID とアクセスについて](../security-center/security-center-identity-access.md)

- [Azure Database for PostgreSQL - Hyperscale (Citus) でユーザーを作成する方法](howto-hyperscale-create-users.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: すべての Azure Active Directory ベースのアクセスに多要素認証を使用する

**ガイダンス**: Azure portal にアクセスするには、Azure Active Directory (Azure AD) の多要素認証を有効にし、Azure Security Center ID とアクセス管理の推奨事項に従います。

- [Azure で多要素認証を有効にする方法](../active-directory/authentication/howto-mfa-getstarted.md)

- [Azure Security Center で ID とアクセスを監視する方法](../security-center/security-center-identity-access.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: すべての管理タスクに専用マシン (特権アクセス ワークステーション) を使用する 

**ガイダンス**: 多要素認証が構成された特権アクセス ワークステーション (PAW) を使用して Azure リソースにログインし、構成します。

- [特権アクセス ワークステーションについて](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Azure で多要素認証を有効にする方法](../active-directory/authentication/howto-mfa-getstarted.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="37-alert-on-account-login-behavior-deviation"></a>3.7: アカウント ログイン動作の偏差に関するアラートを生成する

**ガイダンス**: Azure Active Directory (Azure AD) Privileged Identity Management (PIM) を使用して、環境内で疑わしいアクティビティまたは安全ではないアクティビティが発生したときにログとアラートを生成します。

Azure AD のリスク検出を使用して、危険なユーザーの行動に関するアラートとレポートを表示します。

- [Privileged Identity Management (PIM) をデプロイする方法](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Azure AD のリスク検出の概要](../active-directory/identity-protection/overview-identity-protection.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8:承認された場所からのみ Azure リソースを管理する

**ガイダンス**: ポータルや Azure Resource Manager での IP アドレス範囲または国と地域の特定の論理グループからのアクセスのみを許可するには、条件付きアクセスのネームド ロケーションを使用します。

- [Azure でネームド ロケーションを構成する方法](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="39-use-azure-active-directory"></a>3.9: Azure Active Directory を使用する

**ガイダンス**: PostgreSQL リソースを管理する主要な認証および承認システムとして Azure Active Directory (Azure AD) を使用します。 Azure AD でデータを保護するには、保存データと転送中のデータに強力な暗号化を使用します。 また、Azure AD では、ユーザーの資格情報がソルト化およびハッシュされ、安全に格納されます。

Hyperscale (Citus) サーバー グループ内のユーザーを Azure AD アカウントに直接関連付けることはできません。 データベース オブジェクトへのアクセスのためのユーザー特権を変更するには、PgAdmin や psql などのツールで、標準的な PostgreSQL コマンドを使用します。

- [ユーザー ロールの特権を変更する](https://docs.microsoft.com/azure/postgresql/howto-hyperscale-create-users#how-to-modify-privileges-for-user-role)

- [Azure AD インスタンスを作成して構成する方法](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: ユーザー アクセスを定期的に確認して調整する

**ガイダンス**: ローカル データベースへのアクセス権を持つ、および Azure Active Directory (Azure AD) 経由で PostgreSQL リソースを管理するユーザーの両方のアクセス権を確認し、調整します。

Azure のデータベース リソースを管理するためのアクセス権を持つユーザーについては、古いアカウントを検出するために、Azure AD ログを確認してください。 さらに、Azure ID アクセス レビューを使用して、グループ メンバーシップ、Hyperscale (Citus) へのアクセスに使用される可能性のあるエンタープライズ アプリケーションへのアクセス、およびロールの割り当てを効率的に管理します。 ユーザー アクセスを定期的 (たとえば、90 日ごと) に確認し、正当なユーザーだけが継続してアクセスできるようにする必要があります。

- [PostgreSQL ユーザーと割り当てられたロールを確認する](https://www.postgresql.org/docs/current/database-roles.html)

- [Azure AD のレポートについて](/azure/active-directory/reports-monitoring/)

- [Azure ID アクセス レビューの使用方法](../active-directory/governance/access-reviews-overview.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: 非アクティブ化された資格情報へのアクセスの試行を監視する

**ガイダンス**: Azure Active Directory (Azure AD) 内では、Azure AD サインイン アクティビティ、監査、およびリスク イベント ログのソースにアクセスできるため、任意の SIEM または監視ツールと統合することができます。

このプロセスを効率化するには、Azure AD ユーザー アカウントの診断設定を作成し、監査ログとサインイン ログを Log Analytics ワークスペースに送信します。 Log Analytics ワークスペースで必要なアラートを構成できます。

- [Azure アクティビティ ログを Azure Monitor に統合する方法](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: アカウント サインイン動作の偏差に関するアラートを生成する

**ガイダンス**: Azure Active Directory (Azure AD) レベルで、検出された疑わしいアクションに対する自動応答を構成するには、Azure ADの Identity Protection およびリスク検出の機能を使用します。 Azure Sentinel で自動応答を有効にして、組織のセキュリティ対応を実装することができます。

さらに調査するために、Azure Sentinel にログを取り込むこともできます。

- [Azure AD Identity Protection の概要](../active-directory/identity-protection/overview-identity-protection.md)

- [Azure AD の危険なサインインを表示する方法](../active-directory/identity-protection/overview-identity-protection.md)

- [Azure Sentinel をオンボードする方法](../sentinel/quickstart-onboard.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: サポート シナリオで関連する顧客データに Microsoft がアクセスできるようにする

**ガイダンス**: 現在は利用できません。Hyperscale (Citus) では、カスタマー ロックボックスはまだサポートされていません。

- [カスタマー ロックボックスでサポートされているサービスの一覧](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="data-protection"></a>データ保護

*詳細については、[Azure セキュリティ ベンチマークの「データ保護](../security/benchmarks/security-control-data-protection.md)」を参照してください。*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: 機密情報のインベントリを維持する

**ガイダンス**: タグを使用すると、機密情報を格納または処理する Hyperscale (Citus) インスタンスや関連リソースの追跡に役立ちます。

- [タグを作成して使用する方法](../azure-resource-manager/management/tag-resources.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2:機密情報を格納または処理するシステムを分離する

**ガイダンス**:開発、テスト、および運用で別々のサブスクリプションまたは管理グループ、あるいはその両方を実装します。 管理ロールとファイアウォール規則の組み合わせを使用して、Azure Database for PostgreSQL インスタンスへのネットワーク アクセスを分離して制限します。

- [追加の Azure サブスクリプションを作成する方法](../cost-management-billing/manage/create-subscription.md)

- [管理グループを作成する方法](../governance/management-groups/create-management-group-portal.md)

- [Azure Database for PostgreSQL - Hyperscale (Citus) のファイアウォール規則について](concepts-hyperscale-firewall-rules.md)

- [Hyperscale (Citus) のロールについて](howto-hyperscale-create-users.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4:転送中のすべての機密情報を暗号化する

**ガイダンス**: Hyperscale (Citus) コーディネーター ノードへのクライアント アプリケーション接続には、トランスポート層セキュリティ (TLS) 1.2 が必要です。 お使いのデータベース サーバーとクライアント アプリケーション間に TLS 接続を強制すると、サーバーとお使いのアプリケーション間のデータ ストリームを暗号化することにより、中間者 (man in the middle) 攻撃から保護するのに役立ちます。

Azure portal を使用してプロビジョニングされたすべての Azure Database for PostgreSQL サーバーでは、TLS 接続の適用が既定で有効になります。

安全に接続するために、信頼された証明機関 (CA) 証明書ファイル (.cer) から生成されたローカルの証明書ファイルがサードパーティ アプリケーションに必要な場合があります。

- [Azure Database for PostgreSQL - Hyperscale (Citus) での TLS の構成方法](concepts-hyperscale-ssl-connection-security.md)

- [TLS 接続で証明書検証を必要とするアプリケーション](concepts-hyperscale-ssl-connection-security.md)

**責任**: 共有

**Azure Security Center の監視**: [Azure セキュリティ ベンチマーク](/azure/governance/policy/samples/azure-security-benchmark)は、Security Center の既定のポリシー イニシアチブであり、[Security Center の推奨事項](/azure/security-center/security-center-recommendations)の基礎となります。 このコントロールに関連する Azure Policy 定義は、Security Center によって自動的に有効になります。 この制御に関連するアラートでは、関連するサービスのために [Azure Defender](/azure/security-center/azure-defender) プランが必要になる場合があります。

**Azure Policy 組み込み定義 - Microsoft.DBforPostgreSQL**:

[!INCLUDE [Resource Policy for Microsoft.DBforPostgreSQL 4.4](../../includes/policy/standards/asb/rp-controls/microsoft.dbforpostgresql-4-4.md)]

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6:Azure RBAC を使用してリソースへのアクセスを制御する 

**ガイダンス**: Azure ロールベースのアクセス制御 (Azure RBAC) を使用して、Hyperscale (Citus) コントロール プレーン (Azure portal など) へのアクセスを制御します。 Azure RBAC は、データベース内のユーザーのアクセス許可には影響しません。

データベース レベルでユーザーの特権を変更するには、PgAdmin や psql などのツールを使用して、標準的な PostgreSQL コマンドを使用します。

- [Azure RBAC を構成する方法](../role-based-access-control/role-assignments-portal.md)

- [Azure Database for PostgreSQL の SQL を使用してユーザー アクセスを構成する方法](howto-hyperscale-create-users.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8:機密情報を保存時に暗号化する

**ガイダンス**: 少なくとも 1 日に 1 回、Azure Database for PostgreSQL Hyperscale (Citus) では、データ ファイルとデータベース トランザクション ログのスナップショット バックアップが作成されます。 バックアップを使用すると、サーバーを、保持期間内の任意の時点に復元できます (保持期間は現在、すべてのクラスターで 35 日です)。すべてのバックアップが、AES 256 ビット暗号化を使用して暗号化されます。 PostgreSQL Hyperscale (Citus) オファリングでは、暗号化に Microsoft マネージド キーを使用します。

- [Azure PostgreSQL - Hyperscale (Citus) のバックアップの暗号化について](concepts-hyperscale-backup.md)

**責任**: 共有

**Azure Security Center の監視**: なし

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9:重要な Azure リソースへの変更に関するログとアラート

**ガイダンス**: Hyperscale (Citus) の運用インスタンスやその他の重要または関連するリソースへの変更がいつ発生したかに関するアラートを作成するには、Azure Monitor と Azure アクティビティ ログを使用します。

- [Azure アクティビティ ログ イベントのアラートを作成する方法](../azure-monitor/alerts/alerts-activity-log.md)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="vulnerability-management"></a>脆弱性の管理

*詳細については、[Azure セキュリティ ベンチマークの「脆弱性の管理](../security/benchmarks/security-control-vulnerability-management.md)」を参照してください。*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1:自動化された脆弱性スキャン ツールを実行する

**ガイダンス**: 現在は使用できません。Azure Security Center では、Azure Database for PostgreSQL - Hyperscale (Citus) の脆弱性評価はまだサポートされていません。

- [Azure Security Center の Azure PaaS サービスを対象とする機能](../security-center/features-paas.md)

**責任**: 共有

**Azure Security Center の監視**: なし

## <a name="inventory-and-asset-management"></a>インベントリと資産の管理

*詳細については、[Azure セキュリティ ベンチマークの「インベントリと資産の管理](../security/benchmarks/security-control-inventory-asset-management.md)」を参照してください。*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1:自動化された資産検出ソリューションを使用する

**ガイダンス**: サブスクリプション内のすべてのリソース (Hyperscale (Citus) インスタンスを含む) のクエリや検出を実行するには、Azure Resource Graph を使用します。 テナント内の適切な (読み取り) アクセス許可を持っており、サブスクリプション内のリソースだけでなく、すべての Azure サブスクリプションを列挙できることを確認します。

- [Azure Graph を使用してクエリを作成する方法](../governance/resource-graph/first-query-portal.md)

- [Azure サブスクリプションを表示する方法](/powershell/module/az.accounts/get-azsubscription)

- [Azure RBAC について](../role-based-access-control/overview.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="62-maintain-asset-metadata"></a>6.2:資産メタデータを保持する

**ガイダンス**: メタデータを提供する Hyperscale (Citus) インスタンスやその他の関連リソースにタグを適用し、論理的に分類してまとめます。

- [タグを作成して使用する方法](../azure-resource-manager/management/tag-resources.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="63-delete-unauthorized-azure-resources"></a>6.3:承認されていない Azure リソースを削除する

**ガイダンス**: 必要に応じて、タグ付け、管理グループ、および個別のサブスクリプションを使用して、Hyperscale (Citus) インスタンスと関連リソースの整理と追跡を行います。 定期的にインベントリを調整し、承認されていないリソースがサブスクリプションから適切なタイミングで削除されるようにします。

- [追加の Azure サブスクリプションを作成する方法](../cost-management-billing/manage/create-subscription.md)

- [管理グループを作成する方法](../governance/management-groups/create-management-group-portal.md)

- [タグを作成して使用する方法](../azure-resource-manager/management/tag-resources.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4:承認された Azure リソースのインベントリを定義および管理する

**ガイダンス**:次の組み込みのポリシー定義を使用して、顧客のサブスクリプション内に作成できるリソースの種類に制限を適用するには、Azure Policy を使用します。

- 許可されないリソースの種類

- 許可されるリソースの種類

また、Azure Resource Graph を使用すると、サブスクリプション内のリソースのクエリまたは検出を行えます。

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

- [Azure Graph を使用してクエリを作成する方法](../governance/resource-graph/first-query-portal.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5:承認されていない Azure リソースを監視する

**ガイダンス**:次の組み込みのポリシー定義を使用して、顧客のサブスクリプション内に作成できるリソースの種類に制限を適用するには、Azure Policy を使用します。

- 許可されないリソースの種類
- 許可されるリソースの種類

また、Azure Resource Graph を使用すると、サブスクリプション内のリソースのクエリまたは検出を行えます。

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

- [Azure Graph を使用してクエリを作成する方法](../governance/resource-graph/first-query-portal.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="69-use-only-approved-azure-services"></a>6.9:承認された Azure サービスのみを使用する

**ガイダンス**:次の組み込みのポリシー定義を使用して、顧客のサブスクリプション内に作成できるリソースの種類に制限を適用するには、Azure Policy を使用します。
- 許可されないリソースの種類
- 許可されるリソースの種類

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy を使用して特定のリソースの種類を拒否する方法](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11:Azure Resource Manager を操作するユーザーの機能を制限する

**ガイダンス**: Azure Conditional Access を使用して Azure Resource Manager を操作するユーザーの権限を制限するには、"Microsoft Azure 管理" アプリに対して [アクセスのブロック] を構成します。 これにより、機密情報を含む Hyperscale (Citus) のインスタンスなど、高セキュリティ環境内でのリソースの作成と変更を防ぐことができます。

- [Azure Resource Manager へのアクセスをブロックするように条件付きアクセスを構成する方法](../role-based-access-control/conditional-access-azure-management.md)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="secure-configuration"></a>セキュリティで保護された構成

*詳細については、[Azure セキュリティ ベンチマークの「セキュリティで保護された構成](../security/benchmarks/security-control-secure-configuration.md)」を参照してください。*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1:すべての Azure リソースに対してセキュリティで保護された構成を確立する

**ガイダンス**: Azure Policy を使用して、Hyperscale (Citus) インスタンスの標準のセキュリティ構成を定義および実装します。 Azure Database for PostgreSQL インスタンスのネットワーク構成を監査または適用するためのカスタム ポリシーを作成するには、Azure Policy を使用します。

また、Azure Resource Manager には、テンプレートを JavaScript Object Notation (JSON) でエクスポートする機能があり、構成が確実に組織のセキュリティ要件を満たすかそれを超えるように確認する必要があります。

- [使用可能な Azure Policy エイリアスを表示する方法](/powershell/module/az.resources/get-azpolicyalias)

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

- [Azure portal のテンプレートへの単一および複数リソースのエクスポート](../azure-resource-manager/templates/export-template-portal.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3:セキュリティで保護された Azure リソースの構成を維持する

**ガイダンス**: Azure リソース全体にセキュリティで保護された設定を適用するには、Azure ポリシー [拒否] と [存在する場合はデプロイする] を使用します。  さらに、Azure Resource Manager テンプレートを使用して、組織に必要な Azure リソースのセキュリティ構成を維持できます。 

- [Azure Policy の効果について](../governance/policy/concepts/effects.md)

- [コンプライアンスを強制するポリシーの作成と管理](../governance/policy/tutorials/create-and-manage.md)

- [Azure Resource Manager テンプレートの概要](../azure-resource-manager/templates/overview.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5:Azure リソースの構成を安全に格納する

**ガイダンス**: Hyperscale (Citus) インスタンスと関連リソースにカスタム Azure Policy 定義を使用する場合は、Azure Repos を使ってコードを安全に格納および管理します。

- [Azure DevOps でコードを格納する方法](/azure/devops/repos/git/gitworkflow)

- [Azure Repos のドキュメント](/azure/devops/repos/)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7:Azure リソース用の構成管理ツールをデプロイする

**ガイダンス**:Azure リソース全体にセキュリティで保護された設定を適用するには、Azure ポリシー [拒否] と [存在する場合はデプロイする] を使用します。  さらに、Azure Resource Manager テンプレートを使用して、組織に必要な Azure リソースのセキュリティ構成を維持できます。 

- [Azure Policy の効果について](../governance/policy/concepts/effects.md)

- [コンプライアンスを強制するポリシーの作成と管理](../governance/policy/tutorials/create-and-manage.md)

- [Azure Resource Manager テンプレートの概要](../azure-resource-manager/templates/overview.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9:Azure リソースの自動構成監視を実装する

**ガイダンス**: システム構成のアラート生成、監査、および適用のためのカスタム ポリシーを作成するには、"Microsoft.DBforPostgreSQL" 名前空間で Azure Policy エイリアスを使用します。 Azure ポリシーの [audit]、[deny]、[deploy if not exist] を使用して、Azure Database for PostgreSQL インスタンスおよび関連リソースの構成を自動的に適用します。

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="712-manage-identities-securely-and-automatically"></a>7.12:ID を安全かつ自動的に管理する

**ガイダンス**: 現在、Azure Database for PostgreSQL - Hyperscale (Citus) では、マネージド ID は直接はサポートされていません。 Azure Database for PostgreSQL サーバーを作成するときに、管理者ユーザーの資格情報を指定する必要があります。 Azure portal インターフェイスで追加のユーザー ロールを作成できます。

- [Azure Database for PostgreSQL - Hyperscale (Citus) を作成する](https://docs.microsoft.com/azure/postgresql/quickstart-create-hyperscale-portal#create-a-hyperscale-citus-server-group)

- [追加のユーザー ロールを作成する](https://docs.microsoft.com/azure/postgresql/howto-hyperscale-create-users#how-to-create-additional-user-roles)

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

**ガイダンス**: Microsoft のマルウェア対策は、Azure サービス (Hyperscale (Citus) など) をサポートしている基になるホストで有効になっていますが、顧客のコンテンツに対しては実行されません。

App Service、Data Lake Storage、Blob Storage、Azure Database for PostgreSQL などの非コンピューティング Azure リソースにアップロードされるコンテンツはすべて、事前にスキャンしてください。Microsoft は、これらのインスタンス内のデータにアクセスできません。

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="data-recovery"></a>データの復旧

*詳細については、[Azure セキュリティ ベンチマークの「データの復旧](../security/benchmarks/security-control-data-recovery.md)」を参照してください。*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1:定期的に自動バックアップを行う

**ガイダンス**: Azure Database for PostgreSQL – Hyperscale (Citus) では、各ノードのバックアップを自動的に作成し、ローカル冗長ストレージに格納します。 バックアップを使用して、指定した時間に Hyperscale (Citus) クラスターを復元することができます。

- [Azure Database for PostgreSQL - Hyperscale (Citus) でのバックアップおよび復元の方法](concepts-hyperscale-backup.md)

**責任**: Customer

**Azure Security Center の監視**: [Azure セキュリティ ベンチマーク](/azure/governance/policy/samples/azure-security-benchmark)は、Security Center の既定のポリシー イニシアチブであり、[Security Center の推奨事項](/azure/security-center/security-center-recommendations)の基礎となります。 このコントロールに関連する Azure Policy 定義は、Security Center によって自動的に有効になります。 この制御に関連するアラートでは、関連するサービスのために [Azure Defender](/azure/security-center/azure-defender) プランが必要になる場合があります。

**Azure Policy 組み込み定義 - Microsoft.DBforPostgreSQL**:

[!INCLUDE [Resource Policy for Microsoft.DBforPostgreSQL 9.1](../../includes/policy/standards/asb/rp-controls/microsoft.dbforpostgresql-9-1.md)]

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: システムの完全バックアップを実行し、すべてのカスタマー マネージド キーをバックアップする

**ガイダンス**: 少なくとも 1 日に 1 回、Azure Database for PostgreSQL では、データ ファイルとデータベース トランザクション ログのスナップショット バックアップが作成されます。 バックアップを使用すると、サーバーを、保持期間内の任意の時点に復元できます 保持期間は現在、すべてのクラスターで 35 日です。 すべてのバックアップが、AES 256 ビット暗号化を使用して暗号化されます。

可用性ゾーンをサポートする Azure リージョンでは、バックアップ スナップショットは 3 つの可用性ゾーンに格納されます。 少なくとも 1 つの可用性ゾーンがオンラインになっている限り、Hyperscale (Citus) クラスターは復元可能です。

- [Azure Database for PostgreSQL - Hyperscale (Citus) でのバックアップおよび復元の方法](concepts-hyperscale-backup.md)

**責任**: Customer

**Azure Security Center の監視**: [Azure セキュリティ ベンチマーク](/azure/governance/policy/samples/azure-security-benchmark)は、Security Center の既定のポリシー イニシアチブであり、[Security Center の推奨事項](/azure/security-center/security-center-recommendations)の基礎となります。 このコントロールに関連する Azure Policy 定義は、Security Center によって自動的に有効になります。 この制御に関連するアラートでは、関連するサービスのために [Azure Defender](/azure/security-center/azure-defender) プランが必要になる場合があります。

**Azure Policy 組み込み定義 - Microsoft.DBforPostgreSQL**:

[!INCLUDE [Resource Policy for Microsoft.DBforPostgreSQL 9.2](../../includes/policy/standards/asb/rp-controls/microsoft.dbforpostgresql-9-2.md)]

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3:カスタマー マネージド キーを含むすべてのバックアップを検証する

**ガイダンス**:Azure Database for PostgreSQL では、Hyperscale (Citus) クラスターを復元すると、元のノードのバックアップから新しいクラスターが作成されます。 過去 35 日以内の任意の時点にクラスターを復元することができます。 復元プロセスでは、元のものと同じ Azure リージョン、サブスクリプション、およびリソース グループに新しいクラスターが作成されます。 新しいクラスター構成は、元のクラスター構成と同じです。ノード数、仮想コア数、ストレージ サイズ、およびユーザー ロールが同じです。

ファイアウォール設定と PostgreSQL サーバーのパラメーターは元のサーバー グループから保持されず、既定値にリセットされます。 ファイアウォールによって、すべての接続が阻止されます。 復元後にこれらの設定を手動で調整する必要があります。

- [Azure Database for PostgreSQL - Hyperscale (Citus) でのバックアップおよび復元の方法](concepts-hyperscale-backup.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: バックアップとカスタマー マネージド キーの保護を保証する

**ガイダンス**: 削除された Hyperscale (Citus) クラスターを復元することはできません。 クラスターを削除すると、そのクラスターに属するすべてのノードが削除され、復旧できなくなります。 管理者は、デプロイ後のクラスターのリソースを誤削除や予期せぬ変更から保護するために、管理ロックを利用できます。

- [Azure Database for PostgreSQL - Hyperscale (Citus) でのバックアップおよび復元の方法](concepts-hyperscale-backup.md)

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

**ガイダンス**: Security Center によって各アラートに重大度が割り当てられるため、最初に調査する必要があるアラートの優先順位付けに役立ちます。 重要度は、アラートの発行に使用された調査結果またはメトリックを Security Center がどの程度信頼しているかということと、アラートの原因となったアクティビティの背後に悪意のある意図があったことについての確信の度合いに基づくものです。 

また、サブスクリプション ( 稼働、非稼働など) を明確にマークし、Azure リソースを明確に識別および分類するための命名システムを作成します。

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="103-test-security-response-procedures"></a>10.3:セキュリティ対応手順のテスト

**ガイダンス**:定期的にシステムのインシデント対応機能をテストする演習を実施します。 弱点やギャップを特定し、必要に応じて計画を見直します。 

- [NIST の出版物「IT 計画と機能に関するテスト、トレーニング、演習プログラムのガイド」を参照してください。](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4:セキュリティ インシデントの連絡先の詳細を指定し、セキュリティ インシデントのアラート通知を構成します

**ガイダンス**:セキュリティ インシデントの連絡先情報は、Microsoft Security Response Center (MSRC) で、不正なユーザーまたは権限のないユーザーによるお客様のデータへのアクセスが検出された場合に、Microsoft からの連絡先として使用されます。  事後にインシデントをレビューして、問題が解決されていることを確認します。 

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

**ガイダンス**: お客様の侵入テストが Microsoft のポリシーに違反しないように、確実に次の Microsoft の活動規則に従ってください。 https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1 

- [Microsoft が管理しているクラウド インフラストラクチャ、サービス、アプリケーションに対する Red Teaming およびライブ サイト侵入テストに関する Microsoft の戦略と実施の詳細については、こちらを参照してください。](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**責任**: 共有

**Azure Security Center の監視**: なし

## <a name="next-steps"></a>次のステップ

- 「[Azure セキュリティ ベンチマーク V2 の概要](/azure/security/benchmarks/overview)」を参照してください。
- [Azure セキュリティ ベースライン](/azure/security/benchmarks/security-baselines-overview)の詳細について学習する
