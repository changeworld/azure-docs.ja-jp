<properties
	pageTitle="Xamarin.Forms アプリへのプッシュ通知の追加 | Microsoft Azure"
	description="Azure サービスを使用して Xamarin.Forms アプリにマルチプラットフォーム プッシュ通知を送信する方法について説明します。"
	services="app-service\mobile"
	documentationCenter="xamarin"
	authors="wesmc7777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="06/20/2016"
	ms.author="wesmc"/>

# Xamarin.Forms アプリにプッシュ通知を追加する

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

##概要

このチュートリアルでは、Azure サービスを使用して、各種ネイティブ デバイス プラットフォーム (Android、iOS、Windows) で実行されている Xamarin.Forms アプリにプッシュ通知を送信する方法について説明します。プッシュ通知は、Azure Notification Hubs を使用して Azure Mobile Apps バックエンドから送信されます。さまざまなプッシュ通知サービス (PNS) を使用して、どのプラットフォームで実行されているデバイスにも同じメッセージを送信できるように、テンプレート登録が使用されます。クロスプラットフォーム プッシュ通知の送信の詳細については、[Azure Notification Hubs](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) のドキュメントを参照してください。

対象の Xamarin.Forms アプリでサポートされているすべてのプロジェクトにプッシュ通知を追加します。レコードがバックエンドに挿入されるたびに、プッシュ通知が送信されます。

##前提条件

このチュートリアルで最善の結果が得られるように、最初にチュートリアル「[Xamarin.Forms アプリの作成](app-service-mobile-xamarin-forms-get-started.md)」を完了しておくことをお勧めします。このチュートリアルを完了すると、マルチプラットフォーム TodoList アプリである Xamarin.Forms プロジェクトを作成できます。

ダウンロードしたクイック スタートのサーバー プロジェクトを使用しない場合は、プッシュ通知拡張機能パッケージをプロジェクトに追加する必要があります。サーバーの拡張機能パッケージの詳細については、「[Work with the .NET backend server SDK for Azure Mobile Apps (Azure Mobile Apps 用の .NET バックエンド サーバー SDK を操作する)](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)」を参照してください。

