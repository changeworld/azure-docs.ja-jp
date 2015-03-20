<properties 
	pageTitle="iOS 用 Azure Mobile Engagement の使用" 
	description="iOS の分析やプッシュ通知で Azure Mobile Engagement を使用する方法を説明します。" 
	services="mobile-engagement" 
	documentationCenter="Mobile" 
	authors="kpiteira" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-ios" 
	ms.devlang="objective-c" 
	ms.topic="article" 
	ms.date="02/11/2015" 
	ms.author="kapiteir" />

# Mobile Engagement の使用

<div class="dev-center-tutorial-selector sublanding"><a href="/documentation/articles/mobile-engagement-windows-store-dotnet-get-started/" title="Windows Store">Windows ストア</a><a href="/documentation/articles/mobile-engagement-windows-phone-get-started/" title="Windows Phone">Windows Phone</a><a href="/documentation/articles/mobile-engagement-ios-get-started/" title="iOS"  class="current">iOS</a><a href="/documentation/articles/mobile-engagement-android-get-started/" title="Android">Android</a></div>

このトピックでは、Azure Mobile Engagement を使用してアプリの使用状況を把握し、iOS アプリケーションのセグメント化されたユーザーにプッシュ通知を送信する方法について説明します。 
このチュートリアルでは、Apple プッシュ通知システム (APNS) を使用して基本データを収集し、プッシュ通知を受信する空の iOS アプリケーションを作成します。完了すると、すべてのデバイスに、またはデバイスのプロパティに基づいた特定のユーザーにプッシュ通知をブロードキャストできるようになります。

このチュートリアルでは、Mobile Engagement を使用した簡単なブロードキャスト シナリオのデモンストレーションを行います。Mobile Engagement を使用してデバイスの特定のユーザーとグループに対応する方法を理解するために、次のチュートリアルも一緒に参照してください。 

このチュートリアルには、次のものが必要です。

+ Xcode。MAC アプリ ストアからインストールできます
+ [Mobile Engagement iOS SDK]
+ プッシュ通知証明書 (.p12)。Apple Dev Center で入手できます

このチュートリアルを完了することは、iOS アプリケーションの他のすべての Mobile Engagement チュートリアルの前提条件です。 

<div class="dev-callout"><strong>注</strong> <p>このチュートリアルを完了するには、アクティブな Azure アカウントが必要です。アカウントがない場合は、無料の試用アカウントを数分で作成することができます。詳細については、<a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fja-jp%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Azure の無料評価版サイト</a>を参照してください。</p></div>

<!--
##<a id="register"></a>Enable Apple Push Notification Service

[WACOM.INCLUDE [Enable Apple Push Notifications](../includes/enable-apple-push-notifications.md)]
-->

##<a id="setup-azme"></a>アプリ用 Mobile Engagement のセットアップ

1. [Azure 管理ポータル]にログオンし、画面の下部にある **[+新規]** をクリックします。

2. **[アプリケーション サービス]**、**[Mobile Engagement]**、**[作成]** の順にクリックします。

   	![][7]

3. 表示されるポップアップで、いくつかのフィールドに情報を入力します。
 
   	![][8]

	1. *アプリケーション名*: アプリケーションの名前を入力します。任意の文字を使用できます。
	2. *プラットフォーム*: アプリのターゲット プラットフォームを選択します (アプリが複数のプラットフォームをターゲットとする場合は、各プラットフォームに対してこのチュートリアルを繰り返します)
	3. *アプリケーション リソース名*:この名前を使用して、API および URL 経由でこのアプリケーションにアクセスします。標準の URL 文字のみを使用してください: 自動的に生成された名前を使うのが確実な方法です。また、この名前は一意である必要があるため、名前の競合を避けるため、プラットフォーム名を追加することをお勧めします。
	4. *場所*:このアプリ (またさらに重要なものとしてそのコレクション (以下を参照)) をホストするデータ センターを選択します。
	5. *コレクション*:アプリケーションを既に作成している場合は、以前に作成したコレクションを選択します。それ以外の場合は、新しいコレクションを選択します。


	完了したら、ボタンをクリックしてアプリの作成を完了します。

4. [Application] タブで作成したアプリをクリック/選択します。
 
   	![][9]

5. [Connection Info] をクリックし、SDK 統合にプットする接続設定を表示します。
 
   	![][10]

6. 最後に、"接続文字列" を書き留めます。これは、アプリケーション コードによりこのアプリを特定する際に必要になります。

   	![][11]

	>[AZURE.TIP] 接続文字列の右側にある "コピー" アイコンを使用して、クリップボードにコピーすることもできます。

##<a id="connecting-app"></a>Mobile Engagement バックエンドにアプリを接続する

このチュートリアルでは、データの収集とプッシュ通知の送信に最低限必要な "基本的な統合" について説明します。統合に関する完全なドキュメントは、[Mobile Engagement iOS SDK ドキュメンテーション]にあります。

統合の例を示すために、Android Studio で基本的なアプリを作成します。

###新しい iOS プロジェクトを作成する

