---
title: Azure Synapse Analytics での Cognitive Services
description: Azure Cognitive Services からの事前トレーニング済みモデルを使用して、Azure Synapse Analytics の人工知能 (AI) でご利用のデータを強化します。
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: overview
ms.reviewer: jrasnick, garye, negust, ruxu
ms.date: 06/30/2021
author: garyericson
ms.author: garye
ms.custom: ignite-fall-2021
ms.openlocfilehash: e69730f486303fa508fed4cd53f8df3ee76433ba
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132318467"
---
# <a name="cognitive-services-in-azure-synapse-analytics"></a>Azure Synapse Analytics での Cognitive Services

Azure Cognitive Services からの事前トレーニング済みモデルを使用すると、Azure Synapse Analytics の人工知能 (AI) でご利用のデータを強化できます。

[Azure Cognitive Services](../../cognitive-services/what-are-cognitive-services.md) はクラウドベースのサービスであり、これによって、AI またはデータ サイエンスのスキルを持っていない場合でも、ご利用のデータにコグニティブなインテリジェンスを追加できます。 Synapse Analytics でご利用のデータに対してこれらのサービスを使用する方法はいくつかあります。

- Synapse Analytics の Cognitive Services ウィザードを使用すると、Spark テーブルのデータを使用してコグニティブ サービスに接続する PySpark コードが Synapse ノートブック内に生成されます。 その後、そのサービスでは、トレーニング済みの機械学習モデルを使用して、データに AI を追加するための処理をお客様に代わって行います。 詳細については、[感情分析ウィザード](tutorial-cognitive-services-sentiment.md)と[異常検出ウィザード](tutorial-cognitive-services-anomaly.md)について確認してください。

