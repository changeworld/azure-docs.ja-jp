# [Azure Active Directory のドキュメント](index.md)

# 概要
## [Azure Active Directory とは](fundamentals/active-directory-whatis.md)
## [Azure の ID 管理について](fundamentals/identity-fundamentals.md)
## [Azure ID ソリューションについて](fundamentals/understand-azure-identity-solutions.md)
## [ハイブリッド ID ソリューションの選択](choose-hybrid-identity-solution.md)
## [Azure サブスクリプションを関連付ける](fundamentals/active-directory-how-subscriptions-associated-directory.md)
## [保存場所とデータに関する考慮事項](fundamentals/active-directory-data-storage-eu.md)
## [FAQ](fundamentals/active-directory-faq.md)
## [最新情報](fundamentals/whats-new.md)


# 作業開始
## [Azure AD を使ってみる](fundamentals/get-started-azure-ad.md)
## [Azure AD Premium へのサインアップ](fundamentals/active-directory-get-started-premium.md)
## [カスタム ドメイン名の追加](fundamentals/add-custom-domain.md)
## [会社のブランドの構成](fundamentals/customize-branding.md)
## [Azure AD へのユーザーの追加](fundamentals/add-users-azure-active-directory.md)
## [ユーザーへのライセンスの割り当て](fundamentals/license-users-groups.md)
## [セルフサービスによるパスワードのリセットの構成](authentication/quickstart-sspr.md)
## [Azure AD での組織のプライバシーに関する情報の追加](active-directory-properties-area.md)


# 方法
## 計画と設計
### [Azure AD のアーキテクチャを理解する](fundamentals/active-directory-architecture.md)
### [Azure Active Directory での要求マッピング](active-directory-claims-mapping.md)
### [ハイブリッド ID ソリューションをデプロイする](active-directory-hybrid-identity-design-considerations-overview.md)
#### 要件を確認する
##### [ID](active-directory-hybrid-identity-design-considerations-business-needs.md)
##### [ディレクトリ同期](active-directory-hybrid-identity-design-considerations-directory-sync-requirements.md)
##### [多要素認証](active-directory-hybrid-identity-design-considerations-multifactor-auth-requirements.md)
##### [ID のライフサイクル戦略](active-directory-hybrid-identity-design-considerations-lifecycle-adoption-strategy.md)
#### [データ セキュリティを計画する](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md)
##### [データ保護](active-directory-hybrid-identity-design-considerations-dataprotection-requirements.md)
##### [コンテンツ管理](active-directory-hybrid-identity-design-considerations-contentmgt-requirements.md)
##### [アクセス制御](active-directory-hybrid-identity-design-considerations-accesscontrol-requirements.md)
##### [インシデント対応](active-directory-hybrid-identity-design-considerations-incident-response-requirements.md)
#### ID ライフサイクルを計画する
##### [タスク](active-directory-hybrid-identity-design-considerations-hybrid-id-management-tasks.md)
##### [採用戦略](active-directory-hybrid-identity-design-considerations-identity-adoption-strategy.md)
#### [次のステップ](active-directory-hybrid-identity-design-considerations-nextsteps.md)
#### [ツールの比較](active-directory-hybrid-identity-design-considerations-tools-comparison.md)

