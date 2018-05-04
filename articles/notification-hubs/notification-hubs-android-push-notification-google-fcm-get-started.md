---
title: Azure Notification Hubs の使用 (Android アプリと Firebase Cloud Messaging) | Microsoft Docs
description: このチュートリアルでは、Azure Notification Hubs と Firebase Cloud Messaging を使用して Android デバイスにプッシュ通知を送信する方法について学習します。
services: notification-hubs
documentationcenter: android
keywords: プッシュ通知,プッシュ通知,android プッシュ通知,fcm,firebase cloud messaging
author: jwhitedev
manager: kpiteira
editor: ''
ms.assetid: 02298560-da61-4bbb-b07c-e79bd520e420
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: hero-article
ms.date: 12/22/2017
ms.author: jawh
ms.openlocfilehash: 6768b3e55af5e497c9424bc09130c9dd0948968b
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2018
---
# <a name="get-started-with-azure-notification-hubs-for-android-apps-and-firebase-cloud-messaging"></a>Azure Notification Hubs の使用 (Android アプリと Firebase Cloud Messaging)
[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>概要
> [!IMPORTANT]
> この記事では、Google Firebase Cloud Messaging (FCM) を使用したプッシュ通知について説明します。 まだ Google Cloud Messaging (GCM) を使用している場合は、 [Azure Notification Hubs と GCM を使用した Android へのプッシュ通知の送信](notification-hubs-android-push-notification-google-gcm-get-started.md)に関する記事を参照してください。
> 
> 

このチュートリアルでは、Azure Notification Hubs と Firebase Cloud Messaging を使用して Android アプリケーションにプッシュ通知を送信する方法について説明します。 このチュートリアルでは、Firebase Cloud Messaging (FCM) を使用してプッシュ通知を受信する空の Android アプリケーションを作成します。

[!INCLUDE [notification-hubs-hero-slug](../../includes/notification-hubs-hero-slug.md)]

このチュートリアルの完成したコードについては、GitHub の [こちら](https://github.com/Azure/azure-notificationhubs-samples/tree/master/Android/GetStartedFirebase)からダウンロードできます。

## <a name="prerequisites"></a>前提条件
> [!IMPORTANT]
> このチュートリアルを完了するには、アクティブな Azure アカウントが必要です。 アカウントがない場合は、無料試用アカウントを数分で作成することができます。 詳細については、 [Azure の無料試用版サイト](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-android-get-started)を参照してください。
> 
> 

* このチュートリアルでは、上記のアクティブな Azure アカウント以外に、最新版の [Android Studio](http://go.microsoft.com/fwlink/?LinkId=389797)が必要です。
* Android 2.3 以上 (Firebase Cloud Messaging 対応)。
* Firebase Cloud Messaging には Google Repository revision 27 以上が必要です。
* Google Play Services 9.0.2 以上 (Firebase Cloud Messaging 対応)。
* このチュートリアルを完了することは、Android アプリケーションの他のすべての Notification Hubs チュートリアルの前提条件です。

## <a name="create-a-new-android-studio-project"></a>新しい Android Studio プロジェクトを作成する
1. Android Studio で新しい Android Studio プロジェクトを開始する
   
    ![Android Studio - new project](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-android-studio-new-project.png)
2. **[Phone and Tablet (電話とタブレット)]** フォーム ファクターを選択し、サポートする **[Minimum SDK (最低限の SDK)]** を選択します。 その後、 **[次へ]** をクリックします。
   
    ![Android Studio - project creation workflow](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-android-studio-choose-form-factor.png)
3. メイン アクティビティに **[Empty Activity (空のアクティビティ)]** を選択し、**[Next (次へ)]** をクリックして、**[Finish (完了)]** をクリックします。

## <a name="create-a-project-that-supports-firebase-cloud-messaging"></a>Firebase Cloud Messaging をサポートするプロジェクトを作成する
[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="configure-a-new-notification-hub"></a>新しい Notification Hub を構成する
[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

&emsp;&emsp;6. **[Notification Services]** で **[Google (GCM)]** を選択します。 先ほど [Firebase Console](https://firebase.google.com/console/) からコピーした FCM サーバー キーを入力し、**[保存]** をクリックします。

&emsp;&emsp;![Azure Notification Hubs - Google (GCM)](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-gcm-api.png)

これで、通知ハブが Firebase Cloud Messaging と連動するように構成されました。接続文字列を使用して、プッシュ通知の受信と送信の両方にアプリを登録できます。

## <a id="connecting-app"></a>通知ハブにアプリを接続する
### <a name="add-google-play-services-to-the-project"></a>プロジェクトへの Google Play Services の追加
[!INCLUDE [Add Play Services](../../includes/notification-hubs-android-studio-add-google-play-services.md)]

### <a name="adding-azure-notification-hubs-libraries"></a>Azure Notification Hubs ライブラリを追加する
1. **[app (アプリ)]** の `Build.Gradle` ファイルの **dependencies** セクションに次の行を追加します。
   
    ```java
        compile 'com.microsoft.azure:notification-hubs-android-sdk:0.4@aar'
        compile 'com.microsoft.azure:azure-notifications-handler:1.0.1@aar'
    ```

2. **[dependencies]** セクションの後に次のリポジトリを追加します。
   
    ```java
        repositories {
            maven {
                url "http://dl.bintray.com/microsoftazuremobile/SDK"
            }
        }
    ```

### <a name="updating-the-androidmanifestxml"></a>AndroidManifest.xml ファイルを更新する
1. FCM をサポートするには、[Google の FirebaseInstanceId API](https://firebase.google.com/docs/reference/android/com/google/firebase/iid/FirebaseInstanceId) を利用し、[登録トークンの取得](https://firebase.google.com/docs/cloud-messaging/android/client#sample-register)に使用されるインスタンス ID リスナー サービスをコードに実装する必要があります。 このチュートリアルでは、クラスの名前は `MyInstanceIDService` です。 
   
    次のサービス定義を AndroidManifest.xml ファイルの `<application>` タグ内に追加します。 
   
    ```xml
        <service android:name=".MyInstanceIDService">
            <intent-filter>
                <action android:name="com.google.firebase.INSTANCE_ID_EVENT"/>
            </intent-filter>
        </service>
    ```

2. FirebaseInstanceId API から FCM 登録トークンを受け取ったら、それを使用して [Azure 通知ハブに登録](notification-hubs-push-notification-registration-management.md)します。 この登録はバックグラウンドで `RegistrationIntentService` という名前の `IntentService` を利用してサポートします。 このサービスは FCM 登録トークンの更新も行います。
   
    次のサービス定義を AndroidManifest.xml ファイルの `<application>` タグ内に追加します。 
   
    ```xml
        <service
            android:name=".RegistrationIntentService"
            android:exported="false">
        </service>
    ```

3. 通知を受信するレシーバーも定義する必要があります。 次のレシーバー定義を AndroidManifest.xml ファイルの `<application>` タグ内に追加します。 `<your package>` プレースホルダーを、`AndroidManifest.xml` ファイルの先頭にある実際のパッケージ名に置き換えます。

    ```xml
        <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
            android:permission="com.google.android.c2dm.permission.SEND">
            <intent-filter>
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="<your package name>" />
            </intent-filter>
        </receiver>
    ```

4. 次の必要な FCM 関連のアクセス許可を `</application>` タグの下に追加します。 `<your package>` は、`AndroidManifest.xml` ファイルの先頭にあるパッケージ名に必ず置き換えてください。
   
    これらのアクセス許可の詳細については、「[Setup a GCM Client app for Android (Android 用 GCM クライアント アプリを設定する)](https://developers.google.com/cloud-messaging/android/client#manifest)」と「[Migrate a GCM Client App for Android to Firebase Cloud Messaging (Android 用 GCM クライアント アプリを Firebase Cloud Messaging に移行する)](https://developers.google.com/cloud-messaging/android/android-migrate-fcm#remove_the_permissions_required_by_gcm)」を参照してください。
   
    ```xml
        <uses-permission android:name="android.permission.INTERNET"/>
        <uses-permission android:name="android.permission.GET_ACCOUNTS"/>
        <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
    ```

### <a name="adding-code"></a>コードを追加する
1. [Project (プロジェクト)] ビューで、**[app]** > **[src]** > **[main]** > **[java]** の順に展開します。 **[java]** のパッケージ フォルダーを右クリックして、**[New (新規)]**、**[Java Class (Java クラス)]** の順にクリックします。 `NotificationSettings`という名前の新しいクラスを追加します。 
   
    ![Android Studio - new Java class](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hub-android-new-class.png)
   
    以下の `NotificationSettings` クラスのコード内にある次の 3 つのプレースホルダーを忘れずに更新してください。
   
   * **SenderId**: [Firebase Console](https://firebase.google.com/console/) でプロジェクト設定の **[Cloud Messaging]** タブから先ほど取得した送信者 ID。
   * **HubListenConnectionString**: ハブの **DefaultListenAccessSignature** 接続文字列。 接続文字列をコピーするには、[Azure Portal] で、ハブにある **[アクセス ポリシー]** をクリックします。
   * **HubName**: [Azure Portal] のハブ ブレードに表示される通知ハブの名前を使用します。
     
     `NotificationSettings` のコードは次のとおりです。
     
    ```java
       public class NotificationSettings {
     
           public static String SenderId = "<Your project number>";
           public static String HubName = "<Your HubName>";
           public static String HubListenConnectionString = "<Enter your DefaultListenSharedAccessSignature connection string>";
       }
    ```

2. 上記の手順で `MyInstanceIDService`という名前の別の新しいクラスを追加します。 これはインスタンス ID リスナー サービスの実装になります。
   
    このクラスのコードで、`IntentService` が呼び出され、バックグラウンドで [FCM トークンが更新](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens)されます。
   
    ```java
        import android.content.Intent;
        import android.util.Log;
        import com.google.firebase.iid.FirebaseInstanceIdService;

        public class MyInstanceIDService extends FirebaseInstanceIdService {

            private static final String TAG = "MyInstanceIDService";

            @Override
            public void onTokenRefresh() {

                Log.d(TAG, "Refreshing GCM Registration Token");

                Intent intent = new Intent(this, RegistrationIntentService.class);
                startService(intent);
            }
        };
    ```

1. `RegistrationIntentService`という名前の別の新しいクラスをプロジェクトに追加します。 これが [FCM トークンの更新](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens)と[通知ハブへの登録](notification-hubs-push-notification-registration-management.md)を処理する `IntentService` の実装になります。
   
    次のコードをこのクラスに使用します。
   
    ```java
        import android.app.IntentService;
        import android.content.Intent;
        import android.content.SharedPreferences;
        import android.preference.PreferenceManager;
        import android.util.Log;        
        import com.google.firebase.iid.FirebaseInstanceId;
        import com.microsoft.windowsazure.messaging.NotificationHub;
   
        public class RegistrationIntentService extends IntentService {
   
            private static final String TAG = "RegIntentService";
   
            private NotificationHub hub;
   
            public RegistrationIntentService() {
                super(TAG);
            }
   
            @Override
            protected void onHandleIntent(Intent intent) {
   
                SharedPreferences sharedPreferences = PreferenceManager.getDefaultSharedPreferences(this);
                String resultString = null;
                String regID = null;
                String storedToken = null;
   
                try {
                    String FCM_token = FirebaseInstanceId.getInstance().getToken();
                    Log.d(TAG, "FCM Registration Token: " + FCM_token);
   
                    // Storing the registration ID that indicates whether the generated token has been
                    // sent to your server. If it is not stored, send the token to your server,
                    // otherwise your server should have already received the token.
                    if (((regID=sharedPreferences.getString("registrationID", null)) == null)){
   
                        NotificationHub hub = new NotificationHub(NotificationSettings.HubName,
                                NotificationSettings.HubListenConnectionString, this);
                        Log.d(TAG, "Attempting a new registration with NH using FCM token : " + FCM_token);
                        regID = hub.register(FCM_token).getRegistrationId();
   
                        // If you want to use tags...
                        // Refer to : https://azure.microsoft.com/documentation/articles/notification-hubs-routing-tag-expressions/
                        // regID = hub.register(token, "tag1,tag2").getRegistrationId();
   
                        resultString = "New NH Registration Successfully - RegId : " + regID;
                        Log.d(TAG, resultString);
   
                        sharedPreferences.edit().putString("registrationID", regID ).apply();
                        sharedPreferences.edit().putString("FCMtoken", FCM_token ).apply();
                    }
   
                    // Check if the token may have been compromised and needs refreshing.
                    else if ((storedToken=sharedPreferences.getString("FCMtoken", "")) != FCM_token) {
   
                        NotificationHub hub = new NotificationHub(NotificationSettings.HubName,
                                NotificationSettings.HubListenConnectionString, this);
                        Log.d(TAG, "NH Registration refreshing with token : " + FCM_token);
                        regID = hub.register(FCM_token).getRegistrationId();
   
                        // If you want to use tags...
                        // Refer to : https://azure.microsoft.com/documentation/articles/notification-hubs-routing-tag-expressions/
                        // regID = hub.register(token, "tag1,tag2").getRegistrationId();
   
                        resultString = "New NH Registration Successfully - RegId : " + regID;
                        Log.d(TAG, resultString);
   
                        sharedPreferences.edit().putString("registrationID", regID ).apply();
                        sharedPreferences.edit().putString("FCMtoken", FCM_token ).apply();
                    }
   
                    else {
                        resultString = "Previously Registered Successfully - RegId : " + regID;
                    }
                } catch (Exception e) {
                    Log.e(TAG, resultString="Failed to complete registration", e);
                    // If an exception happens while fetching the new token or updating our registration data
                    // on a third-party server, this ensures that we'll attempt the update at a later time.
                }
   
                // Notify UI that registration has completed.
                if (MainActivity.isVisible) {
                    MainActivity.mainActivity.ToastNotify(resultString);
                }
            }
        }
    ```

2. `MainActivity` クラスで、クラス宣言の上に次の `import` ステートメントを追加します。
   
    ```java
        import com.google.android.gms.common.ConnectionResult;
        import com.google.android.gms.common.GoogleApiAvailability;
        import com.microsoft.windowsazure.notifications.NotificationsManager;
        import android.content.Intent;
        import android.util.Log;
        import android.widget.TextView;
        import android.widget.Toast;
    ```
3. クラスの最上位に、次のプライベート メンバーを追加します。 これらを利用し、[Google の推奨事項に従って Google Play 開発者サービスが利用可能かどうかを確認](https://developers.google.com/android/guides/setup#ensure_devices_have_the_google_play_services_apk)します。
   
    ```java
        public static MainActivity mainActivity;
        public static Boolean isVisible = false;    
        private static final String TAG = "MainActivity";
        private static final int PLAY_SERVICES_RESOLUTION_REQUEST = 9000;
    ```

4. `MainActivity` クラスに、Google Play Services が利用可能かどうかを確認する次のメソッドを追加します。 
   
    ```java
        /**
        * Check the device to make sure it has the Google Play Services APK. If
        * it doesn't, display a dialog that allows users to download the APK from
        * the Google Play Store or enable it in the device's system settings.
        */
    
        private boolean checkPlayServices() {
            GoogleApiAvailability apiAvailability = GoogleApiAvailability.getInstance();
            int resultCode = apiAvailability.isGooglePlayServicesAvailable(this);
            if (resultCode != ConnectionResult.SUCCESS) {
                if (apiAvailability.isUserResolvableError(resultCode)) {
                    apiAvailability.getErrorDialog(this, resultCode, PLAY_SERVICES_RESOLUTION_REQUEST)
                            .show();
                } else {
                    Log.i(TAG, "This device is not supported by Google Play Services.");
                    ToastNotify("This device is not supported by Google Play Services.");
                    finish();
                }
                return false;
            }
            return true;
        }
    ```

5. `MainActivity` クラスに次のコードを追加します。このコードは、Google Play 開発者サービスを確認してから、`IntentService` を呼び出して FCM 登録トークンを取得し、通知ハブに登録します。
   
    ```java
        public void registerWithNotificationHubs()
        {
            if (checkPlayServices()) {
                // Start IntentService to register this application with FCM.
                Intent intent = new Intent(this, RegistrationIntentService.class);
                startService(intent);
            }
        }
    ```

6. `MainActivity` クラスの `OnCreate` メソッドで、アクティビティの作成時に登録プロセスを開始するために次のコードを追加します。
   
    ```java
        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
   
            mainActivity = this;
            NotificationsManager.handleNotifications(this, NotificationSettings.SenderId, MyHandler.class);
            registerWithNotificationHubs();
        }
    ```

7. アプリの状態を確認してアプリ内で報告するには、次の追加メソッドを `MainActivity` に追加します。
   
    ```java
        @Override
        protected void onStart() {
            super.onStart();
            isVisible = true;
        }
   
        @Override
        protected void onPause() {
            super.onPause();
            isVisible = false;
        }
   
        @Override
        protected void onResume() {
            super.onResume();
            isVisible = true;
        }
   
        @Override
        protected void onStop() {
            super.onStop();
            isVisible = false;
        }
   
        public void ToastNotify(final String notificationMessage) {
            runOnUiThread(new Runnable() {
                @Override
                public void run() {
                    Toast.makeText(MainActivity.this, notificationMessage, Toast.LENGTH_LONG).show();
                    TextView helloText = (TextView) findViewById(R.id.text_hello);
                    helloText.setText(notificationMessage);
                }
            });
        }
    ```

8. `ToastNotify` メソッドでは "*Hello World*" `TextView` コントロールを使用し、アプリで状態と通知を継続的に報告します。 activity_main.xml レイアウトでは、そのコントロールに次の ID を追加します。
   
    ```java
       android:id="@+id/text_hello"
    ```

9. 次に、AndroidManifest.xml で定義したレシーバーのサブクラスを追加します。 `MyHandler`という名前の別の新しいクラスをプロジェクトに追加します。
10. 次の import ステートメントを `MyHandler.java` の先頭に追加します。
    
    ```java
        import android.app.NotificationManager;
        import android.app.PendingIntent;
        import android.content.Context;
        import android.content.Intent;
        import android.media.RingtoneManager;
        import android.net.Uri;
        import android.os.Bundle;
        import android.support.v4.app.NotificationCompat;
        import com.microsoft.windowsazure.notifications.NotificationsHandler;
    ```

11. `MyHandler` クラス用に次のコードを追加して、`com.microsoft.windowsazure.notifications.NotificationsHandler` のサブクラスにします。
    
    このコードによって `OnReceive` メソッドがオーバーライドされるため、ハンドラーは受信した通知を報告します。 ハンドラーは `sendNotification()` メソッドを使用して Android の通知マネージャーにもプッシュ通知を送信します。 `sendNotification()` メソッドは、アプリが動作していないときに通知を受信した場合に実行される必要があります。
    
    ```java
        public class MyHandler extends NotificationsHandler {
            public static final int NOTIFICATION_ID = 1;
            private NotificationManager mNotificationManager;
            NotificationCompat.Builder builder;
            Context ctx;
    
            @Override
            public void onReceive(Context context, Bundle bundle) {
                ctx = context;
                String nhMessage = bundle.getString("message");
                sendNotification(nhMessage);
                if (MainActivity.isVisible) {
                    MainActivity.mainActivity.ToastNotify(nhMessage);
                }
            }
    
            private void sendNotification(String msg) {
    
                Intent intent = new Intent(ctx, MainActivity.class);
                intent.addFlags(Intent.FLAG_ACTIVITY_CLEAR_TOP);
    
                mNotificationManager = (NotificationManager)
                        ctx.getSystemService(Context.NOTIFICATION_SERVICE);
    
                PendingIntent contentIntent = PendingIntent.getActivity(ctx, 0,
                        intent, PendingIntent.FLAG_ONE_SHOT);
    
                Uri defaultSoundUri = RingtoneManager.getDefaultUri(RingtoneManager.TYPE_NOTIFICATION);
                NotificationCompat.Builder mBuilder =
                        new NotificationCompat.Builder(ctx)
                                .setSmallIcon(R.mipmap.ic_launcher)
                                .setContentTitle("Notification Hub Demo")
                                .setStyle(new NotificationCompat.BigTextStyle()
                                        .bigText(msg))
                                .setSound(defaultSoundUri)
                                .setContentText(msg);
    
                mBuilder.setContentIntent(contentIntent);
                mNotificationManager.notify(NOTIFICATION_ID, mBuilder.build());
            }
        }
    ```

12. Android Studio のメニュー バーで、**[Build (ビルド)]** > **[Rebuild Project (プロジェクトのリビルド)]** の順にクリックし、コードにエラーがないことを確認します。
13. お使いのデバイスでアプリを実行し、そのアプリが通知ハブに正常に登録されることを確認します。 
    
    > [!NOTE]
    > インスタンス ID サービスの `onTokenRefresh()` メソッドが呼び出されるまでは、初期起動時に登録が失敗する場合があります。 更新すると、通知ハブに正常に登録されるようになります。
    > 
    > 

## <a name="sending-push-notifications"></a>プッシュ通知を送信する
[Azure Portal] から通知を送信して、アプリでプッシュ通知の受信テストを行うことができます。以下に示すハブの **[トラブルシューティング]** セクションを参照してください。

![Azure Notification Hubs - Test Send](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-test-send.png)

[!INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]


## <a name="testing-your-app"></a>アプリケーションのテスト
#### <a name="push-notifications-in-the-emulator"></a>エミュレーターでのプッシュ通知
エミュレーターの内部でプッシュ通知をテストする場合は、エミュレーター イメージがアプリケーション用に選択した Google API レベルをサポートしていることを確認してください。 イメージがネイティブの Google API をサポートしていない場合は、最終的に **SERVICE\_NOT\_AVAILABLE** 例外を受け取ることになります。

上記に加えて、 **Azure ポータル** > **[アカウント]** に関する記事を参照してください。 アカウントを追加していないと、GCM での登録の試行が **AUTHENTICATION\_FAILED** 例外につながる可能性があります。

#### <a name="running-the-application"></a>アプリケーションの実行
1. アプリケーションを実行し、登録 ID の登録完了が報告されていることを確認します。
   
    ![Testing on Android - Channel registration](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-android-studio-registered.png)
2. ハブに登録されているすべての Android デバイスに送信される通知メッセージを入力します。
   
    ![Testing on Android - sending a message](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-android-studio-set-message.png)
3. **[Send Notification (通知の送信)]** を押します。 アプリケーションが実行されているすべてのデバイスで、プッシュ通知メッセージを示す `AlertDialog` インスタンスが表示されます。 実行中のアプリケーションがなくても事前にプッシュ通知に登録されているデバイスでは、Android 通知マネージャーで通知が受信されます。 左上隅から下へスワイプすると、通知を表示できます。
   
    ![Testing on Android - notifications](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-android-studio-received-message.png)

## <a name="next-steps"></a>次の手順
次のステップとして「 [Notification Hubs を使用したユーザーへのプッシュ通知] 」チュートリアルをお勧めします。 このチュートリアルでは、特定のユーザーに対してタグを使用して ASP.NET バックエンドから通知を送信する方法について説明しています。


対象グループごとにユーザーを区分する場合は、「 [Notification Hubs を使用したニュース速報の送信] 」のチュートリアルを参照してください。

Notification Hubs の全般的な情報については、「 [Notification Hubs の概要]」を参照してください。

<!-- Images. -->



<!-- URLs. -->
[Get started with push notifications in Mobile Services]: ../mobile-services-javascript-backend-android-get-started-push.md  
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Referencing a library project]: http://go.microsoft.com/fwlink/?LinkId=389800
[Notification Hubs の概要]: notification-hubs-push-notification-overview.md
[Notification Hubs を使用したユーザーへのプッシュ通知]: notification-hubs-aspnet-backend-gcm-android-push-to-user-google-notification.md
[Notification Hubs を使用したニュース速報の送信]: notification-hubs-aspnet-backend-android-xplat-segmented-gcm-push-notification.md
[Azure Portal]: https://portal.azure.com
