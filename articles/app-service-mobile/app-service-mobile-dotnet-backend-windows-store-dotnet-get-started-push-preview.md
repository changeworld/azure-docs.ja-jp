<properties 
	pageTitle="Azure App Service を使用してユニバーサル Windows アプリにプッシュ通知を追加する" 
	description="Azure App Service を使用して、ユニバーサル Windows アプリにプッシュ通知を送信する方法について説明します。" 
	services="app-service\mobile" 
	documentationCenter="windows" 
	authors="ysxu" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service-mobile" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="06/18/2015" 
	ms.author="yuaxu"/>

# Windows ストア アプリにプッシュ通知を追加する

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push-preview](../../includes/app-service-mobile-selector-get-started-push-preview.md)]

このトピックでは、Azure App Service を使用して .NET バックエンドから Windows ユニバーサル アプリにプッシュ通知を送信する方法について説明します。完了すると、レコードの挿入時に、.NET バックエンドから、登録されているすべての Windows ユニバーサル アプリにプッシュ通知が送信されます。

このチュートリアルでは、プッシュ通知を有効にするための、次の基本的な手順について説明します。

1. [アプリをプッシュ通知に登録する](#register)
2. [構成](#configure)
3. [サービスを更新してプッシュ通知を送信する](#update-service)
4. [アプリケーションにプッシュ通知を追加する](#add-push)
5. [アプリケーションでプッシュ通知をテストする](#test)

このチュートリアルは、App Service Mobile App のクイック スタートに基づいています。このチュートリアルを開始する前に、[App Service モバイル アプリの使用] に関するチュートリアルを完了している必要があります。

このチュートリアルを完了するには、以下が必要です。

* アクティブな [Microsoft ストア アカウント](http://go.microsoft.com/fwlink/p/?LinkId=280045)。
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=391934" target="_blank">Visual Studio Community 2013</a>。

##<a name="review"></a>サーバーのプロジェクト構成を確認する (省略可能)

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-enable-push-preview](../../includes/app-service-mobile-dotnet-backend-enable-push-preview.md)]

##<a id="register"></a>アプリをプッシュ通知に登録する

Azure App Service を使用して Windows ユニバーサル アプリにプッシュ通知を送信するには、アプリケーションを Windows ストアに提出する必要があります。さらに、モバイル アプリ プッシュ通知サービスの資格情報を構成して、WNS と統合する必要があります。

1. アプリケーションをまだ登録していない場合は、Windows ストア アプリのデベロッパー センターで<a href="http://go.microsoft.com/fwlink/p/?LinkID=266582" target="_blank">アプリの提出のページ</a>に移動し、Microsoft アカウントでログインして、**[アプリ名]** をクリックします。

    ![][0]

2. **[アプリ名]** にアプリケーションの名前を入力し、**[アプリの名前の予約]** をクリックして、**[保存]** をクリックします。

    ![][1]

    これでアプリケーションの新しい Windows ストア登録が作成されます。

4. ソリューション エクスプローラーで Windows ストア アプリ プロジェクトを右クリックし、**[ストア]**、**[アプリケーションをストアと関連付ける]** の順にクリックします。

    ![][3]

    **アプリケーションを Windows ストアと関連付ける**ウィザードが表示されます。

5. ウィザードで **[サインイン]** をクリックし、Microsoft アカウントでログインします。

6. ステップ 2. で登録したアプリケーションをクリックし、**[次へ]**、**[関連付け]** の順にクリックします。

    ![][4]

    この操作により、必要な Windows ストア登録情報がアプリケーション マニフェストに追加されます。

7. (省略可能) Windows Phone ストア アプリ プロジェクトのステップ 4.～6. を繰り返します。

7. 新しいアプリケーションの Windows デベロッパー センター ページに戻り、**[サービス]** をクリックします。

    ![][5]

8. **[サービス]** ページで **[Microsoft Azure のモバイル サービス]** の **[ライブ サービス サイト]** をクリックします。

    ![][17]

9. **[アプリ設定]** タブで、**[クライアント シークレット]** と **[パッケージ セキュリティ ID (SID)]** の値をメモしておきます。

    ![][6]

    > [AZURE.NOTE]**セキュリティに関する注意**: クライアント シークレットとパッケージ SID は、重要なセキュリティ資格情報です。これらの値は、他のユーザーと共有したり、アプリケーションで配信したりしないでください。

##<a id="configure"></a>プッシュ要求を送信するようにモバイル アプリを構成する

1. [Azure プレビュー ポータル]にログオンします。**[参照]**、**[モバイル アプリ]** の順に選択し、アプリケーションをクリックしてから、[プッシュ通知サービス] をクリックします。

2. [Windows 通知サービス] で、**クライアント シークレット**と**パッケージ セキュリティ ID (SID)** を入力し、保存します。

App Service モバイル アプリが WNS と連携するように構成されました。

<!-- URLs. -->
[Azure プレビュー ポータル]: https://portal.azure.com/

##<a id="update-service"></a>サービスを更新してプッシュ通知を送信する

アプリでプッシュ通知が有効にされたので、プッシュ通知を送信するようにアプリ バックエンドを更新する必要があります。

1. Visual Studio でソリューションを開いて右クリックし、**[NuGet パッケージの管理]** をクリックします。

2. **Microsoft.Azure.NotificationHubs** を検索し、ソリューション内のすべてのプロジェクトに対して **[インストール]** をクリックします。

3. Visual Studio のソリューション エクスプローラーで、モバイル バックエンド プロジェクト内の **Controllers** フォルダーを展開します。TodoItemController.cs を開きます。ファイルの先頭に、次の `using` ステートメントを追加します。

        using System.Collections.Generic;
        using Microsoft.Azure.NotificationHubs;
        using Microsoft.Azure.Mobile.Server.Config;

4. **InsertAsync** の呼び出しの後の `PostTodoItem` メソッドに次のスニペットを追加します。

        // get Notification Hubs credentials associated with this Mobile App
        string notificationHubName = this.Services.Settings.NotificationHubName;
        string notificationHubConnection = this.Services.Settings.Connections[ServiceSettingsKeys.NotificationHubConnectionString].ConnectionString;

        // connect to notification hub
        NotificationHubClient Hub = NotificationHubClient.CreateClientFromConnectionString(notificationHubConnection, notificationHubName);

        // windows payload
        var windowsToastPayload = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" + item.Text + @"</text></binding></visual></toast>";

        await Hub.SendWindowsNativeNotificationAsync(windowsToastPayload);

    このコードは、このモバイル アプリに関連付けられている通知ハブに、todo 項目の挿入後にプッシュ通知を送信するよう指示します。


## <a name="publish-the-service"></a>モバイル バックエンドを Azure に発行する

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service-preview](../../includes/app-service-mobile-dotnet-backend-publish-service-preview.md)]

##<a id="update-service"></a>アプリケーションにプッシュ通知を追加する

1. Visual Studio でソリューションを開いて右クリックし、**[NuGet パッケージの管理]** をクリックします。 

    [NuGet パッケージの管理] ダイアログ ボックスが表示されます。

2. 管理対象用の App Service モバイル アプリ クライアント SDK を検索します。次に、**[インストール]** をクリックし、ソリューションのすべてのプロジェクトを選択して使用条件に同意します。

    これによって、Windows 向け Azure モバイル プッシュ ライブラリがダウンロード、インストールされ、すべてのプロジェクトにそのライブラリへの参照が追加されます。

3. **App.xaml.cs** プロジェクト ファイルを開き、次の `using` ステートメントを追加します。

        using Windows.Networking.PushNotifications;
        using Microsoft.WindowsAzure.MobileServices;

    ユニバーサル プロジェクトでは、このファイルは `<project_name>.Shared` フォルダーにあります。

4. 同じファイルで、次の **InitNotificationsAsync** メソッド定義を **App** クラスに追加します。
    
        private async void InitNotificationsAsync()
        {
            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
            
            await MobileService.GetPush().RegisterAsync(channel.Uri);
        }
    
    このコードにより、WNS からアプリケーションの ChannelURI が取得され、その ChannelURI が App Service モバイル アプリに登録されます。
    
5. **App.xaml.cs** 内で、**OnLaunched** イベント ハンドラーの先頭に、次に示す新しい **InitNotificationsAsync** メソッドへの呼び出しを追加します。

        InitNotificationsAsync();

    これにより、アプリケーションが起動されるたびに、有効期限付きの ChannelURI が登録されます。

6. ソリューション エクスプローラーで Windows ストア アプリの **Package.appxmanifest** をダブルクリックし、**[通知]** で **[トースト対応]** を **[はい]** に設定します。

    **[ファイル]** メニューの **[すべて保存]** をクリックします。

7. (省略可能) Windows Phone ストア アプリ プロジェクトの前のステップを繰り返します。

8. **F5** キーを押してアプリケーションを実行します。

これで、アプリケーションがトースト通知を受信する準備が整いました。

##<a id="test"></a>アプリケーションでプッシュ通知をテストする

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-windows-universal-test-push-preview](../../includes/app-service-mobile-dotnet-backend-windows-universal-test-push-preview.md)]

<!-- Anchors. -->

<!-- Images. -->
[0]: ./media/app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-push-preview/mobile-services-submit-win8-app.png
[1]: ./media/app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-push-preview/mobile-services-win8-app-name.png
[2]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-windows-universal-app.png
[3]: ./media/app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-push-preview/notification-hub-associate-win8-app.png
[4]: ./media/app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-push-preview/mobile-services-select-app-name.png
[5]: ./media/app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-push-preview/mobile-services-win8-edit-app.png
[6]: ./media/app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-push-preview/mobile-services-win8-app-push-auth.png
[7]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-from-portal.png
[17]: ./media/app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-push-preview/mobile-services-win8-edit2-app.png

<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582

<!---HONumber=July15_HO4-->