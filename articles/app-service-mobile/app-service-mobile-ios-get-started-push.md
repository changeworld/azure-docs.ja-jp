<properties
	pageTitle="Azure Mobile Apps を使用した、iOS アプリへのプッシュ通知の追加"
	description="Azure Mobile Apps を使用して iOS アプリにプッシュ通知を送信する方法について説明します。"
	services="app-service\mobile"
	documentationCenter="ios"
	manager="dwrede"
	editor=""
	authors="krisragh"/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="06/28/2016"
	ms.author="krisragh"/>


# iOS アプリへのプッシュ通知の追加

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## 概要
このチュートリアルでは、[iOS クイック スタート] プロジェクトにプッシュ通知を追加して、レコードが挿入されるたびにプッシュ通知が送信されるようにします。最初に、このチュートリアルの基になっている [iOS のクイック スタート] チュートリアルを完了しておく必要があります。ダウンロードしたクイック スタートのサーバー プロジェクトを使用しない場合は、プッシュ通知拡張機能パッケージをプロジェクトに追加する必要があります。サーバーの拡張機能パッケージの詳細については、「[Work with the .NET backend server SDK for Azure Mobile Apps (Azure Mobile Apps 用の .NET バックエンド サーバー SDK を操作する)](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)」を参照してください。

[iOS シミュレーターはプッシュ通知をサポートしない](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator.html)ため、このチュートリアルでは物理的な iOS デバイスと [Apple Developer Program メンバーシップ](https://developer.apple.com/programs/ios/)が必要です。

##<a name="create-hub"></a>通知ハブを作成する

[AZURE.INCLUDE [app-service-mobile-create-notification-hub](../../includes/app-service-mobile-create-notification-hub.md)]

## <a id="register"></a>アプリをプッシュ通知用に登録する

[AZURE.INCLUDE [Apple プッシュ通知を有効にする](../../includes/enable-apple-push-notifications.md)]

## プッシュ通知を送信するように Azure を構成する

[AZURE.INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

##<a id="update-server"></a>サーバー プロジェクトを更新してプッシュ通知を送信する

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-configure-push-apns](../../includes/app-service-mobile-dotnet-backend-configure-push-apns.md)]

## <a id="add-push"></a>プッシュ通知をアプリに追加する

[AZURE.INCLUDE [app-service-mobile-add-push-notifications-to-ios-app.md](../../includes/app-service-mobile-add-push-notifications-to-ios-app.md)]

## <a id="test"></a>アプリでプッシュ通知をテストする

[AZURE.INCLUDE [アプリでプッシュ通知をテストする](../../includes/test-push-notifications-in-app.md)]

##<a id="more"></a>詳細

* テンプレートを利用すれば、プラットフォーム間のプッシュやローカライズされたプッシュを柔軟に送信できます。テンプレートの登録方法は「[Azure Mobile Services 向け iOS クライアント ライブラリの使用方法](app-service-mobile-ios-how-to-use-client-library.md#templates)」に記載されています。
* タグを利用すれば、特定の区分に属する顧客にプッシュで的を絞ることができます。デバイス インストールにタグを追加する方法は「[Azure Mobile Apps 用 .NET バックエンド サーバー SDK の操作](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#tags)」に記載されています。

<!-- Anchors.  -->
[Generate iOS certificate signing request]: #certificates
[Register your app and enable push notifications]: #register
[Create a provisioning profile for the app]: #profile
[Add push notifications to the app]: #add-push
[Configure your mobile backend to send push requests]: #configure
[Update the server to send push notifications]: #update-server
[Publish the mobile backend to Azure]: #publish-mobile-service
[Test your app]: #test-the-service

<!-- Images. -->

<!-- URLs. -->
[iOS のクイック スタート]: app-service-mobile-ios-get-started.md
[iOS クイック スタート]: app-service-mobile-ios-get-started.md

<!---HONumber=AcomDC_0706_2016-->