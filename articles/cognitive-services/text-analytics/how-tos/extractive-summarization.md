---
title: Text Analytics 抽出要約 API を使用してテキストを要約する
titleSuffix: Azure Cognitive Services
description: この記事では、Azure Cognitive Services Text Analytics 抽出要約 API でテキストを要約する方法について説明します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 08/05/2021
ms.author: aahi
ms.openlocfilehash: f8db386da9890be2a07f201243dbbb4beaf5c499
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2021
ms.locfileid: "122444076"
---
# <a name="how-to-summarize-text-with-text-analytics-preview"></a>方法: Text Analytics を使用してテキストを要約する (プレビュー)

> [!IMPORTANT] 
> Text Analytics 抽出要約は、"現状のまま"、"保証なしで" 提供されるプレビュー機能です。 そのため、Text Analytics 抽出要約 (プレビュー) は、運用環境に実装またはデプロイして使用しないでください。 お客様は、Text Analytics 抽出要約の使用に関するすべての責任を持ちます。 

一般に、テキストの自動要約には、抽出と抽象化の 2 つの方法があります。 Text Analytics API では、バージョン `3.2-preview.1` 以降で抽出要約が提供されています

抽出要約は Azure Text Analytics の機能であり、元のコンテンツ内で最も重要性または関連性の高い情報をまとめて表す文を抽出することで概要を生成します。

この機能は、ユーザーが読むには長すぎると考えられるコンテンツを短縮するように設計されています。 抽出要約では、記事、論文、またはドキュメントが重要な文に要約されます。

API で使用される AI モデルはサービスによって提供されるため、分析対象のコンテンツを送信するだけで済みます。

## <a name="extractive-summarization-and-features"></a>抽出要約と機能

Text Analytics の抽出要約機能では、自然言語処理手法を使用して、非構造化テキスト ドキュメント内の重要な文が検索されます。 これらの文がまとめられて、ドキュメントの主要なアイデアが伝えられます。

抽出要約では、抽出された文と元のドキュメント内でのその位置と共に、システム応答の一部としてランク スコアが返されます。 ランク スコアは、ドキュメントの主要なアイデアに対して文がどの程度関係していると判断されるのかの指標です。 モデルでは、各文に対して 0 から 1 (両端を含む) のスコアが与えられ、要求ごとに最も高いスコアの文が返されます。 たとえば、3 文の要約を要求した場合、サービスからはスコアが最も高い 3 つの文が返されます。

Text Analytics には、[キー フレーズ抽出](./text-analytics-how-to-keyword-extraction.md)というもう 1 つの機能があり、重要な情報を抽出できます。 キー フレーズ抽出と抽出要約のどちらを使用するかを決めるときは、次の点を考慮します。
* キー フレーズ抽出からは語句が返されるのに対し、抽出要約からは文が返されます
* 抽出要約からは、文と共にランク スコアが返されます。 要求ごとに、ランクが上位の文が返されます

## <a name="sending-a-rest-api-request"></a>REST API 要求の送信 

