<properties urlDisplayName="Get Started" pageTitle="Azure Notification Hubs の使用" metaKeywords="" description="Learn how to use Azure Notification Hubs to push notifications." metaCanonical="" services="notification-hubs" documentationCenter="Mobile" title="Get started with Notification Hubs" authors="piyushjo" solutions="" manager="timlt" editor="" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-nokia-x" ms.devlang="java" ms.topic="article" ms.date="01/01/1900" ms.author="piyushjo" />
# 通知ハブの使用

<div class="dev-center-tutorial-selector sublanding"><a href="/ja-jp/documentation/articles/notification-hubs-windows-store-dotnet-get-started/" title="Windows Universal">Windows Universal</a><a href="/ja-jp/documentation/articles/notification-hubs-windows-phone-get-started/" title="Windows Phone">Windows Phone</a><a href="/ja-jp/documentation/articles/notification-hubs-ios-get-started/" title="iOS">iOS</a><a href="/ja-jp/documentation/articles/notification-hubs-android-get-started/" title="Android">Android</a><a href="/ja-jp/documentation/articles/notification-hubs-kindle-get-started/" title="Kindle">Kindle</a><a href="/ja-jp/documentation/articles/notification-hubs-nokia-x-get-started/" title="Nokia X" class="current">Nokia X</a><a href="/ja-jp/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/" title="Xamarin.iOS">Xamarin.iOS</a><a href="/ja-jp/documentation/articles/partner-xamarin-notification-hubs-android-get-started/" title="Xamarin.Android">Xamarin.Android</a></div>

このトピックでは、**Azure Notification Hubs** を使用して **Nokia X** の Android アプリケーションにプッシュ通知を送信する方法について説明します。このチュートリアルでは、Nokia Notification Service を使用してプッシュ通知を受信する空の Android アプリケーションを作成します。完了すると、通知ハブを使用して、アプリケーションを実行するすべてのデバイスにプッシュ通知をブロードキャストできるようになります。

このチュートリアルでは、プッシュ通知を有効にするための、次の基本的な手順について説明します。

