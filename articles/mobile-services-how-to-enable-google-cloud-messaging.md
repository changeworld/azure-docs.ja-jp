<properties 
	pageTitle="Google Cloud Messaging を有効にする方法" 
	description="このチュートリアルでは、Azure Mobile Services を使用して新しいサービスを作成する方法について説明します。" 
	services="mobile-services" 
	documentationCenter="android" 
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

# Google Cloud Messaging を有効にする方法

このトピックでは、Google Cloud Messaging (GCM) を使用して、Android アプリケーションでプッシュ通知を有効にする方法について説明します。取得した API キーを使用して、[Azure 管理ポータル][Management Portal]でプッシュ通知用に Android アプリケーションを登録します。アプリケーションの更新を含めた完全なエンド ツー エンド チュートリアルについては、「[プッシュ通知の使用]」をご覧ください。 

[AZURE.INCLUDE [Enable GCM](../includes/mobile-services-enable-Google-cloud-messaging.md)]

この API キー値を使用することで、サービスが GCM で認証し、アプリケーションの代わりにプッシュ通知を送信できるようになります。

<!-- Anchors. -->


<!-- Images. -->


<!-- URLs. -->
[プッシュ通知の使用]: /documentation/articles/mobile-services-javascript-backend-android-get-started-push/
[Visual Studio 2012 Express for Windows Phone]: https://go.microsoft.com/fwLink/p/?LinkID=268374
[Mobile Services  SDK]: https://go.microsoft.com/fwLink/p/?LinkID=268375

[Management Portal]: https://manage.windowsazure.com/
[.NET バックエンド バージョン]: /documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started

<!--HONumber=47-->
