<properties
	pageTitle="Objective C で IOS の Azure Mobile Engagement を開始する"
	description="iOS アプリの分析やプッシュ通知で Azure Mobile Engagement を使用する方法を説明します。"
	services="mobile-engagement"
	documentationCenter="Mobile"
	authors="MehrdadMzfr"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="hero-article"
	ms.date="08/05/2015"
	ms.author="MehrdadMzfr"/>

# Objective C で IOS アプリ の Azure Mobile Engagement を開始する

> [AZURE.SELECTOR]
- [Universal Windows](mobile-engagement-windows-store-dotnet-get-started.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-get-started.md)
- [iOS | Obj C](mobile-engagement-ios-get-started.md)
- [iOS | Swift](mobile-engagement-ios-swift-get-started.md)
- [Android](mobile-engagement-android-get-started.md)
- [Cordova](mobile-engagement-cordova-get-started.md)

このトピックでは、Azure Mobile Engagement を使用してアプリの使用状況を把握し、iOS アプリケーションのセグメント化されたユーザーにプッシュ通知を送信する方法について説明します。このチュートリアルでは、Apple プッシュ通知システム (APNS) を使用して基本データを収集し、プッシュ通知を受信する空の iOS アプリケーションを作成します。このチュートリアルを完了すると、デバイスのプロパティに基づいて、すべてのデバイスまたは特定のターゲット ユーザーに、プッシュ通知をブロードキャストできるようになります。

このチュートリアルでは、Mobile Engagement を使用した簡単なブロードキャスト シナリオのデモンストレーションを行います。Mobile Engagement を使用してデバイスの特定のユーザーとグループに対応する方法を理解するために、次のチュートリアルも一緒にご覧ください。

このチュートリアルには、次のものが必要です。

+ XCode。Mac アプリ ストアからインストールすることができます。
+ [Mobile Engagement iOS SDK]
+ プッシュ通知証明書 (.p12)。Apple Dev Center で入手できます

このチュートリアルを完了することは、iOS アプリケーションの他のすべての Mobile Engagement チュートリアルの前提条件です。

> [AZURE.IMPORTANT]このチュートリアルを完了することは、その他すべての IOS アプリの Mobile Engagement チュートリアルの前提条件であり、これを完了するには、アクティブな Azure アカウントが必要です。アカウントがない場合は、無料試用版のアカウントを数分で作成することができます。詳細については、<a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fja-JP%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Azure の無料試用版サイト</a>をご覧ください。

<!--
##<a id="register"></a>Enable Apple Push Notification Service

[WACOM.INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]
-->

##<a id="setup-azme"></a>アプリ用の Mobile Engagement の設定

1. Azure ポータルにサインインし、画面の下部にある **[+新規]** をクリックします。

2. **[App Services]**、**[Mobile Engagement]**、**[作成]** の順にクリックします。

	![][7]

3. 表示されたポップアップに、次の情報を入力します。

   ![][8]

   - **アプリケーション名**: アプリケーションの名前を入力します。自由に任意の文字を使用してください。
   - **プラットフォーム**: アプリ向けのターゲット プラットフォーム (**iOS**) を選択します (アプリが複数のプラットフォームをターゲットにしている場合は、各プラットフォームに対してこのチュートリアルを繰り返します)。
   - **アプリケーション リソース名**: この名前を使用して、API と URL を通じてこのアプリケーションにアクセスします。従来の URL の文字のみを使用する必要があります。自動生成された名前は、ベースとなる名前として役立ちます。また、この名前は一意である必要があるので、重複を避けるために、プラットフォーム名を付加することをお勧めします。
   - **場所**: このアプリ (さらに重要なそのコレクション) がホストされるデータ センターを選択します。
   - **コレクション**: アプリケーションを既に作成済みの場合は以前に作成した**コレクション**を選択し、そうでない場合は**新しいコレクション**を選択します。
   - **コレクション名**。 これは、グループのアプリケーションを表します。すべてのアプリを 1 つのグループに含めることで、それらをメトリック集計できます。該当する場合は、ここで会社名や部門を使用する必要があります。

4. **[アプリケーション]** タブで、作成したアプリを選択します。

5. **[接続情報]** をクリックして、モバイル アプリに統合する SDK に組み込む接続設定を表示します。

	![][10]

6. **[接続文字列]** をコピーします - これは、アプリケーション コード内で、このアプリケーションを識別し、Phone アプリから Mobile Engagement サービスに接続するために必要なものです。

	![][11]

##<a id="connecting-app"></a>アプリを Mobile Engagement のバックエンドに接続します

このチュートリアルでは、データを収集してプッシュ通知を送信するために必要な最小限のセットである「基本的な統合」について説明します。統合に関する完全なドキュメントは、Mobile Engagement iOS SDK ドキュメンテーションにあります。

統合のデモンストレーションを行うために、XCode で基本的なアプリを作成します。