この手順は、既にアプリを作成している場合や、iOS 開発に慣れている場合は省略できます。

1. Xcode を起動し、ポップアップで [Create a new Xcode project] を選択します。

   	![][12]

2. アプリ名、会社名などの識別子を入力します。これらは後で必要になるため、書き留めておいてください。次に [Next] をクリックします。

   	![][13]

3. [Single View Application] をクリックし、[Next] をクリックします。

   	![][14]


Xcode で、Mobile Engagement の統合先のデモ アプリが作成されます。

###プロジェクトに SDK ライブラリを含める

SDK ライブラリのダウンロードと統合

1. [Mobile Engagement iOS SDK] のダウンロード
2. コンピューター上のフォルダーに .tar.gz ファイルを抽出します
3. プロジェクトを右クリックし、[Add files to ...] を選択します

	![][17]

4. SDK を抽出したフォルダーに移動し、 `EngagementSDK` フォルダーを選択して、[OK] を押します

	![][18]

5.  `[Build phases]` タブを開き、 `[Link Binary With Libraries]` メニューで以下に示すフレームワークを追加します

	![][19]


###接続文字列を使って Mobile Engagement バックエンドにアプリを接続する

1. アプリケーション デリゲート実装ファイルの上部にある次のコード行をコピーします

		#import "EngagementAgent.h"

2. アプリの  *[Connection Info]* ページの Azure ポータルに戻り、接続文字列をコピーします

	![][11]

3.  `didFinishLaunchingWithOptions` デリゲートに貼り付けます		

		- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
		{
  			[...]
  			[EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}"];
  			[...]
		}

##Mobile Engagement に画面を送信する

データ送信を開始し、ユーザーがアクティブであることを確認するためには、Mobile Engagement バックエンドに少なくとも 1 つの画面 (アクティビティ) を送信する必要があります。このためには、 `UIViewController` クラスを、SDK が提供する  `EngagementViewController` でオーバーライドします。
これを実行するには、 `ViewController.h` ファイルを開き、 `EngagementViewController.h` をインポートし、以下に示すように、 `ViewController` のスーパー クラスを  `EngagementViewController` に置き換えます。

![][22]

##<a id="monitor"></a>リアル タイム監視機能によりアプリの接続状況を確認する方法

このセクションでは、Mobile Engagement のリアル タイム監視機能を使用して、アプリが Mobile Engagement に接続していることを確認する方法を説明します。

1. Mobile Engagement ポータルに移動します

	Azure ポータルで、このプロジェクトで使用しているアプリにいることを確認し、下部の [関与] ボタンをクリックします。

	![][26]

2. アプリの Engagement ポータルの設定ページが表示されます。[監視] タブをクリックします。

	![][30]

3. モニターは、アプリを起動するデバイスをリアルタイムで表示する準備ができています。

	![][31]

4. Xcode に戻り、シミュレーターまたは接続されたデバイスのいずれかでアプリを起動します。

5. 正常に動作した場合、モニターに 1 つのセッションが表示されます。 

**お疲れ様でした。** このチュートリアルの最初のステップが無事完了し、アプリが Mobile Engagement バックエンドに接続され、データの送信が既に開始されています

6. シミュレーターの [ホーム] ボタンをクリックすると、前に示したようにモニターのセッション数が 0 に戻ります

	![][33]



##<a id="integrate-push"></a>プッシュ通知とアプリ内メッセージの有効化

Mobile Engagement では、キャンペーンとの関連で、プッシュ通知とアプリ内メッセージ機能を使用してユーザーとやり取りすることができます。このモジュールは Mobile Engagement ポータルでは "リーチ" と呼ばれます。
次のセクションで、アプリでこれらを受け取るよう設定します。

### リーチ ライブラリをプロジェクトに追加する

1. プロジェクトを右クリックします
2. `[Add file to ...]` を選択します
3. SDK を抽出したフォルダーに移動します
4.  `EngagementReach` フォルダーを選択します
5. [追加] をクリックします。

### アプリケーション デリゲート

1. 実装ファイルの先頭で、Engagement リーチ モジュールをインポートします

		#import "AEReachModule.h"
	
2.  `application:didFinishLaunchingWithOptions` 内にリーチ モジュールを作成し、それを既存の Engagement 初期化行に渡します

		- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
			AEReachModule * reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
			[EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}" modules:reach, nil];
			[...]
			return YES;
		}	

###アプリで APNS プッシュ通知を受信できるようにする

1. 次の行を `application:didFinishLaunchingWithOptions` メソッドに追加します

		if ([application respondsToSelector:@selector(registerUserNotificationSettings:)]) {
			[application registerUserNotificationSettings:[UIUserNotificationSettings settingsForTypes:(UIUserNotificationTypeBadge | UIUserNotificationTypeSound | UIUserNotificationTypeAlert) categories:nil]];
			[application registerForRemoteNotifications];
		}
		else {

			[application registerForRemoteNotificationTypes:(UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeAlert)];
		}

