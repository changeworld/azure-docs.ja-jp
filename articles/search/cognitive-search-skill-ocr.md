---
title: OCR 認知検索スキル (Azure Search) | Microsoft Docs
description: Azure Search のエンリッチメント パイプラインのイメージ ファイルからテキストを抽出します｡
services: search
manager: pablocas
author: luiscabrer
documentationcenter: ''
ms.assetid: ''
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: 48253b68a329d17f213369e8e4ee2e06bdf17992
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/20/2018
ms.locfileid: "34365826"
---
# <a name="ocr-cognitive-skill"></a>OCR 認知スキル

**OCR** スキルはイメージファイルからテキストを抽出します｡ サポートされているファイル形式は以下の通りです｡

+ .JPEG
+ .JPG
+ .PNG
+ .BMP
+ .GIF


## <a name="skill-parameters"></a>スキルのパラメーター

パラメーターの大文字と小文字は区別されます。

| パラメーター名     | [説明] |
|--------------------|-------------|
| detectOrientation | イメージの向きの自動検出を有効にします。 <br/> 有効な値: true / false｡|
|defaultLanguageCode |  入力テキストの言語コード｡ 以下の言語がサポートされています。`ar, cs, da, de, en, es, fi, fr, he, hu, it, ko, pt-br, pt`｡  言語コードが指定されないか null の場合､言語は自動検出されます｡|
| textExtractionAlgorithm | "printed" または "handwritten"｡ "handwritten" テキスト認識の OCR アルゴリズムは現在プレビュー段階であり､英語でのみサポートされます｡ |

## <a name="skill-inputs"></a>スキルの入力

| 入力名      | [説明]                                          |
|---------------|------------------------------------------------------|
| image         | 複合型｡ 現在は "/document/normalized_images" フィールドでのみ機能し､ ```imageAction``` が ```generateNormalizedImages``` に設定されている場合に､Azure Blob インデクサーによって生成されます｡ 詳しくは､[サンプル](#sample-output) をご覧ください｡|


## <a name="skill-outputs"></a>スキルの出力
| 出力名     | [説明]                   |
|---------------|-------------------------------|
| テキスト          | イメージから抽出されたプレーン テキスト｡   |
| layoutText    | 抽出されたテキストと､そのテキストが検出された場所を記述した複合型｡|


## <a name="sample-definition"></a>定義例

```json
{
    "skills": [
      {
        "description": "Extracts text (plain and structured) from image."
        "@odata.type": "#Microsoft.Skills.Vision.OcrSkill",
        "context": "/document/normalized_images/*",
        "defaultLanguageCode": null,
        "detectOrientation": true,
        "inputs": [
          {
            "name": "image",
            "source": "/document/normalized_images/*"
          }
        ],
        "outputs": [
          {
            "name": "text",
            "targetName": "myText"
          },
          {
            "name": "layoutText",
            "targetName": "myLayoutText"
          }
        ]
      }
    ]
 }
```
<a name="sample-output"></a>

## <a name="sample-text-and-layouttext-output"></a>テキストと layoutText 出力例

```json
{
  "text": "Hello World. -John",
  "layoutText":
  {
    "language" : "en",
    "text" : "Hello World. -John",
    "lines" : [
      {
        "boundingBox":
        [ {"x":10, "y":10}, {"x":50, "y":10}, {"x":50, "y":30},{"x":10, "y":30}],
        "text":"Hello World."
      },
      {
        "boundingBox": [ {"x":110, "y":10}, {"x":150, "y":10}, {"x":150, "y":30},{"x":110, "y":30}],
        "text":"-John"
      }
    ],
    "words": [
      {
        "boundingBox": [ {"x":110, "y":10}, {"x":150, "y":10}, {"x":150, "y":30},{"x":110, "y":30}],
        "text":"Hello"
      },
      {
        "boundingBox": [ {"x":110, "y":10}, {"x":150, "y":10}, {"x":150, "y":30},{"x":110, "y":30}],
        "text":"World."
      },
      {
        "boundingBox": [ {"x":110, "y":10}, {"x":150, "y":10}, {"x":150, "y":30},{"x":110, "y":30}],
        "text":"-John"
      }
    ]
  }
}
```

## <a name="sample-merging-text-extracted-from-embedded-images-with-the-content-of-the-document"></a>サンプル: 埋め込まれたイメージから抽出されたテキストとドキュメントの内容をマージ

Text Merger の一般的なユース ケースとしては､ドキュメントの content  フィールドへのイメージのテキスト表現 (OCR スキルからのテキストかイメージのキャプション) のマージがあります｡ 

以下のスキルセット例では､*merged_text* フィールドを作成することで､ドキュメントのテキスト コンテンツとともい､そのドキュメントに埋め込まれている各イメージから OCR で読み込まれたテキストが含めるようにしています｡ 

#### <a name="request-body-syntax"></a>要求本文の構文
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
      "insertPostTag": " ",
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
          "name": "mergedText", "targetname" : "merged_text"
        }
      ]
    }
  ]
}
```
上記のスキルセット例は､normalized-images フィールドが存在していることを前提としています｡ このフィールドを生成するには､以下に示すように､インデクサー定義内の *imageAction* 構成を *generateNormalizedImages* に設定します｡

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
+ [TextMerger スキル](cognitive-search-skill-textmerger.md)
+ [スキルセットの定義方法](cognitive-search-defining-skillset.md)
+ [インデクサーの作成 (REST)](ref-create-indexer.md)