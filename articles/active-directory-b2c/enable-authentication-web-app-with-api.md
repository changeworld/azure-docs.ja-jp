---
title: Azure Active Directory B2C 構成要素を使用して Web API を呼び出す Web アプリで認証を有効にする
description: Azure Active Directory B2C を使用して Web API を呼び出す ASP.NET Web アプリケーションの構成要素。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 06/11/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: 53c81633508bef928faf8919c618b9ad30dd3844
ms.sourcegitcommit: 8651d19fca8c5f709cbb22bfcbe2fd4a1c8e429f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/14/2021
ms.locfileid: "112073134"
---
# <a name="enable-authentication-in-your-own-web-application-that-calls-a-web-api-using-azure-active-directory-b2c"></a>Azure Active Directory B2C を使用して Web API を呼び出す独自の Web アプリケーションで認証を有効にする

この記事では、Web API を呼び出す独自の ASP.NET Web アプリケーションに Azure Active Directory B2C (Azure AD B2C) 認証を追加する方法について説明します。 [OpenID Connect](openid-connect.md) プロトコルを使用する ASP.NET Core ミドルウェアを使用して ASP.NET Core Web アプリケーションを作成する方法について説明します。 この記事を使用して、[Web API を呼び出すサンプル Web アプリケーションで認証を構成](configure-authentication-sample-web-app-with-api.md)し、このサンプル Web アプリを独自の Web アプリに置き換えます。

この記事では、Web アプリケーション プロジェクトについて重点的に説明します。 Web API を作成する手順については、[ToDo リスト Web API サンプル](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/4-WebApp-your-API/4-2-B2C)を参照してください。

## <a name="prerequisites"></a>前提条件

[Web API を呼び出すサンプル Web アプリケーションでの認証の構成](configure-authentication-sample-web-app-with-api.md)に関するページの前提条件と統合の手順を確認します。

## <a name="create-a-web-app-project"></a>Web アプリ プロジェクトの作成

既存の ASP.NET MVC Web アプリ プロジェクトを使用するか、新しいプロジェクトを作成することもできます。 新しいプロジェクトを作成するには、コマンド シェルを開き、次のコマンドを入力します。

```dotnetcli
dotnet new mvc -o mywebapp
```

上記のコマンドでは次のことが行われます。

* 新しい MVC Web アプリを作成します。  
* `-o mywebapp` パラメーターを指定して、アプリのソース ファイルが保存される *mywebapp* という名前のディレクトリを作成します。

## <a name="add-the-authentication-libraries"></a>認証ライブラリを追加する

まず、Microsoft Identity Web ライブラリを追加します。 これは、Web アプリへの Azure AD B2C 認証と承認のサポートの追加を簡単にする ASP.NET Core ライブラリのセットです。 Microsoft Identity Web ライブラリを使って、Cookie ベースの認証を使用する認証パイプラインを設定します。 これが、HTTP 認証メッセージの送受信、トークンの検証、要求の抽出などの処理を担います。

Microsoft Identity Web ライブラリを追加するには、次のコマンドを実行してパッケージをインストールします。 

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

```dotnetcli
dotnet add package Microsoft.Identity.Web
dotnet add package Microsoft.Identity.Web.UI
```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```dotnetcli
Install-Package Microsoft.Identity.Web
Install-Package Microsoft.Identity.Web.UI
```

---


## <a name="initiate-the-authentication-libraries"></a>認証ライブラリを開始する

Microsoft Identity Web ミドルウェアによって、ホスティング プロセスの開始時に実行されるスタートアップ クラスが使用されます。 この手順では、認証ライブラリを開始するために必要なコードを追加します。

`Startup.cs` を開き、クラスの先頭に次の `using` 宣言を追加します。

```csharp
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Authentication.OpenIdConnect;
using Microsoft.Identity.Web;
using Microsoft.Identity.Web.UI;
```

Microsoft Identity Web で Cookie ベースの認証を使用して Web アプリを保護するため、次のコードで、*SameSite* Cookie 設定を設定します。 `AzureADB2C` アプリケーション設定が読み取られ、そのビューでミドルウェア コントローラーが開始されます。 

