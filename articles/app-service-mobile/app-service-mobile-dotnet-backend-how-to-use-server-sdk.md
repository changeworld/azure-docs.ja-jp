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
	ms.date="08/12/2015"
	ms.author="glenga"/>

# Azure モバイル アプリ用 .NET バックエンド サーバー SDK の操作

このトピックでは、Azure App Service Mobile Apps の主要なシナリオで .NET バックエンド サーバー SDK を使用する方法について説明します。Azure モバイル アプリ SDK を使用すると、ASP.NET アプリケーションからモバイル クライアントを操作することができます。

## 方法: SDK をダウンロードして初期化する

SDK は [NuGet.org] で入手できます。このパッケージには、SDK の使用を開始するために必要な基礎機能が含まれています。SDK を初期化するには、**HttpConfiguration** オブジェクトに対して操作を実行する必要があります。

###SDK のインストール

SDK をインストールするには、Visual Studio でサーバー プロジェクトを右クリックして **[NuGet パッケージの管理]** を選択し、[Microsoft.Azure.Mobile.Server](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) パッケージを検索してから、**[インストール]** をクリックします。

###サーバー プロジェクトの初期化

.NET バックエンド サーバー プロジェクトは、**WebApiConfig** クラスの **Register** メソッドで初期化します。このメソッドは、通常、App\_Start フォルダーにあります。サーバー プロジェクトの初期化には、サービスの構成オプションを表す **HttpConfiguration** を使用します。次の例では、機能を追加せずにサーバー プロジェクトを初期化します。

    new MobileAppConfiguration()
        .ApplyTo(config);

個別の機能を有効化するには、**ApplyTo** を呼び出す前に、**MobileAppConfiguration** オブジェクトに対して拡張機能メソッドを呼び出す必要があります。たとえば、次のコードでは、初期化中にすべての API コントローラーに既定のルートを追加します。

	new MobileAppConfiguration()
	    .MapApiControllers()
	    .ApplyTo(config);

機能拡張メソッドの多くは、含めることが可能な追加の NuGet パッケージから入手できます。これらについては以下のセクションで説明します。

### SDK 拡張機能

次の NuGet ベースの拡張機能パッケージでは、アプリケーションで使用可能な各種モバイル機能が提供されます。拡張機能の有効化は、初期化中に **MobileAppConfiguration** オブジェクトを使用して行います。

- [Microsoft.Azure.Mobile.Server.Quickstart] Mobile Apps の基本的なセットアップをサポートします。構成に追加するには、初期化中に **UseDefaultConfiguration** 拡張機能メソッドを呼び出します。この拡張機能には、Notifications、Authentication、Entity、Tables、Crossdomain、および Home パッケージが含まれています。これは、Azure ポータル からダウンロードするクイックスタート サーバー プロジェクトに相当します。

- [Microsoft.Azure.Mobile.Server.Home](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Home/) Web サイトのルートにシンプルなホーム ページを追加します。構成に追加するには、**AddMobileAppHomeController** 拡張機能メソッドを呼び出します。

- [Microsoft.Azure.Mobile.Server.Tables](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Tables/) データ処理のためのクラスが含まれており、データ パイプラインを設定します。構成に追加するには、**AddTables** 拡張機能メソッドを呼び出します。

- [Microsoft.Azure.Mobile.Server.Entity](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Entity/) Entity Framework で SQL Database のデータにアクセスできるようにします。構成に追加するには、**AddTablesWithEntityFramework** 拡張機能メソッドを呼び出します。

- [Microsoft.Azure.Mobile.Server.Authentication] 認証を有効化し、トークン検証用の OWIN ミドルウェアを設定します。構成に追加するには、**AddAppServiceAuthentication** 拡張機能メソッドと **IAppBuilder**.**UseMobileAppAuthentication** 拡張機能メソッドを呼び出します。

- [Microsoft.Azure.Mobile.Server.Notifications] プッシュ通知を有効化し、プッシュ登録エンドポイントを定義します。構成に追加するには、**AddPushNotifications** 拡張機能メソッドを呼び出します。

- [Microsoft.Azure.Mobile.Server.CrossDomain](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.CrossDomain/) モバイル アプリから従来の Web ブラウザーにデータを提供するコントローラーを作成します。構成に追加するには、**MapLegacyCrossDomainController** 拡張機能メソッドを呼び出します。

## 方法: カスタム API コントローラーを定義する

カスタム API コントローラーは、エンドポイントを公開して、モバイル アプリのバックエンドに最も基本的な機能を提供します。カスタム API コントローラー

1. Visual Studio で、Controllers フォルダーを右クリックして **[追加]** > **[コントローラー]** の順にクリックし、**[Web API 2 コントローラー - 空]** を選択して **[追加]** をクリックします。

2. **コントローラー名** (`CustomController` など) を指定して **[追加]** をクリックします。これで、**ApiController** を継承する **CustomController** クラスが新規作成されます。

3. 新しいコントローラーのクラス ファイルに、次の using ステートメントを追加します。

		using Microsoft.Azure.Mobile.Server.Config;

4. 次の例に従って、API コントローラーのクラス定義に **MobileAppControllerAttribute** を適用します。

		[MobileAppController] 
		public class CustomController : ApiController
		{
		      //...
		}

4. App\_Startup フォルダーを参照して WebApiConfig.cs プロジェクト ファイルを開き、次の例に従って **MapApiControllers** 拡張機能メソッドの呼び出しを追加します。

		new MobileAppConfiguration()
		    .MapApiControllers()
		    .ApplyTo(config);

	すべての機能を初期化する **UseDefaultConfiguration** を呼び出す場合は、**MapApiControllers** を呼び出す必要はないことに注意してください。

