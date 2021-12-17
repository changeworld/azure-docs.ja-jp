---
title: 保護された Web API でスコープとアプリ ロールを検証する | Azure
titleSuffix: Microsoft identity platform
description: API が、適切なスコープを持つユーザーに代わるアプリケーションによって、また適切なアプリケーション ロールを持つデーモン アプリによってのみ呼び出されることを確認します。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/19/2021
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 23b9f2e8bb14d74a7f2b722945251acb182b71db
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130222573"
---
# <a name="protected-web-api-verify-scopes-and-app-roles"></a>保護された Web API: スコープとアプリのロールを検証する

この記事では、Web API に承認を追加する方法について説明します。 この保護により、API を呼び出せるのは以下のものだけになります。

- 適切なスコープを持つユーザーに代わるアプリケーション。
- 適切なアプリケーション ロールを持つデーモン アプリ。

この記事のコード スニペットは、GitHub の以下のコード サンプルから抜粋されたものです。

- [ASP.NET Core Web API の増分チュートリアル](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/master/1.%20Desktop%20app%20calls%20Web%20API/TodoListService/Controllers/TodoListController.cs)
- [ASP.NET Web API のサンプル](https://github.com/Azure-Samples/ms-identity-aspnet-webapi-onbehalfof/blob/master/TodoListService/Controllers/TodoListController.cs)

ASP.NET または ASP.NET Core の Web API を保護するには、次のいずれかの項目に `[Authorize]` 属性を追加する必要があります。

- すべてのコントローラー アクションを保護する場合は、コントローラー自体
- API の個々のコントローラー アクション

```csharp
    [Authorize]
    public class TodoListController : Controller
    {
     // ...
    }
```

ただし、この保護は不十分です。 ASP.NET および ASP.NET Core でトークンが検証されることしか保証されません。 API は、API の呼び出しに使用されるトークンが、予期された要求を使用して要求されていることを検証する必要があります。 特に次の要求では検証が必要です。

- API がユーザーの代わりに呼び出される場合は、"_スコープ_"。
- API をデーモン アプリから呼び出すことができる場合は、"_アプリ ロール_"。

## <a name="verify-scopes-in-apis-called-on-behalf-of-users"></a>ユーザーに代わって呼び出される API のスコープの確認

クライアント アプリがユーザーに代わって API を呼び出す場合、API は、API 用の特定のスコープを持つベアラー トークンを要求する必要があります。 詳細については、[「コード構成」の「ベアラー トークン」](scenario-protected-web-api-app-configuration.md#bearer-token)を参照してください。

### <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

ASP.NET Core では、各コントローラー アクションのスコープを検証するために、Microsoft.Identity.Web を使用できます。 コントローラーのレベルまたはアプリケーション全体で検証することもできます。

#### <a name="verify-the-scopes-on-each-controller-action"></a>各コントローラー アクションのスコープを確認する

`[RequiredScope]` 属性を使用して、コントローラー アクション内のスコープを検証できます。 この属性にはいくつかのオーバーライドがあります。 必要なスコープを直接取得するものと、構成に対するキーを取得するものです。

##### <a name="verify-the-scopes-on-a-controller-action-with-hardcoded-scopes"></a>ハードコードされたスコープでコントローラー アクションのスコープを検証する

次のコード スニペットは、ハードコードされたスコープでの `[RequiredScope]` 属性の使用方法を示しています。

```csharp
using Microsoft.Identity.Web

[Authorize]
public class TodoListController : Controller
{
    /// <summary>
    /// The web API will accept only tokens that have the `access_as_user` scope for
    /// this API.
    /// </summary>
    static readonly string[] scopeRequiredByApi = new string[] { "access_as_user" };

    // GET: api/values
    [HttpGet]
    [RequiredScope(scopeRequiredByApi)]
    public IEnumerable<TodoItem> Get()
    {
        // Do the work and return the result.
        // ...
    }
 // ...
}
```

##### <a name="verify-the-scopes-on-a-controller-action-with-scopes-defined-in-configuration"></a>構成で定義されたスコープを使用して、コントローラー アクションのスコープを検証する

構成でこれらの必要なスコープを宣言し、構成キーを参照することもできます。

たとえば、appsettings.json に次のような構成があるとします。

```JSon
{
 "AzureAd" : {
   // more settings
   "Scopes" : "access_as_user access_as_admin"
  }
}
```

この場合、`[RequiredScope]` 属性でこれを参照します。

```csharp
using Microsoft.Identity.Web

[Authorize]
public class TodoListController : Controller
{
    // GET: api/values
    [HttpGet]
    [RequiredScope(RequiredScopesConfigurationKey = "AzureAd:Scopes")]
    public IEnumerable<TodoItem> Get()
    {
        // Do the work and return the result.
        // ...
    }
 // ...
}
```

##### <a name="verify-scopes-conditionally"></a>スコープを条件付きで検証する

スコープを条件付きで検証したい場合があります。 これは、`HttpContext` で `VerifyUserHasAnyAcceptedScope` 拡張メソッドを使用して行います。

```csharp
using Microsoft.Identity.Web

[Authorize]
public class TodoListController : Controller
{
    /// <summary>
    /// The web API will accept only tokens 1) for users, 2) that have the `access_as_user` scope for
    /// this API.
    /// </summary>
    static readonly string[] scopeRequiredByApi = new string[] { "access_as_user" };

    // GET: api/values
    [HttpGet]
    public IEnumerable<TodoItem> Get()
    {
         HttpContext.VerifyUserHasAnyAcceptedScope(scopeRequiredByApi);
        // Do the work and return the result.
        // ...
    }
 // ...
}
```

#### <a name="verify-the-scopes-at-the-level-of-the-controller"></a>コントローラーのレベルでスコープを検証する

コントローラー全体のスコープを検証することもできます

##### <a name="verify-the-scopes-on-a-controller-with-hardcoded-scopes"></a>ハードコードされたスコープでコントローラーのスコープを検証する

次のコード スニペットは、コントローラーのハードコードされたスコープでの `[RequiredScope]` 属性の使用方法を示しています。

```csharp
using Microsoft.Identity.Web

[Authorize]
[RequiredScope(scopeRequiredByApi)]
public class TodoListController : Controller
{
    /// <summary>
    /// The web API will accept only tokens 1) for users, 2) that have the `access_as_user` scope for
    /// this API.
    /// </summary>
    const string[] scopeRequiredByApi = new string[] { "access_as_user" };

    // GET: api/values
    [HttpGet]
    public IEnumerable<TodoItem> Get()
    {
        // Do the work and return the result.
        // ...
    }
 // ...
}
```

##### <a name="verify-the-scopes-on-a-controller-with-scopes-defined-in-configuration"></a>構成で定義されたスコープを使用して、コントローラーのスコープを検証する

アクションと同様に、構成でこれらの必要なスコープを宣言し、構成キーを参照することもできます。

```csharp
using Microsoft.Identity.Web

[Authorize]
[RequiredScope(RequiredScopesConfigurationKey = "AzureAd:Scopes")
public class TodoListController : Controller
{
    // GET: api/values
    [HttpGet]
    public IEnumerable<TodoItem> Get()
    {
        // Do the work and return the result.
        // ...
    }
 // ...
}
```

#### <a name="verify-the-scopes-more-globally"></a>スコープをよりグローバルに検証する

Web API の詳細なスコープを定義し、各コントローラー アクションでスコープを確認することが、お勧めの方法です。 ただし、アプリケーションまたはコントローラーのレベルでスコープを検証することもできます。 詳細については、ASP.NET のコア ドキュメントの[要求ベースの承認](/aspnet/core/security/authorization/claims)に関する記事を参照してください。

#### <a name="what-is-verified"></a>検証される対象

`[RequiredScope]` 属性と `VerifyUserHasAnyAcceptedScope` メソッドは、次の手順のような処理を行います。

- `http://schemas.microsoft.com/identity/claims/scope` または `scp` という名前の要求があることを確認します。
- 要求が、API で想定されているスコープを含む値を持っていることを確認します。

### <a name="aspnet-classic"></a>[ASP.NET Classic](#tab/aspnet)

ASP.NET アプリケーションでは、次の方法でスコープを検証できます。

```CSharp
[Authorize]
public class TodoListController : ApiController
{
    public IEnumerable<TodoItem> Get()
    {
       ValidateScopes(new[] {"read"; "admin" } );
       // ...
    }
```

次に、`ValidateScopes` の簡略化されたバージョンを示します。

```csharp
private void ValidateScopes(IEnumerable<string> acceptedScopes)
{
    //
    // The `role` claim tells you what permissions the client application has in the service.
    // In this case, we look for a `role` value of `access_as_application`.
    //
    Claim scopeClaim = ClaimsPrincipal.Current.FindFirst("scp");
    if (scopeClaim == null || !scopeClaim.Value.Split(' ').Intersect(acceptedScopes).Any())
    {
        throw new HttpResponseException(new HttpResponseMessage
        { StatusCode = HttpStatusCode.Forbidden,
            ReasonPhrase = $"The 'scp' claim does not contain '{scopeClaim}' or was not found"
        });
    }
}
```

ASP.NET Core の `ValidateScopes` の完全なバージョンは、[_ScopesRequiredHttpContextExtensions.cs_](https://github.com/AzureAD/microsoft-identity-web/blob/master/src/Microsoft.Identity.Web/Resource/ScopesRequiredHttpContextExtensions.cs)

---

## <a name="verify-app-roles-in-apis-called-by-daemon-apps"></a>デーモン アプリによって呼び出される API のアプリ ロールの確認

Web API が [デーモン アプリ](scenario-daemon-overview.md)によって呼び出された場合、そのアプリには Web API に対するアプリケーションのアクセス許可が必要です。 「[アプリケーションのアクセス許可 (アプリ ロール) の公開](./scenario-protected-web-api-app-registration.md#exposing-application-permissions-app-roles)」に示されているように、API はそのようなアクセス許可を公開します。 1 つの例として、`access_as_application` アプリ ロールがあります。

ここでは、受け取ったトークンに `roles` 要求が含まれていることと、この要求に想定されている値が含まれていることを API に確認させる必要があります。 この確認コードは、委任されたアクセス許可を確認するコードと似ています。異なるのは、コントローラー アクションでテストされるのが、スコープではなくロールである点です。

### <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

次のコード スニペットは、アプリケーション ロールを検証する方法を示しています

```csharp
using Microsoft.Identity.Web

[Authorize]
public class TodoListController : ApiController
{
    public IEnumerable<TodoItem> Get()
    {
        HttpContext.ValidateAppRole("access_as_application");
        // ...
    }
```

代わりに、コントローラーまたはアクション (または razor ページ) で [Authorize ("role")] 属性を使用できます。

```CSharp
[Authorize("role")]
MyController : ApiController
{
    // ...
}
```

ただし、この場合は、ロール要求を Startup.cs ファイルの "roles" にマップする必要があります。

```CSharp
 services.Configure<OpenIdConnectOptions>(OpenIdConnectDefaults.AuthenticationScheme, options =>
 {
    // The claim in the Jwt token where App roles are available.
    options.TokenValidationParameters.RoleClaimType = "roles";
 });
```

これは、グループに基づいて認可を行う必要がある場合に最適なソリューションではありません。

詳細については、[ロールとグループによる認可](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/5-WebApp-AuthZ)に関する Web アプリの増分チュートリアルを参照してください。

### <a name="aspnet-classic"></a>[ASP.NET Classic](#tab/aspnet)

ASP.NET アプリケーションでは、次の方法でアプリ ロールを検証できます。

```CSharp
[Authorize]
public class TodoListController : ApiController
{
    public IEnumerable<TodoItem> Get()
    {
       ValidateAppRole("access_as_application");
       // ...
    }
```

`ValidateAppRole` の簡略化されたバージョンは、次のとおりです。

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
```

ASP.NET Core の `ValidateAppRole` の完全バージョンは、[_RolesRequiredHttpContextExtensions.cs_](https://github.com/AzureAD/microsoft-identity-web/blob/master/src/Microsoft.Identity.Web/Resource/RolesRequiredHttpContextExtensions.cs) コードを参照してください。

---

### <a name="accepting-app-only-tokens-if-the-web-api-should-be-called-only-by-daemon-apps"></a>Web API がデーモン アプリのみによって呼び出される必要がある場合のアプリ専用トークンの受け入れ

ユーザーは、ユーザー割り当てパターン内でロール要求を使用することもできます。使用方法は、「[方法: アプリケーションにアプリ ロールを追加してトークンで受け取る](howto-add-app-roles-in-azure-ad-apps.md)」を参照してください)。 ロールが両方に割り当て可能な場合は、ロールをチェックすると、アプリはユーザーとして、ユーザーはアプリとしてサインインできるようになります。 この混乱を避けるために、ユーザー用とアプリ用に異なるロールを宣言することをお勧めします。

デーモン アプリのみが Web API を呼び出せるようにしたい場合は、アプリ ロールを検証するときに、トークンがアプリ専用トークンであるという条件を追加します。

```csharp
string oid = ClaimsPrincipal.Current.FindFirst("oid")?.Value;
string sub = ClaimsPrincipal.Current.FindFirst("sub")?.Value;
bool isAppOnly = oid != null && sub != null && oid == sub;
```

逆の条件をチェックすると、ユーザーとしてサインインするアプリのみが API を呼び出すことができます。

### <a name="using-acl-based-authorization"></a>ACL ベースの認可の使用

アプリロール ベースの認可の代わりに、[`roles` 要求のないトークンを制御する](v2-oauth2-client-creds-grant-flow.md#controlling-tokens-without-the-roles-claim)ためのアクセス制御リスト (ACL) ベースの認可パターンで Web API を保護することができます。

ASP.NET Core で Microsoft.Identity.Web を使用している場合は、ACL ベースの認可を使用していることを宣言する必要があります。そうしないと、指定された要求にロールもスコープも含まれない場合、Microsoft Identity Web は例外をスローします。

```Text
System.UnauthorizedAccessException: IDW10201: Neither scope or roles claim was found in the bearer token.
```

この例外を回避するには、appsettings.json の `AllowWebApiToBeAuthorizedByACL` 構成プロパティをプログラムで true に設定します。

```Json
{
 "AzureAD"
 {
  // other properties
  "AllowWebApiToBeAuthorizedByACL" : true,
  // other properties
 }
}
```

`AllowWebApiToBeAuthorizedByACL` を true に設定した場合、ACL メカニズムを確保するのは **自分の責任** になります。

## <a name="next-steps"></a>次のステップ

このシナリオの次の記事「[運用環境に移行する](scenario-protected-web-api-production.md)」に進みます。
