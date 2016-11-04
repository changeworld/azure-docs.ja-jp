---
title: ユニバーサル Windows プラットフォーム (UWP) アプリにプッシュ通知を追加する | Microsoft Docs
description: Azure App Service Mobile Apps と Azure Notification Hubs を使用してユニバーサル Windows プラットフォーム (UWP) アプリにプッシュ通知を送信する方法について説明します。
services: app-service\mobile,notification-hubs
documentationcenter: windows
author: adrianhall
manager: dwrede
editor: ''

ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.author: adrianha

---
# <a name="add-push-notifications-to-your-windows-app"></a>Windows アプリにプッシュ通知を追加する
[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Overview
このトピックでは、Azure App Service の Mobile Apps と Azure Notification Hubs を使用して、ユニバーサル Windows プラットフォーム (UWP) アプリにプッシュ通知を送信する方法を説明します。 このシナリオでは、新しい項目が追加されると、Mobile App バックエンドによって、Windows Notification Service (WNS) に登録されているすべての Windows アプリにプッシュ通知が送信されます。

このチュートリアルは、Mobile Apps のクイック スタートに基づいています。 このチュートリアルを開始する前に、クイック スタート チュートリアル「 [Windows アプリを作成する](app-service-mobile-windows-store-dotnet-get-started.md)」を完了する必要があります。 ダウンロードしたクイック スタートのサーバー プロジェクトを使用しない場合は、プッシュ通知拡張機能パッケージをプロジェクトに追加する必要があります。 サーバーの拡張機能パッケージの詳細については、「 [Work with the .NET backend server SDK for Azure Mobile Apps (Azure Mobile Apps 用の .NET バックエンド サーバー SDK を操作する)](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)」を参照してください。

## <a name="<a-name="create-hub"></a>create-a-notification-hub"></a><a name="create-hub"></a>通知ハブを作成する
[!INCLUDE [app-service-mobile-create-notification-hub](../../includes/app-service-mobile-create-notification-hub.md)]

## <a name="register-your-app-for-push-notifications"></a>アプリケーションをプッシュ通知に登録する
Azure から Windows アプリにプッシュ通知を送信するには、Windows ストア にアプリを送信する必要があります。 その後、サーバー プロジェクトを構成して WNS と統合できます。

1. Visual Studio ソリューション エクスプローラーで、UWP アプリ プロジェクトを右クリックし、**[ストア]**  >  **[アプリケーションをストアと関連付ける]** の順にクリックします。 
   
    ![アプリを Windows ストアと関連付ける](./media/app-service-mobile-windows-store-dotnet-get-started-push/notification-hub-associate-uwp-app.png)
2. ウィザードで **[次へ]** をクリックし、Microsoft アカウントでサインインします。次に、**[新しいアプリケーション名の予約]** にアプリの名前を入力し、**[予約]** をクリックします。
3. アプリの登録が正常に作成されたら、新しいアプリ名を選択し、**[次へ]** をクリックし、**[関連付け]** をクリックします。 この操作により、必要な Windows ストア登録情報がアプリケーション マニフェストに追加されます。  
4. [[Windows デベロッパー センター]](https://dev.windows.com/en-us/overview) に移動し、Microsoft アカウントでサインインして、**[マイ アプリ]** で新しいアプリ登録をクリックします。次に、**[サービス]**  >  **[プッシュ通知]** の順に展開します。 
5. **[プッシュ通知]** ページで、**[Microsoft Azure Mobile Services]** の下にある **[Live サービス サイト]** をクリックします。
6. 登録ページで、**[アプリケーション シークレット]** と **[パッケージ SID]** の値を記録しておきます。モバイル アプリ バックエンドを構成するときに使用します。 
   
    ![アプリを Windows ストアと関連付ける](./media/app-service-mobile-windows-store-dotnet-get-started-push/app-service-mobile-uwp-app-push-auth.png)
   
   > [!IMPORTANT]
   > クライアント シークレットとパッケージ SID は、重要なセキュリティ資格情報です。 これらの値は、他のユーザーと共有したり、アプリケーションで配信したりしないでください。 **[アプリケーション ID]** は、Microsoft アカウント認証を構成するためにシークレットと共に使用されます。
   > 
   > 

## <a name="configure-the-backend-to-send-push-notifications"></a>プッシュ通知を送信するようにバックエンドを構成する
[!INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

## <a name="<a-id="update-service"></a>update-the-server-to-send-push-notifications"></a><a id="update-service"></a>プッシュ通知を送信するようにサーバーを更新する
アプリでプッシュ通知が有効にされたので、プッシュ通知を送信するようにアプリ バックエンドを更新する必要があります。 バックエンド プロジェクトの種類 ([.NET バックエンド](#dotnet)または [Node.js バックエンド](#nodejs)のいずれか) に一致する以下の手順を使用します。

### <a name="<a-name="dotnet"></a>.net-backend-project"></a><a name="dotnet"></a>.NET バックエンド プロジェクト
1. Visual Studio で、サーバー プロジェクトを右クリックして **[NuGet パッケージの管理]** をクリックし、Microsoft.Azure.NotificationHubs を検索して、**[インストール]** をクリックします。 これにより、Notification Hubs のクライアント ライブラリがインストールされます。
2. **Controllers**を展開し、TodoItemController.cs を開いて、次の using ステートメントを追加します。
   
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

### <a name="<a-name="nodejs"></a>node.js-backend-project"></a><a name="nodejs"></a>Node.js バックエンド プロジェクト
1. これをまだ行っていない場合は、[クイック スタート プロジェクト](app-service-mobile-node-backend-how-to-use-server-sdk.md#download-quickstart)をダウンロードするか、[Azure Portal でオンライン エディター](app-service-mobile-node-backend-how-to-use-server-sdk.md#online-editor)を使用します。
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
        var payload = "<toast><visual><binding template=\ToastText01\><text id=\"1\">"
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
3. ローカル コンピューターでファイルを編集するときは、サーバー プロジェクトを再発行します。

## <a name="<a-id="update-app"></a>add-push-notifications-to-your-app"></a><a id="update-app"></a>アプリケーションにプッシュ通知を追加する
次に、アプリは起動時にプッシュ通知に登録する必要があります。 認証を既に有効にしている場合は、プッシュ通知を登録する前にユーザーがサインインしていることを確認します。 詳細については、クイック スタートの完全なサンプルの「 [Authenticate first](https://github.com/Azure-Samples/app-service-mobile-windows-quickstart/blob/master/README.md#authenticate-first) 」を参照してください。

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
4. UWP アプリ プロジェクトをリビルドします。 これで、アプリケーションがトースト通知を受信する準備が整いました。

## <a name="<a-id="test"></a>test-push-notifications-in-your-app"></a><a id="test"></a>アプリケーションでプッシュ通知をテストする
[!INCLUDE [app-service-mobile-windows-universal-test-push](../../includes/app-service-mobile-windows-universal-test-push.md)]

## <a name="<a-id="more"></a>next-steps"></a><a id="more"></a>次のステップ
プッシュ通知についてさらに学習します。

* [Azure Mobile Apps 用の管理されたクライアントの使用方法](app-service-mobile-dotnet-how-to-use-client-library.md#how-to-register-push-templates-to-send-cross-platform-notifications)  
  テンプレートを利用すれば、プラットフォーム間のプッシュやローカライズされたプッシュを柔軟に送信できます。 この記事ではテンプレートの登録方法について説明しています。
* [Work with the .NET backend server SDK for Azure Mobile Apps (Azure Mobile Apps 用の .NET バックエンド サーバー SDK を操作する) (Azure Mobile Apps 用の .NET バックエンド サーバー SDK を操作する)](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#how-to-add-tags-to-a-device-installation-to-enable-push-to-tags)  
  タグを利用して、プッシュ通知の対象となる顧客を絞り込むことができます。  この記事では、タグをデバイス インストールに追加する方法について確認します。
* [プッシュ通知の問題の診断](../notification-hubs/notification-hubs-push-notification-fixer.md)  
  通知が破棄されたり、デバイスに届かなかったりするのにはさまざまな原因があります。 このトピックでは、プッシュ通知のエラーの根本原因を分析、解明する方法について説明しています。 

次のチュートリアルのいずれかに進むことを検討してください。

* [アプリへの認証の追加](app-service-mobile-windows-store-dotnet-get-started-users.md)  
   ID プロバイダーを使用してアプリのユーザーを認証する方法について説明します。
* [アプリのオフライン同期の有効化](app-service-mobile-windows-store-dotnet-get-started-offline-data.md)  
   モバイル アプリ バックエンドを使用してオフライン サポートをアプリに追加する方法について説明します。 オフライン同期を使用すると、エンド ユーザーはネットワークにアクセスできなくても、データの表示、追加、変更など、モバイル アプリケーションとやり取りできます。

<!-- Anchors. -->

<!-- URLs. -->
[Azure App Service で Java Web アプリ]: https://portal.azure.com/

<!-- Images. -->




<!--HONumber=Oct16_HO2-->


