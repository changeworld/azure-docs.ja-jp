<properties
	pageTitle="アプリ (iOS) へのプッシュ通知の追加 | JavaScript バックエンド"
	description="Azure Mobile Services を使用して iOS アプリにプッシュ通知を送信する方法について説明します。"
	services="mobile-services,notification-hubs"
	documentationCenter="ios"
	manager="dwrede"
	editor=""
	authors="krisragh"/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="07/21/2016"
	ms.author="krisragh"/>

# iOS アプリと JavaScript バックエンドにプッシュ通知を追加する

[AZURE.INCLUDE [mobile-services-selector-get-started-push](../../includes/mobile-services-selector-get-started-push.md)]

&nbsp;

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]
> このトピックの Mobile Apps バージョンについては、「[Add Push Notifications to your iOS App (iOS アプリにプッシュ通知を追加する)](../app-service-mobile/app-service-mobile-ios-get-started-push.md)」を参照してください。

このトピックでは、[クイックスタート プロジェクト](mobile-services-ios-get-started.md)にプッシュ通知を追加して、レコードが挿入されるたびにモバイル サービスがプッシュ通知を送信できるようにします。先に[Mobile Services の使用]を完了しておく必要があります。

> [AZURE.NOTE]  [iOS シミュレーターはプッシュ通知をサポートしない](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator.html)ため、物理的な iOS デバイスを使用する必要があります。さらに、有料の[Apple Developer Program メンバーシップ](https://developer.apple.com/programs/ios/)にサインアップする必要があります。

[AZURE.INCLUDE [Apple プッシュ通知を有効にする](../../includes/enable-apple-push-notifications.md)]


## <a id="configure"></a>プッシュ通知を送信するように Azure を構成する

[AZURE.INCLUDE [Azure Mobile Services でプッシュ通知を構成する](../../includes/mobile-services-apns-configure-push.md)]

## <a id="update-scripts"></a>プッシュ通知を送信するようにバックエンド スクリプトを更新する

* [Azure クラシック ポータル]で、**[データ]** タブ、**[TodoItem]** の順にクリックします。**[TodoItem]** で、**[スクリプト]** タブをクリックし、**[挿入]** をクリックします。**TodoItem** テーブルで挿入が発生したときに呼び出される関数が表示されます。

* insert 関数を次のコードに置き換え、[**保存**] をクリックします。これで、新しい挿入スクリプトが登録されます。このスクリプトは [apns オブジェクト]を使用して、挿入要求で指定されたデバイスにプッシュ通知 (挿入されたテキスト) を送信します。このスクリプトでは、プッシュ通知を受け取るためにアプリケーションを閉じる時間を与えるために通知の送信を遅らせています。


```
        function insert(item, user, request) {
            request.execute();
            // Set timeout to delay the notification, to provide time for the
            // app to be closed on the device to demonstrate push notifications
            setTimeout(function() {
                push.apns.send(null, {
                    alert: "Alert: " + item.text,
                    payload: {
                        inAppMessage: "Hey, a new item arrived: '" + item.text + "'"
                    }
                });
            }, 2500);
        }
```

[AZURE.INCLUDE [プッシュ通知をアプリに追加する](../../includes/add-push-notifications-to-app.md)]

[AZURE.INCLUDE [アプリでプッシュ通知をテストする](../../includes/test-push-notifications-in-app.md)]


<!-- Anchors. -->


<!-- Images. -->
[5]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step5.png
[6]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step6.png
[7]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step7.png

[9]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step9.png
[10]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step10.png
[17]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step17.png
[18]: ./media/mobile-services-ios-get-started-push/mobile-services-selection.png
[19]: ./media/mobile-services-ios-get-started-push/mobile-push-tab-ios.png
[20]: ./media/mobile-services-ios-get-started-push/mobile-push-tab-ios-upload.png
[21]: ./media/mobile-services-ios-get-started-push/mobile-portal-data-tables.png
[22]: ./media/mobile-services-ios-get-started-push/mobile-insert-script-push2.png
[23]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push1-ios.png
[24]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push2-ios.png
[25]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push3-ios.png
[26]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push4-ios.png
[28]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step18.png

[101]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-01.png
[102]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-02.png
[103]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-03.png
[104]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-04.png
[105]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-05.png
[106]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-06.png
[107]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-07.png
[108]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-08.png

[110]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-10.png
[111]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-11.png
[112]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-12.png
[113]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-13.png
[114]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-14.png
[115]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-15.png
[116]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-16.png
[117]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-17.png

<!-- URLs.   -->
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456
[Mobile Services iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Apple Push Notification Service]: http://go.microsoft.com/fwlink/p/?LinkId=272584
[Mobile Services の使用]: mobile-services-ios-get-started.md
[Get started with authentication]: mobile-services-ios-get-started-users.md
[Azure クラシック ポータル]: https://manage.windowsazure.com/
[apns オブジェクト]: http://go.microsoft.com/fwlink/p/?LinkId=272333

[Mobile Services server script reference]: http://go.microsoft.com/fwlink/?LinkId=262293

[Send push notifications to authenticated users]: mobile-services-javascript-backend-ios-push-notifications-app-users.md
[What are Notification Hubs?]: ../notification-hubs-overview.md
[Send broadcast notifications to subscribers]: ../notification-hubs-ios-send-breaking-news.md
[Send template-based notifications to subscribers]: ../notification-hubs-ios-send-localized-breaking-news.md
[Mobile Services Objective-C how-to conceptual reference]: mobile-services-windows-dotnet-how-to-use-client-library.md

<!---HONumber=AcomDC_0727_2016-->