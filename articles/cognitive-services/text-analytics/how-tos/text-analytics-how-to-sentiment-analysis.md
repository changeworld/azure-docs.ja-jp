---
title: Text Analytics REST API を使用した感情分析の実行
titleSuffix: Azure Cognitive Services
description: この記事では、Azure Cognitive Services Text Analytics REST API でテキスト内のセンチメントを検出する方法について説明します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: sample
ms.date: 03/09/2020
ms.author: aahi
ms.openlocfilehash: b3c112876bfd2578e6ebaa95c6902aa9b8f832d9
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "79203459"
---
# <a name="how-to-detect-sentiment-using-the-text-analytics-api"></a>方法:Text Analytics API を使用してセンチメントを検出する

Text Analytics API の感情分析機能では、テキストを評価し、各文のセンチメント スコアとセンチメント ラベルを返します。 これは、ソーシャル メディア、顧客レビュー、ディスカッション フォーラムなどで肯定的および否定的センチメントを検出する際に役立ちます。 API で使用される AI モデルはサービスによって提供されるため、分析対象のコンテンツを送信するだけで済みます。

> [!TIP]
> Text Analytics には言語検出用の Linux ベースの Docker コンテナー イメージも用意されているため、データの近くに [Text Analytics コンテナーをインストールして実行](text-analytics-how-to-install-containers.md)することができます。

感情分析はさまざまな言語をサポートしており、さらにプレビューで追加されます。 詳細については、[サポートされている言語](../text-analytics-supported-languages.md)に関するページを参照してください。

## <a name="concepts"></a>概念

Text Analytics API では、機械学習分類アルゴリズムを使用して、0 から 1 の間でセンチメント スコアを生成します。 1 に近いスコアは肯定的センチメント、0 に近いスコアは否定的センチメントを示します。 センチメント分析は、テキスト内の個々のエンティティではなく、ドキュメント全体に対して実行されます。 つまり、センチメント スコアはドキュメントまたは文のレベルで返されます。 

