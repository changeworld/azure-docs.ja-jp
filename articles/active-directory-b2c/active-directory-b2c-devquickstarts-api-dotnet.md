<properties
	pageTitle="Azure AD B2C プレビュー | Microsoft Azure"
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
	ms.topic="article"
	ms.date="12/22/2015"
	ms.author="dastrock"/>

# Azure Active Directory B2C プレビュー: .NET Web API を構築する

<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

Azure Active Directory (Azure AD) B2C では、OAuth 2.0 アクセス トークンを使用して Web API をセキュリティ保護できます。これらのトークンにより、Azure AD B2C を使用するクライアント アプリは API の認証を行うことができます。この記事では、ユーザーのサインアップ、サインイン、プロファイル管理などの処理を含む .NET Model-View-Controller (MVC) の "To-Do List" アプリの作成方法について説明します。各ユーザーの To-Do List は、タスク サービスによって保存されます。この Web API を使用することで、認証されたユーザーは、To-Do List のタスクを作成したり読んだりできます。

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

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

- ID プロバイダーのブレードで、**[ユーザー ID サインアップ]** または **[電子メール サインアップ]** を選択します。
- サインアップ ポリシーで、**[表示名]** と他のサインアップ属性を選択します。
- すべてのポリシーで、アプリケーション要求として **[表示名]** 要求と **[オブジェクト ID]** 要求を選択します。その他のクレームも選択できます。
- ポリシーの作成後、各ポリシーの**名前**をコピーしておきます。これらのポリシー名は後で必要になります。

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

3 つのポリシーの作成が正常に完了したら、いつでもアプリをビルドできます。

## コードのダウンロード

このチュートリアルのコードは、[GitHub](https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet) で管理されています。手順に従ってサンプルを構築するには、[スケルトン プロジェクトを .zip ファイルとしてダウンロード](https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet/archive/skeleton.zip)します。スケルトンを複製することもできます。

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet.git
```

また、完成済みのアプリも、[.zip ファイルとして入手する](https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet/archive/complete.zip)か、同じリポジトリの `complete` ブランチで利用できます。

サンプル コードをダウンロードした後、Visual Studio の .sln ファイルを開いて作業を開始します。ソリューション ファイルには、2 つのプロジェクト `TaskWebApp` と `TaskService` が含まれます。`TaskWebApp` は、ユーザーが対話する MVC Web アプリケーションです。`TaskService` は、各ユーザーの To-Do List を格納する、アプリのバックエンド Web API です。

## タスク Web アプリの構成

ユーザーが `TaskWebApp` と対話するとき、クライアントは Azure AD に要求を送信し、`TaskService` Web API の呼び出しに使用できるトークンを取得します。ユーザーをサインインさせてトークンを取得するには、アプリに関する情報を `TaskWebApp` に提供する必要があります。`TaskWebApp` プロジェクトで、プロジェクトのルートにある `web.config` ファイルを開き、`<appSettings>` セクションの値を次の内容に置き換えます。

```
<appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:Tenant" value="{Enter the name of your B2C directory, e.g. contoso.onmicrosoft.com}" />
    <add key="ida:ClientId" value="{Enter the Application ID assigned to your app by the Azure Portal, e.g.580e250c-8f26-49d0-bee8-1c078add1609}" />
    <add key="ida:ClientSecret" value="{Enter the Application Secret you created in the Azure Portal, e.g. yGNYWwypRS4Sj1oYXd0443n}" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}{1}{2}" />
    <add key="ida:RedirectUri" value="https://localhost:44316/" />
    <add key="ida:SignUpPolicyId" value="[Enter your sign up policy name, e.g. b2c_1_sign_up]" />
    <add key="ida:SignInPolicyId" value="[Enter your sign in policy name, e.g. b2c_1_sign_in]" />
    <add key="ida:UserProfilePolicyId" value="[Enter your edit profile policy name, e.g. b2c_1_profile_edit" />
    <add key="api:TaskServiceUrl" value="https://localhost:44332/" />
</appSettings>
```

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

また、サインイン ポリシー名の指定が必要な 2 つの `[PolicyAuthorize]` デコレーターもあります。`[PolicyAuthorize]` 属性は、認証が必要なアプリのページにユーザーがアクセスを試みたときに特定のポリシーを呼び出すために使用されます。

```C#
// Controllers\HomeController.cs

