<properties
	pageTitle="Notification Hubs の使用 (Xamarin.iOS アプリケーション) | Microsoft Azure"
	description="このチュートリアルでは、Azure Notification Hubs を使用して Xamarin.iOS アプリケーションにプッシュ通知を送信する方法について学習します。"
	services="notification-hubs"
	documentationCenter="xamarin"
	authors="ysxu"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="notification-hubs"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin-ios"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="07/28/2015"
	ms.author="yuaxu"/>

# Notification Hubs の使用

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

##概要

このチュートリアルでは、Azure Notification Hubs を使用して iOS アプリケーションにプッシュ通知を送信する方法について説明します。Apple プッシュ通知サービス (APNs) を使用してプッシュ通知を受信する空の Xamarin.iOS アプリケーションを作成します。完了すると、通知ハブを使用して、アプリケーションを実行するすべてのデバイスにプッシュ通知をブロードキャストできるようになります。完成したコードは、[NotificationHubs アプリケーション][GitHub] サンプルで参照できます。

このチュートリアルでは、Notification Hubs を使用した簡単なブロードキャスト シナリオのデモンストレーションを行います。

##前提条件

このチュートリアルには、次のものが必要です。

+ [XCode 6.0][Install Xcode]
+ iOS 7.0 (またはこれ以降のバージョン) に対応したデバイス
+ iOS Developer Program メンバーシップ
+ [Xamarin.iOS]
+ [Azure Mobile Services コンポーネント]

   >[AZURE.NOTE]プッシュ通知の構成要件により、プッシュ通知のデプロイとテストは、シミュレーターではなく iOS 対応デバイス (iPhone または iPad) で行う必要があります。

このチュートリアルを完了することは、Xamarin.iOS アプリの他のすべての Notification Hubs チュートリアルの前提条件です。

> [AZURE.IMPORTANT]このチュートリアルを完了するには、アクティブな Azure アカウントが必要です。アカウントがない場合は、無料試用版のアカウントを数分で作成することができます。詳細については、[Azure の無料試用版サイト](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fja-JP%2Fdocumentation%2Farticles%2Fpartner-xamarin-notification-hubs-ios-get-started)を参照してください。

Apple Push Notification Service (APNS) では、証明書を使用してモバイル サービスを認証します。次の手順に従って、必要な証明書を作成し、モバイル サービスにアップロードしてください。公式な APNs 機能のドキュメントについては、「[Apple Push Notification Service]」を参照してください。


##<a name="certificates"></a>証明書の署名要求ファイルを生成する

まず、Apple が署名証明書を生成するために使用する、証明書署名要求 (CSR: Certificate Signing Request) ファイルを生成する必要があります。

1. Utilities フォルダーから [Keychain Access] ツールを実行します。

2. **[Keychain Access]** をクリックし、**[証明書アシスタント]** を展開して、**[証明書機関に証明書を要求する]** をクリックします。

  	![][5]

3. **[ユーザーの電子メール アドレス]** を選択し、**[共通名]** と **[CA 電子メール アドレス]** の値を入力します。次に、**[ディスクに保存]** が選択されていることを確認し、**[続行]** をクリックします。

  	![][6]

4. **[名前を付けて保存]** に CSR ファイルの名前を入力し、**[場所]** で保存場所を選択して **[保存]** をクリックします。

  	![][7]

  	これにより、選択した場所に CSR ファイルが保存されます。既定の場所は、デスクトップ上にあります。このファイル用に選択した場所を忘れないでください。

次に、アプリケーションを Apple に登録し、プッシュ通知を有効にし、このエクスポートした CSR をアップロードしてプッシュ通知を作成します。

##<a name="register"></a>アプリをプッシュ通知に登録する

Mobile Services から iOS アプリケーションにプッシュ通知を送信できるようにするには、アプリケーションを Apple に登録し、プッシュ通知にも登録する必要があります。

1. アプリをまだ登録していない場合は、Apple Developer センターで <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">iOS Provisioning Portal</a> に移動し、Apple ID でサインインして、**[ID]** をクリックし、**[アプリ ID]** をクリックします。最後に、**+** 記号をクリックして新しいアプリを登録します。

   	![][105]

2. **[説明]** にアプリの名前を入力し、**[バンドル ID]** に値を入力します。**[App Services]** セクションの **[プッシュ通知]** オプションをオンにして、**[続行]** をクリックします。

   	![][106]

   	![][107]

   	![][108]


	これで、アプリケーション ID が生成され、情報のサブミットが求められます。**[Submit]** をクリックします。

   	![][109]

	**[Submit]** をクリックすると、以下のような **[Registration complete]** 画面が表示されます。**[Done]** をクリックします。

   	![][110]

	> [AZURE.NOTE]**[バンドル ID]** に **MobileServices.Quickstart** 以外の値を指定する場合は、Xcode プロジェクトでバンドル ID の値も更新する必要があります。

