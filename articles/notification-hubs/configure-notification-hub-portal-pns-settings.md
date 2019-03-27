---
title: PNS 設定を使用した Azure 通知ハブの構成 | Microsoft Docs
description: このクイック スタートでは、Azure portal 内でプラットフォーム通知システム (PNS) 設定を使用して通知ハブを構成する方法について説明します。
services: notification-hubs
author: jwargo
manager: patniko
editor: spelluru
ms.service: notification-hubs
ms.workload: mobile
ms.topic: quickstart
ms.date: 02/14/2019
ms.author: jowargo
ms.openlocfilehash: 7f7e4a4d75a8e118da6f026817bc4ecfcc7a60db
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/15/2019
ms.locfileid: "56313967"
---
# <a name="configure-an-azure-notification-hub-with-platform-notification-system-settings-in-the-azure-portal"></a>Azure portal 内でプラットフォーム通知システム設定を使用して Azure 通知ハブを構成する 
Azure Notification Hubs は、任意のバックエンド (クラウドまたはオンプレミス) から任意のプラットフォーム (iOS、Android、Windows、Kindle、Baidu など) に通知を送信できる、使いやすく、かつスケールアウトされたプッシュ エンジンを提供します。 このサービスの詳細については、「[Azure Notification Hubs とは](notification-hubs-push-notification-overview.md)」を参照してください。

まだ行っていなければ、[Azure portal を使用して Azure 通知ハブを作成してください](create-notification-hub-portal.md)。 このクイック スタートでは、Azure portal 内でプラットフォーム通知システム (PNS) 設定を使用して通知ハブを構成する方法について説明します。

## <a name="apple-push-notification-service-apns"></a>Apple Push Notification Service (APNS)
1. Azure portal の **[通知ハブ]** ページで、左側のメニューの **[設定]** の下にある **[Apple (APNS)]** を選択します。
2. **[証明書]** を選択した場合は、次のアクションを実行します。
    1. **ファイル アイコン**を選択し、アップロードする **.p12** ファイルを選びます。 
    2. **パスワード**を指定します。
    3. **[サンドボックス]** モードを選択します。 **[実稼働]** は、ストアからアプリを購入したユーザーにプッシュ通知を送信する場合にのみ使用します。

        ![Azure Portal で APNS 証明書を構成する](./media/notification-hubs-ios-get-started/notification-hubs-apple-config-cert.png)
3. **[トークン]** を選択した場合は、次の手順を実行します。 
    1. **[キー ID]**、**[バンドル ID]**、**[チーム ID]**、および **[トークン]** に値を入力します。
    2. **[サンドボックス]** モードを選択します。 **[実稼働]** は、ストアからアプリを購入したユーザーにプッシュ通知を送信する場合にのみ使用します。

        ![Azure portal 内で APNs トークンを構成する](./media/notification-hubs-ios-get-started/notification-hubs-apple-config-token.png)

Azure Notification Hubs と Apple Push Notification Service (APNs) を使用して iOS デバイスにプッシュ通知を送信する完全なチュートリアルについては、[このチュートリアル](notification-hubs-ios-apple-push-notification-apns-get-started.md)を参照してください。

## <a name="google-firebase-cloud-messaging-fcm"></a>Google Firebase Cloud Messaging (FCM)
1. Azure portal の **[通知ハブ]** ページで、左側のメニューの **[設定]** の下にある **[Google (GCM/FCM)]** を選択します。 
2. 前に保存した FCM プロジェクトの**サーバー キー**を貼り付けます。 
3. ツールバーの **[保存]** を選択します。 

    ![Azure Notification Hubs - Google (FCM)](./media/notification-hubs-android-push-notification-google-fcm-get-started/fcm-server-key.png)
4. 通知ハブが正常に更新されたことを示すアラートのメッセージが表示されます。 **[Save]\(保存\)** ボタンが無効になります。 

Azure Notification Hubs と Google Firebase Cloud Messaging を使用して Android デバイスにプッシュ通知を送信する完全なチュートリアルについては、[このチュートリアル](notification-hubs-android-push-notification-google-fcm-get-started.md)を参照してください。

## <a name="windows-push-notification-service-wns"></a>Windows プッシュ通知サービス (WNS)
1. Azure portal の **[通知ハブ]** ページで、左側のメニューの **[設定]** の下にある **[Windows (WNS)]** を選択します。
2. **[パッケージ SID]** と **[セキュリティ キー]** に値を入力します。
3. ツールバーの **[保存]** を選択します。

    ![[パッケージ SID] ボックスと [セキュリティ キー] ボックス](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png)