## ユーザーの管理
### [Azure AD への新しいユーザーの追加](fundamentals/add-users-azure-active-directory.md)
### [ユーザー プロファイルの管理](fundamentals/active-directory-users-profile-azure-portal.md)
### [アカウントの共有](active-directory-sharing-accounts.md)
### [管理者ロールへのユーザーの割り当て](fundamentals/active-directory-users-assign-role-azure-portal.md)
### [削除済みユーザーの復元](fundamentals/active-directory-users-restore.md)
### [別のディレクトリからのゲスト ユーザーの追加 (B2B)](b2b/what-is-b2b.md)
#### [管理者による B2B ユーザーの追加](b2b/add-users-administrator.md)
#### [インフォメーション ワーカーによる B2B ユーザーの追加](b2b/add-users-information-worker.md)
#### [API とカスタマイズ](b2b/customize-invitation-api.md)
#### [コードと Azure PowerShell のサンプル](b2b/code-samples.md)
#### [セルフサービス サインアップ ポータルのサンプル](b2b/self-service-portal.md)
#### [招待メール](b2b/invitation-email-elements.md)
#### [招待の利用](b2b/redemption-experience.md)
#### [招待を使用せずに B2B ユーザーを追加する](b2b/add-user-without-invite.md)
#### [招待を許可またはブロックする](b2b/allow-deny-list.md)
#### [B2B 向けの条件付きアクセス](b2b/conditional-access.md)
#### [B2B の共有ポリシー](b2b/delegate-invitations.md)
#### [B2B ユーザーをロールに追加する](b2b/add-guest-to-role.md)
#### [動的グループと B2B ユーザー](b2b/use-dynamic-groups.md)
#### [組織から脱退する](b2b/leave-the-organization.md)
#### [監査とレポート](b2b/auditing-and-reporting.md)
#### [ハイブリッド組織向けの B2B](b2b/hybrid-organizations.md)
##### [B2B ユーザーにローカル アプリへのアクセスを付与する](b2b/hybrid-cloud-to-on-premises.md)
##### [ローカル ユーザーにクラウド アプリへのアクセスを付与する](b2b/hybrid-on-premises-to-cloud.md)
#### [B2B と Office 365 の外部共有](b2b/o365-external-user.md)
#### [B2B ライセンス](b2b/licensing-guidance.md)
#### [現時点での制限事項](b2b/current-limitations.md)
#### [FAQ](b2b/faq.md)
#### [B2B のトラブルシューティング](b2b/troubleshoot.md)
#### [B2B ユーザーについて](b2b/user-properties.md)
#### [B2B ユーザー トークン](b2b/user-token.md)
#### [Azure AD 統合アプリ向けの B2B](b2b/configure-saas-apps.md)
#### [B2B ユーザー要求のマッピング](b2b/claims-mapping.md)
#### [B2B コラボレーションと B2C を比較する](b2b/compare-with-b2c.md)
#### [B2B のサポートの利用](b2b/get-support.md)

## [グループとメンバーの管理](fundamentals/active-directory-manage-groups.md)
### グループの管理
#### [Azure Portal](fundamentals/active-directory-groups-create-azure-portal.md)
#### [Azure AD PowerShell for Graph (v2)](active-directory-accessmanagement-groups-settings-v2-cmdlets.md)
#### [Azure AD PowerShell MSOnline](active-directory-accessmanagement-groups-settings-cmdlets.md)
### [グループ メンバーを管理する](fundamentals/active-directory-groups-members-azure-portal.md)
### [グループ所有者を管理する](fundamentals/active-directory-accessmanagement-managing-group-owners.md)
### [グループ メンバーシップを管理する](fundamentals/active-directory-groups-membership-azure-portal.md)
### [グループを使用してライセンスを割り当てる](fundamentals/active-directory-licensing-whatis-azure-portal.md)
#### [グループにライセンスを割り当てる](active-directory-licensing-group-assignment-azure-portal.md)
#### [グループでのライセンスに関する問題を特定して解決する](active-directory-licensing-group-problem-resolution-azure-portal.md)
#### [個別にライセンスを付与されたユーザーをグループベースのライセンスに移行する](active-directory-licensing-group-migration-azure-portal.md)
#### [製品ライセンス間のユーザーの移行](active-directory-licensing-group-product-migration.md)
#### [グループベースのライセンスのその他のシナリオ](active-directory-licensing-group-advanced.md)
#### [グループベースのライセンスの Azure PowerShell の例](active-directory-licensing-ps-examples.md)
#### [Azure AD の製品およびサービス プランに関するリファレンス](active-directory-licensing-product-and-service-plan-reference.md)
### [Office 365 グループの有効期限を設定する](active-directory-groups-lifecycle-azure-portal.md)
### [グループの名前付けポリシーの適用](groups-naming-policy.md)
### [グループをすべて表示する](fundamentals/active-directory-groups-view-azure-portal.md)
### [SaaS アプリへのグループ アクセスの追加](active-directory-accessmanagement-group-saasapps.md)
### [削除された Office 365 グループを復元する](fundamentals/active-directory-groups-restore-azure-portal.md)
### [グループ設定の管理](fundamentals/active-directory-groups-settings-azure-portal.md) 
### 高度なルールの作成
#### [Azure Portal](active-directory-groups-dynamic-membership-azure-portal.md)
### [セルフサービス グループのセットアップ](active-directory-accessmanagement-self-service-group-management.md)
### [トラブルシューティング](active-directory-accessmanagement-troubleshooting.md)

