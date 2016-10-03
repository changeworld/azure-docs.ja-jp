<properties
	pageTitle="Azure Mobile Engagement iOS SDK の概要 | Microsoft Azure"
	description="Azure Mobile Engagement 用 iOS SDK の最新の更新プログラムと手順"
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
	ms.topic="article"
	ms.date="09/14/2016"
	ms.author="piyushjo" />

#iOS SDK for Azure Mobile Engagement

ここでは、iOS アプリで Azure Mobile Engagement を統合する方法の詳細を確認します。まず試してみる場合は、「[15 分間チュートリアル](mobile-engagement-ios-get-started.md)」をご覧ください

[SDK コンテンツ](mobile-engagement-ios-sdk-content.md)について表示するにはここをクリックします。

##統合手順
1. ここから開始: [iOS アプリで Mobile Engagement を統合する方法](mobile-engagement-ios-integrate-engagement.md)

2. 通知: [リーチ (通知) を iOS アプリに統合する方法](mobile-engagement-ios-integrate-engagement-reach.md)

3. タグ付けプランの実装: [iOS アプリで高度な Mobile Engagement のタグ付け API を使用する方法](mobile-engagement-ios-use-engagement-api.md)


##リリース ノート

### 4\.0.0 (2016 年 9 月 12 日)

-   iOS 10 デバイスで処理されない通知を修正しました。
-   XCode 7 が非推奨になりました。

以前のバージョンについては、「[完全リリース ノート](mobile-engagement-ios-release-notes.md)」をご覧ください。

##アップグレードの手順

既にアプリケーションに以前のバージョンの Mobile Engagement を統合してある場合は、SDK をアップグレードするときに、次の点を考慮する必要があります。

SDK のいくつかのバージョンがない場合は、次の手順に従う必要があります。完全な「[アップグレード手順](mobile-engagement-ios-upgrade-procedure.md)」をご覧ください。

まず、新しいバージョンの SDK ごとに、EngagementSDK フォルダーと EngagementReach フォルダーを置き換える (削除し、xcode で再インポートする) 必要があります。

###3\.0.0 から 4.0.0 に移行

### XCode 8
SDK のバージョン 4.0.0 以降では、XCode 8 が必須となります。

> [AZURE.NOTE] XCode 7 に依存している場合は、[iOS Engagement SDK v3.2.4](https://aka.ms/r6oouh) を使用できます。この以前のバージョンの Reach モジュールには、iOS 10 デバイスで実行したときに、システム通知が処理されないという既知のバグがあります。これを修正するには、次のようにアプリケーション デリゲートで非推奨 API の `application:didReceiveRemoteNotification:` を実装する必要があります。

	- (void)application:(UIApplication*)application
	didReceiveRemoteNotification:(NSDictionary*)userInfo
	{
	    [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:nil];
	}

> [AZURE.IMPORTANT] **この回避策はお勧めできません**。この iOS API は推奨されていないため、(マイナー アップグレードも含め) iOS バージョンの今後のアップグレードでこの動作が変更される可能性があります。できるだけ速やかに XCode 8 に切り替えてください。

#### UserNotifications フレームワーク
`UserNotifications` フレームワークをビルド フェーズに追加する必要があります。

プロジェクト エクスプローラーでプロジェクト ペインを開き、適切なターゲットを選択します。次に、**[Build phases (ビルド フェーズ)]** タブを開き、**[Link Binary With Libraries (バイナリをライブラリとリンク)]** メニューで `UserNotifications.framework` フレームワークを追加し、リンクを `Optional` として設定します。

#### アプリケーションのプッシュ機能
XCode 8 はアプリケーションのプッシュ機能をリセットする場合があります。選択したターゲットの `capability` タブで、この機能を再確認してください。

#### 開発者独自の UNUserNotificationCenterDelegate 実装が既にある場合

SDK には UNUserNotificationCenterDelegate プロトコルの独自の実装があります。これは、iOS 10 以降で実行されているデバイスで Engagement 通知のライフ サイクルを監視するために SDK によって使用されます。UNUserNotificationCenter デリゲートはアプリケーションごとに 1 つしか使用できないため、SDK は開発者のデリゲートを検出すると、独自の実装を使用しなくなります。これは、開発者独自のデリゲートに Engagement ロジックを追加する必要があることを意味します。

これを実現する方法は 2 つあります。

デリゲート呼び出しを SDK に転送します。

	#import <UIKit/UIKit.h>
	#import "EngagementAgent.h"
	#import <UserNotifications/UserNotifications.h>


	@interface MyAppDelegate : NSObject <UIApplicationDelegate, UNUserNotificationCenterDelegate>
	@end

	@implementation MyAppDelegate

	- (void)userNotificationCenter:(UNUserNotificationCenter *)center willPresentNotification:(UNNotification *)notification withCompletionHandler:(void (^)(UNNotificationPresentationOptions options))completionHandler
	{
	  // Your own logic.

	  [[EngagementAgent shared] userNotificationCenterWillPresentNotification:notification withCompletionHandler:completionHandler]
	}

	- (void)userNotificationCenter:(UNUserNotificationCenter *)center didReceiveNotificationResponse:(UNNotificationResponse *)response withCompletionHandler:(void(^)())completionHandler
	{
	  // Your own logic.

	  [[EngagementAgent shared] userNotificationCenterDidReceiveNotificationResponse:response withCompletionHandler:completionHandler]
	}
	@end

または、`AEUserNotificationHandler` クラスから継承します。

	#import "AEUserNotificationHandler.h"
	#import "EngagementAgent.h"

	@interface CustomUserNotificationHandler :AEUserNotificationHandler
	@end

	@implementation CustomUserNotificationHandler

	- (void)userNotificationCenter:(UNUserNotificationCenter *)center willPresentNotification:(UNNotification *)notification withCompletionHandler:(void (^)(UNNotificationPresentationOptions options))completionHandler
	{
	  // Your own logic.

	  [super userNotificationCenter:center willPresentNotification:notification withCompletionHandler:completionHandler];
	}

	- (void)userNotificationCenter:(UNUserNotificationCenter *)center didReceiveNotificationResponse: UNNotificationResponse *)response withCompletionHandler:(void(^)())completionHandler
	{
	  // Your own logic.

	  [super userNotificationCenter:center didReceiveNotificationResponse:response withCompletionHandler:completionHandler];
	}

	@end

> [AZURE.NOTE] 通知が Engagement から送信されたものかどうかを確認するには、`userInfo` ディクショナリをエージェントの `isEngagementPushPayload:` クラス メソッドに渡します。

<!---HONumber=AcomDC_0921_2016-->