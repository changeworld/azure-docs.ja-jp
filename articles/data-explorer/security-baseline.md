---
title: データ エクスプローラー用の Azure セキュリティ ベースライン
description: データ エクスプローラー用の Azure セキュリティ ベースライン
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 92a607e4c17a16d07f07c97f626ed98a213d509b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "80289934"
---
# <a name="azure-security-baseline-for-data-explorer"></a>データ エクスプローラー用の Azure セキュリティ ベースライン

データ エクスプローラー用の Azure セキュリティ ベースラインには、デプロイのセキュリティ体制を改善するために役立つ推奨事項が含まれています。

このサービス用のベースラインは、ベスト プラクティス ガイダンスを使用して Azure 上のクラウド ソリューションをセキュリティで保護する方法について推奨事項を提供する [Azure セキュリティ ベンチマーク バージョン 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview) に基づいて作成されています。

詳細については、[Azure セキュリティ ベースラインの概要](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)に関するページを参照してください。

## <a name="network-security"></a>ネットワークのセキュリティ

*詳細については、「[セキュリティ コントロール: ネットワークのセキュリティ](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security)」を参照してください。*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1:Virtual Network でネットワーク セキュリティ グループまたは Azure Firewall を使用してリソースを保護する

**ガイダンス**: Azure Data Explorer では、ご利用の仮想ネットワーク内のサブネットへのクラスターのデプロイがサポートされています。 この機能を使用すれば、ご利用の Azure Data Explorer クラスターのトラフィックにネットワーク セキュリティ グループ (NSG) 規則を適用し、ご利用のオンプレミスのネットワークを Azure Data Explorer クラスターのサブネットに接続し、ご利用のデータ接続ソース (Event Hub や Event Grid) をサービス エンドポイントによってセキュリティで保護することができます。

Azure Data Explorer クラスターを仮想ネットワークにデプロイする方法:  https://docs.microsoft.com/azure/data-explorer/vnet-deployment


**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2:Vnet、サブネット、NIC の構成とトラフィックを監視してログに記録する

**ガイダンス**: ネットワーク セキュリティ グループ (NSG) フロー ログを有効にし、トラフィック監査のためにログをストレージ アカウントに送信します。

NSG フロー ログを有効にする方法:  https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Azure Security Center によって提供されるネットワークのセキュリティについて:  https://docs.microsoft.com/azure/security-center/security-center-network-recommendations


**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="13-protect-critical-web-applications"></a>1.3:重要な Web アプリケーションを保護する

**ガイダンス**: 適用できません。推奨事項は、Azure App Service またはコンピューティング リソース上で実行されている Web アプリケーションを対象にしています。

**Azure Security Center の監視**: 適用外

**責任**: 適用なし

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4:既知の悪意のある IP アドレスとの通信を拒否する

**ガイダンス**: DDoS 攻撃から保護するために、ご利用のデータ エクスプローラー クラスターを保護する仮想ネットワーク上で Azure DDoS Protection Standard を有効にします。 Azure Security Center の統合された脅威インテリジェンスを使用して、既知の悪意のある、または未使用のインターネット IP アドレスとの通信を拒否します。

DDoS 保護を構成する方法:  https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Azure Security Center の統合された脅威インテリジェンスについて:  https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer


**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="15-record-network-packets-and-flow-logs"></a>1.5:ネットワーク パケットとフロー ログを記録する

**ガイダンス**: ご利用の Azure Data Explorer クラスターを保護するために使用されるネットワーク セキュリティ グループ (NSG) に関するフロー ログを有効にし、トラフィックの監査のためにストレージ アカウントにログを送信します。

NSG フロー ログを有効にする方法:  https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal


**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems"></a>1.6:ネットワーク ベースの侵入検出/侵入防止システムをデプロイする

**ガイダンス**: 適用できません。この制御はエンドポイントまたはファイアウォールで実行されます。


**Azure Security Center の監視**: 適用外

**責任**: 適用なし

### <a name="17-manage-traffic-to-your-web-applications"></a>1.7:Web アプリケーションへのトラフィックを管理する

**ガイダンス**: 適用できません。この推奨事項は、Azure App Service またはコンピューティング リソース上で実行されている Web アプリケーションを対象にしています。


**Azure Security Center の監視**: 適用外

**責任**: 適用なし

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8:ネットワーク セキュリティ規則の複雑さと管理オーバーヘッドを最小限に抑える

**ガイダンス**: 仮想ネットワーク サービス タグを使用することで、ご利用の Azure Data Explorer クラスターに関連付けられているネットワーク セキュリティ グループまたは Azure Firewall でのネットワーク アクセス制御を定義します。 セキュリティ規則を作成するときは、特定の IP アドレスの代わりにサービス タグを使うことができます。 規則の適切なソースまたは宛先フィールドにサービス タグ名 (ApiManagement など) を指定することにより、対応するサービスのトラフィックを許可または拒否できます。 サービス タグに含まれるアドレス プレフィックスの管理は Microsoft が行い、アドレスが変化するとサービス タグは自動的に更新されます。

サービス タグの概要と使用方法:  https://docs.microsoft.com/azure/virtual-network/service-tags-overview

Azure Data Explorer におけるサービス タグ構成要件:  https://docs.microsoft.com/azure/data-explorer/vnet-deployment#dependencies-for-vnet-deployment


**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9:ネットワーク デバイスの標準的なセキュリティ構成を保持する

**ガイダンス**: 顧客は Azure Policy を使用して、ネットワーク リソース用の標準的なセキュリティ構成を定義し、実装できます。

顧客は、Azure Blueprints を使用して、ARM テンプレート、RBAC コントロール、ポリシーなどの主要な環境成果物を単一のブループリント定義にパッケージ化することで、大規模な Azure デプロイを簡略化することもできます。 ブループリントを新しいサブスクリプションと環境に簡単に適用し、バージョン管理によって制御と管理を微調整します。

