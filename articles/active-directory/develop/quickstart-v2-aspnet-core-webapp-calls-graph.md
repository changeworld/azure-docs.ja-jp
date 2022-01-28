---
title: 'クイックスタート: ユーザーのサインインを処理して Microsoft Graph を呼び出す ASP.NET Core Web アプリ | Azure'
titleSuffix: Microsoft identity platform
description: このクイックスタートでは、OpenID Connect を使用して ASP.NET Core Web アプリに Microsoft サインインを実装する Microsoft.Identity.Web をアプリから活用して Microsoft Graph を呼び出す方法について説明します。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: portal
ms.workload: identity
ms.date: 11/22/2021
ROBOTS: NOINDEX
ms.author: jmprieur
ms.custom: devx-track-csharp, aaddev, "scenarios:getting-started", "languages:aspnet-core", mode-other
ms.openlocfilehash: e093dd1b5a3942ae0c98dc11feb10dd26ead0162
ms.sourcegitcommit: 3f20f370425cb7d51a35d0bba4733876170a7795
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/27/2022
ms.locfileid: "137804370"
---
# <a name="quickstart-aspnet-core-web-app-that-signs-in-users-and-calls-microsoft-graph-on-their-behalf"></a>クイックスタート: ユーザーのサインインを処理しその代理で Microsoft Graph を呼び出す ASP.NET Core Web アプリ

このクイックスタートでは、ASP.NET Core Web アプリから Azure Active Directory (Azure AD) 組織のユーザーのサインインを処理して Microsoft Graph を呼び出すコード サンプルをダウンロードして実行します。

