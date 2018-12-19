# [Azure Active Directory のドキュメント](index.md)

# 概要
## [Azure Active Directory とは](fundamentals/active-directory-whatis.md)
## [Azure の ID 管理について](fundamentals/identity-fundamentals.md)
## [Azure ID ソリューションについて](fundamentals/understand-azure-identity-solutions.md)
## [Azure サブスクリプションを関連付ける](fundamentals/active-directory-how-subscriptions-associated-directory.md)
## [保存場所とデータに関する考慮事項](fundamentals/active-directory-data-storage-eu.md)
## [FAQ](fundamentals/active-directory-faq.md)
## [最新情報](fundamentals/whats-new.md)


# 作業開始
## [Azure AD Premium へのサインアップ](fundamentals/active-directory-get-started-premium.md)
## [カスタム ドメイン名の追加](fundamentals/add-custom-domain.md)
## [会社のブランドの構成](fundamentals/customize-branding.md)
## [Azure AD へのユーザーの追加](fundamentals/add-users-azure-active-directory.md)
## [ユーザーへのライセンスの割り当て](fundamentals/license-users-groups.md)
## [セルフサービスによるパスワードのリセットの構成](authentication/quickstart-sspr.md)
## [Azure AD での組織のプライバシーに関する情報の追加](active-directory-properties-area.md)
## [Azure Active Directory にアクセスして新しいテナントを作成する](fundamentals/active-directory-access-create-new-tenant.md)


# 方法
## 計画と設計
### [Azure AD のアーキテクチャを理解する](fundamentals/active-directory-architecture.md)
### [Azure Active Directory での要求マッピング](develop/active-directory-claims-mapping.md)
### [ハイブリッド ID ソリューションをデプロイする](hybrid/plan-hybrid-identity-design-considerations-overview.md)
#### 要件を確認する
##### [ID](hybrid/plan-hybrid-identity-design-considerations-business-needs.md)
##### [ディレクトリ同期](hybrid/plan-hybrid-identity-design-considerations-directory-sync-requirements.md)
##### [多要素認証](hybrid/plan-hybrid-identity-design-considerations-multifactor-auth-requirements.md)
##### [ID のライフサイクル戦略](hybrid/plan-hybrid-identity-design-considerations-lifecycle-adoption-strategy.md)
#### [データ セキュリティを計画する](hybrid/plan-hybrid-identity-design-considerations-data-protection-strategy.md)
##### [データ保護](hybrid/plan-hybrid-identity-design-considerations-dataprotection-requirements.md)
##### [コンテンツ管理](hybrid/plan-hybrid-identity-design-considerations-contentmgt-requirements.md)
##### [アクセス制御](hybrid/plan-hybrid-identity-design-considerations-accesscontrol-requirements.md)
##### [インシデント対応](hybrid/plan-hybrid-identity-design-considerations-incident-response-requirements.md)
#### ID ライフサイクルを計画する
##### [タスク](hybrid/plan-hybrid-identity-design-considerations-hybrid-id-management-tasks.md)
##### [採用戦略](hybrid/plan-hybrid-identity-design-considerations-identity-adoption-strategy.md)
#### [次のステップ](hybrid/plan-hybrid-identity-design-considerations-nextsteps.md)
#### [ツールの比較](hybrid/plan-hybrid-identity-design-considerations-tools-comparison.md)

## ユーザーの管理
### [Azure AD への新しいユーザーの追加](fundamentals/add-users-azure-active-directory.md)
### [ユーザー プロファイルの管理](fundamentals/active-directory-users-profile-azure-portal.md)
### [ユーザー パスワードのリセット](fundamentals/active-directory-users-reset-password-azure-portal.md)
### [管理者ロールへのユーザーの割り当て](fundamentals/active-directory-users-assign-role-azure-portal.md)
### [別のディレクトリからのゲスト ユーザーの追加 (B2B)](b2b/what-is-b2b.md)
#### [管理者による B2B ユーザーの追加](b2b/add-users-administrator.md)
#### [インフォメーション ワーカーによる B2B ユーザーの追加](b2b/add-users-information-worker.md)
#### [API とカスタマイズ](b2b/customize-invitation-api.md)
#### [Google のフェデレーション](b2b/google-federation.md)
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
### [グループとそのメンバーの削除](fundamentals/active-directory-groups-delete-group.md)
### [グループ設定の管理](fundamentals/active-directory-groups-settings-azure-portal.md)
## [レポートの管理](reports-monitoring/overview-reports.md)
### [サインイン アクティビティ](reports-monitoring/concept-sign-ins.md)
### [監査アクティビティ](reports-monitoring/concept-audit-logs.md)
### [危険な状態のユーザー](reports-monitoring/concept-user-at-risk.md)
### [リスクの高いサインイン](reports-monitoring/concept-risky-sign-ins.md)
### [リスク イベント](reports-monitoring/concept-risk-events.md)
### [Azure Monitor を使用したログの監視](reports-monitoring/concept-activity-logs-in-azure-monitor.md)
### [FAQ](reports-monitoring/reports-faq.md)