Azure Policy を構成して管理する方法:  https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure Blueprint を作成する方法:  https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal


**Azure Security Center の監視**: 適用外

**責任**: Customer

### <a name="110-document-traffic-configuration-rules"></a>1.10:トラフィック構成規則を文書化する

**ガイダンス**: ご利用のデータ エクスプローラー クラスターに対するネットワーク セキュリティおよびトラフィック フローに関連したネットワーク セキュリティ グループ (NSG) やその他のリソースにタグを使用します。 個々の NSG 規則については、[説明] フィールドを使用して、ネットワークとの間のトラフィックを許可する規則のビジネス ニーズや期間 (など) を指定します。

タグを作成して使用する方法:  https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags


**Azure Security Center の監視**: 適用外

**責任**: Customer

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11:自動化ツールを使用してネットワーク リソース構成を監視し、変更を検出する

**ガイダンス**: Azure Policy を使用して、ネットワーク リソースの構成の検証 (または修復、あるいはその両方) を行います。

Azure Policy を構成して管理する方法: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

## <a name="logging-and-monitoring"></a>ログ記録と監視

*詳細については、「[セキュリティ コントロール: ログ記録と監視](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring)」を参照してください。*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1:承認された時刻同期ソースを使用する

**ガイダンス**: Microsoft は Azure リソースのタイム ソースを維持しています。顧客は、顧客が所有するコンピューティング デプロイの時刻同期を更新できます。

Azure コンピューティング リソースの時刻同期を構成する方法:  https://docs.microsoft.com/azure/virtual-machines/windows/time-sync

**Azure Security Center の監視**: 適用外

**責任**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2:セキュリティ ログの一元管理を構成する

**ガイダンス**: Azure Data Explorer は、診断ログを使用してインジェストの成功と失敗に関する分析情報を取得します。 操作ログを Azure Storage、イベント ハブ、または Log Analytics にエクスポートして、インジェスト ステータスを監視することができます。

Azure Data Explorer インジェスト操作を監視する方法:

https://docs.microsoft.com/azure/data-explorer/using-diagnostic-logs

Azure Data Explorer で監視データを取り込んでクエリを実行する方法:

https://docs.microsoft.com/azure/data-explorer/ingest-data-no-code

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3:Azure リソースの監査ログ記録を有効にする

**ガイダンス**: 特定の操作とログ記録を使用するには、アクセスとログ記録に関する Azure Data Explorer の診断設定を有効にします。 Azure Monitor 内の Azure のアクティビティ ログ (リソースに関する概要レベルのログが含まれる) は、既定で有効になっています。

Azure Data Explorer インジェスト操作を監視する方法:  https://docs.microsoft.com/azure/data-explorer/using-diagnostic-logs

Azure Monitor でプラットフォーム ログとメトリックを収集する方法:  https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings

Azure プラットフォーム ログの概要:  https://docs.microsoft.com/azure/azure-monitor/platform/platform-logs-overview


**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="24-collect-security-logs-from-operating-system"></a>2.4:オペレーティング システムからセキュリティ ログを収集する

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用外

**責任**: 適用なし

### <a name="25-configure-security-log-storage-retention"></a>2.5:セキュリティ ログの保有期間を構成する

**ガイダンス**: Azure Monitor 内で、組織のコンプライアンス規則に従って Log Analytics ワークスペースの保持期間を設定します。 長期/アーカイブ ストレージには Azure Storage アカウントを使用します。

Log Analytics ワークスペースのログ保持パラメーターを設定する方法: https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period


**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="26-monitor-and-review-logs"></a>2.6:ログを監視して確認する

**ガイダンス**: 異常な動作がないかどうかログを分析および監視し、結果を定期的に確認します。 Azure Data Explorer 用の診断設定を有効にしたら、Azure Monitor の Log Analytics ワークスペースを使用してログを確認し、ログ データに対してクエリを実行します。

Log Analytics ワークスペースについて:  https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Azure Monitor でカスタム クエリを実行する方法:  https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries


**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7:異常なアクティビティについてのアラートを有効にする

**ガイダンス**: 適用できません。Azure Data Explorer にはこの機能がありません。


**Azure Security Center の監視**: 適用外

**責任**: 適用なし

### <a name="28-centralize-anti-malware-logging"></a>2.8:マルウェア対策のログ記録を一元管理する

**ガイダンス**: 適用できません。Azure Data Explorer では、マルウェア対策ログ記録が処理されません。

**Azure Security Center の監視**: 適用外

**責任**: 適用なし

### <a name="29-enable-dns-query-logging"></a>2.9:DNS クエリのログ記録を有効にする

**ガイダンス**: 適用できません: DNS クエリは、Azure Data Explorer の機能ではありません。


**Azure Security Center の監視**: 適用外

**責任**: 適用なし

### <a name="210-enable-command-line-audit-logging"></a>2.10:コマンド ライン監査のログ記録を有効にする

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用外

**責任**: 適用なし

## <a name="identity-and-access-control"></a>ID およびアクセス制御

*詳細については、「[セキュリティ コントロール: ID およびアクセス制御](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control)」を参照してください。*

### <a name="31-maintain-inventory-of-administrative-accounts"></a>3.1: 管理者アカウントのインベントリを保持する

**ガイダンス**: Azure Data Explorer では、データベースやテーブルなどのセキュリティで保護されたリソースを操作するためのアクセス許可を持つセキュリティ プリンシパル (ユーザーおよびアプリケーション) と、許可される操作が、セキュリティ ロールによって定義されます。  Kusto クエリを活用すれば、Azure Data Explorer クラスターおよびデータベース用の管理者ロールでプリンシパルを一覧表示できます。
Kusto クエリを使用した Azure Data Explorer でのセキュリティ ロールの管理:  https://docs.microsoft.com/azure/kusto/management/security-roles



**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="32-change-default-passwords-where-applicable"></a>3.2: 既定のパスワードを変更する (該当する場合)