2. 次のように、 `application:didRegisterForRemoteNotificationsWithDeviceToken` メソッドを追加します
 
		- (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken
		{
 		   [[EngagementAgent shared] registerDeviceToken:deviceToken];
		}

3. 次のように、 `didReceiveRemoteNotification` メソッドを追加します
4. 
		- (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo
		{
		    [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo];
		}

###Mobile Engagement にプッシュ証明書へのアクセスを許可する

Mobile Engagement がユーザーに代わりプッシュ通知を送信できるようにするには、Mobile Engagement に証明書へのアクセスを許可する必要があります。このためには、証明書を Mobile Engagement ポータルに構成および入力します。Apple のドキュメントで説明されているように、.p12 証明書を取得していることを確認してください。

1. Mobile Engagement ポータルに移動します

	Azure ポータルで、このプロジェクトで使用しているアプリにいることを確認し、下部の [関与] ボタンをクリックします。

	![][26]

2. Engagement ポータルの設定ページが表示されます。このページで [ネイティブ プッシュ通知] セクションをクリックし、p12 証明書をアップロードします

	![][27]

3. p12 を選択してアップロードし、パスワードを入力します

	![][28]

4. プロビジョニング プロファイルを追加し、ターゲット デバイス用アプリを構築します

これで準備が完了しました。それでは、この基本的な統合を正しく実行したか検証してみましょう。

##<a id="send"></a>アプリケへの通知の送信方法

プッシュ通知をアプリに送る簡単なプッシュ通知キャンペーンを作成してみましょう。

1. Mobile Engagement ポータルで [リーチ] タブに移動します

2. [新しいアナウンスメント] をクリックして、プッシュ キャンペーンを作成します
	
	![][35]

3. キャンペーンの 1 番目のフィールドを設定します

	![][36]

	1. キャンペーンに好きな名前を付けます
	2. 配信時刻を "アプリ外のみ" として選択: これは、テキストを扱う単純な種類の Apple プッシュ通知です。
	3. 通知のテキストに、プッシュ通知の最初の行に表示されるタイトルをまず入力します
	4. 次に、次の行に表示されるメッセージを入力します

4. スクロール ダウンし、コンテンツ セクションで [通知のみ] を選択します

	![][37]

5. これで、最も基本的なキャンペーンの設定が完了しました。もう一度スクロール ダウンして、キャンペーンを作成して保存してみましょう。
![][38]

6. 最後のステップとして、キャンペーンをアクティブ化します
![][39]

7. デバイスにプッシュ通知が表示されます。

<!-- URLs. -->
[Mobile Engagement iOS SDK]: http://go.microsoft.com/?linkid=9864553
[Mobile Engagement Android SDK に関するドキュメント]: http://go.microsoft.com/?linkid=9874682
[Azure 管理ポータル]: https://manage.windowsazure.com

<!-- Images. -->
[7]: ./media/mobile-engagement-ios-get-started/create-mobile-engagement-app.png
[8]: ./media/mobile-engagement-ios-get-started/create-azme-popup.png
[9]: ./media/mobile-engagement-ios-get-started/select-app.png
[10]: ./media/mobile-engagement-ios-get-started/app-main-page-select-connection-info.png
[11]: ./media/mobile-engagement-ios-get-started/app-connection-info-page.png
[12]: ./media/mobile-engagement-ios-get-started/xcode-new-project.png
[13]: ./media/mobile-engagement-ios-get-started/xcode-project-props.png
[14]: ./media/mobile-engagement-ios-get-started/xcode-simple-view.png
[17]: ./media/mobile-engagement-ios-get-started/xcode-add-files.png
[18]: ./media/mobile-engagement-ios-get-started/xcode-select-engagement-sdk.png
[19]: ./media/mobile-engagement-ios-get-started/xcode-build-phases.png
[22]: ./media/mobile-engagement-ios-get-started/xcode-view-controller.png
[23]: ./media/mobile-engagement-ios-get-started/copy-resources.png
[24]: ./media/mobile-engagement-ios-get-started/paste-resources.png
[25]: ./media/mobile-engagement-ios-get-started/paste-resources.png
[26]: ./media/mobile-engagement-ios-get-started/engage-button.png
[27]: ./media/mobile-engagement-ios-get-started/engagement-portal.png
[28]: ./media/mobile-engagement-ios-get-started/native-push-settings.png
[29]: ./media/mobile-engagement-ios-get-started/api-key.png
[30]: ./media/mobile-engagement-ios-get-started/clic-monitor-tab.png
[31]: ./media/mobile-engagement-ios-get-started/monitor.png
[32]: ./media/mobile-engagement-ios-get-started/launch.png
[33]: ./media/mobile-engagement-ios-get-started/monitor-0.png
[35]: ./media/mobile-engagement-ios-get-started/new-announcement.png
[36]: ./media/mobile-engagement-ios-get-started/campaign-first-params.png
[37]: ./media/mobile-engagement-ios-get-started/campaign-content.png
[38]: ./media/mobile-engagement-ios-get-started/campaign-create.png
[39]: ./media/mobile-engagement-ios-get-started/campaign-activate.png

<!--HONumber=47-->