3. 作成したアプリケーション ID を見つけ、その行をクリックします。

   	![][111]

	アプリケーション ID をクリックすると、アプリケーションとアプリケーション ID の詳細が表示されます。

   	![][112]

   	![][113]

4. **[編集]** をクリックし、画面の下部までスクロールして、**[開発プッシュ SSL 証明書]** セクションの **[証明書の作成]** ボタンをクリックします。

   	![][114]

	これで、[iOS 証明書の追加] アシスタントが表示されます。

   	![][115]

	> [AZURE.NOTE]このチュートリアルでは開発証明書を使用します。運用証明書を登録する場合も同じ処理を行います。証明書を Mobile Services にアップロードするときと同じ証明書タイプを設定してください。

5. **[ファイルの選択]** をクリックして、最初の作業で CSR ファイルを保存した場所に移動し、**[生成]** をクリックします。

  	![][116]

6. ポータルで証明書が作成されたら **[ダウンロード]** をクリックし、**[完了]** をクリックします。

  	![][118]

  	![][119]

   	これによって、署名証明書がダウンロードされ、コンピューターの **Downloads** フォルダーに保存されます。

  	![][9]

    > [AZURE.NOTE]既定では、ダウンロードした開発証明書ファイルの名前は **aps\_development.cer** になっています。

7. ダウンロードしたプッシュ証明書 **aps\_development.cer** をダブルクリックします。

	下図のように、新しい証明書が Keychain にインストールされます。

   	![][10]

	> [AZURE.NOTE]証明書の名前は異なることがありますが、名前の前に <strong>Apple Development iOS Push Notification Services:</strong> が付けられます。

	後で、この証明書を使用して .p12 ファイルを生成し、それを通知ハブにアップロードして APNs によるプッシュ通知を有効にします。

##<a name="profile"></a>アプリケーションのプロビジョニング プロファイルを作成する

1. <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">iOS Provisioning Portal</a> に戻って **[Provisioning Profiles]** を選択し、**[All]** を選択してから **+** ボタンをクリックして、新しいプロファイルを作成します。これで、**[iOS プロビジョニング プロファイルの追加]** ウィザードが表示されます。

   	![][120]

2. **[開発]** でプロビジョニング プロファイルの種類として **[iOS アプリ開発]** を選択し、**[続行]** をクリックします。

   	![][121]

3. 次に、**[アプリ ID]** の一覧で Mobile Services のクイック スタート アプリのアプリ ID を選択し、**[続行]** をクリックします。

   	![][122]

4. **[証明書の選択]** 画面で、前に作成した証明書を選択し、**[続行]** をクリックします。

   	![][123]

5. テストに使用するデバイスを選択し、**[続行]** をクリックします。

   	![][124]

6. 最後に、**[プロファイル名]** でプロファイルの名前を選択し、**[生成]** をクリックして、**[完了]** をクリックします。

   	![][125]

   	![][126]

  	新しいプロビジョニング プロファイルが作成されます。

7. Xcode で、Organizer を開いて [デバイス] ビューを選択し、左ウィンドウの **[ライブラリ]** セクションにある **[プロビジョニング プロファイル]** を選択して、前の手順で作成したプロビジョニング プロファイルをインポートします。

8. 左側で、デバイスを選択し、プロビジョニング ファイルをもう一度インポートします。

9. Keychain Access で、新しい証明書を右クリックし、**[エクスポート]** をクリックします。証明書の名前を入力して **[.p12]** 形式を選択し、**[保存]** をクリックします。

   	![][18]

  	エクスポートした証明書のファイル名と場所を書き留めます。

これで、Xcode プロジェクトはコード署名のために新しいプロファイルを使用するようになります。次に、証明書を通知ハブにアップロードする必要があります。

##<a name="configure-hub"></a>通知ハブを構成する

1. [Azure ポータル]にサインインし、画面の下部にある **[+新規]** をクリックします。

2. **[App Services]**、**[Service Bus]**、**[Notification Hub]** の順にクリックし、**[簡易作成]** をクリックします。

   	![][27]

3. 通知ハブの名前を入力して、目的のリージョンを選択し、**[新しい通知ハブを作成する]** をクリックします。

   	![][28]

4. 前の手順で作成した名前空間 (通常は ***通知ハブ名*-ns**) をクリックし、上部にある **[構成]** タブをクリックします。

   	![][29]

