---
title: Text Analytics REST API を使用して感情分析とオピニオン マイニングを実行する
titleSuffix: Azure Cognitive Services
description: この記事では、Azure Cognitive Services Text Analytics API を使用して、テキスト内でセンチメントを検出し、オピニオンを汲み取る方法について説明します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: sample
ms.date: 03/29/2021
ms.author: aahi
ms.openlocfilehash: 7cd2b0a6b943ceb32420ef119a7fc5eddefa2e19
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2021
ms.locfileid: "106276996"
---
# <a name="how-to-sentiment-analysis-and-opinion-mining"></a>方法: 感情分析とオピニオン マイニング

Text Analytics API の感情分析機能には、肯定的および否定的センチメントを検出するための 2 つの方法が用意されています。 感情分析要求を送信すると、文とドキュメントのレベルでセンチメント ラベル ("negative"、"neutral"、"positive" など) と信頼度スコアが API から返されます。 また、感情分析エンドポイントを使用してオピニオン マイニング要求を送信することもできます。これにより、テキスト内の単語 (製品やサービスの属性など) に関連した意見に関する詳細な情報が提供されます。

API で使用される AI モデルはサービスによって提供されるため、分析対象のコンテンツを送信するだけで済みます。

## <a name="sentiment-analysis-versions-and-features"></a>感情分析のバージョンと機能

| 機能                                   | 感情分析 v3 | Sentiment Analysis v3.1 (プレビュー) |
|-------------------------------------------|-----------------------|-----------------------------------|
| 単一要求およびバッチ要求のメソッド    | X                     | X                                 |
| 感情分析のスコアとラベル付け             | X                     | X                                 |
| Linux ベースの [Docker コンテナー](text-analytics-how-to-install-containers.md) | X  |  |
| オピニオン マイニング                            |                       | X                                 |

## <a name="sentiment-analysis"></a>感情分析

バージョン 3.x の感情分析では、文とドキュメントのレベルで返されるセンチメント ラベルが、それぞれの信頼度スコアとともにテキストに適用されます。 

ラベルは、*positive*、*negative*、および *neutral* です。 ドキュメント レベルでは、*mixed* センチメント ラベルが返されることもあります。 ドキュメントのセンチメントは、次のように決定されます。

| 文章のセンチメント                                                                            | 返されるドキュメントのラベル |
|-----------------------------------------------------------------------------------------------|-------------------------|
| ドキュメントに `positive` の文が少なくとも 1 つ含まれている。 残りの文は `neutral` である。 | `positive`              |
| ドキュメントに `negative` の文が少なくとも 1 つ含まれている。 残りの文は `neutral` である。 | `negative`              |
| ドキュメントに、`negative` の文が少なくとも 1 つ、`positive` の文が少なくとも 1 つ含まれている。    | `mixed`                 |
| ドキュメント内のすべての文が `neutral` である。                                                  | `neutral`               |

信頼度スコアは、1 から 0 の範囲で割り当てられます。 スコアが 1 に近いほど、ラベルの分類の信頼度が高いことを示し、スコアが低いほど信頼度が低いことを示します。 各ドキュメントまたは各文で、ラベル (positive、negative、neutral) に関連付けられた予測スコアは合計で 1 になります。 詳細については、[Text Analytics の透明性に関するメモ](/legal/cognitive-services/text-analytics/transparency-note?context=/azure/cognitive-services/text-analytics/context/context)を参照してください。 

## <a name="opinion-mining"></a>オピニオン マイニング

オピニオン マイニングは、バージョン 3.1 のプレビュー以降の感情分析の機能です。 この機能は、自然言語処理 (NLP) ではアスペクトベースの感情分析とも呼ばれます。テキストに含まれる製品やサービスの属性に関連した意見について、より粒度の細かい情報が得られます。 API は、ターゲット (名詞または動詞) と評価 (形容詞) として意見をクローズアップします。

たとえば、あるホテルについて "The room was great, but the staff was unfriendly (部屋はすばらしいのですが、スタッフが無愛想でした)" のようなフィードバックを利用者が残した場合、そのテキストに含まれるターゲット (アスペクト) と、それに関連する評価 (意見) と感情がオピニオン マイニングによって特定されます。 感情分析では、否定的な感情のみが報告される可能性があります。

:::image type="content" source="../media/how-tos/opinion-mining.png" alt-text="オピニオン マイニング例の図" lightbox="../media/how-tos/opinion-mining.png":::

オピニオン マイニングを結果に含めるには、感情分析の要求に `opinionMining=true` フラグを含める必要があります。 オピニオン マイニングの結果が感情分析の応答に含められます。 オピニオン マイニングは感情分析の拡張機能であり、現在の[価格レベル](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/)に含まれています。


## <a name="sending-a-rest-api-request"></a>REST API 要求の送信 

### <a name="preparation"></a>準備

