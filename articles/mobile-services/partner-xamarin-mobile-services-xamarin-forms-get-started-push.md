 <properties
	pageTitle="Mobile Services アプリケーション (Xamarin.Forms) へのプッシュ通知の追加 - Mobile Services"
	description="Azure Mobile Services の Xamarin.Forms アプリでプッシュ通知を使用する方法について説明します。"
	documentationCenter="xamarin"
	authors="wesmc7777"
	manager="dwrede"
	services="mobile-services"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.tgt_pltfrm="mobile-xamarin"
	ms.workload="mobile"
	ms.date="10/05/2015"
	ms.author="wesmc"/>

# Xamarin.Forms アプリにプッシュ通知を追加する
[AZURE.INCLUDE [mobile-services-selector-get-started-push](../../includes/mobile-services-selector-get-started-push.md)]

##概要

このチュートリアルでは、Azure Mobile Services を使用して、iOS、Android、および Windows Xamarin.Forms ソリューションの Windows Phone アプリにプッシュ通知を送信する方法を紹介します。最初にモバイル サービスを作成します。次に、スタート サンプルをダウンロードし、適切なプッシュ通知サービスに登録してから、これらのサービスから通知を受信できるようにコードをソリューションに追加します。

このチュートリアルを完了すると、いずれかのアプリケーションでユーザーがタスクを追加するたびに、モバイル サービスからプッシュ通知が送信されるようになります。完全なサンプルは、ここ [Completed Xamarin.Forms Azure Push Notification Sample (Xamarin.Forms の Azure プッシュ通知の完全なサンプル)] で参照できます。

このチュートリアルには、次のものが必要です。

+ iOS 8 デバイス (iOS シミュレーターでプッシュ通知をテストすることはできません)
+ iOS Developer Program メンバーシップ
+ [Xamarin.iOS Studio]
+ [Azure Mobile Services コンポーネント]
+ アクティブな Google アカウント
+ [Google Cloud Messaging のクライアント コンポーネント]。このコンポーネントは、チュートリアル中に追加します。

このトピックの内容:

