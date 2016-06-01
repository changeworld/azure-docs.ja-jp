<properties
	pageTitle="ユニバーサル Windows プラットフォーム (UWP) アプリにプッシュ通知を追加する | Azure Mobile Apps"
	description="Azure App Service Mobile Apps と Azure Notification Hubs を使用してユニバーサル Windows プラットフォーム (UWP) アプリにプッシュ通知を送信する方法について説明します。"
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
	ms.date="05/15/2016"
	ms.author="glenga"/>

# Windows Runtime 8.1 ユニバーサル アプリへのプッシュ通知の追加

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

##概要

このトピックでは、Azure App Service の Mobile Apps と Azure Notification Hubs を使用してユニバーサル Windows プラットフォーム (UWP) アプリにプッシュ通知を送信する方法を説明します。このシナリオでは、新しい項目が追加されると、Mobile App バックエンドによって、Windows Notification Service (WNS) に登録されているすべての Windows アプリにプッシュ通知が送信されます。

このチュートリアルは、Mobile Apps のクイック スタートに基づいています。このチュートリアルを開始する前に、クイック スタート チュートリアル「[Windows アプリを作成する](app-service-mobile-windows-store-dotnet-get-started.md)」を完了する必要があります。ダウンロードしたクイック スタートのサーバー プロジェクトを使用しない場合は、プッシュ通知拡張機能パッケージをプロジェクトに追加する必要があります。サーバーの拡張機能パッケージの詳細については、「[Work with the .NET backend server SDK for Azure Mobile Apps (Azure Mobile Apps 用の .NET バックエンド サーバー SDK を操作する)](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)」を参照してください。

##<a name="create-hub"></a>通知ハブを作成する

[AZURE.INCLUDE [app-service-mobile-create-notification-hub](../../includes/app-service-mobile-create-notification-hub.md)]

##アプリケーションをプッシュ通知に登録する

Azure から Windows アプリにプッシュ通知を送信するには、Windows ストア にアプリを送信する必要があります。その後、サーバー プロジェクトを構成して WNS と統合できます。

1. Visual Studio ソリューション エクスプローラーで、UWP アプリ プロジェクトを右クリックし、**[ストア]**、**[アプリケーションをストアと関連付ける]** の順にクリックします。 

    ![アプリを Windows ストアと関連付ける](./media/app-service-mobile-windows-store-dotnet-get-started-push/notification-hub-associate-uwp-app.png)
    
2. ウィザードで **[次へ]** をクリックし、Microsoft アカウントでサインインし、**[新しいアプリケーション名の予約]** にアプリの名前を入力し、**[予約]** をクリックします。

3. アプリの登録が正常に作成されたら、新しいアプリ名を選択し、**[次へ]** をクリックし、**[関連付け]** をクリックします。この操作により、必要な Windows ストア登録情報がアプリケーション マニフェストに追加されます。

