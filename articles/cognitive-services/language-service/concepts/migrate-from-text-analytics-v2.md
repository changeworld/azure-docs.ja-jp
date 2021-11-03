---
title: Text Analytics API の v2 の移行ガイド
titleSuffix: Azure Cognitive Services
description: Text Analytics API のバージョン 3 を使用するためにアプリケーションを移行する方法について説明します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 07/06/2021
ms.author: aahi
ms.custom: ignite-fall-2021
ms.openlocfilehash: 396cac23548eb951e47c4ec3a602aadcdfa304f4
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131089805"
---
# <a name="migrate-from-version-2-of-the-text-analytics-api"></a>Text Analytics API のバージョン 2 の移行

アプリケーションで Text Analytics API のバージョン 2.1 を使用している場合、この記事は、アプリケーションをアップグレードして、[Azure Cognitive Service for Language](../overview.md) に含まれるようになった、最新バージョンの機能を使用する際に役立ちます。

## <a name="features"></a>特徴

以下のいずれかの機能を選んで、アプリケーションの更新に使用できる情報を確認してください。

## <a name="sentiment-analysis"></a>[感情分析](#tab/sentiment-analysis)

> [!TIP]
> 最新バージョンの API をアプリケーションに使用する場合、 [感情分析](../sentiment-opinion-mining/how-to/call-api.md)の方法の記事と、最新バージョンの API に関する[クイックスタート](../sentiment-opinion-mining/quickstart.md)を参照してください。 

## <a name="feature-changes"></a>機能の変更 

バージョン 2.1 の感情分析では、API に送信された各ドキュメントに対するセンチメント スコアが 0 から 1 の間で返されます。この場合、スコアは 1 に近いほど、より肯定的な感情を示します。 この機能の現在のバージョンでは、文とドキュメント全体の両方に対するセンチメント ラベル ("肯定的" や "否定的" など) と、それらに関連付けられた信頼度スコアが返されます。 

## <a name="steps-to-migrate"></a>移行手順

### <a name="rest-api"></a>REST API

アプリケーションで REST API が使用される場合は、その要求エンドポイントを、感情分析用の[現在のエンドポイント](../sentiment-opinion-mining/quickstart.md?pivots=rest-api)を使用するように更新します。 たとえば、`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1/sentiment` となります。 また、[API の応答](../sentiment-opinion-mining/how-to/call-api.md)で返されるセンチメント ラベルを使用するようアプリケーションを更新することも必要になります。 

JSON 応答の例については、リファレンス ドキュメントを参照してください。
* [バージョン 2.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9)
* [Version 3.0](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Sentiment) 
* [Version 3.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1/operations/Sentiment)

### <a name="client-libraries"></a>クライアント ライブラリ

最新バージョンの感情分析クライアント ライブラリを使用するには、`Azure.AI.TextAnalytics` 名前空間で最新のソフトウェア パッケージをダウンロードする必要があります。 [クイックスタートの記事](../sentiment-opinion-mining/quickstart.md)には、目的の言語に使用できるコマンドと共に、コード例が記載されています。

## <a name="ner-and-entity-linking"></a>[NER とエンティティ リンク設定](#tab/named-entity-recognition)

> [!TIP]
> 最新バージョンの API をアプリケーションに使用する場合、 API の現在のバージョンについて詳しくは、次の記事をご覧ください。NER:
> * [クイックスタート](../named-entity-recognition/quickstart.md)
> * [API を呼び出す方法](../named-entity-recognition/how-to-call.md) エンティティ リンク設定
> * [クイックスタート](../entity-linking/quickstart.md)
> * [API を呼び出す方法](../entity-linking/how-to/call-api.md)

## <a name="feature-changes"></a>機能の変更

バージョン 2.1 の Text Analytics API では、固有表現認識 (NER) とエンティティ リンク設定に 1 つのエンドポイントが使用されます。 この機能の現在のバージョンでは、名前付きエンティティの検出が拡張され、NER とエンティティ リンク設定の要求に個別のエンドポイントが使用されます。 さらに、[個人 (pii) と医療 (phi) の情報を検出](../personally-identifiable-information/overview.md)できる言語サービスで提供される他の機能も使用できます。 

## <a name="steps-to-migrate"></a>移行手順

### <a name="rest-api"></a>REST API

アプリケーションで REST API が使用される場合は、その要求エンドポイントを NER またはエンティティ リンク設定用の[現在のエンドポイント](../named-entity-recognition/quickstart.md?pivots=rest-api)に更新します。 たとえば、次のように入力します。

Entity Linking
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1/entities/linking`

NER
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1/entities/recognition/general`

また、[API の応答](../named-entity-recognition/how-to-call.md)で返される[エンティティ カテゴリ](../named-entity-recognition/concepts/named-entity-categories.md)を使用するようアプリケーションを更新することも必要になります。

JSON 応答の例については、リファレンス ドキュメントを参照してください。
* [バージョン 2.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634)
* [Version 3.0](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/EntitiesRecognitionGeneral) 
* [Version 3.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1/operations/EntitiesRecognitionGeneral)

### <a name="client-libraries"></a>クライアント ライブラリ

