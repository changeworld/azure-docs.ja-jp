<properties urlDisplayName="Get Started with Push Notifications" pageTitle="プッシュ通知 (Xamarin.iOS) - Mobile Services" metaKeywords="" description="Azure Mobile Services を使用する Xamarin.iOS アプリでプッシュ通知を使用する方法について説明します。" metaCanonical="" disqusComments="0" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="yuaxu" manager="dwrede" services="mobile-services"/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-ios" ms.devlang="Java" ms.topic="article" ms.date="10/20/2014" ms.author="yuaxu" />

# Mobile Services アプリへのプッシュ通知の追加

[WACOM.INCLUDE [mobile-services-selector-get-started-push-xamarin](../includes/mobile-services-selector-get-started-push-xamarin.md)]

<p>このトピックでは、Azure Mobile Services を使用して Xamarin.iOS 8 アプリケーションにプッシュ通知を送信する方法について説明します。このチュートリアルでは、Apple プッシュ通知サービス (APNS) を使用したプッシュ通知を [Mobile Services の使用]プロジェクトに追加します。完了すると、モバイル サービスは、レコードが挿入されるたびにプッシュ通知を送信します。</p>

このチュートリアルでは、プッシュ通知を有効にするための、次の基本的な手順について説明します。

1. [証明書の署名要求を生成する]
2. [アプリケーションを登録し、プッシュ通知を有効にする]
3. [アプリケーションのプロビジョニング プロファイルを作成する]
4. [モバイル サービスを構成する]
5. [Xamarin.iOS アプリケーションを構成する]
6. [アプリケーションにプッシュ通知を追加する]
7. [プッシュ通知を送信するようにスクリプトを更新する]
8. [データを挿入して通知を受け取る]

このチュートリアルには、次のものが必要です。

+ iOS 8 デバイス
+ iOS Developer Program メンバーシップ
+ [Xamarin.iOS Studio]
+ [Azure モバイル サービス コンポーネント]

   <div class="dev-callout"><b>注</b>
   <p>プッシュ通知の構成要件により、プッシュ通知のデプロイとテストは、エミュレーターではなく iOS 対応デバイス (iPhone または iPad) で行う必要があります。</p>
   </div>

Apple Push Notification Service (APNS) では、証明書を使用してモバイル サービスを認証します。次の手順に従って、必要な証明書を作成し、モバイル サービスにアップロードしてください。公式な APNS 機能のドキュメントについては、[Apple Push Notification Service に関するページ]を参照してください。

## <a name="certificates"></a>証明書の署名要求ファイルを生成する

まず、Apple が署名証明書を生成するために使用する、証明書署名要求 (CSR: Certificate Signing Request) ファイルを生成する必要があります。

1. Utilities から **[キーチェーン アクセス] ツール**を実行します。

2. **[キーチェーン アクセス]** をクリックし、**[証明書アシスタント]** を展開して、**[認証局に証明書を要求]** をクリックします。

    ![][5]

3. **[ユーザーのメール アドレス]** に入力し **[通称]** の値を入力します。**[ディスクに保存]** が選択されていることを確認して、**[続ける]** をクリックします。

    ![][6]

4. **[名前と付けて保存]** に証明書署名要求 (CSR) ファイルの名前を入力し、**[場所]** で保存場所を選択して、**[保存]** をクリックします。

    ![][7]
  
    選択した場所を忘れないでください。

次に、アプリケーションを Apple に登録し、プッシュ通知を有効にし、このエクスポートした CSR をアップロードしてプッシュ通知を作成します。

## <a name="register"></a>アプリケーションをプッシュ通知に登録する

モバイル サービスから iOS アプリケーションにプッシュ通知を送信可能にするには、アプリケーションを Apple に登録し、プッシュ通知にも登録する必要があります。 

1. アプリケーションをまだ登録していない場合は、Apple デベロッパー センターで <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">iOS プロビジョニング ポータル</a>に移動し、Apple ID でログインして、**[Identifiers]** をクリックします。次に **[App IDs]** をクリックし、最後に、**+** 記号をクリックして、アプリケーションのアプリケーション ID を作成します。
    
    ![][102]

2. **[Description]** にアプリケーションの名前を入力し、一意の**バンドル識別子**を入力して、覚えておきます。[App Services] セクションの [Push Notifications] オプションをオンにして、**[Continue]** をクリックします。この例では ID として **MobileServices.Quickstart** を使用していますが、アプリケーション ID はすべてのユーザー間で一意である必要があるので、この同じ ID を再利用することはできません。そのため、自分のフル ネームやイニシャルをアプリケーション名の後に付加することをお勧めします。 

    ![][103]
   
