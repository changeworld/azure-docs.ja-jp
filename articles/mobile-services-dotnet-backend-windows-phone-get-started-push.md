<properties 
	pageTitle=".NET ランタイム モバイル サービスを使用したプッシュ通知ハブの使用" 
	description="Notification Hubs と Azure Mobile Services を使用して Windows Phone アプリにプッシュ通知を送信する方法について説明します。" 
	services="mobile-services,notification-hubs" 
	documentationCenter="windows" 
	authors="wesmc7777" 
	writer="wesmc" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/23/2015" 
	ms.author="wesmc"/>

# Mobile Services アプリへのプッシュ通知の追加

[AZURE.INCLUDE [mobile-services-selector-get-started-push-legacy](../includes/mobile-services-selector-get-started-push-legacy.md)]

##概要

このトピックでは、Azure のモバイル サービスを .NET バックエンドで使用して Windows Phone Silverlight 8 アプリケーションにプッシュ通知を送信する方法について説明します。このチュートリアルでは、クイック スタート プロジェクトへの Azure 通知ハブを使用したプッシュ通知を有効にします。完了すると、モバイル サービスは、レコードが挿入されるたびに通知ハブを使用してプッシュ通知を送信します。作成する通知ハブはモバイル サービスでは無料で、モバイル サービスから独立して管理することができ、他のアプリケーションおよびサービスで使用できます。


このチュートリアルは、モバイル サービスのクイック スタートに基づいています。このチュートリアルを開始する前に、「[既存のアプリケーションへの Mobile Services の追加]」を完了してプロジェクトをモバイル サービスに接続している必要があります。

>[AZURE.NOTE]このチュートリアルは Windows Phone 8.1 "Silverlight" アプリケーションを対象にしています。Windows Phone 8.1 ストア アプリケーションを作成している場合は、このチュートリアルの [Windows ストア アプリ](mobile-services-dotnet-backend-windows-store-dotnet-get-started-push) バージョンを参照してください。Windows Phone Silverlight アプリケーションについての情報と Windows Phone ストア アプリケーションとの比較については [Windows Phone Silverlight 8.1 アプリケーション]を参照してください。

##アプリケーションを更新して通知に登録する

アプリケーションがプッシュ通知を受信するには、通知チャネルを登録する必要があります。

1. Visual Studio で App.xaml.cs ファイルを開き、次の `using` ステートメントを追加します。

        using Microsoft.Phone.Notification;

2. 次の `AcquirePushChannel` メソッドを `App` クラスに追加します。

        public static HttpNotificationChannel CurrentChannel { get; private set; }	
        
        private void AcquirePushChannel()
        {
            CurrentChannel = HttpNotificationChannel.Find("MyPushChannel");
            if (CurrentChannel == null)
            {
                CurrentChannel = new HttpNotificationChannel("MyPushChannel");
                CurrentChannel.Open();
                CurrentChannel.BindToShellToast();
            }
            CurrentChannel.ChannelUriUpdated +=
                new EventHandler<NotificationChannelUriEventArgs>(async (o, args) =>
                {
                    // Register for notifications using the new channel
                    System.Exception exception = null;
                    try
                    {
                        await MobileService.GetPush()
                            .RegisterNativeAsync(CurrentChannel.ChannelUri.ToString());
                    }
                    catch (System.Exception ex)
                    {
                        CurrentChannel.Close();
                        exception = ex;
                    }
                    if (exception != null)
                    {
                        Deployment.Current.Dispatcher.BeginInvoke(() =>
                        {
                            MessageBox.Show(exception.Message, 
                                            "Registering for Push Notifications",
                                            MessageBoxButton.OK);
                        });
                    }
            });
            CurrentChannel.ShellToastNotificationReceived += 
                new EventHandler<NotificationEventArgs>((o, args) =>
                {
                    string message = "";
                    foreach (string key in args.Collection.Keys)
                    {
                        message += key + " : " + args.Collection[key] + ", ";
                    }
                    Deployment.Current.Dispatcher.BeginInvoke(() =>
                    {
                        MessageBox.Show(message);
                    });
            });
        }

    このコードは、アプリケーションのチャネル URI が存在する場合はこれを取得します。存在しない場合は作成されます。チャネル URI が開かれ、トースト通知にバインドされます。チャネル URI が完全に開くと、`ChannelUriUpdated` メソッドのハンドラーが呼び出され、チャネルが登録されてプッシュ通知を受信します。登録が失敗した場合、アプリケーションの今後の実行で登録をもう一度試行できるようにチャネルは閉じられます。実行中にアプリケーションがプッシュ通知を受信して処理できるように `ShellToastNotificationReceived` ハンドラーがセットアップされます。
    
