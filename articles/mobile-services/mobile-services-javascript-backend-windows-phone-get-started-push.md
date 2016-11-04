---
title: Mobile Services アプリケーション へのプッシュ通知の追加 (Windows Phone) | Microsoft Docs
description: Azure Mobile Services と Notification Hubs を使用して Windows Phone アプリにプッシュ通知を送信する方法について説明します。
services: mobile-services,notification-hubs
documentationcenter: windows
author: ggailey777
manager: dwrede
editor: ''

ms.service: mobile-services
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: dotnet
ms.topic: article
ms.date: 07/21/2016
ms.author: glenga

---
# Mobile Services アプリへのプッシュ通知の追加
[!INCLUDE [mobile-services-selector-get-started-push](../../includes/mobile-services-selector-get-started-push.md)]

## 概要
このトピックでは、Azure Mobile Services を使用して Windows Phone Silverlight アプリケーションにプッシュ通知を送信する方法について説明します。このチュートリアルでは、クイック スタート プロジェクトへの Azure Notification Hubs を使用したプッシュ通知を有効にします。完了すると、モバイル サービスは、レコードが挿入されるたびに Notification Hubs を使用してプッシュ通知を送信します。作成する通知ハブはモバイル サービスでは無料で、モバイル サービスから独立して管理することができ、他のアプリケーションおよびサービスで使用できます。

このチュートリアルは、TodoList サンプル アプリケーションに基づいています。このチュートリアルを開始する前に、「[既存のアプリケーションへの Mobile Services の追加]」を完了してプロジェクトをモバイル サービスに接続している必要があります。モバイル サービスが接続されていない場合は、プッシュ通知の追加ウィザードによってこの接続を作成できます。

> [!NOTE]
> Windows Phone 8.1 ストア アプリにプッシュ通知を送信するには、チュートリアル「[Windows ストア アプリ](mobile-services-javascript-backend-windows-universal-dotnet-get-started-push.md)」に従ってください。
> 
> 

## <a id="update-app"></a>アプリケーションを更新して通知に登録する
アプリケーションがプッシュ通知を受信するには、通知チャネルを登録する必要があります。

1. Visual Studio で App.xaml.cs ファイルを開き、次の `using` ステートメントを追加します。
   
        using Microsoft.Phone.Notification;
2. 次のコードを App.xaml.cs に追加します。
   
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
   
   > [!NOTE]
   > このチュートリアルでは、モバイル サービスにより、トースト通知がデバイスに送信されます。タイル通知を送信する場合は、チャネルの **BindToShellTile** メソッドを呼び出す必要があります。
   > 
   > 
3. App.xaml.cs 内で、**Application\_Launching** イベント ハンドラーの先頭に、次に示す新しい **AcquirePushChannel** メソッドへの呼び出しを追加します。
   
        AcquirePushChannel();
   
    これにより、ページが読み込まれるたびに登録が要求されるようになります。アプリケーションでは、この登録が常に最新の状態となるように、定期的な登録のみ行うことができます。
4. **F5** キーを押してアプリケーションを実行します。登録キーを示すポップアップ ダイアログが表示されます。
5. ソリューション エクスプローラーで、[**プロパティ**] を展開して WMAppManifest.xml ファイルを開き、[**機能**] タブをクリックして、**ID\_\_\_CAP\_\_\_PUSH\_NOTIFICATION** 機能がオンであることを確認します。
   
    ![VS で通知を有効にする](./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-app-enable-push-wp8.png)
   
    これにより、アプリケーションでトースト通知の使用が有効になります。

## <a id="update-scripts"></a>サーバー スクリプトを更新してプッシュ通知を送信する
最後に、通知を送信するためには、TodoItem テーブルの挿入操作に登録されているスクリプトを更新する必要があります。

1. **[TodoItem]** をクリックし、**[スクリプト]** タブをクリックして、**[挿入]** を選択します。
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
   
    これにより、非認証モードで MPNS に接続するモバイル サービスがプッシュ通知を送信できるようになります。
   
   > [!NOTE]
   > このチュートリアルでは、非認証モードで MPNS を使用します。このモードでは、MPNS はデバイス チャネルに送信できる通知の数を制限します。この制限を削除するには、**[アップロード]** をクリックして証明書を生成してアップロードし、その証明書を選択する必要があります。証明書を生成する方法の詳細については、「[Setting up an authenticated web service to send push notifications for Windows Phone (認証済み Web サービスを設定して Windows Phone のプッシュ通知を送信する)]」を参照してください。
   > 
   > 