NER およびエンティティ リンク設定のクライアント ライブラリの最新バージョンを使用するには、`Azure.AI.TextAnalytics` 名前空間で最新のソフトウェア パッケージをダウンロードする必要があります。 [名前付きエンティティの認識](../named-entity-recognition/quickstart.md)と[エンティティ リンク設定](../entity-linking/quickstart.md)のクイックスタートの記事には、目的の言語に使用できるコマンドと共に、コード例が記載されています。

#### <a name="version-21-entity-categories"></a>バージョン 2.1 でのエンティティのカテゴリ

次の表に、NER v2.1 で返されるエンティティのカテゴリを示します。

| カテゴリ   | 説明                          |
|------------|--------------------------------------|
| Person   |   人の名前。  |
|場所    | 自然および人工のランドマーク、構造、地物、地政学的実体 |
|Organization | 企業、政治団体、音楽バンド、スポーツ クラブ、政府機関、および公的機関。 国籍と宗教は、このエンティティ型には含まれていません。 |
| PhoneNumber | 電話番号 (米国および EU の電話番号のみ)。 |
| Email | メール アドレス。 |
| URL | Web サイトへの URL。 |
| IP | ネットワーク IP アドレス。 |
| DateTime | 日付と時刻。| 
| Date | カレンダーの日付。 |
| Time | 時間帯 |
| DateRange | 日付の範囲。 |
| TimeRange | 時間の範囲。 |
| Duration | 期間。 |
| オン | 設定された繰り返し時間。 |
| Quantity | 数値と数量です。 |
| Number | 数値 |
| パーセント | パーセント。|
| Ordinal | 序数。 |
| Age | 年齢。 |
| Currency | 通貨。 |
| Dimension | ディメンションと測定。 |
| 気温 | 気温。 |

## <a name="language-detection"></a>[言語検出](#tab/language-detection)

> [!TIP]
> 最新バージョンの API をアプリケーションに使用する場合、 [言語検出](../language-detection/how-to/call-api.md)の方法の記事と、最新バージョンの API に関する[クイックスタート](../language-detection/quickstart.md)を参照してください。 

## <a name="feature-changes"></a>機能の変更 

現在のバージョンでは言語検出機能の出力が変更されました。 JSON 応答には、`score` の代わりに `ConfidenceScore` が含まれます。 また、現在のバージョンでは、各ドキュメントの `detectedLanguage` 属性で 1 つの言語のみが返されます。

## <a name="steps-to-migrate"></a>移行手順

### <a name="rest-api"></a>REST API

アプリケーションで REST API が使用される場合、その要求エンドポイントを言語検出用の[現在のエンドポイント](../language-detection/quickstart.md?pivots=rest-api)に更新します。 たとえば、`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1/languages` となります。 また、[API の応答](../language-detection/how-to/call-api.md)で `score` ではなく `ConfidenceScore` を使用するようアプリケーションを更新することも必要になります。 

JSON 応答の例については、リファレンス ドキュメントを参照してください。
* [バージョン 2.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7)
* [Version 3.0](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Languages) 
* [Version 3.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1/operations/Languages)

#### <a name="client-libraries"></a>クライアント ライブラリ

最新バージョンの感情分析クライアント ライブラリを使用するには、`Azure.AI.TextAnalytics` 名前空間で最新のソフトウェア パッケージをダウンロードする必要があります。 [クイックスタートの記事](../language-detection/quickstart.md)には、目的の言語に使用できるコマンドと共に、コード例が記載されています。

## <a name="key-phrase-extraction"></a>[キー フレーズ抽出](#tab/key-phrase-extraction)

> [!TIP]
> 最新バージョンの API をアプリケーションに使用する場合、 [キー フレーズ抽出](../key-phrase-extraction/how-to/call-api.md)の方法の記事と、最新バージョンの API に関する[クイックスタート](../key-phrase-extraction/quickstart.md)を参照してください。 

## <a name="feature-changes"></a>機能の変更 

現在、キー フレーズ抽出機能は、エンドポイントのバージョン以外に変更されていません。

## <a name="steps-to-migrate"></a>移行手順

### <a name="rest-api"></a>REST API

アプリケーションで REST API が使用される場合、その要求エンドポイントをキー フレーズ抽出用の[現在のエンドポイント](../key-phrase-extraction/quickstart.md?pivots=rest-api)に更新します。 例: `https://<your-custom-subdomain>.api.cognitiveservices.azure.com/text/analytics/v3.1/keyPhrases`

JSON 応答の例については、リファレンス ドキュメントを参照してください。
* [バージョン 2.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6)
* [Version 3.0](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/KeyPhrases) 
* [Version 3.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1/operations/KeyPhrases)

### <a name="client-libraries"></a>クライアント ライブラリ

最新バージョンの感情分析クライアント ライブラリを使用するには、`Azure.AI.TextAnalytics` 名前空間で最新のソフトウェア パッケージをダウンロードする必要があります。 [クイックスタートの記事](../key-phrase-extraction/quickstart.md)には、目的の言語に使用できるコマンドと共に、コード例が記載されています。

---

## <a name="see-also"></a>関連項目

* [Azure Cognitive Service for Language とは](../overview.md)
