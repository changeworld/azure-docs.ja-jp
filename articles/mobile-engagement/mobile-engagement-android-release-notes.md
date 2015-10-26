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


#リリース ノート

##4\.1.0 (2015 年 8 月 25 日)

- Android M の新しいアクセス許可モデルを処理します。
- `AndroidManifest.xml` を使用する代わりに実行時に場所の機能を構成できるようになりました。
- アクセス許可のバグ修正: `ACCESS_FINE_LOCATION` を使用する場合、`ACCESS_COARSE_LOCATION` は必要なくなります。
- 安定性が向上しました。

##4\.0.0 (07/06/2015)

-   分析を行い、より確実にプッシュするように内部プロトコルを変更しました。
-   ネイティブ プッシュ通知 (GCM/ADM) がアプリ内通知にも使用されるようになったので、すべての種類のプッシュ キャンペーンについてネイティブのプッシュの資格情報を設定する必要があります。
-   大きい画像による通知を修正しました。これらの画像は、プッシュ後の 10 秒間のみ表示されていました。
-   Web 表示のバグ修正: リンクをクリックすると、既定のアクション URL も実行されていました。
-   ローカル ストレージの管理に関連してまれに起きていたクラッシュを修正しました。
-   動的構成文字列の管理を修正しました。
-   EULA を更新しました。

##3\.0.0 (02/17/2015)

-   Azure Mobile Engagement の最初のリリース
-   appId 構成が接続文字列構成に置き換わりました。
-   任意の XMPP エンティティから任意の XMPP メッセージを送受信する API が削除されました。
-   デバイス間でメッセージを送受信する API が削除されました。
-   セキュリティの強化。
-   Google Play と SmartAd の追跡機能が削除されました。

 

<!---HONumber=Oct15_HO3-->