`ConfigureServices(IServiceCollection services)` 関数を次のコード スニペットに置き換えます。 

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<CookiePolicyOptions>(options =>
    {
        // This lambda determines whether user consent for non-essential cookies is needed for a given request.
        options.CheckConsentNeeded = context => true;
        options.MinimumSameSitePolicy = SameSiteMode.Unspecified;
        // Handling SameSite cookie according to https://docs.microsoft.com/en-us/aspnet/core/security/samesite?view=aspnetcore-3.1
        options.HandleSameSiteCookieCompatibility();
    });

    // Configuration to sign-in users with Azure AD B2C
    services.AddMicrosoftIdentityWebAppAuthentication(Configuration, "AzureAdB2C")
            // Enable token acquisition to call downstream web API
            .EnableTokenAcquisitionToCallDownstreamApi(new string[] { Configuration["TodoList:TodoListScope"] })
            // Add refresh token in-memory cache
            .AddInMemoryTokenCaches();

    services.AddControllersWithViews()
        .AddMicrosoftIdentityUI();

    services.AddRazorPages();

    //Configuring appsettings section AzureAdB2C, into IOptions
    services.AddOptions();
    services.Configure<OpenIdConnectOptions>(Configuration.GetSection("AzureAdB2C"));
}
```

次のコードで、Cookie ポリシーを追加し、認証モデルを使用します。 `Configure` 関数を次のコード スニペットに置き換えます。 

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseExceptionHandler("/Home/Error");
        // The default HSTS value is 30 days. You may want to change this for production scenarios, see https://aka.ms/aspnetcore-hsts.
        app.UseHsts();
    }

    app.UseHttpsRedirection();
    app.UseStaticFiles();

    // Add the Microsoft Identity Web cookie policy
    app.UseCookiePolicy();
    app.UseRouting();
    // Add the ASP.NET Core authentication service
    app.UseAuthentication();
    app.UseAuthorization();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapControllerRoute(
            name: "default",
            pattern: "{controller=Home}/{action=Index}/{id?}");
        
        // Add endpoints for Razor pages
        endpoints.MapRazorPages();
    });
};
```

## <a name="add-the-ui-elements"></a>UI 要素を追加する

ユーザー インターフェイス要素を追加するには、部分ビューを使用します。 部分ビューには、ユーザーがサインインしているかどうかを確認するロジックを含めます。 ユーザーがサインインしていない場合は、部分ビューにサインイン ボタンが表示されます。 ユーザーがサインインしている場合は、ユーザーの表示名とサインアウト ボタンが表示されます。
  
次のコード スニペットを使用して、`Views/Shared` フォルダー内に新しいファイル `_LoginPartial.cshtml` を作成します。

```razor
@using System.Security.Principal
@if (User.Identity.IsAuthenticated)
{
    <ul class="nav navbar-nav navbar-right">
        <li class="navbar-text">Hello @User.Identity.Name</li>
        <!-- The Account controller is not defined in this project. Instead, it is part of Microsoft.Identity.Web.UI nuget package and
            it defines some well known actions such as SignUp/In, SignOut and EditProfile-->
        <li class="navbar-btn">
            <form method="get" asp-area="MicrosoftIdentity" asp-controller="Account" asp-action="EditProfile">
                <button type="submit" class="btn btn-primary" style="margin-right:5px">Edit Profile</button>
            </form>
        </li>
        <li class="navbar-btn">
            <form method="get" asp-area="MicrosoftIdentity" asp-controller="Account" asp-action="SignOut">
                <button type="submit" class="btn btn-primary">Sign Out</button>
            </form>
        </li>
    </ul>
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li class="navbar-btn">
            <form method="get" asp-area="MicrosoftIdentity" asp-controller="Account" asp-action="SignIn">
                <button type="submit" class="btn btn-primary">Sign Up/In</button>
            </form>
        </li>
    </ul>
}
```

追加した *_LoginPartial.cshtml* ファイルを含むように `Views\Shared\_Layout.cshtml` を変更します。 *_Layout.cshtml* ファイルは、ユーザーがページ間を移動するときに一貫したエクスペリエンスを提供する共通のレイアウトです。 アプリのヘッダーやフッターなど、共通のユーザー インターフェイス要素をこのレイアウトに含めます。

> [!NOTE]
> .NET Core のバージョンと、既存のアプリへのサインインを追加するかどうかによって、UI 要素の表示を変える必要があります。 その場合は、ページ レイアウト内の適切な場所に *_LoginPartial* を含めるようにします。

*/Views/Shared/_Layout.cshtml* を開き、次の `div` 要素を追加します。

```razor
<div class="navbar-collapse collapse">
...
</div>
```

この要素を次の Razor コードに置き換えます。

```razor
<div class="navbar-collapse collapse">
  <ul class="nav navbar-nav">
    <li><a asp-area="" asp-controller="Home" asp-action="Index">Home</a></li>
    <li><a asp-area="" asp-controller="Home" asp-action="Claims">Claims</a></li>
    <li><a asp-area="" asp-controller="Home" asp-action="TodoList">To do list</a></li>
  </ul>
  <partial name="_LoginPartial" />
</div>
```

上記の Razor コードには、次の手順で作成する `Claims` と `TodoList` のアクションへのリンクが含まれています。