* [Nokia Notification service を構成する](#register)
* [通知ハブを構成する](#configure-hub)
* [通知ハブにアプリケーションを接続する](#connect-hub)
* [アプリケーションへの通知の送信方法](#send)
* [アプリケーションのテスト](#test-app)

<div class="dev-callout"><strong>注</strong> <p>このチュートリアルを完了するには、アクティブな Azure アカウントが必要です。アカウントがない場合は、無料の試用アカウントを数分で作成することができます。詳細については、<a href="http://www.windowsazure.com/ja-jp/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fja-jp%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Azure の無料評価版サイト</a>を参照してください。</p></div>

このチュートリアルには、次のものが必要です。

1.	Nokia X 開発環境 (<a href="http://developer.nokia.com/resources/library/nokia-x/getting-started/environment-setup.html">ここ</a>に示す手順に従って構成できます)。Nokia X 固有のパッケージをインストールし、次の手順に従って Nokia X エミュレーターを設定します。 
2.	Nokia X デバイス設定 (省略可能) (<a href="http://developer.nokia.com/resources/library/nokia-x/getting-started/device-setup.html">ここ</a>に示す手順に従って構成できます)。
3.	Android SDK (前提条件として Eclipse を使用する必要があります)。<a href="http://go.microsoft.com/fwlink/?linkid=389797&clcid=0x409">ここ</a>からダウンロードできます。
4.	Mobile Services Android SDK。<a href="https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409">ここ<a>からダウンロードできます。 

##<a id="register"></a>Nokia Notification service を構成する

1. <a href="https://console.push.nokia.com/ncm/Web/index.jsp">Nokia Notifications API Developer Console (Nokia Notifications API 開発者コンソール)</a> にサインインします。

2. **[Create services]** タブに移動し、**[Sender ID]** と **[Service description]** に入力して新しいサービスを作成します。

	![][11]

3. サービスの作成が完了したら、**Sender ID** と**Authorization Key**をメモします。 

4. **[My services]** タブに移動し、**Sender ID** と**Authorization Key**ごとに作成したすべてのサービスを一覧表示できます。

	![][12]

5. 詳細については、こちらの<a href="http://developer.nokia.com/resources/library/nokia-x/nokia-notifications/nokia-notifications-developer-guide.html">リンク</a>を参照してください。 

##<a id="configure-hub"></a>通知ハブを構成する

1. [Azure の管理ポータル]にログオンし、画面下部にある **[+ 新規]** をクリックします。

2. **[アプリケーション サービス]**、**[Service Bus]**、**[通知ハブ]**、**[簡易作成]** の順にクリックします。

	![][3]

3. 通知ハブの名前を入力して、目的のリージョンと、任意で名前空間を選択し、**[新しい通知ハブを作成する]** をクリックします。

	![][2]

4. 今作成した通知ハブに移動します。左側の [Service Bus] タブをクリックし、通知ハブを作成した名前空間をクリックしたら、[Notification Hubs] タブをクリックします。 

	![][9]

5. 作成した通知ハブに移動したら、上部の **[構成]** タブをクリックします。

	![][7]

6. 下にスクロールして **nokia x notification settings** を見つけ、Nokia Notification service から取得した承認キーを貼り付けてから、ページ下部の **[保存]** をクリックします。

	![][8]

7. ページ上部の **[ダッシュボード]** タブを選択し、下部の **[接続情報]** をクリックします。 

	![][10]

8.	このチュートリアルの後半で使用することになる **DefaultListenSharedAccessSignature** および **DefaultFullSharedAccessSignature** に対する 2 つの SAS 接続文字列をメモします。 

##<a id="connect-hub"></a>通知ハブにアプリケーションを接続する

###新しい Android プロジェクトの作成

1. Eclipse ADT で、新しい Android プロジェクトを作成します ([File] -> [New] -> [Android Application])。

2. [最小必須 SDK] が **[API 16: Android 4.1 (Jelly Bean)]** に設定されており、その次にある SDK の項目 2 つが最新バージョンに設定されていることを確認します。[次へ] を選択し、ウィザードに従い、**[アクティビティの作成]** が選択されていることを確認して、**空のアクティビティ**を作成します。次のボックスで既定のランチャー アイコンを受け入れ、最後のボックスで **[完了]** をクリックします。

	![][5]

3. プロジェクトのビルド ターゲットが正しく設定されていることを確認します (このサンプルでは、Platform = 4.1.2、API Level = 16)。プロジェクトを右クリックし、[プロパティ] を選択して、プロジェクト プロパティ ダイアログで Android を選択します。 
	
	![][13]

4.	プロジェクトに Nokia Notification Service の JAR ファイルを追加します。この Nokia Notifications ヘルパー ライブラリ、**push.jar** はGCM ヘルパー ライブラリに似た API を備えています。
[Project Properties]、[Java Build Path]、[Libraries]、[Add External JARs] の順にクリックし、**\extras\nokia\nokia_x_services\libs\nna\push.jar** にある **push.jar** を追加します。ライブラリの Javadoc は、Eclipse IDE がインストールされる **\extras\nokia\nokia_x_services\javadocs\nna** にあります。

5. push.jar ライブラリも、Package Explorer でプロジェクトの \libs ディレクトリにコピーしてください。 

6. 通知ハブ Android SDK を<a href="https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409">ここ</a>からダウンロードします。zip ファイルを展開し、パッケージ エクスプローラーで notificationhubs\notification-hubs-0.3.jar ファイルをプロジェクトの \libs ディレクトリにコピーします。

    <div class="dev-callout"><b>注</b>
	<p>ファイル名の末尾にある数値は、今後の SDK リリースで変更される可能性があります。</p>
    </div>

###コードの追加

1. **AndroidManifest.xml** ファイルを開き、アプリケーション要素を次のコード行で置き換えます。このとき、Android アプリケーションの作成時に指定したパッケージ名で `[YourPackageName]` を置き換えます。
		
		<!-- Push Notifications connects to Internet services. -->
	    <uses-permission android:name="android.permission.INTERNET" />
	
	    <!-- Keeps the processor from sleeping when a message is received. -->
	    <uses-permission android:name="android.permission.WAKE_LOCK" />
	
		 <!--
	     Creates a custom permission so only this app can receive its messages.
	
	     NOTE: the permission *must* be called PACKAGE.permission.C2D_MESSAGE,
	           where PACKAGE is the application's package name.
	    -->
	    <permission 
	        android:name="[YourPackageName].permission.C2D_MESSAGE" 
	        android:protectionLevel="signature" />
	    <uses-permission android:name="[YourPackageName].permission.C2D_MESSAGE" />
	
	    <uses-permission android:name="com.nokia.pushnotifications.permission.RECEIVE" />
	    
	    <application
	        android:allowBackup="true"
	        android:icon="@drawable/ic_launcher"
	        android:label="@string/app_name"
	        android:theme="@style/AppTheme" >
	        <activity
	            android:name="[YourPackageName].MainActivity"
	            android:label="@string/app_name" >
	            <intent-filter>
	                <action android:name="android.intent.action.MAIN" />
	                <category android:name="android.intent.category.LAUNCHER" />
	            </intent-filter>
	        </activity>
	        
	        <receiver
	            android:name="com.nokia.push.PushBroadcastReceiver"
	            android:permission="com.nokia.pushnotifications.permission.SEND" >
	            <intent-filter>
	                
					<!-- Receives the actual messages. -->
	                <action android:name="com.nokia.pushnotifications.intent.RECEIVE" />
	                <!-- Receives the registration id. -->
	                <action android:name="com.nokia.pushnotifications.intent.REGISTRATION" />
	                <category android:name="[YourPackageName]" />
	            </intent-filter>
	        </receiver>
	
	        <service android:name="[YourPackageName].PushIntentService" />
	    </application> 

2. パッケージ エクスプローラーで、(`src` ノードの下の) パッケージを右クリックし、**[新規]**、**[クラス]** の順にクリックして、**ConfigurationSettings.java** という新しいクラスを作成します。
![][6]

	サンプルで使用する定数を定義する次のコードを追加します。

		public class ConfigurationSettings {
	    	public static String NotificationHubName = "";
	    	public static String NotificationHubConnectionString = "";
	    	public static String SenderId = "";
		}
	
	Nokia プッシュ コンソールから構成 **SenderId**、および管理ポータルから **NotificationHubName** と **NotificationHubConnectionString** (DefaultListenSharedAccessSignature) を上記の定数に入力します。 

3. **MainActivity.java** で、次のインポート ステートメントを追加します。
	
		import com.nokia.push.PushRegistrar;

	次に、onCreate メソッドに次のコードを追加します。
 
		// Make sure the device has the proper dependencies.
		PushRegistrar.checkDevice(this);
	        
		// Make sure the manifest was properly set - comment out this line
		// while developing the app, then uncomment it when it's ready
		PushRegistrar.checkManifest(this);
	        
		// Register the device with the Nokia Notification service
		PushRegistrar.register(this, ConfigurationSettings.SenderId);

4. **PushIntentService.java** という名前の新しいクラスを追加し、次のコードを追加します。このコードは、Notification Hub への登録を行い、この Notification Hub から受信したメッセージも処理します。 

		import android.app.NotificationManager;
		import android.app.PendingIntent;
		import android.content.Context;
		import android.content.Intent;
		import android.os.Bundle;
		import android.support.v4.app.NotificationCompat;
		import android.util.Log;
		
		import com.microsoft.windowsazure.messaging.NotificationHub;
		import com.nokia.push.PushBaseIntentService;
		
		/**
		 * IntentService responsible for handling push notification messages.
		 */
		public class PushIntentService extends PushBaseIntentService {
			
			private NotificationManager mNotificationManager;
			private static NotificationHub hub;
		    public static final int NOTIFICATION_ID = 1;
		    private static final String TAG = "NokiaXPush/PushIntentService";
		
		    /**
		     * Constructor.
		     */
		    public PushIntentService() {
		    }

		    @Override
		    protected String[] getSenderIds(Context context) {
		        return new String[] { ConfigurationSettings.SenderId };
		    }   
		    
		    @Override
		    protected void onRegistered(Context context, String registrationId) {
		    	Log.i(TAG, "Nokia Registration ID \"" + registrationId + "\"");
		    	RegisterWithNotificationHub(context, registrationId);
		    }
		
		    public static void RegisterWithNotificationHub(Context context, String registrationId) {
		        if (hub == null) {
		            hub = new NotificationHub(
		            		ConfigurationSettings.NotificationHubName, 
		            		ConfigurationSettings.NotificationHubConnectionString, 
		            		context);
		        }
		        try {
					hub.register(registrationId);
			    	Log.i(TAG, "Registered with Notification Hub - '" 
			    			+ ConfigurationSettings.NotificationHubName + "'" 
			    			+ " with RegistrationID - '"
			    			+ registrationId + "'");
				} catch (Exception e) {
					e.printStackTrace();
				}
		    }
		
		    @Override
		    protected void onMessage(Context context, Intent intent) {
		    	String message = intentExtrasToString(intent.getExtras());
		    	Log.i(TAG, "Received message. Extras: " + message);
		    	generateNotification(context, message);
		    }
		    
		    /**
		     * Extracts the key-value pairs from the given Intent extras and returns
		     * them in a string.
		     */
		    private String intentExtrasToString(Bundle extras) {
		        StringBuilder sb = new StringBuilder();
		        sb.append("{ ");
		        
		        for (String key : extras.keySet()) {
		            sb.append(sb.length() <= 2 ? "" : ", ");
		            sb.append(key).append("=").append(extras.get(key));
		        }
		        
		        sb.append(" }");
		        return sb.toString();
		    }
		
		    /**
		     * Issues a notification to inform the user that server has sent a message.
		     */
		    private void generateNotification(Context context, String message)
		    {
		    	mNotificationManager = (NotificationManager)
		                context.getSystemService(Context.NOTIFICATION_SERVICE);
		
		        PendingIntent contentIntent = PendingIntent.getActivity(context, 0,
		            new Intent(context, MainActivity.class), 0);
		
		        NotificationCompat.Builder mBuilder =
		            new NotificationCompat.Builder(context)
		            .setSmallIcon(R.drawable.ic_launcher)
		            .setContentTitle("Notification Hub Demo")
		            .setStyle(new NotificationCompat.BigTextStyle()
		                     .bigText(message))
		            .setContentText(message);
		
		        mBuilder.setContentIntent(contentIntent);
		        mNotificationManager.notify(NOTIFICATION_ID, mBuilder.build());
		    }
		
			@Override
			protected void onError(Context arg0, String errorId) {
		        Log.i(TAG, "Received error:  " + errorId);
			}
		
			@Override
			protected void onUnregistered(Context arg0, String errorId) {
		        Log.i(TAG, "Received recoverable error: " + errorId);
			}
		}

##<a name="send"></a>アプリケーションへの通知の送信方法

Notification Hubs を使用すれば、<a href="http://msdn.microsoft.com/ja-jp/library/windowsazure/dn223264.aspx">REST インターフェイス</a>を使用するどのバックエンドからでも通知が送信できます。このチュートリアルでは、.NET コンソール アプリケーションを使用して表示します。 

1. Visual C# の新しいコンソール アプリケーションを作成します。

	![][4]

2. <a href="http://nuget.org/packages/WindowsAzure.ServiceBus/">WindowsAzure.ServiceBus NuGet package</a> を使用して Azure Service Bus SDK への参照を追加します。Visual Studio のメイン メニューで、**[ツール]**、**[ライブラリ パッケージ マネージャー]**、**[パッケージ マネージャー コンソール]** の順に選択します。次に、コンソール ウィンドウで次のように入力し、Enter キーを押します。

        Install-Package WindowsAzure.ServiceBus

3. Program.cs ファイルを開き、次の using ステートメントを追加します。

        using Microsoft.ServiceBus.Notifications;

4. `Program` クラスで、次のメソッドを追加します。

		private static async void SendNotificationAsync()
		{
			NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<DefaultFullSharedAccessSignatureSASConnectionString>", "<hub name>");
			await hub.SendNokiaXNativeNotificationAsync("{\"data\" : {\"payload\":\"" + "Hello from Azure" + "\"}}");
		}

5. 次に、Main メソッド内に次の行を追加します。

         SendNotificationAsync();
		 Console.ReadLine();

##<a name="test-app"></a>アプリケーションのテスト

実際の電話でこのアプリケーションをテストする場合は、USB ケーブルでコンピューターに電話を接続するだけです。

エミュレーターを使用してこのアプリケーションをテストするには。

1. Eclipse 上部のツール バーで、[実行] をクリックし、アプリケーションを選択します。 

2. これによって、アプリケーションが接続した電話に読み込まれるか、またはエミュレーターが開始してアプリケーションが読み込まれ、実行されます。

3. アプリケーションは、Nokia Notification Service から registrationId を取得し、通知ハブに登録します。

    <div class="dev-callout"><b>注</b>
	<p>
	Android アプリケーションが通知ハブへの登録を完了すると、'Eclipse Logcat' ログに次のようなメッセージが表示されます。
	'Registered with Notification Hub - '<yourNotificationHubName>' with RegistrationID - '<RegistrationIdReturnedByNokiaNotificationService''
	</p>
    </div>
	
4.	.Net コンソール アプリケーションの使用時にテスト通知を送信するには、Visual Studio で F5 キーを押してアプリケーションを実行すると、通知が送信されてデバイスまたはエミュレーターの上部の通知領域に表示されます。 

<!-- Images. -->
[1]: ./media/notification-hubs-nokia-x-get-started/AndroidAppProperties.png
[2]: ./media/notification-hubs-nokia-x-get-started/AzureManagementCreateNH.png
[3]: ./media/notification-hubs-nokia-x-get-started/AzureManagementPortal.png
[4]: ./media/notification-hubs-nokia-x-get-started/ConsoleProject.png
[5]: ./media/notification-hubs-nokia-x-get-started/NewAndroidApp.png
[6]: ./media/notification-hubs-nokia-x-get-started/NewJavaClass.png
[7]: ./media/notification-hubs-nokia-x-get-started/NHConfigure.png
[8]: ./media/notification-hubs-nokia-x-get-started/NHConfigureNokiaX.png
[9]: ./media/notification-hubs-nokia-x-get-started/NHConfigureTopItem.png
[10]: ./media/notification-hubs-nokia-x-get-started/NHDashboard.png
[11]: ./media/notification-hubs-nokia-x-get-started/NokiaConsole.png
[12]: ./media/notification-hubs-nokia-x-get-started/NokiaConsoleService.png
[13]: ./media/notification-hubs-nokia-x-get-started/AndroidBuildTarget.png

<!-- URLs. -->
[Azure 管理ポータル]: https://manage.windowsazure.com/


