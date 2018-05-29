---
title: 名前付きエンティティの認識コグニティブ検索スキル (Azure Search) | Microsoft Docs
description: Azure Search コグニティブ検索パイプライン内のテキストから、人物、場所、および組織の名前付きエンティティを抽出します。
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: 73ffcf5e2ced63fddaf0f5ef2ca7e72a7d94b966
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/07/2018
ms.locfileid: "33786821"
---
#    <a name="named-entity-recognition-cognitive-skill"></a>名前付きエンティティの認識コグニティブ スキル

**名前付きエンティティ認識** スキルは、テキストから名前付きエンティティを抽出します。 使用可能なエンティティ タイプには、`person`、`location`、`organization` が含まれます。

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.NamedEntityRecognitionSkill

## <a name="skill-parameters"></a>スキルのパラメーター

パラメーターの大文字と小文字は区別されます。

| パラメーター名     | [説明] |
|--------------------|-------------|
| categories    | 抽出する必要があるカテゴリの配列。  可能なカテゴリの型は、`"Person"`、`"Location"`、`"Organization"` です。 カテゴリが指定されていない場合、すべての型が返されます。|
|defaultLanguageCode |  入力テキストの言語コード。 次の言語がサポートされます。`ar, cs, da, de, en, es, fi, fr, he, hu, it, ko, pt-br, pt`|
| minimumPrecision  | 0 から 1 の数値。 精度がこの値よりも小さい場合は、エンティティは返されません。 既定値は 0 です。|

## <a name="skill-inputs"></a>スキルの入力

| 入力名      | [説明]                   |
|---------------|-------------------------------|
| languageCode  | 省略可能。 既定値は `"en"` です。  |
| テキスト          | 分析するテキスト。          |

## <a name="skill-outputs"></a>スキルの出力

| 出力名     | [説明]                   |
|---------------|-------------------------------|
| persons      | 各文字列が人物の名前を表す文字列の配列。 |
| 場所  | 各文字列が場所を表す文字列の配列。 |
| organizations  | 各文字列が組織を表す文字列の配列。 |
| entities | 複合型の配列。 各複合型には、次のフィールドが含まれます。 <ul><li>category (`"person"`、`"organization"`、または `"location"`)</li> <li>value (実際のエンティティ名)</li><li>offset (テキスト内で見つかった場所)</li><li>confidence (値が実際のエンティティの信頼度を表す 0 から 1 の値)</li></ul> |

##  <a name="sample-definition"></a>定義例

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
    "categories": [ "Person"],
    "defaultLanguageCode": "en",
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "persons",
        "targetName": "people"
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
             "text": "This is a the loan application for Joe Romero, he is a Microsoft employee who was born in Chile and then moved to Australia… Ana Smith is provided as a reference.",
             "languageCode": "en"
           }
      }
    ]
}
```

##  <a name="sample-output"></a>サンプル出力

```json
{
  "values": [
    {
      "recordId": "1",
      "data" : 
      {
        "persons": [ "Joe Romero", "Ana Smith"],
        "locations": ["Seattle"],
        "organizations":["Microsoft Corporation"],
        "entities":  
        [
          {
            "category":"person",
            "value": "Joe Romero",
            "offset": 45,
            "confidence": 0.87
          },
          {
            "category":"person",
            "value": "Ana Smith",
            "offset": 59,
            "confidence": 0.87
          },
          {
            "category":"location",
            "value": "Seattle",
            "offset": 5,
            "confidence": 0.99
          },
          {
            "category":"organization",
            "value": "Microsoft Corporation",
            "offset": 120,
            "confidence": 0.99
          }
        ]
      }
    }
  ]
}
```


## <a name="error-cases"></a>エラーになる場合
サポートされていない言語コードを指定する場合、またはコンテンツが指定された言語と一致しない場合は、エラーが返され、エンティティは抽出されません。

## <a name="see-also"></a>関連項目

+ [定義済みのスキル](cognitive-search-predefined-skills.md)
+ [スキルセットの定義方法](cognitive-search-defining-skillset.md)