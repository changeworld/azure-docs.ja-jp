---
title: Web API を呼び出す Web アプリを構成する | Azure
titleSuffix: Microsoft identity platform
description: Web API を呼び出す Web アプリのコードを構成する方法について説明します
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/25/2020
ms.author: jmprieur
ms.custom: aaddev, devx-track-python
ms.openlocfilehash: aa377547f7f4961e199ec8d62bf0f1435296f983
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104669306"
---
# <a name="a-web-app-that-calls-web-apis-code-configuration"></a>Web API を呼び出す Web アプリ: コード構成

[ユーザーをサインインさせる Web アプリ](scenario-web-app-sign-user-overview.md)のシナリオで示されているように、Web アプリではユーザーをサインインさせるために [OAuth 2.0 の承認コード フロー](v2-oauth2-auth-code-flow.md)が使用されます。 このフローには、次の 2 つのステップがあります。

1. 承認コードを要求します。 この部分では、ユーザーとのプライベートな対話を Microsoft ID プラットフォームに委任します。 その対話の間に、ユーザーはサインインして Web API の使用に同意します。 プライベートな対話が正常に終了すると、Web アプリはそのリダイレクト URI での承認コードを受け取ります。
1. 承認コードを引き換えることによって、API のアクセス トークンを要求します。

[ユーザーをサインインさせる Web アプリ](scenario-web-app-sign-user-overview.md)のシナリオでは、最初のステップだけが説明されていました。 ここでは、ユーザーをサインインさせるだけでなく、Web API の呼び出しも行うように、Web アプリを変更する方法について説明します。

## <a name="microsoft-libraries-supporting-web-apps"></a>Web アプリをサポートしている Microsoft ライブラリ

次の Microsoft ライブラリで Web アプリがサポートされています。

[!INCLUDE [active-directory-develop-libraries-webapp](../../../includes/active-directory-develop-libraries-webapp.md)]

関心があるプラットフォームのタブを選択してください。

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

## <a name="client-secrets-or-client-certificates"></a>クライアント シークレットまたはクライアント証明書

ご利用の Web アプリでダウンストリーム Web API を呼び出すことができるようになったため、クライアント シークレットまたはクライアント証明書を *appsettings.json* ファイルに指定してください。 次を指定するセクションを追加することもできます。

- ダウンストリーム Web API の URL
- API の呼び出しに必要なスコープ

次の例では、`GraphBeta` セクションでこれらの設定を指定しています。

```JSON
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "[Client_id-of-web-app-eg-2ec40e65-ba09-4853-bcde-bcb60029e596]",
    "TenantId": "common"

   // To call an API
   "ClientSecret": "[Copy the client secret added to the app from the Azure portal]",
   "ClientCertificates": [
  ]
 },
 "GraphBeta": {
    "BaseUrl": "https://graph.microsoft.com/beta",
    "Scopes": "user.read"
    }
}
```

クライアント シークレットの代わりに、クライアント証明書を指定することができます。 次のコード スニペットは、Azure Key Vault に格納されている証明書の使用を示しています。

```JSON
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "[Client_id-of-web-app-eg-2ec40e65-ba09-4853-bcde-bcb60029e596]",
    "TenantId": "common"

   // To call an API
   "ClientCertificates": [
      {
        "SourceType": "KeyVault",
        "KeyVaultUrl": "https://msidentitywebsamples.vault.azure.net",
        "KeyVaultCertificateName": "MicrosoftIdentitySamplesCert"
      }
   ]
  },
  "GraphBeta": {
    "BaseUrl": "https://graph.microsoft.com/beta",
    "Scopes": "user.read"
  }
}
```

