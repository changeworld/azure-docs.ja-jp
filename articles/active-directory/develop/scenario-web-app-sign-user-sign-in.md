---
title: ユーザーをサインインさせる Web アプリ (サインイン) - Microsoft ID プラットフォーム
description: ユーザーをサインインさせる Web アプリをビルドする方法について学習します (サインイン)
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
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3fb7fbba7ec48da580d2a630ae51aa20b3307848
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "65080067"
---
# <a name="web-app-that-signs-in-users---sign-in"></a>ユーザーをサインインさせる Web アプリ - サインイン

ユーザーをサインインさせる Web アプリのコードにサインインを追加する方法について学習します。

## <a name="sign-in"></a>サインイン

サインアウトの実装に必要なのは、前の記事の[アプリのコード構成](scenario-web-app-sign-user-app-configuration.md)でのコードだけです。ユーザーがお客様のアプリにサインインしたら、ユーザーをサインアウトできるようにしたい場合があります。サインアウトは、ASP.NET Core によって自動的に処理されます。

## <a name="what-sign-out-involves"></a>サインアウトの関連事項

Web アプリからのサインアウトでは、サインインしたアカウントに関する情報が Web アプリの状態から削除されるだけではありません。
サインアウトするには、Web アプリによってユーザーが Microsoft ID プラットフォーム v2.0 `logout` エンドポイントにリダイレクトされる必要もあります。Web アプリによってユーザーが `logout` エンドポイントにリダイレクトされると、このエンドポイントでは、ユーザーのセッションがブラウザーから消去されます。 アプリで `logout` エンドポイントに移動しない場合、ユーザーは資格情報を再入力しなくてもアプリで再認証されることがあります。Microsoft ID プラットフォーム v2.0 エンドポイントのシングル サインイン セッションが有効であるためです。

詳細については、概念に関するドキュメント「[Microsoft ID プラットフォーム v2.0 と OpenID Connect プロトコル](v2-protocols-oidc.md)」の「[サインアウト要求を送信する](v2-protocols-oidc.md#send-a-sign-out-request)」セクションを参照してください。

## <a name="application-registration"></a>アプリケーションの登録

アプリケーションの登録中に、**ログアウト後の URI** を登録します。 このチュートリアルでは、 **[認証]** ページの **[詳細設定]** セクションの **[ログアウト URL]** フィールドに `https://localhost:44321/signout-oidc` と登録しました。 詳細については、「[Register the webApp app](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg#register-the-webapp-app-webapp)」 (webApp アプリを登録する) を参照してください。

## <a name="aspnet-core-code"></a>ASP.NET Core コード

### <a name="signout-button"></a>サインアウト ボタン

サインアウト ボタンは `Views\Shared\_LoginPartial.cshtml` で公開されており、認証済みのアカウントがあるとき (つまり、ユーザーが以前にサインインしたとき) にのみ表示されます。

```html
@using Microsoft.Identity.Web
@if (User.Identity.IsAuthenticated)
{
    <ul class="nav navbar-nav navbar-right">
        <li class="navbar-text">Hello @User.GetDisplayName()!</li>
        <li><a asp-area="AzureAD" asp-controller="Account" asp-action="SignOut">Sign out</a></li>
    </ul>
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li><a asp-area="AzureAD" asp-controller="Account" asp-action="SignIn">Sign in</a></li>
    </ul>
}
```

### <a name="signout-action-of-the-accountcontroller"></a>`AccountController` の `Signout()` アクション

Web アプリ上の **[サインアウト]** ボタンを押すと、`Account` コントローラーの `SignOut` アクションがトリガーされます。 以前のバージョンの ASP.NET Core テンプレートでは、`Account` コントローラーは Web アプリに埋め込まれていましたが、現在は ASP.NET Core フレームワーク自体の一部になっているため、これはもう当てはまりません。 

`AccountController` 用のコードは、[AccountController.cs](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Controllers/AccountController.cs) からの ASP.NET Core リポジトリから利用できます。 アカウント制御では次のことを行います。

- Azure AD でサインアウトを実行したときにコントローラーがコールバックされるように、OpenID リダイレクト URI を `/Account/SignedOut` に設定します。
- OpenIdConnect ミドルウェアで Microsoft ID プラットフォーム `logout` エンドポイントに連絡できるようにする `Signout()` を呼び出します。エンドポイントでは次のことが行われます。

  - ブラウザーからセッション Cookie が消去される
  - 最後に**ログアウト URL** のコール バックを呼び出す。既定では、ASP.NET Core の一部としても提供される、サインアウトされた表示ページ [SignedOut.html](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Pages/Account/SignedOut.cshtml) が表示されます。

### <a name="intercepting-the-call-to-the-logout-endpoint"></a>`logout` エンドポイントへの呼び出しをインターセプトする

ASP.NET Core OpenIdConnect ミドルウェアでは、`OnRedirectToIdentityProviderForSignOut` という名前の OpenIdConnect イベントを提供することで、お客様のアプリで Microsoft ID プラットフォーム `logout` エンドポイントへの呼び出しをインターセプトすることができます。 Web アプリでは、サインアウトするときにユーザーに示されるアカウントの選択ダイアログを回避するように、それを使用します。このインターセプションは、`Microsoft.Identity.Web` の再利用可能なライブラリの `AddAzureAdV2Authentication` で行われます。 [StartupHelpers.cs の行 58 から行 66](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/b87a1d859ff9f9a4a98eb7b701e6a1128d802ec5/Microsoft.Identity.Web/StartupHelpers.cs#L58-L66) を参照してください。

```CSharp
public static IServiceCollection AddAzureAdV2Authentication(this IServiceCollection services,
                                                            IConfiguration configuration)
{
    ...
    services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
    {
        ...
        options.Authority = options.Authority + "/v2.0/";
        ...
        // Attempt to avoid displaying the select account dialog when signing out
        options.Events.OnRedirectToIdentityProviderForSignOut = async context =>
        {
            var user = context.HttpContext.User;
            context.ProtocolMessage.LoginHint = user.GetLoginHint();
            context.ProtocolMessage.DomainHint = user.GetDomainHint();
            await Task.FromResult(0);
        };
    }
}
```

## <a name="aspnet-code"></a>ASP.NET コード

ASP.NET で、サインアウトは、コントローラー (例: AccountController) の SignOut() メソッドからトリガーされます。 このメソッドは、(ASP.NET Core での動作とは対照的に) ASP.NET フレームワークの一部ではなく、非同期も使用しません。そのため、次のことが行われます。

- OpenId サインアウト チャレンジの送信
- キャッシュのクリア
- 目的のページへのリダイレクト

```CSharp
/// <summary>
/// Send an OpenID Connect sign-out request.
/// </summary>
public void SignOut()
{
 HttpContext.GetOwinContext()
            .Authentication
            .SignOut(CookieAuthenticationDefaults.AuthenticationType);
 MsalAppBuilder.ClearUserTokenCache();
 Response.Redirect("/");
}
```

## <a name="protocol"></a>Protocol

ASP.NET Core や ASP.NET を使用したくない場合は、[Open ID Connect](./v2-protocols-oidc.md) から入手できるプロトコルのドキュメントを参照できます。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [運用環境への移行](scenario-web-app-sign-user-production.md)
