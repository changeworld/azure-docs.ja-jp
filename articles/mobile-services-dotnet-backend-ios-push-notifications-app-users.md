<properties pageTitle="認証されたユーザーへのプッシュ通知の送信" metaKeywords="push notifications, authentication, users, Notification Hubs, Mobile Services" description="特定の場所にプッシュ通知を送信する方法について説明します。 " metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="krisragh" solutions="Mobile" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="10/10/2014" ms.author="krisragh" />

# 認証されたユーザーへのプッシュ通知の送信

[WACOM.INCLUDE [mobile-services-selector-push-users](../includes/mobile-services-selector-push-users.md)]

<div class="dev-center-tutorial-subselector"><a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-ios-push-notifications-app-users/" title=".NET backend" class="current">.NET バックエンド</a> | <a href="/ja-jp/documentation/articles/mobile-services-javascript-backend-ios-push-notifications-app-users/"  title="JavaScript backend">JavaScript バックエンド</a></div>

このトピックでは、登録された iOS デバイスの認証されたユーザーにプッシュ通知を送信する方法を説明します。前の[プッシュ通知][プッシュ通知の使用]のチュートリアルとは違い、このチュートリアルでは、プッシュ通知のためにクライアントを Notification Hubs に登録するにはユーザーの認証が必要になるように Mobile Services を変更します。登録も変更され、割り当てられたユーザー ID に基づいてタグが追加されます。最後に、サーバー コードが更新されて、すべての登録に対してではなく、認証されたユーザーにのみ通知が送信されます。

このチュートリアルでは、次の手順について説明します。

+ [登録に認証が必要となるようにサービスを更新する]
+ [登録の前にログインするようにアプリケーションを更新する]
+ [アプリケーションをテストする]

このチュートリアルが対象とするのは、Windows ストアと Windows Phone ストアの両方のアプリケーションです。

##前提条件

このチュートリアルの前に、次の Mobile Services のチュートリアルを完了している必要があります。

+ [認証の使用]<br/>TodoList サンプル アプリケーションにログイン要件を追加します。

+ [プッシュ通知の使用]<br/>Notification Hubs を使用してプッシュ通知を送信するよう TodoList サンプル アプリケーションを構成します。

両方のチュートリアルを完了すると、モバイル サービスからのプッシュ通知に、認証されていないユーザーが登録できないようにすることができます。

##<a name="register"></a>登録に認証が必要となるようにサービスを更新する

[WACOM.INCLUDE [mobile-services-dotnet-backend-push-notifications-app-users](../includes/mobile-services-dotnet-backend-push-notifications-app-users.md)]

##<a name="update-app"></a>登録の前にログインするようにアプリケーションを更新する

[WACOM.INCLUDE [mobile-services-ios-push-notifications-app-users-login](../includes/mobile-services-ios-push-notifications-app-users-login.md)]

##<a name="test"></a>アプリケーションをテストする

[WACOM.INCLUDE [mobile-services-ios-push-notifications-app-users-test-app](../includes/mobile-services-ios-push-notifications-app-users-test-app.md)]

<!-- Anchors. -->
[登録に認証が必要となるようにサービスを更新する]: #register
[登録の前にログインするようにアプリケーションを更新する]: #update-app
[アプリケーションをテストする]: #test
[次のステップ]:#next-steps


<!-- URLs. -->
[認証の使用]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users/
[プッシュ通知の使用]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push/

[Azure 管理ポータル]: https://manage.windowsazure.com/
[モバイル サービス .NET の使用方法の概念リファレンス]: /ja-jp/develop/mobile/how-to-guides/work-with-net-client-library
