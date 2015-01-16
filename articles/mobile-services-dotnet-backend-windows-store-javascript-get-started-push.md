<properties pageTitle="プッシュ通知の使用 (Windows ストア) |モバイル デベロッパー センター" metaKeywords="" description="Azure .Net ランタイム Mobile Services と Notification Hubs を使用して Windows ストア JavaScript アプリにプッシュ通知を送信する方法について説明します。" metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="wesmc" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="09/27/2014" ms.author="wesmc" />


# Mobile Services アプリへのプッシュ通知の追加

[WACOM.INCLUDE [mobile-services-selector-get-started-push-legacy](../includes/mobile-services-selector-get-started-push-legacy.md)]

このトピックでは、Azure Mobile Services を .NET バックエンドで使用して Windows ストア アプリにプッシュ通知を送信する方法について説明します。このチュートリアルでは、クイック スタート プロジェクトへの Azure 通知ハブを使用したプッシュ通知を有効にします。完了すると、モバイル サービスは、レコードが挿入されるたびに通知ハブを使用して .NET バックエンドからプッシュ通知を送信します。作成する Notification Hubs は Mobile Services では無料で、Mobile Services から独立して管理することができ、他のアプリケーションおよびサービスで使用できます。

>[WACOM.NOTE]このトピックでは、Windows Notification Service (WNS) を使用して Windows ストア アプリのプッシュ通知を手動で設定する方法について説明します。Windows アプリ プロジェクトでは Visual Studio 2013 を使用して同じプッシュ通知を自動的に設定できます。詳細については、このチュートリアルの[ユニバーサル Windows アプリ バージョン](/ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-universal-javascript-get-started-push)を参照してください。

このチュートリアルでは、プッシュ通知を有効にするための、次の基本的な手順について説明します。

1. [アプリケーションを WNS に登録し、モバイル サービスを構成する](#register)
2. [アプリケーションを更新して通知に登録する](#update-app)
3. [サーバーを更新してプッシュ通知を送信する](#update-server)
4. [ローカル テストのためにプッシュ通知を有効にする](#local-testing)
5. [データを挿入してプッシュ通知を受信する](#test)

このチュートリアルは、モバイル サービスのクイック スタートに基づいています。このチュートリアルを開始する前に、「[モバイル サービスの使用]」または「[モバイル サービスでのデータの使用]」を完了してプロジェクトをモバイル サービスに接続している必要があります。Mobile Services が接続されていない場合は、プッシュ通知の追加ウィザードによってこの接続が作成されます。 

##<a id="register"></a> アプリケーションを WNS に登録し、モバイル サービスを構成する

[WACOM.INCLUDE [mobile-services-notification-hubs-register-windows-store-app](../includes/mobile-services-notification-hubs-register-windows-store-app.md)]

これで、WNS および Notification Hubs と連携するようにモバイル サービスとアプリケーションが構成されました。次に、Windows ストア アプリを更新して通知に登録します。

##<a id="update-app"></a> アプリケーションを更新して通知に登録する

アプリケーションがプッシュ通知を受信するには、通知チャネルを登録する必要があります。

1. default.js ファイルを開き、**MobileServiceClient** インスタンスを作成するコードの後に、次のコードを挿入します。このコードは、プッシュ通知チャネルを作成し、プッシュ通知を登録します。

        // Request a push notification channel.
        Windows.Networking.PushNotifications
            .PushNotificationChannelManager
            .createPushNotificationChannelForApplicationAsync()
            .then(function (channel) {
                // Register for notifications using the new channel
                client.push.registerNative(channel.uri);
            }, function (error) {
                var message = "Registration failed: " + error.message;
                var dialog = new Windows.UI.Popups.MessageDialog(message);
                dialog.showAsync();
            });

    このコードにより、WNS からアプリケーションの ChannelURI が取得され、その ChannelURI がプッシュ通知に登録されます。登録に失敗した場合、メッセージ ダイアログにエラー メッセージが表示されます。

2. Visual Studio で、Package.appxmanifest ファイルを開き、**[アプリケーション UI]** タブで **[トースト対応]** が **[はい]** に設定されていることを確認します。

   	![][1]

   	これにより、アプリケーションでトースト通知の使用が有効になります。ダウンロードしたクイック スタート プロジェクトでは、トースト通知があらかじめ有効になっています。

##<a id="update-server"></a> サーバーを更新してプッシュ通知を送信する


[WACOM.INCLUDE [mobile-services-dotnet-backend-update-server-push](../includes/mobile-services-dotnet-backend-update-server-push.md)]

##<a id="local-testing"></a> ローカル テストのためにプッシュ通知を有効にする

[WACOM.INCLUDE [mobile-services-dotnet-backend-configure-local-push](../includes/mobile-services-dotnet-backend-configure-local-push.md)]

##<a id="test"></a> アプリケーションでプッシュ通知をテストする

[WACOM.INCLUDE [mobile-services-windows-store-test-push](../includes/mobile-services-windows-store-test-push.md)]

## <a name="next-steps"> </a>次のステップ

このチュートリアルでは、Windows ストア アプリで Mobile Services および Notification Hubs を使用してプッシュ通知を送信できるようにするための基本について説明しました。次に、次のチュートリアルのいずれかを完了します。

+ [認証されたユーザーへのプッシュ通知の送信]
	<br/>タグを使用して Mobile Service から認証されたユーザーのみにプッシュ通知を送信する方法について説明します。

次のモバイル サービスと通知ハブのトピックの詳細を確認することをお勧めします。

* [データの使用]
  <br/>Mobile Services を使用してデータの格納およびクエリを実行する方法について説明します。

* [認証の使用]
  <br/>Mobile Services を使用して別のアカウントの種類のアプリケーションのユーザーを認証する方法について説明します。

* [Windows Azure 通知ハブとは]
  <br/>Notification Hubs がすべての主要なクライアント プラットフォーム全体のアプリケーションに通知を配信するための動作を説明します。

* [通知ハブのデバッグ](http://go.microsoft.com/fwlink/p/?linkid=386630)
  </br>Notification Hubs ソリューションのトラブルシューティングとデバッグについて説明します。 

* [モバイル サービス HTML/JavaScript の使用方法の概念リファレンス]
  <br/>JavaScript アプリで Mobile Services を使用する方法について説明します。

<!-- Anchors. -->

<!-- Images. -->

[1]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/enable-toast.png
[2]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/mobile-quickstart-push1.png
[3]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/mobile-quickstart-push2.png


<!-- URLs. -->
[アプリケーションの提出に関するページ: ]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[マイ アプリケーション]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Windows 向け Live SDK]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[モバイル サービスの使用]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started
[データの使用]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data
[認証の使用]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users

[認証されたユーザーへのプッシュ通知の送信]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-push-notifications-app-users/

[Windows Azure 通知ハブとは]: /ja-jp/documentation/articles/notification-hubs-overview/

[モバイル サービス HTML/JavaScript の使用方法の概念リファレンス]: /ja-jp/documentation/articles/mobile-services-html-how-to-use-client-library
