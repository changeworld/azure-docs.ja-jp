<properties
	pageTitle="Azure AD .NET の概要 | Microsoft Azure"
	description="サインインのために Azure AD と連携する .NET MVC Web アプリを構築する方法"
	services="active-directory"
	documentationCenter=".net"
	authors="dstrockis"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="09/16/2016"
	ms.author="dastrock"/>

# Azure AD を使用した Web アプリのサインインおよびサインアウト

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Azure AD を使用すると、数行のコードを追加するだけで、Web アプリの ID 管理を外部委託し、シングル サインインおよびサインアウトを提供することが、簡単に実現できます。ASP.NET Web アプリでは、.NET Framework 4.5 に含まれるコミュニティ駆動の OWIN ミドルウェアの Microsoft 実装を使用することにより、これを達成できます。ここでは、OWIN を使用して次のことを行います。
-	ID プロバイダーとして Azure AD を使用して、ユーザーをアプリにサインインします。
-	ユーザーについての情報を表示します。
-	ユーザーをアプリからサインアウトします。

これを行うには、次の手順を実行する必要があります。

1. アプリケーションを Azure AD に登録する
2. OWIN 認証パイプラインを使用するようにアプリをセットアップする
3. OWIN を使用して、サインイン要求およびサインアウト要求を Azure AD に発行する
4. ユーザーに関するデータを印刷する

