---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 09/08/2021
ms.author: rifox
ms.openlocfilehash: c1d03c13ac4d2f4911baf0315358003dd43e0b3b
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124837816"
---
[!INCLUDE [Install SDK](../install-sdk/install-sdk-ios.md)]

## <a name="set-up-push-notifications"></a>プッシュ通知の設定

モバイル プッシュ通知は、モバイル デバイスで受け取るポップアップ通知です。 通話に関しては、VoIP (ボイス オーバー IP) でのプッシュ通知に焦点を絞ります。 

以下のセクションでは、プッシュ通知の登録、処理、および登録解除を行う方法について説明します。 これらの作業を開始する前に、次の前提条件を満たす必要があります。

1. Xcode で、 **[Signing & Capabilities]\(署名と機能\)** に移動します。 **[+ Capability]\(+ 機能\)** を選択して機能を追加してから、 **[プッシュ通知]** を選択します。
2. **[+ Capability]\(+ 機能\)** を選択して別の機能を追加してから、 **[バックグラウンド モード]** を選びます。
3. **[バックグラウンド モード]** で、 **[ボイス オーバー IP]** と **[リモート通知]** のチェックボックスをオンにします。

:::image type="content" source="../../../../quickstarts/voice-video-calling/media/ios/xcode-push-notification.png" alt-text="Xcode での機能の追加方法を示すスクリーンショット。" lightbox="../../../../quickstarts/voice-video-calling/media/ios/xcode-push-notification.png":::

### <a name="register-for-push-notifications"></a>プッシュ通知に登録する

プッシュ通知に登録するには、デバイス登録トークンを使用して `CallAgent` インスタンスで `registerPushNotification()` を呼び出します。

プッシュ通知への登録は、初期化が正常に完了した後に行われる必要があります。 `callAgent` オブジェクトが破棄されると、`logout` が呼び出されて、プッシュ通知の登録が自動的に解除されます。

```swift
let deviceToken: Data = pushRegistry?.pushToken(for: PKPushType.voIP)
callAgent.registerPushNotifications(deviceToken: deviceToken!) { (error) in
    if(error == nil) {
        print("Successfully registered to push notification.")
    } else {
        print("Failed to register push notification.")
    }
}
```

## <a name="handle-push-notifications"></a>プッシュ通知を処理する
着信通話のプッシュ通知を受信するには、ディクショナリ ペイロードを設定して `CallAgent` インスタンスで `handlePushNotification()` を呼び出します。

```swift
let callNotification = PushNotificationInfo.fromDictionary(pushPayload.dictionaryPayload)

callAgent.handlePush(notification: callNotification) { (error) in
    if (error == nil) {
        print("Handling of push notification was successful")
    } else {
        print("Handling of push notification failed")
    }
}
```
## <a name="unregister-push-notifications"></a>プッシュ通知の登録を解除する

アプリケーションによって、プッシュ通知の登録はいつでも解除できます。 `CallAgent` で `unregisterPushNotification` メソッドを呼び出すだけです。

> [!NOTE]
> ログアウト時に、プッシュ通知からアプリケーションの登録が自動的に解除されることはありません。

```swift
callAgent.unregisterPushNotification { (error) in
    if (error == nil) {
        print("Unregister of push notification was successful")
    } else {
       print("Unregister of push notification failed, please try again")
    }
}
```