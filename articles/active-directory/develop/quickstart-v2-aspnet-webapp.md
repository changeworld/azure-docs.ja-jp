---
title: 'クイック スタート: ユーザーをサインインさせる ASP.NET Web アプリ'
titleSuffix: Microsoft identity platform
description: ASP.NET Web アプリで Azure AD ユーザーをサインインさせる方法を示すコード サンプルをダウンロードして実行します。
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
ms.custom: devx-track-csharp, aaddev, identityplatformtop40, "scenarios:getting-started", "languages:ASP.NET", contperf-fy21q1, mode-other
ms.openlocfilehash: b784950dcd1a5b8f1a55d45719a79a64f1fbdfba
ms.sourcegitcommit: 3f20f370425cb7d51a35d0bba4733876170a7795
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/27/2022
ms.locfileid: "137805054"
---
# <a name="quickstart-aspnet-web-app-that-signs-in-azure-ad-users"></a>クイック スタート: Azure AD ユーザーをサインインさせる ASP.NET Web アプリ

このクイックスタートでは、ASP.NET Web アプリケーションで Azure Active Directory (Azure AD) アカウントを持つユーザーをサインインさせる方法を示すコード サンプルをダウンロードして実行します。

#### <a name="step-1-configure-your-application-in-the-azure-portal"></a>手順 1:Azure portal でのアプリケーションの構成
このクイックスタートのコード サンプルを動作させるには、 **[リダイレクト URI]** に「 **https://localhost:44368/** 」と入力します。

> [!div class="nextstepaction"]
> [この変更を行う]()

> [!div class="alert alert-info"]
> ![構成済み](media/quickstart-v2-aspnet-webapp/green-check.png) アプリケーションはこの属性で構成されています。

#### <a name="step-2-download-the-project"></a>手順 2:プロジェクトのダウンロード

Visual Studio 2019 を使用してプロジェクトを実行します。
> [!div class="sxs-lookup nextstepaction"]
> [コード サンプルをダウンロードします](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-DotNet/archive/master.zip)

[!INCLUDE [active-directory-develop-path-length-tip](../../../includes/active-directory-develop-path-length-tip.md)]


#### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>手順 3:アプリが構成され、実行準備ができる
アプリのプロパティの値を使用してプロジェクトを構成しました。

1. ルート フォルダーに近いローカル フォルダーに .zip ファイルを展開します。 たとえば、*C:\Azure-Samples* に展開します。
   
   Windows におけるパスの長さの制限に起因したエラーを防ぐため、ドライブのルートに近いディレクトリをアーカイブの展開先とすることをお勧めします。
2. Visual Studio でソリューションを開きます (*AppModelv2-WebApp-OpenIDConnect-DotNet.sln*)。
3. Visual Studio のバージョンによっては、プロジェクト **AppModelv2-WebApp-OpenIDConnect-DotNet** を右クリックして **[NuGet パッケージの復元]** を選択することが必要になる場合があります。
4. **[表示]**  >  **[その他のウィンドウ]**  >  **[パッケージ マネージャー コンソール]** を選択してパッケージ マネージャー コンソールを開きます。 次に、`Update-Package Microsoft.CodeDom.Providers.DotNetCompilerPlatform -r` を実行します。

> [!NOTE]
> `Enter_the_Supported_Account_Info_Here`

## <a name="more-information"></a>詳細情報

このセクションでは、ユーザーをサインインさせるために必要なコードの概要を示します。 この概要は、コードの働きや主な引数、また既存の ASP.NET アプリケーションにサインインを追加する方法を理解するうえで役立ちます。


### <a name="how-the-sample-works"></a>このサンプルのしくみ

![サンプル アプリにおける Web ブラウザー、Web アプリ、Microsoft ID プラットフォーム間の対話を表す図。](media/quickstart-v2-aspnet-webapp/aspnetwebapp-intro.svg)

### <a name="owin-middleware-nuget-packages"></a>OWIN ミドルウェア NuGet パッケージ

ASP.NET で OpenID Connect を使用して Cookie ベースの認証を行うために、OWIN ミドルウェア パッケージを使用して認証パイプラインをセットアップできます。 これらのパッケージは、Visual Studio 内のパッケージ マネージャー コンソールから次のコマンドを実行してインストールできます。

```powershell
Install-Package Microsoft.Owin.Security.OpenIdConnect
Install-Package Microsoft.Owin.Security.Cookies
Install-Package Microsoft.Owin.Host.SystemWeb
```

### <a name="owin-startup-class"></a>OWIN スタートアップ クラス

OWIN ミドルウェアでは、ホスト プロセスの開始時に実行される "*スタートアップ クラス*" が使用されます。 このクイックスタートでは、ルート フォルダーに *startup.cs* ファイルがあります。 次のコードは、このクイックスタートで使用されるパラメーターを示しています。

