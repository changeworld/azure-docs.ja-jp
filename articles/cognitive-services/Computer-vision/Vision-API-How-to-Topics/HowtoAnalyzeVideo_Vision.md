---
title: 例:リアルタイムのビデオ分析 - Computer Vision
titleSuffix: Azure Cognitive Services
description: ライブ ビデオ ストリームから取得したフレームに対して、Computer Vision API を使用して、ほぼリアルタイムに分析を実行します。
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 09/09/2019
ms.author: kefre
ms.custom: seodec18
ms.openlocfilehash: 25aed0f042050ebadbc6054fcbf0c68dbf782e5e
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/10/2019
ms.locfileid: "70859077"
---
# <a name="how-to-analyze-videos-in-real-time"></a>リアルタイムでビデオを分析する方法

このガイドでは、ライブ ビデオ ストリームから取得したフレームに対して、ほぼリアルタイムに分析を実行する方法を示します。 そのようなシステムの基本コンポーネントは、次のとおりです。

- ビデオ ソースからフレームを取得する
- 分析するフレームを選択する
- API にこれらのフレームを送信する
- API 呼び出しから返される各分析結果を消費する

これらのサンプルは C# で記述されており、コードは GitHub ([https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/)) にあります。

## <a name="the-approach"></a>アプローチ

ビデオ ストリームに対してほぼリアルタイムの分析を実行する際の問題を解決する方法は複数あります。 レベルの低いものから高いものの順に 3 つのアプローチの概説を開始します。

### <a name="a-simple-approach"></a>単純なアプローチ

ほぼリアルタイムの分析システムに対応する最も単純な設計は、無限ループであり、ここでは繰り返しごとに、フレームを取り込み、分析し、続いて結果を消費します。

```csharp
while (true)
{
    Frame f = GrabFrame();
    if (ShouldAnalyze(f))
    {
        AnalysisResult r = await Analyze(f);
        ConsumeResult(r);
    }
}
```

分析が軽量のクライアント側アルゴリズムで構成されている場合は、このアプローチが適しています。 ただし、分析がクラウドで行われるときは、それに伴う待ち時間のために、API 呼び出しに数秒かかることがあり、その間はイメージのキャプチャは行われず、スレッドは基本的に何もしません。 最大フレーム レートは、API 呼び出しの待ち時間によって制限されます。

### <a name="parallelizing-api-calls"></a>API 呼び出しの並列化

単純な単一スレッドのループは、軽量のクライアント側アルゴリズムに適していますが、クラウド API 呼び出しに関わる待ち時間には十分には適合しません。 この問題の解決策は、実行時間の長い API 呼び出しを、フレームの取り込みと並列に実行できるようにすることです。 C# の場合、タスクベースの並列化を使用して実現できます。

```csharp
while (true)
{
    Frame f = GrabFrame();
    if (ShouldAnalyze(f))
    {
        var t = Task.Run(async () =>
        {
            AnalysisResult r = await Analyze(f);
            ConsumeResult(r);
        }
    }
}
```

このアプローチでは、それぞれの分析を個別のタスクで起動しますが、タスクはバックグラウンドで実行でき、新しいフレームを取得し続けられます。 このため、API 呼び出しが返されるまで待機してメイン スレッドがブロックされることはなくなります。ただし、単純なバージョンで得られているいくつかの保証は失われます。複数の API 呼び出しが並列に行われ、結果が間違った順序で返されることがあります。 このアプローチでは、複数のスレッドが同時に ConsumeResult() 関数に入る可能性も生じますが、関数がスレッドセーフでない場合は、それが危険である可能性があります。 最後に、この単純なコードは作成されるタスクを追跡しないので、例外は自動的に表示されなくなります。 したがって、追加する最終的な構成要素は、分析タスクを追跡し、例外を発生させ、実行時間の長いタスクを強制終了し、一度に 1 つずつ正しい順序で結果が消費されるようにする "consumer" スレッドになります。

### <a name="a-producer-consumer-design"></a>プロデューサー/コンシューマー デザイン