[PolicyAuthorize(Policy = "{Enter the name of your sign in policy, e.g. b2c_1_my_sign_in}")]
public ActionResult Claims()
{
```

```C#
// Controllers\TasksController.cs

[PolicyAuthorize(Policy = "{Enter the name of your sign in policy, e.g. b2c_1_my_sign_in}")]
public class TasksController : Controller
{
```

`TaskWebApp` などの Web アプリが Azure AD B2C を使用する方法については、「[.NET Web アプリを構築する](active-directory-b2c-devquickstarts-web-dotnet.md)」を参照してください。

## API のセキュリティ保護

ユーザーの代わりに API を呼び出すクライアントが用意できたら、OAuth 2.0 ベアラー トークンを使用して `TaskService` をセキュリティ保護できます。API では、Microsoft の Open Web Interface for .NET (OWIN) ライブラリを使用してトークンを受け入れて検証できます。

### OWIN をインストールする
まず、OWIN OAuth 認証パイプラインをインストールします。

```
PM> Install-Package Microsoft.Owin.Security.OAuth -ProjectName TodoListService
PM> Install-Package Microsoft.Owin.Security.Jwt -ProjectName TodoListService
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TodoListService
```

### B2C の詳細情報を入力する
`TaskService` プロジェクトのルートにある `web.config` ファイルを開き、`<appSettings>` セクションの値を置き換えます。これらの値は、API と OWIN ライブラリ全体で使用されます。

```
<appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}/{1}/{2}?p={3}" />
    <add key="ida:Tenant" value="{Enter the name of your B2C tenant - it usually looks like constoso.onmicrosoft.com}" />
    <add key="ida:ClientId" value="{Enter the Application ID assigned to your app by the Azure Portal}" />
    <add key="ida:PolicyId" value="{Enter the name of one of the policies you created, like `b2c_1_my_sign_in_policy`}" />
</appSettings>
```

### OWIN Startup クラスを追加する
OWIN のスタートアップ クラスを `TaskService` プロジェクト (`Startup.cs`) に追加します。プロジェクトを右クリックし、**[追加]**、**[新しいアイテム]** の順に選択して、OWIN を検索します。


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
	public static string commonPolicy = ConfigurationManager.AppSettings["ida:PolicyId"];
	private const string discoverySuffix = ".well-known/openid-configuration";

	public void ConfigureAuth(IAppBuilder app)
	{   
		TokenValidationParameters tvps = new TokenValidationParameters
		{
			// This is where you specify that your API accepts tokens only from its own clients
			ValidAudience = clientId,
		};

		app.UseOAuthBearerAuthentication(new OAuthBearerAuthenticationOptions
		{   
			// This SecurityTokenProvider fetches the Azure AD B2C metadata and signing keys from the OpenID Connect metadata endpoint
			AccessTokenFormat = new JwtFormat(tvps, new OpenIdConnectCachingSecurityTokenProvider(String.Format(aadInstance, tenant, "v2.0", discoverySuffix, commonPolicy)))
		});
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

最後に、`TaskWebApp` と `TaskService` を構築して実行します。電子メール アドレスまたはユーザー名を使用して、アプリにサインアップします。ユーザーの To-Do List にいくつかのタスクを作成すると、クライアントを停止して再起動した後でも、作成したタスクが API に保持されていることを確認できます。

## ポリシーの編集

Azure AD B2C を使用して API をセキュリティ保護した後は、アプリのポリシーでさまざまな設定を試し、その効果 (または不足部分) を API で確認することができます。<!--add **identity providers** to the policies, allowing you users to sign into the Task Client using social accounts.  You can also -->ポリシー内のアプリケーション要求を操作し、Web API で利用できるユーザー情報を変更することができます。追加した要求は、前述のように .NET MVC Web API の `ClaimsPrincipal` オブジェクトで利用できます。

<!--

## Next steps

You can now move onto more advanced B2C topics. You may try:

[Call a web API from a web app]()

[Customize the UX of your B2C app]()

-->

<!---HONumber=AcomDC_0224_2016-->