図については、「[このサンプルのしくみ](#how-the-sample-works)」を参照してください。

## <a name="step-1-configure-your-application-in-the-azure-portal"></a>手順 1:Azure portal でのアプリケーションの構成

このクイックスタートのコード サンプルを動作させるには、アプリの登録の **リダイレクト URI** (`https://localhost:44321/signin-oidc`) と **フロントチャネルのログアウト URL** (`https://localhost:44321/signout-oidc`) を追加します。
> [!div class="nextstepaction"]
> [この変更を行う]()

> [!div class="alert alert-info"]
> ![構成済み](media/quickstart-v2-aspnet-webapp/green-check.png) アプリケーションはこれらの属性で構成されています。

## <a name="step-2-download-the-aspnet-core-project"></a>手順 2:ASP.NET Core プロジェクトをダウンロードする

プロジェクトを実行します。

> [!div class="nextstepaction"]
> [コード サンプルをダウンロードします](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/aspnetcore3-1-callsgraph.zip)

[!INCLUDE [active-directory-develop-path-length-tip](../../../includes/active-directory-develop-path-length-tip.md)]


## <a name="step-3-your-app-is-configured-and-ready-to-run"></a>手順 3:アプリが構成され、実行準備ができる

アプリのプロパティの値を使用してプロジェクトを構成したら、実行する準備は完了です。

> [!NOTE]
> `Enter_the_Supported_Account_Info_Here`

## <a name="about-the-code"></a>コードについて

このセクションでは、ユーザーのサインインを処理し、そのユーザーの代理で Microsoft Graph API を呼び出すために必要なコードの概要を示します。 コードの働きや主な引数を理解するうえでの助けとなるほか、既存の ASP.NET Core アプリケーションにサインイン処理を追加して Microsoft Graph を呼び出す場合にも役立ちます。 このコードでは、[MSAL.NET](msal-overview.md) のラッパーである [Microsoft.Identity.Web](microsoft-identity-web.md) を使用しています。

### <a name="how-the-sample-works"></a>このサンプルのしくみ

![このクイック スタートで生成されたサンプル アプリの動作の紹介](media/quickstart-v2-aspnet-core-webapp-calls-graph/aspnetcorewebapp-intro.svg)

### <a name="startup-class"></a>スタートアップ クラス

*Microsoft.AspNetCore.Authentication* ミドルウェアは、ホスティング プロセスの初期化時に実行される `Startup` クラスを使用します。

```csharp

  public void ConfigureServices(IServiceCollection services)
  {  
    // Get the scopes from the configuration (appsettings.json)
    var initialScopes = Configuration.GetValue<string>("DownstreamApi:Scopes")?.Split(' ');
  
      // Add sign-in with Microsoft
      services.AddAuthentication(OpenIdConnectDefaults.AuthenticationScheme)
        .AddMicrosoftIdentityWebApp(Configuration.GetSection("AzureAd"))

            // Add the possibility of acquiring a token to call a protected web API
            .EnableTokenAcquisitionToCallDownstreamApi(initialScopes)

                // Enables controllers and pages to get GraphServiceClient by dependency injection
                // And use an in memory token cache
                .AddMicrosoftGraph(Configuration.GetSection("DownstreamApi"))
                .AddInMemoryTokenCaches();

      services.AddControllersWithViews(options =>
      {
          var policy = new AuthorizationPolicyBuilder()
              .RequireAuthenticatedUser()
              .Build();
          options.Filters.Add(new AuthorizeFilter(policy));
      });

      // Enables a UI and controller for sign in and sign out.
      services.AddRazorPages()
          .AddMicrosoftIdentityUI();
  }
```

`AddAuthentication()` メソッドは、ブラウザーのシナリオで使用される Cookie ベースの認証を追加し、OpenID Connect へのチャレンジを設定するようにサービスを構成します。

`.AddMicrosoftIdentityWebApp` を含む行によって、Microsoft ID プラットフォーム認証がアプリケーションに追加されます。 これは [Microsoft.Identity.Web](microsoft-identity-web.md) によって提供されます。 その後、*appsettings.json* 構成ファイルの `AzureAD` セクションの情報に基づいて、Microsoft ID プラットフォームを使用してサインインするように構成されます。

| *appsettings.json* のキー | 説明                                                                                                                                                          |
|------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `ClientId`             | Azure portal に登録されているアプリケーションの **アプリケーション (クライアント) ID**。                                                                                       |
| `Instance`             | ユーザーが認証するためのセキュリティ トークン サービス (STS) エンドポイント。 通常、この値は、Azure パブリック クラウドを示す `https://login.microsoftonline.com/` です。 |
| `TenantId`             | テナントの名前またはテナント ID (GUID)。職場または学校アカウントあるいは Microsoft 個人アカウントを使用してユーザーをサインインする場合は *common*。                             |

`EnableTokenAcquisitionToCallDownstreamApi` メソッドによって、アプリケーションは、保護された Web API を呼び出すためのトークンを取得できるようになります。 `AddMicrosoftGraph` によって、コントローラーまたは Razor ページは (依存関係の挿入によって) 直接 `GraphServiceClient` を取得でき、また、`AddInMemoryTokenCaches` メソッドによって、アプリはトークン キャッシュの恩恵を受けることができます。

`Configure()` メソッドには、`app.UseAuthentication()` と `app.UseAuthorization()` という 2 つの重要なメソッドが含まれており、それらの名前付き機能を有効にします。 また、`Configure()` メソッドで、少なくとも 1 つの `endpoints.MapControllerRoute()` 呼び出し (または `endpoints.MapControllers()` 呼び出し) に、Microsoft Identity Web のルートを登録する必要があります。

```csharp
app.UseAuthentication();
app.UseAuthorization();

app.UseEndpoints(endpoints =>
{

    endpoints.MapControllerRoute(
        name: "default",
        pattern: "{controller=Home}/{action=Index}/{id?}");
    endpoints.MapRazorPages();
});

// endpoints.MapControllers(); // REQUIRED if MapControllerRoute() isn't called.
```

### <a name="protect-a-controller-or-a-controllers-method"></a>コントローラーまたはコントローラーのメソッドを保護する

コントローラーのクラスまたはそのメソッドは、`[Authorize]` 属性を適用することで保護することができます。 この `[Authorize]` 属性は、認証されたユーザーのみを許可することでアクセスを制限します。 ユーザーがまだ認証されていない場合、コントローラーにアクセスするための認証チャレンジを開始することができます。 このクイックスタートでは、スコープを構成ファイルから読み取っています。

```csharp
[AuthorizeForScopes(ScopeKeySection = "DownstreamApi:Scopes")]
public async Task<IActionResult> Index()
{
    var user = await _graphServiceClient.Me.Request().GetAsync();
    ViewData["ApiResult"] = user.DisplayName;

    return View();
}
```

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>次のステップ

このクイックスタートで引用した ASP.NET Core コード サンプルがある GitHub リポジトリには、次の操作方法を示す手順とその他のコード サンプルが含まれています。

- 新しい ASP.NET Core Web アプリに認証を追加する
- Microsoft Graph、他の Microsoft API、またはユーザー独自の Web API を呼び出す
- 承認を追加する
- 国内クラウドで、またはソーシャル ID を使用してユーザーをサインインする

> [!div class="nextstepaction"]
> [GitHub の ASP.NET Core Web アプリのチュートリアル](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/)
