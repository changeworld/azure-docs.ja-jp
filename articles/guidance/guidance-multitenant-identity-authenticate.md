<properties
   pageTitle="マルチテナント アプリケーションでの認証 | Microsoft Azure"
   description="マルチテナント アプリケーションで Azure AD のユーザーを認証する方法"
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

# マルチテナント アプリケーションでの認証

この記事は[シリーズの一部](guidance-multitenant-identity.md)です。このシリーズに付属する完成した[サンプル アプリケーション]もあります。

この記事では、マルチテナント アプリケーションで認証に OpenID Connect (OIDC) を使用して Azure Active Directory (Azure AD) のユーザーを認証する方法について説明します。

## 概要

[参照実装](guidance-multitenant-identity-tailspin.md)は、ASP.NET Core 1.0 アプリケーションです。このアプリケーションでは、OIDC 認証フローを実行するために、組み込みの OpenID Connect ミドルウェアを使用しています。次の図では、ユーザーがサインインしたときに行われる処理の概要を示します。

![Authentication flow](media/guidance-multitenant-identity/auth-flow.png)

1.	ユーザーがアプリの "サインイン" ボタンをクリックします。このアクションは、MVC コントローラーによって処理されます。
2.	MVC コントローラーが **ChallengeResult** アクションを返します。
3.	ミドルウェアが **ChallengeResult** をインターセプトし、302 応答を作成します。これにより、ユーザーは Azure AD サインイン ページにリダイレクトされます。
4.	ユーザーが Azure AD で認証を行います。
5.	Azure AD が ID トークンをアプリケーションに送信します。
6.	ミドルウェアが ID トークンを検証します。この時点で、ユーザーはアプリケーションの内部で認証されます。
7.	ミドルウェアがユーザーをアプリケーションにリダイレクトします。

## Azure AD へのアプリケーションの登録

OpenID Connect を有効にするために、SaaS プロバイダーは、アプリケーションをその Azure AD テナント内で登録します。

アプリケーションを登録するには、「[Azure Active Directory とアプリケーションの統合](../active-directory/active-directory-integrating-applications.md)」の「To register an application in the Azure Management Portal (Azure 管理ポータルでアプリケーションを登録するには)」の手順に従ってください。

**[構成]** ページで、次の手順に従います。

-	クライアント ID を書き留めます。
-	**[アプリケーションはマルチテナントです]** で、**[はい]** を選択します。
-	**[応答 URL]** に、Azure AD が認証応答を送信する URL を設定します。アプリのベース URL を使用できます。
  -	注: ホスト名とデプロイされたアプリが一致する限り、任意の URL パスを使用できます。
  -	複数の応答 URL を設定できます。開発時は、アプリをローカルで実行するために、`localhost` アドレスを使用できます。
-	クライアント シークレットを生成します。これには、**[キー]** の **[時間の選択]** ボックスの一覧で、1 年または 2 年を選択します。**[保存]** をクリックするとキーが表示されます。この値は必ずコピーしておいてください。この値は、構成ページを再読み込みしたときに再度表示されません。

## 認証ミドルウェアの構成

このセクションでは、OpenID Connect を使用したマルチテナント認証用に ASP.NET Core 1.0 で認証ミドルウェアを構成する方法について説明します。

スタートアップ クラスで、OpenID Connect ミドルウェアを追加します。

```csharp
app.UseOpenIdConnectAuthentication(options =>
{
    options.AutomaticAuthenticate = true;
    options.AutomaticChallenge = true;
    options.ClientId = [client ID];
    options.Authority = "https://login.microsoftonline.com/common/";
    options.CallbackPath = [callback path];
    options.PostLogoutRedirectUri = [application URI];
    options.SignInScheme = CookieAuthenticationDefaults.AuthenticationScheme;
    options.TokenValidationParameters = new TokenValidationParameters
    {
        ValidateIssuer = false
    };
    options.Events = [event callbacks];
});
```

> [AZURE.NOTE] [Startup.cs](https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Web/Startup.cs) を参照してください。

