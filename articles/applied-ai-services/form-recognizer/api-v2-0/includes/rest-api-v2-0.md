---
title: 'リファレンス: Form Recognizer 2.0 REST API'
description: Form Recognizer の REST API を使用して、カスタム ドキュメントからキーと値のペアとテーブル データを抽出するフォーム処理アプリを作成します。
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 05/25/2021
ms.author: lajanuar
ms.custom: ignite-fall-2021
ms.openlocfilehash: 645ef6947afb52e1a76d859d72649b89e5db0614
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131253141"
---
<!-- markdownlint-disable MD001 -->
<!-- markdownlint-disable MD024 -->
<!-- markdownlint-disable MD033 -->
<!-- markdownlint-disable MD034 -->
> [!NOTE]
> このガイドでは、cURL を使用して REST API 呼び出しを実行します。

|[Form Recognizer REST API](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm)|[Azure REST API リファレンス](/rest/api/azure/)|

## <a name="prerequisites"></a>前提条件

* インストールされた [cURL](https://curl.haxx.se/windows/)。
* [PowerShell バージョン 6.0 以降](/powershell/scripting/install/installing-powershell-core-on-windows)、または同様のコマンド ライン アプリケーション。
* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/cognitive-services/)
* トレーニング データのセットを含む Azure Storage Blob。 トレーニング データ セットをまとめるためのヒントとオプションについては、「[カスタム モデルのトレーニング データ セットを作成する](../../build-training-data-set.md)」を参照してください。 このクイックスタートでは、[サンプル データ セット](https://go.microsoft.com/fwlink/?linkid=2090451)の **Train** フォルダーにあるファイルを使用できます (*sample_data.zip* をダウンロードして展開します)。
* Azure サブスクリプションを用意できたら、Azure portal で <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title="Form Recognizer リソースを作成"  target="_blank">Form Recognizer リソースを作成</a>し、自分のキーとエンドポイントを取得します。 デプロイされたら、 **[リソースに移動]** を選択します。
  * 自分のアプリケーションを Form Recognizer API に接続するには、作成したリソースのキーとエンドポイントが必要になります。 このクイックスタートで後に示すコードに、自分のキーとエンドポイントを貼り付けます。
  * Free 価格レベル (`F0`) を使用してサービスを試用し、後から運用環境用の有料レベルにアップグレードすることができます。
* レシートの画像の URL。 このクイックスタートでは、[サンプルの画像](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/contoso-allinone.jpg)を使用できます。
* 名刺の画像の URL。 このクイックスタートでは、[サンプルの画像](https://raw.githubusercontent.com/Azure/azure-sdk-for-python/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms/business_cards/business-card-english.jpg)を使用できます。
* 請求書の画像の URL。 このクイックスタートでは、[サンプル ドキュメント](https://raw.githubusercontent.com/Azure/azure-sdk-for-python/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms/forms/Invoice_1.pdf)を使用できます。

## <a name="code-examples"></a>コード例

これらのコード スニペットでは、Form Recognizer REST API を使用して次のタスクを実行する方法を示します。
<!-- markdownlint-disable MD001 -->

* [レイアウトを分析する](#analyze-layout)
* [領収書を分析する](#analyze-receipts)
* [カスタム モデルをトレーニングする](#train-a-custom-model)
* [カスタム モデルを使用してフォームを分析する](#analyze-forms-with-a-custom-model)
* [カスタム モデルを管理する](#manage-custom-models)



## <a name="analyze-layout"></a>レイアウトを分析する

Form Recognizer を使用すると、ドキュメント内の表、選択マーク、テキスト、構造を分析して抽出できます。モデルをトレーニングする必要はありません。 レイアウトの抽出の詳細については、[レイアウトの概念ガイド](../../concept-layout.md)を参照してください。 コマンドを実行する前に、次の変更を行います。

1. `{Endpoint}` を、Form Recognizer サブスクリプションで取得したエンドポイントで置き換えます。
1. `{subscription key}` を、前の手順からコピーしたサブスクリプション キーに置き換えます。
1. `\"{your-document-url}` を、サンプル URL のいずれかに置き換えます。

```bash
curl -v -i POST "https://{Endpoint}/formrecognizer/v2.0/layout/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{'source': '{your-document-url}'}"
```

**Operation-Location** ヘッダーを含む `202 (Success)` 応答を受信します。 このヘッダーの値に含まれる操作 ID を使用して、非同期操作の状態のクエリを実行し、結果を取得できます。 次の例では、`analyzeResults/` の後ろの文字列が操作 ID です。

```console
https://cognitiveservice/formrecognizer/v2/layout/analyzeResults/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

### <a name="get-layout-results"></a>レイアウトの結果を取得する

**[https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeLayoutAsync)** API を呼び出した後、 **[https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/GetAnalyzeLayoutResult)** API を呼び出して、操作の状態と抽出されたデータを取得します。 コマンドを実行する前に、次の変更を行います。

1. `{Endpoint}` を、Form Recognizer サブスクリプションで取得したエンドポイントで置き換えます。
1. `{subscription key}` を、前の手順からコピーしたサブスクリプション キーに置き換えます。
1. `{resultId}` を、前の手順の操作 ID に置き換えます。
<!-- markdownlint-disable MD024 -->

```bash
curl -v -X GET "https://{Endpoint}/formrecognizer/v2.0/layout/analyzeResults/{resultId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="examine-the-results"></a>結果を確認する

JSON コンテンツを含む `200 (success)` 応答が返されます。

次の請求書の画像とそれに対応する JSON 出力をご覧ください。

* `"readResults"` ノードには、あらゆるテキスト行が、ページ上の対応する境界ボックスの配置と共に表示されます。
* 抽出された表は、`"pageResults"` セクションに含まれています。 それぞれの表について、テキスト、行インデックス、列インデックス、行スパン、列スパン、境界ボックスなどが抽出されます。

:::image type="content" source="../../media/contoso-invoice.png" alt-text="表を含む Contoso プロジェクト ステートメント ドキュメント。":::

この出力は、簡素化するために一部省略されています。 [GitHub で完全なサンプル出力](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/sample-layout-output.json)を参照してください。

```json
{
    "status": "succeeded",
    "createdDateTime": "2020-08-20T20:36:52Z",
    "lastUpdatedDateTime": "2020-08-20T20:36:58Z",
    "analyzeResult": {
        "version": "2.0.0",
        "readResults": [
            {
                "page": 1,
                "language": "en",
                "angle": 0,
                "width": 8.5,
                "height": 11,
                "unit": "inch",
                "lines": [
                    {
                        "boundingBox": [
                            0.5826,
                            0.4411,
                            2.3387,
                            0.4411,
                            2.3387,
                            0.7969,
                            0.5826,
                            0.7969
                        ],
                        "text": "Contoso, Ltd.",
                        "words": [
                            {
                                "boundingBox": [
                                    0.5826,
                                    0.4411,
                                    1.744,
                                    0.4411,
                                    1.744,
                                    0.7969,
                                    0.5826,
                                    0.7969
                                ],
                                "text": "Contoso,",
                                "confidence": 1
                            },
                            {
                                "boundingBox": [
                                    1.8448,
                                    0.4446,
                                    2.3387,
                                    0.4446,
                                    2.3387,
                                    0.7631,
                                    1.8448,
                                    0.7631
                                ],
                                "text": "Ltd.",
                                "confidence": 1
                            }
                        ]
                    },
                    ...
                ]
            }
        ],
        "pageResults": [
            {
                "page": 1,
                "tables": [
                    {
                        "rows": 5,
                        "columns": 5,
                        "cells": [
                            {
                                "rowIndex": 0,
                                "columnIndex": 0,
                                "text": "Training Date",
                                "boundingBox": [
                                    0.5133,
                                    4.2167,
                                    1.7567,
                                    4.2167,
                                    1.7567,
                                    4.4492,
                                    0.5133,
                                    4.4492
                                ],
                                "elements": [
                                    "#/readResults/0/lines/14/words/0",
                                    "#/readResults/0/lines/14/words/1"
                                ]
                            },
                            ...
                        ]
                    },
                    ...
                ]
            }
        ]
    }
}
```

## <a name="analyze-receipts"></a>領収書を分析する

このセクションでは、事前トレーニング済みの領収書モデルを使用して、米国のレシートから共通フィールドを分析、抽出する方法を示します。 レシートの分析の詳細については、[レシートの概念ガイド](../../concept-receipt.md)を参照してください。 レシートの分析を開始するには、下の cURL コマンドを使用して **[Analyze Receipt](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeReceiptAsync)** API を呼び出します。 コマンドを実行する前に、次の変更を行います。

1. `{Endpoint}` を、Form Recognizer サブスクリプションで取得したエンドポイントで置き換えます。
1. `{your receipt URL}` を、レシートの画像の URL アドレスに置き換えます。
1. `{subscription key>` を、前の手順からコピーしたサブスクリプション キーに置き換えます。

```bash
curl -i -X POST "https://{Endpoint}/formrecognizer/v2.0/prebuilt/receipt/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ 'source': '{your receipt URL}'}"
```

**Operation-Location** ヘッダーを含む `202 (Success)` 応答を受信します。 このヘッダーの値に含まれる操作 ID を使用して、非同期操作の状態のクエリを実行し、結果を取得できます。 次の例では、`operations/` の後ろの文字列が操作 ID です。

```console
https://cognitiveservice/formrecognizer/v2.0/prebuilt/receipt/operations/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

### <a name="get-the-receipt-results"></a>レシートの結果を取得する

**Analyze Receipt** API を呼び出した後に **[Get Analyze Receipt Result](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/GetAnalyzeReceiptResult)** API を呼び出して、操作の状態と抽出されたデータを取得します。 コマンドを実行する前に、次の変更を行います。

1. `{Endpoint}` を、Form Recognizer サブスクリプション キーで取得したエンドポイントで置き換えます。 これは、Form Recognizer リソースの **[概要]** タブにあります。
1. `{operationId}` を、前の手順の操作 ID に置き換えます。
1. `{subscription key}` は、実際のサブスクリプション キーで置き換えてください。

```bash
curl -X GET "https://{Endpoint}/formrecognizer/v2.0/prebuilt/receipt/analyzeResults/{operationId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="examine-the-response"></a>結果の確認

JSON 出力で `200 (Success)` 応答を受信します。 最初のフィールド `"status"` は、操作の状態を示します。 操作が完了していない場合、`"status"` の値は `"running"` または `"notStarted"` になり、手動またはスクリプトでもう一度 API を呼び出す必要があります。 呼び出しの間隔は 1 秒以上あけることをお勧めします。

`"readResults"` ノードには、認識されたすべてのテキストが格納されます (オプションの *includeTextDetails* パラメーターを `true` に設定した場合)。 テキストは、まずページごとに整理され、そのうえで行ごと、さらに個々の単語ごとに整理されます。 `"documentResults"` ノードには、モデルによって検出されたレシート固有の値が格納されます。 税、合計、店舗の住所など、大切なキーと値のペアが存在する場所です。

次のレシートの画像とそれに対応する JSON 出力をご覧ください。

![Contoso ストアのレシート](../../media/contoso-allinone.jpg)

この出力は、読みやすくするために一部省略されています。 [GitHub で完全なサンプル出力](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/receipt-result.json)を参照してください。

```json
{
  "status":"succeeded",
  "createdDateTime":"2019-12-17T04:11:24Z",
  "lastUpdatedDateTime":"2019-12-17T04:11:32Z",
  "analyzeResult":{
    "version":"2.1.0",
    "readResults":[
      {
        "page":1,
        "angle":0.6893,
        "width":1688,
        "height":3000,
        "unit":"pixel",
        "language":"en",
        "lines":[
          {
            "text":"Contoso",
            "boundingBox":[
              635,
              510,
              1086,
              461,
              1098,
              558,
              643,
              604
            ],
            "words":[
              {
                "text":"Contoso",
                "boundingBox":[
                  639,
                  510,
                  1087,
                  461,
                  1098,
                  551,
                  646,
                  604
                ],
                "confidence":0.955
              }
            ]
          },
          ...
        ]
      }
    ],
    "documentResults":[
      {
        "docType":"prebuilt:receipt",
        "pageRange":[
          1,
          1
        ],
        "fields":{
          "ReceiptType":{
            "type":"string",
            "valueString":"Itemized",
            "confidence":0.692
          },
          "MerchantName":{
            "type":"string",
            "valueString":"Contoso Contoso",
            "text":"Contoso Contoso",
            "boundingBox":[
              378.2,
              292.4,
              1117.7,
              468.3,
              1035.7,
              812.7,
              296.3,
              636.8
            ],
            "page":1,
            "confidence":0.613,
            "elements":[
              "#/readResults/0/lines/0/words/0",
              "#/readResults/0/lines/1/words/0"
            ]
          },
          "MerchantAddress":{
            "type":"string",
            "valueString":"123 Main Street Redmond, WA 98052",
            "text":"123 Main Street Redmond, WA 98052",
            "boundingBox":[
              302,
              675.8,
              848.1,
              793.7,
              809.9,
              970.4,
              263.9,
              852.5
            ],
            "page":1,
            "confidence":0.99,
            "elements":[
              "#/readResults/0/lines/2/words/0",
              "#/readResults/0/lines/2/words/1",
              "#/readResults/0/lines/2/words/2",
              "#/readResults/0/lines/3/words/0",
              "#/readResults/0/lines/3/words/1",
              "#/readResults/0/lines/3/words/2"
            ]
          },
          "MerchantPhoneNumber":{
            "type":"phoneNumber",
            "valuePhoneNumber":"+19876543210",
            "text":"987-654-3210",
            "boundingBox":[
              278,
              1004,
              656.3,
              1054.7,
              646.8,
              1125.3,
              268.5,
              1074.7
            ],
            "page":1,
            "confidence":0.99,
            "elements":[
              "#/readResults/0/lines/4/words/0"
            ]
          },
          "TransactionDate":{
            "type":"date",
            "valueDate":"2019-06-10",
            "text":"6/10/2019",
            "boundingBox":[
              265.1,
              1228.4,
              525,
              1247,
              518.9,
              1332.1,
              259,
              1313.5
            ],
            "page":1,
            "confidence":0.99,
            "elements":[
              "#/readResults/0/lines/5/words/0"
            ]
          },
          "TransactionTime":{
            "type":"time",
            "valueTime":"13:59:00",
            "text":"13:59",
            "boundingBox":[
              541,
              1248,
              677.3,
              1261.5,
              668.9,
              1346.5,
              532.6,
              1333
            ],
            "page":1,
            "confidence":0.977,
            "elements":[
              "#/readResults/0/lines/5/words/1"
            ]
          },
          "Items":{
            "type":"array",
            "valueArray":[
              {
                "type":"object",
                "valueObject":{
                  "Quantity":{
                    "type":"number",
                    "text":"1",
                    "boundingBox":[
                      245.1,
                      1581.5,
                      300.9,
                      1585.1,
                      295,
                      1676,
                      239.2,
                      1672.4
                    ],
                    "page":1,
                    "confidence":0.92,
                    "elements":[
                      "#/readResults/0/lines/7/words/0"
                    ]
                  },
                  "Name":{
                    "type":"string",
                    "valueString":"Cappuccino",
                    "text":"Cappuccino",
                    "boundingBox":[
                      322,
                      1586,
                      654.2,
                      1601.1,
                      650,
                      1693,
                      317.8,
                      1678
                    ],
                    "page":1,
                    "confidence":0.923,
                    "elements":[
                      "#/readResults/0/lines/7/words/1"
                    ]
                  },
                  "TotalPrice":{
                    "type":"number",
                    "valueNumber":2.2,
                    "text":"$2.20",
                    "boundingBox":[
                      1107.7,
                      1584,
                      1263,
                      1574,
                      1268.3,
                      1656,
                      1113,
                      1666
                    ],
                    "page":1,
                    "confidence":0.918,
                    "elements":[
                      "#/readResults/0/lines/8/words/0"
                    ]
                  }
                }
              },
              ...
            ]
          },
          "Subtotal":{
            "type":"number",
            "valueNumber":11.7,
            "text":"11.70",
            "boundingBox":[
              1146,
              2221,
              1297.3,
              2223,
              1296,
              2319,
              1144.7,
              2317
            ],
            "page":1,
            "confidence":0.955,
            "elements":[
              "#/readResults/0/lines/13/words/1"
            ]
          },
          "Tax":{
            "type":"number",
            "valueNumber":1.17,
            "text":"1.17",
            "boundingBox":[
              1190,
              2359,
              1304,
              2359,
              1304,
              2456,
              1190,
              2456
            ],
            "page":1,
            "confidence":0.979,
            "elements":[
              "#/readResults/0/lines/15/words/1"
            ]
          },
          "Tip":{
            "type":"number",
            "valueNumber":1.63,
            "text":"1.63",
            "boundingBox":[
              1094,
              2479,
              1267.7,
              2485,
              1264,
              2591,
              1090.3,
              2585
            ],
            "page":1,
            "confidence":0.941,
            "elements":[
              "#/readResults/0/lines/17/words/1"
            ]
          },
          "Total":{
            "type":"number",
            "valueNumber":14.5,
            "text":"$14.50",
            "boundingBox":[
              1034.2,
              2617,
              1387.5,
              2638.2,
              1380,
              2763,
              1026.7,
              2741.8
            ],
            "page":1,
            "confidence":0.985,
            "elements":[
              "#/readResults/0/lines/19/words/0"
            ]
          }
        }
      }
    ]
  }
}
```

## <a name="train-a-custom-model"></a>カスタム モデルをトレーニングする

カスタム モデルをトレーニングするには、Azure Storage BLOB 内にトレーニング データのセットが必要です。 同じ種類または構造の入力済みフォーム (PDF ドキュメントや画像) が少なくとも 5 つ必要です。 トレーニング データをまとめるためのヒントとオプションについては、[カスタム モデル用のトレーニング データ セットの作成](../../build-training-data-set.md)に関する記事を参照してください。

ラベル付けされたデータを使用しないトレーニングが既定の操作であり、その方が単純です。 トレーニング データの一部またはすべてにあらかじめ手動でラベルを付けることもできます。 これは複雑なプロセスですが、トレーニングされたモデルの精度が向上します。

> [!NOTE]
> また、[Form Recognizer のサンプル ラベル付けツール](../../label-tool.md)などのグラフィカル ユーザー インターフェイスを使用してモデルをトレーニングすることもできます。

### <a name="train-a-model-without-labels"></a>ラベルなしでモデルをトレーニングする

Azure BLOB コンテナー内のドキュメントを使用して Form Recognizer モデルをトレーニングするには、次の cURL コマンドを実行して、 **[Train Custom Model]https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/TrainCustomModelAsync)** API を呼び出します。 コマンドを実行する前に、次の変更を行います。

1. `{Endpoint}` を、Form Recognizer サブスクリプションで取得したエンドポイントで置き換えます。
1. `{subscription key}` を、前の手順からコピーしたサブスクリプション キーに置き換えます。
1. `{SAS URL}` を Azure Blob ストレージ コンテナーの共有アクセス署名 (SAS) URL に置き換えます。 

[!INCLUDE [get SAS URL](sas-instruction.md)]

   :::image type="content" source="../../media/quickstarts/get-sas-url.png" alt-text="SAS URL の取得":::

```bash
curl -i -X POST "https://{Endpoint}/formrecognizer/v2.0/custom/models" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ 'source': '{SAS URL}'}"
```

**Location** ヘッダーで `201 (Success)` 応答を受信します。 このヘッダーの値は、トレーニング中の新しいモデルの ID です。

### <a name="train-a-model-with-labels"></a>ラベルを使用してモデルをトレーニングする

ラベルを使用してトレーニングするには、トレーニング ドキュメントと共に、自分の Blob Storage コンテナーに特別なラベル情報ファイル (`\<filename\>.pdf.labels.json`) を用意する必要があります。 [Form Recognizer のサンプル ラベル付けツール](../../label-tool.md)では、これらのラベル ファイルの作成を支援する UI が提供されています。 それらを用意したら、 **[Train Custom Model](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/TrainCustomModelAsync)** API を呼び出すことができます。JSON 本文で `"useLabelFile"` パラメーターを `true` に設定してください。

コマンドを実行する前に、次の変更を行います。

1. `{Endpoint}` を、Form Recognizer サブスクリプションで取得したエンドポイントで置き換えます。
1. `{subscription key}` を、前の手順からコピーしたサブスクリプション キーに置き換えます。
1. `{SAS URL}` を Azure Blob ストレージ コンテナーの共有アクセス署名 (SAS) URL に置き換えます。 [!INCLUDE [get SAS URL](sas-instruction.md)]

   :::image type="content" source="../../media/quickstarts/get-sas-url.png" alt-text="SAS URL の取得":::

```bash
curl -i -X POST "https://{Endpoint}/formrecognizer/v2.0/custom/models" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ 'source': '{SAS URL}', 'useLabelFile':true }"
```

**Location** ヘッダーで `201 (Success)` 応答を受信します。 このヘッダーの値は、トレーニング中の新しいモデルの ID です。

### <a name="get-training-results"></a>トレーニング結果を取得する

トレーニング操作の開始後、新しい操作 **[Get Custom Model](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/GetCustomModel)** を使用して、トレーニングの状態を確認します。 この API 呼び出しにモデル ID を渡して、トレーニングの状態を確認します。

1. `{Endpoint}` を、Form Recognizer サブスクリプション キーで取得したエンドポイントで置き換えます。
1. `{subscription key}` は、実際のサブスクリプション キーで置き換えてください
1. `{model ID}` を、前の手順で受信したモデル ID で置き換えます

```bash
curl -X GET "https://{Endpoint}/formrecognizer/v2.0/custom/models/{model ID}" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

次の形式の JSON 本文を含む `200 (Success)` 応答が送られてきます。 `"status"` フィールドに注目します。 トレーニングが完了すると、この値は `"ready"` になります。 モデルのトレーニングが完了していない場合は、コマンドを再実行して、サービスに対して再度クエリを実行する必要があります。 呼び出しの間隔は 1 秒以上あけることをお勧めします。

`"modelId"` フィールドには、トレーニング中のモデルの ID が含まれています。 これは、次の手順で必要になります。

```json
{
  "modelInfo":{
    "status":"ready",
    "createdDateTime":"2019-10-08T10:20:31.957784",
    "lastUpdatedDateTime":"2019-10-08T14:20:41+00:00",
    "modelId":"1cfb372bab404ba3aa59481ab2c63da5"
  },
  "trainResult":{
    "trainingDocuments":[
      {
        "documentName":"invoices\\Invoice_1.pdf",
        "pages":1,
        "errors":[

        ],
        "status":"succeeded"
      },
      {
        "documentName":"invoices\\Invoice_2.pdf",
        "pages":1,
        "errors":[

        ],
        "status":"succeeded"
      },
      {
        "documentName":"invoices\\Invoice_3.pdf",
        "pages":1,
        "errors":[

        ],
        "status":"succeeded"
      },
      {
        "documentName":"invoices\\Invoice_4.pdf",
        "pages":1,
        "errors":[

        ],
        "status":"succeeded"
      },
      {
        "documentName":"invoices\\Invoice_5.pdf",
        "pages":1,
        "errors":[

        ],
        "status":"succeeded"
      }
    ],
    "errors":[

    ]
  },
  "keys":{
    "0":[
      "Address:",
      "Invoice For:",
      "Microsoft",
      "Page"
    ]
  }
}
```

## <a name="analyze-forms-with-a-custom-model"></a>カスタム モデルを使用してフォームを分析する

次に、新しくトレーニングしたモデルを使用してドキュメントを分析し、そこからキーと値のペアおよびテーブルを抽出します。 次の cURL コマンドを実行して、 **[Analyze Form](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm)** API を呼び出します。 コマンドを実行する前に、次の変更を行います。

1. `{Endpoint}` を、Form Recognizer サブスクリプション キーから取得したエンドポイントで置き換えます。 これは、Form Recognizer リソースの **[概要]** タブにあります。
1. `{model ID}` を、前のセクションで受信したモデル ID で置き換えます。
1. `{SAS URL}` を、Azure Storage にある実際のファイルの SAS URL に置き換えます。 「トレーニング」セクションの手順に従いますが、取得するのは、BLOB コンテナー全体の SAS URL ではなく、分析対象となる特定のファイルの SAS URL です。
1. `{subscription key}` は、実際のサブスクリプション キーで置き換えてください。

```bash
curl -v "https://{Endpoint}/formrecognizer/v2.0/custom/models/{model ID}/analyze?includeTextDetails=true" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" -d "{ 'source': '{SAS URL}' } "
```

**Operation-Location** ヘッダーを含む `202 (Success)` 応答を受信します。 このヘッダーの値は、分析操作の結果を追跡するために使用する結果 ID を含みます。 次の手順で使用できるように、この結果 ID を保存します。

### <a name="get-the-analyze-results"></a>分析結果を取得する

分析操作の結果を照会するには、Get **[Analyze Form Result](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/GetAnalyzeFormResult)** API を呼び出します。

1. `{Endpoint}` を、Form Recognizer サブスクリプション キーから取得したエンドポイントで置き換えます。 これは、Form Recognizer リソースの **[概要]** タブにあります。
1. `{result ID}` を、前のセクションで受信した ID で置き換えます。
1. `{subscription key}` は、実際のサブスクリプション キーで置き換えてください。

```bash
curl -X GET "https://{Endpoint}/formrecognizer/v2.0/custom/models/{model ID}/analyzeResults/{result ID}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

次の形式の JSON 本文を含む `200 (Success)` 応答が送られてきます。 出力は、簡素化するために一部省略されています。 下部の近くにある `"status"` フィールドにご注意ください。 分析操作が完了すると、ここに `"succeeded"` 値が表示されます。 分析操作が完了していない場合は、コマンドを再実行して、サービスに対して再度クエリを実行する必要があります。 呼び出しの間隔は 1 秒以上あけることをお勧めします。

ラベル付けを使用せずにトレーニングしたカスタム モデルでは、キーと値のペアの関連付けとテーブルが JSON 出力の `"pageResults"` ノードに存在します。 ラベル付けを使用してトレーニングしたカスタム モデルでは、キーと値のペアの関連付けが `"documentResults"` ノードに存在します。 *includeTextDetails* URL パラメーターを使用してプレーンテキスト抽出を指定した場合、`"readResults"` ノードには、ドキュメント内のすべてのテキストの内容と位置が表示されます。

このサンプル JSON 出力は、簡素化するために一部省略されています。 [GitHub で完全なサンプル出力](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/analyze-result-invoice-6.pdf.json)を参照してください。

```JSON
{
  "status": "succeeded",
  "createdDateTime": "2020-08-21T00:46:25Z",
  "lastUpdatedDateTime": "2020-08-21T00:46:32Z",
  "analyzeResult": {
    "version": "2.0.0",
    "readResults": [
      {
        "page": 1,
        "angle": 0,
        "width": 8.5,
        "height": 11,
        "unit": "inch",
        "lines": [
          {
            "text": "Project Statement",
            "boundingBox": [
              5.0153,
              0.275,
              8.0944,
              0.275,
              8.0944,
              0.7125,
              5.0153,
              0.7125
            ],
            "words": [
              {
                "text": "Project",
                "boundingBox": [
                  5.0153,
                  0.275,
                  6.2278,
                  0.275,
                  6.2278,
                  0.7125,
                  5.0153,
                  0.7125
                ]
              },
              {
                "text": "Statement",
                "boundingBox": [
                  6.3292,
                  0.275,
                  8.0944,
                  0.275,
                  8.0944,
                  0.7125,
                  6.3292,
                  0.7125
                ]
              }
            ]
          },
        ...
        ]
      }
    ],
    "pageResults": [
      {
        "page": 1,
        "keyValuePairs": [
          {
            "key": {
              "text": "Date:",
              "boundingBox": [
                6.9722,
                1.0264,
                7.3417,
                1.0264,
                7.3417,
                1.1931,
                6.9722,
                1.1931
              ],
              "elements": [
                "#/readResults/0/lines/2/words/0"
              ]
            },
            "confidence": 1
          },
         ...
        ],
        "tables": [
          {
            "rows": 4,
            "columns": 5,
            "cells": [
              {
                "text": "Training Date",
                "rowIndex": 0,
                "columnIndex": 0,
                "boundingBox": [
                  0.6931,
                  4.2444,
                  1.5681,
                  4.2444,
                  1.5681,
                  4.4125,
                  0.6931,
                  4.4125
                ],
                "confidence": 1,
                "rowSpan": 1,
                "columnSpan": 1,
                "elements": [
                  "#/readResults/0/lines/15/words/0",
                  "#/readResults/0/lines/15/words/1"
                ],
                "isHeader": true,
                "isFooter": false
              },
              ...
            ]
          }
        ],
        "clusterId": 0
      }
    ],
    "documentResults": [],
    "errors": []
  }
}
```

### <a name="improve-results"></a>結果を改善する

[!INCLUDE [improve results](improve-result-unlabeled.md)]

## <a name="manage-custom-models"></a>カスタム モデルを管理する

### <a name="get-a-list-of-custom-models"></a>カスタム モデルのリストを取得する

次のコマンドでは、 **[List Custom Models](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/GetCustomModels)** API を使用して、サブスクリプションに属しているすべてのカスタム モデルのリストを返します。

1. `{Endpoint}` を、Form Recognizer サブスクリプションで取得したエンドポイントで置き換えます。
1. `{subscription key}` を、前の手順からコピーしたサブスクリプション キーに置き換えます。

```bash
curl -v -X GET "https://{Endpoint}/formrecognizer/v2.0/custom/models?op=full"
-H "Ocp-Apim-Subscription-Key: {subscription key}"
```

次のような JSON データを含む `200` 成功応答が返されます。 `"modelList"` 要素には、作成したすべてのモデルとその情報が含まれています。

```json
{
  "summary": {
    "count": 0,
    "limit": 0,
    "lastUpdatedDateTime": "string"
  },
  "modelList": [
    {
      "modelId": "string",
      "status": "creating",
      "createdDateTime": "string",
      "lastUpdatedDateTime": "string"
    }
  ],
  "nextLink": "string"
}
```

### <a name="get-a-specific-model"></a>特定のモデルを取得する

特定のカスタム モデルに関する詳細情報を取得するために、次のコマンドでは、 **[Get Custom Model](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/GetCustomModel)** API を使用します。

1. `{Endpoint}` を、Form Recognizer サブスクリプションで取得したエンドポイントで置き換えます。
1. `{subscription key}` を、前の手順からコピーしたサブスクリプション キーに置き換えます。
1. `{modelId}` を、検索するカスタム モデルの ID で置き換えます。

```bash
curl -v -X GET "https://{Endpoint}/formrecognizer/v2.0/custom/models/{modelId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

次のような JSON データを含む `200` 成功応答が返されます。

```json
{
  "modelInfo": {
    "modelId": "string",
    "status": "creating",
    "createdDateTime": "string",
    "lastUpdatedDateTime": "string"
  },
  "keys": {
    "clusters": {}
  },
  "trainResult": {
    "trainingDocuments": [
      {
        "documentName": "string",
        "pages": 0,
        "errors": [
          "string"
        ],
        "status": "succeeded"
      }
    ],
    "fields": [
      {
        "fieldName": "string",
        "accuracy": 0.0
      }
    ],
    "averageModelAccuracy": 0.0,
    "errors": [
      {
        "message": "string"
      }
    ]
  }
}
```

### <a name="delete-a-model-from-the-resource-account"></a>リソース アカウントからモデルを削除する

ID を参照して、アカウントからモデルを削除することもできます。 このコマンドは **[Delete Custom Model](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/DeleteCustomModel)** API を呼び出して、前のセクションで使用したモデルを削除します。

1. `{Endpoint}` を、Form Recognizer サブスクリプションで取得したエンドポイントで置き換えます。
1. `{subscription key}` を、前の手順からコピーしたサブスクリプション キーに置き換えます。
1. `{modelId}` を、検索するカスタム モデルの ID で置き換えます。

```bash
curl -v -X DELETE "https://{Endpoint}/formrecognizer/v2.0/custom/models/{modelId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

モデルが削除対象としてマークされたことを示す `204` 成功応答が返されます。 モデル成果物は、48 時間以内に削除されます。

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、Form Recognizer REST API を使用してモデルをトレーニングし、さまざまな方法でフォームを分析しました。 次に、Form Recognizer API の詳細を把握するためにリファレンス ドキュメントを参照します。

> [!div class="nextstepaction"]
> [REST API リファレンス ドキュメント](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm)