###新しい iOS プロジェクトを作成する

この手順は、既にアプリを作成している場合や、iOS 開発に慣れている場合は省略できます。

1. Xcode を起動し、ポップアップで **[Create a new Xcode project]** を選択します。

	![][12]

2. **[Single View Application]** を選択し、**[次へ]** をクリックします。

	![][14]

3. **[製品名]**、**[組織名]**、 **[組織 ID]** に入力します。**[言語]** ボックスで **[Objective C]** を選択していることを確認します。

	![][13]

Xcode により Mobile Engagement の統合先のデモ アプリが作成されます。

###アプリを Mobile Engagement のバックエンドに接続する

1. [Mobile Engagement iOS SDK] をダウンロードします。
2. .tar.gz ファイルをコンピューター上のフォルダーに展開します。
3. プロジェクトを右クリックし、**[Add files to ...]** を選択します

	![][17]

4. SDK を抽出したフォルダーに移動し、 `EngagementSDK` フォルダーを選択して、**[OK]** を押します。

	![][18]

5. **[フェーズの作成]** タブを開き、**[バイナリとライブラリをリンク]** メニューで以下に示すフレームワークを追加します

	![][19]

6. アプリの **[接続情報]** ページで Azure ポータルに戻り、接続文字列をコピーします。

	![][11]

7. アプリケーション デリゲート実装ファイルを開き、次のコード行を追加します

		#import "EngagementAgent.h"

8. `didFinishLaunchingWithOptions` デリゲートで接続文字列を貼り付けます。

		- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
		{
  			[...]
  			[EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}"];
  			[...]
		}

##<a id="monitor"></a>リアルタイム監視を有効にする

データを送信してユーザーがアクティブであることを確認するには、少なくとも 1 つの画面 (アクティビティ) を Mobile Engagement のバックエンドに送信する必要があります。

- **ViewController.h** ファイルを開き、**EngagementViewController.h** をインポートし、次のように **ViewController** インターフェイスのスーパークラスを **EngagementViewController** で置換します。

![][22]

###アプリがリアルタイム監視に接続していることを確認する

このセクションでは、Mobile Engagement のリアルタイム監視機能を使用して、アプリが Mobile Engagement のバックエンドに接続していることを確認する方法について説明します。

1. Mobile Engagement ポータルに移動します

	Azure ポータルで、このプロジェクトで使用しているアプリにいることを確認し、下部の **[関与]** ボタンをクリックします。

	![][26]

2. アプリの Engagement ポータルの **[設定]** ページが表示されます。そこから **[監視]** タブをクリックします。

	![][30]

3. モニターにはアプリを起動するデバイスがリアルタイムで表示されます。

	![][31]

4. Xcode に戻り、シミュレーターまたは接続されたデバイスのいずれかでアプリを起動します。

5. 正常に動作している場合、モニターに 1 つのセッションが表示されます。

**ご利用ありがとうございます。** チュートリアルの最初のステップが無事完了し、アプリが Mobile Engagement バックエンドに接続され、データの送信が既に開始されています。

6. シミュレーターの **[ホーム]** ボタンをクリックし、上の図のようにモニターのセッション数が 0 に戻ります。

	![][33]

##<a id="integrate-push"></a>プッシュ通知とアプリ内メッセージングを有効にする

Mobile Engagement により、ユーザーと通信を行い、キャンペーンに関するプッシュ通知とアプリ内メッセージングを届けることができます。このモジュールは、Mobile Engagement ポータルで REACH として呼び出されます。次のセクションでは、それらを受信するようにアプリをセットアップします。

### アプリがサイレント プッシュ通知を受信できるようにする

[AZURE.INCLUDE [mobile-engagement-ios-silent-push](../../includes/mobile-engagement-ios-silent-push.md)]


### リーチ ライブラリをプロジェクトに追加する

1. プロジェクトを右クリックします。
2. **[Add file to]** を選択します
3. SDK を抽出したフォルダーに移動します。
4. `EngagementReach` フォルダーを選択します。
5. **[追加]** をクリックします。

### アプリケーション デリゲートを変更する

1. 実装ファイルの上部で、Engagement Reach モジュールをインポートします。

		#import "AEReachModule.h"

2. **application:didFinishLaunchingWithOptions** 内に Reach モジュールを作成し、それを既存の Engagement 初期化行に渡します。

		- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
			AEReachModule * reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
			[EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}" modules:reach, nil];
			[...]
			return YES;
		}

###アプリで APNS プッシュ通知を受信できるようにする

1. 次を **application:didFinishLaunchingWithOptions** メソッドに追加します。

		if ([application respondsToSelector:@selector(registerUserNotificationSettings:)]) {
			[application registerUserNotificationSettings:[UIUserNotificationSettings settingsForTypes:(UIUserNotificationTypeBadge | UIUserNotificationTypeSound | UIUserNotificationTypeAlert) categories:nil]];
			[application registerForRemoteNotifications];
		}
		else {

			[application registerForRemoteNotificationTypes:(UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeAlert)];
		}