感情分析では、作業するテキストの分量が小さいほど、高い品質の結果が得られます。 これは、テキスト ブロックが大きい方がパフォーマンスが向上するキー フレーズ抽出とは反対です。 両方の操作から最善の結果を得るには、両方の操作に応じて入力を再構成することを検討してください。

JSON ドキュメントは、次の形式である必要があります: ID、テキスト、および言語。 感情分析はさまざまな言語をサポートしており、さらにプレビューで追加されます。 詳細については、[サポートされている言語](../language-support.md)に関するページを参照してください。

ドキュメントのサイズは、ドキュメントごとに 5120 文字未満でなければなりません。 1 つのコレクションで許可されるドキュメントの最大数については、概念の下にある[データ制限](../concepts/data-limits.md?tabs=version-3)に関する記事を参照してください。 コレクションは、要求の本文で送信されます。

## <a name="structure-the-request"></a>要求を構造化する

POST 要求を作成します。 次のリファレンス リンクにある [Postman](text-analytics-how-to-call-api.md) または **API テスト コンソール** を使用して、簡単に要求を構造化し、送信することができます。 

#### <a name="version-31-preview"></a>[Version 3.1-preview](#tab/version-3-1)

[感情分析 v3.1 のリファレンス](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/Sentiment)

#### <a name="version-30"></a>[Version 3.0](#tab/version-3)

[感情分析 v3 のリファレンス](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Sentiment)

---

### <a name="request-endpoints"></a>要求エンドポイント

Azure 上の Text Analytics リソースまたはインスタンス化された [Text Analytics コンテナー](text-analytics-how-to-install-containers.md)を使用して、感情分析用の HTTPS エンドポイントを設定します。 使用するバージョンの正しい URL を指定する必要があります。 次に例を示します。

> [!NOTE]
> Azure portal で Text Analytics リソースのキーとエンドポイントを確認できます。 それらは、リソースの **クイック スタート** ページの **リソース管理** の下にあります。 

#### <a name="version-31-preview"></a>[Version 3.1-preview](#tab/version-3-1)

**感情分析**

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.4/sentiment`

**オピニオン マイニング**

オピニオン マイニングの結果を得るには、`opinionMining=true` パラメーターを含める必要があります。 次に例を示します。

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.4/sentiment?opinionMining=true`

既定では、このパラメーターが `false` に設定されています。 

#### <a name="version-30"></a>[Version 3.0](#tab/version-3)

**感情分析**

v3.0 では、使用できるエンドポイントは、感情分析用のみになります。
 
`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/sentiment`

---

要求ヘッダーに Text Analytics API キーが含まれるように設定します。 要求本文で、この分析のために準備した JSON ドキュメントのコレクションを提供します。

### <a name="example-request-for-sentiment-analysis-and-opinion-mining"></a>感情分析とオピニオン マイニングの要求の例  

感情分析のために送信するコンテンツの例を次に示します。 要求の形式は、`v3.0` と `v3.1-preview` のどちらでも同じです。
    
```json
{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "The restaurant had great food and our waiter was friendly."
    }
  ]
}
```

### <a name="post-the-request"></a>要求を投稿する

