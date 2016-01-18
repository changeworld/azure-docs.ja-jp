<properties
	pageTitle="Azure AD B2C プレビュー | Microsoft Azure"
	description="Azure AD B2C を使用し、認証に OAuth 2.0 アクセス トークンを使用してセキュリティ保護された .NET Web API を構築する方法。"
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
	
# Azure AD B2C プレビュー: .NET Web API を構築する

<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

Azure AD B2C では、OAuth 2.0 アクセス トークンを使用して Web API をセキュリティで保護しながら、Azure AD B2C を使用するクライアント アプリを API に対して認証することができます。この記事では、ユーザーのサインアップ、サインイン、プロファイル管理などの処理を含む .NET MVC "To-Do List" アプリの作成方法について説明します。各ユーザーの To Do リストは、Task Service (To Do リスト内のタスクの作成や読み取りを認証済みユーザーに許可する Web API) によって保存されます。

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## 1\.Azure AD B2C ディレクトリの取得

Azure AD B2C を使用するには、ディレクトリ (つまり、テナント) を作成しておく必要があります。ディレクトリは、ユーザー、アプリ、グループなどをすべて格納するためのコンテナーです。まだディレクトリを作成していない場合は、先に進む前に [B2C ディレクトリの作成](active-directory-b2c-get-started.md)に関するページを参照してください。

## 2\.アプリケーションの作成

次に、B2C ディレクトリ内にアプリを作成する必要があります。これによって、アプリと安全に通信するために必要ないくつかの情報が Azure AD に提供されます。アプリを作成するには、[こちらの手順](active-directory-b2c-app-registration.md)に従ってください。このとき、

- アプリケーションに **Web アプリまたは Web API** を含めます。
- Web アプリの場合、**リダイレクト URI** の `https://localhost:44316/` を使用します。これは、このコード サンプルでの Web アプリ クライアントの既定の場所です。
- アプリに割り当てられた**アプリケーション ID** をメモしておきます。このプロジェクトはすぐに必要になります。

 [AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## 3\.ポリシーの作成

Azure AD B2C では、すべてのユーザー エクスペリエンスが[**ポリシー**](active-directory-b2c-reference-policies.md)によって定義されます。このコード サンプルのクライアントには、3 つの ID エクスペリエンス (サインアップ、サインイン、プロファイル編集) が含まれています。[ポリシーについてのリファレンス記事](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy)で説明されているように、種類ごとに 1 つのポリシーを作成する必要があります。3 つのポリシーを作成するときは、必ず以下の操作を行ってください。

- ID プロバイダーのブレードで、**[ユーザー ID サインアップ]** または **[電子メール サインアップ]** を選択します。
- サインアップ ポリシーで、**表示名**と他のいくつかのサインアップ属性を選択します。
- すべてのポリシーで、アプリケーション クレームとして **表示名**と**オブジェクト ID** を選択します。その他の要求も選択できます。
- ポリシーの作成後、各ポリシーの**名前**をメモしておきます。これらのポリシー名はすぐに必要になります。 

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

3 つのポリシーの作成が正常に完了したら、いつでもアプリをビルドできます。

## 4\.コードのダウンロード

このチュートリアルのコードは、[GitHub](https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet) で管理されています。手順に従ってサンプルを構築するために、[スケルトン プロジェクトを .zip 形式でダウンロードする](https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet/archive/skeleton.zip)か、次のようにスケルトンを複製できます。

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet.git
```

また、完成済みのアプリも、[.zip 形式で入手する](https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet/archive/complete.zip)か、同じリポジトリの `complete` ブランチで利用できます。

サンプル コードをダウンロードしたら、Visual Studio の `.sln` ファイルを開いて作業を開始します。ソリューションに `TaskWebApp` プロジェクトと `TaskService` プロジェクトの 2 つのプロジェクトがあることがわかります。`TaskWebApp` は、ユーザーと対話する MVC Web アプリケーションです。`TaskService` は、各ユーザーの To-Do リストを格納する、アプリのバックエンド Web API です。

## 5\.タスク Web アプリの構成

ユーザーが `TaskWebApp` と対話する際、クライアントは Azure AD に要求を送信し、`TaskService` Web API の呼び出しに使用できるトークンを受け取ります。ユーザーのサインインとトークンの取得を行うためには、アプリに関する情報を `TaskWebApp` に提供する必要があります。`TaskWebApp` プロジェクトで、プロジェクトのルートにある `web.config` ファイルを開き、`<appSettings>` セクションの値を次の内容に置き換えます。

```
<appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:Tenant" value="{Enter the name of your B2C directory, e.g. contoso.onmicrosoft.com}" />
    <add key="ida:ClientId" value="{Enter the Application Id assigned to your app by the Azure Portal, e.g.580e250c-8f26-49d0-bee8-1c078add1609}" />
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

また、サインイン ポリシー名の指定が必要な 2 つの `[PolicyAuthorize]` デコレータもあります。`[PolicyAuthorize]` 属性は、認証が必要なアプリのページにユーザーがアクセスを試みたときに特定のポリシーを呼び出すために使用されます。

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

`TaskWebApp` のような Web アプリで Azure AD B2C を使用する方法については、[Web アプリ サインインの概要に関する記事](active-directory-b2c-devquickstarts-web-dotnet.md)をご覧ください。

## 6\.API のセキュリティ保護

ユーザーの代わりに API を呼び出すクライアントが用意できたので、OAuth 2.0 ベアラー トークンを使用して `TaskService` をセキュリティで保護することができます。API では、Microsoft の OWIN ライブラリを使用してトークンを受け入れて検証できます。

#### OWIN をインストールする
まず、OWIN OAuth 認証パイプラインをインストールします。

```
PM> Install-Package Microsoft.Owin.Security.OAuth -ProjectName TodoListService
PM> Install-Package Microsoft.Owin.Security.Jwt -ProjectName TodoListService
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TodoListService
```

#### B2C の詳細情報を入力する
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

#### OWIN Startup クラスを追加する
OWIN Startup クラスを `TaskService` プロジェクト (`Startup.cs`) に追加します。プロジェクトを右クリックし、**[追加]**、**[新しいアイテム]** の順にクリックして、"OWIN" を検索します。
  

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

#### OAuth 2.0 認証を構成する
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
			// This is where you specify that your API only accepts tokens from its own clients
			ValidAudience = clientId,
		};

		app.UseOAuthBearerAuthentication(new OAuthBearerAuthenticationOptions
		{   
			// This SecurityTokenProvider fetches the Azure AD B2C metadata & signing keys from the OpenIDConnect metadata endpoint
			AccessTokenFormat = new JwtFormat(tvps, new OpenIdConnectCachingSecurityTokenProvider(String.Format(aadInstance, tenant, "v2.0", discoverySuffix, commonPolicy)))
		});
	}
}
```

#### タスク コントローラーをセキュリティで保護する
OAuth 2.0 認証を使用するようにアプリが構成されたので、Web API をセキュリティで保護するために必要なのは、`[Authorize]` タグをタスク コントローラーに追加することだけです。このコントローラーでは To Do リストのすべての操作が実行されるため、コントローラー全体をクラス レベルでセキュリティ保護します。よりきめ細かく制御するために、`[Authorize]` タグを個々のアクションに追加することもできます。

```C#
// Controllers\TasksController.cs

