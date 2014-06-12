<properties linkid="develop-mobile-tutorials-get-started-with-push-ios" urlDisplayName="プッシュの使用 (iOS)" pageTitle="プッシュ通知の使用 (iOS) | モバイル デベロッパー センター" metaKeywords="" description="Azure のモバイル サービスを使用して iOS アプリケーションにプッシュ通知を送信する方法について説明します。" metaCanonical="http://www.windowsazure.com/ja-jp/develop/mobile/tutorials/get-started-with-push-dotnet/" services="" documentationCenter="Mobile" title="モバイル サービスでのプッシュ通知の使用" authors=""  solutions="" writer="" manager="" editor=""  />

 
# モバイル サービスでのプッシュ通知の使用
<div class="dev-center-tutorial-selector sublanding"><a href="/ja-jp/develop/mobile/tutorials/get-started-with-push-dotnet" title="Windows ストア C#">Windows ストア C#</a><a href="/ja-jp/develop/mobile/tutorials/get-started-with-push-js" title="Windows ストア JavaScript">Windows ストア JavaScript</a><a href="/ja-jp/develop/mobile/tutorials/get-started-with-push-wp8" title="Windows Phone">Windows Phone</a><a href="/ja-jp/develop/mobile/tutorials/get-started-with-push-ios" title="iOS" class="current">iOS</a><a href="/ja-jp/develop/mobile/tutorials/get-started-with-push-android" title="Android">Android</a><a href="/ja-jp/develop/mobile/tutorials/get-started-with-push-xamarin-ios" title="Xamarin.iOS">Xamarin.iOS</a><a href="/ja-jp/develop/mobile/tutorials/get-started-with-push-xamarin-android" title="Xamarin.Android">Xamarin.Android</a></div>
<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>このトピックでは、Azure のモバイル サービスを使用して iOS アプリケーションにプッシュ通知を送信する方法について説明します。このチュートリアルでは、Apple Push Notification Service (APNS) を使用したプッシュ通知をクイック スタート プロジェクトに追加します。完了すると、モバイル サービスは、レコードが挿入されるたびにプッシュ通知を送信します。</p>
<p>このチュートリアルのビデオ バージョンを見るには、右側のクリップをクリックします。</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/posts/iOS-Support-in-Windows-Azure-Mobile-Services" target="_blank" class="label">チュートリアルを見る</a> <a style="background-image: url('/media/devcenter/mobile/videos/get-started-with-push-ios-180x120.png') !important;" href="http://channel9.msdn.com/posts/iOS-Support-in-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">ビデオを再生する</span></a> <span class="time">10:37</span></div>
</div>

>[WACOM.NOTE] このトピックでは、モバイル サービスによって提供される古いバージョンのサポートを使用してプッシュ通知を有効にする方法を示します。Azure 通知ハブは、モバイル サービスを統合し、数百万台のデバイスに対してテンプレート ベースとプラットフォームのプッシュ通知を送信できるようにします。既定では、通知ハブを使用したプッシュ通知は有効になっていません。また、現時点ではモバイル サービス ライブラリ内に、iOS 用の通知ハブのサポートはありません。ただし、通知ハブ ライブラリを使用してモバイル サービスからプッシュ通知を送信することができます。詳細については、「[通知ハブの使用](/ja-jp/documentation/articles/notification-hubs-ios-get-started/)」を参照してください。

このチュートリアルでは、プッシュ通知を有効にするための、次の基本的な手順について説明します。

1. [証明書の署名要求を生成する]
2. [アプリケーションを登録し、プッシュ通知を有効にする]
3. [アプリケーションのプロビジョニング プロファイルを作成する]
3. [モバイル サービスを構成する]
4. [アプリケーションにプッシュ通知を追加する]
5. [プッシュ通知を送信するようにスクリプトを更新する]
6. [データを挿入して通知を受け取る]

このチュートリアルには、次のものが必要です。

+ [モバイル サービス iOS SDK]
+ [XCode 4.5][Install Xcode]
+ iOS 5.0 (またはこれ以降のバージョン) に対応したデバイス
+ iOS Developer Program メンバーシップ

   > [WACOM.NOTE] プッシュ通知の構成要件により、プッシュ通知の展開とテストは、エミュレーターではなく iOS 対応デバイス (iPhone または iPad) で行う必要があります。

