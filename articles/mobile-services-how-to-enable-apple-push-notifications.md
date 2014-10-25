<properties pageTitle="How to enable Apple push notifications" metaKeywords="" description="Follow this tutorial to create a new service using Azure Mobile Services." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="How to Apple push notifications" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# Apple のプッシュ通知を有効にする方法

このトピックでは、Apple Push Notification Service (APNS) を使用して、iOS アプリケーションでプッシュ通知を有効にする方法について説明します。取得した資格情報を使用して、[Azure の管理ポータル][Azure の管理ポータル]でプッシュ通知用に iOS アプリケーションを登録します。アプリケーションの更新を含めた完全なエンド ツー エンド チュートリアルについては、「[プッシュ通知の使用][プッシュ通知の使用]」を参照してください。

[WACOM.INCLUDE [Enable Apple Push Notifications][Enable Apple Push Notifications]]

この .p12 証明書を使用することで、サービスが APNS で認証し、アプリケーションの代わりにプッシュ通知を送信できるようになります。



  [Azure の管理ポータル]: https://manage.windowsazure.com/
  [プッシュ通知の使用]: /ja-jp/documentation/articles/mobile-services-javascript-backend-ios-get-started-push/
  [Enable Apple Push Notifications]: ../includes/enable-apple-push-notifications.md
