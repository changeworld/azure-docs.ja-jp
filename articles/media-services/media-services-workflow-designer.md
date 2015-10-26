<properties 
	pageTitle="ワークフロー デザイナーを使用して高度なエンコード ワークフローを作成する" 
	description="ワークフロー デザイナーを使用して高度なエンコード ワークフローを作成する方法について説明します。" 
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
	ms.date="09/07/2015"
	ms.author="juliako"/>


#ワークフロー デザイナーを使用して高度なエンコード ワークフローを作成する

##概要
**ワークフロー デザイナー**は、**メディア エンコーダー プレミアム ワークフロー**でエンコードするカスタム ワークフローの設計と構築に使用する Windows デスクトップ ツールです。

このツールは、[既存のワークフロー](media-services-workflow-designer.md#existing_workflows)を変更する場合にも使用できます

>[AZURE.NOTE]ワークフロー デザイナー ツールのコピーを取得するには、mepd@microsoft.com にお問い合わせください。


作成されたワークフロー ファイルはアセットとしてアップロードした後、メディア ファイルのエンコードに使用できます。**.NET** を使用した**メディア エンコーダー プレミアム ワークフロー**によるエンコード方法の詳細については、「[メディア エンコーダー プレミアム ワークフローでの高度なエンコード](media-services-encode-with-premium-workflow.md)」を参照してください。

##<a id="existing_workflows"></a>既存のワークフローを変更する

デザイナー ツールを使用して、既定のワークフロー ファイルを変更できます。既定のワークフロー ファイルは[こちら](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows)から入手できます。フォルダーにはこれらのファイルの説明も含まれています。

以下のビデオでは、デザイナーの使用方法を示しています。

###Day 1 - 作業の開始

Day 1 のビデオの内容は次のとおりです。

- デザイナーの概要
- 基本的なワークフロー - "Hello World"
- Azure Media Services のストリーミングで使用する複数の出力 MP4 ファイルの作成

> [AZURE.VIDEO azure-premium-encoder-workflow-designer-training-videos-day-1]

###Day 2

Day 2 のビデオの内容は次のとおりです。

- さまざまなソース ファイル シナリオ - オーディオの処理
- 高度なロジックを含むワークフロー
- グラフ ステージ

> [AZURE.VIDEO azure-premium-encoder-workflow-designer-training-videos-day-2]

###Day 3

Day 3 のビデオの内容は次のとおりです。

- ワークフローと設計図の内部スクリプト
- 現在のエンコーダの制限事項
- Q & A
 
> [AZURE.VIDEO azure-premium-encoder-workflow-designer-training-videos-day-3]



##Media Services のラーニング パス

AMS のラーニング パスについては、以下を参照してください。

- [AMS のライブ ストリーミング ワークフロー](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-live/)
- [AMS のオンデマンド ストリーミング ワークフロー](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-on-demand/)


##関連項目

[Azure プレミアム エンコーダー ワークフロー デザイナーのトレーニング ビデオ](http://johndeutscher.com/2015/07/06/azure-premium-encoder-workflow-designer-training-videos/)

<!---HONumber=Oct15_HO3-->