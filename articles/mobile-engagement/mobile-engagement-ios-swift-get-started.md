<properties 
	pageTitle="Swift で IOS の Azure モバイル エンゲージメントを開始する" 
	description="iOS アプリ の分析やプッシュ通知で Azure モバイル エンゲージメントを使用する方法を説明します。"
	services="mobile-engagement" 
	documentationCenter="Mobile" 
	authors="piyushjo" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-ios" 
	ms.devlang="swift" 
	ms.topic="article" 
	ms.date="04/30/2015" 
	ms.author="piyushjo" />

# Swift で IOS アプリ の Azure モバイル エンゲージメントを開始する

> [AZURE.SELECTOR]
- [Windows Universal](mobile-engagement-windows-store-dotnet-get-started.md) 
- [Windows Phone Silverlight](mobile-engagement-windows-phone-get-started.md) 
- [iOS - Obj C](mobile-engagement-ios-get-started.md) 
- [iOS - Swift](mobile-engagement-ios-swift-get-started.md)
- [Android](mobile-engagement-android-get-started.md) 

このトピックでは、Azure Mobile Engagement を使用してアプリの使用状況を把握し、iOS アプリケーションのセグメント化されたユーザーにプッシュ通知を送信する方法について説明します。このチュートリアルでは、Apple プッシュ通知システム (APNS) を使用して基本データを収集し、プッシュ通知を受信する空の iOS アプリケーションを作成します。完了すると、デバイス プロパティに基づいて、すべてのデバイスまたは特定のターゲット ユーザーに、プッシュ通知をブロードキャストできるようになります。

このチュートリアルでは、モバイル エンゲージメントを使用した簡単なブロードキャスト シナリオのデモンストレーションを行います。モバイル エンゲージメントを使用してデバイスの特定のユーザーとグループに対応する方法を理解するために、次のチュートリアルも一緒にご覧ください。

このチュートリアルには、次のものが必要です。

+ XCode。Mac アプリ ストアからインストールすることができます。
+ [モバイル エンゲージメント iOS SDK]
+ プッシュ通知証明書 (.p12)。Apple Dev Center で入手できます

このチュートリアルを完了することは、iOS アプリケーションの他のすべての Mobile Engagement チュートリアルの前提条件です。

> [AZURE.IMPORTANT]このチュートリアルを完了することは、その他すべての IOS アプリのモバイル エンゲージメント チュートリアルの前提条件であり、これを完了するには、アクティブな Azure アカウントが必要です。アカウントがない場合は、無料の試用アカウントを数分で作成することができます。詳細については、「<a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fja-jp%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Azure の無料評価版サイト</a>」をご覧ください。

<!--
##<a id="register"></a>Enable Apple Push Notification Service

[WACOM.INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]
-->

##<a id="setup-azme"></a>アプリ用のモバイル エンゲージメントの設定

1. Azure 管理ポータルにログオンし、画面の下部にある **[+新規]** をクリックします。

2. **[アプリ サービス]**、**[モバイル エンゲージメント]**、**[作成]** の順にクリックします。

   	![][7]

3. 表示されたポップアップに、次の情報を入力します。
 
   	![][8]

	- **アプリケーション名**: アプリケーションの名前を入力します。自由に任意の文字を使用してください。
	- **プラットフォーム**: アプリ向けのターゲット プラットフォームを選択します (アプリが複数のプラットフォームをターゲットにしている場合は、各プラットフォームに対してこのチュートリアルを繰り返します)。 
	- **アプリケーション リソース名**: この名前を使用して、API と URL を通じてこのアプリケーションにアクセスします。従来の URL の文字のみを使用する必要があります。自動生成された名前は、ベースとなる名前として役立ちます。また、この名前は一意である必要があるので、重複を避けるために、プラットフォーム名を付加することをお勧めします。
	- **場所**: このアプリ (さらに重要なそのコレクション) がホストされるデータ センターを選択します。 
	- **コレクション**: アプリケーションを既に作成済みの場合は以前に作成したコレクションを選択し、そうでない場合は新しいコレクションを選択します。
	- **コレクション名**。 これは、グループのアプリケーションを表します。すべてのアプリを 1 つのグループに含めることで、それらをメトリック集計できます。該当する場合は、ここで会社名や部門を使用する必要があります。

4. **[アプリケーション]** タブで、作成したアプリを選択します。

