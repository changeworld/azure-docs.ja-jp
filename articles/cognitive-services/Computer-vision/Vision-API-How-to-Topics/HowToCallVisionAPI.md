---
title: 画像分析 API の呼び出し
titleSuffix: Azure Cognitive Services
description: Image Analysis API を呼び出してその動作を構成する方法について説明します。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 09/09/2019
ms.author: kefre
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 3f9a6afe3202df40e26332c3a8c91b8c3eca8a32
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/07/2021
ms.locfileid: "107012270"
---
# <a name="call-the-image-analysis-api"></a>画像分析 API の呼び出し

この記事では、Image Analysis API を呼び出して画像の視覚的特徴に関する情報を返す方法について説明します。

このガイドでは、既に <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="Computer Vision リソースを作成"  target="_blank">Computer Vision リソースを作成</a>し、サブスクリプション キーとエンドポイント URL を取得していることを前提としています。 まだの場合は、[クイックスタート](../quickstarts-sdk/image-analysis-client-library.md)に従って作業を開始してください。
  
## <a name="submit-data-to-the-service"></a>サービスにデータを送信する

ローカルの画像またはリモートの画像を Analyze API に送信します。 ローカルの場合、バイナリ画像データを HTTP 要求本文に配置します。 リモートの場合、要求本文を `{"url":"http://example.com/images/test.jpg"}` のような形式にして、画像の URL を指定します。

## <a name="determine-how-to-process-the-data"></a>データの処理方法を決定する

###  <a name="select-visual-features"></a>視覚的特徴を選択する

[Analyze API](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-3/operations/56f91f2e778daf14a499f21b) を使用すると、サービスの画像分析の特徴すべてにアクセスできます。 使用する特徴を指定するには、URL クエリ パラメーターを設定する必要があります。 パラメーターには、複数の値をコンマで区切って指定できます。 特徴を指定するごとに追加の処理時間が必要になるため、必要なものだけを指定してください。

|URL パラメーター | 値 | 説明|
|---|---|--|
|`visualFeatures`|`Adult` | 画像が事実上のポルノ (裸や性行為を表している) または不快 (極端な暴力や流血を表している) かどうかを検出します。 性的な暗示を含むコンテンツ (わいせつコンテンツ) も検出されます。|
||`Brands` | おおよその場所など、画像内のさまざまなブランドを検出します。 Brands 引数は、英語でのみ使用できます。|
||`Categories` | ドキュメントで定義されている分類法に従って画像コンテンツを分類します。 これは `visualFeatures` の既定値です。|
||`Color` | アクセント カラー、主要カラーのほか、画像が白黒かどうかを判定します。|
||`Description` | サポートされる言語の完全な文章を使用して画像のコンテンツを説明します。|
||`Faces` | 顔の有無を検出します。 存在する場合は、座標、性別、および年齢を生成します。|
||`ImageType` | 画像がクリップ アートか線画かを検出します。|
||`Objects` | おおよその場所など、画像内のさまざまなオブジェクトを検出します。 Objects 引数は、英語でのみ使用できます。|
||`Tags` | 画像のコンテンツに関連した単語の詳細な一覧を使用して画像にタグを付けます。|
|`details`| `Celebrities` | 画像内で検出された場合に有名人を特定します。|
||`Landmarks` |画像内で検出された場合にランドマークを特定します。|

値が指定された URL は次のようになります。

`https://{endpoint}/vision/v2.1/analyze?visualFeatures=Description,Tags&details=Celebrities`

### <a name="specify-languages"></a>言語を指定する

返されるデータの言語を指定することもできます。 次の URL クエリ パラメーターで言語を指定します。 既定値は `en` です。

|URL パラメーター | 値 | 説明|
|---|---|--|
|`language`|`en` | 英語|
||`es` | スペイン語|
||`ja` | 日本語|
||`pt` | Portuguese|
||`zh` | 簡体中国語|

値が指定された URL は次のようになります。

`https://{endpoint}/vision/v2.1/analyze?visualFeatures=Description,Tags&details=Celebrities&language=en`

> [!NOTE]
> **スコープが指定された API 呼び出し**
>
> 画像分析における特徴の一部は、Analyze API 呼び出しを使用した方法だけでなく、直接呼び出すこともできます。 たとえば、`https://{endpoint}/vision/v3.2-preview.3/tag` に対して要求を行うことで、画像タグのみをスコープに指定した分析を実行できます。 個別に呼び出すことができる他の特徴については、[リファレンス ドキュメント](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-3/operations/56f91f2e778daf14a499f21b)を参照してください。

## <a name="get-results-from-the-service"></a>サービスから結果を取得する

サービスから `200` HTTP 応答が返され、その本文には、返されたデータが JSON 文字列の形式で含まれています。 JSON 応答の例を次に示します。

```json
{  
  "tags":[  
    {  
      "name":"outdoor",
      "score":0.976
    },
    {  
      "name":"bird",
      "score":0.95
    }
  ],
  "description":{  
    "tags":[  
      "outdoor",
      "bird"
    ],
    "captions":[  
      {  
        "text":"partridge in a pear tree",
        "confidence":0.96
      }
    ]
  }
}
```

この例のフィールドの説明については、次の表を参照してください。

フィールド | Type | コンテンツ
------|------|------|
Tags  | `object` | タグの配列の最上位オブジェクト。
tags[].Name | `string`    | タグ分類子からのキーワード。
tags[].Score    | `number`    | 信頼度スコア (0 と 1 の間)。
description     | `object`    | 画像の説明に関する最上位オブジェクト。
description.tags[] |    `string`    | タグの一覧。 キャプションを生成する機能で信頼度が不足している場合、おそらく呼び出し元が入手できる情報はタグのみになります。
description.captions[].text    | `string`    | 画像を説明する語句。
description.captions[].confidence    | `number`    | 語句の信頼度スコア。

### <a name="error-codes"></a>エラー コード

考えられるエラーとその原因を次の一覧に示します。

* 400
    * InvalidImageUrl - 画像の URL は形式が正しくないか、アクセスできません。
    * InvalidImageFormat - 入力データが有効な画像ではありません。
    * InvalidImageSize - 入力画像が大きすぎます。
    * NotSupportedVisualFeature - 指定された特徴の種類が無効です。
    * NotSupportedImage - サポートされていない画像 (例: 児童ポルノ)。
    * InvalidDetails - サポートされていない `detail` パラメーター値。
    * NotSupportedLanguage - 要求された操作が指定された言語でサポートされていません。
    * BadArgument - 追加の詳細情報はエラー メッセージに記載されています。
* 415 - サポートされていないメディアの種類エラー。 Content-Type が使用できる種類に含まれていません。
    * 画像 URL の場合: Content-Type は application/json にする必要があります
    * バイナリ画像データの場合: Content-Type は application/octet-stream または multipart/form-data にする必要があります
* 500
    * FailedToProcess
    * Timeout - 画像処理がタイムアウトしました。
    * InternalServerError

## <a name="next-steps"></a>次のステップ

REST API を試すには、[Image Analysis API リファレンス](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-3/operations/56f91f2e778daf14a499f21b)を参照してください。
