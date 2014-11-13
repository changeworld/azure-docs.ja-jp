<properties urlDisplayName="Create a Media Processor" pageTitle="メディア プロセッサを作成する方法 - Azure" metaKeywords="" description="メディア プロセッサ コンポーネントを作成し、Azure Media Services 用にメディア コンテンツのエンコード、形式の変換、暗号化、または復号化を行う方法について説明します。コード サンプルは C# で記述され、Media Services SDK for .NET を利用しています。" metaCanonical="" services="media-services" documentationCenter="" title="方法: メディア プロセッサ インスタンスを取得する" authors="juliako" solutions="" manager="dwrede" editor="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/26/2014" ms.author="juliako" />

# 方法: メディア プロセッサ インスタンスを取得する

この記事は、Azure メディア サービスのプログラミングを紹介するシリーズの一部です。前のトピックについては、[暗号化されたアセットを作成してストレージにアップロードする方法][暗号化されたアセットを作成してストレージにアップロードする方法]に関するページを参照してください。

メディア プロセッサは、メディア サービスのコンポーネントとして、メディア コンテンツのエンコード、形式変換、暗号化、復号化など、特定の処理タスクを担います。通常、メディア コンテンツのエンコード、暗号化、形式変換を行うタスクの作成時にメディア プロセッサを作成します。

次の表は、利用可能なメディア プロセッサの名前と説明の一覧です。

| メディア プロセッサ名         | 説明                                                                                                                                                                                     | 詳細                                                       |
|-------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------|
| Azure メディア エンコーダー   | メディア エンコーダーを使用してエンコード タスクを実行できます。                                                                                                                         | [Azure メディア エンコーダー用のタスク プリセット文字列][Azure メディア エンコーダー用のタスク プリセット文字列] |
| Windows Azure Media Packager  | メディア アセットを .mp4 からスムーズ ストリーミング形式に変換できます。また、スムーズ ストリーミングから Apple HTTP ライブ ストリーミング (HLS) 形式にメディア アセットを変換できます。 | [Azure Media Packager のタスク プリセット][Azure Media Packager のタスク プリセット]               |
| Windows Azure Media Encryptor | PlayReady Protection を使用してメディア アセットを暗号化できます。                                                                                                                       | [Azure Media Packager のタスク プリセット][1]              |
| Azure メディア インデクサー   | メディア ファイルとコンテンツを検索可能にすると共に、クローズド キャプション トラックの生成を可能にします。                                                                              | 該当なし                                                   |
| Storage Decryption            | ストレージ暗号化を使用して暗号化されたメディア アセットを復号化できます。                                                                                                                | 該当なし                                                   |

次のメソッドは、メディア プロセッサ インスタンスを取得する方法を示しています。このコード例では、モジュール レベルの変数 \*\*\_context\*\* を使用してサーバー コンテキストを参照しています。[「方法: メディア サービスにプログラムから接続する」][「方法: メディア サービスにプログラムから接続する」]を参照してください。

    private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
    {
         var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
            ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

        if (processor == null)
            throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

        return processor;
    }

## 次のステップ

これで、プロセッサ インスタンスの取得方法を学習できました。次は、[アセットをエンコードする方法][アセットをエンコードする方法]に関するトピックに進み、Azure メディア エンコーダーを使用してアセットをエンコードする方法を学習します。

  [暗号化されたアセットを作成してストレージにアップロードする方法]: ../media-services-create-encrypted-asset-upload-storage/
  [Azure メディア エンコーダー用のタスク プリセット文字列]: http://msdn.microsoft.com/ja-jp/library/jj129582.aspx
  [Azure Media Packager のタスク プリセット]: http://msdn.microsoft.com/ja-jp/library/hh973635.aspx
  [1]: http://msdn.microsoft.com/ja-jp/library/hh973610.aspx
  [「方法: メディア サービスにプログラムから接続する」]: ../media-services-set-up-computer/
  [アセットをエンコードする方法]: ../media-services-encode-asset/