7. [[Windows デベロッパー センター]](https://dev.windows.com/ja-JP/overview) に移動し、Microsoft アカウントでサインインし、**[マイ アプリ]** で新しいアプリ登録をクリックしてから、**[サービス]**、**[プッシュ通知]** の順に展開します。

8. **[プッシュ通知]** ページで、**[Microsoft Azure Mobile Services]** の下にある **[Live サービス サイト]** をクリックします。

9. 登録ページで、**[アプリケーション シークレット]** と **[パッケージ SID]** の値を記録しておきます。モバイル アプリ バックエンドを構成するときに使用します。

	![アプリを Windows ストアと関連付ける](./media/app-service-mobile-windows-store-dotnet-get-started-push/app-service-mobile-uwp-app-push-auth.png)

    > [AZURE.IMPORTANT] クライアント シークレットとパッケージ SID は、重要なセキュリティ資格情報です。これらの値は、他のユーザーと共有したり、アプリケーションで配信したりしないでください。**[アプリケーション ID]** は、Microsoft アカウント認証を構成するためにシークレットと共に使用されます。

##プッシュ通知を送信するようにバックエンドを構成する

[AZURE.INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]


##<a id="update-service"></a>プッシュ通知を送信するようにサーバーを更新する

アプリでプッシュ通知が有効にされたので、プッシュ通知を送信するようにアプリ バックエンドを更新する必要があります。バックエンド プロジェクトの種類 ([.NET バックエンド](#dotnet)または [Node.js バックエンド](#nodejs)のいずれか) に一致する以下の手順を使用します。

### <a name="dotnet"></a>.NET バックエンド プロジェクト

1. Visual Studio で、サーバー プロジェクトを右クリックして **[NuGet パッケージの管理]** をクリックし、Microsoft.Azure.NotificationHubs を検索して、**[インストール]** をクリックします。これにより、Notification Hubs のクライアント ライブラリがインストールされます。

2. **Controllers** を展開し、TodoItemController.cs を開いて、次の using ステートメントを追加します。

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

	    // Create the notification hub client.
	    NotificationHubClient hub = NotificationHubClient
	        .CreateClientFromConnectionString(notificationHubConnection, notificationHubName);

	    // Define a WNS payload
	    var windowsToastPayload = @"<toast><visual><binding template=""ToastText01""><text id=""1"">"
	                            + item.Text + @"</text></binding></visual></toast>";
	    try
	    {
	        // Send the push notification.
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

4. サーバー プロジェクトを発行します。

### <a name="nodejs"></a>Node.js バックエンド プロジェクト

1. これをまだ行っていない場合は、[クイック スタート プロジェクトをダウンロードする](app-service-mobile-node-backend-how-to-use-server-sdk.md#download-quickstart)か、[Azure ポータルでオンライン エディター](app-service-mobile-node-backend-how-to-use-server-sdk.md#online-editor)を使用します。

2. todoitem.js ファイル内の既存のコードを次のコードに置き換えます。

		var azureMobileApps = require('azure-mobile-apps'),
	    promises = require('azure-mobile-apps/src/utilities/promises'),
	    logger = require('azure-mobile-apps/src/logger');

		var table = azureMobileApps.table();

		table.insert(function (context) {
	    // For more information about the Notification Hubs JavaScript SDK,
	    // see http://aka.ms/nodejshubs
	    logger.info('Running TodoItem.insert');

	    // Define the WNS payload that contains the new item Text.
	    var payload = "<toast><visual><binding template=\ToastText01><text id="1">"
		                            + context.item.text + "</text></binding></visual></toast>";

	    // Execute the insert.  The insert returns the results as a Promise,
	    // Do the push as a post-execute action within the promise flow.
	    return context.execute()
	        .then(function (results) {
	            // Only do the push if configured
	            if (context.push) {
					// Send a WNS native toast notification.
	                context.push.wns.sendToast(null, payload, function (error) {
	                    if (error) {
	                        logger.error('Error while sending push notification: ', error);
	                    } else {
	                        logger.info('Push notification sent successfully!');
	                    }
	                });
	            }
	            // Don't forget to return the results from the context.execute()
	            return results;
	        })
	        .catch(function (error) {
	            logger.error('Error while running context.execute: ', error);
	        });
		});

		module.exports = table;

	これにより、新しい ToDo 項目が挿入されたときには item.text を含む WNS トースト通知が送信されます。

2. ローカル コンピューターでファイルを編集するときは、サーバー プロジェクトを再発行します。

##<a id="update-app"></a>アプリケーションにプッシュ通知を追加する

次に、アプリは起動時にプッシュ通知に登録する必要があります。認証を既に有効にしている場合は、プッシュ通知を登録する前にユーザーがサインインしていることを確認します。詳細については、クイック スタートの完全なサンプルの「[Authenticate first](https://github.com/Azure-Samples/app-service-mobile-windows-quickstart/blob/master/README.md#authenticate-first)」を参照してください。

1. **App.xaml.cs** プロジェクト ファイルを開き、次の `using` ステートメントを追加します。

		using System.Threading.Tasks;
        using Windows.Networking.PushNotifications;

2. 同じファイルで、次の **InitNotificationsAsync** メソッド定義を **App** クラスに追加します。

        private async Task InitNotificationsAsync()
        {
            // Get a channel URI from WNS.
            var channel = await PushNotificationChannelManager
                .CreatePushNotificationChannelForApplicationAsync();

            // Register the channel URI with Notification Hubs.
            await App.MobileService.GetPush().RegisterAsync(channel.Uri);
        }

    このコードにより、WNS からアプリケーションの ChannelURI が取得され、その ChannelURI が App Service モバイル アプリに登録されます。

3. **App.xaml.cs** 内の **OnLaunched** イベント ハンドラーの先頭で、次の例に示すように、**async** 修飾子をメソッド定義に追加し、次の呼び出しを新しい **InitNotificationsAsync** メソッドに追加します。

        protected async override void OnLaunched(LaunchActivatedEventArgs e)
        {
            await InitNotificationsAsync();

			// ...
		}

    これにより、アプリケーションが起動されるたびに、有効期限付きの ChannelURI が登録されます。

4. UWP アプリ プロジェクトをリビルドします。これで、アプリケーションがトースト通知を受信する準備が整いました。

##<a id="test"></a>アプリケーションでプッシュ通知をテストする

[AZURE.INCLUDE [app-service-mobile-windows-universal-test-push](../../includes/app-service-mobile-windows-universal-test-push.md)]


##<a id="more"></a>詳細

* テンプレートを利用すれば、プラットフォーム間のプッシュやローカライズされたプッシュを柔軟に送信できます。テンプレートの登録方法は「[Azure Mobile Apps 用の管理されたクライアントの使用方法](app-service-mobile-dotnet-how-to-use-client-library.md#how-to-register-push-templates-to-send-cross-platform-notifications)」にあります。
* タグを利用すれば、特定の区分に属する顧客にプッシュで的を絞ることができます。デバイス インストールにタグを追加する方法は「[Azure Mobile Apps 用 .NET バックエンド サーバー SDK の操作](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#how-to-add-tags-to-a-device-installation-to-enable-push-to-tags)」に記載されています。

<!-- Anchors. -->

<!-- URLs. -->
[Azure Portal]: https://portal.azure.com/

<!-- Images. -->
))) )

<!---HONumber=AcomDC_0518_2016-->