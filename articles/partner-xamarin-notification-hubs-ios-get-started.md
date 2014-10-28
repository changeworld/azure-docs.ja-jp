<properties linkid="develop-notificationhubs-tutorials-get-started-xamarin-ios" urlDisplayName="Get Started" pageTitle="Get Started with Notification Hubs for Xamarin iOS apps" metaKeywords="" description="Learn how to use Azure Notification Hubs to send push notifications to a Xamarin iOS application." metaCanonical="" disqusComments="0" umbracoNaviHide="1" services="mobile-services,notification-hubs" documentationCenter="" title="Get started with Notification Hubs" authors="donnam" manager="dwrede" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-ios" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="donnam"></tags>

# 通知ハブの使用

<div class="dev-center-tutorial-selector sublanding"><a href="/ja-jp/documentation/articles/notification-hubs-windows-store-dotnet-get-started/" title="Windows ユニバーサル">Windows ユニバーサル</a><a href="/ja-jp/documentation/articles/notification-hubs-windows-phone-get-started/" title="Windows Phone">Windows Phone</a><a href="/ja-jp/documentation/articles/notification-hubs-ios-get-started/" title="iOS">iOS</a><a href="/ja-jp/documentation/articles/notification-hubs-android-get-started/" title="Android">Android</a><a href="/ja-jp/documentation/articles/notification-hubs-kindle-get-started/" title="Kindle">Kindle</a><a href="/ja-jp/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/" title="Xamarin.iOS" class="current">Xamarin.iOS</a><a href="/ja-jp/documentation/articles/partner-xamarin-notification-hubs-android-get-started/" title="Xamarin.Android">Xamarin.Android</a></div>

このトピックでは、Azure Notification Hubs を使用して iOS アプリケーションにプッシュ通知を送信する方法について説明します。
このチュートリアルでは、Apple Push Notification サービス (APNs) を使用してプッシュ通知を受信する空の Xamarin.iOS アプリケーションを作成します。完了すると、通知ハブを使用して、アプリケーションを実行するすべてのデバイスにプッシュ通知をブロードキャストできるようになります。完成したコードは、[NotificationHubs アプリケーション][NotificationHubs アプリケーション] サンプルで参照できます。

このチュートリアルでは、プッシュ通知を有効にするための、次の基本的な手順について説明します。

1.  [証明書の署名要求を生成する][証明書の署名要求を生成する]
2.  [アプリケーションを登録し、プッシュ通知を有効にする][アプリケーションを登録し、プッシュ通知を有効にする]
3.  [アプリケーションのプロビジョニング プロファイルを作成する][アプリケーションのプロビジョニング プロファイルを作成する]
4.  [通知ハブを構成する][通知ハブを構成する]
5.  [通知ハブにアプリケーションを接続する][通知ハブにアプリケーションを接続する]
6.  [バックエンドから通知を送信する][バックエンドから通知を送信する]

このチュートリアルでは、通知ハブを使用した簡単なブロードキャスト シナリオのデモンストレーションを行います。このチュートリアルの前提条件は次のとおりです。

-   [XCode 5.0][XCode 5.0]
-   iOS 5.0 (またはこれ以降のバージョン) に対応したデバイス
-   iOS Developer Program メンバーシップ
-   [Xamarin.iOS][1]
-   [Azure モバイル サービス コンポーネント][Azure モバイル サービス コンポーネント]

   <div class="dev-callout"><b>注</b><br /> <p>プッシュ通知の構成要件により、プッシュ通知のデプロイとテストは、シミュレーターではなく iOS 対応デバイス (iPhone または iPad) で行う必要があります。</p><br /> </div>

</p>
このチュートリアルを完了することは、Xamarin.iOS アプリの他のすべての通知ハブ チュートリアルの前提条件です。

<div class="dev-callout"><strong>注</strong> <p>このチュートリアルを完了するには、アクティブな Azure アカウントが必要です。アカウントがない場合は、無料の試用アカウントを数分で作成することができます。詳細については、<a href="http://www.windowsazure.com/ja-jp/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fja-jp%2Fmanage%2Fservices%2Fnotification-hubs%2Fgetting-started-xamarin-ios%2F" target="_blank">Azure の無料評価版サイト</a>を参照してください。</p></div>

