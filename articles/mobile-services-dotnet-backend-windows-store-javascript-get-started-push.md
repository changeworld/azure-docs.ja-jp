<properties pageTitle="プッシュ通知の使用 (Windows ストア) | モバイル デベロッパー センター" metaKeywords="" description="Azure .NET ランタイム モバイル サービスおよび通知ハブを使用して Windows ストア JavaScript アプリケーションにプッシュ通知を送信する方法について説明します。" metaCanonical="" services="mobile" documentationCenter="Mobile" title="モバイル サービスでのプッシュ通知の使用" authors="wesmc" solutions="" manager="" editor="" />


# モバイル サービスでのプッシュ通知の使用

<div class="dev-center-tutorial-selector sublanding"><a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push" title="Windows ストア C#">Windows ストア C#</a><a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push" title="Windows ストア JavaScript" class="current">Windows ストア JavaScript</a><!--<a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push" title="Windows Phone">Windows Phone</a>--></div>

<div class="dev-center-tutorial-subselector"><a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push" title=".NET バックエンド" class="current">.NET バックエンド</a> | <a href="/ja-jp/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-get-started-push/"  title="JavaScript バックエンド">JavaScript バックエンド</a></div>

このトピックでは、Azure のモバイル サービスを .NET バックエンドで使用して Windows ストア アプリにプッシュ通知を送信する方法について説明します。このチュートリアルでは、クイック スタート プロジェクトへの Azure 通知ハブを使用したプッシュ通知を有効にします。完了すると、モバイル サービスは、レコードが挿入されるたびに通知ハブを使用して .NET バックエンドからプッシュ通知を送信します。作成する通知ハブはモバイル サービスでは無料で、モバイル サービスから独立して管理することができ、他のアプリケーションおよびサービスで使用できます。

>[WACOM.NOTE]通知ハブとのモバイル サービスの統合は、現在プレビューの段階で、Windows プラットフォームでのみ利用可能です。一方、この場合でも、「**通知ハブの使用**」([iOS](/ja-jp/documentation/articles/notification-hubs-ios-get-started)/[Android](/ja-jp/documentation/articles/notification-hubs-android-get-started)) で説明されているように、接続された通知ハブを使用して .NET バックエンド サービスから iOS および Android デバイスにプッシュ通知を送信することができます。

このチュートリアルでは、プッシュ通知を有効にするための、次の基本的な手順について説明します。

1. [アプリケーションを WNS に登録し、モバイル サービスを構成する](#register)
2. [アプリケーションを更新して通知に登録する](#update-app)
3. [サーバーを更新してプッシュ通知を送信する](#update-server)
3. [データを挿入してプッシュ通知を受信する](#test)

このチュートリアルは、モバイル サービスのクイック スタートに基づいています。このチュートリアルを開始する前に、「[モバイル サービスの使用]」または「[モバイル サービスでのデータの使用]」を完了してプロジェクトをモバイル サービスに接続している必要があります。モバイル サービスが接続されていない場合は、プッシュ通知の追加ウィザードによってこの接続が作成されます。

##<a id="register"></a> アプリケーションを WNS に登録し、モバイル サービスを構成する

[WACOM.INCLUDE [mobile-services-javascript-backend-register-windows-store-app](../includes/mobile-services-javascript-backend-register-windows-store-app.md)]

これで、WNS および通知ハブと連携するようにモバイル サービスとアプリケーションが構成されました。次に、Windows ストア アプリを更新して通知に登録します。

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

2. Visual Studio で Package.appxmanifest ファイルを開き、**[アプリケーション UI]** タブで **[トースト対応]** が **[はい]** に設定されていることを確認します。

   	![][1]

   	これにより、アプリケーションでトースト通知の使用が有効になります。ダウンロードしたクイック スタート プロジェクトでは、トースト通知があらかじめ有効になっています。

##<a id="update-server"></a> サーバーを更新してプッシュ通知を送信する


[WACOM.INCLUDE [mobile-services-dotnet-backend-update-server-push](../includes/mobile-services-dotnet-backend-update-server-push.md)]


##<a id="test"></a>アプリケーションでプッシュ通知をテストする

1. Visual Studio で、F5 キーを押してアプリケーションを実行します。

2. アプリケーションで、**[Insert a TodoItem]** にテキストを入力し、**[Save]** をクリックします。

   	![][2]

   	挿入が完了すると、アプリケーションは、WNS からプッシュ通知を受け取ります。

   	![][3]

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
  <br/>.NET ランタイム モバイル サービスを使用してデータの格納およびクエリを実行する方法について説明します。

* [認証の使用]
  <br/>.NET ランタイム モバイル サービスを使用して、別のアカウントの種類のアプリケーションのユーザーを認証する方法について説明します。

* [モバイル サービスのサーバー スクリプト リファレンス]
  <br/>サーバー スクリプトの登録および使用について説明します。

* [モバイル サービス .NET の使用方法の概念リファレンス]
  <br/>.NET でモバイル サービスを使用する方法について説明します

<!-- Anchors. -->

<!-- Images. -->

[1]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/enable-toast.png
[2]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/mobile-quickstart-push1.png
[3]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/mobile-quickstart-push2.png


<!-- URLs. -->
[アプリケーションの提出に関するページ: ]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[マイ アプリケーション]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Windows 向け Live SDK]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[モバイル サービスの使用]: /ja-jp/documentation/articles/mobile-services-windows-store-get-started
[データの使用]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data
[認証の使用]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users
[プッシュ通知の使用]: /ja-jp/documentation/articles/mobile-services-windows-store-javascript-get-started-push

[通知ハブの使用]: /ja-jp/manage/services/notification-hubs/getting-started-windows-dotnet/
[通知ハブとは]: /ja-jp/develop/net/how-to-guides/service-bus-notification-hubs/
[登録者への通知の送信]: /ja-jp/manage/services/notification-hubs/breaking-news-dotnet/
[ユーザーへの通知の送信]: /ja-jp/manage/services/notification-hubs/notify-users/
[ユーザーへのクロスプラットフォーム通知の送信]: /ja-jp/manage/services/notification-hubs/notify-users-xplat-mobile-services/
[モバイル サービスのサーバー スクリプト リファレンス]: http://go.microsoft.com/fwlink/?LinkId=262293
[モバイル サービス .NET の使用方法の概念リファレンス]: /ja-jp/documentation/articles/mobile-services-html-how-to-use-client-library

