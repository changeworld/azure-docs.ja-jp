<properties
   pageTitle="マルチテナント アプリケーションでの承認 | Microsoft Azure"
   description="マルチテナント アプリケーションで承認を実行する方法"
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

# マルチテナント アプリケーションでの承認

この記事は[シリーズの一部]です。このシリーズに付属する完成した[サンプル アプリケーション]もあります。

この記事では、一般的な承認のアプローチを 2 つ説明します。

-	**ロールベースの承認**。ユーザーに割り当てられたロールに基づいてアクションを承認します。たとえば、一部の操作に管理者ロールを必須にします。
-	**リソースベースの承認**。特定のリソースに基づいてアクションを承認します。たとえば、すべてのリソースには所有者がいます。所有者はリソースを削除できますが、他のユーザーは削除できません。

一般的なアプリは両方のアプローチを採用します。たとえば、リソースを削除する場合、ユーザーはリソース所有者_または_管理者である必要があります。

## ロール ベースの承認

[Tailspin Surveys][Tailspin] アプリケーションには、次のロールが定義されています。

- 管理者。そのテナントに属するすべてのアンケートに対してすべての CRUD 操作を実行できます。
- 作成者。新しいアンケートを作成できます。
- 閲覧者。そのテナントに属するすべてのアンケートを読み取ることができます。

ロールは、アプリケーションの_ユーザー_に適用されます。Surveys アプリケーションのユーザーは、管理者、作成者、または閲覧者です。

ロールを定義および管理する方法については、[アプリケーション ロール]に関するページを参照してください。

ロールを管理する方法にかかわらず、承認コードは同様になります。ASP.NET Core 1.0 は、_承認ポリシー_という抽象化を導入しています。この機能を使用してコードで承認ポリシーを定義し、そのポリシーをコントローラー アクションに適用します。ポリシーは、コントローラーから切り離されます。

### ポリシーの作成

ポリシーを定義するには、まず `IAuthorizationRequirement` を実装するクラスを作成します。`AuthorizationHandler` から派生するのが最も簡単です。`Handle` メソッドで、関連する要求を確認してください。

Tailspin Surveys アプリケーションの例を次に示します。

```csharp
public class SurveyCreatorRequirement : AuthorizationHandler<SurveyCreatorRequirement>, IAuthorizationRequirement
{
    protected override void Handle(AuthorizationContext context, SurveyCreatorRequirement requirement)
    {
        if (context.User.HasClaim(ClaimTypes.Role, Roles.SurveyAdmin) ||
            context.User.HasClaim(ClaimTypes.Role, Roles.SurveyCreator))
        {
            context.Succeed(requirement);
        }
    }
}
```

> [AZURE.NOTE] [SurveyCreatorRequirement.cs] を参照してください。

このクラスは、新しいアンケートを作成するユーザーの要件を定義しています。ユーザーには SurveyAdmin または SurveyCreator ロールが割り当てられている必要があります。

startup クラスで、1 つ以上の要件を含む名前付きポリシーを定義します。複数の要件がある場合、ユーザーが承認されるには、_すべて_の要件を満たす必要があります。次のコードでは、2 つのポリシーを定義しています。

```csharp
services.AddAuthorization(options =>
{
    options.AddPolicy(PolicyNames.RequireSurveyCreator,
        policy =>
        {
            policy.AddRequirements(new SurveyCreatorRequirement());
            policy.AddAuthenticationSchemes(CookieAuthenticationDefaults.AuthenticationScheme);
        });

    options.AddPolicy(PolicyNames.RequireSurveyAdmin,
        policy =>
        {
            policy.AddRequirements(new SurveyAdminRequirement());
            policy.AddAuthenticationSchemes(CookieAuthenticationDefaults.AuthenticationScheme);
        });
});
```

> [AZURE.NOTE] [Startup.cs] を参照してください。

