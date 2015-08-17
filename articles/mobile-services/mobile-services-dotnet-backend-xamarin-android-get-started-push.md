<properties 
	pageTitle="Xamarin Android アプリケーション用 Mobile Services の使用 - Azure Mobile Services" 
	description="Azure Mobile Services と Notification Hubs を使用して Xamarin Android アプリにプッシュ通知を送信する方法について説明します。" 
	services="mobile-services" 
	documentationCenter="xamarin" 
	authors="ggailey777" 
	manager="dwrede" 
	editor="mollybos"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-xamarin-android" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="04/09/2015" 
	ms.author="glenga"/>

# Mobile Services アプリへのプッシュ通知の追加

[AZURE.INCLUDE [mobile-services-selector-get-started-push](../../includes/mobile-services-selector-get-started-push.md)]

このトピックでは、Azure モバイル サービスを使用して Xamarin Android アプリにプッシュ通知を送信する方法について説明します。このチュートリアルでは、Google Cloud Messaging (GCM) を使用するプッシュ通知を [Mobile Services の使用]プロジェクトに追加します。完了すると、モバイル サービスは、レコードが挿入されるたびにプッシュ通知を送信します。

このチュートリアルでは、プッシュ通知を有効にするための、次の基本的な手順について説明します。

1. [Google Cloud Messaging を有効にする](#register)
2. [Mobile Services を構成する](#configure)
3. [プッシュ通知のプロジェクトを構成する](#configure-app)
4. [アプリケーションにプッシュ通知コードを追加する](#add-push)
5. [データを挿入して通知を受け取る](#test)

このチュートリアルには、次のものが必要です。

+ アクティブな Google アカウント。
+ [Google Cloud Messaging のクライアント コンポーネント]。このコンポーネントは、チュートリアル中に追加します。

[Xamarin.Android] コンポーネントと [Azure Mobile Services][Azure Mobile Services Component] コンポーネントは、[Mobile Services の使用]を実行していれば、それ以降はプロジェクトにインストールされているはずです。

##<a id="register"></a>Google Cloud Messaging を有効にする

[AZURE.INCLUDE [GCM を有効にする](../../includes/mobile-services-enable-Google-cloud-messaging.md)]

##<a id="configure"></a>プッシュ要求を送信するように Mobile Services を構成する

[AZURE.INCLUDE [mobile-services-android-configure-push](../../includes/mobile-services-android-configure-push.md)]

##<a id="update-server"></a>Mobile Services を更新してプッシュ通知を送信する

[AZURE.INCLUDE [mobile-services-dotnet-backend-android-push-update-service](../../includes/mobile-services-dotnet-backend-android-push-update-service.md)]

##<a id="configure-app"></a>プッシュ通知の既存のプロジェクトを構成する

[AZURE.INCLUDE [mobile-services-xamarin-android-push-configure-project](../../includes/mobile-services-xamarin-android-push-configure-project.md)]

##<a id="add-push"></a>アプリケーションにプッシュ通知コードを追加する

[AZURE.INCLUDE [mobile-services-xamarin-android-push-add-to-app](../../includes/mobile-services-xamarin-android-push-add-to-app.md)]

##<a name="test-app"></a>発行されたモバイル サービスに対してアプリケーションをテストする

Android フォンを USB ケーブルで直接接続するか、エミュレーターで仮想デバイスを使用する方法により、アプリケーションをテストできます。

###<a id="local-testing"></a>ローカル テストのためにプッシュ通知を有効にする

[AZURE.INCLUDE [mobile-services-dotnet-backend-configure-local-push](../../includes/mobile-services-dotnet-backend-configure-local-push.md)]

[AZURE.INCLUDE [mobile-services-android-push-notifications-test](../../includes/mobile-services-android-push-notifications-test.md)]

<!-- URLs. -->
[Mobile Services の使用]: mobile-services-dotnet-backend-xamarin-android-get-started.md


[Google Cloud Messaging のクライアント コンポーネント]: http://components.xamarin.com/view/GCMClient/
[Xamarin.Android]: http://xamarin.com/download/
[Azure Mobile Services Component]: http://components.xamarin.com/view/azure-mobile-services/

<!---HONumber=August15_HO6-->