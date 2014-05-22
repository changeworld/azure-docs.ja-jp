<properties pageTitle="プッシュ通知の使用 (Windows ストア) | モバイル デベロッパー センター" metaKeywords="" description="Azure のモバイル サービスおよび通知ハブを使用して Windows ストア アプリにプッシュ通知を送信する方法について説明します。" metaCanonical="" services="mobile" documentationCenter="Mobile" title="モバイル サービスでのプッシュ通知の使用" authors="glenga" solutions="" manager="" editor="" />


# モバイル サービスでのプッシュ通知の使用

<div class="dev-center-tutorial-selector sublanding"><a href="/ja-jp/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push" title="Windows ストア C#">Windows ストア C#</a><a href="/ja-jp/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-get-started-push" title="Windows ストア JavaScript">Windows ストア JavaScript</a><a href="/ja-jp/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push" title="Windows Phone" class="current">Windows Phone</a><a href="/ja-jp/documentation/articles/mobile-services-ios-get-started-push" title="iOS">iOS</a><a href="/ja-jp/documentation/articles/mobile-services-javascript-backend-android-get-started-push" title="Android">Android</a></div>

<div class="dev-center-tutorial-subselector"><a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push/" title=".NET バックエンド">.NET バックエンド</a> | <a href="/ja-jp/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push/"  title="JavaScript バックエンド" class="current">JavaScript バックエンド</a></div>

このトピックでは、Azure モバイル サービスを使用して Windows ストア アプリケーションにプッシュ通知を送信する方法について説明します。
このチュートリアルでは、クイック スタート プロジェクトへの Azure 通知ハブを使用したプッシュ通知を有効にします。完了すると、モバイル サービスは、レコードが挿入されるたびに通知ハブを使用してプッシュ通知を送信します。作成する通知ハブはモバイル サービスでは無料で、モバイル サービスから独立して管理することができ、他のアプリケーションおよびサービスで使用できます。

>[WACOM.NOTE]このチュートリアルでは、現在プレビュー段階にある、通知ハブとのモバイル サービスの統合を示します。既定では、通知ハブを使用したプッシュ通知の送信は JavaScript のバックエンドからは有効になっていません。新しい通知ハブを作成すると、統合プロセスを元に戻すことはできません。iOS および Android 用のプッシュ通知のみが、[トピックのこのバージョン](/ja-jp/documentation/articles/mobile-services-windows-phone-get-started-push/) で説明している既定のプッシュ サポートを使用することによって現在使用可能です。

このチュートリアルでは、プッシュ通知を有効にするための、次の基本的な手順について説明します。

1. [アプリケーションを更新して通知に登録する](#update-app)
2. [サーバー スクリプトを更新してプッシュ通知を送信する](#update-scripts)
3. [データを挿入してプッシュ通知を受信する](#test)

このチュートリアルは、モバイル サービスのクイック スタートに基づいています。このチュートリアルを開始する前に、「[モバイル サービスの使用]」または「[モバイル サービスでのデータの使用]」を完了してプロジェクトをモバイル サービスに接続している必要があります。モバイル サービスが接続されていない場合は、プッシュ通知の追加ウィザードによってこの接続が作成されます。

##<a id="update-app"></a> アプリケーションを更新して通知に登録する

アプリケーションがプッシュ通知を受信するには、通知チャネルを登録する必要があります。

1. Visual Studio で App.xaml.cs ファイルを開き、次の `using` ステートメントを追加します。

        using Microsoft.Phone.Notification;

3. 次のコードを App.xaml.cs に追加します。
	
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
                    await MobileService.GetPush()
                        .RegisterNativeAsync(CurrentChannel.ChannelUri.ToString());
                });
        }

    このコードにより、WNS からアプリケーションの ChannelURI が取得され、その ChannelURI がプッシュ通知に登録されます。

	>[WACOM.NOTE]このチュートリアルでは、モバイル サービスにより、トースト通知がデバイスに送信されます。タイル通知を送信する場合は、チャネルの **BindToShellTile** メソッドを呼び出す必要があります。

4. App.xaml.cs 内で、**Application_Launching** イベント ハンドラーの先頭に、次に示す新しい **AcquirePushChannel** メソッドへの呼び出しを追加します。

        AcquirePushChannel();

	これにより、ページが読み込まれるたびに登録が要求されるようになります。アプリケーションでは、この登録が常に最新の状態となるように、定期的な登録のみ行うことができます。

5. **F5** キーを押してアプリケーションを実行します。登録キーを示すポップアップ ダイアログが表示されます。
  
6.	ソリューション エクスプローラーで、**[プロパティ]** を展開して WMAppManifest.xml ファイルを開き、**[機能]** タブをクリックして、**ID___CAP___PUSH_NOTIFICATION** 機能がオンであることを確認します。

   	![][1]

   	これにより、アプリケーションでトースト通知の使用が有効になります。

##<a id="update-scripts"></a> サーバー スクリプトを更新してプッシュ通知を送信する

