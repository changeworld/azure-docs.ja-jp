<properties 
	pageTitle="Windows ユニバーサル SDK の概要" 
	description="Azure Mobile Engagement 向け Windows ユニバーサル SDK の概要" 									
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

#Azure Mobile Engagement 向け Windows ユニバーサル SDK の概要

ここから Azure Mobile Engagement を Windows ユニバーサル アプリに統合する方法についての説明を開始します。まず試してみる場合は、[15 分間チュートリアル](mobile-engagement-windows-store-dotnet-get-started.md)を完了してください。

[SDK コンテンツ](mobile-engagement-windows-store-sdk-content.md)について表示するにはここをクリックします。

##統合手順

1. ここから開始: [Windows ユニバーサル アプリにモバイル エンゲージメントを統合する方法](mobile-engagement-windows-store-integrate-engagement.md)

2. 通知: [リーチ (通知) を Windows ユニバーサル アプリに統合する方法](mobile-engagement-windows-store-integrate-engagement-reach.md)

3. タグ付けプランの実装: [Windows ユニバーサル アプリで高度な Mobile Engagement タグ付け API を使用する方法](mobile-engagement-windows-store-use-engagement-api.md)

##リリース ノート

###3\.1.0 (05/21/2015)

-   Mobile Engagement のデバイス ID は、インストール時に生成された GUID に基づいています

以前のバージョンについては、「[完全リリース ノート](mobile-engagement-windows-store-release-notes.md)」をご覧ください。

##アップグレードの手順

既にアプリケーションに以前のバージョンのモバイル エンゲージメントを統合してある場合は、SDK をアップグレードするときに、次の点を考慮する必要があります。

SDK のいくつかのバージョンがない場合は、次の手順に従う必要があります。完全な[アップグレードの手順](mobile-engagement-windows-store-upgrade-procedure.md)をご覧ください。たとえば、0.10.1 から 0.11.0 に移行する場合、まず「0.9.0から 0.10.1」への手順を実行してから「0.10.1 から 0.11.0」への手順を実行する必要があります。

###2\.0.0 から 3.0.0 に移行

#### リソース
この手順では、カスタマイズされたリソースが問題になります。SDK 提供のリソース (html、画像、オーバーレイ) をカスタマイズしている場合、アップグレード前にそのリソースをバックアップし、アップグレードしたリソースにカスタマイズを再適用する必要があります。

### 古いバージョンからのアップグレード

[アップグレード手順](mobile-engagement-windows-store-upgrade-procedure/)をご覧ください

<!---HONumber=August15_HO7-->