4. App.xaml.cs の `Application_Launching` イベント ハンドラーで、次の呼び出しを新しい `AcquirePushChannel` メソッドに追加します。

        AcquirePushChannel();

	これにより、アプリケーションが読み込まれるたびに登録が要求されるようになります。アプリケーションでは、この登録が常に最新の状態となるように、定期的な登録のみ行うことができます。

5. **F5** キーを押してアプリケーションを実行します。登録キーを示すポップアップ ダイアログが表示されます。
  
6. Visual Studio で Package.appxmanifest ファイルを開き、**[アプリケーション UI]** タブで **[トースト対応]** が **[はい]** に設定されていることを確認します。

   	![][1]

   	これにより、アプリケーションでトースト通知の使用が有効になります。

##サーバーを更新してプッシュ通知を送信する

1. Visual Studio のソリューション エクスプローラーで、モバイル サービス プロジェクト内の **Controllers** フォルダーを右クリックします。TodoItemController.cs を開き、次のコードを使用して `PostTodoItem` メソッドの定義を更新します。  

        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            TodoItem current = await InsertAsync(item);
            MpnsPushMessage message = new MpnsPushMessage();
            message.XmlPayload = "<?xml version="1.0" encoding="utf-8"?>" +
                "<wp:Notification xmlns:wp="WPNotification">" +
                   "<wp:Toast>" +
                        "<wp:Text1>" + item.Text + "</wp:Text1>" +
                   "</wp:Toast> " +
                "</wp:Notification>";

            try
            {
                var result = await Services.Push.SendAsync(message);
                Services.Log.Info(result.State.ToString());
            }
            catch (System.Exception ex)
            {
                Services.Log.Error(ex.Message, null, "Push.SendAsync Error");
            }
            return CreatedAtRoute("Tables", new { id = current.Id }, current);
        }

    Todo 項目を挿入した後、このコードは (挿入された項目のテキストを使用して) プッシュ通知を送信します。エラー イベントが発生した場合は、コードはエラー ログ エントリを追加します。そのエントリは、管理ポータル内でモバイル サービスに対応する **[ログ]** タブに表示されます。

2. [Azure の管理ポータル]にログオンし、**[モバイル サービス]** をクリックして、アプリケーションをクリックします。

3. **[プッシュ]** タブをクリックし、**[非認証プッシュ通知を有効にします]** をオンにして、**[保存]** をクリックします。

   	![][4]

	>[AZURE.NOTE]このチュートリアルでは、非認証モードで MPNS を使用します。このモードでは、MPNS はデバイス チャネルに送信できる通知の数を制限します。この制限を削除するには、<strong>[アップロード]</strong> をクリックして証明書を生成してアップロードし、その証明書を選択する必要があります。証明書を生成する方法の詳細については、「<a href="http://msdn.microsoft.com/library/windowsphone/develop/ff941099(v=vs.105).aspx">認証済み Web サービスを設定して Windows Phone のプッシュ通知を送信する</a>」を参照してください。

これにより、非認証モードで MPNS に接続するモバイル サービスがプッシュ通知を送信できるようになります。

##ローカル テストのためにプッシュ通知を有効にする

[AZURE.INCLUDE [mobile-services-dotnet-backend-configure-local-push](../includes/mobile-services-dotnet-backend-configure-local-push.md)]


##アプリケーションでプッシュ通知をテストする

