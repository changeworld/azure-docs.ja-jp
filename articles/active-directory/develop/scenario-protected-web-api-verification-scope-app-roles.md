---
title: 保護された Web API でスコープとアプリ ロールを検証する | Azure
titleSuffix: Microsoft identity platform
description: 保護された Web API をビルドして、アプリケーションのコードを構成する方法について学習します。
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 816a9620a3486b534f9293084b7c4f5b4f748033
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/28/2020
ms.locfileid: "76768127"
---
# <a name="protected-web-api-verify-scopes-and-app-roles"></a>保護された Web API: スコープとアプリのロールを検証する

この記事では、Web API に承認を追加する方法について説明します。 この保護により、API を呼び出せるのは以下のものだけになります。

- 適切なスコープを持つユーザーに代わるアプリケーション。
- 適切なアプリケーション ロールを持つデーモン アプリ。

> [!NOTE]
> この記事のコード スニペットは、完全に機能する次のサンプルから抽出されています。
>
> - GitHub の [ASP.NET Core Web API 増分チュートリアル](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/02352945c1c4abb895f0b700053506dcde7ed04a/1.%20Desktop%20app%20calls%20Web%20API/TodoListService/Controllers/TodoListController.cs#L37)
> - [ASP.NET Web API のサンプル](https://github.com/Azure-Samples/ms-identity-aspnet-webapi-onbehalfof/blob/dfd0115533d5a230baff6a3259c76cf117568bd9/TodoListService/Controllers/TodoListController.cs#L48)

ASP.NET または ASP.NET Core の Web API を保護するには、次のいずれかの項目に `[Authorize]` 属性を追加する必要があります。

- すべてのコントローラー アクションを保護する場合は、コントローラー自体
- API の個々のコントローラー アクション

```csharp
    [Authorize]
    public class TodoListController : Controller
    {
     ...
    }
```

ただし、この保護は不十分です。 ASP.NET および ASP.NET Core でトークンが検証されることしか保証されません。 API は、API の呼び出しに使用されるトークンが、予期された要求を使用して要求されていることを検証する必要があります。 特に次の要求では検証が必要です。

- API がユーザーの代わりに呼び出される場合は、"*スコープ*"。
- API をデーモン アプリから呼び出すことができる場合は、"*アプリ ロール*"。

## <a name="verify-scopes-in-apis-called-on-behalf-of-users"></a>ユーザーに代わって呼び出される API のスコープの確認

クライアント アプリがユーザーに代わって API を呼び出す場合、API は、API 用の特定のスコープを持つベアラー トークンを要求する必要があります。 詳細については、[「コード構成」の「ベアラー トークン」](scenario-protected-web-api-app-configuration.md#bearer-token)を参照してください。

```csharp
[Authorize]
public class TodoListController : Controller
{
    /// <summary>
    /// The web API will accept only tokens 1) for users, 2) that have the `access_as_user` scope for
    /// this API.
    /// </summary>
    const string scopeRequiredByAPI = "access_as_user";

    // GET: api/values
    [HttpGet]
    public IEnumerable<TodoItem> Get()
    {
        VerifyUserHasAnyAcceptedScope(scopeRequiredByAPI);
        // Do the work and return the result.
        ...
    }
...
}
```

`VerifyUserHasAnyAcceptedScope` メソッドは、次の手順のような処理を行います。

- `http://schemas.microsoft.com/identity/claims/scope` または `scp` という名前の要求があることを確認します。
- 要求が、API で想定されているスコープを含む値を持っていることを確認します。

```csharp
    /// <summary>
    /// When applied to a <see cref="HttpContext"/>, verifies that the user authenticated in the
    /// web API has any of the accepted scopes.
    /// If the authenticated user doesn't have any of these <paramref name="acceptedScopes"/>, the
    /// method throws an HTTP Unauthorized error with a message noting which scopes are expected in the token.
    /// </summary>
    /// <param name="acceptedScopes">Scopes accepted by this API</param>
    /// <exception cref="HttpRequestException"/> with a <see cref="HttpResponse.StatusCode"/> set to 
    /// <see cref="HttpStatusCode.Unauthorized"/>
    public static void VerifyUserHasAnyAcceptedScope(this HttpContext context,
                                                     params string[] acceptedScopes)
    {
        if (acceptedScopes == null)
        {
            throw new ArgumentNullException(nameof(acceptedScopes));
        }
        Claim scopeClaim = HttpContext?.User
                                      ?.FindFirst("http://schemas.microsoft.com/identity/claims/scope");
        if (scopeClaim == null || !scopeClaim.Value.Split(' ').Intersect(acceptedScopes).Any())
        {
            context.Response.StatusCode = (int)HttpStatusCode.Unauthorized;
            string message = $"The 'scope' claim does not contain scopes '{string.Join(",", acceptedScopes)}' or was not found";
            throw new HttpRequestException(message);
        }
    }
```

上記の[サンプル コード](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/02352945c1c4abb895f0b700053506dcde7ed04a/Microsoft.Identity.Web/Resource/ScopesRequiredByWebAPIExtension.cs#L47)は、ASP.NET Core 用です。 ASP.NET の場合は、`HttpContext.User` を `ClaimsPrincipal.Current` に置き換え、要求の種類 `"http://schemas.microsoft.com/identity/claims/scope"` を `"scp"` に置き換えるだけです この記事の後の方のコード スニペットも参照してください。

## <a name="verify-app-roles-in-apis-called-by-daemon-apps"></a>デーモン アプリによって呼び出される API のアプリ ロールの確認

Web API が [デーモン アプリ](scenario-daemon-overview.md)によって呼び出された場合、そのアプリには Web API に対するアプリケーションのアクセス許可が必要です。 「[アプリケーションのアクセス許可 (アプリ ロール) の公開](https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-app-registration#exposing-application-permissions-app-roles)」に示されているように、API はそのようなアクセス許可を公開します。 1 つの例として、`access_as_application` アプリ ロールがあります。

ここでは、受け取ったトークンに `roles` 要求が含まれていることと、この要求に想定されている値が含まれていることを API に確認させる必要があります。 この確認コードは、委任されたアクセス許可を確認するコードと似ています。異なるのは、コントローラー アクションでテストされるのが、スコープではなくロールである点です。

```csharp
[Authorize]
public class TodoListController : ApiController
{
    public IEnumerable<TodoItem> Get()
    {
        ValidateAppRole("access_as_application");
        ...
    }
```

`ValidateAppRole` メソッドは、次のようなものになります。

```csharp
private void ValidateAppRole(string appRole)
{
    //
    // The `role` claim tells you what permissions the client application has in the service.
    // In this case, we look for a `role` value of `access_as_application`.
    //
    Claim roleClaim = ClaimsPrincipal.Current.FindFirst("roles");
    if (roleClaim == null || !roleClaim.Value.Split(' ').Contains(appRole))
    {
        throw new HttpResponseException(new HttpResponseMessage
        { StatusCode = HttpStatusCode.Unauthorized,
            ReasonPhrase = $"The 'roles' claim does not contain '{appRole}' or was not found"
        });
    }
}
}
```

今回は、このコード スニペットは ASP.NET 用です。 ASP.NET Core の場合は、`ClaimsPrincipal.Current` を `HttpContext.User` に置き換え、要求の名前 `"roles"` を `"http://schemas.microsoft.com/identity/claims/roles"` に置き換えるだけです この記事の前の方のコード スニペットも参照してください。

### <a name="accepting-app-only-tokens-if-the-web-api-should-be-called-only-by-daemon-apps"></a>Web API がデーモン アプリのみによって呼び出される必要がある場合のアプリ専用トークンの受け入れ

ユーザーは、「[方法: アプリケーションにアプリ ロールを追加してトークンで受け取る](howto-add-app-roles-in-azure-ad-apps.md)」に示されているように、ユーザー割り当てパターンでロール要求を使用することもできます。 ロールが両方に割り当て可能な場合は、ロールをチェックすると、アプリはユーザーとして、ユーザーはアプリとしてサインインできるようになります。 この混乱を避けるために、ユーザー用とアプリ用に異なるロールを宣言することをお勧めします。

デーモン アプリのみが Web API を呼び出せるようにしたい場合は、アプリ ロールを検証するときに、トークンがアプリ専用トークンであるという条件を追加します。

```csharp
string oid = ClaimsPrincipal.Current.FindFirst("oid")?.Value;
string sub = ClaimsPrincipal.Current.FindFirst("sub")?.Value;
bool isAppOnlyToken = oid == sub;
```

逆の条件をチェックすると、ユーザーとしてサインインするアプリのみが API を呼び出すことができます。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [運用環境への移行](scenario-protected-web-api-production.md)
