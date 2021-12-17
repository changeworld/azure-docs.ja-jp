---
title: Azure Active Directory B2C を使用して iOS Swift モバイル アプリケーション オプションを有効にする
description: この記事では、Azure Active Directory B2C を使用して iOS Swift モバイル アプリケーション オプションを有効にするいくつかの方法について説明します。
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 07/29/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: b96e33bedcd4f92b6d75ecea02483e2d22c62ac8
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130041084"
---
# <a name="enable-authentication-options-in-an-ios-swift-app-by-using-azure-ad-b2c"></a>Azure AD B2C を使用して iOS Swift アプリの認証オプションを有効にする 

この記事では、iOS Swift アプリケーション用に Azure Active Directory B2C (Azure AD B2C) の認証エクスペリエンスを有効化、カスタマイズ、および拡張する方法について説明します。 

開始する前に、次の記事の内容をご確認ください。 
* [Azure AD B2C を使用してサンプル iOS Swift アプリケーションで認証を構成する](configure-authentication-sample-ios-app.md)
* [Azure AD B2C を使用して独自の iOS Swift アプリケーションで認証を有効にする](enable-authentication-ios-app.md)

[!INCLUDE [active-directory-b2c-app-integration-custom-domain](../../includes/active-directory-b2c-app-integration-custom-domain.md)]

認証 URL でカスタム ドメインとテナント ID を使用するには、次の操作を行います。 

