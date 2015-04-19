<properties 
	pageTitle="Media Services ビデオ オン デマンドのワークフロー" 
	description="このトピックでは、一般的な Media Services ビデオ オン デマンドのワークフローの手順を説明します。" 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/15/2015" 
	ms.author="juliako"/>


#Media Services ビデオ オン デマンド ワークフロー

##概要

このトピックでは、一般的な Azure Media Services (AMS) ビデオ オン デマンドのワークフローの手順を説明します。各手順は関連するトピックにリンクしています。さまざまなテクノロジを使用して達成できるタスクでは、任意のテクノロジへリンクするボタンがあります (たとえば、.NET または REST)。   

メディア サービスは既存のツールとプロセスに統合できます。たとえば、コンテンツをオンサイトでエンコードして、複数の形式にトランスコードするためにメディア サービスにアップロードし、Azure CDN やサードパーティ CDN を通じて配信します。 

次の図は、ビデオ オンデマンド ワークフローに関連するメディア サービス プラットフォームの主要な部分を示しています。
![VoD workflow][vod-overview]

詳細については、「[Media Services Overview (Media Services の概要)]」をご覧ください(media-services-overview.md)。

##メディア サービス アカウントの作成

**Azure 管理ポータル**を使用して [メディア サービス アカウントを作成]します(media-services-create-account.md)。 

##ストリーミング エンドポイントの構成

[制作中]

##開発環境の設定  

開発環境の **.NET** または **REST API** を選択します。

[AZURE.INCLUDE [media-services-selector-setup](../includes/media-services-selector-setup.md)]

##プログラムによる接続  

**.NET** または **REST API** を選択して、プログラムによって Azure メディア サービスに接続します。

[AZURE.INCLUDE [media-services-selector-connect](../includes/media-services-selector-connect.md)]

##メディアのアップロード 

**Azure の管理ポータル**、**.NET**、**REST API** を使用してファイルをアップロードするには、以下をご覧ください。

[AZURE.INCLUDE [media-services-selector-upload-files](../includes/media-services-selector-upload-files.md)]

##メディアの処理: エンコード、インデックス作成、ジョブの監視

###メディア プロセッサの取得

メディア プロセッサを取得するには、**.NET** または **REST API** を使用します。

[AZURE.INCLUDE [media-services-selector-get-media-processor](../includes/media-services-selector-get-media-processor.md)]

###ジョブの作成 

ジョブは、一連のタスク (エンコード、インデックス作成など) に関するメタデータを含むエンティティです。各タスクでは、入力アセットでアトミック操作が実行されます。エンコード ジョブの作成方法の例については、以下をご覧ください。

[AZURE.INCLUDE [media-services-selector-encode](../includes/media-services-selector-encode.md)]

###ジョブの進行状況の監視

**Azure の管理ポータル**、**.NET**、**REST API** を使用してジョブの進行状況を監視するには、以下をご覧ください。

[AZURE.INCLUDE [media-services-selector-job-progress](../includes/media-services-selector-job-progress.md)]

###インデックス作成

[AZURE.INCLUDE [media-services-selector-index-content](../includes/media-services-selector-index-content.md)]

###エンコード 

「[Encoding with Azure Media Services (Azure Media Services によるエンコード)]」をご覧ください(media-services-encode-asset.md)。

##コンテンツ キー承認ポリシーを構成する 

**.NET** または **REST API** を使用してコンテンツ保護やキー承認ポリシーを構成するには、以下をご覧ください。

[AZURE.INCLUDE [media-services-selector-content-key-auth-policy](../includes/media-services-selector-content-key-auth-policy.md)]

##アセット配信ポリシーの構成

**.NET** または **REST API** を使用してアセット配信ポリシーを構成します。

[AZURE.INCLUDE [media-services-selector-asset-delivery-policy](../includes/media-services-selector-asset-delivery-policy.md)]

##アセットの公開

**Azure 管理ポータル** または **.NET** を使用してアセットを公開します (ロケータを作成して)。

[AZURE.INCLUDE [media-services-selector-publish](../includes/media-services-selector-publish.md)]

##メディア サービス アカウントのスケーリング

**メディア サービス**は、アカウントのプロビジョニングに使用する **[ストリーミング占有ユニット]** と **[エンコード占有ユニット]** の数を指定することで規模を設定できます。 

ストレージ アカウントを追加して、メディア サービス アカウントの規模を設定することもできます。各ストレージ アカウントの上限は 500 TB です。既定の上限を超えるストレージ容量を設定するために、複数のストレージ アカウントを単一のメディア サービス アカウントにアタッチすることを選択できます。

[この](media-services-how-to-scale.md) トピックは関連するトピックにリンクしています。


##コンテンツの再生

詳細については、[既存のプレーヤーを使用したコンテンツの再生]に関するトピックを参照してください(media-services-playback-content.md)。

[vod-overview]: ./media/media-services-overview/media-services-video-on-demand.png
<!--HONumber=47-->
