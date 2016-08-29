<properties
	pageTitle="Windows ユニバーサル SDK 統合"
	description="Azure Mobile Engagement 向け SDK の Windows ユニバーサル統合" 									
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
	ms.date="08/12/2016"
	ms.author="piyushjo;ricksal" />

#Azure Mobile Engagement 向け Windows ユニバーサル SDK 統合

このドキュメントでは、Azure Mobile Engagement Windows ユニバーサル SDK で使用できる統合と構成のすべてのオプションについて説明します。

## 前提条件

このチュートリアルを開始する前に、[15 分間チュートリアル](mobile-engagement-windows-store-dotnet-get-started.md)を完了する必要があります。

## 高度な機能

### レポート機能
次の機能を追加できます。

1. [詳細なレポート オプション](mobile-engagement-windows-store-advanced-reporting.md)

2. [高度な構成オプション](mobile-engagement-windows-store-advanced-configuration.md)

### 通知

[Windows ユニバーサル アプリでリーチ (通知) を統合する方法](mobile-engagement-windows-store-integrate-engagement-reach.md)

### タグ付けプランの実装:

[How to use the advanced Mobile Engagement tagging API in your Windows Universal app (Windows ユニバーサル アプリで高度なモバイル エンゲージメント タグ付け API を使用する方法)](mobile-engagement-windows-store-use-engagement-api.md)

##リリース ノート

###3\.4.0 (04/19/2016)

-   Reach オーバーレイの機能強化。
-   SDK によって出力されるコンソール ログを有効化/無効化/フィルター処理するために "TestLogLevel" API を追加しました。
-   最初のアクティビティを対象としたアクティビティ内通知がアプリ起動時に表示されない問題を修正しました。

以前のバージョンについては、[完全なリリース ノート](mobile-engagement-windows-store-release-notes.md)をご覧ください。

##アップグレードの手順

既にアプリケーションに以前のバージョンの Mobile Engagement を統合してある場合は、SDK をアップグレードするときに、次の点を考慮する必要があります。

SDK の一部のバージョンが不足している場合、いくつかの手順に従う必要があることがあります。完全な[アップグレード手順](mobile-engagement-windows-store-upgrade-procedure.md)を参照してください。たとえば、0.10.1 から 0.11.0 に移行する場合、まず「0.9.0から 0.10.1」への手順を実行してから「0.10.1 から 0.11.0」への手順を実行する必要があります。

###3\.3.0 から 3.4.0 に移行

####テスト ログ

SDK によって生成されるコンソール ログを有効化/無効化/フィルター処理できるようになりました。これをカスタマイズするには、次の例のように `EngagementAgent.Instance.TestLogEnabled` プロパティを `EngagementTestLogLevel` 列挙型の使用可能な値の 1 つに更新します。

			EngagementAgent.Instance.TestLogLevel = EngagementTestLogLevel.Verbose;
			EngagementAgent.Instance.Init();

####リソース

Reach オーバーレイの機能を強化しました。これは SDK NuGet パッケージのリソースの一部です。

新しいバージョンの SDK にアップグレードする際、リソースのオーバーレイ フォルダーにある既存ファイルを保持するかどうかを選択できます。

* 以前のオーバーレイが機能している、または `WebView` 要素を手動で統合している場合、既存ファイルを保持することで、引き続き使用することができます。
* 新しいオーバーレイに更新するには、リソースの `overlay` フォルダー全体を SDK パッケージの新しいフォルダーに置き換えます (UWP アプリ: アップグレード後に %USERPROFILE%\\.nuget\\packages\\MicrosoftAzure.MobileEngagement\\3.4.0\\content\\win81\\Resources から新しいオーバーレイ フォルダーを取得できます)。

> [AZURE.WARNING] 新しいオーバーレイを使用すると、以前のバージョンに対して行ったすべてのカスタマイズが上書きされます。

### 古いバージョンからのアップグレード

[アップグレード手順](mobile-engagement-windows-store-upgrade-procedure.md)をご覧ください

<!---HONumber=AcomDC_0817_2016-->