<properties 
	pageTitle="メディア プロセッサを作成する方法 - Azure" 
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
	ms.date="05/13/2015" 
	ms.author="juliako"/>


#方法: メディア プロセッサ インスタンスを取得する

この記事は、「[Media Services ビデオ オン デマンド ワークフロー](media-services-video-on-demand-workflow.md)」シリーズの一部です。

##概要

メディア プロセッサは、Media Services のコンポーネントとして、メディア コンテンツのエンコード、形式変換、暗号化、復号化など、特定の処理タスクを担います。通常、メディア コンテンツのエンコード、暗号化、形式変換を行うタスクの作成時にメディア プロセッサを作成します。

次の表は、利用可能なメディア プロセッサの名前と説明の一覧です。

メディア プロセッサ名|説明|詳細情報
---|---|---
Azure Media Encoder|Azure Media Encoder を使用してエンコード タスクを実行できます。| [Azure Media Encoder 用のタスク プリセット文字列](http://msdn.microsoft.com/library/jj129582.aspx)
メディア エンコーダー プレミアム ワークフロー|メディア エンコーダー プレミアム ワークフローを使用してエンコード タスクを実行できます。|[メディア エンコーダー プレミアム ワークフローを使用したエンコード](media-services-encode-with-premium-workflow.md)
Azure Media Indexer|メディア ファイルとコンテンツを検索可能にすると共に、クローズド キャプション トラックの生成を可能にします。|[Azure Media Indexer によるメディア ファイルのインデックス作成](media-services-index-content.md)
Microsoft Azure Media Packager|メディア資産を .mp4 からスムーズ ストリーミング形式に変換できます。また、スムーズ ストリーミングから Apple HTTP ライブ ストリーミング (HLS) 形式にメディア資産を変換できます。|[Azure Media Packager 用のタスク プリセット文字列](http://msdn.microsoft.com/library/hh973635.aspx)
Microsoft Azure Media Encryptor|PlayReady Protection を使用してメディア資産を暗号化できます。|[Azure Media Packager 用のタスク プリセット文字列](http://msdn.microsoft.com/library/hh973610.aspx)
Azure Media Hyperlapse (プレビュー)|ビデオ安定化を使用して、ビデオの "凸凹" を取り除いて滑らかにすることができます。コンテンツをすばやく使用可能なクリップにすることもできます。|[Azure Media Hyperlapse](http://go.microsoft.com/fwlink/?LinkId=613274)
Storage Decryption|ストレージ暗号化を使用して暗号化されたメディア資産を復号化できます。|該当なし

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

##次のステップ
これで、プロセッサ インスタンスの取得方法を学習できました。次は、[資産をエンコードする方法][]に関するトピックに進み、Azure Media Encoder を使用して資産をエンコードする方法を学習します。

[資産をエンコードする方法]: media-services-encode-asset.md
[Task Preset Strings for the Azure Media Encoder]: http://msdn.microsoft.com/library/jj129582.aspx
[Media Services にプログラムから接続する方法]: ../media-services-set-up-computer/

<!---HONumber=August15_HO6-->