```csharp
public void Configuration(IAppBuilder app)
{
    app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

    app.UseCookieAuthentication(new CookieAuthenticationOptions());
    app.UseOpenIdConnectAuthentication(
        new OpenIdConnectAuthenticationOptions
        {
            // Sets the client ID, authority, and redirect URI as obtained from Web.config
            ClientId = clientId,
            Authority = authority,
            RedirectUri = redirectUri,
            // PostLogoutRedirectUri is the page that users will be redirected to after sign-out. In this case, it's using the home page
            PostLogoutRedirectUri = redirectUri,
            Scope = OpenIdConnectScope.OpenIdProfile,
            // ResponseType is set to request the code id_token, which contains basic information about the signed-in user
            ResponseType = OpenIdConnectResponseType.CodeIdToken,
            // ValidateIssuer set to false to allow personal and work accounts from any organization to sign in to your application
            // To only allow users from a single organization, set ValidateIssuer to true and the 'tenant' setting in Web.config to the tenant name
            // To allow users from only a list of specific organizations, set ValidateIssuer to true and use the ValidIssuers parameter
            TokenValidationParameters = new TokenValidationParameters()
            {
                ValidateIssuer = false // Simplification (see note below)
            },
            // OpenIdConnectAuthenticationNotifications configures OWIN to send notification of failed authentications to the OnAuthenticationFailed method
            Notifications = new OpenIdConnectAuthenticationNotifications
            {
                AuthenticationFailed = OnAuthenticationFailed
            }
        }
    );
}
```

> |Where  | 説明 |
> |---------|---------|
> | `ClientId`     | Azure portal に登録されているアプリケーションのアプリケーション ID。 |
> | `Authority`    | ユーザーが認証するためのセキュリティ トークン サービス (STS) エンドポイント。 通常、パブリック クラウドでは `https://login.microsoftonline.com/{tenant}/v2.0` になります。 この URL の *{tenant}* には、テナントの名前またはテナント ID を指定するか、共通エンドポイントへの参照を表す `common` を指定します (共通エンドポイントは、マルチテナント型のアプリケーションで使用されます。) |
> | `RedirectUri`  | Microsoft ID プラットフォームに対する認証後にユーザーが誘導される URL。 |
> | `PostLogoutRedirectUri`     | サインオフ後にユーザーが誘導される URL。 |
> | `Scope`     | 要求されているスコープのスペース区切りリスト。 |
> | `ResponseType`     | 認証からの応答に承認コードと ID トークンを含めるという要求。 |
> | `TokenValidationParameters`     | トークン検証のためのパラメーター リスト。 この場合、`ValidateIssuer` は `false` に設定され、任意の個人、あるいは職場または学校のアカウント タイプからのサインインを受け付け可能であることを示します。 |
> | `Notifications`     | `OpenIdConnect` メッセージに対して実行できるデリゲートのリスト。 |


> [!NOTE]
> `ValidateIssuer = false` の設定は、このクイック スタートを単純にするためのものです。 実際のアプリケーションでは、発行者を検証します。 その方法については、サンプルを参照してください。

### <a name="authentication-challenge"></a>認証チャレンジ

コントローラーで認証チャレンジを要求することによって、ユーザーにサインインを強制することができます。

```csharp
public void SignIn()
{
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(
            new AuthenticationProperties{ RedirectUri = "/" },
            OpenIdConnectAuthenticationDefaults.AuthenticationType);
    }
}
```

> [!TIP]
> この方法を使用して認証チャレンジを要求するかどうかは任意です。 通常、認証されたユーザーと認証されていないユーザーの両方に、なんらかのビューへのアクセスを用意したい場合に、これを使用します。 代わりに、次のセクションで説明する方法を使ってコントローラーを保護することもできます。

### <a name="attribute-for-protecting-a-controller-or-a-controller-actions"></a>コントローラーまたはコントローラー アクションを保護するための属性

コントローラーまたはコントローラー アクションは、`[Authorize]` 属性を使用して保護することができます。 この属性は、認証されたユーザーにのみコントローラーのアクションへのアクセスを許可することで、コントローラーまたはアクションへのアクセスを制限します。 認証チャレンジは、`[Authorize]` 属性によって修飾されたいずれかのアクションまたはコントローラーに対して非認証ユーザーがアクセスを試みると自動的に実行されます。

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>次のステップ

アプリケーションや新機能の構築についての完全なステップ バイ ステップ ガイドについては、ASP.NET チュートリアルをお試しください。このクイックスタートの完全な説明も含まれています。

> [!div class="nextstepaction"]
> [ASP.NET Web アプリにサインインを追加する](tutorial-v2-asp-webapp.md)