このチュートリアルは、モバイル サービスのクイック スタートに基づいています。このチュートリアルを開始する前に、「[モバイル サービスの使用]」を完了している必要があります。

Apple Push Notification Service (APNS) では、証明書を使用してモバイル サービスを認証します。次のステップに従って、必要な証明書を作成し、モバイル サービスにアップロードしてください。公式な APNS 機能のドキュメントについては、「[Apple Push Notification Service]」を参照してください。

## 証明書の署名要求ファイルを生成する

まず、Apple が署名証明書を生成するために使用する、証明書署名要求 (CSR: Certificate Signing Request) ファイルを生成する必要があります。

1. Utilities フォルダーから [Keychain Access] ツールを実行します。

2. **[Keychain Access]** をクリックし、**[Certificate Assistant]** を展開して、**[Request a Certificate from a Certificate Authority]** をクリックします。

  	![][5]

3. **[User Email Address]** と **[Common Name]** を選択し、**[Saved to disk]** が選択されていることを確認して、**[Continue]** をクリックします。必要ではないため、"**CA 電子メール アドレス**" フィールドを空白のままにします。

  	![][6]

4. **[Save As]** に証明書署名要求 (CSR) ファイルの名前を入力し、**[Where]** で保存場所を選択して **[Save]** をクリックします。

  	![][7]
  
  	指定選択した場所に CSR ファイルが保存されます。既定の場所は [Desktop] です。このファイル用に選択した場所を忘れないでください。

次に、アプリケーションを Apple に登録し、プッシュ通知を有効にし、このエクスポートした CSR をアップロードしてプッシュ通知を作成します。

## アプリケーションをプッシュ通知に登録する

モバイル サービスから iOS アプリケーションにプッシュ通知を送信できるようにするには、アプリケーションを Apple に登録し、プッシュ通知にも登録する必要があります。

1. アプリケーションをまだ登録していない場合は、Apple デベロッパー センターで <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">iOS プロビジョニング ポータル</a>に移動し、Apple ID でログインして、**[Identifiers]** をクリックし、**[App IDs]** をクリックします。最後に、**+** 記号をクリックして新しいアプリケーションを登録します。

   	![][102] 

2. **[Description]** にアプリケーションの名前を入力し、**[Bundle Identifier]** に値として _MobileServices.Quickstart_ と入力します。[App Services] セクションの [Push Notifications] オプションをオンにして、**[Continue]** をクリックします。この例では ID として **MobileServices.Quickstart** を使用していますが、アプリケーション ID はすべてのユーザー間で一意である必要があるので、この同じ ID を再利用することはできません。そのため、自分のフル ネームやイニシャルをアプリケーション名の後に付加することをお勧めします。

   	![][103]
   
   	これで、アプリケーション ID が生成され、情報の**サブミット**が求められます。**[Submit]** をクリックします。
   
   	![][104] 
   
   	**[Submit]** をクリックすると、以下のような **[Registration complete]** 画面が表示されます。**[Done]** をクリックします。
   
   	![][105]

    > [WACOM.NOTE]<i>MobileServices.Quickstart</i> ではなく <strong>[Bundle Identifier]</strong> の値を指定する場合は、Xcode プロジェクトでバンドル ID の値も更新する必要があります。

3. 作成したアプリケーション ID を見つけ、その行をクリックします。

   	![][106]
   
   	アプリケーション ID をクリックすると、アプリケーションとアプリケーション ID の詳細が表示されます。**[Settings]** をクリックします。
   
   	![][107] 
   
4. 画面の下部までスクロールし、**[Development Push SSL Certificate]** セクションの **[Create Certificate]** ボタンをクリックします。

   	![][108] 

   	これで、[Add iOS Certificate] アシスタントが表示されます。
   
   	![][108] 


    > [WACOM.NOTE] このチュートリアルでは開発証明書を使用します。運用証明書の場合も同じ処理を行います。証明書をモバイル サービスにアップロードするときと同じ証明書タイプを設定してください。

5. **[Choose File]** をクリックして、最初の作業で CSR ファイルを保存した場所に移動し、**[Generate]** をクリックします。

  	![][110]
  