Apple Push Notification Service (APNS) では、証明書を使用してモバイル サービスを認証します。次の手順に従って、必要な証明書を作成し、モバイル サービスにアップロードしてください。公式な APNS 機能のドキュメントについては、「[Apple Push Notification Service][Apple Push Notification Service]」を参照してください。

## <a name="certificates"></a><span class="short-header">CSR ファイルの生成</span>証明書の署名要求ファイルを生成する

まず、Apple が署名証明書を生成するために使用する、証明書署名要求 (CSR: Certificate Signing Request) ファイルを生成する必要があります。

1.  Utilities フォルダーから [Keychain Access] ツールを実行します。

2.  **[Keychain Access]** をクリックし、**[Certificate Assistant]** を展開して、**[Request a Certificate from a Certificate Authority]** をクリックします。

    ![][]

3.  **[ユーザーの電子メール アドレス]** を選択し、**[共通名]** と **[CA 電子メール アドレス]** の値を入力します。次に、**[ディスクに保存]** が選択されていることを確認し、**[続行]** をクリックします。

    ![][2]

4.  **[Save As]** に証明書署名要求 (CSR) ファイルの名前を入力し、**[Where]** で保存場所を選択して **[Save]** をクリックします。

    ![][3]

    指定選択した場所に CSR ファイルが保存されます。既定の場所はデスクトップです。このファイル用に選択した場所を忘れないでください。

次に、アプリケーションを Apple に登録し、プッシュ通知を有効にし、このエクスポートした CSR をアップロードしてプッシュ通知を作成します。

## <a name="register"></a><span class="short-header">アプリの登録</span>アプリをプッシュ通知に登録する

モバイル サービスから iOS アプリケーションにプッシュ通知を送信できるようにするには、アプリケーションを Apple に登録し、プッシュ通知にも登録する必要があります。

1.  アプリケーションをまだ登録していない場合は、Apple デベロッパー センターで [iOS プロビジョニング ポータル][iOS プロビジョニング ポータル]に移動し、Apple ID でログインして、**[Identifiers]** をクリックし、**[App IDs]** をクリックします。最後に、**+** 記号をクリックして新しいアプリケーションを登録します。

    ![][4]

2.  **[説明]** にアプリの名前を入力し、**[バンドル ID]** に値を入力します。[アプリケーション サービス] セクションの [プッシュ通知] オプションをオンにして、**[続行]** をクリックします。

    ![][5]

    ![][6]

    ![][7]

    これで、アプリケーション ID が生成され、情報のサブミットが求められます。**[Submit]** をクリックします。

    ![][8]

    **[Submit]** をクリックすると、以下のような **[Registration complete]** 画面が表示されます。**[Done]** をクリックします。

    ![][9]

    <div class="dev-callout"><b>注</b>
<p><strong>[バンドル ID]</strong> に <b>MobileServices.Quickstart</b> 以外の値を指定する場合は、Xcode プロジェクトでバンドル ID の値も更新する必要があります。</p>
 </div>

3.  作成したアプリケーション ID を見つけ、その行をクリックします。

    ![][10]

    アプリケーション ID をクリックすると、アプリケーションとアプリケーション ID の詳細が表示されます。

    ![][11]

    ![][12]

4.  **[編集]** をクリックし、画面の下部までスクロールして、**[開発プッシュ SSL 証明書]** セクションの **[証明書の作成]** ボタンをクリックします。

    ![][13]

    これで、[Add iOS Certificate] アシスタントが表示されます。

    ![][14]

    <div class="dev-callout"><b>注</b>
<p>このチュートリアルでは開発証明書を使用します。運用証明書の場合も同じ処理を行います。証明書をモバイル サービスにアップロードするときと同じ証明書タイプを設定してください。</p>
</div>

5.  **[ファイルの選択]** をクリックして、最初の作業で CSR ファイルを保存した場所に移動し、**[生成]** をクリックします。

    ![][15]

6.  ポータルで証明書が作成されたら **[ダウンロード]** をクリックし、**[完了]** をクリックします。

    ![][16]

    ![][17]

    これによって、署名証明書がダウンロードされ、コンピューターの **Downloads** フォルダーに保存されます。

    ![][18]

    <div class="dev-callout"><b>注</b>
<p>既定では、ダウンロードした開発証明書ファイルの名前は <strong>aps_development.cer</strong> になっています。</p>
</div>

