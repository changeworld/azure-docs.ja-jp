---
title: Azure Content Moderator - ビデオとトランスクリプトを .NET でモデレートする | Microsoft Docs
description: Content Moderator を使用して、ビデオとトランスクリプトを .NET でモデレートする方法。
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 1/27/2018
ms.author: sajagtap
ms.openlocfilehash: 0f851c030a05880d79a998ed4b4a941082c057b9
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/06/2018
ms.locfileid: "37865473"
---
# <a name="video-and-transcript-moderation-tutorial"></a>ビデオとトランスクリプトをモデレートするためのチュートリアル

Content Moderator のビデオ API を使用してビデオをモデレートし、人間参加型レビュー ツール内にビデオ レビューを作成できます。 

この詳細なチュートリアルは、コンピューター支援型のモデレーションと人間参加型レビューの作成を含む完全なビデオとトランスクリプトのモデレーション ソリューションをビルドする方法を理解するうえで役立ちます。

このチュートリアル用の [C# コンソール アプリケーション ](https://github.com/MicrosoftContentModerator/VideoReviewConsoleApp)をダウンロードします。 コンソール アプリケーションは、SDK と関連パッケージを使用して、以下のタスクを実行します。

- 入力ビデオを圧縮して処理を高速化する
- ビデオをモデレートして、ショットとフレームを分析情報付きで取得する
- フレームのタイムスタンプを使用して、サムネイル (画像) を作成する
- タイムスタンプとサムネイルを送信してビデオ レビューを作成する
- Media Indexer API を使用してビデオの音声をテキスト (トランスクリプト) に変換する
- トランスクリプトをテキスト モデレーション サービスを使用してモデレートする
- モデレートされたトランスクリプトをビデオ レビューに追加する

## <a name="sample-program-outputs"></a>サンプル プログラムの出力

先に進む前に、プログラムからの次のサンプル出力を確認してください。

- [コンソール出力](#program-output)
- [ビデオ レビュー](#video-review-default-view)
- [トランスクリプト ビュー](#video-review-transcript-view)

## <a name="prerequisites"></a>前提条件

1. [Content Moderator レビュー ツール](https://contentmoderator.cognitive.microsoft.com/) Web サイトにサインアップし、C# コンソール アプリケーションがコード内から割り当てる[カスタム タグを作成](Review-Tool-User-Guide/tags.md)します。 次のスクリーンショットは、カスタム タグを示しています。

  ![ビデオのモデレーションのカスタム タグ](images/video-tutorial-custom-tags.png)

1. サンプル アプリケーションを実行するには、Azure アカウントと Azure Media Services アカウントが必要です。 さらに、Content Moderator のプライベート プレビューにアクセスする必要があります。 最後に、Azure Active Directory 認証資格情報が必要です。 この情報を取得する方法の詳細については、[Video Moderation API のクイック スタート](video-moderation-api.md)に関する記事を参照してください。

1. `App.config` ファイルを編集して、Active Directory テナントの名前、サービス エンドポイント、およびサブスクリプション キー (`#####` で示されます) を追加します。 次の情報が必要です。

|キー|説明|
|-|-|
|`AzureMediaServiceRestApiEndpoint`|Azure Media Services (AMS) API のエンドポイント|
|`ClientSecret`|Azure Media Services のサブスクリプション キー|
|`ClientId`|Azure Media Services のクライアント ID|
|`AzureAdTenantName`|組織を表す Active Directory テナント名|
|`ContentModeratorReviewApiSubscriptionKey`|Content Moderator レビュー API のサブスクリプション キー|
|`ContentModeratorApiEndpoint`|Content Moderator API のエンドポイント|
|`ContentModeratorTeamId`|Content Moderator のチーム ID|

## <a name="getting-started"></a>使用の開始

ビデオ モデレーション アプリケーションに対するメイン エントリ ポイントは、`Program.cs` 内の `Program` クラスです。

### <a name="methods-of-class-program"></a>Program クラスのメソッド

|方法|説明|
|-|-|
|`Main`|コマンド ラインを解析し、ユーザー入力を収集し、処理を開始します。|
|`ProcessVideo`|圧縮、アップロード、モデレーションを行って、ビデオ レビューを作成します。|
|`CreateVideoStreamingRequest`|ビデオをアップロードするためのストリームを作成します。|
|`GetUserInputs`|ユーザーの入力を収集します。コマンド ライン オプションが指定されていないときに使用されます。|
|`Initialize`|モデレーション プロセスに必要なオブジェクトを初期化します。|

### <a name="the-main-method"></a>Main メソッド

`Main()` は、実行が開始される場所であるため、ビデオ モデレーション プロセスを理解するための開始点となります。

    static void Main(string[] args)
    {
        if (args.Length == 0)
        {
            string videoPath = string.Empty;
            GetUserInputs(out videoPath);
            Initialize();
            AmsConfigurations.logFilePath = Path.Combine(Path.GetDirectoryName(videoPath), "log.txt");
            try
            {
                ProcessVideo(videoPath).Wait();
            }
            catch (Exception ex)
            {
                Console.WriteLine(ex.Message);
            }
        }
        else
        {
            DirectoryInfo directoryInfo = new DirectoryInfo(args[0]);
            if (args.Length == 2)
                bool.TryParse(args[1], out generateVtt);
            Initialize();
            AmsConfigurations.logFilePath = Path.Combine(args[0], "log.txt");
            var files = directoryInfo.GetFiles("*.mp4", SearchOption.AllDirectories);
            foreach (var file in files)
            {
                try
                {
                    ProcessVideo(file.FullName).Wait();
                }
                catch (Exception ex)
                {
                    Console.WriteLine(ex.Message);
                }
            }
        }
    }

`Main()` は、次のコマンドライン引数を処理します。

- モデレーションのために送信される MPEG-4 ビデオ ファイルを含むディレクトリへのパス。 このディレクトリとそのサブディレクトリ内のすべての `*.mp4` ファイルがモデレーションのために送信されます。
- (省略可能) 音声をモデレートするためにテキスト トランスクリプトを生成する必要があるかどうかを示すブール値 (true/false)。

コマンド ライン引数が指定されていない場合、`Main()` は `GetUserInputs()` を呼び出します。 このメソッドは、1 つのビデオ ファイルへのパスの入力と、テキスト トランスクリプトを生成するかどうかを指定するようにユーザーに要求します。

> [!NOTE]
> コンソール アプリケーションは、[Azure Media Indexer API](https://docs.microsoft.com/azure/media-services/media-services-process-content-with-indexer2) を使用して、アップロードされたビデオのオーディオ トラックからトランスクリプトを生成します。結果は WebVTT 形式で提供されます。 この形式の詳細については、[Web ビデオ テキスト トラック形式](https://developer.mozilla.org/en-US/docs/Web/API/WebVTT_API)に関するページをご覧ください。

### <a name="initialize-and-processvideo-methods"></a>Initialize メソッドと ProcessVideo メソッド

プログラムのオプションの指定元 (コマンド ラインまたは対話型のユーザー入力) に関係なく、`Main()` は、次に `Initialize()` を呼び出して、次のインスタンスを作成します。

|クラス|説明|
|-|-|
|`AMSComponent`|モデレーション用に送信する前に、ビデオ ファイルを圧縮します。|
|`AMSconfigurations`|`App.config` 内のアプリケーションの構成データに接続します。|
|`VideoModerator`| AMS SDK を使用して、アップロード、エンコード、暗号化、およびモデレーションを実行します。|
|`VideoReviewApi`|ビデオ レビューを Content Moderator サービスで管理します。|

これらのクラスについては、このチュートリアルの以降のセクションで詳しく説明します (`AMSConfigurations` は単純なので除外されています)。

最後に、ビデオ ファイルごとに `ProcessVideo()` を呼び出すことで、ファイルが 1 度に 1 つづつ処理されます。

    private static async Task ProcessVideo(string videoPath)
    {
        Stopwatch sw = new Stopwatch();
        sw.Start();
        Console.ForegroundColor = ConsoleColor.White;
        Console.WriteLine("\nVideo compression process started...");

        var compressedVideoPath = amsComponent.CompressVideo(videoPath);
        if (string.IsNullOrWhiteSpace(compressedVideoPath))
        {
            Console.ForegroundColor = ConsoleColor.Red;
            Console.WriteLine("Video Compression failed.");
        }

        Console.WriteLine("\nVideo compression process completed...");

        UploadVideoStreamRequest uploadVideoStreamRequest = CreateVideoStreamingRequest(compressedVideoPath);
        UploadAssetResult uploadResult = new UploadAssetResult();

        if (generateVtt)
        {
            uploadResult.GenerateVTT = generateVtt;
        }
        Console.WriteLine("\nVideo moderation process started...");

        if (!videoModerator.CreateAzureMediaServicesJobToModerateVideo(uploadVideoStreamRequest, uploadResult))
        {
            Console.ForegroundColor = ConsoleColor.Red;
            Console.WriteLine("Video Review process failed.");
        }

        Console.WriteLine("\nVideo moderation process completed...");
        Console.WriteLine("\nVideo review process started...");
        string reviewId = await videoReviewApi.CreateVideoReviewInContentModerator(uploadResult);
        Console.WriteLine("\nVideo review successfully completed...");
        sw.Stop();
        Console.WriteLine("\nTotal Elapsed Time: {0}", sw.Elapsed);
        using (var stw = new StreamWriter(AmsConfigurations.logFilePath, true))
        {
            stw.WriteLine("Video File Name: " + Path.GetFileName(videoPath));
            stw.WriteLine($"ReviewId: {reviewId}");
            stw.WriteLine("Total Elapsed Time: {0}", sw.Elapsed);
        }
    }


`ProcessVideo()` メソッドは、非常にわかりやすいものになっています。 それは、次の操作をこの順序で実行します。

- ビデオを圧縮する
- ビデオを Azure Media Services 資産にアップロードする
- AMS ジョブを作成してビデオをモデレートする
- Content Moderator でビデオ レビューを作成する

次のセクションでは、`ProcessVideo()` によって呼び出される個別のプロセスについて、詳しく検討します。 

## <a name="compressing-the-video"></a>ビデオの圧縮

ネットワーク トラフィックを最小限に抑えるために、アプリケーションは、ビデオ ファイルを H.264 (MPEG-4 AVC) 形式に変換し、640 ピクセルの最大幅にスケーリングします。 H.264 コーデックは、高効率 (高圧縮率) であるため、推奨されています。 圧縮は、Visual Studio ソリューションの `Lib` フォルダーに含まれている、無料の `ffmpeg` コマンド ライン ツールを使用して実行されます。 `ffmpeg` は、もっとも一般的に使用されているビデオ ファイル形式とコーデックを含め、あらゆる形式の入力ファイルをサポートしています。

> [!NOTE]
> コマンド ライン オプションを使用してプログラムを起動するとき、モデレーションのために送信するビデオ ファイルを含むディレクトリを指定します。 このディレクトリ内のファイル名拡張子が `.mp4` であるすべてのファイルが処理されます。 その他のファイル名拡張子を処理するには、`Program.cs` 内の `Main()` メソッドを、目的の拡張子が含まれるように更新します。

1 つのビデオ ファイルを圧縮するコードは、`AMSComponent.cs` 内の `AmsComponent` クラスです。 この機能を担当するメソッドは `CompressVideo()` であり、これを次に示します。

    public string CompressVideo(string videoPath)
    {
        string ffmpegBlobUrl;
        if (!ValidatePreRequisites())
        {
            Console.WriteLine("Configurations check failed. Please cross check the configurations!");
            throw new Exception();
        }

        if (File.Exists(_configObj.FfmpegExecutablePath))
        {
            ffmpegBlobUrl = this._configObj.FfmpegExecutablePath;
        }
        else
        {
            Console.WriteLine("ffmpeg.exe is missing. Please check the Lib folder");
            throw new Exception();
        }

        string videoFilePathCom = videoPath.Split('.')[0] + "_c.mp4";
        ProcessStartInfo processStartInfo = new ProcessStartInfo();
        processStartInfo.WindowStyle = ProcessWindowStyle.Hidden;
        processStartInfo.FileName = ffmpegBlobUrl;
        processStartInfo.Arguments = "-i \"" + videoPath + "\" -vcodec libx264 -n -crf 32 -preset veryfast -vf scale=640:-1 -c:a aac -aq 1 -ac 2 -threads 0 \"" + videoFilePathCom + "\"";
        var process = Process.Start(processStartInfo);
        process.WaitForExit();
        process.Close();
        return videoFilePathCom;
    }

このコードは、次の手順を実行します。

- `App.config` 内の構成に必要なデータがすべて含まれていることを確認する
- `ffmpeg` バイナリが存在することを確認する
- ファイルの基本名に `_c.mp4` を追加することで、出力ファイル名を作成する (例: `Example.mp4` -> `E>xample_c.mp4`)
- 変換を実行するコマンド ライン文字列を作成する
- コマンド ラインを使用して `ffmpeg` プロセスを開始する
- ビデオの処理が終わるまで待機する

> [!NOTE]
> ビデオが H.264 を使用して既に圧縮され、適切なサイズになっていることがわかっている場合は、圧縮をスキップするように `CompressVideo()` を書き直すことができます。

このメソッドは、圧縮された出力ファイルのファイル名を返します。

## <a name="uploading-and-moderating-the-video"></a>ビデオのアップロードとモデレーション

ビデオは、Content Moderation サービスによって処理される前に、Azure Media Services に保存しておく必要があります。 `Program.cs` 内の `Program` クラスに、ビデオをアップロードするために使用されるストリーミング要求を表すオブジェクトを返す短い `CreateVideoStreamingRequest()` メソッドがあります。

    private static UploadVideoStreamRequest CreateVideoStreamingRequest(string compressedVideoFilePath)
    {
        return
            new UploadVideoStreamRequest
            {
                VideoStream = File.ReadAllBytes(compressedVideoFilePath),
                VideoName = Path.GetFileName(compressedVideoFilePath),
                EncodingRequest = new EncodingRequest()
                {
                    EncodingBitrate = AmsEncoding.AdaptiveStreaming
                },
                VideoFilePath = compressedVideoFilePath
            };
    }

結果の `UploadVideoStreamRequest` オブジェクトは、`UploadVideoStreamRequest.cs` 内に定義されます (およびその親である `UploadVideoRequest` が `UploadVideoRequest.cs` 内に定義されます)。 これらのクラスはここには示されていません。それらは短く、圧縮されたビデオ データと、それに関する情報を保持するためだけに機能します。 アップロード プロセスの結果を保持するために、別のデータのみクラスである `UploadAssetResult` (`UploadAssetResult.cs`) が使用されます。 これで、`ProcessVideo()` 内のこれらの行を理解することができます。

    UploadVideoStreamRequest uploadVideoStreamRequest = CreateVideoStreamingRequest(compressedVideoPath);
    UploadAssetResult uploadResult = new UploadAssetResult();

    if (generateVtt)
    {
        uploadResult.GenerateVTT = generateVtt;
    }
    Console.WriteLine("\nVideo moderation process started...");

    if (!videoModerator.CreateAzureMediaServicesJobToModerateVideo(uploadVideoStreamRequest, uploadResult))
    {
        Console.ForegroundColor = ConsoleColor.Red;
        Console.WriteLine("Video Review process failed.");
    }

これらの行は、以下のタスクを実行します。

- 圧縮されたビデオをアップロードするための `UploadVideoStreamRequest` を作成する
- ユーザーがテキスト トランスクリプトを要求した場合に、要求の `GenerateVTT` フラグを設定する
- `CreateAzureMediaServicesJobToModerateVideo()` を呼び出して、アップロードを実行し、結果を受信する

## <a name="deep-dive-into-video-moderation"></a>ビデオのモデレーションを深く掘り下げる

`CreateAzureMediaServicesJobToModerateVideo()` メソッドは `VideoModerator.cs` 内にあり、Azure Media Services とやり取りするためのコードの大部分が含まれています。 このメソッドのソース コードを、次の抜粋に示します。

    public bool CreateAzureMediaServicesJobToModerateVideo(UploadVideoStreamRequest uploadVideoRequest, UploadAssetResult uploadResult)
    {
        asset = CreateAsset(uploadVideoRequest);
        uploadResult.VideoName = uploadVideoRequest.VideoName;
        // Encoding the asset , Moderating the asset, Generating transcript in parallel
        IAsset encodedAsset = null;
        //Creates the job for the tasks.
        IJob job = this._mediaContext.Jobs.Create("AMS Review Job");

        //Adding encoding task to job.
        ConfigureEncodeAssetTask(uploadVideoRequest.EncodingRequest, job);

        ConfigureContentModerationTask(job);

        //adding transcript task to job.
        if (uploadResult.GenerateVTT)
        {
            ConfigureTranscriptTask(job);
        }

        Stopwatch timer = new Stopwatch();
        timer.Start();
        //submit and execute job.
        job.Submit();
        job.GetExecutionProgressTask(new CancellationTokenSource().Token).Wait();
        timer.Stop();
        using (var sw = new StreamWriter(AmsConfigurations.logFilePath, true))
        {
            sw.WriteLine("AMS Job Elapsed Time: {0}", timer.Elapsed);
        }

        if (job.State == JobState.Error)
        {
            throw new Exception("Video moderation has failed due to AMS Job error.");
        }

        UploadAssetResult result = uploadResult;
        encodedAsset = job.OutputMediaAssets[0];
        result.ModeratedJson = GetCmDetail(job.OutputMediaAssets[1]);
        // Check for valid Moderated JSON
        var jsonModerateObject = JsonConvert.DeserializeObject<VideoModerationResult>(result.ModeratedJson);

        if (jsonModerateObject == null)
        {
            return false;
        }
        if (uploadResult.GenerateVTT)
        {
            GenerateTranscript(job.OutputMediaAssets.Last());
        }

        uploadResult.StreamingUrlDetails = PublishAsset(encodedAsset);
        string downloadUrl = GenerateDownloadUrl(asset, uploadVideoRequest.VideoName);
        uploadResult.StreamingUrlDetails.DownloadUri = downloadUrl;
        uploadResult.VideoName = uploadVideoRequest.VideoName;
        uploadResult.VideoFilePath = uploadVideoRequest.VideoFilePath;
        return true;
    }

このコードは、以下のタスクを実行します。

- 実行される処理の AMS ジョブを作成する
- ビデオ ファイルのエンコード、モデレーション、およびテキスト トランスクリプトの生成を行うタスクを追加する
- ジョブを送信し、ファイルをアップロードして処理を開始する
- モデレーション結果、テキスト トランスクリプト (要求された場合)、およびその他の情報を取得する

## <a name="sample-video-moderation-response"></a>サンプル ビデオのモデレーション応答

ビデオ モデレーション ジョブの結果 ([ビデオ モデレーションのクイック スタート](video-moderation-api.md)を参照) は、モデレーション結果を含む JSON データ構造です。 これらの結果には、ビデオ内のフラグメント (ショット) の内訳が含まれ、各フラグメントにはイベント (クリップ) とレビュー用フラグが設定されたキー フレームが含まれています。 各キー フレームは、成人向けまたはわいせつなコンテンツが含まれている可能性によってスコア付けされています。 次の例は、JSON 応答を示します。

    {
        "version": 2,
        "timescale": 90000,
        "offset": 0,
        "framerate": 50,
        "width": 1280,
        "height": 720,
        "totalDuration": 18696321,
        "fragments": [
        {
            "start": 0,
            "duration": 18000
        },
        {
            "start": 18000,
            "duration": 3600,
            "interval": 3600,
            "events": [
            [
            {
                "reviewRecommended": false,
                "adultScore": 0.00001,
                "racyScore": 0.03077,
                "index": 5,
                "timestamp": 18000,
                "shotIndex": 0
            }
            ]
        ]
        },
        {
            "start": 18386372,
            "duration": 119149,
            "interval": 119149,
            "events": [
            [
            {
                "reviewRecommended": true,
                "adultScore": 0.00000,
                "racyScore": 0.91902,
                "index": 5085,
                "timestamp": 18386372,
                "shotIndex": 62
            }
        ]
        ]
        }
    ]
    }

`GenerateVTT` フラグが設定されると、ビデオの音声のトランスクリプトも生成されます。

> [!NOTE]
> コンソール アプリケーションは、[Azure Media Indexer API](https://docs.microsoft.com/azure/media-services/media-services-process-content-with-indexer2) を使用して、アップロードされたビデオのオーディオ トラックからトランスクリプトを生成します。結果は WebVTT 形式で提供されます。 この形式の詳細については、[Web ビデオ テキスト トラック形式](https://developer.mozilla.org/en-US/docs/Web/API/WebVTT_API)に関するページをご覧ください。


## <a name="creating-the-human-in-the-loop-review"></a>人間参加型レビューの作成

モデレーション プロセスは、ビデオからキー フレームの一覧を、オーディオ トラックのトランスクリプトと共に返します。 次の手順は、Content Moderator レビュー ツールでの人間のモデレーター用のレビューの作成です。 `Program.cs` 内の `ProcessVideo()` メソッドに戻ると、`CreateVideoReviewInContentModerator()` メソッドへの呼び出しがあることがわかります。 このメソッドは、次に示すように、`VideoReviewAPI.cs` 内の `videoReviewApi` クラス内にあります。

    public async Task<string> CreateVideoReviewInContentModerator(UploadAssetResult uploadAssetResult)
    {
    
        string reviewId = string.Empty;
        List<ProcessedFrameDetails> frameEntityList = framegenerator.CreateVideoFrames(uploadAssetResult);
        string path = uploadAssetResult.GenerateVTT == true ? this._amsConfig.FfmpegFramesOutputPath + Path.GetFileNameWithoutExtension (uploadAssetResult.VideoName) + "_aud_SpReco.vtt" : "";
        TranscriptScreenTextResult screenTextResult = new TranscriptScreenTextResult();
        
    if (File.Exists(path))
        {
            screenTextResult = await GenerateTextScreenProfanity(reviewId, path, frameEntityList);
            uploadAssetResult.Category1TextScore = screenTextResult.Category1Score;
            uploadAssetResult.Category2TextScore = screenTextResult.Category2Score;
            uploadAssetResult.Category3TextScore = screenTextResult.Category3Score;
            uploadAssetResult.Category1TextTag = screenTextResult.Category1Tag;
            uploadAssetResult.Category2TextTag = screenTextResult.Category2Tag;
            uploadAssetResult.Category3TextTag = screenTextResult.Category3Tag;
        }
        
        var reviewVideoRequestJson = CreateReviewRequestObject(uploadAssetResult, frameEntityList);
        if (string.IsNullOrWhiteSpace(reviewVideoRequestJson))
        {
            throw new Exception("Video review process failed in CreateVideoReviewInContentModerator");
        }
        
        reviewId = JsonConvert.DeserializeObject<List<string>>(ExecuteCreateReviewApi(reviewVideoRequestJson).Result).FirstOrDefault();
        frameEntityList = framegenerator.GenerateFrameImages(frameEntityList, uploadAssetResult, reviewId);
        await CreateAndPublishReviewInContentModerator(uploadAssetResult, frameEntityList, reviewId, path, screenTextResult);
        return reviewId;
    
    }

`CreateVideoReviewInContentModerator()` は、以下のタスクを実行するさまざまなメソッドを呼び出します。

> [!NOTE]
> このコンソール アプリケーションでは、サムネイルを生成するために [FFmpeg](https://ffmpeg.org/) ライブラリを使用しています。 これらのサムネイル (画像) は、[ビデオ モデレーション出力](#sample-video-moderation-response)内のフレーム タイムスタンプに対応します。

|タスク|メソッド|ファイル|
|-|-|-|
|ビデオからキー フレームを抽出し、それらのサムネイル画像を作成する|`CreateVideoFrames()`<br>`GenerateFrameImages()`|`FrameGeneratorServices.cs`|
|テキスト トランスクリプト (利用できる場合) をスキャンし、成人向けまたはわいせつな音声を見つける|`GenerateTextScreenProfanity()`| `VideoReviewAPI.cs`|
|人間が検査するためのビデオ レビュー要求を準備して送信する|`CreateReviewRequestObject()`<br> `ExecuteCreateReviewApi()`<br>`CreateAndPublishReviewInContentModerator()`|`VideoReviewAPI.cs`|

## <a name="video-review-default-view"></a>ビデオ レビューの既定のビュー

次の画面は、上記の手順の結果を示しています。

![ビデオ レビューの既定のビュー](images/video-tutorial-default-view.PNG)

## <a name="transcript-generation"></a>トランスクリプトの生成

このチュートリアルでここまでに提示したコードは、ビジュアル コンテンツが中心になっています。 音声コンテンツのレビューは、前述したように、音声から生成されたトランスクリプトを使用する独立した省略可能なプロセスです。 ここでは、テキスト スクリプトがどのように作成され、レビュープロセスで使用されるかを調べていきます。 トランスクリプトを生成するタスクは、[Azure Media Indexer](https://docs.microsoft.com/azure/media-services/media-services-index-content) サービスで実行されます。

このアプリケーションは、以下のタスクを実行します。

|タスク|メソッド|ファイル|
|-|-|-|
|テキスト トランスクリプトを生成するかどうかを決定する|`Main()`<br>`GetUserInputs()`|`Program.cs`|
|生成する場合は、モデレーションの一部としてトランスクリプション ジョブを送信する|`ConfigureTranscriptTask()`|`VideoModerator.cs`|
|トランスクリプトのローカル コピーを取得する|`GenerateTranscript()`|`VideoModerator.cs`|
|不適切な音声を含むビデオのフレームにフラグを設定する|`GenerateTextScreenProfanity()`<br>`TextScreen()`|`VideoReviewAPI.cs`|
|結果をレビューに追加する|`UploadScreenTextResult()`<br>`ExecuteAddTranscriptSupportFile()`|`VideoReviewAPI.cs`|

### <a name="task-configuration"></a>タスクの構成

トランスクリプション ジョブの送信に飛びましょう。 `CreateAzureMediaServicesJobToModerateVideo()` (説明済み) は、`ConfigureTranscriptTask()` を呼び出します。

    private void ConfigureTranscriptTask(IJob job)
    {
        string mediaProcessorName = _amsConfigurations.MediaIndexer2MediaProcessor;
        IMediaProcessor processor = _mediaContext.MediaProcessors.GetLatestMediaProcessorByName(mediaProcessorName);

        string configuration = File.ReadAllText(_amsConfigurations.MediaIndexerConfigurationJson);
        ITask task = job.Tasks.AddNew("AudioIndexing Task", processor, configuration, TaskOptions.None);
        task.InputAssets.Add(asset);
        task.OutputAssets.AddNew("AudioIndexing Output Asset", AssetCreationOptions.None);
    }

トランスクリプト タスクの構成は、ソリューションの `Lib` フォルダー内の `MediaIndexerConfig.json` ファイルから読み取られます。 構成ファイル用とトランスクリプション プロセスの出力用の AMS 資産が生成されます。 AMS ジョブが実行されると、このタスクは、ビデオ ファイルのオーディオ トラックからテキスト トランスクリプトを作成します。

> [!NOTE]
> サンプル アプリケーションは、英語 (米国) の音声のみを認識します。

### <a name="transcript-generation"></a>トランスクリプトの生成

トランスクリプトは、AMS 資産として発行されます。 好ましくないコンテンツのトランスクリプトをスキャンするため、アプリケーションは、Azure Media Services から資産をダウンロードします。 `CreateAzureMediaServicesJobToModerateVideo()` は、次に示す `GenerateTranscript()` を呼び出してファイルを取得します。

    public bool GenerateTranscript(IAsset asset)
    {
        try
        {
            var outputFolder = this._amsConfigurations.FfmpegFramesOutputPath;
            IAsset outputAsset = asset;
            IAccessPolicy policy = null;
            ILocator locator = null;
            policy = _mediaContext.AccessPolicies.Create("My 30 days readonly policy", TimeSpan.FromDays(360), AccessPermissions.Read);
            locator = _mediaContext.Locators.CreateLocator(LocatorType.Sas, outputAsset, policy, DateTime.UtcNow.AddMinutes(-5));
            DownloadAssetToLocal(outputAsset, outputFolder);
            locator.Delete();
            return true;
        }
        catch
        {   //TODO:  Logging
            Console.WriteLine("Exception occured while generating index for video.");
            throw;
        }
    }

必要な AMS の設定を行った後、AMS 資産をローカル ファイルにコピーする汎用関数である `DownloadAssetToLocal()` を呼び出すことで、実際のダウンロードが実行されます。

## <a name="transcript-moderation"></a>トランスクリプトのモデレーション

用意されたトランスクリプトをスキャンして、レビューで使用します。 レビューの作成は `CreateVideoReviewInContentModerator()` の守備範囲であり、それが `GenerateTextScreenProfanity()` を呼び出してジョブを実行します。 次に、このメソッドが、機能の大半が含まれている `TextScreen()` を呼び出します。 

`TextScreen()` は、以下のタスクを実行します。

- トランスクリプトのタイムスタンプとキャプションを解析する
- テキスト モデレーション用に各キャプションを送信する
- 好ましくない音声コンテンツが含まれている可能性があるフレームにフラグを設定する

これらのタスクを詳しく調べていきましょう。

### <a name="initialize-the-code"></a>コードを初期化する

最初に、すべての変数とコレクションを初期化します。

    private async Task<TranscriptScreenTextResult> TextScreen(string filepath, List<ProcessedFrameDetails> frameEntityList)
    {
        List<TranscriptProfanity> profanityList = new List<TranscriptProfanity>();
        string responseContent = string.Empty;
        HttpResponseMessage response;
        bool category1Tag = false;
        bool category2Tag = false;
        bool category3Tag = false;
        double category1Score = 0;
        double category2Score = 0;
        double category3Score = 0;
        List<string> vttLines = File.ReadAllLines(filepath).Where(line => !line.Contains("NOTE Confidence:") && line.Length > 0).ToList();
        StringBuilder sb = new StringBuilder();
        List<CaptionScreentextResult> csrList = new List<CaptionScreentextResult>();
        CaptionScreentextResult captionScreentextResult = new CaptionScreentextResult() { Captions = new List<string>() };

        // Code from the next sections in the tutorial
    

### <a name="parse-the-transcript-for-captions"></a>トランスクリプトのキャプションを解析する

次に、VTT 形式のトランスクリプトのキャプションとタイムスタンプを解析します。 レビュー ツールは、これらのキャプションをビデオ レビュー画面の [トランスクリプト] タブに表示します。 タイムスタンプを使用して、キャプションを対応するビデオ フレームに同期させます。

        // Code from the previous section(s) in the tutorial

        //
        // Parse the transcript
        //
        foreach (var line in vttLines.Skip(1))
        {
                if (line.Contains("-->"))
                {
                    if (sb.Length > 0)
                    {
                        captionScreentextResult.Captions.Add(sb.ToString());
                        sb.Clear();
                    }
                    if (captionScreentextResult.Captions.Count > 0)
                    {
                        csrList.Add(captionScreentextResult);
                        captionScreentextResult = new CaptionScreentextResult() { Captions = new List<string>() };
                    }
                    string[] times = line.Split(new string[] { "-->" }, StringSplitOptions.RemoveEmptyEntries);
                    string startTimeString = times[0].Trim();
                    string endTimeString = times[1].Trim();
                    int startTime = (int)TimeSpan.ParseExact(startTimeString, @"hh\:mm\:ss\.fff", CultureInfo.InvariantCulture).TotalMilliseconds;
                    int endTime = (int)TimeSpan.ParseExact(endTimeString, @"hh\:mm\:ss\.fff", CultureInfo.InvariantCulture).TotalMilliseconds;
                    captionScreentextResult.StartTime = startTime;
                    captionScreentextResult.EndTime = endTime;
                }
                else
                {
                    sb.Append(line);
                }
                if (sb.Length + line.Length > 1024)
                {
                    captionScreentextResult.Captions.Add(sb.ToString());
                    sb.Clear();
                }
            }
            if (sb.Length > 0)
            {
                captionScreentextResult.Captions.Add(sb.ToString());
            }
            if (captionScreentextResult.Captions.Count > 0)
            {
                csrList.Add(captionScreentextResult);
            }

            // Code from the following section in the quickstart

### <a name="moderate-captions-with-the-text-moderation-service"></a>キャプションをテキスト モデレーション サービスを使用してモデレートする

次に、解析されたテキスト キャプションを、Content Moderator のテキスト API でスキャンします。

> [!NOTE]
> Content Moderator サービス キーには、秒単位の要求数 (RPS) の制限があります。 この制限を超えた場合、SDK は 429 エラー コードを使用して例外をスローします。 
>
> 無料レベルのキーの RPS は 1 に制限されています。

    //
    // Moderate the captions or cues
    //
    int waitTime = 1000;
    foreach (var csr in csrList)
    {
                bool captionAdultTextTag = false;
                bool captionRacyTextTag = false;
                bool captionOffensiveTextTag = false;
                bool retry = true;

                foreach (var caption in csr.Captions)
                {
                    while (retry)
                    {
                        try
                        {
                            System.Threading.Thread.Sleep(waitTime);
                            var lang = await CMClient.TextModeration.DetectLanguageAsync("text/plain", caption);
                            var oRes = await CMClient.TextModeration.ScreenTextWithHttpMessagesAsync(lang.DetectedLanguageProperty, "text/plain", caption, null, null, null, true);
                            response = oRes.Response;
                            responseContent = await response.Content.ReadAsStringAsync();
                            retry = false;
                        }
                        catch (Exception e)
                        {
                            if (e.Message.Contains("429"))
                            {
                                Console.WriteLine($"Moderation API call failed. Message: {e.Message}");
                                waitTime = (int)(waitTime * 1.5);
                                Console.WriteLine($"wait time: {waitTime}");
                            }
                            else
                            {
                                retry = false;
                                Console.WriteLine($"Moderation API call failed. Message: {e.Message}");
                            }
                        }
                    }
                    var jsonTextScreen = JsonConvert.DeserializeObject<TextScreen>(responseContent);
                    if (jsonTextScreen != null)
                    {
                        TranscriptProfanity transcriptProfanity = new TranscriptProfanity();
                        transcriptProfanity.TimeStamp = "";
                        List<Terms> transcriptTerm = new List<Terms>();
                        if (jsonTextScreen.Terms != null)
                        {
                            foreach (var term in jsonTextScreen.Terms)
                            {
                                var profanityobject = new Terms
                                {
                                    Term = term.Term,
                                    Index = term.Index
                                };
                                transcriptTerm.Add(profanityobject);
                            }
                            transcriptProfanity.Terms = transcriptTerm;
                            profanityList.Add(transcriptProfanity);
                        }
                        if (jsonTextScreen.Classification.Category1.Score > _amsConfig.Category1TextThreshold) captionAdultTextTag = true;
                        if (jsonTextScreen.Classification.Category2.Score > _amsConfig.Category2TextThreshold) captionRacyTextTag = true;
                        if (jsonTextScreen.Classification.Category3.Score > _amsConfig.Category3TextThreshold) captionOffensiveTextTag = true;
                        if (jsonTextScreen.Classification.Category1.Score > _amsConfig.Category1TextThreshold) category1Tag = true;
                        if (jsonTextScreen.Classification.Category2.Score > _amsConfig.Category2TextThreshold) category2Tag = true;
                        if (jsonTextScreen.Classification.Category3.Score > _amsConfig.Category3TextThreshold) category3Tag = true;
                        category1Score = jsonTextScreen.Classification.Category1.Score > category1Score ? jsonTextScreen.Classification.Category1.Score : category1Score;
                        category2Score = jsonTextScreen.Classification.Category2.Score > category2Score ? jsonTextScreen.Classification.Category2.Score : category2Score;
                        category3Score = jsonTextScreen.Classification.Category3.Score > category3Score ? jsonTextScreen.Classification.Category3.Score : category3Score;
                    }
                    foreach (var frame in frameEntityList.Where(x => x.TimeStamp >= csr.StartTime && x.TimeStamp <= csr.EndTime))
                    {
                        frame.IsAdultTextContent = captionAdultTextTag;
                        frame.IsRacyTextContent = captionRacyTextTag;
                        frame.IsOffensiveTextContent = captionOffensiveTextTag;
                    }
                }
            }
            TranscriptScreenTextResult screenTextResult = new TranscriptScreenTextResult()
            {
                TranscriptProfanity = profanityList,
                Category1Tag = category1Tag,
                Category2Tag = category2Tag,
                Category3Tag = category3Tag,
                Category1Score = category1Score,
                Category2Score = category2Score,
                Category3Score = category3Score
            };
            return screenTextResult;
    }

### <a name="breaking-down-the-text-moderation-step"></a>テキスト モデレーション手順の詳細

`TextScreen()` は大きなメソッドであるため、分解してみましょう。

1. 最初に、このメソッドは、トランスクリプト ファイルを 1 行づつ読み取ります。 空白行と、`NOTE` と信頼度スコアを含む行は無視されます。 ファイル内の "*キュー*" からタイムスタンプとテキスト項目を抽出します。 キューは、オーディオ トラックからのテキストを表し、開始時刻と終了時刻が含まれています。 キューは、文字列 `-->` を含むタイム スタンプ行で始まります。 1 行または複数行のテキストが続きます。

1. `CaptionScreentextResult` インスタンス (`TranscriptProfanity.cs`内に定義されます) を使用して、各キューから解析された情報を保持します。  新しいタイムスタンプ行が検出されるか、テキストの最大長である 1024 文字に達すると、新しい `CaptionScreentextResult` が `csrList` に追加されます。 

1. 次に、メソッドは、各キューをテキスト モデレーション API に送信します。 `Microsoft.Azure.CognitiveServices.ContentModerator` アセンブリ内に定義されている `ContentModeratorClient.TextModeration.DetectLanguageAsync()` と `ContentModeratorClient.TextModeration.ScreenTextWithHttpMessagesAsync()` の両方を呼び出します。 レート制限を回避するために、メソッドは、各キューを送信する前に一瞬一時停止します。

1. Text Moderation サービスから結果を受信した後、メソッドは、それらを分析して、信頼度のしきい値が満たされているかどうかを確認します。 これらの値は、`App.config` 内に `OffensiveTextThreshold`、`RacyTextThreshold`、および `AdultTextThreshold` として設定されています。 最後に、好ましくない用語そのものも保存されます。 キューの時間範囲内のすべてのフレームに、不快、わいせつ、成人向けのテキストが含まれているとするフラグが設定されます。

1. `TextScreen()` は、全体としてのビデオのテキスト モデレーションの結果を含む `TranscriptScreenTextResult` インスタンスを返します。 このオブジェクトには、さまざまな種類の好ましくないコンテンツのフラグとスコアが、すべての好ましくない用語の一覧と共に含まれています。 呼び出し元の `CreateVideoReviewInContentModerator()` は、`UploadScreenTextResult()` を呼び出してこの情報をレビューにアタッチして、人間のレビュー担当者が利用できるようにします。
 
## <a name="video-review-transcript-view"></a>ビデオ レビューのトランスクリプト ビュー

次の画面は、トランスクリプトの生成結果とモデレーション手順を示しています。

![ビデオ モデレーションのトランスクリプト ビュー](images/video-tutorial-transcript-view.PNG)

## <a name="program-output"></a>プログラムの出力

次のプログラムからのコマンド ライン出力は、さまざまなタスクが完了したときの出力を示しています。 さらに、モデレーションの結果 (JSON 形式) と音声のトランスクリプトは、元のビデオ ファイルと同じディレクトリで利用できます。

    Microsoft.ContentModerator.AMSComponentClient
    Enter the fully qualified local path for Uploading the video :
    "Your File Name.MP4"
    Generate Video Transcript? [y/n] : y
    
    Video compression process started...
    Video compression process completed...
    
    Video moderation process started...
    Video moderation process completed...
    
    Video review process started...
    Video Frames Creation inprogress...
    Frames(83) created successfully.
    Review Created Successfully and the review Id 201801va8ec2108d6e043229ba7a9e6373edec5
    Video review successfully completed...
    
    Total Elapsed Time: 00:05:56.8420355


## <a name="next-steps"></a>次の手順

このチュートリアルで必要な [Visual Studio ソリューション、サンプル ファイル、およびライブラリをダウンロード](https://github.com/MicrosoftContentModerator/VideoReviewConsoleApp)し、お使いの統合で開始します。
