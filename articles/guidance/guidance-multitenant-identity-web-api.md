<properties
   pageTitle="マルチテナント アプリケーションのバックエンド Web API をセキュリティで保護する | Microsoft Azure"
   description="バックエンド web API をセキュリティで保護する方法"
   services=""
   documentationCenter="na"
   authors="MikeWasson"
   manager="roshar"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/16/2016"
   ms.author="mwasson"/>

# マルチテナント アプリケーションのバックエンド Web API をセキュリティで保護する

この記事は[シリーズの一部]です。このシリーズに付属する完成した[サンプル アプリケーション]もあります。

[Tailspin Surveys] アプリケーションは、バックエンド Web API を使用してアンケートに対する CRUD 操作を管理しています。たとえば、ユーザーが [My Surveys] をクリックすると、Web アプリケーションから HTTP 要求が Web API に送信されます。

```
GET /users/{userId}/surveys
```

Web API からは JSON オブジェクトが返されます。

```
{
  "Published":[],
  "Own":[
    {"Id":1,"Title":"Survey 1"},
    {"Id":3,"Title":"Survey 3"},
    ],
  "Contribute": [{"Id":8,"Title":"My survey"}]
}
```

Web API は匿名要求を許可しないため、Web アプリは OAuth 2 ベアラー トークンを使用して自身を認証する必要があります。

> [AZURE.NOTE] これはサーバー対サーバーのシナリオです。アプリケーションは、ブラウザーから API に対して AJAX の呼び出しを実行しません。

使用できる主なアプローチは 2 つです。

- デリゲートされたユーザー ID。Web アプリケーションは、ユーザーの ID を使用して認証します。
- アプリケーション ID。Web アプリケーションは、OAuth2 クライアント資格情報フローで、自身のクライアント ID を使用して認証します。

Tailspin アプリケーションは、デリゲートされたユーザー ID を実装しています。主な違いは次のとおりです。

**デリゲートされたユーザー ID**

- Web API に送信されるベアラー トークンには、ユーザー ID が含まれています。
- Web API は、ユーザー ID に基づいて承認を決定します。
- ユーザーにアクションを実行する権限がない場合、Web アプリケーションは Web API から送信される 403 (Forbidden) エラーを処理する必要があります。
- 通常、Web アプリケーションは、UI に影響がある何らかの承認の決定を行います (UI 要素の表示、非表示など)。
- このアプローチでは、信用されていないクライアント (JavaScript アプリケーションや、ネイティブ クライアント アプリケーションなど) は Web API を使用できない可能性があります。

**アプリケーション ID**

- Web API は、ユーザーに関する情報を取得しません。
- Web API は、ユーザー ID に基づく承認を実行できません。すべての承認は、Web アプリケーションが決定します。  
- 信頼されていないクライアント (JavaScript やネイティブ クライアント アプリケーション) は、Web API を使用できません。
- このアプローチは、Web API に承認ロジックがないため、実装がやや簡単です。

いずれのアプローチでも、Web アプリケーションはアクセス トークンを取得する必要があります。アクセス トークンは、Web API の呼び出しに必要な資格情報です。

- デリゲートされたユーザー ID の場合、トークンは IDP から取得する必要があります。IDP はユーザーの代理でトークンを発行できます。

- クライアントの資格情報の場合、アプリケーションは IDP からトークンを取得するか、独自のトークン サーバーをホストすることができます(ただし、一からトークン サーバーを作成せずに、[IdentityServer3] などの十分にテストされたフレームワークを使用してください)。 Azure AD を使用して認証する場合、クライアント資格情報フローを使用している場合でも、Azure AD からアクセス トークンを取得することを強くお勧めします。

以降、この記事では、アプリケーションが Azure AD を使用して認証している前提で説明します。

![アクセス トークンを取得する](media/guidance-multitenant-identity/access-token.png)

## Azure AD に Web API を登録する

Azure AD から Web API のベアラー トークンを発行するには、Azure AD で構成が必要です。

1. [Azure AD に Web API を登録します]。

2. Web アプリのクライアント ID を、Web API アプリケーション マニフェストの `knownClientApplications` プロパティに追加します。[アプリケーション マニフェストの更新]に関するページを参照してください。

3. [Web API を呼び出すアクセス許可を Web アプリケーションに付与します]。

  Microsoft Azure 管理ポータルでは、2 種類のアクセス許可を設定できます。アプリケーション ID (クライアント資格情報フロー) 用の [アプリケーションのアクセス許可]、またはデリゲートされたユーザー ID 用の [デリゲートされたアクセス許可] です。

  ![デリゲートされたアクセス許可](media/guidance-multitenant-identity/delegated-permissions.png)