プッシュ通知を iOS デバイスに送信するには、[Apple Developer Program メンバーシップ](https://developer.apple.com/programs/ios/)が必要です。また、[iOS シミュレーターはプッシュ通知をサポートしない](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator.html)ため、物理的な iOS デバイスを使用する必要があります。

##<a name="create-hub"></a>通知ハブを作成する

[AZURE.INCLUDE [app-service-mobile-create-notification-hub](../../includes/app-service-mobile-create-notification-hub.md)]

##プッシュ通知を送信するようにサーバー プロジェクトを更新する

[AZURE.INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]


##(省略可能) Android プロジェクトを構成して実行する

このセクションを完了すると、Android 用の Xamarin.Forms Droid プロジェクトのプッシュ通知を有効にすることができます。


###Google Cloud Messaging (GCM) を有効にする

[AZURE.INCLUDE [mobile-services-enable-google-cloud-messaging](../../includes/mobile-services-enable-google-cloud-messaging.md)]

###GCM を使用してプッシュ要求を送信するようにモバイル アプリ バックエンドを構成する

[AZURE.INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

###Android プロジェクトにプッシュ通知を追加する

Google Cloud Messaging (GCM) を使用するようにバックエンドが構成されている場合は、GCM へのアプリの登録、モバイル アプリ バックエンドを介した Azure Notification Hubs によるプッシュ通知へのアプリの登録、アプリによる通知の受信を可能にするコンポーネントとコードをクライアントに追加できます。

1. **Droid** プロジェクトで **Components** フォルダーを右クリックし、**[Get More Components... (その他のコンポーネントを取得...)]** をクリックします。**Google Cloud Messaging Client** コンポーネントを検索し、それをプロジェクトに追加します。このコンポーネントは、Xamarin Android プロジェクトのプッシュ通知をサポートします。


2. MainActivity.cs プロジェクト ファイルを開き、次の using ステートメントをファイルの先頭に追加します。

		using Gcm.Client;

3. **LoadApplication** の呼び出し後、次のコードを **OnCreate** メソッドに追加します。

	    try
	    {
	        // Check to ensure everything's setup right
	        GcmClient.CheckDevice(this);
	        GcmClient.CheckManifest(this);

	        // Register for push notifications
	        System.Diagnostics.Debug.WriteLine("Registering...");
	        GcmClient.Register(this, PushHandlerBroadcastReceiver.SENDER_IDS);
	    }
	    catch (Java.Net.MalformedURLException)
	    {
	        CreateAndShowDialog("There was an error creating the client. Verify the URL.", "Error");
	    }
	    catch (Exception e)
	    {
	        CreateAndShowDialog(e.Message, "Error");
	    }


4. 次のように、新しい **CreateAndShowDialog** ヘルパーを追加します。

		private void CreateAndShowDialog(String message, String title)
		{
			AlertDialog.Builder builder = new AlertDialog.Builder(this);

			builder.SetMessage (message);
			builder.SetTitle (title);
			builder.Create().Show ();
		}


5. **MainActivity** クラスに次のコードを追加します。

		// Create a new instance field for this activity.
		static MainActivity instance = null;

		// Return the current activity instance.
		public static MainActivity CurrentActivity
		{
		    get
		    {
		        return instance;
		    }
		}

	これにより、現在の **MainActivity** インスタンスが公開され、メイン UI スレッドで実行できるようになります。

6. **OnCreate** メソッドの先頭にある変数 `instance` を次のように初期化します。

		// Set the current instance of MainActivity.
		instance = this;

2. `GcmService.cs` という名前の **Droid** プロジェクトに新しいクラス ファイルを追加します。次の **using** ステートメントはファイルの先頭に配置してください。

		using Android.App;
		using Android.Content;
		using Android.Media;
		using Android.Support.V4.App;
		using Android.Util;
		using Gcm.Client;
		using Microsoft.WindowsAzure.MobileServices;
		using Newtonsoft.Json.Linq;
		using System;
		using System.Collections.Generic;
		using System.Diagnostics;
		using System.Text;


9. ファイルの先頭の **using** ステートメントと **namespace** 宣言の間に、次のアクセス許可要求を追加します。

		[assembly: Permission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
		[assembly: UsesPermission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
		[assembly: UsesPermission(Name = "com.google.android.c2dm.permission.RECEIVE")
		[assembly: UsesPermission(Name = "android.permission.INTERNET")]
		[assembly: UsesPermission(Name = "android.permission.WAKE_LOCK")]
		//GET_ACCOUNTS is only needed for android versions 4.0.3 and below
		[assembly: UsesPermission(Name = "android.permission.GET_ACCOUNTS")]

10. 名前空間に次のクラス定義を追加します。

		[BroadcastReceiver(Permission = Gcm.Client.Constants.PERMISSION_GCM_INTENTS)]
		[IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_MESSAGE }, Categories = new string[] { "@PACKAGE_NAME@" })]
		[IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_REGISTRATION_CALLBACK }, Categories = new string[] { "@PACKAGE_NAME@" })]
		[IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_LIBRARY_RETRY }, Categories = new string[] { "@PACKAGE_NAME@" })]
		public class PushHandlerBroadcastReceiver : GcmBroadcastReceiverBase<GcmService>
		{
		    public static string[] SENDER_IDS = new string[] { "<PROJECT_NUMBER>" };
		}

	>[AZURE.NOTE]**<PROJECT\_NUMBER>** を、前にメモしたプロジェクト番号に置き換えます。

11. 空の **GcmService** クラスを、新しいブロードキャスト レシーバーを使用する次のコードに置き換えます。

		 [Service]
		 public class GcmService : GcmServiceBase
		 {
		     public static string RegistrationID { get; private set; }

		     public GcmService()
		         : base(PushHandlerBroadcastReceiver.SENDER_IDS){}
		 }


12. **OnRegistered** イベント ハンドラーをオーバーライドし、**Register** メソッドを実装する次のコードを **GcmService** クラスに追加します。

		protected override void OnRegistered(Context context, string registrationId)
		{
		    Log.Verbose("PushHandlerBroadcastReceiver", "GCM Registered: " + registrationId);
		    RegistrationID = registrationId;

            var push = TodoItemManager.DefaultManager.CurrentClient.GetPush();

		    MainActivity.CurrentActivity.RunOnUiThread(() => Register(push, null));
		}

        public async void Register(Microsoft.WindowsAzure.MobileServices.Push push, IEnumerable<string> tags)
        {
            try
            {
                const string templateBodyGCM = "{"data":{"message":"$(messageParam)"}}";

                JObject templates = new JObject();
                templates["genericMessage"] = new JObject
                {
                	{"body", templateBodyGCM}
                };

                await push.RegisterAsync(RegistrationID, templates);
                Log.Info("Push Installation Id", push.InstallationId.ToString());
            }
            catch (Exception ex)
            {
                System.Diagnostics.Debug.WriteLine(ex.Message);
                Debugger.Break();
            }
        }

		Note that this code uses the `messageParam` parameter in the template registration. 