> [!TIP]
> また、クライアント ライブラリの最新プレビュー バージョンを使用して、抽出要約を使用することもできます。 GitHub の次のサンプルを参照してください。 
> * [.NET](https://github.com/Azure/azure-sdk-for-net/blob/main/sdk/textanalytics/Azure.AI.TextAnalytics/samples/Sample8_ExtractSummary.md)
> * [Java](https://github.com/Azure/azure-sdk-for-java/blob/main/sdk/textanalytics/azure-ai-textanalytics/src/samples/java/com/azure/ai/textanalytics/lro/AnalyzeExtractiveSummarization.java)
> * [Python](https://github.com/Azure/azure-sdk-for-python/blob/main/sdk/textanalytics/azure-ai-textanalytics/samples/sample_extract_summary.py)

### <a name="preparation"></a>準備

Text Analytics の他の機能とは異なり、抽出要約機能は非同期専用の操作であり、/analyze エンドポイントを通してアクセスできます。 JSON 要求のデータは、「[/analyze エンドポイントへの非同期要求](./text-analytics-how-to-call-api.md?tabs=asynchronous#api-request-formats)」で説明されている形式に従う必要があります。

抽出要約では、ドキュメント入力に幅広い言語がサポートされています。 詳細については、[サポートされている言語](../language-support.md)に関するページを参照してください。

ドキュメントのサイズは、ドキュメントごとに 125,000 文字未満でなければなりません。 1 つのコレクションで許可されるドキュメントの最大数については、[データ制限](../concepts/data-limits.md?tabs=version-3)に関する記事を参照してください。 コレクションは、要求の本文で送信されます。

### <a name="structure-the-request"></a>要求を構造化する

POST 要求を作成します。 次の参照リンクにある [Postman](text-analytics-how-to-call-api.md) または **API テスト コンソール** を使用することで、簡単に要求を構成して送信できます。 

[テキスト要約の参照](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-2-preview-1/operations/Analyze)

### <a name="request-endpoints"></a>要求エンドポイント

Azure で Text Analytics リソースを使用して、抽出要約用の HTTPS エンドポイントを設定します。 次に例を示します。

> [!NOTE]
> Azure portal で Text Analytics リソースのキーとエンドポイントを確認できます。 それらは、リソースの **[Key and endpoint]\(キーとエンドポイント\)** ページにあります。 

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.2-preview.1/analyze`

要求ヘッダーに Text Analytics API キーが含まれるように設定します。 要求本文で、この分析のために準備した JSON ドキュメントのコレクションを提供します。

### <a name="example-request"></a>要求の例 

次に示すのは、要約のために送信するコンテンツの例です。これを、デモンストレーションのために[統合 AI のために包括的な表現](https://www.microsoft.com/research/blog/a-holistic-representation-toward-integrative-ai/)を使用して抽出します。 抽出要約 API を使用すると、はるかに長い入力テキストを受け取ることができます。 Text Analytics API のデータ制限の詳細については、[データ制限](../Concepts/data-limits.md)に関するページを参照してください。

1 つの要求に複数のドキュメントを含めることができます。 

ドキュメントごとに次のパラメーターがあります
* `id` では、ドキュメントを示します 
* `language` では、ドキュメントのソース言語を示し、既定値は `en` です
* `text` では、ドキュメントのテキストを添付します。

次のパラメーターは、1 つの要求内のすべてのドキュメントで共有されます。 これらのパラメーターは、要求の `tasks` 定義で指定できます。
* `model-version` では、使用するモデルのバージョンを指定し、既定値は `latest` です。 詳細については、[モデルのバージョン](../concepts/model-versioning.md)に関する記事を参照してください 
* `sentenceCount` では、返される文の数を指定し、既定値は `3` です。 範囲は 1 から 20 です。
* `sortBy` では、抽出された文が返される順序を指定します。 `sortBy` に指定できる値は `Offset` と `Rank` であり、既定値は `Offset` です。 値 `Offset` は、元のドキュメント内での文の開始位置です。    

```json
{
  "analysisInput": {
    "documents": [
      {
        "language": "en",
        "id": "1",
        "text": "At Microsoft, we have been on a quest to advance AI beyond existing techniques, by taking a more holistic, human-centric approach to learning and understanding. As Chief Technology Officer of Azure AI Cognitive Services, I have been working with a team of amazing scientists and engineers to turn this quest into a reality. In my role, I enjoy a unique perspective in viewing the relationship among three attributes of human cognition: monolingual text (X), audio or visual sensory signals, (Y) and multilingual (Z). At the intersection of all three, there’s magic—what we call XYZ-code as illustrated in Figure 1—a joint representation to create more powerful AI that can speak, hear, see, and understand humans better. We believe XYZ-code will enable us to fulfill our long-term vision: cross-domain transfer learning, spanning modalities and languages. The goal is to have pretrained models that can jointly learn representations to support a broad range of downstream AI tasks, much in the way humans do today. Over the past five years, we have achieved human performance on benchmarks in conversational speech recognition, machine translation, conversational question answering, machine reading comprehension, and image captioning. These five breakthroughs provided us with strong signals toward our more ambitious aspiration to produce a leap in AI capabilities, achieving multisensory and multilingual learning that is closer in line with how humans learn and understand. I believe the joint XYZ-code is a foundational component of this aspiration, if grounded with external knowledge sources in the downstream AI tasks."
      }
    ]
  },
  "tasks": {
    "extractiveSummarizationTasks": [
      {
        "parameters": {
          "model-version": "latest",
          "sentenceCount": 3,
          "sortBy": "Offset"
        }
      }
    ]
  }
}
```

### <a name="post-the-request"></a>要求を投稿する

抽出要約 API は、要求を受け取った時点で実行されます。 分単位および秒単位で送信できる要求のサイズと数については、概要の「[data limits (データ制限)](../overview.md#data-limits)」セクションを参照してください。

Text Analytics 抽出要約 API は非同期の API なので、応答オブジェクトにテキストは含まれません。 ただし、GET 要求を行ってジョブの状態と出力を確認するには、応答ヘッダーの `operation-location` キーの値が必要です。  POST 要求の応答ヘッダーの operation-location KEY 値の例を次に示します。

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.2-preview.1/analyze/jobs/<jobID>`

ジョブの状態を確認するには、POST 応答の operation-location KEY ヘッダー値の URL に対して GET 要求を行います。  ジョブの状態を反映するには、次の状態が使用されます: `NotStarted`、`running`、`succeeded`、`failed`、`rejected`。  

ジョブが成功した場合、API の出力が GET 要求の本文で返されます。 


### <a name="view-the-results"></a>結果の確認

GET 要求の応答の例を次に示します。  出力を取得に使用できるのは `expirationDateTime` (ジョブが作成されてから 24 時間) が経過するまでです。それを過ぎると、出力は消去されます。 多言語と絵文字のサポートにより、応答にはテキスト オフセットが含まれる場合があります。 詳細については[オフセットの処理方法](../concepts/text-offsets.md)に関するページを参照してください。

### <a name="example-response"></a>応答の例
  
抽出要約機能からは以下が返されます 

```json
{
    "jobId": "be437134-a76b-4e45-829e-9b37dcd209bf",
    "lastUpdateDateTime": "2021-06-11T05:43:37Z",
    "createdDateTime": "2021-06-11T05:42:32Z",
    "expirationDateTime": "2021-06-12T05:42:32Z",
    "status": "succeeded",
    "errors": [],
    "results": {
        "documents": [
            {
                "id": "1",
                "sentences": [
                    {
                        "text": "At Microsoft, we have been on a quest to advance AI beyond existing techniques, by taking a more holistic, human-centric approach to learning and understanding.",
                        "rankScore": 0.7673416137695312,
                        "Offset": 0,
                        "length": 160
                    },
                    {
                        "text": "In my role, I enjoy a unique perspective in viewing the relationship among three attributes of human cognition: monolingual text (X), audio or visual sensory signals, (Y) and multilingual (Z).",
                        "rankScore": 0.7644073963165283,
                        "Offset": 324,
                        "length": 192
                    },
                    {
                        "text": "At the intersection of all three, there’s magic—what we call XYZ-code as illustrated in Figure 1—a joint representation to create more powerful AI that can speak, hear, see, and understand humans better.",
                        "rankScore": 0.7623870968818665,
                        "Offset": 517,
                        "length": 203
                    }    
                ],
                "warnings": []
            }
        ],
        "errors": [],
        "modelVersion": "2021-08-01"
    }
}
```

## <a name="summary"></a>まとめ

この記事では、Text Analytics 抽出要約 API を使用した抽出要約の概念とワークフローについて説明しました。 抽出要約は、次の場合に使用することがあります。

* ドキュメントの処理を支援して効率を向上させます。
* 長いドキュメント、レポート、その他のテキスト フォームから重要な情報を収集します。
* ドキュメントで重要な文を強調表示します。
* ライブラリ内のドキュメントにすばやく目を通します。
* ニュース フィード コンテンツを生成します。

## <a name="see-also"></a>関連項目

* [Text Analytics の概要](../overview.md)
* [新機能](../whats-new.md)
* [モデルのバージョン](../concepts/model-versioning.md)
