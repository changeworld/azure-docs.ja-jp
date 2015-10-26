<properties 
	pageTitle="Windows ユニバーサル アプリ SDK コンテンツ" 
	description="Azure モバイル エンゲージメント向け Windows ユニバーサル アプリ SDK の内容詳細" 					
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="piyushjo" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="08/10/2015" 
	ms.author="piyushjo" />

#Windows ユニバーサル アプリ SDK コンテンツ

このドキュメントでは、アプリケーションで SDK により展開されるコンテンツについて説明します。

##`/Resources` フォルダー

このフォルダーには、モバイル エンゲージメントが必要とするすべてのリソースが含まれています。アプリに適合するように、これらをカスタマイズすることもできます。

- `EngagementConfiguration.xml`: モバイル エンゲージメントの構成ファイルであり、エンゲージメントの設定をカスタマイズできます (モバイル エンゲージメント接続文字列、クラッシュ レポートなど)。

### /html フォルダー

- `EngagementNotification.html` : `Notification` Web ビュー html デザイン。

- `EngagementAnnouncement.html` : `Announcement` Web ビュー html デザイン。

### /images フォルダー

- `EngagementIconNotification.png`: 通知の左側に表示されるブランド アイコン。これは自分のブランド アイコンに換えます。

- `EngagementIconOk.png` : Reach コンテンツ ページの [`Ok`] アイコン (アクション ボタンまたは検証ボタンとして使用)。

- `EngagementIconNOK.png` : Reach コンテンツ ページの検証ボタンが無効の場合に使用する [`NOK`] アイコン。
 
- `EngagementIconClose.png` : Reach 通知やコンテンツの [`Close`] アイコン (閉じるボタンとして使用)。

### /overlay フォルダー

- `EngagementOverlayAnnouncement.xaml` : `Announcement` xaml デザイン。

- `EngagementOverlayAnnouncement.xaml.cs` : `EngagementOverlayAnnouncement.xaml` リンク コード。
 
- `EngagementOverlayNotification.xaml` : `Notification` xaml デザイン。
 
- `EngagementOverlayNotification.xaml.cs` : `EngagementOverlayNotification.xaml` リンク コード。
 
- `EngagementPageOverlay.cs` : `Overlay` アナウンスと通知の表示コード。
  

<!---HONumber=Oct15_HO3-->