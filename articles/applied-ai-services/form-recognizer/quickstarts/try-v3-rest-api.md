---
title: 'クイックスタート: Form Recognizer REST API | プレビュー'
titleSuffix: Azure Applied AI Services
description: Form Recognizer REST API v3.0 を使用したフォームとドキュメントの処理、データ抽出、分析 (プレビュー)
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 10/07/2021
ms.author: lajanuar
ms.openlocfilehash: 3b2ff000a43618442da5670aac9dc1a094f0cbf2
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/19/2021
ms.locfileid: "130161171"
---
# <a name="get-started-form-recognizer-rest-api---preview"></a>概要: Form Recognizer REST API | プレビュー

>[!NOTE]
> Form Recognizer v3.0 は現在、パブリック プレビュー段階です。 一部の機能がサポートされなかったり、機能が制限されたりすることがあります。 

| [Form Recognizer REST API](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument) | [Azure REST API リファレンス](/rest/api/azure/) |

Azure Cognitive Services Form Recognizer は、機械学習を使用してドキュメントからフォーム フィールド、テキスト、テーブルを抽出して分析するクラウド サービスです。 お使いのワークフローとアプリケーションに Microsoft のクライアント ライブラリ SDK を統合して、Form Recognizer モデルを簡単に呼び出すことができます。

### <a name="form-recognizer-models"></a>Form Recognizer モデル

 REST API では、次のモデルと機能がサポートされています。

* 🆕一般的なドキュメント - テキスト、テーブル、構造、キーと値のペア、名前付きエンティティを分析および抽出します。|
* レイアウト - モデルをトレーニングすることなく、フォーム ドキュメント内のラジオ ボタンやチェック ボックスなどのテーブル、行、単語、選択マークを分析および抽出します。
* カスタム - 独自のフォームの種類でトレーニングしたモデルを使用して、カスタム フォームからフォーム フィールドや他のコンテンツを分析および抽出します。
* 請求書 - 事前トレーニング済みの請求書モデルを使用して、請求書から共通フィールドを分析および抽出します。
* レシート - 事前トレーニング済みのレシート モデルを使用して、レシートから共通フィールドを分析および抽出します。
* 身分証明書 - 事前トレーニング済みの身分証明書モデルを使用して、パスポートや運転免許証などの身分証明書から共通フィールドを分析および抽出します。
* 名刺 - 事前トレーニング済みの名刺モデルを使用して、名刺から共通フィールドを分析および抽出します。

このクイックスタートでは、次の機能を使用して、フォームとドキュメントからデータと値を分析および抽出します。

