---
title: Text Analytics API を呼び出す
titleSuffix: Azure Cognitive Services
description: この記事では、Azure Cognitive Services Text Analytics REST API と Postman を呼び出す方法について説明します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 11/19/2020
ms.author: aahi
ms.openlocfilehash: 2977946b2e1f37aa356ee075d2caac237170df0f
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/20/2020
ms.locfileid: "95993341"
---
# <a name="how-to-call-the-text-analytics-rest-api"></a>Text Analytics REST API を呼び出す方法

この記事では、Text Analytics REST API と [Postman](https://www.postman.com/downloads/) を使用して主要な概念を示します。 API により、サービスの機能を使用するための同期と非同期の複数のエンドポイントが提供されます。 

## <a name="using-the-api-asynchronously"></a>API の非同期的な使用

v3.1-preview.3 以降の Text Analytics API には、2 つの非同期エンドポイントがあります。 

* Text Analytics 用の `/analyze` エンドポイントを使用すると、API の 1 回の呼び出しで、複数のテキスト分析機能を使用して同じテキスト ドキュメントのセットを分析できます。 以前は、複数の機能を使用するには、操作ごとに個別に API を呼び出す必要がありました。 Text Analytics の複数の機能で、大きなドキュメントのセットを分析する必要があるときは、この機能を検討してください。

* Text Analytics for Health 用の `/health` エンドポイント。医療ドキュメントから関連する医療情報を抽出してラベル付けすることができます。  

非同期に使用できる機能を確認するには、次の表を参照してください。 `/analyze` エンドポイントから呼び出すことができるのは一部の機能だけであることに注意してください。 

| 機能 | Synchronous | 非同期 |
|--|--|--|
| 言語検出 | ✔ |  |
| センチメント分析 | ✔ |  |
| 意見マイニング | ✔ |  |
| キー フレーズの抽出 | ✔ | ✔* |
| 固有表現認識 (PII および PHI を含む) | ✔ | ✔* |
| Text Analytics for Health (コンテナー) | ✔ |  |
| Text Analytics for Health (API) |  | ✔  |

`*` - `/analyze` エンドポイントを通して非同期に呼び出されます。


[!INCLUDE [text-analytics-api-references](../includes/text-analytics-api-references.md)]

[!INCLUDE [v3 region availability](../includes/v3-region-availability.md)]

## <a name="prerequisites"></a>前提条件


> [!NOTE]
> `/analyze` または `/health` エンドポイントを使用する場合は、Standard (S) [価格レベル](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/)を使用する Text Analytics リソースが必要です。

1.  まだ持っていない場合は、最初に、[Azure portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) にアクセスして新しい Text Analytics リソースを作成します。 `/analyze` または `/health` エンドポイントを使用する場合は、Standard (S) 価格レベルを選択します。

2.  エンドポイントを使用するリージョンを選択します。

3.  Text Analytics リソースを作成し、ページの左側にある "キーとエンドポイントのブレード" に移動します。 後で API を呼び出すときに使用するキーをコピーします。 後で `Ocp-Apim-Subscription-Key` ヘッダーの値としてこの値を追加します。


<a name="json-schema"></a>

## <a name="api-request-format"></a>API 要求の形式

#### <a name="synchronous"></a>[Synchronous](#tab/synchronous)

API 要求の形式は、すべての同期操作について同じです。 ドキュメントは生の構造化されていないテキストとして JSON オブジェクトで送信されます。 XML はサポートされていません。 JSON スキーマは、以下で説明する要素で構成されています。

| 要素 | 有効な値 | 必須 | 使用方法 |
|---------|--------------|-----------|-------|
|`id` |データ型は文字列ですが、実際にはドキュメント ID は整数になる傾向があります。 | 必須 | 構造体に提供する ID が出力に使用されます。 言語コード、キー フレーズ、およびセンチメント スコアが、要求の各 ID に対して生成されます。|
|`text` | 最大 5,120 文字の、構造化されていない生のテキスト。 | 必須 | 言語の検出では、任意の言語でテキストを表現できます。 センチメント分析、キー フレーズ抽出、およびエンティティ識別の場合、テキストは[サポートされている言語](../language-support.md)である必要があります。 |
|`language` | [サポートされている言語](../language-support.md)の 2 文字の [ISO 639-1](https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes) コード | 場合により異なる | センチメント分析、キー フレーズ抽出、およびエンティティ リンク設定には必須。言語検出には省略可能。 除外してもエラーにはなりませんが、それなしでは分析力は低下します。 言語コードは提供した `text` に対応する必要があります。 |

同期 Text Analytics エンドポイントの API 要求の例を次に示します。 

```json
{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "Sample text to be sent to the text analytics api."
    }
  ]
}
```

#### <a name="analyze"></a>[解析](#tab/analyze)

> [!NOTE]
> Text Analytics クライアント ライブラリの最新のプレリリース版を使用すると、クライアント オブジェクトを使用して非同期分析操作を呼び出すことができます。 GitHub で例を見つけることができます。
* [C#](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics)
* [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/)
* [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics)

`/analyze` エンドポイントを使用すると、サポートされている Text Analytics 機能のうち、1 つの API 呼び出しで使用するものを選択できます。 現在、このエンドポイントでは次ものがサポートされています。

* ような作業を 
* 固有表現認識 (PII および PHI を含む)

| 要素 | 有効な値 | 必須 | 使用方法 |
|---------|--------------|-----------|-------|
|`displayName` | String | 省略可能 | ジョブに対する一意の識別子の表示名として使用されます。|
|`analysisInput` | 後の `documents` フィールドが含まれます | 必須 | 送信するドキュメントの情報が含まれます。 |
|`documents` | 後の `id` および `text` フィールドが含まれます | 必須 | 送信される各ドキュメントの情報と、ドキュメントの名前のテキストが含まれます。 |
|`id` | String | 必須 | 指定した ID は、出力を構造化するために使用されます。 |
|`text` | 最大 125,000 文字の、構造化されていない生のテキスト。 | 必須 | 英語である必要があります。現在サポートされている言語はこれだけです。 |
|`tasks` | 次の Text Analytics 機能が含まれます: `entityRecognitionTasks`、`keyPhraseExtractionTasks`、または `entityRecognitionPiiTasks`。 | 必須 | 使用する 1 つ以上の Text Analytics 機能。 `entityRecognitionPiiTasks` には、`pii` または `phi` に設定できる省略可能な `domain` パラメーターがあることに注意してください。 指定されていない場合、システムの既定値の `pii` になります。 |
|`parameters` | 後の `model-version` および `stringIndexType` フィールドが含まれます | 必須 | このフィールドは、上で選択した機能タスクに含まれています。 これらには、使用するモデル バージョンとインデックスの種類に関する情報が含まれます。 |
|`model-version` | String | 必須 | 呼び出しで使用するモデルのバージョンを指定します。  |
|`stringIndexType` | String | 必須 | プログラミング環境に合ったテキスト デコーダーを指定します。  サポートされている種類は、`textElement_v8` (既定)、`unicodeCodePoint`、`utf16CodeUnit` です。 詳細については、[テキストのオフセットに関する記事](../concepts/text-offsets.md#offsets-in-api-version-31-preview)を参照してください。  |
|`domain` | String | 省略可能 | `entityRecognitionPiiTasks` タスクへのパラメーターとしてのみ適用され、`pii` または `phi` に設定できます。 指定されていない場合は、既定で `pii` になります。  |

```json
{
    "displayName": "My Job",
    "analysisInput": {
        "documents": [
            {
                "id": "doc1",
                "text": "It's incredibly sunny outside! I'm so happy"
            },
            {
                "id": "doc2",
                "text": "Pike place market is my favorite Seattle attraction."
            }
        ]
    },
    "tasks": {
        "entityRecognitionTasks": [
            {
                "parameters": {
                    "model-version": "latest",
                    "stringIndexType": "TextElements_v8"
                }
            }
        ],
        "keyPhraseExtractionTasks": [{
            "parameters": {
                "model-version": "latest"
            }
        }],
        "entityRecognitionPiiTasks": [{
            "parameters": {
                "model-version": "latest"
            }
        }]
    }
}

```

#### <a name="text-analytics-for-health"></a>[Text Analytics for Health](#tab/health)

Text Analytics for Health でホストされた API への API 要求の形式は、コンテナーの場合と同じです。 ドキュメントは生の構造化されていないテキストとして JSON オブジェクトで送信されます。 XML はサポートされていません。 JSON スキーマは、以下で説明する要素で構成されています。  Text Analytics for Health のパブリック プレビューへのアクセスを要求するには、[Cognitive Services 要求フォーム](https://aka.ms/csgate)に記入して送信してください。 Text Analytics for Health の使用には課金されません。 

| 要素 | 有効な値 | 必須 | 使用方法 |
|---------|--------------|-----------|-------|
|`id` |データ型は文字列ですが、実際にはドキュメント ID は整数になる傾向があります。 | 必須 | 構造体に提供する ID が出力に使用されます。 |
|`text` | 最大 5,120 文字の、構造化されていない生のテキスト。 | 必須 | 現在は英語テキストのみがサポートされていることに注意してください。 |
|`language` | [サポートされている言語](../language-support.md)の 2 文字の [ISO 639-1](https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes) コード | 必須 | 現在は、`en` のみがサポートされています。 |

Text Analytics for Health エンドポイントの API 要求の例を次に示します。 

```json
example.json

{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "Subject was administered 100mg remdesivir intravenously over a period of 120 min"
    }
  ]
}
```

---

>[!TIP]
> Text Analytics API へのデータ送信に対するレートとサイズの制限については、[データとレートの制限](../concepts/data-limits.md)に関する記事を参照してください。


## <a name="set-up-a-request"></a>要求の設定 

Postman (または別の Web API テスト ツール) で、使用する機能のエンドポイントを追加します。 次の表で適切なエンドポイントの形式を確認し、`<your-text-analytics-resource>` をご自分のリソース エンドポイントに置き換えます。 例:

`https://my-resource.cognitiveservices.azure.com/text/analytics/v3.0/languages`

#### <a name="synchronous"></a>[Synchronous](#tab/synchronous)

| 機能 | 要求の種類 | リソースのエンドポイント |
|--|--|--|
| 言語検出 | POST | `<your-text-analytics-resource>/text/analytics/v3.0/languages` |
| センチメント分析 | POST | `<your-text-analytics-resource>/text/analytics/v3.0/sentiment` |
| オピニオン マイニング | POST | `<your-text-analytics-resource>/text/analytics/v3.0/sentiment?opinionMining=true` |
| キー フレーズの抽出 | POST | `<your-text-analytics-resource>/text/analytics/v3.0/keyPhrases` |
| 固有表現認識 - 一般 | POST | `<your-text-analytics-resource>/text/analytics/v3.0/entities/recognition/general` |
| 固有表現認識 - PII | POST | `<your-text-analytics-resource>/text/analytics/v3.0/entities/recognition/pii` |
| 固有表現認識 - PHI | POST |  `<your-text-analytics-resource>/text/analytics/v3.0/entities/recognition/pii?domain=phi` |

#### <a name="analyze"></a>[解析](#tab/analyze)

| 機能 | 要求の種類 | リソースのエンドポイント |
|--|--|--|
| 分析ジョブを送信する | POST | `https://<your-text-analytics-resource>/text/analytics/v3.1-preview.3/analyze` |
| 分析の状態と結果を取得する | GET | `https://<your-text-analytics-resource>/text/analytics/v3.1-preview.3/analyze/jobs/<Operation-Location>` |

#### <a name="text-analytics-for-health"></a>[Text Analytics for Health](#tab/health)

| 機能 | 要求の種類 | リソースのエンドポイント |
|--|--|--|
| Text Analytics for Health ジョブを送信する  | POST | `https://<your-text-analytics-resource>/text/analytics/v3.1-preview.3/entities/health/jobs` |
| ジョブの状態と結果を取得する | GET | `https://<your-text-analytics-resource>/text/analytics/v3.1-preview.3/entities/health/jobs/<Operation-Location>` |
| ジョブの取り消し | DELETE | `https://<your-text-analytics-resource>/text/analytics/v3.1-preview.3/entities/health/jobs/<Operation-Location>` |

--- 

エンドポイントを取得した後、Postman (または別の Web API テスト ツール) で次のことを行います。

1. 使用する機能の要求の種類を選択します。
2. 上の表から目的の適切な操作のエンドポイントを貼り付けます。
3. 次の 3 つの要求ヘッダーを設定します。

   + `Ocp-Apim-Subscription-Key`: Azure portal から取得したアクセス キー
   + `Content-Type`: application/json
   + `Accept`: application/json

    Postman を使用している場合、`/keyPhrases` エンドポイントであるとすると、要求は次のスクリーンショットのようになります。
    
    ![エンドポイントとヘッダーがある要求のスクリーン ショット](../media/postman-request-keyphrase-1.png)
    
4. **[Body]\(本文\)** の形式として **[raw]\(未加工\)** を選択します
    
    ![本文の設定がある要求のスクリーン ショット](../media/postman-request-body-raw.png)

5. 有効な形式の JSON ドキュメントをいくつか貼り付けます。 上の「**API 要求の形式**」セクションの例を使用します。詳細と例については、以下のトピックを参照してください。

      + [言語検出](text-analytics-how-to-language-detection.md)
      + [キー フレーズ抽出](text-analytics-how-to-keyword-extraction.md)
      + [感情分析](text-analytics-how-to-sentiment-analysis.md)
      + [エンティティの認識](text-analytics-how-to-entity-linking.md)

## <a name="send-the-request"></a>要求を送信する

API 要求を送信します。 同期エンドポイントを呼び出した場合、応答は、要求で指定した各ドキュメント ID の項目が含まれる 1 つの JSON ドキュメントとして、すぐに表示されます。

非同期の `/analyze` または `/health` エンドポイントを呼び出した場合は、202 応答コードを受信したことを確認します。 結果を表示するには、この応答を取得する必要があります。

1. API の応答で、API に送信したジョブを示す `Operation-Location` をヘッダーから見つけます。 
2. 使用したエンドポイントに対する GET 要求を作成します。 エンドポイントの形式については[上の表](#set-up-a-request)を参照し、[API リファレンス ドキュメント](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/AnalyzeStatus)を参照してください。 例:

    `https://my-resource.cognitiveservices.azure.com/text/analytics/v3.1-preview.3/analyze/jobs/<Operation-Location>`

3. `Operation-Location` を要求に追加します。

4. 応答は、要求で指定した各ドキュメント ID の項目が含まれる単一の JSON ドキュメントです。

## <a name="example-api-responses"></a>API 応答の例
 
# <a name="synchronous"></a>[Synchronous](#tab/synchronous)

同期エンドポイントの応答は、使用するエンドポイントによって異なります。 応答の例については、次の記事を参照してください。

+ [言語検出](text-analytics-how-to-language-detection.md#step-3-view-the-results)
+ [キー フレーズ抽出](text-analytics-how-to-keyword-extraction.md#step-3-view-results)
+ [感情分析](text-analytics-how-to-sentiment-analysis.md#view-the-results)
+ [エンティティの認識](text-analytics-how-to-entity-linking.md#view-results)

# <a name="analyze"></a>[解析](#tab/analyze)

成功した場合、`/analyze` エンドポイントへの GET 要求からは、割り当てられたタスクが含まれるオブジェクトが返されます。 たとえば、「 `keyPhraseExtractionTasks` 」のように指定します。 これらのタスクには、適切な Text Analytics 機能からの応答オブジェクトが含まれています。 詳しくは、以下の記事をご覧ください。

+ [キー フレーズ抽出](text-analytics-how-to-keyword-extraction.md#step-3-view-results)
+ [エンティティの認識](text-analytics-how-to-entity-linking.md#view-results)


```json
{
  "displayName": "My Analyze Job",
  "jobId": "dbec96a8-ea22-4ad1-8c99-280b211eb59e_637408224000000000",
  "lastUpdateDateTime": "2020-11-13T04:01:14Z",
  "createdDateTime": "2020-11-13T04:01:13Z",
  "expirationDateTime": "2020-11-14T04:01:13Z",
  "status": "running",
  "errors": [],
  "tasks": {
      "details": {
          "name": "My Analyze Job",
          "lastUpdateDateTime": "2020-11-13T04:01:14Z"
      },
      "completed": 1,
      "failed": 0,
      "inProgress": 2,
      "total": 3,
      "keyPhraseExtractionTasks": [
          {
              "name": "My Analyze Job",
              "lastUpdateDateTime": "2020-11-13T04:01:14.3763516Z",
              "results": {
                  "inTerminalState": true,
                  "documents": [
                      {
                          "id": "doc1",
                          "keyPhrases": [
                              "sunny outside"
                          ],
                          "warnings": []
                      },
                      {
                          "id": "doc2",
                          "keyPhrases": [
                              "favorite Seattle attraction",
                              "Pike place market"
                          ],
                          "warnings": []
                      }
                  ],
                  "errors": [],
                  "modelVersion": "2020-07-01"
              }
          }
      ]
  }
}
```

# <a name="text-analytics-for-health"></a>[Text Analytics for Health](#tab/health)

Text Analytics for Health の非同期 API 応答の詳細については、次の記事を参照してください。

+ [Text Analytics for Health](text-analytics-for-health.md#hosted-asynchronous-web-api-response)


--- 

## <a name="see-also"></a>関連項目

* [Text Analytics の概要](../overview.md)
* [よく寄せられる質問 (FAQ)](../text-analytics-resource-faq.md)</br>
* [Text Analytics 製品ページ](//go.microsoft.com/fwlink/?LinkID=759712)
* [Text Analytics クライアント ライブラリの使用](../quickstarts/text-analytics-sdk.md)
* [新機能](../whats-new.md)