## <a name="add-the-claims-view"></a>要求ビューを追加する

`Views/Home` フォルダーの下にある ID トークン要求を表示するには、`Claims.cshtml` ビューを追加します。

```razor
@using System.Security.Claims

@{
  ViewData["Title"] = "Claims";
}
<h2>@ViewData["Title"].</h2>

<table class="table-hover table-condensed table-striped">
  <tr>
    <th>Claim Type</th>
    <th>Claim Value</th>
  </tr>

  @foreach (Claim claim in User.Claims)
  {
    <tr>
      <td>@claim.Type</td>
      <td>@claim.Value</td>
    </tr>
  }
</table>
```

この手順では、*Claims.cshtml* ビューを *Home* コントローラーにリンクする `Claims` アクションを追加します。 `[Authorize]` 属性を使用して、認証されたユーザーのみが Claims アクションにアクセスできるように制限します。  

`/Controllers/HomeController.cs` コントローラーに次のアクションを追加します。

```csharp
[Authorize]
public IActionResult Claims()
{
    return View();
}
```

クラスの先頭に次の `using` 宣言を追加します。

```csharp
using Microsoft.AspNetCore.Authorization;
```

## <a name="add-the-to-do-list-view"></a>ToDo リスト ビューを追加する

ToDo Web API を呼び出すには、スコープが適切なアクセス トークンが必要です。 この手順では、`Home` コントローラーにアクションを追加します。 `Views/Home` フォルダーの下に、`TodoList.cshtml` ビューを追加します。

```razor
@{
    ViewData["Title"] = "To do list";
}

<div class="text-left">
  <h1 class="display-4">Your access token</h1>
  @* Remove following line in production environments *@
  <code>@ViewData["accessToken"]</code>
</div>
```

ビューを追加した後、*TodoList.cshtml* ビューを *Home* コントローラーにリンクする `TodoList` アクションを追加します。 `[Authorize]` 属性を使用して、認証されたユーザーのみが TodoList アクションにアクセスできるように制限します。  

`/Controllers/HomeController.cs` コントローラーで、次のアクション クラス メンバーを追加し、トークン取得サービスをコントローラーに挿入します。

```csharp
public class HomeController : Controller
{
    private readonly ILogger<HomeController> _logger;

    // Add the token acquisition service member variable
    private readonly ITokenAcquisition _tokenAcquisition; 
    
    // Inject the acquisition service
    public HomeController(ILogger<HomeController> logger, ITokenAcquisition tokenAcquisition)
    {
        _logger = logger;
        // Set the acquisition service member variable
        _tokenAcquisition = tokenAcquisition;
    }

    // More code...
}
```

次に、以下のアクションを追加します。 このアクションは、ベアラー トークンに加えて Web API を呼び出す方法を示しています。 

```csharp
[Authorize]
public async Task<IActionResult> TodoListAsync()
{
    // Acquire an access token with the relevant scopes.
    var accessToken = await _tokenAcquisition.GetAccessTokenForUserAsync(new[] { "https://your-tenant.onmicrosoft.com/tasks-api/tasks.read", "https://your-tenant.onmicrosoft.com/tasks-api/tasks.write" });
    
    // Remove this line in production environments    
    ViewData["accessToken"] = accessToken;

    using (HttpClient client = new HttpClient())
    {
        client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);

        HttpResponseMessage response = await client.GetAsync("https://path-to-your-web-api");
    }

    return View();
}
```

## <a name="add-the-app-settings"></a>アプリ設定を追加する

Azure AD B2C ID プロバイダーの設定は、`appsettings.json` ファイルに格納されています。 appsettings.json を開き、「[ステップ 5: サンプル Web アプリを構成する](configure-authentication-sample-web-app-with-api.md#step-5-configure-the-sample-web-app)」の説明に従ってアプリ設定を追加します。

## <a name="run-your-application"></a>アプリケーションを実行する

1. プロジェクトをビルドして実行します。
1. [http://.azurewebsites.net/admin](https://localhost:5001) を参照します。 
1. **[SignIn/Up]\(サインイン/サインアップ\)** を選択します。
1. サインインアップまたはサインイン プロセスを完了します。

認証に成功すると、ナビゲーション バーに表示名が表示されます。 

* Azure AD B2C トークンによってアプリに返される要求を表示するには、 **[Claims]\(要求\)** を選択します。
* アクセス トークンを表示するには、 **[To do list]\(ToDo リスト\)** を選択します。

## <a name="next-steps"></a>次のステップ

* [Web アプリの Azure AD B2C 認証エクスペリエンスをカスタマイズおよび強化する](enable-authentication-web-application-options.md)方法を学ぶ
