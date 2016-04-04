<properties
	pageTitle="Azure Notification Hubs から Android へのプッシュ通知の送信 | Microsoft Azure"
	description="このチュートリアルでは、Azure Notification Hubs を使用して Android デバイスにプッシュ通知を送信する方法について学習します。"
	services="notification-hubs"
	documentationCenter="android"
	keywords="プッシュ通知,プッシュ通知,Android プッシュ通知"
	authors="wesmc7777"
	manager="dwrede"
	editor=""/>
<tags
	ms.service="notification-hubs"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="hero-article"
	ms.date="03/15/2016"
	ms.author="wesmc"/>

# Azure Notification Hubs から Android へのプッシュ通知の送信

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

##概要

> [AZURE.IMPORTANT] このチュートリアルを完了するには、アクティブな Azure アカウントが必要です。アカウントがない場合は、無料試用版のアカウントを数分で作成することができます。詳細については、[Azure の無料試用版サイト](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fja-JP%2Fdocumentation%2Farticles%2Fnotification-hubs-android-get-started)を参照してください。

このチュートリアルでは、Azure Notification Hubs を使用して Android アプリケーションにプッシュ通知を送信する方法について説明します。Google Cloud Messaging (GCM) を使用してプッシュ通知を受信する空の Android アプリケーションを作成します。

Notification Hubs を使用して対象設定済みのプッシュ通知を送信する方法を理解するために、[タグ付けに関するチュートリアル](./notification-hubs-routing-tag-expressions.md)も併せて参照してください。


## 開始する前に

[AZURE.INCLUDE [notification-hubs-hero-slug](../../includes/notification-hubs-hero-slug.md)]

