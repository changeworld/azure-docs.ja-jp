---
title: "Azure Notification Hubs と Firebase Cloud Messaging で Android にプッシュ通知を送信する |Microsoft ドキュメント"
description: "このチュートリアルでは、Azure Notification Hubs と Firebase クラウド メッセージングを使用して、Android デバイスにプッシュ通知をする方法を学習します。"
services: notification-hubs
documentationcenter: android
keywords: "プッシュ通知、プッシュ通知、android のプッシュ通知、fcm、firebase クラウドのメッセージング"
author: ysxu
manager: erikre
editor: 
ms.assetid: 02298560-da61-4bbb-b07c-e79bd520e420
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: hero-article
ms.date: 07/14/2016
ms.author: yuaxu
ms.openlocfilehash: 45a3fa5c7190e039fd637c78a41eeb3f6ede9bc7
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2017
---
# <a name="sending-push-notifications-to-android-with-azure-notification-hubs"></a>Azure Notification Hubs とアプリにプッシュ通知を送信します。
[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>概要
> [!IMPORTANT]
> このトピックでは、プッシュ通知に Google Firebase クラウド メッセージング (FCM) を示します。 Google Cloud Messaging (GCM) を引き続き使用している場合は、次を参照してください。 [Azure Notification Hubs と GCM Android にプッシュ通知を送信する](notification-hubs-android-push-notification-google-gcm-get-started.md)です。
> 
> 

このチュートリアルでは、Azure Notification Hubs と Firebase Cloud Messaging を使用して Android のアプリケーションにプッシュ通知を送信する方法を示します。
Firebase クラウド メッセージング (FCM) を使用して、プッシュ通知を受信する空白の Android アプリを作成します。

[!INCLUDE [notification-hubs-hero-slug](../../includes/notification-hubs-hero-slug.md)]

このチュートリアルの完成したコードは、GitHub からダウンロードできます。[ここ](https://github.com/Azure/azure-notificationhubs-samples/tree/master/Android/GetStartedFirebase)です。

## <a name="prerequisites"></a>前提条件
> [!IMPORTANT]
> このチュートリアルを完了するには、アクティブな Azure アカウントが必要です。 アカウントを持っていない場合は、ほんの数分で無料の試用アカウントを作成できます。 詳細については、「 [Azure 無料評価版](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-android-get-started)です。
> 
> 

* 上記で説明したアクティブな Azure アカウント、に加えては、このチュートリアルは、の最新バージョンを必要があります。 [Android Studio](http://go.microsoft.com/fwlink/?LinkId=389797)です。
* Android 2.3 以降 Firebase Cloud Messaging のです。
* Google リビジョン 27 またはそれ以降は、Firebase Cloud Messaging の必要があります。
* Google Play サービス 9.0.2 または Firebase Cloud Messaging のそれ以降。
* Android アプリの他のすべての Notification Hubs のチュートリアルの前提条件では、このチュートリアルを完了します。

## <a name="create-a-new-android-studio-project"></a>新しい Android Studio プロジェクトを作成します。
1. Android Studio で、新しい Android Studio プロジェクトを開始します。
   
       ![Android Studio - new project](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-android-studio-new-project.png)
2. 選択、**電話とタブレット**フォーム ファクター、および**最小 SDK**をサポートします。 [ **次へ**] をクリックします。
   
       ![Android Studio - project creation workflow](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-android-studio-choose-form-factor.png)
3. 選択**空のアクティビティ**メインのアクティビティをクリックして**[次へ]**、順にクリック**完了**です。

## <a name="create-a-project-that-supports-firebase-cloud-messaging"></a>Firebase Cloud Messaging をサポートするプロジェクトを作成します。
[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="configure-a-new-notification-hub"></a>新しい通知ハブを構成します。
[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

&emsp;&emsp;6. **設定**ブレードで、通知ハブは、select の**Notification Services**し**Google (GCM)**です。 コピーした FCM サーバー キーを入力して、 [Firebase コンソール](https://firebase.google.com/console/) をクリック**保存**です。

&emsp;&emsp;![Azure Notification Hubs の Google (GCM)](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-gcm-api.png)

通知ハブが Firebase クラウド Messagin を使用するようになりました設定されていて、両方に接続文字列を受信し、プッシュ通知を送信するアプリを登録します。

## <a id="connecting-app"></a>アプリの通知ハブを接続します。
### <a name="add-google-play-services-to-the-project"></a>Google Play サービスをプロジェクトに追加します。
[!INCLUDE [Add Play Services](../../includes/notification-hubs-android-studio-add-google-play-services.md)]

### <a name="adding-azure-notification-hubs-libraries"></a>Azure Notification Hubs のライブラリを追加します。
1. `Build.Gradle`ファイルで、**アプリ**の次の行を追加、**の依存関係**セクションです。
   
        compile 'com.microsoft.azure:notification-hubs-android-sdk:0.4@aar'
        compile 'com.microsoft.azure:azure-notifications-handler:1.0.1@aar'
2. 後に次のリポジトリを追加、**の依存関係**セクションです。
   
        repositories {
            maven {
                url "http://dl.bintray.com/microsoftazuremobile/SDK"
            }
        }

### <a name="updating-the-androidmanifestxml"></a>AndroidManifest.xml を更新しています。
1. FCM をサポートするためにするために使用されるコードで、インスタンス ID リスナー サービスを実装する必要がありますお[登録トークンを取得する](https://firebase.google.com/docs/cloud-messaging/android/client#sample-register)を使用して[Google の FirebaseInstanceId API](https://firebase.google.com/docs/reference/android/com/google/firebase/iid/FirebaseInstanceId)です。 このチュートリアルでは、クラスの名前を付けますおは`MyInstanceIDService`します。 
   
    次のサービス定義ファイルを追加、AndroidManifest.xml、内部、`<application>`タグ。 
   
        <service android:name=".MyInstanceIDService">
            <intent-filter>
                <action android:name="com.google.firebase.INSTANCE_ID_EVENT"/>
            </intent-filter>
        </service>
2. FirebaseInstanceId API から、FCM 登録トークンを受け取って、一度を使用してに[Azure 通知ハブに登録](notification-hubs-push-notification-registration-management.md)です。 使用して、バック グラウンドでこの登録がサポートされます、`IntentService`という`RegistrationIntentService`です。 このサービスは、FCM 登録トークンを更新する場合もあります。
   
    次のサービス定義ファイルを追加、AndroidManifest.xml、内部、`<application>`タグ。 
   
        <service
            android:name=".RegistrationIntentService"
            android:exported="false">
        </service>
3. 通知を受信する受信者も定義します。 内部 AndroidManifest.xml ファイルに次の受信者定義を追加、`<application>`タグ。 置換、`<your package>`プレース ホルダーの上部に表示される、実際のパッケージ名、`AndroidManifest.xml`ファイル。
   
        <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
            android:permission="com.google.android.c2dm.permission.SEND">
            <intent-filter>
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="<your package name>" />
            </intent-filter>
        </receiver>
4. 次の追加に必要な FCM 関連する以下のアクセス許可、`</application>`タグ。 必ず置き換えて`<your package>`の上部に表示されるパッケージ名を持つ、`AndroidManifest.xml`ファイル。
   
    これらのアクセス許可の詳細については、次を参照してください。 [Android の GCM クライアント アプリをセットアップ](https://developers.google.com/cloud-messaging/android/client#manifest)と[Firebase Cloud Messaging する Android の GCM クライアント アプリの移行](https://developers.google.com/cloud-messaging/android/android-migrate-fcm#remove_the_permissions_required_by_gcm)です。
   
        <uses-permission android:name="android.permission.INTERNET"/>
        <uses-permission android:name="android.permission.GET_ACCOUNTS"/>
        <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />

### <a name="adding-code"></a>コードを追加します。
1. In the Project View, expand **app** > **src** > **main** > **java**. 下のパッケージ フォルダーを右クリックして**java**、 をクリックして**新規**、クリックして**Java クラス**です。 という名前の新しいクラスを追加`NotificationSettings`です。 
   
    ![Android Studio - 新しい Java クラス](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hub-android-new-class.png)
   
    これら 3 つ更新することを確認の次のコード内のプレース ホルダー、`NotificationSettings`クラス。
   
   * **SenderId**: 以前に取得した送信者 Id、 **Cloud Messaging**  タブで、プロジェクト設定の[Firebase コンソール](https://firebase.google.com/console/)です。
   * **HubListenConnectionString**: **DefaultListenAccessSignature**ハブの接続文字列。 クリックしてその接続文字列をコピーすることができます**アクセス ポリシー**上、**設定**ブレード、ハブでの[Azure Portal]です。
   * **HubName**: でハブ ブレードに表示される通知ハブの名前を使用して、 [Azure Portal]です。
     
     `NotificationSettings`コードに示します。
     
       パブリック クラス NotificationSettings {
     
           public static String SenderId = "<Your project number>";
           public static String HubName = "<Your HubName>";
           public static String HubListenConnectionString = "<Enter your DefaultListenSharedAccessSignature connection string>";
       }
2. 上記の手順を使用して、という別の新しいクラスを追加`MyInstanceIDService`です。 インスタンス ID リスナー サービス実装になります。
   
    このクラスのコードを呼び出す、`IntentService`に[FCM のトークンの更新](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens)バック グラウンドでします。
   
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


1. 別の新しいクラスをという名前のプロジェクトに追加`RegistrationIntentService`です。 実装になります、`IntentService`を処理する[FCM トークンを更新する](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens)と[通知ハブに登録する](notification-hubs-push-notification-registration-management.md)です。
   
    このクラスには、次のコードを使用します。
   
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
   
                    // Storing the registration id that indicates whether the generated token has been
                    // sent to your server. If it is not stored, send the token to your server,
                    // otherwise your server should have already received the token.
                    if (((regID=sharedPreferences.getString("registrationID", null)) == null)){
   
                        NotificationHub hub = new NotificationHub(NotificationSettings.HubName,
                                NotificationSettings.HubListenConnectionString, this);
                        Log.d(TAG, "Attempting a new registration with NH using FCM token : " + FCM_token);
                        regID = hub.register(FCM_token).getRegistrationId();
   
                        // If you want to use tags...
                        // Refer to : https://azure.microsoft.com/en-us/documentation/articles/notification-hubs-routing-tag-expressions/
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
                        // Refer to : https://azure.microsoft.com/en-us/documentation/articles/notification-hubs-routing-tag-expressions/
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
2. `MainActivity`クラス、次の追加`import`クラス宣言の前のステートメント。
   
        import com.google.android.gms.common.ConnectionResult;
        import com.google.android.gms.common.GoogleApiAvailability;
        import com.microsoft.windowsazure.notifications.NotificationsManager;
        import android.content.Intent;
        import android.util.Log;
        import android.widget.TextView;
        import android.widget.Toast;
3. クラスの上部にある次のプライベート メンバーを追加します。 これらを使用して[Google Play サービスの可用性を確認して、Google の推奨に従って](https://developers.google.com/android/guides/setup#ensure_devices_have_the_google_play_services_apk)です。
   
        public static MainActivity mainActivity;
        public static Boolean isVisible = false;    
        private static final String TAG = "MainActivity";
        private static final int PLAY_SERVICES_RESOLUTION_REQUEST = 9000;
4. `MainActivity`クラスは、Google Play サービスの可用性に次のメソッドを追加します。 
   
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
5. `MainActivity`クラスを呼び出す前に Google Play サービスのチェックは、次のコードを追加、 `IntentService` FCM 登録トークンを取得し、通知ハブに登録します。
   
        public void registerWithNotificationHubs()
        {
            if (checkPlayServices()) {
                // Start IntentService to register this application with FCM.
                Intent intent = new Intent(this, RegistrationIntentService.class);
                startService(intent);
            }
        }
6. `OnCreate`のメソッド、`MainActivity`クラスで、アクティビティが作成されるときに、登録プロセスを開始するには、次のコードを追加します。
   
        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
   
            mainActivity = this;
            NotificationsManager.handleNotifications(this, NotificationSettings.SenderId, MyHandler.class);
            registerWithNotificationHubs();
        }
7. これらの追加メソッドを追加、`MainActivity`をアプリでアプリの状態とレポートの状態を確認します。
   
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
8. `ToastNotify`メソッドを使用、 *"Hello World"* `TextView`レポートの状態と永続的に、アプリで通知を制御します。 Activity_main.xml レイアウトでは、そのコントロールの次の id を追加します。
   
       android:id="@+id/text_hello"
9. 次に、AndroidManifest.xml で定義して、受信者のサブクラスを追加します。 同じ名前のプロジェクトに別の新しいクラスを追加`MyHandler`です。
10. 先頭に次の import ステートメントを追加`MyHandler.java`:
    
        import android.app.NotificationManager;
        import android.app.PendingIntent;
        import android.content.Context;
        import android.content.Intent;
        import android.media.RingtoneManager;
        import android.net.Uri;
        import android.os.Bundle;
        import android.support.v4.app.NotificationCompat;
        import com.microsoft.windowsazure.notifications.NotificationsHandler;
11. 次のコードを追加、`MyHandler`クラスのサブクラスを行う`com.microsoft.windowsazure.notifications.NotificationsHandler`です。
    
    このコードは、`OnReceive`メソッド、ハンドラーが受信した通知が報告されるようにします。 ハンドラーもプッシュ通知に送信 Android 通知マネージャーを使用して、`sendNotification()`メソッドです。 `sendNotification()`アプリが実行されていないと、通知を受信したときに、メソッドを実行する必要があります。
    
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
12. Android Studio でメニュー バーで、**ビルド** > **プロジェクトの再構築**にエラーが、コード内に存在しないことを確認してください。
13. デバイスでアプリを実行し、通知ハブに正常に登録することを確認します。 
    
    > [!NOTE]
    > 初期起動までで登録が失敗する、 `onTokenRefresh()` Id サービスのインスタンスのメソッドが呼び出されます。 更新には、開始が通知ハブの登録が成功する必要があります。
    > 
    > 

## <a name="sending-push-notifications"></a>プッシュ通知を送信します。
経由で送信して、アプリにプッシュ通知を受信をテストすることができます、 [Azure Portal]- 検索の**トラブルシューティング**ハブ ブレードで、セクションの次のようにします。

![Azure Notification Hubs のテスト送信](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-test-send.png)

[!INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]

## <a name="optional-send-push-notifications-directly-from-the-app"></a>(省略可能)アプリから直接にプッシュ通知を送信します。
> [!IMPORTANT]
> クライアント アプリケーションから通知を送信するには、この例は学習の目的でのみ提供されます。 これが必要になりますので、`DefaultFullSharedAccessSignature`クライアント アプリ上に存在するユーザーがアクセスするクライアントに不正な通知を送信するリスクを通知ハブを公開します。
> 
> 

通常、バックエンド サーバーを使用して通知を送信するとします。 場合によっては、クライアント アプリケーションから直接プッシュ通知を送信できるようにする場合があります。 このセクションを使用してクライアントからの通知を送信する方法について説明、 [Azure 通知ハブの REST API](https://msdn.microsoft.com/library/azure/dn223264.aspx)です。

1. In Android Studio Project View, expand **App** > **src** > **main** > **res** > **layout**. 開く、`activity_main.xml`レイアウト ファイルをクリック、**テキスト**タブは、ファイルのテキストの内容を更新します。 更新、次のコードでこれが新規追加`Button`と`EditText`通知ハブにプッシュ通知メッセージを送信するためのコントロールです。 直前の下部で、次のコードを追加`</RelativeLayout>`です。
   
        <Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/send_button"
        android:id="@+id/sendbutton"
        android:layout_centerVertical="true"
        android:layout_centerHorizontal="true"
        android:onClick="sendNotificationButtonOnClick" />
   
        <EditText
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:id="@+id/editTextNotificationMessage"
        android:layout_above="@+id/sendbutton"
        android:layout_centerHorizontal="true"
        android:layout_marginBottom="42dp"
        android:hint="@string/notification_message_hint" />
2. In Android Studio Project View, expand **App** > **src** > **main** > **res** > **values**. 開く、`strings.xml`ファイルし、新しいによって参照されている文字列値を追加する`Button`と`EditText`コントロール。 これらのファイルの下部にある直前に追加`</resources>`です。
   
        <string name="send_button">Send Notification</string>
        <string name="notification_message_hint">Enter notification message text</string>
3. `NotificationSetting.java`ファイルに追加し、次の設定を`NotificationSettings`クラスです。
   
    更新`HubFullAccess`で、 **DefaultFullSharedAccessSignature**ハブの接続文字列。 この接続文字列をコピーすることができます、 [Azure Portal] をクリックして**アクセス ポリシー**上、**設定**通知ハブのブレードです。
   
        public static String HubFullAccess = "<Enter Your DefaultFullSharedAccessSignature Connection string>";
4. `MainActivity.java`ファイルに追加し、次`import`上記のステートメント、`MainActivity`クラスです。
   
        import java.io.BufferedOutputStream;
        import java.io.BufferedReader;
        import java.io.InputStreamReader;
        import java.io.OutputStream;
        import java.net.HttpURLConnection;
        import java.net.URL;
        import java.net.URLEncoder;
        import javax.crypto.Mac;
        import javax.crypto.spec.SecretKeySpec;
        import android.util.Base64;
        import android.view.View;
        import android.widget.EditText;
5. `MainActivity.java`ファイルの先頭に次のメンバーを追加の`MainActivity`クラスです。    
   
        private String HubEndpoint = null;
        private String HubSasKeyName = null;
        private String HubSasKeyValue = null;
6. メッセージを送信する通知ハブへの POST 要求を認証するソフトウェア Access Signature (SaS) トークンを作成する必要があります。 これは、接続文字列のキーのデータを解析して、SaS トークンを作成し、前述のように、[の一般的な概念](http://msdn.microsoft.com/library/azure/dn495627.aspx)REST API リファレンスです。 次のコードでは、実装例を示します。
   
    `MainActivity.java`には、次のメソッドを追加、`MainActivity`クラス、接続文字列を解析します。
   
        /**
         * Example code from http://msdn.microsoft.com/library/azure/dn495627.aspx
         * to parse the connection string so a SaS authentication token can be
         * constructed.
         *
         * @param connectionString This must be the DefaultFullSharedAccess connection
         *                         string for this example.
         */
        private void ParseConnectionString(String connectionString)
        {
            String[] parts = connectionString.split(";");
            if (parts.length != 3)
                throw new RuntimeException("Error parsing connection string: "
                        + connectionString);
   
            for (int i = 0; i < parts.length; i++) {
                if (parts[i].startsWith("Endpoint")) {
                    this.HubEndpoint = "https" + parts[i].substring(11);
                } else if (parts[i].startsWith("SharedAccessKeyName")) {
                    this.HubSasKeyName = parts[i].substring(20);
                } else if (parts[i].startsWith("SharedAccessKey")) {
                    this.HubSasKeyValue = parts[i].substring(16);
                }
            }
        }
7. `MainActivity.java`には、次のメソッドを追加、 `MainActivity` SaS 認証トークンを作成するクラス。
   
        /**
         * Example code from http://msdn.microsoft.com/library/azure/dn495627.aspx to
         * construct a SaS token from the access key to authenticate a request.
         *
         * @param uri The unencoded resource URI string for this operation. The resource
         *            URI is the full URI of the Service Bus resource to which access is
         *            claimed. For example,
         *            "http://<namespace>.servicebus.windows.net/<hubName>"
         */
        private String generateSasToken(String uri) {
   
            String targetUri;
            String token = null;
            try {
                targetUri = URLEncoder
                        .encode(uri.toString().toLowerCase(), "UTF-8")
                        .toLowerCase();
   
                long expiresOnDate = System.currentTimeMillis();
                int expiresInMins = 60; // 1 hour
                expiresOnDate += expiresInMins * 60 * 1000;
                long expires = expiresOnDate / 1000;
                String toSign = targetUri + "\n" + expires;
   
                // Get an hmac_sha1 key from the raw key bytes
                byte[] keyBytes = HubSasKeyValue.getBytes("UTF-8");
                SecretKeySpec signingKey = new SecretKeySpec(keyBytes, "HmacSHA256");
   
                // Get an hmac_sha1 Mac instance and initialize with the signing key
                Mac mac = Mac.getInstance("HmacSHA256");
                mac.init(signingKey);
   
                // Compute the hmac on input data bytes
                byte[] rawHmac = mac.doFinal(toSign.getBytes("UTF-8"));
   
                // Using android.util.Base64 for Android Studio instead of
                // Apache commons codec
                String signature = URLEncoder.encode(
                        Base64.encodeToString(rawHmac, Base64.NO_WRAP).toString(), "UTF-8");
   
                // Construct authorization string
                token = "SharedAccessSignature sr=" + targetUri + "&sig="
                        + signature + "&se=" + expires + "&skn=" + HubSasKeyName;
            } catch (Exception e) {
                if (isVisible) {
                    ToastNotify("Exception Generating SaS : " + e.getMessage().toString());
                }
            }
   
            return token;
        }
8. `MainActivity.java`には、次のメソッドを追加、`MainActivity`を処理するクラス、**通知の送信**クリックのボタンをクリックし、組み込みの REST API を使用してハブにプッシュ通知メッセージを送信します。
   
        /**
         * Send Notification button click handler. This method parses the
         * DefaultFullSharedAccess connection string and generates a SaS token. The
         * token is added to the Authorization header on the POST request to the
         * notification hub. The text in the editTextNotificationMessage control
         * is added as the JSON body for the request to add a GCM message to the hub.
         *
         * @param v
         */
        public void sendNotificationButtonOnClick(View v) {
            EditText notificationText = (EditText) findViewById(R.id.editTextNotificationMessage);
            final String json = "{\"data\":{\"message\":\"" + notificationText.getText().toString() + "\"}}";
   
            new Thread()
            {
                public void run()
                {
                    try
                    {
                        // Based on reference documentation...
                        // http://msdn.microsoft.com/library/azure/dn223273.aspx
                        ParseConnectionString(NotificationSettings.HubFullAccess);
                        URL url = new URL(HubEndpoint + NotificationSettings.HubName +
                                "/messages/?api-version=2015-01");
   
                        HttpURLConnection urlConnection = (HttpURLConnection)url.openConnection();
   
                        try {
                            // POST request
                            urlConnection.setDoOutput(true);
   
                            // Authenticate the POST request with the SaS token
                            urlConnection.setRequestProperty("Authorization", 
                                generateSasToken(url.toString()));
   
                            // Notification format should be GCM
                            urlConnection.setRequestProperty("ServiceBusNotification-Format", "gcm");
   
                            // Include any tags
                            // Example below targets 3 specific tags
                            // Refer to : https://azure.microsoft.com/en-us/documentation/articles/notification-hubs-routing-tag-expressions/
                            // urlConnection.setRequestProperty("ServiceBusNotification-Tags", 
                            //        "tag1 || tag2 || tag3");
   
                            // Send notification message
                            urlConnection.setFixedLengthStreamingMode(json.length());
                            OutputStream bodyStream = new BufferedOutputStream(urlConnection.getOutputStream());
                            bodyStream.write(json.getBytes());
                            bodyStream.close();
   
                            // Get reponse
                            urlConnection.connect();
                            int responseCode = urlConnection.getResponseCode();
                            if ((responseCode != 200) && (responseCode != 201)) {
                                BufferedReader br = new BufferedReader(new InputStreamReader((urlConnection.getErrorStream())));
                                String line;
                                StringBuilder builder = new StringBuilder("Send Notification returned " +
                                        responseCode + " : ")  ;
                                while ((line = br.readLine()) != null) {
                                    builder.append(line);
                                }
   
                                ToastNotify(builder.toString());
                            }
                        } finally {
                            urlConnection.disconnect();
                        }
                    }
                    catch(Exception e)
                    {
                        if (isVisible) {
                            ToastNotify("Exception Sending Notification : " + e.getMessage().toString());
                        }
                    }
                }
            }.start();
        }

## <a name="testing-your-app"></a>アプリのテスト
#### <a name="push-notifications-in-the-emulator"></a>エミュレーターでのプッシュ通知
エミュレーターの内部のプッシュ通知をテストする場合は、エミュレーターの画像が、アプリ用に選択した Google API レベルをサポートしていることを確認します。 /Compile には、イメージがネイティブの Google Api をサポートしていない場合、**サービス\_いない\_利用可能**例外。

上記に加え、実行中のエミュレーターの下に Google アカウントを追加したことを確認してください**設定** > **アカウント**です。 それ以外の場合、GCM 登録しようとするがあります、**認証\_失敗**例外。

#### <a name="running-the-application"></a>アプリケーションの実行
1. アプリを実行して、登録 ID が成功した登録の報告されることに注意してください。
   
       ![Testing on Android - Channel registration](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-android-studio-registered.png)
2. ハブに登録されているすべての Android デバイスに送信される通知メッセージを入力します。
   
       ![Testing on Android - sending a message](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-android-studio-set-message.png)
3. キーを押して**通知を送信**です。 実行しているアプリを持つ任意のデバイスが表示されます、`AlertDialog`プッシュ通知メッセージを持つインスタンス。 プッシュ通知用にアプリが実行されている必要はありませんが、以前に登録されているデバイスは Android 通知マネージャー内に、通知に受け取ります。 これらは、左上から下へスワイプして表示できます。
   
       ![Testing on Android - notifications](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-android-studio-received-message.png)

## <a name="next-steps"></a>次のステップ
お勧め、[ユーザーへのプッシュ通知を使用して Notification Hubs] 」チュートリアル、次の手順で作成します。 特定のユーザーを対象とするタグを使用して ASP.NET バックエンドから通知を送信する方法が表示されます。

対象グループと、ユーザーをセグメント化する場合は、チェック アウト、[最新ニュースを送信する通知ハブを使用して]チュートリアルです。

Notification Hubs に関するより一般的な情報については、次を参照してください。 この[Notification Hubs ガイダンス]です。

<!-- Images. -->



<!-- URLs. -->
[Get started with push notifications in Mobile Services]: ../mobile-services-javascript-backend-android-get-started-push.md  
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Referencing a library project]: http://go.microsoft.com/fwlink/?LinkId=389800
[Azure Classic Portal]: https://manage.windowsazure.com/
[Notification Hubs ガイダンス]: notification-hubs-push-notification-overview.md
[ユーザーへのプッシュ通知を使用して Notification Hubs]: notification-hubs-aspnet-backend-gcm-android-push-to-user-google-notification.md
[最新ニュースを送信する通知ハブを使用して]: notification-hubs-aspnet-backend-android-xplat-segmented-gcm-push-notification.md
[Azure Portal]: https://portal.azure.com