これで、アプリケーション ID が生成され、情報の**サブミット**が求められます。**[Submit]** をクリックします。
   
    ![][104] 
   
   **[Submit]** をクリックすると、以下のような **[Registration complete]** 画面が表示されます。**[Done]** をクリックします。
   
    ![][105]    

3. 作成したアプリケーション ID を見つけ、その行をクリックします。 

    ![][106]
   
   アプリケーションの ID をクリックすると、そのアプリケーションの詳細と ID が表示されます。**[Settings]** をクリックします。
   
    ![][107] 
   
4. 画面の下部までスクロールし、**[Development Push SSL Certificate]** セクションの **[Create Certificate]** ボタンをクリックします。

    ![][108] 

    これで、[Add iOS Certificate] アシスタントが表示されます。
   
注: このチュートリアルでは開発証明書を使用します。運用証明書の場合も同じ処理を行います。証明書をモバイル サービスにアップロードするときと同じ証明書タイプを設定してください。

5. **[Choose File]** をクリックして、前に CSR ファイルを保存した場所に移動してから、**[Generate]** をクリックします。 

    ![][110]
  
6. ポータルで証明書が作成されたら **[Download]** ボタンをクリックし、**[Done]** をクリックします。
 
    ![][111]  

    これによって、署名証明書がダウンロードされ、コンピューターの Downloads フォルダーに保存されます。 

    ![][9] 

注: 既定では、ダウンロードした開発証明書ファイルの名前は <strong>aps_development.cer</strong> になっています。

7. ダウンロードしたプッシュ証明書 **aps_development.cer** をダブルクリックします。

    下図のように、新しい証明書が Keychain にインストールされます。

    ![][10]

注: 証明書の名前は異なることがありますが、名前の前に <strong>Apple Development iOS Push Notification Services:</strong> が付けられます。

後で、この証明書を使用して .p12 ファイルを生成し、それをモバイル サービスにアップロードして APNS による認証を有効にします。

## <a name="profile"></a>アプリケーションのプロビジョニング プロファイルを作成する
 
1. <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">iOS プロビジョニング ポータル</a>に戻って **[Provisioning Profiles]** を選択し、**[All]** を選択してから **[+]** をクリックして、新しいプロファイルを作成します。これで、**Add iOS Provisiong Profile** ウィザードが起動されます。

    ![][112]

2. **[Development]** でプロビジョニング プロファイルの種類として **[iOS App Development]** を選択し、**[Continue]** をクリックします。

3. 次に、**[App ID]** の一覧でモバイル サービスのクイック スタート アプリケーションのアプリケーション ID を選択し、**[Continue]** をクリックします。

    ![][113]

4. **[Select certificates]** 画面で、前に作成した証明書を選択し、**[Continue]**. をクリックします。

    ![][114]

5. 次に、テストに使用する**デバイス**を選択し、**[Continue]** をクリックします。
  
    ![][115]

6. 最後に、**[Profile Name]** でプロファイルの名前を選択し、**[Generate]** をクリックしてから、**[Done]** をクリックします。

    ![][116]

    新しいプロビジョニング プロファイルが作成されます。

    ![][117]

## <a name="configure-mobileServices"></a>プッシュ要求を送信するようにモバイル サービスを構成する

アプリケーションを APNS に登録し、プロジェクトを構成した後で、モバイル サービスを APNS と統合するように構成する必要があります。

1. キーチェーン アクセスで、新しい証明書を右クリックし、**[エクスポート]** をクリックします。ファイルに名前を付けて **[.p12]** 形式を選択し、**[保存]** をクリックします。

    ![][28]

    エクスポートした証明書のファイル名と場所を書き留めます。

2. [Azure の管理ポータル]にログオンし、**[Mobile Services]** をクリックして、アプリケーションをクリックします。

    ![][18]

3. **[プッシュ]** タブをクリックし、**[Apple プッシュ通知の設定]** で **[アップロード]** をクリックします。

    ![][19]

    [証明書のアップロード] ダイアログ ボックスが表示されます。

4. **[ファイル]** をクリックし、エクスポートした証明書 .p12 ファイルを選択します。**[パスワード]** を入力し、正しい **[モード]** が選択されていることを確認して **[保存]** をクリックします。

    ![][20] 

