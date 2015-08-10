<properties 
	pageTitle="Azure App Service を使用して Xamarin iOS アプリにプッシュ通知を追加する" 
	description="Azure App Service を使用して Xamarin iOS アプリにプッシュ通知を送信する方法について説明します" 
	services="app-service\mobile" 
	documentationCenter="xamarin" 
	authors="ysxu"
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service-mobile" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-xamarin-ios" 
	ms.devlang="dotnet" 
	ms.topic="article"
	ms.date="06/18/2015" 
	ms.author="yuaxu"/>

# Xamarin iOS アプリにプッシュ通知を追加する

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push-preview](../../includes/app-service-mobile-selector-get-started-push-preview.md)]

このトピックでは、Azure App Service を使用して Xamarin iOS 8 アプリにプッシュ通知を送信する方法について説明します。このチュートリアルでは、Apple Push Notification サービス (APNs) を使用したプッシュ通知を [App Service モバイル アプリの使用]に関するページで使用したプロジェクトに追加します。完了すると、モバイル バックエンドは、レコードが挿入されるたびにプッシュ通知を送信します。

このチュートリアルには、次のものが必要です。

+ iOS 8 デバイス
+ iOS Developer Program メンバーシップ
+ [Xamarin.iOS Studio]
+ [Azure Mobile Services コンポーネント]

   >[AZURE.NOTE]プッシュ通知の構成要件により、プッシュ通知のデプロイとテストは、エミュレーターではなく iOS 対応デバイス (iPhone または iPad) で行う必要があります。

Apple Push Notification Service (APNs) では、証明書を使用してモバイル アプリを認証します。次の手順に従って、必要な証明書を作成し、モバイル アプリにアップロードしてください。公式な APNS 機能のドキュメントについては、「[Apple Push Notification Service]」を参照してください。

##<a name="review"></a>サーバーのプロジェクト構成を確認する (省略可能)

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-enable-push-preview](../../includes/app-service-mobile-dotnet-backend-enable-push-preview.md)]

## <a name="certificates"></a>証明書の署名要求ファイルを生成する

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

## <a name="register"></a>アプリをプッシュ通知に登録する

モバイル アプリから iOS デバイスにプッシュ通知を送信できるようにするには、アプリケーションを Apple に登録し、プッシュ通知にも登録する必要があります。

1. アプリケーションをまだ登録していない場合は、Apple デベロッパー センターで iOS Provisioning Portal</a> に移動し、Apple ID でログインして、**[Identifiers]** をクリックします。次に **[App IDs]** をクリックし、最後に **+** 記号をクリックして、アプリケーションのアプリケーション ID を作成します。
    
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
   
    メモ: このチュートリアルでは開発証明書を使用します。運用証明書の場合も同じ処理を行います。証明書をモバイル アプリにアップロードするときと同じ証明書の種類を設定してください。

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

    メモ: 証明書の名前は異なることがありますが、名前の前に <strong>Apple Development iOS Push Notification Services:</strong> が付けられます。

後で、この証明書を使用して .p12 ファイルを生成し、それをモバイル サービスにアップロードして APNs による認証を有効にします。

## <a name="profile"></a>アプリケーションのプロビジョニング プロファイルを作成する
 
1. <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">iOS Provisioning Portal</a> に戻って **[Provisioning Profiles]** を選択し、**[All]** を選択してから **+** ボタンをクリックして、新しいプロファイルを作成します。これで、**Add iOS Provisiong Profile** ウィザードが起動します。

    ![][112]

2. **[Development]** でプロビジョニング プロファイルの種類として **[iOS App Development]** を選択し、**[Continue]** をクリックします。

3. 次に、**[App ID]** ボックスでモバイル アプリのクイック スタート アプリケーションのアプリケーション ID を選択し、**[Continue]** をクリックします。

    ![][113]

4. **[Select certificates]** 画面で、前に作成した証明書を選択し、**[Continue]** をクリックします。

    ![][114]

5. 次に、テストに使用する**デバイス**を選択し、**[続行]** をクリックします。
  
    ![][115]

6. 最後に、**[Profile Name]** でプロファイルの名前を選択し、**[Generate]** をクリックしてから、**[Done]** をクリックします。

    ![][116]

    新しいプロビジョニング プロファイルが作成されます。

    ![][117]

## <a name="configure-appServiceMobile"></a>プッシュ要求を送信するように App Service モバイル バックエンドを構成する

