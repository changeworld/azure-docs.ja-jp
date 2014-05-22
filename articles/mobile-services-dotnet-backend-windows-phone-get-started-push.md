<properties pageTitle=".NET ランタイム モバイル サービスを使用したプッシュ通知ハブの使用" metaKeywords="" description="Azure .NET ランタイム モバイル サービスおよび通知ハブを使用してプッシュ通知を Windows Phone アプリケーションに送信する方法について説明します。" metaCanonical="" services="mobile" documentationCenter="Mobile" title="モバイル サービスでのプッシュ通知の使用" authors="wesmc"  solutions="" writer="wesmc" manager="" editor=""  />


# モバイル サービスでのプッシュ通知の使用

<div class="dev-center-tutorial-selector sublanding"> 
	<a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push" title="Windows ストア C#">Windows ストア C#</a>
	<a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push" title="Windows ストア JavaScript">Windows ストア JavaScript</a>
	<a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push" title="Windows Phone" class="current">Windows Phone</a>
</div>

<div class="dev-center-tutorial-subselector">
	<a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push" title=".NET バックエンド" class="current">.NET バックエンド</a> |
	<a href="/ja-jp/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push/"  title="JavaScript バックエンド">JavaScript バックエンド</a>
</div>

このトピックでは、Azure .NET ランタイム モバイル サービスを使用して Windows Phone 8 アプリにプッシュ通知を送信する方法について説明します。
このチュートリアルでは、クイック スタート プロジェクトへの Azure 通知ハブを使用したプッシュ通知を有効にします。完了すると、.NET ランタイム モバイル サービスは、レコードが挿入されるたびに通知ハブを使用してプッシュ通知を送信します。作成する通知ハブはモバイル サービスでは無料で、モバイル サービスから独立して管理することができ、他のアプリケーションおよびサービスで使用できます。

このチュートリアルでは、プッシュ通知を有効にするための、次の基本的な手順について説明します。

