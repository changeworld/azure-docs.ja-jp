1.  ファイル `AndroidManifest.xml` を開きます。次の手順では、コード内の *`**my_app_package**`* をプロジェクトのアプリケーション パッケージの名前 (`manifest` タグの `package` 属性の値) に置き換えます。

2.  次の新しいアクセス許可を、既存の `uses-permission` 要素の後に追加します。

        <permission android:name="**my_app_package**.permission.C2D_MESSAGE" 
            android:protectionLevel="signature" />
        <uses-permission android:name="**my_app_package**.permission.C2D_MESSAGE" /> 
        <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
        <uses-permission android:name="android.permission.GET_ACCOUNTS" />
        <uses-permission android:name="android.permission.WAKE_LOCK" />

3.  `application` 開始タグの後に次のコードを追加します。

        <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
                                        android:permission="com.google.android.c2dm.permission.SEND">
            <intent-filter>
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="**my_app_package**" />
            </intent-filter>
        </receiver>

4.  [Mobile Services Android SDK][] をダウンロードし、解凍します。**notifications** フォルダーを開き、**notifications-n.jar** ファイルを Eclipse プロジェクトの *libs* フォルダーにコピーした後、*libs* フォルダーを最新の情報に更新します。

    <div class="dev-callout"><b>注</b>
<p>ファイル名の末尾にある数値は、今後の SDK リリースで変更される可能性があります。</p>
</div>

5.  ファイル *ToDoItemActivity.java* を開き、次の import ステートメントを追加します。

        import com.microsoft.windowsazure.notifications.NotificationsManager;

6.  次のプライベート変数をクラスに追加します。ここで、*`<PROJECT_NUMBER>`* は、最初の手順で Google によってアプリケーションに割り当てられたプロジェクト番号です。

        public static final String SENDER_ID = "<PROJECT_NUMBER>";

7.  **onCreate** メソッドで、MobileServiceClient をインスタンス化する前に、デバイスに対応する通知ハンドラーを登録する次のコードを追加します。

        NotificationsManager.handleNotifications(this, SENDER_ID, MyHandler.class);

    後で、このコード内で参照されている MyHandler.class を定義します。

8.  Package Explorer で (`src` ノードの下にある) パッケージを右クリックし、**[New]**、**[Class]** の順にクリックします。

9.  **[Name]** に「`MyHandler`」と入力し、**[Superclass]** に「`com.microsoft.windowsazure.notifications.NotificationsHandler`」と入力します。**[Finish]** をクリックします。

    ![][]

    これで、新しい MyHandler クラスが作成されます。

10. 次の `MyHandler` クラスの import ステートメントを追加します。

        import android.app.NotificationManager;
        import android.app.PendingIntent;
        import android.content.Context;
        import android.content.Intent;
        import android.os.Bundle;
        import android.support.v4.app.NotificationCompat;

        import com.microsoft.windowsazure.mobileservices.Registration;
        import com.microsoft.windowsazure.mobileservices.RegistrationCallback;

11. 次に、`MyHandler` クラスの次のメンバーを追加します。

        public static final int NOTIFICATION_ID = 1;
        private NotificationManager mNotificationManager;
        NotificationCompat.Builder builder;
        Context ctx;

12. `MyHandler` クラスで、次のコードを追加して、**onRegistered** メソッドをオーバーライドします。これにより、モバイル サービスの Notification Hubs にデバイスが登録されます。

        @Override
        public void onRegistered(Context context, String gcmRegistrationId) {
            super.onRegistered(context, gcmRegistrationId);

            ToDoActivity.mClient.getPush().register(gcmRegistrationId, null, new RegistrationCallback() {
                @Override
                public void onRegister(Registration registration, Exception exception) {
                      if (exception != null) {
                            // handle error
                      }
                }
            });
        }

13. `MyHandler` クラスで、次のコードを追加して、**onReceive** メソッドをオーバーライドします。このコードは、通知を受信したときにその通知を表示します。

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

これで、アプリケーションがプッシュ通知をサポートするように更新されました。

<!-- URLs. -->

  [Mobile Services Android SDK]: http://go.microsoft.com/fwlink/p/?LinkID=280126
  []: ./media/mobile-services-android-get-started-push/mobile-services-android-create-class.png