クライアントは **MobileAppControllerAttribute** が適用されていないコントローラーにもアクセスできますが、こうしたコントローラーはモバイル アプリ クライアント SDK を使用するクライアントには適切に使用されません。

## 方法: テーブル コントローラーを定義する

テーブル コントローラーは、テーブル ベースのデータ ストア (SQL Database や Azure テーブル ストレージなど) にあるエンティティ データへのアクセスを提供します。テーブル コントローラーは、次に示すように **TableController** 汎用クラスから継承します。このクラスでは、汎用タイプはテーブル スキーマを表すモデルのエンティティです。

	public class TodoItemController : TableController<TodoItem>
    {  
		//...
	}

テーブル コントローラーは、**AddTables** 拡張機能メソッドを使用して初期化します。次の例では、データ アクセスに Entity Framework を使用するテーブル コントローラーを初期化します。

    new MobileAppConfiguration().AddTables(
        new MobileAppTableConfiguration()
        .MapTableControllers()
        .AddEntityFramework()).ApplyTo(config);
 
Azure SQL Database のデータへのアクセスに Entity Framework を使用するテーブル コントローラーの例については、Azure ポータル からダウンロードしたクイックスタート サーバー プロジェクトの **TodoItemController** クラスを参照してください。

## 方法: サーバー プロジェクトに認証を追加する

**MobileAppConfiguration** オブジェクトを拡張して OWIN ミドルウェアを構成すると、サーバー プロジェクトに認証を追加することができます。[Microsoft.Azure.Mobile.Server.Quickstart] パッケージをインストールし、**UseDefaultConfiguration** 拡張機能メソッドを呼び出している場合は、手順 4 に進むことができます。

1. Visual Studio で、[Microsoft.Azure.Mobile.Server.Authentication] パッケージをインストールします。 

2. App\_Startup フォルダーを参照して WebApiConfig.cs プロジェクト ファイルを開き、次に示すように初期化中に **AddAppServiceAuthentication** 拡張機能メソッドへの呼び出しを追加します。

		new MobileAppConfiguration()
			// other features...
			.AddAppServiceAuthentication()
			.ApplyTo(config);

3. Startup.cs プロジェクト ファイルで、**Configuration** メソッドの先頭に次のコード行を追加します。

		app.UseMobileAppAuthentication(config);

	これにより、関連付けられた App Service ゲートウェイで発行されたトークンを Azure モバイル アプリが検証できるようにする、OWIN ミドルウェア コンポーネントが追加されます。

4. 認証が必要なすべてのコントローラーまたはメソッドに `[Authorize]` 属性を追加します。これで、ユーザーはエンドポイントや特定の API にアクセスするときに認証を受ける必要があるようになりました。

Mobile Apps バックエンドに認証クライアントを追加する方法については、「[アプリへの認証の追加](app-service-mobile-dotnet-backend-ios-get-started-users-preview.md)」を参照してください。

## 方法: サーバー プロジェクトにプロジェクトを追加する

**MobileAppConfiguration** オブジェクトを拡張して Notification Hubs クライアントを作成すると、サーバー プロジェクトにプッシュ通知を追加することができます。[Microsoft.Azure.Mobile.Server.Quickstart] パッケージをインストールし、**UseDefaultConfiguration** 拡張機能メソッドを呼び出している場合は、手順 3 に進むことができます。

1. Visual Studio でサーバー プロジェクトを右クリックし、**[NuGet パッケージの管理]** をクリックし、Microsoft.Azure.Mobile.Server.Notifications を検索して **[インストール]** をクリックします。これにより、[Microsoft.Azure.Mobile.Server.Notifications] パッケージがインストールされます。
 
3. 上記の手順を繰り返して、Notification Hubs クライアント ライブラリが含まれる `Microsoft.Azure.NotificationHubs` パッケージをインストールします。

2. App\_Startup フォルダーを参照して WebApiConfig.cs プロジェクト ファイルを開き、次に示すように初期化中に **AddPushNotifications** 拡張機能メソッドへの呼び出しを追加します。

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
        ServiceSettingsDictionary settings = 
            config.GetMobileAppSettingsProvider().GetMobileAppSettings();
        
        // Get the Notification Hubs credentials for the Mobile App.
        string notificationHubName = settings.NotificationHubName;
        string notificationHubConnection = settings
            .Connections[MobileAppSettingsKeys.NotificationHubConnectionString].ConnectionString;

        // Create a new Notification Hub client.
        NotificationHubClient hub = NotificationHubClient
        .CreateClientFromConnectionString(notificationHubConnection, notificationHubName);

これで、Notification Hubs クライアントを使用して、登録済みデバイスにプッシュ通知を送信できるようになりました。詳細については、「[アプリケーションにプッシュ通知を追加する](app-service-mobile-dotnet-backend-ios-get-started-push-preview.md)」を参照してください。Notification Hubs で実行可能なすべての操作については、「[Notification Hubs の概要](../notification-hubs/notification-hubs-overview.md)」を参照してください。

## 方法: サーバー プロジェクトの発行

サーバー プロジェクトを Azure に発行するには、次の手順に従います。

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service-preview](../../includes/app-service-mobile-dotnet-backend-publish-service-preview.md)]


[NuGet.org]: http://www.nuget.org/
[Microsoft.Azure.Mobile.Server.Quickstart]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Quickstart/
[Microsoft.Azure.Mobile.Server.Authentication]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Authentication/
[Microsoft.Azure.Mobile.Server.Notifications]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Notifications/

<!---HONumber=August15_HO8-->