*Microsoft.Identity.Web* では、構成またはコードの両方で証明書を記述するいくつかの方法を提供しています。 詳細については、GitHub 上の「[Microsoft.Identity.Web - 証明書の使用](https://github.com/AzureAD/microsoft-identity-web/wiki/Using-certificates)」を参照してください。

## <a name="startupcs"></a>Startup.cs

Web アプリでは、ダウンストリーム API のトークンを取得する必要があります。 これを指定するには、`.AddMicrosoftIdentityWebApi(Configuration)` の後に `.EnableTokenAcquisitionToCallDownstreamApi()` 行を追加します。 この行により、コントローラーおよびページのアクションで使用できる `ITokenAcquisition` サービスが公開されます。 ただし、次の 2 つのオプションでわかるように、これはもっと簡単に行うことができます。 また、*Startup.cs* で、`.AddInMemoryTokenCaches()` などのトークン キャッシュの実装を選択する必要もあります。

   ```csharp
   using Microsoft.Identity.Web;

   public class Startup
   {
     // ...
     public void ConfigureServices(IServiceCollection services)
     {
     // ...
     services.AddAuthentication(OpenIdConnectDefaults.AuthenticationScheme)
             .AddMicrosoftIdentityWebApp(Configuration, Configuration.GetSection("AzureAd"))
               .EnableTokenAcquisitionToCallDownstreamApi(new string[]{"user.read" })
               .AddInMemoryTokenCaches();
      // ...
     }
     // ...
   }
   ```

`EnableTokenAcquisitionToCallDownstreamApi` に渡されるスコープは省略可能であり、Web アプリでログイン時にスコープとそれらのスコープに対するユーザーの同意を要求できます。 スコープを指定しない場合は、*Microsoft.Identity.Web* によって、増分同意エクスペリエンスが有効になります。

トークンを自分で取得しない場合は、*Microsoft.Identity.Web* に、Web アプリから Web API を呼び出すための 2 つのメカニズムが用意されています。 選択するオプションは、Microsoft Graph または別の API のどちらを呼び出すかによって異なります。

### <a name="option-1-call-microsoft-graph"></a>オプション 1: Microsoft Graph の呼び出し

Microsoft Graph を呼び出す場合は、*Microsoft.Identity.Web* を使用すると、API アクションで `GraphServiceClient` (Microsoft Graph SDK によって公開されている) を直接使用することができます。 Microsoft Graph を公開するには、次の手順を実行します。

1. プロジェクトに、[Microsoft.Identity.Web.MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Identity.Web.MicrosoftGraph) NuGet パッケージを追加します。
1. *Startup.cs* ファイルで `.EnableTokenAcquisitionToCallDownstreamApi()` の後に `.AddMicrosoftGraph()` を追加します。 `.AddMicrosoftGraph()` にはいくつかのオーバーライドがあります。 構成セクションをパラメーターとして受け取るオーバーライドを使用すると、コードは次のようになります。

   ```csharp
   using Microsoft.Identity.Web;

   public class Startup
   {
     // ...
     public void ConfigureServices(IServiceCollection services)
     {
     // ...
     services.AddAuthentication(OpenIdConnectDefaults.AuthenticationScheme)
             .AddMicrosoftIdentityWebApp(Configuration, Configuration.GetSection("AzureAd"))
               .EnableTokenAcquisitionToCallDownstreamApi(new string[]{"user.read" })
                  .AddMicrosoftGraph(Configuration.GetSection("GraphBeta"))
               .AddInMemoryTokenCaches();
      // ...
     }
     // ...
   }
   ```

### <a name="option-2-call-a-downstream-web-api-other-than-microsoft-graph"></a>オプション 2:Microsoft Graph 以外のダウンストリーム Web API を呼び出す

Microsoft Graph 以外の Web API を呼び出すために、*Microsoft.Identity.Web* には、トークンを要求し、ダウンストリーム Web API を呼び出す `.AddDownstreamWebApi()` が用意されています。

   ```csharp
   using Microsoft.Identity.Web;

   public class Startup
   {
     // ...
     public void ConfigureServices(IServiceCollection services)
     {
     // ...
     services.AddAuthentication(OpenIdConnectDefaults.AuthenticationScheme)
             .AddMicrosoftIdentityWebApp(Configuration, "AzureAd")
               .EnableTokenAcquisitionToCallDownstreamApi(new string[]{"user.read" })
                  .AddDownstreamWebApi("MyApi", Configuration.GetSection("GraphBeta"))
               .AddInMemoryTokenCaches();
      // ...
     }
     // ...
   }
   ```

### <a name="summary"></a>まとめ

Web API と同様に、さまざまなトークン キャッシュの実装を選択できます。 詳細については、GitHub の [Microsoft.Identity.Web - トークン キャッシュのシリアル化](https://aka.ms/ms-id-web/token-cache-serialization)のページを参照してください。

次の図は、*Microsoft.Identity.Web* のさまざまな可能性と、*Startup.cs* ファイルへの影響を示しています。

:::image type="content" source="media/scenarios/microsoft-identity-web-startup-cs.svg" alt-text="Web API を呼び出し、トークン キャッシュの実装を指定するための Startup.cs のサービス構成オプションを示すブロック図":::

> [!NOTE]
> これらのコード例を完全に理解するために、[ASP.NET Core の基礎](/aspnet/core/fundamentals)、特に[依存関係の挿入](/aspnet/core/fundamentals/dependency-injection)と[オプション](/aspnet/core/fundamentals/configuration/options)についてよく理解してください。

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

ユーザーのサインインは OpenID Connect (OIDC) ミドルウェアに委任されるので、OIDC プロセスと対話する必要があります。 対話する方法は、使用するフレームワークによって異なります。

ASP.NET の場合は、ミドルウェアの OIDC イベントをサブスクライブします。

- Open ID Connect ミドルウェアを使用することで、ASP.NET Core に承認コードを要求させます。 ASP.NET/ASP.NET Core により、ユーザーはサインインおよび同意できるようになります。
- Web アプリをサブスクライブして、承認コードを受け取ります。 このサブスクリプションは、C# の委任を使用することで行われます。
- 承認コードを受け取ったら、MSAL ライブラリを使用してそれを引き換えます。 結果のアクセス トークンと更新トークンは、トークン キャッシュに格納されます。 アプリケーションの他の部分 (コントローラーなど) でキャッシュを使用して、他のトークンをサイレントに取得できます。

この記事および以下のコードの例は、[ASP.NET Web アプリ サンプル](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect)から抜粋されたものです。 完全な実装の詳細については、そのサンプルをご覧ください。

# <a name="java"></a>[Java](#tab/java)

この記事および以下のコードの例は、[Microsoft Graph を呼び出す Java Web アプリケーション](https://github.com/Azure-Samples/ms-identity-java-webapp) (MSAL for Java を使用する Web アプリ サンプル) から抜粋されています。
そのサンプルでは、現在、MSAL for Java を使用して、承認コードの URL を生成し、Microsoft ID プラットフォームに対する承認エンドポイントへのナビゲーションを処理しています。 また、Sprint セキュリティを使用してユーザーをサインインさせることもできます。 完全な実装の詳細については、そのサンプルをご覧ください。

# <a name="python"></a>[Python](#tab/python)

この記事および以下のコードの例は、[Microsoft Graph を呼び出す Python Web アプリケーション](https://github.com/Azure-Samples/ms-identity-python-webapp) (MSAL.Python を使用する Web アプリ サンプル) から抜粋されています。
そのサンプルでは、現在、MSAL.Python を使用して、承認コードの URL を生成し、Microsoft ID プラットフォームに対する承認エンドポイントへのナビゲーションを処理しています。 完全な実装の詳細については、そのサンプルをご覧ください。

---

## <a name="code-that-redeems-the-authorization-code"></a>承認コードを引き換えるコード

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Microsoft.Identity.Web では、正しい OpenID Connect 設定を設定し、コードの受信イベントをサブスクライブして、コードを引き換えることで、コードを簡単にすることができます。 認証コードを引き換えるために、追加のコードは必要ありません。 この仕組みの詳細については、[Microsoft.Identity.Web のソース コード](https://github.com/AzureAD/microsoft-identity-web/blob/c29f1a7950b940208440bebf0bcb524a7d6bee22/src/Microsoft.Identity.Web/WebAppExtensions/WebAppCallsWebApiAuthenticationBuilderExtensions.cs#L140)をご覧ください。

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

OpenID Connect の構成と、`OnAuthorizationCodeReceived` イベントのサブスクリプションが [App_Start\Startup.Auth.cs](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/App_Start/Startup.Auth.cs) ファイルで行われることを除き、ASP.NET での処理は ASP.NET Core と同じように行われます。 ASP.NET では [Web.config#L15](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/master/WebApp/Web.config#L15) で `RedirectUri` を指定する必要があることを除き、概念も ASP.NET Core と似ています。 この構成は、アプリケーションをデプロイするときに変更する必要があるため、ASP.NET Core の場合より堅牢さが少し低下します。

Startup.Auth.cs のコードを次に示します。

```csharp
public partial class Startup
{
  public void ConfigureAuth(IAppBuilder app)
  {
    app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

    app.UseCookieAuthentication(new CookieAuthenticationOptions());

    // Custom middleware initialization. This is activated when the code obtained from a code_grant is present in the query string (&code=<code>).
    app.UseOAuth2CodeRedeemer(
        new OAuth2CodeRedeemerOptions
        {
            ClientId = AuthenticationConfig.ClientId,
            ClientSecret = AuthenticationConfig.ClientSecret,
            RedirectUri = AuthenticationConfig.RedirectUri
        }
      );

  app.UseOpenIdConnectAuthentication(
      new OpenIdConnectAuthenticationOptions
      {
        // The `Authority` represents the v2.0 endpoint - https://login.microsoftonline.com/common/v2.0.
        Authority = AuthenticationConfig.Authority,
        ClientId = AuthenticationConfig.ClientId,
        RedirectUri = AuthenticationConfig.RedirectUri,
        PostLogoutRedirectUri = AuthenticationConfig.RedirectUri,
        Scope = AuthenticationConfig.BasicSignInScopes + " Mail.Read", // A basic set of permissions for user sign-in and profile access "openid profile offline_access"
        TokenValidationParameters = new TokenValidationParameters
        {
            ValidateIssuer = false,
            // In a real application, you would use IssuerValidator for additional checks, such as making sure the user's organization has signed up for your app.
            //     IssuerValidator = (issuer, token, tvp) =>
            //     {
            //        //if(MyCustomTenantValidation(issuer))
            //        return issuer;
            //        //else
            //        //    throw new SecurityTokenInvalidIssuerException("Invalid issuer");
            //    },
            //NameClaimType = "name",
        },
        Notifications = new OpenIdConnectAuthenticationNotifications()
        {
            AuthorizationCodeReceived = OnAuthorizationCodeReceived,
            AuthenticationFailed = OnAuthenticationFailed,
        }
      });
  }

  private async Task OnAuthorizationCodeReceived(AuthorizationCodeReceivedNotification context)
  {
      // Upon successful sign-in, get the access token and cache it by using MSAL.
      IConfidentialClientApplication clientApp = MsalAppBuilder.BuildConfidentialClientApplication(new ClaimsPrincipal(context.AuthenticationTicket.Identity));
      AuthenticationResult result = await clientApp.AcquireTokenByAuthorizationCode(new[] { "Mail.Read" }, context.Code).ExecuteAsync();
  }

  private Task OnAuthenticationFailed(AuthenticationFailedNotification<OpenIdConnectMessage, OpenIdConnectAuthenticationOptions> notification)
  {
      notification.HandleResponse();
      notification.Response.Redirect("/Error?message=" + notification.Exception.Message);
      return Task.FromResult(0);
  }
}
```

# <a name="java"></a>[Java](#tab/java)

「[ユーザーをサインインさせる Web アプリ: コード構成](scenario-web-app-sign-user-app-configuration.md?tabs=java#initialization-code)」を参照して、Java サンプルで承認コードを取得する方法を理解してください。 アプリがコードを受け取った後、[AuthFilter.java#L51-L56](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java#L51-L56) では次のことが行われます。

1. [AuthHelper.java#L67-L97](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L67-L97) の `AuthHelper.processAuthenticationCodeRedirect` メソッドに委任します。
1. `getAuthResultByAuthCode` を呼び出します。

```Java
class AuthHelper {
  // Code omitted
  void processAuthenticationCodeRedirect(HttpServletRequest httpRequest, String currentUri, String fullUrl)
            throws Throwable {

  // Code omitted
  AuthenticationResponse authResponse = AuthenticationResponseParser.parse(new URI(fullUrl), params);

  // Code omitted
  IAuthenticationResult result = getAuthResultByAuthCode(
                    httpRequest,
                    oidcResponse.getAuthorizationCode(),
                    currentUri);

// Code omitted
  }
}
```

`getAuthResultByAuthCode` メソッドは [AuthHelper.java#L176](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L176) で定義されています。 MSAL の `ConfidentialClientApplication` を作成した後、承認コードから作成された `AuthorizationCodeParameters` を使用して `acquireToken()` を呼び出します。

```Java
   private IAuthenticationResult getAuthResultByAuthCode(
            HttpServletRequest httpServletRequest,
            AuthorizationCode authorizationCode,
            String currentUri) throws Throwable {

        IAuthenticationResult result;
        ConfidentialClientApplication app;
        try {
            app = createClientApplication();

            String authCode = authorizationCode.getValue();
            AuthorizationCodeParameters parameters = AuthorizationCodeParameters.builder(
                    authCode,
                    new URI(currentUri)).
                    build();

            Future<IAuthenticationResult> future = app.acquireToken(parameters);

            result = future.get();
        } catch (ExecutionException e) {
            throw e.getCause();
        }

        if (result == null) {
            throw new ServiceUnavailableException("authentication result was null");
        }

        SessionManagementHelper.storeTokenCacheInSession(httpServletRequest, app.tokenCache().serialize());

        return result;
    }

    private ConfidentialClientApplication createClientApplication() throws MalformedURLException {
        return ConfidentialClientApplication.builder(clientId, ClientCredentialFactory.create(clientSecret)).
                authority(authority).
                build();
    }
```

# <a name="python"></a>[Python](#tab/python)

認証コードフローは、「[ユーザーをサインインさせる Web アプリ: コード構成](scenario-web-app-sign-user-app-configuration.md?tabs=python#initialization-code)」で示されているように要求されます。 その後、Flask によって URL `/getAToken` からルーティングされた `authorized` 関数でコードが受信されます。 このコードの完全なコンテキストについては、[app.py#L30-L44](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L30-L44) を参照してください。

```python
 @app.route("/getAToken")  # Its absolute URL must match your app's redirect_uri set in AAD.
def authorized():
    if request.args['state'] != session.get("state"):
        return redirect(url_for("login"))
    cache = _load_cache()
    result = _build_msal_app(cache).acquire_token_by_authorization_code(
        request.args['code'],
        scopes=app_config.SCOPE,  # Misspelled scope would cause an HTTP 400 error here.
        redirect_uri=url_for("authorized", _external=True))
    if "error" in result:
        return "Login failure: %s, %s" % (
            result["error"], result.get("error_description"))
    session["user"] = result.get("id_token_claims")
    _save_cache(cache)
    return redirect(url_for("index"))
```

---

機密クライアント アプリケーションでは、クライアント シークレットではなく、クライアント証明書またはクライアント アサーションを使用してその ID を証明することもできます。
クライアント アサーションの使用は高度なシナリオであり、詳細については[クライアント アサーション](msal-net-client-assertions.md)に関するページをご覧ください。

## <a name="token-cache"></a>トークンのキャッシュ

> [!IMPORTANT]
> Web アプリまたは Web API でのトークン キャッシュの実装は、[ファイル ベース](scenario-desktop-acquire-token.md#file-based-token-cache)であることが多いデスクトップ アプリケーションでの実装とは異なります。
> セキュリティとパフォーマンスのため、Web アプリと Web API では、ユーザー アカウントごとに 1 つのトークン キャッシュが存在することが重要です。 アカウントごとにトークン キャッシュをシリアル化する必要があります。

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

ASP.NET Core のチュートリアルでは、依存関係の挿入を使用して、アプリケーションの Startup.cs ファイルでトークン キャッシュの実装を決定できます。 Microsoft.Identity.Web には、「[トークン キャッシュのシリアル化](msal-net-token-cache-serialization.md#token-cache-for-a-web-app-confidential-client-application)」で説明されている構築済みのトークン キャッシュ シリアライザーが付属しています。 興味深い可能性として、ASP.NET Core の[分散メモリ キャッシュ](/aspnet/core/performance/caching/distributed#distributed-memory-cache)を選択できます。

```csharp
// Use a distributed token cache by adding:
    services.AddMicrosoftIdentityWebAppAuthentication(Configuration, "AzureAd")
            .EnableTokenAcquisitionToCallDownstreamApi(
                initialScopes: new string[] { "user.read" })
            .AddDistributedTokenCaches();

// Then, choose your implementation.
// For instance, the distributed in-memory cache (not cleared when you stop the app):
services.AddDistributedMemoryCache();

// Or a Redis cache:
services.AddStackExchangeRedisCache(options =>
{
 options.Configuration = "localhost";
 options.InstanceName = "SampleInstance";
});

// Or even a SQL Server token cache:
services.AddDistributedSqlServerCache(options =>
{
 options.ConnectionString = _config["DistCache_ConnectionString"];
 options.SchemaName = "dbo";
 options.TableName = "TestCache";
});
```

トークン キャッシュ プロバイダーの詳細については、Microsoft.Identity.Web の[トークン キャッシュのシリアル化](https://aka.ms/ms-id-web/token-cache-serialization)に関する記事、および Web アプリのチュートリアルの [ASP.NET Core Web アプリのチュートリアル | トークンキャッシュ](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-2-TokenCache)のフェーズもご覧ください。

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Web アプリまたは Web API でのトークン キャッシュの実装は、[ファイル ベース](scenario-desktop-acquire-token.md#file-based-token-cache)であることが多いデスクトップ アプリケーションでの実装とは異なります。

Web アプリの実装では、ASP.NET セッションまたはサーバー メモリを使用できます。 たとえば、[MsalAppBuilder.cs#L39-L51](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Utils/MsalAppBuilder.cs#L39-L51) で MSAL.NET アプリケーションの作成後にキャッシュの実装をフックする方法を参照してください。

```csharp
public static class MsalAppBuilder
{
 // Omitted code
    public static IConfidentialClientApplication BuildConfidentialClientApplication(ClaimsPrincipal currentUser)
    {
      IConfidentialClientApplication clientapp = ConfidentialClientApplicationBuilder.Create(AuthenticationConfig.ClientId)
            .WithClientSecret(AuthenticationConfig.ClientSecret)
            .WithRedirectUri(AuthenticationConfig.RedirectUri)
            .WithAuthority(new Uri(AuthenticationConfig.Authority))
            .Build();

      // After the ConfidentialClientApplication is created, we overwrite its default UserTokenCache with our implementation.
      MSALPerUserMemoryTokenCache userTokenCache = new MSALPerUserMemoryTokenCache(clientapp.UserTokenCache, currentUser ?? ClaimsPrincipal.Current);

      return clientapp;
  }
```

# <a name="java"></a>[Java](#tab/java)

MSAL Java では、トークン キャッシュをシリアル化および逆シリアル化するメソッドが提供されています。 Java のサンプルでは、セッションからのシリアル化が処理されています ([AuthHelper.java#L99-L122](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L99-L122) の `getAuthResultBySilentFlow` メソッドを参照)。

```Java
IAuthenticationResult getAuthResultBySilentFlow(HttpServletRequest httpRequest, HttpServletResponse httpResponse)
      throws Throwable {

  IAuthenticationResult result =  SessionManagementHelper.getAuthSessionObject(httpRequest);

  IConfidentialClientApplication app = createClientApplication();

  Object tokenCache = httpRequest.getSession().getAttribute("token_cache");
  if (tokenCache != null) {
      app.tokenCache().deserialize(tokenCache.toString());
  }

  SilentParameters parameters = SilentParameters.builder(
          Collections.singleton("User.Read"),
          result.account()).build();

  CompletableFuture<IAuthenticationResult> future = app.acquireTokenSilently(parameters);
  IAuthenticationResult updatedResult = future.get();

  // Update session with latest token cache.
  SessionManagementHelper.storeTokenCacheInSession(httpRequest, app.tokenCache().serialize());

  return updatedResult;
}
```

`SessionManagementHelper` クラスの詳細は、[MSAL for Java のサンプル](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/SessionManagementHelper.java)で提供されています。

# <a name="python"></a>[Python](#tab/python)

Python のサンプルでは、要求ごとに機密クライアント アプリケーションを再作成し、Flask セッション キャッシュにシリアル化することによって、アカウントごとに 1 つのキャッシュが確保されています。

```python
from flask import Flask, render_template, session, request, redirect, url_for
from flask_session import Session  # https://pythonhosted.org/Flask-Session
import msal
import app_config


app = Flask(__name__)
app.config.from_object(app_config)
Session(app)

# Code omitted here for simplicity

def _load_cache():
    cache = msal.SerializableTokenCache()
    if session.get("token_cache"):
        cache.deserialize(session["token_cache"])
    return cache

def _save_cache(cache):
    if cache.has_state_changed:
        session["token_cache"] = cache.serialize()

def _build_msal_app(cache=None):
    return msal.ConfidentialClientApplication(
        app_config.CLIENT_ID, authority=app_config.AUTHORITY,
        client_credential=app_config.CLIENT_SECRET, token_cache=cache)
```

---

## <a name="next-steps"></a>次のステップ

この時点で、ユーザーがサインインすると、トークンはトークン キャッシュに格納されます。 それが Web アプリの他の部分でどのように使用されるかを見てみましょう。

[グローバル サインアウト時にキャッシュからアカウントを削除する](scenario-web-app-call-api-sign-in.md)
