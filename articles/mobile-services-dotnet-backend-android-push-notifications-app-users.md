<properties linkid="/documentation/articles/mobile-services-dotnet-backend-android-push-notifications-app-users" pageTitle="Send push notifications to authenticated users" metaKeywords="push notifications, authentication, users, Notification Hubs, Mobile Services" description="Learn how to send push notifications to specific " metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Send push notifications to authenticated users" authors="wesmc" solutions="Mobile" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-android" ms.devlang="java" ms.topic="article" ms.date="01/01/1900" ms.author="wesmc"></tags>

# 認証されたユーザーへのプッシュ通知の送信

<div class="dev-center-tutorial-selector sublanding">
<a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-push-notifications-app-users" title="Windows ストア C#">Windows ストア C#</a>
<a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-push-notifications-app-users" title="Windows ストア JavaScript">Windows ストア JavaScript</a>
<a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-phone-push-notifications-app-users" title="Windows Phone">Windows Phone</a>
<a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-ios-push-notifications-app-users" title="iOS">iOS</a>
<a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-android-push-notifications-app-users" title="Android" class="current">Android</a>
</div>

<div class="dev-center-tutorial-subselector">
<a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-android-push-notifications-app-users/" title=".NET バックエンド" class="current">.NET バックエンド</a> | 
<a href="/ja-jp/documentation/articles/mobile-services-javascript-backend-android-push-notifications-app-users/"  title="JavaScript バックエンド">JavaScript バックエンド</a>
</div>

このトピックでは、登録されたデバイスの認証されたユーザーにプッシュ通知を送信する方法を説明します。前の[プッシュ通知][プッシュ通知]のチュートリアルとは違い、このチュートリアルでは、プッシュ通知のためにクライアントを通知ハブに登録するには、ユーザーの認証が必要になるようにモバイル サービスを変更します。登録も変更され、割り当てられたユーザー ID に基づいてタグが追加されます。最後に、サーバー コードが更新されて、すべての登録に対してではなく、認証されたユーザーにのみ通知が送信されます。

このチュートリアルでは、次の手順について説明します。

-   [登録に認証が必要となるようにサービスを更新する][登録に認証が必要となるようにサービスを更新する]
-   [登録の前にログインするようにアプリケーションを更新する][登録の前にログインするようにアプリケーションを更新する]
-   [アプリケーションをテストする][アプリケーションをテストする]

このチュートリアルが対象とするのは、Android アプリケーションです。

## 前提条件

このチュートリアルの前に、次の Mobile Services のチュートリアルを完了している必要があります。

-   [認証の使用][認証の使用]
    TodoList サンプル アプリケーションにログイン要件を追加します。

-   [プッシュ通知の使用][プッシュ通知]
    通知ハブを使用してプッシュ通知を送信するよう TodoList サンプル アプリケーションを構成します。

両方のチュートリアルを完了すると、モバイル サービスからのプッシュ通知に、認証されていないユーザーが登録できないようにすることができます。

## <a name="register"></a>登録に認証が必要となるようにサービスを更新する

[WACOM.INCLUDE [mobile-services-dotnet-backend-push-notifications-app-users][mobile-services-dotnet-backend-push-notifications-app-users]]

## <a name="update-app"></a>登録の前にログインするようにアプリケーションを更新する

[WACOM.INCLUDE [mobile-services-android-push-notifications-app-users][mobile-services-android-push-notifications-app-users]]

## <a name="test"></a> アプリケーションをテストする

[WACOM.INCLUDE [mobile-services-android-test-push-users][mobile-services-android-test-push-users]]

<br/>
<br/>
<br/>


  [Windows ストア C#]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-push-notifications-app-users "Windows ストア C#"
  [Windows ストア JavaScript]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-push-notifications-app-users "Windows ストア JavaScript"
  [Windows Phone]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-phone-push-notifications-app-users "Windows Phone"
  [iOS]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-ios-push-notifications-app-users "iOS"
  [Android]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-android-push-notifications-app-users "Android"
  [.NET バックエンド]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-android-push-notifications-app-users/ ".NET バックエンド"
  [JavaScript バックエンド]: /ja-jp/documentation/articles/mobile-services-javascript-backend-android-push-notifications-app-users/ "JavaScript バックエンド"
  [プッシュ通知]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-android-get-started-push/
  [登録に認証が必要となるようにサービスを更新する]: #register
  [登録の前にログインするようにアプリケーションを更新する]: #update-app
  [アプリケーションをテストする]: #test
  [認証の使用]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-android-get-started-users/
  [mobile-services-dotnet-backend-push-notifications-app-users]: ../includes/mobile-services-dotnet-backend-push-notifications-app-users.md
  [mobile-services-android-push-notifications-app-users]: ../includes/mobile-services-android-push-notifications-app-users.md
  [mobile-services-android-test-push-users]: ../includes/mobile-services-android-test-push-users.md
