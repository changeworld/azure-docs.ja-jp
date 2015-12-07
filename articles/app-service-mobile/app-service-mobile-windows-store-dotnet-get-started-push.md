<properties 
	pageTitle="Windows Runtime 8.1 ユニバーサル アプリへのプッシュ通知の追加 | Azure Mobile Apps" 
	description="Azure App Service Mobile Apps と Azure Notification Hubs を使用して Windows アプリにプッシュ通知を送信する方法について説明します。" 
	services="app-service\mobile,notification-hubs" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service-mobile" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="11/10/2015" 
	ms.author="glenga"/>

# Windows Runtime 8.1 ユニバーサル アプリへのプッシュ通知の追加

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]&nbsp;[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

##概要

このトピックでは、Azure App Service Mobile Apps と Azure Notification Hubs を使用して Windows Runtime 8.1 ユニバーサル アプリにプッシュ通知を送信する方法を説明します。このシナリオでは、新しい項目が追加されると、Mobile App バックエンドによって、Windows Notification Service (WNS) に登録されているすべての Windows アプリにプッシュ通知が送信されます。

このチュートリアルは、App Service Mobile App のクイック スタートに基づいています。このチュートリアルを開始する前に、クイック スタート チュートリアル「[Windows アプリを作成する](../app-service-mobile-windows-store-dotnet-get-started.md)」を完了する必要があります。ダウンロードしたクイック スタートのサーバー プロジェクトを使用しない場合は、プッシュ通知拡張機能パッケージをプロジェクトに追加する必要があります。サーバーの拡張機能パッケージの詳細については、「[Work with the .NET backend server SDK for Azure Mobile Apps (Azure Mobile Apps 用の .NET バックエンド サーバー SDK を操作する)](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)」を参照してください。

##前提条件

このチュートリアルを完了するには、以下が必要です。

* アクティブな [Microsoft ストア アカウント](http://go.microsoft.com/fwlink/p/?LinkId=280045)。
* [Visual Studio Community 2013](https://go.microsoft.com/fwLink/p/?LinkID=391934)
* [クイック スタート チュートリアル](../app-service-mobile-windows-store-dotnet-get-started.md)を完了していること。  


##<a name="create-hub"></a>通知ハブを作成する

[AZURE.INCLUDE [app-service-mobile-create-notification-hub](../../includes/app-service-mobile-create-notification-hub.md)]

##アプリケーションをプッシュ通知に登録する

Azure から Windows アプリにプッシュ通知を送信するには、Windows ストア にアプリを送信する必要があります。その後、サーバー プロジェクトを構成して WNS と統合できます。

[AZURE.INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]


##プッシュ要求を送信するようにモバイル アプリを構成する

[AZURE.INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]


##<a id="update-service"></a>プッシュ通知を送信するようにサーバーを更新する

アプリでプッシュ通知が有効にされたので、プッシュ通知を送信するようにアプリ バックエンドを更新する必要があります。

1. Visual Studio でサーバー プロジェクトを右クリックし、**[NuGet パッケージを管理]** をクリックして `Microsoft.Azure.NotificationHubs` を見つけ、**[インストール]** をクリックします。これにより、Notification Hubs のクライアント ライブラリがインストールされます。

2. サーバー プロジェクトで、**[Controllers]**、**[TodoItemController.cs]** の順に開き、次の using ステートメントを追加します。

		using System.Collections.Generic;
		using Microsoft.Azure.NotificationHubs;
		using Microsoft.Azure.Mobile.Server.Config;

3. **PostTodoItem** メソッドで、次のコードを **InsertAsync** に対する呼び出しの後ろに追加します。

        // Get the settings for the server project.
        HttpConfiguration config = this.Configuration;
        MobileAppSettingsDictionary settings = 
			this.Configuration.GetMobileAppSettingsProvider().GetMobileAppSettings();
        
        // Get the Notification Hubs credentials for the Mobile App.
        string notificationHubName = settings.NotificationHubName;
        string notificationHubConnection = settings
            .Connections[MobileAppSettingsKeys.NotificationHubConnectionString].ConnectionString;

        // Create a new Notification Hub client.
        NotificationHubClient hub = NotificationHubClient
        .CreateClientFromConnectionString(notificationHubConnection, notificationHubName);

		// Define a WNS payload
		var windowsToastPayload = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" 
                                + item.Text + @"</text></binding></visual></toast>";

        try
        {
			// Send the push notification and log the results.
            var result = await hub.SendWindowsNativeNotificationAsync(windowsToastPayload);

            // Write the success result to the logs.
            config.Services.GetTraceWriter().Info(result.State.ToString());
        }
        catch (System.Exception ex)
        {
            // Write the failure result to the logs.
            config.Services.GetTraceWriter()
                .Error(ex.Message, null, "Push.SendAsync Error");
        }

    このコードは、新しい項目が挿入された後、プッシュ通知を送信するように通知ハブに指示します。


## <a name="publish-the-service"></a>モバイル バックエンドを Azure に発行する

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service](../../includes/app-service-mobile-dotnet-backend-publish-service.md)]

##<a id="update-service"></a>アプリケーションにプッシュ通知を追加する

1. 共有の **App.xaml.cs** プロジェクト ファイルを開き、次の `using` ステートメントを追加します。

		using System.Threading.Tasks;  
        using Windows.Networking.PushNotifications;       

2. 同じファイルで、次の **InitNotificationsAsync** メソッド定義を **App** クラスに追加します。
    
        private async Task InitNotificationsAsync()
        {
            var channel = await PushNotificationChannelManager
                .CreatePushNotificationChannelForApplicationAsync();

            await MobileService.GetPush().RegisterAsync(channel.Uri);
        }
    
    このコードにより、WNS からアプリケーションの ChannelURI が取得され、その ChannelURI が App Service モバイル アプリに登録されます。
    
3. **App.xaml.cs** 内の **OnLaunched** イベント ハンドラーの先頭で、次の例に示すように、**async** 修飾子をメソッド定義に追加し、次の呼び出しを新しい **InitNotificationsAsync** メソッドに追加します。

        protected async override void OnLaunched(LaunchActivatedEventArgs e)
        {
            await InitNotificationsAsync();

			// ...
		}

    これにより、アプリケーションが起動されるたびに、有効期限付きの ChannelURI が登録されます。

4. ソリューション エクスプローラーで Windows ストア アプリの **Package.appxmanifest** をダブルクリックし、**[通知]** で **[トースト対応]** を **[はい]** に設定します。

    **[ファイル]** メニューの **[すべて保存]** をクリックします。

5. Windows Phone ストア アプリ プロジェクトの前の手順を繰り返します。

これで、アプリケーションがトースト通知を受信する準備が整いました。

##<a id="test"></a>アプリケーションでプッシュ通知をテストする

[AZURE.INCLUDE [app-service-mobile-windows-universal-test-push](../../includes/app-service-mobile-windows-universal-test-push.md)]

##<a id="more"></a>詳細

* テンプレートを利用すれば、プラットフォーム間のプッシュやローカライズされたプッシュを柔軟に送信できます。テンプレートの登録方法は「[Azure Mobile Apps 用の管理されたクライアントの使用方法](app-service-mobile-dotnet-how-to-use-client-library.md)」にあります。
* タグを利用すれば、特定の区分に属する顧客にプッシュで的を絞ることができます。デバイス インストールにタグを追加する方法は「[Azure Mobile Apps 用 .NET バックエンド サーバー SDK の操作](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)」にあります。

<!-- Anchors. -->

<!-- URLs. -->
[Azure Portal]: https://portal.azure.com/

<!-- Images. -->

<!---HONumber=AcomDC_1125_2015-->