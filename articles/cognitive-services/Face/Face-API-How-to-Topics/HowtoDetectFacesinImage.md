---
title: 検出 API を呼び出す - Face
titleSuffix: Azure Cognitive Services
description: このガイドでは、顔検出を使用して、特定の画像から年齢、感情、頭部姿勢などの属性を抽出する方法を示します。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 08/04/2021
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: 1fe1cddcbd194ec4b8ca25edfc4907631cb18df1
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121744824"
---
# <a name="call-the-detect-api"></a>検出 API を呼び出す

このガイドでは、顔検出 API を使用して、特定の画像から年齢、感情、頭部姿勢などの属性を抽出する方法を示します。 この API の動作をニーズに合わせて構成するさまざまな方法を学習します。

このガイドのコード スニペットは Azure Cognitive Services Face クライアント ライブラリを使用して C# で記述されています。 同じ機能を [REST API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) でも使用できます。


## <a name="setup"></a>セットアップ

このガイドでは、Face サブスクリプション キーとエンドポイント URL を使用して、`faceClient` という名前の [FaceClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient) オブジェクトを既に作成していることを前提としています。 この機能を設定する方法については、クイックスタートのいずれかに従ってください。

## <a name="submit-data-to-the-service"></a>サービスにデータを送信する

顔を検出し、画像内での位置を取得するには、[DetectWithUrlAsync](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithurlasync) または [DetectWithStreamAsync](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithstreamasync) メソッドを呼び出します。 **DetectWithUrlAsync** は URL 文字列を入力として受け取り、**DetectWithStreamAsync** は画像の未加工のバイト ストリームを入力として受け取ります。

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="basic1":::

返された [DetectedFace](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface) オブジェクトを照会して、そのオブジェクトの一意の ID と、顔のピクセル座標を示す四角形を取得できます。 これにより、どの顔 ID が元の画像のどの顔にマップされているかを知ることができます。

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="basic2":::

顔の位置と寸法を解析する方法については、「[FaceRectangle](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.facerectangle)」を参照してください。 通常、この四角形には、目、眉、鼻、口が含まれます。 頭の上部、耳、あごは必ずしも含まれません。 顔の四角形を使用して、頭部全体をトリミングしたり、上半身のポートレートを取得したりするには、四角形を各方向に拡張する必要があります。

## <a name="determine-how-to-process-the-data"></a>データの処理方法を定義する

このガイドでは、Detect 呼び出しの仕様、たとえば、渡すことができる引数や返されたデータで実行できることを中心に説明を進めます。 必要な機能だけを照会することをお勧めします。 操作が増えると、完了までの時間が長くなります。

### <a name="get-face-landmarks"></a>顔のランドマークの取得

[顔のランドマーク](../concepts/face-detection.md#face-landmarks)とは、瞳孔、鼻の先など、顔の中の見つけやすいポイントのことです。 顔のランドマーク データを取得するには、_detectionModel_ パラメーターを `DetectionModel.Detection01` に、_returnFaceLandmarks_ パラメーターを `true` に設定します。

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="landmarks1":::

### <a name="get-face-attributes"></a>顔の属性の取得

顔検出 API では、顔の四角形とランドマークに加えて、顔に関するいくつかの概念属性を分析できます。 完全な一覧については、[顔の属性](../concepts/face-detection.md#attributes)の概念に関するセクションを参照してください。

顔の属性を分析するには、_detectionModel_ パラメーターを `DetectionModel.Detection01` に、_returnFaceAttributes_ パラメーターを [FaceAttributeType Enum](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype) 値のリストに設定します。

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="attributes1":::


## <a name="get-results-from-the-service"></a>サービスから結果を取得する

### <a name="face-landmark-results"></a>顔のランドマークの結果

次のコードは、鼻と瞳孔の位置を取得する方法を示しています。

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="landmarks2":::

顔のランドマーク データを使用して、顔の方向を正確に計算することもできます。 たとえば、顔の回転は、口の中心から目の中心までのベクターとして定義できます。 この次のコードではこのベクターが計算されます。

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="direction":::

顔の方向がわかったら、四角形の顔フレームを回転させて、より適切な向きに修正できます。 画像内の顔をトリミングするには、顔が常に縦に表示されるように、画像をプログラムで回転させます。


### <a name="face-attribute-results"></a>顔属性の結果

次のコードは、元の呼び出しで要求した顔属性データを取得する方法を示しています。

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="attributes2":::

各属性の詳細については、「[Face detection and attributes (顔の検出と属性)](../concepts/face-detection.md)」概念ガイドを参照してください。

## <a name="next-steps"></a>次のステップ

このガイドでは、顔検出と分析のさまざまな機能を使用する方法について説明しました。 次に、これらの機能をアプリに統合して、ユーザーの顔データを追加します。

- [チュートリアル: Face サービスにユーザーを追加する](../enrollment-overview.md)

## <a name="related-articles"></a>関連記事

- [リファレンス ドキュメント (REST)](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
- [リファレンス ドキュメント (.NET SDK)](/dotnet/api/overview/azure/cognitiveservices/face-readme)
