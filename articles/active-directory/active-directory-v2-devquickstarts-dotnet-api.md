<properties
	pageTitle="アプリ モデル v2.0 &gt;Net Web API| Microsoft Azure"
	description="個人の Microsoft アカウントと職場/学校アカウントの両方からトークンを受け付ける .NET MVC Web API を構築する方法を説明します。"
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
	ms.date="09/11/2015"
	ms.author="dastrock"/>

# アプリ モデル v2.0 プレビュー: MVC Web API をセキュリティで保護する

v2.0 アプリ モデルでは、[OAuth 2.0](active-directory-v2-protocols.md#oauth2-authorization-code-flow) アクセス トークンを使用して Web API を保護でき、ユーザーが個人または職場/学校の Microsoft アカウントの両方を使って Web API に安全にアクセスできるようにすることができます。

> [AZURE.NOTE]この情報は、v2.0 アプリ モデルのパブリック プレビューに関するものです。一般公開されている Azure AD サービスと連携する手順については、「[Azure Active Directory 開発者ガイド](active-directory-developers-guide.md)」を参照してください。

ASP.NET Web API では、.NET Framework 4.5 に含まれる Microsoft の OWIN ミドルウェアを使用することにより、これを達成できます。ここでは、OWIN を使用して、次の機能を持つ "To Do List" MVC Web API を構築します: クライアントがユーザーの To-Do list からタスクを作成、読み取りできるようにする機能、保護対象の API を指定する機能、および Web API 呼び出しが有効なアクセス トークンを含んでいることを検証する機能。

これを行うには、次の手順を実行する必要があります。

1. アプリを Azure AD に登録する
2. OWIN 認証パイプラインを使用するようにアプリをセットアップする
3. To Do List Web API を呼び出すようにクライアント アプリを構成する

このチュートリアルのコードは、[GitHub](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet) で管理されています。追加の参考資料として、[アプリのスケルトン (.zip) をダウンロード](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet/archive/skeleton.zip)したり、スケルトンを複製したりすることができます:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet.git```

完成したアプリは、このチュートリアルの終わりにも示しています。


## 1\.アプリを登録します
[apps.dev.microsoft.com](https://apps.dev.microsoft.com) で新しいアプリを作成するか、この[詳細な手順](active-directory-v2-app-registration.md)に従います。次のことを確認します。

- アプリに割り当てられた**アプリケーション ID** をメモしておきます。これは後で必要になります。

この Visual Studio ソリューションには、単純な WPF アプリである "TodoListClient" も含まれています。TodoListClient は、ユーザーがどのようにサインインし、クライアントが Web API にどのように要求を発行するかを示すために使用されます。ここでは、TodoListClient と TodoListService の両方に同じアプリが使用されています。TodoListClient を構成するために、以下のことも行う必要があります。

- アプリ用の**モバイル** プラットフォームを追加します。
- ポータルから**リダイレクト URI** をメモしておきます。既定値の `urn:ietf:wg:oauth:2.0:oob`を使用する必要があります。


## 2\.アプリで OWIN 認証パイプラインを使用するよう設定します。

アプリの登録が完了したら、受け取った要求やトークンを検証するために、v2.0 エンドポイントと通信するようアプリを設定する必要があります。

-	まず、ソリューションを開き、パッケージ マネージャー コンソールを使用して、OWIN ミドルウェア NuGet パッケージを TodoListService プロジェクトに追加する必要があります。

```
PM> Install-Package Microsoft.Owin.Security.OAuth -ProjectName TodoListService
PM> Install-Package Microsoft.Owin.Security.Jwt -ProjectName TodoListService
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TodoListService
```

-	OWIN Startup クラスを TodoListService プロジェクト (`Startup.cs`) に追加します。プロジェクトを右クリックし、**[追加]**、**[新しいアイテム]** の順にクリックして、"OWIN" を検索します。アプリが起動すると、OWIN ミドルウェアは `Configuration(…)` メソッドを呼び出します。
-	クラス宣言を `public partial class Startup` に変更します。このクラスの一部を別のファイルで既に実装しています。`Configuration(…)` メソッドで、ConfgureAuth(…) を呼び出して、Web アプリ用の認証をセットアップします。

```C#
public partial class Startup
{
    public void Configuration(IAppBuilder app)
    {
        ConfigureAuth(app);
    }
}
```

-	ファイル `App_Start\Startup.Auth.cs` を開き、`ConfigureAuth(…)` メソッドを実装します。これにより Web API を v2.0 エンドポイントからのトークンを受け入れるように設定できます。

```C#
public void ConfigureAuth(IAppBuilder app)
{
		var tvps = new TokenValidationParameters
		{
				// In this app, the TodoListClient and TodoListService
				// are represented using the same Application Id - we use
				// the Application Id to represent the audience, or the
				// intended recipient of tokens.

				ValidAudience = clientId,

				// In a real applicaiton, you might use issuer validation to
				// verify that the user's organization (if applicable) has
				// signed up for the app.  Here, we'll just turn it off.

				ValidateIssuer = false,
		};

		// Set up the OWIN pipeline to use OAuth 2.0 Bearer authentication.
		// The options provided here tell the middleware about the type of tokens
		// that will be recieved, which are JWTs for the v2.0 endpoint.

		// NOTE: The usual WindowsAzureActiveDirectoryBearerAuthenticaitonMiddleware uses a
		// metadata endpoint which is not supported by the v2.0 endpoint.  Instead, this
		// OpenIdConenctCachingSecurityTokenProvider can be used to fetch & use the OpenIdConnect
		// metadata document.

		app.UseOAuthBearerAuthentication(new OAuthBearerAuthenticationOptions
		{
				AccessTokenFormat = new Microsoft.Owin.Security.Jwt.JwtFormat(tvps, new OpenIdConnectCachingSecurityTokenProvider("https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration")),
		});
}
```

-	次に、`[Authorize]` 属性と OAuth 2.0 ベアラー認証を使用して、コントローラーとアクションを保護します。認証タグを使用して、`Controllers\TodoListController.cs` クラスを修飾します。これにより、ユーザーはそのページにアクセスする前に、サインインが必要になります。

```C#
[Authorize]
public class TodoListController : ApiController
{
```

- 承認呼び出し元が `TodoListController` API の 1 つを正常に呼び出すときに、呼び出し元についての情報にアクセスするアクションが必要な場合があります。OWIN は `ClaimsPrincpal` オブジェクトを通して、ベアラー トークン内のクレームにアクセスできるようにします。  

```C#
public IEnumerable<TodoItem> Get()
{
    // You can use the ClaimsPrincipal to access information about the
    // user making the call.  In this case, we use the 'sub' or
    // NameIdentifier claim to serve as a key for the tasks in the data store.

    Claim subject = ClaimsPrincipal.Current.FindFirst(ClaimTypes.NameIdentifier);

    return from todo in todoBag
           where todo.Owner == subject.Value
           select todo;
}
```

-	最後に、TodoListService プロジェクトのルートにある `web.config` ファイルを開いて、構成値を `<appSettings>` セクションで入力します。
  -	`ida:Audience` は、ポータルで入力したアプリの**アプリケーション ID** です。

## 3\.クライアント アプリを構成し、サービスを実行する
Todo List Service の動作を確認できるようにするには、Todo List Client を構成して、Todo List Client が v2.0 エンドポイントからトークンを取得し、サービスを呼び出せるようにする必要があります。

- TodoListClient プロジェクトで `App.config` を開いて、`<appSettings>` セクションに構成値を入力します。
  -	`ida:ClientId` は、ポータルからコピーしたアプリケーション ID です。
	- `ida:RedirectUri` は、ポータルの**リダイレクト URI** です。

最後に、各プロジェクトをクリーンアップし、ビルドして、実行します。 これで、.NET MVC Web API で個人の Microsoft アカウントと職場/学校アカウントの両方からトークンが受け付けられるようになりました。TodoListClient にサインインし、Web API を呼び出して、タスクをユーザーの To-Do リストに追加します。

参照用に、完成したサンプル (構成値を除く) が[ここに .zip として提供されています](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet/archive/complete.zip)。または、GitHub から複製することもできます。

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet.git```

## 次のステップ
ここからは、さらなるトピックに進むことができます。次のチュートリアルを試してみてください。

[v2.0 アプリ モデルで Web アプリから Web API を呼び出す >>](active-directory-devquickstarts-webapp-webapi-dotnet.md)

その他のリソースについては、以下を参照してください。 - [アプリ モデル v2.0 プレビュー >>](active-directory-appmodel-v2-overview.md) - [StackOverflow "azure-active-directory" タグ >>](http://stackoverflow.com/questions/tagged/azure-active-directory)

<!---HONumber=Oct15_HO3-->