最後の「プロデューサー/コンシューマー」システムでは、以前の無限ループに類似したプロデューサー スレッドを使用します。 ただし、プロデューサーは、利用できるようになるとすぐに分析結果を消費するのではなく、単にタスクをキュー入れて追跡します。

```csharp
// Queue that will contain the API call tasks.
var taskQueue = new BlockingCollection<Task<ResultWrapper>>();

// Producer thread.
while (true)
{
    // Grab a frame.
    Frame f = GrabFrame();

    // Decide whether to analyze the frame.
    if (ShouldAnalyze(f))
    {
        // Start a task that will run in parallel with this thread.
        var analysisTask = Task.Run(async () =>
        {
            // Put the frame, and the result/exception into a wrapper object.
            var output = new ResultWrapper(f);
            try
            {
                output.Analysis = await Analyze(f);
            }
            catch (Exception e)
            {
                output.Exception = e;
            }
            return output;
        }

        // Push the task onto the queue.
        taskQueue.Add(analysisTask);
    }
}
```

また、コンシューマー スレッドもあり、これはタスクをキューから取り出し、タスクが完了するのを待機して、結果を表示するか、スローされた例外を発生させます。 キューの使用により、システムの最大フレーム レートを制限せずに一度に 1 つずつ正しい順序で結果が使用されることを保証できます。

```csharp
// Consumer thread.
while (true)
{
    // Get the oldest task.
    Task<ResultWrapper> analysisTask = taskQueue.Take();
 
    // Await until the task is completed.
    var output = await analysisTask;

    // Consume the exception or result.
    if (output.Exception != null)
    {
        throw output.Exception;
    }
    else
    {
        ConsumeResult(output.Analysis);
    }
}
```

## <a name="implementing-the-solution"></a>ソリューションの実装

### <a name="getting-started"></a>Getting Started (概要)

できるだけ迅速にアプリを稼働させるために、多くのシナリオを実装できるだけ柔軟で使いやすくなるように考慮して上記のシステムを実装しています。 コードにアクセスするには、[https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis) に移動します。

ライブラリには、FrameGrabber クラスが含まれています。これは Web カメラからのビデオ フレームを処理するために、前述したプロデューサー/コンシューマー システムを実装しています。 ユーザーは、正確なフォームの API 呼び出しを指定でき、クラスは、新しいフレームが獲得されたとき、または新しい分析結果が利用可能になったときに呼び出しコードに知らせるためにイベントを使用します。

いくつかの可能性を示すため、ライブラリを使用する 2 つのサンプル アプリを取り上げます。 1 つは、単純なコンソール アプリであり、この簡略化バージョンを下に再現しています。 これは既定の Web カメラからのフレームを取得し、それらを顔検出のために Face API に送信します。

```csharp
using System;
using System.Linq;
using Microsoft.Azure.CognitiveServices.Vision.Face;
using Microsoft.Azure.CognitiveServices.Vision.Face.Models;
using VideoFrameAnalyzer;

namespace BasicConsoleSample
{
    internal class Program
    {
        const string ApiKey = "<your API key>";
        const string Endpoint = "https://<your API region>.api.cognitive.microsoft.com";

        private static void Main(string[] args)
        {
            // Create grabber.
            FrameGrabber<DetectedFace[]> grabber = new FrameGrabber<DetectedFace[]>();

            // Create Face API Client.
            FaceClient faceClient = new FaceClient(new ApiKeyServiceClientCredentials(ApiKey))
            {
                Endpoint = Endpoint
            };

            // Set up a listener for when we acquire a new frame.
            grabber.NewFrameProvided += (s, e) =>
            {
                Console.WriteLine($"New frame acquired at {e.Frame.Metadata.Timestamp}");
            };

            // Set up Face API call.
            grabber.AnalysisFunction = async frame =>
            {
                Console.WriteLine($"Submitting frame acquired at {frame.Metadata.Timestamp}");
                // Encode image and submit to Face API.
                return (await faceClient.Face.DetectWithStreamAsync(frame.Image.ToMemoryStream(".jpg"))).ToArray();
            };

            // Set up a listener for when we receive a new result from an API call.
            grabber.NewResultAvailable += (s, e) =>
            {
                if (e.TimedOut)
                    Console.WriteLine("API call timed out.");
                else if (e.Exception != null)
                    Console.WriteLine("API call threw an exception.");
                else
                    Console.WriteLine($"New result received for frame acquired at {e.Frame.Metadata.Timestamp}. {e.Analysis.Length} faces detected");
            };

            // Tell grabber when to call API.
            // See also TriggerAnalysisOnPredicate
            grabber.TriggerAnalysisOnInterval(TimeSpan.FromMilliseconds(3000));

            // Start running in the background.
            grabber.StartProcessingCameraAsync().Wait();

            // Wait for key press to stop.
            Console.WriteLine("Press any key to stop...");
            Console.ReadKey();

            // Stop, blocking until done.
            grabber.StopProcessingAsync().Wait();
        }
    }
}
```

