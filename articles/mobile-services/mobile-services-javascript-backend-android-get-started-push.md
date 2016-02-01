
<properties
	pageTitle="プッシュ通知の使用 (Android JavaScript) | Microsoft Azure"
	description="Azure モバイル サービスを使用して Android JavaScript アプリにプッシュ通知を送信する方法について説明します。"
	services="mobile-services, notification-hubs"
	documentationCenter="android"
	authors="RickSaling"
	writer="ricksal"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="article"
	ms.date="01/20/2016"
	ms.author="ricksal"/>


# Mobile Services Android アプリへのプッシュ通知の追加

[AZURE.INCLUDE [mobile-services-selector-get-started-push](../../includes/mobile-services-selector-get-started-push.md)]

## 概要

このトピックでは、Azure Mobile Services を使用して、Google Cloud Messaging (GCM) を使用する Android アプリにプッシュ通知を送信する方法について説明します。このチュートリアルの前提条件である、クイック スタート プロジェクトに、プッシュ通知を追加します。プッシュ通知はモバイル サービスに含まれている Azure Notification Hub で有効になっています。完了すると、モバイル サービスは、レコードが挿入されるたびにプッシュ通知を送信します。

## 前提条件

[AZURE.INCLUDE [mobile-services-android-prerequisites](../../includes/mobile-services-android-prerequisites.md)]

