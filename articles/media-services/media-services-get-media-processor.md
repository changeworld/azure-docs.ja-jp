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

メディア プロセッサは、メディア サービスのコンポーネントとして、メディア コンテンツのエンコード、形式変換、暗号化、復号化など、特定の処理タスクを担います。通常、メディア コンテンツのエンコード、暗号化、形式変換を行うタスクの作成時にメディア プロセッサを作成します。

次の表は、利用可能なメディア プロセッサの名前と説明の一覧です。

<table border="2" cellspacing="0" cellpadding="5" style="border: 2px solid #000000;">
  <thead>
    <tr>
       <th>メディア プロセッサ名</th>
       <th>説明</th>
	<th>詳細</th>
    </tr>
  </thead>
  <tbody>
    <tr>
       <td>Azure メディア エンコーダー</td>
       <td>Azure メディア エンコーダーを使用してエンコード タスクを実行できます。</td>
       <td><a href="http://msdn.microsoft.com/library/jj129582.aspx">Azure メディア エンコーダー用のタスク プリセット文字列</a></td>
    </tr>
    <tr>
       <td>メディア エンコーダー プレミアム ワークフロー</td>
       <td>メディア エンコーダー プレミアム ワークフローを使用してエンコード タスクを実行できます。</td>
       <td><a href="http://azure.microsoft.com/documentation/articles/media-services-encode-with-premium-workflow/">メディア エンコーダー プレミアム ワークフローを使用したエンコード</a></td>
    </tr>    
	<tr>
        <td>Azure メディア インデクサー</td>
        <td>メディア ファイルとコンテンツを検索可能にすると共に、クローズド キャプション トラックの生成を可能にします。</td>
		<td><a href="http://azure.microsoft.com/documentation/articles/media-services-index-content/">Azure Media Indexer によるメディア ファイルのインデックス作成</a></td>
    </tr>
    <tr>
        <td>Windows Azure Media Packager</td>
        <td>メディア アセットを .mp4 からスムーズ ストリーミング形式に変換できます。また、スムーズ ストリーミングから Apple HTTP ライブ ストリーミング (HLS) 形式にメディア アセットを変換できます。</td>
		<td><a href="http://msdn.microsoft.com/library/hh973635.aspx">Azure Media Packager 用のタスクのプリセット</a></td>
    </tr>
    <tr>
        <td>Windows Azure Media Encryptor</td>
        <td>PlayReady Protection を使用してメディア アセットを暗号化できます。</td>
        <td><a href="http://msdn.microsoft.com/library/hh973610.aspx">Azure Media Packager 用のタスクのプリセット</a></td>
    </tr>
	<tr>
		<td>Azure Media Hyperlapse (プレビュー)</td>
		<td>ビデオ安定化を使用して、ビデオの "凸凹" を取り除いて滑らかにすることができます。コンテンツをすばやく使用可能なクリップにすることもできます。</td>
		<td><a href="http://go.microsoft.com/fwlink/?LinkId=613274">Azure Media Hyperlapse</a></td>
	</tr>
    <tr>
        <td>Storage Decryption</td>
        <td>ストレージ暗号化を使用して暗号化されたメディア アセットを復号化できます。</td>
		<td>該当なし</td>
    </tr>  </tbody>
</table>

<br />

##MediaProcessor の取得

次のメソッドは、メディア プロセッサ インスタンスを取得する方法を示しています。このコード例では、「[方法: メディア サービスにプログラムから接続する]」で説明しているように、モジュール レベルの変数 **_context** を使用してサーバー コンテキストを参照しています。

	private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
	{
	     var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
	        ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();
	
	    if (processor == null)
	        throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));
	
	    return processor;
	}

##次のステップ
これで、プロセッサ インスタンスの取得方法を学習できました。次は、[アセットをエンコードする方法][]に関するトピックに進み、Azure メディア エンコーダーを使用してアセットをエンコードする方法を学習します。

[アセットをエンコードする方法]: media-services-encode-asset.md
[Task Preset Strings for the Azure Media Encoder]: http://msdn.microsoft.com/library/jj129582.aspx
[方法: メディア サービスにプログラムから接続する]: ../media-services-set-up-computer/

<!---HONumber=July15_HO2-->