5. **[接続情報]** をクリックして、モバイル アプリに統合する SDK に組み込む接続設定を表示します。
 
   	![][10]

6. **[接続文字列]** をコピーします - これは、アプリケーション コード内で、このアプリケーションを識別し、Phone アプリからモバイル エンゲージメント サービスに接続するために必要なものです。

   	![][11]

##<a id="connecting-app"></a>アプリをモバイル エンゲージメントのバックエンドに接続する

このチュートリアルでは、データを収集してプッシュ通知を送信するために必要な最小限のセットである「基本的な統合」について説明します。統合に関する完全なドキュメントは、[Mobile Engagement iOS SDK ドキュメンテーション]にあります。

統合のデモンストレーションを行うために、XCode で基本的なアプリを作成します。

###新しい iOS プロジェクトを作成する

この手順は、既にアプリを作成している場合や、iOS 開発に慣れている場合は省略できます。

1. Xcode を起動し、ポップアップで **「Create a new Xcode project」** を選択します。

   	![][12]

2. **[Single View Application]** を選択し、[次へ] をクリックします。

   	![][14]

3. **製品名**、**組織名**、 **組織 ID** を入力します。言語で **[Swift]** を選択していることを確認します。

   	![][40]

Xcode で、Mobile Engagement の統合先のデモ アプリが作成されます。

###アプリをモバイル エンゲージメントのバックエンドに接続する 

1. [Mobile Engagement iOS SDK] のダウンロード
2. コンピューター上のフォルダーに .tar.gz ファイルを抽出します
3. プロジェクトを右クリックし、[Add files to ...] を選択します

	![][17]

4. SDK を抽出したフォルダーに移動し、 `EngagementSDK` フォルダーを選択して、[OK] を押します。

	![][18]

5. `Build Phases` タブを開き、`Link Binary With Libraries` メニューで次に示すように、フレームワークを追加します。

	![][19]

6. ブリッジ ヘッダーを作成して、[作成] > [新規] > [ファイル] > [iOS] > [ソース] > [ヘッダー ファイル] を選択して SDK's Objective C API を使用できるようにします。

	![][41]

7. ブリッジ ヘッダー ファイルを編集して、AzME OBJECTIVE-C コードを Swift コードに公開し、次の imports を追加します。

		/* Mobile Engagement Agent */
		#import "AEModule.h"
		#import "AEPushDelegate.h"
		#import "AEPushMessage.h"
		#import "AEStorage.h"
		#import "EngagementAgent.h"
		#import "EngagementTableViewController.h"
		#import "EngagementViewController.h"
		#import "AEIdfaProvider.h"

8. [ビルドの設定] で、Swift コンパイラの Objective-C Bridging Header のビルド設定で Swift コンパイラ - コード生成にこのヘッダーへのパスがあることを確認します。パスの例を次に示します。 **$(SRCROOT)/MySuperApp/MySuperApp-Bridging-Header.h (パスによる)**

9. アプリの *[接続情報]* ページで Azure ポータルに戻り、[接続文字列] をコピーします。

	![][11]

10. `didFinishLaunchingWithOptions` 認証で、接続文字列を貼り付けます。

		func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject: AnyObject]?) -> Bool
		{
  			[...]
				EngagementAgent.init("Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}")
  			[...]
		}

##<a id="monitor"></a>リアルタイム監視の有効化

データを送信してユーザーがアクティブであることを確認するには、少なくとも 1 つの画面 (アクティビティ) をモバイル エンゲージメントのバックエンドに送信する必要があります。

- `ViewController.h` ファイルを開き、`EngagementViewController.h` をインポートし、`ViewController` インターフェイスのスーパー クラスを `EngagementViewController` に置き換えます。

###アプリがリアルタイム監視に接続していることを確認する

このセクションでは、モバイル エンゲージメントのリアルタイム監視機能を使用して、アプリがモバイル エンゲージメントのバックエンドに接続していることを確認する方法を説明します。

1. Mobile Engagement ポータルに移動します

	Azure ポータルで、このプロジェクトで使用しているアプリにいることを確認し、下部の [関与] ボタンをクリックします。

	![][26]

2. アプリの Engagement ポータルの設定ページが表示されます。[監視] タブをクリックします。

	![][30]