## アクセス トークンの取得

Web API を呼び出す前に、Web アプリケーションは Azure AD からアクセス トークンを取得します。.NET アプリケーションの場合、[Azure AD Authentication Library (ADAL) for .NET][ADAL] を使用します。

OAuth 2 承認コード フローの場合、アプリケーションはアクセス トークンの承認コードを交換します。次のコードでは、ADAL を使用してアクセス トークンを取得します。このコードは、`AuthorizationCodeReceived` イベント中に呼び出されます。

```csharp
// The OpenID Connect middleware sends this event when it gets the authorization code.   
public override async Task AuthorizationCodeReceived(AuthorizationCodeReceivedContext context)
{
    string authorizationCode = context.ProtocolMessage.Code;
    string authority = "https://login.microsoftonline.com/" + tenantID
    string resourceID = "https://tailspin.onmicrosoft.com/surveys.webapi" // App ID URI
    ClientCredential credential = new ClientCredential(clientId, clientSecret);

    AuthenticationContext authContext = new AuthenticationContext(authority, tokenCache);
    AuthenticationResult authResult = await authContext.AcquireTokenByAuthorizationCodeAsync(
        authorizationCode, new Uri(redirectUri), credential, resourceID);

    // If successful, the token is in authResult.AccessToken
}
```

必要な各種パラメーターを次に示します。

- `authority`サインインしたユーザーのテナント ID から派生します(SaaS プロバイダーのテナント ID ではありません)。  
- `authorizationCode` IDP から返される承認コード。
- `clientId`Web アプリケーションのクライアント ID。
- `clientSecret`Web アプリケーションのクライアント シークレット。
- `redirectUri`OpenID Connect 用に設定したリダイレクト URI。IDP がトークンを使用してコールバックする場所です。
- `resourceID`Web API のアプリ ID URI。Azure AD で Web API を登録するときに作成した URI です。
- `tokenCache`アクセス トークンをキャッシュするオブジェクト。[トークンのキャッシュ]に関するページを参照してください。

`AcquireTokenByAuthorizationCodeAsync` が成功すると、ADAL はトークンをキャッシュします。後でキャッシュからトークンを取得するには、次のように AcquireTokenSilentAsync を呼び出します。

```csharp
AuthenticationContext authContext = new AuthenticationContext(authority, tokenCache);
var result = await authContext.AcquireTokenSilentAsync(resourceID, credential, new UserIdentifier(userId, UserIdentifierType.UniqueId));
```

この `userId` はユーザーのオブジェクト ID です。この ID は `http://schemas.microsoft.com/identity/claims/objectidentifier` 要求内にあります。

## アクセス トークンを使用して Web API を呼び出す

トークンを取得したら、HTTP 要求の Authorization ヘッダーで Web API に送信します。

```
Authorization: Bearer xxxxxxxxxx
```

Surveys アプリケーションの次の拡張メソッドで、**HttpClient** クラスを使用して HTTP 要求の Authorization ヘッダーを設定します。

```csharp
public static async Task<HttpResponseMessage> SendRequestWithBearerTokenAsync(this HttpClient httpClient, HttpMethod method, string path, object requestBody, string accessToken, CancellationToken ct)
{
    var request = new HttpRequestMessage(method, path);
    if (requestBody != null)
    {
        var json = JsonConvert.SerializeObject(requestBody, Formatting.None);
        var content = new StringContent(json, Encoding.UTF8, "application/json");
        request.Content = content;
    }

    request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);
    request.Headers.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));

    var response = await httpClient.SendAsync(request, ct);
    return response;
}
```

> [AZURE.NOTE] [HttpClientExtensions.cs] を参照してください。

## Web API で認証する

Web API は、ベアラー トークンを認証する必要があります。ASP.NET Core 1.0 の場合、 [Microsoft.AspNet.Authentication.JwtBearer][JwtBearer] パッケージを使用できます。このパッケージには、OpenID Connect ベアラー トークンをアプリケーションに渡すことができるミドルウェアが含まれています。

Web API の `Startup` クラスにミドルウェアを登録します。

```csharp
app.UseJwtBearerAuthentication(options =>
{
    options.Audience = "[app ID URI]";
    options.Authority = "https://login.microsoftonline.com/common/";
    options.TokenValidationParameters = new TokenValidationParameters
    {
        //Instead of validating against a fixed set of known issuers, we perform custom multi-tenant validation logic
        ValidateIssuer = false,
    };
    options.Events = new SurveysJwtBearerEvents();
});
```

