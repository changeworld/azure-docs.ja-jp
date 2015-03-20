<properties 
	pageTitle="Azure モバイル エンゲージメント Windows Phone SDK 統合" 
	description="Azure モバイル エンゲージメント向け Windows Phone SDK の内容詳細" 					
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="lalathie" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="" 
	ms.topic="article" 
	ms.date="01/24/2015" 
	ms.author="kapiteir" />
	
#SDK の内容

このドキュメントでは SDK のアーカイブの内容を一覧にして説明しています。

## `Root` フォルダー

このフォルダーには、ソフトウェア ライセンスのコピーとオンライン ドキュメントへのリンクが含まれています。

`documentation.html` :Windows Phone 用のエンゲージメント SDK のオンライン ドキュメントへのリンクが含まれてます。

このフォルダーには、ライセンス ファイルも含まれています。

## `/lib` フォルダー

このフォルダーには、エンゲージメント SDK を入手する場所についての情報が含まれています。

`azuresdk-mobileengagement-windowsphone-X.X.X.nupkg` :SDK 統合用の Nuget パッケージです。

## `/Resources` フォルダー

このフォルダーには、エンゲージメントが必要とするべてのリソースが含まれています。アプリに合わせてカスタマイズすることもできます。

`EngagementIconNotification.png` :ブランド アイコンは、通知の左側に表示されます。

`EngagementIconOk.png` :リーチ ページ ApplicationBarの [OK] アイコンです。

`EngagementIconCancel.png` :リーチ ページ ApplicationBar の [キャンセル] アイコンです。

`EngagementIconCloseLight.png` :エンゲージメントのリーチ通知のWindows Phone ライト テーマ用 [終了] アイコンです。

`EngagementIconCloseDark.png` :エンゲージメントのリーチ通知のWindows Phone ダーク テーマ用 [終了] アイコンです。

`EngagementConfiguration.xml` :エンゲージメントの構成ファイルです。ここで、エンゲージメントの設定をカスタマイズができます (エンゲージメントの接続文字列、クラッシュのレポート...)。

## `/src/agent` フォルダー

このフォルダーには EngagementPage が含まれています。

`EngagementPage.cs` :エンゲージメントに自動的にアクティビティを報告するページの基本クラスです。

## `/src/reach` フォルダー

最後に、このフォルダーで、各ページの既定の XAML が表示されます (さらにそれらに対応する C\#)。

自分のページの基礎として使用ができます。必要な作業を正確に知るにはコメントに従ってください。

### TextView のお知らせ

`EngagementDefaultTextViewAnnouncementPage.xaml`

`EngagementDefaultTextViewAnnouncementPage.xaml.cs`

### WebView のお知らせ

`EngagementDefaultWebViewAnnouncementPage.xaml`

`EngagementDefaultWebViewAnnouncementPage.xaml.cs`

### ポーリング

`EngagementDefaultPollPage.xaml`

`EngagementDefaultPollPage.xaml.cs`

### 通知

`EngagementBasicNotificationView.xaml`

`EngagementBasicNotificationView.xaml.cs`

<!--HONumber=47-->
