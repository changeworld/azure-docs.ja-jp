<properties 
	pageTitle="メディア プロセッサを作成する方法 | Microsoft Azure" 
	description="メディア プロセッサ コンポーネントを作成し、Azure Media Services 用にメディア コンテンツのエンコード、形式の変換、暗号化、または復号化を行う方法について説明します。コード サンプルは C# で記述され、Media Services SDK for .NET を利用しています。" 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/22/2016" 
	ms.author="juliako"/>


#方法: メディア プロセッサ インスタンスを取得する

> [AZURE.SELECTOR]
- [.NET](media-services-get-media-processor.md)
- [REST ()](media-services-rest-get-media-processor.md)


##概要

メディア プロセッサは、Media Services のコンポーネントとして、メディア コンテンツのエンコード、形式変換、暗号化、復号化など、特定の処理タスクを担います。通常、メディア コンテンツのエンコード、暗号化、形式変換を行うタスクの作成時にメディア プロセッサを作成します。

次の表は、利用可能なメディア プロセッサの名前と説明の一覧です。

メディア プロセッサ名|説明|詳細情報
---|---|---
メディア エンコーダー スタンダード|オンデマンド エンコードのための標準機能を備えています。 |[Azure オンデマンド メディア エンコーダーの概要と比較](media-services-encode-asset.md)
メディア エンコーダー Premium ワークフロー|メディア エンコーダー Premium ワークフローを使用してエンコード タスクを実行できます。|[Azure オンデマンド メディア エンコーダーの概要と比較](media-services-encode-asset.md)
Azure Media Indexer| メディア ファイルとコンテンツを検索可能にすると共に、クローズド キャプション トラックの生成を可能にします。|[Azure Media Indexer](media-services-index-content.md)
Azure Media Hyperlapse (プレビュー)|ビデオ安定化を使用して、ビデオの "凸凹" を取り除いて滑らかにすることができます。コンテンツをすばやく使用可能なクリップにすることもできます。|[Azure Media Hyperlapse](media-services-hyperlapse-content.md)
Azure Media Encoder|償却対象
Storage Decryption| 償却対象|
Azure Media Packager|償却対象|
Azure Media Encryptor|償却対象|

##メディア プロセッサの取得

次のメソッドは、メディア プロセッサ インスタンスを取得する方法を示しています。このコード例では、[Media Services にプログラムから接続する方法](media-services-dotnet-connect-programmatically.md)に関するページで説明しているように、モジュール レベルの変数 **\_context** を使用してサーバー コンテキストを参照しています。

	private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
	{
		var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
		ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();
		
		if (processor == null)
		throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));
		
		return processor;
	}


##Media Services のラーニング パス

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##フィードバックの提供

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##次のステップ

これで、メディア プロセッサ インスタンスを取得する方法がわかりました。次は、[資産のエンコード方法](media-services-dotnet-encode-with-media-encoder-standard.md)に関するトピックに進んでください。このトピックでは、Media Encoder Standard を使用して資産をエンコードする方法を説明します。

<!---HONumber=AcomDC_0629_2016-->