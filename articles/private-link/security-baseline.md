---
title: Azure Private Link の Azure セキュリティ ベースライン
description: Azure Private Link セキュリティ ベースラインでは、Azure セキュリティ ベンチマークで指定されているセキュリティに関する推奨事項を実装するための手順のガイダンスとリソースが提供されます。
author: msmbaldwin
ms.service: private-link
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 7dacec1521787e55ee4c7fd5f08b9e56d9dd4842
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101736000"
---
# <a name="azure-security-baseline-for-azure-private-link"></a>Azure Private Link の Azure セキュリティ ベースライン

このセキュリティ ベースラインは、[Azure セキュリティ ベンチマーク](../security/benchmarks/overview.md)のガイダンスを Azure Private Link に適用します。 Azure セキュリティ ベンチマークには、Azure 上のクラウド ソリューションをセキュリティで保護する方法に関する推奨事項がまとめてあります。 内容は、**セキュリティ制御** によってグループ化されています。これは、Azure セキュリティ ベンチマークと、Azure Private Link に適用できる関連ガイダンスによって定義されています。 Azure Private Link に適用できない **制御** は、除外されています。 Azure Private Link を完全に Azure セキュリティ ベンチマークにマップする方法については、[完全な Azure Virtual Network セキュリティ ベースライン マッピング ファイル](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)を参照してください。

## <a name="network-security"></a>ネットワークのセキュリティ

*詳細については、[Azure セキュリティ ベンチマークの「ネットワークのセキュリティ](../security/benchmarks/security-control-network-security.md)」を参照してください。*

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11:自動化ツールを使用してネットワーク リソース構成を監視し、変更を検出する

**ガイダンス**: Azure アクティビティ ログを使用して、リソース構成を監視し、Private Link に関連するネットワーク リソースの変更を検出します。 

重要なリソースへの変更が発生するとトリガーされる Azure Monitor 内のアラートを作成します。

