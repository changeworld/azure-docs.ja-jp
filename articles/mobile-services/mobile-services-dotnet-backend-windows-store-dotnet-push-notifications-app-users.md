<properties
	pageTitle="認証されたユーザー (ユニバーサル Windows 8.1) へのプッシュ通知の送信 | Azure Mobile Services"
	description="Azure Mobile Services を使用して、ユニバーサル Windows 8.1 アプリを実行している特定の認証済みユーザーにプッシュ通知を送信する方法について説明します。"
	services="mobile-services,notification-hubs"
	documentationCenter="windows"
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="10/05/2015"
	ms.author="glenga"/>

# 認証されたユーザーへのプッシュ通知の送信

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


[AZURE.INCLUDE [mobile-services-selector-push-users](../../includes/mobile-services-selector-push-users.md)]

##概要

このトピックでは、登録されたデバイスの認証されたユーザーにプッシュ通知を送信する方法を説明します。前の[プッシュ通知][Get started with push notifications]のチュートリアルとは違い、このチュートリアルでは、プッシュ通知のためにクライアントを通知ハブに登録するには、ユーザーの認証が必要になるようにモバイル サービスを変更します。また、登録を変更して、割り当てられたユーザー ID に基づいてタグが追加されるようにします。最後に、サーバー コードを更新して、すべての登録に対してではなく、認証されたユーザーにのみ通知が送信されるようにします。

このチュートリアルが対象とするのは、Windows ストアと Windows Phone ストアの両方のアプリケーションです。

##前提条件

このチュートリアルの前に、次の Mobile Services のチュートリアルを完了している必要があります。

+ [認証の使用] TodoList サンプル アプリケーションにログイン要件を追加します。

+ [プッシュ通知の使用] Notification Hubs を使用してプッシュ通知を送信するよう TodoList サンプル アプリケーションを構成します。

両方のチュートリアルを完了すると、モバイル サービスからのプッシュ通知に、認証されていないユーザーが登録できないようにすることができます。

##<a name="register"></a>登録に認証が必要となるようにサービスを更新する

[AZURE.INCLUDE [mobile-services-dotnet-backend-push-notifications-app-users](../../includes/mobile-services-dotnet-backend-push-notifications-app-users.md)]

##<a name="update-app"></a>登録の前にログインするようにアプリケーションを更新する

[AZURE.INCLUDE [mobile-services-windows-store-dotnet-push-notifications-app-users](../../includes/mobile-services-windows-store-dotnet-push-notifications-app-users.md)]

##<a name="test"></a>アプリケーションをテストする

[AZURE.INCLUDE [mobile-services-windows-test-push-users](../../includes/mobile-services-windows-test-push-users.md)]



<!-- Anchors. -->
[Updating the service to require authentication for registration]: #register
[Updating the app to log in before registration]: #update-app
[Testing the app]: #test
[Next Steps]: #next-steps


<!-- URLs. -->
[認証の使用]: ../mobile-services-dotnet-backend-windows-store-dotnet-get-started-users.md
[Get started with push notifications]: ../mobile-services-dotnet-backend-windows-store-dotnet-get-started-push.md
[プッシュ通知の使用]: ../mobile-services-dotnet-backend-windows-store-dotnet-get-started-push.md

<!---HONumber=AcomDC_1203_2015-->