1. [新しいモバイル サービスを作成する](#create-service)
2. [スタート サンプルをダウンロードおよび構成する](#download-starter-sample)
4. [Xamarin.Forms.iOS アプリにプッシュ通知を追加する](#iOS)
5. [Xamarin.Forms.Android アプリにプッシュ通知を追加する](#Android)
6. [Xamarin.Forms.Windows アプリにプッシュ通知を追加する](#Windows)
7. [すべてのアプリにプッシュ通知を送信する Azure テーブルの挿入スクリプトを更新する](#all-apps)

## <a name="create-service"> </a>新しいモバイル サービスを作成する

[AZURE.INCLUDE [mobile-services-create-new-service-data](../../includes/mobile-services-create-new-service-data.md)]

アプリケーション データを新しいモバイル サービスに格納できるようにするには、最初に新しいテーブルを作成する必要があります。

1. 管理ポータルで、**[モバイル サービス]** をクリックし、先ほど作成したモバイル サービスをクリックします。

2. **[データ]** タブをクリックし、**[作成]** をクリックします。

    ![][123]

   	**[新しいテーブルの作成]** ダイアログ ボックスが表示されます。

3. **[テーブル名]** に「_TodoItem_」と入力し、チェック ボタンをクリックします。

    ![][124]

  	これにより、既定のアクセス許可が設定された新しいストレージ テーブル **TodoItem** が作成されます。既定のアクセス許可とは、アプリケーションの任意のユーザーがテーブル内のデータにアクセスし、変更を加えることができることを表します。

    > [AZURE.NOTE]Mobile Services のクイック スタートでも同じテーブル名が使用されています。ただし、それぞれのテーブルは、特定のモバイル サービスに固有のスキーマ内で作成されています。これには、複数のモバイル サービスで同じデータベースを使用した場合にデータの競合を回避する目的があります。

4. 新しい **TodoItem** テーブルをクリックし、データ行がないことを確認します。

5. **[列]** タブをクリックし、自動的に作成される **id** 列のみがあることを確認します。

  	これは、Mobile Services のテーブルの最小要件です。

    > [AZURE.NOTE]モバイル サービスで動的スキーマを有効にすると、挿入操作または更新操作によって JSON オブジェクトがモバイル サービスに送信されるときに新しい列が自動的に作成されます。

これで、新しいモバイル サービスをアプリケーションのデータ ストレージとして使用する準備が整いました。

## <a name="download-starter-sample"></a>スタート サンプルをダウンロードおよび構成する
既存のサンプルにプッシュ通知を追加します。
  
1. サンプル [Xamarin.Forms Azure Push Notification Starter Sample (Xamarin.Forms の Azure プッシュ通知スタート サンプル)] をダウンロードします。

2. 管理ポータルで、**[Mobile Services]** をクリックし、モバイル サービスをクリックします。**[ダッシュボード]** タブをクリックし、**[サイトの URL]** の値をメモしておきます。**[キーの管理]** をクリックし、**[アプリケーション キー]** の値をメモしておきます。これらの値は、アプリケーション コードからモバイル サービスにアクセスするときに必要になります。

3. ソリューションの **ToDoAzure(Portable)** プロジェクトで、 **Constants.cs** ファイルを開き、`ApplicationURL` と `ApplicationKey` を、サイト URL および前の手順で取得したアプリケーション キーで置き換えます。

## <a name="iOS"></a>Xamarin.Forms.iOS アプリにプッシュ通知を追加する

Apple Push Notification サービス (APNS) を使用して、iOS アプリにプッシュ通知を追加します。アクティブな Google アカウントおよび [Google Cloud Messaging クライアント コンポーネント]が必要です。

>[AZURE.IMPORTANT]Apple Push Notification サービス (APNS) の要件に合わせて、プッシュ通知を、エミュレーターではなく iOS 対応デバイス (iPhone または iPad) にデプロイおよびテストする必要があります。

APNS では、証明書を使用してモバイル サービスを認証します。次の手順に従って、必要な証明書を作成し、Mobile Service にアップロードしてください。公式な APNS 機能のドキュメントについては、「[Apple Push Notification Service]」を参照してください。

### <a name="certificates"></a>証明書の署名要求ファイルを生成する

まず、Apple が署名証明書を生成するために使用する、証明書署名要求 (CSR: Certificate Signing Request) ファイルを生成する必要があります。

1. ユーティリティから、**キーチェーン アクセス** ツールを実行します。

2. **[Keychain Access]** をクリックし、**[Certificate Assistant]** を展開して、**[Request a Certificate from a Certificate Authority]** をクリックします。

    ![][5]

3. **[ユーザーのメールアドレス]** にメール アドレスを入力し、**[通称]** の値を入力します。**[ディスクに保存]** が選択されていることを確認し、**[続ける]** をクリックします。

    ![][6]

4. **[Save As]** に証明書署名要求 (CSR) ファイルの名前を入力し、**[Where]** で保存場所を選択して **[Save]** をクリックします。

    ![][7]

    選択した場所を忘れないでください。

次に、アプリケーションを Apple に登録し、プッシュ通知を有効にし、このエクスポートした CSR をアップロードしてプッシュ通知を作成します。

### <a name="register"></a>アプリケーションをプッシュ通知に登録する

モバイル サービスから iOS アプリケーションにプッシュ通知を送信可能にするには、アプリケーションを Apple に登録し、プッシュ通知にも登録する必要があります。

1. アプリケーションをまだ登録していない場合は、Apple デベロッパー センターで <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">iOS プロビジョニング ポータル</a>に移動し、Apple ID でログインして、**[Identifiers]** をクリックします。次に **[App IDs]** をクリックし、最後に、**+** 記号をクリックして、アプリケーションのアプリケーション ID を作成します。

    ![][102]

2. **[Description]** にアプリケーションの名前を入力し、一意のバンドル識別子を **[Bundle Identifier]** に入力して、それを覚えておきます。[App Services] セクションの [Push Notifications] オプションをオンにして、**[Continue]** をクリックします。この例では ID として **MobileServices.Quickstart** を使用していますが、アプリケーション ID はすべてのユーザー間で一意である必要があるので、この同じ ID を再利用することはできません。そのため、自分のフル ネームやイニシャルをアプリケーション名の後に付加することをお勧めします。

    ![][103]

    これで、アプリケーション ID が生成され、情報の**サブミット**が求められます。**[Submit]** をクリックします。

    ![][104]

    **[Submit]** をクリックすると、以下のような **[Registration complete]** 画面が表示されます。**[Done]** をクリックします。

    ![][105]

3. 作成したアプリケーション ID を見つけ、その行をクリックします。

    ![][106]

    アプリケーション ID をクリックすると、アプリケーションとアプリケーション ID の詳細が表示されます。**[Settings]** をクリックします。

    ![][107]

4. 画面の下部までスクロールし、**[Development Push SSL Certificate]** セクションの **[Create Certificate]** ボタンをクリックします。

    ![][108]

    これで、[Add iOS Certificate] アシスタントが表示されます。

    メモ: このチュートリアルでは開発証明書を使用します。運用証明書の場合も同じ処理を行います。証明書をモバイル サービスにアップロードするときと同じ証明書タイプを設定してください。

5. **[Choose File]** をクリックして、前に CSR ファイルを保存した場所に移動してから、**[Generate]** をクリックします。

    ![][110]

6. ポータルで証明書が作成されたら **[Download]** ボタンをクリックし、**[Done]** をクリックします。

    ![][111]

    これによって、署名証明書がダウンロードされ、コンピューターの Downloads フォルダーに保存されます。

    ![][9]

    メモ: 既定では、ダウンロードした開発証明書ファイルの名前は <strong>aps\_development.cer</strong> になっています。

7. ダウンロードしたプッシュ証明書 **aps\_development.cer** をダブルクリックします。

    下図のように、新しい証明書が Keychain にインストールされます。

    ![][10]

    メモ: 証明書の名前は異なることがありますが、名前の前に <strong>Apple Development iOS Push Notification Services:</strong> が付きます。

後で、この証明書を使用して .p12 ファイルを生成し、それを Mobile Service にアップロードして APNS による認証を有効にします。

### <a name="profile"></a>アプリケーションのプロビジョニング プロファイルを作成する

1. <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">iOS Provisioning Portal</a> に戻って **[Provisioning Profiles]** を選択し、**[All]** を選択してから **+** ボタンをクリックして、新しいプロファイルを作成します。これで、**Add iOS Provisiong Profile** ウィザードが起動します。

    ![][112]

2. **[Development]** でプロビジョニング プロファイルの種類として **[iOS App Development]** を選択し、**[Continue]** をクリックします。

3. 次に、**[アプリ ID]** の一覧で Mobile Services のクイック スタート アプリのアプリ ID を選択し、**[続行]** をクリックします。

    ![][113]

4. **[Select certificates]** 画面で、前に作成した証明書を選択し、**[Continue]** をクリックします。

    ![][114]

5. 次に、テストに使用する**デバイス**を選択し、**[続行]** をクリックします。

    ![][115]

6. 最後に、**[Profile Name]** でプロファイルの名前を選択し、**[Generate]** をクリックしてから、**[Done]** をクリックします。

    ![][116]

    新しいプロビジョニング プロファイルが作成されます。

    ![][117]

7. Xcode で、Organizer を開いて [Devices] ビューを選択し、左ウィンドウの **[Library]** セクションにある **[Provisioning Profiles]** を選択して、中央ウィンドウの一番下にある **[Refresh]** をクリックします。

### <a name="configure-mobileServices"></a>プッシュ要求を送信するように Mobile Services を構成する

アプリケーションを APNS に登録し、プロジェクトを構成した後で、モバイル サービスを APNS と統合するように構成する必要があります。

1. [Keychain Access] で、新しい証明書を右クリックし、**[Export]** をクリックします。ファイルに名前を付けて **[.p12]** 形式を選択し、**[Save]** をクリックします。

    ![][28]

    エクスポートした証明書のファイル名と場所を書き留めます。

2. [Azure の管理ポータル]にログオンし、**[モバイル サービス]** をクリックして、アプリケーションをクリックします。

    ![][18]

3. **[プッシュ]** タブをクリックし、**[Apple プッシュ通知の設定]** で **[アップロード]** をクリックします。

    ![][19]

    [証明書のアップロード] ダイアログ ボックスが表示されます。

4. **[ファイル]** をクリックし、エクスポートした証明書 .p12 ファイルを選択します。**[パスワード]** を入力し、正しい **[モード]** が選択されていることを確認して **[保存]** をクリックします。

    ![][20]

APNS と連携するようにモバイル サービスが構成されました。

### <a name="configure-app"></a>Xamarin.iOS アプリケーションを構成する

1. Xamarin.Studio または Visual Studio で、**Info.plist** を開き、前に作成した ID で **Bundle Identifier** を更新します。

    ![][121]

2. **[Background Modes]** まで下へスクロールし、**[Enable Background Modes]** と **[Remote notifications]** の各チェック ボックスをオンにします。

    ![][122]

3. ソリューション パネルでプロジェクトをダブルクリックし、**[Project Options]** を開きます。

4.  **[Build]** で **[iOS Bundle Signing]** を選択し、対応する **ID** とこのプロジェクトに対して設定した**プロビジョニング プロファイル**を選択します。

    ![][120]

    これで、Xamarin プロジェクトはコード署名のために新しいプロファイルを使用するようになります。公式の Xamarin デバイス プロビジョニングのドキュメントについては、[Xamarin デバイス プロビジョニング]に関するページを参照してください。

### <a name="add-push"></a>アプリケーションにプッシュ通知を追加する

1. Xamarin.Studio または Visual Studio で **ToDoAzure.iOS** プロジェクトを展開し、**AppDelegate** クラスを開いてから **FinishedLaunching** イベントを次のコードで置換します。

        public override bool FinishedLaunching(UIApplication app, NSDictionary options)
        {
             // registers for push for iOS8
            var settings = UIUserNotificationSettings.GetSettingsForTypes(
                UIUserNotificationType.Alert
                | UIUserNotificationType.Badge
                | UIUserNotificationType.Sound,
                new NSSet());

            global::Xamarin.Forms.Forms.Init();
            instance = this;
            CurrentPlatform.Init();
            
            todoItemManager = new ToDoItemManager();
            App.SetTodoItemManager(todoItemManager);


            UIApplication.SharedApplication.RegisterUserNotificationSettings(settings);
            UIApplication.SharedApplication.RegisterForRemoteNotifications();
            
            LoadApplication(new App());
            return base.FinishedLaunching(app, options);
        }

6. **AppDelegate** で、**RegisteredForRemoteNotifications** イベントをオーバーライドします。

        public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
        {
            // Modify device token
            string _deviceToken = deviceToken.Description;
            _deviceToken = _deviceToken.Trim('<', '>').Replace(" ", "");

            // Get Mobile Services client
            MobileServiceClient client = todoItemManager.GetClient;

            // Register for push with Mobile Services
            IEnumerable<string> tag = new List<string>() { "uniqueTag" };
            
            const string template = "{"aps":{"alert":"$(message)"}}";

            var expiryDate = DateTime.Now.AddDays(90).ToString
                (System.Globalization.CultureInfo.CreateSpecificCulture("ja-jp"));

            var push = client.GetPush();

            push.RegisterTemplateAsync(_deviceToken, template, expiryDate, "myTemplate", tag)
        }

7. **AppDelegate** で、**ReceivedRemoteNotification** イベントをオーバーライドします。

        public override void ReceivedRemoteNotification(UIApplication application, NSDictionary userInfo)
        {
            NSObject inAppMessage;

            bool success = userInfo.TryGetValue(new NSString("inAppMessage"), out inAppMessage);

            if (success)
            {
                var alert = new UIAlertView("Got push notification", inAppMessage.ToString(), null, "OK", null);
                alert.Show();
            }
        }

これで、アプリケーションがプッシュ通知をサポートするように更新されました。

### <a name="update-scripts"></a>管理ポータルで登録されている挿入スクリプトを更新する

1. 管理ポータルで、**[データ]** タブをクリックし、**TodoItem** テーブルをクリックします。

    ![][21]

2. **[todoitem]** で、**[スクリプト]** タブをクリックし、**[挿入]** をクリックします。

    ![][22]

    **TodoItem** テーブルで挿入が発生したときに呼び出される関数が表示されます。

3. insert 関数を次のコードに置き換え、**[保存]** をクリックします。

          function insert(item, user, request) {
          // Execute the request and send notifications.
             request.execute({
             success: function() {                      
              // Create a template-based payload.
              var payload = '{ "message" : "New item added: ' + item.text + '" }';            

              // Write the default response and send a notification
              // to all platforms.            
              push.send(null, payload, {               
                  success: function(pushResponse){
                  console.log("Sent push:", pushResponse);
                  // Send the default response.
                  request.respond();
                  },              
                  error: function (pushResponse) {
                      console.log("Error Sending push:", pushResponse);
                       // Send the an error response.
                      request.respond(500, { error: pushResponse });
                      }           
               });                 
              }
           });   
          }

    これで、新しい insert スクリプトが登録されます。このスクリプトは、挿入要求で指定されたデバイスにプッシュ通知 (挿入されたテキスト) を送信します。

   >[AZURE.NOTE]このスクリプトでは、トースト通知を受け取るためにアプリケーションを閉じる時間を与えるために通知の送信を遅らせています。

### <a name="test"></a>アプリケーションでプッシュ通知をテストする

1. **[Run]** を押して、プロジェクトをビルドし、iOS 対応のデバイスでアプリケーションを開始します。**[OK]** をクリックして、プッシュ通知を受け入れます。

   >[AZURE.NOTE]アプリケーションからのプッシュ通知を明示的に受け入れる必要があります。これが必要であるのは、初めてアプリケーションを実行するときだけです。

2. アプリケーションで [**追加**] ボタンをクリックし、タスクのタイトルを追加してから [**保存**] ボタンをクリックします。 

3. 通知が受信されたことを確認し、**[OK]** をクリックして通知を破棄します。


これで、このチュートリアルは終了です。

## <a name="Android"></a>Xamarin.Forms.Android アプリにプッシュ通知を追加する

Google Cloud Messaging (GCM) サービスを使用して、Android アプリにプッシュ通知を追加します。アクティブな Google アカウントおよび [Google Cloud Messaging クライアント コンポーネント]が必要です。

###<a id="register"></a>Google Cloud Messaging を有効にする

[AZURE.INCLUDE [mobile-services-enable-Google-cloud-messaging](../../includes/mobile-services-enable-google-cloud-messaging.md)]

###<a id="configure"></a>プッシュ要求を送信するように Mobile Services を構成する

[AZURE.INCLUDE [mobile-services-android-configure-push](../../includes/mobile-services-android-configure-push.md)]

###<a id="update-scripts"></a>通知を送信するように登録済み挿入スクリプトを更新する

>[AZURE.NOTE]次の手順では、Azure 管理ポータルの TodoItem テーブルに対する挿入操作に登録されているスクリプトを更新する方法を示します。このモバイル サービス スクリプトは、Visual Studio のサーバー エクスプローラーの Azure ノードで、直接アクセスして編集することもできます。

管理ポータルで、**[データ]** タブをクリックし、**TodoItem** テーブルをクリックします。

   ![][21]

2. **[todoitem]** で、**[スクリプト]** タブをクリックし、**[挿入]** をクリックします。

   ![][22]

    This displays the function that is invoked when an insert occurs in the **TodoItem** table.

3. insert 関数を次のコードに置き換え、**[保存]** をクリックします。

          function insert(item, user, request) {
          // Execute the request and send notifications.
             request.execute({
             success: function() {                      
              // Create a template-based payload.
              var payload = '{ "message" : "New item added: ' + item.text + '" }';            

              // Write the default response and send a notification
              // to all platforms.            
              push.send(null, payload, {               
                  success: function(pushResponse){
                  console.log("Sent push:", pushResponse);
                  // Send the default response.
                  request.respond();
                  },              
                  error: function (pushResponse) {
                      console.log("Error Sending push:", pushResponse);
                       // Send the an error response.
                      request.respond(500, { error: pushResponse });
                      }           
               });                 
              }
           });   
          }


    これで、新しい insert スクリプトが登録されます。このスクリプトは、挿入要求で指定されたデバイスにプッシュ通知 (挿入されたテキスト) を送信します。

   >[AZURE.NOTE]このスクリプトでは、トースト通知を受け取るためにアプリケーションを閉じる時間を与えるために通知の送信を遅らせています。


###<a id="configure-app"></a>プッシュ通知の既存のプロジェクトを構成する

1. [ソリューション] ビューで、Xamarin.Android アプリケーションの **Components** フォルダーを展開し、Azure Mobile Services パッケージがインストールされていることを確認します。 

2. **Components** フォルダーを右クリックし、[**その他のコンポーネントを取得...**] をクリックし、[**Google Cloud Messaging Client**] コンポーネントを検索し、それをプロジェクトに追加します。

1. MainActivity.cs プロジェクト ファイルを開き、次の using ステートメントをクラスに追加します。

		using Gcm.Client;


4.	**MainActivity** クラスで、**LoadApplication** メソッドへの呼び指しの後で、次のコードを **OnCreate** メソッドに追加します。
            
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
                CreateAndShowDialog(new Exception("There was an error creating the Mobile Service. Verify the URL"), "Error");
            }
            catch (Exception e)
            {
                CreateAndShowDialog(e, "Error");
            }

これで **MainActivity** は、プッシュ通知を追加するための準備が整いました。

###<a id="add-push"></a>アプリケーションにプッシュ通知コードを追加する

5. ToDoAzure.Droid プロジェクト内で、`GcmService` という名前の新しいクラスを作成します。

5. 次の using ステートメントを **GcmService** に追加します。

		using Gcm.Client;
		using Microsoft.WindowsAzure.MobileServices;

6. **using** ステートメントと **namespace** 宣言の間に次のアクセス許可要求を追加します。

		[assembly: Permission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
        [assembly: UsesPermission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
        [assembly: UsesPermission(Name = "com.google.android.c2dm.permission.RECEIVE")]

        //GET_ACCOUNTS is only needed for android versions 4.0.3 and below
        [assembly: UsesPermission(Name = "android.permission.GET_ACCOUNTS")]
        [assembly: UsesPermission(Name = "android.permission.INTERNET")]
        [assembly: UsesPermission(Name = "android.permission.WAKE_LOCK")]

7. **GcmService.cs** プロジェクト ファイルで、次のクラスを追加します。
 
        [BroadcastReceiver(Permission = Gcm.Client.Constants.PERMISSION_GCM_INTENTS)]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_MESSAGE }, Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_REGISTRATION_CALLBACK }, Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_LIBRARY_RETRY }, Categories = new string[] { "@PACKAGE_NAME@" })]

        public class PushHandlerBroadcastReceiver : GcmBroadcastReceiverBase<GcmService>
        {
        
            public static string[] SENDER_IDS = new string[] { "<PROJECT_NUMBER>" };

        }

	上記のコードの _`<PROJECT_NUMBER>`_ を、Google の開発者ポータルでアプリケーションをプロビジョニングしたときに Google によって割り当てられたプロジェクト番号に置き換える必要があります。

8. GcmService.cs プロジェクト ファイルで、**GcmService** クラスを定義する次のコードを追加します。
 
         [Service]
         public class GcmService : GcmServiceBase
         {
             public static string RegistrationID { get; private set; }

             public GcmService()
                 : base(PushHandlerBroadcastReceiver.SENDER_IDS){}
         }


	このクラスは **GcmServiceBase** から派生したものであり、このクラスに **Service** 属性を適用する必要があるということに注意してください。

	>[AZURE.NOTE]**GcmServiceBase** クラスでは、**OnRegistered()** メソッド、O**nUnRegistered()** メソッド、**OnMessage()** メソッド、および **OnError()** メソッドを実装しています。**GcmService** クラスに含まれるこれらのメソッドをオーバーライドする必要があります。

5. **OnRegistered** イベント ハンドラーをオーバーライドする次のコードを **GcmService** クラスに追加します。

        protected override void OnRegistered(Context context, string registrationId)
        {
            Log.Verbose(PushHandlerBroadcastReceiver.TAG, "GCM Registered: " + registrationId);
            RegistrationID = registrationId;

            createNotification("GcmService Registered...", "The device has been Registered, Tap to View!");

            MobileServiceClient client =  MainActivity.DefaultService.todoItemManager.GetClient;
            
            var push = client.GetPush();

            MainActivity.DefaultService.RunOnUiThread(() => Register(push, null));

        }
        public async void Register(Microsoft.WindowsAzure.MobileServices.Push push, IEnumerable<string> tags)
        {
            try
            {
                const string template = "{"data":{"message":"$(message)"}}";

                await push.RegisterTemplateAsync(RegistrationID, template, "mytemplate", tags);
            }
            catch (Exception ex)
            {
                System.Diagnostics.Debug.WriteLine(ex.Message);
                Debugger.Break();
            }
        }

	このメソッドでは、返された GCM 登録 ID を使用して、プッシュ通知のために Azure に登録します。

10. **GcmService** の **OnMessage** メソッドを次のコードでオーバーライドします。

        protected override void OnMessage(Context context, Intent intent)
        {
            Log.Info(PushHandlerBroadcastReceiver.TAG, "GCM Message Received!");

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

12. **OnUnRegistered()** および **OnError()** (これらはプロジェクトをコンパイルするために必要) 用の次のメソッド オーバーライド コードを追加します。

        protected override void OnError(Context context, string errorId)
        {
              Log.Error(PushHandlerBroadcastReceiver.TAG, "GCM Error: " + errorId);
        }

###<a id="test"></a>アプリケーションでプッシュ通知をテストする

Android フォンを USB ケーブルで直接接続するか、エミュレーターで仮想デバイスを使用する方法により、アプリケーションをテストできます。

このアプリケーションをエミュレーターで実行する場合は、Google API をサポートしている Android Virtual Device (AVD) を使用してください。

> [AZURE.IMPORTANT]プッシュ通知を受信するには、Android Virtual Device で Google アカウントを設定する必要があります (エミュレーターで、**[Settings]** に移動して **[Add Account]** をクリックします)。さらに、エミュレーターがインターネットに接続されていることを確認します。

1. **[ツール]** で **[Android エミュレーター マネージャーを開く]** をクリックし、デバイスを選択してから **[編集]** をクリックします。
    
    ![][125]

2. **[Target]** で **[Google APIs]** を選択し、**[OK]** をクリックします。
    
    ![][126]

3. 上部のツール バーで、**[実行]** をクリックし、アプリを選択します。これによりエミュレーターが起動し、アプリケーションが実行されます。

  アプリにより、GCM から *registrationId* が取得され、Notification Hub に登録されます。

1. アプリケーションで、新しいタスクを追加します。

2. 画面の上端から下へスワイプし、通知を表示するためのデバイスの通知センターを開きます。

	![][127]

## <a name="Windows"></a>Xamarin.Forms.Windows アプリにプッシュ通知を追加する

このチュートリアルでは、Azure Mobile Services を使用して、Xamarin.Forms ソリューションに含まれる Windows Phone Silverlight アプリにプッシュ通知を送信する方法を紹介します。

###<a id="update-app"></a>アプリケーションを更新して通知に登録する

アプリケーションがプッシュ通知を受信するには、通知チャネルを登録する必要があります。

1. Visual Studio で App.xaml.cs ファイルを開き、次の `using` ステートメントを追加します。

        using Microsoft.Phone.Notification;

3. 次のコードを App.xaml.cs に追加します。
	
        public static HttpNotificationChannel CurrentChannel { get; private set; }

        private void AcquirePushChannel()
        {
            CurrentChannel = HttpNotificationChannel.Find("MyPushChannel");

            if (CurrentChannel == null)
            {
                CurrentChannel = new HttpNotificationChannel("MyPushChannel");
                CurrentChannel.Open();
                CurrentChannel.BindToShellToast();
            }

            CurrentChannel.ChannelUriUpdated +=
                new EventHandler<NotificationChannelUriEventArgs>(async (o, args) =>
                {

                   // Register for notifications using the new channel
                    const string template =
                    "<?xml version="1.0" encoding="utf-8"?><wp:Notification " +
                    "xmlns:wp="WPNotification"><wp:Toast><wp:Text1>$(message)</wp:Text1></wp:Toast></wp:Notification>";

                    await client.GetPush()
                        .RegisterTemplateAsync(CurrentChannel.ChannelUri.ToString(), template, "mytemplate");
                });
        }

    このコードは Windows Phone 8.x "Silverlight" で使用される Microsoft Push Notification Service (MPNS) からアプリの ChannelURI を取得し、それをプッシュ通知用に登録します。

	>[AZURE.NOTE]このチュートリアルでは、モバイル サービスにより、トースト通知がデバイスに送信されます。タイル通知を送信する場合は、チャネルの **BindToShellTile** メソッドを呼び出す必要があります。

4. App.xaml.cs 内で、**Application\_Launching** イベント ハンドラーの先頭に、次に示す新しい **AcquirePushChannel** メソッドへの呼び出しを追加します。

        AcquirePushChannel();

	これにより、ページが読み込まれるたびに登録が要求されるようになります。アプリケーションでは、この登録が常に最新の状態となるように、定期的な登録のみ行うことができます。

5. **F5** キーを押してアプリケーションを実行します。登録キーを示すポップアップ ダイアログが表示されます。
  
6.	ソリューション エクスプローラーで、[**プロパティ**] を展開して WMAppManifest.xml ファイルを開き、[**機能**] タブをクリックして、**ID\_\_\_CAP\_\_\_PUSH\_NOTIFICATION** 機能がオンであることを確認します。

   	![VS で通知を有効にする](./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-app-enable-push-wp8.png)

   	これにより、アプリケーションでトースト通知の使用が有効になります。

###<a id="update-scripts"></a>サーバー スクリプトを更新してプッシュ通知を送信する

最後に、通知を送信するためには、TodoItem テーブルの挿入操作に登録されているスクリプトを更新する必要があります。

1. 管理ポータルで、**[データ]** タブをクリックし、**TodoItem** テーブルをクリックします。

    ![][21]

2. **[todoitem]** で、**[スクリプト]** タブをクリックし、**[挿入]** をクリックします。

    ![][22]

    **TodoItem** テーブルで挿入が発生したときに呼び出される関数が表示されます。

3. insert 関数を次のコードに置き換え、**[保存]** をクリックします。
          
          function insert(item, user, request) {
          // Execute the request and send notifications.
             request.execute({
             success: function() {                      
              // Create a template-based payload.
              var payload = '{ "message" : "New item added: ' + item.text + '" }';            

              // Write the default response and send a notification
              // to all platforms.            
              push.send(null, payload, {               
                  success: function(pushResponse){
                  console.log("Sent push:", pushResponse);
                  // Send the default response.
                  request.respond();
                  },              
                  error: function (pushResponse) {
                      console.log("Error Sending push:", pushResponse);
                       // Send the an error response.
                      request.respond(500, { error: pushResponse });
                      }           
               });                 
              }
           });   
          }


    これで、新しい insert スクリプトが登録されます。このスクリプトは、挿入要求で指定されたデバイスにプッシュ通知 (挿入されたテキスト) を送信します。

3. **[プッシュ]** タブをクリックし、**[非認証プッシュ通知を有効にします]** をオンにして、**[保存]** をクリックします。

	これにより、非認証モードで MPNS に接続するモバイル サービスがプッシュ通知を送信できるようになります。

	>[AZURE.NOTE]このチュートリアルでは、非認証モードで MPNS を使用します。このモードでは、MPNS はデバイス チャネルに送信できる通知の数を制限します。この制限を削除するには、**[アップロード]** をクリックして証明書を生成してアップロードし、その証明書を選択する必要があります。証明書を生成する方法の詳細については、「Setting up an authenticated web service to send push notifications for Windows Phone (認証済み Web サービスを、Windows Phone のプッシュ通知を送信するように設定する)」を参照してください。

###<a id="test"></a>アプリケーションでプッシュ通知をテストする

1. Visual Studio で、F5 キーを押してアプリケーションを実行します。

    >[AZURE.NOTE]Windows Phone エミュレーターでテストする場合、401 認証エラー "RegistrationAuthorizationException" が発生する場合があります。これは、Windows Phone エミュレーターのホスト PC の時計との同期方法によって `RegisterNativeAsync()` 呼び出し中に発生します。セキュリティ トークンで拒否される場合があります。これを解決するには、テストする前にエミュレーターの時計を手動で設定します。

5. アプリケーションで **Hello push** というタイトルの新しいタスクを作成して、すぐに [開始] ボタンまたは [戻る] ボタンを押してアプリケーションを閉じます。

  	これにより、追加された項目を保存するための挿入要求がモバイル サービスに送信されます。デバイスは **hello push** というトースト通知を受信します。

	![受信したトースト通知](./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-quickstart-push5-wp8.png)

	>[AZURE.NOTE]アプリケーションが開いている場合は通知を受信しません。アプリケーション起動中にトースト通知を受信するには、[ShellToastNotificationReceived](http://msdn.microsoft.com/library/windowsphone/develop/microsoft.phone.notification.httpnotificationchannel.shelltoastnotificationreceived(v=vs.105).aspx) イベントを処理する必要があります。
   
<!-- Anchors. -->
[Generate the certificate signing request]: #certificates
[Register your app and enable push notifications]: #register
[Create a provisioning profile for the app]: #profile
[Configure Mobile Services]: #configure-mobileServices
[Configure the Xamarin.iOS App]: #configure-app
[Update scripts to send push notifications]: #update-scripts
[Add push notifications to the app]: #add-push
[Insert data to receive notifications]: #test

<!-- Images. -->

[5]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-step5.png
[6]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-step6.png
[7]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-step7.png

[9]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-step9.png
[10]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-step10.png

[17]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-step17.png
[18]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-selection.png
[19]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-push-tab-ios.png
[20]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-push-tab-ios-upload.png
[21]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-portal-data-tables.png
[22]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-insert-script-push2.png
[23]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-quickstart-push1-ios.png
[24]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-quickstart-push2-ios.png
[25]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-quickstart-push3-ios.png
[26]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-quickstart-push4-ios.png
[28]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-step18.png

[101]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-01.png
[102]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-02.png
[103]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-03.png
[104]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-04.png
[105]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-05.png
[106]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-06.png
[107]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-07.png
[108]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-08.png

[110]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-10.png
[111]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-11.png
[112]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-12.png
[113]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-13.png
[114]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-14.png
[115]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-15.png
[116]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-16.png
[117]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-17.png

[120]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-20.png
[121]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-21.png
[122]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-22.png
[123]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-data-tab-empty.png
[124]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-create-todoitem-table.png
[125]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/notification-hub-create-android-app7.png
[126]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/notification-hub-create-android-app8.png
[127]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/notification-area-received.png


[Xamarin.iOS Studio]: http://xamarin.com/platform
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456
[Mobile Services iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Apple Push Notification Service]: http://go.microsoft.com/fwlink/p/?LinkId=272584
[Get started with Mobile Services]: mobile-services-ios-get-started.md

[Xamarin デバイス プロビジョニング]: http://developer.xamarin.com/guides/ios/getting_started/installation/device_provisioning/


[Azure の管理ポータル]: https://manage.windowsazure.com/
[apns object]: http://go.microsoft.com/fwlink/p/?LinkId=272333
[Azure Mobile Services コンポーネント]: http://components.xamarin.com/view/azure-mobile-services/
[completed example project]: http://go.microsoft.com/fwlink/p/?LinkId=331303
[Xamarin.iOS]: http://xamarin.com/download
[Google Cloud Messaging のクライアント コンポーネント]: http://components.xamarin.com/view/GCMClient/
[Google Cloud Messaging クライアント コンポーネント]: http://components.xamarin.com/view/GCMClient/
[Xamarin.Forms Azure Push Notification Starter Sample (Xamarin.Forms の Azure プッシュ通知スタート サンプル)]: https://github.com/Azure/mobile-services-samples/tree/master/TodoListXamarinForms
[Completed Xamarin.Forms Azure Push Notification Sample (Xamarin.Forms の Azure プッシュ通知の完全なサンプル)]: https://github.com/Azure/mobile-services-samples/tree/master/GettingStartedWithPushXamarinForms
 

<!---HONumber=Oct15_HO3-->