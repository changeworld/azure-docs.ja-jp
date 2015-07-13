<properties 
	pageTitle="Azure Notification Hubs の使用" 
	description="Azure Notification Hubs を使用してプッシュ通知を行う方法について説明します。" 
	services="notification-hubs" 
	documentationCenter="android" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor=""/>
<tags 
	ms.service="notification-hubs" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-android" 
	ms.devlang="java" 
	ms.topic="article" 
	ms.date="05/27/2015" 
	ms.author="wesmc"/>

# 通知ハブの使用

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

##概要

このトピックでは、Azure Notification Hubs を使用して Android アプリケーションにプッシュ通知を送信する方法について説明します。このチュートリアルでは、Google Cloud Messaging (GCM) を使用してプッシュ通知を受信する空の Android アプリケーションを作成します。完了すると、通知ハブを使用して、アプリケーションを実行するすべてのデバイスにプッシュ通知をブロードキャストできるようになります。

このチュートリアルでは、通知ハブを使用した簡単なブロードキャスト シナリオのデモンストレーションを行います。通知ハブを使用してデバイスの特定のユーザーとグループに対応する方法を理解するために、次のチュートリアルも一緒に参照してください。


##前提条件

このチュートリアルには、次のものが必要です。

+ Android Studio。<a href="http://go.microsoft.com/fwlink/?LinkId=389797">こちら</a>からダウンロードできます。
+ このチュートリアルを完了するには、アクティブな Azure アカウントが必要です。アカウントがない場合は、無料の試用アカウントを数分で作成することができます。詳細については、[Azure の無料評価版サイト](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fja-jp%2Fdocumentation%2Farticles%2Fnotification-hubs-android-get-started%2F)を参照してください。


このチュートリアルを完了することは、Android アプリケーションの他のすべての通知ハブ チュートリアルの前提条件です。


##Google Cloud Messaging をサポートするプロジェクトを作成する

[AZURE.INCLUDE [mobile-services-enable-Google-cloud-messaging](../../includes/mobile-services-enable-Google-cloud-messaging.md)]


##新しい Notification Hub を構成する

[AZURE.INCLUDE [notification-hubs-android-configure-push](../../includes/notification-hubs-android-configure-push.md)]

##<a id="connecting-app"></a>Notification Hub にアプリを接続する

###新しい Android プロジェクトを作成する

1. Android Studio で新しい Android Studio プロジェクトを開始する

   	![][13]

2. **[Phone and Tablet (電話とタブレット)]** フォーム ファクターとサポートする **[Minimum SDK (最低限の SDK)]** を選択します。その後、**[次へ]** をクリックします。

   	![][14]

3. 主なアクティビティに **[Blank Activity (空のアクティビティ)]** を選択します。**[次へ]** をクリックし、**[完了]** をクリックします。

###プロジェクトへの Google Play Services の追加

[AZURE.INCLUDE [Play サービスの追加](../../includes/mobile-services-add-Google-play-services.md)]

###コードの追加

