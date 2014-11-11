<properties linkid="manage-services-notification-hubs-getting-started-xamarin-android" urlDisplayName="" pageTitle="Get started with Notification Hubs for Xamarin.Android apps" metaKeywords="" description="Learn how to use Azure Notification Hubs to send push notifications to a Xamarin Android application." metaCanonical="" authors="donnam" solutions="" manager="dwrede" editor="" services="mobile-services,notification-hubs" documentationCenter="" title="Get started with Notification Hubs" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-android" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="donnam" />

# 通知ハブの使用

<div class="dev-center-tutorial-selector sublanding"><a href="/ja-jp/documentation/articles/notification-hubs-windows-store-dotnet-get-started/" title="Windows ユニバーサル">Windows ユニバーサル</a><a href="/ja-jp/documentation/articles/notification-hubs-windows-phone-get-started/" title="Windows Phone">Windows Phone</a><a href="/ja-jp/documentation/articles/notification-hubs-ios-get-started/" title="iOS">iOS</a><a href="/ja-jp/documentation/articles/notification-hubs-android-get-started/" title="Android">Android</a><a href="/ja-jp/documentation/articles/notification-hubs-kindle-get-started/" title="Kindle">Kindle</a><a href="/ja-jp/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/" title="Xamarin.iOS">Xamarin.iOS</a><a href="/ja-jp/documentation/articles/partner-xamarin-notification-hubs-android-get-started/" title="Xamarin.Android" class="current">Xamarin.Android</a></div>

このトピックでは、Azure Notification Hubs を使用して Xamarin.Android アプリケーションにプッシュ通知を送信する方法について説明します。
このチュートリアルでは、Google Cloud Messaging (GCM) を使用してプッシュ通知を受信する空の Xamarin.Android アプリケーションを作成します。完了すると、通知ハブを使用して、アプリケーションを実行するすべてのデバイスにプッシュ通知をブロードキャストできるようになります。完成したコードは、[NotificationHubs アプリケーション][NotificationHubs アプリケーション] サンプルで参照できます。

このチュートリアルでは、プッシュ通知を有効にするための、次の基本的な手順について説明します。

1.  [Google Cloud Messaging を有効にする][Google Cloud Messaging を有効にする]
2.  [通知ハブを構成する][通知ハブを構成する]
3.  [通知ハブにアプリケーションを接続する][通知ハブにアプリケーションを接続する]
4.  [エミュレーターを使用してアプリケーションを実行する][エミュレーターを使用してアプリケーションを実行する]
5.  [バックエンドから通知を送信する][バックエンドから通知を送信する]

このチュートリアルでは、通知ハブを使用した簡単なブロードキャスト シナリオのデモンストレーションを行います。このチュートリアルには、次のものが必要です。

-   [Xamarin.Android][1]
-   アクティブな Google アカウント
-   [Azure モバイル サービス コンポーネント][Azure モバイル サービス コンポーネント]
-   [Google Cloud Messaging コンポーネント][Google Cloud Messaging コンポーネント]

このチュートリアルを完了することは、Xamarin.Android アプリの他のすべての通知ハブ チュートリアルの前提条件です。

<div class="dev-callout"><strong>注</strong> <p>このチュートリアルを完了するには、アクティブな Azure アカウントが必要です。アカウントがない場合は、無料の試用アカウントを数分で作成することができます。詳細については、<a href="http://www.windowsazure.com/ja-jp/pricing/free-trial/?WT.mc_id=A9C9624B5&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fja-jp%2Fmanage%2Fservices%2Fnotification-hubs%2Fgetting-started-xamarin-android%2F" target="_blank">Azure の無料評価版サイト</a>を参照してください。</p></div>

## <a name="register"></a><span class="short-header">Google Cloud Messaging を有効にする</span>Google Cloud Messaging を有効にする

<div class="dev-callout"><b>注</b>
<p>このトピックの手順を完了するには、検証済みの電子メール アドレスを持つ Google アカウントが必要になります。新しい Google アカウントを作成するには、<a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a> にアクセスしてください。</p>
</div>

1.  [Google apis][Google apis] Web サイトに移動し、Google アカウント資格情報でサインインして、**[プロジェクトの作成]** をクリックします。

    ![][0]

    <div class="dev-callout"><b>注</b>
