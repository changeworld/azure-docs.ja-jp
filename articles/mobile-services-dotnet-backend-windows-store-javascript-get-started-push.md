<properties pageTitle="Get started with push notifications (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure .Net Runtime Mobile Services and Notification Hubs to send push notifications to your Windows Store JavaScript app." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="wesmc" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="09/23/2014" ms.author="wesmc"></tags>

# モバイル サービスでのプッシュ通知の使用

[WACOM.INCLUDE [mobile-services-selector-get-started-push-legacy][mobile-services-selector-get-started-push-legacy]]

このトピックでは、Azure のモバイル サービスを .NET バックエンドで使用して Windows ストア アプリにプッシュ通知を送信する方法について説明します。このチュートリアルでは、クイック スタート プロジェクトへの Azure 通知ハブを使用したプッシュ通知を有効にします。完了すると、モバイル サービスは、レコードが挿入されるたびに通知ハブを使用して .NET バックエンドからプッシュ通知を送信します。作成する通知ハブはモバイル サービスでは無料で、モバイル サービスから独立して管理することができ、他のアプリケーションおよびサービスで使用できます。

> [WACOM.NOTE] このトピックでは、Windows Notification Service (WNS) を使用して Windows Store アプリのプッシュ通知を手動で設定する方法について説明します。Windows アプリ プロジェクトでは Visual Studio 2013 を使用して同じプッシュ通知を自動的に設定できます。詳細情報は、このチュートリアルの「[ユニバーサル Windows アプリ バージョン][ユニバーサル Windows アプリ バージョン]」を参照してください。

このチュートリアルでは、プッシュ通知を有効にするための、次の基本的な手順について説明します。

1.  [アプリケーションを WNS に登録し、モバイル サービスを構成する][アプリケーションを WNS に登録し、モバイル サービスを構成する]
2.  [アプリケーションを更新して通知に登録する][アプリケーションを更新して通知に登録する]
3.  [サーバーを更新してプッシュ通知を送信する][サーバーを更新してプッシュ通知を送信する]
4.  [ローカル テストのためにプッシュ通知を有効にする][ローカル テストのためにプッシュ通知を有効にする]
5.  [データを挿入してプッシュ通知を受信する][データを挿入してプッシュ通知を受信する]

このチュートリアルは、モバイル サービスのクイック スタートに基づいています。このチュートリアルを開始する前に、「[モバイル サービスの使用][モバイル サービスの使用]」または「[モバイル サービスでのデータの使用][モバイル サービスでのデータの使用]」を完了してプロジェクトをモバイル サービスに接続している必要があります。モバイル サービスが接続されていない場合は、プッシュ通知の追加ウィザードによってこの接続が作成されます。

## <span id="register"></span></a> アプリケーションを WNS に登録し、モバイル サービスを構成する

[WACOM.INCLUDE [mobile-services-notification-hubs-register-windows-store-app][mobile-services-notification-hubs-register-windows-store-app]]

これで、WNS および通知ハブと連携するようにモバイル サービスとアプリケーションが構成されました。次に、Windows ストア アプリを更新して通知に登録します。

## <span id="update-app"></span></a> アプリケーションを更新して通知に登録する

アプリケーションがプッシュ通知を受信するには、通知チャネルを登録する必要があります。

1.  default.js ファイルを開き、**MobileServiceClient** インスタンスを作成するコードの後に、次のコードを挿入します。このコードは、プッシュ通知チャネルを作成し、プッシュ通知を登録します。

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

2.  Visual Studio で Package.appxmanifest ファイルを開き、**[アプリケーション UI]** タブで **[トースト対応]** が **[はい]** に設定されていることを確認します。

    ![][]

    これにより、アプリケーションでトースト通知の使用が有効になります。ダウンロードしたクイック スタート プロジェクトでは、トースト通知があらかじめ有効になっています。

## <span id="update-server"></span></a> サーバーを更新してプッシュ通知を送信する

[WACOM.INCLUDE [mobile-services-dotnet-backend-update-server-push][mobile-services-dotnet-backend-update-server-push]]

## <span id="local-testing"></span></a> ローカル テストのためにプッシュ通知を有効にする

[WACOM.INCLUDE [mobile-services-dotnet-backend-configure-local-push][mobile-services-dotnet-backend-configure-local-push]]

## <span id="test"></span></a>アプリケーションでプッシュ通知をテストする

[WACOM.INCLUDE [mobile-services-windows-store-test-push][mobile-services-windows-store-test-push]]

## <a name="next-steps"> </a>次のステップ

このチュートリアルでは、Windows ストア アプリケーションを有効にしてモバイル サービスと通知ハブを使用してプッシュ通知を送信する基本事項について説明しました。次に、次のチュートリアルのいずれかを完了します。

-   [認証されたユーザーにプッシュ通知を送信する][認証されたユーザーにプッシュ通知を送信する]
    タグを使用してモバイル サービスから認証されたユーザーのみにプッシュ通知を送信する方法について説明します。

次のモバイル サービスと通知ハブのトピックの詳細を確認することをお勧めします。

-   [データの使用][モバイル サービスでのデータの使用]
    モバイル サービスを使用してデータの格納およびクエリの実行の方法について説明します。

-   [認証の使用][認証の使用]
    モバイル サービスを使用して別のアカウントの種類のアプリケーションのユーザーを認証する方法について説明します。

-   [Windows Azure 通知ハブとは][Windows Azure 通知ハブとは]
    通知ハブがすべての主要なクライアント プラットフォーム全体のアプリケーションに通知を配信するための動作を説明します。

-   [モバイル サービス HTML/JavaScript の使用方法の概念リファレンス][モバイル サービス HTML/JavaScript の使用方法の概念リファレンス]
    JavaScript アプリでモバイル サービスを使用する方法について説明します。

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [mobile-services-selector-get-started-push-legacy]: ../includes/mobile-services-selector-get-started-push-legacy.md
  [ユニバーサル Windows アプリ バージョン]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-universal-javascript-get-started-push
  [アプリケーションを WNS に登録し、モバイル サービスを構成する]: #register
  [アプリケーションを更新して通知に登録する]: #update-app
  [サーバーを更新してプッシュ通知を送信する]: #update-server
  [ローカル テストのためにプッシュ通知を有効にする]: #local-testing
  [データを挿入してプッシュ通知を受信する]: #test
  [モバイル サービスの使用]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started
  [モバイル サービスでのデータの使用]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data
  [mobile-services-notification-hubs-register-windows-store-app]: ../includes/mobile-services-notification-hubs-register-windows-store-app.md
  []: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/enable-toast.png
  [mobile-services-dotnet-backend-update-server-push]: ../includes/mobile-services-dotnet-backend-update-server-push.md
  [mobile-services-dotnet-backend-configure-local-push]: ../includes/mobile-services-dotnet-backend-configure-local-push.md
  [mobile-services-windows-store-test-push]: ../includes/mobile-services-windows-store-test-push.md
  [認証されたユーザーにプッシュ通知を送信する]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-push-notifications-app-users/
  [認証の使用]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users
  [Windows Azure 通知ハブとは]: /ja-jp/documentation/articles/notification-hubs-overview/
  [モバイル サービス HTML/JavaScript の使用方法の概念リファレンス]: /ja-jp/documentation/articles/mobile-services-html-how-to-use-client-library