## [レポートの管理](active-directory-reporting-azure-portal.md)
### [サインイン アクティビティ](active-directory-reporting-activity-sign-ins.md)
### [監査アクティビティ](active-directory-reporting-activity-audit-logs.md)
### [危険な状態のユーザー](active-directory-reporting-security-user-at-risk.md)
### [リスクの高いサインイン](active-directory-reporting-security-risky-sign-ins.md)
### [リスク イベント](active-directory-reporting-risk-events.md)
### [FAQ](active-directory-reporting-faq.md)
### タスク
#### [名前付きの場所の構成](active-directory-named-locations.md)
#### [アクティビティ レポートの検出](active-directory-reporting-migration.md)
#### [Azure Active Directory Power BI コンテンツ パックの使用](active-directory-reporting-power-bi-content-pack-how-to.md)
#### [リスクのフラグ付きユーザーの修復](active-directory-report-security-user-at-risk-remediation.md)
### リファレンス
#### [保持](active-directory-reporting-retention.md)
#### [待機時間](active-directory-reporting-latencies-azure-portal.md)
#### [監査アクティビティのリファレンス](active-directory-reporting-activity-audit-reference.md)
#### [サインイン アクティビティのエラー コード](active-directory-reporting-activity-sign-ins-errors.md)
#### [多要素認証](active-directory-reporting-activity-sign-ins-mfa.md)



### トラブルシューティング
#### [見つからない監査データ](active-directory-reporting-troubleshoot-missing-audit-data.md)
#### [ダウンロードに見つからないデータ](active-directory-reporting-troubleshoot-missing-data-download.md)
#### [Azure Active Directory アクティビティ ログ コンテンツ パックのエラー](active-directory-reporting-troubleshoot-content-pack.md)
### [プログラムによるアクセス](active-directory-reporting-api-getting-started-azure-portal.md)
#### [前提条件](active-directory-reporting-api-prerequisites-azure-portal.md)
#### [監査のサンプル](active-directory-reporting-api-audit-samples.md)
#### [サインインのサンプル](active-directory-reporting-api-sign-in-activity-samples.md)
#### [証明書の使用](active-directory-reporting-api-with-certificates.md)

## パスワードの管理
### [パスワードの概要](authentication/active-directory-passwords-overview.md)
### ユーザー ドキュメント
#### [パスワードのリセットまたは変更](active-directory-passwords-update-your-own-password.md)
#### [パスワードのベスト プラクティス](active-directory-secure-passwords.md)
#### [セルフサービスのパスワード リセットのための登録](active-directory-passwords-reset-register.md)
### [SSPR のしくみ](authentication/concept-sspr-howitworks.md)
### [SSPR デプロイ ガイド](authentication/howto-sspr-deployment.md)
### [SSPR と Windows 10](authentication/tutorial-sspr-windows.md)
### [SSPR のポリシー](authentication/concept-sspr-policy.md)
### [SSPR のカスタマイズ](authentication/concept-sspr-customization.md)
### [SSPR データ要件](authentication/howto-sspr-authenticationdata.md)
### [SSPR のレポート](authentication/howto-sspr-reporting.md)
### [脆弱なパスワードを排除する](authentication/concept-password-ban-bad.md)
### [禁止パスワード リストを構成する](authentication/howto-password-ban-bad.md)
### [オンプレミスの統合](authentication/concept-password-ban-bad-on-premises.md)
### [Azure AD のパスワード保護をデプロイする](authentication/howto-password-ban-bad-on-premises.md)
### [Azure AD のパスワード保護を構成する](authentication/howto-password-ban-bad-on-premises-operations.md)
### [Azure AD のパスワード保護を監視する](authentication/howto-password-ban-bad-on-premises-troubleshoot.md)
### IT 管理者: パスワードのリセット
#### [Azure Portal](fundamentals/active-directory-users-reset-password-azure-portal.md)
### [SSPR のライセンス](authentication/concept-sspr-licensing.md)
### [パスワード ライトバック](authentication/howto-sspr-writeback.md)
### [トラブルシューティング](authentication/active-directory-passwords-troubleshoot.md)
### [FAQ](authentication/active-directory-passwords-faq.md)


## デバイスを管理する
### [はじめに](device-management-introduction.md)
### [Azure Portal の使用](device-management-azure-portal.md)
### [Azure AD Join の計画](active-directory-azureadjoin-deployment-aadjoindirect.md)
### [FAQ](device-management-faq.md)
### タスク
#### [Azure AD 登録済み Windows 10 デバイスの設定](device-management-azuread-registered-devices-windows10-setup.md)
#### [Azure AD 参加済みデバイスの設定](device-management-azuread-joined-devices-setup.md)
#### [ハイブリッド Azure AD 参加済みデバイスの設定](device-management-hybrid-azuread-joined-devices-setup.md)
#### [オンプレミスにデプロイする](active-directory-device-registration-on-premises-setup.md)
#### [Windows 10 の初回実行時における Azure AD への参加](device-management-azuread-joined-devices-frx.md)
### トラブルシューティング
#### [ハイブリッド Azure AD 参加済みの Windows 10 および Windows Server 2016 デバイス](device-management-troubleshoot-hybrid-join-windows-current.md)
#### [ハイブリッド Azure AD 参加済みの従来の Windows デバイス](device-management-troubleshoot-hybrid-join-windows-legacy.md)

