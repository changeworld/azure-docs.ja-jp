---
title: Text Analytics API の新機能
titleSuffix: Text Analytics - Azure Cognitive Services
description: この記事では、Azure Cognitive Services Text Analytics の新しいリリースや機能に関する情報を提供します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 06/03/2020
ms.author: aahi
ms.openlocfilehash: 7ab886b65e665bdd38e2461e49fa051213602f0e
ms.sourcegitcommit: 813f7126ed140a0dff7658553a80b266249d302f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/06/2020
ms.locfileid: "84462798"
---
# <a name="whats-new-in-the-text-analytics-api"></a>Text Analytics API の新機能

Text Analytics API は継続的に更新されます。 常に最新の開発情報を把握していただけるよう、この記事では新しいリリースと機能に関する情報を提供します。

## <a name="may-2020"></a>2020 年 5 月

### <a name="text-analytics-api-v3-general-availability"></a>Text Analytics API v3 の一般提供

Text Analysis API v3 の一般提供が開始されました。次の点が更新されています。

* モデル バージョン `2020-04-01`
* 各機能の[データ制限](concepts/data-limits.md)を更新
* [感情分析 (SA) v3](how-tos/text-analytics-how-to-sentiment-analysis.md) の[言語のサポート](language-support.md)を更新
* エンティティ リンク設定に対する個別のエンドポイント 
* [固有表現認識 (NER) v3](how-tos/text-analytics-how-to-entity-linking.md) の新しい "Address" エンティティ カテゴリ
* NER v3 の新しい下位カテゴリ:
   * Location - Geographical (場所 - 地理)
   * Location - Structural (場所 - 構造)
   * Organization - Stock Exchange (組織 - 証券取引所)
   * Organization - Medical (組織 - 医療)
   * Organization - Sports (組織 - スポーツ)
   * Event - Cultural (イベント - 文化)
   * Event - Natural (イベント - 自然)
   * Event - Sports (イベント - スポーツ)

JSON 応答に次のプロパティが追加されました。
   * `SentenceText` (感情分析)
   * `Warnings` (各ドキュメント) 

JSON 応答に含まれる次のプロパティの名前が変更されています (該当する場合)。

* `score` の名前が `confidenceScore` に変更されました
    * `confidenceScore` の小数点以下の有効桁数が 2 桁に変更されました。 
* `type` の名前が `category` に変更されました
* `subtype` の名前が `subcategory` に変更されました

[!INCLUDE [v3 region availability](includes/v3-region-availability.md)]

> [!div class="nextstepaction"]
> [Text Analytics API v3 についての詳細](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Languages)

### <a name="text-analytics-api-v31-public-preview"></a>Text Analytics API v3.1 パブリック プレビュー
   * 新しい感情分析機能 - [意見マイニング](how-tos/text-analytics-how-to-sentiment-analysis.md#opinion-mining)
   * 保護された医療情報 (`PHI`) 用の新しい[個人情報 (`PII`) ドメイン フィルター](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features)。
   * 新しい個人情報 (`PII`) カテゴリ:
      * 国際疾病分類 (ICD-9-CM)
      * 国際疾病分類 (ICD-10-CM)

> [!div class="nextstepaction"]
> [Text Analytics API v3.1 プレビューについての詳細](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-1/operations/Languages)

## <a name="february-2020"></a>2020 年 2 月

### <a name="sdk-support-for-text-analytics-api-v3-public-preview"></a>Text Analytics API v3 の SDK サポートのパブリック プレビュー

[統合された Azure SDK リリース](https://techcommunity.microsoft.com/t5/azure-sdk/january-2020-unified-azure-sdk-release/ba-p/1097290)の一部として、Text Analytics API v3 SDK は、次のプログラミング言語のパブリック プレビューとして使用できるようになりました。
   * [C#](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-csharp)
   * [Python](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-python)
   * [JavaScript (Node.js)](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-javascript)
   * [Java](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-java)
   
   > [!div class="nextstepaction"]
> [Text Analytics API v3 SDK についての詳細](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3)

### <a name="named-entity-recognition-v3-public-preview"></a>名前付きエンティティの認識 v3 パブリック プレビュー

テキスト内で見つかる一般エンティティおよび個人情報エンティティの検出を拡張するため、名前付きエンティティの認識 (NER) v3 パブリック プレビュー サービスで追加のエンティティ型を使用できるようになりました。 この更新では、次のような[モデル バージョン](concepts/model-versioning.md) `2020-02-01` が導入されています。

* 次の一般エンティティ型の認識 (英語のみ):
    * PersonType
    * Product
    * Event
    * Location のサブタイプとしての Geopolitical Entity (GPE)
    * スキル

* 次の個人情報エンティティ型の認識 (英語のみ):
    * Person
    * Organization
    * Quantity のサブタイプとしての Age
    * DateTime のサブタイプとしての Date
    * Email 
    * Phone Number (米国のみ)
    * URL
    * IP アドレス

> [!div class="nextstepaction"]
> [名前付きエンティティの認識 v3 に関する詳細](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features)

### <a name="october-2019"></a>2019 年 10 月

#### <a name="named-entity-recognition-ner"></a>名前付きエンティティの認識 (NER)

* 個人情報エンティティ型の認識に対する[新しいエンドポイント](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesRecognitionPii) (英語のみ)

* [エンティティの認識](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesRecognitionGeneral)と[エンティティのリンク](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesLinking)に対する個別のエンドポイント。

* [モデル バージョン](concepts/model-versioning.md) `2019-10-01`。次のものが含まれます。
    * テキストで見つかるエンティティの検出と分類の拡張。 
    * 次の新しいエンティティ型の認識:
        * 電話番号
        * IP アドレス

エンティティのリンクでは、英語とスペイン語がサポートされています。 NER の言語サポートは、エンティティ型によって異なります。

#### <a name="sentiment-analysis-v3-public-preview"></a>感情分析 v3 のパブリック プレビュー

* センチメントを分析するための[新しいエンドポイント](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/Sentiment)。
* [モデル バージョン](concepts/model-versioning.md) `2019-10-01`。次のものが含まれます。

    * API のテキストの分類とスコア付けの精度および詳細の大幅な改善。
    * テキスト内のさまざまなセンチメントに対する自動ラベル付け。
    * ドキュメントおよび文レベルでの感情分析と出力。 

英語 (`en`)、日本語 (`ja`)、簡体中国語 (`zh-Hans`)、繁体中国語 (`zh-Hant`)、フランス語 (`fr`)、イタリア語 (`it`)、スペイン語 (`es`)、オランダ語 (`nl`)、ポルトガル語 (`pt`)、ドイツ語 (`de`) がサポートされており、次のリージョンで利用できます: `Australia East`、`Central Canada`、`Central US`、`East Asia`、`East US`、`East US 2`、`North Europe`、`Southeast Asia`、`South Central US`、`UK South`、`West Europe`、`West US 2`。 

> [!div class="nextstepaction"]
> [感情分析 v3 の詳細情報](how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features)

## <a name="next-steps"></a>次のステップ

* [Text Analytics API とは](overview.md)  
* [ユーザー シナリオの例](text-analytics-user-scenarios.md)
* [感情分析](how-tos/text-analytics-how-to-sentiment-analysis.md)
* [言語検出](how-tos/text-analytics-how-to-language-detection.md)
* [エンティティの認識](how-tos/text-analytics-how-to-entity-linking.md)
* [キー フレーズ抽出](how-tos/text-analytics-how-to-keyword-extraction.md)
