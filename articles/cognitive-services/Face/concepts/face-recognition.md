---
title: 顔認識の概念
titleSuffix: Azure Cognitive Services
description: この記事では、顔認証の概念、関連する操作、およびその基礎となるデータ構造について説明します。
services: cognitive-services
author: PatrickFarley
manager: nitime
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: pafarley
ms.openlocfilehash: 6f22e48c869ebc2cf4101127f3d87cc7836da35d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121747546"
---
# <a name="face-recognition-concepts"></a>顔認識の概念

この記事では、顔認証の概念、関連する操作、およびその基礎となるデータ構造について説明します。 顔認識とは、大まかに言えば、顔を使って個人を検証したり、識別したりする方法のことを指します。 検証とは、2 つの顔を受け取り、それらが同じ顔であるかどうかを返す 1 対 1 の照合であり、識別とは、1 つの顔を入力として受け取り、一致する候補のセットを返す 1 対多の照合です。 顔認識は、企業やアプリで (遠隔地にいる) ユーザーが本人であることを確認するために使用する ID 検証シナリオを実装する上で重要です。

## <a name="related-data-structures"></a>関連のデータ構造

認識操作では、主に次のデータ構造を使用します。 これらのオブジェクトはクラウドに格納され、その ID 文字列によって表すことができます。 ID 文字列は、サブスクリプション内で常に一意です。 名前フィールドは重複している可能性があります。

|名前|説明|
|:--|:--|
|DetectedFace| この 1 つの顔の表現は、[顔検出](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)操作によって取得されます。 その ID は作成後 24 時間で期限が切れます。|
|PersistedFace| DetectedFace オブジェクトがグループ (FaceList または Person) に追加されると、これらは PersistedFace オブジェクトになります。 これらはいつでも[取得](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c)することができ、期限が切れることはありません。|
|[FaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b) または [LargeFaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc)| このデータ構造は、PersistedFace オブジェクトの類別された一覧です。 FaceList には一意の ID、名前の文字列が含まれ、任意でユーザー データの文字列が含まれます。|
|[Person](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c)| このデータ構造は、同じ人に属する PersistedFace オブジェクトの一覧です。 これには一意の ID、名前の文字列が含まれ、任意でユーザー データの文字列が含まれます。|
|[PersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) または [LargePersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d)| このデータ構造は、Person オブジェクトの類別された一覧です。 これには一意の ID、名前の文字列が含まれ、任意でユーザー データの文字列が含まれます。 PersonGroup は、認識操作で使用するには、その前に[トレーニングする](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249)必要があります。|

## <a name="recognition-operations"></a>認識操作

このセクションでは、基礎となる操作で前述のデータ構造を使って、どのように顔の識別および検証を行うのかを説明します。

### <a name="persongroup-creation-and-training"></a>PersonGroup の作成とトレーニング

照合の対象となる一連の人物の保存先となる [PersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) または [LargePersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d) を作成する必要があります。 PersonGroup には [Person](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c) オブジェクトが格納されています。これは、それぞれ個々の人物を表し、その人物に属する一連の顔データが保存されています。

[Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249) 操作では、顔データの比較に使用するデータ セットを準備します。

### <a name="identification"></a>識別

[Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) 操作では、(DetectedFace または PersistedFace オブジェクトから) 1 つまたは複数のソースとなる顔 ID と、PersonGroup または LargePersonGroup を受け取ります。 これは、ソースとなるそれぞれの顔が属している可能性のある Person オブジェクトのリストを返します。 返される Person オブジェクトは、予測信頼度値を含む Candidate オブジェクトとしてまとめられます。


### <a name="verification"></a>検証

[Verify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a) 操作では、(DetectedFace または PersistedFace オブジェクトから) 1 つの顔 ID と Person オブジェクトを受け取ります。 ここでは、その顔が同じ人物のものであるかどうかの判断が行われます。 検証は 1 対 1 の照合で、Identify API 呼び出しの結果に対する最終的なチェックとして使用できます。 ただし、必要に応じて、API のパフォーマンスを向上させるために候補となる人物が属している PersonGroup を渡すことができます。


## <a name="input-data"></a>入力データ

次のヒントを使用して、確実に入力画像から最も正確な認識結果が得られるようにします。

* サポートされている入力画像形式は、JPEG、PNG、GIF (最初のフレーム)、BMP です。
* 画像ファイル サイズは 6 MB 以内であることが必要です。
* Person オブジェクトを作成するときに、さまざまな種類の角度と照明を備えた写真を使用します。
* 次のような技術的な課題のために、一部の顔を認識できない場合があります。
  * 強すぎる照明を含む画像 (強烈な逆光照明など)
  * 一方または両方の目を遮っている障害物
  * 髪質や顔の毛の違い
  * 年齢による顔立ちの変化
  * 極端な表情

## <a name="next-steps"></a>次のステップ

顔認識の概念を理解したので、トレーニング済みの PersonGroup に対して顔を識別するスクリプトを作成します。

* [Face クライアント ライブラリのクイックスタート](../Quickstarts/client-libraries.md)