1. Notification Hubs Android SDK を<a href="https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409">ここ</a>からダウンロードします。.zip ファイルを展開し、**notificationhubs\notification-hubs-0.4.jar** と **notifications\notifications-1.0.1.jar** をプロジェクトの **app\libs** ディレクトリにコピーします。Android Studio の [Project View (プロジェクト ビュー)] ウィンドウにある **libs** フォルダーへファイルを直接ドラッグできます。libs フォルダーを更新します。



	これら 2 つのパッケージのリファレンス ドキュメントは、以下のリンク * [com.microsoft.windowsazure.messaging](http://dl.windowsazure.com/androiddocs/com/microsoft/windowsazure/messaging/package-summary.html) * [com.microsoft.windowsazure.notifications](http://dl.windowsazure.com/androiddocs/com/microsoft/windowsazure/notifications/package-summary.html) にあります。


    > [AZURE.NOTE]ファイル名の末尾にある数値は、今後の SDK リリースで変更される可能性があります。

2. ここで、GCM から登録 ID を取得し、その ID を使用してアプリケーション インスタンスを通知ハブに登録するようにアプリケーションを設定します。

	AndroidManifest.xml ファイルで、`</application>` タグのすぐ下に次の許可を追加します。`<your package>` は、AndroidManifest.xml ファイルの上部に表示されるパッケージ名に必ず置き換えてください (この例では `com.example.testnotificationhubs`)。

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


4. クラスの最上位に、次のプライベート メンバーを追加します。

		private String SENDER_ID = "<your project number>";
		private GoogleCloudMessaging gcm;
		private NotificationHub hub;
    	private String HubName = "<Enter Your Hub Name>";
		private String HubListenConnectionString = "<Your default listen connection string>";


	次の 3 つのプレース ホルダーを必ず更新します。* **SENDER_ID**: `SENDER_ID` を [Google Cloud Console](http://cloud.google.com/console) で作成したプロジェクトから取得したプロジェクト番号に設定します。* **HubListenConnectionString**: `HubListenConnectionString` をハブ用の **DefaultListenAccessSignature** 接続文字列に設定します。[Microsoft Azure 管理ポータル]のハブにある**[ダッシュボード]** タブの **[View Connection String (接続文字列の表示)]** をクリックすると接続文字列がコピーできます。**HubName**: 使用するハブの Azure のページ上部に表示される通知ハブの名前 (完全な URL では**ありません**)です。たとえば、「`"myhub"`」のように入力します。



5. **MainActivity** クラスの **OnCreate** のメソッドで、アクティビティの作成時に登録を実行するように次のコードを追加します。

        MyHandler.mainActivity = this;
        NotificationsManager.handleNotifications(this, SENDER_ID, MyHandler.class);
        gcm = GoogleCloudMessaging.getInstance(this);
        hub = new NotificationHub(HubName, HubListenConnectionString, this);
        registerWithNotificationHubs();

6. **registerWithNotificationHubs()** メソッドには **MainActivity.java** で次のコードを追加します。このメソッドでは、Google Cloud Messaging と Notification Hub に登録した後に登録成功をレポートします。

    	@SuppressWarnings("unchecked")
    	private void registerWithNotificationHubs() {
        	new AsyncTask() {
            	@Override
            	protected Object doInBackground(Object... params) {
                	try {
                    	String regid = gcm.register(SENDER_ID);
                    DialogNotify("Registered Successfully","RegId : " + 
						hub.register(regid).getRegistrationId());
                	} catch (Exception e) {
                    	DialogNotify("Exception",e.getMessage());
                    	return e;
                	}
                	return null;
            	}
        	}.execute(null, null, null);
    	}

		
		/**
		  * A modal AlertDialog for displaying a message on the UI thread
		  * when theres an exception or message to report.
		  * 
		  * @param title   Title for the AlertDialog box. 
		  * @param message The message displayed for the AlertDialog box.
		  */
    	public void DialogNotify(final String title,final String message)
    	{
        	final AlertDialog.Builder dlg;
        	dlg = new AlertDialog.Builder(this);

        	runOnUiThread(new Runnable() {
            	@Override
            	public void run() {
                	AlertDialog dlgAlert = dlg.create();
                	dlgAlert.setTitle(title);
                	dlgAlert.setButton(DialogInterface.BUTTON_POSITIVE, 
						(CharSequence) "OK", 
						new DialogInterface.OnClickListener() {
                            public void onClick(DialogInterface dialog, int which) {
                                dialog.dismiss();
                            }
                        });
                	dlgAlert.setMessage(message);
                	dlgAlert.setCancelable(false);
                	dlgAlert.show();
            	}
        	});
    	}

7. Android には通知が表示されないため、独自の受信者を記述する必要があります。**AndroidManifest.xml** で、`<application>` 要素内に次の要素を追加します。

	> [AZURE.NOTE]プレースホルダーは、パッケージ名に置き換えてください。

        <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
            android:permission="com.google.android.c2dm.permission.SEND">
            <intent-filter>
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="<your package name>" />
            </intent-filter>
        </receiver>


8. [プロジェクト] ビューで、**[アプリ]**、**[src]**、** [main]**、**[java]** と展開します。**[java]** のパッケージ フォルダーを右クリックして、**[新規]**、**[Java Class (Java クラス)]** と順にクリックします。

	![][6]

9. 新しいクラスの **[名前]** フィールで、**MyHandler** と入力して **[OK]** とクリックします。
	

10. **MyHandler.java** の先頭に、次の import ステートメントを追加します。

		import android.app.NotificationManager;
		import android.app.PendingIntent;
		import android.content.Context;
		import android.content.Intent;
		import android.os.Bundle;
		import android.support.v4.app.NotificationCompat;
		import com.microsoft.windowsazure.notifications.NotificationsHandler;
		

11. 次のようにクラスの宣言を更新し、次のように `MyHandler` を `com.microsoft.windowsazure.notifications.NotificationsHandler` のサブクラスにします。

		public class MyHandler extends NotificationsHandler {


12. 次のコードを `MyHandler` クラスに追加します。

	このコードは `OnReceive` メソッドを上書きするため、ハンドラーが `AlertDialog` をポップアップ表示し、受信した通知を表示します。またハンドラ―は `sendNotification()` メソッドを使用して Android の通知マネージャーにも通知を送信します。

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
        	mainActivity.DialogNotify("Received Notification",nhMessage);
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
	
13. Android Studio のメニュー バーで、**[ビルド]**、**[Rebuild Project (プロジェクトのリビルド)]** と順にクリックし、エラーが検出されないかどうかを確認します。

##通知の送信方法



以下の画面に示すように、通知ハブの [デバッグ] タブを使用して、Azure ポータルで通知を送信し、アプリケーションで通知の受信テストを行うことができます。

![][30]

[AZURE.INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]

![][31]

1. Android Studio の [プロジェクト] ビューで**[アプリ]**、[**src**]、[**main**]、[**res**]、[**layout**] と展開します。**activity_main.xml** レイアウト ファイルを開き、**[テキスト]** タブをクリックしてファイルのテキスト内容の更新します。次のコードで更新します。このコードで通知ハブに通知メッセージを送信するための新しい `Button` と `EditText` コントロールを追加します。このコードは一番下の `</RelativeLayout>` のすぐ前に追加します。

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

2. Android Studio の [プロジェクト] ビューで**[アプリ]**、**[src]**、**[main]**、**[res]**、**[values]** と順に展開します。**strings.xml** ファイルを開き、新しい `Button` と `EditText` コントロールで参照される文字列の値を追加します。これらは、ファイル一番下の `</resources>` の前に追加します。

        <string name="send_button">Send Notification</string>
        <string name="notification_message_hint">Enter notification message text</string>


3. **MainActivity** ファイルで、`MainActivity` クラスの上に次のステートメント `import` を追加します。

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


3. **MainActivity** ファイルで、`MainActivity` クラスの上に次のメンバーを追加します。

	`HubName` にハブの名前を入力します。名前空間ではありません。たとえば、「myhub」です。また、**DefaultFullSharedAccessSignature** 接続文字列を入力します。この接続文字列は、通知ハブの **[ダッシュボード]** タブで **[View Connection String (接続文字列の表示)]** をクリックすると、[Microsoft Azure 管理ポータル]からコピーできます。

	    private String HubEndpoint = null;
	    private String HubSasKeyName = null;
	    private String HubSasKeyValue = null;
		private String HubFullAccess = "<Enter Your DefaultFullSharedAccess Connection string>";

4. アクティビティではハブの名前と、ハブの完全な共有アクセス接続文字列を保持します。通知ハブにメッセージを送信するには、POST 要求を認証するためのソフトウェア アクセス署名 (SaS) トークンを作成する必要があります。これを実行するには、接続文字列からキーのデータを解析し、「[共通概念](http://msdn.microsoft.com/library/azure/dn495627.aspx)」のREST API リファレンスで説明したように SaS トークンを作成します。

	**MainActivity.java** で、次のメソッドを `MainActivity` クラスに追加して接続文字列を解析します。

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

5. **MainActivity.java** で、次のメソッドを `MainActivity` クラスに追加して SaS 認証トークンを作成します。

        /**
         * Example code from http://msdn.microsoft.com/library/azure/dn495627.aspx to
         * construct a SaS token from the access key to authenticate a request.
         * 
         * @param uri The un-encoded resource URI string for this operation. The resource
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
	            // Apache commons codec.
                String signature = URLEncoder.encode(
                        Base64.encodeToString(rawHmac, Base64.NO_WRAP).toString(), "UTF-8");
    
                // construct authorization string
                String token = "SharedAccessSignature sr=" + targetUri + "&sig="
                        + signature + "&se=" + expires + "&skn=" + HubSasKeyName;
                return token;
            } catch (Exception e) {
                DialogNotify("Exception Generating SaS",e.getMessage().toString());
            }
    
            return null;
        }


6. **MainActivity.java** で、次のメソッドを `MainActivity` クラスに追加して、**[Send Notification (通知の送信)]** ボタン クリックを処理し、REST API を使用してハブに通知メッセージを送信します。

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
    
                        // Authenticate the POST request with the SaS token.
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

####エミュレーターのテスト
エミュレーターをテストする場合は、エミュレーター イメージが、アプリ用に選択した Google API レベルをサポートされていることを確認します。イメージが Google API をサポートしていない場合、**SERVICE_NOT_AVAILABLE** 例外を受け取ることになります。

また、**[設定]**、[**アカウント**] で実行しているエミュレーターに Google アカウントが追加していることを確認します。それ以外の場合、GCM での登録の試行が **AUTHENTICATION_FAILED** 例外になる可能性があります。

####アプリケーションをテストする     

1. アプリケーションを実行し、無事に登録された登録 ID が報告されることを確認します。

   	![][18]

2. ハブに登録されているすべての Android デバイスに送信される通知メッセージを入力します。

   	![][19]

3. **[Send Notification (通知の送信)]** を押します。実行しているアプリケーションがあるすべてのデバイスで、通知メッセージと `AlertDialog` が表示されます。実行中のアプリケーションがなくても事前に登録されているデバイスでは、通知マネージャーに追加された通知を受信します。左上から下へスワイプすると、通知を表示できます。

   	![][21]

##次のステップ

この簡単な例では、すべての Android デバイスに通知をブロードキャストします。特定のユーザーをターゲットとするには、「[通知ハブを使用したユーザーへのプッシュ通知]」を参照してください。一方、対象グループごとにユーザーを区分する場合は、「[通知ハブを使用したニュース速報の送信]」を参照してください。通知ハブの使用方法の詳細については、「[Windows Azure 通知ハブの概要]」を参照してください。


<!-- Images. -->
[1]: ./media/notification-hubs-android-get-started/mobile-services-google-new-project.png
[2]: ./media/notification-hubs-android-get-started/mobile-services-google-create-server-key.png
[3]: ./media/notification-hubs-android-get-started/mobile-services-google-create-server-key2.png
[4]: ./media/notification-hubs-android-get-started/mobile-services-google-create-server-key3.png
[5]: ./media/notification-hubs-android-get-started/mobile-services-google-enable-GCM.png
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
[Microsoft Azure 管理ポータル]: https://manage.windowsazure.com/
[Windows Azure 通知ハブの概要]: http://msdn.microsoft.com/library/jj927170.aspx
[通知ハブを使用したユーザーへのプッシュ通知]: notification-hubs-aspnet-backend-android-notify-users.md
[通知ハブを使用したニュース速報の送信]: notification-hubs-aspnet-backend-android-breaking-news.md
 

<!---HONumber=62-->