<p>既にプロジェクトがある場合は、ログイン後に <strong>[ダッシュボード]</strong> ページが表示されます。ダッシュボードで新しいプロジェクトを作成するには、<strong>[API Project]</strong> を展開し、<strong>[Other projects]</strong> の下の <strong>[Create]</strong> をクリックして、プロジェクト名を入力してから <strong>[Create project]</strong> をクリックします。</p>
</div>

2.  左側の列の **[概要]** をクリックし、**[ダッシュボード]** セクションのプロジェクト番号をメモします。

    チュートリアルの後の方で、クライアントの PROJECT\_ID 変数に、この値を設定します。

3.  [Google apis][Google apis] ページで **[サービス]** をクリックし、トグルをクリックして **[Google Cloud Messaging for Android]** を有効にし、サービスの条件に同意します。

4.  **[API アクセス]** をクリックし、**[新しいサーバー キーを作成]** をクリックします。

    ![][2]

5.  **[サーバー キーを API プロジェクト用に構成]** で、**[作成]** をクリックします。

    ![][3]

6.  **[API key]** の値をメモしておきます。

    ![][4]

次に、この API キー値を使用して、通知ハブが GCM で認証し、アプリケーションの代わりにプッシュ通知を送信できるようにします。

## <a name="configure-hub"></a><span class="short-header">通知ハブを構成する</span>通知ハブを構成する

1.  [Azure 管理ポータル][Azure 管理ポータル]にログオンし、画面の下部にある **[+新規]** をクリックします。

2.  **[アプリケーション サービス]**、**[Service Bus]**、**[通知ハブ]**、**[簡易作成]** の順にクリックします。

    ![][5]

3.  通知ハブの名前を入力して、目的のリージョンを選択し、**[新しい通知ハブを作成する]** をクリックします。

    ![][6]

4.  前の手順で作成した名前空間 (通常は "***通知ハブ名*-ns**") をクリックし、上部にある **[構成]** をクリックします。

    ![][7]

5.  上部にある **[通知ハブ]** タブをクリックし、前の手順で作成した通知ハブをクリックします。

    ![][8]

6.  上部にある **[構成]** タブをクリックし、前のセクションで取得した **API キー**値を入力して、**[保存]** をクリックします。

    ![][9]

7.  上部にある **[ダッシュボード]** タブを選択し、**[接続情報]** をクリックします。2 つの接続文字列をメモします。

    ![][10]

これで、通知ハブが GCM と連動するように構成されました。接続文字列にアプリケーションを登録し、プッシュ通知を送信できます。

## <a name="connecting-app"></a><span class="short-header">アプリケーションを接続する</span>通知ハブにアプリケーションを接続する

### 新しいプロジェクトを作成する

1.  Xamarin Studio (または Visual Studio) で、新しい Android プロジェクトを作成します ([File]、[New]、[Solution]、[Android Application])。

    ![][11]

    ![][12]

2.  [ソリューション] ビューで新しいプロジェクトを右クリックして **[オプション]** を選択し、プロジェクトのプロパティを開きます。**[Build]** セクションで **[Android Application]** 項目を選択します。

    ![][13]

3.  **[最小 Android バージョン]** を [API Level 8] に設定します。

4.  **[ターゲット Android バージョン]** を、ターゲットとする API バージョンに設定します (API レベル 8 以上にする必要があります)。

5.  **パッケージ名**の先頭の文字は、小文字にしてください。

    <div class="dev-callout"><b>注</b>
<p>パッケージ名の先頭の文字は、小文字にする必要があります。小文字にしないと、以下のプッシュ通知に **BroadcastReceiver** と **IntentFilter** を登録するときに、アプリケーション マニフェスト エラーが発生します。</p>
</div>

### Google Cloud Messaging クライアントをプロジェクトに追加する

Xamarin コンポーネント ストアから入手できる Google Cloud Messaging クライアントにより、Xamarin.Android でプッシュ通知をサポートするプロセスが効率化されます。

1.  Xamarin.Android アプリケーションの [コンポーネント] フォルダーを右クリックし、**[コンポーネントの取得]** を選択します。

2.  **Google Cloud Messaging クライアント** コンポーネントを検索します。

3.  コンポーネントを Xamarin.Android アプリケーションに追加します。必要なアセンブリ参照が自動的に追加されます。

### Xamarin.NotificationHub をプロジェクトに追加する

このアセンブリは、Azure Notification Hubs に登録するための簡単な方法を提供します。ここに示す手順に従ってダウンロードしてください。[サンプル ダウンロード][NotificationHubs アプリケーション]にもあります。