### タスク
#### [サインイン レポートのダウンロード](reports-monitoring/quickstart-download-sign-in-report.md)
#### [監査レポートのダウンロード](reports-monitoring/quickstart-download-audit-report.md)
#### [名前付きの場所の構成](reports-monitoring/quickstart-configure-named-locations.md)
#### [アクティビティ レポートの検出](reports-monitoring/howto-find-activity-reports.md)
#### [Azure AD Power BI コンテンツ パックの使用](reports-monitoring/howto-power-bi-content-pack.md)
#### [リスクのフラグ付きユーザーの修復](reports-monitoring/howto-remediate-users-flagged-for-risk.md)
#### [Azure イベント ハブへのアクティビティ ログのルーティング](reports-monitoring/quickstart-azure-monitor-stream-logs-to-event-hub.md)
#### [Azure ストレージ アカウントへのアクティビティ ログのアーカイブ](reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md)
#### [Azure Monitor を使用したアクティビティ ログと Splunk の統合](reports-monitoring/tutorial-integrate-activity-logs-with-splunk.md)
#### [Azure Monitor を使用したアクティビティ ログと SumoLogic の統合](reports-monitoring/howto-integrate-activity-logs-with-sumologic.md)
#### [Azure Monitor を使用したアクティビティ ログと Log Analytics の統合](reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

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

## アプリを管理する
### [概要](manage-apps/what-is-application-management.md)
### [使用の開始](manage-apps/plan-an-application-integration.md)
### [SaaS アプリの統合に関するチュートリアル](saas-apps/tutorial-list.md)

### [SaaS アプリへのユーザーのプロビジョニングとプロビジョニング解除](manage-apps/user-provisioning.md) 
#### [アプリの統合に関するチュートリアル](saas-apps/tutorial-list.md) 
#### [SCIM 対応アプリへのプロビジョニングの自動化](manage-apps/use-scim-to-provision-users-and-groups.md) 
#### [属性マッピングのカスタマイズ](manage-apps/customize-application-attributes.md) 
#### [属性マッピングの式の書き方](manage-apps/functions-for-customizing-application-data.md) 
#### [スコープ フィルターの使用](manage-apps/define-conditional-rules-for-provisioning-user-accounts.md) 
#### [自動ユーザー プロビジョニングについてのレポート](manage-apps/check-status-user-account-provisioning.md) 
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
##### [ワイルドカード](manage-apps/application-proxy-wildcard.md)
##### [個人データの削除](manage-apps/application-proxy-remove-personal-data.md)


#### 発行に関するチュートリアル
##### [リモート デスクトップ](manage-apps/application-proxy-integrate-with-remote-desktop-services.md)
##### [SharePoint](manage-apps/application-proxy-integrate-with-sharepoint-server.md)
##### [Microsoft Teams](manage-apps/application-proxy-integrate-with-teams.md)
##### [Tableau](manage-apps/application-proxy-integrate-with-tableau.md)
##### [Qlik](manage-apps/application-proxy-qlik.md)
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

### [Azure AD アプリケーションの同意エクスペリエンスについて](develop/application-consent-experience.md)

### トラブルシューティング



#### アクセス パネル
##### [アプリが表示されない](manage-apps/access-panel-troubleshoot-application-not-appearing.md)
##### [予期しないアプリが表示される](manage-apps/access-panel-troubleshoot-unexpected-application.md)
##### [サインインできない](manage-apps/access-panel-troubleshoot-web-sign-in-problem.md)
##### [ブラウザー拡張機能のインストール時のエラー](manage-apps/access-panel-extension-problem-installing.md)
##### [セルフサービス アプリ アクセスの使用方法](manage-apps/access-panel-manage-self-service-access.md)
##### [セルフサービス アプリ アクセスの使用時のエラー](manage-apps/access-panel-troubleshoot-self-service-access.md)

#### アプリの追加
##### [アプリの種類の選択](manage-apps/choose-application-type.md)
##### [一般的な問題 - ギャラリー アプリ](manage-apps/adding-gallery-app-common-problems.md)
##### [一般的な問題 - ギャラリー以外のアプリ](manage-apps/adding-non-gallery-app-common-problems.md)

