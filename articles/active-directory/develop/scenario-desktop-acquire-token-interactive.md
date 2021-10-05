---
title: Web API を呼び出し、対話式でトークンを取得する (デスクトップ アプリ) | Azure
titleSuffix: Microsoft identity platform
description: Web API を呼び出し、対話式でアプリのトークンを取得するデスクトップ アプリを構築する方法について説明します
services: active-directory
author: maliksahil
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/25/2021
ms.author: sahmalik
ms.custom: aaddev, devx-track-python, has-adal-ref
ms.openlocfilehash: 6cd9e4ff795e1051267aa20e244e03c37c15f831
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124837791"
---
# <a name="desktop-app-that-calls-web-apis-acquire-a-token-interactively"></a>Web API を呼び出すデスクトップ アプリ: 対話式でトークンを取得する

次の例は、Microsoft Graph を使用してユーザーのプロファイルを読み取るためにトークンを対話形式で取得する最小限のコードを示しています。

# <a name="net"></a>[.NET](#tab/dotnet)

### <a name="in-msalnet"></a>MSAL.NET の場合

```csharp
string[] scopes = new string[] {"user.read"};
var app = PublicClientApplicationBuilder.Create(clientId).Build();
var accounts = await app.GetAccountsAsync();
AuthenticationResult result;
try
{
 result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
             .ExecuteAsync();
}
catch(MsalUiRequiredException)
{
 result = await app.AcquireTokenInteractive(scopes)
             .ExecuteAsync();
}
```

### <a name="mandatory-parameters"></a>必須のパラメーター

`AcquireTokenInteractive` の必須のパラメーターは 1 つだけです (``scopes``)。これには、トークンが必要なスコープを定義する文字列のリストが含まれています。 Microsoft Graph 用のトークンの場合、必要なスコープは各 Microsoft Graph API の API リファレンスの「アクセス許可」というセクションにあります。 たとえば、[ユーザーの連絡先を一覧表示する](/graph/api/user-list-contacts)には、"User.Read"、"Contacts.Read" スコープを使用する必要があります。 詳細については、「[Microsoft Graph のアクセス許可のリファレンス](/graph/permissions-reference)」を参照してください。

Android では、示されているように `.WithParentActivityOrWindow` を使用して親アクティビティを指定する必要もあります。これにより、操作後にトークンが親アクティビティに戻ります。 そのように指定しないと、`.ExecuteAsync()` の呼び出し時に例外がスローされます。

### <a name="specific-optional-parameters-in-msalnet"></a>MSAL.NET の特定の省略可能なパラメーター

#### <a name="withparentactivityorwindow"></a>WithParentActivityOrWindow

UI は対話型であるため、重要です。 `AcquireTokenInteractive` には、特定の省略可能なパラメーターが 1 つあり、それをサポートするプラットフォームに対して親 UI を指定できます。 デスクトップ アプリケーションで使用する場合、`.WithParentActivityOrWindow` の型はプラットフォームによって異なります。 または、サインイン ダイアログが画面のどこに表示されるかを制御したくない場合は、省略可能な親ウィンドウ パラメーターを省略してウィンドウを作成することができます。 これは、呼び出しを他のバックエンド サービスに渡すために使用され、ユーザーの操作にウィンドウを必要としないコマンド ライン ベースのアプリケーションに適用できます。

```csharp
// net45
WithParentActivityOrWindow(IntPtr windowPtr)
WithParentActivityOrWindow(IWin32Window window)

// Mac
WithParentActivityOrWindow(NSWindow window)

// .NET Standard (this will be on all platforms at runtime, but only on NetStandard at build time)
WithParentActivityOrWindow(object parent).
```

解説:

