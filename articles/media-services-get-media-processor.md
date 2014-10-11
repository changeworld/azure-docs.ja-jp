<properties linkid="develop-media-services-how-to-guides-create-media-processor" urlDisplayName="Create a Media Processor" pageTitle="How to Create a Media Processor - Azure" metaKeywords="" description="Learn how to create a media processor component to encode, convert format, encrypt, or decrypt media content for Azure Media Services. Code samples are written in C# and use the Media Services SDK for .NET." metaCanonical="" services="media-services" documentationCenter="" title="How to: Get a Media Processor Instance" authors="migree" solutions="" manager="" editor="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="migree"></tags>

# 方法: メディア プロセッサ インスタンスを取得する

この記事は、Azure メディア サービスのプログラミングを紹介するシリーズの一部です。前のトピックについては、[暗号化されたアセットを作成してストレージにアップロードする方法][]に関するページを参照してください。

メディア プロセッサは、メディア サービスのコンポーネントとして、メディア コンテンツのエンコード、形式変換、暗号化、復号化など、特定の処理タスクを担います。通常、メディア コンテンツのエンコード、暗号化、形式変換を行うタスクの作成時にメディア プロセッサを作成します。

次の表は、利用可能なメディア プロセッサの名前と説明の一覧です。

<table border="1">
<tr><th>メディア プロセッサ名</th><th>説明</th><th>詳細</th></tr>
<tr><td>Azure メディア エンコーダー</td><td>メディア エンコーダーを使用してエンコード タスクを実行できます。</td><td><a href="http://msdn.microsoft.com/en-us/library/jj129582.aspx">Azure メディア エンコーダー用のタスク プリセット文字列</a></td></tr>
<tr><td>Azure Media Packager</td><td>メディア アセットを .mp4 からスムーズ ストリーミング形式に変換できます。また、スムーズ ストリーミングから Apple HTTP ライブ ストリーミング (HLS) 形式にメディア アセットを変換できます。</td><td><a href="http://msdn.microsoft.com/en-us/library/hh973635.aspx">Azure Media Packager のタスク プリセット</a></td></tr>
<tr><td>Azure Media Encryptor</td><td>PlayReady Protection を使用してメディア アセットを暗号化できます。</td><td><a href="http://msdn.microsoft.com/en-us/library/hh973610.aspx">Azure Media Packager のタスク プリセット</a></td></tr>
<tr><td>Storage Decryption</td><td>ストレージ暗号化を使用して暗号化されたメディア アセットを復号化できます。</td><td>該当なし</td></tr>
</table>

次のメソッドは、メディア プロセッサ インスタンスを取得する方法を示しています。このコード例では、モジュール レベルの変数 **_context** を使用してサーバー コンテキストを参照しています。[「方法: メディア サービスにプログラムから接続する」][]を参照してください。

    private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
    {
         var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
            ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

        if (processor == null)
            throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

        return processor;
    }

## 次のステップ

これで、プロセッサ インスタンスの取得方法を学習できました。次は、[アセットをエンコードする方法][]に関するトピックに進み、Azure メディア エンコーダーを使用してアセットをエンコードする方法を学習します。

  [暗号化されたアセットを作成してストレージにアップロードする方法]: http://go.microsoft.com/fwlink/?LinkID=301733&clcid=0x409
  [Azure メディア エンコーダー用のタスク プリセット文字列]: http://msdn.microsoft.com/en-us/library/jj129582.aspx
  [Azure Media Packager のタスク プリセット]: http://msdn.microsoft.com/en-us/library/hh973635.aspx
  [1]: http://msdn.microsoft.com/en-us/library/hh973610.aspx
  [「方法: メディア サービスにプログラムから接続する」]: http://www.windowsazure.com/en-us/develop/media-services/how-to-guides/set-up-computer-for-media-services
  [アセットをエンコードする方法]: http://go.microsoft.com/fwlink/?LinkId=301753