[Authorize]
public class TasksController : ApiController
{
	...
}
```

#### トークンからユーザー情報を取得する
`TaskController` はデータベースにタスクを格納します。このデータベースでは、各タスクに、そのタスクを "所有" するユーザーが関連付けられています。所有者は、ユーザーの**オブジェクト ID** によって識別されます (すべてのポリシーにアプリケーション クレームとしてオブジェクト ID を追加する必要があったのは、このためです)。

```C#
// Controllers\TasksController.cs

public IEnumerable<Models.Task> Get()
{
	string owner = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
	IEnumerable<Models.Task> userTasks = db.Tasks.Where(t => t.owner == owner);
	return userTasks;
}
```

## 7\.サンプル アプリの実行

最後に、`TaskWebApp` と `TaskService` の両方をビルドして実行します。電子メール アドレスまたはユーザー名を使用して、アプリにサインアップします。ユーザーの To Do リストにいくつかのタスクを作成すると、クライアントを停止して再起動した後でも、作成したタスクが API に保持されていることを確認できます。

## 8\.ポリシーの編集

Azure AD B2C を使用して API をセキュリティで保護したので、アプリのポリシーでさまざまな設定を試し、その効果 (または不足部分) を API で確認することができます。<!--add **identity providers**
to the policies, allowing you users to sign into the Task Client using social accounts.  You can also -->ポリシー内の**アプリケーション クレーム**を操作し、Web API で利用できるユーザー情報を変更することができます。その他のクレームを追加した場合、そのクレームは、前述のように .NET MVC Web API の `ClaimsPrincipal` オブジェクト内で利用できます。

<!--

## Next Steps

You can now move onto more advanced B2C topics.  You may want to try:

[Calling a Web API from a Web App >>]()

[Customizing the your B2C App's UX >>]()

-->

<!---HONumber=AcomDC_0107_2016-->