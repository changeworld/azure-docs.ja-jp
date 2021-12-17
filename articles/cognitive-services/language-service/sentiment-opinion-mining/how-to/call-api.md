---
title: 感情分析とオピニオン マイニングの実行方法
titleSuffix: Azure Cognitive Services
description: この記事では、テキスト内の感情の検出とオピニオン マイニングの方法について説明します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: sample
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-sentiment-opinion-mining, ignite-fall-2021
ms.openlocfilehash: e94918baac4946194e2382c3a336bdd52c510f50
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132713575"
---
# <a name="how-to-use-sentiment-analysis-and-opinion-mining"></a>方法: 感情分析とオピニオン マイニングを使用する 

感情分析とオピニオン マイニングの 2 つは、プラスとマイナスのセンチメントを検出する方法です。 感情分析を使用すると、文やドキュメントのレベルでセンチメント ラベル ("negative"、"neutral"、"positive" など) と信頼度スコアが得られます。 オピニオン マイニングでは、テキスト内の単語 (製品やサービスの特性など) に関連する意見について、詳細な情報が得られます。

> [!TIP]
> [クイック スタートの記事](../quickstart.md)に従って、この機能を使い始めることができます。 また、[Language Studio](../../language-studio.md) を使うと、コードを記述しなくても、要求の例を作成できます。


## <a name="sentiment-analysis"></a>感情分析

感情分析では、文とドキュメントのレベルで返されるセンチメント ラベルとそれぞれの信頼度スコアがテキストに適用されます。 

ラベルは、*positive*、*negative*、および *neutral* です。 ドキュメント レベルでは、*mixed* センチメント ラベルが返されることもあります。 ドキュメントのセンチメントは、次のように決定されます。

| 文章のセンチメント                                                                            | 返されるドキュメントのラベル |
|-----------------------------------------------------------------------------------------------|-------------------------|
| ドキュメントに `positive` の文が少なくとも 1 つ含まれている。 残りの文は `neutral` である。 | `positive`              |
| ドキュメントに `negative` の文が少なくとも 1 つ含まれている。 残りの文は `neutral` である。 | `negative`              |
| ドキュメントに、`negative` の文が少なくとも 1 つ、`positive` の文が少なくとも 1 つ含まれている。    | `mixed`                 |
| ドキュメント内のすべての文が `neutral` である。                                                  | `neutral`               |

信頼度スコアは、1 から 0 の範囲で割り当てられます。 スコアが 1 に近いほど、ラベルの分類の信頼度が高いことを示し、スコアが低いほど信頼度が低いことを示します。 各ドキュメントまたは各文で、ラベル (positive、negative、neutral) に関連付けられた予測スコアは合計で 1 になります。 詳細については、[責任ある AI の透明性に関するメモ](/legal/cognitive-services/text-analytics/transparency-note?context=/azure/cognitive-services/text-analytics/context/context)を参照してください。 

## <a name="opinion-mining"></a>オピニオン マイニング

オピニオン マイニングは、感情分析の機能の 1 つです。 この機能は、自然言語処理 (NLP) ではアスペクトベースの感情分析とも呼ばれます。テキストに含まれる製品やサービスの属性に関連した意見について、より粒度の細かい情報が得られます。 API は、ターゲット (名詞または動詞) と評価 (形容詞) として意見をクローズアップします。

たとえば、あるホテルについて "The room was great, but the staff was unfriendly (部屋はすばらしいのですが、スタッフが無愛想でした)" のようなフィードバックを利用者が残した場合、そのテキストに含まれるターゲット (アスペクト) と、それに関連する評価 (意見) と感情がオピニオン マイニングによって特定されます。 感情分析では、否定的な感情のみが報告される可能性があります。

:::image type="content" source="../media/opinion-mining.png" alt-text="オピニオン マイニング例の図" lightbox="../media/opinion-mining.png":::

REST API を使用してオピニオン マイニングの結果を取得するには、感情分析の要求に `opinionMining=true` フラグを含める必要があります。 オピニオン マイニングの結果が感情分析の応答に含められます。 オピニオン マイニングは感情分析の拡張機能であり、現在の[価格レベル](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/)に含まれています。

## <a name="determine-how-to-process-the-data-optional"></a>データの処理方法を決定する (省略可能)

### <a name="specify-the-sentiment-analysis-model"></a>感情分析モデルを指定する

既定では、感情分析には、そのテキストで使用できる最新の AI モデルが使われます。 特定のバージョンのモデルを使うように API 要求を構成することもできます。 指定したモデルが、感情分析操作を実行するために使われます。

| サポートされているバージョン | 最新の一般公開バージョン | 最新のプレビュー バージョン |
|--|--|--|
| `2019-10-01`, `2020-04-01`, `2021-10-01-preview` | `2020-04-01`   | `2021-10-01-preview`   |

### <a name="using-a-preview-model-version"></a>プレビュー モデル バージョンの使用

API 呼び出しでプレビュー モデル バージョンを使用するには、model version パラメーターを使用してモデル バージョンを指定する必要があります。 たとえば、Python を使用して要求を送信する場合は、次のようになります。

```python
result = text_analytics_client.analyze_sentiment(documents, show_opinion_mining=True, model_version="2021-10-01-preview")
```

または、REST API を使用する場合は、次のようになります。

```rest
https://your-resource-name.cognitiveservices.azure.com/text/analytics/v3.1/sentiment?opinionMining=true&model-version=2021-10-01-preview
```

