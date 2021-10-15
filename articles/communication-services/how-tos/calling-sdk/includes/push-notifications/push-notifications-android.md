---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 09/08/2021
ms.author: rifox
ms.openlocfilehash: 9ecf53a84a804cd312f4d3a98661f4e688726577
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124837814"
---
[!INCLUDE [Install SDK](../install-sdk/install-sdk-android.md)]

### <a name="additional-prerequisites-for-push-notifications"></a>プッシュ通知の追加の前提条件

Cloud Messaging (FCM) が有効になっている Firebase アカウントが設定されており、Firebase Cloud Messaging サービスが Azure Notification Hubs インスタンスに接続されているものとします。 詳細については、「[Communication Services の通知](../../../../concepts/notifications.md)」を参照してください。
また、このチュートリアルでは、Android Studio バージョン 3.6 以降を使用してアプリケーションがビルドされているものとします。

Firebase Cloud Messaging から通知メッセージを受信するには、Android アプリケーションに対する一連のアクセス許可が必要です。 `AndroidManifest.xml` ファイルで、`<manifest ...>` の直後か、または `</application>` タグの下に次の一連のアクセス許可を追加します。

```xml
<uses-permission android:name="android.permission.INTERNET"/>
<uses-permission android:name="android.permission.GET_ACCOUNTS"/>
<uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
```

## <a name="overview"></a>概要
モバイル プッシュ通知は、モバイル デバイスに表示されるポップアップ通知です。 通話に関しては、VoIP (ボイス オーバー IP 通話) でのプッシュ通知に焦点を絞ります。 プッシュ通知の登録を行い、プッシュ通知を処理してから、プッシュ通知を登録解除します。

## <a name="register-for-push-notifications"></a>プッシュ通知に登録する

プッシュ通知を登録するには、アプリケーションは、デバイス登録トークンを使用して `CallAgent` インスタンスの `registerPushNotification()` を呼び出す必要があります。

デバイス登録トークンを取得するには、お使いのアプリケーション モジュールの `build.gradle` ファイルの `dependencies` セクションに次の行を追加して、Firebase SDK を追加します。

```groovy
// Add the SDK for Firebase Cloud Messaging
implementation 'com.google.firebase:firebase-core:16.0.8'
implementation 'com.google.firebase:firebase-messaging:20.2.4'
```

まだない場合は、お使いのプロジェクト レベルの *build.gradle* ファイルの `dependencies` セクションに以下を追加します。

```groovy
classpath 'com.google.gms:google-services:4.3.3'
```

まだない場合、ファイルの先頭に次のプラグインを追加します。

```groovy
apply plugin: 'com.google.gms.google-services'
```

ツール バーの *[今すぐ同期]* を選択します。 次のコード スニペットを追加し、クライアント アプリケーション インスタンス用に Firebase Cloud Messaging SDK で生成されたデバイス登録トークンを取得します。メイン Activity のヘッダーには、インスタンス用に下の import を必ず追加するようにしてください。 これらは、スニペットでトークンを取得するために必要です。

```java
import com.google.android.gms.tasks.OnCompleteListener;
import com.google.android.gms.tasks.Task;
import com.google.firebase.iid.FirebaseInstanceId;
import com.google.firebase.iid.InstanceIdResult;
```

トークンを取得するには、このスニペットを追加します。

```java
FirebaseInstanceId.getInstance().getInstanceId()
    .addOnCompleteListener(new OnCompleteListener<InstanceIdResult>() {
        @Override
        public void onComplete(@NonNull Task<InstanceIdResult> task) {
            if (!task.isSuccessful()) {
                Log.w("PushNotification", "getInstanceId failed", task.getException());
                return;
            }

            // Get new Instance ID token
            String deviceToken = task.getResult().getToken();
            // Log
            Log.d("PushNotification", "Device Registration token retrieved successfully");
        }
    });
```
着信通話プッシュ通知用の通話サービス SDKに、デバイス登録トークンを登録します。

```java
String deviceRegistrationToken = "<Device Token from previous section>";
try {
    callAgent.registerPushNotification(deviceRegistrationToken).get();
}
catch(Exception e) {
    System.out.println("Something went wrong while registering for Incoming Calls Push Notifications.")
}
```

## <a name="push-notification-handling"></a>プッシュ通知を処理する

着信通話プッシュ通知を受信するには、ペイロードを設定して *CallAgent* インスタンスで *handlePushNotification()* を呼び出します。

Firebase Cloud Messaging からペイロードを取得するには、*FirebaseMessagingService* Firebase SDK クラスを拡張し、`onMessageReceived` メソッドをオーバーライドする、新しいサービスをまず作成 ([ファイル] > [新規] > [サービス] > [サービス]) します。 このメソッドは、Firebase Cloud Messaging によってアプリケーションにプッシュ通知が配信されると呼び出されるイベント ハンドラーです。

```java
public class MyFirebaseMessagingService extends FirebaseMessagingService {
    private java.util.Map<String, String> pushNotificationMessageDataFromFCM;

    @Override
    public void onMessageReceived(RemoteMessage remoteMessage) {
        // Check if message contains a notification payload.
        if (remoteMessage.getNotification() != null) {
            Log.d("PushNotification", "Message Notification Body: " + remoteMessage.getNotification().getBody());
        }
        else {
            pushNotificationMessageDataFromFCM = remoteMessage.getData();
        }
    }
}
```
次のサービス定義を `AndroidManifest.xml` ファイルの `<application>` タグ内に追加します。

```xml
<service
    android:name=".MyFirebaseMessagingService"
    android:exported="false">
    <intent-filter>
        <action android:name="com.google.firebase.MESSAGING_EVENT" />
    </intent-filter>
</service>
```

- ペイロードを取得したら、*CallAgent* インスタンスで *handlePushNotification* メソッドを呼び出すことによって処理されるように、*Communication Services* の SDK にそれを渡して内部 *IncomingCallInformation* オブジェクトに解析することができます。 `CallAgent` インスタンスを作成するには、`CallClient` クラスで `createCallAgent(...)` メソッドを呼び出します。

```java
try {
    IncomingCallInformation notification = IncomingCallInformation.fromMap(pushNotificationMessageDataFromFCM);
    Future handlePushNotificationFuture = callAgent.handlePushNotification(notification).get();
}
catch(Exception e) {
    System.out.println("Something went wrong while handling the Incoming Calls Push Notifications.");
}
```

プッシュ通知メッセージの処理が成功し、すべてのイベント ハンドラーが適切に登録されると、アプリケーションによって発信されます。

## <a name="unregister-push-notifications"></a>プッシュ通知の登録を解除する

アプリケーションによって、プッシュ通知の登録はいつでも解除できます。 登録を解除するには、callAgent で `unregisterPushNotification()` メソッドを呼び出します。

```java
try {
    callAgent.unregisterPushNotification().get();
}
catch(Exception e) {
    System.out.println("Something went wrong while un-registering for all Incoming Calls Push Notifications.")
}
```