最後に、通知を送信するためには、TodoItem テーブルの挿入操作に登録されているスクリプトを更新する必要があります。

1. **[TodoItem]** をクリックし、**[スクリプト]** タブをクリックして、**[挿入]** を選択します。

   	![][10]

2. insert 関数を次のコードに置き換え、**[保存]** をクリックします。

		function insert(item, user, request) {
		// Define a payload for the Windows Phone toast notification.
		var payload = '<?xml version="1.0" encoding="utf-8"?>' +
		    '<wp:Notification xmlns:wp="WPNotification"><wp:Toast>' +
		    '<wp:Text1>New Item</wp:Text1><wp:Text2>' + item.text + 
		    '</wp:Text2></wp:Toast></wp:Notification>';
		
		request.execute({
		    success: function() {
		        // If the insert succeeds, send a notification.
		    	push.mpns.send(null, payload, 'toast', 22, {
		            success: function(pushResponse) {
		                console.log("Sent push:", pushResponse);
						request.respond();
		                },              
		                error: function (pushResponse) {
		                    console.log("Error Sending push:", pushResponse);
							request.respond(500, { error: pushResponse });
		                    }
		                });
		            }
		        });      
		}

	この insert スクリプトによって、挿入が成功した後、すべての Windows Phone アプリケーション登録にプッシュ通知が (挿入された項目のテキストと共に) 送信されます。

3. **[プッシュ]** タブをクリックし、**[非認証プッシュ通知を有効にします]** をオンにして、**[保存]** をクリックします。

	![][11]

	これにより、非認証モードで MPNS に接続するモバイル サービスがプッシュ通知を送信できるようになります。

	>[WACOM.NOTE]このチュートリアルでは、非認証モードで MPNS を使用します。このモードでは、MPNS はデバイス チャネルに送信できる通知の数を制限します。この制限を削除するには、**[アップロード]** をクリックして証明書を生成してアップロードし、その証明書を選択する必要があります。証明書を生成する方法の詳細については、「[Setting up an authenticated web service to send push notifications for Windows Phone (認証済み Web サービスを設定して Windows Phone のプッシュ通知を送信する)]」を参照してください。

##<a id="test"></a>アプリケーションでプッシュ通知をテストする

1. Visual Studio で、F5 キーを押してアプリケーションを実行します。

5. そのアプリケーションで、テキスト ボックスに「hello push」というテキストを入力し、**[Save]** をクリックします。

   	![][4]

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

* [データの使用]
  <br/>モバイル サービスを使用してデータの格納およびクエリを実行する方法について説明します。

* [認証の使用]
  <br/>Windows アカウントを使用してアプリケーションのユーザーを認証する方法について説明します。

* [モバイル サービスのサーバー スクリプト リファレンス]
  <br/>サーバー スクリプトの登録および使用について説明します。

* [モバイル サービス .NET の使用方法の概念リファレンス]
  <br/>.NET でモバイル サービスを使用する方法について説明します

<!-- Anchors. -->

<!-- Images. -->
[1]: ./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-app-enable-push-wp8.png
[2]: ./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-quickstart-push1-wp8.png
[3]: ./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-quickstart-push2-wp8.png
[4]: ./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-quickstart-push3-wp8.png
[5]: ./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-quickstart-push4-wp8.png
[10]: ./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-insert-script-push2.png
[11]: ./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-push-tab.png

<!-- URLs. -->
[アプリケーションの提出に関するページ: ]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[マイ アプリケーション]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Windows 向け Live SDK]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[モバイル サービスの使用]: /ja-jp/documentation/articles/mobile-services-windows-phone-get-started
[モバイル サービスでのデータの使用]: /ja-jp/documentation/articles/mobile-services-windows-phone-get-started-data
[認証の使用]: /ja-jp/documentation/articles/mobile-services-windows-phone-get-started-users
[プッシュ通知の使用]: /ja-jp/documentation/articles/mobile-services-windows-phone-get-started-push

[通知ハブの使用]: /ja-jp/manage/services/notification-hubs/getting-started-windows-dotnet/
[Azure 通知ハブとは]: /ja-jp/develop/net/how-to-guides/service-bus-notification-hubs/
[登録者への通知の送信]: /ja-jp/manage/services/notification-hubs/breaking-news-dotnet/
[ユーザーへの通知の送信]: /ja-jp/manage/services/notification-hubs/notify-users/
[ユーザーへのクロスプラットフォーム通知の送信]: /ja-jp/manage/services/notification-hubs/notify-users-xplat-mobile-services/
[モバイル サービスのサーバー スクリプト リファレンス]: http://go.microsoft.com/fwlink/?LinkId=262293
[モバイル サービス .NET の使用方法の概念リファレンス]: /ja-jp/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library
[Setting up an authenticated web service to send push notifications for Windows Phone (認証済み Web サービスを設定して Windows Phone のプッシュ通知を送信する)]: http://msdn.microsoft.com/ja-jp/library/windowsphone/develop/ff941099(v=vs.105).aspx

