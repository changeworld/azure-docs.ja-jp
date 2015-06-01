<properties
	pageTitle="通知ハブの使用 (Xamarin.Android アプリケーション)"
	description="Azure Notification Hubs を使用して Xamarin Android アプリケーションにプッシュ通知を送信する方法について説明します。"
	authors="ysxu"
	manager="dwrede"
	editor=""
	services="notification-hubs"
	documentationCenter="xamarin"/>

<tags
	ms.service="notification-hubs"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin-android"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="04/14/2015"
	ms.author="yuaxu"/>

# 通知ハブの使用

[AZURE.INCLUDE [notification-hubs-selector-get-started](../includes/notification-hubs-selector-get-started.md)]

## 概要

このトピックでは、Azure Notification Hubs を使用して Xamarin.Android アプリケーションにプッシュ通知を送信する方法について説明します。このチュートリアルでは、Google Cloud Messaging (GCM) を使用してプッシュ通知を受信する空の Xamarin.Android アプリケーションを作成します。完了すると、通知ハブを使用して、アプリケーションを実行するすべてのデバイスにプッシュ通知をブロードキャストできるようになります。完成したコードは、[NotificationHubs アプリケーション][GitHub] サンプルで参照できます。

このチュートリアルでは、通知ハブを使用した簡単なブロードキャスト シナリオのデモンストレーションを行います。

## 前提条件

このチュートリアルには、次のものが必要です。

+ [Xamarin.Android]
+ アクティブな Google アカウント
+ [Azure Mobile Services コンポーネント]
+ [Azure Messaging コンポーネント]
+ [Google Cloud Messaging のクライアント コンポーネント]

このチュートリアルを完了することは、Xamarin.Android アプリの他のすべての通知ハブ チュートリアルの前提条件です。

> [AZURE.IMPORTANT]このチュートリアルを完了するには、アクティブな Azure アカウントが必要です。アカウントがない場合は、無料の試用アカウントを数分で作成することができます。詳細については、[Azure の無料評価版サイト](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A9C9624B5&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fja-jp%2Fdocumentation%2Farticles%2Fpartner-xamarin-notification-hubs-android-get-started%2F)を参照してください。

## <a name="register"></a>Google Cloud Messaging を有効にする

[AZURE.INCLUDE [mobile-services-enable-Google-cloud-messaging](../includes/mobile-services-enable-Google-cloud-messaging.md)]

## <a name="configure-hub"></a>Notification Hub を構成する

[AZURE.INCLUDE [notification-hubs-android-configure-push](../includes/notification-hubs-android-configure-push.md)]

## <a name="connecting-app"></a>Notification Hub にアプリケーションを接続する

### 新しいプロジェクトを作成する

1. Xamarin Studio (または Visual Studio) で、**[ファイル]**、**[新規]** をクリックした後、**[新しいソリューション]** ダイアログで **[Android アプリケーション]** をクリックし、最後に **[OK]** をクリックします。

   ![][14]

	This creates a new Android project.

2. [ソリューション] ビューで新しいプロジェクトを右クリックして **[オプション]** を選択し、プロジェクトのプロパティを開きます。**[Build]** セクションで **[Android Application]** 項目を選択します。

   ![][15]

3. **[最小 Android バージョン]** を [API Level 8] に設定します。

4. **[ターゲット Android バージョン]** を、ターゲットとする API バージョンに設定します (API レベル 8 以上にする必要があります)。

5. **パッケージ名**の先頭の文字は、小文字にしてください。

	> [AZURE.IMPORTANT]パッケージ名の先頭の文字は、小文字にする必要があります。小文字にしないと、以下のプッシュ通知に **BroadcastReceiver** と **IntentFilter** を登録するときに、アプリケーション マニフェスト エラーが発生します。

### プロジェクトに必要なコンポーネントを追加します。

Xamarin コンポーネント ストアから入手できる Google Cloud Messaging クライアントにより、Xamarin.Android でプッシュ通知をサポートするプロセスが効率化されます。

1. Xamarin.Android アプリケーションの [コンポーネント] フォルダーを右クリックし、**[コンポーネントの取得]** を選択します。

2. 「**Azure Mobile Services**」というコンポーネントを検索し、それをプロジェクトに追加します。

3. 「**Azure Messaging**」というコンポーネントを検索し、それをプロジェクトに追加します。