1. [カスタム ドメインの有効化](custom-domain.md)に関する記事のガイダンスに従います。
1. `kAuthorityHostName` クラス メンバーをご利用のカスタム ドメインで更新します。
1. `kTenantName` クラス メンバーをご利用の[テナント ID](tenant-management.md#get-your-tenant-id) で更新します。

次の Swift コードは、変更前のアプリ設定を示しています。

```swift
let kTenantName = "contoso.onmicrosoft.com" 
let kAuthorityHostName = "contoso.b2clogin.com" 
```

次の Swift コードは、変更後のアプリ設定を示しています。

```swift
let kTenantName = "00000000-0000-0000-0000-000000000000" 
let kAuthorityHostName = "login.contoso.com" 
```

[!INCLUDE [active-directory-b2c-app-integration-login-hint](../../includes/active-directory-b2c-app-integration-login-hint.md)]

1. カスタム ポリシーを使用している場合は、[直接サインインの設定](direct-signin.md#prepopulate-the-sign-in-name)に関する記事の説明に従って、必要な入力要求を追加します。 
1. Microsoft Authentication Library (MSAL) 構成オブジェクトを探し、ログイン ヒントを使用して `withLoginHint()` メソッドを追加します。

```swift
let parameters = MSALInteractiveTokenParameters(scopes: kScopes, webviewParameters: self.webViewParameters!)
parameters.promptType = .selectAccount
parameters.authority = authority
parameters.loginHint = "bob@contoso.com"
// More settings here

applicationContext.acquireToken(with: parameters) { (result, error) in
...
```

[!INCLUDE [active-directory-b2c-app-integration-domain-hint](../../includes/active-directory-b2c-app-integration-domain-hint.md)]

1. 外部 ID プロバイダーのドメイン名を確認します。 詳細については、「[サインインをソーシャル プロバイダーにリダイレクトする](direct-signin.md#redirect-sign-in-to-a-social-provider)」を参照してください。 
1. リスト オブジェクトを作成するか既存のものを使用して、追加のクエリ パラメーターを格納します。
1. 対応するドメイン名を含む `domain_hint` パラメーターをリストに追加します (例: `facebook.com`)。
1. 追加のクエリ パラメーター リストを MSAL 構成オブジェクトの `extraQueryParameters` 属性に渡します。

```swift
let extraQueryParameters: [String: String] = ["domain_hint": "facebook.com"]

let parameters = MSALInteractiveTokenParameters(scopes: kScopes, webviewParameters: self.webViewParameters!)
parameters.promptType = .selectAccount
parameters.authority = authority
parameters.extraQueryParameters = extraQueryParameters
// More settings here

applicationContext.acquireToken(with: parameters) { (result, error) in
...
```

[!INCLUDE [active-directory-b2c-app-integration-ui-locales](../../includes/active-directory-b2c-app-integration-ui-locales.md)]

1. [言語のカスタマイズを構成します](language-customization.md)。
1. リスト オブジェクトを作成するか既存のものを使用して、追加のクエリ パラメーターを格納します。
1. 対応する言語コードを含む `ui_locales` パラメーターをリストに追加します (例: `en-us`)。
1. 追加のクエリ パラメーター リストを MSAL 構成オブジェクトの `extraQueryParameters` 属性に渡します。

```swift
let extraQueryParameters: [String: String] = ["ui_locales": "en-us"]

let parameters = MSALInteractiveTokenParameters(scopes: kScopes, webviewParameters: self.webViewParameters!)
parameters.promptType = .selectAccount
parameters.authority = authority
parameters.extraQueryParameters = extraQueryParameters
// More settings here

applicationContext.acquireToken(with: parameters) { (result, error) in
...
```

[!INCLUDE [active-directory-b2c-app-integration-custom-parameters](../../includes/active-directory-b2c-app-integration-custom-parameters.md)]

1. [ContentDefinitionParameters](customize-ui-with-html.md#configure-dynamic-custom-page-content-uri) 要素を構成します。
1. リスト オブジェクトを作成するか既存のものを使用して、追加のクエリ パラメーターを格納します。
1. `campaignId` などのカスタム クエリ文字列パラメーターを追加します。 パラメーター値を設定します (例: `germany-promotion` )。
1. 追加のクエリ パラメーター リストを MSAL 構成オブジェクトの `extraQueryParameters` 属性に渡します。

```swift
let extraQueryParameters: [String: String] = ["campaignId": "germany-promotion"]

let parameters = MSALInteractiveTokenParameters(scopes: kScopes, webviewParameters: self.webViewParameters!)
parameters.promptType = .selectAccount
parameters.authority = authority
parameters.extraQueryParameters = extraQueryParameters
// More settings here

applicationContext.acquireToken(with: parameters) { (result, error) in
...
```


[!INCLUDE [active-directory-b2c-app-integration-id-token-hint](../../includes/active-directory-b2c-app-integration-id-token-hint.md)]

1. カスタム ポリシーで、[ID トークン ヒントの技術プロファイル](id-token-hint.md)を定義します。
1. コードで、ID トークンを生成または取得してから、そのトークンを変数に設定します (例: `idToken`)。 
1. リスト オブジェクトを作成するか既存のものを使用して、追加のクエリ パラメーターを格納します。
1. ID トークンを格納する、対応する変数が含まれる `id_token_hint` パラメーターを追加します。
1. 追加のクエリ パラメーター リストを MSAL 構成オブジェクトの `extraQueryParameters` 属性に渡します。

```swift
let extraQueryParameters: [String: String] = ["id_token_hint": idToken]

let parameters = MSALInteractiveTokenParameters(scopes: kScopes, webviewParameters: self.webViewParameters!)
parameters.promptType = .selectAccount
parameters.authority = authority
parameters.extraQueryParameters = extraQueryParameters
// More settings here

applicationContext.acquireToken(with: parameters) { (result, error) in
...
```

[!INCLUDE [active-directory-b2c-app-integration-logging](../../includes/active-directory-b2c-app-integration-logging.md)]


MSAL ロガーは、MSAL 要求を行う前の、アプリ起動シーケンスの可能な限り早い段階で設定する必要があります。 *AppDelegate.swift* `application` メソッドで MSAL [ログ](../active-directory/develop/msal-logging-ios.md)を構成します。

次のコード スニペットでは、MSAL ログを構成する方法を示します。

```swift
func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool {
        
        MSALGlobalConfig.loggerConfig.logLevel = .verbose
        MSALGlobalConfig.loggerConfig.setLogCallback { (logLevel, message, containsPII) in
            
            // If PiiLoggingEnabled is set YES, this block will potentially contain sensitive information (Personally Identifiable Information), but not all messages will contain it.
            // containsPII == YES indicates if a particular message contains PII.
            // You might want to capture PII only in debug builds, or only if you take necessary actions to handle PII properly according to legal requirements of the region
            if let displayableMessage = message {
                if (!containsPII) {
                    #if DEBUG
                    // NB! This sample uses print just for testing purposes
                    // You should only ever log to NSLog in debug mode to prevent leaking potentially sensitive information
                    print(displayableMessage)
                    #endif
                }
            }
        }
        return true
    }
```

## <a name="embedded-web-view-experience"></a>埋め込み Web ビュー エクスペリエンス

対話型の認証には Web ブラウザー が必要です。 既定では、MSAL ライブラリはシステム Web ビューを使用します。 サインイン中に、MSAL ライブラリにより、Azure AD B2C ユーザー インターフェイスを使用した iOS システム Web ビューがポップアップ表示されます。  

詳細については、[iOS/macOS のブラウザーと WebView のカスタマイズ](../active-directory/develop/customize-webviews.md)に関する記事を参照してください。

ユーザーの要件によっては、埋め込み Web ビューを使用することもできます。 埋め込み Web ビューと MSAL のシステム Web ビューには、ビジュアルとシングル サインオンの動作に違いがあります。

![システム Web ビュー エクスペリエンスと埋め込み Web ビュー エクスペリエンスの違いを示すスクリーンショット。](./media/enable-authentication-ios-app-options/system-web-browser-vs-embedded-view.png)

> [!IMPORTANT]
> プラットフォームの既定値 (通常はシステム ブラウザー) を使用することをお勧めします。 システム ブラウザーは、以前にログインしたことのあるユーザーを記憶するのに適しています。 Google などの一部の ID プロバイダーでは、埋め込みビュー エクスペリエンスがサポートされていません。

この動作を変更するには、`MSALWebviewParameters` の `webviewType` 属性を `wkWebView` に変更します。 次の例で、ビューの種類を Web ビューから埋め込みビューに変更する方法を示します。 

```swift
func initWebViewParams() {
    self.webViewParameters = MSALWebviewParameters(authPresentationViewController: self)
    
    // Use embedded view experience
    self.webViewParameters?.webviewType = .wkWebView
}
```

## <a name="next-steps"></a>次の手順

- 詳細については、[iOS Swift 用 MSAL の構成オプション](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki)に関するページを参照してください。