1.  [Xamarin.NotificationHub Github ページ][Xamarin.NotificationHub Github ページ]にアクセスし、ソース フォルダーをダウンロードしてビルドします。

2.  **\_external** フォルダーを Xamarin.Android プロジェクト フォルダー内に作成し、コンパイルされた **ByteSmith.WindowsAzure.Messaging.Android.dll** をその場所にコピーします。

3.  Xamarin.Android プロジェクトを Xamarin Studio (または Visual Studio) で開きます。

4.  プロジェクトの **[参照]** フォルダーを右クリックし、**[参照の編集]** を選択します。

5.  **[.Net アセンブリ]** タブに移動します。プロジェクトの **\_external** フォルダーを参照し、前の手順でビルドした **ByteSmith.WindowsAzure.Messaging.Android.dll** を選択し、**[追加]** をクリックします。[OK] をクリックしてダイアログ ボックスを閉じます。

### プロジェクトで通知ハブを設定する

1.  **Constants.cs** クラスを作成し、次の定数値を定義します (プレースホルダーは値に置き換えます)。

        public const string SenderID = "<GoogleProjectNumber>"; // Google API Project Number

        // Azure app specific connection string and hub path
        public const string ConnectionString = "<Azure connection string>";
        public const string NotificationHubPath = "<hub path>";

2.  次の using ステートメントを **MainActivity.cs** に追加します。

        using ByteSmith.WindowsAzure.Messaging;
        using Gcm.Client;

3.  **MainActivity** クラスで次のメソッドを作成します。

        private void RegisterWithGCM()
        {
            // Check to ensure everything's setup right
            GcmClient.CheckDevice(this);
            GcmClient.CheckManifest(this);

            // Register for push notifications
            System.Diagnostics.Debug.WriteLine("Registering...");
            GcmClient.Register(this, Constants.SenderID);
        }

4.  新しい **MyBroadcastReceiver** クラスを作成します。

    <div class="dev-callout"><b>注</b>
<p>次に **BroadcastReceiver** を 1 から作成する手順を説明します。ただし、**MyBroadcastReceiver.cs** を手動ですばやく作成する代わりの手順として、GitHub のサンプル Xamarin.Android プロジェクトにある **GcmService.cs** ファイルを参照してください。**GcmService.cs** を複製してクラス名を変更する方法も利用できます。</p>
</div>

5.  次の using ステートメントを、(前の追加したコンポーネントとアセンブリを参照する) **MyBroadcastReceiver.cs** に追加します。

        using ByteSmith.WindowsAzure.Messaging;
        using Gcm.Client;

6.  **using** ステートメントと **namespace** 宣言の間に次のアクセス許可要求を追加します。

        [assembly: Permission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
        [assembly: UsesPermission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
        [assembly: UsesPermission(Name = "com.google.android.c2dm.permission.RECEIVE")]

        //GET_ACCOUNTS is only needed for android versions 4.0.3 and below
        [assembly: UsesPermission(Name = "android.permission.GET_ACCOUNTS")]
        [assembly: UsesPermission(Name = "android.permission.INTERNET")]
        [assembly: UsesPermission(Name = "android.permission.WAKE_LOCK")]

7.  **MyBroadcastReceiver.cs** で、**MyBroadcastReceiver** クラスを変更して次の内容に合わせます。

        [BroadcastReceiver(Permission=Gcm.Client.Constants.PERMISSION_GCM_INTENTS)]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_MESSAGE }, Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_REGISTRATION_CALLBACK }, Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_LIBRARY_RETRY }, Categories = new string[] { "@PACKAGE_NAME@" })]
        public class MyBroadcastReceiver : GcmBroadcastReceiverBase<GcmService>
        {
            public static string[] SENDER_IDS = new string[] { Constants.SenderID };

            public const string TAG = "MyBroadcastReceiver-GCM";
        }

8.  別のクラスを、**PushHandlerServiceBase** から派生している **PushHandlerService** という名前の **MyBroadcastReceiver.cs** に追加します。クラスでは必ず **Service** ディレクティブを使用してください。

        [Service] //Must use the service tag
        public class GcmService : GcmServiceBase
        {
            public static string RegistrationID { get; private set; }
            private NotificationHub Hub { get; set; }

            public GcmService() : base(Constants.SenderID) 
            {
                Log.Info(MyBroadcastReceiver.TAG, "GcmService() constructor"); 
            }
        }

