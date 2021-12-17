---
title: テキスト分割コグニティブ スキル
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search の AI 強化パイプラインの長さに基づいて、テキストをチャンクまたはページに分割します。
author: LiamCavanagh
ms.author: liamca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/12/2021
ms.openlocfilehash: e2531bcb010b83f795cc7f77b68848c86c4c470a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131010671"
---
# <a name="text-split-cognitive-skill"></a>テキスト分割コグニティブ スキル

**テキスト分割** スキルは、テキストをテキストのチャンクに分割します。 テキストを特定の長さの文章またはページに分割するかどうかを指定できます。 このスキルは、他のスキル ダウンストリームでテキストの最大長の要件がある場合に、特に便利です。 

> [!NOTE]
> このスキルは Cognitive Services にバインドされていません。 これは課金対象外で、Cognitive Services の重要な要件はありません。

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.SplitSkill 

## <a name="skill-parameters"></a>スキルのパラメーター

パラメーターの大文字と小文字は区別されます。

| パラメーター名     | 説明 |
|--------------------|-------------|
| `textSplitMode`    | `pages` または `sentences` のいずれか | 
| `maximumPageLength` | `textSplitMode` が `pages` に設定されている場合にのみ適用されます。 これは、`String.Length` で計測される最大ページ長を文字数で表します。 最小値は 300、最大値は 100000、既定値は 10000 です。  アルゴリズムではできる限り文章の境界でテキストを分割しようとするため、各チャンクのサイズは `maximumPageLength` より若干小さくなる可能性があります。 | 
| `defaultLanguageCode` | (省略可能) 次の言語コードのいずれか: `am, bs, cs, da, de, en, es, et, fr, he, hi, hr, hu, fi, id, is, it, ja, ko, lv, no, nl, pl, pt-PT, pt-BR, ru, sk, sl, sr, sv, tr, ur, zh-Hans`。 既定値は英語 (en) です。 次の考慮事項があります。<ul><li>言語コードを指定することで、中国語、日本語、韓国語などの空白スペースのない言語で、単語が途中で分割されるのを避けることができます。</li><li>言語がわからない場合 (つまり、入力用のテキストを [LanguageDetectionSkill](cognitive-search-skill-language-detection.md) に分割する必要がある場合)、既定の英語 (en) で十分です。 </li></ul>  |


## <a name="skill-inputs"></a>スキルの入力

| パラメーター名       | 説明      |
|----------------------|------------------|
| `text`    | 部分文字列に分割するテキスト。 |
| `languageCode`    | (省略可能) ドキュメントの言語コード。 言語がわからない場合 (つまり、入力用のテキストを [LanguageDetectionSkill](cognitive-search-skill-language-detection.md) に分割する必要がある場合)、この入力を削除すると安全です。 上記の `defaultLanguageCode` パラメーターのサポート対象の一覧にその言語がない場合は、警告が出力され、テキストは分割されません。  |

## <a name="skill-outputs"></a>スキルの出力 

| パラメーター名     | 説明 |
|--------------------|-------------|
| `textItems`   | 抽出された部分文字列の配列。 |


##  <a name="sample-definition"></a>定義例

```json
{
    "@odata.type": "#Microsoft.Skills.Text.SplitSkill",
    "textSplitMode" : "pages", 
    "maximumPageLength": 1000,
    "defaultLanguageCode": "en",
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
            "name": "textItems",
            "targetName": "mypages"
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
            "data": {
                "text": "This is the loan application for Joe Romero, a Microsoft employee who was born in Chile and who then moved to Australia...",
                "languageCode": "en"
            }
        },
        {
            "recordId": "2",
            "data": {
                "text": "This is the second document, which will be broken into several pages...",
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
            "data": {
                "textItems": [
                    "This is the loan…",
                    "On the second page we…"
                ]
            }
        },
        {
            "recordId": "2",
            "data": {
                "textItems": [
                    "This is the second document...",
                    "On the second page of the second doc…"
                ]
            }
        }
    ]
}
```

## <a name="error-cases"></a>エラーになる場合
言語がサポートされていない場合は、警告が生成されます。

## <a name="see-also"></a>関連項目

+ [組み込みのスキル](cognitive-search-predefined-skills.md)
+ [スキルセットの定義方法](cognitive-search-defining-skillset.md)
