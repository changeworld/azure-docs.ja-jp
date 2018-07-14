---
title: Face API で画像内の顔を検出する | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: Cognitive Services の Face API を使用して、画像内の顔を検出します。
services: cognitive-services
author: SteveMSFT
manager: corncar
ms.service: cognitive-services
ms.component: face-api
ms.topic: article
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: 57cd0915450428399fd680638aa4fae2cdbe17c9
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35373200"
---
# <a name="how-to-detect-faces-in-image"></a>画像内の顔を検出する方法

このガイドでは、性別、年齢、または姿勢などの顔の属性を抽出して、画像から顔を検出する方法を説明します。 サンプルは Face API クライアント ライブラリを使用して C# で記述されています。 

## <a name="concepts"></a> 概念

このガイドの次の概念についてよくわからない場合は、いつでも[用語集](../Glossary.md)で定義を検索してください。 

- 顔検出
- 顔のランドマーク
- 頭部姿勢
- 顔の属性

## <a name="preparation"></a> 準備

このサンプルでは、次の機能について説明します。 

- 画像から顔を検出し、四角形のフレームを使用してマークする
- 瞳孔、鼻、または口の位置を分析し、それらを画像にマークする
- 顔の頭部姿勢、性別、年齢を分析する

これらの機能を実行するには、少なくとも 1 つの明瞭な顔がある画像を準備する必要があります。 

## <a name="step1"></a> 手順 1: API 呼び出しを承認する

Face API を呼び出すたびに、サブスクリプション キーが必要です。 このキーは、クエリ文字列パラメーターによって渡すか、要求ヘッダー内で指定する必要があります。 クエリ文字列を介してサブスクリプション キーを渡すには、例として[画面 - 検出](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)の要求 URL を参照してください。

```
https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes]
&subscription-key=<Subscription Key>
```

代替手段として、HTTP 要求のヘッダーでサブスクリプション キーを指定することもできます (**ocp-apim-subscription-key: &lt;サブスクリプション キー&gt;**)。クライアント ライブラリを使用する場合は、サブスクリプション キーは、FaceServiceClient クラスのコンストラクターを介して渡されます。 例: 
```CSharp
faceServiceClient = new FaceServiceClient("<Subscription Key>");
```

## <a name="step2"></a> 手順 2: サービスに画像をアップロードし、顔検出を実行する

顔検出を実行する最も基本的な方法は、画像を直接アップロードすることです。 この処理を行うには、コンテンツの種類を application/octet-stream と指定し、JPEG 画像から読み取られたデータと指定して、"POST" 要求を送信します。 画像の最大サイズは 4 MB です。

クライアント ライブラリを使用してアップロードによる顔検出を実行するには、Stream オブジェクトで渡します。 次の例を見てください。
```CSharp
using (Stream s = File.OpenRead(@"D:\MyPictures\image1.jpg"))
{
    var faces = await faceServiceClient.DetectAsync(s, true, true);
 
    foreach (var face in faces)
    {
        var rect = face.FaceRectangle;
        var landmarks = face.FaceLandmarks;
    }
}
```

FaceServiceClient の DetectAsync メソッドは非同期である点に注意してください。 await 句を使用するためには、呼び出し元のメソッドも非同期としてマークする必要があります。
画像が既に Web 上にあり、URL がある場合は、URL を指定して顔検出を実行することもできます。 この例では、要求本文は URL を含む JSON 文字列になります。
クライアント ライブラリを使用した URL による顔検出は、DetectAsync メソッドの別のオーバーロードを使用して簡単に実行できます。
```CSharp
string imageUrl = "http://news.microsoft.com/ceo/assets/photos/06_web.jpg";
var faces = await faceServiceClient.DetectAsync(imageUrl, true, true);
 
foreach (var face in faces)
{
    var rect = face.FaceRectangle;
    var landmarks = face.FaceLandmarks;
}
``` 

検出された顔と共に返される FaceRectangle プロパティは、基本的には顔上のピクセル単位の位置です。 通常、この四角形には目、眉、鼻、口が含まれています。頭の上部、耳、あごは含まれていません。 頭部全体または中距離から撮影されたポートレート (写真 ID の種類の画像) をトリミングする場合、アプリケーションによっては顔の面積が小さすぎるために、四角形の顔フレームの領域を拡大したいことがあります。 顔の位置をより正確に特定するには、次のセクションで説明する顔のランドマーク (顔の特徴や顔の向きのメカニズムの特定) を使用すると便利です。

## <a name="step3"></a> 手順 3: 顔のランドマークを理解して使用する