詳細については、リファレンス ドキュメントを参照してください。
* [REST API](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1/operations/Sentiment)
* [.NET](/dotnet/api/azure.ai.textanalytics.analyzesentimentaction?view=azure-dotnet#properties)
* [Python](/python/api/azure-ai-textanalytics/azure.ai.textanalytics.textanalyticsclient?view=azure-python#analyze-sentiment-documents----kwargs-)
* [Java](/java/api/com.azure.ai.textanalytics.models.analyzesentimentoptions.setmodelversion?view=azure-java-stable#com_azure_ai_textanalytics_models_AnalyzeSentimentOptions_setModelVersion_java_lang_String_)
* [JavaScript](/javascript/api/@azure/ai-text-analytics/analyzesentimentoptions?view=azure-node-latest)

### <a name="input-languages"></a>入力言語

感情分析で処理されるドキュメントを送信するときに、[サポートされている言語](../language-support.md)のどれでそれが書かれているかを指定できます。 言語を指定しないと、感情分析には既定で英語が使われます。 さまざまな[多言語と絵文字のエンコード](../../concepts/multilingual-emoji-support.md)をサポートするため、API からの応答でオフセットが返される場合があります。 

## <a name="submitting-data"></a>データの送信

感情分析とオピニオン マイニングでは、作業するテキストの分量が小さいほど、高い品質の結果が得られます。 これは、テキストのブロックが大きいほどパフォーマンスが向上する、キー フレーズ抽出などの一部の機能とは逆です。 

API 要求を送信するには、言語リソースのエンドポイントとキーが必要です。

> [!NOTE]
> 言語リソースのキーとエンドポイントは、Azure portal で確認できます。 それらは、リソースの **[Key and endpoint]\(キーとエンドポイント\)** ページの **[リソース管理]** にあります。 

要求が受信されると分析が実行されます。 分単位および秒単位で送信できる要求のサイズと数については、以下のデータ制限に関するセクションをご覧ください。

感情分析とオピニオン マイニングの機能の同期的な使用はステートレスです。 データはアカウントに保存されず、結果がすぐに応答で返されます。

これらの機能を非同期的に使うと、API の結果は、応答で示される要求取り込み時刻から 24 時間利用できます。 この時間が経過すると、結果は消去され、取得できなくなります。

## <a name="getting-sentiment-analysis-and-opinion-mining-results"></a>感情分析とオピニオン マイニングの結果を取得する

API から結果を受け取るとき、返されるキー フレーズの順序は、モデルによって内部的に決定されます。 結果をアプリケーションにストリーミングすることも、ローカル システムのファイルに出力を保存することもできます。

感情分析は、ドキュメント全体およびそこに含まれるそれぞれの文について、センチメント ラベルと信頼度スコアを返します。 スコアが 1 に近いほど、ラベルの分類の信頼度が高いことを示し、スコアが低いほど信頼度が低いことを示します。 ドキュメントには複数の文が含まれる場合があり、それぞれのドキュメントまたは文の範囲内での信頼度スコアの合計は 1 になります。

テキスト内のターゲット (名詞または動詞) とそれに関連する評価 (形容詞) がオピニオン マイニングによって特定されます。 たとえば、"*The restaurant had great food and our server was friendly*" という文には、*food* と *server* という 2 つのターゲットが存在します。 各ターゲットに評価が存在します。 たとえば、*food* の評価は *great* であり、*server* の評価は *friendly* です。

API は、ターゲット (名詞または動詞) と評価 (形容詞) として意見を返します。


## <a name="data-limits"></a>データ制限

> [!NOTE]
> * 制限を超えるドキュメントを分析する必要がある場合は、テキストを小さなチャンクに分割してから API に送信することができます。 
> * ドキュメントとは、テキスト文字の 1 つの文字列です。  

| 制限 | 値 |
|------------------------|---------------|
| 1 つのドキュメントの最大サイズ (同期) | [StringInfo.LengthInTextElements](/dotnet/api/system.globalization.stringinfo.lengthintextelements) によって計測された 5,120 文字。  |
| 要求ごとの最大文字数 (非同期) | 送信ドキュメント全体で 125,000 文字。[StringInfo.LengthInTextElements](/dotnet/api/system.globalization.stringinfo.lengthintextelements) によって計測されます。  |
| 要求全体の最大サイズ | 1 MB。 |
| 要求あたりのドキュメントの最大数 | 10 |

ドキュメントが文字数制限を超える場合の API の動作は、使っている機能により異なります。

* 非同期:
  * いずれかのドキュメントが最大サイズを超えている場合、API は要求全体を拒否し、`400 bad request` エラーを返します。
* 同期:  
  * API は、最大サイズを超えるドキュメントの処理は行わず、無効なドキュメント エラーを返します。 API 要求に複数のドキュメントが含まれている場合、API は、それらが文字数制限内であれば処理を続行します。

1 つの要求で送信できるドキュメントの最大数を超えると、HTTP 400 エラー コードが生成されます。

### <a name="rate-limits"></a>転送率の制限

レート制限は[価格レベル](https://aka.ms/unifiedLanguagePricing)によって異なります。

| レベル          | 1 秒あたりの要求数 | 1 分あたりの要求数 |
|---------------|---------------------|---------------------|
| S/マルチサービス | 1000                | 1000                |
| S0/F0         | 100                 | 300                 |

## <a name="see-also"></a>関連項目

* [感情分析とオピニオン マイニングの概要](../overview.md)