使用されるモデルは、大規模なテキストのコーパスとセンチメント関連付けを使って事前にトレーニングされています。 テキスト処理、品詞の分析、単語の配置、単語の関連付けなどの分析手法が組み合わせて使用されます。 アルゴリズムの詳細については、[Text Analytics の概要](https://blogs.technet.microsoft.com/machinelearning/2015/04/08/introducing-text-analytics-in-the-azure-ml-marketplace/)に関する記事を参照してください。 現時点では、ユーザー独自のトレーニング データを提供することはできません。 

ドキュメントに含まれているのが大きなテキスト ブロックではなく、それよりも少ない数の文である場合、スコア付けの正確性が向上する傾向があります。 客観性評価段階では、モデルは、ドキュメント全体として客観的か、またはセンチメントが含まれているかどうかを決定します。 大部分が客観的なドキュメントはセンチメント検出段階には進まず、0.50 スコアが付けられ、それ以上は処理されません。 ドキュメントがパイプライン内で続行される場合、次のフェーズでは 0.50 より上または下のスコアが生成されます。 スコアは、ドキュメント内で検出されたセンチメントの程度によって異なります。

## <a name="sentiment-analysis-versions-and-features"></a>感情分析のバージョンと機能

Text Analytics API には、2 つのバージョンの感情分析 (v2 と v3) が用意されています。 感情分析 v3 (パブリック プレビュー) では、API のテキスト分類とスコア付けの正確性および詳細が大幅に改善されています。

> [!NOTE]
> * 感情分析 v3 の要求の形式と[データ制限](../overview.md#data-limits)は、以前のバージョンと同じです。
> * 感情分析 v3 は、`Australia East`、`Central Canada`、`Central US`、`East Asia`、`East US`、`East US 2`、`North Europe`、`Southeast Asia`、`South Central US`、`UK South`、`West Europe`、`West US 2` の各リージョンで利用できます。

| 機能                                   | 感情分析 v2 | 感情分析 v3 |
|-------------------------------------------|-----------------------|-----------------------|
| 単一要求およびバッチ要求のメソッド    | X                     | X                     |
| ドキュメント全体のセンチメント スコア  | X                     | X                     |
| 個々の文のセンチメント スコア |                       | X                     |
| センチメント ラベル付け                        |                       | X                     |
| モデルのバージョン管理                   |                       | X                     |

#### <a name="version-30-preview"></a>[Version 3.0-preview](#tab/version-3)

### <a name="sentiment-scoring"></a>センチメント スコアリング

感情分析 v3 では、テキストをセンチメント ラベルで分類します (以下を参照)。 返されるスコアは、テキストが肯定的、否定的、または中立のいずれであるかの、モデルの信頼度を表します。 値が大きいほど、信頼度が高くなります。 

### <a name="sentiment-labeling"></a>センチメント ラベル付け

感情分析 v3 では、文章およびドキュメント レベルでスコアとラベルが返されます。 スコアおよびラベルは、`positive`、 `negative`、および `neutral` です。 ドキュメント レベルでは、`mixed` センチメント ラベルも返されます。この場合、スコアは返されません。 ドキュメントのセンチメントは、次のように決定されます。

| 文章のセンチメント                                                                            | 返されるドキュメントのラベル |
|-----------------------------------------------------------------------------------------------|-------------------------|
| ドキュメントに `positive` の文が少なくとも 1 つ含まれている。 残りの文は `neutral` である。 | `positive`              |
| ドキュメントに `negative` の文が少なくとも 1 つ含まれている。 残りの文は `neutral` である。 | `negative`              |
| ドキュメントに、`negative` の文が少なくとも 1 つ、`positive` の文が少なくとも 1 つ含まれている。    | `mixed`                 |
| ドキュメント内のすべての文が `neutral` である。                                                  | `neutral`               |

### <a name="model-versioning"></a>モデルのバージョン管理

> [!NOTE]
> バージョン `v3.0-preview.1` 以降では、感情分析に対するモデルのバージョン管理を使用できます。

[!INCLUDE [v3-model-versioning](../includes/model-versioning.md)]

### <a name="example-c-code"></a>C# のコード例

このバージョンの感情分析を呼び出す C# アプリケーションの例については、[GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/dotnet/Language/TextAnalyticsSentiment.cs) をご覧ください。


#### <a name="version-21"></a>[バージョン 2.1](#tab/version-2)

### <a name="sentiment-scoring"></a>センチメント スコアリング

センチメント アナライザーでは、テキストが主に正または負に分類されます。 0 ～ 1 の範囲のスコアが割り当てられます。 0\.5 に近い値は、中立または不確定です。 0\.5 のスコアは、中立性を示します。 文字列の感情分析をすることができない場合、またはセンチメントがない場合、スコアは必ず正確に 0.5 になります。 たとえば、英語の言語コードでスペイン語の文字列を渡した場合、スコアは 0.5 になります。

---

## <a name="sending-a-rest-api-request"></a>REST API 要求の送信 

### <a name="preparation"></a>準備

感情分析では、作業するテキストの分量が小さいほど、高い品質の結果が得られます。 これは、テキスト ブロックが大きい方がパフォーマンスが向上するキー フレーズ抽出とは反対です。 両方の操作から最善の結果を得るには、両方の操作に応じて入力を再構成することを検討してください。

JSON ドキュメントは、次の形式である必要があります: ID、テキスト、および言語。

ドキュメントのサイズは、ドキュメントごとに 5120 文字未満でなければなりません。 コレクションごとに最大 1000 個の項目 (ID) を含めることができます。 コレクションは、要求の本文で送信されます。

## <a name="structure-the-request"></a>要求を構造化する

POST 要求を作成します。 次のリファレンス リンクにある [Postman](text-analytics-how-to-call-api.md) または **API テスト コンソール**を使用して、簡単に要求を構造化し、送信することができます。 

#### <a name="version-30-preview"></a>[Version 3.0-preview](#tab/version-3)

[感情分析 v3 のリファレンス](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/Sentiment)

#### <a name="version-21"></a>[バージョン 2.1](#tab/version-2)

[感情分析 v2 のリファレンス](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9)

---

Azure 上の Text Analytics リソースまたはインスタンス化された [Text Analytics コンテナー](text-analytics-how-to-install-containers.md)を使用して、感情分析用の HTTPS エンドポイントを設定します。 使用するバージョンの正しい URL を指定する必要があります。 次に例を示します。

> [!NOTE]
> Azure portal で Text Analytics リソースのキーとエンドポイントを確認できます。 それらは、リソースの**クイック スタート** ページの**リソース管理**の下にあります。 

#### <a name="version-30-preview"></a>[Version 3.0-preview](#tab/version-3)

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/sentiment`

#### <a name="version-21"></a>[バージョン 2.1](#tab/version-2)

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v2.1/sentiment`

---

要求ヘッダーに Text Analytics API キーが含まれるように設定します。 要求本文で、この分析のために準備した JSON ドキュメントのコレクションを提供します。

### <a name="example-sentiment-analysis-request"></a>感情分析要求の例 

感情分析のために送信するコンテンツの例を次に示します。 要求の形式は、どちらのバージョンの API でも同じです。
    
```json
{
    "documents": [
    {
        "language": "en",
        "id": "1",
        "text": "Hello world. This is some input text that I love."
    },
    {
        "language": "en",
        "id": "2",
        "text": "It's incredibly sunny outside! I'm so happy."
    }
    ],
}
```

### <a name="post-the-request"></a>要求を投稿する

要求が受信されると分析が実行されます。 分単位および秒単位で送信できる要求のサイズと数については、概要の「[data limits (データ制限)](../overview.md#data-limits)」セクションを参照してください。

Text Analytics API はステートレスです。 データはアカウントに保存されず、結果がすぐに応答で返されます。


### <a name="view-the-results"></a>結果の確認

センチメント アナライザーでは、テキストが主に正または負に分類されます。 0 ～ 1 の範囲のスコアが割り当てられます。 0\.5 に近い値は、中立または不確定です。 0\.5 のスコアは、中立性を示します。 文字列の感情分析をすることができない場合、またはセンチメントがない場合、スコアは必ず正確に 0.5 になります。 たとえば、英語の言語コードでスペイン語の文字列を渡した場合、スコアは 0.5 になります。

出力はすぐに返されます。 JSON を受け入れるアプリケーションに結果をストリーミングすることも、出力をローカル システム上のファイルに保存することもできます。 次に、データの並べ替え、検索、および操作に使用できるアプリケーション内に出力をインポートします。 多言語と絵文字のサポートにより、応答にはテキスト オフセットが含まれる場合があります。 詳細については[オフセットの処理方法](../concepts/text-offsets.md)に関するページを参照してください。

#### <a name="version-30-preview"></a>[Version 3.0-preview](#tab/version-3)

### <a name="sentiment-analysis-v3-example-response"></a>感情分析 v3 の応答の例

感情分析 v3 からの応答には、分析された各文およびドキュメントのセンチメント ラベルとセンチメント スコアが記載されています。 ドキュメントのセンチメント ラベルが `mixed` の場合、`documentScores` は返されません。

```json
{
    "documents": [
        {
            "id": "1",
            "sentiment": "positive",
            "documentScores": {
                "positive": 0.98570585250854492,
                "neutral": 0.0001625834556762,
                "negative": 0.0141316400840878
            },
            "sentences": [
                {
                    "sentiment": "neutral",
                    "sentenceScores": {
                        "positive": 0.0785155147314072,
                        "neutral": 0.89702343940734863,
                        "negative": 0.0244610067456961
                    },
                    "offset": 0,
                    "length": 12
                },
                {
                    "sentiment": "positive",
                    "sentenceScores": {
                        "positive": 0.98570585250854492,
                        "neutral": 0.0001625834556762,
                        "negative": 0.0141316400840878
                    },
                    "offset": 13,
                    "length": 36
                }
            ]
        },
        {
            "id": "2",
            "sentiment": "positive",
            "documentScores": {
                "positive": 0.89198976755142212,
                "neutral": 0.103382371366024,
                "negative": 0.0046278294175863
            },
            "sentences": [
                {
                    "sentiment": "positive",
                    "sentenceScores": {
                        "positive": 0.78401315212249756,
                        "neutral": 0.2067587077617645,
                        "negative": 0.0092281140387058
                    },
                    "offset": 0,
                    "length": 30
                },
                {
                    "sentiment": "positive",
                    "sentenceScores": {
                        "positive": 0.99996638298034668,
                        "neutral": 0.0000060341349126,
                        "negative": 0.0000275444017461
                    },
                    "offset": 31,
                    "length": 13
                }
            ]
        }
    ],
    "errors": []
}
```

#### <a name="version-21"></a>[バージョン 2.1](#tab/version-2)

### <a name="sentiment-analysis-v2-example-response"></a>感情分析 v2 の応答の例

感情分析 v2 からの応答には、送信された各ドキュメントのセンチメント スコアが記載されています。

```json
{
  "documents": [{
    "id": "1",
    "score": 0.98690706491470337
  }, {
    "id": "2",
    "score": 0.95202046632766724
  }],
  "errors": []
}
```

---

## <a name="summary"></a>まとめ

この記事では、Text Analytics API を使用した感情分析の概念とワークフローについて説明しました。 要約すると:

+ 感情分析では、選択した言語に対して 2 つのバージョンを使用できます。
+ 要求本文内の JSON ドキュメントには、ID、テキスト、および言語のコードが含まれます。
+ POST 要求は、ユーザーのサブスクリプションで有効な、個人用に設定された[アクセス キーとエンドポイント](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource)を使用して `/sentiment` エンドポイントに対して行われます。
+ ドキュメント ID ごとのセンチメント スコアで構成される応答出力は、JSON を受け入れるすべてのアプリにストリーミングすることができます。 たとえば、Excel や Power BI です。

## <a name="see-also"></a>関連項目

* [Text Analytics の概要](../overview.md)
* [Text Analytics クライアント ライブラリの使用](../quickstarts/text-analytics-sdk.md)
* [新機能](../whats-new.md)
