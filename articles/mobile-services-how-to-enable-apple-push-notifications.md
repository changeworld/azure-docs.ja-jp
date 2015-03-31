<properties 
	pageTitle="Apple のプッシュ通知を有効にする方法" 
	description="このチュートリアルでは、Azure Mobile Services を使用して新しいサービスを作成する方法について説明します。" 
	services="mobile-services" 
	documentationCenter="ios" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="11/21/2014" 
	ms.author="glenga"/>

# Apple のプッシュ通知を有効にする方法

このトピックでは、Apple Push Notification Service (APNS) を使用して、iOS アプリケーションでプッシュ通知を有効にする方法について説明します。取得した資格情報を使用して、[Azure 管理ポータル][Management Portal]でプッシュ通知用に iOS アプリケーションを登録します。アプリケーションの更新を含めた完全なエンド ツー エンド チュートリアルについては、「[プッシュ通知の使用]」をご覧ください。 

[AZURE.INCLUDE [Enable Apple Push Notifications](../includes/enable-apple-push-notifications.md)]

この .p12 証明書を使用することで、サービスが APNS で認証し、アプリケーションの代わりにプッシュ通知を送信できるようになります。

<!-- Anchors. -->


<!-- Images. -->


<!-- URLs. -->
[プッシュ通知の使用]: /documentation/articles/mobile-services-javascript-backend-ios-get-started-push/
[Mobile Services  SDK]: https://go.microsoft.com/fwLink/p/?LinkID=268375

[Management Portal]: https://manage.windowsazure.com/


<!--HONumber=47-->