6. ポータルで証明書が作成されたら **[Download]** ボタンをクリックし、**[Done]** をクリックします。
 
  	![][111]  

   	これによって、署名証明書がダウンロードされ、コンピューターの Downloads フォルダーに保存されます。

  	![][9] 

    > [WACOM.NOTE] 既定では、ダウンロードした開発証明書ファイルの名前は **aps_development.cer** になっています。

7. ダウンロードしたプッシュ証明書 **aps_development.cer** をダブルクリックします。

   	下図のように、新しい証明書が Keychain にインストールされます。

   	![][10]

	> [WACOM.NOTE] 証明書の名前は異なることがありますが、名前の前に **Apple Development iOS Push Notification Services:** が付けられます。

後で、この証明書を使用して .p12 ファイルを生成し、それをモバイル サービスにアップロードして APNS による認証を有効にします。

## アプリケーションのプロビジョニング プロファイルを作成する
 
1. <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">iOS プロビジョニング ポータル</a>に戻って **[Provisioning Profiles]** を選択し、**[All]** を選択してから **+** ボタンをクリックして、新しいプロファイルを作成します。これで、**Add iOS Provisiong Profile** ウィザードが起動されます。

   	![][112]

2. **[Development]** でプロビジョニング プロファイルの種類として **[iOS App Development]** を選択し、**[Continue]** をクリックします。

   	![][113]

3. 次に、**[App ID]** の一覧でモバイル サービスのクイック スタート アプリケーションのアプリケーション ID を選択し、**[Continue]** をクリックします。

   	![][114]

4. **[Select certificates]** 画面で、前に作成した証明書を選択し、**[Continue]** をクリックします。
  
   	![][115]

5. 次に、テストに使用する**デバイス**を選択し、**[Continue]** をクリックします。

   	![][116]

6. 最後に、**[Profile Name]** でプロファイルの名前を選択し、**[Generate]** をクリックしてから、**[Done]** をクリックします。

   	![][117]
	
  	新しいプロビジョニング プロファイルが作成されます。

7. Xcode で、Organizer を開いて [Devices] ビューを選択し、左ウィンドウの **[Library]** セクションにある **[Provisioning Profiles]** を選択して、中央ウィンドウの一番下にある **[Refresh]** をクリックします。

   	![][101]

8. **[Targets]** の **[Quickstart]** をクリックし、**[Code Signing Identity]** を展開して、**[Debug]** で新しいプロファイルを選択します。

   	![][17]

これで、Xcode プロジェクトはコード署名のために新しいプロファイルを使用するようになります。次に、証明書をモバイル サービスにアップロードする必要があります。

## プッシュ要求を送信するようにモバイル サービスを構成する

アプリケーションを APNS に登録し、プロジェクトを構成した後で、モバイル サービスを APNS と統合するように構成する必要があります。

1. Keychain Access で、新しい証明書を右クリックし、**[Export]** をクリックします。ファイルの名前を [QuickstartPusher] にして **[.p12]** 形式を選択し、**[Save]** をクリックします。

   	![][28]

  エクスポートした証明書のファイル名と場所を書き留めます。

	> [WACOM.NOTE] このチュートリアルでは QuickstartPusher.p12 ファイルを作成します。ファイル名と場所は同じである必要はありません。

2. [Azure の管理ポータル]にログオンし、**[モバイル サービス]** をクリックして、アプリケーションをクリックします。

   	![][18]

3. **[プッシュ]** タブをクリックし、**[アップロード]** をクリックします。

   	![][19]

	[証明書のアップロード] ダイアログ ボックスが表示されます。

4. **[ファイル]** をクリックし、エクスポートした証明書ファイル [QuickstartPusher.p12] を選択します。**[パスワード]** を入力し、正しい **[モード]** が選択されていることを確認して **[保存]** をクリックします。

   	![][20] 

    > [WACOM.NOTE] このチュートリアルでは開発証明書を使用します。

APNS と連携するようにモバイル サービスが構成されました。

## アプリケーションにプッシュ通知を追加する

1. Xcode で QSAppDelegate.h ファイルを開き、***window** プロパティの下に次のプロパティを追加します。

        @property (strong, nonatomic) NSString *deviceToken;

    > [WACOM.NOTE] モバイル サービスで動的スキーマが有効になっていると、このプロパティを含む新しい項目が挿入されたときに、新しい "'deviceToken'" 列が自動的に **TodoItem** テーブルに追加されます。

