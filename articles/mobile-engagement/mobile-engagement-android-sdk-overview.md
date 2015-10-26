<properties 
	pageTitle="Azure Mobile Engagement Android SDK の統合" 
	description="Android SDK for Azure Mobile Engagement の最新の更新情報と更新手順について"
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="piyushjo" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-android" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="08/10/2015" 
	ms.author="piyushjo" />


#Android SDK for Azure Mobile Engagement

Azure Mobile Engagement を Android アプリに統合する方法の詳細について紹介します。まず試してみる場合は、「[15 分間チュートリアル](mobile-engagement-android-get-started.md)」をご覧ください

[SDK コンテンツ](mobile-engagement-android-sdk-content.md)について表示するにはここをクリックします。

##統合手順
1. ここから開始: [Engagement を Android アプリに統合する方法](mobile-engagement-android-integrate-engagement.md)

2. 通知: [リーチ (通知) を Android アプリに統合する方法](mobile-engagement-android-integrate-engagement-reach.md)
	1. Google Cloud Messaging (GCM): [GCM を Mobile Engagement に統合する方法](mobile-engagement-android-gcm-integrate.md)
	2. Amazon Device Messaging (ADM): [ADM を Mobile Engagement に統合する方法](mobile-engagement-android-adm-integrate.md)

3. タグ付けプランの実装: [Android アプリで高度な Mobile Engagement のタグ付け API を使用する方法](mobile-engagement-android-use-engagement-api.md)


##リリース ノート

##4\.1.0 (2015 年 8 月 25 日)

- Android M の新しいアクセス許可モデルを処理します。
- `AndroidManifest.xml` を使用する代わりに実行時に場所の機能を構成できるようになりました。
- アクセス許可のバグ修正: `ACCESS_FINE_LOCATION` を使用する場合、`ACCESS_COARSE_LOCATION` は必要なくなります。
- 安定性が向上しました。

以前のバージョンについては、「[完全リリース ノート](mobile-engagement-android-release-notes.md)」をご覧ください。

##アップグレードの手順

SDK の旧バージョンをアプリケーションに既に統合している場合、[アップグレード手順](mobile-engagement-android-upgrade-procedure.md)を参照してください。

<!---HONumber=Oct15_HO3-->