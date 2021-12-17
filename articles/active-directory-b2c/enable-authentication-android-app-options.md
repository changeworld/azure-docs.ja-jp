---
title: Azure Active Directory B2C を使用して Android モバイル アプリケーション オプションを有効にする
description: この記事では、Azure Active Directory B2C を使用して Android モバイル アプリケーション オプションを有効にするいくつかの方法について説明します。
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 11/11/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: 8631c5c0852f915596e3b51c76054301eca64c02
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132289199"
---
# <a name="configure-authentication-options-in-an-android-app-by-using-azure-ad-b2c"></a>Azure AD B2C を使用して Android アプリで認証オプションを構成する 

この記事では、Android アプリケーションの Azure Active Directory B2C (Azure AD B2C) 認証エクスペリエンスをカスタマイズおよび拡張する方法について説明します。 

開始する前に、次の記事の内容をご確認ください。 
* [Azure AD B2C を使用して サンプル Android アプリで認証オプションを構成する](configure-authentication-sample-android-app.md)
* [Azure AD B2C を使用して独自の Android アプリで認証を有効にする](enable-authentication-android-app.md)

[!INCLUDE [active-directory-b2c-app-integration-custom-domain](../../includes/active-directory-b2c-app-integration-custom-domain.md)]

認証 URL でカスタム ドメインとテナント ID を使用するには、[カスタム ドメインを有効にする](custom-domain.md)方法に関するページのガイダンスに従ってください。 Microsoft Authentication Library (MSAL) 構成オブジェクトを見つけて、*authorities* を実際のカスタム ドメイン名とテナント ID を使用するように変更します。


#### <a name="kotlin"></a>[Kotlin](#tab/kotlin)

次の Kotlin コードは、変更前の MSAL 構成オブジェクトを示しています。

```kotlin
val parameters = AcquireTokenParameters.Builder()
        .startAuthorizationFromActivity(activity)
        .fromAuthority("https://contoso.b2clogin.com/fabrikamb2c.contoso.com/B2C_1_susi")
        // More settings here
        .build()

b2cApp!!.acquireToken(parameters)
```

次の Kotlin コードは、変更後の MSAL 構成オブジェクトを示しています。

```kotlin
val parameters = AcquireTokenParameters.Builder()
        .startAuthorizationFromActivity(activity)
        .fromAuthority("https://custom.domain.com/00000000-0000-0000-0000-000000000000/B2C_1_susi")
        // More settings here
        .build()

b2cApp!!.acquireToken(parameters)
```


#### <a name="java"></a>[Java](#tab/java)

次の Kotlin コードは、変更前の MSAL 構成オブジェクトを示しています。

```java
AcquireTokenParameters parameters = new AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(getActivity())
    .fromAuthority("https://contoso.b2clogin.com/fabrikamb2c.contoso.com/B2C_1_susi")
    // More settings here
    .build();

b2cApp.acquireToken(parameters);
```
次の Kotlin コードは、変更後の MSAL 構成オブジェクトを示しています。

```java
AcquireTokenParameters parameters = new AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(getActivity())
    .fromAuthority("https://custom.domain.com/00000000-0000-0000-0000-000000000000/B2C_1_susi")
    // More settings here
    .build();

b2cApp.acquireToken(parameters);
```

--- 

[!INCLUDE [active-directory-b2c-app-integration-login-hint](../../includes/active-directory-b2c-app-integration-login-hint.md)]