9.  **GcmServiceBase** は、**OnRegistered()** メソッド、**OnUnRegistered()** メソッド、**OnMessage()** メソッド、**OnRecoverableError()** メソッド、および **OnError()** メソッドを実装しています。実装クラス **GcmService** は、これらのメソッドをオーバーライドする必要があります。これらのメソッドは、通知ハブとのやり取りに応答して呼び出されます。

10. **PushHandlerService** の **OnRegistered()** メソッドを次のコードでオーバーライドします。

        protected override async void OnRegistered(Context context, string registrationId)
        {
            Log.Verbose(MyBroadcastReceiver.TAG, "GCM Registered: " + registrationId);
            RegistrationID = registrationId;

            createNotification("GcmService-GCM Registered...", "The device has been Registered, Tap to View!");

            Hub = new NotificationHub(Constants.NotificationHubPath, Constants.ConnectionString);
            try
            {
                await Hub.UnregisterAllAsync(registrationId);
            }
            catch (Exception ex)
            {
                Debug.WriteLine(ex.Message);
                Debugger.Break();
            }

            var tags = new List<string>() { "falcons" }; // create tags if you want

            try
            {
                var hubRegistration = await Hub.RegisterNativeAsync(registrationId, tags);
            }
            catch (Exception ex)
            {
                Debug.WriteLine(ex.Message); 
                Debugger.Break();
            }
        }

    <div class="dev-callout"><b>注</b>
<p>上の **OnRegistered()** コードでは、特定のメッセージング チャネルを登録するタグを指定できる点に注目してください。</p>
</div>

11. **PushHandlerService** の **OnMessage** メソッドを次のコードでオーバーライドします。

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

12. ユーザーに通知するには、上で使用されているように次の **createNotification** メソッドを **PushHandlerService** に追加します。

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

13. 抽象メンバー **OnUnRegistered()**、**OnRecoverableError()**、および **OnError()** をオーバーライドして、コードがコンパイルされるようにします。

## <a name="run-app"></a><span class="short-header">アプリを実行する</span>エミュレーターでアプリを実行する

このアプリケーションをエミュレーターで実行する場合は、Google API をサポートしている Android Virtual Device (AVD) を使用してください。

1.  **[ツール]** で **[Android エミュレーター マネージャーを開く]** をクリックし、デバイスを選択してから **[編集]** をクリックします。

    ![][14]

2.  **[Target]** で **\[Google APIs]** を選択し、**[OK]** をクリックします。

    ![][15]

3.  上部のツール バーで、**[実行]** をクリックし、アプリを選択します。これによりエミュレーターが起動し、アプリケーションが実行されます。

4.  アプリにより、GCM から *registrationId* が取得され、通知ハブに登録されます。

    <div class="dev-callout"><b>注</b>
<p>プッシュ通知を受信するには、Android Virtual Device で Google アカウントを設定する必要があります (エミュレーターで、[**Settings**] に移動して [**Add Account**] をクリックします)。さらに、エミュレーターがインターネットに接続されていることを確認します。</p>
</div>

## <a name="send"></a><span class="short-header">通知を送信する</span>バックエンドから通知を送信する

通知は、[REST インターフェイス][REST インターフェイス]を使用するどのバックエンドからも Notification Hubs を使用して送信できます。このチュートリアルでは、.NET コンソール アプリケーションと、ノード スクリプトを使用するモバイル サービスで通知を送信します。

.NET アプリケーションを使用して通知を送信するには

1.  Visual C# の新しいコンソール アプリケーションを作成します。

    ![][16]

2.  [WindowsAzure.ServiceBus NuGet パッケージ][WindowsAzure.ServiceBus NuGet パッケージ]を使用して Azure Service Bus SDK への参照を追加します。Visual Studio のメイン メニューで、**[ツール]**、**[ライブラリ パッケージ マネージャー]**、**[パッケージ マネージャー コンソール]** の順に選択します。次に、コンソール ウィンドウで次のように入力します。

        Install-Package WindowsAzure.ServiceBus

    次に、Enter キーを押します。

3.  Program.cs ファイルを開き、次の using ステートメントを追加します。

        using Microsoft.ServiceBus.Notifications;

4.  `Program` クラスで、次のメソッドを追加します。

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            await hub.SendGcmNativeNotificationAsync("{ \"data\" : {\"msg\":\"Hello from Azure!\"}}");
        }

5.  次に、Main メソッド内に次の行を追加します。

         SendNotificationAsync();
         Console.ReadLine();

6.  F5 キーを押してアプリケーションを実行します。トースト通知を受信します。

    ![][17]