## <a id="test"></a>アプリケーションでプッシュ通知をテストする
1. Visual Studio で、F5 キーを押してアプリケーションを実行します。
   
   > [!NOTE]
   > Windows Phone エミュレーターでテストする場合、401 認証エラー "RegistrationAuthorizationException" が発生する場合があります。これは、Windows Phone エミュレーターのホスト PC の時計との同期方法によって `RegisterNativeAsync()` 呼び出し中に発生します。セキュリティ トークンで拒否される場合があります。これを解決するには、テストする前にエミュレーターの時計を手動で設定します。
   > 
   > 
2. アプリケーションのテキストボックスに「hello push」と入力して、**[保存]** をクリックし、すぐに [開始] ボタンまたは [戻る] ボタンを押してアプリケーションを閉じます。
   
       ![アプリケーションにテキストを入力する](./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-quickstart-push3-wp8.png)
   
      これにより、追加された項目を保存するための挿入要求がモバイル サービスに送信されます。デバイスは **hello push** というトースト通知を受信します。
   
    ![受信したトースト通知](./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-quickstart-push5-wp8.png)
   
   > [!NOTE]
   > アプリケーションが開いている場合は通知を受信しません。アプリケーション起動中にトースト通知を受信するには、[ShellToastNotificationReceived](http://msdn.microsoft.com/library/windowsphone/develop/microsoft.phone.notification.httpnotificationchannel.shelltoastnotificationreceived.aspx) イベントを処理する必要があります。
   > 
   > 

## <a name="next-steps"> </a>次のステップ
このチュートリアルでは、Windows ストア アプリで Mobile Services および Notification Hubs を使用してプッシュ通知を送信できるようにするための基本について説明しました。次に、次のチュートリアルのいずれかを完了します。

* [登録者へのブロードキャスト通知の送信](../notification-hubs/notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md) <br/>ユーザーが興味のあるカテゴリに関してプッシュ通知を登録して、プッシュ通知を受信できるようにする方法について説明します。
* [登録者へのプラットフォーム固有の通知の送信](../notification-hubs/notification-hubs-aspnet-cross-platform-notification.md) <br/>テンプレートを使用して、バックエンドでプラットフォームに固有のペイロードを作成する必要なくモバイル サービスからプッシュ通知を送信する方法について説明します。

Mobile Services と Notification Hubs については次のトピックを参照してください。

* [Azure Notification Hubs - 診断ガイドライン](../notification-hubs/notification-hubs-push-notification-fixer.md) <br/>プッシュ通知の問題をトラブルシューティングする方法について説明します。
* [認証の使用] <br/>Mobile Services を使用して別のアカウントの種類のアプリケーションのユーザーを認証する方法について説明します。
* [Notification Hubs とは] <br/>Notification Hubs がすべての主要なクライアント プラットフォーム全体のアプリケーションに通知を配信するための動作を説明します。
* [Mobile Services .NET の使用方法の概念リファレンス] <br/>.NET で Mobile Services を使用する方法について説明します。
* [Mobile Services のサーバー スクリプト リファレンス] <br/>モバイル サービスでビジネス ロジックを実装する方法を説明します。

<!-- Anchors. -->

<!-- Images. -->


<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[既存のアプリケーションへの Mobile Services の追加]: mobile-services-windows-phone-get-started-data.md
[認証の使用]: mobile-services-windows-phone-get-started-users.md

[Setting up an authenticated web service to send push notifications for Windows Phone (認証済み Web サービスを設定して Windows Phone のプッシュ通知を送信する)]: http://msdn.microsoft.com/library/windowsphone/develop/ff941099(v=vs.105).aspx

[Mobile Services のサーバー スクリプト リファレンス]: http://go.microsoft.com/fwlink/?LinkId=262293
[Mobile Services .NET の使用方法の概念リファレンス]: mobile-services-windows-dotnet-how-to-use-client-library.md

[Notification Hubs とは]: ../notification-hubs/notification-hubs-overview.md

<!---HONumber=AcomDC_0727_2016-->