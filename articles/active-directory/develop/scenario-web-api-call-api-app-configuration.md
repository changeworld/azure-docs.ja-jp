---
title: Web API を呼び出す Web API を構成する | Azure
titleSuffix: Microsoft identity platform
description: Web API を呼び出す Web API を構築する方法について説明します (アプリのコード構成)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/16/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 82b5e1d9753fbb65fd81f24b06016d302457144e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "76834095"
---
# <a name="a-web-api-that-calls-web-apis-code-configuration"></a>Web API を呼び出す Web API:コード構成

Web API を登録した後は、アプリケーションのコードを構成することができます。

ダウンストリーム Web API を呼び出すように Web API を構成するために使用するコードは、Web API を保護するために使用されるコードの上に構築されます。 詳細については、[保護された Web API: アプリ構成](scenario-protected-web-api-app-configuration.md)に関するページを参照してください。

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

## <a name="code-subscribed-to-ontokenvalidated"></a>OnTokenValidated をサブスクライブするコード

次のように、保護されている Web API のコード構成に加え、API が呼び出されたときに受け取るベアラー トークンの検証をサブスクライブする必要があります。

```csharp
/// <summary>
/// Protects the web API with the Microsoft identity platform, or Azure Active Directory (Azure AD) developer platform
/// This supposes that the configuration files have a section named "AzureAD"
/// </summary>
/// <param name="services">The service collection to which to add authentication</param>
/// <param name="configuration">Configuration</param>
/// <returns></returns>
public static IServiceCollection AddProtectedApiCallsWebApis(this IServiceCollection services,
                                                             IConfiguration configuration,
                                                             IEnumerable<string> scopes)
{
    services.AddTokenAcquisition();
    services.Configure<JwtBearerOptions>(AzureADDefaults.JwtBearerAuthenticationScheme, options =>
    {
        // When an access token for our own web API is validated, we add it
        // to the MSAL.NET cache so that it can be used from the controllers.
        options.Events = new JwtBearerEvents();

        options.Events.OnTokenValidated = async context =>
        {
            context.Success();

            // Adds the token to the cache and handles the incremental consent
            // and claim challenges
            AddAccountToCacheFromJwt(context, scopes);
            await Task.FromResult(0);
        };
    });
    return services;
}
```

## <a name="on-behalf-of-flow"></a>On-Behalf-Of フロー

AddAccountToCacheFromJwt() メソッドは次を実行する必要があります。

- Microsoft Authentication Library (MSAL) 機密クライアント アプリケーションをインスタンス化します。
- `AcquireTokenOnBehalf` メソッドを呼び出します。 この呼び出しにより、Web API のクライアントによって取得されたベアラー トークンが同じユーザーのベアラー トークンに照らして交換されます (ただし、ダウンストリーム API を呼び出す API が含まれています)。

### <a name="instantiate-a-confidential-client-application"></a>機密クライアントをインスタンス化する

このフローは機密クライアント フローでのみ使用可能なため、保護された Web API は、クライアントの資格情報 (クライアント シークレットまたは証明書) を、`WithClientSecret` メソッドまたは `WithCertificate` メソッドを介して [ConfidentialClientApplicationBuilder](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationbuilder) クラスに提供します。