Windows デバイス上で実行されているユニバーサル Windows プラットフォーム (UWP) アプリにプッシュ通知を送信する完全なチュートリアルについては、[このチュートリアル](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)を参照してください。

## <a name="windows-phone---microsoft-push-notification-service"></a>Windows Phone - Microsoft プッシュ通知サービス
1. Azure portal の **[通知ハブ]** ページで、**[設定]** の下にある **[Windows Phone (MPNS)]** を選択します。
2. 非認証プッシュを有効にする場合は、**[非認証プッシュを有効にする]** を選択し、ツール バーの **[保存]** を選択します。

    ![Azure Portal - [非認証プッシュを有効にする]](./media/notification-hubs-windows-phone-get-started/azure-portal-unauth.png)
3. **認証済み**プッシュを使用する場合は、次の手順に従います。
    1. ツール バーの **[証明書のアップロード]** を選択します。
    2. **ファイル アイコン**を選択し、証明書ファイルを選びます。
    3. 証明書の**パスワード**を入力します。 
    4. **[OK]** を選択して、**[証明書のアップロード]** ページを閉じます。 
    5. **[Windows Phone(MPNS)]** ページで、ツール バーの **[保存]** を選択します。

Microsoft プッシュ通知サービス (MPNS) を使用して Windows Phone 8 アプリにプッシュ通知を送信する完全なチュートリアルについては、[このチュートリアル](notification-hubs-windows-mobile-push-notifications-mpns.md)を参照してください。
      
## <a name="amazon-device-messaging-adm"></a>Amazon Device Messaging (ADM)
1. Azure portal の **[通知ハブ]** ページで、左側のメニューの **[設定]** の下にある **[Amazon (ADM)]** を選択します。
2. **[クライアント ID]** と **[クライアント シークレット]** に値を入力します。
3. ツールバーの **[保存]** を選択します。
    
    ![Azure Notification Hubs - ADM の設定](./media/notification-hubs-kindle-get-started/notification-hub-adm-settings.png)

Azure Notification Hubs を使用して Kindle アプリケーションにプッシュ通知を送信する完全なチュートリアルについては、[このチュートリアル](notification-hubs-kindle-amazon-adm-push-notification.md)を参照してください。

## <a name="baidu-android-china"></a>Baidu (Android China)
1. Azure portal の **[通知ハブ]** ページで、左側のメニューの **[設定]** の下にある **[Baidu (Android China)]** を選択します。 
2. Baidu コンソールから取得した、Baidu クラウド プッシュ プロジェクトの **API キー**を入力します。 
3. Baidu コンソールから取得した、Baidu クラウド プッシュ プロジェクトの**秘密鍵**を入力します。 
4. ツールバーの **[保存]** を選択します。 

    ![Azure Notification Hubs - Baidu (Android China)](./media/notification-hubs-baidu-get-started/AzureNotificationServicesBaidu.png)
4. 通知ハブが正常に更新されたことを示すアラートのメッセージが表示されます。 **[Save]\(保存\)** ボタンが無効になります。 

Azure Notification Hubs と Baidu クラウド プッシュを使用してプッシュ通知を送信する完全なチュートリアルについては、[このチュートリアル](notification-hubs-baidu-china-android-notifications-get-started.md)を参照してください。

## <a name="next-steps"></a>次の手順
このクイック スタートでは、Azure portal 内で通知ハブに対してさまざまなプラットフォーム通知システムを構成する方法について説明しました。 

これらの各プラットフォームにプッシュ通知を送信するステップバイステップの完全な手順については、「**チュートリアル**」セクションにあるチュートリアルを参照してください。

- [Azure Notification Hubs と Apple Push Notification Service (APNs) を使用して iOS デバイスにプッシュ通知を送信する](notification-hubs-ios-apple-push-notification-apns-get-started.md)。
- [Azure Notification Hubs と Google Firebase Cloud Messaging を使用して Android デバイスにプッシュ通知を送信する](notification-hubs-android-push-notification-google-fcm-get-started.md)。
- [Windows デバイス上で実行されているユニバーサル Windows プラットフォーム (UWP) アプリにプッシュ通知を送信する](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)。
- [Microsoft プッシュ通知サービス (MPNS) を使用して Windows Phone 8 アプリにプッシュ通知を送信する](notification-hubs-windows-mobile-push-notifications-mpns.md)。
- [Kindle アプリケーションにプッシュ通知を送信する](notification-hubs-kindle-amazon-adm-push-notification.md)。
- [Azure Notification Hubs と Baidu クラウド プッシュを使用してプッシュ通知を送信する](notification-hubs-baidu-china-android-notifications-get-started.md)。
