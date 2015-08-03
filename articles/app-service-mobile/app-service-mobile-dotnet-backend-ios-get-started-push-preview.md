<properties
	pageTitle="Azure App Service を使用して iOS アプリにプッシュ通知を追加する"
	description="Azure App Service を使用して iOS アプリにプッシュ通知を送信する方法について説明します。"
	services="app-service\mobile"
	documentationCenter="ios"
	manager="dwrede"
	editor=""
	authors="krisragh"/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="06/01/2015"
	ms.author="krisragh"/>


# iOS アプリにプッシュ通知を追加する

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push-preview](../../includes/app-service-mobile-selector-get-started-push-preview.md)]

このトピックでは、[クイックスタート プロジェクト](app-service-mobile-dotnet-backend-ios-get-started-preview.md)にプッシュ通知を追加して、レコードが挿入されるたびにモバイル サービスがプッシュ通知を送信できるようにします。先に「[Mobile Services の使用]」を完了しておく必要があります。

> [AZURE.NOTE] [IOS シミュレーターはプッシュ通知をサポートしない](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator.html)ため、物理的な iOS デバイスを使用する必要があります。さらに、有料の [Apple Developer Program メンバーシップ](https://developer.apple.com/programs/ios/)にサインアップする必要があります。

##<a name="review"></a>サーバーのプロジェクト構成を確認する (省略可能)

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-enable-push-preview](../../includes/app-service-mobile-dotnet-backend-enable-push-preview.md)]

[AZURE.INCLUDE [Apple プッシュ通知を有効にする](../../includes/enable-apple-push-notifications.md)]

## プッシュ通知を送信するように Azure を構成する

[AZURE.INCLUDE [app-service-mobile-apns-configure-push-preview](../../includes/app-service-mobile-apns-configure-push-preview.md)]

##<a id="update-server"></a>プッシュ通知を送信するようにバックエンド コードを更新する

* バックエンド コード用の Visual Studio プロジェクトをダウンロードします。ポータルで、**[参照]**、アプリ名、**[クライアントの追加]**、**[iOS]** (Objective-C または Swift)、**[サーバー プロジェクトをダウンロードして実行する]** の順にクリックします。**Controllers**、TodoItemController.cs の順に開き、次の using ステートメントを追加します。

```
			using Microsoft.Azure.Mobile.Server.Config;
			using Microsoft.Azure.NotificationHubs;
```

* 以下を `PostTodoItem` の `InsertAsync` 呼び出しの後ろに追加します。Todo 項目が挿入されると、このコードはプッシュ通知を項目のテキストと共に送信します。

```
        // get Notification Hubs credentials associated with this Mobile App
        string notificationHubName = this.Services.Settings.NotificationHubName;
        string notificationHubConnection = this.Services.Settings.Connections[ServiceSettingsKeys.NotificationHubConnectionString].ConnectionString;

        // connect to notification hub
        NotificationHubClient Hub = NotificationHubClient.CreateClientFromConnectionString(notificationHubConnection, notificationHubName);

        // iOS payload
        var appleNotificationPayload = "{"aps":{"alert":"" + item.Text + ""}}";

        await Hub.SendAppleNativeNotificationAsync(appleNotificationPayload);
```

## <a name="publish-the-service"></a>モバイル サービスを Azure に発行する

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service-preview](../../includes/app-service-mobile-dotnet-backend-publish-service-preview.md)]

[AZURE.INCLUDE [プッシュ通知をアプリに追加する](../../includes/app-service-add-push-notifications-to-app.md)]

[AZURE.INCLUDE [アプリでプッシュ通知をテストする](../../includes/test-push-notifications-in-app.md)]

<!-- Anchors.  -->
[Generate iOS certificate signing request]: #certificates
[Register your app and enable push notifications]: #register
[Create a provisioning profile for the app]: #profile
[Add push notifications to the app]: #add-push
[Configure your mobile backend to send push requests]: #configure
[Update the server to send push notifications]: #update-server
[Publish the mobile backend to Azure]: #publish-mobile-service
[Test your app]: #test-the-service

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

<!-- URLs. -->
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456
[Azure Mobile App iOS SDK]: https://go.microsoft.com/fwLink/?LinkID=529823
[Azure Notification Hubs Nuget]: https://www.nuget.org/packages/WindowsAzure.ServiceBus/
[Apple Push Notification Service]: http://go.microsoft.com/fwlink/p/?LinkId=272584
[Get started with Mobile Services]: ../mobile-services-dotnet-backend-ios-get-started.md
[Mobile Services の使用]: app-service-mobile-dotnet-backend-ios-get-started-preview.md
[Azure Management Portal]: https://manage.windowsazure.com/
[apns object]: http://go.microsoft.com/fwlink/p/?LinkId=272333
 

<!---HONumber=July15_HO4-->