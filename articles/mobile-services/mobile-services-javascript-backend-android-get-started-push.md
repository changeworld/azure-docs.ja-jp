
<properties 
	pageTitle="プッシュ通知の使用 (Android JavaScript) | モバイル デベロッパー センター" 
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
	ms.date="02/03/2015" 
	ms.author="ricksal"/>


# Mobile Services アプリへのプッシュ通知の追加

[AZURE.INCLUDE [mobile-services-selector-get-started-push](../../includes/mobile-services-selector-get-started-push.md)]

## 概要

このトピックでは、Azure モバイル サービスで Google Cloud Messaging (GCM) を使用して Android アプリにプッシュ通知を送信する方法について説明します。このチュートリアルでは、Azure Notification Hubs を使用したプッシュ通知をクイック スタート プロジェクトに追加します。完了すると、モバイル サービスは、レコードが挿入されるたびにプッシュ通知を送信します。

<!-- [AZURE.NOTE] If you would like to see the source code of the completed app, go <a href="https://github.com/RickSaling/mobile-services-samples/tree/androidStudio/GettingStartedWithPush/AndroidStudio" target="_blank">here</a>.
-->

> [AZURE.NOTE]このチュートリアルの Eclipse バージョンを確認する場合は、「[プッシュ通知の使用 (Eclipse)]」をご覧ください。


##前提条件

[AZURE.INCLUDE [mobile-services-android-prerequisites](../../includes/mobile-services-android-prerequisites.md)]

##<a id="register"></a>Google Cloud Messaging を有効にする

[AZURE.INCLUDE [mobile-services-enable-Google-cloud-messaging](../../includes/mobile-services-enable-Google-cloud-messaging.md)]

##<a id="configure"></a>プッシュ要求を送信するように Mobile Services を構成する

[AZURE.INCLUDE [mobile-services-android-configure-push](../../includes/mobile-services-android-configure-push.md)]

##<a id="add-push"></a>アプリケーションにプッシュ通知を追加する

###Android SDK バージョンの検証

[AZURE.INCLUDE [SDK の確認](../../includes/mobile-services-verify-android-sdk-version.md)]

次の手順は、Google Play サービスをインストールすることです。Google Cloud Messaging には、マニフェストの **minSdkVersion** プロパティが準拠する必要がある、開発およびテストに関する最小 API レベル要件があります。

古いデバイスを使用している場合は、[Google Play Services SDK のセットアップに関するページ]を参考に、どれだけ小さな値を設定できるか判断し、適切に設定してください。

###プロジェクトへの Google Play Services の追加

[AZURE.INCLUDE [Play サービスの追加](../../includes/mobile-services-add-Google-play-services.md)]

###コードの追加

[AZURE.INCLUDE [mobile-services-android-getting-started-with-push](../../includes/mobile-services-android-getting-started-with-push.md)]


##<a id="update-scripts"></a>管理ポータルで登録されている挿入スクリプトを更新する

[AZURE.INCLUDE [mobile-services-javascript-backend-android-push-insert-script](../../includes/mobile-services-javascript-backend-android-push-insert-script.md)]


##<a id="test"></a>アプリケーションでプッシュ通知をテストする

Android フォンを USB ケーブルで直接接続するか、エミュレーターで仮想デバイスを使用する方法により、アプリケーションをテストできます。

###テスト用の Android エミュレーターの設定

このアプリケーションをエミュレーターで実行する場合は、Google API をサポートしている Android Virtual Device (AVD) を使用してください。

1. Eclipse を再開し、Package Explorer でプロジェクトを右クリックして、**[Properties]**、**[Android]** の順にクリックし、**[Google APIs]** をオンにしてから **[OK]** をクリックします。

	![](./media/mobile-services-javascript-backend-android-get-started-push/mobile-services-import-android-properties.png)

  	これで、プロジェクトの対象が Google API になります。

2. **[Window]** で **[Android Virtual Device Manager]** を選択し、デバイスを選択してから **[Edit]** をクリックします。

	![](./media/mobile-services-javascript-backend-android-get-started-push/mobile-services-android-virtual-device-manager.png)

3. **[Target]** で **[Google APIs]** を選択し、[OK] をクリックします。

   	![](./media/mobile-services-javascript-backend-android-get-started-push/mobile-services-android-virtual-device-manager-edit.png)

	これで、AVD が Google API を使用するようになります。

###テストの実行

1. Eclipse の **[Run]** メニューの **[Run]** をクリックして、アプリケーションを開始します。

2. アプリケーションで、意味のあるテキスト (たとえば、「_新しいモバイル サービス タスク_」) を入力し、**[Add]** をクリックします。

  	![](./media/mobile-services-javascript-backend-android-get-started-push/mobile-quickstart-push1-android.png)

3. 画面の上端から下へスワイプし、通知を表示するためのデバイスの通知センターを開きます。


これで、このチュートリアルは終了です。


## <a name="next-steps"> </a>次のステップ

<!---This tutorial demonstrated the basics of enabling an Android app to use Mobile Services and Notification Hubs to send push notifications. Next, consider completing the next tutorial, [Send push notifications to authenticated users], which shows how to use tags to send push notifications from a Mobile Service to only an authenticated user.

+ [Send push notifications to authenticated users]
	<br/>Learn how to use tags to send push notifications from a Mobile Service to only an authenticated user.

+ [Send broadcast notifications to subscribers]
	<br/>Learn how users can register and receive push notifications for categories they're interested in.

+ [Send template-based notifications to subscribers]
	<br/>Learn how to use templates to send push notifications from a Mobile Service, without having to craft platform-specific payloads in your back-end.
-->

Mobile Services と通知ハブについては次のトピックを参照してください。

* [データの使用] <br/>Mobile Services を使用してデータの格納およびクエリを実行する方法について説明します。

* [認証の使用] <br/>Mobile Services を使用して別のアカウントの種類のアプリケーションのユーザーを認証する方法について説明します。

* [通知ハブとは] <br/>通知ハブがすべての主要なクライアント プラットフォーム全体のアプリケーションに通知を配信するための動作を説明します。

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
[プッシュ通知の使用 (Eclipse)]: mobile-services-javascript-backend-android-get-started-push-EC.md
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Get started with Mobile Services]: mobile-services-android-get-started.md
[データの使用]: mobile-services-android-get-started-data.md
[認証の使用]: mobile-services-android-get-started-users.md
[Get started with push notifications]: /develop/mobile/tutorials/get-started-with-push-js
[Push notifications to app users]: /develop/mobile/tutorials/push-notifications-to-users-js
[Authorize users with scripts]: /develop/mobile/tutorials/authorize-users-in-scripts-js
[JavaScript and HTML]: /develop/mobile/tutorials/get-started-with-push-js
[Google Play Services SDK のセットアップに関するページ]: http://go.microsoft.com/fwlink/?LinkId=389801
[Azure Management Portal]: https://manage.windowsazure.com/
[Mobile Services 向け Android クライアント ライブラリの使用方法]: mobile-services-android-how-to-use-client-library.md

[gcm object]: http://go.microsoft.com/fwlink/p/?LinkId=282645

[Mobile Services のサーバー スクリプト リファレンス]: http://go.microsoft.com/fwlink/?LinkId=262293

[Send push notifications to authenticated users]: mobile-services-javascript-backend-android-push-notifications-app-users.md

[通知ハブとは]: ../notification-hubs-overview.md
[Send broadcast notifications to subscribers]: ../notification-hubs-android-send-breaking-news.md
[Send template-based notifications to subscribers]: ../notification-hubs-android-send-localized-breaking-news.md

<!--HONumber=54--> 