---
title: 名刺 - Form Recognizer
titleSuffix: Azure Applied AI Services
description: Form Recognizer API を使用した名刺分析に関連する概念 (使用法と制限) について説明します。
services: cognitive-services
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 04/30/2021
ms.author: lajanuar
ms.openlocfilehash: 92817318108370f1edf9ca2b38bf01226612b53a
ms.sourcegitcommit: 34feb2a5bdba1351d9fc375c46e62aa40bbd5a1f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/10/2021
ms.locfileid: "111890782"
---
# <a name="form-recognizer-prebuilt-business-cards-model"></a>Form Recognizer の事前構築済みの名刺モデル

Azure Form Recognizer では、事前構築済みの名刺モデルを使用して、名刺から連絡先情報を分析して抽出できます。 これは、強力な光学式文字認識 (OCR) 機能と名刺解釈モデルを組み合わせて、英語の名刺から重要な情報を抽出します。 抽出されるのは、個人の連絡先情報、会社名、役職などです。 あらかじめ構築された Business Card API は、Form Recognizer v2.1 で一般公開されています。

## <a name="customer-scenarios"></a>顧客シナリオ

Business Card API で抽出されたデータは、さまざまなタスクを実行するために使用できます。 このように連絡先情報を自動的に抽出すると、クライアントに対応するロールを持つユーザーの時間を節約できます。 次に、顧客が Business Card API を使用して行った作業の例をいくつかご紹介します。

* 名刺から連絡先情報を抽出し、連絡先の電話番号をすばやく作成します。
* CRM と統合して、名刺画像を使用して自動的に連絡先を作成します。
* 潜在顧客を追跡します。
* 既存の名刺画像から、連絡先情報を一括して抽出します。

また、Business Card API では、[AI Builder の名刺処理機能](/ai-builder/prebuilt-business-card)も強化されています。


## <a name="try-it-out"></a>試してみる

Form Recognizer レシート サービスを試すには、オンラインのサンプル UI ツールにアクセスしてください。

