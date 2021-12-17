---
title: センチメント コグニティブ スキル (V3)
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search で AI エンリッチメント パイプラインにテキストのセンチメント ラベルを提供します。
manager: jennmar
author: careyjmac
ms.author: chalton
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/12/2021
ms.openlocfilehash: d42eb099091840ed5dbab61abbad47e087504ab3
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/29/2021
ms.locfileid: "129214524"
---
# <a name="sentiment-cognitive-skill-v3"></a>センチメント コグニティブ スキル (V3)

V3 **センチメント** のスキルは、非構造化テキストを評価し、各レコードについて、文章とドキュメントレベルでサービスによって検出された最高の信頼スコアに基づいて、センチメント ラベル ("ネガティブ"、"中立"、"ポジティブ" など) を提供します。 このスキルでは、Cognitive Services の [Text Analytics](../cognitive-services/text-analytics/overview.md) のバージョン 3 によって提供される機械学習モデルが使用されます。 また、[Text Analytics API のオピニオン マイニング機能](../cognitive-services/text-analytics/how-tos/text-analytics-how-to-sentiment-analysis.md#opinion-mining)も公開されており、テキストに含まれる製品やサービスの属性に関連する意見に関するより詳細な情報を提供します。

> [!NOTE]
> このスキルは Cognitive Services にバインドされており、1 日にインデクサーあたり 20 ドキュメントを超えるトランザクションには[課金対象リソース](cognitive-search-attach-cognitive-services.md)が必要です。 組み込みスキルの実行は、既存の [Cognitive Services の従量課金制の価格](https://azure.microsoft.com/pricing/details/cognitive-services/)で課金されます。
>

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.V3.SentimentSkill

## <a name="data-limits"></a>データ制限
レコードの最大サイズは、[`String.Length`](/dotnet/api/system.string.length) によって測定されるため、5,000 文字にする必要があります。 データをセンチメント スキルに送信する前に分割する必要がある場合は、[テキスト分割スキル](cognitive-search-skill-textsplit.md)を使用します。

## <a name="skill-parameters"></a>スキルのパラメーター

パラメーターの大文字と小文字は区別されます。

| パラメーター名 | 説明 |
|----------------|----------------------|
| `defaultLanguageCode` | (省略可能) 言語を明示的に指定しないドキュメントに適用する言語コード。 <br/> [サポートされる言語の完全な一覧](../cognitive-services/text-analytics/language-support.md)を参照 |
| `modelVersion`   | (省略可能) Text Analytics サービスを呼び出すときに使用するモデルのバージョン。 指定しない場合、既定では直近のバージョンになります。 絶対に必要な場合以外は、この値を指定しないことをお勧めします。 詳細については、「[Text Analytics API でのモデルのバージョン管理](../cognitive-services/text-analytics/concepts/model-versioning.md)」を参照してください。 |
| `includeOpinionMining` | `true` に設定されている場合、 [TextAnalytics の意見マイニング機能](../cognitive-services/text-analytics/how-tos/text-analytics-how-to-sentiment-analysis.md#opinion-mining)が有効になります。これにより、アスペクト ベースのセンチメント分析を出力結果に含めることができます。 既定値は `false` です。 |

## <a name="skill-inputs"></a>スキルの入力 

| 入力名 | 説明 |
|--------------------|-------------|
| `text` | 分析されるテキスト。|
| `languageCode`    |  (省略可能) レコードの言語を示す文字列。 このプロパティが指定されていない場合の既定値は "en" です。 <br/>[サポートされる言語の完全な一覧](../cognitive-services/text-analytics/language-support.md)を参照してください。|

## <a name="skill-outputs"></a>スキルの出力

| 出力名 | 説明 |
|--------------------|-------------|
| `sentiment` | 分析されたテキスト全体のセンチメント ラベル (ポジティブ、中立、またはネガティブ) を表す文字列値。 |
| `confidenceScores` | 3 つの double 値を持つ複合型。ポジティブ、中立、またはネガティブの各評価に 1 つずつです。 値の範囲は 0 から 1.00 です。1.00 は、指定されたラベルの割り当てで可能な限り高い信頼度を表します。 |
| `sentences` | 文章によってテキスト文のセンチメントを分割する複合型のコレクション。 また、`includeOpinionMining` が `true` に設定されている場合は、ターゲットと評価の形式でオピニオン マイニングの結果が返されます。|


##  <a name="sample-definition"></a>定義例

```json
{
    "@odata.type": "#Microsoft.Skills.Text.V3.SentimentSkill",
    "context": "/document",
    "includeOpinionMining": true,
    "inputs": [
        {
            "name": "text",
            "source": "/document/content"
        },
        {
            "name": "languageCode",
            "source": "/document/languageCode"
        }
    ],
    "outputs": [
        {
            "name": "sentiment",
            "targetName": "sentiment"
        },
        {
            "name": "confidenceScores",
            "targetName": "confidenceScores"
        },
        {
            "name": "sentences",
            "targetName": "sentences"
        }
    ]
}
```

##  <a name="sample-input"></a>サンプル入力

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "text": "I had a terrible time at the hotel. The staff was rude and the food was awful.",
                "languageCode": "en"
            }
        }
    ]
}
```

##  <a name="sample-output"></a>サンプル出力

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "sentiment": "negative",
                "confidenceScores": {
                    "positive": 0.0,
                    "neutral": 0.0,
                    "negative": 1.0
                },
                "sentences": [
                    {
                        "text": "I had a terrible time at the hotel.",
                        "sentiment": "negative",
                        "confidenceScores": {
                            "positive": 0.0,
                            "neutral": 0.0,
                            "negative": 1.0
                        },
                        "offset": 0,
                        "length": 35,
                        "targets": [],
                        "assessments": [],
                    },
                    {
                        "text": "The staff was rude and the food was awful.",
                        "sentiment": "negative",
                        "confidenceScores": {
                            "positive": 0.0,
                            "neutral": 0.0,
                            "negative": 1.0
                        },
                        "offset":36,
                        "length": 42,
                        "targets": [
                            {
                                "text": "staff",
                                "sentiment": "negative",
                                "confidenceScores": {
                                    "positive": 0.0,
                                    "neutral": 0.0,
                                    "negative": 1.0
                                },
                                "offset": 40,
                                "length": 5,
                                "relations": [
                                    {
                                        "relationType": "assessment",
                                        "ref": "#/documents/0/sentences/1/assessments/0",
                                    }
                                ]
                            },
                            {
                                "text": "food",
                                "sentiment": "negative",
                                "confidenceScores": {
                                    "positive": 0.0,
                                    "neutral": 0.0,
                                    "negative": 1.0
                                },
                                "offset": 63,
                                "length": 4,
                                "relations": [
                                    {
                                        "relationType": "assessment",
                                        "ref": "#/documents/0/sentences/1/assessments/1",
                                    }
                                ]
                            }
                        ],
                        "assessments": [
                            {
                                "text": "rude",
                                "sentiment": "negative",
                                "confidenceScores": {
                                    "positive": 0.0,
                                    "neutral": 0.0,
                                    "negative": 1.0
                                },
                                "offset": 50,
                                "length": 4,
                                "isNegated": false
                            },
                            {
                                "text": "awful",
                                "sentiment": "negative",
                                "confidenceScores": {
                                    "positive": 0.0,
                                    "neutral": 0.0,
                                    "negative": 1.0
                                },
                                "offset": 72,
                                "length": 5,
                                "isNegated": false
                            }
                        ],
                    }
                ]
            }
        }
    ]
}
```

## <a name="warning-cases"></a>警告のケース
テキストが空の場合は、警告が生成され、センチメント結果は返されません。
言語がサポートされていない場合は、警告が生成され、センチメント 結果は返されません。

## <a name="see-also"></a>関連項目

+ [組み込みのスキル](cognitive-search-predefined-skills.md)
+ [スキルセットの定義方法](cognitive-search-defining-skillset.md)