また、このコードでは、認証スキームも設定しています。このスキームでは、承認が失敗した場合、どの認証ミドルウェアを実行するかを ASP.NET に指示します。この例では、Cookie 認証ミドルウェアを指定します。これは、Cookie 認証ミドルウェアによってユーザーが "Forbidden" ページにリダイレクトされる可能性があるためです。"Forbidden" ページの場所は、Cookie ミドルウェアの AccessDeniedPath オプションに設定されています。[認証ミドルウェアの構成]に関するページを参照してください。

### コントローラー アクションを承認する

最後に、MVC コントローラーのアクションを承認するには、`Authorize` 属性のポリシーを設定します。

```csharp
[Authorize(Policy = "SurveyCreatorRequirement")]
public IActionResult Create()
{
    // ...
}
```

旧バージョンの ASP.NET では、この属性の **Roles** プロパティを設定します。

```csharp
// old way
[Authorize(Roles = "SurveyCreator")]

```

このプロパティは ASP.NET Core 1.0 でもサポートされていますが、承認ポリシーと比較すると短所があります。

-	特定の要求の種類を前提としています。ポリシーであれば、任意の要求の種類を確認できます。ロールは 1 種類の要求のみです。
-	ロール名は、属性にハードコーディングされています。ポリシーの場合、承認ロジックは 1 個所で管理されるので更新が簡単です。また、構成設定から読み込むこともできます。
-	ポリシーを使用すると、1 つのロール メンバーシップでは表現できない、より複雑な承認判断 (21 歳以上の年齢など) を実行できます。

## リソース ベースの承認

_リソース ベースの承認_は、操作の影響を受ける特定のリソースに承認が依存している場合に常に発生します。Tailspin Surveys アプリケーションでは、すべてのアンケートに 1 人の所有者がいて、0 対多の共同作成者がいます。

-	所有者は、アンケートの読み取り、更新、削除、発行、発行の取り消しを行うことができます。
-	所有者は、アンケートに共同作成者を割り当てることができます。
-	共同作成者は、アンケートの読み取りと更新を行うことができます。

"所有者" と "共同作成者" はアプリケーション ロールではありません。アンケートごとにアプリケーション データベースに保存されます。アプリはユーザーがアンケートを削除できるかどうかを確認するために、ユーザーがそのアンケートの所有者かどうかを確認します。

ASP.NET Core 1.0 では、**AuthorizationHandler** から派生し、**Handle** メソッドを上書きすることで、リソースベースの承認を実装します。

```csharp
public class SurveyAuthorizationHandler : AuthorizationHandler<OperationAuthorizationRequirement, Survey>
{
     protected override void Handle(AuthorizationContext context, OperationAuthorizationRequirement operation, Survey resource)
    {
    }
}
```

Survey オブジェクトのこのクラスは厳密に型指定されます。開始時に DI のクラスを登録します。

```csharp
services.AddSingleton<IAuthorizationHandler>(factory =>
{
    return new SurveyAuthorizationHandler();
});
```

承認チェックを実行するには、**IAuthorizationService** インターフェイスを使用します。このインターフェイスは、コントローラーに挿入できます。次のコードでは、ユーザーがアンケートを読み取ることができるかどうかを確認します。

```csharp
if (await _authorizationService.AuthorizeAsync(User, survey, Operations.Read) == false)
{
    return new HttpStatusCodeResult(403);
}
```

`Survey` オブジェクトで渡すため、この呼び出しによって `SurveyAuthorizationHandler` が呼び出されます。

承認コードでは、ユーザーのすべてのロールベースのアクセス許可とリソースベースのアクセス許可を集計し、目的の操作に対して集計セットを確認することをお勧めします。Surveys アプリの例を次に示します。このアプリケーションには、いくつかのアクセス許可が定義されています。

- 管理者
- 共同作成者
- 作成者
- 所有者
- 閲覧者

また、アンケートに対して実行できる操作のセットも定義されています。

- 作成
- 読み取り
- 更新
- 削除
- 発行
- 発行の取り消し

次のコードは、特定のユーザーとアンケートのアクセス許可の一覧を作成します。このコードは、ユーザーのアプリ ロールと、アンケートの所有者/共同作成者フィールドの両方を確認します。