> [AZURE.NOTE] [Startup.cs] を参照してください。

- **Audience**。Azure AD に Web API を登録したときに作成した Web API のアプリ ID URL に Audience を設定します。
- **Authority**。マルチテナント アプリケーションの場合は、https://login.microsoftonline.com/common/`` に設定します。
- **TokenValidationParameters**。マルチテナント アプリケーションの場合は、 **ValidateIssuer** を false に設定します。つまり、アプリケーションは発行者を検証します。
- **Events** は、**JwtBearerEvents** から派生したクラスです。

### 発行者の検証

**JwtBearerEvents.ValidatedToken** イベントでトークン発行者を検証します。発行者は "iss" 要求で送信されます。

Surveys アプリケーションでは、Web API は[テナントのサインアップ]を処理しません。そのため、発行者がアプリケーション データベース内に存在するかどうかのみを確認します。存在しない場合は例外がスローされ、認証が失敗します。

```csharp
public override async Task ValidatedToken(ValidatedTokenContext context)
{
    var principal = context.AuthenticationTicket.Principal;
    var tenantManager = context.HttpContext.RequestServices.GetService<TenantManager>();
    var userManager = context.HttpContext.RequestServices.GetService<UserManager>();
    var issuerValue = principal.GetIssuerValue();
    var tenant = await tenantManager.FindByIssuerValueAsync(issuerValue);

    if (tenant == null)
    {
        // the caller was not from a trusted issuer - throw to block the authentication flow
        throw new SecurityTokenValidationException();
    }
}
```

> [AZURE.NOTE] [SurveysJwtBearerEvents.cs] を参照してください。

また、**ValidatedToken** イベントを使用して[要求の変換]を実行することもできます。ただし、要求は Azure AD から直接送信されるので、Web アプリケーションが要求の変換を実行しても、Web API が受け取るベアラー トークンには反映されません。

## 承認

承認の一般的な説明については、[承認]に関するページを参照してください。Web API の場合、主な違いは、JwtBearer ミドルウェアが承認の応答を処理する点です。

たとえば、認証済みユーザーに対するコントローラー アクションを制限する場合などです。

```csharp
[Authorize(ActiveAuthenticationSchemes = JwtBearerDefaults.AuthenticationScheme)]
```


ユーザーが認証されていない場合、401 状態コードが返されます。

承認ポリシーでコントローラー アクションを制限するには、次のコードを使用します。

```csharp
[Authorize(Policy = PolicyNames.RequireSurveyCreator)]
```

ユーザーが認証されていない場合は 401 状態コードが返されます。ユーザーが認証済みで承認されていない場合は 403 状態コードが返されます。起動時にポリシーを登録するには、次のコードを使用します。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthorization(options =>
    {
        options.AddPolicy(PolicyNames.RequireSurveyCreator,
            policy =>
            {
                policy.AddRequirements(new SurveyCreatorRequirement());
                policy.AddAuthenticationSchemes(JwtBearerDefaults.AuthenticationScheme);
            });
    });
}
```


## その他のリソース

- [Azure AD の認証シナリオ][auth-scenarios]

<!-- links -->
[ADAL]: https://msdn.microsoft.com/library/azure/jj573266.aspx
[auth-scenarios]: ../active-directory/active-directory-authentication-scenarios.md/#web-application-to-web-api
[JwtBearer]: https://www.nuget.org/packages/Microsoft.AspNet.Authentication.JwtBearer
[シリーズの一部]: guidance-multitenant-identity.md
[Tailspin Surveys]: guidance-multitenant-identity-tailspin.md
[IdentityServer3]: https://github.com/IdentityServer/IdentityServer3
[Azure AD に Web API を登録します]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/docs/running-the-app.md#register-the-surveys-web-api
[アプリケーション マニフェストの更新]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/docs/running-the-app.md#update-the-application-manifests
[Web API を呼び出すアクセス許可を Web アプリケーションに付与します]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/docs/running-the-app.md#give-the-web-app-permissions-to-call-the-web-api
[トークンのキャッシュ]: guidance-multitenant-identity-token-cache.md
[HttpClientExtensions.cs]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Common/HttpClientExtensions.cs
[Startup.cs]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.WebAPI/Startup.cs
[テナントのサインアップ]: guidance-multitenant-identity-signup.md
[SurveysJwtBearerEvents.cs]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.WebAPI/SurveyJwtBearerEvents.cs
[要求の変換]: guidance-multitenant-identity-claims.md#claims-transformations
[承認]: guidance-multitenant-identity-authorize.md
[サンプル アプリケーション]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps

<!---HONumber=AcomDC_0302_2016-->