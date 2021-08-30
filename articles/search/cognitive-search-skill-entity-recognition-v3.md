---
title: エンティティ認識 (V3) の認知スキル
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search のエンリッチメント パイプラインのテキストから Text Analytics V3 を使用してさまざまな種類のエンティティを抽出します。
manager: jennmar
author: ayokande
ms.author: aakande
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/12/2021
ms.openlocfilehash: 14811591feea9df735bf41e23a81e3a96faa2662
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121862669"
---
# <a name="entity-recognition-cognitive-skill-v3"></a>エンティティ認識の認知スキル (V3)

**エンティティ認識** スキルによってテキストからさまざまな種類のエンティティが抽出されます。 これらのエンティティは、人や組織から URL や電話番号まで、14 の異なるカテゴリに分類されます。 このスキルでは、Cognitive Services の [Text Analytics](../cognitive-services/text-analytics/overview.md) によって提供される機械学習モデルが使用されます。

> [!NOTE]
> このスキルは Cognitive Services にバインドされており、1 日にインデクサーあたり 20 ドキュメントを超えるトランザクションには[課金対象リソース](cognitive-search-attach-cognitive-services.md)が必要です。 組み込みスキルの実行は、既存の [Cognitive Services の従量課金制の価格](https://azure.microsoft.com/pricing/details/cognitive-services/)で課金されます。
>

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.V3.EntityRecognitionSkill

## <a name="data-limits"></a>データ制限
レコードのサイズは、[`String.Length`](/dotnet/api/system.string.length) で測定して 50,000 文字以下にする必要があります。 データを EntityRecognition スキルに送信する前に分割する必要がある場合は、[テキスト分割スキル](cognitive-search-skill-textsplit.md)の使用を検討してください。

## <a name="skill-parameters"></a>スキルのパラメーター

パラメーターでは大文字と小文字が区別されます。パラメーターはすべて任意です。

| パラメーター名     | 説明 |
|--------------------|-------------|
| `categories`    | 抽出する必要があるカテゴリの配列。  可能なカテゴリの型は、`"Person"`、`"Location"`、`"Organization"`、`"Quantity"`、`"DateTime"`、`"URL"`、`"Email"`、`"PersonType"`、`"Event"`、`"Product"`、`"Skill"`、`"Address"`、`"Phone Number"`、`"IP Address"` です。 カテゴリが指定されていない場合、すべての型が返されます。|
| `defaultLanguageCode` |    入力テキストの言語コード。 既定の言語コードが指定されていない場合、既定の言語コードとして英語 (en) が使用されます。 <br/> [サポートされる言語の完全な一覧](../cognitive-services/text-analytics/language-support.md)を参照してください。 すべての言語ですべてのエンティティ カテゴリがサポートされているわけではありません。以下の注意事項を参照してください。|
| `minimumPrecision` | 0 から 1 の値。 (`namedEntities` 出力の) 信頼度スコアがこの値よりも小さい場合は、エンティティは返されません。 既定値は 0 です。 |
| `modelVersion` | (省略可能) Text Analytics サービスを呼び出すときに使用するモデルのバージョン。 指定しない場合、既定では利用可能な最新のものになります。 絶対に必要な場合以外は、この値を指定しないことをお勧めします。 詳細については、「[Text Analytics API でのモデルのバージョン管理](../cognitive-services/text-analytics/concepts/model-versioning.md)」を参照してください。|


## <a name="skill-inputs"></a>スキルの入力

| 入力名      | 説明                   |
|---------------|-------------------------------|
| `languageCode`    | レコードの言語を示す文字列。 このパラメーターが指定されていない場合、既定の言語コードがレコードを分析するために使用されます。 <br/>[サポートされる言語の完全な一覧](../cognitive-services/text-analytics/language-support.md)を参照してください。 |
| `text`          | 分析するテキスト。          |

## <a name="skill-outputs"></a>スキルの出力

> [!NOTE]
>言語によっては、一部のエンティティ カテゴリがサポートされていません。 使用する言語でサポートされているエンティティ カテゴリについては [「Text Analytics API v3 のサポートされるエンティティ カテゴリ」](../cognitive-services/text-analytics/named-entity-types.md)に関するページを参照してください。

| 出力名      | 説明                   |
|---------------|-------------------------------|
| `persons`       | 各文字列が人物の名前を表す文字列の配列。 |
| `locations`  | 各文字列が場所を表す文字列の配列。 |
| `organizations`  | 各文字列が組織を表す文字列の配列。 |
| `quantities`  | 各文字列が数量を表す文字列の配列。 |
| `dateTimes`  | 各文字列が DateTime (テキストに表示される) 値を表す文字列の配列。 |
| `urls` | 各文字列が URL を表す文字列の配列。 |
| `emails` | 各文字列が電子メールを表す文字列の配列。 |
| `personTypes` | 各文字列が PersonType を表す文字列の配列。 |
| `events` | 各文字列がイベントを表す文字列の配列。 |
| `products` | 各文字列が製品を表す文字列の配列。 |
| `skills` | 各文字列がスキルを表す文字列の配列。 |
| `addresses` | 各文字列がアドレスを表す文字列の配列。 |
| `phoneNumbers` | 各文字列が電話番号を表す文字列の配列。 |
| `ipAddresses` | 各文字列が IP アドレスを表す文字列の配列。 |
| `namedEntities` | 次のフィールドが含まれる複合型の配列。 <ul><li>category</li> <li>subcategory</li> <li>confidenceScore (値が高いほど、実際のエンティティに近づきます)</li> <li>length (このエンティティの長さ (文字数))</li> <li>offset (テキスト内で見つかった場所)</li> <li>text (テキストに表示される実際のエンティティ名)</li></ul> |


##    <a name="sample-definition"></a>定義例

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.V3.EntityRecognitionSkill",
    "context": "/document",
    "categories": [ "Person", "Email"],
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
            "name": "persons", 
            "targetName": "people"
        },
        {
            "name": "emails", 
            "targetName": "emails"
        },
        {
            "name": "namedEntities", 
            "targetName": "namedEntities"
        }
    ]
  }
```
##    <a name="sample-input"></a>サンプル入力

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
             "text": "Contoso Corporation was founded by Jean Martin. They can be reached at contact@contoso.com",
             "languageCode": "en"
           }
      }
    ]
}
```

##    <a name="sample-output"></a>サンプル出力

```json
{
  "values": [
    {
      "recordId": "1",
      "data" : 
      {
        "people": [ "Jean Martin"],
        "emails":["contact@contoso.com"],
        "namedEntities": 
        [
          {
            "category": "Person",
            "subcategory": null,
            "length": 11,
            "offset": 35,
            "confidenceScore": 0.98,
            "text": "Jean Martin"
          },
          {
            "category": "Email",
            "subcategory": null,
            "length": 19,
            "offset": 71,
            "confidenceScore": 0.8,
            "text": "contact@contoso.com"
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