7.  ダウンロードしたプッシュ証明書 **aps\_development.cer** をダブルクリックします。

    下図のように、新しい証明書が Keychain にインストールされます。

    ![][19]

    > [WACOM.NOTE]
    > 証明書の名前は異なることがありますが、名前の前に **Apple Development iOS Push Notification Services:** が付けられます。

    後で、この証明書を使用して .p12 ファイルを生成し、それを通知ハブにアップロードして APNS によるプッシュ通知を有効にします。

## <a name="profile"></a><span class="short-header">アプリのプロビジョニング</span>アプリのプロビジョニング プロファイルを作成する

1.  [iOS プロビジョニング ポータル][iOS プロビジョニング ポータル]に戻って **[Provisioning Profiles]** を選択し、**[All]** を選択してから **+** ボタンをクリックして、新しいプロファイルを作成します。これで、**Add iOS Provisioning Profile** ウィザードが表示されます。

    ![][20]

2.  **[開発]** でプロビジョニング プロファイルの種類として **[iOS アプリ開発]** を選択し、**[続行]** をクリックします。

    ![][21]

3.  次に、**[アプリ ID]** の一覧で Mobile Services のクイック スタート アプリのアプリ ID を選択し、**[続行]** をクリックします。

    ![][22]

4.  **[証明書の選択]** 画面で、前に作成した証明書を選択し、**[続行]** をクリックします。

    ![][23]

5.  次に、テストに使用する**デバイス**を選択し、**[続行]** をクリックします。

    ![][24]

6.  最後に、**[プロファイル名]** でプロファイルの名前を選択し、**[生成]** をクリックして、**[完了]** をクリックします。

    ![][25]

    ![][26]

    新しいプロビジョニング プロファイルが作成されます。

7.  Xcode で、Organizer を開いて [デバイス] ビューを選択し、左ウィンドウの **[ライブラリ]** セクションにある **[プロビジョニング プロファイル]** を選択して、前の手順で作成したプロビジョニング プロファイルをインポートします。

8.  左側で、デバイスを選択し、プロビジョニング ファイルをもう一度インポートします。

9.  Keychain Access で、新しい証明書を右クリックし、**[エクスポート]** をクリックします。証明書の名前を入力して **[.p12]** 形式を選択し、**[保存]** をクリックします。

    ![][27]

    エクスポートした証明書のファイル名と場所を書き留めます。

これで、Xcode プロジェクトはコード署名のために新しいプロファイルを使用するようになります。次に、証明書を通知ハブにアップロードする必要があります。

## <a name="configure-hub"></a><span class="short-header">通知ハブを構成する</span>通知ハブを構成する

1.  [Azure 管理ポータル][Azure 管理ポータル]にログオンし、画面の下部にある **[+新規]** をクリックします。

2.  **[アプリケーション サービス]**、**[Service Bus]**、**[通知ハブ]**、**[簡易作成]** の順にクリックします。

    ![][28]

3.  通知ハブの名前を入力して、目的のリージョンを選択し、**[新しい通知ハブを作成する]** をクリックします。

    ![][29]

4.  前の手順で作成した名前空間 (通常は "***通知ハブ名*-ns**") をクリックし、上部にある **[構成]** タブをクリックします。

    ![][30]

5.  上部にある **[通知ハブ]** タブをクリックし、前の手順で作成した通知ハブをクリックします。

    ![][31]

6.  上部にある **[構成]** タブを選択し、Apple 通知設定の **[アップロード]** をクリックします。次に、前にエクスポートした **.p12** 証明書と、証明書のパスワードを選択します。**[運用]** プッシュ サービス (ストアからアプリケーションを購入したユーザーにプッシュ通知を送信する場合) と **[サンドボックス]** (開発時) プッシュ サービスのどちらを使用するかを選択してください。

    ![][32]

7.  上部にある **[ダッシュボード]** タブをクリックし、**[接続情報]** をクリックします。2 つの接続文字列をメモします。

    ![][33]

これで、通知ハブが APN と連動するように構成されました。接続文字列にアプリケーションを登録し、通知を送信できます。

## <a name="connecting-app"></a><span class="short-header">アプリケーションを接続する</span>通知ハブにアプリケーションを接続する

### WindowsAzure.Messaging ライブラリをダウンロードする

このアセンブリは、Azure Notification Hubs に登録するための簡単な方法を提供します。ここに示す手順に従ってダウンロードしてください。[サンプル ダウンロード][NotificationHubs アプリケーション]にもあります。

