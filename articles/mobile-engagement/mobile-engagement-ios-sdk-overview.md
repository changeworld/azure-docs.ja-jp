<properties
	pageTitle="Azure Mobile Engagement iOS SDK の概要"
	description="Azure Mobile Engagement 用 iOS SDK の最新の更新プログラムと手順"
	services="mobile-engagement"
	documentationCenter="mobile"
	authors="piyushjo"
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

#iOS SDK for Azure Mobile Engagement

ここでは、iOS アプリで Azure Mobile Engagement を統合する方法の詳細を確認します。まず試してみる場合は、「[15 分間チュートリアル](mobile-engagement-ios-get-started.md)」をご覧ください

[SDK コンテンツ](mobile-engagement-ios-sdk-content.md)について表示するにはここをクリックします。

##統合手順
1. ここから開始: [iOS アプリで Mobile Engagement を統合する方法](mobile-engagement-ios-integrate-engagement.md)

2. 通知: [リーチ (通知) を iOS アプリに統合する方法](mobile-engagement-ios-integrate-engagement-reach.md)

3. タグ付けプランの実装: [iOS アプリで高度な Mobile Engagement のタグ付け API を使用する方法](mobile-engagement-ios-use-engagement-api.md)


##リリース ノート

###3\.2.1 (12/11/2015)

-   新しいアプリケーション インスタンスがディープ リンクを持つ通知によってトリガーされたときの遅延を修正。 

以前のバージョンについては、「[完全リリース ノート](mobile-engagement-ios-release-notes.md)」をご覧ください。

##アップグレードの手順

既にアプリケーションに以前のバージョンのモバイル エンゲージメントを統合してある場合は、SDK をアップグレードするときに、次の点を考慮する必要があります。

SDK のいくつかのバージョンがない場合は、次の手順に従う必要があります。完全な「[アップグレード手順](mobile-engagement-ios-upgrade-procedure.md)」をご覧ください。

まず、新しいバージョンの SDK ごとに、EngagementSDK フォルダーと EngagementReach フォルダーを置き換える (削除し、xcode で再インポートする) 必要があります。

###2\.0.0 から 3.0.0 に移行
iOS 4.X のサポートが終了。このバージョンから、アプリケーションのデプロイ ターゲットは iOS 6 以降である必要があります。

アプリケーションでリーチを使用している場合は、リモート通知を受け取れるように、`remote-notification` 値を Info.plist ファイル内の `UIBackgroundModes` 配列に追加する必要があります。

アプリケーション デリゲートのメソッド `application:didReceiveRemoteNotification:` は、`application:didReceiveRemoteNotification:fetchCompletionHandler:` に置き換える必要があります。

AEPushDelegate.h インターフェイスは廃止されるため、すべての参照を削除する必要があります。これには、`[[EngagementAgent shared] setPushDelegate:self]` の削除や、アプリケーション デリゲートからのデリゲート メソッドの削除も含まれます。

	-(void)willRetrieveLaunchMessage;
	-(void)didFailToRetrieveLaunchMessage;
	-(void)didReceiveLaunchMessage:(AEPushMessage*)launchMessage;

<!---HONumber=AcomDC_1217_2015-->