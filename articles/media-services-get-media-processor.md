<properties urlDisplayName="Create a Media Processor" pageTitle="メディア プロセッサを作成する方法 - Azure" metaKeywords="" description="Learn how to create a media processor component to encode, convert format, encrypt, or decrypt media content for Azure Media Services. Code samples are written in C# and use the Media Services SDK for .NET." metaCanonical="" services="media-services" documentationCenter="" title="How to: Get a Media Processor Instance" authors="juliako" solutions="" manager="dwrede" editor="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/30/2014" ms.author="juliako" />





<h1>方法:メディア プロセッサ インスタンスを取得する</h1>
この記事は、Azure メディア サービスのプログラミングを紹介するシリーズの一部です。前のトピックについては、「[方法: 暗号化されたアセットを作成してストレージにアップロードする](../media-services-create-encrypted-asset-upload-storage/)」を参照してください。

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
       <td>メディア エンコーダーを使用してエンコード タスクを実行できます。</td>
       <td><a href="http://msdn.microsoft.com/ja-jp/library/jj129582.aspx"> Azure メディア エンコーダー用のタスク プリセット文字列</a></td>
    </tr>
    <tr>
        <td>Windows Azure Media Packager</td>
        <td>メディア アセットを .mp4 からスムーズ ストリーミング形式に変換できます。また、スムーズ ストリーミングから Apple HTTP ライブ ストリーミング (HLS) 形式にメディア アセットを変換できます。</td>
		<td><a href="http://msdn.microsoft.com/ja-jp/library/hh973635.aspx">Azure Media Packager のタスク プリセット</a></td>
    </tr>
    <tr>
        <td>Windows Azure Media Encryptor</td>
        <td>PlayReady Protection を使用してメディア アセットを暗号化できます。</td>
        <td><a href="http://msdn.microsoft.com/ja-jp/library/hh973610.aspx">Azure Media Packager のタスク プリセット</a></td>
    </tr>
    <tr>
        <td>Azure メディア インデクサー</td>
        <td>メディア ファイルとコンテンツを検索可能にすると共に、クローズド キャプション トラックの生成を可能にします。</td>
		<td>該当なし</td>
    </tr>
    <tr>
        <td>Storage Decryption</td>
        <td>ストレージ暗号化を使用して暗号化されたメディア アセットを復号化できます。</td>
		<td>該当なし</td>
    </tr>  </tbody>
</table>

<br />

次のメソッドは、メディア プロセッサ インスタンスを取得する方法を示しています。このコード例では、モジュール レベルの変数 **_context** を使用してサーバー コンテキストを参照しています。「[方法:メディア サービスにプログラムから接続する]」を参照してください。

<pre><code>
private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
{
     var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
        ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

    if (processor == null)
        throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

    return processor;
}
</code></pre>

<h2>次のステップ</h2>
これで、プロセッサ インスタンスの取得方法を学習できました。次は、「[方法: アセットをエンコードする][]」トピックに進みます。このトピックでは、Azure メディア エンコーダーを使用してアセットをエンコードする方法が説明されています。

[方法: アセットをエンコードする]: ../media-services-encode-asset/
[Azure Media Encoder 用のタスク プリセット文字列]: http://msdn.microsoft.com/ja-jp/library/jj129582.aspx
[方法: メディア サービスにプログラムから接続する]: ../media-services-set-up-computer/

<!--HONumber=35_1-->