スタートアップ クラスの詳細については、ASP.NET Core 1.0 ドキュメントの「[Application Startup (アプリケーションのスタートアップ)](https://docs.asp.net/en/latest/fundamentals/startup.html)」を参照してください。

次のミドルウェア オプションを設定します。

- **ClientId**。アプリケーションのクライアント ID。Azure AD にアプリケーションを登録したときに取得した ID です。
- **Authority**。マルチテナント アプリケーションの場合は、`https://login.microsoftonline.com/common/` に設定します。これは、Azure AD の共通エンドポイントの URL です。これにより、任意の Azure AD テナントのユーザーがサインインできます。共通エンドポイントの詳細については、[このブログの投稿](http://www.cloudidentity.com/blog/2014/08/26/the-common-endpoint-walks-like-a-tenant-talks-like-a-tenant-but-is-not-a-tenant/)を参照してください。
- **TokenValidationParameters** では、**ValidateIssuer** を false に設定します。これは、ID トークンの発行者値がアプリによって検証されることを意味します (トークン自体は引き続きミドルウェアが検証します)。 発行者の検証の詳細については、「[発行者の検証](guidance-multitenant-identity-claims.md#issuer-validation)」を参照してください。
- **CallbackPath**。Azure AD に登録した応答 URL のパスと同じパスを設定します。たとえば、応答 URL が `http://contoso.com/aadsignin` の場合は、**CallbackPath** に `aadsignin` を設定する必要があります。このオプションを設定しない場合、既定値は `signin-oidc` です。
- **PostLogoutRedirectUri**。サインアウトしたユーザーをリダイレクトする URL を指定します。これは、匿名要求を許可するページ (通常はホーム ページ) にする必要があります。
- **SignInScheme**。`CookieAuthenticationDefaults.AuthenticationScheme` に設定します。この設定は、ユーザーが承認された後、ユーザー要求がローカルの Cookie に保存されることを意味します。この Cookie により、ブラウザー セッションでユーザーがログインした状態が保たれます。
- **[イベント]:** イベントのコールバック。「[認証イベント](#authentication-events)」を参照してください。

さらに、Cookie 認証ミドルウェアをパイプラインに追加します。このミドルウェアは、ユーザー要求を Cookie に書き込んだ後、以降のページ読み込み中に Cookie を読み取ります。

```csharp
app.UseCookieAuthentication(options =>
{
    options.AutomaticAuthenticate = true;
    options.AutomaticChallenge = true;
    options.AccessDeniedPath = "/Home/Forbidden";
});
```

## 認証フローの開始

ASP.NET MVC で認証フローを開始するには、次のように、コントローラーから **ChallengeResult** を返します。

```csharp
[AllowAnonymous]
public IActionResult SignIn()
{
    return new ChallengeResult(
        OpenIdConnectDefaults.AuthenticationScheme,
        new AuthenticationProperties
        {
            IsPersistent = true,
            RedirectUri = Url.Action("SignInCallback", "Account")
        });
}
```

これにより、ミドルウェアは、認証エンドポイントにリダイレクトする 302 (Found) 応答を返します。

## ユーザー ログイン セッション

既に説明したように、ユーザーが初めてサインインするときに、Cookie 認証ミドルウェアがユーザー要求を Cookie に書き込みます。それ以降、HTTP 要求は、Cookie を読み取ることによって認証されます。

Cookie ミドルウェアが既定で書き込みを行うのは、ユーザーがブラウザーを閉じると削除される[セッション Cookie][session-cookie] です。ユーザーは、次にそのサイトにアクセスする際、もう一度サインインする必要があります。ただし、**ChallengeResult** の **IsPersistent** を true に設定した場合、ミドルウェアは永続的な Cookie に書き込みを行うため、ユーザーはブラウザーを閉じた後もログインしたままになります。Cookie の有効期限を構成できます (「[Controlling cookie options (Cookie のオプションの制御)][cookie-options]」を参照)。永続的な Cookie はユーザーにとってより便利ですが、毎回ユーザーにサインインを求める必要がある一部のアプリケーション (銀行取引アプリケーションなど) には適切でない場合があります。

## OpenID Connect ミドルウェアについて

ASP.NET の OpenID Connect ミドルウェアは、プロトコルの詳細のほとんどが公開されていません。このセクションでは、プロトコル フローを理解するのに役立つ可能性のある、実装に関するいくつかの注意事項について説明します。

最初に、ASP.NET の観点から認証フローを見ていきましょう (アプリと Azure AD 間の OIDC プロトコル フローの詳細は無視します)。そのプロセスを次の図に示します。

![Sign-in flow](media/guidance-multitenant-identity/sign-in-flow.png)

この図では、2 つの MVC コントローラーがあります。Account コントローラーはサインイン要求を処理し、Home コントローラーはホーム ページを提供します。

次に、認証プロセスを示します。

1. ユーザーが "サインイン" ボタンをクリックすると、ブラウザーから GET 要求が送信されます (例: `GET /Account/SignIn/`)。
2. Account コントローラーが `ChallengeResult` を返します。
3. OIDC ミドルウェアが、Azure AD にリダイレクトする HTTP 302 応答を返します。
4. ブラウザーが Azure AD に認証要求を送信します。
5. ユーザーが Azure AD にサインインすると、Azure AD から認証応答が返されます。
6. OIDC ミドルウェアが要求プリンシパルを作成して Cookie 認証ミドルウェアに渡します。
7. Cookie ミドルウェアが要求プリンシパルをシリアル化し、Cookie を設定します。
8. OIDC ミドルウェアにより、アプリケーションのコールバック URL にリダイレクトされます。
10. ブラウザーがそのリダイレクトに従い、要求で Cookie を送信します。
11. Cookie ミドルウェアが Cookie を要求プリンシパルに逆シリアル化し、要求プリンシパルと同じになるように `HttpContext.User` を設定します。要求は MVC コントローラーにルーティングされます。

### 認証チケット

認証が成功すると、OIDC ミドルウェアは認証チケットを作成します。認証チケットには、ユーザーの要求を保持する要求プリンシパルが含まれます。チケットには、**AuthenticationValidated** イベントまたは **TicketReceived** イベント内でアクセスできます。

> [AZURE.NOTE] 認証フロー全体が完了するまで、`HttpContext.User` は、認証されたユーザー_ではなく_、匿名プリンシパルを保持します。匿名プリンシパルには、空の要求コレクションがあります。認証が完了し、アプリがリダイレクトされた後、Cookie ミドルウェアは、認証 Cookie を逆シリアル化し、`HttpContext.User` を、認証されたユーザーを表す要求プリンシパルに設定します。

### 認証イベント

認証プロセス中、OpenID Connect ミドルウェアは、一連のイベントを発生させます。

- **RedirectToAuthenticationEndpoint**。ミドルウェアが認証エンドポイントにリダイレクトされる直前に呼び出されます。このイベントを使用すると、(たとえば、要求パラメーターを追加するために) リダイレクト URL を変更できます。例については、「[管理者の同意プロンプトを追加する](guidance-multitenant-identity-signup.md#adding-the-admin-consent-prompt)」を参照してください。

- **AuthorizationResponseReceived**。ミドルウェアが ID プロバイダー (IDP) から認証応答を受け取った後、かつミドルウェアがその応答を検証する前に呼び出されます。

- **AuthorizationCodeReceived**。承認コードで呼び出されます。

- **TokenResponseReceived**。ミドルウェアが IDP からアクセス トークンを取得した後に呼び出されます。承認コード フローにのみ当てはまります。

- **AuthenticationValidated**。ミドルウェアが ID トークンを検証した後に呼び出されます。この時点で、アプリケーションには、ユーザーに関する一連の検証済みの要求があります。このイベントを使用すると、要求に対する追加の検証を実行したり、要求を変換したりできます。「[マルチテナント アプリケーションでの要求ベース ID の操作](guidance-multitenant-identity-claims.md)」を参照してください。

- **UserInformationReceived**。ミドルウェアがユーザー情報エンドポイントからユーザー プロファイルを取得する場合に呼び出されます。承認コード フローのみ、およびミドルウェア オプションが `GetClaimsFromUserInfoEndpoint = true` の場合にのみ当てはまります。

- **TicketReceived**。認証が完了したときに呼び出されます。これは、認証が成功したことを想定した最後のイベントです。このイベントが処理された後、ユーザーがアプリケーションにサインインします。

- **AuthenticationFailed**。認証が失敗した場合に呼び出されます。このイベントを使用すると、エラー ページにリダイレクトするなどして、認証エラーを処理します。

これらのイベントのコールバックを指定するには、ミドルウェアで **Events** オプションを設定します。イベント ハンドラーを宣言するには、ラムダを使用したインライン宣言と **OpenIdConnectEvents** から派生したクラスでの宣言という 2 つの方法があります。

ラムダを使用したインライン宣言は次のとおりです。

```csharp
app.UseOpenIdConnectAuthentication(options =>
{
    // Other options not shown.

    options.Events = new OpenIdConnectEvents
    {
        OnTicketReceived = (context) =>
        {
             // Handle event
             return Task.FromResult(0);
        },
        // other events
    }
});
```

**OpenIdConnectEvents** から派生した宣言は次のとおりです。

```csharp
public class SurveyAuthenticationEvents : OpenIdConnectEvents
{
    public override Task TicketReceived(TicketReceivedContext context)
    {
        // Handle event
        return base.TicketReceived(context);
    }
    // other events
}

// In Startup.cs:
app.UseOpenIdConnectAuthentication(options =>
{
    // Other options not shown.

    options.Events = new SurveyAuthenticationEvents();
});
```

イベントのコールバックに大量のロジックが含まれる場合は、スタートアップ クラスが乱雑にならないように、2 番目の方法を使用することをお勧めします。参照実装ではこの方法が使用されています。[SurveyAuthenticationEvents.cs](https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Web/Security/SurveyAuthenticationEvents.cs) を参照してください。

### OpenID Connect のエンドポイント

Azure AD では、[OpenID Connect Discovery](https://openid.net/specs/openid-connect-discovery-1_0.html) がサポートされています。この方式では、ID プロバイダー (IDP) は[既知のエンドポイント](https://openid.net/specs/openid-connect-discovery-1_0.html#ProviderConfig)から JSON メタデータ ドキュメントを返します。メタデータ ドキュメントには、次のような情報が含まれます。

-	承認エンドポイントの URL。これは、アプリがユーザーの認証を行うためにリダイレクトされる場所です。
-	"終了セッション" エンドポイントの URL。これは、ユーザーをログアウトするためにアプリが移動する場所です。
-	署名キーを取得する URL。これは、クライアントが IDP から取得した OIDC トークンを検証するために使用します。

既定では、OIDC ミドルウェアは、このメタデータの取得方法を認識しています。ミドルウェアの **Authority** オプションを設定すると、ミドルウェアによってメタデータの URL が作成されます (**MetadataAddress** オプションを設定すると、メタデータの URL をオーバーライドできます)。

### OpenID Connect のフロー

OIDC ミドルウェアは、既定で、フォーム ポスト応答モードによるハイブリッド フローを使用します。

-	_ハイブリッド フロー_は、クライアントが承認サーバーへの同じラウンドトリップ内で ID トークンと承認コードを取得できることを意味します。
-	_フォーム ポスト応答モード_は、承認サーバーが HTTP POST 要求を使用して ID トークンと承認コードをアプリに送信することを意味します。値は form-urlencoded 形式 (content type = "application/x-www-form-urlencoded") です。

OIDC ミドルウェアが承認エンドポイントにリダイレクトされる場合、リダイレクト URL には、OIDC で必要なクエリ文字列パラメーターがすべて含まれます。ハイブリッド フローのパラメーターは次のとおりです。

-	client\_id。この値は、**ClientId** オプションで設定します。
-	scope = "openid profile"。OIDC 要求であり、ユーザーのプロファイルが必要であることを意味します。
-	response\_type = "code id\_token"。ハイブリッド フローを指定します。
-	response\_mode = "form\_post"。フォーム ポスト応答を指定します。

別のフローを指定するには、オプションの **ResponseType** プロパティを設定します。次に例を示します。

```csharp
app.UseOpenIdConnectAuthentication(options =>
{
    options.ResponseType = "code"; // Authorization code flow

    // Other options
}
```

[cookie-options]: https://docs.asp.net/en/latest/security/authentication/cookie.html#controlling-cookie-options
[session-cookie]: https://en.wikipedia.org/wiki/HTTP_cookie#Session_cookie
[サンプル アプリケーション]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps

<!---HONumber=AcomDC_0302_2016-->