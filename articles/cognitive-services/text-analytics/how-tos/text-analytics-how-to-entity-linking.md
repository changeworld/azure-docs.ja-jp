---
title: Text Analytics API でエンティティ認識を利用する
titleSuffix: Azure Cognitive Services
description: Text Analytics REST API を使用して、テキスト内のエンティティの ID を識別して明確にする方法について説明します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 01/06/2019
ms.author: aahi
ms.openlocfilehash: a9bfa2a51341bf60f92378f5d96a390bd1d92db1
ms.sourcegitcommit: c32050b936e0ac9db136b05d4d696e92fefdf068
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/08/2020
ms.locfileid: "75732780"
---
# <a name="how-to-use-named-entity-recognition-in-text-analytics"></a>Text Analytics で名前付きエンティティの認識を使用する方法

Text Analytics API を使用すると、構造化されていないテキストから、あいまいさを解消したエンティティの一覧を、Web 上にある詳しい情報へのリンクと共に取得することができます。 この API は、名前付きエンティティの認識 (NER) とエンティティ リンクの両方をサポートしています。

### <a name="entity-linking"></a>Entity Linking

エンティティ リンク設定は、テキスト内で見つかったエンティティの個性を識別してあいまいさを解消する機能です (例: `Mars` という単語が出現した場合に、それが惑星を指すのか、古代ローマの戦争の神様を指すのかを判定する)。 このプロセスで、テキスト内の認識されたエンティティをリンクするためには、適切な言語のナレッジ ベースが存在している必要があります。 


### <a name="named-entity-recognition-ner"></a>名前付きエンティティの認識 (NER)

名前付きエンティティの認識 (NER) は、テキスト形式のさまざまなエンティティを識別して、事前に定義したクラスまたは種類に分類する機能です。 たとえば、人や場所、組織が該当します。

## <a name="named-entity-recognition-versions-and-features"></a>名前付きエンティティの認識のバージョンと機能

Text Analytics API の名前付きエンティティの認識には、v2 と v3 の 2 つのバージョンがあります。 バージョン 3 (パブリック プレビュー) では、より細かくエンティティを検出および分類できるようになっています。

| 機能                                                         | NER v2 | NER v3 |
|-----------------------------------------------------------------|--------|--------|
| 単一要求およびバッチ要求のメソッド                          | X      | X      |
| 複数のカテゴリにまたがる基本的なエンティティの認識              | X      | X      |
| 認識されたエンティティの分類の拡張                 |        | X      |
| エンティティ リンク設定と NER 要求の送信のためのエンドポイントが分けられました。 |        | X      |
| モデルのバージョン管理                                                |        | X      |