**ガイダンス**: Azure AD には既定のパスワードという概念がありません。 パスワードを必要とする他の Azure リソースでは、パスワードが強制的に作成されます。これには複雑な要件と、サービスによって異なるパスワードの最小文字数が適用されます。 既定のパスワードが使用される可能性があるサードパーティ製のアプリケーションとマーケットプレース サービスについては、お客様が責任を負うものとします。


**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="33-ensure-the-use-of-dedicated-administrative-accounts"></a>3.3: 専用管理者アカウントの使用を保証する

**ガイダンス**: 顧客は、専用管理者アカウントの使用に関する標準的な操作手順を作成できます。 Azure Security Center ID とアクセス管理を使用して、管理者アカウントの数を監視します。

顧客はまた、Microsoft サービスの Azure AD Privileged Identity Management の特権ロール、および Azure ARM を使用して、Just-In-Time または Just-Enough-Access を有効にすることもできます。 

Azure AD Privileged Identity Management とは: https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="34-utilize-single-sign-on-sso-with-azure-active-directory"></a>3.4: Azure Active Directory でシングル サインオン (SSO) を利用する

**ガイダンス**: 可能な限り、顧客はサービスごとに個別のスタンドアロン資格情報を構成するのではなく、SSO を Azure Active Directory (Azure AD) と一緒に使用します。 Azure Security Center ID とアクセス管理の推奨事項を使用してください。

Azure AD を使用した SSO について: https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on


**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="35-use-multifactor-authentication-for-all-azure-active-directory-based-access"></a>3.5: すべての Azure Active Directory ベースのアクセスに多要素認証を使用する

**ガイダンス**: Azure Active Directory (Azure AD) 多要素認証 (MFA) を有効にし、Azure Security Center ID とアクセス管理の推奨事項に従います。

Azure で MFA を有効にする方法: https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted 

Azure Security Center 内で ID とアクセスを監視する方法:  https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="36-use-of-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: すべての管理タスクに専用のコンピューター (特権アクセス ワークステーション) の使用

**ガイダンス**: 多要素認証 (MFA) が構成された PAW (特権アクセス ワークステーション) を使用してログインし、Azure リソースを構成します。

特権アクセス ワークステーションについて: https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Azure で MFA を有効にする方法:  https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted


**Azure Security Center の監視**: 適用外

**責任**: Customer

### <a name="37-log-and-alert-on-suspicious-activity-on-administrative-accounts"></a>3.7: 管理者アカウントでの疑わしいアクティビティに関するログとアラート

**ガイダンス**: 環境内で疑わしいアクティビティまたは安全でないアクティビティが発生したときに、Azure Active Directory (Azure AD) セキュリティ レポートを使用して、ログおよびアラートを生成します。 Azure Security Center を使用して ID およびアクセス アクティビティを監視します。

危険なアクティビティのフラグが設定された Azure AD ユーザーを識別する方法:  https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk

Azure Security Center でユーザーの ID およびアクセス アクティビティを監視する方法:  https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="38-manage-azure-resource-from-only-approved-locations"></a>3.8:承認された場所からの Azure リソースのみを管理する

**ガイダンス**: 顧客は、条件付きアクセスのネームド ロケーションを使用して、IP アドレス範囲または国もしくはリージョンの特定の論理グループからのアクセスのみを許可します。

Azure でネームド ロケーションを構成する方法:  https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations


**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="39-utilize-azure-active-directory"></a>3.9: Azure Active Directory を利用する

**ガイダンス**: Azure Active Directory (Azure AD) は、Azure Data Explorer に対する認証方法として推奨されています。 次のようなさまざまな認証シナリオがサポートされています。

ユーザー認証 (対話型ログオン): 人間のプリンシパルを認証するために使用されます。

アプリケーション認証 (非対話型ログオン): 人間のユーザーがいない状態で実行または認証する必要があるサービスおよびアプリケーションを認証するために使用されます。

Azure Data Explorer アクセス制御の概要: https://docs.microsoft.com/azure/kusto/management/access-control

Azure Active Directory での認証:  https://docs.microsoft.com/azure/kusto/management/access-control/aad



**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: ユーザー アクセスを定期的に確認して調整する

**ガイダンス**: Azure Active Directory (Azure AD) では、古いアカウントの検出に役立つログが提供されます。 また、Azure ID アクセス レビューを使用して、グループ メンバーシップ、エンタープライズ アプリケーションへのアクセス、およびロールの割り当てを効率的に管理します。 ユーザーのアクセスを定期的にレビューし、適切なユーザーのみが継続的なアクセス権を持っていることを確認できます。 

Azure AD により Azure Data Explorer アクセスを認証する方法:  https://docs.microsoft.com/azure/kusto/management/access-control/how-to-authenticate-with-aad

Azure AD レポート:  https://docs.microsoft.com/azure/active-directory/reports-monitoring/

Azure ID アクセス レビューの使用方法:  https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview


**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: 非アクティブ化されたアカウントへのアクセスの試行を監視する

**ガイダンス**: Azure Active Directory (Azure AD) サインイン アクティビティ、監査、およびリスク イベント ログのソースを使用して監視を行うことができるため、任意のセキュリティ情報イベント管理 (SIEM) または監視ツールとの統合が可能です。

 このプロセスを効率化するには、Azure Active Directory ユーザー アカウントの診断設定を作成し、監査ログとサインイン ログを Log Analytics ワークスペースに送信します。 顧客は Log Analytics ワークスペースで必要なアラートを構成します。

Azure アクティビティ ログを Azure Monitor に統合する方法:  https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics


**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: アカウント ログイン動作の偏差に関するアラート

**ガイダンス**: ユーザー ID に関連する検出された疑わしいアクションへの自動応答を構成するには、Azure Active Directory (Azure AD) リスク検出および Identity Protection 機能を使用します。 また、さらに詳しく調査するためにデータを Azure Sentinel に取り込むこともできます。

