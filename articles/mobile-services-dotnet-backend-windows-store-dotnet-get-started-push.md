<properties pageTitle=".NET ランタイム モバイル サービスを使用したプッシュ通知ハブの使用" metaKeywords="" description="Azure Mobile Services と Notification Hubs を使用して Windows ストア アプリにプッシュ通知を送信する方法について説明します。" metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="wesmc,ricksal" solutions="mobile" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/23/2014" ms.author="wesmc,ricksal" />


# Mobile Services アプリへのプッシュ通知の追加

[WACOM.INCLUDE [mobile-services-selector-get-started-push-legacy](../includes/mobile-services-selector-get-started-push-legacy.md)]

このトピックでは、Azure Mobile Services を .NET バックエンドで使用してユニバーサル Windows アプリにプッシュ通知を送信する方法について説明します。このチュートリアルでは、ユニバーサル Windows クイック スタート プロジェクトへの Azure 通知ハブを使用してプッシュ通知を有効にします。完了すると、モバイル サービスは、レコードが挿入されるたびに通知ハブを使用して .NET バックエンドからプッシュ通知を送信します。作成する Notification Hubs は Mobile Services では無料で、Mobile Services から独立して管理することができ、他のアプリケーションおよびサービスで使用できます。

>[WACOM.NOTE]このトピックでは、Windows Notification Service (WNS) を使用して Windows ストア アプリのプッシュ通知を手動で設定する方法について説明します。Windows アプリ プロジェクトでは Visual Studio 2013 を使用して同じプッシュ通知を自動的に設定できます。詳細については、このチュートリアルの[ユニバーサル Windows アプリ バージョン](/ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-universal-dotnet-get-started-push)を参照してください。

このチュートリアルでは、プッシュ通知を有効にするための、次の基本的な手順について説明します。

