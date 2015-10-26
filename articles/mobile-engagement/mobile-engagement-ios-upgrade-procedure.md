<properties
	pageTitle="Azure Mobile Engagement iOS SDK のアップグレード手順"
	description="Azure Mobile Engagement 用 iOS SDK の最新の更新プログラムと手順"
	services="mobile-engagement"
	documentationCenter="mobile"
	authors="MehrdadMzfr"
	manager="dwrede"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="08/05/2015"
	ms.author="MehrdadMzfr" />

#アップグレードの手順

既にアプリケーションに以前のバージョンのモバイル エンゲージメントを統合してある場合は、SDK をアップグレードするときに、次の点を考慮する必要があります。

まず、新しいバージョンの SDK ごとに、EngagementSDK フォルダーと EngagementReach フォルダーを置き換える (削除し、xcode で再インポートする) 必要があります。

##2\.0.0 から 3.0.0 に移行
iOS 4.X のサポートが終了。このバージョンから、アプリケーションのデプロイ ターゲットは iOS 6 以降である必要があります。

アプリケーションでリーチを使用している場合は、リモート通知を受け取れるように、`remote-notification` 値を Info.plist ファイル内の `UIBackgroundModes` 配列に追加する必要があります。

アプリケーション デリゲートのメソッド `application:didReceiveRemoteNotification:` は、`application:didReceiveRemoteNotification:fetchCompletionHandler:` に置き換える必要があります。

AEPushDelegate.h インターフェイスは廃止されるため、すべての参照を削除する必要があります。これには、`[[EngagementAgent shared] setPushDelegate:self]` の削除や、アプリケーション デリゲートからのデリゲート メソッドの削除も含まれます。

	-(void)willRetrieveLaunchMessage;
	-(void)didFailToRetrieveLaunchMessage;
	-(void)didReceiveLaunchMessage:(AEPushMessage*)launchMessage;

##1\.16.0 から 2.0.0 に移行
Azure Mobile Engagement を使用するアプリに Capptain SAS によって提供される Capptain サービスから SDK の統合を移行する方法を次に示します。以前のバージョンから移行する場合は、Capptain web サイトをご覧のうえ、まず 1.16 に移行し、次の手順を適用してください。

>[Azure.IMPORTANT]Capptain と Mobile Engagement は、同じサービスではありません。次の手順では、クライアント アプリケーションを移行する方法についてのみ詳しく説明します。アプリで SDK を移行しても、データは Capptain サーバーから Mobile Engagement のサーバーに移行されません。

### エージェント

メソッド `registerApp:` は、新しいメソッド `init:` で置き換えられました。その結果、アプリケーション デリゲートは更新され、接続文字列を使用する必要があります。

			- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
			{
			  [...]
			  [EngagementAgent init:@"YOUR_CONNECTION_STRING"];
			  [...]
			}

SmartAd の追跡が SDK から削除されました。`AETrackModule` クラスのすべてのインスタンスを削除する必要があります。

### クラス名の変更

ブランド変更の一部として、いくつかの変更が必要なクラス名とファイル名があります。

「CP」で始まるクラスは、「AE」で始まる名前に変更されました。

例:

-   `CPModule.h` は `AEModule.h` に変更されました。

「Capptain」で始まるクラスは、「Engagement」で始まる名前に変更されました。

次に例を示します。

-   クラス `CapptainAgent` は `EngagementAgent` に変更されました。
-   クラス `CapptainTableViewController` は `EngagementTableViewController` に変更されました。
-   クラス `CapptainUtils` は `EngagementUtils` に変更されました。
-   クラス `CapptainViewController` は `EngagementViewController` に変更されました。

<!---HONumber=Oct15_HO3-->