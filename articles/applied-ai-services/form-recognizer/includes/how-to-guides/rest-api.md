---
title: Azure Form Recognizer REST API を使用する
description: Form Recognizer の REST API を使用して、カスタム ドキュメントからキーと値のペアとテーブル データを抽出するフォーム処理アプリを作成します。
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 11/02/2021
ms.author: lajanuar
ms.custom: ignite-fall-2021
ms.openlocfilehash: d74e7e84461eadc40cd4d2940ae083a411f12f57
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131089911"
---
<!-- markdownlint-disable MD001 -->
<!-- markdownlint-disable MD024 -->
<!-- markdownlint-disable MD033 -->
<!-- markdownlint-disable MD034 -->

> [!NOTE]
>
> * このプロジェクトでは、cURL を使用して REST API 呼び出しを実行し、Azure Form Recognizer API バージョン **2.1** を対象としています。

| [Form Recognizer REST API](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeWithCustomForm) | [Azure REST API リファレンス](/rest/api/azure/) |

## <a name="prerequisites"></a>前提条件

* インストールされた [cURL](https://curl.haxx.se/windows/)。
* [PowerShell バージョン 6.0 以降](/powershell/scripting/install/installing-powershell-core-on-windows)、または同様のコマンド ライン アプリケーション。
* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/cognitive-services/)
* トレーニング データのセットを含む Azure Storage Blob。 トレーニング データ セットをまとめるためのヒントとオプションについては、「[カスタム モデルのトレーニング データ セットを作成する](../../build-training-data-set.md)」を参照してください。 [サンプル データ セット](https://go.microsoft.com/fwlink/?linkid=2090451) (*sample_data.zip* をダウンロードして展開します) の **Train** フォルダーにあるファイルを使用できます。
* Azure サブスクリプションを用意できたら、Azure portal で <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title="Form Recognizer リソースを作成"  target="_blank">Form Recognizer リソースを作成</a>し、自分のキーとエンドポイントを取得します。 デプロイされたら、 **[リソースに移動]** を選択します。
  * 自分のアプリケーションを Form Recognizer API に接続するには、作成したリソースのキーとエンドポイントが必要になります。 このプロジェクトの後で示すコードに、自分のキーとエンドポイントを貼り付けます。
  * Free 価格レベル (<ph id="ph1">`F0`</ph>) を使用してサービスを試用し、後から運用環境用の有料レベルにアップグレードすることができます。
<<<<<<< HEAD:articles/applied-ai-services/form-recognizer/includes/how-to-guides/rest-api.md
* **レシートの画像** の URL。 [サンプル画像](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/contoso-allinone.jpg)を使用できます。
* **名刺の画像** の URL。 [サンプル画像](https://raw.githubusercontent.com/Azure/azure-sdk-for-python/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms/business_cards/business-card-english.jpg)を使用できます。
* **請求書の画像** の URL。 [サンプル ドキュメント](https://raw.githubusercontent.com/Azure/azure-sdk-for-python/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms/forms/Invoice_1.pdf)を使用できます。
* **身分証明書の画像** の URL。 [サンプル画像](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/DriverLicense.png)を使用できます。
=======
* **レシートの画像** の URL。 このクイックスタートでは、[サンプルの画像](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/contoso-allinone.jpg)を使用できます。
* **名刺の画像** の URL。 このクイックスタートでは、[サンプルの画像](https://raw.githubusercontent.com/Azure/azure-sdk-for-python/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms/business_cards/business-card-english.jpg)を使用できます。
* **請求書の画像** の URL。 このクイックスタートでは、[サンプル ドキュメント](https://raw.githubusercontent.com/Azure/azure-sdk-for-python/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms/forms/Invoice_1.pdf)を使用できます。
* **身分証明書の画像** の URL。 [サンプル画像](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/DriverLicense.png)を使用できます。
>>>>>>> 103f7cf9752d7b4e4c9bf3da2c3649ad27ebfd2f:articles/applied-ai-services/form-recognizer/includes/quickstarts/rest-api.md

## <a name="analyze-layout"></a>レイアウトを分析する

Form Recognizer を使用すると、ドキュメント内の表、選択マーク、テキスト、構造を分析して抽出できます。モデルをトレーニングする必要はありません。 レイアウトの抽出の詳細については、[レイアウトの概念ガイド](../../concept-layout.md)を参照してください。 コマンドを実行する前に、次の変更を行います。

1. `{endpoint}` を、Form Recognizer サブスクリプションで取得したエンドポイントで置き換えます。
1. `{subscription key}` を、前の手順からコピーしたサブスクリプション キーに置き換えます。
1. `\"{your-document-url}` を、サンプル URL のいずれかに置き換えます。

#### <a name="request"></a>Request

```bash
curl -v -i POST "https://{endpoint}/formrecognizer/v2.1/layout/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{'source': '{your-document-url}'}"
```

#### <a name="operation-location"></a>Operation-Location

**Operation-Location** ヘッダーを含む `202 (Success)` 応答を受信します。 このヘッダーの値に含まれる結果 ID を使用して、非同期操作の状態のクエリを実行し、結果を取得できます。

https://<span></span>cognitiveservice/formrecognizer/v2.1/layout/analyzeResults/ **{resultId}** 

次の例では、URL の一部として、`analyzeResults/` の後の文字列が結果 ID になります。

```console
https://cognitiveservice/formrecognizer/v2/layout/analyzeResults/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

### <a name="get-layout-results"></a>レイアウトの結果を取得する

**[Analyze Layout](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeLayoutAsync)** API を呼び出した後に **[Get Analyze Layout Result](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/GetAnalyzeLayoutResult)** API を呼び出して、操作の状態と抽出されたデータを取得します。 コマンドを実行する前に、次の変更を行います。

1. `{endpoint}` を、Form Recognizer サブスクリプションで取得したエンドポイントで置き換えます。
1. `{subscription key}` を、前の手順からコピーしたサブスクリプション キーに置き換えます。
1. `{resultId}` を、前の手順の結果 ID に置き換えます。
<!-- markdownlint-disable MD024 -->

#### <a name="request"></a>Request

```bash
curl -v -X GET "https://{endpoint}/formrecognizer/v2.1/layout/analyzeResults/{resultId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="examine-the-results"></a>結果を確認する

JSON コンテンツを含む `200 (success)` 応答が返されます。

次の請求書の画像とそれに対応する JSON 出力をご覧ください。

* `"readResults"` ノードには、あらゆるテキスト行が、ページ上の対応する境界ボックスの配置と共に表示されます。
* `selectionMarks` ノードには、すべての選択マーク (チェック ボックス、ラジオ マーク) と、その状態が "選択済み" と "未選択" のどちらであるかが示されます。
* 抽出された表は、`"pageResults"` セクションに含まれています。 それぞれの表について、テキスト、行インデックス、列インデックス、行スパン、列スパン、境界ボックスなどが抽出されます。

:::image type="content" source="../../media/contoso-invoice.png" alt-text="表を含む Contoso プロジェクト ステートメント ドキュメント。":::

#### <a name="response-body"></a>応答本文

この出力は、簡素化するために一部省略されています。 [GitHub で完全なサンプル出力](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/sample-layout-output.json)を参照してください。

```json
{
    "status": "succeeded",
    "createdDateTime": "2020-08-20T20:40:50Z",
    "lastUpdatedDateTime": "2020-08-20T20:40:55Z",
    "analyzeResult": {
        "version": "2.1.0",
        "readResults": [
            {
                "page": 1,
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
                "selectionMarks": [
                    {
                        "boundingBox": [
                            3.9737,
                            3.7475,
                            4.1693,
                            3.7475,
                            4.1693,
                            3.9428,
                            3.9737,
                            3.9428
                        ],
                        "confidence": 0.989,
                        "state": "selected"
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
                                    "#/readResults/0/lines/12/words/0",
                                    "#/readResults/0/lines/12/words/1"
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

このセクションでは、事前トレーニング済みの領収書モデルを使用して、米国のレシートから共通フィールドを分析、抽出する方法を示します。 レシートの分析の詳細については、[レシートの概念ガイド](../../concept-receipt.md)を参照してください。 レシートの分析を開始するには、下の cURL コマンドを使用して **[Analyze Receipt](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeReceiptAsync)** API を呼び出します。 コマンドを実行する前に、次の変更を行います。

1. `{endpoint}` を、Form Recognizer サブスクリプションで取得したエンドポイントで置き換えます。
1. `{your receipt URL}` を、レシートの画像の URL アドレスに置き換えます。
1. `{subscription key>` を、前の手順からコピーしたサブスクリプション キーに置き換えます。

#### <a name="request"></a>Request

```bash
curl -i -X POST "https://{endpoint}/formrecognizer/v2.1/prebuilt/receipt/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ 'source': '{your receipt URL}'}"
```

#### <a name="operation-location"></a>Operation-Location

**Operation-Location** ヘッダーを含む `202 (Success)` 応答を受信します。 このヘッダーの値に含まれる結果 ID を使用して、非同期操作の状態のクエリを実行し、結果を取得できます。

*https://<span></span>cognitiveservice/formrecognizer/v2.1/prebuilt/receipt/analyzeResults/**{resultId}* **

次の例では、`operations/` の後の文字列が結果 ID です。

```console
https://cognitiveservice/formrecognizer/v2.1/prebuilt/receipt/operations/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

### <a name="get-receipt-results"></a>レシートの結果を取得する

**Analyze Receipt** API を呼び出した後に **[Get Analyze Receipt Result](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/GetAnalyzeReceiptResult)** API を呼び出して、操作の状態と抽出されたデータを取得します。 コマンドを実行する前に、次の変更を行います。

1. `{endpoint}` を、Form Recognizer サブスクリプション キーで取得したエンドポイントで置き換えます。 これは、Form Recognizer リソースの **[概要]** タブにあります。
1. `{resultId}` を、前の手順の結果 ID に置き換えます。
1. `{subscription key}` は、実際のサブスクリプション キーで置き換えてください。

#### <a name="request"></a>Request

```bash
curl -X GET "https://{endpoint}/formrecognizer/v2.1/prebuilt/receipt/analyzeResults/{resultId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="examine-the-response"></a>結果の確認

JSON 出力で `200 (Success)` 応答を受信します。 最初のフィールド `"status"` は、操作の状態を示します。 操作が完了していない場合、`"status"` の値は `"running"` または `"notStarted"` になり、手動またはスクリプトでもう一度 API を呼び出す必要があります。 呼び出しの間隔は 1 秒以上あけることをお勧めします。

`"readResults"` ノードには、認識されたすべてのテキストが格納されます (オプションの *includeTextDetails* パラメーターを `true` に設定した場合)。 テキストは、まずページごとに整理され、そのうえで行ごと、さらに個々の単語ごとに整理されます。 `"documentResults"` ノードには、モデルによって検出されたレシート固有の値が格納されます。 税、合計、店舗の住所など、大切なキーと値のペアが存在する場所です。

次のレシートの画像とそれに対応する JSON 出力をご覧ください。

![Contoso ストアのレシート](../../media/contoso-allinone.jpg)

#### <a name="response-body"></a>応答本文

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

## <a name="analyze-business-cards"></a>名刺を分析する

このセクションでは、事前トレーニング済みのモデルを使用して、英語の名刺から共通フィールドを分析、抽出する方法を示します。 名刺の分析の詳細については、[名刺の概念ガイド](../../concept-business-card.md)を参照してください。 名刺の分析を開始するには、下の cURL コマンドを使用して **[Analyze Business Card](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeBusinessCardAsync)** API を呼び出します。 コマンドを実行する前に、次の変更を行います。

1. `{endpoint}` を、Form Recognizer サブスクリプションで取得したエンドポイントで置き換えます。
1. `{your business card URL}` を、レシートの画像の URL アドレスに置き換えます。
1. `{subscription key}` を、前の手順からコピーしたサブスクリプション キーに置き換えます。

#### <a name="request"></a>Request

```bash
curl -i -X POST "https://{endpoint}/formrecognizer/v2.1/prebuilt/businessCard/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ 'source': '{your receipt URL}'}"
```

#### <a name="operation-location"></a>Operation-Location

**Operation-Location** ヘッダーを含む `202 (Success)` 応答を受信します。 このヘッダーの値に含まれる結果 ID を使用して、非同期操作の状態のクエリを実行し、結果を取得できます。

_https://<span></span>cognitiveservice/formrecognizer/v2.1/prebuilt/businessCard/analyzeResults/ **{resultId}**_

次の例では、URL の一部として、`analyzeResults/` の後の文字列が結果 ID になります。

```console
https://cognitiveservice/formrecognizer/v2.1/prebuilt/businessCard/analyzeResults/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

### <a name="get-business-card-results"></a>名刺の結果を取得する

**Analyze Business Card** API を呼び出した後に **[Get Analyze Business Card Result](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/GetAnalyzeBusinessCardResult)** API を呼び出して、操作の状態と抽出されたデータを取得します。 コマンドを実行する前に、次の変更を行います。

1. `{endpoint}` を、Form Recognizer サブスクリプション キーで取得したエンドポイントで置き換えます。 これは、Form Recognizer リソースの **[概要]** タブにあります。
1. `{resultId}` を、前の手順の結果 ID に置き換えます。
1. `{subscription key}` は、実際のサブスクリプション キーで置き換えてください。

```bash
curl -v -X GET https://{endpoint}/formrecognizer/v2.1/prebuilt/businessCard/analyzeResults/{resultId}"
-H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="examine-the-response"></a>結果の確認

JSON 出力で `200 (Success)` 応答を受信します。

`"readResults"` ノードには、認識されたすべてのテキストが格納されます。 テキストは、まずページごとに整理され、そのうえで行ごと、さらに個々の単語ごとに整理されます。 `"documentResults"` ノードには、モデルによって検出された名刺固有の値が格納されます。 これは、会社名、名、姓、電話番号など、有益な連絡先情報が表示される場所です。

![Contoso 社の名刺](../../media/business-card-english.jpg)

このサンプル JSON 出力は、読みやすくするために一部省略されています。 [GitHub で完全なサンプル出力](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/business-card-result.json)を参照してください。

```json
{
    "status": "succeeded",
    "createdDateTime":"2021-02-09T18:14:05Z",
    "lastUpdatedDateTime":"2021-02-09T18:14:10Z",
    "analyzeResult": {
        "version": "2.1.0",
        "readResults": [
            {
             "page":1,
             "angle":-16.6836,
             "width":4032,
             "height":3024,
             "unit":"pixel"
          }
        ],
        "documentResults": [
            {
                "docType": "prebuilt:businesscard",
                "pageRange": [
                    1,
                    1
                ],
                "fields": {
                    "ContactNames": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "object",
                                "valueObject": {
                                    "FirstName": {
                                        "type": "string",
                                        "valueString": "Avery",
                                        "text": "Avery",
                                        "boundingBox": [
                                            703,
                                            1096,
                                            1134,
                                            989,
                                            1165,
                                            1109,
                                            733,
                                            1206
                                        ],
                                        "page": 1
                                },
                                "text": "Dr. Avery Smith",
                                "boundingBox": [
                                    419.3,
                                    1154.6,
                                    1589.6,
                                    877.9,
                                    1618.9,
                                    1001.7,
                                    448.6,
                                    1278.4
                                ],
                                "confidence": 0.993
                            }
                        ]
                    },
                    "Emails": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "avery.smith@contoso.com",
                                "text": "avery.smith@contoso.com",
                                "boundingBox": [
                                    2107,
                                    934,
                                    2917,
                                    696,
                                    2935,
                                    764,
                                    2126,
                                    995
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "Websites": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "https://www.contoso.com/",
                                "text": "https://www.contoso.com/",
                                "boundingBox": [
                                    2121,
                                    1002,
                                    2992,
                                    755,
                                    3014,
                                    826,
                                    2143,
                                    1077
                                ],
                                "page": 1,
                                "confidence": 0.995
                            }
                        ]
                    }
                }
            }
        ]
    }
}
```

このスクリプトでは、**Analyze Business Card** 操作が完了するまで、コンソールに応答が出力されます。

## <a name="analyze-invoices"></a>請求書を分析する

Form Recognizer を使用して、指定された請求書ドキュメントからフィールド テキストとセマンティック値を抽出できます。  請求書の分析を開始するには、下の cURL コマンドを使用します。 請求書の分析の詳細については、[請求書の概念ガイド](../../concept-invoice.md)を参照してください。 請求書の分析を開始するには、下の cURL コマンドを使用して **[Analyze Invoice](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/5ed8c9843c2794cbb1a96291)** API を呼び出します。 コマンドを実行する前に、次の変更を行います。

1. `{endpoint}` を、Form Recognizer サブスクリプションで取得したエンドポイントで置き換えます。
1. `{your invoice URL}` を、請求書ドキュメントの URL アドレスで置き換えます。
1. `{subscription key}` は、実際のサブスクリプション キーで置き換えてください。

#### <a name="request"></a>Request

```bash
curl -v -i POST https://{endpoint}/formrecognizer/v2.1/prebuilt/invoice/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key:  {subscription key}" --data-ascii "{'source': '{your invoice URL}'}"
```

#### <a name="operation-location"></a>Operation-Location

**Operation-Location** ヘッダーを含む `202 (Success)` 応答を受信します。 このヘッダーの値に含まれる結果 ID を使用して、非同期操作の状態のクエリを実行し、結果を取得できます。

 _https://<span></span>cognitiveservice/formrecognizer/v2.1/prebuilt/receipt/analyzeResults/ **{resultId}**_

次の例では、URL の一部として、`analyzeResults/` の後の文字列が結果 ID になります。

```console
https://cognitiveservice/formrecognizer/v2.1/prebuilt/invoice/analyzeResults/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

### <a name="get-invoice-results"></a>請求書の結果を取得する

**Analyze Invoice** API を呼び出した後に **[Get Analyze Invoice Result](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/5ed8c9acb78c40a2533aee83)** API を呼び出して、操作の状態と抽出されたデータを取得します。 コマンドを実行する前に、次の変更を行います。

1. `{endpoint}` を、Form Recognizer サブスクリプション キーで取得したエンドポイントで置き換えます。 これは、Form Recognizer リソースの **[概要]** タブにあります。
1. `{resultId}` を、前の手順の結果 ID に置き換えます。
1. `{subscription key}` は、実際のサブスクリプション キーで置き換えてください。

#### <a name="request"></a>Request

```bash
curl -v -X GET "https://{endpoint}/formrecognizer/v2.1/prebuilt/invoice/analyzeResults/{resultId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="examine-the-response"></a>結果の確認

JSON 出力で `200 (Success)` 応答を受信します。

* `"readResults"` フィールドには、請求書から抽出された各テキスト行が含まれます。
* `"pageResults"` には、請求書から抽出されたテーブルと選択マークが含まれます。
* `"documentResults"` フィールドには、請求書の最も重要な部分のキーと値の情報が含まれます。

次の請求書ドキュメントとそれに対応する JSON 出力をご覧ください。

* [サンプル請求書](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/curl/form-recognizer/sample-invoice.pdf)

#### <a name="response-body"></a>応答本文

この JSON コンテンツは、読みやすくするために一部省略されています。 [GitHub で完全なサンプル出力](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/sample-invoice-output.json)を参照してください。

```json
{
    "status": "succeeded",
    "createdDateTime": "2020-11-06T23:32:11Z",
    "lastUpdatedDateTime": "2020-11-06T23:32:20Z",
    "analyzeResult": {
        "version": "2.1.0",
        "readResults": [{
            "page": 1,
            "angle": 0,
            "width": 8.5,
            "height": 11,
            "unit": "inch"
        }],
        "pageResults": [{
            "page": 1,
            "tables": [{
                "rows": 3,
                "columns": 4,
                "cells": [{
                    "rowIndex": 0,
                    "columnIndex": 0,
                    "text": "QUANTITY",
                    "boundingBox": [0.4953,
                    5.7306,
                    1.8097,
                    5.7306,
                    1.7942,
                    6.0122,
                    0.4953,
                    6.0122]
                },
                {
                    "rowIndex": 0,
                    "columnIndex": 1,
                    "text": "DESCRIPTION",
                    "boundingBox": [1.8097,
                    5.7306,
                    5.7529,
                    5.7306,
                    5.7452,
                    6.0122,
                    1.7942,
                    6.0122]
                },
                ...
                ],
                "boundingBox": [0.4794,
                5.7132,
                8.0158,
                5.714,
                8.0118,
                6.5627,
                0.4757,
                6.5619]
            },
            {
                "rows": 2,
                "columns": 6,
                "cells": [{
                    "rowIndex": 0,
                    "columnIndex": 0,
                    "text": "SALESPERSON",
                    "boundingBox": [0.4979,
                    4.963,
                    1.8051,
                    4.963,
                    1.7975,
                    5.2398,
                    0.5056,
                    5.2398]
                },
                {
                    "rowIndex": 0,
                    "columnIndex": 1,
                    "text": "P.O. NUMBER",
                    "boundingBox": [1.8051,
                    4.963,
                    3.3047,
                    4.963,
                    3.3124,
                    5.2398,
                    1.7975,
                    5.2398]
                },
                ...
                ],
                "boundingBox": [0.4976,
                4.961,
                7.9959,
                4.9606,
                7.9959,
                5.5204,
                0.4972,
                5.5209]
            }]
        }],
        "documentResults": [{
            "docType": "prebuilt:invoice",
            "pageRange": [1,
            1],
            "fields": {
                "AmountDue": {
                    "type": "number",
                    "valueNumber": 610,
                    "text": "$610.00",
                    "boundingBox": [7.3809,
                    7.8153,
                    7.9167,
                    7.8153,
                    7.9167,
                    7.9591,
                    7.3809,
                    7.9591],
                    "page": 1,
                    "confidence": 0.875
                },
                "BillingAddress": {
                    "type": "string",
                    "valueString": "123 Bill St, Redmond WA, 98052",
                    "text": "123 Bill St, Redmond WA, 98052",
                    "boundingBox": [0.594,
                    4.3724,
                    2.0125,
                    4.3724,
                    2.0125,
                    4.7125,
                    0.594,
                    4.7125],
                    "page": 1,
                    "confidence": 0.997
                },
                "BillingAddressRecipient": {
                    "type": "string",
                    "valueString": "Microsoft Finance",
                    "text": "Microsoft Finance",
                    "boundingBox": [0.594,
                    4.1684,
                    1.7907,
                    4.1684,
                    1.7907,
                    4.2837,
                    0.594,
                    4.2837],
                    "page": 1,
                    "confidence": 0.998
                },
                ...
            }
        }]
    }
}
```

## <a name="analyze-identity-id-documents"></a>身分証明書を分析する

身分証明書の分析を開始するには、下の cURL コマンドを使用します。 身分証明書の分析の詳細については、[身分証明書の概念ガイド](../../concept-id-document.md)を参照してください。 身分証明書の分析を開始するには、以下の cURL コマンドを使用して 「 **[身分証明書の分析](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/5f74a7738978e467c5fb8707)** 」の API を呼び出します。 コマンドを実行する前に、次の変更を行います。

1. `{endpoint}` を、Form Recognizer サブスクリプションで取得したエンドポイントで置き換えます。
1. `{your ID document URL}` を、レシートの画像の URL アドレスに置き換えます。
1. `{subscription key}` を、前の手順からコピーしたサブスクリプション キーに置き換えます。

#### <a name="request"></a>Request

```bash
curl -i -X POST "https://{endpoint}/formrecognizer/v2.1/prebuilt/idDocument/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ 'source': '{your ID document URL}'}"
```

#### <a name="operation-location"></a>Operation-Location

**Operation-Location** ヘッダーを含む `202 (Success)` 応答を受信します。 このヘッダーの値に含まれる結果 ID を使用して、非同期操作の状態のクエリを実行し、結果を取得できます。

_https://<span></span>cognitiveservice/formrecognizer/v2.1/prebuilt/documentId/analyzeResults/ **{resultId}**_

次の例では、`analyzeResults/` の後の文字列が結果 ID です。

```console
https://westus.api.cognitive.microsoft.com/formrecognizer/v2.1/prebuilt/idDocument/analyzeResults/83d0137b-28e1-4051-98ce-42bd21f77ae0
```

### <a name="get-the-analyze-id-document-result"></a>Analyze ID Document の結果を取得する

**Analyze ID Document** API を呼び出した後に **[Get Analyze ID Document Result](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/GetAnalyzeBusinessCardResult)** API を呼び出して、操作の状態と抽出されたデータを取得します。  コマンドを実行する前に、次の変更を行います。

1. `{endpoint}` を、Form Recognizer サブスクリプション キーで取得したエンドポイントで置き換えます。 これは、Form Recognizer リソースの **[概要]** タブにあります。
1. `{resultId}` を、前の手順の結果 ID に置き換えます。
1. `{subscription key}` は、実際のサブスクリプション キーで置き換えてください。

#### <a name="request"></a>Request

```bash
curl -X GET "https://{endpoint}/formrecognizer/v2.1/prebuilt/businessCard/analyzeResults/{resultId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="examine-the-response"></a>結果の確認

JSON 出力で `200 (Success)` 応答を受信します。 最初のフィールド `"status"` は、操作の状態を示します。 操作が完了していない場合、`"status"` の値は `"running"` または `"notStarted"` になります。`succeeded` の値を受け取るまで、手動またはスクリプトでもう一度 API を呼び出す必要があります。 呼び出しの間隔は 1 秒以上あけることをお勧めします。

* `"readResults"` フィールドには、身分証明書から抽出されたすべてのテキスト行が含まれます。
* `"documentResults"` フィールドには、入力ドキュメント内で検出された身分証明書をそれぞれ表すオブジェクトの配列が含まれます。

サンプルの身分証明書とそれに対応する JSON 出力を以下に示します

* :::image type="content" source="https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/DriverLicense.png" alt-text="サンプルの運転免許証":::

#### <a name="response-body"></a>応答本文

```json
{
    "status": "succeeded",
    "createdDateTime": "2021-04-13T17:24:52Z",
    "lastUpdatedDateTime": "2021-04-13T17:24:55Z",
    "analyzeResult": {
      "version": "2.1.0",
      "readResults": [
        {
          "page": 1,
          "angle": -0.2823,
          "width": 450,
          "height": 294,
          "unit": "pixel"
        }
      ],
      "documentResults": [
        {
          "docType": "prebuilt:idDocument:driverLicense",
          "docTypeConfidence": 0.995,
          "pageRange": [
            1,
            1
          ],
          "fields": {
            "Address": {
              "type": "string",
              "valueString": "123 STREET ADDRESS YOUR CITY WA 99999-1234",
              "text": "123 STREET ADDRESS YOUR CITY WA 99999-1234",
              "boundingBox": [
                158,
                151,
                326,
                151,
                326,
                177,
                158,
                177
              ],
              "page": 1,
              "confidence": 0.965
            },
            "CountryRegion": {
              "type": "countryRegion",
              "valueCountryRegion": "USA",
              "confidence": 0.99
            },
            "DateOfBirth": {
              "type": "date",
              "valueDate": "1958-01-06",
              "text": "01/06/1958",
              "boundingBox": [
                187,
                133,
                272,
                132,
                272,
                148,
                187,
                149
              ],
              "page": 1,
              "confidence": 0.99
            },
            "DateOfExpiration": {
              "type": "date",
              "valueDate": "2020-08-12",
              "text": "08/12/2020",
              "boundingBox": [
                332,
                230,
                414,
                228,
                414,
                244,
                332,
                245
              ],
              "page": 1,
              "confidence": 0.99
            },
            "DocumentNumber": {
              "type": "string",
              "valueString": "LICWDLACD5DG",
              "text": "LIC#WDLABCD456DG",
              "boundingBox": [
                162,
                70,
                307,
                68,
                307,
                84,
                163,
                85
              ],
              "page": 1,
              "confidence": 0.99
            },
            "FirstName": {
              "type": "string",
              "valueString": "LIAM R.",
              "text": "LIAM R.",
              "boundingBox": [
                158,
                102,
                216,
                102,
                216,
                116,
                158,
                116
              ],
              "page": 1,
              "confidence": 0.985
            },
            "LastName": {
              "type": "string",
              "valueString": "TALBOT",
              "text": "TALBOT",
              "boundingBox": [
                160,
                86,
                213,
                85,
                213,
                99,
                160,
                100
              ],
              "page": 1,
              "confidence": 0.987
            },
            "Region": {
              "type": "string",
              "valueString": "Washington",
              "confidence": 0.99
            },
            "Sex": {
              "type": "string",
              "valueString": "M",
              "text": "M",
              "boundingBox": [
                226,
                190,
                232,
                190,
                233,
                201,
                226,
                201
              ],
              "page": 1,
              "confidence": 0.99
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

Azure BLOB コンテナー内のドキュメントを使用して Form Recognizer モデルをトレーニングするには、次の cURL コマンドを実行して、 **[Train Custom Model](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/TrainCustomModelAsync)** API を呼び出します。 コマンドを実行する前に、次の変更を行います。

1. `{endpoint}` を、Form Recognizer サブスクリプションで取得したエンドポイントで置き換えます。
1. `{subscription key}` を、前の手順からコピーしたサブスクリプション キーに置き換えます。
1. `{SAS URL}` を Azure Blob ストレージ コンテナーの共有アクセス署名 (SAS) URL に置き換えます。 [!INCLUDE [get SAS URL](../../includes/sas-instructions.md)]

   :::image type="content" source="../../media/quickstarts/get-sas-url.png" alt-text="SAS URL の取得":::

#### <a name="request"></a>Request

```bash
curl -i -X POST "https://{endpoint}/formrecognizer/v2.1/custom/models" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ 'source': '{SAS URL}'}"
```

#### <a name="location"></a>場所

**Location** ヘッダーで `201 (Success)` 応答を受信します。 このヘッダーの値に含まれる、新しくトレーニングされたモデルのモデル ID を使用して、操作の状態のクエリを実行し、結果を取得できます。

_https://{endpoint}/formrecognizer/v2.1/custom/models/ **{modelId}**_

次の例では、URL の一部として、`models/` の後の文字列がモデル ID になります。

```console
https://westus.api.cognitive.microsoft.com/formrecognizer/v2.1/custom/models/77d8ecad-b8c1-427e-ac20-a3fe4af503e9
```

### <a name="train-a-model-with-labels"></a>ラベルを使用してモデルをトレーニングする

ラベルを使用してトレーニングするには、トレーニング ドキュメントと共に、自分の Blob Storage コンテナーに特別なラベル情報ファイル (`\<filename\>.pdf.labels.json`) を用意する必要があります。 [Form Recognizer のサンプル ラベル付けツール](../../label-tool.md)では、これらのラベル ファイルの作成を支援する UI が提供されています。 それらを用意したら、 **[Train Custom Model](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/TrainCustomModelAsync)** API を呼び出すことができます。JSON 本文で `"useLabelFile"` パラメーターを `true` に設定してください。

コマンドを実行する前に、次の変更を行います。

1. `{endpoint}` を、Form Recognizer サブスクリプションで取得したエンドポイントで置き換えます。
1. `{subscription key}` を、前の手順からコピーしたサブスクリプション キーに置き換えます。
1. `{SAS URL}` を Azure Blob ストレージ コンテナーの共有アクセス署名 (SAS) URL に置き換えます。 [!INCLUDE [get SAS URL](../../includes/sas-instructions.md)]

   :::image type="content" source="../../media/quickstarts/get-sas-url.png" alt-text="SAS URL の取得":::

#### <a name="request"></a>Request

```bash
curl -i -X POST "https://{endpoint}/formrecognizer/v2.1/custom/models" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ 'source': '{SAS URL}', 'useLabelFile':true}"
```

#### <a name="location"></a>場所

**Location** ヘッダーで `201 (Success)` 応答を受信します。 このヘッダーの値に含まれる、新しくトレーニングされたモデルのモデル ID を使用して、操作の状態のクエリを実行し、結果を取得できます。

_https://{endpoint}/formrecognizer/v2.1/custom/models/ **{modelId}**_

次の例では、URL の一部として、`models/` の後の文字列がモデル ID になります。

```console
https://westus.api.cognitive.microsoft.com/formrecognizer/v2.1/custom/models/4da0bf8e-5325-467c-93bb-9ff13d5f72a2
```

### <a name="get-training-results"></a>トレーニング結果を取得する

トレーニング操作の開始後、 **[Get Custom Model](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/GetCustomModel)** を使用して、トレーニングの状態を確認します。 この API 要求にモデル ID を渡して、トレーニングの状態を確認します。

1. `{endpoint}` を、Form Recognizer サブスクリプション キーで取得したエンドポイントで置き換えます。
1. `{subscription key}` は、実際のサブスクリプション キーで置き換えてください
1. `{model ID}` を、前の手順で受信したモデル ID で置き換えます

#### <a name="request"></a>Request

```bash
curl -X GET "https://{endpoint}/formrecognizer/v2.1/custom/models/{modelId}" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