3. モニターは、アプリを起動するデバイスをリアルタイムで表示する準備ができています。

	![][31]

4. Xcode に戻り、シミュレーターまたは接続されたデバイスのいずれかでアプリを起動します。

5. 正常に動作している場合、モニターに 1 つのセッションが表示されます。

**ご利用ありがとうございます。** チュートリアルの最初のステップが無事完了し、アプリが Mobile Engagement バックエンドに接続され、データの送信が既に開始されています

6. シミュレーターの [ホーム] ボタンをクリックすると、前に示したようにモニターのセッション数が 0 に戻ります

	![][33]

##<a id="integrate-push"></a>プッシュ通知とアプリ内メッセージングを有効にする

モバイル エンゲージメントにより、ユーザーと通信を行い、キャンペーンのコンテキストに関するプッシュ通知とアプリ内メッセージングを届けることができます。このモジュールは、モバイル エンゲージメント ポータルで REACH として呼び出されます。次のセクションでは、それらを受信するためにアプリをセットアップします。

### リーチ ライブラリをプロジェクトに追加する

1. プロジェクトを右クリックします
2. SELECT`Add file to ...`
3. SDK を抽出したフォルダーに移動します
4. `EngagementReach` フォルダーを選択します。
5. [追加] をクリックします。
6. ブリッジ ヘッダー ファイルを編集して、AzME Objective-C Reach ヘッダーを公開し、次の imports を追加します。

		/* Mobile Engagement Reach */
		#import "AE_TBXML.h"
		#import "AEAnnouncementViewController.h"
		#import "AEAutorotateView.h"
		#import "AEContentViewController.h"
		#import "AEDefaultAnnouncementViewController.h"
		#import "AEDefaultNotifier.h"
		#import "AEDefaultPollViewController.h"
		#import "AEInteractiveContent.h"
		#import "AENotificationView.h"
		#import "AENotifier.h"
		#import "AEPollViewController.h"
		#import "AEReachAbstractAnnouncement.h"
		#import "AEReachAnnouncement.h"
		#import "AEReachContent.h"
		#import "AEReachDataPush.h"
		#import "AEReachDataPushDelegate.h"
		#import "AEReachModule.h"
		#import "AEReachNotifAnnouncement.h"
		#import "AEReachPoll.h"
		#import "AEViewControllerUtil.h"
		#import "AEWebAnnouncementJsBridge.h"

### アプリケーション デリゲートを変更する

1. `didFinishLaunchingWithOptions` 内に reach モジュールを作成し、それを既存の Engagement 初期化行に渡します

		func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject: AnyObject]?) -> Bool {
			let reach = AEReachModule.moduleWithNotificationIcon(UIImage(named:"icon.png")) as! AEReachModule
			EngagementAgent.init("Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}", modulesArray:[reach])
			[...]
			return true
		}	

###アプリで APNS プッシュ通知を受信できるようにする
1. 次の行を `didFinishLaunchingWithOptions` メソッドに追加します。

		if application.respondsToSelector("registerUserNotificationSettings:")
		{
			application.registerUserNotificationSettings(UIUserNotificationSettings(
			forTypes: (UIUserNotificationType.Alert | UIUserNotificationType.Badge | UIUserNotificationType.Sound),
			categories: nil))
			application.registerForRemoteNotifications()
		}
		else
		{
			application.registerForRemoteNotificationTypes(UIRemoteNotificationType.Alert | UIRemoteNotificationType.Badge | UIRemoteNotificationType.Sound)
		}

2. 次のように、`didRegisterForRemoteNotificationsWithDeviceToken` メソッドを追加します。

		func application(application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: NSData)
		{
			EngagementAgent.shared().registerDeviceToken(deviceToken)
		}

3. 次のように、`didReceiveRemoteNotification` メソッドを追加します。

		func application(application: UIApplication, didReceiveRemoteNotification userInfo: [NSObject : AnyObject])
		{
			EngagementAgent.shared().applicationDidReceiveRemoteNotification(userInfo)
		}

###Mobile Engagement にプッシュ証明書へのアクセスを許可する

Mobile Engagement がユーザーに代わりプッシュ通知を送信できるようにするには、Mobile Engagement に証明書へのアクセスを許可する必要があります。このためには、証明書を Mobile Engagement ポータルに構成および入力します。Apple のドキュメントで説明されているように、.p12 証明書を取得していることを確認してください。