Azure AD の危険なサインインを表示する方法:  https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Identity Protection のリスク ポリシーを構成して有効にする方法:  https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies


**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: サポート シナリオで関連する顧客データに Microsoft がアクセスできるようにする

**ガイダンス**: Microsoft が顧客データにアクセスする必要のあるサポート シナリオでは、カスタマー ロックボックスに、顧客が顧客データへのアクセス要求を確認し、承認または拒否するためのインターフェイスが用意されています。

カスタマー ロックボックスについて:

https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

## <a name="data-protection"></a>データ保護

*詳細については、「[セキュリティ コントロール: データ保護](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection)」を参照してください。*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: 機密情報のインベントリを維持する

**ガイダンス**: 機密情報を格納または処理する Azure リソースを追跡しやすくするには、タグを使用します。

タグを作成して使用する方法:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2:機密情報を格納または処理するシステムを分離する

**ガイダンス**:開発、テスト、および運用で別々のサブスクリプションまたは管理グループ、あるいはその両方を実装します。 Azure Data Explorer クラスターについては、仮想ネットワークまたはサブネットによって他のリソースから分離し、適切にタグ付けを行い、ネットワーク セキュリティ グループ (NSG) または Azure Firewall 内でセキュリティで保護する必要があります。 機密データを格納または処理するデータ エクスプローラー クラスターは、十分に分離する必要があります。

追加の Azure サブスクリプションを作成する方法:  https://docs.microsoft.com/azure/billing/billing-create-subscription

管理グループを作成する方法:  https://docs.microsoft.com/azure/governance/management-groups/create

タグを作成して使用する方法:  https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Azure Data Explorer クラスターを仮想ネットワークにデプロイする方法:  https://docs.microsoft.com/azure/data-explorer/vnet-deployment

セキュリティ構成を使用して NSG を作成する方法:  https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic


**Azure Security Center の監視**: 適用外

**責任**: Customer

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3:機密情報の承認されていない転送を監視してブロックする

**ガイダンス**: 適用できません。Microsoft によって管理される基になるプラットフォームの場合、Microsoft は顧客のすべてのコンテンツを機密として扱い、顧客データを損失や漏洩から保護するためにあらゆる手段を尽くします。 Azure 内の顧客データが確実にセキュリティで保護されるように、Microsoft では一連の堅牢なデータ保護制御および機能を実装して管理しています。

Azure での顧客データの保護について:  https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Azure Security Center の監視**: 適用外

**責任**: 適用なし

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4:転送中のすべての機密情報を暗号化する

**ガイダンス**: Azure Data Explorer クラスターでは、既定で TLS 1.2 がネゴシエートされます。 Azure リソースに接続しているすべてのクライアントが TLS 1.2 以上をネゴシエートできることを確認します。

**Azure Security Center の監視**: 適用外

**責任**: 共有

### <a name="45-divuse-an-active-discovery-tool-to-identify-sensitive-datadiv"></a>4.5: <div>アクティブ検出ツールを使用して機密データを特定する</div>

**ガイダンス**: Azure Data Explorer では、データの識別、分類、損失防止機能はまだ使用できません。 コンプライアンスのために必要な場合は、サードパーティ ソリューションを実装します。

Microsoft によって管理される基になるプラットフォームの場合、Microsoft は顧客のすべてのコンテンツを機密として扱い、顧客データを損失や漏洩から保護するためにあらゆる手段を尽くします。 Azure 内の顧客データが確実にセキュリティで保護されるように、Microsoft では一連の堅牢なデータ保護制御および機能を実装して管理しています。

Azure での顧客データの保護について:  https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Azure Security Center の監視**: 適用外

**責任**: Customer

### <a name="46-divuse-azure-rbac-to-control-access-to-resourcesdiv"></a>4.6: <div>Azure RBAC を使用してリソースへのアクセスを制御する</div>

**ガイダンス**: Azure Data Explorer を使用すると、ロールベースのアクセス制御 (RBAC) モデルを使用して、データベースとテーブルへのアクセスを制御することができます。 このモデルでは、プリンシパル (ユーザー、グループ、およびアプリ) がロールにマッピングされます。 プリンシパルは、割り当てられたロールに従ってリソースにアクセスできます。

Azure Data Explorer の RBAC を構成するためのロールとアクセス許可のリストおよび手順: https://docs.microsoft.com/azure/data-explorer/manage-database-permissions


**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7:ホスト ベースのデータ損失防止を使用してアクセス制御を適用する

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

Microsoft では、Azure Data Explorer 用の基になるインフラストラクチャを管理しており、顧客データの損失や漏洩を防ぐための厳格な管理を実施してきました。

Azure での顧客データの保護について:  https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Azure Security Center の監視**: 適用外

**責任**: 適用なし

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8:機密情報を保存時に暗号化する

**ガイダンス**: Azure Disk Encryption は、データを保護して、組織のセキュリティおよびコンプライアンス コミットメントを満たすのに役立ちます。 クラスター仮想マシンの OS とデータ ディスクのボリューム暗号化が提供されます。 また、Azure Key Vault とも統合されます。これにより、ディスク暗号化キーとシークレットを制御および管理できると共に、Azure Storage で保存中の VM ディスクの全データが確実に暗号化されるようにすることができます。

Azure Data Explorer クラスターで保存時の暗号化を有効にする方法:  https://docs.microsoft.com/azure/data-explorer/manage-cluster-security


**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9:重要な Azure リソースへの変更に関するログとアラート

**ガイダンス**: ご利用の Azure Data Explorer クラスター上でリソース レベルの変更が行われたときにアラートを作成するには、Azure Monitor を Azure アクティビティ ログと一緒に使用します。

Azure アクティビティ ログ イベントのアラートを作成する方法:  https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

Azure アクティビティ ログ イベントのアラートを作成する方法:  https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log


**Azure Security Center の監視**: はい

**責任**: Customer

## <a name="vulnerability-management"></a>脆弱性の管理

