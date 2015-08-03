<properties 
	pageTitle="Azure Mobile Engagement Android SDK の統合" 
	description="Android SDK for Azure Mobile Engagement の最新の更新情報と更新手順について"
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="kpiteira" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-android" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="02/12/2015" 
	ms.author="kapiteir" />


#リリース ノート

##4.0.0 (07/06/2015)

-   分析を行い、より確実にプッシュするように内部プロトコルを変更しました。
-   ネイティブ プッシュ通知 (GCM/ADM) がアプリ内通知にも使用されるようになったので、すべての種類のプッシュ キャンペーンについてネイティブのプッシュの資格情報を設定する必要があります。
-   大きい画像による通知を修正しました。これらの画像は、プッシュ後の 10 秒間のみ表示されていました。
-   Web アナウンス内で、既定のアクション URL が設定されたリンクをクリックした場合の動作を修正しました。
-   ローカル ストレージの管理に関連してまれに起きていたクラッシュを修正しました。
-   動的構成文字列の管理を修正しました。
-   EULA を更新しました。

##3.0.0 (02/17/2015)

-   Azure モバイル エンゲージメントの最初のリリース
-   appId 構成が接続文字列構成に置き換わりました。
-   任意の XMPP エンティティから任意の XMPP メッセージを送受信する API が削除されました。
-   デバイス間でメッセージを送受信する API が削除されました。
-   セキュリティの強化。
-   Google Play と SmartAd の追跡機能が削除されました。

 

<!---HONumber=July15_HO4-->