要求が受信されると分析が実行されます。 分単位および秒単位で送信できる要求のサイズと数については、概要の「[data limits (データ制限)](../overview.md#data-limits)」セクションを参照してください。

Text Analytics API はステートレスです。 データはアカウントに保存されず、結果がすぐに応答で返されます。


### <a name="view-the-results"></a>結果の確認

出力はすぐに返されます。 JSON を受け入れるアプリケーションに結果をストリーミングすることも、出力をローカル システム上のファイルに保存することもできます。 次に、データの並べ替え、検索、および操作に使用できるアプリケーション内に出力をインポートします。 多言語と絵文字のサポートにより、応答にはテキスト オフセットが含まれる場合があります。 詳細については[オフセットの処理方法](../concepts/text-offsets.md)に関するページを参照してください。

#### <a name="version-31-preview"></a>[Version 3.1-preview](#tab/version-3-1)

### <a name="sentiment-analysis-and-opinion-mining-example-response"></a>感情分析とオピニオン マイニングの応答の例

> [!IMPORTANT]
> 次に示すのは、API の v3.1 で提供されている、オピニオン マイニングを感情分析と共に使用するための JSON 例です。 オピニオン マイニングを要求しない場合、API 応答は **[バージョン 3.0]** タブと同じになります。  

感情分析 v3.1 では、感情分析とオピニオン マイニングの両方の応答オブジェクトを返すことができます。
  
感情分析は、ドキュメント全体およびそこに含まれるそれぞれの文について、センチメント ラベルと信頼度スコアを返します。 スコアが 1 に近いほど、ラベルの分類の信頼度が高いことを示し、スコアが低いほど信頼度が低いことを示します。 ドキュメントには複数の文が含まれる場合があり、それぞれのドキュメントまたは文の範囲内での信頼度スコアの合計は 1 になります。

テキスト内のターゲット (名詞または動詞) とそれに関連する評価 (形容詞) がオピニオン マイニングによって特定されます。 以下の応答では、"*The restaurant had great food and our waiter was friendly (レストランの食事はすばらしく、ウエーターの態度も良かった)* " という文に、"*food (食事)* " と "*waiter (ウエーター)* " の 2 つのターゲットが存在します。 それぞれのターゲットの `relations` プロパティには `ref` 値があり、関連する `documents`、`sentences`、`assessments` オブジェクトへの URI 参照が設定されています。

API は、ターゲット (名詞または動詞) と評価 (形容詞) として意見を返します。

```json
{
  "documents": [
    {
      "id": "1",
      "sentiment": "positive",
      "confidenceScores": {
        "positive": 1,
        "neutral": 0,
        "negative": 0
      },
      "sentences": [
        {
          "sentiment": "positive",
          "confidenceScores": {
            "positive": 1,
            "neutral": 0,
            "negative": 0
          },
          "offset": 0,
          "length": 58,
          "text": "The restaurant had great food and our waiter was friendly.",
          "targets": [
            {
              "sentiment": "positive",
              "confidenceScores": {
                "positive": 1,
                "negative": 0
              },
              "offset": 25,
              "length": 4,
              "text": "food",
              "relations": [
                {
                  "relationType": "assessment",
                  "ref": "#/documents/0/sentences/0/assessments/0"
                }
              ]
            },
            {
              "sentiment": "positive",
              "confidenceScores": {
                "positive": 1,
                "negative": 0
              },
              "offset": 38,
              "length": 6,
              "text": "waiter",
              "relations": [
                {
                  "relationType": "assessment",
                  "ref": "#/documents/0/sentences/0/assessments/1"
                }
              ]
            }
          ],
          "assessments": [
            {
              "sentiment": "positive",
              "confidenceScores": {
                "positive": 1,
                "negative": 0
              },
              "offset": 19,
              "length": 5,
              "text": "great",
              "isNegated": false
            },
            {
              "sentiment": "positive",
              "confidenceScores": {
                "positive": 1,
                "negative": 0
              },
              "offset": 49,
              "length": 8,
              "text": "friendly",
              "isNegated": false
            }
          ]
        }
      ],
      "warnings": []
    }
  ],
  "errors": [],
  "modelVersion": "2020-04-01"
}
```

#### <a name="version-30"></a>[Version 3.0](#tab/version-3)

### <a name="sentiment-analysis-example-response"></a>感情分析の応答の例

感情分析は、ドキュメント全体およびそこに含まれるそれぞれの文について、センチメント ラベルと信頼度スコアを返します。 スコアが 1 に近いほど、ラベルの分類の信頼度が高いことを示し、スコアが低いほど信頼度が低いことを示します。 ドキュメントには複数の文が含まれる場合があり、それぞれのドキュメントまたは文の範囲内での信頼度スコアの合計は 1 になります。

感情分析 v3 からの応答には、分析された各文およびドキュメントのセンチメント ラベルとセンチメント スコアが記載されています。

```json
{
    "documents": [
        {
            "id": "1",
            "sentiment": "positive",
            "confidenceScores": {
                "positive": 1.0,
                "neutral": 0.0,
                "negative": 0.0
            },
            "sentences": [
                {
                    "sentiment": "positive",
                    "confidenceScores": {
                        "positive": 1.0,
                        "neutral": 0.0,
                        "negative": 0.0
                    },
                    "offset": 0,
                    "length": 58,
                    "text": "The restaurant had great food and our waiter was friendly."
                }
            ],
            "warnings": []
        }
    ],
    "errors": [],
    "modelVersion": "2020-04-01"
}
```

---

## <a name="summary"></a>まとめ

この記事では、Text Analytics API を使用した感情分析の概念とワークフローについて説明しました。 要約すると:

+ 感情分析とオピニオン マイニングは、選択した言語で利用可能です。
+ 要求本文内の JSON ドキュメントには、ID、テキスト、および言語のコードが含まれます。
+ POST 要求は、ユーザーのサブスクリプションで有効な、個人用に設定された[アクセス キーとエンドポイント](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource)を使用して `/sentiment` エンドポイントに対して行われます。
+ オピニオン マイニングの結果を取得するには、感情分析要求で `opinionMining=true` を使用します。
+ ドキュメント ID ごとのセンチメント スコアで構成される応答出力は、JSON を受け入れるすべてのアプリにストリーミングすることができます。 たとえば、Excel や Power BI です。

## <a name="see-also"></a>関連項目

* [Text Analytics の概要](../overview.md)
* [Text Analytics クライアント ライブラリの使用](../quickstarts/client-libraries-rest-api.md)
* [新機能](../whats-new.md)