1. カスタム ポリシーを使用している場合は、[直接サインインの設定](direct-signin.md#prepopulate-the-sign-in-name)に関する記事の説明に従って、必要な入力要求を追加します。 
1. MSAL 構成オブジェクトを見つけて、ログイン ヒントを含む `withLoginHint()` メソッドを追加します。

#### <a name="kotlin"></a>[Kotlin](#tab/kotlin)


```kotlin
val parameters = AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(activity)
    .withLoginHint("bob@contoso.com") 
    // More settings here
    .build()

b2cApp!!.acquireToken(parameters)
```

#### <a name="java"></a>[Java](#tab/java)

```java
AcquireTokenParameters parameters = new AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(getActivity())
    .withLoginHint("bob@contoso.com")  
    // More settings here
    .build();

b2cApp.acquireToken(parameters);

```

--- 

[!INCLUDE [active-directory-b2c-app-integration-domain-hint](../../includes/active-directory-b2c-app-integration-domain-hint.md)]

1. 外部 ID プロバイダーのドメイン名を確認します。 詳細については、「[サインインをソーシャル プロバイダーにリダイレクトする](direct-signin.md#redirect-sign-in-to-a-social-provider)」を参照してください。 
1. リスト オブジェクトを作成するか既存のものを使用して、追加のクエリ パラメーターを格納します。
1. 対応するドメイン名を含む `domain_hint` パラメーターをリストに追加します (例: `facebook.com`)。
1. 追加のクエリ パラメーター リストを MSAL 構成オブジェクトの `withAuthorizationQueryStringParameters` メソッドに渡します。

#### <a name="kotlin"></a>[Kotlin](#tab/kotlin)

```kotlin
val extraQueryParameters: MutableList<Pair<String, String>> = ArrayList()
extraQueryParameters.add(Pair("domain_hint", "facebook.com"))

val parameters = AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(activity)
    .withAuthorizationQueryStringParameters(extraQueryParameters) 
    // More settings here
    .build()

b2cApp!!.acquireToken(parameters)
```

#### <a name="java"></a>[Java](#tab/java)

```java
List<Pair<String, String>> extraQueryParameters = new ArrayList<>();
extraQueryParameters.add( new Pair<String, String>("domain_hint", "facebook.com"));

AcquireTokenParameters parameters = new AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(getActivity())
    .withAuthorizationQueryStringParameters(extraQueryParameters) 
    // More settings here
    .build();

b2cApp.acquireToken(parameters);
```

--- 

[!INCLUDE [active-directory-b2c-app-integration-ui-locales](../../includes/active-directory-b2c-app-integration-ui-locales.md)]

1. [言語のカスタマイズを構成します](language-customization.md)。
1. リスト オブジェクトを作成するか既存のものを使用して、追加のクエリ パラメーターを格納します。
1. 対応する言語コードを含む `ui_locales` パラメーターをリストに追加します (例: `en-us`)。
1. 追加のクエリ パラメーター リストを MSAL 構成オブジェクトの `withAuthorizationQueryStringParameters` メソッドに渡します。

#### <a name="kotlin"></a>[Kotlin](#tab/kotlin)

```kotlin
val extraQueryParameters: MutableList<Pair<String, String>> = ArrayList()
extraQueryParameters.add(Pair("ui_locales", "en-us"))

val parameters = AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(activity)
    .withAuthorizationQueryStringParameters(extraQueryParameters) 
    // More settings here
    .build()

b2cApp!!.acquireToken(parameters)
```

#### <a name="java"></a>[Java](#tab/java)

```java
List<Pair<String, String>> extraQueryParameters = new ArrayList<>();
extraQueryParameters.add( new Pair<String, String>("ui_locales", "en-us"));

AcquireTokenParameters parameters = new AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(getActivity())
    .withAuthorizationQueryStringParameters(extraQueryParameters) 
    // More settings here
    .build();

b2cApp.acquireToken(parameters);
```

--- 

[!INCLUDE [active-directory-b2c-app-integration-custom-parameters](../../includes/active-directory-b2c-app-integration-custom-parameters.md)]

1. [ContentDefinitionParameters](customize-ui-with-html.md#configure-dynamic-custom-page-content-uri) 要素を構成します。
1. リスト オブジェクトを作成するか既存のものを使用して、追加のクエリ パラメーターを格納します。
1. `campaignId` などのカスタム クエリ文字列パラメーターを追加します。 パラメーター値を設定します (例: `germany-promotion` )。
1. 追加のクエリ パラメーター リストを MSAL 構成オブジェクトの `withAuthorizationQueryStringParameters` メソッドに渡します。

#### <a name="kotlin"></a>[Kotlin](#tab/kotlin)

```kotlin
val extraQueryParameters: MutableList<Pair<String, String>> = ArrayList()
extraQueryParameters.add(Pair("campaignId", "germany-promotion"))

val parameters = AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(activity)
    .withAuthorizationQueryStringParameters(extraQueryParameters) 
    // More settings here
    .build()

b2cApp!!.acquireToken(parameters)
```

#### <a name="java"></a>[Java](#tab/java)

```java
List<Pair<String, String>> extraQueryParameters = new ArrayList<>();
extraQueryParameters.add( new Pair<String, String>("campaignId", "germany-promotion"));

AcquireTokenParameters parameters = new AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(getActivity())
    .withAuthorizationQueryStringParameters(extraQueryParameters) 
    // More settings here
    .build();

b2cApp.acquireToken(parameters);
```

--- 

[!INCLUDE [active-directory-b2c-app-integration-id-token-hint](../../includes/active-directory-b2c-app-integration-id-token-hint.md)]

1. カスタム ポリシーで、[ID トークン ヒントの技術プロファイル](id-token-hint.md)を定義します。
1. コードで、ID トークンを生成または取得してから、そのトークンを変数に設定します (例: `idToken`)。 
1. リスト オブジェクトを作成するか既存のものを使用して、追加のクエリ パラメーターを格納します。
1. ID トークンを格納する、対応する変数が含まれる `id_token_hint` パラメーターを追加します。
1. 追加のクエリ パラメーター リストを MSAL 構成オブジェクトの `withAuthorizationQueryStringParameters` メソッドに渡します。

#### <a name="kotlin"></a>[Kotlin](#tab/kotlin)

```kotlin
val extraQueryParameters: MutableList<Pair<String, String>> = ArrayList()
extraQueryParameters.add(Pair("id_token_hint", idToken))

val parameters = AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(activity)
    .withAuthorizationQueryStringParameters(extraQueryParameters) 
    // More settings here
    .build()

b2cApp!!.acquireToken(parameters)
```

#### <a name="java"></a>[Java](#tab/java)

```java
List<Pair<String, String>> extraQueryParameters = new ArrayList<>();
extraQueryParameters.add( new Pair<String, String>("id_token_hint", idToken));

AcquireTokenParameters parameters = new AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(getActivity())
    .withAuthorizationQueryStringParameters(extraQueryParameters) 
    // More settings here
    .build();

b2cApp.acquireToken(parameters);
```

--- 

## <a name="embedded-web-view-experience"></a>埋め込み Web ビュー エクスペリエンス

対話型の認証には Web ブラウザー が必要です。 既定では、MSAL ライブラリはシステム Web ビューを使用します。 サインイン中に、MSAL ライブラリにより、Azure AD B2C ユーザー インターフェイスを使用した Android システム Web ビューがポップアップ表示されます。  

詳細については、「[Android で MSAL を使用してクロスアプリ SSO を有効にする](../active-directory/develop/msal-android-single-sign-on.md#sso-through-system-browser)」という記事を参照してください。

ユーザーの要件によっては、埋め込み Web ビューを使用することもできます。 埋め込み Web ビューと MSAL のシステム Web ビューには、ビジュアルとシングル サインオンの動作に違いがあります。

![システム Web ビュー エクスペリエンスと埋め込み Web ビュー エクスペリエンスの違いを示すスクリーンショット。](./media/enable-authentication-android-app-options/system-web-browser-vs-embedded-view.png)

> [!IMPORTANT]
> プラットフォームの既定値 (通常はシステム ブラウザー) を使用することをお勧めします。 システム ブラウザーは、以前にログインしたことのあるユーザーを記憶するのに適しています。 Google などの一部の ID プロバイダーでは、埋め込みビュー エクスペリエンスがサポートされていません。

この動作を変更するには、*app/src/main/res/raw/auth_config_b2c.json* ファイルを開きます。 次に、`WEBVIEW` の値を指定して `authorization_user_agent` 属性を追加します。 次の例で、ビューの種類を Web ビューから埋め込みビューに変更する方法を示します。

```json
{
  "authorization_user_agent": "WEBVIEW" 
}
```

## <a name="next-steps"></a>次のステップ

- Android の構成の詳細については、「[Android 用 MSAL 構成オプション](https://github.com/AzureAD/microsoft-authentication-library-for-android/wiki)」を参照してください。