4. 「**Google Cloud Messaging Client**」というコンポーネントを検索し、それをプロジェクトに追加します。


### プロジェクトで通知ハブを設定する

1. **Constants.cs** クラスを作成し、次の定数値を定義します (プレースホルダーは値に置き換えます)。

        public const string SenderID = "<GoogleProjectNumber>"; // Google API Project Number

        // Azure app specific connection string and hub path
        public const string ConnectionString = "<Azure connection string>";
        public const string NotificationHubPath = "<hub path>";

2. 次の using ステートメントを **MainActivity.cs** に追加します。

		using Microsoft.WindowsAzure.MobileServices;
		using Gcm.Client;

3. **MainActivity** クラスで次のメソッドを作成します。

		private void RegisterWithGCM()
        {
            // Check to ensure everything's setup right
            GcmClient.CheckDevice(this);
            GcmClient.CheckManifest(this);

            // Register for push notifications
            System.Diagnostics.Debug.WriteLine("Registering...");
            GcmClient.Register(this, Constants.SenderID);
        }

4. 新しい **MyBroadcastReceiver** クラスを作成します。

	> [AZURE.NOTE]次に **BroadcastReceiver** を 1 から作成する手順を説明します。ただし、**MyBroadcastReceiver.cs** を手動ですばやく作成する代わりの手順として、GitHub のサンプル Xamarin.Android プロジェクトにある **GcmService.cs** ファイルを参照してください。**GcmService.cs** を複製してクラス名を変更する方法も利用できます。

5. 次の using ステートメントを、(前の追加したコンポーネントとアセンブリを参照する) **MyBroadcastReceiver.cs** に追加します。

		using WindowsAzure.Messaging;
		using Gcm.Client;

5. **using** ステートメントと **namespace** 宣言の間に次のアクセス許可要求を追加します。

		[assembly: Permission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
		[assembly: UsesPermission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
		[assembly: UsesPermission(Name = "com.google.android.c2dm.permission.RECEIVE")]

		//GET_ACCOUNTS is only needed for android versions 4.0.3 and below
		[assembly: UsesPermission(Name = "android.permission.GET_ACCOUNTS")]
		[assembly: UsesPermission(Name = "android.permission.INTERNET")]
		[assembly: UsesPermission(Name = "android.permission.WAKE_LOCK")]

6. **MyBroadcastReceiver.cs** で、**MyBroadcastReceiver** クラスを変更して次の内容に合わせます。

    	[BroadcastReceiver(Permission=Gcm.Client.Constants.PERMISSION_GCM_INTENTS)]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_MESSAGE }, 
			Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_REGISTRATION_CALLBACK }, 
			Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_LIBRARY_RETRY }, 
			Categories = new string[] { "@PACKAGE_NAME@" })]
        public class MyBroadcastReceiver : GcmBroadcastReceiverBase<PushHandlerService>
        {
            public static string[] SENDER_IDS = new string[] { Constants.SenderID };

            public const string TAG = "MyBroadcastReceiver-GCM";
        }

7. **MyBroadcastReceiver.cs** に、 **GcmServiceBase** から派生した **PushHandlerService** という名前の別のクラスを追加します。**Service** 属性をクラスに適用するようにしてください。

    	[Service] // Must use the service tag
    	public class PushHandlerService : GcmServiceBase
    	{
        	public static string RegistrationID { get; private set; }
        	private NotificationHub Hub { get; set; }

        	public PushHandlerService() : base(Constants.SenderID)
       		{
            	Log.Info(MyBroadcastReceiver.TAG, "PushHandlerService() constructor");
        	}
    	}


8. **GcmServiceBase** は、**OnRegistered()** メソッド、**OnUnRegistered()** メソッド、**OnMessage()** メソッド、**OnRecoverableError()** メソッド、および **OnError()** メソッドを実装しています。実装クラス **PushHandlerService** は、これらのメソッドをオーバーライドする必要があります。これらのメソッドは、通知ハブとのやり取りに応答して呼び出されます。

