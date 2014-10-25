<properties pageTitle="How to enable Google Cloud Messaging" metaKeywords="" description="Follow this tutorial to create a new service using Azure Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="How to create a new mobile service" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-android" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# Google Cloud Messaging を有効にする方法

このトピックでは、Google Cloud Messaging (GCM) を使用して、Android アプリケーションでプッシュ通知を有効にする方法について説明します。取得した API キーを使用して、[Azure の管理ポータル][Azure の管理ポータル]でプッシュ通知用に Android アプリケーションを登録します。アプリケーションの更新を含めた完全なエンド ツー エンド チュートリアルについては、「[プッシュ通知の使用][プッシュ通知の使用]」を参照してください。

[WACOM.INCLUDE [Enable GCM][Enable GCM]]

この API キー値を使用することで、サービスが GCM で認証し、アプリケーションの代わりにプッシュ通知を送信できるようになります。



  [Azure の管理ポータル]: https://manage.windowsazure.com/
  [プッシュ通知の使用]: /ja-jp/documentation/articles/mobile-services-javascript-backend-android-get-started-push/
  [Enable GCM]: ../includes/mobile-services-enable-Google-cloud-messaging.md