## アプリを管理する
### [概要](manage-apps/what-is-application-management.md)
### [使用の開始](manage-apps/plan-an-application-integration.md)
### [SaaS アプリの統合に関するチュートリアル](saas-apps/tutorial-list.md)


### [Cloud App Discovery](manage-apps/cloud-app-discovery.md)
#### [スナップショット レポートの作成](manage-apps/cloud-app-discovery-create-snapshot-reports.md)
#### [継続的なレポートの構成](https://docs.microsoft.com/cloud-app-security/discovery-docker)
#### [カスタム ログ パーサーを使用する](https://docs.microsoft.com/cloud-app-security/custom-log-parser)

### [SaaS アプリへのユーザーのプロビジョニングとプロビジョニング解除](active-directory-saas-app-provisioning.md) 
#### [アプリの統合に関するチュートリアル](saas-apps/tutorial-list.md) 
#### [SCIM 対応アプリへのプロビジョニングの自動化](manage-apps/use-scim-to-provision-users-and-groups.md) 
#### [属性マッピングのカスタマイズ](active-directory-saas-customizing-attribute-mappings.md) 
#### [属性マッピングの式の書き方](active-directory-saas-writing-expressions-for-attribute-mappings.md) 
#### [スコープ フィルターの使用](active-directory-saas-scoping-filters.md) 
#### [自動ユーザー プロビジョニングについてのレポート](active-directory-saas-provisioning-reporting.md) 
#### [ユーザー プロビジョニングのトラブルシューティング](active-directory-application-provisioning-content-map.md) 

### [アプリ プロキシを使用してリモートでアプリにアクセスする](manage-apps/application-proxy.md)
#### 作業開始
##### [アプリケーション プロキシを有効にする](manage-apps/application-proxy-enable.md)
##### [アプリを発行する](manage-apps/application-proxy-publish-azure-portal.md)
##### [カスタム ドメイン](manage-apps/application-proxy-configure-custom-domain.md)
#### [シングル サインオン](manage-apps/application-proxy-single-sign-on.md)
##### [KCD を使用した SSO](manage-apps/application-proxy-configure-single-sign-on-with-kcd.md)
##### [ヘッダーを使用した SSO](manage-apps/application-proxy-configure-single-sign-on-with-ping-access.md)
##### [パスワード保管を使用した SSO](manage-apps/application-proxy-configure-single-sign-on-password-vaulting.md)
#### 概念
##### [コネクタ](manage-apps/application-proxy-connectors.md)
##### [セキュリティ](manage-apps/application-proxy-security.md)
##### [ネットワーク](manage-apps/application-proxy-network-topology.md)


##### [TMG または UAG からのアップグレード](manage-apps/application-proxy-migration.md)

#### 詳細な構成
##### [異なるネットワークに発行する](manage-apps/application-proxy-connector-groups.md)
##### [プロキシ サーバー](manage-apps/application-proxy-configure-connectors-with-proxy-servers.md)
##### [要求に対応するアプリケーション](manage-apps/application-proxy-configure-for-claims-aware-applications.md)
##### [ネイティブ クライアント アプリ](manage-apps/application-proxy-configure-native-client-application.md)
##### [サイレント インストール](manage-apps/application-proxy-register-connector-powershell.md)
##### [カスタム ホーム ページ](manage-apps/application-proxy-configure-custom-home-page.md)
##### [インライン リンクの変換](manage-apps/application-proxy-configure-hard-coded-link-translation.md)
##### [ワイルドカード](active-directory-application-proxy-wildcard.md)
##### [個人データの削除](manage-apps/application-proxy-remove-personal-data.md)


#### 発行に関するチュートリアル
##### [リモート デスクトップ](manage-apps/application-proxy-integrate-with-remote-desktop-services.md)
##### [SharePoint](manage-apps/application-proxy-integrate-with-sharepoint-server.md)
##### [Microsoft Teams](manage-apps/application-proxy-integrate-with-teams.md)
##### [Tableau](manage-apps/application-proxy-integrate-with-tableau.md)
##### [Qlik](active-directory-application-proxy-qlik.md)
#### [PowerShell](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#application_proxy_application_management) 

#### [トラブルシューティング](manage-apps/application-proxy-troubleshoot.md)

