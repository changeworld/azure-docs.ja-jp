<properties
	pageTitle="Mobile Apps 用 .NET バックエンド サーバー SDK を操作する方法 | Azure App Service"
	description="Azure App Service Mobile Apps 用の .NET バックエンド サーバー SDK を操作する方法について説明します。"
	services="app-service\mobile"
	documentationCenter=""
	authors="ggailey777" 
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-multiple"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="09/18/2015"
	ms.author="glenga"/>

# Azure Mobile Apps 用 .NET バックエンド サーバー SDK の操作

このトピックでは、Azure App Service Mobile Apps の主要なシナリオで .NET バックエンド サーバー SDK を使用する方法について説明します。Azure Mobile Apps SDK を使用すると、ASP.NET アプリケーションからモバイル クライアントを操作することができます。

>[AZURE.TIP] [Azure Mobile Apps 向け .NET サーバー SDK](https://github.com/Azure/azure-mobile-apps-net-server) は、オープン ソースとして GitHub で公開されています。リポジトリには、いくつかのサンプル プロジェクトと同様に、サーバー全体の SDK ユニット テスト スイートが含まれています。

## 方法: SDK をダウンロードして初期化する

SDK は [NuGet.org] で入手できます。このパッケージには、SDK の使用を開始するために必要な基礎機能が含まれています。SDK を初期化するには、**HttpConfiguration** オブジェクトに対して操作を実行する必要があります。

###SDK のインストール

SDK をインストールするには、Visual Studio でサーバー プロジェクトを右クリックして **[NuGet パッケージの管理]** を選択し、[Microsoft.Azure.Mobile.Server](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) パッケージを検索してから、**[インストール]** をクリックします。

###<a name="server-project-setup"></a> サーバー プロジェクトの初期化

.NET バックエンド サーバー プロジェクトは、他の ASP.NET プロジェクトと同じように、OWIN スタートアップ クラスを組み込むことによって初期化します。Visual Studio でこのクラスを追加するには、サーバー プロジェクトを右クリックして、**[追加]**、**[新しい項目]**、**[Web]**、**[全般]**、**[OWIN スタートアップ クラス]** の順に選択します。

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

個別の機能を有効化するには、**ApplyTo** を呼び出す前に、**MobileAppConfiguration** オブジェクトに対して拡張機能メソッドを呼び出す必要があります。たとえば、次のコードでは、初期化中にすべての API コントローラーに既定のルートを追加します。

	new MobileAppConfiguration()
	    .MapApiControllers()
	    .ApplyTo(config);

機能拡張メソッドの多くは、含めることが可能な追加の NuGet パッケージから入手できます。これらについては以下のセクションで説明します。Azure ポータルからのサーバーのクイックスタートは **UseDefaultConfiguration()** を呼び出します。これは次のセットアップと同等です。
    
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

- [Microsoft.Azure.Mobile.Server.Authentication] 認証を有効にし、トークンの検証に使用する OWIN ミドルウェアを設定します。構成に追加するには、**AddAppServiceAuthentication** 拡張メソッドと **IAppBuilder**.**UseMobileAppAuthentication** 拡張メソッドを呼び出します。

- [Microsoft.Azure.Mobile.Server.Notifications] プッシュ通知を有効にし、プッシュ登録エンドポイントを定義します。構成に追加するには、**AddPushNotifications** 拡張メソッドを呼び出します。

- [Microsoft.Azure.Mobile.Server.CrossDomain](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.CrossDomain/) モバイル アプリから従来の Web ブラウザーにデータを提供するコントローラーを作成します。構成に追加するには、**MapLegacyCrossDomainController** 拡張メソッドを呼び出します。

## 方法: カスタム API コントローラーを定義する

カスタム API コントローラーは、エンドポイントを公開して、モバイル アプリのバックエンドに最も基本的な機能を提供します。カスタム API コントローラー

1. Visual Studio で、Controllers フォルダーを右クリックして、**[追加]**、**[コントローラー]** の順にクリックし、**[Web API 2 コントローラー - 空]** を選択して **[追加]** をクリックします。

2. **コントローラー名** (`CustomController` など) を指定し、**[追加]** をクリックします。これで、**ApiController** から継承した新しい **CustomController** クラスが作成されます。

3. 新しいコントローラーのクラス ファイルに、次の using ステートメントを追加します。

		using Microsoft.Azure.Mobile.Server.Config;

4. 次の例に従って、API コントローラーのクラス定義に **MobileAppControllerAttribute** を適用します。

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

クライアントは **MobileAppControllerAttribute** が適用されていないコントローラーにもアクセスできますが、こうしたコントローラーはモバイル アプリ クライアント SDK を使用するクライアントでは適切に使用されません。

## 方法: テーブル コントローラーを定義する

テーブル コントローラーは、テーブル ベースのデータ ストア (SQL Database や Azure テーブル ストレージなど) にあるエンティティ データへのアクセスを提供します。テーブル コントローラーは、次のように **TableController** ジェネリック クラスから継承します。このクラスでは、ジェネリック型はテーブル スキーマを表すモデルのエンティティです。

	public class TodoItemController : TableController<TodoItem>
    {  
		//...
	}

テーブル コントローラーは、**AddTables** 拡張メソッドを使用して初期化します。次の例では、データ アクセスに Entity Framework を使用するテーブル コントローラーを初期化します。

    new MobileAppConfiguration().AddTables(
        new MobileAppTableConfiguration()
        .MapTableControllers()
        .AddEntityFramework()).ApplyTo(config);
 
Azure SQL Database のデータへのアクセスに Entity Framework を使用するテーブル コントローラーの例については、Azure ポータル からダウンロードしたクイックスタート サーバー プロジェクトの **TodoItemController** クラスを参照してください。

## 方法: サーバー プロジェクトに認証を追加する

**MobileAppConfiguration** オブジェクトを拡張し、OWIN ミドルウェアを構成すると、サーバー プロジェクトに認証を追加することができます。[Microsoft.Azure.Mobile.Server.Quickstart] パッケージをインストールし、**UseDefaultConfiguration** 拡張メソッドを呼び出している場合は、手順 3 に進むことができます。

1. Visual Studio で、[Microsoft.Azure.Mobile.Server.Authentication] パッケージをインストールします。 

2. Startup.cs プロジェクト ファイルで、**Configuration** メソッドの先頭に次のコード行を追加します。

		app.UseMobileAppAuthentication(config);

	これにより、関連付けられた App Service ゲートウェイで発行されたトークンを Azure モバイル アプリが検証できるようにする、OWIN ミドルウェア コンポーネントが追加されます。

3. 認証が必要なすべてのコントローラーまたはメソッドに `[Authorize]` 属性を追加します。これで、ユーザーはエンドポイントや特定の API にアクセスするときに認証を受ける必要があるようになりました。

Mobile Apps バックエンドに対してクライアントを認証する方法については、「[アプリケーションに認証を追加する](app-service-mobile-ios-get-started-users.md)」をご覧ください。

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

これで、Notification Hubs クライアントを使用して、登録済みデバイスにプッシュ通知を送信できるようになりました。詳細については、「[アプリケーションにプッシュ通知を追加する](app-service-mobile-ios-get-started-push.md)」をご覧ください。Notification Hubs で実行可能なすべての操作については、「[Azure Notification Hubs](../notification-hubs/notification-hubs-overview.md)」をご覧ください。

## 方法: タグにプッシュするために、タグをデバイス インストールに追加する

上記の「**方法: カスタム API コントローラーを定義する**」の後、バックエンドにカスタム API を設定し、Notification Hubs と連動させ、タグを特定のデバイス インストールに追加することがあります。クライアント ローカル ストレージに保存されているインストール ID と追加するタグ (タグはバックエンドで直接指定できるため、これは任意) を指定します。Notification Hubs と連動させ、タグをデバイス インストール ID に追加するために、次のスニペットをコントローラーに追加してください。

[Azure Notification Hubs NuGet](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) の使用 ([参照](https://msdn.microsoft.com/library/azure/mt414893.aspx)):

		var hub = NotificationHubClient.CreateClientFromConnectionString("my-connection-string", "my-hub");

		hub.PatchInstallation("my-installation-id", new[]
		{
		    new PartialUpdateOperation
		    {
		        Operation = UpdateOperationType.Add,
		        Path = "/tags",
		        Value = "{my-tag}"
		    }
		});
	

これらのタグにプッシュするために、[Notification Hubs API](https://msdn.microsoft.com/library/azure/dn495101.aspx) を利用します。

カスタム API を立ち上げ、バックエンドで直接、デバイス インストールを Notification Hubs に登録することもできます。

## 方法: サーバー プロジェクトの発行

サーバー プロジェクトを Azure に発行するには、次の手順に従います。

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service](../../includes/app-service-mobile-dotnet-backend-publish-service.md)]


[NuGet.org]: http://www.nuget.org/
[Microsoft.Azure.Mobile.Server.Quickstart]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Quickstart/
[Microsoft.Azure.Mobile.Server.Authentication]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Authentication/
[Microsoft.Azure.Mobile.Server.Notifications]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Notifications/

<!---HONumber=Nov15_HO4-->