詳細については、[言語サポート](../language-support.md#sentiment-analysis-key-phrase-extraction-and-named-entity-recognition)に関するページを参照してください。

#### <a name="version-2tabversion-2"></a>[Version 2](#tab/version-2)

### <a name="entity-types"></a>エンティティの種類

> [!NOTE]
> 名前付きエンティティの認識 (NER) バージョン 2 では、次のエンティティのみがサポートされます。 NER v3 はパブリック プレビュー段階ですが、テキスト内で認識されるエンティティの数と深さが大幅に拡張されています。   

| 種類  | SubType | 例 |
|:-----------   |:------------- |:---------|
| Person        | 該当なし\*         | "Jeff", "Bill Gates"     |
| Location      | 該当なし\*         | "Redmond, Washington", "Paris"  |
| Organization  | 該当なし\*         | "Microsoft"   |
| Quantity      | Number        | "6", "six"     |
| Quantity      | [パーセント]    | "50%"､"fifty percent"|
| Quantity      | Ordinal       | "2nd"､"second"     |
| Quantity      | Age           | "90 day old"､"30 years old"    |
| Quantity      | Currency      | "$10.99"     |
| Quantity      | Dimension     | "10 miles"､"40 cm"     |
| Quantity      | 気温   | "32 degrees"    |
| DateTime      | 該当なし\*         | "6:30PM February 4, 2012"      |
| DateTime      | Date          | "May 2nd, 2017", "05/02/2017"   |
| DateTime      | Time          | "8am", "8:00"  |
| DateTime      | DateRange     | "May 2nd to May 5th"    |
| DateTime      | TimeRange     | "6pm to 7pm"     |
| DateTime      | Duration      | "1 minute and 45 seconds"   |
| DateTime      | オン           | "every Tuesday"     |
| URL           | 該当なし\*         | "https:\//www.bing.com"    |
| Email         | 該当なし\*         | "support@contoso.com" |
| 米国の電話番号  | 該当なし\*         | (米国の電話番号のみ) "(312) 555-0176" |
| IP アドレス    | 該当なし\*         | "10.0.0.100" |

\* 入力および抽出されたエンティティによっては、一部エンティティで `SubType` が省略されることがあります。  一覧に含まれているすべてのサポートされているエンティティの種類は、英語、簡体中国語、フランス語、ドイツ語、スペイン語でのみ使用できます。

### <a name="request-endpoints"></a>要求エンドポイント

名前付きエンティティの認識 v2 では、NER とエンティティ リンク設定の要求に単一のエンドポイントを使用します。

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v2.1/entities`

#### <a name="version-3-public-previewtabversion-3"></a>[バージョン 3 (パブリック プレビュー)](#tab/version-3)

### <a name="entity-types"></a>エンティティの種類

名前付きエンティティの認識 v3 では、複数の種類に対応する拡張された検出が提供されます。 現在、NER v3 は次のカテゴリのエンティティを認識できます。

* 全般
* 個人情報 

サポートされているエンティティと言語の詳しいリストについては、[NER v3 でサポートされるエンティティの種類](../named-entity-types.md)に関する記事を参照してください。

### <a name="request-endpoints"></a>要求エンドポイント

名前付きエンティティの認識 v3 では、NER とエンティティ リンク設定の要求に別個のエンドポイントを使用します。 要求に応じて、次の URL 形式を使用します。

NER
* 一般的なエンティティ- `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/recognition/general`

* 個人情報 - `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/recognition/pii`

エンティティ リンク設定
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/linking`

### <a name="model-versioning"></a>モデルのバージョン管理

[!INCLUDE [v3-model-versioning](../includes/model-versioning.md)]

---

## <a name="sending-a-rest-api-request"></a>REST API 要求の送信

### <a name="preparation"></a>準備

JSON ドキュメントは、次の形式である必要があります: ID、テキスト、言語。

各ドキュメントは 5,120 文字未満である必要があり、コレクションあたり最大 1,000 の項目 (ID) を含めることができます。 コレクションは、要求の本文で送信されます。

### <a name="structure-the-request"></a>要求を構造化する

POST 要求を作成します。 次のリンクにある [Postman](text-analytics-how-to-call-api.md) または **API テスト コンソール**を使用して、簡単に要求を構造化し、送信することができます。 

[!INCLUDE [text-analytics-find-resource-information](../includes/find-azure-resource-info.md)]

#### <a name="version-2tabversion-2"></a>[Version 2](#tab/version-2)

[名前付きエンティティの認識 (NER) v2 リファレンス](https://eastus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634)

バージョン 2 では、エンティティ リンク設定と NER の要求に次のエンドポイントが使用されます。 

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v2.1/entities`

#### <a name="version-3tabversion-3"></a>[Version 3](#tab/version-3)

[名前付きエンティティの認識 v3 リファレンス](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesRecognitionGeneral)

バージョン 3 では、NER とエンティティ リンク設定の要求に別々のエンドポイントが使用されます。 要求に応じて、次の URL 形式を使用します。

NER
* 一般的なエンティティ- `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/recognition/general`

* 個人情報エンティティ - `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/recognition/pii`

エンティティ リンク設定
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/linking`

---

要求ヘッダーに Text Analytics API キーが含まれるように設定します。 要求本文では、用意した JSON ドキュメントを指定します。

### <a name="example-ner-request"></a>NER 要求の例 

次に示したのは、API に送信するコンテンツの例です。 要求の形式は、どちらのバージョンの API でも同じです。

```json
{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "I had a wonderful trip to Seattle last week."
    }
  ]
}
```

## <a name="post-the-request"></a>要求を投稿する

要求が受信されると分析が実行されます。 分単位および秒単位で送信できる要求のサイズと数については、概要の「[データ制限](../overview.md#data-limits)」セクションを参照してください。

Text Analytics API はステートレスです。 データはアカウントに保存されず、結果がすぐに応答で返されます。

## <a name="view-results"></a>結果の表示

すべての POST 要求で、ID と検出されたエンティティのプロパティを含む JSON 形式の応答が返されます。

出力はすぐに返されます。 結果は、JSON を受け付けるアプリケーションにストリームするか、ローカル システム上のファイルに出力を保存してから、そのファイルを、データの並べ替え、検索、および操作が可能なアプリケーションにインポートすることができます。

#### <a name="version-2tabversion-2"></a>[Version 2](#tab/version-2)

### <a name="example-ner-v2-response"></a>NER v2 の応答の例
```json
{
  "documents": [{
    "id": "1",
    "entities": [{
      "name": "Seattle",
      "matches": [{
        "wikipediaScore": 0.15046201222847677,
        "entityTypeScore": 0.80624294281005859,
        "text": "Seattle",
        "offset": 26,
        "length": 7
      }],
      "wikipediaLanguage": "en",
      "wikipediaId": "Seattle",
      "wikipediaUrl": "https://en.wikipedia.org/wiki/Seattle",
      "bingId": "5fbba6b8-85e1-4d41-9444-d9055436e473",
      "type": "Location"
    }, {
      "name": "last week",
      "matches": [{
        "entityTypeScore": 0.8,
        "text": "last week",
        "offset": 34,
        "length": 9
      }],
      "type": "DateTime",
      "subType": "DateRange"
    }]
  }],
  "errors": []
}
```

#### <a name="version-3-public-previewtabversion-3"></a>[バージョン 3 (パブリック プレビュー)](#tab/version-3)

### <a name="example-v3-responses"></a>v3 の応答の例

バージョン 3 では、NER とエンティティ リンク設定に別々のエンドポイントが用意されています。 両方の操作の応答を次に示します。

#### <a name="example-ner-response"></a>NER の応答の例

```json
{
    "documents": [{
    "id": "1",
    "entities": [{
        "text": "Seattle",
        "type": "Location",
        "offset": 26,
        "length": 7,
        "score": 0.80624294281005859
    }, {
        "text": "last week",
        "type": "DateTime",
        "subtype": "DateRange",
        "offset": 34,
        "length": 9,
        "score": 0.8
    }]
    }],
    "errors": [],
    "modelVersion": "2019-10-01"
}
```

#### <a name="example-entity-linking-response"></a>エンティティ リンク設定の応答の例

```json
{
  "documents": [{
    "id": "1",
    "entities": [{
      "name": "Seattle",
      "matches": [{
        "text": "Seattle",
        "offset": 26,
        "length": 7,
        "score": 0.15046201222847677
      }],
      "language": "en",
      "id": "Seattle",
      "url": "https://en.wikipedia.org/wiki/Seattle",
      "dataSource": "Wikipedia"
    }]
  }],
  "errors": [],
  "modelVersion": "2019-10-01"
}
```

---

## <a name="summary"></a>まとめ

この記事では、Cognitive Services の Text Analytics を使用するエンティティ リンク設定の概念とワークフローについて説明しました。 要約すると:

* 名前付きエンティティの認識では、特定の言語を対象に 2 つのバージョンを使用できます。
* 要求本文内の JSON ドキュメントには、ID、テキスト、および言語のコードが含まれます。
* POST 要求は、ユーザーのサブスクリプションで有効な、個人用に設定された[アクセス キーとエンドポイント](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource)を使用して 1 つまたは複数のエンドポイントに送信されます。
* リンクされているエンティティ (ドキュメント ID ごとに信頼度スコア、オフセット、Web リンクが含まれている) で構成される応答の出力は、どのアプリケーションでも使用できます

## <a name="next-steps"></a>次のステップ

* [Text Analytics の概要](../overview.md)
* [Text Analytics クライアント ライブラリの使用](../quickstarts/text-analytics-sdk.md)
* [新機能](../whats-new.md)