### エンタープライズ アプリの管理
#### [ユーザーを割り当てる](manage-apps/assign-user-or-group-access-portal.md)
#### [ブランドをカスタマイズする](manage-apps/change-name-or-logo-portal.md)
#### [ユーザーのサインインを無効にする](manage-apps/disable-user-sign-in-portal.md)
#### [ユーザーを削除する](manage-apps/remove-user-or-group-access-portal.md)
#### [すべてのアプリを表示する](manage-apps/view-applications-portal.md)
#### [ユーザー アカウントのプロビジョニング管理](manage-apps/configure-automatic-user-provisioning-portal.md)
#### [エンタープライズ アプリのシングル サインオンの管理](manage-apps/configure-single-sign-on-portal.md)
#### [SAML アプリの詳細な証明書署名](manage-apps/certificate-signing-options.md)
#### [ユーザーのエクスペリエンスからアプリケーションを非表示にする](manage-apps/hide-application-from-user-portal.md)
### [HRD ポリシーを使用してサインイン自動アクセラレーションを構成する](manage-apps/configure-authentication-for-federated-users-portal.md)
### [AD FS アプリを Azure AD に移行する](manage-apps/migrate-adfs-apps-to-azure.md) 
### [アプリへのアクセスを管理する](manage-apps/what-is-access-management.md)
#### [SSO アクセス](manage-apps/what-is-single-sign-on.md)
#### [SSO の証明書](manage-apps/manage-certificates-for-federated-single-sign-on.md)
#### [テナント制限](manage-apps/tenant-restrictions.md)
#### [ユーザーのプロビジョニングに SCIM を使用する](manage-apps/use-scim-to-provision-users-and-groups.md)


### トラブルシューティング



#### アクセス パネル
##### [アプリが表示されない](application-access-panel-unexpected-application-not-appearing.md)
##### [予期しないアプリが表示される](application-access-panel-unexpected-application-appears.md)
##### [サインインできない](application-access-panel-web-sign-in-problem.md)
##### [ブラウザー拡張機能のインストール時のエラー](application-access-panel-extension-problem-installing.md)
##### [セルフサービス アプリ アクセスの使用方法](application-access-panel-self-service-applications-how-to.md)
##### [セルフサービス アプリ アクセスの使用時のエラー](application-access-panel-self-service-applications-problem.md)

#### アプリの追加
##### [アプリの種類の選択](application-config-add-app-problem-how-to-choose-application-type.md)
##### [一般的な問題 - ギャラリー アプリ](application-config-add-app-problem-problem-adding-gallery-app.md)
##### [一般的な問題 - ギャラリー以外のアプリ](application-config-add-app-problem-problem-adding-non-gallery-app.md)

#### アプリケーション プロキシ
##### [アプリ ページを表示できない](application-proxy-page-appearance-broken-problem.md)
##### [アプリケーション読み込みに時間がかかりすぎる](application-proxy-page-load-speed-problem.md)
##### [アプリケーション ページ上のリンクが動作しない](application-proxy-page-links-broken-problem.md)
##### [アプリに対して開く必要のあるポート](application-proxy-connectivity-ports-how-to.md)
##### [アプリのコネクタ グループに動作するコネクタがない](application-proxy-connectivity-no-working-connector.md)
##### [管理ポータルでの構成](application-proxy-config-how-to.md)
##### [アプリに対するシングル サインオンの構成](application-proxy-config-sso-how-to.md)
##### [管理ポータルでのアプリの作成に関する問題](application-proxy-config-problem.md)
##### [Kerberos の制約付き委任を構成する](application-proxy-back-end-kerberos-constrained-delegation-how-to.md)
##### [PingAccess を使用した構成](application-proxy-back-end-ping-access-how-to.md)
##### ["この企業アプリケーションにアクセスできない" というエラー](application-proxy-sign-in-bad-gateway-timeout-error.md)
##### [アプリケーション プロキシ エージェント コネクタのインストールに関する問題](application-proxy-connector-installation-problem.md)


#### アプリケーションの登録
##### [アプリケーション オブジェクトのフィールドの入力](application-dev-registration-config-specific-application-property-how-to.md)
##### [トークン有効期間の既定値の変更](application-dev-registration-config-change-token-lifetime-how-to.md)

#### 認証
##### [エンドポイントの構成](application-dev-registration-config-how-to.md)

#### 条件付きアクセス
##### [顧客がデバイス登録の前提条件を満たしていない](active-directory-conditional-access.md)
##### [条件付きアクセス ポリシーが正しく設定されていないことが原因でテナントがブロックされる](active-directory-conditional-access-device-remediation.md)
##### [企業ネットワークのルールを無効にする方法とタイミング](https://aka.ms/calocation)
##### [Azure AD でユーザーが登録できるデバイス数を増やす方法](active-directory-azureadjoin-setup.md)
##### [Exchange Online の条件付きアクセスの設定方法](https://aka.ms/csforexchange)
##### [Windows 7 デバイスの条件付きアクセスの設定方法](active-directory-conditional-access.md#device-based-conditional-access)
##### [条件付きアクセスでサポートされるアプリケーション](active-directory-conditional-access-supported-apps.md)