![IConfidentialClientApplication メソッドの一覧](https://user-images.githubusercontent.com/13203188/55967244-3d8e1d00-5c7a-11e9-8285-a54b05597ec9.png)

```csharp
IConfidentialClientApplication app;

#if !VariationWithCertificateCredentials
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
           .WithClientSecret(config.ClientSecret)
           .Build();
#else
// Building the client credentials from a certificate
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
    .WithCertificate(certificate)
    .Build();
#endif
```

最後に、機密クライアント アプリケーションでは、クライアント シークレットや証明書を介して ID を証明するのではなく、クライアント アサーションを使用して ID を証明することができます。
この高度なシナリオの詳細については、「[機密クライアント アサーション](msal-net-client-assertions.md)」を参照してください。

### <a name="how-to-call-on-behalf-of"></a>On-Behalf-Of を呼び出す方法

On-Behalf-Of (OBO) 呼び出しを行うには、`IConfidentialClientApplication` インターフェイスで [AcquireTokenOnBehalf メソッド](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.acquiretokenonbehalfofparameterbuilder)を呼び出します。

`UserAssertion` クラスは、Web API によって専用のクライアントから受信されるベアラー トークンで構築されます。 [2 つのコンストラクター](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.clientcredential.-ctor?view=azure-dotnet)、すなわち
* JSON Web トークン (JWT) ベアラー トークンを取得するものと、
* 任意の種類のユーザー アサーション (別の種類のセキュリティ トークン。その型は、`assertionType` という名前の追加パラメーターに指定される) を取得するものがあります

![UserAssertion のプロパティとメソッド](https://user-images.githubusercontent.com/13203188/37082180-afc4b708-21e3-11e8-8af8-a6dcbd2dfba8.png)

実際には、OBO フローは、ダウンストリーム API のトークンを取得して、それを MSAL.NET ユーザー トークン キャッシュに格納するためによく使用されます。 こうすれば、Web API の他の部分は、後で ``AcquireTokenOnSilent`` の[オーバーライド](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.clientapplicationbase.acquiretokensilent?view=azure-dotnet)で呼び出してダウンストリーム API を呼び出すことができます。 この呼び出しには、必要な場合、トークンを最新の情報に更新する効果があります。

```csharp
private void AddAccountToCacheFromJwt(IEnumerable<string> scopes, JwtSecurityToken jwtToken, ClaimsPrincipal principal, HttpContext httpContext)
{
    try
    {
        UserAssertion userAssertion;
        IEnumerable<string> requestedScopes;
        if (jwtToken != null)
        {
            userAssertion = new UserAssertion(jwtToken.RawData, "urn:ietf:params:oauth:grant-type:jwt-bearer");
            requestedScopes = scopes ?? jwtToken.Audiences.Select(a => $"{a}/.default");
        }
        else
        {
            throw new ArgumentOutOfRangeException("tokenValidationContext.SecurityToken should be a JWT Token");
        }

        // Create the application
        var application = BuildConfidentialClientApplication(httpContext, principal);

        // .Result to make sure that the cache is filled in before the controller tries to get access tokens
        var result = application.AcquireTokenOnBehalfOf(requestedScopes.Except(scopesRequestedByMsalNet),
                                                        userAssertion)
                                .ExecuteAsync()
                                .GetAwaiter().GetResult();
     }
     catch (MsalException ex)
     {
         Debug.WriteLine(ex.Message);
         throw;
     }
}
```
# <a name="java"></a>[Java](#tab/java)

On-Behalf-Of (OBO) フローは、ダウンストリーム Web API を呼び出すトークンを取得するために使用されます。 このフローでは、Web API は、ユーザーが委任したアクセス許可を持つベアラー トークンをクライアント アプリケーションから受信し、このトークンを別のアクセス トークンと交換してダウンストリーム Web API を呼び出します。

次のコードでは、Web API で Spring Security フレームワークの `SecurityContextHolder` を使用して、検証済みのベアラー トークンを取得します。 次に、MSAL Java ライブラリを使用して、`OnBehalfOfParameters` を設定した `acquireToken` を使ってダウンストリーム API のトークンを取得します。 MSAL はトークンをキャッシュし、API への後続の呼び出しで `acquireTokenSilently` を使用してキャッシュされたトークンを取得できるようにします。

```Java
@Component
class MsalAuthHelper {

    @Value("${security.oauth2.client.authority}")
    private String authority;

    @Value("${security.oauth2.client.client-id}")
    private String clientId;

    @Value("${security.oauth2.client.client-secret}")
    private String secret;

    @Autowired
    CacheManager cacheManager;

    private String getAuthToken(){
        String res = null;
        Authentication authentication = SecurityContextHolder.getContext().getAuthentication();

        if(authentication != null){
            res = ((OAuth2AuthenticationDetails) authentication.getDetails()).getTokenValue();
        }
        return res;
    }

    String getOboToken(String scope) throws MalformedURLException {
        String authToken = getAuthToken();

        ConfidentialClientApplication application =
                ConfidentialClientApplication.builder(clientId, ClientCredentialFactory.create(secret))
                        .authority(authority).build();

        String cacheKey = Hashing.sha256()
                .hashString(authToken, StandardCharsets.UTF_8).toString();

        String cachedTokens = cacheManager.getCache("tokens").get(cacheKey, String.class);
        if(cachedTokens != null){
            application.tokenCache().deserialize(cachedTokens);
        }

        IAuthenticationResult auth;
        SilentParameters silentParameters =
                SilentParameters.builder(Collections.singleton(scope))
                        .build();
        auth = application.acquireTokenSilently(silentParameters).join();

        if (auth == null){
            OnBehalfOfParameters parameters =
                    OnBehalfOfParameters.builder(Collections.singleton(scope),
                            new UserAssertion(authToken))
                            .build();

            auth = application.acquireToken(parameters).join();
        }

        cacheManager.getCache("tokens").put(cacheKey, application.tokenCache().serialize());

        return auth.accessToken();
    }
}
```

# <a name="python"></a>[Python](#tab/python)

On-Behalf-Of (OBO) フローは、ダウンストリーム Web API を呼び出すトークンを取得するために使用されます。 このフローでは、Web API は、ユーザーが委任したアクセス許可を持つベアラー トークンをクライアント アプリケーションから受信し、このトークンを別のアクセス トークンと交換してダウンストリーム Web API を呼び出します。

Python Web API は、クライアントから受信したベアラー トークンを検証するために何らかのミドルウェアを使用する必要があります。 Web API は、[`acquire_token_on_behalf_of`](https://msal-python.readthedocs.io/en/latest/?badge=latest#msal.ConfidentialClientApplication.acquire_token_on_behalf_of) メソッドを呼び出すことにより、MSAL Python ライブラリを使用してダウンストリーム API のアクセス トークンを取得できます。 このフローを MSAL Python でデモンストレーションするサンプルはまだ用意されていません。

---

また、[Node.js と Azure Functions](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-onbehalfof-azurefunctions/blob/master/MiddleTierAPI/MyHttpTrigger/index.js#L61) への OBO フロー実装の例を確認することもできます。

## <a name="protocol"></a>Protocol

OBO プロトコルの詳細については、「[Microsoft ID プラットフォームと OAuth 2.0 On-Behalf-Of フロー](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow)」をご覧ください。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Web API を呼び出す Web API:アプリのトークンの取得](scenario-web-api-call-api-acquire-token.md)