1.  GitHub から [WindowsAzure.Messaging][WindowsAzure.Messaging] のソースをダウンロードします。

2.  プロジェクトをコンパイルし、出力アセンブリ **WindowsAzure.Messaging.dll** を見つけます。これは、以下で Xamarin.iOS アプリケーションを設定するときに必要です。

### 新しいプロジェクトを作成する

1.  Xamarin Studio で、新しい iOS プロジェクトを作成し、**[単一枠ビュー アプリケーション]** テンプレートを選択します。

    ![][34]

2.  まず、Azure Mobile Services コンポーネントへの参照を追加します。[Solution] ビューで、プロジェクトの **[Components]** フォルダーを右クリックし、**[Get More Components]** を選択します。**Azure モバイル サービス** コンポーネントを検索し、そのコンポーネントをプロジェクトに追加します。

3.  ここで、前にダウンロードした WindowsAzure.Messaging ライブラリへの参照を追加します。**[参照]** フォルダーを右クリックし、**[参照の編集]** を選択します。**[.Net アセンブリ]** タブで、**WindowsAzure.Messaging.dll** を見つけます。

4.  **AppDelegate.cs** で、次の using ステートメントを追加します。

        using Microsoft.WindowsAzure.MobileServices;

        using WindowsAzure.Messaging;

5.  **SBNotificationHub** のインスタンスを宣言します。

        private SBNotificationHub Hub { get; set; }

6.  次の変数を使用して **Constants.cs** クラスを作成します。

        // Azure app specific connection string and hub path
        public const string ConnectionString = "<Azure connection string>";
        public const string NotificationHubPath = "<Azure hub path>";

7.  **AppDelegate.cs** で、**FinishedLaunching()** を更新して次の内容に合わせます。

        public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
        {
            UIRemoteNotificationType notificationTypes = UIRemoteNotificationType.Alert | 
                UIRemoteNotificationType.Badge | UIRemoteNotificationType.Sound;
            UIApplication.SharedApplication.RegisterForRemoteNotificationTypes(notificationTypes); 

            return true;
        }

8.  **AppDelegate.cs** で **RegisteredForRemoteNotifications()** メソッドをオーバーライドします。

        public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
        {
            Hub = new SBNotificationHub(Constants.ConnectionString, Constants.NotificationHubPath);

            Hub.UnregisterAllAsync (deviceToken, (error) => {
                if (error != null) 
                {
                    Console.WriteLine("Error calling Unregister: {0}", error.ToString());
                    return;
                } 

                NSSet tags = null; // create tags if you want
                Hub.RegisterNativeAsync(deviceToken, tags, (errorCallback) => {
                    if (errorCallback != null)
                        Console.WriteLine("RegisterNativeAsync error: " + errorCallback.ToString());
                });
            });
        }

9.  **AppDelegate.cs** で **ReceivedRemoteNotification()** メソッドをオーバーライドします。

        public override void ReceivedRemoteNotification(UIApplication application, NSDictionary userInfo)
        {
            ProcessNotification(userInfo, false);
        }

10. **AppDelegate.cs** で次の **ProcessNotification()** メソッドを作成します。

        void ProcessNotification(NSDictionary options, bool fromFinishedLaunching)
        {
            // Check to see if the dictionary has the aps key.  This is the notification payload you would have sent
            if (null != options && options.ContainsKey(new NSString("aps")))
            {
                //Get the aps dictionary
                NSDictionary aps = options.ObjectForKey(new NSString("aps")) as NSDictionary;

                string alert = string.Empty;

                //Extract the alert text
                // NOTE: If you're using the simple alert by just specifying 
                // "  aps:{alert:"alert msg here"}  " this will work fine.
                // But if you're using a complex alert with Localization keys, etc., 
                // your "alert" object from the aps dictionary will be another NSDictionary. 
                // Basically the json gets dumped right into a NSDictionary, 
                // so keep that in mind.
                if (aps.ContainsKey(new NSString("alert")))
                    alert = (aps [new NSString("alert")] as NSString).ToString();

                //If this came from the ReceivedRemoteNotification while the app was running,
                // we of course need to manually process things like the sound, badge, and alert.
                if (!fromFinishedLaunching)
                {
                    //Manually show an alert
                    if (!string.IsNullOrEmpty(alert))
                    {
                        UIAlertView avAlert = new UIAlertView("Notification", alert, null, "OK", null);
                        avAlert.Show();
                    }
                }           
            }
        }

    <div class="dev-callout"><b>注</b>
