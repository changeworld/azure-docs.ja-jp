<properties
	pageTitle="Azure Mobile Apps を使用して Android アプリにプッシュ通知を追加する"
	description="Azure Mobile Apps を使用して Android アプリにプッシュ通知を送信する方法について説明します。"
	services="app-service\mobile"
	documentationCenter="android"
	manager="dwrede"
	editor=""
	authors="ysxu"/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="article"
	ms.date="02/03/2016"
	ms.author="yuaxu"/>

# プッシュ通知を Android アプリに追加する

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]&nbsp;

## 概要
このチュートリアルでは、[Android クイック スタート] プロジェクトにプッシュ通知を追加して、レコードが挿入されるたびにプッシュ通知が送信されるようにします。最初に、このチュートリアルの基になっている「[Android クイック スタート]」チュートリアルを完了しておく必要があります。ダウンロードしたクイック スタートのサーバー プロジェクトを使用しない場合は、プッシュ通知拡張機能パッケージをプロジェクトに追加する必要があります。サーバーの拡張機能パッケージの詳細については、「[Work with the .NET backend server SDK for Azure Mobile Apps (Azure Mobile Apps 用の .NET バックエンド サーバー SDK を操作する)](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)」を参照してください。

##前提条件

このチュートリアルを完了するには、以下が必要です。

* 確認済みの電子メール アドレスがある [Google アカウント](http://go.microsoft.com/fwlink/p/?LinkId=268302)
* [Visual Studio Community 2013](https://go.microsoft.com/fwLink/p/?LinkID=391934) (Node.js バックエンド プロジェクトには不要)
* [クイック スタート チュートリアル](app-service-mobile-android-get-started.md)を完了していること。

##<a name="create-hub"></a>通知ハブを作成する

[AZURE.INCLUDE [app-service-mobile-create-notification-hub](../../includes/app-service-mobile-create-notification-hub.md)]

## Google Cloud Messaging を有効にする

[AZURE.INCLUDE [mobile-services-enable-google-cloud-messaging](../../includes/mobile-services-enable-google-cloud-messaging.md)]

##プッシュ要求を送信するように Mobile App バックエンドを構成する

[AZURE.INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

##<a id="update-service"></a>サーバー プロジェクトをプッシュ通知を送信するように更新する

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-configure-push-google](../../includes/app-service-mobile-dotnet-backend-configure-push-google.md)]

## アプリケーションにプッシュ通知を追加する

Android アプリ プロジェクトでプッシュ通知を処理する準備が整っていることを確認する必要があります。

###Android SDK バージョンの検証

[AZURE.INCLUDE [app-service-mobile-verify-android-sdk-version](../../includes/app-service-mobile-verify-android-sdk-version.md)]

次の手順は、Google Play サービスをインストールすることです。Google Cloud Messaging には、マニフェストの **minSdkVersion** プロパティが準拠する必要がある、開発およびテストに関する最小 API レベル要件があります。

古いデバイスを使用している場合は、Google Play Services SDK のセットアップに関するページを参考に、どれだけ小さな値を設定できるか判断し、適切に設定してください。

###プロジェクトへの Google Play Services の追加

[AZURE.INCLUDE [Play サービスの追加](../../includes/app-service-mobile-add-google-play-services.md)]

###コードの追加

[AZURE.INCLUDE [app-service-mobile-android-getting-started-with-push](../../includes/app-service-mobile-android-getting-started-with-push.md)]

## 発行されたモバイル サービスに対してアプリケーションをテストする

Android フォンを USB ケーブルで直接接続するか、エミュレーターで仮想デバイスを使用する方法により、アプリケーションをテストできます。

##<a id="more"></a>詳細

* タグを利用すれば、特定の区分に属する顧客にプッシュで的を絞ることができます。デバイス インストールにタグを追加する方法は「[Azure Mobile Apps 用 .NET バックエンド サーバー SDK の操作](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)」に記載されています。

<!-- URLs -->
[Android クイック スタート]: app-service-mobile-android-get-started.md

<!---HONumber=AcomDC_0204_2016-->