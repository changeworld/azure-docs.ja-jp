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
	ms.tgt_pltfrm="" 
	ms.devlang="java" 
	ms.topic="article" 
	ms.date="02/23/2015" 
	ms.author="wesmc"/>

# 認証されたユーザーへのプッシュ通知の送信

[AZURE.INCLUDE [mobile-services-selector-push-users](../includes/mobile-services-selector-push-users.md)]

## 概要

このトピックでは、登録されたデバイスの認証されたユーザーにプッシュ通知を送信する方法を説明します。前の[プッシュ通知][プッシュ通知の使用]に関するチュートリアルとは違い、このチュートリアルでは、Mobile Services を変更して、プッシュ通知のためにクライアントを Notification Hubs に登録する際にユーザーの認証が必要になるようにします。また、登録を変更して、割り当てられたユーザー ID に基づいてタグが追加されるようにします。最後に、サーバー コードを更新して、すべての登録に対してではなく、認証されたユーザーにのみ通知が送信されるようにします。


このチュートリアルが対象とするのは、Android アプリケーションです。

## 前提条件 

このチュートリアルの前に、次の Mobile Services のチュートリアルを完了している必要があります。

+ [Mobile Services アプリへの認証の追加]<br/>TodoList サンプル アプリケーションにログイン要件を追加します。

+ [プッシュ通知の使用]<br/>Notification Hubs を使用して、TodoList サンプル アプリのプッシュ通知を構成します。 

両方のチュートリアルを完了すると、モバイル サービスからのプッシュ通知に、認証されていないユーザーが登録できないようにできます。

## 登録に認証が必要となるようにサービスを更新する

[AZURE.INCLUDE [mobile-services-dotnet-backend-push-notifications-app-users](../includes/mobile-services-dotnet-backend-push-notifications-app-users.md)] 

## 登録の前にログインするようにアプリケーションを更新する

[AZURE.INCLUDE [mobile-services-android-push-notifications-app-users](../includes/mobile-services-android-push-notifications-app-users.md)] 

## アプリケーションをテストする

[AZURE.INCLUDE [mobile-services-android-test-push-users](../includes/mobile-services-android-test-push-users.md)] 


<!---##Next steps

In the next tutorial, [Service-side authorization of Mobile Services users][Authorize users with scripts], you will take the user ID value provided by Mobile Services based on an authenticated user and use it to filter the data returned by Mobile Services. Learn more about how to use Mobile Services with .NET in [Mobile Services .NET How-to Conceptual Reference]-->


<!-- URLs. -->
[Mobile Services アプリへの認証の追加]: /documentation/articles/mobile-services-dotnet-backend-android-get-started-users/
[プッシュ通知の使用]: /documentation/articles/mobile-services-dotnet-backend-android-get-started-push/

[Azure 管理ポータル]: https://manage.windowsazure.com/
[Mobile Services .NET の使用方法の概念リファレンス]: /develop/mobile/how-to-guides/work-with-net-client-library

<!--HONumber=47-->