## <a name="analyze-forms-with-a-custom-model"></a>カスタム モデルを使用してフォームを分析する

次に、新しくトレーニングしたモデルを使用してドキュメントを分析し、そこからフィールドおよびテーブルを抽出します。 次の cURL コマンドを実行して、 **[Analyze Form](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeWithCustomForm)** API を呼び出します。 コマンドを実行する前に、次の変更を行います。

1. `{endpoint}` を、Form Recognizer サブスクリプション キーから取得したエンドポイントで置き換えます。 これは、Form Recognizer リソースの **[概要]** タブにあります。
1. `{model ID}` を、前のセクションで受信したモデル ID で置き換えます。
1. `{SAS URL}` を、Azure Storage にある実際のファイルの SAS URL に置き換えます。 「トレーニング」セクションの手順に従いますが、取得するのは、BLOB コンテナー全体の SAS URL ではなく、分析対象となる特定のファイルの SAS URL です。
1. `{subscription key}` は、実際のサブスクリプション キーで置き換えてください。

#### <a name="request"></a>Request

```bash
curl -v "https://{endpoint}/formrecognizer/v2.1/custom/models/{modelId}/analyze?includeTextDetails=true" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" -d "{ 'source': '{SAS URL}' } "
```

**Operation-Location** ヘッダーを含む `202 (Success)` 応答を受信します。 このヘッダーの値には、分析操作の結果を追跡するために使用する結果 ID が含まれます。