2 つ目のサンプル アプリはさらに少し興味深いもので、ビデオ フレームに対してどの API を呼び出すかの選択が可能になっています。 左側では、アプリはライブ ビデオのプレビューを表示し、右側では、対応するフレームにオーバーレイされる最新の API 結果を表示します。

ほとんどのモードで、左のライブ ビデオと右に表示された分析との間に視覚的な遅延が生じます。 この遅延は、API 呼び出しにかかった時間です。 これに該当しないのは、"EmotionsWithClientFaceDetect" モードになっているときで、このモードでは、Cognitive Services にイメージを送信する前に、OpenCV を使用してクライアント コンピューターでローカルに顔検出が実行されます。 こうすることで、検出した顔をすぐに表示し、その後 API 呼び出しが戻ったときに後から感情を更新できます。 これは "ハイブリッド" アプローチの可能性を示しており、クライアント上で単純な処理をいくつか実行してから、必要に応じて Cognitive Services APIs を使用し、より高度な分析によって処理を強化することができます。

![タグが表示された画像を示す LiveCameraSample アプリのスクリーンショット](../../Video/Images/FramebyFrame.jpg)

### <a name="integrating-into-your-codebase"></a>コードベースへの統合

このサンプルを開始するには、次の手順に従います。

1. Vision API の API キーを[サブスクリプション](https://azure.microsoft.com/try/cognitive-services/)から取得します。 ビデオ フレーム分析の場合、適用可能な API は次のとおりです。
    - [Computer Vision API](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home)
    - [Face API](https://docs.microsoft.com/azure/cognitive-services/face/overview)
2. [Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/) GitHub リポジトリを複製します

3. Visual Studio 2015 以降でサンプルを開き、サンプル アプリケーションをビルドして実行します。
    - BasicConsoleSample の場合、Face API キーは、[BasicConsoleSample/Program.cs](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/blob/master/Windows/BasicConsoleSample/Program.cs) 内に直接ハードコードされています。
    - LiveCameraSample の場合、アプリの設定ウィンドウにキーを入力する必要があります。 これらは、セッションを移動してもユーザー データとして残されます。

統合する準備ができたら、**自分のプロジェクトから単純に VideoFrameAnalyzer ライブラリを参照します。**

VideoFrameAnalyzer のイメージ、音声、ビデオ、またはテキストの解釈機能は、Azure Cognitive Services を使用しています。 Microsoft は、開発者が (このアプリから) アップロードするイメージ、音声、ビデオ、およびその他のデータを受け取り、サービス向上の目的でそれらを使用する場合があります。 アプリによって Azure Cognitive Services に送信されるデータの所有者の保護にご協力をお願いします。

## <a name="summary"></a>まとめ

このガイドでは、Face API と Computer Vision API を使用してライブ ビデオ ストリームでほぼリアルタイムの分析を実行する方法と、サンプル コードを使用して作業を開始する方法について説明しました。 [Azure Cognitive Services のサインアップ ページ](https://azure.microsoft.com/try/cognitive-services/)で取得した無料の API キーを使用して、アプリのビルドを開始できます。

[GitHub リポジトリ](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/)では、お気軽にフィードバックや提案を提供してください。API に関するより幅広いフィードバックについては、[UserVoice サイト](https://cognitive.uservoice.com/)にお寄せください。