1. モバイル エンゲージメント ポータルに移動します。このプロジェクト用に使用しているアプリを対象にしていることを確認し、下部にある [関与] ボタンをクリックします。

	![][26]

2. Engagement ポータルの設定ページが表示されます。このページで [ネイティブ プッシュ通知] セクションをクリックし、p12 証明書をアップロードします

	![][27]

3. p12 を選択してアップロードし、パスワードを入力します

	![][28]

4. プロビジョニング プロファイルを追加し、ターゲット デバイス用アプリを構築します

これで準備が完了しました。それでは、この基本的な統合を正しく実行したか検証してみましょう。

##<a id="send"></a>アプリへ通知を送信する

プッシュ通知をアプリに送る簡単なプッシュ通知キャンペーンを作成してみましょう。

1. Mobile Engagement ポータルで [リーチ] タブに移動します

2. **[新しいお知らせ]** をクリックして、プッシュ キャンペーンを作成します。
	
	![][35]

3. キャンペーンの 1 番目のフィールドを設定します

	![][36]

	- 	キャンペーンに希望する任意の名前を付けます。
	- 	配信時刻は "アプリ外のみ" を選択: これは、テキストを扱う単純な種類の Apple プッシュ通知です。
	- 	通知のテキストに、プッシュ通知の最初の行に表示されるタイトルをまず入力します
	- 	次に、次の行に表示されるメッセージを入力します


4. スクロール ダウンし、コンテンツ セクションで [通知のみ] を選択します

	![][37]

5. 最も基本的なキャンペーンの設定が完了したので、もう一度下にスクロールし、キャンペーンを作成して保存します。![][38]

6. 最後の手順で、キャンペーンをアクティブ化します。![][39]

7. デバイスにプッシュ通知が表示されます。

<!-- URLs. -->
[Mobile Engagement iOS SDK]: http://go.microsoft.com/?linkid=9864553
[モバイル エンゲージメント iOS SDK]: http://go.microsoft.com/?linkid=9864553
[Mobile Engagement Android SDK documentation]: http://go.microsoft.com/?linkid=9874682

<!-- Images. -->
[7]: ./media/mobile-engagement-ios-swift-get-started/create-mobile-engagement-app.png
[8]: ./media/mobile-engagement-ios-swift-get-started/create-azme-popup.png
[10]: ./media/mobile-engagement-ios-swift-get-started/app-main-page-select-connection-info.png
[11]: ./media/mobile-engagement-ios-swift-get-started/app-connection-info-page.png
[12]: ./media/mobile-engagement-ios-swift-get-started/xcode-new-project.png
[13]: ./media/mobile-engagement-ios-get-started/xcode-project-props.png
[14]: ./media/mobile-engagement-ios-swift-get-started/xcode-simple-view.png
[17]: ./media/mobile-engagement-ios-swift-get-started/xcode-add-files.png
[18]: ./media/mobile-engagement-ios-swift-get-started/xcode-select-engagement-sdk.png
[19]: ./media/mobile-engagement-ios-swift-get-started/xcode-build-phases.png
[22]: ./media/mobile-engagement-ios-get-started/xcode-view-controller.png
[26]: ./media/mobile-engagement-ios-swift-get-started/engage-button.png
[27]: ./media/mobile-engagement-ios-swift-get-started/engagement-portal.png
[28]: ./media/mobile-engagement-ios-swift-get-started/native-push-settings.png
[30]: ./media/mobile-engagement-ios-swift-get-started/clic-monitor-tab.png
[31]: ./media/mobile-engagement-ios-swift-get-started/monitor.png
[33]: ./media/mobile-engagement-ios-swift-get-started/monitor-0.png
[35]: ./media/mobile-engagement-ios-swift-get-started/new-announcement.png
[36]: ./media/mobile-engagement-ios-swift-get-started/campaign-first-params.png
[37]: ./media/mobile-engagement-ios-swift-get-started/campaign-content.png
[38]: ./media/mobile-engagement-ios-swift-get-started/campaign-create.png
[39]: ./media/mobile-engagement-ios-swift-get-started/campaign-activate.png
[40]: ./media/mobile-engagement-ios-swift-get-started/SwiftSelection.png
[41]: ./media/mobile-engagement-ios-swift-get-started/AddHeaderFile.png

<!--HONumber=54--> 