9. **PushHandlerService** の **OnRegistered()** メソッドを次のコードでオーバーライドします。

        protected override void OnRegistered(Context context, string registrationId)
        {
            Log.Verbose(MyBroadcastReceiver.TAG, "GCM Registered: " + registrationId);
            RegistrationID = registrationId;

            createNotification("PushHandlerService-GCM Registered...", "The device has been Registered, Tap to View!");

            Hub = new NotificationHub (Constants.NotificationHubPath, Constants.ConnectionString);
            try
            {
                Hub.UnregisterAll(registrationId);
            }
            catch (Exception ex)
            {
                Debug.WriteLine(ex.Message);
                Debugger.Break();
            }

            var tags = new List<string>() { "falcons" }; // create tags if you want

            try
            {
                var hubRegistration = Hub.RegisterNative (registrationId, tags);
            }
            catch (Exception ex)
            {
                Debug.WriteLine(ex.Message);
                Debugger.Break();
            }
        }

	> [AZURE.NOTE]上の **OnRegistered()** コードでは、特定のメッセージング チャネルを登録するタグを指定できる点に注目してください。

10. **PushHandlerService** の **OnMessage** メソッドを次のコードでオーバーライドします。

        protected override void OnMessage(Context context, Intent intent)
        {
            Log.Info(MyBroadcastReceiver.TAG, "GCM Message Received!");

            var msg = new StringBuilder();

            if (intent != null && intent.Extras != null)
            {
                foreach (var key in intent.Extras.KeySet())
                    msg.AppendLine(key + "=" + intent.Extras.Get(key).ToString());
            }

            string messageText = intent.Extras.GetString("msg");
            if (!string.IsNullOrEmpty(messageText))
            {
                createNotification("New hub message!", messageText);
                return;
            }

            createNotification("Unknown message details", msg.ToString());
        }

11. ユーザーに通知するには、上で使用されているように次の **createNotification** メソッドを **PushHandlerService** に追加します。

        void createNotification(string title, string desc)
        {
            //Create notification
            var notificationManager = GetSystemService(Context.NotificationService) as NotificationManager;

            //Create an intent to show ui
            var uiIntent = new Intent(this, typeof(MainActivity));

            //Create the notification
            var notification = new Notification(Android.Resource.Drawable.SymActionEmail, title);

            //Auto cancel will remove the notification once the user touches it
            notification.Flags = NotificationFlags.AutoCancel;

            //Set the notification info
            //we use the pending intent, passing our ui intent over which will get called
            //when the notification is tapped.
            notification.SetLatestEventInfo(this, title, desc, PendingIntent.GetActivity(this, 0, uiIntent, 0));

            //Show the notification
            notificationManager.Notify(1, notification);
        }

12. 抽象メンバー **OnUnRegistered()**、**OnRecoverableError()**、および **OnError()** をオーバーライドして、コードがコンパイルされるようにします。


## <a name="run-app"></a>エミュレーターを使用してアプリケーションを実行する

このアプリケーションをエミュレーターで実行する場合は、Google API をサポートしている Android Virtual Device (AVD) を使用してください。

	> [AZURE.IMPORTANT] In order to receive push notifications, you must set up a Google account on your Android Virtual Device (in the emulator, navigate to **Settings** and click **Add Account**). Also, make sure that the emulator is connected to the Internet.

1. **[ツール]** で **[Android エミュレーター マネージャーを開く]** をクリックし、デバイスを選択してから **[編集]** をクリックします。

   ![][18]

2. **[Target]** で **[Google APIs]** を選択し、**[OK]** をクリックします。

   ![][19]

3. 上部のツール バーで、**[実行]** をクリックし、アプリを選択します。これによりエミュレーターが起動し、アプリケーションが実行されます。

  アプリにより、GCM から *registrationId* が取得され、通知ハブに登録されます。

<a name="send"></a>バックエンドから通知を送信する

通知は、<a href="http://msdn.microsoft.com/library/windowsazure/dn223264.aspx">REST インターフェイス</a>を使用するどのバックエンドからも Notification Hubs を使用して送信できます。このチュートリアルでは、.NET コンソール アプリケーションと、ノード スクリプトを使用するモバイル サービスで通知を送信します。

.NET アプリケーションを使用して通知を送信するには

1. Visual C# の新しいコンソール アプリケーションを作成します。

   ![][20]