*詳細については、「[セキュリティ コントロール: 脆弱性の管理](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management)」を参照してください。*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1:自動化された脆弱性スキャン ツールを実行する

**ガイダンス**: ご利用の Azure Data Explorer リソースをセキュリティで保護する方法については Azure Security Center からの推奨事項に従ってください。

Microsoft では、Azure Data Explorer をサポートしている基になるシステム上で脆弱性の管理も行います。

Azure Security Center の推奨事項について: https://docs.microsoft.com/azure/security-center/recommendations-reference

**Azure Security Center の監視**: はい

**責任**: Microsoft

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2:自動化されたオペレーティング システム修正プログラム管理ソリューションを展開する

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用外

**責任**: 適用なし

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3:自動化されたサードパーティ ソフトウェア修正プログラム管理ソリューションをデプロイする

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用外

**責任**: 適用なし

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4:バックツーバックの脆弱性スキャンを比較する

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用外

**責任**: 適用なし

### <a name="55-utilize-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5:検出された脆弱性の修復に優先順位を付けるには、リスク評価プロセスを使用します。

**ガイダンス**: Azure Security Center によって提供される既定のリスク評価 (セキュリティ スコア) を使用します。
Azure Security Center のセキュリティ スコアについて:  https://docs.microsoft.com/azure/security-center/security-center-secure-score


**Azure Security Center の監視**: はい

**責任**: Customer

## <a name="inventory-and-asset-management"></a>インベントリと資産の管理

*詳細については、「[セキュリティ コントロール: インベントリと資産の管理](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management)」を参照してください。*

### <a name="61-utilize-azure-asset-discovery"></a>6.1:Azure Asset Discovery を活用する

**ガイダンス**: Azure Resource Graph を使用して、サブスクリプション内のすべてのリソース (コンピューティング、ストレージ、ネットワーク、ポート、プロトコルなど) のクエリまたは検出を行います。 テナントで適切な (読み取り) アクセス許可を確認し、サブスクリプション内のリソースだけでなく、すべての Azure サブスクリプションを列挙します。

従来の Azure リソースは Resource Graph で検出できますが、今後は Azure Resource Manager リソースを作成して使用することを強くお勧めします。

Azure Resource Graph を使用してクエリを作成する方法:  https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Azure サブスクリプションを表示する方法:  https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Azure RBAC について:  https://docs.microsoft.com/azure/role-based-access-control/overview



**Azure Security Center の監視**: 適用外

**責任**: Customer

### <a name="62-maintain-asset-metadata"></a>6.2:資産メタデータを保持する

**ガイダンス**: メタデータを提供する Azure リソースにタグを適用すると、それらのリソースが各分類に論理的に整理されます。

タグを作成して使用する方法: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags


**Azure Security Center の監視**: 適用外

**責任**: Customer

### <a name="63-delete-unauthorized-azure-resources"></a>6.3:承認されていない Azure リソースを削除する

**ガイダンス**: 資産の整理と追跡を行うには、適切な名前付け規則、タグ付け、管理グループ、または個別のサブスクリプションを必要に応じて使用できます。 Azure Resource Graph を使用すれば、定期的にインベントリを調整し、承認されていないリソースがサブスクリプションから適切なタイミングで確実に削除されるようにすることができます。 

追加の Azure サブスクリプションを作成する方法:  https://docs.microsoft.com/azure/billing/billing-create-subscription

管理グループを作成する方法: https://docs.microsoft.com/azure/governance/management-groups/create

タグを作成して使用する方法: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Azure Resource Graph を使用してクエリを作成する方法:  https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal



**Azure Security Center の監視**: 適用外

**責任**: Customer

### <a name="64-maintain-inventory-of-approved-azure-resources-and-software-titles"></a>6.4:承認された Azure リソースとソフトウェア タイトルのインベントリを保持する

**ガイダンス**: 組織のニーズに応じて、承認された Azure リソースとコンピューティング リソース用に承認されたソフトウェアのインベントリを作成する必要があります。  


**Azure Security Center の監視**: 適用外

**責任**: Customer

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5:承認されていない Azure リソースを監視する

**ガイダンス**: Azure Policy を使用すると、次の組み込みのポリシー定義によって、顧客のサブスクリプション内に作成できるリソースの種類に制限を設けることができます。

    - 許可されないリソースの種類

    - 許可されるリソースの種類

ポリシーによって生成されたイベントを監視するには、 

Azure Monitor を使用して監視できるアクティビティ ログを使用します。

さらに、Azure Resource Graph を使用すると、サブスクリプション内のリソースのクエリまたは検出を行うことができます。

チュートリアル:コンプライアンスを強制するポリシーの作成と管理: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

クイック スタート:Azure Resource Graph エクスプローラーを使用して初めての Resource Graph クエリを実行する: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Azure Monitor を使用してアクティビティ ログ アラートを作成、表示、管理する:  https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log



**Azure Security Center の監視**: 適用外

**責任**: Customer

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6:コンピューティング リソース内の承認されていないソフトウェア アプリケーションを監視する

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用外

**責任**: 適用なし

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7:承認されていない Azure リソースとソフトウェア アプリケーションを削除する

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースと Azure 全体を対象にしています。

**Azure Security Center の監視**: 適用外

**責任**: 適用なし

### <a name="68-utilize-only-approved-applications"></a>6.8:承認されたアプリケーションのみを使用する

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用外

**責任**: 適用なし

### <a name="69-utilize-only-approved-azure-services"></a>6.9:承認された Azure サービスのみを利用する

**ガイダンス**: Azure Policy を使用すると、次の組み込みのポリシー定義によって、顧客のサブスクリプション内に作成できるリソースの種類に制限を設けることができます。

    - 許可されないリソースの種類

    - 許可されるリソースの種類

チュートリアル:コンプライアンスを強制するポリシーの作成と管理: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure Policy のサンプル: https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types



**Azure Security Center の監視**: 適用外

**責任**: Customer

