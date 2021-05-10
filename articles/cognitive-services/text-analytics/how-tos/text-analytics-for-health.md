---
title: Text Analytics for Health を使用する方法
titleSuffix: Azure Cognitive Services
description: Text Analytics for Health を使用して、構造化されていない臨床テキストから医療情報を抽出してラベル付けする方法について説明します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 03/11/2021
ms.author: aahi
ms.custom: references_regions
ms.openlocfilehash: 80a943d235783852f57832363b5af8048f010575
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104599436"
---
# <a name="how-to-use-text-analytics-for-health-preview"></a>方法:Text Analytics for Health (プレビュー) を使用する

> [!IMPORTANT] 
> Text Analytics for Health は、"現状のまま"、"保証なしで" 提供されるプレビュー機能です。 そのため、**Text Analytics for Health (プレビュー) は、運用環境に実装またはデプロイして使用しないでください。** Text Analytics for Health は、医療デバイス、臨床サポート、診断ツール、または他のテクノロジ (病気や他の状況の診断、治療、軽減、取り扱い、防止での使用が意図されているもの) として使用することを意図されたり、使用できるようにされているものではなく、そのような目的でのこの機能の使用に対して、マイクロソフトからはライセンスや権利は付与されません。 この機能は、専門的な医療のアドバイスや医学的意見、診断、治療、または医療専門家による医学的判断に代わるものとして実装またはデプロイするために設計されたり、それを意図されたりしたものではなく、そのようには使用しないでください。 お客様は、Text Analytics for Health の使用に関するすべての責任を持ちます。 Microsoft では、Text Analytics for Health またはその機能に関連して提供されるすべての資料が、すべての医療目的に対して十分であること、またはすべての人の健康的または医療的な要件を満たすことについて、いっさい保証しません。 


Text Analytics for Health は、医師のメモ、退院要約、臨床ドキュメント、電子健康記録などの非構造化テキストからの、関連する医療情報の抽出とラベル付けが行われる、Text Analytics API サービスの機能です。  このサービスを利用するには、次の 2 つの方法があります。 