[AZURE.INCLUDE [app-service-mobile-apns-configure-push-preview](../../includes/app-service-mobile-apns-configure-push-preview.md)]

##<a id="update-server"></a>プッシュ通知を送信するようにサーバーを更新する

1. Visual Studio でソリューションを開いて右クリックし、**[NuGet パッケージの管理]** をクリックします。

2. **Microsoft.Azure.NotificationHubs** を検索し、ソリューション内のすべてのプロジェクトに対して **[インストール]** をクリックします。

3. Visual Studio のソリューション エクスプローラーで、モバイル バックエンド プロジェクト内の **Controllers** フォルダーを展開します。TodoItemController.cs を開きます。ファイルの先頭に、次の `using` ステートメントを追加します。

        using System.Collections.Generic;
        using Microsoft.Azure.NotificationHubs;

4. **InsertAsync** の呼び出しの後の `PostTodoItem` メソッドに次のスニペットを追加します。

        // get Notification Hubs credentials associated with this Mobile App
        string notificationHubName = this.Services.Settings.NotificationHubName;
        string notificationHubConnection = this.Services.Settings.Connections[ServiceSettingsKeys.NotificationHubConnectionString].ConnectionString;

        // connect to notification hub
        NotificationHubClient Hub = NotificationHubClient.CreateClientFromConnectionString(notificationHubConnection, notificationHubName);

        // iOS payload
        var appleNotificationPayload = "{"aps":{"alert":"" + item.Text + ""}}";

        await Hub.Push.SendAppleNativeNotificationAsync(appleNotificationPayload);

    このコードは、このモバイル アプリに関連付けられている通知ハブに、todo 項目の挿入後にプッシュ通知を送信するよう指示します。


## <a name="publish-the-service"></a>モバイル バックエンドを Azure に発行する

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service-preview](../../includes/app-service-mobile-dotnet-backend-publish-service-preview.md)]

## <a name="configure-app"></a>Xamarin.iOS アプリケーションを構成する

1. Xamarin.Studio で、**Info.plist** を開き、前に作成した ID で **Bundle Identifier** を更新します。

    ![][121]

2. **[Background Modes]** まで下へスクロールし、**[Enable Background Modes]** と **[Remote notifications]** の各チェック ボックスをオンにします。

    ![][122]

3. ソリューション パネルでプロジェクトをダブルクリックし、**[Project Options]** を開きます。

4.  **[Build]** で **[iOS Bundle Signing]** を選択し、対応する **ID** とこのプロジェクトに対して設定した**プロビジョニング プロファイル**を選択します。

    ![][120]

    これで、Xamarin プロジェクトはコード署名のために新しいプロファイルを使用するようになります。公式の Xamarin デバイス プロビジョニングのドキュメントについては、[Xamarin デバイス プロビジョニング]に関するページを参照してください。

## <a name="add-push"></a>アプリケーションにプッシュ通知を追加する

1. **QSTodoService** で、既存のクライアント宣言をオーバーライドすると、**AppDelegate** がモバイル クライアントを取得できます。
        
        public MobileServiceClient client { get; private set; }

2. **AppDelegate** で、**FinishedLaunching** イベントをオーバーライドします。

        public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
        {
            // registers for push for iOS8
            var settings = UIUserNotificationSettings.GetSettingsForTypes(
                UIUserNotificationType.Alert 
                | UIUserNotificationType.Badge 
                | UIUserNotificationType.Sound, 
                new NSSet());

            UIApplication.SharedApplication.RegisterUserNotificationSettings(settings); 
            UIApplication.SharedApplication.RegisterForRemoteNotifications();

            return true;
        }

3. 同じファイルで **RegisteredForRemoteNotifications** イベントをオーバーライドします。

        public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
        {
            MobileServiceClient client = QSTodoService.DefaultService.GetClient;

            // Register for push with your mobile app
            var push = client.GetPush();
            push.RegisterAsync(deviceToken);
        }

4. 次に、**DidReceivedRemoteNotification** イベントをオーバーライドします。

        public override void DidReceiveRemoteNotification (UIApplication application, NSDictionary userInfo, Action<UIBackgroundFetchResult> completionHandler)
        {
            NSDictionary aps = userInfo.ObjectForKey(new NSString("aps")) as NSDictionary;

            string alert = string.Empty;
            if (aps.ContainsKey(new NSString("alert")))
                alert = (aps [new NSString("alert")] as NSString).ToString();

            //show alert
            if (!string.IsNullOrEmpty(alert))
            {
                UIAlertView avAlert = new UIAlertView("Notification", alert, null, "OK", null);
                avAlert.Show();
            }
        }

