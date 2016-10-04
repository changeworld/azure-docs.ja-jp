<properties
	pageTitle="Objective C で IOS の Azure Mobile Engagement を開始する | Microsoft Azure"
	description="iOS アプリの分析やプッシュ通知で Azure Mobile Engagement を使用する方法を説明します。"
	services="mobile-engagement"
	documentationCenter="mobile"
	authors="piyushjo"
	manager="erikre"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="hero-article"
	ms.date="09/14/2016"
	ms.author="piyushjo" />

# Objective C で IOS アプリ の Azure Mobile Engagement を開始する

[AZURE.INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

このトピックでは、Azure Mobile Engagement を使用してアプリの使用状況を把握し、iOS アプリケーションのセグメント化されたユーザーにプッシュ通知を送信する方法について説明します。このチュートリアルでは、Apple プッシュ通知システム (APNS) を使用して基本データを収集し、プッシュ通知を受信する空の iOS アプリケーションを作成します。

このチュートリアルには、次のものが必要です。

+ XCode 8 (Mac アプリ ストアからインストールすることができます)
+ [モバイル エンゲージメント iOS SDK]

このチュートリアルを完了することは、iOS アプリケーションの他のすべての Mobile Engagement チュートリアルの前提条件です。

> [AZURE.NOTE] このチュートリアルを完了するには、アクティブな Azure アカウントが必要です。アカウントがない場合は、無料試用版のアカウントを数分で作成することができます。詳細については、[Azure の無料試用版サイト](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fja-JP%2Fdocumentation%2Farticles%2Fmobile-engagement-ios-get-started)を参照してください。

##<a id="setup-azme"></a>iOS アプリ用に Mobile Engagement を設定する

[AZURE.INCLUDE [ポータルで Mobile Engagement アプリを作成する](../../includes/mobile-engagement-create-app-in-portal.md)]

##<a id="connecting-app"></a>アプリを Mobile Engagement のバックエンドに接続します

このチュートリアルでは、データを収集してプッシュ通知を送信するために必要な最小限のセットである「基本的な統合」について説明します。統合に関する完全なドキュメントは、「[Mobile Engagement iOS SDK 統合](mobile-engagement-ios-sdk-overview.md)」を参照してください。

統合のデモンストレーションを行うために、XCode で基本的なアプリを作成します。

###新しい iOS プロジェクトを作成する

[AZURE.INCLUDE [新しい iOS プロジェクトを作成する](../../includes/mobile-engagement-create-new-ios-app.md)]

###アプリを Mobile Engagement のバックエンドに接続する

1. [Mobile Engagement iOS SDK] をダウンロードします。
2. .tar.gz ファイルをコンピューター上のフォルダーに展開します。
3. プロジェクトを右クリックし、**[Add files to ...]** を選択します。

	![][1]

4. SDK を抽出したフォルダーに移動し、`EngagementSDK` フォルダーを選択して、**[OK]** を押します。

	![][2]

5. **[フェーズの作成]** タブを開き、**[バイナリとライブラリをリンク]** メニューで、次のようにフレームワークを追加します。

	![][3]

6. アプリの **[接続情報]** ページで Azure ポータルに戻り、接続文字列をコピーします。

	![][4]

7. 次のコード行を **AppDelegate.m** ファイルに追加します。

		#import "EngagementAgent.h"

8. ここで、接続文字列を `didFinishLaunchingWithOptions` デリゲートに貼り付けます。

		- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
		{
  			[...]   
  			[EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}"];
  			[...]
		}

9. `setTestLogEnabled` は、問題を識別するための SDK ログを有効にするステートメントです (省略可能)。

##<a id="monitor"></a>リアルタイム監視を有効にする

データを送信してユーザーがアクティブであることを確認するには、少なくとも 1 つの画面 (アクティビティ) を Mobile Engagement のバックエンドに送信する必要があります。

1. **ViewController.h** ファイルを開き、**EngagementViewController.h** をインポートします。

    `# import "EngagementViewController.h"`

2. **ViewController** インターフェイスのスーパー クラスを `EngagementViewController` に置き換えます。

	`@interface ViewController : EngagementViewController`

##<a id="monitor"></a>リアルタイム監視を使用してアプリを接続する

[AZURE.INCLUDE [リアルタイム監視を使用してアプリを接続する](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>プッシュ通知とアプリ内メッセージングを有効にする

Mobile Engagement により、ユーザーと通信を行い、キャンペーンに関するプッシュ通知とアプリ内メッセージングを届けることができます。このモジュールは、Mobile Engagement ポータルで REACH として呼び出されます。次のセクションでは、それらを受信するようにアプリをセットアップします。

### アプリがサイレント プッシュ通知を受信できるようにする

[AZURE.INCLUDE [mobile-engagement-ios-silent-push](../../includes/mobile-engagement-ios-silent-push.md)]

### リーチ ライブラリをプロジェクトに追加する

1. プロジェクトを右クリックします。
2. **[Add file to ...]** を選択します
3. SDK を抽出したフォルダーに移動します。
4. `EngagementReach` フォルダーを選択します
5. **[追加]** をクリックします。

### アプリケーション デリゲートを変更する

1. **AppDeletegate.m** ファイルに戻り、Engagement Reach モジュールをインポートします。

		#import "AEReachModule.h"
		#import <UserNotifications/UserNotifications.h>

2. `application:didFinishLaunchingWithOptions` 内に Reach モジュールを作成し、それをEngagement を初期化する既存の行に渡します。

		- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
			AEReachModule * reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
			[EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}" modules:reach, nil];
			[...]
			return YES;
		}

###アプリで APNS プッシュ通知を受信できるようにする

1. 次の行を `application:didFinishLaunchingWithOptions` メソッドに追加します。

		if (NSFoundationVersionNumber >= NSFoundationVersionNumber_iOS_8_0)
		{
			if (NSFoundationVersionNumber > NSFoundationVersionNumber_iOS_9_x_Max)
			{
				[UNUserNotificationCenter.currentNotificationCenter requestAuthorizationWithOptions:(UNAuthorizationOptionBadge | UNAuthorizationOptionSound | UNAuthorizationOptionAlert) completionHandler:^(BOOL granted, NSError * _Nullable error) {}];
			}else
			{
				[application registerUserNotificationSettings:[UIUserNotificationSettings settingsForTypes:(UIUserNotificationTypeBadge | UIUserNotificationTypeSound | UIUserNotificationTypeAlert)   categories:nil]];
			}
			[application registerForRemoteNotifications];
		}
		else
		{
			[application registerForRemoteNotificationTypes:(UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeAlert)];
		}

2. 次のように、`application:didRegisterForRemoteNotificationsWithDeviceToken` メソッドを追加します。

		- (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken
		{
 			[[EngagementAgent shared] registerDeviceToken:deviceToken];
			NSLog(@"Registered Token: %@", deviceToken);
		}

3. 次のように、`didFailToRegisterForRemoteNotificationsWithError` メソッドを追加します。

		- (void)application:(UIApplication*)application didFailToRegisterForRemoteNotificationsWithError:(NSError*)error
		{
		   
		   NSLog(@"Failed to get token, error: %@", error);
		}

4. 次のように、`didReceiveRemoteNotification:fetchCompletionHandler` メソッドを追加します。

		- (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult result))handler
		{
			[[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:handler];
		}

[AZURE.INCLUDE [mobile-engagement-ios-send-push-push](../../includes/mobile-engagement-ios-send-push.md)]

<!-- URLs. -->
[Mobile Engagement iOS SDK]: http://aka.ms/qk2rnj
[モバイル エンゲージメント iOS SDK]: http://aka.ms/qk2rnj

<!-- Images. -->
[1]: ./media/mobile-engagement-ios-get-started/xcode-add-files.png
[2]: ./media/mobile-engagement-ios-get-started/xcode-select-engagement-sdk.png
[3]: ./media/mobile-engagement-ios-get-started/xcode-build-phases.png
[4]: ./media/mobile-engagement-ios-get-started/app-connection-info-page.png

<!---HONumber=AcomDC_0928_2016-->