<properties linkid="develop-notificationhubs-tutorials-get-started-android" urlDisplayName="作業の開始" pageTitle="Azure 通知ハブの使用" metaKeywords="" description="Azure 通知ハブを使用してプッシュ通知を行う方法について説明します。" metaCanonical="" services="notification-hubs" documentationCenter="Mobile" title="通知ハブの使用" authors="ricksal" solutions="" manager="dwrede" editor="" />
#通知ハブの使用

<div class="dev-center-tutorial-selector sublanding"><a href="/ja-jp/manage/services/notification-hubs/getting-started-windows-dotnet" title="Windows ストア C#">Windows ストア C#</a><a href="/ja-jp/documentation/articles/notification-hubs-windows-phone-get-started/" title="Windows Phone">Windows Phone</a><a href="/ja-jp/documentation/articles/notification-hubs-ios-get-started/" title="iOS">iOS</a><a href="/ja-jp/documentation/articles/notification-hubs-android-get-started/" title="Android" class="current">Android</a><a href="/ja-jp/documentation/articles/notification-hubs-kindle-get-started/" title="Kindle">Kindle</a><a href="/ja-jp/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/" title="Xamarin.iOS">Xamarin.iOS</a><a href="/ja-jp/documentation/articles/partner-xamarin-notification-hubs-android-get-started/" title="Xamarin.Android">Xamarin.Android</a></div>

このトピックでは、Azure 通知ハブを使用して Android アプリケーションにプッシュ通知を送信する方法について説明します。
このチュートリアルでは、Google Cloud Messaging (GCM) を使用してプッシュ通知を受信する空の Android アプリケーションを作成します。完了すると、通知ハブを使用して、アプリケーションを実行するすべてのデバイスにプッシュ通知をブロードキャストできるようになります。

このチュートリアルでは、プッシュ通知を有効にするための、次の基本的な手順について説明します。

