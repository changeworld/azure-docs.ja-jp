<properties pageTitle="Get started with push notifications (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services and Notification Hubs to send push notifications to your Windows Store app." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="glenga" solutions="" manager="dwrede" editor=""  />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="09/24/2014" ms.author="glenga"></tags>

# モバイル サービスでのプッシュ通知の使用

[WACOM.INCLUDE [mobile-services-selector-get-started-push-legacy][mobile-services-selector-get-started-push-legacy]]

このトピックでは、Azure モバイル サービスを使用して Windows Phone Silverlight アプリケーションにプッシュ通知を送信する方法について説明します。このチュートリアルでは、クイック スタート プロジェクトへの Azure 通知ハブを使用したプッシュ通知を有効にします。完了すると、モバイル サービスは、レコードが挿入されるたびに通知ハブを使用してプッシュ通知を送信します。作成する通知ハブはモバイル サービスでは無料で、モバイル サービスから独立して管理することができ、他のアプリケーションおよびサービスで使用できます。

> [WACOM.NOTE] このチュートリアルでは、モバイル サービスと通知ハブの統合を説明します。これはモバイル サービスからプッシュ通知を送信する方法になります。従来のプッシュを使用する古いモバイル サービスを使用していて、通知ハブを使用できるようにアップグレードしていない場合、このチュートリアルの一環として*アップグレードすることをお勧めします*。今回アップグレードしない場合は、チュートリアル「[プッシュ通知の使用 (従来)][プッシュ通知の使用 (従来)]」に従う必要があります。.

このチュートリアルでは、プッシュ通知を有効にするための、次の基本的な手順について説明します。

1.  [アプリケーションを更新して通知に登録する][アプリケーションを更新して通知に登録する]
2.  [サーバー スクリプトを更新してプッシュ通知を送信する][サーバー スクリプトを更新してプッシュ通知を送信する]
3.  [データを挿入してプッシュ通知を受信する][データを挿入してプッシュ通知を受信する]

このチュートリアルは、モバイル サービスのクイック スタートに基づいています。このチュートリアルを開始する前に、「[モバイル サービスの使用][モバイル サービスの使用]」または「[モバイル サービスでのデータの使用][モバイル サービスでのデータの使用]」を完了してプロジェクトをモバイル サービスに接続している必要があります。モバイル サービスが接続されていない場合は、プッシュ通知の追加ウィザードによってこの接続が作成されます。

> [WACOM.NOTE] Windows Phone 8.1 ストア アプリにプッシュ通知を送信するには、チュートリアル「[Windows ストア アプリ][Windows ストア アプリ]」に従ってください。

## <span id="update-app"></span></a> アプリケーションを更新して通知に登録する

アプリケーションがプッシュ通知を受信するには、通知チャネルを登録する必要があります。

1.  Visual Studio で App.xaml.cs ファイルを開き、次の `using` ステートメントを追加します。

        using Microsoft.Phone.Notification;

2.  次のコードを App.xaml.cs に追加します。

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

    > [WACOM.NOTE] このチュートリアルでは、モバイル サービスにより、トースト通知がデバイスに送信されます。タイル通知を送信する場合は、チャネルの **BindToShellTile** メソッドを呼び出す必要があります。

3.  App.xaml.cs 内で、**Application\_Launching** イベント ハンドラーの先頭に、次に示す新しい **AcquirePushChannel** メソッドへの呼び出しを追加します。

        AcquirePushChannel();

    これにより、ページが読み込まれるたびに登録が要求されるようになります。アプリケーションでは、この登録が常に最新の状態となるように、定期的な登録のみ行うことができます。

4.  **F5** キーを押してアプリケーションを実行します。登録キーを示すポップアップ ダイアログが表示されます。

5.  ソリューション エクスプローラーで、**[プロパティ]** を展開して WMAppManifest.xml ファイルを開き、**[機能]** タブをクリックして、**ID___CAP___PUSH_NOTIFICATION** 機能がオンであることを確認します。

    ![][]

    これにより、アプリケーションでトースト通知の使用が有効になります。

## <span id="update-scripts"></span></a> サーバー スクリプトを更新してプッシュ通知を送信する

最後に、通知を送信するためには、TodoItem テーブルの挿入操作に登録されているスクリプトを更新する必要があります。

1.  **[TodoItem]** をクリックし、**[スクリプト]** タブをクリックして、**[挿入]** を選択します。

    ![][1]

2.  insert 関数を次のコードに置き換え、**[保存]** をクリックします。

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

3.  **[プッシュ]** タブをクリックし、**[非認証プッシュ通知を有効にします]** をオンにして、**[保存]** をクリックします。

    > [WACOM.NOTE] 古いモバイル サービスを使用してこのチュートリアルを完了すると、**[プッシュ]** タブの下にある **[拡張プッシュの有効化]** にリンクが表示される場合があります。ここをクリックしてモバイル サービスをアップグレードして通知ハブを統合させます。この変更は元に戻すことはできません。運用環境のモバイル サービスで拡張プッシュ通知を有効にする方法についての詳細は、[このガイダンス][このガイダンス]を参照してください。

    ![][2]

    これにより、非認証モードで MPNS に接続するモバイル サービスがプッシュ通知を送信できるようになります。

    > [WACOM.NOTE] このチュートリアルでは、非認証モードで MPNS を使用します。このモードでは、MPNS はデバイス チャネルに送信できる通知の数を制限します。この制限を削除するには、**[アップロード]** をクリックして証明書を生成してアップロードし、その証明書を選択する必要があります。証明書を生成する方法の詳細については、「[Setting up an authenticated web service to send push notifications for Windows Phone (認証済み Web サービスを設定して Windows Phone のプッシュ通知を送信する)][Setting up an authenticated web service to send push notifications for Windows Phone (認証済み Web サービスを設定して Windows Phone のプッシュ通知を送信する)]」を参照してください。

