<properties 
	pageTitle="プッシュ通知の使用 (Windows ストア) | モバイル デベロッパー センター" 
	description="Azure Mobile Services と Notification Hubs を使用して Windows ストア アプリにプッシュ通知を送信する方法について説明します。" 
	services="mobile-services, notification-hubs" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="09/24/2014" 
	ms.author="glenga"/>


# Mobile Services アプリへのプッシュ通知の追加

[AZURE.INCLUDE [mobile-services-selector-get-started-push-legacy](../includes/mobile-services-selector-get-started-push-legacy.md)]

このトピックでは、Azure Mobile Services を使用して Windows Phone Silverlight アプリケーションにプッシュ通知を送信する方法について説明します。このチュートリアルでは、クイック スタート プロジェクトへの Azure 通知ハブを使用したプッシュ通知を有効にします。完了すると、モバイル サービスは、レコードが挿入されるたびに通知ハブを使用してプッシュ通知を送信します。作成する通知ハブはモバイル サービスでは無料で、モバイル サービスから独立して管理することができ、他のアプリケーションおよびサービスで使用できます。

このチュートリアルでは、プッシュ通知を有効にするための、次の基本的な手順について説明します。

1. [アプリケーションを更新して通知に登録する](#update-app)
2. [サーバー スクリプトを更新してプッシュ通知を送信する](#update-scripts)
3. [データを挿入してプッシュ通知を受信する](#test)

このチュートリアルは、モバイル サービスのクイック スタートに基づいています。このチュートリアルを開始する前に、「[モバイル サービスの使用]」または「[モバイル サービスでのデータの使用]」を完了してプロジェクトをモバイル サービスに接続している必要があります。モバイル サービスが接続されていない場合は、プッシュ通知の追加ウィザードによってこの接続が作成されます。 

>[AZURE.NOTE]Windows Phone 8.1 ストア アプリにプッシュ通知を送信するには、このチュートリアルの [Windows ストア アプリ バージョン] (mobile-services-javascript-backend-windows-store-dotnet-get-started-push.md) に従ってください。

##<a id="update-app"></a>アプリケーションを更新して通知に登録する

アプリケーションがプッシュ通知を受信するには、通知チャネルを登録する必要があります。

1. Visual Studio で App.xaml.cs ファイルを開き、次の  `using` ステートメントを追加します。

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

    このコードは Windows Phone 8.x "Silverlight" で使用される Microsoft Push Notification Service (MPNS) からアプリの ChannelURI を取得し、それをプッシュ通知用に登録します。

	>[AZURE.NOTE]このチュートリアルでは、モバイル サービスにより、トースト通知がデバイスに送信されます。タイル通知を送信する場合は、チャネルの **BindToShellTile** メソッドを呼び出す必要があります。

4. App.xaml.cs 内で、**Application_Launching** イベント ハンドラーの先頭に、次に示す新しい **AcquirePushChannel** メソッドへの呼び出しを追加します。

        AcquirePushChannel();

	これにより、ページが読み込まれるたびに登録が要求されるようになります。アプリケーションでは、この登録が常に最新の状態となるように、定期的な登録のみ行うことができます。 

5. **F5** キーを押してアプリケーションを実行します。登録キーを示すポップアップ ダイアログが表示されます。
  
6.	ソリューション エクスプローラーで、**[プロパティ]** を展開して WMAppManifest.xml ファイルを開き、**[機能]** タブをクリックして、**ID___CAP___PUSH_NOTIFICATION** 機能がオンであることを確認します。

   	![][1]

   	これにより、アプリケーションでトースト通知の使用が有効になります。 

##<a id="update-scripts"></a>サーバー スクリプトを更新してプッシュ通知を送信する

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

	>[AZURE.NOTE]古いモバイル サービスを使用してこのチュートリアルを実行する場合は、**[プッシュ]** タブの下に、**[拡張プッシュの有効化]** というリンクが表示される場合があります。このリンクをクリックすると、モバイル サービスが更新され、Notification Hubs と統合されます。この変更を元に戻すことはできません。運用環境のモバイル サービスで拡張プッシュ通知を有効にする方法の詳細については、<a href="http://go.microsoft.com/fwlink/p/?LinkId=391951">このガイダンス</a>を参照してください。

	![][11]

	これにより、非認証モードで MPNS に接続するモバイル サービスがプッシュ通知を送信できるようになります。

	>[AZURE.NOTE]このチュートリアルでは、非認証モードで MPNS を使用します。このモードでは、MPNS はデバイス チャネルに送信できる通知の数を制限します。この制限を削除するには、**[アップロード]** をクリックして証明書を生成してアップロードし、その証明書を選択する必要があります。証明書を生成する方法の詳細については、「[Setting up an authenticated web service to send push notifications for Windows Phone (認証済み Web サービスを設定して Windows Phone のプッシュ通知を送信する)]」を参照してください。

##<a id="test"></a>アプリケーションでプッシュ通知をテストする

1. Visual Studio で、F5 キーを押してアプリケーションを実行します。

    >[AZURE.NOTE] Windows Phone エミュレーターでテストする場合、401 認証エラー "RegistrationAuthorizationException" が発生する場合があります。これは、Windows Phone エミュレーターがホスト PC の時計と同期する方法が原因となり、 `RegisterNativeAsync()` 呼び出し中に発生します。セキュリティ トークンで拒否される場合があります。これを解決するには、テストする前にエミュレーターの時計を手動で設定します。

5. アプリケーションのテキストボックスに「hello push」と入力して、**[保存]** をクリックし、すぐに [開始] ボタンまたは [戻る] ボタンを押してアプリケーションを閉じます。

   	![][4]

  	これにより、追加された項目を保存するための挿入要求がモバイル サービスに送信されます。デバイスは **hello push** というトースト通知を受信します。

	![][5]

	>[AZURE.NOTE]アプリケーションが開いている場合は通知を受信しません。アプリケーションの実行中にトースト通知を受信するには、[ShellToastNotificationReceived](http://msdn.microsoft.com/library/windowsphone/develop/microsoft.phone.notification.httpnotificationchannel.shelltoastnotificationreceived(v=vs.105).aspx) イベントを処理する必要があります。



## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Windows ストア アプリで Mobile Services および Notification Hubs を使用してプッシュ通知を送信できるようにするための基本について説明しました。次に、次のチュートリアルのいずれかを完了します。

+ [認証されたユーザーへのプッシュ通知の送信]
	<br/>タグを使用してモバイル サービスから認証されたユーザーのみにプッシュ通知を送信する方法について説明します。

+ [通知ハブを使用したニュース速報の送信]
	<br/>ユーザーが興味のあるカテゴリに関してプッシュ通知を登録して、プッシュ通知を受信できるようにする方法について説明します。

<!---+ [通知ハブを使用したローカライズ ニュース速報の送信]
	<br/>テンプレートを使用して、バックエンドでプラットフォームに固有のペイロードを作成する必要なくモバイル サービスからプッシュ通知を送信する方法について説明します。
-->
Mobile Services と通知ハブについては次のトピックを参照してください。

* [データの使用]
  <br/>Mobile Services を使用してデータの格納およびクエリを実行する方法について説明します。

* [認証の使用]
  <br/>Mobile Services を使用して、別のアカウントの種類のアプリケーションのユーザーを認証する方法について説明します。

* [Notification Hubs とは]
  <br/>通知ハブがすべての主要なクライアント プラットフォーム全体のアプリケーションに通知を配信するための動作を説明します。

* [Mobile Services .NET の使用方法の概念リファレンス]
  <br/>.NET で Mobile Services を使用する方法について説明します。

* [モバイル サービスのサーバー スクリプト リファレンス]
  <br/>モバイル サービスでビジネス ロジックを実装する方法を説明します。

<!-- Anchors. -->

<!-- Images. -->
[1]: ./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-app-enable-push-wp8.png
[2]: ./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-quickstart-push1-wp8.png
[3]: ./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-quickstart-push2-wp8.png
[4]: ./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-quickstart-push3-wp8.png
[5]: ./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-quickstart-push5-wp8.png
[10]: ./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-insert-script-push2.png
[11]: ./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-push-tab.png

<!-- URLs. -->
[アプリケーションの提出に関するページ]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[マイ アプリケーション]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Windows 向け live SDK]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[モバイル サービスの使用]: /ja-jp/documentation/articles/mobile-services-windows-phone-get-started
[データの使用]: /ja-jp/documentation/articles/mobile-services-windows-phone-get-started-data
[認証の使用]: /ja-jp/documentation/articles/mobile-services-windows-phone-get-started-users

[認証済み Web サービスを設定して Windows Phone のプッシュ通知を送信する]に関するページ: http://msdn.microsoft.com/library/windowsphone/develop/ff941099(v=vs.105).aspx

[モバイル サービスのサーバー スクリプト リファレンス]: http://go.microsoft.com/fwlink/?LinkId=262293
[Mobile Services .NET の使用方法の概念リファレンス]: /ja-jp/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library


[認証されたユーザーへのプッシュ通知の送信]: /ja-jp/documentation/articles/mobile-services-javascript-backend-windows-phone-push-notifications-app-users/

[Notification Hubs とは]: /ja-jp/documentation/articles/notification-hubs-overview/
[通知ハブを使用したニュース速報の送信]: /ja-jp/documentation/articles/notification-hubs-windows-phone-send-breaking-news/
[通知ハブを使用したローカライズ ニュース速報の送信]: /ja-jp/documentation/articles/notification-hubs-windows-phone-send-localized-breaking-news/


<!--HONumber=42-->