13. **OnMessage** を実装する次のコードを追加します。

		protected override void OnMessage(Context context, Intent intent)
		{
		    Log.Info("PushHandlerBroadcastReceiver", "GCM Message Received!");

		    var msg = new StringBuilder();

		    if (intent != null && intent.Extras != null)
		    {
		        foreach (var key in intent.Extras.KeySet())
		            msg.AppendLine(key + "=" + intent.Extras.Get(key).ToString());
		    }

		    //Store the message
		    var prefs = GetSharedPreferences(context.PackageName, FileCreationMode.Private);
		    var edit = prefs.Edit();
		    edit.PutString("last_msg", msg.ToString());
		    edit.Commit();

		    string message = intent.Extras.GetString("message");
		    if (!string.IsNullOrEmpty(message))
		    {
		        createNotification("New todo item!", "Todo item: " + message);
		        return;
		    }

		    string msg2 = intent.Extras.GetString("msg");
		    if (!string.IsNullOrEmpty(msg2))
		    {
		        createNotification("New hub message!", msg2);
		        return;
		    }

		    createNotification("Unknown message details", msg.ToString());
		}

        void createNotification(string title, string desc)
        {
            //Create notification
            var notificationManager = GetSystemService(Context.NotificationService) as NotificationManager;

            //Create an intent to show ui
            var uiIntent = new Intent(this, typeof(MainActivity));

            //Use Notification Builder
            NotificationCompat.Builder builder = new NotificationCompat.Builder(this);

            //Create the notification
            //we use the pending intent, passing our ui intent over which will get called
            //when the notification is tapped.
            var notification = builder.SetContentIntent(PendingIntent.GetActivity(this, 0, uiIntent, 0))
                    .SetSmallIcon(Android.Resource.Drawable.SymActionEmail)
                    .SetTicker(title)
                    .SetContentTitle(title)
                    .SetContentText(desc)

                    //Set the notification sound
                    .SetSound(RingtoneManager.GetDefaultUri(RingtoneType.Notification))

                    //Auto cancel will remove the notification once the user touches it
                    .SetAutoCancel(true).Build();

            //Show the notification
            notificationManager.Notify(1, notification);
        }

	これにより、受信した通知が処理され、表示される通知マネージャーに送信されます。

14. また、**GcmServiceBase** では、**OnUnRegistered** および **OnError** ハンドラー メソッドを実装する必要があります。これらは、次のように実装できます。

		protected override void OnUnRegistered(Context context, string registrationId)
		{
			Log.Error("PushHandlerBroadcastReceiver", "Unregistered RegisterationId : " + registrationId);
		}

		protected override void OnError(Context context, string errorId)
		{
			Log.Error("PushHandlerBroadcastReceiver", "GCM Error: " + errorId);
		}

これで、Android デバイスまたはエミュレーターで実行されているアプリでプッシュ通知をテストする準備が整いました。

###Android アプリでプッシュ通知をテストする

最初の 2 つの手順は、エミュレーターでテストする場合にのみ必要です。

1. 次に示すように Android Virtual Device (AVD) Manager で Google API がターゲットとして設定された仮想デバイスに対してデプロイまたはデバッグする必要があります。

2. **[Apps]**、**[Settings]**、**[Add account]** の順にクリックして、Google アカウントを Android デバイスに追加し、プロンプトに従って既存の Google アカウントをデバイスに追加し、新しいアカウントを作成します。

1. Visual Studio または Xamarin Studio で、**Droid** プロジェクトを右クリックし、**[スタートアップ プロジェクトに設定]** をクリックします。

2. **[実行]** をクリックしてプロジェクトをビルドし、Android デバイスまたはエミュレーターでアプリを開始します。

3. アプリケーションで、タスクを入力し、プラス (**+**) アイコンをクリックします。

4. 項目が追加されたときに、通知が受信されていることを確認します。


##(省略可能) iOS プロジェクトを構成して実行する

このセクションでは、iOS デバイス用の Xamarin iOS プロジェクトを実行します。iOS デバイスを使用していない場合は、このセクションを省略できます。

[AZURE.INCLUDE [notification-hubs-xamarin-enable-apple-push-notifications](../../includes/notification-hubs-xamarin-enable-apple-push-notifications.md)]


####APNS 用に通知ハブを構成する