5. 上部にある **[Notification Hubs]** タブをクリックし、前の手順で作成した Notification Hubs をクリックします。

   	![][210]

6. 上部にある **[構成]** タブを選択し、Apple 通知設定の **[アップロード]** をクリックします。次に、前にエクスポートした **.p12** 証明書と、証明書のパスワードを選択します。**[運用]** プッシュ サービス (ストアからアプリケーションを購入したユーザーにプッシュ通知を送信する場合) と **[サンドボックス]** (開発時) プッシュ サービスのどちらを使用するかを選択してください。

   	![][211]

7. 上部にある **[ダッシュボード]** タブをクリックし、**[接続情報]** をクリックします。2 つの接続文字列をメモします。

   	![][212]

これで、通知ハブが APNs と連動するように構成されました。接続文字列にアプリケーションを登録し、通知を送信できます。

##<a name="connecting-app"></a>Notification Hub にアプリケーションを接続する

### 新しいプロジェクトを作成する

1. Xamarin Studio で、新しい iOS プロジェクトを作成し、**[統合 API]**、**[単一ビュー アプリケーション]** テンプレートの順に選択します。

   	![][31]

2. Azure Messaging コンポーネントへの参照を追加します。[ソリューション] ビューで、プロジェクトの **[コンポーネント]** フォルダーを右クリックし、**[他のコンポーネントを取得する]** を選択します。**Azure Messaging** コンポーネントを検索し、そのコンポーネントをプロジェクトに追加します。

3. **AppDelegate.cs** で、次の using ステートメントを追加します。

    using WindowsAzure.Messaging;

4. **SBNotificationHub** のインスタンスを宣言します。

		private SBNotificationHub Hub { get; set; }

5. 次の変数を使用して **Constants.cs** クラスを作成します。

        // Azure app-specific connection string and hub path
        public const string ConnectionString = "<Azure connection string>";
        public const string NotificationHubPath = "<Azure hub path>";


6. **AppDelegate.cs** で、**FinishedLaunching()** を更新して次の内容に合わせます。

        public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
        {
            UIRemoteNotificationType notificationTypes = UIRemoteNotificationType.Alert |
                UIRemoteNotificationType.Badge | UIRemoteNotificationType.Sound;
            UIApplication.SharedApplication.RegisterForRemoteNotificationTypes(notificationTypes);

            return true;
        }

7. **AppDelegate.cs** で **RegisteredForRemoteNotifications()** メソッドをオーバーライドします。

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

8. **AppDelegate.cs** で **ReceivedRemoteNotification()** メソッドをオーバーライドします。

        public override void ReceivedRemoteNotification(UIApplication application, NSDictionary userInfo)
        {
            ProcessNotification(userInfo, false);
        }

9. **AppDelegate.cs** で次の **ProcessNotification()** メソッドを作成します。

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
                // "  aps:{alert:"alert msg here"}  ", this will work fine.
                // But if you're using a complex alert with Localization keys, etc.,
                // your "alert" object from the aps dictionary will be another NSDictionary.
                // Basically the JSON gets dumped right into a NSDictionary,
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

    > [AZURE.NOTE]ネットワーク接続がないなどの状況に対処するために、**FailedToRegisterForRemoteNotifications()** をオーバーライドすることを選択できます。


10. デバイスでアプリケーションを実行します。

##<a name="send"></a>バックエンドから通知を送信する

通知は、<a href="http://msdn.microsoft.com/library/windowsazure/dn223264.aspx">REST インターフェイス</a>を介してどのバックエンドからも Notification Hubs を使用して送信できます。このチュートリアルでは、.NET コンソール アプリケーションによる方法と、ノード スクリプトを介したモバイル サービスによる方法で、通知を送信します。

.NET アプリケーションを使用して通知を送信するには

1. Visual C# の新しいコンソール アプリケーションを作成します。

   	![][213]

2. <a href="http://nuget.org/packages/WindowsAzure.ServiceBus/">WindowsAzure.ServiceBus NuGet パッケージ</a>を使用して、Azure Service Bus SDK に参照を追加します。Visual Studio のメイン メニューで、**[ツール]**、**[ライブラリ パッケージ マネージャー]**、**[パッケージ マネージャー コンソール]** の順に選択します。次に、コンソール ウィンドウで次のように入力し、Enter キーを押します。

        Install-Package WindowsAzure.ServiceBus

2. Program.cs ファイルを開き、次の using ステートメントを追加します。

        using Microsoft.ServiceBus.Notifications;

3. `Program` クラスで、次のメソッドを追加します。

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            var alert = "{"aps":{"alert":"Hello from .NET!"}}";
            await hub.SendAppleNativeNotificationAsync(alert);
        }

