---
title: Azure Active Directory B2C の構成要素を使用して Web API を呼び出す Web アプリで認証を有効にする
description: この記事では、Azure Active Directory B2C を使用して Web API を呼び出す ASP.NET Web アプリの構成要素について説明します。
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 11/10/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: 987fbce373b0b6696f13fb40aa2731c3c239aea4
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2021
ms.locfileid: "132156027"
---
# <a name="enable-authentication-in-web-apps-that-call-a-web-api-by-using-azure-ad-b2c"></a>Azure AD B2C を使用して Web API を呼び出す Web アプリで認証を有効にする

この記事では、Web API を呼び出す ASP.NET Web アプリケーションに Azure Active Directory B2C (Azure AD B2C) 認証を追加する方法を示します。 [OpenID Connect](openid-connect.md) プロトコルを使用する ASP.NET Core ミドルウェアが含まれる ASP.NET Core Web アプリケーションを作成する方法について説明します。 

この記事を、[Web API を呼び出すサンプル Web アプリケーションで認証を構成する](configure-authentication-sample-web-app-with-api.md)ことに関するページと共に使用するには、サンプル Web アプリを独自の Web アプリに置換します。

この記事では、Web アプリケーション プロジェクトに焦点を絞ります。 Web API を作成する方法の手順については、["ToDo リスト" という Web API サンプル](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/4-WebApp-your-API/4-2-B2C)を参照してください。

## <a name="prerequisites"></a>前提条件

[Web API を呼び出すサンプル Web アプリケーションで認証を構成する](configure-authentication-sample-web-app-with-api.md)ことに関するページで、前提条件と統合の手順を確認します。

以降のセクションでは、Azure Active Directory B2C (Azure AD B2C) 認証を ASP.NET Web アプリケーションに追加する方法の手順を示します。

## <a name="step-1-create-a-web-app-project"></a>手順 1: Web アプリ プロジェクトを作成する

既存の ASP.NET Model View Controller (MVC) Web アプリ プロジェクトを使用することも、新しいプロジェクトを作成することもできます。 新しいプロジェクトを作成するには、コマンド シェルを開いてから、次のコマンドを実行します。

```dotnetcli
dotnet new mvc -o mywebapp
```

上記のコマンドにより、新しい MVC Web アプリが作成されます。 `-o mywebapp` パラメーターを指定して、アプリのソース ファイルが保存される *mywebapp* という名前のディレクトリを作成します。

## <a name="step-2-add-the-authentication-libraries"></a>手順 2: 認証ライブラリを追加する

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

## <a name="step-3-initiate-the-authentication-libraries"></a>手順 3: 認証ライブラリを開始する

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

    // Configuration to sign in users with Azure AD B2C
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

以下のコードでは、Cookie ポリシーを追加し、そのポリシーで認証モデルを使用しています。 `Configure` 関数を次のコード スニペットに置き換えます。 

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

## <a name="step-4-add-the-ui-elements"></a>手順 4: UI 要素を追加する

ユーザー インターフェイス要素を追加するには、部分ビューを使用します。 部分ビューには、ユーザーがサインインしているかどうかを確認するロジックを含めます。 ユーザーがサインインしていない場合は、部分ビューにサインイン ボタンが表示されます。 ユーザーがサインインしている場合は、そのユーザーの表示名とサインアウト ボタンが表示されます。
  
次のコード スニペットを使用して、`Views/Shared` フォルダー内に新しいファイル `_LoginPartial.cshtml` を作成します。

```razor
@using System.Security.Principal
@if (User.Identity.IsAuthenticated)
{
    <ul class="nav navbar-nav navbar-right">
        <li class="navbar-text">Hello @User.Identity.Name</li>
        <!-- The Account controller is not defined in this project. Instead, it is part of Microsoft.Identity.Web.UI nuget package, and it defines some well-known actions, such as SignUp/In, SignOut, and EditProfile. -->
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

追加した *_LoginPartial.cshtml* ファイルを含むように `Views\Shared\_Layout.cshtml` を変更します。 *_Layout.cshtml* ファイルは、ユーザーがページ間を移動するときに一貫したエクスペリエンスを提供する共通のレイアウトです。 このレイアウトには、アプリのヘッダーやフッターなど、共通のユーザー インターフェイス要素を含めます。

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

## <a name="step-5-add-the-claims-view"></a>手順 5: 要求ビューを追加する

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

コントローラーである */Controllers/HomeController.cs* に、次のアクションを追加します。

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

## <a name="step-6-add-the-todolistcshtml-view"></a>手順 6: TodoList.cshtml ビューを追加する

TodoList.cshtml Web API を呼び出すには、適切なスコープを持つアクセス トークンが必要です。 この手順では、`Home` コントローラーにアクションを追加します。 `Views/Home` フォルダーの下に、`TodoList.cshtml` ビューを追加します。

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

ビューを追加した後に、*TodoList.cshtml* ビューを *Home* コントローラーにリンクする `TodoList` アクションを追加します。 `[Authorize]` 属性を使用して、認証されたユーザーのみが TodoList アクションにアクセスできるように制限します。  

*/Controllers/HomeController.cs* コントローラーに次のアクション クラス メンバーを追加し、トークン取得サービスをコントローラーに挿入します。

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

ここで次のアクションを追加します。これは、ベアラー トークンに加えて Web API を呼び出す方法を示しています。 

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

## <a name="step-7-add-the-app-settings"></a>手順 7: アプリ設定を追加する

Azure AD B2C ID プロバイダーの設定は、*appsettings.json* ファイルに格納されています。 「[Azure AD B2C を使用して Web API を呼び出すサンプル Web アプリで認証を構成する](configure-authentication-sample-web-app-with-api.md#step-5-configure-the-sample-web-app)」の「手順 5: サンプル Web アプリを構成する」の説明に従って、*appsettings.json* を開き、アプリ設定を追加します。

## <a name="step-8-run-your-application"></a>手順 8: アプリケーションを実行する

1. プロジェクトをビルドして実行します。
1. https://localhost:5001 に移動してから **[サインイン/サインアップ]** を選択します。
1. サインインまたはサインアップのプロセスを完了します。

アプリで認証に成功したら、ナビゲーション バーで表示名を確認します。 

* Azure AD B2C トークンによってアプリに返される要求を表示するには、 **[要求]** を選択します。
* アクセス トークンを表示するには、 **[To do list]\(ToDo リスト\)** を選択します。

## <a name="next-steps"></a>次のステップ

具体的には、次の方法を学習します。
* [Web アプリの Azure AD B2C 認証エクスペリエンスをカスタマイズおよび強化する](enable-authentication-web-application-options.md)
* [独自の Web API で認証を有効にする](enable-authentication-web-api.md)