* [**一般的なドキュメント**](#try-it-general-document-model)

* [**Layout**](#try-it-layout-model)

* **[事前構築済みの請求書]** #try-it-prebuilt-invoice-model)

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/cognitive-services)

* インストールされた [cURL](https://curl.haxx.se/windows/)。

* [PowerShell バージョン 6.0 以降](/powershell/scripting/install/installing-powershell-core-on-windows)、または同様のコマンド ライン アプリケーション。

* Cognitive Services または Form Recognizer リソース。 Azure サブスクリプションを用意できたら、Azure portal で[単一サービス](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer)または[マルチサービス](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne)の Form Recognizer リソースを作成し、キーとエンドポイントを取得します。 Free 価格レベル (<ph id="ph1">`F0`</ph>) を使用してサービスを試用し、後から運用環境用の有料レベルにアップグレードすることができます。

> [!TIP]
> 1 つのエンドポイント/キーで複数の Cognitive Services にアクセスする予定の場合は、Cognitive Services リソースを作成します。 Form Recognizer アクセスのみの場合は、Form Recognizer リソースを作成します。 [Azure Active Directory 認証](/azure/active-directory/authentication/overview-authentication)を使用する場合は、単一サービス リソースが必要になることに注意してください。

* リソースがデプロイされたら、 **[リソースに移動]** をクリックします。 自分のアプリケーションを Form Recognizer API に接続するには、作成したリソースのキーとエンドポイントが必要になります。 このクイックスタートで後に示すコードに、自分のキーとエンドポイントを貼り付けます。

  :::image type="content" source="../media/containers/keys-and-endpoint.png" alt-text="スクリーンショット: Azure portal のキーとエンドポイントの場所。":::

## <a name="try-it-general-document-model"></a>**試してみる**: 一般的なドキュメント モデル

> [!div class="checklist"]
>
> * この例では、**URI にフォーム ドキュメント ファイル** が必要になります。 このクイックスタートでは、Microsoft の[サンプル フォーム ドキュメント](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf)を使用できます。 コマンドを実行する前に、次の変更を行います。

1. `{endpoint}` を、Form Recognizer サブスクリプションで取得したエンドポイントで置き換えます。
1. `{subscription key}` を、前の手順からコピーしたサブスクリプション キーに置き換えます。
1. `{your-document-url}` をサンプル フォーム ドキュメントの URL に置き換えます。

#### <a name="request"></a>Request

```bash
 curl -v -i POST "https://{endpoint}/formrecognizer/documentModels/prebuilt-document:analyze?api-version=2021-09-30-preview&api-version=2021-09-30-preview HTTP/1.1" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{'source': '{your-document-url}'}"
```

#### <a name="operation-location"></a>Operation-Location

**Operation-Location** ヘッダーを含む `202 (Success)` 応答を受信します。 このヘッダーの値に含まれる結果 ID を使用して、非同期操作の状態のクエリを実行し、結果を取得できます。

https://{host}/formrecognizer/documentModels/{modelId}/analyzeResults/ **{resultId}** ?api-version=2021-07-30-preview

### <a name="get-general-document-results"></a>一般的なドキュメントの結果を取得する

**[Analyze document](https://westus.api.cognitive.microsoft.com/formrecognizer/documentModels/prebuilt-document:analyze?api-version=2021-09-30-preview&stringIndexType=textElements)** API を呼び出した後に **[Get analyze result](https://westus.api.cognitive.microsoft.com/formrecognizer/documentModels/prebuilt-document/analyzeResults/{resultId}?api-version=2021-09-30-preview)** API を呼び出して、操作の状態と抽出されたデータを取得します。 コマンドを実行する前に、次の変更を行います。

1. `{endpoint}` を、Form Recognizer サブスクリプションで取得したエンドポイントで置き換えます。
1. `{subscription key}` を、前の手順からコピーしたサブスクリプション キーに置き換えます。
1. `{resultId}` を、前の手順の結果 ID に置き換えます。
<!-- markdownlint-disable MD024 -->

#### <a name="request"></a>Request

```bash
curl -v -X GET "https://{endpoint}/formrecognizer/documentModels/prebuilt-document/analyzeResults/{resultId}?api-version=2021-09-30-preview&api-version=2021-09-30-preview"
```

### <a name="examine-the-response"></a>結果の確認

JSON 出力で `200 (Success)` 応答を受信します。 最初のフィールド `"status"` は、操作の状態を示します。 操作が完了していない場合、`"status"` の値は `"running"` または `"notStarted"` になり、手動またはスクリプトでもう一度 API を呼び出す必要があります。 呼び出しの間隔は 1 秒以上あけることをお勧めします。

`"analyzeResults"` ノードには、認識されたすべてのテキストが格納されます。 テキストは、ページ、行、テーブル、キーと値のペア、およびエンティティで分類されます。

#### <a name="sample-response"></a>応答のサンプル

```json
{
    "status": "succeeded",
    "createdDateTime": "2021-09-28T16:52:51Z",
    "lastUpdatedDateTime": "2021-09-28T16:53:08Z",
    "analyzeResult": {
        "apiVersion": "2021-09-30-preview",
        "modelId": "prebuilt-document",
        "stringIndexType": "textElements",
        "content": "content extracted",
        "pages": [
            {
                "pageNumber": 1,
                "angle": 0,
                "width": 8.4722,
                "height": 11,
                "unit": "inch",
                "words": [
                    {
                        "content": "Case",
                        "boundingBox": [
                            1.3578,
                            0.2244,
                            1.7328,
                            0.2244,
                            1.7328,
                            0.3502,
                            1.3578,
                            0.3502
                        ],
                        "confidence": 1,
                        "span": {
                            "offset": 0,
                            "length": 4
                        }
                    }

                ],
                "lines": [
                    {
                        "content": "Case",
                        "boundingBox": [
                            1.3578,
                            0.2244,
                            3.2879,
                            0.2244,
                            3.2879,
                            0.3502,
                            1.3578,
                            0.3502
                        ],
                        "spans": [
                            {
                                "offset": 0,
                                "length": 22
                            }
                        ]
                    }
                ]
            }
        ],
        "tables": [
            {
                "rowCount": 8,
                "columnCount": 3,
                "cells": [
                    {
                        "kind": "columnHeader",
                        "rowIndex": 0,
                        "columnIndex": 0,
                        "rowSpan": 1,
                        "columnSpan": 1,
                        "content": "Applicant's Name:",
                        "boundingRegions": [
                            {
                                "pageNumber": 1,
                                "boundingBox": [
                                    1.9198,
                                    4.277,
                                    3.3621,
                                    4.2715,
                                    3.3621,
                                    4.5034,
                                    1.9198,
                                    4.5089
                                ]
                            }
                        ],
                        "spans": [
                            {
                                "offset": 578,
                                "length": 17
                            }
                        ]
                    }
                ],
                "spans": [
                    {
                        "offset": 578,
                        "length": 300
                    },
                    {
                        "offset": 1358,
                        "length": 10
                    }
                ]
            }
        ],
        "keyValuePairs": [
            {
                "key": {
                    "content": "Case",
                    "boundingRegions": [
                        {
                            "pageNumber": 1,
                            "boundingBox": [
                                1.3578,
                                0.2244,
                                1.7328,
                                0.2244,
                                1.7328,
                                0.3502,
                                1.3578,
                                0.3502
                            ]
                        }
                    ],
                    "spans": [
                        {
                            "offset": 0,
                            "length": 4
                        }
                    ]
                },
                "value": {
                    "content": "A Case",
                    "boundingRegions": [
                        {
                            "pageNumber": 1,
                            "boundingBox": [
                                1.8026,
                                0.2276,
                                3.2879,
                                0.2276,
                                3.2879,
                                0.3502,
                                1.8026,
                                0.3502
                            ]
                        }
                    ],
                    "spans": [
                        {
                            "offset": 5,
                            "length": 17
                        }
                    ]
                },
                "confidence": 0.867
            }
        ],
        "entities": [
            {
                "category": "Person",
                "content": "Jim Smith",
                "boundingRegions": [
                    {
                        "pageNumber": 1,
                        "boundingBox": [
                            3.4672,
                            4.3255,
                            5.7118,
                            4.3255,
                            5.7118,
                            4.4783,
                            3.4672,
                            4.4783
                        ]
                    }
                ],
                "confidence": 0.93,
                "spans": [
                    {
                        "offset": 596,
                        "length": 21
                    }
                ]
            }
        ],
        "styles": [
            {
                "isHandwritten": true,
                "confidence": 0.95,
                "spans": [
                    {
                        "offset": 565,
                        "length": 12
                    },
                    {
                        "offset": 3493,
                        "length": 1
                    }
                ]
            }
        ]
    }
}

```

## <a name="try-it-layout-model"></a>**試してみる**: レイアウト モデル

> [!div class="checklist"]
>
> * この例では、**URI にフォーム ドキュメント ファイル** が必要になります。 このクイックスタートでは、Microsoft の[サンプル フォーム ドキュメント](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf)を使用できます。

 コマンドを実行する前に、次の変更を行います。

1. `{endpoint}` を、Form Recognizer サブスクリプションで取得したエンドポイントで置き換えます。
1. `{subscription key}` を、前の手順からコピーしたサブスクリプション キーに置き換えます。
1. `"{your-document-url}` を、サンプル URL のいずれかに置き換えます。

#### <a name="request"></a>Request

```bash
bash
 curl -v -i POST "https://{endpoint}/formrecognizer/documentModels/prebuilt-layout:analyze?api-version=2021-09-30-preview&api-version=2021-09-30-preview HTTP/1.1" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{'source': '{your-document-url}'}"
```

#### <a name="operation-location"></a>Operation-Location

**Operation-Location** ヘッダーを含む `202 (Success)` 応答を受信します。 このヘッダーの値に含まれる結果 ID を使用して、非同期操作の状態のクエリを実行し、結果を取得できます。

`https://{host}/formrecognizer/documentModels/{modelId}/analyzeResults/**{resultId}**?api-version=2021-07-30-preview`

### <a name="get-layout-results"></a>レイアウトの結果を取得する

**[Analyze document](https://westus.api.cognitive.microsoft.com/formrecognizer/documentModels/prebuilt-layout:analyze?api-version=2021-09-30-preview&stringIndexType=textElements)** API を呼び出した後に **[Get analyze result](https://westus.api.cognitive.microsoft.com/formrecognizer/documentModels/prebuilt-layout/analyzeResults/{resultId}?api-version=2021-09-30-preview)** API を呼び出して、操作の状態と抽出されたデータを取得します。 コマンドを実行する前に、次の変更を行います。

1. `{endpoint}` を、Form Recognizer サブスクリプションで取得したエンドポイントで置き換えます。
1. `{subscription key}` を、前の手順からコピーしたサブスクリプション キーに置き換えます。
1. `{resultId}` を、前の手順の結果 ID に置き換えます。
<!-- markdownlint-disable MD024 -->

#### <a name="request"></a>Request

```bash
curl -v -X GET "https://{endpoint}/formrecognizer/documentModels/prebuilt-layout/analyzeResults/{resultId}?api-version=2021-09-30-preview&api-version=2021-09-30-preview"
```

### <a name="examine-the-response"></a>結果の確認

JSON 出力で `200 (Success)` 応答を受信します。 最初のフィールド `"status"` は、操作の状態を示します。 操作が完了していない場合、`"status"` の値は `"running"` または `"notStarted"` になり、手動またはスクリプトでもう一度 API を呼び出す必要があります。 呼び出しの間隔は 1 秒以上あけることをお勧めします。

## <a name="try-it-prebuilt-invoice-model"></a>**試してみる**: 事前構築済みの請求書モデル

> [!div class="checklist"]
>
> * この例では、**URI に指定された請求書ドキュメント ファイル** が必要になります。 このクイックスタートでは、Microsoft の[サンプル請求書ドキュメント](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-invoice.pdf)を使用できます。

### <a name="choose-the-invoice-prebuilt-model-id"></a>請求書の事前構築済みモデル ID を選択する

請求書に限らず、複数の事前構築済みモデルから選択できます。各モデルには、独自のサポートされているフィールドのセットが含まれます。 分析操作に使用するモデルは、分析するドキュメントの種類によって異なります。 Form Recognizer サービスで現在サポートされている事前構築済みモデルのモデル ID を次に示します。

* **prebuilt-invoice**: 請求書からテキスト、選択マーク、テーブル、キーと値のペア、キー情報を抽出します。
* **prebuilt-businessCard**: 名刺からテキストとキー情報を抽出します。
* **prebuilt-idDocument**: 運転免許証と国際パスポートからテキストとキー情報を抽出します。
* **prebuilt-receipt**: レシートからテキストとキー情報を抽出します。

コマンドを実行する前に、次の変更を行います。

1. `{endpoint}` を、Form Recognizer サブスクリプションで取得したエンドポイントで置き換えます。
1. `{subscription key}` を、前の手順からコピーしたサブスクリプション キーに置き換えます。
1. `\"{your-document-url}` を、サンプル URL のいずれかに置き換えます。

#### <a name="request"></a>Request

```bash
bash
 curl -v -i POST "https://{endpoint}/formrecognizer/documentModels/prebuilt-invoice:analyze?api-version=2021-09-30-preview&api-version=2021-09-30-preview HTTP/1.1" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{'source': '{your-document-url}'}"
```

#### <a name="operation-location"></a>Operation-Location

**Operation-Location** ヘッダーを含む `202 (Success)` 応答を受信します。 このヘッダーの値に含まれる結果 ID を使用して、非同期操作の状態のクエリを実行し、結果を取得できます。

https:\//{host}/formrecognizer/documentModels/{modelId}/analyzeResults/ **{resultId}** ?api-version=2021-07-30-preview

### <a name="get-invoice-results"></a>請求書の結果を取得する

**[Analyze document](https://westus.api.cognitive.microsoft.com/formrecognizer/documentModels/prebuilt-invoice:analyze?api-version=2021-09-30-preview&stringIndexType=textElements)** API を呼び出した後に **[Get analyze result](https://westus.api.cognitive.microsoft.com/formrecognizer/documentModels/prebuilt-invoice/analyzeResults/{resultId}?api-version=2021-09-30-preview)** API を呼び出して、操作の状態と抽出されたデータを取得します。 コマンドを実行する前に、次の変更を行います。

1. `{endpoint}` を、Form Recognizer サブスクリプションで取得したエンドポイントで置き換えます。
1. `{subscription key}` を、前の手順からコピーしたサブスクリプション キーに置き換えます。
1. `{resultId}` を、前の手順の結果 ID に置き換えます。
<!-- markdownlint-disable MD024 -->

#### <a name="request"></a>Request

```bash
curl -v -X GET "https://{endpoint}/formrecognizer/documentModels/prebuilt-invoice/analyzeResults/{resultId}?api-version=2021-09-30-preview&api-version=2021-09-30-preview"
```

### <a name="examine-the-response"></a>結果の確認

JSON 出力で `200 (Success)` 応答を受信します。 最初のフィールド `"status"` は、操作の状態を示します。 操作が完了していない場合、`"status"` の値は `"running"` または `"notStarted"` になり、手動またはスクリプトでもう一度 API を呼び出す必要があります。 呼び出しの間隔は 1 秒以上あけることをお勧めします。

### <a name="improve-results"></a>結果を改善する

[!INCLUDE [improve results](../includes/improve-results-unlabeled.md)]

## <a name="manage-custom-models"></a>カスタム モデルを管理する

### <a name="get-a-list-of-models"></a>モデルの一覧を取得する

プレビュー v3.0 の   [List models](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/GetModels) 要求は、カスタム モデルに加えて、事前構築済みモデルのページ単位の一覧を返します。 状態が成功のモデルのみが含まれます。 進行中または失敗したモデルは、[List Operations](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/GetOperations) 要求を使用して列挙できます。 nextLink プロパティを使用して、モデルの次のページ (ある場合) にアクセスします。 サポートされているドキュメントとそのフィールドの一覧など、返される各モデルに関する詳細情報を取得するには、 [Get Model](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/GetOperations) 要求でモデル ID を渡します。 

```bash
curl -v -X GET "https://{endpoint}/formrecognizer/documentModels?api-version=2021-07-30-preview"
```

### <a name="get-a-specific-model"></a>特定のモデルを取得する

プレビュー v3.0 の [Get model](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/GetModel) は、状態が成功の特定モデルに関する情報を取得します。 失敗および進行中のモデルの場合は、[Get Operation](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/GetOperation) を使用して、モデル作成操作の状態と生成されるエラーを追跡します。

```bash
curl -v -X GET "https://{endpoint}/formrecognizer/documentModels/{modelId}?api-version=2021-07-30-preview" 
```

### <a name="delete-a-model"></a>モデルを削除する

プレビュー v3.0 の [Delete model](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/DeleteModel) 要求ではカスタム モデルが削除され、今後の操作でモデル ID にアクセスできなくなります。  同じモデル ID を使用して、競合することなく新しいモデルを作成できます。

```bash
curl -v -X DELETE "https://{endpoint}/formrecognizer/documentModels/{modelId}?api-version=2021-07-30-preview"
```

## <a name="next-steps"></a>次の手順

このクイックスタートでは、Form Recognizer REST API プレビュー (v3.0) を使用して、さまざまな方法でフォームを分析しました。 次に、Form Recognizer API の詳細を把握するためにリファレンス ドキュメントを探索します。

> [!div class="nextstepaction"]
> [REST API プレビュー (v3.0) リファレンス ドキュメント](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument)

