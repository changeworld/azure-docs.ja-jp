<properties 
	pageTitle="Azure Media Services を使用したライブ ストリーミングの配信" 
	description="このトピックでは、一般的なメディア サービスのライブ ストリーミングのワークフローの手順を説明します。" 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/10/2015" 
	ms.author="juliako"/>


#Azure Media Services を使用したライブ ストリーミングの配信

##概要

このトピックでは、一般的な Azure Media Services (AMS) のライブ ストリーミングのワークフローの手順を説明します。各手順は関連するトピックにリンクしています。さまざまなテクノロジを使用して達成できるタスクでは、任意のテクノロジへリンクするボタンがあります (たとえば、.NET または REST)。   

メディア サービスは既存のツールとプロセスに統合できます。たとえば、コンテンツをオンサイトでエンコードして、複数の形式にトランスコードするためにメディア サービスにアップロードし、Azure CDN やサードパーティ CDN を通じて配信します。 

次の図は、ライブ ストリーミング ワークフローに関連する Media Services プラットフォームの主要な部分を示しています。

![ライブのワークフロー][live-overview]

このトピックでは、ライブ ストリーミングに関連する概念を説明し、ライブ ストリーミングのタスクの実行方法を示すトピックへのリンクを紹介します。

##概念

ライブ ストリーミングに関連する概念については、「[Azure Media Services の概念](media-services-concepts.md)」をご覧ください。

##Media Services アカウントの作成

**Azure 管理ポータル**を使用して、[Media Services アカウントを作成](media-services-create-account.md)します。

##ストリーミング エンドポイントの構成

ストリーミング エンドポイントの概要と、その管理方法については、「[Media Services アカウントでストリーミング エンドポイントを管理する方法](media-services-manage-origins.md)」をご覧ください。

##開発環境の設定  

開発環境の **.NET** または **REST API** を選択します。

[AZURE.INCLUDE [media-services-selector-setup](../../includes/media-services-selector-setup.md)]

##プログラムによる接続  

**.NET** または **REST API** を選択して、プログラムによって Azure メディア サービスに接続します。

[AZURE.INCLUDE [media-services-selector-connect](../../includes/media-services-selector-connect.md)]


##内部設置型のライブ エンコーダーを使用してマルチ ビットレート ストリーミングをチャネルに出力する

##サードパーティのライブ トランスコーダーの操作

詳細については、「[Azure Media Services でのサード パーティ ライブ エンコーダーの使用](https://msdn.microsoft.com/library/azure/dn783464.aspx)」をご覧ください。

##チャネル、プログラム、アセットの管理

**概要**: [チャネルとプログラムの管理の概要](media-services-manage-channels-overview.md).

**[ポータル]**、**[.NET]**、**[REST API]** をクリックするとサンプルを確認できます。

[AZURE.INCLUDE [media-services-selector-manage-channels](../../includes/media-services-selector-manage-channels.md)]

##アセット配信ポリシーの構成

**.NET** または **REST API** を使用してアセット配信ポリシーを構成します。

[AZURE.INCLUDE [media-services-selector-asset-delivery-policy](../../includes/media-services-selector-asset-delivery-policy.md)]

##コンテンツ キーの作成

 **.NET** や **REST API** を使用してアセットを暗号化するコンテンツ キーを作成します。

[AZURE.INCLUDE [media-services-selector-create-contentkey](../../includes/media-services-selector-create-contentkey.md)]

##コンテンツ キーの承認ポリシーの構成 

**.NET** または **REST API** を使用してコンテンツ保護やキー承認ポリシーを構成するには、以下をご覧ください。

[AZURE.INCLUDE [media-services-selector-content-key-auth-policy](../../includes/media-services-selector-content-key-auth-policy.md)]


##アセットの公開と配信

**概要**: [コンテンツの配信の概要](media-services-deliver-content-overview.md)

**Azure 管理ポータル** または **.NET** を使用してアセットを公開します (ロケータを作成して)。

[AZURE.INCLUDE [media-services-selector-publish](../../includes/media-services-selector-publish.md)]


##Azure CDN の有効化

Media Services では、Azure CDN との統合をサポートしています。Azure CDN を有効にする方法については、「[Media Services アカウントでストリーミング エンドポイントを管理する方法](media-services-manage-origins.md#enable_cdn)」をご覧ください。

##Media Services アカウントのスケーリング

準備するアカウントの **ストリーミング占有ユニット** の数を指定して、**Media Services ** の規模を設定できます。 

ストリーミング ユニットの規模の設定については、[「ストリーミング ユニットの拡張」](media-services-manage-origins.md#scale_streaming_endpoints.md)をご覧ください。




[live-overview]: ./media/media-services-live-streaming-workflow/media-services-live-streaming-current.png


<!--HONumber=52--> 