モバイル サービスを使用して通知を送信するには、「[モバイル サービスの使用][モバイル サービスの使用]」に従った後、次の手順を実行します。

1.  [Azure 管理ポータル][Azure 管理ポータル]にログオンし、[モバイル サービス] をクリックします。

2.  上部にある **[スケジューラ]** タブを選択します。

    ![][18]

3.  スケジュールされた新しいジョブを作成して名前を挿入し、**[要求時]** をクリックします。

    ![][19]

4.  ジョブが作成されたら、ジョブ名をクリックします。上部のバーにある **[スクリプト]** タブをクリックします。

5.  スケジューラ関数内に次のスクリプトを挿入します。必ず、プレースホルダーを、通知ハブの名前と既に取得してある *DefaultFullSharedAccessSignature* の接続文字列に置き換えてください。**[保存]** をクリックします。

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

6.  下部のバーにある **[一度だけ実行する]** をクリックします。トースト通知を受信します。

## <a name="next-steps"> </a>次のステップ

この簡単な例では、すべての Android デバイスに通知をブロードキャストします。特定のユーザーをターゲットとするには、「[通知ハブを使用したユーザーへのプッシュ通知][通知ハブを使用したユーザーへのプッシュ通知]」を参照してください。一方、対象グループごとにユーザーを区分する場合は、「[通知ハブを使用したニュース速報の送信][通知ハブを使用したニュース速報の送信]」を参照してください。通知ハブの使用方法の詳細については、「[Windows Azure 通知ハブの概要][Windows Azure 通知ハブの概要]」と「[方法: Windows Azure 通知ハブ (Android アプリ)][方法: Windows Azure 通知ハブ (Android アプリ)]」を参照してください。

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [Xamarin.Android]: /ja-jp/documentation/articles/partner-xamarin-notification-hubs-android-get-started/ "Xamarin.Android"
  [NotificationHubs アプリケーション]: http://go.microsoft.com/fwlink/p/?LinkId=331329
  [Google Cloud Messaging を有効にする]: #register
  [通知ハブを構成する]: #configure-hub
  [通知ハブにアプリケーションを接続する]: #connecting-app
  [エミュレーターを使用してアプリケーションを実行する]: #run-app
  [バックエンドから通知を送信する]: #send
  [1]: http://xamarin.com/download/
  [Azure モバイル サービス コンポーネント]: http://components.xamarin.com/view/azure-mobile-services/
  [Google Cloud Messaging コンポーネント]: http://components.xamarin.com/view/GCMClient/
  [Google apis]: http://go.microsoft.com/fwlink/p/?LinkId=268303
  [0]: ./media/partner-xamarin-notification-hubs-android-get-started/mobile-services-google-developers.png
  [2]: ./media/partner-xamarin-notification-hubs-android-get-started/mobile-services-google-create-server.png
  [3]: ./media/partner-xamarin-notification-hubs-android-get-started/mobile-services-google-create-server2.png
  [4]: ./media/partner-xamarin-notification-hubs-android-get-started/mobile-services-google-create-server3.png
  [Azure 管理ポータル]: https://manage.windowsazure.com/
  [5]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-from-portal.png
  [6]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-from-portal2.png
  [7]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-select-from-portal.png
  [8]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-select-from-portal2.png
  [9]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-configure-android.png
  [10]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-connection-strings.png
  [11]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app1.png
  [12]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app2.png
  [13]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app3.png
  [Xamarin.NotificationHub Github ページ]: https://github.com/SaschaDittmann/Xamarin.NotificationHub
  [14]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app7.png
  [15]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app8.png
  [REST インターフェイス]: http://msdn.microsoft.com/ja-jp/library/windowsazure/dn223264.aspx
  [16]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-console-app.png
  [WindowsAzure.ServiceBus NuGet パッケージ]: http://nuget.org/packages/WindowsAzure.ServiceBus/
  [17]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-android-toast.png
  [モバイル サービスの使用]: /ja-jp/develop/mobile/tutorials/get-started-xamarin-android/#create-new-service
  [18]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-scheduler1.png
  [19]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-scheduler2.png
  [通知ハブを使用したユーザーへのプッシュ通知]: /ja-jp/manage/services/notification-hubs/notify-users-aspnet
  [通知ハブを使用したニュース速報の送信]: /ja-jp/manage/services/notification-hubs/breaking-news-dotnet
  [Windows Azure 通知ハブの概要]: http://msdn.microsoft.com/ja-jp/library/jj927170.aspx