#### API の検索
##### [API の検索](application-dev-api-find-an-api-how-to.md)

#### アクセスの管理
##### [アプリへのユーザーとグループの割り当て](application-access-assignment-how-to-add-assignment.md)
##### [アプリへのユーザー アクセスの削除](application-access-assignment-how-to-remove-assignment.md)
##### [セルフサービス アプリの割り当ての構成](application-access-self-service-how-to.md)
##### [予期しないユーザーが割り当てられる](application-access-unexpected-user-assignment.md)
##### [アプリケーション リストの予期しないアプリ](application-access-unexpected-application.md)

#### マルチテナント アプリ
##### [新しいアプリの構成](application-dev-setup-multi-tenant-app.md)
##### [アプリ ギャラリーへの追加](application-dev-registration-config-multi-tenant-application-add-to-gallery-how-to.md)

#### アクセス許可
##### [API のアクセス許可の選択](application-dev-perms-for-given-api.md)
##### [アプリへのアクセス許可の付与](application-dev-registration-config-grant-permissions-how-to.md)
##### [委任されたアクセス許可とアプリケーションのアクセス許可](application-dev-delegated-and-app-perms.md)
##### [アプリケーションへの同意](application-dev-consent-framework.md)

#### プロビジョニング
##### [所要時間](application-provisioning-when-will-provisioning-finish-specific-user.md)
##### [長時間かかる - ギャラリー アプリ](application-provisioning-when-will-provisioning-finish.md)
##### [ユーザー プロビジョニングの構成 - ギャラリー アプリ](application-provisioning-config-how-to.md)
##### [ユーザー プロビジョニングの構成に関する問題 - ギャラリー アプリ](application-provisioning-config-problem.md)
##### [ギャラリー アプリケーションへのユーザー プロビジョニングを構成中の管理者の資格情報の保存に関する問題](application-provisioning-config-problem-storage-limit.md)
##### [ユーザーがプロビジョニングされない - ギャラリー アプリ](application-provisioning-config-problem-no-users-provisioned.md)
##### [間違ったユーザーがプロビジョニングされている - ギャラリー アプリ](application-provisioning-config-problem-wrong-users-provisioned.md)

#### シングル サインオン
##### [方法の選択](application-config-sso-how-to-choose-sign-on-method.md)
##### [構成](application-dev-registration-config-sso-how-to.md)
##### [フェデレーションの構成 - ギャラリー アプリ](application-config-sso-how-to-configure-federated-sso-gallery.md)
##### [フェデレーションの構成の一般的な問題 - ギャラリー アプリ](application-config-sso-problem-configure-federated-sso-gallery.md)
##### [フェデレーションの構成 - ギャラリー以外のアプリ](application-config-sso-how-to-configure-federated-sso-non-gallery.md)
##### [フェデレーションの構成の一般的な問題 - ギャラリー以外のアプリ](application-config-sso-problem-configure-federated-sso-non-gallery.md)
##### [パスワードの構成 - ギャラリー アプリ](application-config-sso-how-to-configure-password-sso-gallery.md)
##### [パスワードの構成の一般的な問題 - ギャラリー アプリ](application-config-sso-problem-configure-password-sso-gallery.md)
##### [パスワードの構成 - ギャラリー以外のアプリ](application-config-sso-how-to-configure-password-sso-non-gallery.md)
##### [パスワードの構成の一般的な問題 - ギャラリー以外のアプリ](application-config-sso-problem-configure-password-sso-non-gallery.md)

#### ユーザー サインインに関する問題
##### [予期しない同意プロンプト](application-sign-in-unexpected-user-consent-prompt.md)
##### [ユーザーの同意エラー](application-sign-in-unexpected-user-consent-error.md)
##### [カスタム ポータルからのサインインに関する問題](application-sign-in-other-problem-deeplink.md)
##### [アクセス パネルからのサインインに関する問題](application-sign-in-other-problem-access-panel.md)
##### [アプリケーションのサインイン ページでのエラー](application-sign-in-problem-application-error.md)
##### [パスワード シングル サインオンに関する問題 - ギャラリー以外のアプリ](application-sign-in-problem-password-sso-non-gallery.md)
##### [パスワード シングル サインオンに関する問題 - ギャラリー アプリ](application-sign-in-problem-password-sso-gallery.md)
##### [Microsoft アプリへのサインインに関する問題](application-sign-in-problem-first-party-microsoft.md)
##### [フェデレーション シングル サインオンに関する問題 - ギャラリー以外のアプリ](application-sign-in-problem-federated-sso-non-gallery.md)
##### [フェデレーション シングル サインオンに関する問題 - ギャラリー アプリ](application-sign-in-problem-federated-sso-gallery.md)
##### [カスタム開発されたアプリに関する問題](application-sign-in-problem-custom-dev.md)
##### [オンプレミス アプリに関する問題 - アプリケーション プロキシの問題](application-sign-in-problem-on-premises-application-proxy.md)