APNS と連携するようにモバイル サービスが構成されました。

## <a name="configure-app"></a>Xamarin.iOS アプリケーションを構成する

1. Xamarin.Studio で、**Info.plist** を開き、前に作成した ID を持つ**バンドル識別子**を更新します。

    ![][121]

2. **[Background Modes]** まで下にスクロールし、**[Enable Background Modes]** チェック ボックスと、**[Remote notifications]** チェック ボックスをオンにします。 

    ![][122]

3. ソリューションのパネルでプロジェクトをダブルクリックし、**[Project Options]** を開きます。

4.  **[iOS Bundle Signing]** を **[Build]** の下で選択し、対応する **ID** とこのプロジェクトに対して設定した**プロビジョニング プロファイル**を選択します。 

    ![][120]

    これで、Xamarin プロジェクトはコード署名のために新しいプロファイルを使用するようになります。公式の Xamarin デバイス プロビジョニングのドキュメントについては、[Xamarin Device Provisioning (Xamarin デバイス プロビジョニング)]を参照してください。

## <a name="add-push"></a>アプリケーションにプッシュ通知を追加する

1. Xamarin.Studio で AppDelegate.cs プロジェクト ファイルを開き、次のプロパティを追加します。

        public string DeviceToken { get; set; }

2. **TodoItem** クラスを開き、次のプロパティを追加します。

        [JsonProperty(PropertyName = "deviceToken")]
        public string DeviceToken { get; set; }

3. **QSTodoService** で、次のようになるよう、既存のクライアントの宣言をオーバーライドします。
        
        public MobileServiceClient client { get; private set; }

4. 次のメソッドを追加して、**AppDelegate** が後でクライアントを取得してプッシュ通知を登録できるようにします。

        public MobileServiceClient GetClient {
            get{ 
                return client;
            }
        }

5. **AppDelegate** で、**FinishedLaunching** イベントをオーバーライドします。 

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

6. **AppDelegate** で、**RegisteredForRemoteNotifications** イベントをオーバーライドします。

        public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
        {
            // Modify device token
            DeviceToken = deviceToken.Description;
            DeviceToken = DeviceToken.Trim ('<', '>').Replace (" ", "");

            // Get Mobile Services client
            MobileServiceClient client = QSTodoService.DefaultService.GetClient;

            // Register for push with Mobile Services
            IEnumerable<string> tag = new List<string>() { "uniqueTag" };
            var push = client.GetPush ();
            push.RegisterNativeAsync (DeviceToken, tag);
        }

7. **AppDelegate** で、**ReceivedRemoteNotification** イベントをオーバーライドします。

        public override void ReceivedRemoteNotification(UIApplication application, NSDictionary userInfo)
        {
            Debug.WriteLine(userInfo.ToString());
            NSObject inAppMessage;

            bool success = userInfo.TryGetValue(new NSString("inAppMessage"), out inAppMessage);

            if (success)
            {
                var alert = new UIAlertView("Got push notification", inAppMessage.ToString(), null, "OK", null);
                alert.Show();
            }
        }

8. **TodoListViewController** で、**AppDelegeate** に格納されているデバイス トークンを取得し、これを追加される **TodoItem** に格納するように **OnAdd** アクションを変更します。

string deviceToken = ((AppDelegate)UIApplication.SharedApplication.Delegate).DeviceToken;

        var newItem = new TodoItem() 
        {
            Text = itemText.Text, 
            Complete = false,
            DeviceToken = deviceToken
        };

これで、アプリケーションがプッシュ通知をサポートするように更新されました。

## <a name="update-scripts"></a>管理ポータルで登録されている挿入スクリプトを更新する

1. 管理ポータルで、**[データ]** タブをクリックし、**TodoItem** テーブルをクリックします。 

    ![][21]

2. **[todoitem]** で、**[スクリプト]** タブをクリックし、**[挿入]** をクリックします。
   
    ![][22]

    **TodoItem** テーブルで挿入が発生したときに呼び出される関数が表示されます。

3. insert 関数を次のコードに置き換え、**[保存]** をクリックします。

        function insert(item, user, request) {
            request.execute();
            // Set timeout to delay the notification, to provide time for the 
            // app to be closed on the device to demonstrate toast notifications
            setTimeout(function() {
                push.apns.send("uniqueTag", {
                    alert: "Toast: " + item.text,
                    payload: {
                        inAppMessage: "Hey, a new item arrived: '" + item.text + "'"
                    }
                });
            }, 2500);
        }

    これで、新しい insert スクリプトが登録されます。このスクリプトは [apns オブジェクト]を使用して、挿入要求で指定されたデバイスにプッシュ通知 (挿入されたテキスト) を送信します。 

    <div class="dev-callout"><b>注</b>
   <p>このスクリプトでは、トースト通知を受け取るためにアプリケーションを閉じる時間を与えるために通知の送信を遅らせています。</p>
   </div> 