#### アプリケーション プロキシ
##### [アプリ ページを表示できない](manage-apps/application-proxy-page-appearance-broken-problem.md)
##### [アプリケーション読み込みに時間がかかりすぎる](manage-apps/application-proxy-page-load-speed-problem.md)
##### [アプリケーション ページ上のリンクが動作しない](manage-apps/application-proxy-page-links-broken-problem.md)
##### [アプリに対して開く必要のあるポート](manage-apps/application-proxy-connectivity-ports-how-to.md)
##### [アプリのコネクタ グループに動作するコネクタがない](manage-apps/application-proxy-connectivity-no-working-connector.md)
##### [管理ポータルでの構成](manage-apps/application-proxy-config-how-to.md)
##### [アプリに対するシングル サインオンの構成](manage-apps/application-proxy-config-sso-how-to.md)
##### [管理ポータルでのアプリの作成に関する問題](manage-apps/application-proxy-config-problem.md)
##### [Kerberos の制約付き委任を構成する](manage-apps/application-proxy-back-end-kerberos-constrained-delegation-how-to.md)
##### [PingAccess を使用した構成](manage-apps/application-proxy-back-end-ping-access-how-to.md)
##### ["この企業アプリケーションにアクセスできない" というエラー](manage-apps/application-proxy-sign-in-bad-gateway-timeout-error.md)
##### [アプリケーション プロキシ エージェント コネクタのインストールに関する問題](manage-apps/application-proxy-connector-installation-problem.md)


#### アプリケーションの登録
##### [アプリケーション オブジェクトのフィールドの入力](develop/registration-config-specific-application-property-how-to.md)
##### [トークン有効期間の既定値の変更](develop/registration-config-change-token-lifetime-how-to.md)

#### Authentication
##### [エンドポイントの構成](develop/registration-config-how-to.md)

#### 条件付きアクセス
##### [顧客がデバイス登録の前提条件を満たしていない](active-directory-conditional-access.md)
##### [企業ネットワークのルールを無効にする方法とタイミング](https://aka.ms/calocation)
##### [Azure AD でユーザーが登録できるデバイス数を増やす方法](active-directory-azureadjoin-setup.md)
##### [Exchange Online の条件付きアクセスの設定方法](https://aka.ms/csforexchange)
##### [Windows 7 デバイスの条件付きアクセスの設定方法](active-directory-conditional-access.md#device-based-conditional-access)
##### [条件付きアクセスでサポートされるアプリケーション](active-directory-conditional-access-supported-apps.md)

#### API の検索
##### [API の検索](develop/api-find-an-api-how-to.md)

#### アクセスの管理
##### [アプリへのユーザーとグループの割り当て](manage-apps/methods-for-assigning-users-and-groups.md)
##### [アプリへのユーザー アクセスの削除](manage-apps/methods-for-removing-user-access.md)
##### [セルフサービス アプリの割り当ての構成](manage-apps/manage-self-service-access.md)
##### [予期しないユーザーが割り当てられる](manage-apps/ways-users-get-assigned-to-applications.md)
##### [アプリケーション リストの予期しないアプリ](manage-apps/application-types.md)

#### マルチテナント アプリ
##### [新しいアプリの構成](develop/setup-multi-tenant-app.md)
##### [アプリ ギャラリーへの追加](develop/registration-config-multi-tenant-application-add-to-gallery-how-to.md)

#### アクセス許可
##### [API のアクセス許可の選択](develop/perms-for-given-api.md)
##### [委任されたアクセス許可とアプリケーションのアクセス許可](develop/delegated-and-app-perms.md)
##### [アプリケーションへの同意](develop/consent-framework.md)

#### プロビジョニング
##### [所要時間](manage-apps/application-provisioning-when-will-provisioning-finish-specific-user.md)
##### [長時間かかる - ギャラリー アプリ](manage-apps/application-provisioning-when-will-provisioning-finish.md)
##### [ユーザー プロビジョニングの構成 - ギャラリー アプリ](manage-apps/application-provisioning-config-how-to.md)
##### [ユーザー プロビジョニングの構成に関する問題 - ギャラリー アプリ](manage-apps/application-provisioning-config-problem.md)
##### [ギャラリー アプリケーションへのユーザー プロビジョニングを構成中の管理者の資格情報の保存に関する問題](manage-apps/application-provisioning-config-problem-storage-limit.md)
##### [ユーザーがプロビジョニングされない - ギャラリー アプリ](manage-apps/application-provisioning-config-problem-no-users-provisioned.md)
##### [間違ったユーザーがプロビジョニングされている - ギャラリー アプリ](manage-apps/application-provisioning-config-problem-wrong-users-provisioned.md)
##### [SCIM 互換性に関する問題 - ギャラリー以外のアプリ](manage-apps/application-provisioning-config-problem-scim-compatibility.md)

