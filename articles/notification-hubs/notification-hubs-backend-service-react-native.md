---
title: バックエンド サービス経由で Azure Notification Hubs を使用して React Native アプリにプッシュ通知を送信する | Microsoft Docs
description: バックエンド サービス経由で Azure Notification Hubs を使用する React Native アプリにプッシュ通知を送信する方法を学習します。
author: alexeystrakh
ms.service: notification-hubs
ms.topic: tutorial
ms.date: 06/11/2020
ms.author: alstrakh
ms.openlocfilehash: 30cf22d99a675d4525d972aeb889cec30a2e9b94
ms.sourcegitcommit: bcb962e74ee5302d0b9242b1ee006f769a94cfb8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2020
ms.locfileid: "86060474"
---
# <a name="tutorial-send-push-notifications-to-react-native-apps-using-azure-notification-hubs-via-a-backend-service"></a>チュートリアル:バックエンド サービス経由で Azure Notification Hubs を使用して React Native アプリにプッシュ通知を送信する  

[![サンプルのダウンロード](./media/notification-hubs-backend-service-react-native/download.png) サンプルをダウンロードします](https://github.com/xamcat/mobcat-samples/tree/master/notification_hub_backend_service)  

> [!div class="op_single_selector"]
>
> * [Xamarin.Forms](notification-hubs-backend-service-xamarin-forms.md)
> * [React Native](notification-hubs-backend-service-react-native.md)

このチュートリアルでは、[Azure Notification Hubs](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-overview) を使用して、**Android** と **iOS** をターゲットとする [React Native](https://reactnative.dev/) アプリケーションにプッシュ通知を送信します。  

[!INCLUDE [Notification Hubs Backend Service Introduction](../../includes/notification-hubs-backend-service-introduction.md)]

このチュートリアルでは、次の手順について説明します。

> [!div class="checklist"]
>
> * [プッシュ通知サービスと Azure Notification Hubs を設定します。](#set-up-push-notification-services-and-azure-notification-hub)
> * [ASP.NET Core Web API バックエンド アプリケーションを作成します。](#create-an-aspnet-core-web-api-backend-application)
> * [クロスプラットフォームの React Native アプリケーションを作成します。](#create-a-cross-platform-react-native-application)
> * [プッシュ通知用のネイティブ Android プロジェクトを構成します。](#configure-the-native-android-project-for-push-notifications)
> * [プッシュ通知用のネイティブ iOS プロジェクトを構成します。](#configure-the-native-ios-project-for-push-notifications)
> * [ソリューションをテストします。](#test-the-solution)

## <a name="prerequisites"></a>前提条件

手順を進めてゆくには、以下が必要です。

* リソースを作成および管理できる [Azure サブスクリプション](https://portal.azure.com)。
* [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) がインストールされている Mac (または **.NET によるモバイル開発**のワークロードを備えた、[Visual Studio 2019](https://visualstudio.microsoft.com/vs) を実行している PC)。
* **Android** (物理デバイスまたはエミュレーター デバイス) または **iOS** (物理デバイスのみ) のいずれかでアプリを実行する機能。

Android の場合は次のものが必要です。

* 開発者用のロック解除済みの物理デバイスまたはエミュレーター *(API 26 以降を実行していて Google Play 開発者サービスがインストールされているもの)* 。

iOS の場合は次のものが必要です。

* アクティブな [Apple Developer アカウント](https://developer.apple.com)。
* [開発者アカウントに登録](https://help.apple.com/developer-account/#/dev40df0d9fa)されている物理 iOS デバイス *(iOS 13.0 以降を実行しているもの)* 。
* [物理デバイスでアプリを実行](https://help.apple.com/xcode/mac/current/#/dev5a825a1ca)できるように **.p12** [開発証明書](https://help.apple.com/developer-account/#/dev04fd06d56)が**キーチェーン**にインストールされている。

> [!NOTE]
> iOS シミュレーターではリモート通知がサポートされていないため、このサンプルを iOS で試すときには物理デバイスが必要です。 ただし、このチュートリアルを完了するために、アプリを **Android** と **iOS** の両方で実行する必要はありません。

以前に経験がなくとも、この第一原理の例に記載されている手順に従うことができます。 ただし、以下の面について知識があると役立ちます。

* [Apple Developer ポータル](https://developer.apple.com)
* [ASP.NET Core](https://docs.microsoft.com/aspnet/core/introduction-to-aspnet-core?view=aspnetcore-3.1)
* [Google Firebase Console](https://console.firebase.google.com/u/0/)
* [Microsoft Azure](https://portal.azure.com) と、[Azure Notification Hubs を使用して iOS アプリにプッシュ通知を送信すること](ios-sdk-get-started.md)。
* [React Native](https://reactnative.dev/docs/getting-started)

示される手順は [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) と [Visual Studio Code](https://code.visualstudio.com/download) を対象としていますが、[Visual Studio 2019](https://visualstudio.microsoft.com/vs) を使用して進めることもできます。

## <a name="set-up-push-notification-services-and-azure-notification-hub"></a>プッシュ通知サービスと Azure Notification Hubs を設定する

このセクションでは、 **[Firebase Cloud Messaging (FCM)](https://firebase.google.com/docs/cloud-messaging)** と **[Apple Push Notification Services (APNS)](https://developer.apple.com/library/archive/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html)** をセットアップします。 次に、通知ハブを作成し、それらのサービスと連携するように構成します。

[!INCLUDE [Create a Firebase project and enable Firebase Cloud Messaging](../../includes/notification-hubs-common-enable-firebase-cloud-messaging.md)]

[!INCLUDE [Enable Apple Push Notifications](../../includes/notification-hubs-common-enable-apple-push-notifications.md)]

[!INCLUDE [Create a notification hub](../../includes/notification-hubs-common-create-notification-hub.md)]

[!INCLUDE [Configure your notification hub with APNs information](../../includes/notification-hubs-common-configure-with-apns-information.md)]

[!INCLUDE [Configure your notification hub with FCM information](../../includes/notification-hubs-common-configure-with-fcm-information.md)]

## <a name="create-an-aspnet-core-web-api-backend-application"></a>ASP.NET Core Web API バックエンド アプリケーションを作成する

このセクションでは、[デバイスの登録](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-registration-management#what-is-device-registration)と React Native モバイル アプリへの通知の送信を処理する [ASP.NET Core Web API](https://dotnet.microsoft.com/apps/aspnet/apis) バックエンドを作成します。

[!INCLUDE [Create an ASP.NET Core Web API backend application](../../includes/notification-hubs-backend-service-web-api.md)]

## <a name="create-a-cross-platform-react-native-application"></a>クロスプラットフォームの React Native アプリケーションを作成する

このセクションでは、クロスプラットフォーム方式でプッシュ通知を実装する [React Native](https://reactnative.dev/) モバイル アプリケーションを構築します。

[!INCLUDE [Sample application generic overview](../../includes/notification-hubs-backend-service-sample-app-overview.md)]

[!INCLUDE [Create React Native application](../../includes/notification-hubs-backend-service-sample-app-reactnative.md)]

## <a name="configure-the-native-android-project-for-push-notifications"></a>プッシュ通知用のネイティブ Android プロジェクトを構成する

[!INCLUDE [Configure the native Android project](../../includes/notification-hubs-backend-service-configure-reactnative-android.md)]

## <a name="configure-the-native-ios-project-for-push-notifications"></a>プッシュ通知用のネイティブ iOS プロジェクトを構成する

[!INCLUDE [Configure the native iOS project](../../includes/notification-hubs-backend-service-configure-reactnative-ios.md)]

## <a name="test-the-solution"></a>ソリューションをテストする

これで、バックエンド サービス経由で通知の送信をテストできるようになりました。

[!INCLUDE [Testing the solution](../../includes/notification-hubs-backend-service-testing.md)]

## <a name="next-steps"></a>次のステップ

これで、バックエンド サービスを介して通知ハブに接続された基本的な React Native アプリが完成し、通知を送受信できるようになりました。

[!INCLUDE [Next steps](../../includes/notification-hubs-backend-service-next-steps.md)]

## <a name="troubleshooting"></a>トラブルシューティング

[!INCLUDE [Troubleshooting](../../includes/notification-hubs-backend-service-troubleshooting.md)]

## <a name="related-links"></a>関連リンク

* [Azure Notification Hubs の概要](notification-hubs-push-notification-overview.md)
* [Visual Studio for Mac のインストール](https://docs.microsoft.com/visualstudio/mac/installation?view=vsmac-2019)
* [Visual Studio Code のインストール](https://code.visualstudio.com/download)
* [React Native 開発環境の設定](https://reactnative.dev/docs/environment-setup)
* [バックエンド操作用の Notification Hubs SDK](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)
* [GitHub 上の Notification Hubs SDK](https://github.com/Azure/azure-notificationhubs)
* [アプリケーション バックエンドへの登録](notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification.md)
* [登録管理](notification-hubs-push-notification-registration-management.md)
* [タグの使用](notification-hubs-tags-segment-push-message.md)
* [カスタム テンプレートの使用](notification-hubs-templates-cross-platform-push-messages.md)