- .NET Standard では、想定される `object` は `Activity` (Android の場合)、`UIViewController` (iOS の場合)、`NSWindow` (Mac の場合)、`IWin32Window` または `IntPr` (Windows の場合) です。
- Windows では、埋め込みブラウザーが適切な UI 同期コンテキストを取得するように、UI スレッドから `AcquireTokenInteractive` を呼び出す必要があります。 UI スレッドから呼び出さない場合は、メッセージが適切にポンプされなかったり、UI によるデッドロックが発生したりする可能性があります。 UI スレッドでない場所で UI スレッドから Microsoft 認証ライブラリ (MSAL) を呼び出す方法の 1 つとしては、WPF で `Dispatcher` を使用します。
- WPF を使用している場合に WPF コントロールからウィンドウを取得するには、`WindowInteropHelper.Handle` クラスを使用できます。 そして、呼び出しは WPF コントロール (`this`) から次のように行われます。

  ```csharp
  result = await app.AcquireTokenInteractive(scopes)
                    .WithParentActivityOrWindow(new WindowInteropHelper(this).Handle)
                    .ExecuteAsync();
  ```

#### <a name="withprompt"></a>WithPrompt

`WithPrompt()` は、プロンプトを指定してユーザーとのインタラクティビティを制御するために使用します。 厳密な動作は [Microsoft.Identity.Client.Prompt](/dotnet/api/microsoft.identity.client.prompt) 構造体で制御できます。

この構造体では次の定数を定義します。