このチュートリアルの完成したコードについては、GitHub の[こちら](https://github.com/Azure/azure-notificationhubs-samples/tree/master/Android/GetStarted)を参照してください。


##前提条件

上記のアクティブな Azure アカウント以外に、このチュートリアルで必要となるのは、最新版の [Android Studio](http://go.microsoft.com/fwlink/?LinkId=389797) のみです。

このチュートリアルを完了することは、Android アプリケーションの他のすべての Notification Hubs チュートリアルの前提条件です。

##Google Cloud Messaging をサポートするプロジェクトを作成する

[AZURE.INCLUDE [mobile-services-enable-Google-cloud-messaging](../../includes/mobile-services-enable-google-cloud-messaging.md)]


##新しい Notification Hub を構成する


[AZURE.INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]


&emsp;&emsp;7.**[設定]** ブレードで、**[Notification Services]**、[**Google (GCM)]** の順に選択します。API キーを入力して保存します。

&emsp;&emsp;![Azure Notification Hubs - Google (GCM)](./media/notification-hubs-android-get-started/notification-hubs-gcm-api.png)

これで、通知ハブが GCM と連動するように構成されました。接続文字列を使用して、プッシュ通知の受信と送信の両方にアプリを登録できます。

##<a id="connecting-app"></a>通知ハブにアプリを接続する

###新しい Android プロジェクトを作成する

1. Android Studio で新しい Android Studio プロジェクトを開始する

   	![Android Studio - new project][13]

2. **[電話とタブレット]** フォーム ファクターを選択し、サポートする **[最低限の SDK]** を選択します。その後、**[次へ]** をクリックします。

   	![Android Studio - project creation workflow][14]

3. 主なアクティビティに **[空のアクティビティ]** を選択し、**[次へ]** をクリックして、**[完了]** をクリックします。

###プロジェクトへの Google Play Services の追加

[AZURE.INCLUDE [Play サービスの追加](../../includes/notification-hubs-android-studio-add-google-play-services.md)]

###コードの追加

1. [NBintray の Notification-Hubs-Android-SDK](https://bintray.com/microsoftazuremobile/SDK/Notification-Hubs-Android-SDK/0.4) にある **[Files]** タブから `notification-hubs-0.4.jar` ファイルをダウンロードします。Android Studio の [Project View] ウィンドウにある **libs** フォルダーへファイルを直接ドラッグしてください。そのうえでファイルを右クリックし、**[Add as Library]** をクリックします。

2. **[app]** の `Build.Gradle` ファイルの **[dependencies]** セクションに次の行を追加します。

	    compile 'com.microsoft.azure:azure-notifications-handler:1.0.1@aar'

	**[dependencies]** セクションの後に次のリポジトリを追加します。

		repositories {
		    maven {
		        url "http://dl.bintray.com/microsoftazuremobile/SDK"
		    }
		}

3. GCM から登録 ID を取得し、それを使用してアプリケーション インスタンスを通知ハブに登録するようにアプリケーションを設定します。

	`AndroidManifest.xml` ファイルで、`</application>` タグのすぐ下に次の許可を追加します。`<your package>` は、`AndroidManifest.xml` ファイルの上部に表示されるパッケージ名に必ず置き換えてください (この例では `com.example.testnotificationhubs`)。

		<uses-permission android:name="android.permission.INTERNET"/>
		<uses-permission android:name="android.permission.GET_ACCOUNTS"/>
		<uses-permission android:name="android.permission.WAKE_LOCK"/>
		<uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />

		<permission android:name="<your package>.permission.C2D_MESSAGE" android:protectionLevel="signature" />
		<uses-permission android:name="<your package>.permission.C2D_MESSAGE"/>

3. **MainActivity** クラスで、クラス宣言の上に次のステートメント `import` を追加します。

		import android.app.AlertDialog;
		import android.content.DialogInterface;
		import android.os.AsyncTask;
		import com.google.android.gms.gcm.*;
		import com.microsoft.windowsazure.messaging.*;
		import com.microsoft.windowsazure.notifications.NotificationsManager;
		import android.widget.Toast;



4. クラスの最上位に、次のプライベート メンバーを追加します。これを使用して、アプリとクラウド サービスとの間にプッシュ通知チャネルを構成します。

		private String SENDER_ID = "<your project number>";
		private GoogleCloudMessaging gcm;
		private NotificationHub hub;
    	private String HubName = "<Enter Your Hub Name>";
		private String HubListenConnectionString = "<Your default listen connection string>";
	    private static Boolean isVisible = false;


	次の 3 つのプレースホルダーを以下のとおりに置き換えてください。
	* **SENDER\_ID**: `SENDER_ID` を、[Google Cloud Console](http://cloud.google.com/console) で取得しておいたプロジェクト番号に設定します。
	* **HubListenConnectionString**: `HubListenConnectionString` を、ハブの **DefaultListenAccessSignature** 接続文字列に設定します。接続文字列をコピーするには、[Azure ポータル]でハブの **[設定]** ブレードにある **[アクセス ポリシー]** をクリックします。
	* **HubName**: [Azure ポータル]のハブ ブレードに表示される通知ハブの名前を使用します。


5. `MainActivity` クラスの `OnCreate` メソッドで、アクティビティの作成時に通知ハブに登録するために次のコードを追加します。

        MyHandler.mainActivity = this;
        NotificationsManager.handleNotifications(this, SENDER_ID, MyHandler.class);
        gcm = GoogleCloudMessaging.getInstance(this);
        hub = new NotificationHub(HubName, HubListenConnectionString, this);
        registerWithNotificationHubs();

6. `MainActivity.java` で、`registerWithNotificationHubs()` メソッドに以下のコードを追加します。このメソッドでは、Google Cloud Messaging と Notification Hub に登録した後に登録成功をレポートします。

    	@SuppressWarnings("unchecked")
    	private void registerWithNotificationHubs() {
        	new AsyncTask() {
            	@Override
            	protected Object doInBackground(Object... params) {
                	try {
                    	String regid = gcm.register(SENDER_ID);
                    ToastNotify("Registered Successfully - RegId : " +
						hub.register(regid).getRegistrationId());
                	} catch (Exception e) {
                    	ToastNotify("Registration Exception Message - " + e.getMessage());
                    	return e;
                	}
                	return null;
            	}
        	}.execute(null, null, null);
    	}


7. アプリが実行され、表示されているときに通知を表示するように、`ToastNotify` メソッドをアクティビティに追加します。また、`onStart`、`onPause`、`onResume`、`onStop` をオーバーライドして、アクティビティがトーストを表示するために認識されるかどうかを指定します。

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

	    public void ToastNotify(final String notificationMessage)
	    {
	        if (isVisible == true)
	            runOnUiThread(new Runnable() {
	                @Override
	                public void run() {
	                    Toast.makeText(MainActivity.this, notificationMessage, Toast.LENGTH_LONG).show();
	                }
	            });
	    }

8. Android では既定で通知表示が処理されないため、自分で受信者を記述する必要があります。`AndroidManifest.xml` で、`<application>` 要素内に次の要素を追加します。

	> [AZURE.NOTE] プレースホルダーは、パッケージ名に置き換えてください。

        <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
            android:permission="com.google.android.c2dm.permission.SEND">
            <intent-filter>
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="<your package name>" />
            </intent-filter>
        </receiver>


9. [プロジェクト] ビューで、**[アプリ]**、**[src]**、** [main]**、**[java]** と展開します。**[java]** のパッケージ フォルダーを右クリックして、**[新規]**、**[Java Class (Java クラス)]** と順にクリックします。

	![Android Studio - new Java class][6]

10. 新しいクラスの **[名前]** フィールドに「**MyHandler**」と入力して **[OK]** をクリックします。


11. 次の import ステートメントを `MyHandler.java` の先頭に追加します。

		import android.app.NotificationManager;
		import android.app.PendingIntent;
		import android.content.Context;
		import android.content.Intent;
		import android.os.Bundle;
		import android.support.v4.app.NotificationCompat;
		import com.microsoft.windowsazure.notifications.NotificationsHandler;


12. 次のようにクラスの宣言を更新し、次のように `MyHandler` を `com.microsoft.windowsazure.notifications.NotificationsHandler` のサブクラスにします。

		public class MyHandler extends NotificationsHandler {


13. 次のコードを `MyHandler` クラスに追加します。

	このコードは `OnReceive` メソッドを上書きするため、ハンドラーがトーストをポップアップ表示し、受信した通知を表示します。ハンドラーは `sendNotification()` メソッドを使用して Android の通知マネージャーにもプッシュ通知を送信します。

    	public static final int NOTIFICATION_ID = 1;
    	private NotificationManager mNotificationManager;
    	NotificationCompat.Builder builder;
    	Context ctx;

    	static public MainActivity mainActivity;

    	@Override
    	public void onReceive(Context context, Bundle bundle) {
        	ctx = context;
        	String nhMessage = bundle.getString("message");

        	sendNotification(nhMessage);
	        mainActivity.ToastNotify(nhMessage);
    	}

    	private void sendNotification(String msg) {
        	mNotificationManager = (NotificationManager)
                ctx.getSystemService(Context.NOTIFICATION_SERVICE);

        	PendingIntent contentIntent = PendingIntent.getActivity(ctx, 0,
                new Intent(ctx, MainActivity.class), 0);

			NotificationCompat.Builder mBuilder =
				new NotificationCompat.Builder(ctx)
					.setSmallIcon(R.mipmap.ic_launcher)
					.setContentTitle("Notification Hub Demo")
					.setStyle(new NotificationCompat.BigTextStyle()
					.bigText(msg))
					.setContentText(msg);

			mBuilder.setContentIntent(contentIntent);
			mNotificationManager.notify(NOTIFICATION_ID, mBuilder.build());
		}

14. Android Studio のメニュー バーで、**[ビルド]**、**[プロジェクトのリビルド]** の順にクリックし、コードにエラーがないことを確認します。

##プッシュ通知を送信する

[Azure ポータル]から通知を送信して、アプリでプッシュ通知の受信テストを行うことができます。以下に示す、ハブ ブレードの **[トラブルシューティング]** セクションを参照してください。

![Azure Notification Hubs - Test Send](./media/notification-hubs-android-get-started/notification-hubs-test-send.png)

[AZURE.INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]

## (省略可能) アプリから直接プッシュ通知を送信する

テストでは、通常、開発中のアプリケーションから直接プッシュ通知を送信できることが必要になります。このセクションでは、このシナリオに適切に対応する方法について説明します。

1. Android Studio の [プロジェクト] ビューで **[アプリ]**、**[src]**、**[main]**、**[res]**、**[layout]** の順に展開します。`activity_main.xml` レイアウト ファイルを開き、**[テキスト]** タブをクリックしてファイルのテキスト内容の更新します。次のコードで更新します。このコードで通知ハブにプッシュ通知メッセージを送信するための新しい `Button` コントロールと `EditText` コントロールを追加します。このコードは一番下の `</RelativeLayout>` のすぐ前に追加します。

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

2. この行を `build.gradle` ファイルの `android` に追加します。

		useLibrary 'org.apache.http.legacy'

3. Android Studio の [Project] ビューで **[App]**、**[src]**、**[main]**、**[res]**、**[values]** の順に展開します。`strings.xml` ファイルを開き、新しい `Button` コントロールと `EditText` コントロールで参照される文字列の値を追加します。これらはファイルの一番下の `</resources>` のすぐ前に追加します。

        <string name="send_button">Send Notification</string>
        <string name="notification_message_hint">Enter notification message text</string>


4. `MainActivity.java` ファイルで、`MainActivity` クラスの上に次のステートメント `import` を追加します。

		import java.net.URLEncoder;
		import javax.crypto.Mac;
		import javax.crypto.spec.SecretKeySpec;

		import android.util.Base64;
		import android.view.View;
		import android.widget.EditText;

		import org.apache.http.HttpResponse;
		import org.apache.http.client.HttpClient;
		import org.apache.http.client.methods.HttpPost;
		import org.apache.http.entity.StringEntity;
		import org.apache.http.impl.client.DefaultHttpClient;


5. `MainActivity.java` ファイルで、`MainActivity` クラスの上に次のメンバーを追加します。

	`HubFullAccess` をハブの **DefaultFullSharedAccessSignature** 接続文字列で更新します。この接続文字列は [Azure ポータル]からコピーできます。通知ハブの **[設定]** ブレードで **[アクセス ポリシー]** をクリックします。

	    private String HubEndpoint = null;
	    private String HubSasKeyName = null;
	    private String HubSasKeyValue = null;
		private String HubFullAccess = "<Enter Your DefaultFullSharedAccess Connection string>";

6. アクティビティではハブの名前と、ハブの完全な共有アクセス接続文字列を保持します。通知ハブにメッセージを送信するには、POST 要求を認証するためのソフトウェア アクセス署名 (SaS) トークンを作成する必要があります。これを実行するには、接続文字列からキーのデータを解析し、「[共通概念](http://msdn.microsoft.com/library/azure/dn495627.aspx)」の REST API リファレンスで説明したように SaS トークンを作成します。

	`MainActivity.java` で、次のメソッドを `MainActivity` クラスに追加して接続文字列を解析します。

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

7. `MainActivity.java` で、次のメソッドを `MainActivity` クラスに追加して SaS 認証トークンを作成します。

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
                String token = "SharedAccessSignature sr=" + targetUri + "&sig="
                        + signature + "&se=" + expires + "&skn=" + HubSasKeyName;
                return token;
            } catch (Exception e) {
                DialogNotify("Exception Generating SaS",e.getMessage().toString());
            }

            return null;
        }


8. `MainActivity.java` で、次のメソッドを `MainActivity` クラスに追加して、**[通知の送信]** ボタン クリックを処理し、組み込みの REST API を使用してハブにプッシュ通知メッセージを送信します。

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
            final String json = "{"data":{"message":"" + notificationText.getText().toString() + ""}}";

            new Thread()
            {
                public void run()
                {
                    try
                    {
                        HttpClient client = new DefaultHttpClient();

                        // Based on reference documentation...
                        // http://msdn.microsoft.com/library/azure/dn223273.aspx
                        ParseConnectionString(HubFullAccess);
                        String url = HubEndpoint + HubName + "/messages/?api-version=2015-01";
                        HttpPost post = new HttpPost(url);

                        // Authenticate the POST request with the SaS token
                        post.setHeader("Authorization", generateSasToken(url));

                        // JSON content for GCM
                        post.setHeader("Content-Type", "application/json;charset=utf-8");

                        // Notification format should be GCM
                        post.setHeader("ServiceBusNotification-Format", "gcm");
                        post.setEntity(new StringEntity(json));

                        HttpResponse response = client.execute(post);
                    }
                    catch(Exception e)
                    {
                        DialogNotify("Exception",e.getMessage().toString());
                    }
                }
            }.start();
        }



##アプリケーションのテスト

####エミュレーターでのプッシュ通知

エミュレーターの内部でプッシュ通知をテストする場合は、エミュレーター イメージがアプリケーション用に選択した Google API レベルをサポートしていることを確認してください。イメージがネイティブの Google API をサポートしていない場合は、**SERVICE\_NOT\_AVAILABLE** 例外を受け取ることになります。

上記に加えて、**[設定]**、**[アカウント]** で実行しているエミュレーターに Google アカウントを追加していることを確認してください。それ以外の場合、GCM での登録の試行が **AUTHENTICATION\_FAILED** 例外になる可能性があります。

####アプリケーションの実行

1. アプリケーションを実行し、登録 ID の登録完了が報告されていることを確認します。

   	![Testing on Android - Channel registration][18]

2. ハブに登録されているすべての Android デバイスに送信される通知メッセージを入力します。

   	![Testing on Android - sending a message][19]

3. **[Send Notification (通知の送信)]** を押します。アプリケーションが実行されているすべてのデバイスで、`AlertDialog` インスタンスがプッシュ通知メッセージと共に表示されます。実行中のアプリケーションがなくても事前にプッシュ通知に登録されているデバイスでは、Android 通知マネージャーで通知が受信されます。左上隅から下へスワイプすると、通知を表示できます。

   	![Testing on Android - notifications][21]

##次のステップ

次のステップとして「[Notification Hubs を使用したユーザーへのプッシュ通知]」チュートリアルをお勧めします。このチュートリアルでは、特定のユーザーに対してタグを使用して ASP.NET バックエンドから通知を送信する方法について説明しています。

対象グループごとにユーザーを区分する場合は、チュートリアル「[通知ハブを使用したニュース速報の送信]」を参照してください。

Notification Hubs の全般的な情報については、「[Notification Hubs の概要]」を参照してください。

<!-- Images. -->
[6]: ./media/notification-hubs-android-get-started/notification-hub-android-new-class.png

[12]: ./media/notification-hubs-android-get-started/notification-hub-connection-strings.png

[13]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-new-project.png
[14]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-choose-form-factor.png
[15]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app4.png
[16]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app5.png
[17]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app6.png

[18]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-registered.png
[19]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-set-message.png

[20]: ./media/notification-hubs-android-get-started/notification-hub-create-console-app.png
[21]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-received-message.png
[22]: ./media/notification-hubs-android-get-started/notification-hub-scheduler1.png
[23]: ./media/notification-hubs-android-get-started/notification-hub-scheduler2.png
[29]: ./media/mobile-services-android-get-started-push/mobile-eclipse-import-Play-library.png

[30]: ./media/notification-hubs-android-get-started/notification-hubs-debug-hub-gcm.png

[31]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-add-ui.png


<!-- URLs. -->
[Get started with push notifications in Mobile Services]: ../mobile-services-javascript-backend-android-get-started-push.md
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Referencing a library project]: http://go.microsoft.com/fwlink/?LinkId=389800
[Azure Classic Portal]: https://manage.windowsazure.com/
[Notification Hubs の概要]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs を使用したユーザーへのプッシュ通知]: notification-hubs-aspnet-backend-android-notify-users.md
[通知ハブを使用したニュース速報の送信]: notification-hubs-aspnet-backend-android-breaking-news.md
[Azure ポータル]: https://portal.azure.com

<!---HONumber=AcomDC_0316_2016-->