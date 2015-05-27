<properties 
	pageTitle="Mobile Services アプリへのプッシュ通知の追加 - Mobile Services" 
	description="Azure Mobile Services を使用する Xamarin.Android アプリでプッシュ通知を使用する方法について説明します。" 
	documentationCenter="xamarin" 
	authors="ggailey777" 
	manager="dwrede" 
	services="mobile-services" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-xamarin-android" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="03/12/2015" 
	ms.author="glenga"/>

# Mobile Services アプリへのプッシュ通知の追加

[AZURE.INCLUDE [mobile-services-selector-get-started-push](../includes/mobile-services-selector-get-started-push.md)]

このトピックでは、Azure モバイル サービスを使用して Xamarin Android アプリにプッシュ通知を送信する方法について説明します。このチュートリアルでは、Google Cloud Messaging (GCM) を使用するプッシュ通知を [Mobile Services の使用]プロジェクトに追加します。完了すると、モバイル サービスは、レコードが挿入されるたびにプッシュ通知を送信します。

このチュートリアルでは、プッシュ通知を有効にするための、次の基本的な手順について説明します。

1. [Google Cloud Messaging を有効にする](#register)
2. [Mobile Services を構成する](#configure)
4. [プッシュ通知のプロジェクトを構成する](#configure-app)
5. [アプリケーションにプッシュ通知コードを追加する](#add-push)
6. [データを挿入して通知を受け取る](#test)

このチュートリアルには、次のものが必要です。

+ アクティブな Google アカウント。
+ [Google Cloud Messaging のクライアント コンポーネント]。このコンポーネントは、チュートリアル中に追加します。

[Xamarin.Android] コンポーネントと [Azure Mobile Services][Azure Mobile Services Component] コンポーネントは、[Mobile Services の使用]または[既存のアプリケーションへの Mobile Services の追加]のいずれかを完了していれば、プロジェクトにインストールされています。

##<a id="register"></a>Google Cloud Messaging を有効にする

[AZURE.INCLUDE [mobile-services-enable-Google-cloud-messaging](../includes/mobile-services-enable-Google-cloud-messaging.md)]

##<a id="configure"></a>プッシュ要求を送信するようにモバイル サービスを構成する

[AZURE.INCLUDE [mobile-services-android-configure-push](../includes/mobile-services-android-configure-push.md)]

##<a id="update-scripts"></a>通知を送信するように登録済み挿入スクリプトを更新する

>[AZURE.NOTE]次の手順では、Azure 管理ポータルの TodoItem テーブルに対する挿入操作に登録されているスクリプトを更新する方法を示します。このモバイル サービス スクリプトは、Visual Studio のサーバー エクスプローラーの Azure ノードで、直接アクセスして編集することもできます。

[AZURE.INCLUDE [mobile-services-javascript-backend-android-push-insert-script](../includes/mobile-services-javascript-backend-android-push-insert-script.md)]


##<a id="configure-app"></a>プッシュ通知用に既存のプロジェクトを構成する

[AZURE.INCLUDE [mobile-services-xamarin-android-push-configure-project](../includes/mobile-services-xamarin-android-push-configure-project.md)]

##<a id="add-push"></a>アプリケーションにプッシュ通知コードを追加する

[AZURE.INCLUDE [mobile-services-xamarin-android-push-add-to-app](../includes/mobile-services-xamarin-android-push-add-to-app.md)]

##<a id="test"></a>アプリケーションでプッシュ通知をテストする

Android フォンを USB ケーブルで直接接続するか、エミュレーターで仮想デバイスを使用する方法により、アプリケーションをテストできます。

[AZURE.INCLUDE [mobile-services-android-push-notifications-test](../includes/mobile-services-android-push-notifications-test.md)]

これで、このチュートリアルは終了です。

## <a name="next-steps"></a>次のステップ

Mobile Services と通知ハブについては次のトピックを参照してください。

* [既存のアプリケーションへの Mobile Services の追加] <br/>Mobile Services を使用してデータの格納およびクエリを実行する方法について説明します。

* [認証の使用](partner-xamarin-mobile-services-android-get-started-users.md) <br/>Mobile Services を使用して別のアカウントの種類のアプリケーションのユーザーを認証する方法について説明します。

* [通知ハブとは](notification-hubs-overview.md) <br/>通知ハブがすべての主要なクライアント プラットフォーム全体のアプリケーションに通知を配信するための動作を説明します。

* [通知ハブのデバッグ](http://go.microsoft.com/fwlink/p/?linkid=386630) </br>通知ハブ ソリューションのトラブルシューティングとデバッグについて説明します。

* [Mobile Services 向け .NET クライアント ライブラリの使用方法](mobile-services-windows-dotnet-how-to-use-client-library.md) <br/>Xamarin C# コードと共に Mobile Services を使用する方法を説明します。

* [Mobile Services のサーバー スクリプト リファレンス](mobile-services-how-to-use-server-scripts.md) <br/>モバイル サービスでビジネス ロジックを実装する方法を説明します。

<!-- URLs. -->
[Mobile Services の使用]: partner-xamarin-mobile-services-ios-get-started.md
[既存のアプリケーションへの Mobile Services の追加]: partner-xamarin-mobile-services-android-get-started-data.md

[Google Cloud Messaging のクライアント コンポーネント]: http://components.xamarin.com/view/GCMClient/
[Xamarin.Android]: http://xamarin.com/download/
[Azure Mobile Services Component]: http://components.xamarin.com/view/azure-mobile-services/
<!--HONumber=54-->