2. QSAppDelegate.m で、実装内の次のハンドラー メソッドを置き換えます。

        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:
        (NSDictionary *)launchOptions
        {
            // Register for remote notifications
            [[UIApplication sharedApplication] registerForRemoteNotificationTypes:
            UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
            return YES;
        }

3. QSAppDelegate.m で、実装内に次のハンドラー メソッドを追加します。

        //これで登録されたため、デバイス トークン (文字列として) を AppDelegate インスタンスに保存し、
        //まず、山かっこを削除するようにします。
        - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:
        (NSData *)deviceToken {
            NSCharacterSet *angleBrackets = [NSCharacterSet characterSetWithCharactersInString:@"<>"];
            self.deviceToken = [[deviceToken description] stringByTrimmingCharactersInSet:angleBrackets];
        }

4. QSAppDelegate.m で、実装内に次のハンドラー メソッドを追加します。

        // 登録の失敗を処理します。ここでは、deviceToken を空の文字列に設定して
        //挿入の失敗を防ぎます。
        - (void)application:(UIApplication *)application didFailToRegisterForRemoteNotificationsWithError:
        (NSError *)error {
            NSLog(@"Failed to register for remote notifications: %@", error);
            self.deviceToken = @"";
        }

5. QSAppDelegate.m で、実装内に次のハンドラー メソッドを追加します。

        // アプリケーションが実行されていると、トースト アラートは機能しないため、アプリケーションが処理を実行します。
        // ペイロードのユーザー情報を使用して UIAlertView を表示します。
        - (void)application:(UIApplication *)application didReceiveRemoteNotification:
        (NSDictionary *)userInfo {
            NSLog(@"%@", userInfo);
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
            [userInfo objectForKey:@"inAppMessage"] delegate:nil cancelButtonTitle:
            @"OK" otherButtonTitles:nil, nil];
            [alert show];
        }

5. QSTodoListViewController.m で、デリゲートを使用してデバイス トークンを取得できるように QSAppDelegate.h ファイルをインポートします。

        #import "QSAppDelegate.h"

6. QSTodoListViewController.m で、次の行を検索して **(IBAction)onAdd** アクションを変更します。

        NSDictionary *item = @{ @"text" : itemText.text, @"complete" : @(NO) }; 
 
   これを次のコードに置き換えます。

        // Get a reference to the AppDelegate to easily retrieve the deviceToken
        QSAppDelegate *delegate = [[UIApplication sharedApplication] delegate];
    
        NSDictionary *item = @{
            @"text" : itemText.text,
            @"complete" : @(NO),
            // add the device token property to our todo item payload
            @"deviceToken" : delegate.deviceToken
        };

   	これによって、デバイス トークンを取得するための参照が **QSAppDelegate** に追加され、要求ペイロードがそのデバイス トークンを含めるように変更されます。

   	> [WACOM.NOTE] このコードを、<strong>addItem</strong> メソッドへの呼び出しの前に追加する必要があります。

これで、アプリケーションがプッシュ通知をサポートするように更新されました。

## 管理ポータルで登録されている挿入スクリプトを更新する

1. 管理ポータルで、**[データ]** タブをクリックし、**TodoItem** テーブルをクリックします。

   	![][21]

2. **todoitem** で、**[スクリプト]** タブをクリックし、**[挿入]** をクリックします。
   
  	![][22]

   	**TodoItem** テーブルで挿入が発生したときに呼び出される関数が表示されます。

3. insert 関数を次のコードに置き換え、**[保存]** をクリックします。

        function insert(item, user, request) {
            request.execute();
            // Set timeout to delay the notification, to provide time for the 
            // app to be closed on the device to demonstrate toast notifications
            setTimeout(function() {
                push.apns.send(item.deviceToken, {
                    alert: "Toast: " + item.text,
                    payload: {
                        inAppMessage: "Hey, a new item arrived: '" + item.text + "'"
                    }
                });
            }, 2500);
        }

   	これで、新しい挿入スクリプトが登録されます。このスクリプトは [apns オブジェクト]を使用して、挿入要求で指定されたデバイスにプッシュ通知 (挿入されたテキスト) を送信します。


   	> [WACOM.NOTE] このスクリプトでは、トースト通知を受け取るためにアプリケーションを閉じる時間を与えるために通知の送信を遅らせています。

