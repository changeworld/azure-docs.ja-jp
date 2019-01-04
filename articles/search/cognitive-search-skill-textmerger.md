---
title: テキスト マージ コグニティブ検索スキル - Azure Search
description: フィールドのコレクションからテキストを 1 つの統合されたフィールドにマージします。 Azure Search のエンリッチメント パイプラインでこのコグニティブ スキル使用します。
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: b29d32d39b4efb7e242a3ae3213512798622d1e9
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/12/2018
ms.locfileid: "53314518"
---
#    <a name="text-merge-cognitive-skill"></a>テキスト マージ コグニティブ スキル

**テキスト マージ** スキルは、フィールドのコレクションからのテキストを 1 つのフィールドに統合します。 

> [!NOTE]
> 2018 年 12 月 21 日から、Azure Search のスキルセットに Cognitive Services リソースを関連付けることができるようになります。 これにより、スキルセットの実行への課金を開始できます。 この日付には、ドキュメント クラッキング ステージの一部として画像抽出への課金も開始します。 ドキュメントからのテキスト抽出は、引き続き追加コストなしで提供されます。
>
> 組み込みスキルの実行は、既存の [Cognitive Services の従来課金制の価格](https://azure.microsoft.com/pricing/details/cognitive-services/)で課金されます。 画像抽出の価格はプレビュー価格で課金され、[Azure Search 価格のページ](https://go.microsoft.com/fwlink/?linkid=2042400)で説明されています。 [詳細情報](cognitive-search-attach-cognitive-services.md)。

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.MergeSkill

## <a name="skill-parameters"></a>スキルのパラメーター

パラメーターの大文字と小文字は区別されます。

| パラメーター名     | 説明 |
|--------------------|-------------|
| insertPreTag  | 各挿入前に含まれる文字列。 既定値は `" "` です。 スペースを省略するには、値を `""` に設定します。  |
| insertPostTag | 各挿入後に含まれる文字列。 既定値は `" "` です。 スペースを省略するには、値を `""` に設定します。  |


##  <a name="sample-input"></a>サンプル入力
このスキルに使用可能な入力を提供する JSON ドキュメントは、次のようになります。

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
             "text": "The brown fox jumps over the dog" ,
             "itemsToInsert": ["quick", "lazy"],
             "offsets": [3, 28],
           }
      }
    ]
}
```

##  <a name="sample-output"></a>サンプル出力
この例は、*insertPreTag* が `" "` に設定され、*insertPostTag* が `""` に設定されているという想定で、以前の入力の出力を示しています。 

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
             "mergedText": "The quick brown fox jumps over the lazy dog" 
           }
      }
    ]
}
```

## <a name="extended-sample-skillset-definition"></a>拡張サンプル スキルの定義

テキスト マージ使用の一般的なシナリオは、イメージのテキスト表現 (OCR スキルからのテキストかイメージのキャプション) をドキュメントの content フィールドにマージすることです。 

次のサンプル スキルセットでは、OCR スキルを使用して、ドキュメントに埋め込まれた画像からテキストを抽出します。 次に、*merged_text* フィールドを作成して、元のテキストと各イメージから OCR されたテキストの両方を格納します。 

```json
{
  "description": "Extract text from images and merge with content text to produce merged_text",
  "skills":
  [
    {
        "name": "OCR skill",
        "description": "Extract text (plain and structured) from image.",
        "@odata.type": "#Microsoft.Skills.Vision.OcrSkill",
        "context": "/document/normalized_images/*",
        "defaultLanguageCode": "en",
        "detectOrientation": true,
        "inputs": [
          {
            "name": "image",
            "source": "/document/normalized_images/*"
          }
        ],
        "outputs": [
          {
            "name": "text"
          }
        ]
    },
    {
      "@odata.type": "#Microsoft.Skills.Text.MergeSkill",
      "description": "Create merged_text, which includes all the textual representation of each image inserted at the right location in the content field.",
      "context": "/document",
      "insertPreTag": " ",
      "insertPostTag": " "
      "inputs": [
        {
          "name":"text", "source": "/document/content"
        },
        {
          "name": "itemsToInsert", "source": "/document/normalized_images/*/text"
        },
        {
          "name":"offsets", "source": "/document/normalized_images/*/contentOffset" 
        }
      ],
      "outputs": [
        {
          "name": "mergedText", "targetName" : "merged_text"
        }
      ]
    }
  ]
}
```
上記のサンプル スキルセットは、normalized-images フィールドが存在していることを前提としています。 normalized-images フィールドを取得するには、以下に示すように、インデクサー定義内の *imageAction* 構成を *generateNormalizedImages* に設定します。

```json
{  
   //...rest of your indexer definition goes here ... 
  "parameters":{  
      "configuration":{  
         "dataToExtract":"contentAndMetadata",
         "imageAction":"generateNormalizedImages"
      }
   }
}
```

## <a name="see-also"></a>関連項目

+ [定義済みのスキル](cognitive-search-predefined-skills.md)
+ [スキルセットの定義方法](cognitive-search-defining-skillset.md)
+ [インデクサーの作成 (REST)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)
