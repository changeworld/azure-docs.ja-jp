<properties 
	pageTitle="Azure モバイル エンゲージメント iOS SDK の概要" 
	description="Azure モバイル エンゲージメント用 iOS SDK の最新の更新プログラムと手順"
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="kpiteira" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-ios" 
	ms.devlang="objective-c" 
	ms.topic="article" 
	ms.date="02/12/2015" 
	ms.author="kapiteir" />

# 2.0.0

ここでは、iOS アプリで Azure モバイル エンゲージメントを統合する方法の詳細を確認します。まず試してみたい場合は、[15 分間のチュートリアル](mobile-engagement-ios-get-started.md) をご確認ください。

クリックして、[SDK の内容](mobile-engagement-ios-sdk-content.md) をご覧ください。

## 統合手順
1. はじめに: [IOS アプリでモバイル エンゲージメントを統合する方法](mobile-engagement-ios-integrate-engagement.md)

2. 通知: [IOS アプリでリーチ (通知) を統合する方法](mobile-engagement-ios-integrate-engagement-reach.md)

3. タグ計画の実装: [iOS アプリで API をタグ付けする高度なモバイル エンゲージメントを使用する方法](mobile-engagement-ios-use-engagement-api.md)


## リリース ノート

### 2.0.0 (02/17/2015)

-   Azure モバイル エンゲージメントの最初のリリース
-   appId/sdkKey 構成が接続文字列の構成で置き換えられました。
-   任意の XMPP エンティティから任意の XMPP メッセージを送受信する API が削除されました。
-   デバイス間でメッセージを送受信する API が削除されました。
-   セキュリティが強化されました。
-   SmartAd の追跡が削除されました。

以前のバージョンについては、[全リリース ノート](mobile-engagement-ios-release-notes.md) をご覧ください。

## アップグレードの手順

既に古いバージョンのエンゲージメントをアプリケーションに統合している場合は、SDK をアップグレードするときに次の点を考慮する必要があります。

一部のバージョンの SDK が不足している場合は、いくつかの手順を実行する必要があります。[アップグレードの手順] をご覧ください(mobile-engagement-ios-upgrade-procedure.md)。

まず、新しいバージョンの SDK ごとに、EngagementSDK フォルダーと EngagementReach フォルダーを置き換える (削除し、xcode で再インポートする) 必要があります。

### 1.16.0 から 2.0.0 へ
次に、Capptain SAS が提供する Capptain サービスから、Azure モバイル エンゲージメントを備えたアプリに SDK 統合を移行する方法について説明します。 

>[Azure.IMPORTANT] Capptain とモバイル エンゲージメントは、同じサービスではありません。次の手順では、クライアント アプリを移行する方法についてのみ説明します。アプリ内の SDK を移行しても、Capptain サーバーからモバイル エンゲージメント サーバーにデータは移行されません。

以前のバージョンから移行する場合は、Capptain の Web サイトを参照して、まず 1.16 に移行してから次の手順を適用してください。

#### エージェント

メソッド `registerApp:` は、新しいメソッド `init:` で置き換えられました。その結果、アプリケーション デリゲートは更新され、接続文字列を使用する必要があります。

			- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
			{
			  [...]
			  [EngagementAgent init:@"YOUR_CONNECTION_STRING"];
			  [...]
			}

SmartAd の追跡が SDK から削除されました。 `AETrackModule` クラスのすべてのインスタンスを削除する必要があります。

#### クラス名の変更

ブランド変更の一部として、いくつかの変更が必要なクラス名とファイル名があります。

「CP」で始まるクラスは、「AE」で始まる名前に変更されました。

例:

-   `CPModule.h` は、 `AEModule.h` に変更されました。

「Capptain」で始まるクラスは、「Engagement」で始まる名前に変更されました。

次に例を示します。

-   クラス  `CapptainAgent` は、 `EngagementAgent` に変更されました。
-   クラス  `CapptainTableViewController` は、 `EngagementTableViewController` に変更されました。
-   クラス  `CapptainUtils` は、 `EngagementUtils` に変更されました。
-   クラス  `CapptainViewController` は、 `EngagementViewController` に変更されました。

<!--HONumber=47-->
