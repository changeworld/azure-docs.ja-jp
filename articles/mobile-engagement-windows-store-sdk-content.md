<properties 
	pageTitle="Azure モバイル エンゲージメント Windows ストア SDK の内容" 
	description="Azure モバイル エンゲージメントの Windows ストア SDK の最新更新プログラムと手順について" 					
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="lalathie" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="" 
	ms.topic="article" 
	ms.date="02/12/2015" 
	ms.author="kapiteir" />

#SDK の内容

このドキュメントでは、SDK アーカイブの内容を一覧にして説明しています。

## `Root` フォルダー

このフォルダーには、ソフトウェア ライセンスのコピーとオンライン ドキュメントへのリンクが含まれています。

`documentation.html` :Windows 8 C\# Metro アプリケーション向けエンゲージメント SDK のオンライン ドキュメントへのリンクが含まれています。

このフォルダーには、ライセンス ファイルも含まれています。

## `/lib` フォルダー

このフォルダーには、エンゲージメント SDK を取得する場所に関する情報が含まれています。

`azuresdk-mobileengagement-windows-X.X.X.nupkg` :SDK 統合用の Nuget パッケージ。

## `/Resources` フォルダー

このフォルダーには、エンゲージメントが必要とするすべてのリソースが含まれています。アプリに適合するように、これらをカスタマイズすることもできます。

`EngagementConfiguration.xml` :エンゲージメントの構成ファイルであり、エンゲージメントの設定をカスタマイズできます (エンゲージメント接続文字列、クラッシュ レポートなど)。

### /html フォルダー

`EngagementNotification.html` : `通知` Web ビューの html 設計。

`EngagementAnnouncement.html` : `アナウンス` Web ビューの html 設計。

### /images フォルダー

`EngagementIconNotification.png` :通知の左側に表示されるブランド アイコン。

`EngagementIconOk.png` :Reach コンテンツ ページの  `[OK]` アイコン (アクション ボタンまたは検証ボタンとして使用)。

`EngagementIconNOK.png` :Reach コンテンツ ページの検証ボタンが無効の場合に使用する  `[NOK]` アイコン。

`EngagementIconClose.png` :Reach 通知やコンテンツの  `[閉じる]` アイコン (閉じるボタンとして使用)。

### /overlay フォルダー

`EngagementOverlayAnnouncement.xaml` : `アナウンス`の xaml 設計。

`EngagementOverlayAnnouncement.xaml.cs` : `EngagementOverlayAnnouncement.xaml` のリンク コード。

`EngagementOverlayNotification.xaml` : `通知`の xaml 設計。

`EngagementOverlayNotification.xaml.cs` : `EngagementOverlayNotification.xaml` のリンク コード。

`EngagementPageOverlay.cs` : `オーバーレイ`によるアナウンスと通知の表示コード。

## `/src/agent` フォルダー

このフォルダーには EngagementPage が含まれています。

`EngagementPage.cs` :自動的にアクティビティをエンゲージメントに報告するページの基本クラス。

<!--HONumber=47-->
