---
title: Emotion API for Video を呼び出す | Microsoft Docs
description: Cognitive Services で Emotion API for Video を呼び出す方法を説明します。
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: article
ms.date: 02/06/2017
ms.author: anroth
ms.openlocfilehash: 0875013b2061a84e3e23ae90c1106382672fdca6
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35374157"
---
# <a name="how-to-call-emotion-api-for-video"></a>Emotion API for Video を呼び出す方法

> [!IMPORTANT]
> Video API のプレビューは 2017 年 10 月 30 日をもって終了します。 新しい [Video Indexer API のプレビュー](https://azure.microsoft.com/services/cognitive-services/video-indexer/)をお試しください。ビデオから分析情報を手軽に抽出でき、ビデオ内で話される言葉や表情、性格、感情を検知することで、検索結果などのコンテンツの検索エクスペリエンスを強化できます。 [詳細情報](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview)。

このガイドでは、Emotion API for Video を呼び出す方法を示します。 サンプルは、Emotion API for Video クライアント ライブラリを使用して C# で記述されています。

### <a name="Prep">準備</a> 
Emotion API for Video を使用するには、人が映っているビデオが必要になります。望ましいのは、人がカメラに向いているビデオです。

### <a name="Step1">手順 1: API 呼び出しを承認する</a> 
Emotion API for Video の呼び出しごとに、サブスクリプション キーが必要です。 このキーは、クエリ文字列パラメーターによって渡すか、要求ヘッダー内で指定する必要があります。 クエリ文字列によってサブスクリプション キーを渡す場合は、例として、下記の Emotion API for Video の要求 URL を参照してください。

```
https://westus.api.cognitive.microsoft.com/emotion/v1.0/recognizeInVideo&subscription-key=<Your subscription key>
```

その代わりに、HTTP 要求ヘッダー内でサブスクリプション キーを指定することもできます。

```
ocp-apim-subscription-key: <Your subscription key>
```

クライアント ライブラリを使用するときには、VideoServiceClient クラスのコンストラクターを介してサブスクリプション キーが渡されます。 例: 

```
var emotionServiceClient = new emotionServiceClient("Your subscription key");
```
サブスクリプション キーを取得するには、サブスクリプションについてのページ を参照してください (https://azure.microsoft.com/try/cognitive-services/)。 

### <a name="Step2">手順 2: サービスにビデオをアップロードして状態を確認する</a>
Emotion API for Video のいずれかの呼び出しを実行する場合の最も基本的な方法は、ビデオを直接アップロードすることです。 これを行うには、コンテンツの種類を application/octet-stream と指定し、同時にデータの読み取りはビデオ ファイルからだと指定して、"POST" 要求を送信します。 ビデオの最大サイズは 100 MB です。

クライアント ライブラリの使用時に、アップロードによる安定化は、ストリーム オブジェクトに入れて渡すことで行います。 次の例を見てください。

```
Operation videoOperation;
using (var fs = new FileStream(@"C:\Videos\Sample.mp4", FileMode.Open))
{
      videoOperation = await videoServiceClient.CreateOperationAsync(fs, OperationType.recognizeInVideo);
}
```

VideoServiceClient の CreateOperationAsync メソッドは非同期であることに注意してください。 await 句を使用するためには、呼び出し元のメソッドも非同期としてマークする必要があります。
ビデオがすでに Web 上にあり、パブリック URL を持っている場合、ビデオ用の Emotion API には、URL を指定することによってアクセスできます。 この例では、要求本文は、URL を含む JSON 文字列になります。

クライアント ライブラリの使用時に、URL による安定化は、CreateOperationAsync メソッドの別のオーバー ロードを使用することで、容易に実行できます。


```
var videoUrl = "http://www.example.com/sample.mp4";
Operation videoOperation = await videoServiceClient.CreateOperationAsync(videoUrl, OperationType. recognizeInVideo);

```

このアップロード方法は、すべての Emotion API for Video 呼び出しで共通です。 

ビデオをアップロードしたら、次に実行する操作は、ビデオの状態を確認することです。 ビデオ ファイルは通常、他のファイルよりもサイズが大きく多様なため、ユーザーはこの手順で長い処理時間を予想できます。 時間は、ファイルのサイズと長さによって異なります。

クライアント ライブラリを使用して、GetOperationResultAsync メソッド使用時の操作の状態と結果を取得できます。


```
var operationResult = await videoServiceClient.GetOperationResultAsync(videoOperation);

```
一般に、クライアント側が、"Succeeded" または "Failed" と表示されるまで操作の状態を定期的に取得する必要があります。

```
OperationResult operationResult;
while (true)
{
      operationResult = await videoServiceClient.GetOperationResultAsync(videoOperation);
      if (operationResult.Status == OperationStatus.Succeeded || operationResult.Status == OperationStatus.Failed)
      {
           break;
      }

      Task.Delay(30000).Wait();
}

```

VideoOperationResult の状態が "Succeeded" と表示されている場合、結果は、VideoOperationResult を VideoOperationInfoResult<VideoAggregateRecognitionResult> にキャストし、ProcessingResult フィールドにアクセスすることで取得できます。

```
var emotionRecognitionJsonString = ((VideoOperationInfoResult<VideoAggregateRecognitionResult>)operationResult).ProcessingResult;
```

### <a name="Step3">手順 3: 感情認識を取得して解釈し、JSON 出力を追跡する</a>

出力結果には顔のメタデータが含まれていて、JSON 形式の所定のファイル内にあります。

手順 2. で説明したように、JSON 出力は、状態が "Succeeded" として表示されているときに、OperationResult の ProcessingResult フィールドで取得でます。

顔の検出と追跡の JSON には、次の属性が含まれます。

Attribute | 説明
-------------|-------------
version | Emotion API for Video の JSON のバージョンを指します。
タイムスケール | ビデオの 1 秒あたりの "ティック数" です。
offset  |タイムスタンプの時間オフセットです。 バージョン 1.0 の Emotion API for Video では、これは常に 0 になります。 この値は、今後サポートされるシナリオで変更される可能性があります。
framerate | ビデオの 1 秒あたりのフレーム数です。
fragments   | メタデータは、フラグメントと呼ばれる異なる小さな部分に切り刻まれます。 各フラグメントには、開始、継続時間、間隔数、およびイベントが含まれます。
start   | 最初のイベントの開始時間 (ティック数単位)。
duration |  フラグメントの長さです (ティック数単位)。
interval |  フラグメント内の各イベントの長さです (ティック数単位)。
events  | イベントの配列です。 外側の配列は、1 つの時間間隔を表します。 内側の配列は、その時点で発生した 0 個以上のイベントで構成されます。
windowFaceDistribution |    イベント中に特定の感情を持っている顔の割合です。
windowMeanScores |  イメージに含まれる顔の各感情についての平均スコアです。

JSON をこのような書式設定にするのは、将来のシナリオに向けて API を準備するためです。将来は、メタデータを迅速に取得し、大きなサイズの結果のストリームを管理することが重要になります。 この書式設定では、断片化 (メタデータを時間に基づく部分に分割できるようにします。これで、必要なものだけをダウンロードできます) と、セグメント化 (大きくなりすぎた場合にイベントを分割できるようにします) の両方の手法を使用しています。 簡単な計算でデータを変換できます。 たとえば、イベントが 6300 (ティック) に始まり、タイムスケールが 2997 (ティック/秒)、フレームレートが 29.97 (フレーム/秒) である場合、次のようになります。

*   開始/タイムスケール = 2.1 秒
*   秒数 x (フレームレート/タイムスケール) = 63 フレーム

顔の検出と追跡のために JSON からフレーム単位の形式を抽出する簡単な例を次に示します。

```
var emotionRecognitionTrackingResultJsonString = operationResult.ProcessingResult;
var emotionRecognitionTracking = JsonConvert.DeserializeObject<EmotionRecognitionResult>(emotionRecognitionTrackingResultJsonString, settings);
```
感情は時間の経過とともに平準化されるため、結果を元のビデオ上にオーバーレイする視覚化をビルドする場合は、提供されるタイムスタンプから 250 ミリ秒を減算します。

### <a name="Summary">まとめ</a>
このガイドでは、Emotion API for Video の機能について説明しました。ビデオをアップロードする方法、その状態を確認する方法、感情認識のメタデータを取得する方法などです。

API の詳細情報については、API リファレンス ガイドとして、[Emotion API for Video のリファレンス](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/56f8d40e1984551ec0a0984e)を参照してください。
