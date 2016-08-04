<properties
	pageTitle="Azure AD B2C | Microsoft Azure"
	description="Azure Active Directory B2C を使用し、認証に OAuth 2.0 アクセス トークンを使用してセキュリティ保護された .NET Web API を構築する方法。"
	services="active-directory-b2c"
	documentationCenter=".net"
	authors="dstrockis"
	manager="msmbaldwin"
	editor=""/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="07/22/2016"
	ms.author="dastrock"/>

# Azure Active Directory B2C: .NET Web API を構築する

<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)] -->

Azure Active Directory (Azure AD) B2C では、OAuth 2.0 アクセス トークンを使用して Web API をセキュリティ保護できます。これらのトークンにより、Azure AD B2C を使用するクライアント アプリは API の認証を行うことができます。この記事では、ユーザーによる CRUD タスクを可能にする .NET Model-View-Controller (MVC) の "To-Do List" API を作成する方法について説明します。この Web API は Azure AD B2C を使用してセキュリティで保護されるため、認証済みのユーザーのみが To-Do List を管理できます。

## Azure AD B2C ディレクトリの作成

Azure AD B2C を使用するには、ディレクトリ (つまり、テナント) を作成しておく必要があります。ディレクトリは、ユーザー、アプリ、グループなどをすべて格納するためのコンテナーです。まだディレクトリを作成していない場合は、先に進む前に [B2C ディレクトリを作成](active-directory-b2c-get-started.md)してください。

## アプリケーションの作成

次に、B2C ディレクトリにアプリを作成する必要があります。これにより、アプリと安全に通信するために必要な情報を Azure AD に提供します。アプリを作成するには、[こちらの手順](active-directory-b2c-app-registration.md)に従ってください。次を行ってください。