### [アプリの開発](active-directory-applications-guiding-developers-for-lob-applications.md)
### [ドキュメント ライブラリ](active-directory-apps-index.md)

## ディレクトリの管理
### [Azure AD Connect](./connect/active-directory-aadconnect.md)
### カスタム ドメイン名
#### [クイックスタート](fundamentals/add-custom-domain.md)
#### [カスタム ドメイン名を追加する](active-directory-domains-manage-azure-portal.md)
### [ディレクトリを管理する](fundamentals/active-directory-administer.md)
### [ディレクトリを削除する](directory-delete-howto.md)
### [複数のディレクトリ](active-directory-licensing-directory-independence.md)
### [セルフサービス サインアップ](active-directory-self-service-signup.md)
### [管理されていないディレクトリの引き継ぎ](domains-admin-takeover.md)
### [Enterprise State Roaming](active-directory-windows-enterprise-state-roaming-overview.md)
#### [有効にする](active-directory-windows-enterprise-state-roaming-enable.md)
#### [グループ ポリシーの設定](active-directory-windows-enterprise-state-roaming-group-policy-settings.md)
#### [Windows 10 の設定](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md)
#### [FAQ](active-directory-windows-enterprise-state-roaming-faqs.md)
#### [トラブルシューティング](active-directory-windows-enterprise-state-roaming-troubleshooting.md)


### [Azure AD Connect を使用してオンプレミスの ID を統合する](./connect/active-directory-aadconnect.md)

## リソースへのアクセスを委任する
### [管理者ロール](active-directory-assign-admin-roles-azure-portal.md)
#### [ユーザーに管理者ロールを割り当てる](fundamentals/active-directory-users-assign-role-azure-portal.md)
#### [メンバーとゲスト ユーザーのアクセス許可の比較](fundamentals/users-default-permissions.md)
### [管理者ロールのセキュリティ保護](admin-roles-best-practices.md)  
#### [緊急アクセス用管理者アカウントの作成](active-directory-admin-manage-emergency-access-accounts.md)
### [管理単位](active-directory-administrative-units-management.md)
### [トークンの有効期間を構成する](active-directory-configurable-token-lifetimes.md)
### [特権ロールのセキュリティ保護](admin-roles-best-practices.md)

## アクセス レビュー
### [アクセス レビューの概要](active-directory-azure-ad-controls-access-reviews-overview.md)
### [アクセス レビューの完了](active-directory-azure-ad-controls-complete-access-review.md)
### [アクセス レビューの作成](active-directory-azure-ad-controls-create-access-review.md)
### [アクセス レビューを実行する方法](active-directory-azure-ad-controls-perform-access-review.md)
### [アクセスをレビューする方法](active-directory-azure-ad-controls-how-to-review-your-access.md)
### [アクセス レビューでのゲスト アクセス](active-directory-azure-ad-controls-manage-guest-access-with-access-reviews.md)
### [レビューでのユーザー アクセスの管理](active-directory-azure-ad-controls-manage-user-access-with-access-reviews.md)
### [プログラムとコントロールの管理](active-directory-azure-ad-controls-manage-programs-controls.md)
### [アクセス レビュー結果の取得](active-directory-azure-ad-controls-retrieve-access-review.md)