2. <a href="http://nuget.org/packages/WindowsAzure.ServiceBus/">WindowsAzure.ServiceBus NuGet パッケージ</a>を使用して Azure Service Bus SDK への参照を追加します。Visual Studio のメイン メニューで、**[ツール]**、**[ライブラリ パッケージ マネージャー]**、**[パッケージ マネージャー コンソール]** の順に選択します。次に、コンソール ウィンドウで次のように入力します。

        Install-Package WindowsAzure.ServiceBus

    次に、Enter キーを押します。

2. Program.cs ファイルを開き、次の using ステートメントを追加します。

        using Microsoft.ServiceBus.Notifications;

3. `Program` クラスで、次のメソッドを追加します。

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            await hub.SendGcmNativeNotificationAsync("{ "data" : {"msg":"Hello from Azure!"}}");
        }

4. 次に、Main メソッド内に次の行を追加します。

         SendNotificationAsync();
		 Console.ReadLine();

5. F5 キーを押してアプリケーションを実行します。トースト通知を受信します。

   ![][21]

モバイル サービスを使用して通知を送信するには、「[モバイル サービスの使用]」に従った後、次の手順を実行します。

1. [Azure 管理ポータル]にログオンし、[モバイル サービス] をクリックします。

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

6. 下部のバーにある **[一度だけ実行する]** をクリックします。トースト通知を受信します。

## <a name="next-steps"> </a>次のステップ

この簡単な例では、すべての Android デバイスに通知をブロードキャストします。特定のユーザーをターゲットとするには、「[通知ハブを使用したユーザーへのプッシュ通知]」を参照してください。一方、対象グループごとにユーザーを区分する場合は、「[通知ハブを使用したニュース速報の送信]」を参照してください。通知ハブの使用方法の詳細については、「[Windows Azure 通知ハブの概要]」と「[方法: Windows Azure 通知ハブ (Android アプリ)]」を参照してください。

<!-- Anchors. -->
[Enable Google Cloud Messaging]: #register
[Configure your Notification Hub]: #configure-hub
[Connecting your app to the Notification Hub]: #connecting-app
[Run your app with the emulator]: #run-app
[Send notifications from your back-end]: #send
[Next Steps]: #next-steps

<!-- Images. -->
[1]: ./media/partner-xamarin-notification-hubs-android-get-started/mobile-services-google-developers.png
[2]: ./media/partner-xamarin-notification-hubs-android-get-started/mobile-services-google-create-server.png
[3]: ./media/partner-xamarin-notification-hubs-android-get-started/mobile-services-google-create-server2.png
[4]: ./media/partner-xamarin-notification-hubs-android-get-started/mobile-services-google-create-server3.png

[7]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-from-portal.png
[8]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-from-portal2.png
[9]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-select-from-portal.png
[10]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-select-from-portal2.png
[11]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-configure-android.png
[12]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-connection-strings.png

[13]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app1.png
[14]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app2.png
[15]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app3.png

[18]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app7.png
[19]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app8.png

[20]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-console-app.png
[21]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-android-toast.png
[22]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-scheduler1.png
[23]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-scheduler2.png

<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[モバイル サービスの使用]: /develop/mobile/tutorials/get-started-xamarin-android/#create-new-service
[JavaScript and HTML]: /develop/mobile/tutorials/get-started-with-push-js

[Azure 管理ポータル]: https://manage.windowsazure.com/
[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Windows Azure 通知ハブの概要]: http://msdn.microsoft.com/library/jj927170.aspx
[方法: Windows Azure 通知ハブ (Android アプリ)]: http://msdn.microsoft.com/library/dn282661.aspx

[通知ハブを使用したユーザーへのプッシュ通知]: /manage/services/notification-hubs/notify-users-aspnet
[通知ハブを使用したニュース速報の送信]: /manage/services/notification-hubs/breaking-news-dotnet
[GCMClient Component page]: http://components.xamarin.com/view/GCMClient
[Xamarin.NotificationHub GitHub page]: https://github.com/SaschaDittmann/Xamarin.NotificationHub
[GitHub]: http://go.microsoft.com/fwlink/p/?LinkId=331329
[Xamarin.Android]: http://xamarin.com/download/
[Azure Mobile Services コンポーネント]: http://components.xamarin.com/view/azure-mobile-services/
[Google Cloud Messaging のクライアント コンポーネント]: http://components.xamarin.com/view/GCMClient/
[Azure Messaging コンポーネント]: http://components.xamarin.com/view/azure-messaging
<!--HONumber=52-->