- アプリケーションに **Web アプリ**または **Web API** を含めます。
- Web アプリの**リダイレクト URI (Uniform Resource Identifier)** である `https://localhost:44316/` を使用します。これは、このコード サンプルでの Web アプリ クライアントの既定の場所です。
- アプリに割り当てられた**アプリケーション ID** をコピーしておきます。この情報は後で必要になります。

 [AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## ポリシーの作成

Azure AD B2C では、すべてのユーザー エクスペリエンスが[ポリシー](active-directory-b2c-reference-policies.md)によって定義されます。このコード サンプルのクライアントには、3 つの ID エクスペリエンス (サインアップ、サインイン、プロファイル編集) が含まれています。[ポリシーについてのリファレンス記事](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy)で説明されているように、種類ごとにポリシーを作成する必要があります。3 つのポリシーを作成するときは、以下の点に注意してください。

- ID プロバイダーのブレードで、**[User ID sign-up (ユーザー ID サインアップ)]** または **[Email sign-up (電子メール サインアップ)]** を選択します。
- サインアップ ポリシーで、**[表示名]** と他のサインアップ属性を選択します。
- すべてのポリシーで、アプリケーション要求として **[表示名]** 要求と **[オブジェクト ID]** 要求を選択します。その他のクレームも選択できます。
- ポリシーの作成後、各ポリシーの**名前**をコピーしておきます。これらのポリシー名は後で必要になります。

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

3 つのポリシーの作成が正常に完了したら、いつでもアプリをビルドできます。

## コードのダウンロード

このチュートリアルのコードは、[GitHub](https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet) で管理されています。手順に従ってサンプルをビルドする場合は、[スケルトン プロジェクトを .zip ファイルとしてダウンロード](https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet/archive/skeleton.zip)できます。スケルトンを複製することもできます。

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet.git
```

また、完成済みのアプリも、[.zip ファイルとして入手する](https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet/archive/complete.zip)か、同じリポジトリの `complete` ブランチで入手できます。

サンプル コードをダウンロードした後、Visual Studio の .sln ファイルを開いて作業を開始します。ソリューション ファイルには、2 つのプロジェクト `TaskWebApp` と `TaskService` が含まれます。`TaskWebApp` は、ユーザーが操作する MVC Web アプリケーションです。`TaskService` は、各ユーザーの To-Do List を格納する、アプリのバックエンド Web API です。

## タスク Web アプリの構成

ユーザーが `TaskWebApp` を操作するとき、クライアントは Azure AD に要求を送信し、`TaskService` Web API の呼び出しに使用できるトークンを取得します。ユーザーをサインインさせてトークンを取得するには、アプリに関する情報を `TaskWebApp` に提供する必要があります。`TaskWebApp` プロジェクトで、プロジェクトのルートにある `web.config` ファイルを開き、`<appSettings>` セクションの値を次の内容に置き換えます。`AadInstance`、`RedirectUri`、`TaskServiceUrl` の各値はそのまま使用できます。

```
  <appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:Tenant" value="fabrikamb2c.onmicrosoft.com" />
    <add key="ida:ClientId" value="90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}/v2.0/.well-known/openid-configuration?p={1}" />
    <add key="ida:RedirectUri" value="https://localhost:44316/" />
    <add key="ida:SignUpPolicyId" value="b2c_1_sign_up" />
    <add key="ida:SignInPolicyId" value="b2c_1_sign_in" />
    <add key="ida:UserProfilePolicyId" value="b2c_1_edit_profile" />
    <add key="api:TaskServiceUrl" value="https://localhost:44332/" />
  </appSettings>
```

この記事では、`TaskWebApp` クライアントの構築については取り上げていません。Azure AD B2C を使用して Web アプリを構築する方法については、[.NET Web アプリのチュートリアル](active-directory-b2c-devquickstarts-web-dotnet.md)を参照してください。

## API のセキュリティ保護

ユーザーの代わりに API を呼び出すクライアントが用意できたら、OAuth 2.0 ベアラー トークンを使用して `TaskService` をセキュリティ保護できます。API では、Microsoft の Open Web Interface for .NET (OWIN) ライブラリを使用してトークンを受け入れて検証できます。

### OWIN をインストールする
まず、OWIN OAuth 認証パイプラインをインストールします。

```
PM> Install-Package Microsoft.Owin.Security.OAuth -ProjectName TaskService
PM> Install-Package Microsoft.Owin.Security.Jwt -ProjectName TaskService
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TaskService
```

### B2C の詳細情報を入力する
`TaskService` プロジェクトのルートにある `web.config` ファイルを開き、`<appSettings>` セクションの値を置き換えます。これらの値は、API と OWIN ライブラリ全体で使用されます。`AadInstance` 値は変更せずにそのまま使用できます。

```
  <appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}/v2.0/.well-known/openid-configuration?p={1}" />
    <add key="ida:Tenant" value="fabrikamb2c.onmicrosoft.com" />
    <add key="ida:ClientId" value="90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6" />
    <add key="ida:SignUpPolicyId" value="b2c_1_sign_up" />
    <add key="ida:SignInPolicyId" value="b2c_1_sign_in" />
    <add key="ida:UserProfilePolicyId" value="b2c_1_edit_profile" />
  </appSettings>
```

### OWIN Startup クラスを追加する
OWIN のスタートアップ クラスを `TaskService` プロジェクト (`Startup.cs`) に追加します。プロジェクトを右クリックし、**[追加]**、**[新しい項目]** の順に選択して、"OWIN" を検索します。


```C#
// Startup.cs

// Change the class declaration to "public partial class Startup" - we’ve already implemented part of this class for you in another file.
public partial class Startup
{
	// The OWIN middleware will invoke this method when the app starts
    public void Configuration(IAppBuilder app)
    {
        ConfigureAuth(app);
    }
}
```

### OAuth 2.0 認証を構成する
`App_Start\Startup.Auth.cs` ファイルを開いて、`ConfigureAuth(...)` メソッドを実装します。

```C#
// App_Start\Startup.Auth.cs