<p>ネットワーク接続がないなどの状況に対処するため、**FailedToRegisterForRemoteNotifications()** をオーバーライドすることを選択できます。</p>
</div>

11. デバイスでアプリケーションを実行します。

## <a name="send"></a><span class="short-header">通知を送信する</span>バックエンドから通知を送信する

通知は、[REST インターフェイス][REST インターフェイス]を使用するどのバックエンドからも通知ハブを使用して送信できます。このチュートリアルでは、.NET コンソール アプリケーションと、ノード スクリプトを使用するモバイル サービスで通知を送信します。

.NET アプリケーションを使用して通知を送信するには

1.  Visual C# の新しいコンソール アプリケーションを作成します。

    ![][35]

2.  [WindowsAzure.ServiceBus NuGet パッケージ][WindowsAzure.ServiceBus NuGet パッケージ]を使用して Azure Service Bus SDK への参照を追加します。Visual Studio のメイン メニューで、**[ツール]**、**[ライブラリ パッケージ マネージャー]**、**[パッケージ マネージャー コンソール]** の順に選択します。次に、コンソール ウィンドウで次のように入力します。

        Install-Package WindowsAzure.ServiceBus and press Enter.

3.  Program.cs ファイルを開き、次の using ステートメントを追加します。

        using Microsoft.ServiceBus.Notifications;

4.  `Program` クラスで、次のメソッドを追加します。

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            var alert = "{\"aps\":{\"alert\":\"Hello from .NET!\"}}";
            await hub.SendAppleNativeNotificationAsync(alert);
        }

5.  次に、`Main` メソッドに次の行を追加します。

         SendNotificationAsync();
         Console.ReadLine();

6.  F5 キーを押してアプリケーションを実行します。デバイスでアラートを受信します。Wi-fi を使用している場合、接続が機能していることを確認します。

Apple の「[Local and Push Notification Programming Guide (ローカルおよびプッシュ通知プログラミング ガイド)][Local and Push Notification Programming Guide (ローカルおよびプッシュ通知プログラミング ガイド)]」に、使用できるすべてのペイロードが記載されています。

モバイル サービスを使用して通知を送信するには、「[モバイル サービスの使用][モバイル サービスの使用]」に従った後、次の手順を実行します。

1.  [Azure 管理ポータル][Azure 管理ポータル]にログオンし、[モバイル サービス] をクリックします。

2.  上部にある **[スケジューラ]** タブを選択します。

    ![][36]

3.  スケジュールされた新しいジョブを作成して名前を挿入し、**[要求時]** をクリックします。

    ![][37]

4.  ジョブが作成されたら、ジョブ名をクリックします。上部のバーにある **[スクリプト]** タブをクリックします。

5.  スケジューラ関数内に次のスクリプトを挿入します。必ず、プレースホルダーを、通知ハブの名前と既に取得してある *DefaultFullSharedAccessSignature* の接続文字列に置き換えてください。**[保存]** をクリックします。

        var azure = require('azure');
        var notificationHubService = azure.createNotificationHubService('<Hubname>', '<SAS Full access >');
        notificationHubService.apns.send(
            null,
            {"aps":
                {
                "alert": "Hello from Mobile Services!"
                }
            },
            function (error)
            {
                if (!error) {
                    console.warn("Notification successful");
                }
            }
        );

6.  下部のバーにある **[一度だけ実行する]** をクリックします。デバイスでアラートを受信します。

## <a name="next-steps"> </a>次のステップ