```csharp
protected override void Handle(AuthorizationContext context, OperationAuthorizationRequirement operation, Survey resource)
{
    var permissions = new List<UserPermissionType>();
    string userTenantId = context.User.GetTenantIdValue();
    int userId = ClaimsPrincipalExtensions.GetUserKey(context.User);
    string user = context.User.GetUserName();

    if (resource.TenantId == userTenantId)
    {
        // Admin can do anything, as long as the resource belongs to the admin's tenant.
        if (context.User.HasClaim(ClaimTypes.Role, Roles.SurveyAdmin))
        {
            context.Succeed(operation);
            return;
        }

        if (context.User.HasClaim(ClaimTypes.Role, Roles.SurveyCreator))
        {
            permissions.Add(UserPermissionType.Creator);
        }
        else
        {
            permissions.Add(UserPermissionType.Reader);
        }

        if (resource.OwnerId == userId)
        {
            permissions.Add(UserPermissionType.Owner);
        }
    }
    if (resource.Contributors != null && resource.Contributors.Any(x => x.UserId == userId))
    {
        permissions.Add(UserPermissionType.Contributor);
    }
    if (ValidateUserPermissions[operation](permissions))
    {
        context.Succeed(operation);
    }
}
```

> [AZURE.NOTE] [SurveyAuthorizationHandler.cs] を参照してください。

マルチテナント アプリケーションの場合、アクセス許可が他のテナントのデータに "漏れ" ないようにする必要があります。Surveys アプリでは、複数のテナントにまたがる共同作成者アクセス許可を設定できます。そのため、別のテナントのユーザーを共同作成者として割り当てることができます。その他のアクセス許可は、そのユーザーのテナントに属するリソースに制限されています。そのため、コードはテナント ID を確認してから、そのアクセス許可を付与します(アンケートの作成時に割り当てられる `TenantId` フィールド)。

次の手順では、アクセス許可に対して操作 (読み取り、更新、削除など) を確認します。Surveys アプリは、関数のルックアップ テーブルを使用して、この手順を実装します。

```csharp
static readonly Dictionary<OperationAuthorizationRequirement, Func<List<UserPermissionType>, bool>> ValidateUserPermissions
    = new Dictionary<OperationAuthorizationRequirement, Func<List<UserPermissionType>, bool>>

    {
        { Operations.Create, x => x.Contains(UserPermissionType.Creator) },

        { Operations.Read, x => x.Contains(UserPermissionType.Creator) ||
                                x.Contains(UserPermissionType.Reader) ||
                                x.Contains(UserPermissionType.Contributor) ||
                                x.Contains(UserPermissionType.Owner) },

        { Operations.Update, x => x.Contains(UserPermissionType.Contributor) ||
                                x.Contains(UserPermissionType.Owner) },

        { Operations.Delete, x => x.Contains(UserPermissionType.Owner) },

        { Operations.Publish, x => x.Contains(UserPermissionType.Owner) },

        { Operations.UnPublish, x => x.Contains(UserPermissionType.Owner) }
    };
```

## その他のリソース

- [リソース ベースの承認](https://docs.asp.net/en/latest/security/authorization/resourcebased.html)
- [カスタム ポリシーベースの承認](https://docs.asp.net/en/latest/security/authorization/policies.html)

<!-- Links -->
[Tailspin]: guidance-multitenant-identity-tailspin.md
[シリーズの一部]: guidance-multitenant-identity.md
[アプリケーション ロール]: guidance-multitenant-identity-app-roles.md
[SurveyCreatorRequirement.cs]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Security/Policy/SurveyCreatorRequirement.cs
[Startup.cs]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Web/Startup.cs
[認証ミドルウェアの構成]: guidance-multitenant-identity-authenticate.md#configuring-the-authentication-middleware
[SurveyAuthorizationHandler.cs]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Security/Policy/SurveyAuthorizationHandler.cs
[サンプル アプリケーション]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps

<!---HONumber=AcomDC_0302_2016-->