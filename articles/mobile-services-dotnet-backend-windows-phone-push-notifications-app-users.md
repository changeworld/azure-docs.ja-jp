<properties 
	pageTitle="認証されたユーザーへのプッシュ通知の送信" 
	description="特定の場所にプッシュ通知を送信する方法について説明します。" 
	services="mobile-services, notification-hubs" 
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
	ms.date="09/29/2014" 
	ms.author="glenga"/>

# 認証されたユーザーへのプッシュ通知の送信

[AZURE.INCLUDE [mobile-services-selector-push-users](../includes/mobile-services-selector-push-users.md)]

このトピックでは、登録されたデバイスの認証されたユーザーにプッシュ通知を送信する方法を説明します。前の[プッシュ通知][プッシュ通知の使用]に関するチュートリアルとは違い、このチュートリアルでは、Mobile Services を変更して、プッシュ通知のためにクライアントを Notification Hubs に登録する際にユーザーの認証が必要になるようにします。また、登録を変更して、割り当てられたユーザー ID に基づいてタグが追加されるようにします。最後に、サーバー コードを更新して、すべての登録に対してではなく、認証されたユーザーにのみ通知が送信されるようにします。

このチュートリアルでは、次の手順について説明します。:

+ [登録に認証が必要となるようにサービスを更新する]
+ [登録の前にログインするようにアプリケーションを更新する]
+ [アプリケーションをテストする]
 
このチュートリアルが対象とするのは、Windows Phone 8.0 および Windows Phone 8.1 ("Silverlight") のアプリケーションです。Windows Phone 8.1 ストア アプリについては、[このトピックの Windows ストア バージョン](/ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-push-notifications-app-users)を参照してください。

##前提条件 

このチュートリアルの前に、次の Mobile Services のチュートリアルを完了している必要があります。:

+ [認証の使用]<br/>TodoList サンプル アプリケーションにログイン要件を追加します。

+ [プッシュ通知の使用]<br/>Notification Hubs を使用して、TodoList サンプル アプリのプッシュ通知を構成します。 

両方のチュートリアルを完了すると、モバイル サービスからのプッシュ通知に、認証されていないユーザーが登録できないようにすることができます。

##<a name="register"></a>登録に認証が必要となるようにサービスを更新する

[AZURE.INCLUDE [mobile-services-dotnet-backend-push-notifications-app-users](../includes/mobile-services-dotnet-backend-push-notifications-app-users.md)] 

##<a name="update-app"></a>登録の前にログインするようにアプリケーションを更新する

[AZURE.INCLUDE [mobile-services-windows-phone-push-notifications-app-users](../includes/mobile-services-windows-phone-push-notifications-app-users.md)] 


##<a name="test"></a>アプリケーションをテストする

[AZURE.INCLUDE [mobile-services-windows-test-push-users](../includes/mobile-services-windows-test-push-users.md)] 

<!---## <a name="next-steps"></a>次のステップ

次のチュートリアル「[モバイル サービス ユーザーのサービス側の承認][スクリプトを使用したユーザーの承認]」では、認証されたユーザーに基づいて Mobile Services によって提供されるユーザー ID 値を受け取り、それを使用して、Mobile Services から返されたデータをフィルター処理します。.NET で Mobile Services を使用する方法の詳細については、[Mobile Services .NET の使用方法の概念リファレンス]に関する記事を参照してください。-->

<!-- Anchors. -->
[登録に認証が必要となるようにサービスを更新する]: #register
[登録の前にログインするようにアプリケーションを更新する]: #update-app
[アプリケーションをテストする]: #test
[次のステップ]:#next-steps


<!-- URLs. -->
[認証の使用]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users/
[プッシュ通知の使用]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push/

[Azure の管理ポータル]: https://manage.windowsazure.com/
[Mobile Services .NET の使用方法の概念リファレンス]: /ja-jp/develop/mobile/how-to-guides/work-with-net-client-library


<!--HONumber=42-->