この簡単な例では、すべての iOS デバイスに通知をブロードキャストします。特定のユーザーをターゲットとするには、「[通知ハブを使用したユーザーへのプッシュ通知][通知ハブを使用したユーザーへのプッシュ通知]」を参照してください。一方、対象グループごとにユーザーを区分する場合は、「[通知ハブを使用したニュース速報の送信][通知ハブを使用したニュース速報の送信]」を参照してください。通知ハブの使用方法の詳細については、「[Windows Azure 通知ハブの概要][Windows Azure 通知ハブの概要]」と「[方法: Windows Azure 通知ハブ (iOS アプリ)][方法: Windows Azure 通知ハブ (iOS アプリ)]」を参照してください。

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [Windows ユニバーサル]: /ja-jp/documentation/articles/notification-hubs-windows-store-dotnet-get-started/ "Windows ユニバーサル"
  [Windows Phone]: /ja-jp/documentation/articles/notification-hubs-windows-phone-get-started/ "Windows Phone"
  [iOS]: /ja-jp/documentation/articles/notification-hubs-ios-get-started/ "iOS"
  [Android]: /ja-jp/documentation/articles/notification-hubs-android-get-started/ "Android"
  [Kindle]: /ja-jp/documentation/articles/notification-hubs-kindle-get-started/ "Kindle"
  [Xamarin.iOS]: /ja-jp/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/ "Xamarin.iOS"
  [Xamarin.Android]: /ja-jp/documentation/articles/partner-xamarin-notification-hubs-android-get-started/ "Xamarin.Android"
  [NotificationHubs アプリケーション]: http://go.microsoft.com/fwlink/p/?LinkId=331329
  [証明書の署名要求を生成する]: #certificates
  [アプリケーションを登録し、プッシュ通知を有効にする]: #register
  [アプリケーションのプロビジョニング プロファイルを作成する]: #profile
  [通知ハブを構成する]: #configure-hub
  [通知ハブにアプリケーションを接続する]: #connecting-app
  [バックエンドから通知を送信する]: #send
  [XCode 5.0]: https://go.microsoft.com/fwLink/p/?LinkID=266532
  [1]: http://xamarin.com/download
  [Azure モバイル サービス コンポーネント]: http://components.xamarin.com/view/azure-mobile-services/
  [Azure の無料評価版サイト]: http://www.windowsazure.com/ja-jp/pricing/free-trial/?WT.mc_id=A643EE910&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fja-jp%2Fmanage%2Fservices%2Fnotification-hubs%2Fgetting-started-xamarin-ios%2F
  [Apple Push Notification Service]: http://go.microsoft.com/fwlink/p/?LinkId=272584
  []: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-step5.png
  [2]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-step6.png
  [3]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-step7.png
  [iOS プロビジョニング ポータル]: http://go.microsoft.com/fwlink/p/?LinkId=272456
  [4]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-05.png
  [5]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-06.png
  [6]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-07.png
  [7]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-08.png
  [8]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-09.png
  [9]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-10.png
  [10]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-11.png
  [11]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-12.png
  [12]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-13.png
  [13]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-14.png
  [14]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-15.png
  [15]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-16.png
  [16]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-18.png
  [17]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-19.png
  [18]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-step9.png
  [19]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-step10.png
  [20]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-20.png
  [21]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-21.png
  [22]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-22.png
  [23]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-23.png
  [24]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-24.png
  [25]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-25.png
  [26]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-26.png
  [27]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-step18.png
  [Azure 管理ポータル]: https://manage.windowsazure.com/
  [28]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-from-portal.png
  [29]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-from-portal2.png
  [30]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-select-from-portal.png
  [31]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-select-from-portal2.png
  [32]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-configure-ios.png
  [33]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-connection-strings.png
  [WindowsAzure.Messaging]: https://github.com/infosupport/WindowsAzure.Messaging.iOS
  [34]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-ios-app.png
  [REST インターフェイス]: http://msdn.microsoft.com/ja-jp/library/windowsazure/dn223264.aspx
  [35]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-console-app.png
  [WindowsAzure.ServiceBus NuGet パッケージ]: http://nuget.org/packages/WindowsAzure.ServiceBus/
  [Local and Push Notification Programming Guide (ローカルおよびプッシュ通知プログラミング ガイド)]: http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW1
  [モバイル サービスの使用]: /ja-jp/develop/mobile/tutorials/get-started-xamarin-ios
  [36]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-scheduler1.png
  [37]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-scheduler2.png
  [通知ハブを使用したユーザーへのプッシュ通知]: /ja-jp/manage/services/notification-hubs/notify-users-aspnet
  [通知ハブを使用したニュース速報の送信]: /ja-jp/manage/services/notification-hubs/breaking-news-dotnet
  [Windows Azure 通知ハブの概要]: http://msdn.microsoft.com/ja-jp/library/jj927170.aspx
  [方法: Windows Azure 通知ハブ (iOS アプリ)]: http://msdn.microsoft.com/ja-jp/library/jj927168.aspx
