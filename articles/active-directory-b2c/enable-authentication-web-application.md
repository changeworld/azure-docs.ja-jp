---
title: Azure Active Directory B2C の構成要素を使用して Web アプリで認証を有効にする
description: この記事では、Azure Active Directory B2C の構成要素を使用して ASP.NET Web アプリでユーザーをサインインおよびサインアップする方法について説明します。
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 06/11/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: 66b37e5464d548559b8d4e08922f1970e0c8bb7f
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130040818"
---
# <a name="enable-authentication-in-your-own-web-app-by-using-azure-ad-b2c"></a>Azure AD B2C を使用して独自の Web アプリで認証を有効にする

この記事では、独自の ASP.NET Web アプリケーションに Azure Active Directory B2C (Azure AD B2C) 認証を追加する方法について説明します。 [OpenID Connect](openid-connect.md) プロトコルを使用する ASP.NET Core ミドルウェアを使用して ASP.NET Core Web アプリケーションを作成する方法について説明します。 

この記事は、[サンプル Web アプリで認証を構成](configure-authentication-sample-web-app.md)する方法に関する記事と合わせてご利用ください。その際は、サンプルの Web アプリを独自の Web アプリに置き換えてください。

## <a name="prerequisites"></a>前提条件

前提条件と統合の手順を確認するには、[サンプル Web アプリケーションでの認証の構成](configure-authentication-sample-web-app.md)に関する記事を参照してください。

## <a name="step-1-create-a-web-app-project"></a>手順 1: Web アプリ プロジェクトを作成する

既存の ASP.NET Model-View-Controller (MVC) Web アプリ プロジェクトを使用することも、新しいプロジェクトを作成することもできます。 新しいプロジェクトを作成するには、コマンド シェルを開いてから、次のコマンドを入力します。

```dotnetcli
dotnet new mvc -o mywebapp
```

前述のコマンドでは、次のことを行います。

* 新しい MVC Web アプリを作成します。  
* `-o mywebapp` パラメーターを指定して、アプリのソース ファイルが保存される *mywebapp* という名前のディレクトリを作成します。

## <a name="step-2-add-the-authentication-libraries"></a>手順 2: 認証ライブラリを追加する

Web アプリへの Azure AD B2C 認証と認可のサポートの追加を簡単にする ASP.NET Core ライブラリのセットである、Microsoft Identity Web ライブラリを追加します。 Microsoft Identity Web ライブラリを使って、Cookie ベースの認証を使用する認証パイプラインを設定します。 これが、HTTP 認証メッセージの送受信、トークンの検証、要求の抽出などの処理を担います。

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

*Startup.cs* を開いてから、クラスの先頭に以下の `using` 宣言を追加します。

```csharp
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Authentication.OpenIdConnect;
using Microsoft.Identity.Web;
using Microsoft.Identity.Web.UI;
```

Microsoft Identity Web で Cookie ベースの認証を使用して Web アプリを保護するため、次のコードで、*SameSite* Cookie 設定を設定します。 次に、`AzureAdB2C` アプリケーション設定が読み取られ、そのビューでミドルウェア コントローラーが開始されます。 

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
    services.AddMicrosoftIdentityWebAppAuthentication(Configuration, "AzureAdB2C");

    services.AddControllersWithViews()
        .AddMicrosoftIdentityUI();

    services.AddRazorPages();

    //Configuring appsettings section AzureAdB2C, into IOptions
    services.AddOptions();
    services.Configure<OpenIdConnectOptions>(Configuration.GetSection("AzureAdB2C"));
}
```

次のコードでは、Cookie ポリシーが追加され、認証モデルが使用されます。 `Configure` 関数を次のコード スニペットに置き換えます。 

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

ユーザー インターフェイスの要素を追加するには、ユーザーがサインインしているかどうかを確認するためのロジックを含む部分ビューを使用します。 ユーザーがサインインしていない場合は、部分ビューにサインイン ボタンが表示されます。 ユーザーがサインインしている場合は、ユーザーの表示名とサインアウト ボタンが表示されます。
  
次のコード スニペットを使用して、新しいファイル *\_LoginPartial.cshtml* を */Views/Shared* フォルダー内に作成します。

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

追加した *_LoginPartial.cshtml* ファイルを含むように */Views/Shared_Layout.cshtml* ファイルを変更します。 *_Layout.cshtml* ファイルは、ユーザーがページ間を移動するときに一貫したエクスペリエンスを提供する共通のレイアウトです。 このレイアウトには、アプリのヘッダーやフッターなど、共通のユーザー インターフェイス要素が含まれます。

> [!NOTE]
> 実行中の .NET Core のバージョンと、既存のアプリへのサインインを追加するかどうかによって、UI 要素の外観が異なる場合があります。 その場合は、ページ レイアウト内の適切な場所に *_LoginPartial* を含めるようにします。

*/Views/Shared/_Layout.cshtml* ファイルを開いてから、次の `div` 要素を追加します。

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
  </ul>
  <partial name="_LoginPartial" />
</div>
```