1. [Azure ポータル](https://portal.azure.com/)にログインします。**[参照]**、**[Mobile Apps]**、作成したモバイル アプリ、**[設定]**、**[プッシュ]**、**[Apple (APNS)]**、**[証明書のアップロード]** の順にクリックします。以前にエクスポートした .p12 プッシュ証明書ファイルをアップロードします。開発とテスト用に開発プッシュ証明書を作成した場合は、**[サンドボックス]** を選択します。それ以外の場合は、**[運用]** を選択します。これで、iOS のプッシュ通知と連携するようにサービスが構成されました。

	![](./media/app-service-mobile-xamarin-ios-get-started-push/mobile-app-upload-apns-cert.png)


	次に、Xamarin Studio または Visual Studio で iOS のプロジェクト設定を構成します。

[AZURE.INCLUDE [app-service-mobile-xamarin-ios-configure-project](../../includes/app-service-mobile-xamarin-ios-configure-project.md)]


####iOS アプリへのプッシュ通知の追加

1. 次の `using` ステートメントを **AppDelegate.cs** ファイルの先頭に追加します。

        using Microsoft.WindowsAzure.MobileServices;
		using Newtonsoft.Json.Linq;


2. iOS プロジェクトで、AppDelegate.cs を開き、リモート通知をサポートするように `FinishedLaunching` を次のように更新します。

		public override bool FinishedLaunching (UIApplication app, NSDictionary options)
		{
			global::Xamarin.Forms.Forms.Init ();

			Microsoft.WindowsAzure.MobileServices.CurrentPlatform.Init();

            // IMPORTANT: uncomment this code to enable sync on Xamarin.iOS
            // For more information, see: http://go.microsoft.com/fwlink/?LinkId=620342
            //SQLitePCL.CurrentPlatform.Init();

            // registers for push for iOS8
            var settings = UIUserNotificationSettings.GetSettingsForTypes(
                UIUserNotificationType.Alert
                | UIUserNotificationType.Badge
                | UIUserNotificationType.Sound,
                new NSSet());

            UIApplication.SharedApplication.RegisterUserNotificationSettings(settings);
            UIApplication.SharedApplication.RegisterForRemoteNotifications();

			LoadApplication (new App ());

			return base.FinishedLaunching (app, options);
		}


4. AppDelegate.cs で、通知に登録するために、**RegisteredForRemoteNotifications** イベントのオーバーライドを追加します。

        public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
        {
            const string templateBodyAPNS = "{"aps":{"alert":"$(messageParam)"}}";

            JObject templates = new JObject();
            templates["genericMessage"] = new JObject
                {
                  {"body", templateBodyAPNS}
                };

            // Register for push with your mobile app
            Push push = TodoItemManager.DefaultManager.CurrentClient.GetPush();
            push.RegisterAsync(deviceToken, templates);
        }

5. AppDelegate.cs で、アプリの実行中に受信した通知を処理するために、**DidReceivedRemoteNotification** イベントのオーバーライドを追加します。

        public override void DidReceiveRemoteNotification(UIApplication application, NSDictionary userInfo, Action<UIBackgroundFetchResult> completionHandler)
        {
            NSDictionary aps = userInfo.ObjectForKey(new NSString("aps")) as NSDictionary;

            string alert = string.Empty;
            if (aps.ContainsKey(new NSString("alert")))
                alert = (aps[new NSString("alert")] as NSString).ToString();

            //show alert
            if (!string.IsNullOrEmpty(alert))
            {
                UIAlertView avAlert = new UIAlertView("Notification", alert, null, "OK", null);
                avAlert.Show();
            }
        }

これで、アプリケーションがプッシュ通知をサポートするように更新されました。

####iOS アプリでプッシュ通知をテストする

1. iOS プロジェクトを右クリックし、**[スタートアップ プロジェクトに設定]** をクリックします。

2. Visual Studio で **[実行]** または **F5** キーを押して、プロジェクトをビルドし、iOS デバイスでアプリケーションを開始します。**[OK]** をクリックして、プッシュ通知を受け入れます。

	> [AZURE.NOTE] アプリケーションからのプッシュ通知を明示的に受け入れる必要があります。これが必要であるのは、初めてアプリケーションを実行するときだけです。

3. アプリケーションで、タスクを入力し、プラス (**+**) アイコンをクリックします。

4. 通知が受信されたことを確認し、**[OK]** をクリックして通知を破棄します。


##(省略可能) Windows プロジェクトを構成して実行する

このセクションでは、Windows デバイス用の Xamarin.Forms WinApp プロジェクトと Xamarin.Forms WinPhone81 プロジェクトを実行します。次の手順では、ユニバーサル Windows プラットフォーム (UWP) プロジェクトもサポートされています。Windows デバイスを使用していない場合は、このセクションを省略できます。


####WNS を使用して Windows アプリをプッシュ通知に登録する

[AZURE.INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]


####WNS 用に通知ハブを構成する

[AZURE.INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]


####Windows アプリにプッシュ通知を追加する

1. Visual Studio で Windows プロジェクトの **App.xaml.cs** を開き、次の **using** ステートメントを追加します。

		using Newtonsoft.Json.Linq;
		using Microsoft.WindowsAzure.MobileServices;
		using System.Threading.Tasks;
		using Windows.Networking.PushNotifications;
		using <your_TodoItemManager_portable_class_namespace>;

	`<your_TodoItemManager_portable_class_namespace>` を、`TodoItemManager` クラスが含まれているポータブル プロジェクトの名前空間に置き換えます。
 

2. App.xaml.cs で、次の **InitNotificationsAsync** メソッドを追加します。

        private async Task InitNotificationsAsync()
        {
            var channel = await PushNotificationChannelManager
                .CreatePushNotificationChannelForApplicationAsync();

            const string templateBodyWNS = 
				"<toast><visual><binding template="ToastText01"><text id="1">$(messageParam)</text></binding></visual></toast>";

            JObject headers = new JObject();
            headers["X-WNS-Type"] = "wns/toast";

            JObject templates = new JObject();
            templates["genericMessage"] = new JObject
			{
				{"body", templateBodyWNS},
				{"headers", headers} // Needed for WNS.
			};

            await TodoItemManager.DefaultManager.CurrentClient.GetPush()
				.RegisterAsync(channel.Uri, templates);
        }

	このメソッドによって、プッシュ通知チャネルが取得され、対象の通知ハブからテンプレート通知を受け取るためのテンプレートが登録されます。*messageParam* をサポートするテンプレート通知がこのクライアントに配信されるようになります。

3. App.xaml.cs で、`async` 修飾子を追加して **OnLaunched** イベント ハンドラー メソッドの定義を更新します。その後で、メソッドの末尾に次のコード行を追加します。

        await InitNotificationsAsync();

	これにより、アプリの起動時に毎回プッシュ通知登録が作成または更新されるようになります。これを行うことは、WNS プッシュ チャネルが常にアクティブであることを保証するために重要です。

4. Visual Studio のソリューション エクスプローラーで、**Package.appxmanifest** ファイルを開き、**[通知]** で **[トースト対応]** を **[はい]** に設定します。

5. アプリをビルドし、エラーがないことを確認します。これで、クライアント アプリケーションが、モバイル アプリ バックエンドから送信されるテンプレート通知に登録されました。ソリューションのすべての Windows プロジェクトについて、このセクションを繰り返します。


####Windows アプリでプッシュ通知をテストする

1. Visual Studio で、Windows プロジェクトを右クリックし、**[スタートアップ プロジェクトに設定]** をクリックします。

2. **[実行]** ボタンを押してプロジェクトをビルドし、アプリケーションを開始します。

3. アプリケーションで、新しい todoitem の名前を入力し、プラス (**+**) アイコンをクリックして追加します。

4. 項目が追加されたときに、通知が受信されていることを確認します。

##次のステップ

プッシュ通知についてさらに学習します。

* [Azure Mobile Apps 用 .NET バックエンド サーバー SDK の操作](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#how-to-add-tags-to-a-device-installation-to-enable-push-to-tags) タグを利用して、プッシュ通知の対象となる顧客を絞ることができます。この記事では、タグをデバイス インストールに追加する方法について確認します。

* [プッシュ通知の問題の診断](../notification-hubs/notification-hubs-push-notification-fixer.md) 通知が破棄されたり、デバイスに届かなかったりするのにはさまざまな原因があります。このトピックでは、プッシュ通知のエラーの根本原因を分析、解明する方法について説明しています。

次のチュートリアルのいずれかに進むことを検討してください。

* [アプリへの認証の追加](app-service-mobile-xamarin-forms-get-started-users.md) ID プロバイダーを使用してアプリのユーザーを認証する方法について説明します。

* [アプリのオフライン同期の有効化](app-service-mobile-xamarin-forms-get-started-offline-data.md) モバイル アプリ バックエンドを使用して、オフライン サポートをアプリに追加する方法について説明します。オフライン同期を使用すると、エンド ユーザーはネットワークにアクセスできなくても、データの表示、追加、変更など、モバイル アプリとやり取りできます。

<!-- Images. -->

<!-- URLs. -->
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532
[apns object]: http://go.microsoft.com/fwlink/p/?LinkId=272333

<!---HONumber=AcomDC_0629_2016-->