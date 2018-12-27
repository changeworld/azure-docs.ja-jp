---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: 654bc3745768fccea41d7c3991142bf7183b54be
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/24/2018
ms.locfileid: "42809659"
---
1. **アプリ** プロジェクトで、`AndroidManifest.xml` ファイルを開きます。 `application` 開始タグの後に次のコードを追加します。

    ```xml
    <service android:name=".ToDoMessagingService">
        <intent-filter>
            <action android:name="com.google.firebase.MESSAGING_EVENT"/>
        </intent-filter>
    </service>
    <service android:name=".ToDoInstanceIdService">
        <intent-filter>
            <action android:name="com.google.firebase.INSTANCE_ID_EVENT"/>
        </intent-filter>
    </service>
    ```

2. `ToDoActivity.java` ファイルを開き、次のように変更します。

    - import ステートメントを追加します。

        ```java
        import com.google.firebase.iid.FirebaseInstanceId;
        ```

    - `MobileServiceClient` の定義を、**private** から **private static** に変更し、次のようにします。

        ```java
        private static MobileServiceClient mClient;
        ```

    - `registerPush` メソッドを追加します。

        ```java
        public static void registerPush() {
            final String token = FirebaseInstanceId.getInstance().getToken();
            if (token != null) {
                new AsyncTask<Void, Void, Void>() {
                    protected Void doInBackground(Void... params) {
                        mClient.getPush().register(token);
                        return null;
                    }
                }.execute();
            }
        }
        ```

    - `ToDoActivity` クラスの **onCreate** メソッドを更新します。 `MobileServiceClient` がインスタンス化された後に、このコードを追加します。

        ```java
        registerPush();
        ```

3. 通知を処理する新しいクラスを追加します。 プロジェクト エクスプローラーで、**[app]** > **java** > **<お使いのプロジェクト名前空間>** ノードの順に開き、パッケージ名のノードを右クリックします。 **[New (新規)]** をクリックし、**[Java Class (Java クラス)]** をクリックします。 [名前] に「`ToDoMessagingService`」と入力して、[OK] をクリックします。 次に、クラスの宣言を以下の内容に置き換えます。

    ```java
    import android.app.Notification;
    import android.app.NotificationManager;
    import android.app.PendingIntent;
    import android.content.Context;
    import android.content.Intent;

    import com.google.firebase.messaging.FirebaseMessagingService;
    import com.google.firebase.messaging.RemoteMessage;

    public class ToDoMessagingService extends FirebaseMessagingService {

        private static final int NOTIFICATION_ID = 1;

        @Override
        public void onMessageReceived(RemoteMessage remoteMessage) {
            String message = remoteMessage.getData().get("message");
            if (message != null) {
                sendNotification("Notification Hub Demo", message);
            }
        }

        private void sendNotification(String title, String messageBody) {
            PendingIntent contentIntent = PendingIntent.getActivity(this, 0, new Intent(this, ToDoActivity.class), 0);
            Notification.Builder notificationBuilder = new Notification.Builder(this)
                    .setSmallIcon(R.drawable.ic_launcher)
                    .setContentTitle(title)
                    .setContentText(messageBody)
                    .setContentIntent(contentIntent);
            NotificationManager notificationManager = (NotificationManager) getSystemService(Context.NOTIFICATION_SERVICE);
            if (notificationManager != null) {
                notificationManager.notify(NOTIFICATION_ID, notificationBuilder.build());
            }
        }
    }
    ```

4. トークンの更新を処理する別のクラスを追加します。 `ToDoInstanceIdService` Java クラスを作成し、クラスの宣言を以下の内容に置き換えます。

    ```java
    import com.google.firebase.iid.FirebaseInstanceIdService;

    public class ToDoInstanceIdService extends FirebaseInstanceIdService {

        @Override
        public void onTokenRefresh() {
            ToDoActivity.registerPush();
        }
    }
    ```

これで、アプリケーションがプッシュ通知をサポートするように更新されました。