2. 次のように **application:didRegisterForRemoteNotificationsWithDeviceToken** メソッドを追加します。

		- (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken
		{
 			[[EngagementAgent shared] registerDeviceToken:deviceToken];
		}

3. 次のように **didReceiveRemoteNotification:fetchCompletionHandler** メソッドを追加します。

		- (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult result))handler
		{
			[[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:handler];
		}

###Mobile Engagement アクセスをプッシュ証明書に与える

Mobile Engagement がユーザーに代わりプッシュ通知を送信できるようにするには、Mobile Engagement に証明書へのアクセスを許可する必要があります。このためには、証明書を Mobile Engagement ポータルに構成および入力します。Apple ドキュメントで説明されているように、.p12 証明書を取得していることを確認してください。

1. Mobile Engagement ポータルに移動します。このプロジェクト用に使用しているアプリを対象にしていることを確認し、下部にある **[関与]** ボタンをクリックします。

	![][26]

2. Engagement ポータルの **[設定]** ページが表示されます。このページで **[ネイティブ プッシュ通知]** セクションをクリックし、p12 証明書をアップロードします

	![][27]

3. p12 を選択してアップロードし、パスワードを入力します。

	![][28]

4. プロビジョニング プロファイルを追加し、ターゲット デバイス用アプリを構築します。

これで準備が完了しました。それでは、この基本的な統合が正しく設定されているかどうかを検証してみましょう。

##<a id="send"></a>アプリへ通知を送信する

プッシュ通知をアプリに送る簡単なプッシュ通知キャンペーンを作成してみましょう。

1. Mobile Engagement ポータルの [**リーチ**] タブに移動します。

2. **[新しいお知らせ]** をクリックして、プッシュ通知キャンペーンを作成します。

	![][35]

3. キャンペーンの 1 番目のフィールドを設定します。

	![][36]

	- 	キャンペーンに任意の名前を付けます。
	- 	**[配信時刻]** には **[アプリ外のみ]** を選択します。これはテキストを扱う単純な種類の Apple プッシュ通知です。
	- 	通知のテキストに、プッシュ通知の最初の行に表示されるタイトルをまず入力します。
	- 	次に、2 番目の行になるメッセージを入力します。


4. 下にスクロールし、**[コンテンツ]** セクションで [通知のみ] を選択します。

	![][37]

5. 最も基本的なキャンペーンの設定が完了しました。もう一度下にスクロールし、キャンペーンを作成して保存します。![][38]

6. 最後の手順で、キャンペーンをアクティブ化します。![][39]

7. デバイスにプッシュ通知が表示されます。

<!-- URLs. -->
[Mobile Engagement iOS SDK]: http://go.microsoft.com/?linkid=9864553
[Mobile Engagement Android SDK documentation]: http://go.microsoft.com/?linkid=9874682

<!-- Images. -->
[7]: ./media/mobile-engagement-ios-get-started/create-mobile-engagement-app.png
[8]: ./media/mobile-engagement-ios-get-started/create-azme-popup.png
[10]: ./media/mobile-engagement-ios-get-started/app-main-page-select-connection-info.png
[11]: ./media/mobile-engagement-ios-get-started/app-connection-info-page.png
[12]: ./media/mobile-engagement-ios-get-started/xcode-new-project.png
[13]: ./media/mobile-engagement-ios-get-started/xcode-project-props.png
[14]: ./media/mobile-engagement-ios-get-started/xcode-simple-view.png
[17]: ./media/mobile-engagement-ios-get-started/xcode-add-files.png
[18]: ./media/mobile-engagement-ios-get-started/xcode-select-engagement-sdk.png
[19]: ./media/mobile-engagement-ios-get-started/xcode-build-phases.png
[22]: ./media/mobile-engagement-ios-get-started/xcode-view-controller.png
[26]: ./media/mobile-engagement-ios-get-started/engage-button.png
[27]: ./media/mobile-engagement-ios-get-started/engagement-portal.png
[28]: ./media/mobile-engagement-ios-get-started/native-push-settings.png
[30]: ./media/mobile-engagement-ios-get-started/clic-monitor-tab.png
[31]: ./media/mobile-engagement-ios-get-started/monitor.png
[33]: ./media/mobile-engagement-ios-get-started/monitor-0.png
[35]: ./media/mobile-engagement-ios-get-started/new-announcement.png
[36]: ./media/mobile-engagement-ios-get-started/campaign-first-params.png
[37]: ./media/mobile-engagement-ios-get-started/campaign-content.png
[38]: ./media/mobile-engagement-ios-get-started/campaign-create.png
[39]: ./media/mobile-engagement-ios-get-started/campaign-activate.png

<!---HONumber=August15_HO9-->