- Synapse Machine Learning ([SynapseML](https://github.com/microsoft/SynapseML)) を使用すると、効果的で拡張性の高い予測と分析のモデルをさまざまな Spark データ ソースから構築できます。 Synapse Spark には、[Spark での Cognitive Services](https://github.com/microsoft/SynapseML/blob/master/docs/cogsvc.md) などの組み込みの SynapseML ライブラリが用意されています。 詳細については、[チュートリアル](#tutorials)を参照してください。

- ウィザードによって生成された PySpark コード、またはチュートリアルで用意されているサンプル SynapseML コードから始めることで、ご利用のデータに対して他のコグニティブ サービスを使用するための独自のコードを作成できます。 利用可能なサービスの詳細については、「[Azure Cognitive Services とは](../../cognitive-services/what-are-cognitive-services.md)」を参照してください。




## <a name="get-started"></a>はじめに

チュートリアル「[Azure Synapse Analytics で Cognitive Services を使用するための前提条件](tutorial-configure-cognitive-services-synapse.md)」では、Synapse Analytics で Cognitive Services を使用する前に行う必要があるいくつかの手順について説明します。

## <a name="tutorials"></a>チュートリアル

次のチュートリアルでは、Synapse Analytics で Cognitive Services を使用する完全な例を示します。

- [Cognitive Services を使用した感情分析](tutorial-cognitive-services-sentiment.md) - 顧客コメントのサンプル データ セットを使用して、各行でコメントのセンチメントが示される列を含む Spark テーブルを作成します。

- [Cognitive Services による異常検出](tutorial-cognitive-services-anomaly.md) - 時系列データのサンプル データ セットを使用して、各行のデータが異常であるかどうかを示す列を含む Spark テーブルを作成します。

- [Microsoft Machine Learning for Apache Spark を使用して機械学習アプリケーションを構築する](tutorial-build-applications-use-mmlspark.md) - このチュートリアルでは、SynapseML を使用して Cognitive Services から複数のモデルにアクセスする方法を示します。

- 「[Form Recognizer AI サービスの使用](tutorial-form-recognizer-use-mmlspark.md)」では、[Form Recognizer](../../applied-ai-services/form-recognizer/index.yml)を使用してフォームとドキュメントを分析し、Azure Synapse Analytics でテキストとデータを抽出する方法を示します。 

- 「[Cognitive Service を使用した Text Analytics](tutorial-text-analytics-use-mmlspark.md)」では、[Text Analytics](../../cognitive-services/text-analytics/index.yml) を使用して、Azure Synapse Analytics の非構造化テキストを分析する方法を説明します。

- 「[Cognitive Service を使用した Translator](tutorial-translator-use-mmlspark.md)」では、Azure Synapse Analytics でインテリジェントな多言語ソリューションを構築するための、[Translator](../../cognitive-services/Translator/index.yml) の使用方法について説明します。

- 「[Cognitive Service を使用した Computer Vision](tutorial-computer-vision-use-mmlspark.md)」では、[Computer Vision](../../cognitive-services/computer-vision/index.yml) を使用して Azure Synapse Analytics 上のイメージを分析する方法について説明します。

## <a name="available-cognitive-services-apis"></a>利用可能な Cognitive Services APIs
### <a name="bing-image-search"></a>Bing Image Search

| API の種類                                   | SynapseML API                  | Cognitive Service API (バージョン)                                                                                               | DEP VNet のサポート |
| ------------------------------------------ | ------------------------------ | ------------------------------------------------------------------------------------------------------------------------------- | ---------------- |
|Bing Image Search|BingImageSearch|画像 - Visual Search V7.0| サポートされていません|

### <a name="anomaly-detector"></a>Anomaly Detector

| API の種類                                   | SynapseML API                  | Cognitive Service API (バージョン)                                                                                               | DEP VNet のサポート |
| ------------------------------------------ | ------------------------------ | ------------------------------------------------------------------------------------------------------------------------------- | ---------------- |
| 前回の異常の検出                        | DetectLastAnomaly              | Detect Last Point V1.0                                                                                                          | サポートされています        |
| 異常の検出                           | DetectAnomalies                | Detect Entire Series V1.0                                                                                                       | サポートされています        |
| 単純な検出の異常                     | SimpleDetectAnomalies          | Detect Entire Series V1.0                                                                                                       | サポートされています        |

### <a name="computer-vision"></a>Computer Vision

| API の種類                                   | SynapseML API                  | Cognitive Service API (バージョン)                                                                                               | DEP VNet のサポート |
| ------------------------------------------ | ------------------------------ | ------------------------------------------------------------------------------------------------------------------------------- | ---------------- |
| OCR                                        | OCR                            | Recognize Printed Text V2.0                                                                                                     | サポートされています        |
| テキスト認識                             | RecognizeText                  | Recognize Text V2.0                                                                                                             | サポートされています        |
| 画像の読み取り                                 | ReadImage                      | Read V3.1                                                                                                                       | サポートされています        |
| サムネイルの生成                        | GenerateThumbnails             | Generate Thumbnail V2.0                                                                                                         | サポートされています        |
| Analyze Image (画像を分析する)                              | AnalyzeImage                   | Analyze Image V2.0                                                                                                              | サポートされています        |
| ドメイン固有のコンテンツの認識          | RecognizeDomainSpecificContent | Analyze Image By Domain V2.0                                                                                                    | サポートされています        |
| タグ イメージ                                  | TagImage                       | Tag Image V2.0                                                                                                                  | サポートされています        |
| Describe Image (画像を説明する)                             | DescribeImage                  | Describe Image V2.0                                                                                                             | サポートされています        |


### <a name="translator"></a>[変換者]

| API の種類                                   | SynapseML API                  | Cognitive Service API (バージョン)                                                                                               | DEP VNet のサポート |
| ------------------------------------------ | ------------------------------ | ------------------------------------------------------------------------------------------------------------------------------- | ---------------- |
| テキストの翻訳                             | Translate                      | Translate V3.0                                                                                                                  | サポートされていません    |
| テキストの表記変換                         | Transliterate                  | Transliterate V3.0                                                                                                              | サポートされていません    |
| 言語の検出                            | Detect                         | Detect V3.0                                                                                                                     | サポートされていません    |
| 文の分割                             | BreakSentence                  | Break Sentence V3.0                                                                                                             | サポートされていません    |
| 辞書検索 (翻訳の代替候補) | DictionaryLookup               | Dictionary Lookup V3.0                                                                                                          | サポートされていません    |
| ドキュメント変換                       | DocumentTranslator             | Document Translation V1.0                                                                                                       | サポートされていません    |


### <a name="face"></a>Face

| API の種類                                   | SynapseML API                  | Cognitive Service API (バージョン)                                                                                               | DEP VNet のサポート |
| ------------------------------------------ | ------------------------------ | ------------------------------------------------------------------------------------------------------------------------------- | ---------------- |
| 顔の検出                                | DetectFace                     | Detect With Url V1.0                                                                                                            | サポートされています        |
| 似た顔の検索                          | FindSimilarFace                | Find Similar V1.0                                                                                                               | サポートされています        |
| 顔のグループ化                                | GroupFaces                     | Group V1.0                                                                                                                      | サポートされています        |
| 顔の識別                             | IdentifyFaces                  | Identify V1.0                                                                                                                   | サポートされています        |
| 顔の確認                               | VerifyFaces                    | Verify Face To Face V1.0                                                                                                        | サポートされています        |

### <a name="form-recognizer"></a>Form Recognizer
| API の種類                                   | SynapseML API                  | Cognitive Service API (バージョン)                                                                                               | DEP VNet のサポート |
| ------------------------------------------ | ------------------------------ | ------------------------------------------------------------------------------------------------------------------------------- | ---------------- |
| Analyze Layout                             | AnalyzeLayout                  | Analyze Layout Async V2.1                                                                                                       | サポートされています        |
| 領収書の分析                           | AnalyzeReceipts                | Analyze Receipt Async V2.1                                                                                                      | サポートされています        |
| 名刺の分析                     | AnalyzeBusinessCards           | Analyze Business Card Async V2.1                                                                                                | サポートされています        |
| 請求書の分析                           | AnalyzeInvoices                | Analyze Invoice Async V2.1                                                                                                      | サポートされています        |
| 身分証明書の分析                       | AnalyzeIDDocuments             | identification (ID) document model V2.1                                                                                         | サポートされています        |
| カスタム モデルの一覧表示                         | ListCustomModels               | List Custom Models V2.1                                                                                                         | サポートされています        |
| カスタム モデルの取得                           | GetCustomModel                 | Get Custom Models V2.1                                                                                                          | サポートされています        |
| カスタム モデルの分析                       | AnalyzeCustomModel             | Analyze With Custom Model V2.1                                                                                                  | サポートされています        |

### <a name="speech-to-text"></a>音声テキスト変換
| API の種類                                   | SynapseML API                  | Cognitive Service API (バージョン)                                                                                               | DEP VNet のサポート |
| ------------------------------------------ | ------------------------------ | ------------------------------------------------------------------------------------------------------------------------------- | ---------------- |
| 音声テキスト変換                             | SpeechToText                   | SpeechToText V1.0 |  サポートされていません    |
| 音声テキスト変換 SDK                         | SpeechToTextSDK                | Using Speech SDK Version 1.14.0                                                                                                 | サポートされていません    |


### <a name="text-analytics"></a>Text Analytics

| API の種類                                   | SynapseML API                  | Cognitive Service API (バージョン)                                                                                               | DEP VNet のサポート |
| ------------------------------------------ | ------------------------------ | ------------------------------------------------------------------------------------------------------------------------------- | ---------------- |
| テキスト センチメント V2                          | TextSentimentV2                | Sentiment V2.0                                                                                                                  | サポートされています        |
| Language Detector V2                       | LanguageDetectorV2             | Languages V2.0                                                                                                                  | サポートされています        |
| Entity Detector V2                         | EntityDetectorV2               | Entities Linking V2.0                                                                                                           | サポートされています        |
| NER V2                                     | NERV2                          | Entities Recognition General V2.0                                                                                               | サポートされています        |
| Key Phrase Extractor V2                    | KeyPhraseExtractorV2           | Key Phrases V2.0                                                                                                                | サポートされています        |
| テキスト センチメント                             | TextSentiment                  | Sentiment V3.1                                                                                                                  | サポートされています        |
| Key Phrase Extractor                       | KeyPhraseExtractor             | Key Phrases V3.1                                                                                                                | サポートされています        |
| PII                                        | PII                            | Entities Recognition Pii V3.1                                                                                                   | サポートされています        |
| NER                                        | NER                            | Entities Recognition General V3.1                                                                                               | サポートされています        |
| Language Detector                          | LanguageDetector               | Languages V3.1                                                                                                                  | サポートされています        |
| Entity Detector                            | EntityDetector                 | Entities Linking V3.1                                                                                                           | サポートされています        |


## <a name="next-steps"></a>次のステップ

- [Azure Synapse Analytics の機械学習機能](what-is-machine-learning.md)
- [Cognitive Services とは](../../cognitive-services/what-are-cognitive-services.md)
- [Synapse Analytics ギャラリーからのサンプル ノートブックを使用する](quickstart-gallery-sample-notebook.md)
