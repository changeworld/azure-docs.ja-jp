<properties 
	pageTitle="認証されたユーザーへのプッシュ通知の送信" 
	description="特定の場所にプッシュ通知を送信する方法について説明します。" 
	services="mobile-services,notification-hubs" 
	documentationCenter="android" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="notification-hubs" 
	ms.devlang="java" 
	ms.topic="article" 
	ms.date="06/03/2015" 
	ms.author="wesmc"/>

# 認証されたユーザーへのプッシュ通知の送信

[AZURE.INCLUDE [mobile-services-selector-push-users](../../includes/mobile-services-selector-push-users.md)]

##概要

このトピックでは、登録されたデバイスの認証されたユーザーにプッシュ通知を送信する方法を説明します。前の[プッシュ通知][Get started with push notifications]のチュートリアルとは違い、このチュートリアルでは、プッシュ通知のためにクライアントを通知ハブに登録するには、ユーザーの認証が必要になるようにモバイル サービスを変更します。また、登録を変更して、割り当てられたユーザー ID に基づいてタグが追加されるようにします。最後に、サーバー コードを更新して、すべての登録に対してではなく、認証されたユーザーにのみ通知が送信されるようにします。


このチュートリアルが対象とするのは、Android アプリケーションです。

##前提条件 

このチュートリアルの前に、次の Mobile Services のチュートリアルを完了している必要があります。

+ [Mobile Services アプリへの認証の追加]<br/>TodoList サンプル アプリケーションにログイン要件を追加します。

+ [プッシュ通知の使用]<br/>通知ハブを使用してプッシュ通知を送信するよう TodoList サンプル アプリケーションを構成します。

両方のチュートリアルを完了すると、モバイル サービスからのプッシュ通知に、認証されていないユーザーが登録できないようにすることができます。

##登録に認証が必要となるようにサービスを更新する

[AZURE.INCLUDE [mobile-services-dotnet-backend-push-notifications-app-users](../../includes/mobile-services-dotnet-backend-push-notifications-app-users.md)]

##登録の前にログインするようにアプリケーションを更新する

[AZURE.INCLUDE [mobile-services-android-push-notifications-app-users](../../includes/mobile-services-android-push-notifications-app-users.md)]

##アプリケーションをテストする

[AZURE.INCLUDE [mobile-services-android-test-push-users](../../includes/mobile-services-android-test-push-users.md)]


<!---##Next steps

In the next tutorial, [Service-side authorization of Mobile Services users](mobile-services-javascript-backend-service-side-authorization.md), you will take the user ID value provided by Mobile Services based on an authenticated user and use it to filter the data returned by Mobile Services. Learn more about how to use Mobile Services with .NET in [Mobile Services .NET How-to Conceptual Reference]-->


<!-- URLs. -->
[Mobile Services アプリへの認証の追加]: mobile-services-dotnet-backend-android-get-started-users.md
[Get started with push notifications]: mobile-services-dotnet-backend-android-get-started-push.md
[プッシュ通知の使用]: mobile-services-dotnet-backend-android-get-started-push.md

[Azure Management Portal]: https://manage.windowsazure.com/
[Mobile Services .NET How-to Conceptual Reference]: /develop/mobile/how-to-guides/work-with-net-client-library
 

<!---HONumber=July15_HO4-->