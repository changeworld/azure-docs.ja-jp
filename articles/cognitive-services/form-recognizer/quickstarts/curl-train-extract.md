---
title: クイック スタート:cURL を使用してモデルをトレーニングし、フォーム データを抽出する - Form Recognizer
titleSuffix: Azure Cognitive Services
description: このクイックスタートでは、cURL で Form Recognizer REST API を使用してモデルをトレーニングし、フォームからデータを抽出します。
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: form-recognizer
ms.topic: quickstart
ms.date: 04/15/2019
ms.author: pafarley
ms.openlocfilehash: 1afe9239dcc3f5a24d2e950ec7b563bf53d1f04c
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/06/2019
ms.locfileid: "65143229"
---
# <a name="quickstart-train-a-form-recognizer-model-and-extract-form-data-using-rest-api-with-curl"></a>クイック スタート:cURL で REST API を使用して Form Recognizer モデルをトレーニングし、フォーム データを抽出する

このクイックスタートでは、cURL で Form Recognizer の REST API を使用してトレーニングし、フォームをスコア付けしてキーと値のペアおよびテーブルを抽出します。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="prerequisites"></a>前提条件

* アクセスが制限された Form Recognizer プレビューへのアクセスを取得する必要があります。 プレビューへのアクセスを取得するには、[Cognitive Services Form Recognizer アクセス要求](https://aka.ms/FormRecognizerRequestAccess)フォームに記入して送信します。 
* [cURL](https://curl.haxx.se/windows/) が必要です。
* Form Recognizer のサブスクリプション キーが必要です。 [Cognitive Services アカウントを作成する](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)手順に従って Form Recognizer にサブスクライブし、キーを取得します。
* 最小セットとして、同じ種類の 5 つのフォームが必要です。 このクイックスタートでは、[サンプル データセット](https://go.microsoft.com/fwlink/?linkid=2090451)を使用できます。

## <a name="train-a-form-recognizer-model"></a>Form Recognizer モデルをトレーニングする

まず、トレーニング データのセットが必要です。 Azure Blob のデータまたはローカルのトレーニング データを使用できます。 主な入力データと同じ種類/構造のサンプル フォーム (PDF ドキュメントやイメージ) が少なくとも 5 つ必要です。 あるいは、フォームのファイル名に "empty" という単語を含む、単一の空のフォームを使用することもできます。

Azure Blob コンテナー内のドキュメントを使用して Form Recognizer モデルをトレーニングするには、次の cURL コマンドを実行して、**Train** API を呼び出します。 コマンドを実行する前に、次の変更を加えます。

* `<Endpoint>` を、Form Recognizer サブスクリプション キーから取得したエンドポイントで置き換えます。 これは、Form Recognizer リソースの概要タブにあります。
* `<SAS URL>` を、トレーニング データのある Azure BLOB Storage コンテナー共有アクセス署名 (SAS) URL で置き換えます。  
* `<subscription key>` は、実際のサブスクリプション キーで置き換えてください。

```bash
curl -X POST "https://<Endpoint>/formrecognizer/v1.0-preview/custom/train" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" --data-ascii "{ \"source\": \""<SAS URL>"\"}"
```

次の JSON 出力で `200 (Success)` 応答を受信します。

```json
{
  "modelId": "59e2185e-ab80-4640-aebc-f3653442617b",
  "trainingDocuments": [
    {
      "documentName": "Invoice_1.pdf",
      "pages": 1,
      "errors": [],
      "status": "success"
    },
    {
      "documentName": "Invoice_2.pdf",
      "pages": 1,
      "errors": [],
      "status": "success"
    },
    {
      "documentName": "Invoice_3.pdf",
      "pages": 1,
      "errors": [],
      "status": "success"
    },
    {
      "documentName": "Invoice_4.pdf",
      "pages": 1,
      "errors": [],
      "status": "success"
    },
    {
      "documentName": "Invoice_5.pdf",
      "pages": 1,
      "errors": [],
      "status": "success"
    }
  ],
  "errors": []
}
```

以降のステップで必要になるため、`"modelId"` の値を書き留めておきます。
  
## <a name="extract-key-value-pairs-and-tables-from-forms"></a>フォームからキーと値のペアとテーブルを抽出する

次に、ドキュメントを分析して、そこからキーと値のペアとテーブルを抽出します。 次の cURL コマンドを実行して **Model - Analyze** API を呼び出します。 コマンドを実行する前に、次の変更を加えます。

* `<Endpoint>` を、Form Recognizer サブスクリプション キーから取得したエンドポイントで置き換えます。 これは、Form Recognizer リソースの **[概要]** タブにあります。
* `<modelID>` を、前のモデル トレーニングのステップで受信したモデル ID で置き換えます。
* `<path to your form>` を、フォームへのファイル パスで置き換えます。
* `<subscription key>` は、実際のサブスクリプション キーで置き換えてください。
* `<file type>` をファイルの種類で置き換えます。サポートされる種類は、pdf、image/jpeg、image/png です。

```bash
curl -X POST "https://<Endpoint>/formrecognizer/v1.0-preview/custom/models/<modelID>/analyze" -H "Content-Type: multipart/form-data" -F "form=@\"<path to your form>\";type=application/<file type>" -H "Ocp-Apim-Subscription-Key: <subscription key>"
```

### <a name="examine-the-response"></a>結果の確認

正常な応答が JSON で返され、フォームから抽出されたキーと値のペアとテーブルが示されます。

```bash
{
  "status": "success",
  "pages": [
    {
      "number": 1,
      "height": 792,
      "width": 612,
      "clusterId": 0,
      "keyValuePairs": [
        {
          "key": [
            {
              "text": "Address:",
              "boundingBox": [
                57.4,
                683.1,
                100.5,
                683.1,
                100.5,
                673.7,
                57.4,
                673.7
              ]
            }
          ],
          "value": [
            {
              "text": "1 Redmond way Suite",
              "boundingBox": [
                57.4,
                671.3,
                154.8,
                671.3,
                154.8,
                659.2,
                57.4,
                659.2
              ],
              "confidence": 0.86
            },
            {
              "text": "6000 Redmond, WA",
              "boundingBox": [
                57.4,
                657.1,
                146.9,
                657.1,
                146.9,
                645.5,
                57.4,
                645.5
              ],
              "confidence": 0.86
            },
            {
              "text": "99243",
              "boundingBox": [
                57.4,
                643.4,
                85,
                643.4,
                85,
                632.3,
                57.4,
                632.3
              ],
              "confidence": 0.86
            }
          ]
        },
        {
          "key": [
            {
              "text": "Invoice For:",
              "boundingBox": [
                316.1,
                683.1,
                368.2,
                683.1,
                368.2,
                673.7,
                316.1,
                673.7
              ]
            }
          ],
          "value": [
            {
              "text": "Microsoft",
              "boundingBox": [
                374,
                687.9,
                418.8,
                687.9,
                418.8,
                673.7,
                374,
                673.7
              ],
              "confidence": 1
            },
            {
              "text": "1020 Enterprise Way",
              "boundingBox": [
                373.9,
                673.5,
                471.3,
                673.5,
                471.3,
                659.2,
                373.9,
                659.2
              ],
              "confidence": 1
            },
            {
              "text": "Sunnayvale, CA 87659",
              "boundingBox": [
                373.8,
                659,
                479.4,
                659,
                479.4,
                645.5,
                373.8,
                645.5
              ],
              "confidence": 1
            }
          ]
        }
      ],
      "tables": [
        {
          "id": "table_0",
          "columns": [
            {
              "header": [
                {
                  "text": "Invoice Number",
                  "boundingBox": [
                    38.5,
                    585.2,
                    113.4,
                    585.2,
                    113.4,
                    575.8,
                    38.5,
                    575.8
                  ]
                }
              ],
              "entries": [
                [
                  {
                    "text": "34278587",
                    "boundingBox": [
                      38.5,
                      547.3,
                      82.8,
                      547.3,
                      82.8,
                      537,
                      38.5,
                      537
                    ],
                    "confidence": 1
                  }
                ]
              ]
            },
            {
              "header": [
                {
                  "text": "Invoice Date",
                  "boundingBox": [
                    139.7,
                    585.2,
                    198.5,
                    585.2,
                    198.5,
                    575.8,
                    139.7,
                    575.8
                  ]
                }
              ],
              "entries": [
                [
                  {
                    "text": "6/18/2017",
                    "boundingBox": [
                      139.7,
                      546.8,
                      184,
                      546.8,
                      184,
                      537,
                      139.7,
                      537
                    ],
                    "confidence": 1
                  }
                ]
              ]
            },
            {
              "header": [
                {
                  "text": "Invoice Due Date",
                  "boundingBox": [
                    240.5,
                    585.2,
                    321,
                    585.2,
                    321,
                    575.8,
                    240.5,
                    575.8
                  ]
                }
              ],
              "entries": [
                [
                  {
                    "text": "6/24/2017",
                    "boundingBox": [
                      240.5,
                      546.8,
                      284.8,
                      546.8,
                      284.8,
                      537,
                      240.5,
                      537
                    ],
                    "confidence": 1
                  }
                ]
              ]
            },
            {
              "header": [
                {
                  "text": "Charges",
                  "boundingBox": [
                    341.3,
                    585.2,
                    381.2,
                    585.2,
                    381.2,
                    575.8,
                    341.3,
                    575.8
                  ]
                }
              ],
              "entries": [
                [
                  {
                    "text": "$56,651.49",
                    "boundingBox": [
                      387.6,
                      546.4,
                      437.5,
                      546.4,
                      437.5,
                      537,
                      387.6,
                      537
                    ],
                    "confidence": 1
                  }
                ]
              ]
            },
            {
              "header": [
                {
                  "text": "VAT ID",
                  "boundingBox": [
                    442.1,
                    590,
                    474.8,
                    590,
                    474.8,
                    575.8,
                    442.1,
                    575.8
                  ]
                }
              ],
              "entries": [
                [
                  {
                    "text": "PT",
                    "boundingBox": [
                      447.7,
                      550.6,
                      460.4,
                      550.6,
                      460.4,
                      537,
                      447.7,
                      537
                    ],
                    "confidence": 1
                  }
                ]
              ]
            }
          ]
        }
      ]
    }
  ],
  "errors": []
}
```

## <a name="next-steps"></a>次の手順

このガイドでは、cURL で Form Recognizer REST API を使用してモデルをトレーニングし、サンプル ケースで実行しました。 次に、Form Recognizer API の詳細を把握するためにリファレンス ドキュメントを参照します。

> [!div class="nextstepaction"]
> [REST API リファレンス ドキュメント](https://aka.ms/form-recognizer/api)