## <span id="test"></span></a>アプリケーションでプッシュ通知をテストする

1.  Visual Studio で、F5 キーを押してアプリケーションを実行します。

    > [WACOM.NOTE] Windows Phone エミュレーターでテストする場合、401 認証エラー「RegistrationAuthorizationException」が発生する場合があります。これは、Windows Phone エミュレーターのホスト PC の時計との同期方法によって `RegisterNativeAsync()` 呼び出し中に発生します。セキュリティ トークンで拒否される場合があります。これを解決するには、テストする前にエミュレーターの時計を手動で設定します。

2.  アプリケーションのテキストボックスに「hello push」と入力して、**[保存]** をクリックし、すぐに [開始] ボタンまたは [戻る] ボタンを押してアプリケーションを閉じます。

    ![][3]

    これにより、追加された項目を保存するための挿入要求がモバイル サービスに送信されます。デバイスは **hello push** というトースト通知を受信します。

    ![][4]

    > [WACOM.NOTE] アプリケーションが開いている場合は通知を受信しません。アプリケーション起動中にトースト通知を受信するには、[ShellToastNotificationReceived][ShellToastNotificationReceived] イベントを処理する必要があります。

## <a name="next-steps"> </a>次のステップ

このチュートリアルでは、Windows ストア アプリケーションを有効にしてモバイル サービスと通知ハブを使用してプッシュ通知を送信する基本事項について説明しました。次に、次のチュートリアルのいずれかを完了します。

-   [認証されたユーザーにプッシュ通知を送信する][認証されたユーザーにプッシュ通知を送信する]
    タグを使用してモバイル サービスから認証されたユーザーのみにプッシュ通知を送信する方法について説明します。

-   [登録者への通知の送信][登録者への通知の送信]
    ユーザーが興味のあるカテゴリに関してプッシュ通知を登録して、プッシュ通知を受信できるようにする方法について説明します。

<!---+ [Send template-based notifications to subscribers]     <br/>Learn how to use templates to send push notifications from a Mobile Service, without having to craft platform-specific payloads in your back-end. -->

Mobile Services と通知ハブについては次のトピックを参照してください。

-   [データの使用][モバイル サービスでのデータの使用]
    モバイル サービスを使用してデータの格納およびクエリの実行の方法について説明します。

-   [認証の使用][認証の使用]
    モバイル サービスを使用して別のアカウントの種類のアプリケーションのユーザーを認証する方法について説明します。

-   [Windows Azure 通知ハブとは][Windows Azure 通知ハブとは]
    通知ハブがすべての主要なクライアント プラットフォーム全体のアプリケーションに通知を配信するための動作を説明します。

-   [モバイル サービス .NET の使用方法の概念リファレンス][モバイル サービス .NET の使用方法の概念リファレンス]
    .NET で Mobile Services を使用する方法について説明します。

-   [モバイル サービスのサーバー スクリプト リファレンス][モバイル サービスのサーバー スクリプト リファレンス]
    モバイル サービスでビジネス ロジックを実装する方法を説明します。

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [mobile-services-selector-get-started-push-legacy]: ../includes/mobile-services-selector-get-started-push-legacy.md
  [プッシュ通知の使用 (従来)]: /ja-jp/documentation/articles/mobile-services-windows-phone-get-started-push/
  [アプリケーションを更新して通知に登録する]: #update-app
  [サーバー スクリプトを更新してプッシュ通知を送信する]: #update-scripts
  [データを挿入してプッシュ通知を受信する]: #test
  [モバイル サービスの使用]: /ja-jp/documentation/articles/mobile-services-windows-phone-get-started
  [モバイル サービスでのデータの使用]: /ja-jp/documentation/articles/mobile-services-windows-phone-get-started-data
  [Windows ストア アプリ]: /ja-jp/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push
  []: ./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-app-enable-push-wp8.png
  [1]: ./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-insert-script-push2.png
  [このガイダンス]: http://go.microsoft.com/fwlink/p/?LinkId=391951
  [2]: ./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-push-tab.png
  [Setting up an authenticated web service to send push notifications for Windows Phone (認証済み Web サービスを設定して Windows Phone のプッシュ通知を送信する)]: http://msdn.microsoft.com/ja-jp/library/windowsphone/develop/ff941099(v=vs.105).aspx
  [3]: ./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-quickstart-push3-wp8.png
  [4]: ./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-quickstart-push5-wp8.png
  [ShellToastNotificationReceived]: http://msdn.microsoft.com/ja-jp/library/windowsphone/develop/microsoft.phone.notification.httpnotificationchannel.shelltoastnotificationreceived(v=vs.105).aspx
  [認証されたユーザーにプッシュ通知を送信する]: /ja-jp/documentation/articles/mobile-services-javascript-backend-windows-phone-push-notifications-app-users/
  [登録者への通知の送信]: /ja-jp/documentation/articles/notification-hubs-windows-phone-send-breaking-news/
  [認証の使用]: /ja-jp/documentation/articles/mobile-services-windows-phone-get-started-users
  [Windows Azure 通知ハブとは]: /ja-jp/documentation/articles/notification-hubs-overview/
  [モバイル サービス .NET の使用方法の概念リファレンス]: /ja-jp/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library
  [モバイル サービスのサーバー スクリプト リファレンス]: http://go.microsoft.com/fwlink/?LinkId=262293