### <a name="610-implement-approved-application-list"></a>6.10:承認されたアプリケーションの一覧を実装する

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用外

**責任**: 適用なし

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6.11:スクリプトを使用して Azure Resource Manager を操作するユーザーの機能を制限する

**ガイダンス**: Azure Conditional Access を使用して Azure Resource Manager を操作するユーザーの権限を制限するには、"Microsoft Azure 管理" アプリに対して [アクセスのブロック] を構成します。 これにより、ご利用の Azure サブスクリプション内でのリソースの作成と変更ができなくなります。 

条件付きアクセスを使用して Azure 管理へのアクセスを管理する: https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management



**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12:コンピューティング リソース内のスクリプトを実行するユーザーの機能を制限する

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用外

**責任**: 適用なし

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13:リスクの高いアプリケーションを物理的または論理的に分離する

**ガイダンス**: 適用できません。この推奨事項は、Azure App Service またはコンピューティング リソース上で実行されている Web アプリケーションを対象にしています。

**Azure Security Center の監視**: 適用外

**責任**: 適用なし

## <a name="secure-configuration"></a>セキュリティで保護された構成

*詳細については、「[セキュリティ コントロール: セキュリティで保護された構成](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration)」を参照してください。*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1:すべての Azure リソースのセキュリティで保護された構成を確立する

**ガイダンス**: ご利用の Azure リソースの構成を監査または適用するには、Azure Policy エイリアスを使用してカスタム ポリシーを作成します。 組み込みの Azure Policy 定義を使用することもできます。

また、Azure Resource Manager には、テンプレートを JavaScript Object Notation (JSON) でエクスポートする機能があり、構成が確実に組織のセキュリティ要件を満たすかそれを超えるように確認する必要があります。

また、ご利用の Azure リソース用の安全な構成基準として Azure Security Center からの推奨事項を使用することもできます。

使用可能な Azure Policy エイリアスを表示する方法: https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

チュートリアル:コンプライアンスを強制するポリシーの作成と管理: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure portal のテンプレートへの単一および複数リソースのエクスポート: https://docs.microsoft.com/azure/azure-resource-manager/templates/export-template-portal

セキュリティの推奨事項 - リファレンス ガイド: https://docs.microsoft.com/azure/security-center/recommendations-reference



**Azure Security Center の監視**: 適用外

**責任**: Customer

### <a name="72-establish-secure-configurations-for-your-operating-system"></a>7.2:オペレーティング システムのセキュリティで保護された構成を確立する

**ガイダンス**: 適用できません。このガイドラインは、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用外

**責任**: 適用なし

### <a name="73-maintain-secure-configurations-for-all-azure-resources"></a>7.3:すべての Azure リソースのセキュリティで保護された構成を保持する

**ガイダンス**: Azure リソース全体にセキュリティで保護された設定を適用するには、Azure ポリシー [拒否] と [存在する場合はデプロイする] を使用します。  Change Tracking、ポリシー コンプライアンス ダッシュボード、カスタム ソリューションなどのソリューションを使用すれば、ご利用の環境内でのセキュリティの変更を容易に特定できます。

Azure Policy の効果について: https://docs.microsoft.com/azure/governance/policy/concepts/effects

コンプライアンスを強制するポリシーの作成と管理:  https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Change Tracking ソリューションを使用してご利用の環境内の変更を追跡する: https://docs.microsoft.com/azure/automation/change-tracking

Azure リソースのコンプライアンス データを取得する: https://docs.microsoft.com/azure/governance/policy/how-to/get-compliance-data



**Azure Security Center の監視**: 適用外

**責任**: Customer

### <a name="74-maintain-secure-configurations-for-operating-systems"></a>7.4:オペレーティング システムのセキュリティで保護された構成を保持する

**ガイダンス**: 適用できません。このガイドラインは、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用外

**責任**: 適用なし

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5:Azure リソースの構成を安全に格納する

**ガイダンス**: カスタム Azure ポリシー、Azure Resource Manager テンプレート、Desired State Configuration スクリプトなど、ご利用のコードを安全に格納して管理するには、Azure Repos を使用します。Azure DevOps で管理するリソースにアクセスするには、Azure Active Directory (Azure AD) で定義された (Azure DevOps に統合されている場合)、または Active Directory で定義された (TFS に統合されている場合) 特定のユーザー、組み込みのセキュリティ グループ、またはグループにアクセス許可を付与したり、そのアクセス許可を拒否したりできます。

Azure DevOps でコードを格納する方法:  https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Azure DevOps でのアクセス許可とグループについて:  https://docs.microsoft.com/azure/devops/organizations/security/about-permissions



**Azure Security Center の監視**: 適用外

**責任**: Customer

### <a name="76-securely-store-custom-operating-system-images"></a>7.6:カスタム オペレーティング システム イメージを安全に格納する

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用外

**責任**: 適用なし

### <a name="77-deploy-system-configuration-management-tools"></a>7.7:システム構成管理ツールをデプロイする

**ガイダンス**: Azure Policy を使用して、Azure リソースの標準的なセキュリティ構成を定義して実装します。 ご利用の Azure リソースのネットワーク構成を監査または適用するには、Azure Policy エイリアスを使用してカスタム ポリシーを作成します。 また、特定のリソースに関連する組み込みのポリシー定義を使用することもできます。  さらに、Azure Automation を使用して、構成の変更をデプロイすることもできます。

Azure Policy を構成して管理する方法:  https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

エイリアスを使用する方法: https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases



**Azure Security Center の監視**: 適用外

**責任**: Customer

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8:オペレーティング システム用のシステム構成管理ツールをデプロイする

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用外

**責任**: 適用なし

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9:Azure サービスの自動構成監視を実装する

**ガイダンス**: システム構成に関するアラートの生成、システム構成の監査および適用を行うには、Azure Policy エイリアスを使用してカスタム ポリシーを作成します。 ご利用の Azure リソースの構成を自動的に適用するには、Azure Policy の [audit]、[deny]、[deploy if not exist] を使用します。

