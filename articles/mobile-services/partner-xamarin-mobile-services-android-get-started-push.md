---
title: Xamarin Android アプリへのプッシュ通知の追加 | Microsoft Docs
description: Azure Mobile Services と Azure Notification Hubs を使用している Xamarin.Android アプリ向けに、Google Cloud Messaging を使用したプッシュ通知を構成する方法について説明します。
documentationcenter: xamarin
author: ggailey777
manager: dwrede
services: mobile-services
editor: ''

ms.service: mobile-services
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: article
ms.date: 07/21/2016
ms.author: glenga

---
# Mobile Services アプリへのプッシュ通知の追加
[!INCLUDE [mobile-services-selector-get-started-push](../../includes/mobile-services-selector-get-started-push.md)]

&nbsp;

[!INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

> このトピックの Mobile Apps バージョンについては、「[Xamarin.Android アプリへのプッシュ通知の追加](../app-service-mobile/app-service-mobile-xamarin-android-get-started-push.md)」を参照してください。
> 
> 

## 概要
このトピックでは、Azure Mobile Services を使用して Xamarin Android アプリにプッシュ通知を送信する方法について説明します。このチュートリアルでは、Google Cloud Messaging (GCM) を使用するプッシュ通知を [Mobile Services の使用]プロジェクトに追加します。完了すると、モバイル サービスは、レコードが挿入されるたびにプッシュ通知を送信します。

このチュートリアルには、次のものが必要です。

* アクティブな Google アカウント。
* [Google Cloud Messaging のクライアント コンポーネント]。このコンポーネントは、チュートリアル中に追加します。

Xamarin と [Azure Mobile Services コンポーネント]は、「[Mobile Services の使用]」のチュートリアルを完了していれば、プロジェクトに既にインストールされています。

## <a id="register"></a>Google Cloud Messaging を有効にする
[!INCLUDE [mobile-services-enable-Google-cloud-messaging](../../includes/mobile-services-enable-google-cloud-messaging.md)]

## <a id="configure"></a>プッシュ要求を送信するように Mobile Services を構成する
[!INCLUDE [mobile-services-android-configure-push](../../includes/mobile-services-android-configure-push.md)]

## <a id="update-scripts"></a>通知を送信するように登録済み挿入スクリプトを更新する
> [!TIP]
> 次の手順では、Azure クラシック ポータルの TodoItem テーブルに対する挿入操作に登録されているスクリプトを更新する方法を示します。このモバイル サービス スクリプトは、Visual Studio のサーバー エクスプローラーの Azure ノードで、直接アクセスして編集することもできます。
> 
> 

[!INCLUDE [mobile-services-javascript-backend-android-push-insert-script](../../includes/mobile-services-javascript-backend-android-push-insert-script.md)]

## <a id="configure-app"></a>プッシュ通知用に既存のプロジェクトを構成する
[!INCLUDE [mobile-services-xamarin-android-push-configure-project](../../includes/mobile-services-xamarin-android-push-configure-project.md)]

## <a id="add-push"></a>アプリケーションにプッシュ通知コードを追加する
[!INCLUDE [mobile-services-xamarin-android-push-add-to-app](../../includes/mobile-services-xamarin-android-push-add-to-app.md)]

## <a id="test"></a>アプリケーションでプッシュ通知をテストする
Android フォンを USB ケーブルで直接接続するか、エミュレーターで仮想デバイスを使用する方法により、アプリケーションをテストできます。

[!INCLUDE [mobile-services-android-push-notifications-test](../../includes/mobile-services-android-push-notifications-test.md)]

これで、このチュートリアルは終了です。

## <a name="next-steps"></a>次のステップ
Mobile Services と Notification Hubs については次のトピックを参照してください。

* [認証の使用](mobile-services-android-get-started-users.md) <br/>Mobile Services を使用して別のアカウントの種類のアプリケーションのユーザーを認証する方法について説明します。
* [Notification Hubs とは](../notification-hubs/notification-hubs-push-notification-overview.md) <br/>Notification Hubs がすべての主要なクライアント プラットフォーム全体のアプリケーションに通知を配信するための動作を説明します。
* [Notification Hubs のデバッグ](http://go.microsoft.com/fwlink/p/?linkid=386630) </br>Notification Hubs ソリューションのトラブルシューティングとデバッグについて説明します。
* [Mobile Services 向け .NET クライアント ライブラリの使用方法](mobile-services-dotnet-how-to-use-client-library.md) <br/>Xamarin C# コードと共に Mobile Services を使用する方法を説明します。
* [Mobile Services のサーバー スクリプト リファレンス](mobile-services-how-to-use-server-scripts.md) <br/>モバイル サービスでビジネス ロジックを実装する方法を説明します。

<!-- URLs. -->
[Mobile Services の使用]: mobile-services-ios-get-started.md

[Google Cloud Messaging のクライアント コンポーネント]: http://components.xamarin.com/view/GCMClient/
[Azure Mobile Services コンポーネント]: http://components.xamarin.com/view/azure-mobile-services/

<!---HONumber=AcomDC_0727_2016-->