- `SelectAccount` により、ユーザーがセッションを確立しているアカウントを含む、アカウントの選択ダイアログ ボックスが STS で強制的に表示されます。 このオプションは、アプリケーション開発者が各種の ID の中からユーザーに選択させる場合に役立ちます。 このオプションを使用すると、MSAL から ID プロバイダーに `prompt=select_account` が送信されます。 これは既定のオプションです。 これにより、使用可能な情報 (アカウント、ユーザーのセッションの有無など) に基づいて最適なエクスペリエンスが提供されます。 正当な理由がない限り、このオプションを変更しないでください。
- `Consent` により、以前に同意が得られている場合であっても、アプリケーション開発者がユーザーの同意を求めることができます。 この場合、MSAL から ID プロバイダーに `prompt=consent` が送信されます。 このオプションは、アプリケーションを使用するたびに同意ダイアログ ボックスをユーザーに表示することが組織のガバナンスで求められる、セキュリティを重視する一部のアプリケーションで使用できます。
- `ForceLogin` により、このユーザー プロンプトが不要な場合でも、アプリケーション開発者がサービスによってユーザーに資格情報の入力を求めることができます。 このオプションは、トークンの取得に失敗した場合に、ユーザーが再度サインインできるようにするのに役立ちます。 この場合、MSAL から ID プロバイダーに `prompt=login` が送信されます。 これは、アプリケーションの特定の部分にアクセスするたびにユーザーが再度サインインすることが組織のガバナンスで求められる、セキュリティを重視するアプリケーションで使用されることがあります。
- `Create` により、`prompt=create` を ID プロバイダーに送信することで、外部 ID に使用されるサインアップ エクスペリエンスがトリガーされます。 このプロンプトは、Azure AD B2C アプリには送信しないでください。 詳細については、「[セルフサービス サインアップのユーザー フローをアプリに追加する](../external-identities/self-service-sign-up-user-flow.md)」を参照してください。
- `Never` (.NET 4.5 および WinRT の場合のみ) はユーザーに入力を求めませんが、代わりに非表示の埋め込み Web ビューに格納された Cookie の使用を試行します。 詳細については、MSAL.NET の Web ビューを参照してください。 このオプションを使用すると、失敗する場合があります。 その場合、`AcquireTokenInteractive` は、UI 操作が必要であることを通知するために例外をスローします。 別の `Prompt` パラメーターを使用することが必要になります。
- `NoPrompt` では ID プロバイダーにプロンプトは送信されません。そのため、ID プロバイダー側が、ユーザーに提示する最適なサインイン エクスペリエンス (シングル サインオンまたはアカウントの選択) を決定します。 また、このオプションは、Azure Active Directory (Azure AD) B2C のプロファイルの編集ポリシーでは必須です。 詳細については、[Azure AD B2C での詳細](https://aka.ms/msal-net-b2c-specificities)に関するページを参照してください。

#### <a name="withuseembeddedwebview"></a>WithUseEmbeddedWebView

このメソッドを使用すると、埋め込みの WebView またはシステム WebView (使用可能な場合) を強制的に使用するかどうかを指定できます。 詳細については、[Web ブラウザーの使用](msal-net-web-browsers.md)に関する記事を参照してください。

```csharp
var result = await app.AcquireTokenInteractive(scopes)
                    .WithUseEmbeddedWebView(true)
                    .ExecuteAsync();
```

#### <a name="withextrascopetoconsent"></a>WithExtraScopeToConsent

この修飾子は、複数のリソースに対するユーザーの事前の同意を求め、かつ (通常は MSAL.NET または Microsoft ID プラットフォームで使用される) 増分同意を使用しない高度なシナリオで使用されます。 詳細については、「[複数のリソースでユーザーの同意を事前に取得する](scenario-desktop-production.md#have-the-user-consent-upfront-for-several-resources)」を参照してください。

```csharp
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

#### <a name="withcustomwebui"></a>WithCustomWebUi

Web UI は、ブラウザーを起動するメカニズムです。 このメカニズムは、専用の UI WebBrowser コントロール、またはブラウザーを開くことを委任する方法の場合があります。
MSAL によりほとんどのプラットフォームに Web UI の実装が提供されますが、ブラウザーを自分でホストしたい場合もあります。

- プラットフォームがデスクトップ上の MSAL (Blazor、Unity、Mono など) によって明示的にカバーされていない場合。
- UI でアプリケーションのテストを行い、Selenium で使用できる自動化されたブラウザーを使用する必要がある場合。
- ブラウザーと MSAL を実行するアプリが別々のプロセスにある場合。

##### <a name="at-a-glance"></a>概略

これを実現するには、MSAL に `start Url` を指定します。これは、エンド ユーザーが自分のユーザー名などの項目を入力できるように、選択したブラウザーに表示される必要があります。
認証が完了したら、アプリから MSAL に `end Url` を返す必要があります。これには、Azure AD によって提供されるコードが含まれます。
`end Url` のホストは常に `redirectUri` です。 `end Url` をインターセプトするには、次のいずれかを行います。

- `redirect Url` にヒットするまでブラウザーのリダイレクトを監視します。
- ブラウザーを URL にリダイレクトさせ、これを監視します。

##### <a name="withcustomwebui-is-an-extensibility-point"></a>拡張ポイントとしての WithCustomWebUi

`WithCustomWebUi` は、パブリック クライアント アプリケーションで独自の UI を提供するために使用できる拡張ポイントです。 また、ユーザーが ID プロバイダーの /Authorize エンドポイントを経由し、サインインして同意できるようにすることもできます。 MSAL.NET では、その後で認証コードを引き換えて、トークンを取得できます。 たとえば、電子アプリケーション (Visual Studio フィードバックなど) に Web 操作を提供させて、ほとんどの処理を MSAL.NET に任せるために Visual Studio で使用されます。 また、UI オートメーションを提供する場合に使用することもできます。 パブリック クライアント アプリケーションの場合、MSAL.NET では Proof Key for Code Exchange (PKCE) 標準を使用してセキュリティが確保されるようにします。 コードを引き換えることができるのは MSAL.NET のみです。 詳細については、「[RFC 7636 - Proof Key for Code Exchange by OAuth Public Clients](https://tools.ietf.org/html/rfc7636)」を参照してください。

  ```csharp
  using Microsoft.Identity.Client.Extensions;
  ```

##### <a name="use-withcustomwebui"></a>WithCustomWebUi を使用する

`.WithCustomWebUI` を使用するには、次の手順に従います。

  1. `ICustomWebUi` インターフェイスを実装します。 詳細については、[こちらの Web サイト](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/053a98d16596be7e9ca1ab916924e5736e341fe8/src/Microsoft.Identity.Client/Extensibility/ICustomWebUI.cs#L32-L70)を参照してください。 1 つの `AcquireAuthorizationCodeAsync` メソッドを実装し、MSAL.NET によって計算された認証コード URL を受け入れます。 次に、ユーザーに ID プロバイダーとの対話を実行させ、ID プロバイダーが実装のコールバックに使用した URL (認証コードを含む) を返します。 問題がある場合は、MSAL と適切に連携する `MsalExtensionException` 例外が実装によってスローされます。
  2. `AcquireTokenInteractive` の呼び出しで、カスタム Web UI のインスタンスを渡す `.WithCustomUI()` 修飾子を使用します。

     ```csharp
     result = await app.AcquireTokenInteractive(scopes)
                       .WithCustomWebUi(yourCustomWebUI)
                       .ExecuteAsync();
     ```

##### <a name="examples-of-implementation-of-icustomwebui-in-test-automation-seleniumwebui"></a>テスト自動化における ICustomWebUi の実装例:SeleniumWebUI

MSAL.NET チームでは、この拡張メカニズムを使用するように UI テストを書き換えました。 関心がある場合は、MSAL.NET のソース コードで [SeleniumWebUI](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/053a98d16596be7e9ca1ab916924e5736e341fe8/tests/Microsoft.Identity.Test.Integration/Infrastructure/SeleniumWebUI.cs#L15-L160) クラスをご確認ください。

##### <a name="provide-a-great-experience-with-systemwebviewoptions"></a>SystemWebViewOptions を使用して優れたエクスペリエンスを提供する

MSAL.NET 4.1 [`SystemWebViewOptions`](/dotnet/api/microsoft.identity.client.systemwebviewoptions) から、以下を指定できます。

- システム Web ブラウザーでのサインインあるいは同意エラーの場合に、(`BrowserRedirectError`) に移動する URI または (`HtmlMessageError`) を表示する HTML フラグメント。
- サインインあるいは同意が成功した場合に、(`BrowserRedirectSuccess`) に移動する URI または (`HtmlMessageSuccess`) を表示する HTML フラグメント。
- システム ブラウザーを起動するために実行するアクション。 `OpenBrowserAsync` デリゲートを設定して、独自の実装を提供できます。 このクラスでは、2 つのブラウザー (Microsoft Edge 用の `OpenWithEdgeBrowserAsync` と [Chromium に基づく Microsoft Edge](https://www.windowscentral.com/faq-edge-chromium) 用 の `OpenWithChromeEdgeBrowserAsync`) の既定の実装も用意されています。

この構造体を使用するには、次の例のように記述します。

```csharp
IPublicClientApplication app;
...

options = new SystemWebViewOptions
{
 HtmlMessageError = "<b>Sign-in failed. You can close this tab ...</b>",
 BrowserRedirectSuccess = "https://contoso.com/help-for-my-awesome-commandline-tool.html"
};

var result = app.AcquireTokenInteractive(scopes)
                .WithEmbeddedWebView(false)       // The default in .NET Core
                .WithSystemWebViewOptions(options)
                .Build();
```

#### <a name="other-optional-parameters"></a>その他の省略可能なパラメーター

`AcquireTokenInteractive` のその他すべての省略可能なパラメーターについては、[AcquireTokenInteractiveParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokeninteractiveparameterbuilder#methods) に関する記事を参照してください。

# <a name="java"></a>[Java](#tab/java)

```java
private static IAuthenticationResult acquireTokenInteractive() throws Exception {

    // Load token cache from file and initialize token cache aspect. The token cache will have
    // dummy data, so the acquireTokenSilently call will fail.
    TokenCacheAspect tokenCacheAspect = new TokenCacheAspect("sample_cache.json");

    PublicClientApplication pca = PublicClientApplication.builder(CLIENT_ID)
            .authority(AUTHORITY)
            .setTokenCacheAccessAspect(tokenCacheAspect)
            .build();

    Set<IAccount> accountsInCache = pca.getAccounts().join();
    // Take first account in the cache. In a production application, you would filter
    // accountsInCache to get the right account for the user authenticating.
    IAccount account = accountsInCache.iterator().next();

    IAuthenticationResult result;
    try {
        SilentParameters silentParameters =
                SilentParameters
                        .builder(SCOPE, account)
                        .build();

        // try to acquire token silently. This call will fail since the token cache
        // does not have any data for the user you are trying to acquire a token for
        result = pca.acquireTokenSilently(silentParameters).join();
    } catch (Exception ex) {
        if (ex.getCause() instanceof MsalException) {

            InteractiveRequestParameters parameters = InteractiveRequestParameters
                    .builder(new URI("http://localhost"))
                    .scopes(SCOPE)
                    .build();

            // Try to acquire a token interactively with system browser. If successful, you should see
            // the token and account information printed out to console
            result = pca.acquireToken(parameters).join();
        } else {
            // Handle other exceptions accordingly
            throw ex;
        }
    }
    return result;
}
```

# <a name="macos"></a>[macOS](#tab/macOS)

### <a name="in-msal-for-ios-and-macos"></a>iOS および macOS 用の MSAL の場合

Objective-C:

```objc
MSALInteractiveTokenParameters *interactiveParams = [[MSALInteractiveTokenParameters alloc] initWithScopes:scopes webviewParameters:[MSALWebviewParameters new]];
[application acquireTokenWithParameters:interactiveParams completionBlock:^(MSALResult *result, NSError *error) {
    if (!error)
    {
        // You'll want to get the account identifier to retrieve and reuse the account
        // for later acquireToken calls
        NSString *accountIdentifier = result.account.identifier;

        NSString *accessToken = result.accessToken;
    }
}];
```

Swift:

```swift
let interactiveParameters = MSALInteractiveTokenParameters(scopes: scopes, webviewParameters: MSALWebviewParameters())
application.acquireToken(with: interactiveParameters, completionBlock: { (result, error) in

    guard let authResult = result, error == nil else {
        print(error!.localizedDescription)
        return
    }

    // Get access token from result
    let accessToken = authResult.accessToken
})
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

MSAL ノードでは、コード交換のための証明鍵 (PKCE) を使用して認可コード フロー経由でトークンを取得します。 プロセスには 2 つの手順があります。まず、アプリケーションで、認証コードを生成するために使用できる URL が取得されます。 この URL は、任意のブラウザーで開くことができ (ユーザーが資格情報を入力できます)、認可コードと共に `redirectUri` (アプリ登録時に登録済み) にリダイレクトされます。 次に、アプリケーションでは受け取った認可コードを `acquireTokenByCode()` メソッドに渡し、そこでアクセス トークンに交換されます。

```javascript
const msal = require("@azure/msal-node");

const msalConfig = {
    auth: {
        clientId: "your_client_id_here",
        authority: "your_authority_here",
    }
};

const pca = new msal.PublicClientApplication(msalConfig);

const {verifier, challenge} = await msal.cryptoProvider.generatePkceCodes();

const authCodeUrlParameters = {
    scopes: ["User.Read"],
    redirectUri: "your_redirect_uri",
    codeChallenge: challenge, // PKCE Code Challenge
    codeChallengeMethod: "S256" // PKCE Code Challenge Method 
};

// get url to sign user in and consent to scopes needed for application
pca.getAuthCodeUrl(authCodeUrlParameters).then((response) => {
    console.log(response);

    const tokenRequest = {
        code: response["authorization_code"],
        codeVerifier: verifier // PKCE Code Verifier 
        redirectUri: "your_redirect_uri",
        scopes: ["User.Read"],
    };

    // acquire a token by exchanging the code
    pca.acquireTokenByCode(tokenRequest).then((response) => {
        console.log("\nResponse: \n:", response);
    }).catch((error) => {
        console.log(error);
    });
}).catch((error) => console.log(JSON.stringify(error)));
```

# <a name="python"></a>[Python](#tab/python)

MSAL Python 1.7 以降では、対話型のトークン取得メソッドが提供されます。

```python
result = None

# Firstly, check the cache to see if this end user has signed in before
accounts = app.get_accounts(username=config["username"])
if accounts:
    result = app.acquire_token_silent(config["scope"], account=accounts[0])

if not result:
    result = app.acquire_token_interactive(  # It automatically provides PKCE protection
         scopes=config["scope"])
```

---
### <a name="next-steps"></a>次のステップ

このシナリオの次の記事である「[デスクトップ アプリから Web API を呼び出す](scenario-desktop-call-api.md)」に進みます。