* [Google Cloud Messaging を有効にする](#register)
* [通知ハブを構成する](#configure-hub)
* [通知ハブにアプリケーションを接続する](#connecting-app)
* [アプリへの通知の送信方法](#send)
* [アプリケーションのテスト](#run-app)

このチュートリアルでは、通知ハブを使用した簡単なブロードキャスト シナリオのデモンストレーションを行います。通知ハブを使用してデバイスの特定のユーザーとグループに対応する方法を理解するために、次のチュートリアルも一緒に参照してください。

このチュートリアルには、次のものが必要です。

+ Android SDK (前提条件として Eclipse を使用する必要があります)。<a href="http://go.microsoft.com/fwlink/?LinkId=389797">ここ</a>からダウンロードできます。
+ [モバイル サービス Android SDK]

このチュートリアルを完了することは、Android アプリケーションの他のすべての通知ハブ チュートリアルの前提条件です。

<div class="dev-callout"><strong>メモ</strong><p>このチュートリアルを完了するには、アクティブな Azure アカウントが必要です。アカウントがない場合は、無料の試用アカウントを数分で作成することができます。詳細については、<a href="http://www.windowsazure.com/ja-jp/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fja-jp%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Azure の無料評価版のサイト</a>を参照してください。</p></div>

##<a id="register"></a>Google Cloud Messaging を有効にする

[WACOM.INCLUDE [Enable GCM](../includes/mobile-services-enable-Google-cloud-messaging.md)]

次に、この API キー値を使用して、通知ハブが GCM で認証し、アプリケーションの代わりにプッシュ通知を送信できるようにします。

##<a id="configure-hub"></a>通知ハブを構成する

1. [Azure 管理ポータル]にログオンし、画面の下部にある **[+新規]** をクリックします。

2. **[アプリケーション サービス]**、**[サービス バス]**、**[通知ハブ]**、**[簡易作成]** の順にクリックします。

   	![][7]

3. 通知ハブの名前を入力して、目的のリージョンを選択し、**[新しい通知ハブを作成する]** をクリックします。

   	![][8]

4. 前の手順で作成した名前空間 (通常は "****通知ハブ名**-ns") をクリックし、上部にある **[構成]** タブをクリックします。

   	![][9]

5. 上部にある **[通知ハブ]** タブをクリックし、前の手順で作成した通知ハブをクリックします。

   	![][10]

6. 上部にある **[構成]** タブをクリックし、前のセクションで取得した **API キー**値を入力して、**[保存]** をクリックします。

   	![][11]

7. 上部にある **[ダッシュボード]** タブを選択し、**[接続文字列の表示]** をクリックします。2 つの接続文字列をメモします。


これで、通知ハブが GCM と連動するように構成されました。接続文字列にアプリケーションを登録し、プッシュ通知を送信できます。

##<a id="connecting-app"></a>通知ハブにアプリケーションを接続する

###新しい Android プロジェクトの作成

1. Eclipse ADT で新しい Android プロジェクトを作成します ([File]、[New]、[Android Application])。

   	![][13]

2. **[Minimum Required SDK]** が *[API 8: Android 2.2 (Froyo)]* に設定され、次の 2 つの SDK エントリが使用可能な最新バージョンに設定されていることを確認します。[次へ] を選択し、ウィザードに従い、**[Create activity]** が選択されていることを確認して、空のアクティビティを作成します。次のボックスで既定のランチャー アイコンを受け入れ、最後のボックスで **[完了]** をクリックします。

   	![][14]

###プロジェクトへの Google Play Services の追加

[WACOM.INCLUDE [Add Play Services](../includes/mobile-services-add-Google-play-services.md)]

###コードの追加

1. 通知ハブ Android SDK を<a href="https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409">ここ</a>からダウンロードします。.zip ファイルを展開し、Package Explorer で notificationhubs\notification-hubs-0.1.jar ファイルをプロジェクトの \libs ディレクトリにコピーします。

2. [Mobile Services Android SDK] をダウンロードし、解凍します。**notifications** フォルダーを開き、**notifications-1.0.1.jar** ファイルを Eclipse プロジェクトの *libs* フォルダーにコピーした後、*libs* フォルダーを最新の情報に更新します。

    <div class="dev-callout"><b>メモ</b>
	<p>ファイル名の末尾にある数値は、今後の SDK リリースで変更される可能性があります。</p>
    </div>

	ここで、GCM から *registrationId* を取得し、registrationId を使用してアプリケーション インスタンスを通知ハブに登録するようにアプリケーションを設定します。

3. AndroidManifest.xml ファイルで、<uses-sdk/>要素のすぐ下に次の行を追加します。`<your package>` を、手順 1 でアプリケーションに選択したパッケージに置き換えます (この例では `com.yourCompany.wams_notificationhubs`)。

        <uses-permission android:name="android.permission.INTERNET"/>
		<uses-permission android:name="android.permission.GET_ACCOUNTS"/>
		<uses-permission android:name="android.permission.WAKE_LOCK"/>
		<uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />

		<permission android:name="<your package>.permission.C2D_MESSAGE" android:protectionLevel="signature" />
		<uses-permission android:name="<your package>.permission.C2D_MESSAGE"/>

4. **MainActivity** クラスで、次のステートメントを追加します。

		import android.os.AsyncTask;	
		import com.google.android.gms.gcm.*;
		import com.microsoft.windowsazure.messaging.*;
		import com.microsoft.windowsazure.notifications.NotificationsManager;


5. クラスの最上位に、次のプライベート メンバーを追加します。

	<div class="dev-callout"><b>メモ</b>
    <p>SENDER_2ID に、先に取得したプロジェクト番号が設定されていることを確認します。</p>
    </div> 

		private String SENDER_ID = "<your project number>";
		private GoogleCloudMessaging gcm;
		private NotificationHub hub;

6. **OnCreate** メソッドで次のコードを追加し、プレースホルダーを、前のセクションで取得したリッスン アクセス権を持つ接続文字列と、(完全な URL **ではなく**) Azure でハブのページの最上部に表示される通知ハブの名前に置き換えます。

		NotificationsManager.handleNotifications(this, SENDER_ID, MyHandler.class);

		gcm = GoogleCloudMessaging.getInstance(this);
        
		String connectionString = "<your listen access connection string>";
		hub = new NotificationHub("<your notification hub name>", connectionString, this);
		
		registerWithNotificationHubs();

7. MainActivity.java で、次のメソッドを作成します。

		@SuppressWarnings("unchecked")
		private void registerWithNotificationHubs() {
		   new AsyncTask() {
		      @Override
		      protected Object doInBackground(Object... params) {
		         try {
		            String regid = gcm.register(SENDER_ID);
		            hub.register(regid);
		         } catch (Exception e) {
		            return e;
		         }
		         return null;
		     }
		   }.execute(null, null, null);
		}

8. Android には通知が表示されないため、独自の受信者を記述する必要があります。**AndroidManifest.xml** で、`<application/>` 要素内に次の要素を追加します。

	<div class="dev-callout"><b>メモ</b>
    <p>プレースホルダーは、パッケージ名に置き換えてください。</p>
    </div> 

        <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
            android:permission="com.google.android.c2dm.permission.SEND">
            <intent-filter>
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="**my_app_package**" />
            </intent-filter>
        </receiver>


9. Package Explorer で (`[src]` ノードの下にある) パッケージを右クリックし、**[New]**、**[Class]** の順にクリックします。

10. **[Name]** に「`MyHandler`」と入力し、**[Superclass]** に「`com.microsoft.windowsazure.notifications.NotificationsHandler`」と入力して、**[Finish]** をクリックします。

	![][6]

	これで、新しい MyHandler クラスが作成されます。

11. 次の import ステートメントを追加します。

		import android.app.NotificationManager;
		import android.app.PendingIntent;
		import android.content.Context;
		import android.content.Intent;
		import android.os.Bundle;
		import android.support.v4.app.NotificationCompat;
		

12. 次のコードをクラスに追加します。

		public static final int NOTIFICATION_ID = 1;
		private NotificationManager mNotificationManager;
		NotificationCompat.Builder builder;
		Context ctx;
	
		
		@Override
		public void onReceive(Context context, Bundle bundle) {
		    ctx = context;
		    String nhMessage = bundle.getString("msg");
	
		    sendNotification(nhMessage);
		}
	
		private void sendNotification(String msg) {
			mNotificationManager = (NotificationManager)
		              ctx.getSystemService(Context.NOTIFICATION_SERVICE);
	
		    PendingIntent contentIntent = PendingIntent.getActivity(ctx, 0,
		          new Intent(ctx, MainActivity.class), 0);
	
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
	

##<a name="send"></a>アプリへの通知の送信方法

通知は、<a href="http://msdn.microsoft.com/ja-jp/library/windowsazure/dn223264.aspx">REST インターフェイス</a>を使用するどのバックエンドからも通知ハブを使用して送信できます。このチュートリアルでは、.NET コンソール アプリケーションと、ノード スクリプトを使用するモバイル サービスで通知を送信する 2 つの方法について説明します。

###.NET コンソール アプリケーションを使用して通知を送信するには

1. Visual C# の新しいコンソール アプリケーションを作成します。

   	![][20]

2. <a href="http://nuget.org/packages/WindowsAzure.ServiceBus/">WindowsAzure.ServiceBus NuGet パッケージ</a>を使用して Azure のサービス バスに参照を追加します。Visual Studio のメイン メニューで、**[ツール]**、**[ライブラリ パッケージ マネージャー]**、**[パッケージ マネージャー コンソール]** の順に選択します。次に、コンソール ウィンドウで次のように入力します。

        Install-Package WindowsAzure.ServiceBus

    次に、Enter キーを押します。

3. Program.cs ファイルを開き、次の using ステートメントを追加します。

        using Microsoft.ServiceBus.Notifications;

4. `Program` クラスで、次のメソッドを追加します。

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            await hub.SendGcmNativeNotificationAsync("{ \"data\" : {\"msg\":\"Hello from Azure!\"}}");
        }

5. 次に、Main メソッド内に次の行を追加します。

         SendNotificationAsync();
		 Console.ReadLine();


###モバイル サービスを使用して通知を送信するには

1. [Azure 管理ポータル]にログオンし、[モバイル サービス] をクリックします。モバイル サービスがまだ存在しない場合は、「[モバイル サービスの使用]」の手順に従います。

2. 上部にある **[スケジューラ]** タブを選択します。

   	![][22]

3. スケジュールされた新しいジョブを作成して名前を挿入し、**[要求時]** をクリックします。

   	![][23]

4. ジョブが作成されたら、ジョブ名をクリックします。上部のバーにある **[スクリプト]** タブをクリックします。

5. スケジューラ関数内に次のスクリプトを挿入します。必ず、プレースホルダーを、通知ハブの名前と既に取得してある *DefaultFullSharedAccessSignature* の接続文字列に置き換えてください。**[保存]** をクリックします。

        var azure = require('azure');
		var notificationHubService = azure.createNotificationHubService('<hub name>', '<connection string>');
		notificationHubService.gcm.send(null,'{"data":{"msg" : "Hello from Mobile Services!"}}',
    	  function (error)
    	  {
        	if (!error) {
               console.warn("Notification successful");
            }
            else
            {
              console.warn("Notification failed" + error);
            }
          }
	    );


##<a name="run-app"></a>アプリケーションのテスト

実際の電話でこのアプリケーションをテストする場合は、USB ケーブルでコンピューターに電話を接続するだけです。

エミュレーターを使用してこのアプリケーションをテストするには。

1. Google API をサポートしている Android Virtual Device (AVD) を使用します。

2. **[Window]** で **[Android Virtual Device Manager]** をクリックし、デバイスを選択してから **[Edit]** をクリックします。

   	![][18]

3. **[Target]** で **[Google APIs]** を選択し、[OK] をクリックします。**

   	![][19]

4. 	プッシュ通知を受信するには、Android Virtual Device で Google アカウントを設定する必要があります (エミュレーターで、<strong>[Settings]</strong> に移動して <strong>[Add Account]</strong> をクリックします)。さらに、エミュレーターがインターネットに接続されていることを確認します。



どのデバイスを選択した場合でも、次に以下のことを行います。

1. 上部の Eclipse ツール バーで、**[Run]** をクリックし、アプリケーションを選択します。これによって、アプリケーションが接続した電話に読み込まれるか、またはエミュレーターが開始してアプリケーションが読み込まれ、実行されます。

2. アプリケーションにより、GCM から *registrationId* が取得され、通知ハブに登録されます。

3. 次に、アプリケーションに通知を送信するために、前のセクションのいずれかの方法を使用します。

	- .Net コンソール アプリケーションを使用する場合は、Visual Studio で F5 キーを押してアプリケーションを実行します。これによって、通知が送信されます。
	- また、モバイル サービス スクリプトを使用している場合は、モバイル サービス画面の下段のバーで **[一度だけ実行する]** をクリックします。これによって、スクリプトで通知が送信されます。
 
5. アイコンが通知領域 (左上隅) に表示されます。通知ドロワを引き出し、通知を表示します。

   	![][21]

## <a name="next-steps"> </a>次のステップ

この簡単な例では、すべての Android デバイスに通知をブロードキャストします。特定のユーザーをターゲットとするには、「[通知ハブを使用したユーザーへのプッシュ通知]」を参照してください。一方、対象グループごとにユーザーを区分する場合は、「[通知ハブを使用したニュース速報の送信]」を参照してください。通知ハブの使用方法の詳細については、「[通知ハブの概要]」と「[方法: Azure 通知ハブ (Android アプリ)]」を参照してください。


<!-- Images. -->
[1]: ./media/notification-hubs-android-get-started/mobile-services-google-new-project.png
[2]: ./media/notification-hubs-android-get-started/mobile-services-google-create-server-key.png
[3]: ./media/notification-hubs-android-get-started/mobile-services-google-create-server-key2.png
[4]: ./media/notification-hubs-android-get-started/mobile-services-google-create-server-key3.png
[5]: ./media/notification-hubs-android-get-started/mobile-services-google-enable-GCM.png
[6]: ./media/notification-hubs-android-get-started/notification-hub-android-new-class.png
[7]: ./media/notification-hubs-android-get-started/notification-hub-create-from-portal.png
[8]: ./media/notification-hubs-android-get-started/notification-hub-create-from-portal2.png
[9]: ./media/notification-hubs-android-get-started/notification-hub-select-from-portal.png
[10]: ./media/notification-hubs-android-get-started/notification-hub-select-from-portal2.png
[11]: ./media/notification-hubs-android-get-started/notification-hub-configure-android.png
[12]: ./media/notification-hubs-android-get-started/notification-hub-connection-strings.png

[13]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app.png
[14]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app2.png
[15]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app4.png
[16]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app5.png
[17]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app6.png

[18]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app7.png
[19]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app8.png

[20]: ./media/notification-hubs-android-get-started/notification-hub-create-console-app.png
[21]: ./media/notification-hubs-android-get-started/notification-hub-android-toast.png
[22]: ./media/notification-hubs-android-get-started/notification-hub-scheduler1.png
[23]: ./media/notification-hubs-android-get-started/notification-hub-scheduler2.png
[29]: ./media/mobile-services-android-get-started-push/mobile-eclipse-import-Play-library.png

<!-- URLs. -->
[モバイル サービス Android SDK]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[アプリケーションの提出に関するページ: ]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[マイ アプリケーション]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Windows 向け Live SDK]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[モバイル サービスの使用]: /ja-jp/develop/mobile/tutorials/get-started/#create-new-service
[データの使用]: /ja-jp/develop/mobile/tutorials/get-started-with-data-android
[認証の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-users-android
[プッシュ通知の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-push-android
[アプリケーション ユーザーへのプッシュ通知]: /ja-jp/develop/mobile/tutorials/push-notifications-to-users-android
[スクリプトを使用したユーザーの承認]: /ja-jp/develop/mobile/tutorials/authorize-users-in-scripts-android
[JavaScript と HTML]: /ja-jp/develop/mobile/tutorials/get-started-with-push-js
[ライブラリ プロジェクトの参照]: http://go.microsoft.com/fwlink/?LinkId=389800
[Azure 管理ポータル]: https://manage.windowsazure.com/
[wns オブジェクトに関するページ]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[通知ハブの概要]: http://msdn.microsoft.com/ja-jp/library/jj927170.aspx
[方法: Azure 通知ハブ (Android アプリ)]: http://msdn.microsoft.com/ja-jp/library/dn282661.aspx

[通知ハブを使用したユーザーへのプッシュ通知]: /ja-jp/manage/services/notification-hubs/notify-users-aspnet
[通知ハブを使用したニュース速報の送信]: /ja-jp/manage/services/notification-hubs/breaking-news-dotnet