> [!div class="nextstepaction"]
> [事前構築済みモデルを試す](https://aka.ms/fott-2.1-ga)

## <a name="what-does-the-business-card-service-do"></a>名刺サービスにはどのような機能がありますか。

事前構築済みの Business Card API は、名刺からキー フィールドを抽出し、それを構成済みの JSON 応答で返します。

![FOTT + JSON 出力からの Contoso の明細画像](./media/business-card-example.jpg)

### <a name="fields-extracted"></a>抽出されるフィールド:

|名前| 種類 | 説明 | Text | 値 (標準化された出力) |
|:-----|:----|:----|:----|:----|
| ContactNames | オブジェクトの配列 | 名刺から抽出された連絡先の名前 | [{ "FirstName": "Chris", "LastName": "Smith" }] | |
| FirstName | string | 連絡先の名 | "Chris" | "Chris" |
| LastName | string | 連絡先の姓 |     "Smith" | "Smith" |
| CompanyNames | 文字列の配列 | 名刺から抽出された会社名 | ["CONTOSO"] | CONTOSO |
| Departments | 文字列の配列 | 連絡先の部署または組織 | ["Cloud & Al Department"] | Cloud & Al Department |
| JobTitles | 文字列の配列 | リストされている連絡先の役職 | ["Senior Researcher"] | Senior Researcher |
| メール | 文字列の配列 | 名刺から抽出された連絡先のメールアドレス | ["chris.smith@contoso.com"] | chris.smith@contoso.com |
| Websites | 文字列の配列 | 名刺から抽出された Web サイト | ["https://www.contoso.com"] | https://www.contoso.com |
| アドレス | 文字列の配列 | 名刺から抽出された住所 | ["4001 1st Ave NE Redmond, WA 98052"] | 4001 1st Ave NE Redmond, WA 98052 |
| MobilePhones | 電話番号の配列 | 名刺から抽出された携帯電話番号 | ["+1 (987) 123-4567"] | +19871234567 |
| Fax | 電話番号の配列 | 名刺から抽出された Fax 電話番号 | ["+1 (987) 312-6745"] | +19873126745 |
| WorkPhones | 電話番号の配列 | 名刺から抽出された勤務先電話番号 | ["+1 (987) 213-5674"] | +19872135674 | 
| OtherPhones     | 電話番号の配列 | 名刺から抽出されたその他の電話番号 | ["+1 (987) 213-5673"] | +19872135673 |


また、Business Card API では、名刺から認識されたすべてのテキストを返すこともできます。 この OCR 出力は JSON 応答に含まれています。

### <a name="input-requirements"></a>入力要件

[!INCLUDE [input requirements](./includes/input-requirements-receipts.md)]

## <a name="supported-locales"></a>サポート対象のロケール

**あらかじめ構築された Business Card v2.1** では、**en-us**、**en-au**、**en-ca**、**en-gb**、**en-in** のロケールをサポートしています。

## <a name="the-analyze-business-card-operation"></a>名刺の分析操作

[名刺の分析](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeBusinessCardAsync)では、名刺の画像または PDF を入力として受け取り、目的の値を抽出します。 この呼び出しにより、`Operation-Location` という応答ヘッダー フィールドが返されます。 `Operation-Location` 値は、次の手順で使用される結果 ID を含む URL です。

|応答ヘッダー| 結果の URL |
|:-----|:----|
|Operation-Location | `https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeBusinessCardAsync` |

## <a name="the-get-analyze-business-card-result-operation"></a>名刺の分析結果の取得操作

2 番目の手順では、[名刺の分析結果の取得](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/GetAnalyzeBusinessCardResult)操作を呼び出します。 この操作では、名刺の分析操作によって作成された結果 ID を入力として受け取ります。 これにより、次の設定可能な値を持つ **status** フィールドが含まれた JSON 応答が返されます。 **succeeded** の値が返されるまで、この操作を対話形式で呼び出します。 1 秒あたりの要求数 (RPS) を超えないようにするために、間隔は 3 - 5 秒あけてください。

|フィールド| 型 | 設定可能な値 |
|:-----|:----:|:----|
|status | string | notStarted: 分析操作は開始されていません。<br /><br />running: 分析操作が進行中です。<br /><br />failed: 分析操作は失敗しました。<br /><br />succeeded: 分析操作は成功しました。|

**status** フィールドの値が **succeeded** の場合、JSON 応答には名刺の解釈と、必要に応じてテキスト認識の結果 (要求した場合) が含まれます。 名刺の解釈の結果は名前付きフィールド値のディクショナリとして編成され、各値には、抽出されたテキスト、正規化された値、境界ボックス、信頼および対応する単語要素が含まれます。 テキスト認識の結果は行と単語の階層として編成され、テキスト、境界ボックス、信頼情報が含まれます。

![サンプルの名刺の出力](./media/business-card-results.png)

### <a name="sample-json-output"></a>サンプル JSON 出力

名刺の分析結果の取得操作への応答は、抽出されたすべての情報が含まれる名刺の構造化表現になります。  [サンプルの名刺ファイル](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/business-card-english.jpg)と、その構造化された出力である[サンプルの名刺出力](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/business-card-result.json)を参照してください。

成功した JSON 応答の例を次に示します (わかりやすくするために出力が短縮されています)。
* `"readResults"` ノードには、認識されたすべてのテキストが格納されます。 テキストは、まずページごとに整理され、そのうえで行ごと、さらに個々の単語ごとに整理されます。
* `"documentResults"` ノードには、モデルによって検出された名刺固有の値が格納されます。 名、姓、会社名など、大切な連絡先情報が存在する場所です。

```json
{
    "status": "succeeded",
    "createdDateTime": "2021-05-27T02:18:35Z",
    "lastUpdatedDateTime": "2021-05-27T02:18:37Z",
    "analyzeResult": {
        "version": "2.1.0",
        "readResults": [
            {
                "page": 1,
                "angle": 0.0255,
                "width": 2592,
                "height": 4608,
                "unit": "pixel",
                "lines": [
                    {
                        "text": "CONTOSO",
                        "boundingBox": [
                            533,
                            1570,
                            1334,
                            1570,
                            1333,
                            1721,
                            533,
                            1720
                        ],
                        "words": [
                            {
                                "text": "CONTOSO",
                                "boundingBox": [
                                    535,
                                    1571,
                                    1278,
                                    1571,
                                    1279,
                                    1722,
                                    534,
                                    1719
                                ],
                                "confidence": 0.994
                            }
                        ],
                        "appearance": {
                            "style": {
                                "name": "other",
                                "confidence": 0.878
                            }
                        }
                    },
                    ...
                ]
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
                    "Addresses": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "4001 1st Ave NE Redmond, WA 98052",
                                "text": "4001 1st Ave NE Redmond, WA 98052",
                                "boundingBox": [
                                    400,
                                    2789,
                                    1514,
                                    2789,
                                    1514,
                                    2857,
                                    400,
                                    2857
                                ],
                                "page": 1,
                                "confidence": 0.986,
                                "elements": [
                                    "#/readResults/0/lines/9/words/0",
                                    "#/readResults/0/lines/9/words/1",
                                    "#/readResults/0/lines/9/words/2",
                                    "#/readResults/0/lines/9/words/3",
                                    "#/readResults/0/lines/9/words/4",
                                    "#/readResults/0/lines/9/words/5",
                                    "#/readResults/0/lines/9/words/6"
                                ]
                            }
                        ]
                    },
                    "CompanyNames": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "CONTOSO",
                                "text": "CONTOSO",
                                "boundingBox": [
                                    535,
                                    1571,
                                    1278,
                                    1571,
                                    1279,
                                    1722,
                                    534,
                                    1719
                                ],
                                "page": 1,
                                "confidence": 0.985,
                                "elements": [
                                    "#/readResults/0/lines/0/words/0"
                                ]
                            }
                        ]
                    },
                    "ContactNames": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "object",
                                "valueObject": {
                                    "FirstName": {
                                        "type": "string",
                                        "valueString": "Chris",
                                        "text": "Chris",
                                        "boundingBox": [
                                            1556,
                                            2018,
                                            1915,
                                            2021,
                                            1915,
                                            2156,
                                            1558,
                                            2154
                                        ],
                                        "page": 1,
                                        "elements": [
                                            "#/readResults/0/lines/1/words/0"
                                        ]
                                    },
                                    "LastName": {
                                        "type": "string",
                                        "valueString": "Smith",
                                        "text": "Smith",
                                        "boundingBox": [
                                            1944,
                                            2021,
                                            2368,
                                            2016,
                                            2364,
                                            2156,
                                            1944,
                                            2156
                                        ],
                                        "page": 1,
                                        "elements": [
                                            "#/readResults/0/lines/1/words/1"
                                        ]
                                    }
                                },
                                "text": "Chris Smith",
                                "boundingBox": [
                                    1556.1,
                                    2010.3,
                                    2368,
                                    2016,
                                    2367,
                                    2159.6,
                                    1555.1,
                                    2154
                                ],
                                "page": 1,
                                "confidence": 0.99,
                                "elements": [
                                    "#/readResults/0/lines/1/words/0",
                                    "#/readResults/0/lines/1/words/1"
                                ]
                            }
                        ]
                    },
                    "Departments": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "Cloud & Al Department",
                                "text": "Cloud & Al Department",
                                "boundingBox": [
                                    1578,
                                    2288.8,
                                    2277,
                                    2295.1,
                                    2276.3,
                                    2367.8,
                                    1577.3,
                                    2361.5
                                ],
                                "page": 1,
                                "confidence": 0.989,
                                "elements": [
                                    "#/readResults/0/lines/3/words/0",
                                    "#/readResults/0/lines/3/words/1",
                                    "#/readResults/0/lines/3/words/2",
                                    "#/readResults/0/lines/3/words/3"
                                ]
                            }
                        ]
                    },
                    "Emails": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "chris.smith@contoso.com",
                                "text": "chris.smith@contoso.com",
                                "boundingBox": [
                                    1583,
                                    2381,
                                    2309,
                                    2382,
                                    2308,
                                    2445,
                                    1584,
                                    2447
                                ],
                                "page": 1,
                                "confidence": 0.99,
                                "elements": [
                                    "#/readResults/0/lines/4/words/0"
                                ]
                            }
                        ]
                    },
                    "Faxes": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "phoneNumber",
                                "valuePhoneNumber": "+19873126745",
                                "text": "+1 (987) 312-6745",
                                "boundingBox": [
                                    740,
                                    2703.8,
                                    1273,
                                    2702.1,
                                    1273.2,
                                    2774.1,
                                    740.2,
                                    2775.8
                                ],
                                "page": 1,
                                "confidence": 0.99,
                                "elements": [
                                    "#/readResults/0/lines/8/words/1",
                                    "#/readResults/0/lines/8/words/2",
                                    "#/readResults/0/lines/8/words/3"
                                ]
                            }
                        ]
                    },
                    "JobTitles": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "Senior Researcher",
                                "text": "Senior Researcher",
                                "boundingBox": [
                                    1578,
                                    2206,
                                    2117,
                                    2207.6,
                                    2116.8,
                                    2272.6,
                                    1577.8,
                                    2271
                                ],
                                "page": 1,
                                "confidence": 0.99,
                                "elements": [
                                    "#/readResults/0/lines/2/words/0",
                                    "#/readResults/0/lines/2/words/1"
                                ]
                            }
                        ]
                    },
                    "MobilePhones": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "phoneNumber",
                                "valuePhoneNumber": "+19871234567",
                                "text": "+1 (987) 123-4567",
                                "boundingBox": [
                                    744,
                                    2529,
                                    1281,
                                    2529,
                                    1281,
                                    2603,
                                    744,
                                    2603
                                ],
                                "page": 1,
                                "confidence": 0.99,
                                "elements": [
                                    "#/readResults/0/lines/5/words/1",
                                    "#/readResults/0/lines/5/words/2",
                                    "#/readResults/0/lines/5/words/3"
                                ]
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
                                    1576,
                                    2462,
                                    2383,
                                    2462,
                                    2380,
                                    2535,
                                    1576,
                                    2535
                                ],
                                "page": 1,
                                "confidence": 0.99,
                                "elements": [
                                    "#/readResults/0/lines/6/words/0"
                                ]
                            }
                        ]
                    },
                    "WorkPhones": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "phoneNumber",
                                "valuePhoneNumber": "+19872135674",
                                "text": "+1 (987) 213-5674",
                                "boundingBox": [
                                    736,
                                    2617.6,
                                    1267.1,
                                    2618.5,
                                    1267,
                                    2687.5,
                                    735.9,
                                    2686.6
                                ],
                                "page": 1,
                                "confidence": 0.984,
                                "elements": [
                                    "#/readResults/0/lines/7/words/1",
                                    "#/readResults/0/lines/7/words/2",
                                    "#/readResults/0/lines/7/words/3"
                                ]
                            }
                        ]
                    }
                }
            }
        ]
    }
}
```

## <a name="next-steps"></a>次の手順

* [Form Recognizer サンプル UI](https://fott-preview.azurewebsites.net/) で、独自の名詞とサンプルを試してみてください。
* 選択した開発言語で Form Recognizer を使用して名詞アプリの作成を始めるには、[Form Recognizer のクイックスタート](quickstarts/client-library.md)を完了します。
