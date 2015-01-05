<properties urlDisplayName="Get Started" pageTitle="Azure Notification Hubs の使用" metaKeywords="" description="Learn how to use Azure Notification Hubs to push notifications." metaCanonical="" services="notification-hubs" documentationCenter="Mobile" title="Get started with Notification Hubs" authors="ricksal" solutions="" manager="dwrede" editor="" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="11/21/2014" ms.author="ricksal" />
# 通知ハブの使用

<div class="dev-center-tutorial-selector sublanding"><a href="/ja-jp/documentation/articles/notification-hubs-windows-store-dotnet-get-started/" title="Windows Universal">Windows ユニバーサル</a><a href="/ja-jp/documentation/articles/notification-hubs-windows-phone-get-started/" title="Windows Phone">Windows Phone</a><a href="/ja-jp/documentation/articles/notification-hubs-ios-get-started/" title="iOS">iOS</a><a href="/ja-jp/documentation/articles/notification-hubs-android-get-started/" title="Android" class="current">Android</a><a href="/ja-jp/documentation/articles/notification-hubs-kindle-get-started/" title="Kindle">Kindle</a><a href="/ja-jp/documentation/articles/notification-hubs-baidu-get-started/" title="Baidu">Baidu</a><a href="/ja-jp/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/" title="Xamarin.iOS">Xamarin.iOS</a><a href="/ja-jp/documentation/articles/partner-xamarin-notification-hubs-android-get-started/" title="Xamarin.Android">Xamarin.Android</a></div>

このトピックでは、Azure 通知ハブを使用して Android アプリケーションにプッシュ通知を送信する方法について説明します。 
このチュートリアルでは、Google Cloud Messaging (GCM) を使用してプッシュ通知を受信する空の Android アプリケーションを作成します。完了すると、通知ハブを使用して、アプリケーションを実行するすべてのデバイスにプッシュ通知をブロードキャストできるようになります。

このチュートリアルでは、プッシュ通知を有効にするための、次の基本的な手順について説明します。