## <a name="test"></a>アプリケーションでプッシュ通知をテストする

1. **[Run]** を押して、プロジェクトをビルドし、iOS 対応のデバイスでアプリケーションを開始します。**[OK]** をクリックして、プッシュ通知を受け入れます。

    ![][23]

    <div class="dev-callout"><b>注</b>
    <p> アプリケーションからのプッシュ通知を明示的に受け入れる必要があります。これが必要なのは、初めてアプリケーションを実行するときだけです。</p>
    </div>

2. アプリケーションで、意味のあるテキスト (たとえば、「新しい Mobile Services タスク」) を入力し、プラス記号 (**+**) のアイコンをクリックします。

    ![][24]

3. 通知が受信されたことを確認し、**[OK]** をクリックして通知を破棄します。

    ![][25]

4. 手順 2 を繰り返してすぐにアプリケーションを閉じ、次のトーストが表示されることを確認します。

    ![][26]

これで、このチュートリアルは終了です。

<!-- Anchors. -->
[証明書の署名要求を生成する]: #certificates
[アプリケーションを登録し、プッシュ通知を有効にする]: #register
[アプリケーションのプロビジョニング プロファイルを作成する]: #profile
[モバイル サービスを構成する]: #configure-mobileServices
[Xamarin.iOS アプリケーションを構成する]: #configure-app
[プッシュ通知を送信するようにスクリプトを更新する]: #update-scripts
[アプリケーションにプッシュ通知を追加する]: #add-push
[データを挿入して通知を受け取る]: #test

<!-- Images. -->

[5]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-step5.png
[6]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-step6.png
[7]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-step7.png

[9]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-step9.png
[10]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-step10.png

[17]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-step17.png
[18]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-selection.png
[19]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-push-tab-ios.png
[20]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-push-tab-ios-upload.png
[21]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-portal-data-tables.png
[22]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-insert-script-push2.png
[23]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-quickstart-push1-ios.png
[24]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-quickstart-push2-ios.png
[25]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-quickstart-push3-ios.png
[26]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-quickstart-push4-ios.png
[28]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-step18.png

[101]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-01.png
[102]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-02.png
[103]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-03.png
[104]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-04.png
[105]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-05.png
[106]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-06.png
[107]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-07.png
[108]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-08.png

[110]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-10.png
[111]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-11.png
[112]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-12.png
[113]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-13.png
[114]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-14.png
[115]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-15.png
[116]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-16.png
[117]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-17.png

[120]:./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-20.png
[121]:./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-21.png
[122]:./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-22.png

[Xamarin.iOS Studio]: http://xamarin.com/platform
[Xcode のインストール]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS プロビジョニング ポータル]: http://go.microsoft.com/fwlink/p/?LinkId=272456
[モバイル サービス iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Apple Push Notification Service]: http://go.microsoft.com/fwlink/p/?LinkId=272584
[モバイル サービスの使用]: /ja-jp/develop/mobile/tutorials/get-started-xamarin-ios
[データの使用]: /ja-jp/develop/mobile/tutorials/get-started-with-data-xamarin-ios
[認証の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-users-xamarin-ios
[プッシュ通知の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-push-xamarin-ios
[アプリケーション ユーザーへのプッシュ通知]: /ja-jp/develop/mobile/tutorials/push-notifications-to-users-ios
[スクリプトを使用したユーザーの承認]: /ja-jp/develop/mobile/tutorials/authorize-users-in-scripts-xamarin-ios
[Xamarin Device Provisioning (Xamarin デバイス プロビジョニング)]: http://developer.xamarin.com/guides/ios/getting_started/installation/device_provisioning/


[Azure 管理ポータル]: https://manage.windowsazure.com/
[apns オブジェクト]: http://go.microsoft.com/fwlink/p/?LinkId=272333
[Azure モバイル サービス コンポーネント]: http://components.xamarin.com/view/azure-mobile-services/
[完成したサンプル プロジェクト]: http://go.microsoft.com/fwlink/p/?LinkId=331303