* [Web ベースの API (非同期)](#structure-the-api-request-for-the-hosted-asynchronous-web-api)
* [Docker コンテナー (同期)](#hosted-asynchronous-web-api-response)   

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Introducing-Text-Analytics-for-Health/player]

## <a name="features"></a>特徴

Text Analytics for Health により、英語のテキストに対して固有表現認識 (NER)、関係抽出、エンティティ否定、エンティティ リンク設定が実行されて、臨床および生物医学に関する構造化されていないテキストに含まれる分析情報が明らかにされます。

### <a name="named-entity-recognition"></a>[名前付きエンティティの認識](#tab/ner)

固有表現認識では、非構造化テキストに記載されている語句が検出され、それを診断、薬剤名、症状や兆候、年齢など、1 つまたは複数のセマンティックの種類に関連付けることができます。

> [!div class="mx-imgBorder"]
> ![Health NER](../media/ta-for-health/health-named-entity-recognition.png)

### <a name="relation-extraction"></a>[関係抽出](#tab/relation-extraction)

関係抽出により、テキスト内で言及されている概念間の意味のある関係が識別されます。 たとえば、"条件の時間" の関係は、条件名を時刻に関連付けるか、省略形と完全な説明の間に関連付けることによって検出されます。  

> [!div class="mx-imgBorder"]
> ![Health RE](../media/ta-for-health/health-relation-extraction.png)


### <a name="entity-linking"></a>[Entity Linking](#tab/entity-linking)

エンティティ リンク設定では、テキストで言及されている説明されている固有表現を、Unified Medical Language System (UMLS) を含む概念の定義済みデータベースにある概念に関連付けることによって、個別のエンティティが明確にされます。 また、医療の概念には、追加の正規化の形式として、優先する名前が割り当てられます。

> [!div class="mx-imgBorder"]
> ![Health EL](../media/ta-for-health/health-entity-linking.png)

Text Analytics for Health では、統一医療言語システム ([UMLS](https://www.nlm.nih.gov/research/umls/sourcereleasedocs/index.html)) メタシソーラス知識ソースにある保健衛生と生物医学のボキャブラリへのリンクがサポートされています。

### <a name="assertion-detection"></a>[アサーションの検出](#tab/assertion-detection) 

医療コンテンツの意味は、否定、または条件付きアサーションなどの修飾子によって大きな影響を受け、これが誤って伝わると重大な影響を及ぼす可能性があります。 Text Analytics for Health では、テキスト内のエンティティに対するアサーションの検出の 3 つのカテゴリがサポートされます。 

* 確実性
* 条件付き
* 関連付け

> [!div class="mx-imgBorder"]
> ![Health NEG](../media/ta-for-health/assertions.png)

---

サポートされているすべてのエンティティの一覧については、Text Analytics for Health によって返される[エンティティのカテゴリ](../named-entity-types.md?tabs=health)を参照してください。 信頼度スコアの詳細については、[Text Analytics の透明性に関するメモ](/legal/cognitive-services/text-analytics/transparency-note#general-guidelines-to-understand-and-improve-performance?context=/azure/cognitive-services/text-analytics/context/context)をご覧ください。 

### <a name="supported-languages-and-regions"></a>サポートされている言語とリージョン

Text Analytics for Health では、英語のドキュメントのみがサポートされます。 

現在、Text Analytics for Health のホストされた Web API は、次のリージョンでのみ利用できます: 米国西部 2、米国東部 2、米国中部、北ヨーロッパ、西ヨーロッパ。

## <a name="request-access-to-the-public-preview"></a>パブリック プレビューへのアクセスを要求する

Text Analytics for Health のパブリック プレビューへのアクセスを要求するには、[Cognitive Services 要求フォーム](https://aka.ms/csgate)に記入して送信します。 Text Analytics for Health の使用には課金されません。 

このフォームでは、ユーザー、会社、コンテナーを使用するユーザー シナリオに関する情報が要求されます。 フォームを送信すると、そのフォームは Azure Cognitive Services チームによって確認されます。その後、チームから決定事項がメールで届きます。

> [!IMPORTANT]
> * このフォームでは、Azure サブスクリプション ID に関連付けられているメール アドレスを使用する必要があります。
> * 使用する Azure リソースは、承認された Azure サブスクリプション ID で作成されている必要があります。 
> * Microsoft からのアプリケーションの状態に関する更新については、電子メール (受信トレイと迷惑フォルダーの両方) を確認してください。

## <a name="using-the-docker-container"></a>Docker コンテナーの使用 

独自の環境で Text Analytics for Health コンテナーを実行するには、こちらの[コンテナーのダウンロードとインストールに関する手順](../how-tos/text-analytics-how-to-install-containers.md?tabs=healthcare)に従ってください。

## <a name="using-the-client-library"></a>クライアント ライブラリの使用

Text Analytics クライアント ライブラリの最新のプレリリース版を使用すると、クライアント オブジェクトを使用して Text Analytics for Health を呼び出すことができます。 リファレンス ドキュメントと、GitHub の例を参照してください。
* [C#](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics)
* [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/)
* [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics)



## <a name="sending-a-rest-api-request"></a>REST API 要求の送信 

### <a name="preparation"></a>準備

Text Analytics for Health を使用すると、処理するテキストの量が少ない方が、高い品質の結果を得られます。 これは、大きなテキスト ブロックの方がよい結果が得られる、キー フレーズ抽出のような他の Text Analytics の機能とは対照的です。 これらの操作から最善の結果を得るには、必要に応じて入力を再構成することを検討します。

JSON ドキュメントは、次の形式である必要があります: ID、テキスト、および言語。 

ドキュメントのサイズは、ドキュメントごとに 5120 文字未満でなければなりません。 1 つのコレクションで許可されるドキュメントの最大数については、概念の下にある[データ制限](../concepts/data-limits.md?tabs=version-3)に関する記事を参照してください。 コレクションは、要求の本文で送信されます。

### <a name="structure-the-api-request-for-the-hosted-asynchronous-web-api"></a>ホストされた非同期 Web API に対する API 要求を作成する

コンテナーとホストされた Web API の両方について、POST 要求を作成する必要があります。 [Postman](text-analytics-how-to-call-api.md)、cURL コマンド、または [Text Analytics for Health のホストされた API リファレンス](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/Health)の **API テスト コンソール** を使用して、POST 要求をすばやく作成し、目的のリージョンのホストされた Web API に送信することができます。 

> [!NOTE]
> 非同期 `/analyze` と `/health` の両方のエンドポイントは、次のリージョンでのみ使用できます。米国西部 2、米国東部 2、米国中部、北ヨーロッパ、西ヨーロッパ。  これらのエンドポイントへの要求を成功させるには、これらのリージョンのいずれかでリソースが作成されていることを確認してください。

次に示すのは、Text Analytics for Health API 要求の POST 本文に添付される JSON ファイルの例です。

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

### <a name="hosted-asynchronous-web-api-response"></a>ホストされた非同期 Web API の応答 

この POST 要求は非同期操作にジョブを送信するために使用されるので、応答オブジェクトにテキストはありません。  ただし、ジョブと出力の状態を確認するための GET 要求を行うには、応答ヘッダーの operation-location KEY 値が必要です。  POST 要求の応答ヘッダーの operation-location KEY 値の例を次に示します。

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.4/entities/health/jobs/<jobID>`

ジョブの状態を確認するには、POST 応答の operation-location KEY ヘッダー値の URL に対して GET 要求を行います。  ジョブの状態を反映するには、次の状態が使用されます: `NotStarted`、`running`、`succeeded`、`failed`、`rejected`、`cancelling`、`cancelled`。  

`NotStarted` または `running` 状態のジョブは、GET 要求と同じ URL に対する DELETE HTTP 呼び出しを使用して取り消すことができます。  DELETE 呼び出しの詳細については、[Text Analytics for Health のホストされた API のリファレンス](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/CancelHealthJob)に関するページを参照してください。

GET 要求の応答の例を次に示します。  出力を取得に使用できるのは `expirationDateTime` (ジョブが作成されてから 24 時間) が経過するまでです。それを過ぎると、出力は消去されます。

```json
{
    "jobId": "be437134-a76b-4e45-829e-9b37dcd209bf",
    "lastUpdateDateTime": "2021-03-11T05:43:37Z",
    "createdDateTime": "2021-03-11T05:42:32Z",
    "expirationDateTime": "2021-03-12T05:42:32Z",
    "status": "succeeded",
    "errors": [],
    "results": {
        "documents": [
            {
                "id": "1",
                "entities": [
                    {
                        "offset": 25,
                        "length": 5,
                        "text": "100mg",
                        "category": "Dosage",
                        "confidenceScore": 1.0
                    },
                    {
                        "offset": 31,
                        "length": 10,
                        "text": "remdesivir",
                        "category": "MedicationName",
                        "confidenceScore": 1.0,
                        "name": "remdesivir",
                        "links": [
                            {
                                "dataSource": "UMLS",
                                "id": "C4726677"
                            },
                            {
                                "dataSource": "DRUGBANK",
                                "id": "DB14761"
                            },
                            {
                                "dataSource": "GS",
                                "id": "6192"
                            },
                            {
                                "dataSource": "MEDCIN",
                                "id": "398132"
                            },
                            {
                                "dataSource": "MMSL",
                                "id": "d09540"
                            },
                            {
                                "dataSource": "MSH",
                                "id": "C000606551"
                            },
                            {
                                "dataSource": "MTHSPL",
                                "id": "3QKI37EEHE"
                            },
                            {
                                "dataSource": "NCI",
                                "id": "C152185"
                            },
                            {
                                "dataSource": "NCI_FDA",
                                "id": "3QKI37EEHE"
                            },
                            {
                                "dataSource": "NDDF",
                                "id": "018308"
                            },
                            {
                                "dataSource": "RXNORM",
                                "id": "2284718"
                            },
                            {
                                "dataSource": "SNOMEDCT_US",
                                "id": "870592005"
                            },
                            {
                                "dataSource": "VANDF",
                                "id": "4039395"
                            }
                        ]
                    },
                    {
                        "offset": 42,
                        "length": 13,
                        "text": "intravenously",
                        "category": "MedicationRoute",
                        "confidenceScore": 1.0
                    },
                    {
                        "offset": 73,
                        "length": 7,
                        "text": "120 min",
                        "category": "Time",
                        "confidenceScore": 0.94
                    }
                ],
                "relations": [
                    {
                        "relationType": "DosageOfMedication",
                        "entities": [
                            {
                                "ref": "#/results/documents/0/entities/0",
                                "role": "Dosage"
                            },
                            {
                                "ref": "#/results/documents/0/entities/1",
                                "role": "Medication"
                            }
                        ]
                    },
                    {
                        "relationType": "RouteOfMedication",
                        "entities": [
                            {
                                "ref": "#/results/documents/0/entities/1",
                                "role": "Medication"
                            },
                            {
                                "ref": "#/results/documents/0/entities/2",
                                "role": "Route"
                            }
                        ]
                    },
                    {
                        "relationType": "TimeOfMedication",
                        "entities": [
                            {
                                "ref": "#/results/documents/0/entities/1",
                                "role": "Medication"
                            },
                            {
                                "ref": "#/results/documents/0/entities/3",
                                "role": "Time"
                            }
                        ]
                    }
                ],
                "warnings": []
            }
        ],
        "errors": [],
        "modelVersion": "2021-03-01"
    }
}
```


### <a name="structure-the-api-request-for-the-container"></a>コンテナーへの API 要求を作成する

[Postman を使用](text-analytics-how-to-call-api.md)するか、次の cURL 要求の例を使用して、デプロイしたコンテナーにクエリを送信します。`serverURL` 変数は適切な値に置き換えます。  コンテナーへの URL での API のバージョンは、ホストされた API とは異なることに注意してください。

```bash
curl -X POST 'http://<serverURL>:5000/text/analytics/v3.2-preview.1/entities/health' --header 'Content-Type: application/json' --header 'accept: application/json' --data-binary @example.json

```

次の JSON は、Text Analytics for Health API 要求の POST 本文に添付されている JSON ファイルの例です。

```json
example.json

{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "Patient reported itchy sores after swimming in the lake."
    },
    {
      "language": "en",
      "id": "2",
      "text": "Prescribed 50mg benadryl, taken twice daily."
    }
  ]
}
```

### <a name="container-response-body"></a>コンテナーの応答本文

次の JSON は、コンテナー化された同期呼び出しからの Text Analytics for Health API 応答本文の例です。

```json
{
    "documents": [
        {
            "id": "1",
            "entities": [
                {
                    "offset": 25,
                    "length": 5,
                    "text": "100mg",
                    "category": "Dosage",
                    "confidenceScore": 1.0
                },
                {
                    "offset": 31,
                    "length": 10,
                    "text": "remdesivir",
                    "category": "MedicationName",
                    "confidenceScore": 1.0,
                    "name": "remdesivir",
                    "links": [
                        {
                            "dataSource": "UMLS",
                            "id": "C4726677"
                        },
                        {
                            "dataSource": "DRUGBANK",
                            "id": "DB14761"
                        },
                        {
                            "dataSource": "GS",
                            "id": "6192"
                        },
                        {
                            "dataSource": "MEDCIN",
                            "id": "398132"
                        },
                        {
                            "dataSource": "MMSL",
                            "id": "d09540"
                        },
                        {
                            "dataSource": "MSH",
                            "id": "C000606551"
                        },
                        {
                            "dataSource": "MTHSPL",
                            "id": "3QKI37EEHE"
                        },
                        {
                            "dataSource": "NCI",
                            "id": "C152185"
                        },
                        {
                            "dataSource": "NCI_FDA",
                            "id": "3QKI37EEHE"
                        },
                        {
                            "dataSource": "NDDF",
                            "id": "018308"
                        },
                        {
                            "dataSource": "RXNORM",
                            "id": "2284718"
                        },
                        {
                            "dataSource": "SNOMEDCT_US",
                            "id": "870592005"
                        },
                        {
                            "dataSource": "VANDF",
                            "id": "4039395"
                        }
                    ]
                },
                {
                    "offset": 42,
                    "length": 13,
                    "text": "intravenously",
                    "category": "MedicationRoute",
                    "confidenceScore": 1.0
                },
                {
                    "offset": 73,
                    "length": 7,
                    "text": "120 min",
                    "category": "Time",
                    "confidenceScore": 0.94
                }
            ],
            "relations": [
                {
                    "relationType": "DosageOfMedication",
                    "entities": [
                        {
                            "ref": "#/documents/0/entities/0",
                            "role": "Dosage"
                        },
                        {
                            "ref": "#/documents/0/entities/1",
                            "role": "Medication"
                        }
                    ]
                },
                {
                    "relationType": "RouteOfMedication",
                    "entities": [
                        {
                            "ref": "#/documents/0/entities/1",
                            "role": "Medication"
                        },
                        {
                            "ref": "#/documents/0/entities/2",
                            "role": "Route"
                        }
                    ]
                },
                {
                    "relationType": "TimeOfMedication",
                    "entities": [
                        {
                            "ref": "#/documents/0/entities/1",
                            "role": "Medication"
                        },
                        {
                            "ref": "#/documents/0/entities/3",
                            "role": "Time"
                        }
                    ]
                }
            ],
            "warnings": []
        }
    ],
    "errors": [],
    "modelVersion": "2021-03-01"
}
```

### <a name="assertion-output"></a>アサーションの出力

Text Analytics for Health はアサーション修飾子を返します。これは、テキスト内の概念のコンテキストをより深く理解するために、医療コンセプトに割り当てられた有益な属性です。 これらの修飾子は 3 つのカテゴリに分かれており、それぞれが異なる側面に焦点を置いており、相互に排他的な値のセットを含んでいます。 各エンティティには、カテゴリごとに 1 つの値のみが割り当てられます。 各カテゴリの最も一般的な値が既定値です。 サービスの出力応答には、既定値とは異なるアサーション修飾子だけが含まれます。

**CERTAINTY** - 概念の存在に関する情報と (存在または不在)、その存在に関するテキストがどの程度確実か (的確または可能性) に関する情報を提供します。
*   **Positive** [既定値]: 概念が存在するか、発生しています。
* **Negative**: 概念は現在存在していないか、発生していません。
* **Positive_Possible**: 概念は存在する可能性がありますが、不明確要素があります。
* **Negative_Possible**: 概念が存在する可能性はほとんどありませんが、ある程度の不明確要素があります。
* **Neutral_Possible**: 概念は、存在する可能性と存在しない可能性があり、どちらの側にも偏っていません。

**CONDITIONALITY** - 概念の存在が特定の条件に依存するかどうかに関する情報を提供します。 
*   **None** [既定値]: 概念は事実であり、仮説でなく、特定の条件に依存しません。
*   **Hypothetical**: 概念は発達するか、将来発生する可能性があります。
*   **Conditional**: 概念は存在するか、特定の条件下でのみ発生します。

**ASSOCIATION** - 概念はテキストの対象または別の誰かと関連付けられているかどうかを示します。
*   **Subject** [既定値]: 概念は、テキストの対象 (通常は患者) に関連付けられています。
*   **Someone_Else**: 概念は、テキストの対象ではない誰かに関連付けられています。


アサーションの検出は、次の例のように、否定されたエンティティを、確実性カテゴリの負の値として表します。

```json
{
                        "offset": 381,
                        "length": 3,
                        "text": "SOB",
                        "category": "SymptomOrSign",
                        "confidenceScore": 0.98,
                        "assertion": {
                            "certainty": "negative"
                        },
                        "name": "Dyspnea",
                        "links": [
                            {
                                "dataSource": "UMLS",
                                "id": "C0013404"
                            },
                            {
                                "dataSource": "AOD",
                                "id": "0000005442"
                            },
    ...
```

### <a name="relation-extraction-output"></a>関係抽出の出力

Text Analytics for Health は、さまざまな概念間の関係を認識します。たとえば、属性とエンティティの間の関係 (身体構造の傾向、投薬など)、エンティティ間の関係 (省略の検出など) を示します。

**ABBREVIATION**

**DIRECTION_OF_BODY_STRUCTURE**

**DIRECTION_OF_CONDITION**

**DIRECTION_OF_EXAMINATION**

**DIRECTION_OF_TREATMENT**

**DOSAGE_OF_MEDICATION**

**FORM_OF_MEDICATION**

**FREQUENCY_OF_MEDICATION**

**FREQUENCY_OF_TREATMENT**

**QUALIFIER_OF_CONDITION**

**RELATION_OF_EXAMINATION**

**ROUTE_OF_MEDICATION** 

**TIME_OF_CONDITION**

**TIME_OF_EVENT**

**TIME_OF_EXAMINATION**

**TIME_OF_MEDICATION**

**TIME_OF_TREATMENT**

**UNIT_OF_CONDITION**

**UNIT_OF_EXAMINATION**

**VALUE_OF_CONDITION**  

**VALUE_OF_EXAMINATION**

> [!NOTE]
> * CONDITION を参照する関係は、DIAGNOSIS エンティティ型または SYMPTOM_OR_SIGN エンティティ型のいずれかを参照できます。
> * MEDICATION を参照する関係は、MEDICATION_NAME エンティティ型または MEDICATION_CLASS エンティティ型を参照できます。
> * TIME を参照する関係は、TIME エンティティ型または DATE エンティティ型のいずれかを参照できます。

リレーションシップ抽出の出力には、リレーションシップの型のエンティティの URI 参照と割り当てられたロールが含まれます。 次に例を示します。

```json
                "relations": [
                    {
                        "relationType": "DosageOfMedication",
                        "entities": [
                            {
                                "ref": "#/results/documents/0/entities/0",
                                "role": "Dosage"
                            },
                            {
                                "ref": "#/results/documents/0/entities/1",
                                "role": "Medication"
                            }
                        ]
                    },
                    {
                        "relationType": "RouteOfMedication",
                        "entities": [
                            {
                                "ref": "#/results/documents/0/entities/1",
                                "role": "Medication"
                            },
                            {
                                "ref": "#/results/documents/0/entities/2",
                                "role": "Route"
                            }
                        ]
...
]
```

## <a name="see-also"></a>関連項目

* [Text Analytics の概要](../overview.md)
* [名前付きエンティティのカテゴリ](../named-entity-types.md)
* [新機能](../whats-new.md)
