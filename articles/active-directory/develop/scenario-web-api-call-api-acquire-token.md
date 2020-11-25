---
title: Web API を呼び出す Web API のトークンを取得する | Azure
titleSuffix: Microsoft identity platform
description: アプリのトークンを取得する必要がある Web API を呼び出す Web API を構築する方法について説明します。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/15/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 465544f1d861fc09d4b843270c6f3527036ee6a8
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/14/2020
ms.locfileid: "94628008"
---
# <a name="a-web-api-that-calls-web-apis-acquire-a-token-for-the-app"></a>Web API を呼び出す Web API: アプリのトークンを取得する

クライアント アプリケーション オブジェクトを構築した後、それを使用して、Web API を呼び出すために使用できるトークンを取得します。

## <a name="code-in-the-controller"></a>コントローラーのコード

### <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

*Microsoft.Identity.Web* には、Microsoft Graph またはダウンストリーム Web API を呼び出す便利なサービスを提供する拡張メソッドが追加されています。 これらのメソッドの詳細については、「[Web API を呼び出す Web API:API を呼び出す](scenario-web-api-call-api-call-api.md)」を参照してください。 これらのヘルパー メソッドを使用すれば、トークンを手動で取得する必要はありません。

ただし、トークンを手動で取得する場合は、API コントローラーで取得することを目的とした *Microsoft.Identity.Web* の使用の例が、次のコードによって示されています。 *todolist* という名前のダウンストリーム API を呼び出します。
ダウンストリーム API を呼び出すためのトークンを取得するには、コントローラーのコンストラクター (Blazor を使用する場合はページ コンストラクター) に依存関係を挿入して `ITokenAcquisition` サービスを挿入し、コントローラー アクションで使用します。これで、ユーザーのトークン (`GetAccessTokenForUserAsync`)、またはデーモン シナリオの場合はアプリケーション自体 (`GetAccessTokenForAppAsync`) のトークンが取得さされます。

```csharp
[Authorize]
public class MyApiController : Controller
{
    /// <summary>
    /// The web API will accept only tokens 1) for users, 2) that have the `access_as_user` scope for
    /// this API.
    /// </summary>
    static readonly string[] scopeRequiredByApi = new string[] { "access_as_user" };

     static readonly string[] scopesToAccessDownstreamApi = new string[] { "api://MyTodolistService/access_as_user" };

    private readonly ITokenAcquisition _tokenAcquisition;

    public MyApiController(ITokenAcquisition tokenAcquisition)
    {
        _tokenAcquisition = tokenAcquisition;
    }

    public IActionResult Index()
    {
        HttpContext.VerifyUserHasAnyAcceptedScope(scopeRequiredByApi);

        string accessToken = _tokenAcquisition.GetAccessTokenForUserAsync(scopesToAccessDownstreamApi);
        return await callTodoListService(accessToken);
    }
}
```

`callTodoListService` メソッドの詳細については、「[Web API を呼び出す Web API: API を呼び出す](scenario-web-api-call-api-call-api.md)」を参照してください。

### <a name="java"></a>[Java](#tab/java)

API コントローラーのアクションで呼び出されるコードの例を次に示します。 ダウンストリーム API - Microsoft Graph が呼び出されます。

```java
@RestController
public class ApiController {

    @Autowired
    MsalAuthHelper msalAuthHelper;

    @RequestMapping("/graphMeApi")
    public String graphMeApi() throws MalformedURLException {

        String oboAccessToken = msalAuthHelper.getOboToken("https://graph.microsoft.com/.default");

        return callMicrosoftGraphMeEndpoint(oboAccessToken);
    }

}
```

### <a name="python"></a>[Python](#tab/python)

Python Web API では、クライアントから受信したベアラー トークンを検証するためにミドルウェアを使用する必要があります。 Web API は、[`acquire_token_on_behalf_of`](https://msal-python.readthedocs.io/en/latest/?badge=latest#msal.ConfidentialClientApplication.acquire_token_on_behalf_of) メソッドを呼び出すことにより、MSAL Python ライブラリを使用してダウンストリーム API のアクセス トークンを取得できます。 このフローを MSAL Python でデモンストレーションするサンプルはまだ用意されていません。

## <a name="advanced-accessing-the-signed-in-users-token-cache-from-background-apps-apis-and-services"></a>(上級) バックグラウンドのアプリ、API、サービスからサインイン ユーザーのトークン キャッシュにアクセスする

[!INCLUDE [advanced-token-caching](../../../includes/advanced-token-cache.md)]

---

## <a name="next-steps"></a>次のステップ

このシナリオの次の記事である [API の呼び出し](scenario-web-api-call-api-call-api.md)に関する記事に進みます。
