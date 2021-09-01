---
title: 言語検出コグニティブ スキル
titleSuffix: Azure Cognitive Search
description: 非構造化テキストを評価し、各レコードに対し、Azure Cognitive Search の AI エンリッチメント パイプラインの分析の強度を示すスコアと共に言語識別子を返します。
author: LiamCavanagh
ms.author: liamca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/12/2021
ms.openlocfilehash: 01b6449baa7c5d13b041f886074425d78e037579
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121862105"
---
#   <a name="language-detection-cognitive-skill"></a>言語検出コグニティブ スキル

**言語検出** スキルは、入力テキストの言語を検出し、要求で送信されたすべてのドキュメントごとに 1 つの言語コードを報告します。 言語コードは、分析の強度を示すスコアとペアリングされます。 このスキルでは、Cognitive Services の [Text Analytics](../cognitive-services/text-analytics/overview.md) によって提供される機械学習モデルが使用されます。

この機能は、テキストの言語をその他のスキル ([感情分析スキル](cognitive-search-skill-sentiment-v3.md)や[テキスト分割スキル](cognitive-search-skill-textsplit.md)など) への入力として提供する必要がある場合に特に便利です。

言語検出では Bing の自然言語処理ライブラリが使用され、その数は Text Analytics に記載された[サポートされている言語とリージョン](../cognitive-services/text-analytics/language-support.md)の数を超えています。 言語の正確な一覧は公開されていませんが、広域で話されているすべての言語のほか、変種、方言、および一部の地方言語や文化言語も含まれます。 使用頻度の低い言語で表されるコンテンツがある場合、[言語検出 API を試して](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Languages)、コードが返されるかどうか確認できます。 検出できない言語の応答は `(Unknown)` です。

> [!NOTE]
> このスキルは Cognitive Services にバインドされており、1 日にインデクサーあたり 20 ドキュメントを超えるトランザクションには[課金対象リソース](cognitive-search-attach-cognitive-services.md)が必要です。 組み込みスキルの実行は、既存の [Cognitive Services の従量課金制の価格](https://azure.microsoft.com/pricing/details/cognitive-services/)で課金されます。
>

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.LanguageDetectionSkill

## <a name="data-limits"></a>データ制限
レコードのサイズは、[`String.Length`](/dotnet/api/system.string.length) で測定して 50,000 文字以下にする必要があります。 データを言語検出スキルに送信する前に分割する必要がある場合は、[テキスト分割スキル](cognitive-search-skill-textsplit.md)を使用できます。

## <a name="skill-parameters"></a>スキルのパラメーター

パラメーターの大文字と小文字は区別されます。

| 入力 | 説明 |
|---------------------|-------------|
| `defaultCountryHint` | (省略可能) 言語検出モデルが言語を明確に区別できない場合に、ISO 3166-1 alpha-2 の 2 文字の国番号を提供してヒントとして使用することができます。 詳細については、このトピックに関する [Text Analytics のドキュメント](../cognitive-services/text-analytics/how-tos/text-analytics-how-to-language-detection.md#ambiguous-content)を参照してください。 具体的には、`defaultCountryHint` パラメーターは、`countryHint` の入力を明示的に指定しないドキュメントで使用されます。  |
| `modelVersion`   | (省略可能) Text Analytics サービスを呼び出すときに使用するモデルのバージョン。 指定しない場合、既定では利用可能な最新のものになります。 絶対に必要な場合以外は、この値を指定しないことをお勧めします。 詳細については、「[Text Analytics API でのモデルのバージョン管理](../cognitive-services/text-analytics/concepts/model-versioning.md)」を参照してください。 |

## <a name="skill-inputs"></a>スキルの入力

パラメーターの大文字と小文字は区別されます。

| 入力     | 説明 |
|--------------------|-------------|
| `text` | 分析されるテキスト。|
| `countryHint` | 言語検出モデルが言語を明確に区別できない場合に、ヒントとして使用する ISO 3166-1 alpha-2 の 2 文字の国番号。 詳細については、このトピックに関する [Text Analytics のドキュメント](../cognitive-services/text-analytics/how-tos/text-analytics-how-to-language-detection.md#ambiguous-content)を参照してください。 |

## <a name="skill-outputs"></a>スキルの出力

| 出力名    | 説明 |
|--------------------|-------------|
| `languageCode` | 識別された言語の ISO 6391 言語コード。 例: "en"。 |
| `languageName` | 言語の名前。 例: "English"。 |
| `score` | 0 から 1 の値。 言語が正しく識別されている確率。 文章内に言語が混在している場合、スコアが 1 よりも低くなる場合があります。  |

##  <a name="sample-definition"></a>定義例

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
    "inputs": [
      {
        "name": "text",
        "source": "/document/text"
      },
      {
        "name": "countryHint",
        "source": "/document/countryHint"
      }
    ],
    "outputs": [
      {
        "name": "languageCode",
        "targetName": "myLanguageCode"
      },
      {
        "name": "languageName",
        "targetName": "myLanguageName"
      },
      {
        "name": "score",
        "targetName": "myLanguageScore"
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
        "data":
           {
             "text": "Glaciers are huge rivers of ice that ooze their way over land, powered by gravity and their own sheer weight. "
           }
      },
      {
        "recordId": "2",
        "data":
           {
             "text": "Estamos muy felices de estar con ustedes."
           }
      },
      {
        "recordId": "3",
        "data":
           {
             "text": "impossible",
             "countryHint": "fr"
           }
      }
    ]
```


##  <a name="sample-output"></a>サンプル出力

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
            {
              "languageCode": "en",
              "languageName": "English",
              "score": 1,
            }
      },
      {
        "recordId": "2",
        "data":
            {
              "languageCode": "es",
              "languageName": "Spanish",
              "score": 1,
            }
      },
      {
        "recordId": "3",
        "data":
            {
              "languageCode": "fr",
              "languageName": "French",
              "score": 1,
            }
      }
    ]
}
```

## <a name="see-also"></a>関連項目

+ [組み込みのスキル](cognitive-search-predefined-skills.md)
+ [スキルセットの定義方法](cognitive-search-defining-skillset.md)