## ID をセキュリティで保護する
### [条件付きアクセス](active-directory-conditional-access-azure-portal.md)
#### [作業開始](active-directory-conditional-access-azure-portal-get-started.md)
#### クイック スタート
##### [クラウド アプリごとの MFA を構成する](active-directory-conditional-access-app-based-mfa.md)
##### [使用条件への同意を要求する](active-directory-conditional-access-tou.md)
#### チュートリアル
##### [クラシック MFA ポリシーの移行](active-directory-conditional-access-migration-mfa.md)
#### 概念
##### [ベースラインの保護](active-directory-conditional-access-baseline-protection.md)
##### [条件](active-directory-conditional-access-conditions.md)
##### [場所の条件](active-directory-conditional-access-locations.md)
##### [コントロール](active-directory-conditional-access-controls.md)
##### [What if ツール](active-directory-conditional-access-whatif.md)
##### [Office 365 サービスのデバイス ポリシーについて](active-directory-conditional-access-device-policies.md)
#### ハウツー ガイド
##### [ベスト プラクティス](active-directory-conditional-access-best-practices.md)
##### [信頼されていないネットワークからのアクセスの試行に関する条件付きアクセス ポリシーを構成する](active-directory-conditional-access-untrusted-networks.md)
##### [デバイスベースの条件付きアクセスの設定](active-directory-conditional-access-policy-connected-applications.md)
##### [アプリベースの条件付きアクセスの設定](active-directory-conditional-access-mam.md)
##### [ユーザーとアプリの使用条件を指定する](active-directory-tou.md)
##### [従来のポリシーの移行](active-directory-conditional-access-migration.md)
##### [VPN 接続の設定](https://docs.microsoft.com/windows-server/remote/remote-access/vpn/always-on-vpn/deploy/always-on-vpn-deploy)
##### [SharePoint Online と Exchange Online の設定](active-directory-conditional-access-no-modern-authentication.md)
##### [修復](active-directory-conditional-access-device-remediation.md)
#### [テクニカル リファレンス](active-directory-conditional-access-technical-reference.md)
#### [FAQ](active-directory-conditional-faqs.md)

### 証明書ベースの認証
#### [Android](active-directory-certificate-based-authentication-android.md)
#### [iOS](active-directory-certificate-based-authentication-ios.md)
#### [作業開始](active-directory-certificate-based-authentication-get-started.md)

### [Azure AD Identity Protection](active-directory-identityprotection.md)
#### [有効にする](active-directory-identityprotection-enable.md)
#### [脆弱性を検出する](active-directory-identityprotection-vulnerabilities.md)
#### [リスク イベント](active-directory-identity-protection-risk-events.md)
#### [Notifications](active-directory-identityprotection-notifications.md)
#### [サインイン エクスペリエンス](active-directory-identityprotection-flows.md)
#### [リスク イベントをシミュレートする](active-directory-identityprotection-playbook.md)
#### [ユーザーのブロックを解除する](active-directory-identityprotection-unblock-howto.md)
#### [FAQ](active-directory-identity-protection-faqs.md)
#### [用語集](active-directory-identityprotection-glossary.md)
#### [Microsoft Graph](active-directory-identityprotection-graph-getting-started.md)
### [Privileged Identity Management](active-directory-privileged-identity-management-configure.md)

## Azure AD と他のサービスのと統合 
### [LinkedIn を Azure AD と統合する](linkedin-integration.md)

## [Azure に AD FS をデプロイする](active-directory-aadconnect-azure-adfs.md)
### [高可用性](active-directory-adfs-in-azure-with-azure-traffic-manager.md)
### [署名ハッシュ アルゴリズムを変更する](active-directory-federation-sha256-guidance.md)

## [トラブルシューティング](fundamentals/active-directory-troubleshooting-support-howto.md)

## Azure AD の概念実証 (PoC) のデプロイ
### [PoC プレイブック: はじめに](active-directory-playbook-intro.md)
### [PoC プレイブック: 主な要素](active-directory-playbook-ingredients.md)
### [PoC プレイブック: 実装](active-directory-playbook-implementation.md)
### [PoC プレイブック: 構成要素](active-directory-playbook-building-blocks.md)


# リファレンス
## [コード サンプル](https://azure.microsoft.com/resources/samples/?service=active-directory)
## [Azure PowerShell コマンドレット](/powershell/azure/overview)
## [Java API リファレンス](/java/api)
## [.NET API](/active-directory/adal/microsoft.identitymodel.clients.activedirectory)
## [サービスの制限と制約](active-directory-service-limits-restrictions.md)

# 関連項目
## [Multi-Factor Authentication](/azure/multi-factor-authentication/)
## [Azure AD Connect](./connect/active-directory-aadconnect.md)
## [Azure AD Connect Health](./connect-health/active-directory-aadconnect-health.md)
## [開発者向け Azure AD](./develop/active-directory-how-to-integrate.md)
## [Azure AD Privileged Identity Management](./privileged-identity-management/active-directory-securing-privileged-access.md)

# リソース
## [Azure フィードバック フォーラム](https://feedback.azure.com/forums/169401-azure-active-directory)
## [Azure のロードマップ](https://azure.microsoft.com/roadmap/?category=security-identity)
## [MSDN フォーラム](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)
## [料金](https://azure.microsoft.com/pricing/details/active-directory/)
## [料金計算ツール](https://azure.microsoft.com/pricing/calculator/)
## [サービスの更新情報](https://azure.microsoft.com/updates/?product=active-directory)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-active-directory)
## [ビデオ](https://azure.microsoft.com/documentation/videos/index/?services=active-directory)
