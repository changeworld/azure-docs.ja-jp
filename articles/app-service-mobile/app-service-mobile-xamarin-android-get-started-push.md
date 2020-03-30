---
title: Xamarin.Android アプリへのプッシュ通知の追加
description: Azure App Service と Azure Notification Hubs を使用して、Xamarin.Android アプリにプッシュ通知を送信する方法について説明します。
ms.assetid: 6f7e8517-e532-4559-9b07-874115f4c65b
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 5657be0dbaeb46f8f899a9b4a2f8ba9b4fe9ebaa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "79226495"
---
# <a name="add-push-notifications-to-your-xamarinandroid-app"></a>Xamarin.Android アプリへのプッシュ通知の追加

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>概要

このチュートリアルでは、[Xamarin.Android クイックスタート](app-service-mobile-windows-store-dotnet-get-started.md) プロジェクトにプッシュ通知を追加して、レコードが挿入されるたびにプッシュ通知が送信されるようにします。

ダウンロードしたクイックスタートのサーバー プロジェクトを使用しない場合は、プッシュ通知拡張機能パッケージを追加する必要があります。 詳細については、「[Azure Mobile Apps 用 .NET バックエンド サーバー SDK の操作](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルには、次のセットアップが必要です。

* アクティブな Google アカウント。 [accounts.google.com](https://go.microsoft.com/fwlink/p/?LinkId=268302)で、Google アカウントにサインアップできます。
* [Google Cloud Messaging のクライアント コンポーネント](https://components.xamarin.com/view/GCMClient/)。

## <a name="configure-a-notification-hub"></a><a name="configure-hub"></a>通知ハブを構成する

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="enable-firebase-cloud-messaging"></a><a id="register"></a>Firebase Cloud Messaging を有効にする

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="configure-azure-to-send-push-requests"></a>プッシュ要求を送信するように Azure を構成する

[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push-for-firebase.md)]

## <a name="update-the-server-project-to-send-push-notifications"></a><a id="update-server"></a>サーバー プロジェクトをプッシュ通知を送信するように更新する

[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a name="configure-the-client-project-for-push-notifications"></a><a id="configure-app"></a>プッシュ通知のクライアント プロジェクトを構成する

[!INCLUDE [mobile-services-xamarin-android-push-configure-project](../../includes/mobile-services-xamarin-android-push-configure-project.md)]

## <a name="add-push-notifications-code-to-your-app"></a><a id="add-push"></a>アプリケーションにプッシュ通知コードを追加する

[!INCLUDE [app-service-mobile-xamarin-android-push-add-to-app](../../includes/app-service-mobile-xamarin-android-push-add-to-app.md)]

## <a name="test-push-notifications-in-your-app"></a><a name="test"></a>アプリケーションでプッシュ通知をテストする

エミュレーターで仮想デバイスを使用して、アプリをテストできます。 エミュレーターで実行するときに必要な追加の構成手順があります。

1. 仮想デバイスには、Android Virtual Device (AVD) Manager で Google API をターゲットとして設定する必要があります。

    ![](./media/app-service-mobile-xamarin-android-get-started-push/google-apis-avd-settings.png)

2. **[アプリ]**  >  **[設定]**  >  **[アカウントの追加]** をクリックして Android デバイスに Google アカウントを追加した後、画面の指示に従います。

    ![](./media/app-service-mobile-xamarin-android-get-started-push/add-google-account.png)

3. これまでと同様に ToDoList アプリを実行し、新しい ToDo 項目を挿入します。 今回は、通知領域に通知アイコンが表示されます。 通知ドロアーを開いて、通知の全文を表示できます。

    ![](./media/app-service-mobile-xamarin-android-get-started-push/android-notifications.png)

<!-- URLs. -->
[Xamarin.Android quick start]: app-service-mobile-xamarin-android-get-started.md
[Google Cloud Messaging Client Component]: https://components.xamarin.com/view/GCMClient/
[Azure Mobile Services Component]: https://components.xamarin.com/view/azure-mobile-services/
