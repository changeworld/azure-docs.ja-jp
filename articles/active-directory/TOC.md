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
### [ユーザー パスワードのリセット](fundamentals/active-directory-users-reset-password-azure-portal.md)
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
### [グループの管理](fundamentals/active-directory-groups-create-azure-portal.md)
### [削除された Office 365 グループを復元する](fundamentals/active-directory-groups-restore-azure-portal.md)
### [グループ設定の管理](fundamentals/active-directory-groups-settings-azure-portal.md)
## [レポートの管理](reports-monitoring/overview-reports.md)
### [サインイン アクティビティ](reports-monitoring/concept-sign-ins.md)
### [監査アクティビティ](reports-monitoring/concept-audit-logs.md)
### [危険な状態のユーザー](reports-monitoring/concept-user-at-risk.md)
### [リスクの高いサインイン](reports-monitoring/concept-risky-sign-ins.md)
### [リスク イベント](reports-monitoring/concept-risk-events.md)
### [Azure Monitor を使用したログの監視](reports-monitoring/overview-activity-logs-in-azure-monitor.md)
### [FAQ](reports-monitoring/reports-faq.md)

### タスク
#### [名前付きの場所の構成](active-directory-named-locations.md)
#### [アクティビティ レポートの検出](reports-monitoring/howto-find-activity-reports.md)
#### [Azure AD Power BI コンテンツ パックの使用](reports-monitoring/howto-power-bi-content-pack.md)
#### [リスクのフラグ付きユーザーの修復](reports-monitoring/howto-remediate-users-flagged-for-risk.md)
#### [Azure イベント ハブへのアクティビティ ログのルーティング](reports-monitoring/quickstart-azure-monitor-stream-logs-to-event-hub.md)
#### [Azure ストレージ アカウントへのアクティビティ ログのアーカイブ](reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md)
#### [Azure Monitor を使用したアクティビティ ログと Splunk の統合](reports-monitoring/tutorial-integrate-activity-logs-with-splunk.md)

### リファレンス
#### [保持](reports-monitoring/reference-reports-data-retention.md)
#### [待機時間](reports-monitoring/reference-reports-latencies.md)
#### [監査アクティビティのリファレンス](reports-monitoring/reference-audit-activities.md)
#### [サインイン アクティビティのエラー コード](reports-monitoring/reference-sign-ins-error-codes.md)
#### [Azure Monitor での監査ログのスキーマの解釈](reports-monitoring/reference-azure-monitor-audit-log-schema.md)
#### [Azure Monitor でのサインイン ログのスキーマの解釈](reports-monitoring/reference-azure-monitor-sign-ins-log-schema.md)

### トラブルシューティング
#### [Azure AD アクティビティ ログに見つからないデータ](reports-monitoring/troubleshoot-missing-audit-data.md)
#### [ダウンロードに見つからないデータ](reports-monitoring/troubleshoot-missing-data-download.md)
#### [Azure AD アクティビティ ログ コンテンツ パックのエラー](reports-monitoring/troubleshoot-content-pack.md)
#### [Azure AD Reporting API のエラー](reports-monitoring/troubleshoot-graph-api.md)

### [プログラムによるアクセス](reports-monitoring/concept-reporting-api.md)
#### [前提条件](reports-monitoring/howto-configure-prerequisites-for-reporting-api.md)
#### [証明書の使用](reports-monitoring/tutorial-access-api-with-certificates.md)

## [パスワードの管理](authentication/concept-sspr-howitworks.md)
### ユーザー ドキュメント
#### [パスワードのリセットまたは変更](user-help/active-directory-passwords-update-your-own-password.md)
#### [セルフサービスのパスワード リセットのための登録](user-help/active-directory-passwords-reset-register.md)


## デバイスを管理する
### [概要](devices/overview.md)

### クイック スタート
#### [Azure AD 登録済み Windows 10 デバイスの設定](user-help/device-management-azuread-registered-devices-windows10-setup.md)
#### [Azure AD 参加済みデバイスの設定](user-help/device-management-azuread-joined-devices-setup.md)

### チュートリアル
#### [マネージド ドメインのハイブリッド Azure AD 参加を構成する](devices/hybrid-azuread-join-managed-domains.md)
#### [フェデレーション ドメインのハイブリッド Azure AD 参加を構成する](devices/hybrid-azuread-join-federated-domains.md)
#### [ハイブリッド Azure AD 参加を手動で構成する](devices/hybrid-azuread-join-manual-steps.md)
#### [Windows 10 の初回実行時に Azure AD 参加を構成する](devices/azuread-joined-devices-frx.md)