_https://<span></span>cognitiveservice/formrecognizer/v2.1/custom/models/{modelId}/analyzeResults/ **{resultId}**_

次の例では、URL の一部として、`analyzeResults/` の後の文字列が結果 ID になります。

```console
https://cognitiveservice/formrecognizer/v2/layout/analyzeResults/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

次の手順で使用できるように、この結果 ID を保存します。

### <a name="get-the-analyze-results"></a>分析結果を取得する

分析操作の結果を照会するには、Get **[Analyze Form Result](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/GetAnalyzeFormResult)** API を呼び出します。

1. `{endpoint}` を、Form Recognizer サブスクリプション キーから取得したエンドポイントで置き換えます。 これは、Form Recognizer リソースの **[概要]** タブにあります。
1. `{result ID}` を、前のセクションで受信した ID で置き換えます。
1. `{subscription key}` は、実際のサブスクリプション キーで置き換えてください。

#### <a name="request"></a>Request

```bash
curl -X GET "https://{endpoint}/formrecognizer/v2.1/custom/models/{modelId}/analyzeResults/{resultId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

次の形式の JSON 本文を含む `200 (Success)` 応答が送られてきます。 出力は、簡素化するために一部省略されています。 下部の近くにある `"status"` フィールドにご注意ください。 分析操作が完了すると、ここに `"succeeded"` 値が表示されます。 分析操作が完了していない場合は、コマンドを再実行して、サービスに対して再度クエリを実行する必要があります。 呼び出しの間隔は 1 秒以上あけることをお勧めします。