## サンプル コード
完成したソース コードを表示するには、[ここ](https://github.com/Azure/mobile-services-samples/tree/master/GettingStartedWithPush)に移動します。

## Google Cloud Messaging を有効にする

[AZURE.INCLUDE [mobile-services-enable-Google-cloud-messaging](../../includes/mobile-services-enable-google-cloud-messaging.md)]

## プッシュ要求を送信するようにモバイル サービスを構成する

[AZURE.INCLUDE [mobile-services-android-configure-push](../../includes/mobile-services-android-configure-push.md)]

## アプリケーションにプッシュ通知を追加する



次の手順は、Google Play サービスをインストールすることです。Google Cloud Messaging には、マニフェストの **minSdkVersion** プロパティが準拠する必要がある、開発およびテストに関する最小 API レベル要件があります。

古いデバイスを使用している場合は、[Google Play Services SDK のセットアップに関するページ]を参考に、どれだけ小さな値を設定できるか判断し、適切に設定してください。

###プロジェクトへの Google Play Services の追加

[AZURE.INCLUDE [Play サービスの追加](../../includes/mobile-services-add-google-play-services.md)]

###コードの追加

[AZURE.INCLUDE [mobile-services-android-getting-started-with-push](../../includes/mobile-services-android-getting-started-with-push.md)]


## Azure クラシック ポータルで登録されている挿入スクリプトを更新する

[AZURE.INCLUDE [mobile-services-javascript-backend-android-push-insert-script](../../includes/mobile-services-javascript-backend-android-push-insert-script.md)]


## アプリケーションでプッシュ通知をテストする

Android フォンを USB ケーブルで直接接続するか、エミュレーターで仮想デバイスを使用する方法により、アプリケーションをテストできます。

###テスト用の Android エミュレーターの設定

このアプリケーションをエミュレーターで実行する場合は、Google API をサポートしている Android Virtual Device (AVD) を使用してください。

1. ツールバーの右端から [Android Virtual Device Manager] を選択し、デバイスを選択して右側の [編集] アイコンをクリックします。

	![](./media/mobile-services-javascript-backend-android-get-started-push/mobile-services-android-virtual-device-manager.png)

2. デバイスの説明行で **[変更]** を選択して、**[Google APIs]** を選択してから [ok] をクリックします。

   	![](./media/mobile-services-javascript-backend-android-get-started-push/mobile-services-android-virtual-device-manager-edit.png)

	これで、AVD が Google API を使用するようになります。

###テストの実行

1. **[実行]** メニュー アイテムの **[Run app (アプリを実行)]** をクリックして、アプリケーションを開始します。

2. アプリケーションで、意味のあるテキスト (たとえば、「新しいモバイル サービス タスク」) を入力し、**[Add]** をクリックします。

  	![](./media/mobile-services-javascript-backend-android-get-started-push/mobile-quickstart-push1-android.png)

3. 画面の上端から下へスワイプし、通知を表示するためのデバイスの通知ドロアーを開きます。


これで、このチュートリアルは終了です。

## トラブルシューティング

### Android SDK バージョンの検証

[AZURE.INCLUDE [SDK の確認](../../includes/mobile-services-verify-android-sdk-version.md)]

## 次のステップ

<!---This tutorial demonstrated the basics of enabling an Android app to use Mobile Services and Notification Hubs to send push notifications. Next, consider completing the next tutorial, [Send push notifications to authenticated users], which shows how to use tags to send push notifications from a Mobile Service to only an authenticated user.

+ [Send broadcast notifications to subscribers]
	<br/>Learn how users can register and receive push notifications for categories they're interested in.

+ [Send template-based notifications to subscribers]
	<br/>Learn how to use templates to send push notifications from a Mobile Service, without having to craft platform-specific payloads in your back-end.
-->

Mobile Services と Notification Hubs については次のトピックを参照してください。

* [認証の使用] <br/>Mobile Services を使用して別のアカウントの種類のアプリケーションのユーザーを認証する方法について説明します。

* [Notification Hubs とは] <br/>Notification Hubs がすべての主要なクライアント プラットフォーム全体のアプリケーションに通知を配信するための動作を説明します。

* [Notification Hubs アプリケーションのデバッグ](http://go.microsoft.com/fwlink/p/?linkid=386630) </br>Notification Hubs ソリューションのトラブルシューティングとデバッグについて説明します。

* [Mobile Services 向け Android クライアント ライブラリの使用方法] <br/>Android と共に Mobile Services を使用する方法を説明します。

* [Mobile Services のサーバー スクリプト リファレンス] <br/>モバイル サービスでビジネス ロジックを実装する方法を説明します。


<!-- Anchors. -->
[Register your app for push notifications and configure Mobile Services]: #register
[Update the generated push notification code]: #update-scripts
[Insert data to receive notifications]: #test
[Next Steps]: #next-steps

<!-- Images. -->
[13]: ./media/mobile-services-windows-store-javascript-get-started-push/mobile-quickstart-push1.png
[14]: ./media/mobile-services-windows-store-javascript-get-started-push/mobile-quickstart-push2.png


<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Get started with Mobile Services]: mobile-services-android-get-started.md
[認証の使用]: mobile-services-android-get-started-users.md
[Get started with push notifications]: /develop/mobile/tutorials/get-started-with-push-js
[Push notifications to app users]: /develop/mobile/tutorials/push-notifications-to-users-js
[Authorize users with scripts]: /develop/mobile/tutorials/authorize-users-in-scripts-js
[JavaScript and HTML]: /develop/mobile/tutorials/get-started-with-push-js
[Google Play Services SDK のセットアップに関するページ]: http://go.microsoft.com/fwlink/?LinkId=389801
[Azure classic portal]: https://manage.windowsazure.com/
[Mobile Services 向け Android クライアント ライブラリの使用方法]: mobile-services-android-how-to-use-client-library.md

[gcm object]: http://go.microsoft.com/fwlink/p/?LinkId=282645

[Mobile Services のサーバー スクリプト リファレンス]: http://go.microsoft.com/fwlink/?LinkId=262293
[Notification Hubs とは]: ../notification-hubs-overview.md
[Send broadcast notifications to subscribers]: ../notification-hubs-android-send-breaking-news.md
[Send template-based notifications to subscribers]: ../notification-hubs-android-send-localized-breaking-news.md

<!---HONumber=AcomDC_0121_2016-->