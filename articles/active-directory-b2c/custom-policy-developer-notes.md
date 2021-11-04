---
title: ユーザー フローとカスタム ポリシーの開発者向けのメモ
titleSuffix: Azure AD B2C
description: ユーザー フローとカスタム ポリシーで Azure AD B2C を構成および管理する開発者向けのメモ。
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 11/02/2021
ms.custom: project-no-code
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: 6221566c83f50891792bd037a82b90d29fd76c68
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131424460"
---
# <a name="developer-notes-for-azure-active-directory-b2c"></a>Azure Active Directory B2C の開発者向けのメモ

Azure Active Directory B2C の[ユーザー フローとカスタム ポリシー](user-flow-overview.md)が一般提供されました。 Azure AD B2C 機能は継続的に開発されているので、ほとんどの機能は一般提供されていますが、一部の機能はソフトウェア リリース サイクルの異なる段階にあります。 この記事では、Azure AD B2C の累積的な改善について説明し、機能の可用性を指定します。

## <a name="terms-for-features-in-public-preview"></a>パブリック プレビュー段階の機能に対する使用条件

- パブリック プレビュー機能の使用は、評価目的のみで使用することが推奨されています。
- [サービス レベル アグリーメント (SLA)](https://azure.microsoft.com/support/legal/sla/active-directory-b2c) はパブリック プレビュー機能には適用されません。
- パブリック プレビュー機能に対するサポート要求は、通常のサポート チャネルを通じて提出できます。

## <a name="user-flows"></a>ユーザー フロー

|機能  |ユーザー フロー  |カスタム ポリシー  |Notes  |
|---------|:---------:|:---------:|---------|
| メールとパスワードによる[サインアップとサインイン](add-sign-up-and-sign-in-policy.md)。 | GA | GA| |
| ユーザー名とパスワードによる[サインアップとサインイン](add-sign-up-and-sign-in-policy.md)。| GA | GA | |
| [プロファイル編集フロー](add-profile-editing-policy.md) | GA | GA | |
| [セルフサービス パスワード リセット](add-password-reset-policy.md) | GA| GA| |
| [パスワードの強制的なリセット](force-password-reset.md) | GA | NA | |
| [電話によるサインアップとサインイン](phone-authentication-user-flows.md) | GA | GA | |
| [条件付きアクセスと ID 保護](conditional-access-user-flow.md) | GA | GA | SAML アプリケーションでは使用できません |

## <a name="oauth-20-application-authorization-flows"></a>OAuth 2.0 アプリケーション認可フロー

Azure AD B2C と連携できる OAuth 2.0 および OpenId Connect アプリケーションの認証フローを、次の表にまとめています。

|機能  |ユーザー フロー  |カスタム ポリシー  |Notes  |
|---------|:---------:|:---------:|---------|
[承認コード](authorization-code-flow.md) | GA | GA | ユーザーが Web アプリケーションにサインインできます。 Web アプリケーションで認可コードを受け取ります。 承認コードと引き換えに、Web API を呼び出すためのトークンを取得します。|
[認可コードと PKCE](authorization-code-flow.md)| GA | GA | ユーザーが、モバイルおよびシングルページ アプリケーションにサインインできます。 アプリケーションで Proof Key for Code Exchange (PKCE) を使用して認可コードを受け取ります。 承認コードと引き換えに、Web API を呼び出すためのトークンを取得します。  |
[クライアント資格情報付与](https://tools.ietf.org/html/rfc6749#section-4.4)| GA | GA | アプリケーションの ID を使用して Web 上のリソースにアクセスできます。 バックグラウンドでの実行が必要なサーバー間の相互作用に使用され、ユーザーとの即時の相互動作は必要ありません。  <br />  <br />  Azure AD B2C テナントでこの機能を使うには、Azure AD B2C テナントの Azure AD エンドポイントを使用します。 詳しくは、[OAuth 2.0 のクライアント資格情報フロー](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md)に関する記事をご覧ください。 このフローでは、Azure AD B2C の[ユーザー フローおよびカスタム ポリシー](user-flow-overview.md)の設定を使用しません。 |
[デバイス認可付与](https://tools.ietf.org/html/rfc8628)| NA | NA | ユーザーは、スマート TV、IoT デバイス、プリンターなどの入力制限のあるデバイスにサインインできます。  |
[暗黙的なフロー](implicit-flow-single-page-application.md) | GA | GA |  ユーザーがシングルページ アプリケーションにサインインできます。 バック エンド サーバーと資格情報のやりとりをせずに、アプリで使用するトークンを直接取得します。|
[On-Behalf-Of](../active-directory/develop/v2-oauth2-on-behalf-of-flow.md)| NA | NA | アプリケーションでサービスまたは Web API を呼び出し、それがさらに別のサービスまたは Web API を呼び出す必要があります。 <br />  <br /> 中間層のサービスから下流のサービスに対して認証要求を行うには、"*クライアント資格情報*" トークンを認可ヘッダーに埋め込みます。 必要に応じて、Azure AD B2C ユーザーのトークンを含むカスタム ヘッダーも使用できます。  |
[OpenID Connect](openid-connect.md) | GA | GA | OpenID Connect には、ID トークンの概念が導入されています。ID トークンとは、クライアントがユーザーの本人性を確認できるセキュリティ トークンです。 |
[OpenId Connect ハイブリッド フロー](openid-connect.md) | GA | GA | Web アプリケーションから認可要求を行って ID トークンと認可コードを取得できます。  |
[リソース所有者のパスワード資格情報 (ROPC)](add-ropc-policy.md) | GA | GA | モバイル アプリケーションで直接パスワードを処理して、ユーザーをサインインさせることができます。 |
| [サインアウト](session-behavior.md#sign-out)| GA | GA | |
| [シングル サインアウト](session-behavior.md#sign-out)  | NA | プレビュー | |

### <a name="oauth-20-options"></a>OAuth 2.0 のオプション

|機能  |ユーザー フロー  |カスタム ポリシー  |Notes  |
|---------|:---------:|:---------:|---------|
| [サインインをソーシャル プロバイダーにリダイレクトする](direct-signin.md#redirect-sign-in-to-a-social-provider) | GA | GA | クエリ文字列パラメーター `domain_hint`。 |
| [サインイン名を事前入力する](direct-signin.md#prepopulate-the-sign-in-name) | GA | GA | クエリ文字列パラメーター `login_hint`。 |
| `client_assertion` を使用して JSON をユーザー体験に挿入| NA| 非推奨 |  |
| [id_token_hint](id-token-hint.md) として JSON をユーザー体験に挿入する | NA | GA | |
| [ID プロバイダー トークンをアプリケーションに渡す](idp-pass-through-user-flow.md)| プレビュー| プレビュー| 例: Facebook からアプリへ。 |
| [サインインしたままにする (KMSI)](session-behavior.md#enable-keep-me-signed-in-kmsi)| GA| GA| |

## <a name="saml2-application-authentication-flows"></a>SAML2 アプリケーションの認証フロー

Azure AD B2C と連携できる Security Assertion Markup Language (SAML) アプリケーションの認証フローを、次の表にまとめています。

|機能  |ユーザー フロー  |カスタム ポリシー  |Notes  |
|---------|:---------:|:---------:|---------|
[SP 開始](saml-service-provider.md) | NA | GA | POST とリダイレクトのバインディング。 |
[IDP 開始](saml-service-provider-options.md#configure-idp-initiated-flow) | NA | GA | ID プロバイダーは Azure AD B2C です。  |

## <a name="user-experience-customization"></a>ユーザー エクスペリエンスのカスタマイズ

|機能  |ユーザー フロー  |カスタム ポリシー  |Notes  |
|---------|:---------:|:---------:|---------|
| [複数言語のサポート](localization.md)| GA | GA | |
| [カスタム ドメイン](custom-domain.md)| GA | GA | |
| [カスタム メール確認](custom-email-mailjet.md) | NA | GA| |
| [組み込みテンプレートを使用したユーザー インターフェイスのカスタマイズ](customize-ui.md) | GA| GA| |
| [カスタム テンプレートを使用したユーザー インターフェイスのカスタマイズ](customize-ui-with-html.md) | GA| GA| HTML テンプレートを使用します。 |
| [ページ レイアウト バージョン](page-layout.md) | GA | GA | |
| [JavaScript](javascript-and-page-layout.md) | GA | GA | |
| [埋め込みサインイン エクスペリエンス](embedded-login.md) | NA |  プレビュー| インライン フレーム要素の `<iframe>` を使用します。 |
| [パスワードの複雑さ](password-complexity.md) | GA | GA | |
| [メールの確認を無効にする](disable-email-verification.md) | GA|  GA| 運用環境での使用はお勧めしません。 サインアップ プロセスでの電子メールの検証を無効にすると、スパムにつながる場合があります。 |




## <a name="identity-providers"></a>ID プロバイダー

|機能  |ユーザー フロー  |カスタム ポリシー  |Notes  |
|---------|:---------:|:---------:|---------|
|[AD FS](identity-provider-adfs.md) | NA | GA | |
|[Amazon](identity-provider-amazon.md) | GA | GA | |
|[Apple](identity-provider-apple-id.md) | GA | GA | |
|[Azure AD (シングルテナント)](identity-provider-azure-ad-single-tenant.md) | GA | GA | |
|[Azure AD (マルチテナント)](identity-provider-azure-ad-multi-tenant.md) | NA  | GA | |
|[Azure AD B2C](identity-provider-azure-ad-b2c.md) | GA | GA | |
|[eBay](identity-provider-ebay.md) | NA | プレビュー | |
|[Facebook](identity-provider-facebook.md) | GA | GA | |
|[GitHub](identity-provider-github.md) | プレビュー | GA | |
|[Google](identity-provider-google.md) | GA | GA | |
|[ID.me](identity-provider-id-me.md) | GA | GA | |
|[LinkedIn](identity-provider-linkedin.md) | GA | GA | |
|[Microsoft アカウント](identity-provider-microsoft-account.md) | GA | GA | |
|[QQ](identity-provider-qq.md) | プレビュー | GA | |
|[Salesforce](identity-provider-salesforce.md) | GA | GA | |
|[Salesforce (SAML プロトコル)](identity-provider-salesforce-saml.md) | NA | GA | |
|[Twitter](identity-provider-twitter.md) | GA | GA | |
|[WeChat](identity-provider-wechat.md) | プレビュー | GA | |
|[Weibo](identity-provider-weibo.md) | プレビュー | GA | |

## <a name="generic-identity-providers"></a>汎用の ID プロバイダー

|機能  |ユーザー フロー  |カスタム ポリシー  |Notes  |
|---------|:---------:|:---------:|---------|
|[OAuth2](oauth2-technical-profile.md) | NA | GA | たとえば、[Google](identity-provider-google.md)、[GitHub](identity-provider-github.md)、[Facebook](identity-provider-facebook.md) などです。|
|[OAuth1](oauth1-technical-profile.md) | NA | GA | たとえば、「[Twitter](identity-provider-twitter.md)」とします。 |
|[OpenID Connect](openid-connect-technical-profile.md) | GA | GA | たとえば、[Azure AD](identity-provider-azure-ad-single-tenant.md) などです。  |
|[SAML2](identity-provider-generic-saml.md) | NA | GA | たとえば、[Salesforce](identity-provider-salesforce-saml.md) と [AD-FS](identity-provider-adfs.md) などです。 |
| WSFED | NA | NA | |

### <a name="api-connectors"></a>API コネクタ

|機能  |ユーザー フロー  |カスタム ポリシー  |Notes  |
|---------|:---------:|:---------:|---------|
|[API コネクタ](api-connectors-overview.md) | プレビュー | GA | |
|[基本認証を使用したセキュリティ保護](secure-rest-api.md#http-basic-authentication) | プレビュー | GA | |
|[クライアント証明書認証を使用したセキュリティ保護](secure-rest-api.md#https-client-certificate-authentication) | プレビュー | GA | |
|[OAuth2 ベアラー認証を使用したセキュリティ保護](secure-rest-api.md#oauth2-bearer-authentication) | NA | GA | |
|[API キー認証のセキュリティ保護](secure-rest-api.md#api-key-authentication) | NA | GA | |


## <a name="custom-policy-features"></a>カスタム ポリシー機能

### <a name="session-management"></a>セッションの管理

| 機能 |  カスタム ポリシー | Notes |
| ------- | :--: | ----- |
| [既定の SSO セッション プロバイダー](custom-policy-reference-sso.md#defaultssosessionprovider) | GA |  |
| [外部ログイン セッション プロバイダー](custom-policy-reference-sso.md#externalloginssosessionprovider) | GA |  |
| [SAML SSO セッション プロバイダー](custom-policy-reference-sso.md#samlssosessionprovider) | GA |  |
| [OAuth SSO セッション プロバイダー](custom-policy-reference-sso.md#oauthssosessionprovider)  | GA|  |


### <a name="components"></a>Components

| 機能 | カスタム ポリシー | Notes |
| ------- | :--: | ----- |
| [電話要素認証](phone-factor-technical-profile.md) | GA |  |
| [Azure AD MFA 認証](multi-factor-auth-technical-profile.md) | プレビュー |  |
| [ワンタイム パスワード](one-time-password-technical-profile.md) | GA |  |
| ローカル ディレクトリとしての [Azure Active Directory](active-directory-technical-profile.md) | GA |  |
| [述語の検証](predicates.md) | GA | 例: パスワードの複雑さ。 |
| [表示コントロール](display-controls.md) | GA |  |
| [サブ体験](subjourneys.md) | GA | |

### <a name="developer-interface"></a>開発者向けインターフェイス

| 機能 | カスタム ポリシー | Notes |
| ------- | :--: | ----- |
| Azure portal | GA |   |
| [Application Insights のユーザー体験ログ](troubleshoot-with-application-insights.md) | プレビュー | 開発中のトラブルシューティングに使用される。  |
| [Application Insights のイベント ログ](analytics-with-application-insights.md) | プレビュー | 運用環境内でユーザー フローを監視するために使用される。 |

## <a name="responsibilities-of-custom-policy-feature-set-developers"></a>カスタム ポリシー機能セット開発者の責任

手動のポリシー構成で付与される Azure AD B2C の基になるプラットフォームへのアクセス レベルは低くなるため、信頼できる一意のフレームワークが作成されます。 カスタム ID プロバイダー、信頼関係、外部サービスとの統合、ステップバイステップのワークフローの起こり得る多くの配列には、設計および構成に対する系統的なアプローチが必要です。

カスタム ポリシーの機能セットを使用する開発者は、次のガイドラインに従う必要があります。

- カスタム ポリシーとキー/シークレット管理の構成言語について理解を深める。 詳細については、「[TrustFrameworkPolicy](trustframeworkpolicy.md)」を参照してください。
- シナリオおよびカスタム統合の主導権を得る。 ご自分の作業内容を文書化し、ライブ サイト組織に通知します。
- シナリオの体系的なテストを実行する。
- ソフトウェア開発とステージングのベスト プラクティスに従ってください。 少なくとも 1 つの開発およびテスト環境が推奨されます。
- ID プロバイダーによる新しい開発と、統合するサービスに関する情報を常に入手する。 たとえば、シークレットの変更や、サービスの予定された変更と予定外の変更を把握します。
- アクティブな監視を設定し、運用環境の応答性を監視する。 Application Insights との統合の詳細については、[Azure Active Directory B2C: ログの収集](analytics-with-application-insights.md)に関するページを参照してください。
- Azure サブスクリプションの連絡先のメール アドレスを最新に保ち、Microsoft のライブ サイト チームのメールに対応できるようにしておく。
- Microsoft ライブ サイト チームからのアドバイスがあった場合に、すぐに対処する。

## <a name="next-steps"></a>次のステップ

- [Azure AD B2C に使用可能な Microsoft Graph 操作](microsoft-graph-operations.md)を確認します。
- [カスタム ポリシーについて、およびユーザー フローとの違い](custom-policy-overview.md)について学習します。