最初に、[アプリのスケルトンをダウンロードするか](https://github.com/AzureADQuickStarts/WebApp-OpenIdConnect-DotNet/archive/skeleton.zip)、または[完全なサンプルをダウンロードします](https://github.com/AzureADQuickStarts/WebApp-OpenIdConnect-DotNet/archive/complete.zip)。アプリケーションの登録先となる Azure AD テナントも必要です。テナントを所有していない場合は、「[How to get an Azure Active Directory tenant (Azure Active Directory テナントの取得方法)](active-directory-howto-tenant.md)」を参照して取得してください。

## *1.アプリケーションを Azure AD に登録する*
アプリケーションがユーザー認証を処理できるようにするには、まず、アプリケーションをテナントに登録する必要があります。

- Microsoft Azure 管理ポータルにサインインします。
- 左側のナビゲーションで **[Active Directory]** をクリックします。
- アプリケーションの登録先となるテナントを選択します。
- **[アプリケーション]** タブをクリックし、下部のドロアーで [追加] をクリックします。
- 画面の指示に従い、新しい **Web アプリケーションまたは WebAPI** を作成します。
    - アプリケーションの **[名前]** には、エンド ユーザーがアプリケーションの機能を把握できるような名前を設定します。
    -	**[サインオン URL]** は、アプリのベース URL です。スケルトンの既定値は、`https://localhost:44320/` です。
    - **[アプリケーション ID/URI]** は、アプリケーションの一意識別子です。形式は、`https://<tenant-domain>/<app-name>` (たとえば、`https://contoso.onmicrosoft.com/my-first-aad-app`) です。
- 登録が完了すると、AAD により、アプリケーションに一意のクライアント ID が割り当てられます。この値は次のセクションで必要になるので、[構成] タブからコピーします。

## *2.OWIN 認証パイプラインを使用するようにアプリをセットアップする*
ここでは、OpenID Connect 認証プロトコルを使用するように、OWIN ミドルウェアを構成します。OWIN は、サインイン要求またはサインアウト要求の発行、ユーザー セッションの管理、ユーザーに関する情報の取得などを行うために使用されます。

-	まず、パッケージ マネージャー コンソールを使用して、OWIN ミドルウェア NuGet パッケージをプロジェクトに追加します。

```
PM> Install-Package Microsoft.Owin.Security.OpenIdConnect
PM> Install-Package Microsoft.Owin.Security.Cookies
PM> Install-Package Microsoft.Owin.Host.SystemWeb
```

-	OWIN Startup クラスを `Startup.cs` という名前のプロジェクトに追加します。プロジェクトを右クリックし、**[追加]**、**[新しいアイテム]** の順にクリックし、"OWIN" を検索します。アプリが起動すると、OWIN ミドルウェアは `Configuration(...)` メソッドを呼び出します。
-	クラス宣言を `public partial class Startup` に変更します。このクラスの一部は別ファイルで実装済みです。`Configuration(...)` メソッドで、ConfgureAuth(…) を呼び出して、Web アプリ用の認証をセットアップします。

```C#
public partial class Startup
{
    public void Configuration(IAppBuilder app)
    {
        ConfigureAuth(app);
    }
}
```

-	ファイル `App_Start\Startup.Auth.cs` を開いて、`ConfigureAuth(...)` メソッドを実装します。`OpenIDConnectAuthenticationOptions` で提供されたパラメーターは、アプリが Azure AD と通信するための調整役として機能します。OpenID Connect ミドルウェアは、ユーザーに意識されない Cookie を使用するため、Cookie 認証もセットアップする必要があります。

```C#
public void ConfigureAuth(IAppBuilder app)
{
    app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

    app.UseCookieAuthentication(new CookieAuthenticationOptions());

    app.UseOpenIdConnectAuthentication(
        new OpenIdConnectAuthenticationOptions
        {
            ClientId = clientId,
            Authority = authority,
            PostLogoutRedirectUri = postLogoutRedirectUri,
        });
}
```

-	最後に、プロジェクトのルートにある `web.config` ファイルを開いて、構成値を `<appSettings>` セクションで入力します。
    -	アプリの `ida:ClientId` は、手順 1. で Azure ポータルからコピーした GUID です。
    -	`ida:Tenant` は、Azure AD テナントの名前 (たとえば、"contoso.onmicrosoft.com") です。
    -	`ida:PostLogoutRedirectUri` は、サインアウト要求が正常に完了した後に、ユーザーがリダイレクトされる Azure AD を示します。

## *3.OWIN を使用して、サインイン要求およびサインアウト要求を Azure AD に発行する*
これまでに、アプリは、OpenID Connect 認証プロトコルを使用して Azure AD と適切に通信するように構成されています。OWIN は、認証メッセージの構築、Azure AD からのトークンの検証、およびユーザー セッションの維持を行うためのすべての煩わしい処理を実行します。OWIN により処理されないのは、ユーザーにサインインおよびサインアウトの方法を提供する処理のみです。

- コントローラーで承認タグを使用することにより、特定のページでは、サインインしてからでないとアクセスできないようにすることができます。`Controllers\HomeController.cs` を開いて、`[Authorize]` タグを About コントローラーに追加します。

```C#
[Authorize]
public ActionResult About()
{
  ...
```

-	OWIN を使用することにより、コード内から認証要求を直接発行することもできます。`Controllers\AccountController.cs` を開きます。SignIn() アクションおよび SignOut() アクションで、それぞれ OpenID Connect チャレンジおよびサインアウト要求を発行します。

```C#
public void SignIn()
{
    // Send an OpenID Connect sign-in request.
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(new AuthenticationProperties { RedirectUri = "/" }, OpenIdConnectAuthenticationDefaults.AuthenticationType);
    }
}
public void SignOut()
{
    // Send an OpenID Connect sign-out request.
    HttpContext.GetOwinContext().Authentication.SignOut(
        OpenIdConnectAuthenticationDefaults.AuthenticationType, CookieAuthenticationDefaults.AuthenticationType);
}
```

-	ここで、`Views\Shared\_LoginPartial.cshtml` を開きます。ここは、ユーザーに、アプリのサインイン リンクおよびサインアウト リンクを表示し、ユーザーの名前をビュー内に表示する場所です。

```HTML
@if (Request.IsAuthenticated)
{
    <text>
        <ul class="nav navbar-nav navbar-right">
            <li class="navbar-text">
                Hello, @User.Identity.Name!
            </li>
            <li>
                @Html.ActionLink("Sign out", "SignOut", "Account")
            </li>
        </ul>
    </text>
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li>@Html.ActionLink("Sign in", "SignIn", "Account", routeValues: null, htmlAttributes: new { id = "loginLink" })</li>
    </ul>
}
```

## *4.ユーザー情報を表示する*
OpenID Connect を使用してユーザーの認証処理を実行すると、Azure AD は id\_token をアプリケーションに返します。id\_token には、"クレーム"、またはユーザーに関する "アサーション" を含みます。これらのクレームを使用して、アプリを個人向けにカスタマイズすることができます。

- `Controllers\HomeController.cs` ファイルを開きます。`ClaimsPrincipal.Current` セキュリティ プリンシパル オブジェクトを介して、コントローラー内のユーザー クレームにアクセスできます。

```C#
public ActionResult About()
{
    ViewBag.Name = ClaimsPrincipal.Current.FindFirst(ClaimTypes.Name).Value;
    ViewBag.ObjectId = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
    ViewBag.GivenName = ClaimsPrincipal.Current.FindFirst(ClaimTypes.GivenName).Value;
    ViewBag.Surname = ClaimsPrincipal.Current.FindFirst(ClaimTypes.Surname).Value;
    ViewBag.UPN = ClaimsPrincipal.Current.FindFirst(ClaimTypes.Upn).Value;

    return View();
}
```

最後に、アプリを構築して実行します。 *.onmicrosoft.com ドメインを使用して、テナントに新しいユーザーを作成します (作成していない場合)。そのユーザーとしてサインインすると、ユーザーの ID が上部のナビゲーション バーにどのように反映されるかがわかります。サインアウトし、テナント内の別のユーザーとして、再びサインインします。状況が許す場合は、このアプリケーションの別のインスタンス (独自のクライアント ID を使用) を登録して実行し、シングル サインオンの実際の動作を確認します。

参照用の完全なサンプル (構成値を除く) が、[ここ](https://github.com/AzureADQuickStarts/WebApp-OpenIdConnect-DotNet/archive/complete.zip)で提供されています。

これ以降は、さらに高度なトピックに進むことができます。次のチュートリアルを試してみてください。

[Azure AD を使用することによる Web API の保護](active-directory-devquickstarts-webapi-dotnet.md)

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]

<!---HONumber=AcomDC_0921_2016-->