上記の Razor コードには、次の手順で作成する `Claims` のアクションへのリンクが含まれています。

## <a name="step-5-add-the-claims-view"></a>手順 5: 要求ビューを追加する

*/Views/Home* フォルダーの下にある ID トークン要求を表示するには、*Claims.cshtml* ビューを追加します。

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

この手順では、*Claims.cshtml* ビューを *Home* コントローラーにリンクする `Claims` アクションを追加します。 `Claims` アクションでは `Authorize` 属性を使用します。これにより、認証されたユーザーのみがそのアクションにアクセスできるように制限されます。  

コントローラーである */Controllers/HomeController.cs* に、次のアクションを追加します。

```csharp
[Authorize]
public IActionResult Claims()
{
    return View();
}
```

クラスの先頭に、次の `using` 宣言を追加します。

```csharp
using Microsoft.AspNetCore.Authorization;
```

## <a name="step-6-add-the-app-settings"></a>手順 6: アプリ設定を追加する

Azure AD B2C ID プロバイダーの設定は、*appsettings.json* ファイルに格納されています。 *appsettings.json* を開いてから、以下の設定を追加します。

```JSon
"AzureAdB2C": {
  "Instance": "https://<your-tenant-name>.b2clogin.com",
  "ClientId": "<web-app-application-id>",
  "Domain": "<your-b2c-domain>",
  "SignedOutCallbackPath": "/signout/<your-sign-up-in-policy>",
  "SignUpSignInPolicyId": "<your-sign-up-in-policy>"
}
```

必要な情報については、[サンプル Web アプリで認証を構成する](configure-authentication-sample-web-app.md)方法に関する記事を参照してください。 次の設定を使用します。

* **インスタンス**: `<your-tenant-name>` を Azure AD B2C [テナント名](tenant-management.md#get-your-tenant-name)の最初の部分に置き換えます (例: `https://contoso.b2clogin.com`)。
* **ドメイン**: `<your-b2c-domain>` を Azure AD B2C の完全な [テナント名](tenant-management.md#get-your-tenant-name)に置き換えます (例: `contoso.onmicrosoft.com`)。
* **クライアント ID**: `<web-app-application-id>` を [手順 2](configure-authentication-sample-web-app.md#step-2-register-a-web-application) のアプリケーション ID に置き換えます。
* **ポリシー名**: `<your-sign-up-in-policy>` を [手順 1](configure-authentication-sample-web-app.md#step-1-configure-your-user-flow) で作成したユーザー フローに置き換えます。

## <a name="step-7-run-your-application"></a>手順 7: アプリケーションを実行する

1. プロジェクトをビルドして実行します。
1. `https://localhost:5001` にアクセスします。 
1. **[サインアップ/イン]** を選択します。
1. サインインアップまたはサインイン プロセスを完了します。

正しく認証されると、ナビゲーション バーに自分の表示名が表示されます。 Azure AD B2C トークンによってアプリに返される要求を表示するには、 **[要求]** を選択します。

## <a name="next-steps"></a>次のステップ
* [Web アプリの Azure AD B2C 認証エクスペリエンスをカスタマイズおよび強化する](enable-authentication-web-application-options.md)方法を学ぶ。