### ハウツーガイド
#### [Azure AD 参加を計画する](devices/azureadjoin-plan.md)
#### [ハイブリッド Azure AD 参加の実装を計画する](devices/hybrid-azuread-join-plan.md)
#### [デバイスのハイブリッド Azure AD 参加を制御する](devices/hybrid-azuread-join-control.md)
#### [Azure AD 参加済みデバイスにローカル管理者を割り当てる](devices/assign-local-admin.md)
#### [ハイブリッド Azure AD 参加済みの最新 Windows デバイスのトラブルシューティングを行う](devices/troubleshoot-hybrid-join-windows-current.md)
#### [ハイブリッド Azure AD 参加済みのレガシ Windows デバイスのトラブルシューティングを行う](devices/troubleshoot-hybrid-join-windows-legacy.md)

### 概念
#### [Azure Portal によるデバイスの管理](devices/device-management-azure-portal.md)
#### [FAQ](devices/faq.md)

## アプリを管理する
### [概要](manage-apps/what-is-application-management.md)
### [使用の開始](manage-apps/plan-an-application-integration.md)
### [SaaS アプリの統合に関するチュートリアル](saas-apps/tutorial-list.md)

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
#### [アプリケーションを追加する](manage-apps/add-application-portal.md)
#### [テナント アプリを表示する](manage-apps/view-applications-portal.md)
#### [シングル サインオンの構成](manage-apps/configure-single-sign-on-portal.md)
#### [ユーザーを割り当てる](manage-apps/assign-user-or-group-access-portal.md)
#### [ブランドをカスタマイズする](manage-apps/change-name-or-logo-portal.md)
#### [ユーザーのサインインを無効にする](manage-apps/disable-user-sign-in-portal.md)
#### [ユーザーを削除する](manage-apps/remove-user-or-group-access-portal.md)

#### [ユーザー アカウントのプロビジョニング管理](manage-apps/configure-automatic-user-provisioning-portal.md)

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

#### Authentication
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
### [ディレクトリを管理する](fundamentals/active-directory-administer.md)
### [Enterprise State Roaming](active-directory-windows-enterprise-state-roaming-overview.md)
#### [有効にする](active-directory-windows-enterprise-state-roaming-enable.md)
#### [グループ ポリシーの設定](active-directory-windows-enterprise-state-roaming-group-policy-settings.md)
#### [Windows 10 の設定](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md)
#### [FAQ](active-directory-windows-enterprise-state-roaming-faqs.md)
#### [トラブルシューティング](active-directory-windows-enterprise-state-roaming-troubleshooting.md)


### [Azure AD Connect を使用してオンプレミスの ID を統合する](./connect/active-directory-aadconnect.md)

### [トークンの有効期間を構成する](active-directory-configurable-token-lifetimes.md)

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
### 証明書ベースの認証
#### [Android](active-directory-certificate-based-authentication-android.md)
#### [iOS](active-directory-certificate-based-authentication-ios.md)
#### [はじめに](active-directory-certificate-based-authentication-get-started.md)

### Azure AD Identity Protection
#### [概要](identity-protection/overview.md)
#### [有効にする](identity-protection/enable.md)
#### [脆弱性を検出する](identity-protection/vulnerabilities.md)
#### [リスク イベント](active-directory-identity-protection-risk-events.md)
#### [Notifications](identity-protection/notifications.md)
#### [サインイン エクスペリエンス](identity-protection/flows.md)
#### [リスク イベントをシミュレートする](identity-protection/playbook.md)
#### [ユーザーのブロックを解除する](identity-protection/howto-unblock-user.md)
#### [FAQ](identity-protection/faqs.md)
#### [用語集](identity-protection/glossary.md)
#### [Microsoft Graph](identity-protection/graph-get-started.md)
### [Privileged Identity Management](privileged-identity-management/pim-configure.md?toc=%2fazure%2factive-directory%2ftoc.json)

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

# 関連項目
## [Multi-Factor Authentication](/azure/multi-factor-authentication/)
## [Azure AD Connect](./connect/active-directory-aadconnect.md)
## [Azure AD Connect Health](./connect-health/active-directory-aadconnect-health.md)
## [開発者向け Azure AD](./develop/active-directory-how-to-integrate.md)
## [Azure AD Privileged Identity Management](./privileged-identity-management/pim-configure.md)

# リソース
## [Azure AD のデプロイ計画](./fundamentals/active-directory-deployment-plans.md)
## [Azure フィードバック フォーラム](https://feedback.azure.com/forums/169401-azure-active-directory)
## [Azure のロードマップ](https://azure.microsoft.com/roadmap/?category=security-identity)
## [MSDN フォーラム](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)
## [料金](https://azure.microsoft.com/pricing/details/active-directory/)
## [料金計算ツール](https://azure.microsoft.com/pricing/calculator/)
## [サービスの更新情報](https://azure.microsoft.com/updates/?product=active-directory)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-active-directory)
## [ビデオ](https://azure.microsoft.com/documentation/videos/index/?services=active-directory)