1. [アプリケーションを WNS に登録し、モバイル サービスを構成する](#register)
2. [アプリケーションを更新して通知に登録する](#update-app)
3. [サーバーを更新してプッシュ通知を送信する](#update-server)
4. [ローカル テストのためにプッシュ通知を有効にする](#local-testing)
3. [データを挿入してプッシュ通知を受信する](#test)

このチュートリアルは、モバイル サービスのクイック スタートに基づいています。このチュートリアルを開始する前に、「[モバイル サービスの使用]」または「[データの使用]」チュートリアルを完了して、プロジェクトをモバイル サービスに接続している必要があります。 

>[WACOM.NOTE]このトピックは Windows Phone ストア 8.1 アプリをサポートします。プッシュ通知を Windows Phone 8 または Windows Phone Silverlight 8.1 アプリに追加するには、このバージョンの[モバイル サービスでのプッシュ通知の使用に関するページ](/ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push)を参照してください。

##<a id="register"></a> アプリケーションを WNS に登録し、モバイル サービスを構成する

[WACOM.INCLUDE [mobile-services-notification-hubs-register-windows-store-app](../includes/mobile-services-notification-hubs-register-windows-store-app.md)]

これで、WNS および Notification Hubs と連携するようにモバイル サービスとアプリケーションが構成されました。次に、ユニバーサル Windows アプリを更新して通知に登録します。

##<a id="update-app"></a> アプリケーションを更新して通知に登録する

アプリケーションがプッシュ通知を受信するには、通知チャネルを登録する必要があります。

1. Visual Studio で App.xaml.cs ファイルを開き、次の `using` ステートメントを追加します。

        using Windows.Networking.PushNotifications;
		using Windows.UI.Popups;

2. 次の `InitNotificationAsync` メソッドを **App** クラスに追加して、プッシュ通知チャネルを作成し、プッシュ通知に登録します。 
	
        private async void InitNotificationsAsync()
        {
            // Request a push notification channel.
            var channel = await PushNotificationChannelManager
                .CreatePushNotificationChannelForApplicationAsync();

            // Register for notifications using the new channel
            System.Exception exception = null;
            try
            {
                await MobileService.GetPush().RegisterNativeAsync(channel.Uri);
            }
            catch (System.Exception ex)
            {
                exception = ex;
            }
            if (exception != null)
            {
                var dialog = new MessageDialog(exception.Message, "Registering Channel URI");
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
        }


    このコードにより、WNS からアプリケーションの ChannelURI が取得され、その ChannelURI がプッシュ通知に登録されます。
    
3. App.xaml.cs 内で、**OnLaunched** イベント ハンドラーの先頭に、次に示す新しい **InitNotificationsAsync** メソッドへの呼び出しを追加します。

        InitNotificationsAsync();

	これにより、ページが読み込まれるたびに登録が要求されるようになります。アプリケーションでは、この登録が常に最新の状態となるように、定期的な登録のみ行うことができます。 

4. Visual Studio で Package.appxmanifest ファイルを開き、**[アプリケーション UI]** タブで **[トースト対応]** が **[はい]** に設定されていることを確認します。ファイルを保存します。

   	![][1]

   	これにより、アプリケーションでトースト通知の使用が有効になります。 

##<a id="update-server"></a> サーバーを更新してプッシュ通知を送信する

[WACOM.INCLUDE [mobile-services-dotnet-backend-update-server-push](../includes/mobile-services-dotnet-backend-update-server-push.md)]

##<a id="local-testing"></a> ローカル テストのためにプッシュ通知を有効にする

[WACOM.INCLUDE [mobile-services-dotnet-backend-configure-local-push](../includes/mobile-services-dotnet-backend-configure-local-push.md)]

##<a id="test"></a> アプリケーションでプッシュ通知をテストする

[WACOM.INCLUDE [mobile-services-windows-store-test-push](../includes/mobile-services-windows-store-test-push.md)]

## <a name="next-steps"> </a>次のステップ

このチュートリアルでは、Windows ストア アプリで Mobile Services および Notification Hubs を使用してプッシュ通知を送信できるようにするための基本について説明しました。次は、タグを使用して、プッシュ通知を Mobile Services から認証ユーザーにのみ送信する方法を説明した、次のチュートリアル「[プッシュ通知を認証ユーザーに送信する]」を行うことをお勧めします。

<!--+ [Send push notifications to authenticated users]
	<br/>タグを使用して Mobile Service から認証されたユーザーのみにプッシュ通知を送信する方法について説明します。

+ [登録者へのブロードキャスト通知の送信]
	<br/>ユーザーが興味のあるカテゴリに関してプッシュ通知を登録して、プッシュ通知を受信できるようにする方法について説明します。

+ [登録者へのテンプレートベース通知の送信]
	<br/>テンプレートを使用して、バックエンドでプラットフォームに固有のペイロードを作成する必要なくモバイル サービスからプッシュ通知を送信する方法について説明します。
-->
Mobile Services と通知ハブについては次のトピックを参照してください。

* [データの使用]
  <br/>Mobile Services を使用してデータの格納およびクエリを実行する方法について説明します。

* [認証の使用]
  <br/>Mobile Services を使用して別のアカウントの種類のアプリケーションのユーザーを認証する方法について説明します。

* [Windows Azure 通知ハブとは]
  <br/>Notification Hubs がすべての主要なクライアント プラットフォーム全体のアプリケーションに通知を配信するための動作を説明します。

* [通知ハブのデバッグ](http://go.microsoft.com/fwlink/p/?linkid=386630)
  </br>Notification Hubs ソリューションのトラブルシューティングとデバッグについて説明します。 

* [モバイル サービス .NET の使用方法の概念リファレンス]
  <br/>.NET で Mobile Services を使用する方法について説明します。

<!-- Anchors. -->

<!-- Images. -->


[1]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/enable-toast.png
[2]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/mobile-quickstart-push1.png
[3]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/mobile-quickstart-push2.png


<!-- URLs. -->
[アプリケーションの提出に関するページ]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[マイ アプリケーション]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Windows 向け Live SDK]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services (Mobile Services の使用)]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started
[データの使用]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data
[認証の使用]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users

[Send push notifications to authenticated users (認証されたユーザーへのプッシュ通知の送信)]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-push-notifications-app-users/

[What are Notification Hubs? (Notification Hubs とは)]: /ja-jp/documentation/articles/notification-hubs-overview/
[登録者へのブロードキャスト通知の送信]: /ja-jp/documentation/articles/notification-hubs-windows-store-dotnet-send-breaking-news/
[登録者へのテンプレートベース通知の送信]: /ja-jp/documentation/articles/notification-hubs-windows-store-dotnet-send-localized-breaking-news/


[Mobile Services .NET の使用方法の概念リファレンス]: /ja-jp/documentation/articles/mobile-services-html-how-to-use-client-library
