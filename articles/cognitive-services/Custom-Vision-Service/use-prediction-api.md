---
title: 予測エンドポイントを使用してプログラムで分類器を指定してテストする - Custom Vision
titleSuffix: Azure Cognitive Services
description: API を使用して、Custom Vision Service の分類器によるイメージのテストをプログラムから行う方法を説明します。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 10/27/2021
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: 56c2e5849381becfcd561828300cf4780a1e168b
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132708819"
---
# <a name="call-the-prediction-api"></a>Prediction API の呼び出し

モデルをトレーニングした後、イメージを Prediction API エンドポイントに送信することで、プログラムからイメージをテストできます。 このガイドでは、Prediction API を呼び出してイメージにスコアを付ける方法について説明します。 この API の動作をニーズに合わせて構成できるさまざまな方法を学習します。


> [!NOTE]
> このドキュメントでは、C# 用 .NET クライアント ライブラリを使用して、Prediction API にイメージを送信する方法について説明します。 詳細および例については、[Prediction API リファレンス](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c15)に関するページを参照してください。

## <a name="setup"></a>セットアップ

### <a name="publish-your-trained-iteration"></a>トレーニング済みイテレーションを発行する

[Custom Vision の Web ページ](https://customvision.ai)から自分のプロジェクトを選択し、__[Performance]__ \(パフォーマンス) タブを選択します。

Prediction API にイメージを送信するには、まず予測のイテレーションを発行する必要があり、これは __[発行]__ を選択して、発行されたイテレーションの名前を指定して実行できます。 これにより、モデルが、Custom Vision Azure リソースの Prediction API にアクセスできるようになります。

![[Publish]\(発行\) ボタンが赤い四角形で囲まれた、パフォーマンス タブが表示されます。](./media/use-prediction-api/unpublished-iteration.png)

モデルが正常に発行されると、左側のサイドバー内のイテレーションの横に [発行済み] ラベルが表示されます。また、イテレーションの名前がイテレーションの説明内に表示されます。

![発行済みラベルと発行済みイテレーションの名前が赤い四角形で囲まれた、パフォーマンス タブが表示されます。](./media/use-prediction-api/published-iteration.png)

### <a name="get-the-url-and-prediction-key"></a>URL と予測キーを取得する

モデルが発行されたら、必要な情報を取得するために __[Prediction URL]__ を選択します。 これにより、ダイアログが開き、__Prediction URL__ や __Prediction-Key__ などの Prediction API を使用するための情報が表示されます。

![[Prediction URL] ボタンが赤色の四角形で囲まれた、パフォーマンス タブが表示されます。](./media/use-prediction-api/published-iteration-prediction-url.png)

![イメージ ファイルと Prediction-Key 値を使用するための Prediction URL が赤い四角形で囲まれた、パフォーマンス タブが表示されます。](./media/use-prediction-api/prediction-api-info.png)

## <a name="submit-data-to-the-service"></a>サービスにデータを送信する

このガイドでは、Custom Vision 予測キーとエンドポイント URL を使用して、`predictionClient` という名前の **[CustomVisionPredictionClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.prediction.customvisionpredictionclient?view=azure-dotnet-preview)** オブジェクトを既に作成していることを前提としています。 この機能を設定する方法については、[クイックスタート](quickstarts/image-classification.md)のいずれかに従ってください。

このガイドでは、ローカル イメージを使用するため、トレーニング済みのモデルに送信するイメージをダウンロードします。 次のコードは、ローカル パスを指定し、そのパスにあるファイルのバイトストリームを取得するようにユーザーに要求します。

```csharp
Console.Write("Enter image file path: ");
string imageFilePath = Console.ReadLine();
byte[] byteData = GetImageAsByteArray(imageFilePath);
```

以下のヘルパー メソッドを含めます。

```csharp
private static byte[] GetImageAsByteArray(string imageFilePath)
{
    FileStream fileStream = new FileStream(imageFilePath, FileMode.Open, FileAccess.Read);
    BinaryReader binaryReader = new BinaryReader(fileStream);
    return binaryReader.ReadBytes((int)fileStream.Length);
}
```

**[ClassifyImageAsync](/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.prediction.customvisionpredictionclientextensions.classifyimageasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Vision_CustomVision_Prediction_CustomVisionPredictionClientExtensions_ClassifyImageAsync_Microsoft_Azure_CognitiveServices_Vision_CustomVision_Prediction_ICustomVisionPredictionClient_System_Guid_System_String_System_IO_Stream_System_String_System_Threading_CancellationToken_)** メソッドは、プロジェクト ID とローカルに格納されているイメージを受け取り、指定されたモデルに対してイメージをスコア付けします。

```csharp
// Make a prediction against the new project
Console.WriteLine("Making a prediction:");
var result = predictionApi.ClassifyImageAsync(project.Id, publishedModelName, byteData);
```

## <a name="determine-how-to-process-the-data"></a>データの処理方法を決定する

必要に応じて、別の方法を選択して、サービスがスコアリング操作を実行する方法を構成できます ( **[CustomVisionPredictionClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.prediction.customvisionpredictionclient?view=azure-dotnet)** クラスのメソッドを参照してください)。 

わかりやすくするために、上記の非同期バージョンのメソッドを使用できますが、プログラムがかなりの時間ロックされる可能性があります。

**-WithNoStore** メソッドでは、予測の完了後にサービスによって予測イメージが保持されないようにする必要があります。 通常、このようなイメージはサービスによって保持されるので、モデルの将来の反復のためにトレーニング データとして追加することもできます。

**-WithHttpMessages** メソッドは、API 呼び出しの未加工の HTTP 応答を返します。

## <a name="get-results-from-the-service"></a>サービスから結果を取得する

サービスでは、 **[ImagePrediction](/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.prediction.models.imageprediction?view=azure-dotnet)** オブジェクトの形式で結果が返されます。 **Predictions** プロパティには、 **[PredictionModel](/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.prediction.models.predictionmodel?view=azure-dotnet)** オブジェクトの一覧が含まれています。これらはそれぞれ、単一のオブジェクト予測を表します。 これには、ラベルの名前と、イメージ内でオブジェクトが検出された境界ボックスの座標が含まれます。 その後、アプリはこのデータを解析して、たとえば、ラベルが付けられたオブジェクト フィールドを含むイメージをスクリーンに表示できます。 

## <a name="next-steps"></a>次のステップ

このガイドでは、C# SDK を使用して、カスタム画像分類器/検出器にイメージを送信し、応答をプログラムで受信する方法について説明しました。 次は、C# を使用してエンドツーエンドのシナリオを完了したり、さまざまな言語の SDK を使用して作業を開始したりする方法について説明します。

* [クイック スタート: Custom Vision SDK](quickstarts/image-classification.md)