Azure Policy を構成して管理する方法:  https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


**Azure Security Center の監視**: 適用外

**責任**: Customer

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10:オペレーティング システムの自動構成監視を実装する

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用外

**責任**: 適用なし

### <a name="711-securely-manage-azure-secrets"></a>7.11:Azure シークレットを安全に管理する

**ガイダンス**: Azure Disk Encryption では、ご利用の Azure Data Explorer クラスター仮想マシンの OS とデータ ディスクのボリューム暗号化が実現されます。 また、Azure Key Vault とも統合されます。これにより、ディスク暗号化キーとシークレットを制御および管理することができ、Azure Storage で保存中の VM ディスクの全データが確実に暗号化されます。

Azure Data Explorer でのクラスターのセキュリティ保護の方法:  https://docs.microsoft.com/azure/data-explorer/manage-cluster-security


**Azure Security Center の監視**: 現在は使用できません

**責任**: Microsoft

### <a name="712-securely-and-automatically-manage-identities"></a>7.12:ID を安全かつ自動的に管理する

**ガイダンス**: マネージド ID を使用して、Azure AD で自動的に管理される ID を Azure サービスに提供します。 マネージド ID を使用すると、コードに資格情報を追加しなくても、Azure AD の認証をサポートするさまざまなサービス (Key Vault を含む) に対して認証を行うことができます。マネージド ID を構成する方法:  https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

Azure Data Explorer クラスターのマネージド ID の構成:  https://docs.microsoft.com/azure/data-explorer/managed-identities


**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13:意図しない資格情報の公開を排除する

**ガイダンス**: コード内で資格情報を特定する資格情報スキャナーを実装します。 また、資格情報スキャナーを使うと、検出された資格情報を、Azure Key Vault などのより安全な場所に移動しやすくなります。 

資格情報スキャナーを設定する方法: https://secdevtools.azurewebsites.net/helpcredscan.html

**Azure Security Center の監視**: 適用外

**責任**: Customer

## <a name="malware-defense"></a>マルウェアからの防御

*詳細については、「[セキュリティ コントロール: マルウェアからの防御](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense)」を参照してください。*

### <a name="81-utilize-centrally-managed-anti-malware-software"></a>8.1:一元管理されるマルウェア対策ソフトウェアを利用する

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

Microsoft のマルウェア対策は、Azure サービス (Azure Data Explorer など) をサポートしている基になるホストで有効になっていますが、顧客のコンテンツに対しては実行されません。

**Azure Security Center の監視**: 適用外

**責任**: 適用なし

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2:非コンピューティング Azure リソースにアップロードするファイルを事前にスキャンする

**ガイダンス**: Microsoft のマルウェア対策は、Azure サービス (Azure Data Explorer など) をサポートしている基になるホストで有効になっていますが、顧客のコンテンツに対しては実行されません。

Azure Data Explorer、Data Lake Storage、Blob Storage、Azure Database for PostgreSQL などの非コンピューティング Azure リソースにアップロードされるコンテンツはすべて、事前にスキャンしてください。Microsoft は、これらのインスタンス内のデータにアクセスできません。



**Azure Security Center の監視**: 適用外

**責任**: Customer

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>手順 8.3:マルウェア対策ソフトウェアと署名が確実に更新されるようにする

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

Microsoft のマルウェア対策は、Azure サービスをサポートする基になるホストで有効になっていますが、顧客のコンテンツに対しては実行されません。

**Azure Security Center の監視**: 適用外

**責任**: 適用なし

## <a name="data-recovery"></a>データの復旧

*詳細については、「[セキュリティ コントロール: データの復旧](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery)」を参照してください。*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1:定期的な自動バックアップを保証する

**ガイダンス**: ご利用の Data Explorer クラスターで使用される Microsoft Azure ストレージ アカウント内のデータは、持続性と高可用性を確保するため、常にレプリケートされています。 Azure Storage では、計画されたイベントや計画外のイベント (一時的なハードウェア障害、ネットワークの停止または停電、大規模な自然災害など) から保護するためにデータがコピーされます。 同じデータ センター内、同じリージョン内の複数のゾーン データ センター間、または地理的に分離されたリージョン間でデータをレプリケートすることもできます。

Azure Storage の冗長性とサービスレベル アグリーメントについて:  https://docs.microsoft.com/azure/storage/common/storage-redundancy

データをストレージにエクスポートする:  https://docs.microsoft.com/azure/kusto/management/data-export/export-data-to-storage



**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: システムの完全バックアップを実行し、すべてのカスタマー マネージド キーをバックアップする

**ガイダンス**: Azure Data Explorer は、保存されているストレージ アカウント内のすべてのデータを暗号化します。 規定では、データは Microsoft のマネージド キーで暗号化されます。 暗号化キーをさらに制御するために、データの暗号化に使用する目的で、カスタマー マネージド キーを提供できます。 顧客が管理するキーは Azure Key Vault に格納する必要があります。 

C# を使用してカスタマー マネージド キーを構成する: https://docs.microsoft.com/azure/data-explorer/customer-managed-keys-csharp

Azure Resource Manager テンプレートを使用してカスタマー マネージド キーを構成する:  https://docs.microsoft.com/azure/data-explorer/customer-managed-keys-resource-manager

Azure Key Vault の証明書をバックアップする方法:  https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate?view=azurermps-6.13.0


**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3:カスタマー マネージド キーを含むすべてのバックアップを検証する

**ガイダンス**: ご利用の Azure Key Vault シークレットのデータ復元を定期的にテストします。

Azure Key Vault の証明書を復元する方法:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultcertificate?view=azurermps-6.13.0 

C# を使用してカスタマー マネージド キーを構成する: https://docs.microsoft.com/azure/data-explorer/customer-managed-keys-csharp

Azure Resource Manager テンプレートを使用してカスタマー マネージド キーを構成する:  https://docs.microsoft.com/azure/data-explorer/customer-managed-keys-resource-manager