1. Visual Studio で、F5 キーを押してアプリケーションを実行します。

    >[AZURE.NOTE]Windows Phone エミュレーターでテストする場合、401 認証エラー "RegistrationAuthorizationException" が発生する場合があります。これは、Windows Phone エミュレーターのホスト PC の時計との同期方法によって `RegisterNativeAsync()` 呼び出し中に発生します。セキュリティ トークンで拒否される場合があります。これを解決するには、テストする前にエミュレーターの時計を手動で設定します。

5. アプリケーションのテキストボックスに「hello push」と入力して、**[保存]** をクリックし、すぐに [開始] ボタンまたは [戻る] ボタンを押してアプリケーションを閉じます。

   	![][2]

  	これにより、追加された項目を保存するための挿入要求がモバイル サービスに送信されます。デバイスは **hello push** というトースト通知を受信します。

	![][5]

	>[AZURE.NOTE]アプリケーションが開いている場合は通知を受信しません。アプリケーション起動中にトースト通知を受信するには、[ShellToastNotificationReceived](http://msdn.microsoft.com/library/windowsphone/develop/microsoft.phone.notification.httpnotificationchannel.shelltoastnotificationreceived.aspx) イベントを処理する必要があります。

##次のステップ

このチュートリアルでは、Windows Phone アプリケーションを有効にしてモバイル サービスと通知ハブを使用してプッシュ通知を送信する基本事項について説明しました。次は、タグを使用して、プッシュ通知をモバイル サービスから認証ユーザーにのみ送信する方法を説明した、次のチュートリアル「[プッシュ通知を認証ユーザーに送信する]」を行うことをお勧めします。

<!--+ [Send push notifications to authenticated users]
	<br/>Learn how to use tags to send push notifications from a Mobile Service to only an authenticated user.

+ [Send broadcast notifications to subscribers]
	<br/>Learn how users can register and receive push notifications for categories they're interested in.
-->
次のモバイル サービスと通知ハブのトピックの詳細を確認することをお勧めします。

* [データの使用] <br/>Mobile Services を使用してデータの格納およびクエリを実行する方法について説明します。

* [認証の使用] <br/>Mobile Services を使用して別のアカウントの種類のアプリケーションのユーザーを認証する方法について説明します。

* [通知ハブとは] <br/>通知ハブがすべての主要なクライアント プラットフォーム全体のアプリケーションに通知を配信するための動作を説明します。

* [Notification Hubs アプリケーションのデバッグ](http://go.microsoft.com/fwlink/p/?linkid=386630) </br>Notification Hubs ソリューションのトラブルシューティングとデバッグについて説明します。

* [Mobile Services .NET の使用方法の概念リファレンス] <br/>.NET で Mobile Services を使用する方法について説明します。



<!-- Images. -->


[1]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-push/mobile-app-enable-push-wp8.png
[2]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-push/mobile-quickstart-push3-wp8.png
[3]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-push/mobile-quickstart-push4-wp8.png
[4]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-push/mobile-push-tab.png
[5]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-push/mobile-quickstart-push5-wp8.png

<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: mobile-services-dotnet-backend-windows-phone-get-started.md
[既存のアプリケーションへの Mobile Services の追加]: mobile-services-dotnet-backend-windows-phone-get-started-data.md
[認証の使用]: mobile-services-dotnet-backend-windows-phone-get-started-users.md

[プッシュ通知を認証ユーザーに送信する]: mobile-services-dotnet-backend-windows-phone-push-notifications-app-users.md

[通知ハブとは]: notification-hubs-overview.md
[Send broadcast notifications to subscribers]: notification-hubs-windows-phone-send-breaking-news.md
[Send template-based notifications to subscribers]: notification-hubs-windows-phone-send-localized-breaking-news.md


[Mobile Services .NET の使用方法の概念リファレンス]: mobile-services-html-how-to-use-client-library.md
[Windows Phone Silverlight 8.1 アプリケーション]: http://msdn.microsoft.com/library/windowsphone/develop/dn642082(v=vs.105).aspx
[Azure の管理ポータル]: https://manage.windowsazure.com/

<!--HONumber=54-->