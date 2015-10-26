<properties 
	pageTitle="メディア プロセッサを作成する方法 | Microsoft Azure" 
	description="メディア プロセッサ コンポーネントを作成し、Azure Media Services 用にメディア コンテンツのエンコード、形式の変換、暗号化、または復号化を行う方法について説明します。コード サンプルは C# で記述され、Media Services SDK for .NET を利用しています。" 
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


#方法: メディア プロセッサ インスタンスを取得する

> [AZURE.SELECTOR]
- [.NET](media-services-get-media-processor.md)
- [REST](media-services-rest-get-media-processor.md)
 

##概要

メディア プロセッサは、Media Services のコンポーネントとして、メディア コンテンツのエンコード、形式変換、暗号化、復号化など、特定の処理タスクを担います。通常、メディア コンテンツのエンコード、暗号化、形式変換を行うタスクの作成時にメディア プロセッサを作成します。

次の表は、利用可能なメディア プロセッサの名前と説明の一覧です。

メディア プロセッサ名|説明|詳細情報
---|---|---
Azure Media Encoder|Azure Media Encoder を使用してエンコード タスクを実行できます。|[Azure Media Encoder](media-services-encode-asset.md#azure_media_encoder)
メディア エンコーダー スタンダード|Media Encoder Standard を使用してエンコード タスクを実行できます。|[Azure Media Encoder](media-services-encode-asset.md#media_encoder_standard)
メディア エンコーダー プレミアム ワークフロー|メディア エンコーダー プレミアム ワークフローを使用してエンコード タスクを実行できます。|[メディア エンコーダー プレミアム ワークフロー](media-services-encode-asset.md#media_encoder_premium_wokrflow)
Azure Media Indexer| メディア ファイルとコンテンツを検索可能にすると共に、クローズド キャプション トラックの生成を可能にします。|[Azure Media Indexer によるメディア ファイルのインデックス作成](media-services-index-content.md)
Azure Media Hyperlapse (プレビュー)|ビデオ安定化を使用して、ビデオの "凸凹" を取り除いて滑らかにすることができます。コンテンツをすばやく使用可能なクリップにすることもできます。|		[Azure Media Hyperlapse](http://azure.microsoft.com/blog/?p=286281&preview=1&_ppp=61e1a0b3db)</a>
Storage Decryption| ストレージ暗号化を使用して暗号化されたメディア資産を復号化できます。|該当なし
Microsoft Azure Media Packager|メディア資産を .mp4 からスムーズ ストリーミング形式に変換できます。また、スムーズ ストリーミングから Apple HTTP ライブ ストリーミング (HLS) 形式にメディア資産を変換できます。|[Azure Media Packager 用のタスク プリセット文字列](http://msdn.microsoft.com/library/hh973635.aspx)
Microsoft Azure Media Encryptor|PlayReady Protection を使用してメディア資産を暗号化できます。|[Azure Media Packager 用のタスク プリセット文字列](http://msdn.microsoft.com/library/hh973610.aspx)

##MediaProcessor の取得

次のメソッドは、メディア プロセッサ インスタンスを取得する方法を示しています。このコード例では、[Media Services にプログラムから接続する方法]に関するページで説明しているように、モジュール レベルの変数 **\_context** を使用してサーバー コンテキストを参照しています。

	private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
	{
	     var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
	        ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();
	
	    if (processor == null)
	        throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));
	
	    return processor;
	}


##Media Services のラーニング パス

AMS のラーニング パスについては、以下を参照してください。

- [AMS のライブ ストリーミング ワークフロー](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-live/)
- [AMS のオンデマンド ストリーミング ワークフロー](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-on-demand/)


##次のステップ
これで、プロセッサ インスタンスの取得方法を学習できました。次は、[アセットをエンコードする方法][]に関するトピックに進み、Azure メディア エンコーダーを使用してアセットをエンコードする方法を学習します。

[アセットをエンコードする方法]: media-services-encode-asset.md
[Task Preset Strings for the Azure Media Encoder]: http://msdn.microsoft.com/library/jj129582.aspx
[Media Services にプログラムから接続する方法]: ../media-services-set-up-computer/

<!---HONumber=Oct15_HO3-->