**Azure Security Center の監視**: 適用外

**責任**: Customer

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: バックアップとカスタマー マネージド キーの保護を保証する

**ガイダンス**: 顧客は Key Vault で論理的な削除を有効にして、偶発的または悪意のある削除からキーを保護することができます。  また、コンテナーまたはオブジェクトが削除状態にある場合に保持期間が経過するまでそれらを消去できないように、削除保護を有効にすることもできます。  

Key Vault で論理的な削除と消去保護を有効にする方法:  https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete

C# を使用してカスタマー マネージド キーを構成する: https://docs.microsoft.com/azure/data-explorer/customer-managed-keys-csharp

Azure Resource Manager テンプレートを使用してカスタマー マネージド キーを構成する:  https://docs.microsoft.com/azure/data-explorer/customer-managed-keys-resource-manager



**Azure Security Center の監視**: はい

**責任**: Customer

## <a name="incident-response"></a>インシデント対応

*詳細については、「[セキュリティ コントロール: インシデント対応](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response)」を参照してください。*

### <a name="101-create-incident-response-guide"></a>10.1:インシデント対応ガイドを作成する

**ガイダンス**: 組織のインシデント対応ガイドを作成します。 要員のすべてのロールを定義するインシデント対応計画が記述されていることと、検出からインシデント後のレビューまでのインシデント対応/管理のフェーズがあることを確認します。
    

    Guidance on building your own security incident response process: https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

    

    Microsoft Security Response Center's Anatomy of an Incident: https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/

    

    Customer may also leverage NIST's Computer Security Incident Handling Guide to aid in the creation of their own incident response plan: https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final

    



**Azure Security Center の監視**: 適用外

**責任**: Customer

### <a name="102-create-incident-scoring-and-prioritization-procedure"></a>10.2:インシデントのスコアリングと優先順位付けの手順を作成する

**ガイダンス**: Security Center によって各アラートに重大度が割り当てられるため、最初に調査する必要があるアラートの優先順位付けに役立ちます。 重要度は、アラートの発行に使用された Security Center の信頼度と、アラートの原因となったアクティビティの背後に悪意のある意図があったかどうかの信頼レベルに基づいて決まります。 
    

    Additionally, clearly mark subscriptions (for ex. production, non-prod) using tags and create a naming system to clearly identify and categorize Azure resources, especially those processing sensitive data.  It is your responsibility to prioritize the remediation of alerts based on the criticality of the Azure resources and environment where the incident occurred.

    

    Security alerts in Azure Security Center: https://docs.microsoft.com/azure/security-center/security-center-alerts-overview

    

    Use tags to organize your Azure resources: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags



**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="103-test-security-response-procedures"></a>10.3:セキュリティ対応手順のテスト

**ガイダンス**: ご利用のシステムのインシデント対応機能を定期的にテストする演習を実施することで、ご利用の Azure リソースの保護を支援できます。 弱点やギャップを特定し、必要に応じて計画を見直します。
    

    Refer to NIST's publication: Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities: https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf



**Azure Security Center の監視**: 適用外

**責任**: Customer

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-nbspfor-security-incidents"></a>10.4:セキュリティ インシデントの連絡先の詳細を指定し、セキュリティ インシデントのアラート通知を構成する&nbsp;

**ガイダンス**: セキュリティ インシデントの連絡先情報は、Microsoft Security Response Center (MSRC) でユーザーのデータが違法または権限のないユーザーによってアクセスされたことが検出された場合に、Microsoft からの連絡先として使用されます。 事後にインシデントをレビューして、問題が解決されていることを確認します。
    

Azure Security Center のセキュリティ連絡先を設定する方法: https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5:インシデント対応システムにセキュリティ アラートを組み込む

**ガイダンス**: 連続エクスポート機能を使用して Azure Security Center のアラートと推奨事項をエクスポートすると、Azure リソースへのリスクを特定できます。 連続エクスポートを使用すると、アラートと推奨事項を手動で、または継続した連続的な方法でエクスポートできます。 Azure Security Center データ コネクタを使用してアラートを Azure Sentinel にストリーミングできます。
    

    How to configure continuous export: https://docs.microsoft.com/azure/security-center/continuous-export

    

    How to stream alerts into Azure Sentinel: https://docs.microsoft.com/azure/sentinel/connect-azure-security-center



**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="106-automate-the-response-to-security-alerts"></a>10.6:セキュリティ アラートへの対応を自動化する

**ガイダンス**: Azure Security Center のワークフロー自動化機能を使用すると、セキュリティのアラートと推奨事項に対して "Logic Apps" で自動的に応答をトリガーし、Azure リソースを保護できます。
    

    How to configure Workflow Automation and Logic Apps: https://docs.microsoft.com/azure/security-center/workflow-automation



**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

## <a name="penetration-tests-and-red-team-exercises"></a>侵入テストとレッド チーム演習

*詳細については、「[セキュリティ コントロール: 侵入テストとレッド チーム演習](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises)」を参照してください。*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-to-remediate-all-critical-security-findings-within-60-days"></a>11.1:Azure リソースの定期的な侵入テストを実行し、すべての重要なセキュリティ調査結果を 60 日以内に確実に修復する

**ガイダンス**: 侵入テストが Microsoft のポリシーに違反しないようにするために、Microsoft の活動規則に従ってください: https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

Microsoft が管理しているクラウド インフラストラクチャ、サービス、アプリケーションに対する Red Teaming およびライブ サイト侵入テストに関する Microsoft の戦略と実施の詳細については、こちらの https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e で確認できます。

**Azure Security Center の監視**: 適用外

**責任**: 共有

## <a name="next-steps"></a>次のステップ

- [Azure セキュリティ ベンチマーク](https://docs.microsoft.com/azure/security/benchmarks/overview)に関するページを参照する
- [Azure セキュリティ ベースライン](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)の詳細について学習する
