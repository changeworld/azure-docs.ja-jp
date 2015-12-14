<properties
	pageTitle="認証されたユーザーへのプッシュ通知の送信 (.NET バックエンド)"
	description="特定の場所にプッシュ通知を送信する方法について説明します。"
	services="mobile-services,notification-hubs"
	documentationCenter="ios"
	authors="krisragh"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="10/01/2015"
	ms.author="krisragh"/>

# 認証されたユーザーへのプッシュ通知の送信

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


[AZURE.INCLUDE [mobile-services-selector-push-users](../../includes/mobile-services-selector-push-users.md)]

このトピックでは、iOS 上の認証されたユーザーにプッシュ通知を送信する方法を説明します。このチュートリアルを開始する前に、まず、「[認証の使用]」 と 「[プッシュ通知の使用]」を実行してください。

このチュートリアルでは、まずユーザーに認証を受けるように求める必要があります。次にプッシュ通知用の通知ハブに登録します。さらに、認証されたユーザーのみに通知が送信されるようにサーバー スクリプトを更新します。

##<a name="register"></a>登録に認証が必要となるようにサービスを更新する

[AZURE.INCLUDE [mobile-services-dotnet-backend-push-notifications-app-users](../../includes/mobile-services-dotnet-backend-push-notifications-app-users.md)]

##<a name="update-app"></a>登録の前にログインするようにアプリケーションを更新する

[AZURE.INCLUDE [mobile-services-ios-push-notifications-app-users-login](../../includes/mobile-services-ios-push-notifications-app-users-login.md)]

##<a name="test"></a>アプリケーションをテストする

[AZURE.INCLUDE [mobile-services-ios-push-notifications-app-users-test-app](../../includes/mobile-services-ios-push-notifications-app-users-test-app.md)]

<!-- Anchors. -->
[Updating the service to require authentication for registration]: #register
[Updating the app to log in before registration]: #update-app
[Testing the app]: #test
[Next Steps]: #next-steps


<!-- URLs. -->
[認証の使用]: mobile-services-dotnet-backend-ios-get-started-users.md
[プッシュ通知の使用]: mobile-services-dotnet-backend-ios-get-started-push.md
[Mobile Services .NET How-to Conceptual Reference]: /develop/mobile/how-to-guides/work-with-net-client-library

<!---HONumber=AcomDC_1203_2015-->