* [Google Cloud Messaging を有効にする](#register)
* [Notification Hub を構成する](#configure-hub)
* [Notification Hub にアプリケーションを接続する](#connecting-app)
* [アプリケーションへの通知の送信方法](#send)
* [アプリケーションのテスト](#run-app)

このチュートリアルでは、通知ハブを使用した簡単なブロードキャスト シナリオのデモンストレーションを行います。通知ハブを使用してデバイスの特定のユーザーとグループに対応する方法を理解するために、次のチュートリアルも一緒に参照してください。 

このチュートリアルには、次のものが必要です。

+ Android SDK (前提条件として Eclipse を使用する必要があります)。 <a href="http://go.microsoft.com/fwlink/?LinkId=389797">ここからダウンロードできます。</a>
+ the [Mobile Services Android SDK]

このチュートリアルを完了することは、Android アプリケーションの他のすべての通知ハブ チュートリアルの前提条件です。 

<div class="dev-callout"><strong>注</strong> <p>このチュートリアルを完了するには、アクティブな Azure アカウントが必要です。アカウントがない場合は、無料の試用アカウントを数分で作成することができます。詳細については、「 <a href="http://www.windowsazure.com/ja-jp/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fja-jp%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Azure 無料評価版</a>」を参照してください。</p></div>

##<a id="register"></a>Google Cloud Messaging を有効にする

[WACOM.INCLUDE [Enable GCM](../includes/mobile-services-enable-Google-cloud-messaging.md)]

次に、この API キー値を使用して、通知ハブが GCM で認証し、アプリケーションの代わりにプッシュ通知を送信できるようにします。

##<a id="configure-hub"></a>通知ハブを構成する

1. [Azure の管理ポータル]にログオンし、画面の下部にある **[+新規]** をクリックします。

2. **[アプリケーション サービス]**、**[Service Bus]**、**[通知ハブ]**、**[簡易作成]** の順にクリックします。

   	![][7]

3. 通知ハブの名前を入力して、目的のリージョンを選択し、**[新しい通知ハブを作成する]** をクリックします。

   	![][8]

4. 前の手順で作成した名前空間 (通常は ***通知ハブ名*-ns**) **[構成]** タブをクリックします。

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

2. **[最小必須 SDK]** が *API 8: Android 2.2 (Froyo)* に設定されており、その次にある SDK の項目 2 つが最新バージョンに設定されていることを確認します。[次へ] を選択し、ウィザードに従い、**[Create activity]** が選択されていることを確認して、空のアクティビティを作成します。次のボックスで既定のランチャー アイコンを受け入れ、最後のボックスで **[完了]** をクリックします。

   	![][14]

###プロジェクトへの Google Play Services の追加

[WACOM.INCLUDE [Add Play Services](../includes/mobile-services-add-Google-play-services.md)]

###コードの追加

1. Notification Hubs Android SDK を <a href="https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409">ここからダウンロードします</a>。.zip ファイルを展開し、Package Explorer で notificationhubs\notification-hubs-0.1.jar ファイルをプロジェクトの \libs ディレクトリにコピーします。

2. [Mobile Services Android SDK] をダウンロードし、解凍します。**notifications** フォルダーを開き、**notifications-1.0.1.jar** ファイルを Eclipse プロジェクトの *libs* フォルダーにコピーした後、*libs* フォルダーを最新の情報に更新します。

    <div class="dev-callout"><b>注</b>
	<p>ファイル名の末尾にある数値は、今後の SDK リリースで変更される可能性があります。</p>
    </div>

	ここで、GCM から *registrationId* を取得し、registrationId を使用してアプリケーション インスタンスを通知ハブに登録するようにアプリケーションを設定します。

3. AndroidManifest.xml ファイルで、<uses-sdk/> 要素のすぐ下に次の行を追加します。`<your package>` を、手順 1 でアプリケーションに選択したパッケージに置き換えます (この例では `com.yourCompany.wams_notificationhubs`)。

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

	<div class="dev-callout"><b>注</b>
    <p>SENDER_ID に、先に取得したプロジェクト番号が設定されていることを確認します。</p>
    </div> 

		private String SENDER_ID = "<your project number>";
		private GoogleCloudMessaging gcm;
		private NotificationHub hub;

6. **OnCreate** メソッドで次のコードを追加し、プレースホルダーを、前のセクションで取得したリッスン アクセス権を持つ接続文字列と、(**、完全な URL ** ではなく) Azure でハブのページの最上部に表示される通知ハブの名前に置き換えます。

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

	<div class="dev-callout"><b>注</b>
    <p>プレースホルダーは、パッケージ名に置き換えてください。</p>
    </div> 

        <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
            android:permission="com.google.android.c2dm.permission.SEND">
            <intent-filter>
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="**my_app_package**" />
            </intent-filter>
        </receiver>


9. Package Explorer で (`src` ノードの下にある) パッケージを右クリックし、**[New]**、**[Class]** の順にクリックします。

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
	

##<a name="send"></a>アプリケーションへの通知の送信方法

通知は、 <a href="http://msdn.microsoft.com/ja-jp/library/windowsazure/dn223264.aspx">REST インターフェイスを使用するどのバックエンドからも Notification Hubs を使用して送信できます</a>。このチュートリアルでは、.NET コンソール アプリケーションを使用して通知を送信します。Notification Hubs に統合されている Azure Mobile Services バックエンドから通知を送信する方法の例については、「**Mobile Services アプリへのプッシュ通知の追加**」 ([.NET バックエンド](/ja-jp/documentation/articles/mobile-services-javascript-backend-android-get-started-push/) | [JavaScript バックエンド](/ja-jp/documentation/articles/mobile-services-javascript-backend-android-get-started-push/)) を参照してください。REST API を使用して通知を送信する方法の例については、「**Java/PHP から Notification Hubs を使用する方法**」 ([Java](/ja-jp/documentation/articles/notification-hubs-java-backend-how-to/) | [PHP](/ja-jp/documentation/articles/notification-hubs-php-backend-how-to/)) を参照してください。

1. Visual Studio で、**[ファイル]** メニューの **[新規作成]**、**[プロジェクト]** をクリックし、**[Visual C#]** で **[Windows]**、**[コンソール アプリケーション]**、**[OK]** の順にクリックします。  

   	![][20]

	これで、新しいコンソール アプリケーション プロジェクトが作成されます。

2. **[ツール]** メニューで **[ライブラリ パッケージ マネージャー]**、**[パッケージ マネージャー コンソール]** の順にクリックします。 

	これで、パッケージ マネージャー コンソールが表示されます。

3. コンソール ウィンドウで、次のコマンドを実行します。

        Install-Package WindowsAzure.ServiceBus
    
	これで、 <a href="http://nuget.org/packages/WindowsAzure.ServiceBus/">WindowsAzure.ServiceBus NuGet パッケージを使用して Azure Service Bus SDK への参照が追加されます</a>。 

4. Program.cs ファイルを開き、次の "using" ステートメントを追加します。

        using Microsoft.ServiceBus.Notifications;

5. **Program** クラスで、次のメソッドを追加します。

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            await hub.SendGcmNativeNotificationAsync("{ \"data\" : {\"msg\":\"Hello from Azure!\"}}");
        }

   	"hub name" プレースホルダーは、ポータルの **[通知ハブ]** タブに表示される通知ハブの名前に置き換えてください。また、接続文字列のプレースホルダーは、「Notification Hub を構成する」で取得した **DefaultFullSharedAccessSignature** という接続文字列に置き換えてください。 

	>[WACOM.NOTE]**リッスン** アクセスではなく、**フル** アクセスを持つ接続文字列を使用してください。リッスン アクセス文字列に通知を送信するアクセス許可はありません。

5. 次に、**Main** メソッド内に次の行を追加します。

         SendNotificationAsync();
		 Console.ReadLine();

##<a name="run-app"></a>アプリケーションのテスト

エミュレーターでアプリケーションをテストする前に、こうしたエミュレーターの構成手順を完了する必要があります (物理デバイスでテストする場合はスキップ)。

1. Google API をサポートしている Android Virtual Device (AVD) を使用します。

2. **[Window]** で **[Android Virtual Device Manager]** をクリックし、デバイスを選択してから **[Edit]** をクリックします。

   	![][18]

3. **[Target]** で **[Google APIs]** を選択し、**[OK]** をクリックします。

   	![][19]

4. 	プッシュ通知を受信するには、Android Virtual Device で Google アカウントを設定する必要があります (エミュレーターで、 <strong>[**Settings**]</strong> に移動して <strong>[**Add Account**]</strong>をクリックします)。さらに、エミュレーターがインターネットに接続されていることを確認します。


次の手順を使用して、アプリケーションをデバイスまたはエミュレーターで実行します。

1. 上部の Eclipse ツール バーで、**[Run]** をクリックし、アプリケーションを選択します。 
 
	これによりエミュレーターが起動し (エミュレーターを使用している場合)、アプリケーションが読み込まれ、実行されます。アプリケーションにより、GCM から *registrationId* が取得され、通知ハブに登録されます。

3. Visual Studio で F5 キーを押して、コンソール アプリケーションを実行します。 

	通知がアプリケーションに送信されます。  
 
5. アイコンが通知領域 (左上隅) に表示されたら、通知ドロワを引き出して、通知を表示します。  

   	![][21]

## <a name="next-steps"> </a>次のステップ

この簡単な例では、すべての Android デバイスに通知をブロードキャストします。特定のユーザーをターゲットとするには、「[Azure Notification Hubs によるユーザーへの通知]」を参照してください。一方、対象グループごとにユーザーを区分する場合は、「[通知ハブを使用したニュース速報の送信]」を参照してください。Notification Hubs の使用方法の詳細については、「[Notification Hubs の概要]」を参照してください。


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
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[アプリケーションの提出に関するページ]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[マイ アプリケーション]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Windows 向け Live SDK]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Mobile Services の使用]: /ja-jp/develop/mobile/tutorials/get-started/#create-new-service
[データの使用]: /ja-jp/develop/mobile/tutorials/get-started-with-data-android
[認証の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-users-android
[プッシュ通知の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-push-android
[アプリケーション ユーザーへのプッシュ通知]: /ja-jp/develop/mobile/tutorials/push-notifications-to-users-android
[スクリプトを使用したユーザーの承認]: /ja-jp/develop/mobile/tutorials/authorize-users-in-scripts-android
[JavaScript と HTML]: /ja-jp/develop/mobile/tutorials/get-started-with-push-js
[Referencing a library project (ライブラリ プロジェクトの参照)]: http://go.microsoft.com/fwlink/?LinkId=389800
[Azure の管理ポータル]: https://manage.windowsazure.com/
[wns オブジェクト]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[通知ハブの概要]: http://msdn.microsoft.com/ja-jp/library/jj927170.aspx

[Azure Notification Hubs によるユーザーへの通知]: /ja-jp/manage/services/notification-hubs/notify-users-aspnet
[通知ハブを使用したニュース速報の送信]: /ja-jp/manage/services/notification-hubs/breaking-news-dotnet


<!--HONumber=35.1-->
