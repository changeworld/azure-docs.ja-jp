1. アプリ プロジェクトで、`AndroidManifest.xml` ファイルを開きます。続く 2 つの手順では、コード内の _`**my_app_package**`_ を、プロジェクトのアプリケーション パッケージの名前 (`manifest` タグの `package` 属性の値) に置き換えます。 

2. 次の新しいアクセス許可を、既存の `uses-permission` 要素の後に追加します。

        <permission android:name="**my_app_package**.permission.C2D_MESSAGE" 
            android:protectionLevel="signature" />
        <uses-permission android:name="**my_app_package**.permission.C2D_MESSAGE" /> 
        <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
        <uses-permission android:name="android.permission.GET_ACCOUNTS" />
        <uses-permission android:name="android.permission.WAKE_LOCK" />

3. `application` 開始タグの後に次のコードを追加します。

        <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
            						 	android:permission="com.google.android.c2dm.permission.SEND">
            <intent-filter>
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="**my_app_package**" />
            </intent-filter>
        </receiver>


4. [Mobile Services Android SDK] をダウンロードし、解凍します。**notifications** フォルダーを開き、**notifications-n.jar** ファイルを Eclipse プロジェクトの *libs* フォルダーにコピーした後、*libs* フォルダーを最新の情報に更新します。

    > [AZURE.NOTE]ファイル名の末尾にある数値は、今後の SDK リリースで変更される可能性があります。

5.  ファイル *ToDoItemActivity.java* を開き、次の import ステートメントを追加します。

		import com.microsoft.windowsazure.notifications.NotificationsManager;


6. 次のプライベート変数をクラスに追加します。_`<PROJECT_NUMBER>`_ を、最初の手順で Google によってアプリケーションに割り当てられたプロジェクト番号に置き換えます。

		public static final String SENDER_ID = "<PROJECT_NUMBER>";

7. *MobileServiceClient* の定義を **private** から **public static** に変更し、次のようにします。

		public static MobileServiceClient mClient;



9. 次に、通知を処理する新しいクラスを追加する必要があります。Package Explorer で (`src` ノードの下にある) パッケージを右クリックし、**[New]**、**[Class]** の順にクリックします。

10. **[Name]** に「`MyHandler`」と入力し、**[Superclass]** に「`com.microsoft.windowsazure.notifications.NotificationsHandler`」と入力してから、**[Finish]** をクリックします。

	![](./media/mobile-services-android-get-started-push/mobile-services-android-create-class.png)

	これで、新しい MyHandler クラスが作成されます。

11. 次の `MyHandler` クラスの import ステートメントを追加します。

		import android.app.NotificationManager;
		import android.app.PendingIntent;
		import android.content.Context;
		import android.content.Intent;
		import android.os.AsyncTask;
		import android.os.Bundle;
		import android.support.v4.app.NotificationCompat;

	
12. 次に、`MyHandler` クラスの次のメンバーを追加します。

		public static final int NOTIFICATION_ID = 1;
		private NotificationManager mNotificationManager;
		NotificationCompat.Builder builder;
		Context ctx;


13. `MyHandler` クラスで、次のコードを追加して、**onRegistered** メソッドをオーバーライドします。このコードにより、デバイスがMobile Services の通知ハブに登録されます。

		@Override
		public void onRegistered(Context context,  final String gcmRegistrationId) {
		    super.onRegistered(context, gcmRegistrationId);
	
		    new AsyncTask<Void, Void, Void>() {
		    		    	
		    	protected Void doInBackground(Void... params) {
		    		try {
		    		    ToDoActivity.mClient.getPush().register(gcmRegistrationId, null);
		    		    return null;
	    		    }
	    		    catch(Exception e) { 
			    		// handle error    		    
	    		    }
					return null;  		    
	    		}
		    }.execute();
		}



14. `MyHandler` クラスで、次のコードを追加して、**onReceive** メソッドをオーバーライドします。このコードは、通知を受信したときにその通知を表示します。

		@Override
		public void onReceive(Context context, Bundle bundle) {
		    ctx = context;
		    String nhMessage = bundle.getString("message");
	
		    sendNotification(nhMessage);
		}
	
		private void sendNotification(String msg) {
			mNotificationManager = (NotificationManager)
		              ctx.getSystemService(Context.NOTIFICATION_SERVICE);
	
		    PendingIntent contentIntent = PendingIntent.getActivity(ctx, 0,
		          new Intent(ctx, ToDoActivity.class), 0);
	
		    NotificationCompat.Builder mBuilder =
		          new NotificationCompat.Builder(ctx)
		          .setSmallIcon(R.drawable.ic_launcher)
		          .setContentTitle("Notification Hub Demo")
		          .setStyle(new NotificationCompat.BigTextStyle()
		                     .bigText(msg))
		          .setContentText(msg);
	
		     mBuilder.setContentIntent(contentIntent);
		     mNotificationManager.notify(NOTIFICATION_ID, mBuilder.build());
		}


15. TodoActivity.java ファイルに戻り、*ToDoActivity* クラスの **onCreate** メソッドを更新して、通知のハンドラー クラスを登録します。*MobileServiceClient* がインスタンス化された後に、このコードを追加してください。


		NotificationsManager.handleNotifications(this, SENDER_ID, MyHandler.class);

    これで、アプリケーションがプッシュ通知をサポートするように更新されました。

<!-- URLs. -->
[Mobile Services Android SDK]: http://aka.ms/Iajk6q

<!---HONumber=August15_HO6-->