#### シングル サインオン
##### [方法の選択](manage-apps/single-sign-on-modes.md)
##### [構成](develop/registration-config-sso-how-to.md)
##### [フェデレーションの構成 - ギャラリー アプリ](manage-apps/configure-federated-single-sign-on-gallery-applications.md)
##### [フェデレーションの構成の一般的な問題 - ギャラリー アプリ](manage-apps/configure-federated-single-sign-on-gallery-applications-problems.md)
##### [フェデレーションの構成 - ギャラリー以外のアプリ](manage-apps/configure-federated-single-sign-on-non-gallery-applications.md)
##### [フェデレーションの構成の一般的な問題 - ギャラリー以外のアプリ](manage-apps/configure-federated-single-sign-on-non-gallery-applications-problems.md)
##### [パスワードの構成 - ギャラリー アプリ](manage-apps/configure-password-single-sign-on-gallery-applications.md)
##### [パスワードの構成の一般的な問題 - ギャラリー アプリ](manage-apps/configure-password-single-sign-on-gallery-applications-problems.md)
##### [パスワードの構成 - ギャラリー以外のアプリ](manage-apps/configure-password-single-sign-on-non-gallery-applications.md)
##### [パスワードの構成の一般的な問題 - ギャラリー以外のアプリ](manage-apps/configure-password-single-sign-on-non-gallery-applications-problems.md)

#### ユーザー サインインに関する問題
##### [予期しない同意プロンプト](manage-apps/application-sign-in-unexpected-user-consent-prompt.md)
##### [ユーザーの同意エラー](manage-apps/application-sign-in-unexpected-user-consent-error.md)
##### [カスタム ポータルからのサインインに関する問題](manage-apps/application-sign-in-other-problem-deeplink.md)
##### [アクセス パネルからのサインインに関する問題](manage-apps/application-sign-in-other-problem-access-panel.md)
##### [アプリケーションのサインイン ページでのエラー](manage-apps/application-sign-in-problem-application-error.md)
##### [パスワード シングル サインオンに関する問題 - ギャラリー以外のアプリ](manage-apps/application-sign-in-problem-password-sso-non-gallery.md)
##### [パスワード シングル サインオンに関する問題 - ギャラリー アプリ](manage-apps/application-sign-in-problem-password-sso-gallery.md)
##### [Microsoft アプリへのサインインに関する問題](manage-apps/application-sign-in-problem-first-party-microsoft.md)
##### [フェデレーション シングル サインオンに関する問題 - ギャラリー以外のアプリ](manage-apps/application-sign-in-problem-federated-sso-non-gallery.md)
##### [フェデレーション シングル サインオンに関する問題 - ギャラリー アプリ](manage-apps/application-sign-in-problem-federated-sso-gallery.md)
##### [カスタム開発されたアプリに関する問題](manage-apps/application-sign-in-problem-custom-dev.md)
##### [オンプレミス アプリに関する問題 - アプリケーション プロキシの問題](manage-apps/application-sign-in-problem-on-premises-application-proxy.md)


## ディレクトリの管理
### [Azure AD Connect](hybrid/whatis-hybrid-identity.md)
### カスタム ドメイン名
#### [クイックスタート](fundamentals/add-custom-domain.md)
### [ディレクトリを管理する](fundamentals/active-directory-administer.md)
### [Azure AD Connect を使用してオンプレミスの ID を統合する](hybrid/whatis-hybrid-identity.md)

### [トークンの有効期間を構成する](develop/active-directory-configurable-token-lifetimes.md)

## ID をセキュリティで保護する

### [Privileged Identity Management](privileged-identity-management/pim-configure.md?toc=%2fazure%2factive-directory%2ftoc.json)



## [トラブルシューティング](fundamentals/active-directory-troubleshooting-support-howto.md)

## Azure AD の概念実証 (PoC) のデプロイ
### [PoC プレイブック:概要](active-directory-playbook-intro.md)
### [PoC プレイブック:内容](active-directory-playbook-ingredients.md)
### [PoC プレイブック:実装](active-directory-playbook-implementation.md)
### [PoC プレイブック:構成要素](active-directory-playbook-building-blocks.md)

# リファレンス
## [コード サンプル](https://azure.microsoft.com/resources/samples/?service=active-directory)
## [Azure PowerShell コマンドレット](/powershell/azure/overview)
## [Java API リファレンス](/java/api)
## [.NET API](/active-directory/adal/microsoft.identitymodel.clients.activedirectory)

# 関連項目
## [Multi-Factor Authentication](/azure/multi-factor-authentication/)
## [Azure AD Connect](hybrid/whatis-hybrid-identity.md)
## [Azure AD Connect Health](hybrid/whatis-hybrid-identity-health.md)
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
## [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-active-directory)
## [ビデオ](https://azure.microsoft.com/documentation/videos/index/?services=active-directory)
