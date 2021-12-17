---
title: エンティティ リンクのコグニティブ スキル
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search のエンリッチメント パイプラインのテキストから、さまざまなリンクされたエンティティを抽出します。
manager: jennmar
author: ayokande
ms.author: aakande
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/12/2021
ms.openlocfilehash: 4b052e6ac5746d771e7725b39c13fd57b20facb8
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2021
ms.locfileid: "123038465"
---
# <a name="entity-linking-cognitive-skill"></a>エンティティ リンクのコグニティブ スキル

**エンティティ リンク** のスキルは、よく知られたナレッジ ベース (Wikipedia) 内の記事へのリンクを含む、認識されたエンティティの一覧を返します。

> [!NOTE]
> このスキルは Cognitive Services [Text Analytics](../cognitive-services/text-analytics/overview.md) にバインドされており、1 日にインデクサーあたり 20 ドキュメントを超えるトランザクションには[課金対象リソース](cognitive-search-attach-cognitive-services.md)が必要です。 組み込みスキルの実行は、既存の [Cognitive Services の従量課金制の価格](https://azure.microsoft.com/pricing/details/cognitive-services/)で課金されます。
>

## <a name="odatatype"></a>@odata.type

Microsoft.Skills.Text.V3.EntityLinkingSkill

## <a name="data-limits"></a>データ制限

レコードのサイズは、[`String.Length`](/dotnet/api/system.string.length) で測定して 50,000 文字以下にする必要があります。 データを EntityLinking スキルに送信する前に分割する必要がある場合は、[テキスト分割スキル](cognitive-search-skill-textsplit.md)の使用を検討してください。

## <a name="skill-parameters"></a>スキルのパラメーター

パラメーター名は、大文字と小文字が区別され、すべて省略可能です。

| パラメーター名     | 説明 |
|--------------------|-------------|
| `defaultLanguageCode` |    入力テキストの言語コード。 既定の言語コードが指定されていない場合、既定の言語コードとして英語 (en) が使用されます。 <br/> [サポートされる言語の完全な一覧](../cognitive-services/text-analytics/language-support.md)を参照してください。 |
| `minimumPrecision` | 0 から 1 の値。 (`entities` 出力の) 信頼度スコアがこの値よりも小さい場合は、エンティティは返されません。 既定値は 0 です。 |
| `modelVersion` | (省略可能) Text Analytics サービスを呼び出すときに使用するモデルのバージョン。 指定しない場合、既定では利用可能な最新のものになります。 絶対に必要な場合以外は、この値を指定しないことをお勧めします。 詳細については、「[Text Analytics API でのモデルのバージョン管理](../cognitive-services/text-analytics/concepts/model-versioning.md)」を参照してください。|

## <a name="skill-inputs"></a>スキルの入力

| 入力名      | 説明                   |
|---------------|-------------------------------|
| `languageCode`    | レコードの言語を示す文字列。 このパラメーターが指定されていない場合、既定の言語コードがレコードを分析するために使用されます。 <br/>[サポートされる言語の完全な一覧](../cognitive-services/text-analytics/language-support.md)を参照してください。 |
| `text`          | 分析するテキスト。          |

## <a name="skill-outputs"></a>スキルの出力

| 出力名      | 説明                   |
|---------------|-------------------------------|
| `entities` | 次のフィールドが含まれる複合型の配列。 <ul><li>name (テキストに表示される実際のエンティティ名)</li> <li>id </li> <li>language (スキルで決定されたテキストの言語)</li> <li>url (このエンティティへのリンクされた url)</li> <li>bingId (このリンクされたエンティティの bingId)</li> <li>dataSource (url に関連付けられたデータ ソース) </li> <li>matches (`text`、`offset`、`length`、`confidenceScore` を含む複合型の配列)</li></ul>|

## <a name="sample-definition"></a>定義例

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.V3.EntityLinkingSkill",
    "context": "/document",
    "defaultLanguageCode": "en", 
    "minimumPrecision": 0.5, 
    "inputs": [
        {
            "name": "text", 
            "source": "/document/content"
        },
        {
            "name": "languageCode", 
            "source": "/document/language"
        }
    ],
    "outputs": [
        {
            "name": "entities", 
            "targetName": "entities" 
        }
    ]
}
```

## <a name="sample-input"></a>サンプル入力

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
             "text": "Microsoft is liked by many.",
             "languageCode": "en"
           }
      }
    ]
}
```

## <a name="sample-output"></a>サンプル出力

```json
{
  "values": [
    {
      "recordId": "1",
      "data" : 
      {
        "entities": [
          {
            "name": "Microsoft", 
            "id": "Microsoft",
            "language": "en", 
            "url": "https://en.wikipedia.org/wiki/Microsoft", 
            "bingId": "a093e9b9-90f5-a3d5-c4b8-5855e1b01f85", 
            "dataSource": "Wikipedia", 
            "matches": [
                {
                    "text": "Microsoft", 
                    "offset": 0, 
                    "length": 9, 
                    "confidenceScore": 0.13 
                }
            ]
          }
        ],
      }
    }
  ]
}
```

このスキルの出力のエンティティに対して返されるオフセットは、[Text Analytics API](../cognitive-services/text-analytics/overview.md) から直接返されることに注意してください。つまり、これらを使用して元の文字列にインデックスを作成する場合は、正しい内容を抽出するために .NET の [StringInfo](/dotnet/api/system.globalization.stringinfo) クラスを使用する必要があります。  [詳細については、こちらで確認できます。](../cognitive-services/text-analytics/concepts/text-offsets.md)

## <a name="warning-cases"></a>警告のケース

ドキュメントの言語コードがサポートされていない場合、警告が返され、エンティティは抽出されません。

## <a name="see-also"></a>関連項目

+ [組み込みのスキル](cognitive-search-predefined-skills.md)
+ [スキルセットの定義方法](cognitive-search-defining-skillset.md)