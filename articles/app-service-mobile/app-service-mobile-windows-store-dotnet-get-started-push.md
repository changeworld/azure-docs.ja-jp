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
	ms.date="08/14/2015" 
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

1. Visual Studio ソリューション エクスプローラーで、Windows ストア アプリ プロジェクトを右クリックし、**[ストア]**、**[アプリケーションをストアと関連付ける]** の順にクリックします。 

    ![アプリを Windows ストアと関連付ける](./media/app-service-mobile-windows-store-dotnet-get-started-push/notification-hub-associate-win8-app.png)
    
2. ウィザードで **[次へ]** をクリックし、Microsoft アカウントでサインインし、**[新しいアプリケーション名の予約]** にアプリの名前を入力し、**[予約]** をクリックします。

3. アプリの登録が正常に作成されたら、新しいアプリ名を選択し、**[次へ]** をクリックし、**[関連付け]** をクリックします。この操作により、必要な Windows ストア登録情報がアプリケーション マニフェストに追加されます。

7. Windows ストア アプリ用に以前に作成したものと同じ登録を使用して、Windows Phone ストア アプリ プロジェクトに対してステップ 1 と 3 を繰り返します。

7. [[Windows デベロッパー センター]](https://dev.windows.com/ja-JP/overview) に移動し、Microsoft アカウントでサインインし、**[マイ アプリ]** で新しいアプリ登録をクリックしてから、**[サービス]**、**[プッシュ通知]** の順に展開します。

8. **[プッシュ通知]** ページで、**[Microsoft Azure Mobile Services]** の下にある **[Live サービス サイト]** をクリックします。

9. **[アプリ設定]** タブで、**[クライアント シークレット]** と **[パッケージ SID]** の値をメモしておきます。

    ![デベロッパー センターでのアプリの設定](./media/app-service-mobile-windows-store-dotnet-get-started-push/mobile-services-win8-app-push-auth.png)

    > [AZURE.IMPORTANT]クライアント シークレットとパッケージ SID は、重要なセキュリティ資格情報です。これらの値は、他のユーザーと共有したり、アプリケーションで配信したりしないでください。

##プッシュ要求を送信するようにモバイル アプリを構成する

1. [Azure ポータル]にログオンし、**[参照]**、**[モバイル アプリ]**、使用しているアプリ、**[プッシュ通知サービス]** の順に選択します。

2. **[Windows Notification Service]** で、Live サービス サイトから取得した **[セキュリティ キー]** (クライアント シークレット) と **[パッケージ SID]** を入力し、**[保存]** をクリックします。

これで、Mobile App バックエンドが WNS と連携するように構成されます。

##<a id="update-service"></a>プッシュ通知を送信するようにサーバーを更新する

アプリでプッシュ通知が有効にされたので、プッシュ通知を送信するようにアプリ バックエンドを更新する必要があります。

1. Visual Studio でサーバー プロジェクトを右クリックし、**[NuGet パッケージを管理]** をクリックして `Microsoft.Azure.NotificationHubs` を見つけ、**[インストール]** をクリックします。これにより、Notification Hubs のクライアント ライブラリがインストールされます。

3. サーバー プロジェクトで、**[Controllers]**、**[TodoItemController.cs]** の順に開き、次の using ステートメントを追加します。

		using System.Collections.Generic;
		using Microsoft.Azure.NotificationHubs;
		using Microsoft.Azure.Mobile.Server.Config;
	

2. **PostTodoItem** メソッドで、次のコードを **InsertAsync** に対する呼び出しの後ろに追加します。

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

1. Visual Studio でソリューションを開いて右クリックし、**[NuGet パッケージの管理]** をクリックします。 

    [NuGet パッケージの管理] ダイアログ ボックスが表示されます。

2. 管理対象用の App Service Mobile App クライアント SDK を検索します。次に、**[インストール]** をクリックし、ソリューションのすべてのクライアント プロジェクトを選択し、使用条件に同意します。

    これによって、Windows 向け Azure モバイル プッシュ ライブラリがダウンロード、インストールされ、すべてのクライアント プロジェクトにそのライブラリへの参照が追加されます。

3. 共有の **App.xaml.cs** プロジェクト ファイルを開き、次の `using` ステートメントを追加します。

		using System.Threading.Tasks;  
        using Windows.Networking.PushNotifications;       

4. 同じファイルで、次の **InitNotificationsAsync** メソッド定義を **App** クラスに追加します。
    
        private async Task InitNotificationsAsync()
        {
            var channel = await PushNotificationChannelManager
                .CreatePushNotificationChannelForApplicationAsync();

            await App.MobileService.GetPush().RegisterAsync(channel.Uri);
        }
    
    このコードにより、WNS からアプリケーションの ChannelURI が取得され、その ChannelURI が App Service モバイル アプリに登録されます。
    
5. **App.xaml.cs** 内の **OnLaunched** イベント ハンドラーの先頭で、次の例に示すように、**async** 修飾子をメソッド定義に追加し、次の呼び出しを新しい **InitNotificationsAsync** メソッドに追加します。

        protected async override void OnLaunched(LaunchActivatedEventArgs e)
        {
            await InitNotificationsAsync();

			// ...
		}

    これにより、アプリケーションが起動されるたびに、有効期限付きの ChannelURI が登録されます。

6. ソリューション エクスプローラーで Windows ストア アプリの **Package.appxmanifest** をダブルクリックし、**[通知]** で **[トースト対応]** を **[はい]** に設定します。

    **[ファイル]** メニューの **[すべて保存]** をクリックします。

7. Windows Phone ストア アプリ プロジェクトの前の手順を繰り返します。

これで、アプリケーションがトースト通知を受信する準備が整いました。

##<a id="test"></a>アプリケーションでプッシュ通知をテストする

[AZURE.INCLUDE [app-service-mobile-windows-universal-test-push](../../includes/app-service-mobile-windows-universal-test-push.md)]

<!-- Anchors. -->

<!-- URLs. -->
[Azure ポータル]: https://portal.azure.com/

<!-- Images. -->

<!---HONumber=Nov15_HO2-->