顔のランドマークとは、顔面上の一連の細かい点です。通常は、瞳孔、眼窩、鼻などの顔の構成要素の点です。 顔のランドマークは、顔検出中に分析できる省略可能な属性です。 [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) を呼び出すときに、returnFaceLandmarks クエリ パラメーターにブール値として true を渡すことができます。また、検索結果に顔のランドマークを含めるために、FaceServiceClient クラスの DetectAsync メソッドに省略可能なパラメーター returnFaceLandmarks を使用することもできます。

既定では、27個 の定義済みランドマーク ポイントがあります。 次の図は、27 個のポイントすべての定義方法を示しています。

![HowToDetectFace](../Images/landmarks.1.jpg)

返されるポイントは、顔の四角形のフレームと同様にピクセル単位です。 そのため、画像内の特定の要点を簡単にマークできます。 次のコードは、鼻と瞳孔の位置を取得する方法を示しています。
```CSharp
var faces = await faceServiceClient.DetectAsync(imageUrl, returnFaceLandmarks:true);
 
foreach (var face in faces)
{
    var rect = face.FaceRectangle;
    var landmarks = face.FaceLandmarks;
 
    double noseX = landmarks.NoseTip.X;
    double noseY = landmarks.NoseTip.Y;
 
    double leftPupilX = landmarks.PupilLeft.X;
    double leftPupilY = landmarks.PupilLeft.Y;
 
    double rightPupilX = landmarks.PupilRight.X;
    double rightPupilY = landmarks.PupilRight.Y;
}
``` 

顔のランドマークを使用すると、顔の特徴を画像にマークできるだけでなく、顔の方向を正確に計算することもできます。 たとえば、顔の方向は、口の中心から目の中心までのベクトルとして定義することができます。 以下のコードは、これを詳しく示しています。

```CSharp
var landmarks = face.FaceLandmarks;
 
var upperLipBottom = landmarks.UpperLipBottom;
var underLipTop = landmarks.UnderLipTop;
 
var centerOfMouth = new Point(
    (upperLipBottom.X + underLipTop.X) / 2,
    (upperLipBottom.Y + underLipTop.Y) / 2);
 
var eyeLeftInner = landmarks.EyeLeftInner;
var eyeRightInner = landmarks.EyeRightInner;
 
var centerOfTwoEyes = new Point(
    (eyeLeftInner.X + eyeRightInner.X) / 2,
    (eyeLeftInner.Y + eyeRightInner.Y) / 2);
 
Vector faceDirection = new Vector(
    centerOfTwoEyes.X - centerOfMouth.X,
    centerOfTwoEyes.Y - centerOfMouth.Y);
``` 

顔が向いている方向がわかると、顔に合わせて四角形の顔フレームを回転させることができます。 顔のランドマークを使用すると、より詳細で利便性が高くなります。

## <a name="step4"></a> 手順 4: その他の顔属性を使用する

Face - Detect API では、顔のランドマークだけでなく、顔のその他の属性を分析することもできます。 以下のような属性があります。

- 年齢
- 性別
- 笑顔の強さ
- 顔ひげ
- 3D 頭部姿勢

これらの属性は統計的アルゴリズムを使用して予測されるので、常に 100% 正確とは言えません。 ただし、これらの属性を使用して顔を分類する場合には役に立ちます。 各属性の詳細については、「[Glossary](../Glossary.md)」(用語集) を参照してください。

顔検出時の顔属性の抽出の簡単な例を次に示します。
```CSharp
var requiredFaceAttributes = new FaceAttributeType[] {
                FaceAttributeType.Age,
                FaceAttributeType.Gender,
                FaceAttributeType.Smile,
                FaceAttributeType.FacialHair,
                FaceAttributeType.HeadPose,
                FaceAttributeType.Glasses
            };
var faces = await faceServiceClient.DetectAsync(imageUrl,
    returnFaceLandmarks: true,
    returnFaceAttributes: requiredFaceAttributes);

foreach (var face in faces)
{
    var id = face.FaceId;
    var attributes = face.FaceAttributes;
    var age = attributes.Age;
    var gender = attributes.Gender;
    var smile = attributes.Smile;
    var facialHair = attributes.FacialHair;
    var headPose = attributes.HeadPose;
    var glasses = attributes.Glasses;
}
``` 
## <a name="summary"></a> まとめ

このガイドでは、[Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) API の機能、Web 上でローカルのアップロードされた画像や画像の URL の顔を検出する方法、四角形の顔フレームを返して顔を検出する方法、顔のランドマーク、3D 頭部姿勢などの属性を分析する方法について学びました。

API の詳細については、[Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) の API リファレンス ガイドを参照してください。

## <a name="related"></a> 関連トピック

[画像内の顔を識別する方法](HowtoIdentifyFacesinImage.md)