public partial class Startup
{
    // These values are pulled from web.config
    public static string aadInstance = ConfigurationManager.AppSettings["ida:AadInstance"];
    public static string tenant = ConfigurationManager.AppSettings["ida:Tenant"];
    public static string clientId = ConfigurationManager.AppSettings["ida:ClientId"];
    public static string signUpPolicy = ConfigurationManager.AppSettings["ida:SignUpPolicyId"];
    public static string signInPolicy = ConfigurationManager.AppSettings["ida:SignInPolicyId"];
    public static string editProfilePolicy = ConfigurationManager.AppSettings["ida:UserProfilePolicyId"];

    public void ConfigureAuth(IAppBuilder app)
    {   
        app.UseOAuthBearerAuthentication(CreateBearerOptionsFromPolicy(signUpPolicy));
        app.UseOAuthBearerAuthentication(CreateBearerOptionsFromPolicy(signInPolicy));
        app.UseOAuthBearerAuthentication(CreateBearerOptionsFromPolicy(editProfilePolicy));
    }

    public OAuthBearerAuthenticationOptions CreateBearerOptionsFromPolicy(string policy)
    {
        TokenValidationParameters tvps = new TokenValidationParameters
        {
            // This is where you specify that your API only accepts tokens from its own clients
            ValidAudience = clientId,
            AuthenticationType = policy,
        };

        return new OAuthBearerAuthenticationOptions
        {
            // This SecurityTokenProvider fetches the Azure AD B2C metadata & signing keys from the OpenIDConnect metadata endpoint
            AccessTokenFormat = new JwtFormat(tvps, new OpenIdConnectCachingSecurityTokenProvider(String.Format(aadInstance, tenant, policy))),
        };
    }
}
```

### タスク コントローラーをセキュリティで保護する
OAuth 2.0 認証を使用するようにアプリを構成した後は、タスク コントローラーに `[Authorize]` タグを追加することによって、Web API をセキュリティ保護できます。このコントローラーでは To-Do List のすべての操作が実行されるため、コントローラー全体をクラス レベルでセキュリティ保護する必要があります。よりきめ細かく制御するために、`[Authorize]` タグを個々のアクションに追加することもできます。

```C#
// Controllers\TasksController.cs

[Authorize]
public class TasksController : ApiController
{
	...
}
```

### トークンからユーザー情報を取得する
`TasksController` はデータベースにタスクを格納します。このデータベースでは、各タスクに、そのタスクを "所有" するユーザーが関連付けられています。所有者は、ユーザーの**オブジェクト ID** によって識別されます (すべてのポリシーにアプリケーション要求としてオブジェクト ID を追加する必要があったのは、このためです)。

```C#
// Controllers\TasksController.cs

public IEnumerable<Models.Task> Get()
{
	string owner = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
	IEnumerable<Models.Task> userTasks = db.Tasks.Where(t => t.owner == owner);
	return userTasks;
}
```

## サンプル アプリを実行する

最後に、`TaskWebApp` と `TaskService` を両方ともビルドして実行します。電子メール アドレスまたはユーザー名を使用して、アプリにサインアップします。ユーザーの To-Do List にいくつかのタスクを作成すると、クライアントを停止して再起動した後でも、作成したタスクが API に保持されていることを確認できます。

## ポリシーの編集

Azure AD B2C を使用して API をセキュリティ保護した後は、アプリのポリシーでさまざまな設定を試し、その効果 (または不足部分) を API で確認することができます。ポリシー内のアプリケーション要求を操作し、Web API で利用できるユーザー情報を変更することができます。追加した要求は、既に説明したとおり .NET MVC Web API の `ClaimsPrincipal` オブジェクトで利用できます。

<!--

## 次のステップ

さらに高度な B2C のトピックに進むことができます。 次のチュートリアルを試してみてください。

[Call a web API from a web app]()

[Customize the UX of your B2C app]()

-->

<!---HONumber=AcomDC_0727_2016-->