これで、アプリケーションがプッシュ通知をサポートするように更新されました。


## <a name="publish-the-service"></a>モバイル バックエンドを Azure に発行する

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service-preview](../../includes/app-service-mobile-dotnet-backend-publish-service-preview.md)]

## <a name="test"></a>アプリケーションでプッシュ通知をテストする

1. **[Run]** ボタンを押して、プロジェクトをビルドし、iOS 対応のデバイスでアプリケーションを開始します。**[OK]** をクリックして、プッシュ通知を受け入れます。
	
	> [AZURE.NOTE]アプリケーションからのプッシュ通知を明示的に受け入れる必要があります。これが必要であるのは、初めてアプリケーションを実行するときだけです。

2. アプリケーションで、タスクを入力し、プラス (\*\*+\*\*) アイコンをクリックします。

3. 通知が受信されたことを確認し、**[OK]** をクリックして通知を破棄します。

4. 手順 2. を繰り返してすぐにアプリケーションを閉じたら、通知が表示されることを確認します。

これで、このチュートリアルは終了です。

<!-- Images. -->

[24]: ./media/mobile-services-ios-get-started-push/mobile-services-quickstart-push2-ios.png
[App Service モバイル アプリの使用]: app-service-mobile-dotnet-backend-xamarin-ios-get-started-preview.md

[5]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview/mobile-services-ios-push-step5.png
[6]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview/mobile-services-ios-push-step6.png
[7]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview/mobile-services-ios-push-step7.png

[9]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview/mobile-services-ios-push-step9.png
[10]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview/mobile-services-ios-push-step10.png

[17]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-step17.png
[18]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-selection.png
[19]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-push-tab-ios.png
[20]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-push-tab-ios-upload.png
[21]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-portal-data-tables.png
[22]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-insert-script-push2.png
[23]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-quickstart-push1-ios.png
[25]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-quickstart-push3-ios.png
[26]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-quickstart-push4-ios.png
[28]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-step18.png

[101]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-01.png
[102]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview/mobile-services-ios-push-02.png
[103]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview/mobile-services-ios-push-03.png
[104]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview/mobile-services-ios-push-04.png
[105]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview/mobile-services-ios-push-05.png
[106]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview/mobile-services-ios-push-06.png
[107]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview/mobile-services-ios-push-07.png
[108]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview/mobile-services-ios-push-08.png

[110]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview/mobile-services-ios-push-10.png
[111]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview/mobile-services-ios-push-11.png
[112]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview/mobile-services-ios-push-12.png
[113]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview/mobile-services-ios-push-13.png
[114]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview/mobile-services-ios-push-14.png
[115]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview/mobile-services-ios-push-15.png
[116]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview/mobile-services-ios-push-16.png
[117]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview/mobile-services-ios-push-17.png

[120]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview/mobile-services-ios-push-20.png
[121]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview/mobile-services-ios-push-21.png
[122]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview/mobile-services-ios-push-22.png

[Xamarin.iOS Studio]: http://xamarin.com/platform
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456
[Mobile Services iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Apple Push Notification Service]: http://go.microsoft.com/fwlink/p/?LinkId=272584
[Get started with Mobile Services]: /ja-jp/develop/mobile/tutorials/get-started-xamarin-ios
[Get started with data]: /ja-jp/develop/mobile/tutorials/get-started-with-data-xamarin-ios
[Get started with authentication]: /ja-jp/develop/mobile/tutorials/get-started-with-users-xamarin-ios
[Get started with push notifications]: /ja-jp/develop/mobile/tutorials/get-started-with-push-xamarin-ios
[Push notifications to app users]: /ja-jp/develop/mobile/tutorials/push-notifications-to-users-ios
[Authorize users with scripts]: /ja-jp/develop/mobile/tutorials/authorize-users-in-scripts-xamarin-ios
[Xamarin デバイス プロビジョニング]: http://developer.xamarin.com/guides/ios/getting_started/installation/device_provisioning/


[Azure Management Portal]: https://manage.windowsazure.com/
[apns object]: http://go.microsoft.com/fwlink/p/?LinkId=272333
[Azure Mobile Services コンポーネント]: http://components.xamarin.com/view/azure-mobile-services/
[completed example project]: http://go.microsoft.com/fwlink/p/?LinkId=331303

 

<!---HONumber=July15_HO5-->