## アプリケーションでプッシュ通知をテストする

1. **[Run]** を押して、プロジェクトをビルドし、iOS 対応のデバイスでアプリケーションを開始します。**[OK]** をクリックして、プッシュ通知を受け入れます。

  	![][23]

    > [WACOM.NOTE] アプリケーションからのプッシュ通知を明示的に受け入れる必要があります。これが必要であるのは、初めてアプリケーションを実行するときだけです。

2. アプリケーションで、意味のあるテキスト (たとえば、「_A new Mobile Services task_」) を入力し、プラス (**+**) アイコンをクリックします。

  	![][24]

3. 通知が受信されたことを確認し、**[OK]** をクリックして通知を破棄します。

  	![][25]

4. ステップ 2 を繰り返してすぐにアプリケーションを閉じ、次のトーストが表示されることを確認します。

  	![][26]

これで、このチュートリアルは終了です。

## 次のステップ

この単純な例では、ユーザーは挿入したばかりのデータのプッシュ通知を受け取ります。APNS によって使用されるデバイス トークンは、要求のクライアントによってモバイル サービスに提供されます。次のチュートリアル、「[Push notifications to app users (アプリケーション ユーザーへのプッシュ通知)]」では、デバイス トークンを格納するための個別の Devices テーブルを作成し、挿入が発生したときに、すべての格納されているチャネルにプッシュ通知を送信します。

<!-- Anchors. -->
[証明書の署名要求を生成する]: #certificates
[アプリケーションを登録し、プッシュ通知を有効にする]: #register
[アプリケーションのプロビジョニング プロファイルを作成する]: #profile
[モバイル サービスを構成する]: #configure
[プッシュ通知を送信するようにスクリプトを更新する]: #update-scripts
[アプリケーションにプッシュ通知を追加する]: #add-push
[データを挿入して通知を受け取る]: #test
[次のステップ]:#next-steps

<!-- Images. -->
[5]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step5.png
[6]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step6.png
[7]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step7.png

[9]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step9.png
[10]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step10.png
[17]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step17.png
[18]: ./media/mobile-services-ios-get-started-push/mobile-services-selection.png
[19]: ./media/mobile-services-ios-get-started-push/mobile-push-tab-ios.png
[20]: ./media/mobile-services-ios-get-started-push/mobile-push-tab-ios-upload.png
[21]: ./media/mobile-services-ios-get-started-push/mobile-portal-data-tables.png
[22]: ./media/mobile-services-ios-get-started-push/mobile-insert-script-push2.png
[23]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push1-ios.png
[24]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push2-ios.png
[25]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push3-ios.png
[26]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push4-ios.png
[28]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step18.png

[101]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-01.png
[102]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-02.png
[103]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-03.png
[104]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-04.png
[105]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-05.png
[106]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-06.png
[107]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-07.png
[108]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-08.png

[110]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-10.png
[111]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-11.png
[112]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-12.png
[113]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-13.png
[114]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-14.png
[115]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-15.png
[116]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-16.png
[117]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-17.png

<!-- URLs. -->
[Xcode のインストール]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS プロビジョニング ポータル]: http://go.microsoft.com/fwlink/p/?LinkId=272456
[モバイル サービス iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Apple Push Notification Service]: http://go.microsoft.com/fwlink/p/?LinkId=272584
[モバイル サービスの使用]: /ja-jp/develop/mobile/tutorials/get-started-ios
[データの使用]: /ja-jp/develop/mobile/tutorials/get-started-with-data-ios
[認証の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-users-ios
[プッシュ通知の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-push-ios
[Push notifications to app users (アプリケーション ユーザーへのプッシュ通知)]: /ja-jp/develop/mobile/tutorials/push-notifications-to-users-ios
[スクリプトを使用したユーザーの承認]: /ja-jp/develop/mobile/tutorials/authorize-users-in-scripts-ios
[Azure の管理ポータル]: https://manage.windowsazure.com/
[apns オブジェクト]: http://go.microsoft.com/fwlink/p/?LinkId=272333


