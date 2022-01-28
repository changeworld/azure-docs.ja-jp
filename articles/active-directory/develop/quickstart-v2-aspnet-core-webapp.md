---
title: 'クイックスタート: ASP.NET Core Web アプリに "Microsoft ID でサインイン" を追加する | Azure'
titleSuffix: Microsoft identity platform
description: このクイックスタートでは、OpenID Connect を使用して、ASP.NET Core Web アプリに Microsoft サインインを実装する方法について説明します
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
ms.custom: devx-track-csharp, aaddev, identityplatformtop40, "scenarios:getting-started", "languages:aspnet-core", mode-other
ms.openlocfilehash: 7dad1d4840e24c1a2a7b65094ee5c28f45a73f44
ms.sourcegitcommit: 3f20f370425cb7d51a35d0bba4733876170a7795
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/27/2022
ms.locfileid: "137802506"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-an-aspnet-core-web-app"></a>クイック スタート:ASP.NET Core Web アプリに Microsoft サインインを追加する

このクイックスタートでは、ASP.NET Core Web アプリで Azure Active Directory (Azure AD) 組織のユーザーをサインインする方法を示すコード サンプルをダウンロードして実行します。  

#### <a name="step-1-configure-your-application-in-the-azure-portal"></a>手順 1:Azure portal でのアプリケーションの構成
このクイックスタートのコード サンプルを動作させるには、次のことを行います。
- **[リダイレクト URI]** に「 **https://localhost:44321/** 」および「 **https://localhost:44321/signin-oidc** 」と入力します。
- **[フロントチャネルのログアウト URL]** に「 **https://localhost:44321/signout-oidc** 」と入力します。 

要求の ID トークンが承認エンドポイントによって発行されます。
> [!div class="nextstepaction"]
> [この変更を行う]()

> [!div class="alert alert-info"]
> ![構成済み](media/quickstart-v2-aspnet-webapp/green-check.png) アプリケーションはこれらの属性で構成されています。

#### <a name="step-2-download-the-aspnet-core-project"></a>手順 2:ASP.NET Core プロジェクトをダウンロードする

プロジェクトを実行します。

> [!div class="nextstepaction"]
> [コード サンプルをダウンロードします](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/aspnetcore3-1.zip)

[!INCLUDE [active-directory-develop-path-length-tip](../../../includes/active-directory-develop-path-length-tip.md)]


#### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>手順 3:アプリが構成され、実行準備ができる
アプリのプロパティの値を使用してプロジェクトを構成したら、実行する準備は完了です。

> [!NOTE]
> `Enter_the_Supported_Account_Info_Here`

## <a name="more-information"></a>詳細情報

このセクションでは、ユーザーをサインインさせるために必要なコードの概要を示します。 この概要は、コードの働きや主な引数、また既存の ASP.NET Core アプリケーションにサインインを追加する方法を理解するうえで役立ちます。

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="how-the-sample-works"></a>このサンプルのしくみ
>
> ![サンプル アプリにおける Web ブラウザー、Web アプリ、Microsoft ID プラットフォーム間の対話を表す図。](media/quickstart-v2-aspnet-core-webapp/aspnetcorewebapp-intro.svg)

### <a name="startup-class"></a>スタートアップ クラス

*Microsoft.AspNetCore.Authentication* ミドルウェアは、ホスティング プロセスの起動時に実行される `Startup` クラスを使用します。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(OpenIdConnectDefaults.AuthenticationScheme)
        .AddMicrosoftIdentityWebApp(Configuration.GetSection("AzureAd"));

    services.AddControllersWithViews(options =>
    {
        var policy = new AuthorizationPolicyBuilder()
            .RequireAuthenticatedUser()
            .Build();
        options.Filters.Add(new AuthorizeFilter(policy));
    });
   services.AddRazorPages()
        .AddMicrosoftIdentityUI();
}
```

`AddAuthentication()` メソッドは、Cookie ベースの認証を追加するようサービスを構成します。 この認証は、ブラウザーのシナリオで使用されるほか、チャレンジを OpenID Connect に設定する際に使用されます。

`.AddMicrosoftIdentityWebApp` を含む行によって、Microsoft ID プラットフォーム認証がアプリケーションに追加されます。 その後、*appsettings.json* 構成ファイルの `AzureAD` セクションにある次の情報に基づいてユーザーのサインインを処理するように構成されます。

| *appsettings.json* のキー | 説明                                                                                                                                                          |
|------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `ClientId`             | Azure portal に登録されているアプリケーションのアプリケーション (クライアント) ID。                                                                                       |
| `Instance`             | ユーザーが認証するためのセキュリティ トークン サービス (STS) エンドポイント。 通常、この値は、Azure パブリック クラウドを示す `https://login.microsoftonline.com/` です。 |
| `TenantId`             | テナントの名前またはテナント ID (GUID)。職場または学校アカウントあるいは Microsoft 個人アカウントを使用してユーザーをサインインする場合は `common`。                             |

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
```

### <a name="attribute-for-protecting-a-controller-or-methods"></a>コントローラーまたはメソッドを保護するための属性

`[Authorize]` 属性を使用して、コントローラーまたはコントローラーのメソッドを保護できます。 この属性は、認証されたユーザーのみを許可することで、コントローラーまたはメソッドへのアクセスを制限します。 ユーザーが認証されていない場合、コントローラーにアクセスするための認証チャレンジを開始することができます。

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>次のステップ

この ASP.NET Core チュートリアルを含む GitHub リポジトリには、次の方法を示す手順とその他のコード サンプルが含まれています。

- 新しい ASP.NET Core Web アプリに認証を追加する。
- Microsoft Graph、他の Microsoft API、またはユーザー独自の Web API を呼び出す。
- 承認を追加する。
- 国内クラウドで、またはソーシャル ID を使用してユーザーのサインインを処理する。

> [!div class="nextstepaction"]
> [GitHub の ASP.NET Core Web アプリのチュートリアル](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/)
