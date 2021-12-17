---
title: 例:リアルタイムのビデオ分析 - Face
titleSuffix: Azure Cognitive Services
description: ライブ ビデオ ストリームから取得したフレームに対し、Face サービスを使用して、ほぼリアルタイムに分析を実行します。
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 03/01/2018
ms.author: sbowles
ms.custom: devx-track-csharp
ms.openlocfilehash: 4624e60ba6ffbcfc11ad641c098bb40b1ae6afab
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131071403"
---
# <a name="example-how-to-analyze-videos-in-real-time"></a>例:リアルタイムでビデオを分析する方法

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

分析が軽量のクライアント側アルゴリズムで構成されている場合は、このアプローチが適しています。 ただし、分析がクラウドで行われるときは、それに伴う待ち時間のために、API 呼び出しに数秒かかることがあります。 その間はイメージのキャプチャは行われず、スレッドは基本的に何もしません。 最大フレーム レートは、API 呼び出しの待ち時間によって制限されます。

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

このコードはそれぞれの分析を個別のタスクで起動しますが、タスクはバックグラウンドで実行でき、新しいフレームを取得し続けられます。 この方法であれば、API 呼び出しから制御が戻るまで待機する間、メイン スレッドがブロックされることはなくなりますが、単純なバージョンで得られているいくつかの保証は失われます。 複数の API 呼び出しが並列に行われ、結果が間違った順序で返されることがあります。 また、これにより、複数のスレッドが同時に ConsumeResult() 関数に入る可能性も生じますが、関数がスレッドセーフでない場合は、それが危険である可能性があります。 最後に、この単純なコードは作成されるタスクを追跡しないので、例外は自動的に表示されなくなります。 したがって最後のステップとして、分析タスクを追跡し、例外を発生させ、実行時間の長いタスクを中止し、正しい順序で結果が消費されるようにする "consumer" スレッドを追加します。

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

### <a name="getting-started"></a>作業の開始

できるだけ迅速にアプリを稼働させるために、前述した柔軟なシステム実装を使用します。 コードにアクセスするには、[https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis) に移動します。

ライブラリには、FrameGrabber クラスが含まれています。これは Web カメラからのビデオ フレームを処理するために、前述したプロデューサー/コンシューマー システムを実装しています。 ユーザーは、正確なフォームの API 呼び出しを指定でき、クラスは、新しいフレームが獲得されたとき、または新しい分析結果が利用可能になったときに呼び出しコードに知らせるためにイベントを使用します。

いくつかの可能性を示すため、ライブラリを使用する 2 つのサンプル アプリを取り上げます。 1 つは、単純なコンソール アプリであり、その簡略化バージョンを下に再現しています。 これは既定の Web カメラからのフレームを取得し、それらを顔検出のために Face サービスに送信します。

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/analyze.cs":::

2 つ目のサンプル アプリはさらに少し興味深いもので、ビデオ フレームに対してどの API を呼び出すかの選択が可能になっています。 左側では、アプリはライブ ビデオのプレビューを表示し、右側では、対応するフレームにオーバーレイされる最新の API 結果を表示します。

ほとんどのモードで、左のライブ ビデオと右に表示された分析との間に視覚的な遅延が生じます。 この遅延は、API 呼び出しにかかった時間です。 これに該当しないのは、"EmotionsWithClientFaceDetect" モードになっているときで、このモードでは、Cognitive Services にイメージを送信する前に、OpenCV を使用してクライアント コンピューターでローカルに顔検出が実行されます。 こうすることで、検出した顔をすぐに表示し、その後 API 呼び出しから制御が戻ったときに感情を更新できます。 これは "ハイブリッド" アプローチの例であり、クライアントで単純な処理をいくつか実行するものです。さらに、Cognitive Services APIs を使えば、必要に応じてより高度な分析によってこの処理を強化することができます。

![HowToAnalyzeVideo](../../Video/Images/FramebyFrame.jpg)

### <a name="integrating-into-your-codebase"></a>コードベースへの統合

このサンプルを開始するには、次の手順に従います。

1. [Azure アカウント](https://azure.microsoft.com/free/cognitive-services/)を作成します。 既にある場合は、次の手順に進むことができます。
2. Azure portal で Computer Vision と Face のリソースを作成し、キーとエンドポイントを取得します。 設定中に必ず Free レベル (F0) を選択してください。
   - [Computer Vision](https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision)
   - [Face](https://portal.azure.com/#create/Microsoft.CognitiveServicesFace): リソースがデプロイされたら、 **[リソースに移動]** をクリックし、各リソースのキーとエンドポイントを収集します。 
3. [Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/) GitHub リポジトリを複製します。
4. Visual Studio でサンプルを開き、サンプル アプリケーションをビルドして実行します。
    - BasicConsoleSample の場合、Face キーは、[BasicConsoleSample/Program.cs](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/blob/master/Windows/BasicConsoleSample/Program.cs) 内に直接ハードコーディングされています。
    - LiveCameraSample の場合、アプリの設定ウィンドウにキーを入力する必要があります。 これらは、セッションを移動してもユーザー データとして残されます。
        

統合する準備ができたら、**自分のプロジェクトから VideoFrameAnalyzer ライブラリを参照します。** 

## <a name="summary"></a>まとめ

このガイドでは、Face、Computer Vision、Emotion API を使用してライブ ビデオ ストリームでほぼリアルタイムの分析を実行する方法と、サンプル コードを使用して作業を開始する方法について説明しました。

[GitHub リポジトリ](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/)で、気軽にフィードバックや提案を提供してください。API に関するより幅広いフィードバックについては、[UserVoice](https://feedback.azure.com/d365community/forum/09041fae-0b25-ec11-b6e6-000d3a4f0858) サイトを利用してください。

## <a name="related-topics"></a>関連トピック
- [検出 API を呼び出す](HowtoDetectFacesinImage.md)
