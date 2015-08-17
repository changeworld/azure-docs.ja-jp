<properties 
	pageTitle="認証されたユーザーへのプッシュ通知の送信" 
	description="特定の場所にプッシュ通知を送信する方法について説明します。" 
	services="mobile-services,notification-hubs" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="06/04/2015" 
	ms.author="glenga"/>

# 認証されたユーザーへのプッシュ通知の送信

[AZURE.INCLUDE [mobile-services-selector-push-users](../../includes/mobile-services-selector-push-users.md)]

このトピックでは、登録されたデバイスの認証されたユーザーにプッシュ通知を送信する方法を説明します。前の「[アプリケーションにプッシュ通知を追加する]」チュートリアルとは違い、このチュートリアルでは、プッシュ通知のためにクライアントを通知ハブに登録するには、ユーザーの認証が必要になるようにモバイル サービスを変更します。また、登録を変更して、割り当てられたユーザー ID に基づいてタグが追加されるようにします。最後に、サーバー コードを更新して、すべての登録に対してではなく、認証されたユーザーにのみ通知が送信されるようにします。

このチュートリアルが対象とするのは、Windows Phone 8.0 および Windows Phone 8.1 Silverlight のアプリケーションです。Windows Phone 8.1 ストアのアプリケーションについては、[このトピックの Windows ストア版](mobile-services-dotnet-backend-windows-store-dotnet-push-notifications-app-users.md)を参照してください。

##前提条件 

このチュートリアルの前に、次の Mobile Services のチュートリアルを完了している必要があります。

+ [アプリに認証を追加する] <br/>TodoList サンプル アプリにサインイン要件を追加します。

+ [アプリケーションにプッシュ通知を追加する]<br/>Notification Hubs を使用してプッシュ通知を送信するように TodoList サンプル アプリを構成します。

両方のチュートリアルを完了すると、モバイル サービスからのプッシュ通知に、認証されていないユーザーが登録できないようにすることができます。

##<a name="register"></a>登録に認証が必要となるようにサービスを更新する

[AZURE.INCLUDE [mobile-services-dotnet-backend-push-notifications-app-users](../../includes/mobile-services-dotnet-backend-push-notifications-app-users.md)]

##<a name="update-app"></a>登録の前にログインするようにアプリケーションを更新する

[AZURE.INCLUDE [mobile-services-windows-phone-push-notifications-app-users](../../includes/mobile-services-windows-phone-push-notifications-app-users.md)]


##<a name="test"></a>アプリケーションをテストする

[AZURE.INCLUDE [mobile-services-windows-test-push-users](../../includes/mobile-services-windows-test-push-users.md)]

<!-- Anchors. -->
[Updating the service to require authentication for registration]: #register
[Updating the app to log in before registration]: #update-app
[Testing the app]: #test
[Next Steps]: #next-steps


<!-- URLs. -->
[アプリに認証を追加する]: mobile-services-dotnet-backend-windows-phone-get-started-users.md
[アプリケーションにプッシュ通知を追加する]: mobile-services-dotnet-backend-windows-phone-get-started-push.md

[Azure Management Portal]: https://manage.windowsazure.com/
 

<!---HONumber=August15_HO6-->