- [Azure アクティビティ ログ イベントを表示して取得する方法](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Azure Monitor でアラートを作成する方法](../azure-monitor/alerts/alerts-activity-log.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

## <a name="logging-and-monitoring"></a>ログ記録と監視

*詳細については、[Azure セキュリティ ベンチマークの「ログ記録と監視](../security/benchmarks/security-control-logging-monitoring.md)」を参照してください。*

### <a name="22-configure-central-security-log-management"></a>2.2:セキュリティ ログの一元管理を構成する

**ガイダンス**: Azure Monitor 経由でログを取り込み、Private Link エンドポイント、仮想ネットワーク、ネットワーク セキュリティ グループなどのネットワーク リソースによって生成されたセキュリティ データを集計します。 

Azure Monitor 内で Log Analytics ワークスペースを使用してクエリを発行し、分析を実行して、長期/アーカイブ ストレージには Azure Storage アカウントを使用します。

また、組織のビジネス要件に基づいて、Azure Sentinel またはサード パーティの SIEM に対してデータを有効にしてオンボードします。

- [Azure Sentinel をオンボードする方法](../sentinel/quickstart-onboard.md)

- [Azure Monitor でプラットフォーム ログとメトリックを収集する方法](../azure-monitor/essentials/diagnostic-settings.md)

- [Private Link のログ記録と監視](private-link-overview.md#logging-and-monitoring)

- [ネットワーク セキュリティ グループの診断ログ](../virtual-network/virtual-network-nsg-manage-log.md)

- [Azure Monitor とサードパーティの SIEM 統合を開始する方法](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3:Azure リソースの監査ログ記録を有効にする

**ガイダンス**: Azure Monitor アクティビティ ログを有効にします。これにより、操作を開始したユーザー、操作の発生日時、操作の状態、その他の有用な監査情報など、Private Link リソースに対して行われた操作がログに記録されます。 

- [Azure Monitor でプラットフォーム ログとメトリックを収集する方法](../azure-monitor/essentials/diagnostic-settings.md)

- [Azure アクティビティ ログ イベントを表示および取得する](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Private Link のログ記録と監視](private-link-overview.md#logging-and-monitoring)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="25-configure-security-log-storage-retention"></a>2.5:セキュリティ ログのストレージ保持を構成する

**ガイダンス**: Private Link に関連するログでは、Azure Monitor 内の組織のコンプライアンス規則に従って Log Analytics ワークスペースの保持期間を設定します。 ログの長期またはアーカイブ ストレージには Azure Storage アカウントを使用します。

- [Log Analytics でデータ保持期間を変更する](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

- [Azure Storage アカウント ログの保持ポリシーを構成する方法](../storage/common/manage-storage-analytics-logs.md#configure-logging)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="26-monitor-and-review-logs"></a>2.6:ログを監視して確認する

**ガイダンス**: 異常な動作がないかログの分析と監視を行って、定期的に結果を確認します。 Azure Monitor の Log Analytics ワークスペースを使用してログを確認し、ログ データに対してクエリを実行します。

もう 1 つのオプションは、Azure Sentinel またはサード パーティの SIEM に対してデータを有効にしてオンボードすることです。

- [Azure Sentinel をオンボードする方法](../sentinel/quickstart-onboard.md)

- [Log Analytics ワークスペースについて](../azure-monitor/logs/log-analytics-tutorial.md)

- [Azure Monitor でカスタム クエリを実行する方法](../azure-monitor/logs/get-started-queries.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7:異常なアクティビティについてのアラートを有効にする

**ガイダンス**: セキュリティ ログやイベントで検出される異常なアクティビティを監視し、アラートを送信するために、Log Analytics ワークスペースと共に構成された Security Center を使用します。

組織のビジネス要件に基づいて、Azure Sentinel またはサード パーティの SIEM に対してデータを有効にしてオンボードします。

- [Azure Sentinel をオンボードする方法](../sentinel/quickstart-onboard.md)

- [Azure Security Center でアラートを管理する方法](../security-center/security-center-managing-and-responding-alerts.md)

- [Log Analytics のログ データに関するアラートを送信する方法](../azure-monitor/alerts/tutorial-response.md)

**Azure Security Center の監視**: はい

**責任**: Customer

## <a name="identity-and-access-control"></a>ID とアクセスの制御

*詳細については、[Azure セキュリティ ベンチマークの「ID およびアクセス制御](../security/benchmarks/security-control-identity-access-control.md)」を参照してください。*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: 管理アカウントのインベントリを維持する

**ガイダンス**: 明示的な割り当てとクエリの実行が可能である Azure Active Directory (Azure AD) 組み込みの管理者ロールを使用します。 Azure AD PowerShell モジュールを実行してアドホック クエリを実行し、管理グループのメンバーであるアカウントを検出します。

- [PowerShell を使用して Azure AD でディレクトリ ロールを取得する方法](/powershell/module/azuread/get-azureaddirectoryrole)

- [PowerShell を使用して Azure AD でディレクトリ ロールのメンバーを取得する方法](/powershell/module/azuread/get-azureaddirectoryrolemember)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: 専用管理者アカウントを使用する

**ガイダンス**: 専用管理者アカウントの使用に関する標準的な操作手順を作成します。 Security Center の ID とアクセス管理の機能を使用して、管理者アカウントの数を監視します。

また、Microsoft サービスの Azure Active Directory (Azure AD) Privileged Identity Management の特権ロール、および Azure Resource Manager を使用して、Just-In-Time/Just-Enough-Access を有効にします。

- [Privileged Identity Management について](../active-directory/privileged-identity-management/index.yml)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Azure Active Directory シングル サインオン (SSO) を使用する

**ガイダンス**: 可能な限り、サービスごとに個別のスタンドアロン資格情報を構成するのではなく、Azure Active Directory でシングル サインオンを使用します。

- [Azure Active Directory でのアプリケーションへのシングル サインオン](../active-directory/manage-apps/what-is-single-sign-on.md)

- [Azure Security Center で ID とアクセスを監視する方法](../security-center/security-center-identity-access.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: すべての Azure Active Directory ベースのアクセスに多要素認証を使用する

**ガイダンス**: Azure Active Directory (Azure AD) 多要素認証 (MFA) を有効にし、Security Center ID とアクセス管理の推奨事項に従います。

- [Azure で MFA を有効にする方法](../active-directory/authentication/howto-mfa-getstarted.md)

- [Azure Security Center で ID とアクセスを監視する方法](../security-center/security-center-identity-access.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3.6: セキュリティで保護された Azure マネージド ワークステーションを管理タスクに使用する

**ガイダンス**: 多要素認証が構成された特権アクセス ワークステーション (PAW) を使用してログインし、Azure ネットワーク リソースを構成します。

- [特権アクセス ワークステーションについて](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Azure で MFA を有効にする方法](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: 管理者アカウントからの疑わしいアクティビティに関するログとアラート

**ガイダンス**:Azure Active Directory (Azure AD) のリスク検出機能を使用して、危険なユーザーの行動に関するアラートとレポートを表示します。 Security Center のリスク検出アラートを Azure Monitor に取り込み、アクション グループを使用してカスタムのアラートまたは通知を構成します。

- [Azure Security Center のリスク検出 (疑わしいアクティビティ) について](../active-directory/identity-protection/overview-identity-protection.md)

- [Azure アクティビティ ログを Azure Monitor に統合する方法](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [カスタムのアラートおよび通知用にアクション グループを構成する方法](../azure-monitor/alerts/action-groups.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8:承認された場所からのみ Azure リソースを管理する

**ガイダンス**: 条件付きアクセスのネームド ロケーションを使用して、IP アドレス範囲または国、地域の特定の論理グループからのアクセスのみを許可します。

- [Azure でネームド ロケーションを構成する方法](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="39-use-azure-active-directory"></a>3.9: Azure Active Directory を使用する

**ガイダンス**: Azure Active Directory (Azure AD) を中央認証と承認システムとして使用します。 Azure AD では、データの保存時と転送時に強力な暗号化を使用してデータが保護され、さらに、ユーザーの資格情報がソルト化およびハッシュされ、安全に保存されます。  

- [Azure AD インスタンスを作成して構成する方法](../active-directory-domain-services/tutorial-create-instance.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: ユーザー アクセスを定期的に確認して調整する

**ガイダンス**: Azure Active Directory では、古いアカウントの検出に役立つログが提供されます。 また、Azure ID アクセス レビューを使用して、グループ メンバーシップ、エンタープライズ アプリケーションへのアクセス、ロールの割り当てを効率的に管理します。 ユーザー アクセスを定期的にレビューし、適切なユーザーのみが継続的なアクセス権を持っていることを確認できます。

- [Azure AD のレポートの概要](../active-directory/reports-monitoring/index.yml)

- [Azure ID アクセス レビューの使用方法](../active-directory/governance/access-reviews-overview.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: 非アクティブ化された資格情報へのアクセスの試行を監視する

**ガイダンス**:Azure Active Directory (Azure AD) サインイン アクティビティ、監査、リスク イベント ログのソースを使用して、任意の SIEM または監視ツールと統合します。

このプロセスを効率化するには、Azure AD ユーザー アカウントの診断設定を作成し、監査ログとサインイン ログを Log Analytics ワークスペースに送信します。 Log Analytics ワークスペース内に目的のアラートを構成します。

- [Azure アクティビティ ログを Azure Monitor に統合する方法](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: アカウントのサインイン動作の偏差に関するアラートを生成する

**ガイダンス**: Azure Active Directory (Azure AD) のリスクおよび Identity Protection 機能を使用して、ネットワーク リソースのユーザー ID に関連して検出された疑わしいアクションに対する自動応答を構成します。 

さらに詳しく調査するために、Azure Sentinel にデータを取り込みます。

- [Azure AD の危険なサインインを表示する方法](../active-directory/identity-protection/overview-identity-protection.md)

- [Identity Protection のリスク ポリシーを構成して有効にする方法](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Azure Sentinel をオンボードする方法](../sentinel/quickstart-onboard.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

## <a name="data-protection"></a>データ保護

*詳細については、[Azure セキュリティ ベンチマークの「データ保護](../security/benchmarks/security-control-data-protection.md)」を参照してください。*

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2:機密情報を格納または処理するシステムを分離する

**ガイダンス**: 環境の種類やデータの機密度レベルなど、個々のセキュリティ ドメイン用の個別のサブスクリプションと管理グループを使用して分離を実装します。 

ビジネス要件に基づいて、アプリケーションやエンタープライズ環境で Azure リソースへのアクセス レベルを制限します。 

Azure ロールベースのアクセス制御 (Azure RBAC) を使用して、Azure リソースへのアクセスを制御します。

- [追加の Azure サブスクリプションを作成する方法](../cost-management-billing/manage/create-subscription.md)

- [管理グループを作成する方法](../governance/management-groups/create-management-group-portal.md)

- [タグを作成して使用する方法](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4:転送中のすべての機密情報を暗号化する

**ガイダンス**:転送中のすべての機密情報を暗号化します。 仮想ネットワーク内の Azure リソースに接続するクライアントが TLS 1.2 以上をネゴシエートできるようにします。 Private Link は、基になるプロトコルに干渉しません。 お客様が使用する他のオファリングに対してベスト プラクティスを使用します。

該当する場合、保存時と転送時の暗号化に関する Security Center の推奨事項に従います。

- [Azure での転送中の暗号化の概要](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

**Azure Security Center の監視**: はい

**責任**: 共有

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6:Azure RBAC を使用してリソースへのアクセスを制御する

**ガイダンス**: Azure ロールベースのアクセス制御 (Azure RBAC) を使用してデータとリソースへのアクセスを制御します。それ以外の場合は、サービス固有のアクセス制御方法を使用します。

- [ここで、Azure の組み込みロールの簡単な説明と一意の ID について確認してください](../role-based-access-control/built-in-roles.md)

PowerShell コマンド "Get-AzRoleDefinition" または "az role definition list" を呼び出して、環境内のロールの一覧を取得します。

Private Link の "可視性" 設定を使用してサービスの公開を制御するオプションを確認します (Private Link)。 これらの可視性の設定により、コンシューマーがサービスに接続できるかどうかが決まります。 

他の仮想ネットワークからの使用に応じてサービスをプライベートにします (Azure RBAC のアクセス許可のみ)。 限られた一連の信頼できるサブスクリプションに公開を制限するか、またはパブリックにして、すべての Azure サブスクリプションが Private Link サービスで接続を要求できるようにします。

- [Azure RBAC を構成する方法](../role-based-access-control/role-assignments-portal.md)

- [Private Link サービスのプロパティ](private-link-service-overview.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9:重要な Azure リソースへの変更に関するログとアラート

**ガイダンス**: Azure Monitor アクティビティ ログのアラートを使用して、Private Link サービスやエンドポイントなど、重要な Azure リソースに変更が加えられたときにアラートが作成されるようにします。 

- [ネットワーク セキュリティ グループの診断ログ](private-link-overview.md#logging-and-monitoring)

- [Azure アクティビティ ログ イベントのアラートを作成する方法](../azure-monitor/alerts/alerts-activity-log.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

## <a name="inventory-and-asset-management"></a>インベントリと資産の管理

*詳細については、[Azure セキュリティ ベンチマークの「インベントリと資産の管理](../security/benchmarks/security-control-inventory-asset-management.md)」を参照してください。*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1:自動化された資産検出ソリューションを使用する

**ガイダンス**: Azure Resource Graph を使用して、Private Link サービスやサブスクリプション内のエンドポイントなど、すべてのネットワーク リソースをクエリして検出します。 

テナント内の適切な (読み取り) アクセス許可を持っており、サブスクリプション内のリソースだけでなく、すべての Azure サブスクリプションを列挙できることを確認します。

- [Azure Graph を使用してクエリを作成する方法](../governance/resource-graph/first-query-portal.md)

- [Azure サブスクリプションを表示する方法](/powershell/module/az.accounts/get-azsubscription)

- [Azure RBAC について](../role-based-access-control/overview.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="62-maintain-asset-metadata"></a>6.2:資産メタデータを保持する

**ガイダンス**: メタデータを使用して Azure リソースにタグを適用し、それらを各分類に論理的に整理します。

- [タグを作成して使用する方法](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="63-delete-unauthorized-azure-resources"></a>6.3:承認されていない Azure リソースを削除する

**ガイダンス**: 必要に応じて、タグ付け、管理グループ、個別のサブスクリプションを使用して、Private Link や関連リソースを整理し追跡します。 

定期的にインベントリを調整し、承認されていないリソースがサブスクリプションから適切なタイミングで削除されるようにします。

- [追加の Azure サブスクリプションを作成する方法](../cost-management-billing/manage/create-subscription.md)

- [管理グループを作成する方法](../governance/management-groups/create-management-group-portal.md)

- [タグを作成して使用する方法](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4:承認された Azure リソースのインベントリを定義および管理する

**ガイダンス**: 組織のニーズに基づき、承認された Azure リソースとコンピューティング リソース用のソフトウェアのインベントリを作成します。

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5:承認されていない Azure リソースを監視する

**ガイダンス**: 次の組み込みのポリシー定義を使用して、顧客のサブスクリプション内に作成できるリソースの種類に制限を適用するには、Azure Policy を使用します。

- 許可されないリソースの種類

- 許可されるリソースの種類

また、Azure Resource Graph を使用して、サブスクリプション内のリソースをクエリまたは検出します。 これは、高いセキュリティに基づいた環境 (ストレージ アカウントを使用するものなど) に役立ちます。

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

- [Azure Graph を使用してクエリを作成する方法](../governance/resource-graph/first-query-portal.md)

- [プライベート リンク用の Azure Policy サンプルの組み込み](../governance/policy/samples/built-in-policies.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="67-remove-unapproved-azure-resources"></a>6.7:承認されていない Azure リソースを削除する

**ガイダンス**: お客様は、お客様の会社のポリシーで求められているように、Azure Policy を使用してリソースの作成や使用を防ぐことができます。 承認されていないリソースを削除するための独自のプロセスを実装できます。

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="69-use-only-approved-azure-services"></a>6.9:承認された Azure サービスのみを使用する

**ガイダンス**: 次の組み込みのポリシー定義を使用して、顧客のサブスクリプション内に作成できるリソースの種類に制限を適用するには、Azure Policy を使用します。

- 許可されないリソースの種類

- 許可されるリソースの種類

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy を使用して特定のリソースの種類を拒否する方法](../governance/policy/samples/index.md)

- [プライベート リンク用の Azure Policy サンプルの組み込み](../governance/policy/samples/built-in-policies.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11:Azure Resource Manager を操作するユーザーの機能を制限する

**ガイダンス**:"Microsoft Azure Management" アプリに対して [アクセスのブロック] を構成することによって、Azure Resource Manager を操作するユーザーの機能を制限するには、Azure 条件付きアクセスを使用します。

- [Azure Resource Manager へのアクセスをブロックするように条件付きアクセスを構成する方法](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

## <a name="secure-configuration"></a>セキュリティで保護された構成

*詳細については、[Azure セキュリティ ベンチマークの「セキュリティで保護された構成](../security/benchmarks/security-control-secure-configuration.md)」を参照してください。*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1:すべての Azure リソースに対してセキュリティで保護された構成を確立する

**ガイダンス**: Azure Policy エイリアスを使用してカスタム ポリシーを作成し、組み込みの Azure Policy 定義と共に Azure ネットワーク リソースの構成を監査または適用します。

Azure Resource Manager には JavaScript Object Notation (JSON) でテンプレートをエクスポートする機能があります。 この成果物をレビューして、構成が組織のセキュリティ要件を満たしているか、またはそれ以上であることを確認する必要があります。

Azure リソースの安全な構成基準として Security Center からの推奨事項を実装します。

- [使用可能な Azure Policy エイリアスを表示する方法](/powershell/module/az.resources/get-azpolicyalias)

- [チュートリアル:コンプライアンスを強制するポリシーの作成と管理](../governance/policy/tutorials/create-and-manage.md)

- [プライベート リンク用の Azure Policy サンプルの組み込み](../governance/policy/samples/built-in-policies.md)

- [Azure portal のテンプレートへの単一および複数リソースのエクスポート](../azure-resource-manager/templates/export-template-portal.md)

- [セキュリティの推奨事項 - リファレンス ガイド](../security-center/recommendations-reference.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3:セキュリティで保護された Azure リソースの構成を維持する

**ガイダンス**: Azure Resource Manager テンプレートと Azure Policy を使用して、Private Link に関連付けられている Azure リソースと関連リソースを安全に構成します。  

Azure Resource Manager テンプレートは、Azure リソースのデプロイに使用される JavaScript Object Notation (JSON) ベースのファイルです。カスタム テンプレートは、すべてコード リポジトリ内に安全に保存し、維持する必要があります。 

Azure リソース全体にセキュリティで保護された設定を適用するには、Azure ポリシー [拒否] と [存在する場合はデプロイする] を使用します。

- [Azure Resource Manager テンプレートの作成に関する情報](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy の効果について](../governance/policy/concepts/effects.md)

- [プライベート エンドポイント用の Azure Resource Manager テンプレートのサンプル](/azure/templates/microsoft.network/2019-11-01/privateendpoints)

- [プライベート リンク用の Azure Policy サンプルの組み込み](../governance/policy/samples/built-in-policies.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5:Azure リソースの構成を安全に格納する

**ガイダンス**: カスタム Azure ポリシー、Azure Resource Manager テンプレート、Desired State Configuration スクリプトなどのコードを安全に格納して管理するには、Azure DevOps を使用します。 

Azure Active Directory (Azure AD) (アクセスに関して Azure DevOps と統合されている場合)、または Active Directory (Team Foundation Server と統合されている場合) で定義されている特定のユーザー、組み込みのセキュリティ グループ、またはグループにアクセス許可を付与するか、それらのアクセス許可を拒否します。

- [Azure DevOps でコードを格納する方法](/azure/devops/repos/git/gitworkflow)

- [Azure DevOps でのアクセス許可とグループについて](/azure/devops/organizations/security/about-permissions)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7:Azure リソース用の構成管理ツールをデプロイする

**ガイダンス**:Azure Policy を使用して、Azure リソースの標準的なセキュリティ構成を定義して実装します。 

ご利用の Azure リソースのネットワーク構成を監査または適用するには、Azure Policy エイリアスを使用してカスタム ポリシーを作成します。 

また、サブスクリプションで管理されている特定のリソースに関連する組み込みのポリシー定義も使用します。

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

- [エイリアスを使用する方法](../governance/policy/concepts/definition-structure.md#aliases)

- [プライベート リンク用の Azure Policy サンプルの組み込み](../governance/policy/samples/built-in-policies.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9:Azure リソースの自動構成監視を実装する

**ガイダンス**: Azure Policy を使用して、Azure リソースの構成を監査します。 たとえば、Azure Policy は、プライベート エンドポイントで構成されていないリソースを検出するために使用できます。

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

- [プライベート リンク用の Azure Policy サンプルの組み込み](../governance/policy/samples/built-in-policies.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="data-recovery"></a>データの復旧

*詳しくは、「[Azure Security ベンチマーク:データの復旧](../security/benchmarks/security-control-data-recovery.md)」を参照してください。*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1:定期的な自動バックアップを保証する

**ガイダンス**: Azure Resource Manager を使用して、Private Link サービス、エンドポイント、関連リソースをデプロイします。 Azure Resource Manager には、Private Link エンドポイントと関連リソースを復元するためのバックアップとして使用できるテンプレートをエクスポートする機能があります。 

Azure Automation を使用して、Azure Resource Manager テンプレートのエクスポート API を定期的に呼び出します。

- [Azure Resource Manager の概要](../azure-resource-manager/management/overview.md)

- [プライベート エンドポイント用の Azure Resource Manager テンプレートのサンプル](/azure/templates/microsoft.network/2019-11-01/privateendpoints)

- [Azure portal のテンプレートへの単一および複数リソースのエクスポート](../azure-resource-manager/templates/export-template-portal.md)

- [リソース グループ - テンプレートのエクスポート](/rest/api/resources/resourcegroups/exporttemplate)

- [Azure Automation の概要](../automation/automation-intro.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: システムの完全バックアップを実行し、すべてのカスタマー マネージド キーをバックアップする

**ガイダンス**: Azure Resource Manager を使用して、Private Link サービス、エンドポイント、関連リソースをデプロイします。 Azure Resource Manager には、Private Link エンドポイントと関連リソースを復元するためのバックアップとして使用できるテンプレートをエクスポートする機能があります。  

Azure Automation を使用して、Azure Resource Manager テンプレートのエクスポート API を定期的に呼び出します。  

Azure Key Vault 内のカスタマー マネージド キーをバックアップします。

- [Azure Resource Manager の概要](../azure-resource-manager/management/overview.md)

- [プライベート エンドポイント用の Azure Resource Manager テンプレートのサンプル](/azure/templates/microsoft.network/2019-11-01/privateendpoints)

- [Azure portal のテンプレートへの単一および複数リソースのエクスポート](../azure-resource-manager/templates/export-template-portal.md)

- [リソース グループ - テンプレートのエクスポート](/rest/api/resources/resourcegroups/exporttemplate)

- [Azure Automation の概要](../automation/automation-intro.md)

- [Azure 上でキー コンテナーのキーをバックアップする方法](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3:カスタマー マネージド キーを含むすべてのバックアップを検証する

**ガイダンス**: ビジネス要件に応じて、分離サブスクリプションに対して Azure Resource Manager テンプレートのデプロイを定期的に実行できることを確認します。 

また、バックアップされたカスタマー マネージド キーの復元も確認します。

- [ARM テンプレートと Azure portal でリソースをデプロイする](../azure-resource-manager/templates/deploy-portal.md)

- [Azure でキー コンテナーのキーを復元する方法](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: バックアップとカスタマー マネージド キーの保護を保証する

**ガイダンス**: Azure DevOps を使用して、Azure Resource Manager テンプレートなどのコードを安全に格納し、管理します。 

Azure Active Directory (Azure AD) (リソースへのアクセスに関して Azure DevOps と統合されている場合)、または Active Directory (Team Foundation Server と統合されている場合) で定義されている特定のユーザー、組み込みのセキュリティ グループ、またはグループにアクセス許可を付与するか、それらのアクセス許可を拒否します。

- [Azure DevOps でコードを格納する方法](/azure/devops/repos/git/gitworkflow)

- [Azure DevOps でのアクセス許可とグループについて](/azure/devops/organizations/security/about-permissions)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="incident-response"></a>インシデント対応

*詳細については、[Azure セキュリティ ベンチマークの「インシデント対応](../security/benchmarks/security-control-incident-response.md)」を参照してください。*

### <a name="101-create-an-incident-response-guide"></a>10.1:インシデント対応ガイドを作成する

**ガイダンス**: 組織のインシデント対応ガイドを作成します。 

要員のすべてのロールを定義する文書化されたインシデント対応計画、および検出からインシデント後のレビューまでのインシデント処理または管理のフェーズを確認します。

- [独自のセキュリティ インシデント対応プロセスを構築するためのガイダンス](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft Security Response Center のインシデントの構造](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [お客様は、独自のインシデント対応計画の作成に役立つ NIST の「コンピューター セキュリティ インシデント対応ガイド」を利用することもできます](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2:インシデントのスコアリングと優先順位付けの手順を作成する

**ガイダンス**:Security Center によって各アラートに重大度が割り当てられるため、最初に調査する必要があるアラートの優先順位付けに役立ちます。 重要度は、アラートの発行に使用された Security Center の信頼度と、アラートの原因となったアクティビティの背後に悪意のある意図があったかどうかの信頼レベルに基づいて決まります。

タグを使用してサブスクリプションを明確にマークし (運用、非運用など)、Azure リソース (特に、機密データを処理するもの) を明確に識別して分類するための命名システムを作成します。  

インシデントが発生した Azure リソースと環境の重要度に基づいて、アラートの修復に優先順位を付けることは、お客様の責任です。

- [Security alerts in Azure Security Center](../security-center/security-center-alerts-overview.md)

- [タグを使用した Azure リソースの整理](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="103-test-security-response-procedures"></a>10.3:セキュリティ対応手順のテスト

**ガイダンス**:ご利用のシステムのインシデント対応機能を定期的にテストする演習を実施することで、ご利用の Azure リソースの保護を支援できます。 弱点やギャップを特定し、必要に応じて計画を見直します。

- [NIST の出版物 - IT 計画と機能に関するテスト、トレーニング、および演習プログラムのガイド](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4:セキュリティ インシデントの連絡先の詳細を指定し、セキュリティ インシデントのアラート通知を構成します

**ガイダンス**:セキュリティ インシデントの連絡先情報は、Microsoft Security Response Center (MSRC) でユーザーのデータが違法または権限のないユーザーによってアクセスされたことが検出された場合に、Microsoft からの連絡先として使用されます。 

インシデントを発生後にレビューし、問題が解決されたことを確認します。

- [Azure Security Center のセキュリティ連絡先を設定する方法](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5:インシデント対応システムにセキュリティ アラートを組み込む

**ガイダンス**: 連続エクスポート機能を使用して Security Center のアラートと推奨事項をエクスポートすると、Azure リソースに対するリスクを特定できます。 連続エクスポートを使用すると、アラートと推奨事項を手動で、または継続した連続的な方法でエクスポートできます。 

また、業務ごとに必要に応じて、Security Center データ コネクタを使用してアラートを Azure Sentinel にストリーミングします。

- [連続エクスポートを構成する方法](../security-center/continuous-export.md)

- [Azure Sentinel にアラートをストリーミングする方法](../sentinel/connect-azure-security-center.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="106-automate-the-response-to-security-alerts"></a>10.6:セキュリティ アラートへの対応を自動化する

**ガイダンス**: Security Center のワークフロー自動化機能を使用して、セキュリティのアラートと推奨事項に対して Logic Apps で応答を自動的にトリガーし、Azure リソースを保護します。

- [ワークフローの自動化と Logic Apps を構成する方法](../security-center/workflow-automation.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

## <a name="penetration-tests-and-red-team-exercises"></a>侵入テストとレッド チーム演習

*詳細については、[Azure セキュリティ ベンチマークの「侵入テストとレッド チーム演習](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)」を参照してください。*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1:Azure リソースの通常の侵入テストを実施し、セキュリティに関する重大な調査結果がすべて、確実に修復されるようにする

**ガイダンス**: Microsoft の実施ルールに従い、お客様の侵入テストが Microsoft のポリシーに違反していないことを確認します。 

Microsoft が管理しているクラウド インフラストラクチャ、サービス、アプリケーションに対する Red Teaming およびライブ サイト侵入テストに関する Microsoft の戦略と実施を活用してください。

- [侵入テストの実施ルール](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center の監視**: 適用なし

**責任**: 共有

## <a name="next-steps"></a>次のステップ

- [Azure セキュリティ ベンチマーク](../security/benchmarks/overview.md)に関するページを参照する
- [Azure セキュリティ ベースライン](../security/benchmarks/security-baselines-overview.md)の詳細について学習する