4. `Main` メソッド内に次の行を追加します。

         SendNotificationAsync();
		 Console.ReadLine();

5. F5 キーを押してアプリケーションを実行します。デバイスでアラートを受信します。Wi-Fi を使用する場合は、接続が機能していることを確認します。

Apple の「[Local and Push Notification Programming Guide (ローカルおよびプッシュ通知プログラミング ガイド)]」に、使用できるすべてのペイロードが記載されています。

モバイル サービスを使用して通知を送信するには、「[Mobile Services の使用]」に従った後、次の手順を実行します。

1. [Azure ポータル]にサインインし、モバイル サービスを選択します。

2. 上部にある **[Scheduler]** タブを選択します。

   	![][215]

3. スケジュールされた新しいジョブを作成して名前を挿入し、**[要求時]** をクリックします。

   	![][216]

4. ジョブが作成されたら、ジョブ名をクリックします。上部のバーにある **[スクリプト]** タブをクリックします。

5. スケジューラ関数内に次のスクリプトを挿入します。必ず、プレースホルダーを、通知ハブの名前と既に取得してある *DefaultFullSharedAccessSignature* の接続文字列に置き換えてください。**[保存]** をクリックします。

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


6. 下部のバーにある **[一度だけ実行する]** をクリックします。デバイスでアラートを受信します。

## <a name="next-steps"> </a>次のステップ

この簡単な例では、すべての iOS デバイスに通知をブロードキャストします。特定のユーザーをターゲットとするには、「[Notification Hubs を使用したユーザーへのプッシュ通知]」のチュートリアルを参照してください。対象グループごとにユーザーを区分する場合は、「[Notification Hubs を使用したニュース速報の送信]」を参照してください。Notification Hubs の使用方法の詳細については、「[Microsoft Azure Notification Hubs の概要]」と「[方法: Microsoft Azure Notification Hubs (iOS アプリ)]」を参照してください。

<!-- Anchors. -->
[Generate the certificate signing request]: #certificates
[Register your app and enable push notifications]: #register
[Create a provisioning profile for the app]: #profile
[Configure your Notification Hub]: #configure-hub
[Connecting your app to the Notification Hub]: #connecting-app
[Send notifications from your back-end]: #send
[Next Steps]: #next-steps

<!-- Images. -->
[5]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-step5.png
[6]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-step6.png
[7]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-step7.png

[9]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-step9.png
[10]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-step10.png
[18]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-step18.png
[105]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-05.png
[106]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-06.png
[107]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-07.png
[108]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-08.png
[109]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-09.png
[110]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-10.png
[111]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-11.png
[112]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-12.png
[113]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-13.png
[114]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-14.png
[115]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-15.png
[116]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-16.png

[118]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-18.png
[119]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-19.png

[120]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-20.png
[121]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-21.png
[122]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-22.png
[123]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-23.png
[124]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-24.png
[125]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-25.png
[126]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-26.png

[27]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-from-portal.png
[28]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-from-portal2.png
[29]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-select-from-portal.png
[210]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-select-from-portal2.png
[211]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-configure-ios.png
[212]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-connection-strings.png


[213]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-console-app.png



[215]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-scheduler1.png
[216]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-scheduler2.png


[31]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-ios-app.png




<!-- URLs. -->
[Mobile Services iOS SDK]: http://go.microsoft.com/fwLink/?LinkID=266533
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[Mobile Services の使用]: /develop/mobile/tutorials/get-started-xamarin-ios
[Azure ポータル]: https://manage.windowsazure.com/
[Microsoft Azure Notification Hubs の概要]: http://msdn.microsoft.com/library/jj927170.aspx
[方法: Microsoft Azure Notification Hubs (iOS アプリ)]: http://msdn.microsoft.com/library/jj927168.aspx
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456

[Notification Hubs を使用したユーザーへのプッシュ通知]: /manage/services/notification-hubs/notify-users-aspnet
[Notification Hubs を使用したニュース速報の送信]: /manage/services/notification-hubs/breaking-news-dotnet

[Local and Push Notification Programming Guide (ローカルおよびプッシュ通知プログラミング ガイド)]: http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW1
[Apple Push Notification Service]: http://go.microsoft.com/fwlink/p/?LinkId=272584

[Azure Mobile Services コンポーネント]: http://components.xamarin.com/view/azure-mobile-services/
[GitHub]: http://go.microsoft.com/fwlink/p/?LinkId=331329
[Xamarin.iOS]: http://xamarin.com/download
[WindowsAzure.Messaging]: https://github.com/infosupport/WindowsAzure.Messaging.iOS

<!---HONumber=August15_HO9-->