ラベル付けを使用せずにトレーニングしたカスタム モデルでは、キーと値のペアの関連付けとテーブルが JSON 出力の `"pageResults"` ノードに存在します。 ラベル付けを使用してトレーニングしたカスタム モデルでは、キーと値のペアの関連付けが `"documentResults"` ノードに存在します。 *includeTextDetails* URL パラメーターを使用してプレーンテキスト抽出を指定した場合、`"readResults"` ノードには、ドキュメント内のすべてのテキストの内容と位置が表示されます。

このサンプル JSON 出力は、簡素化するために一部省略されています。 [GitHub で完全なサンプル出力](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/analyze-result-invoice-6.pdf.json)を参照してください。

#### <a name="response-body"></a>応答本文

```JSON
{
  "status": "succeeded",
  "createdDateTime": "2020-08-21T01:13:28Z",
  "lastUpdatedDateTime": "2020-08-21T01:13:42Z",
  "analyzeResult": {
    "version": "2.1.0",
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
              5.0444,
              0.3613,
              8.0917,
              0.3613,
              8.0917,
              0.6718,
              5.0444,
              0.6718
            ],
            "words": [
              {
                "text": "Project",
                "boundingBox": [
                  5.0444,
                  0.3587,
                  6.2264,
                  0.3587,
                  6.2264,
                  0.708,
                  5.0444,
                  0.708
                ]
              },
              {
                "text": "Statement",
                "boundingBox": [
                  6.3361,
                  0.3635,
                  8.0917,
                  0.3635,
                  8.0917,
                  0.6396,
                  6.3361,
                  0.6396
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
                6.9833,
                1.0615,
                7.3333,
                1.0615,
                7.3333,
                1.1649,
                6.9833,
                1.1649
              ],
              "elements": [
                "#/readResults/0/lines/2/words/0"
              ]
            },
            "value": {
              "text": "9/10/2020",
              "boundingBox": [
                7.3833,
                1.0802,
                7.925,
                1.0802,
                7.925,
                1.174,
                7.3833,
                1.174
              ],
              "elements": [
                "#/readResults/0/lines/3/words/0"
              ]
            },
            "confidence": 1
          },
          ...
        ],
        "tables": [
          {
            "rows": 5,
            "columns": 5,
            "cells": [
              {
                "text": "Training Date",
                "rowIndex": 0,
                "columnIndex": 0,
                "boundingBox": [
                  0.6944,
                  4.2779,
                  1.5625,
                  4.2779,
                  1.5625,
                  4.4005,
                  0.6944,
                  4.4005
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

[!INCLUDE [improve results](../../includes/improve-results-unlabeled.md)]

## <a name="manage-custom-models"></a>カスタム モデルを管理する

### <a name="get-a-list-of-custom-models"></a>カスタム モデルのリストを取得する

次のコマンドでは、 **[List Custom Models](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/GetCustomModels)** API を使用して、サブスクリプションに属しているすべてのカスタム モデルのリストを返します。

1. `{endpoint}` を、Form Recognizer サブスクリプションで取得したエンドポイントで置き換えます。
1. `{subscription key}` を、前の手順からコピーしたサブスクリプション キーに置き換えます。

#### <a name="request"></a>Request

```bash
curl -v -X GET "https://{endpoint}/formrecognizer/v2.1/custom/models?op=full"
-H "Ocp-Apim-Subscription-Key: {subscription key}"
```

#### <a name="response-body"></a>応答本文

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

特定のカスタム モデルに関する詳細情報を取得するために、次のコマンドでは、 **[Get Custom Model](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/GetCustomModel)** API を使用します。

1. `{endpoint}` を、Form Recognizer サブスクリプションで取得したエンドポイントで置き換えます。
1. `{subscription key}` を、前の手順からコピーしたサブスクリプション キーに置き換えます。
1. `{modelId}` を、検索するカスタム モデルの ID で置き換えます。

### <a name="request"></a>Request

```bash
curl -v -X GET "https://{endpoint}/formrecognizer/v2.1/custom/models/{modelId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

#### <a name="request-body"></a>要求本文

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

ID を参照して、アカウントからモデルを削除することもできます。 このコマンドは **[Delete Custom Model](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/DeleteCustomModel)** API を呼び出して、前のセクションで使用したモデルを削除します。

1. `{endpoint}` を、Form Recognizer サブスクリプションで取得したエンドポイントで置き換えます。
1. `{subscription key}` を、前の手順からコピーしたサブスクリプション キーに置き換えます。
1. `{modelId}` を、検索するカスタム モデルの ID で置き換えます。

### <a name="request"></a>Request

```bash
curl -v -X DELETE "https://{endpoint}/formrecognizer/v2.1/custom/models/{modelId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

モデルが削除対象としてマークされたことを示す `204` 成功応答が返されます。 モデル成果物は、48 時間以内に削除されます。

## <a name="next-steps"></a>次のステップ

このプロジェクトでは、Form Recognizer REST API を使用して、さまざまな方法でフォームを分析しました。 次に、Form Recognizer API の詳細を把握するためにリファレンス ドキュメントを探索します。

> [!div class="nextstepaction"]
> [REST API リファレンス ドキュメント](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeWithCustomForm)
