<properties
	pageTitle="Mobile Apps 用 .NET バックエンド サーバー SDK を操作する方法 | Azure App Service"
	description="Azure App Service Mobile Apps 用の .NET バックエンド サーバー SDK を操作する方法について説明します。"
	keywords="App Service, Azure App Service, モバイル アプリ, スケール, スケーラブル, アプリのデプロイ, Azure App のデプロイ"
	services="app-service\mobile"
	documentationCenter=""
	authors="ggailey777"
	manager="erikre"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-multiple"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="03/06/2016"
	ms.author="glenga"/>

# Azure Mobile Apps 用 .NET バックエンド サーバー SDK の操作

[AZURE.INCLUDE [app-service-mobile-selector-server-sdk](../../includes/app-service-mobile-selector-server-sdk.md)]

このトピックでは、Azure App Service Mobile Apps の主要なシナリオで .NET バックエンド サーバー SDK を使用する方法について説明します。Azure Mobile Apps SDK を使用すると、ASP.NET アプリケーションからモバイル クライアントを操作することができます。

>[AZURE.TIP] [Azure Mobile Apps 向け .NET サーバー SDK](https://github.com/Azure/azure-mobile-apps-net-server) は、オープン ソースとして GitHub で公開されています。リポジトリには、いくつかのサンプル プロジェクトと同様に、サーバー全体の SDK ユニット テスト スイートが含まれています。

## リファレンス ドキュメント

サーバー SDK のリファレンス ドキュメントについては、「[Azure Mobile Apps .NET リファレンス](https://msdn.microsoft.com/library/azure/dn961176.aspx)」を参照してください。

## <a name="create-app"></a>方法: モバイル アプリケーション用の .NET バックエンドを作成する

新しいプロジェクトを開始する場合は、[Azure ポータル]または Visual Studio を使用して、App Service アプリケーションを作成できます。このセクションでは、このいずれかを使用して、シンプルな To Do List API をホストするモバイル アプリケーション バックエンドを新規作成する方法を紹介します。これをローカルで実行することも、プロジェクトをクラウド ベースの App Service モバイル アプリに発行することもできます。

既存のプロジェクトにモバイル機能を追加する場合は、「[SDK をダウンロードして初期化する](#install-sdk)」セクションを参照してください。

### Azure ポータルで .NET バックエンドを作成する

[Azure ポータル]でモバイル アプリケーションを新規作成できます。以下の手順を実行するか、チュートリアル「[モバイル アプリを作成する](app-service-mobile-ios-get-started.md)」の手順でクライアントとサーバーをまとめて新規作成することもできます。

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

&nbsp;&nbsp;9.**[テーブル API の作成]** の _[はじめに]_ ブレードに戻り、**[バックエンド言語]** として **[C#]** を選択します。

&nbsp;&nbsp;10.[ダウンロード] をクリックし、圧縮されたプロジェクト ファイルをローカル コンピューターに抽出して、Visual Studio でソリューションを開きます。

### Visual Studio 2013 または Visual Studio 2015 を使用して .NET バックエンドを作成する

Visual Studio で Mobile Apps プロジェクトを作成するには、[Azure SDK for .NET](https://azure.microsoft.com/downloads/) バージョン 2.8.1 以降をインストールする必要があります。SDK のインストールが完了したら、ASP.NET アプリケーションを新規作成します。

1. **[新しいプロジェクト]** ダイアログを開きます (*[ファイル]*、**[新規作成]**、**[プロジェクト...]** の順にクリックします)。

2. **[テンプレート]**、**[Visual C#]** の順に展開し、**[Web]** を選択します。

3. **[ASP.NET Web アプリケーション]** を選択します。

4. プロジェクト名を入力します。次に、 **[OK]** をクリックします

5. _ASP.NET 4.5.2 テンプレート_の **[Azure Mobile App]** を選択します。このプロジェクトを発行できるクラウドに新しいモバイル アプリを作成するために、**[クラウドでホストする]** チェック ボックスをオンにします。

6. **[OK]** をクリックします。アプリケーションが作成され、ソリューション エクスプローラーに表示されます。

## <a name="install-sdk"></a>方法: SDK をダウンロードして初期化する

SDK は [NuGet.org] で入手できます。このパッケージには、SDK の使用を開始するために必要な基礎機能が含まれています。SDK を初期化するには、**HttpConfiguration** オブジェクトに対して操作を実行する必要があります。

###SDK のインストール

SDK をインストールするには、Visual Studio でサーバー プロジェクトを右クリックして **[NuGet パッケージの管理]** を選択し、[Microsoft.Azure.Mobile.Server](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) パッケージを検索してから、**[インストール]** をクリックします。

###<a name="server-project-setup"></a> サーバー プロジェクトの初期化

.NET バックエンド サーバー プロジェクトは、他の ASP.NET プロジェクトと同じように、OWIN スタートアップ クラスを組み込むことによって初期化します。NuGet パッケージ `Microsoft.Owin.Host.SystemWeb` が参照されていることを確認します。Visual Studio でこのクラスを追加するには、サーバー プロジェクトを右クリックして、**[追加]**、**[新しい項目]**、**[Web]**、**[全般]**、**[OWIN スタートアップ クラス]** の順に選択します。

これにより、次の属性を持つクラスが生成されます。

    [assembly: OwinStartup(typeof(YourServiceName.YourStartupClassName))]

OWIN スタートアップ クラスの `Configuration()` メソッドで、サービスの構成オプションを表す **HttpConfiguration** オブジェクトを使用してサーバー プロジェクトをセットアップします。次の例では、機能を追加せずにサーバー プロジェクトを初期化します。

	// in OWIN startup class
	public void Configuration(IAppBuilder app)
	{
	    HttpConfiguration config = new HttpConfiguration();

	    new MobileAppConfiguration()
	        // no added features
	        .ApplyTo(config);

	    app.UseWebApi(config);
	}

個別の機能を有効化するには、**ApplyTo** を呼び出す前に、**MobileAppConfiguration** オブジェクトに対して拡張機能メソッドを呼び出す必要があります。たとえば、次のコードでは、初期化中に `[MobileAppController]` 属性が設定されているすべての API コントローラーに既定のルートを追加します。

	new MobileAppConfiguration()
	    .MapApiControllers()
	    .ApplyTo(config);

`MapApiControllers` は、`[MobileAppController]` 属性が設定されているコントローラーのみマップすることに注意してください。

機能拡張メソッドの多くは、含めることが可能な追加の NuGet パッケージから入手できます。これらについては以下のセクションで説明します。

Azure ポータルからのサーバーのクイックスタートは **UseDefaultConfiguration()** を呼び出します。これは次のセットアップと同等です。

		new MobileAppConfiguration()
			.AddMobileAppHomeController()             // from the Home package
			.MapApiControllers()
			.AddTables(                               // from the Tables package
				new MobileAppTableConfiguration()
					.MapTableControllers()
					.AddEntityFramework()             // from the Entity package
				)
			.AddPushNotifications()                   // from the Notifications package
			.MapLegacyCrossDomainController()         // from the CrossDomain package
			.ApplyTo(config);


### SDK 拡張機能

次の NuGet ベースの拡張機能パッケージでは、アプリケーションで使用可能な各種モバイル機能が提供されます。拡張機能の有効化は、初期化中に **MobileAppConfiguration** オブジェクトを使用して行います。

- [Microsoft.Azure.Mobile.Server.Quickstart] Mobile Apps の基本的なセットアップをサポートします。構成に追加するには、初期化中に **UseDefaultConfiguration** 拡張機能メソッドを呼び出します。この拡張機能には、Notifications、Authentication、Entity、Tables、Crossdomain、および Home パッケージが含まれています。これは、Azure ポータル からダウンロードするクイックスタート サーバー プロジェクトに相当します。

- [Microsoft.Azure.Mobile.Server.Home](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Home/) Web サイトのルートに、*このモバイル アプリが実行される既定のページ*を実装します。構成に追加するには、**AddMobileAppHomeController** 拡張メソッドを呼び出します。

- [Microsoft.Azure.Mobile.Server.Tables](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Tables/) データ処理のためのクラスが含まれており、データ パイプラインを設定します。構成に追加するには、**AddTables** 拡張メソッドを呼び出します。

- [Microsoft.Azure.Mobile.Server.Entity](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Entity/) Entity Framework で SQL Database のデータにアクセスできるようにします。構成に追加するには、**AddTablesWithEntityFramework** 拡張メソッドを呼び出します。

- [Microsoft.Azure.Mobile.Server.Authentication] 認証を有効にし、トークンの検証に使用する OWIN ミドルウェアを設定します。構成に追加するには、**AddAppServiceAuthentication** 拡張メソッドと **IAppBuilder**.**UseAppServiceAuthentication** 拡張メソッドを呼び出します。

- [Microsoft.Azure.Mobile.Server.Notifications] プッシュ通知を有効にし、プッシュ登録エンドポイントを定義します。構成に追加するには、**AddPushNotifications** 拡張メソッドを呼び出します。

- [Microsoft.Azure.Mobile.Server.CrossDomain](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.CrossDomain/) モバイル アプリから従来の Web ブラウザーにデータを提供するコントローラーを作成します。構成に追加するには、**MapLegacyCrossDomainController** 拡張メソッドを呼び出します。

- [Microsoft.Azure.Mobile.Server.Login] では、AppServiceLoginHandler.CreateToken() メソッドを使用したカスタム認証のプレビュー サポートを提供します。これは静的メソッドであり、この構成で有効にする必要はありません。

## <a name="publish-server-project"></a>方法: サーバー プロジェクトを発行する

このセクションでは、Visual Studio から .NET バックエンド プロジェクトを発行する方法を示します。[Azure App Service のデプロイに関するドキュメント](../app-service-web/web-sites-deploy.md)に記載されている Git などの方法を使用して、バックエンド プロジェクトをデプロイすることもできます。

1. Visual Studio でプロジェクトをリビルドして、NuGet パッケージを復元します。

2. ソリューション エクスプローラーで目的のプロジェクトを右クリックし、**[発行]** をクリックします。初めて発行するときには、発行プロファイルを定義する必要があります。既に定義したプロファイルがある場合は、単にそれを選択し、**[発行]** をクリックします。

2. 発行先の選択を求められた場合は、**[Microsoft Azure App Service]**、**[次へ]** の順にクリックして、必要であれば Azure の資格情報でサインインします。Visual Studio によって Azure から発行設定が直接ダウンロードされ、安全に保存されます。

	![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-1.png)

3. **サブスクリプション**を選択し、**[表示]** で **[リソースの種類]** を選択して、**[モバイル アプリ]** を展開します。次に、モバイル アプリ バックエンドをクリックし、**[OK]** をクリックします。

	![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-2.png)

4. 発行プロファイル情報を確認し、**[発行]** をクリックします。

	![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-3.png)

	モバイル アプリ バックエンドが正常に発行されると、そのことを示すランディング ページが表示されます。

	![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-success.png)

##<a name="define-table-controller"></a> 方法: テーブル コントローラーを定義する

テーブル コントローラーは、テーブル ベースのデータ ストア (SQL Database や Azure Table Storage など) にあるエンティティ データへのアクセスを提供します。テーブル コントローラーは、次のように **TableController** ジェネリック クラスから継承します。このクラスでは、ジェネリック型はテーブル スキーマを表すモデルのエンティティです。

	public class TodoItemController : TableController<TodoItem>
    {
		//...
	}

テーブル コントローラーは、**AddTables** 拡張メソッドを使用して初期化します。これにより、`TableController` のすべてのサブクラスが `/tables/` の下に追加されます。

次の例では、データ アクセスに Entity Framework を使用するテーブル コントローラーを初期化します。

    new MobileAppConfiguration().AddTables(
        new MobileAppTableConfiguration()
        .MapTableControllers()
        .AddEntityFramework()).ApplyTo(config);

Azure SQL Database のデータへのアクセスに Entity Framework を使用するテーブル コントローラーの例については、Azure ポータル からダウンロードしたクイックスタート サーバー プロジェクトの **TodoItemController** クラスを参照してください。

### 方法: テーブルのページング サイズを調整する

既定では、Azure Mobile Apps は、要求ごとに 50 個のレコードを返します。これにより、クライアントが長期間 UI スレッドまたはサーバーを占有することがなくなるため、優れたユーザー エクスペリエンスが保証されます。テーブルのページング サイズの変更を有効にするには、サーバー側の "許可されているクエリ サイズ" とクライアント側のページ サイズを大きくする必要があります。ページング サイズを大きくするには、次の行を使用してテーブル コントローラーを調整します。

    [EnableQuery(PageSize = 500)]

PageSize が、クライアントから要求されるサイズ以上になるようにしてください。クライアントのページ サイズを変更する方法の詳細については、特定のクライアントのハウツー ドキュメントを参照してください。

## 方法: カスタム API コントローラーを定義する

カスタム API コントローラーは、エンドポイントを公開して、モバイル アプリのバックエンドに最も基本的な機能を提供します。[MobileAppController] 属性を使用して、モバイル固有の API コント ローラーを登録できます。この属性は、ルートを登録し、Mobile Apps JSON シリアライザーも設定します。

1. Visual Studio で、Controllers フォルダーを右クリックして、**[追加]**、**[コントローラー]** の順にクリックし、**[Web API 2 コントローラー - 空]** を選択して **[追加]** をクリックします。

2. **コントローラー名** (`CustomController` など) を指定し、**[追加]** をクリックします。これで、**ApiController** から継承した新しい **CustomController** クラスが作成されます。

3. 新しいコントローラーのクラス ファイルに、次の using ステートメントを追加します。

		using Microsoft.Azure.Mobile.Server.Config;

4. 次の例に従って、API コントローラーのクラス定義に **[MobileAppController]** 属性を適用します。

		[MobileAppController]
		public class CustomController : ApiController
		{
		      //...
		}

4. 次の例に示すように、App\_Start/Startup.MobileApp.cs ファイルに **MapApiControllers** 拡張メソッドの呼び出しを追加します。

		new MobileAppConfiguration()
		    .MapApiControllers()
		    .ApplyTo(config);

	代わりに、すべての機能を初期化する **UseDefaultConfiguration** を呼び出す場合は、**MapApiControllers** を呼び出す必要はありません。

クライアントは **MobileAppControllerAttribute** が適用されていないコントローラーにもアクセスできますが、こうしたコントローラーはモバイル アプリ クライアント SDK を使用するクライアントでは適切に使用されない場合があります。

## 方法: 認証を操作する

Mobile Apps は、アプリの認証を有効化する処理を簡単にするために、App Service 認証と ASP.NET の機能を使用します。このセクションでは、.NET バックエンド サーバー プロジェクトで以下の認証関連のタスクを実行する方法を説明します。

+ [方法: サーバー プロジェクトに認証を追加する](#add-auth)
+ [方法: アプリケーションにカスタム認証を使用する](#custom-auth)
+ [方法: 認証されたユーザー情報を取得する](#user-info)
+ [方法: 承認されたユーザーに対するデータ アクセスを制限する](#authorize)

### <a name="add-auth"></a>方法: サーバー プロジェクトに認証を追加する

**MobileAppConfiguration** オブジェクトを拡張し、OWIN ミドルウェアを構成すると、サーバー プロジェクトに認証を追加することができます。[Microsoft.Azure.Mobile.Server.Quickstart] パッケージをインストールし、**UseDefaultConfiguration** 拡張メソッドを呼び出している場合は、手順 3 に進むことができます。

1. Visual Studio で、[Microsoft.Azure.Mobile.Server.Authentication] パッケージをインストールします。

2. Startup.cs プロジェクト ファイルで、**Configuration** メソッドの先頭に次のコード行を追加します。

		app.UseAppServiceAuthentication(config);

	これにより、関連付けられた App Service ゲートウェイで発行されたトークンを Azure モバイル アプリが検証できるようにする、OWIN ミドルウェア コンポーネントが追加されます。

3. 認証が必要なすべてのコントローラーまたはメソッドに `[Authorize]` 属性を追加します。これで、ユーザーはこのエンドポイントや特定の API にアクセスするときに認証が必須になりました。

Mobile Apps バックエンドに対してクライアントを認証する方法については、「[アプリケーションに認証を追加する](app-service-mobile-ios-get-started-users.md)」をご覧ください。

### <a name="custom-auth"></a>方法: アプリケーションにカスタム認証を使用する

App Service Authentication/Authorization プロバイダーの中に使用したいものがない場合は、自身でログイン システムを用意することもできます。これを行うには、[Microsoft.Azure.Mobile.Server.Login] パッケージをインストールします。

ユーザーをサインインするかどうかを判断するための独自のロジックを提供する必要があります。たとえば、データベース内のソルトを使用してハッシュ化されたパスワードと照合することができます。次の例では、`isValidAssertion()` メソッドがこれらの照合を実行します。このメソッドは他の場所で定義されています。

カスタム認証を公開するには、ApiController を新規作成し、以下のような登録とログインのアクションを公開します。クライアントは、ユーザーから関連情報を収集し、本文にユーザー情報を含めた HTTPS POST を API に送信することでログインを試行できます。サーバーでアサーションを検証したら、`AppServiceLoginHandler.CreateToken()` メソッドを使用してトークンを発行できます。

ログイン アクションの例は次のようになります。

		public IHttpActionResult Post([FromBody] JObject assertion)
		{
			if (isValidAssertion(assertion)) // user-defined function, checks against a database
			{
				JwtSecurityToken token = AppServiceLoginHandler.CreateToken(new Claim[] { new Claim(JwtRegisteredClaimNames.Sub, assertion["username"]) },
					mySigningKey,
					myAppURL,
					myAppURL,
					TimeSpan.FromHours(24) );
				return Ok(new LoginResult()
				{
					AuthenticationToken = token.RawData,
					User = new LoginResultUser() { UserId = userName.ToString() }
				});
			}
			else // user assertion was not valid
			{
				return this.Request.CreateUnauthorizedResponse();
			}
		}

上記では、LoginResult と LoginResultUser は、プロパティを公開するだけのシンプルなオブジェクトです。クライアントでは、ログインの応答が次の形式の JSON オブジェクトとして返されるものと想定されています。

		{
			"authenticationToken": "<token>",
			"user": {
				"userId": "<userId>"
			}
		}

`AppServiceLoginHandler.CreateToken()` メソッドには、_audience_ パラメーターと _issuer_ パラメーターが含まれています。どちらも通常は、HTTPS スキームを使用して、アプリケーション ルートの URL に設定します。同様に、_secretKey_ をアプリケーションの署名キーの値に設定する必要があります。これは機密性の高い値なので、共有したりクライアントに保存したりしないでください。この値を取得するには、App Service でホストされているときに _WEBSITE\_AUTH\_SIGNING\_KEY_ 環境変数を参照します。ローカル デバッグの実行で必要な場合は、「[認証に関するローカル デバッグ](#local-debug)」セクションの手順に従ってキーを取得し、アプリケーション設定として保存します。

また、発行されたトークンの有効期間と、含めたい要求を指定する必要があります。サンプル コードに示すとおり、サブジェクトの要求を指定する必要があります。

また、手動の HTTP POST の代わりに、`loginAsync()` メソッド (プラットフォームによって名前が異なる場合があります) を使用するようにクライアント コードを簡略化できます。POST するアサーション オブジェクトに関連付けられている、追加のトークン パラメーターを受け取るオーバーロードを使用します。この場合、プロバイダーは独自のカスタム名にする必要があります。その後、サーバーで、このカスタム名を含む _/.auth/login/{customProviderName}_ パスにログイン アクションを配置する必要があります。このパスにコントローラーを配置するには、MobileAppConfiguration を適用する前に、HttpConfiguration にルートを追加します。

		config.Routes.MapHttpRoute("CustomAuth", ".auth/login/CustomAuth", new { controller = "CustomAuth" });

上の文字列 "CustomAuth" をログイン操作をホストしているコントローラーの名前に置き換えます。

>[AZURE.TIP] loginAsync() アプローチを使用することで、サービスの後続の呼び出しごとに認証トークンが添付されます。

###<a name="user-info"></a>方法: 認証されたユーザー情報を取得する

ユーザーが App Service によって認証されると、.NET バックエンド コードで、割り当てられたユーザー ID とその他の情報にアクセスできます。これは、バックエンドで特定のユーザーに対する承認を判断する場合に便利です。たとえば、特定のユーザーがテーブル行やその他のリソースにアクセスできるかどうかの判断などです。次のコードは、ログインしたユーザーのユーザー ID を取得する方法を示しています。

    // Get the SID of the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;

SID はプロバイダー固有のユーザー ID から派生し、特定のユーザーとログイン プロバイダーに対して静的です。

App Service では、ログイン プロバイダーからの特定の要求を行うこともできます。これにより、Facebook Graph API を使用したりして、プロバイダーからの詳細情報を要求することができます。ポータルのプロバイダー ブレードで、要求を指定できます。いくつかの要求では、プロバイダーの追加の構成が必要です。

次のコードは、**GetAppServiceIdentityAsync** 拡張メソッドを呼び出して、ログイン資格情報を取得します。この情報には、Facebook Graph API に対する要求を行うために必要なアクセス トークンが含まれています。

    // Get the credentials for the logged-in user.
    var credentials =
        await this.User
        .GetAppServiceIdentityAsync<FacebookCredentials>(this.Request);

    if (credentials.Provider == "Facebook")
    {
        // Create a query string with the Facebook access token.
        var fbRequestUrl = "https://graph.facebook.com/me/feed?access_token="
            + credentials.AccessToken;

        // Create an HttpClient request.
        var client = new System.Net.Http.HttpClient();

        // Request the current user info from Facebook.
        var resp = await client.GetAsync(fbRequestUrl);
        resp.EnsureSuccessStatusCode();

        // Do something here with the Facebook user information.
        var fbInfo = await resp.Content.ReadAsStringAsync();
    }

**GetAppServiceIdentityAsync** 拡張メソッドを動作させるには、`System.Security.Principal` の using ステートメントを追加する必要があります。

## 方法: サーバー プロジェクトにプロジェクトを追加する

**MobileAppConfiguration** オブジェクトを拡張し、Notification Hubs クライアントを作成すると、サーバー プロジェクトにプッシュ通知を追加することができます。[Microsoft.Azure.Mobile.Server.Quickstart] パッケージをインストールし、**UseDefaultConfiguration** 拡張メソッドを呼び出している場合は、手順 3 に進むことができます。

1. Visual Studio で、サーバー プロジェクトを右クリックして **[NuGet パッケージの管理]** をクリックし、Microsoft.Azure.Mobile.Server.Notifications を検索して、**[インストール]** をクリックします。これにより、[Microsoft.Azure.Mobile.Server.Notifications] パッケージがインストールされます。

3. 上記の手順を繰り返して、Notification Hubs クライアント ライブラリが含まれる `Microsoft.Azure.NotificationHubs` パッケージをインストールします。

2. App\_Start/Startup.MobileApp.cs で、次に示すように、初期化中に **AddPushNotifications** 拡張メソッドの呼び出しを追加します。

		new MobileAppConfiguration()
			// other features...
			.AddPushNotifications()
			.ApplyTo(config);

	これにより、サーバー プロジェクトにプッシュ通知の登録エンドポイントが作成されます。このエンドポイントは、関連する通知ハブにクライアントが登録を行う際に使用されます。ここでは、通知の送信に使用される Notification Hub クライアントを追加する必要があります。

3. プッシュ通知の送信元にするコントローラーで、次の using ステートメントを追加します。

		using System.Collections.Generic;
		using Microsoft.Azure.NotificationHubs;

4. Notification Hubs クライアントを作成する次のコードを追加します。

        // Get the settings for the server project.
        HttpConfiguration config = this.Configuration;
        MobileAppSettingsDictionary settings =
            config.GetMobileAppSettingsProvider().GetMobileAppSettings();

        // Get the Notification Hubs credentials for the Mobile App.
        string notificationHubName = settings.NotificationHubName;
        string notificationHubConnection = settings
            .Connections[MobileAppSettingsKeys.NotificationHubConnectionString].ConnectionString;

        // Create a new Notification Hub client.
        NotificationHubClient hub = NotificationHubClient
        .CreateClientFromConnectionString(notificationHubConnection, notificationHubName);

これで、Notification Hubs クライアントを使用して、登録済みデバイスにプッシュ通知を送信できるようになりました。詳細については、「[アプリケーションにプッシュ通知を追加する](app-service-mobile-ios-get-started-push.md)」をご覧ください。Notification Hubs で実行可能なすべての操作については、「[Azure 通知ハブ](../notification-hubs/notification-hubs-overview.md)」をご覧ください。

##<a name="tags"></a>方法: ターゲットを絞ったプッシュを有効にするために、タグをデバイス インストールに追加する

Notification Hubs では、タグを使用して、ターゲットを絞った通知を特定の登録に送信できます。1 つのタグが自動的に作成されます。そのタグは、特定のデバイス上のアプリのインスタンスに固有のインストール ID です。インストール ID を使用した登録も*インストール*と呼ばれます。インストール ID を使用して、タグの追加など、インストールの管理操作を実行できます。インストール ID には、**MobileServiceClient** の **installationId** プロパティからアクセスできます。

次の例では、Notification Hubs でインストール ID を使用して特定のインストールにタグを追加する方法を示します。

	hub.PatchInstallation("my-installation-id", new[]
	{
	    new PartialUpdateOperation
	    {
	        Operation = UpdateOperationType.Add,
	        Path = "/tags",
	        Value = "{my-tag}"
	    }
	});

インストールを作成するときは、プッシュ通知の登録時にクライアントから提供されたタグはすべてバックエンドでは無視されることに注意してください。クライアントがインストールにタグを追加できるようにするには、上記のパターンを使用してタグを追加する新しいカスタム API を作成する必要があります。クライアントがインストールにタグを追加できるようにするカスタム API コントローラーの例については、「App Service Mobile Apps completed quickstart sample for .NET backend (.NET バックエンド向けの App Service Mobile Apps の完成したクイックスタート サンプル)」の「[Client-added push notification tags (クライアントが追加したプッシュ通知タグ)](https://github.com/Azure-Samples/app-service-mobile-dotnet-backend-quickstart/blob/master/README.md#client-added-push-notification-tags)」を参照してください。

##<a name="push-user"></a>方法: 認証されたユーザーにプッシュ通知を送信する

認証されたユーザーがプッシュ通知を登録すると、ユーザー ID タグが登録に自動的に追加されます。このタグを使用すると、特定のユーザーが登録したすべてのデバイスにプッシュ通知を送信できます。次のコードでは、要求を行ったユーザーの SID を取得し、そのユーザーのすべてのデバイス登録にテンプレート プッシュ通知を送信します。

    // Get the current user SID and create a tag for the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;
    string userTag = "_UserId:" + sid;

    // Build a dictionary for the template with the item message text.
    var notification = new Dictionary<string, string> { { "message", item.Text } };

    // Send a template notification to the user ID.
    await hub.SendTemplateNotificationAsync(notification, userTag);

認証されたクライアントからプッシュ通知を登録する場合は、登録を試みる前に、認証が完了していることを確認します。詳細については、「App Service Mobile Apps completed quickstart sample for .NET backend (.NET バックエンド向けの App Service Mobile Apps の完成したクイックスタート サンプル)」の「[Push to users (ユーザーへのプッシュ)](https://github.com/Azure-Samples/app-service-mobile-dotnet-backend-quickstart/blob/master/README.md#push-to-users)」を参照してください。

## 方法: .NET サーバー SDK のデバッグとトラブルシューティングを実行する

Azure App Service には、ASP.NET アプリケーションのデバッグとトラブルシューティングの方法が複数用意されています。

- [Azure App Service の監視](../app-service-web/web-sites-monitor.md)
- [Azure App Service での診断ログの有効化](../app-service-web/web-sites-enable-diagnostic-log.md)
- [Visual Studio での Azure App Service のトラブルシューティング](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md)

### ログの記録

標準の ASP.NET トレース書き込みを使用して、App Service の診断ログを作成できます。ログに書き込めるようにするには、モバイル アプリ バックエンドで診断を有効にする必要があります。

診断を有効にして、ログに書き込むには、次の手順を実行します。

1. 「[診断を有効にする方法](../app-service-web/web-sites-enable-diagnostic-log.md#enablediag)」の手順に従います。

2. 次の using ステートメントをコード ファイルに追加します。

		using System.Web.Http.Tracing;

3. .NET バックエンドから診断ログに書き込むために、次のようにトレース ライターを作成します。

		ITraceWriter traceWriter = this.Configuration.Services.GetTraceWriter();
		traceWriter.Info("Hello, World");

4. サーバー プロジェクトを再発行し、モバイル アプリ バックエンドにアクセスして、ログ記録付きでコード パスを実行します。

5. 「[ログをダウンロードする方法](../app-service-web/web-sites-enable-diagnostic-log.md#download)」で説明されているように、ログをダウンロードして評価します。

### <a name="local-debug"></a>認証に関するローカル デバッグ

クラウドに発行する前に変更をテストするために、ローカルでアプリケーションを実行できます。多くのアプリは、Visual Studio で *F5* キーを押すだけで実行できます。ただし、認証を使用している場合は追加の考慮事項がいくつかあります。

App Service Authentication/Authorization を使用してクラウド ベースのモバイル アプリを構成する必要があります。また、クライアントに代替ログイン ホストとしてクラウド エンドポイントを指定する必要があります。必要な手順の詳細については、選択したクライアント プラットフォーム ([iOS](app-service-mobile-ios-how-to-use-client-library.md)、[Windows/Xamarin](app-service-mobile-dotnet-how-to-use-client-library.md)) のドキュメントを参照してください。

アプリケーションに [Microsoft.Azure.Mobile.Server.Authentication] がインストールされていることを確認します。次に、`MobileAppConfiguration` が `HttpConfiguration` に適用された後で、アプリケーションの OWIN Startup クラスに次のコードを追加します。

		app.UseAppServiceAuthentication(new AppServiceAuthenticationOptions()
		{
			SigningKey = ConfigurationManager.AppSettings["authSigningKey"],
			ValidAudiences = new[] { ConfigurationManager.AppSettings["authAudience"] },
			ValidIssuers = new[] { ConfigurationManager.AppSettings["authIssuer"] },
			TokenHandler = config.GetMobileAppTokenHandler()
		});

上記の例では、Web.config ファイル内のアプリケーション設定 _authAudience_ と _authIssuer_ を、HTTPS スキームを使用して、それぞれアプリケーション ルートの URL に構成する必要があります。同様に、_authSigningKey_ をアプリケーションの署名キーの値に設定する必要があります。これは機密性の高い値なので、共有したりクライアントに保存したりしないでください。この値を取得するには、[Azure ポータル]でアプリに移動し、**[ツール]** をクリックします。**[Kudu]** を選択し、**[移動]** をクリックします。この操作により、サイトの Kudu 管理エンドポイントに移動します。**[環境]** をクリックし、_WEBSITE\_AUTH\_SIGNING\_KEY_ で値を確認します。これがローカル アプリ構成の _authSigningKey_ で使用する必要がある値です。

ローカルで実行されているサーバーで、クライアントがクラウド ベースのエンドポイントから取得するトークンを検証できるようになりました。


[Azure ポータル]: https://portal.azure.com
[NuGet.org]: http://www.nuget.org/
[Microsoft.Azure.Mobile.Server.Quickstart]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Quickstart/
[Microsoft.Azure.Mobile.Server.Authentication]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Authentication/
[Microsoft.Azure.Mobile.Server.Login]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Login/
[Microsoft.Azure.Mobile.Server.Notifications]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Notifications/

<!---HONumber=AcomDC_0525_2016-->