1. [アプリケーションを更新して通知に登録する](#update-app)
3. [サーバーを更新してプッシュ通知を送信する](#update-server)
3. [データを挿入してプッシュ通知を受信する](#test)

このチュートリアルは、モバイル サービスのクイック スタートに基づいています。このチュートリアルを開始する前に、「[モバイル サービスの使用]」または「[モバイル サービスでのデータの使用]」を完了してプロジェクトをモバイル サービスに接続している必要があります。

##<a id="update-app"></a> アプリケーションを更新して通知に登録する

アプリケーションがプッシュ通知を受信するには、通知チャネルを登録する必要があります。

1. Visual Studio で App.xaml.cs ファイルを開き、次の `using` ステートメントを追加します。

        using Microsoft.Phone.Notification;

2. 次の `AcquirePushChannel` メソッドを `App` クラスに追加します。
	
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
    
4. App.xaml.cs の `Application_Launching` イベント ハンドラーで、次に示す新しい `AcquirePushChannel` メソッドの呼び出しを追加します。

        AcquirePushChannel();

	これにより、アプリケーションが読み込まれるたびに登録が要求されるようになります。アプリケーションでは、この登録が常に最新の状態となるように、定期的な登録のみ行うことができます。

5. **F5** キーを押してアプリケーションを実行します。登録キーを示すポップアップ ダイアログが表示されます。
  
6. Visual Studio で Package.appxmanifest ファイルを開き、**[アプリケーション UI]** タブで **[トースト対応]** が **[はい]** に設定されていることを確認します。

   	![][1]

   	これにより、アプリケーションでトースト通知の使用が有効になります。

##<a id="update-server"></a> サーバーを更新してプッシュ通知を送信する

[WACOM.INCLUDE [mobile-services-dotnet-backend-update-server-push](../includes/mobile-services-dotnet-backend-update-server-push.md)]

<ol start="2">
<li><p><a href=" https://manage.windowsazure.com/" target="_blank">Azure の管理ポータル</a>にログオンし、<strong>[モバイル サービス]</strong> をクリックして、アプリケーションをクリックします。</p></li>

<li><p><strong>[プッシュ]</strong> タブをクリックし、<strong>[非認証プッシュ通知を有効にします]</strong> をオンにして、<strong>[保存]</strong> をクリックします。</p>

</li>
</ol>

   ![][4]

>[WACOM.NOTE]このチュートリアルでは、非認証モードで MPNS を使用します。このモードでは、MPNS はデバイス チャネルに送信できる通知の数を制限します。この制限を削除するには、<strong>[アップロード]</strong> をクリックして証明書を生成してアップロードし、その証明書を選択する必要があります。証明書を生成する方法の詳細については、「<a href="http://msdn.microsoft.com/ja-jp/library/windowsphone/develop/ff941099(v=vs.105).aspx">Setting up an authenticated web service to send push notifications for Windows Phone (認証済み Web サービスを設定して Windows Phone のプッシュ通知を送信する)</a>」を参照してください。

これにより、非認証モードで MPNS に接続するモバイル サービスがプッシュ通知を送信できるようになります。

##<a id="test"></a>アプリケーションでプッシュ通知をテストする

1. Visual Studio で、F5 キーを押してアプリケーションを実行します。

5. そのアプリケーションで、テキスト ボックスに「hello push」というテキストを入力し、**[Save]** をクリックします。

   	![][2]

  	これにより、追加された項目を保存するための挿入要求がモバイル サービスに送信されます。アプリケーションは **hello push** というトースト通知を受信します。

## <a name="next-steps"> </a>次のステップ

このチュートリアルでは、Windows ストア アプリでモバイル サービスのデータを操作できるようにするための基本について説明しました。次は、このチュートリアルで作成した GetStartedWithData アプリケーションに基づく次のいずれかのチュートリアルを行うことをお勧めします。

+ [通知ハブの使用]
  <br/>Windows ストア アプリで通知ハブを活用する方法について説明します。

+ [登録者への通知の送信]
	<br/>ユーザーが興味のあるカテゴリに関してプッシュ通知を登録して、プッシュ通知を受信できるようにする方法について説明します。

+ [ユーザーへの通知の送信]
	<br/>モバイル サービスから任意のデバイスを使用している特定のユーザーにプッシュ通知を送信する方法について説明します。

+ [ユーザーへのクロスプラットフォーム通知の送信]
	<br/>テンプレートを使用して、バックエンドでプラットフォームに固有のペイロードを作成する必要なくモバイル サービスからプッシュ通知を送信する方法について説明します。

次のモバイル サービスのトピックの詳細を確認することをお勧めします。

* [モバイル サービスでのデータの使用]
  <br/>.NET ランタイム モバイル サービスを使用してデータの格納およびクエリを実行する方法について説明します。

* [認証の使用]
  <br/>.NET ランタイム モバイル サービスを使用して、別のアカウントの種類のアプリケーションのユーザーを認証する方法について説明します。

* [モバイル サービスのサーバー スクリプト リファレンス]
  <br/>サーバー スクリプトの登録および使用について説明します。

* [モバイル サービス .NET の使用方法の概念リファレンス]
  <br/>.NET でモバイル サービスを使用する方法について説明します

<!-- Anchors. -->

<!-- Images. -->


[1]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-push/mobile-app-enable-push-wp8.png
[2]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-push/mobile-quickstart-push3-wp8.png
[3]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-push/mobile-quickstart-push4-wp8.png
[4]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-push/mobile-push-tab.png

<!-- URLs. -->
[アプリケーションの提出に関するページ]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[モバイル サービスの使用]: /ja-jp/documentation/articles/mobile-services-windows-store-get-started
[モバイル サービスでのデータの使用]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data
[認証の使用]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users
[Get started with push notifications]: /ja-jp/documentation/articles/mobile-services-windows-store-dotnet-get-started-push

[通知ハブの使用]: /ja-jp/manage/services/notification-hubs/getting-started-windows-dotnet/
[通知ハブとは]: /ja-jp/develop/net/how-to-guides/service-bus-notification-hubs/
[登録者への通知の送信]: /ja-jp/manage/services/notification-hubs/breaking-news-dotnet/
[ユーザーへの通知の送信]: /ja-jp/manage/services/notification-hubs/notify-users/
[ユーザーへのクロスプラットフォーム通知の送信]: /ja-jp/manage/services/notification-hubs/notify-users-xplat-mobile-services/
[モバイル サービスのサーバー スクリプト リファレンス]: http://go.microsoft.com/fwlink/?LinkId=262293
[モバイル サービス .NET の使用方法の概念リファレンス]: /ja-jp/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library

