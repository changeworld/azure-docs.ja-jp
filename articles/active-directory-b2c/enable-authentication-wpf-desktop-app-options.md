---
title: Azure Active Directory B2C を使用して WPF デスクトップ アプリケーションのオプションを有効にする
description: いくつかの方法を使用して、WPF デスクトップ アプリケーションのオプションの使用を有効にします。
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 08/04/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: 16b625f739df35604b649b9d7391d969669af6be
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130040765"
---
# <a name="enable-authentication-options-in-a-wpf-desktop-app-by-using-azure-ad-b2c"></a>Azure AD B2C を使用して WPF デスクトップ アプリの認証オプションを有効にする 

この記事では、Windows Presentation Foundation (WPF) デスクトップ アプリケーション用に Azure Active Directory B2C (Azure AD B2C) の認証エクスペリエンスをカスタマイズおよび拡張する方法について説明します。 

始める前に、[Azure AD B2C を使用したサンプル WPF デスクトップ アプリケーションでの認証の構成](configure-authentication-sample-wpf-desktop-app.md)に関する記事を理解しておいてください。


[!INCLUDE [active-directory-b2c-app-integration-login-hint](../../includes/active-directory-b2c-app-integration-login-hint.md)]

1. カスタム ポリシーを使用している場合は、[直接サインインの設定](direct-signin.md#prepopulate-the-sign-in-name)に関する記事の説明に従って、必要な入力要求を追加します。 
1. Microsoft Authentication Library (MSAL) 構成オブジェクトを探し、ログイン ヒントを使用して `withLoginHint()` メソッドを追加します。

```csharp
authResult = await app.AcquireTokenInteractive(App.ApiScopes)
    .WithParentActivityOrWindow(new WindowInteropHelper(this).Handle)
    .WithLoginHint("bob@contoso.com")
    .ExecuteAsync();
```

[!INCLUDE [active-directory-b2c-app-integration-domain-hint](../../includes/active-directory-b2c-app-integration-domain-hint.md)]

1. 外部 ID プロバイダーのドメイン名を確認します。 詳細については、「[サインインをソーシャル プロバイダーにリダイレクトする](direct-signin.md#redirect-sign-in-to-a-social-provider)」を参照してください。 
1. `Dictionary` オブジェクトを作成するか、既存のものを使用して、追加のクエリ パラメーターを格納します。
1. 対応するドメイン名が含まれる `domain_hint` パラメーターをディクショナリに追加します (例:`facebook.com`)。
1. 追加のクエリ パラメーター オブジェクトを、MSAL 構成オブジェクトの `WithExtraQueryParameters` メソッドに渡します。

```csharp
Dictionary<string, string> extraQueryParameters = new Dictionary<string, string>();
extraQueryParameters.Add("domain_hint", "facebook.com");

authResult = await app.AcquireTokenInteractive(App.ApiScopes)
    .WithParentActivityOrWindow(new WindowInteropHelper(this).Handle)
    .WithExtraQueryParameters(extraQueryParameters)
    .ExecuteAsync();
```

[!INCLUDE [active-directory-b2c-app-integration-ui-locales](../../includes/active-directory-b2c-app-integration-ui-locales.md)]

1. [言語のカスタマイズを構成します](language-customization.md)。
1. `Dictionary` オブジェクトを作成するか、既存のものを使用して、追加のクエリ パラメーターを格納します。
1. 対応する言語コードが含まれる `ui_locales` パラメーターを、ディクショナリに追加します (例:`en-us`)。
1. 追加のクエリ パラメーター オブジェクトを、MSAL 構成オブジェクトの `WithExtraQueryParameters` メソッドに渡します。

```csharp
Dictionary<string, string> extraQueryParameters = new Dictionary<string, string>();
extraQueryParameters.Add("ui_locales", "en-us");

authResult = await app.AcquireTokenInteractive(App.ApiScopes)
    .WithParentActivityOrWindow(new WindowInteropHelper(this).Handle)
    .WithExtraQueryParameters(extraQueryParameters)
    .ExecuteAsync();
```

[!INCLUDE [active-directory-b2c-app-integration-custom-parameters](../../includes/active-directory-b2c-app-integration-custom-parameters.md)]

1. [ContentDefinitionParameters](customize-ui-with-html.md#configure-dynamic-custom-page-content-uri) 要素を構成します。
1. `Dictionary` オブジェクトを作成するか、既存のものを使用して、追加のクエリ パラメーターを格納します。
1. `campaignId` などのカスタム クエリ文字列パラメーターを追加します。 パラメーター値を設定します (例: `germany-promotion` )。
1. 追加のクエリ パラメーター オブジェクトを、MSAL 構成オブジェクトの `WithExtraQueryParameters` メソッドに渡します。

```csharp
Dictionary<string, string> extraQueryParameters = new Dictionary<string, string>();
extraQueryParameters.Add("campaignId", "germany-promotion");

authResult = await app.AcquireTokenInteractive(App.ApiScopes)
    .WithParentActivityOrWindow(new WindowInteropHelper(this).Handle)
    .WithExtraQueryParameters(extraQueryParameters)
    .ExecuteAsync();
```

[!INCLUDE [active-directory-b2c-app-integration-id-token-hint](../../includes/active-directory-b2c-app-integration-id-token-hint.md)]

1. カスタム ポリシーで、[ID トークン ヒントの技術プロファイル](id-token-hint.md)を定義します。
1. コードで、ID トークンを生成または取得してから、そのトークンを変数に設定します (例: `idToken`)。 
1. `Dictionary` オブジェクトを作成するか、既存のものを使用して、追加のクエリ パラメーターを格納します。
1. ID トークンを格納する、対応する変数が含まれる `id_token_hint` パラメーターを追加します。
1. 追加のクエリ パラメーター オブジェクトを、MSAL 構成オブジェクトの `extraQueryParameters` 属性に渡します。

```csharp
Dictionary<string, string> extraQueryParameters = new Dictionary<string, string>();
extraQueryParameters.Add("id_token_hint", idToken);

authResult = await app.AcquireTokenInteractive(App.ApiScopes)
    .WithParentActivityOrWindow(new WindowInteropHelper(this).Handle)
    .WithExtraQueryParameters(extraQueryParameters)
    .ExecuteAsync();
```


[!INCLUDE [active-directory-b2c-app-integration-logging](../../includes/active-directory-b2c-app-integration-logging.md)]


次のコード スニペットでは、MSAL ログを構成する方法を示します。

```csharp
PublicClientApp = PublicClientApplicationBuilder.Create(ClientId)
    .WithB2CAuthority(AuthoritySignUpSignIn)
    .WithRedirectUri(RedirectUri)
    .WithLogging(Log, LogLevel.Info, false) // don't log P(ersonally) I(dentifiable) I(nformation) details on a regular basis
    .Build();
```

## <a name="configure-the-redirect-uri"></a>リダイレクト URI を構成する

[デスクトップ アプリの登録](configure-authentication-sample-wpf-desktop-app.md#step-23-register-the-desktop-app)プロセスの過程でリダイレクト URI を選択する際には、以下の重要な点に注意してください。

* **開発**: 開発用途とデスクトップ アプリでは、リダイレクト URI を `http://localhost` に設定することができます。要求に含まれるすべてのポートが Azure AD B2C によって考慮されます。 登録された URI にポートが含まれている場合、そのポートのみが Azure AD B2C によって使用されます。 たとえば、登録されたリダイレクト URI が `http://localhost` である場合、要求におけるリダイレクト URI を `http://localhost:<randomport>` とすることができます。 登録されたリダイレクト URI が `http://localhost:8080` である場合、要求におけるリダイレクト URI は `http://localhost:8080` とする必要があります。
* **一意**:リダイレクト URI のスキームは、すべてのアプリケーションで一意である必要があります。 `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect` の例では、`com.onmicrosoft.contosob2c.exampleapp` はスキームです。 このパターンに従う必要があります。 2 つのアプリケーションが同じスキームを共有している場合、ユーザーにはアプリケーションの選択権があります。 ユーザーが選択を誤った場合、サインインは失敗します。
* **完全**:リダイレクト URI には、スキームとパスの両方が必要です。 パスには、ドメインの後に少なくとも 1 つのスラッシュ文字を含める必要があります。 たとえば、`//oauth/` は機能しますが、`//oauth` は失敗します。 URI に特殊文字を含めないでください。 たとえば、アンダースコア文字 (_) は使用できません。

## <a name="next-steps"></a>次のステップ

- 詳細は、「[.NET、UWP、NetCore、Xamarin 用の MSAL の構成オプション](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki)」を参照してください。
