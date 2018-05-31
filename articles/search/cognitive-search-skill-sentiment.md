---
title: センチメント コグニティブ検索スキル (Azure Search) | Microsoft Docs
description: Azure Search のエンリッチメント パイプラインのテキストからセンチメントを抽出します。
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
ms.openlocfilehash: 1ddbba5b881cd05a997cd24a9396d5b722376e6f
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/07/2018
ms.locfileid: "33786761"
---
#   <a name="sentiment-cognitive-skill"></a>センチメント コグニティブ スキル

**センチメント** スキルは、正と負の連続体に沿って非構造化テキストを評価し、各レコードに対して、0 から 1 の数値スコアを返します。 1 に近いスコアは正のセンチメント、0 に近いスコアは負のセンチメントを示します。

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.SentimentSkill

## <a name="data-limits"></a>データ制限
レコードの最大サイズは、`String.Length` によって測定されるため、5000 文字にする必要があります。 データをセンチメント アナライザーに送信する前に分割する必要がある場合は、[テキスト分割スキル](cognitive-search-skill-textsplit.md)を使用します。


## <a name="skill-parameters"></a>スキルのパラメーター

パラメーターの大文字と小文字は区別されます。

| パラメーター名 |                      |
|----------------|----------------------|
| defaultLanguageCode | (省略可能) 言語を明示的に指定しないドキュメントに適用する言語コード。 <br/> [サポートされる言語の完全な一覧](../cognitive-services/text-analytics/text-analytics-supported-languages.md)を参照 |

## <a name="skill-inputs"></a>スキルの入力 

| 入力名 | [説明] |
|--------------------|-------------|
| テキスト | 分析されるテキスト。|
| languageCode  |  (省略可能) レコードの言語を示す文字列。 このプロパティが指定されていない場合の既定値は "en" です。 <br/>[サポートされる言語の完全な一覧](../cognitive-services/text-analytics/text-analytics-supported-languages.md)を参照してください。|

## <a name="skill-outputs"></a>スキルの出力

| 出力名 | [説明] |
|--------------------|-------------|
| スコア | 分析されたテキストのセンチメントを表す 0 から 1 の値。 0 に近い値は負のセンチメント、0.5 に近い値は中立のセンチメント、1 に近い値は正のセンチメントを持ちます。|


##  <a name="sample-definition"></a>定義例

```json
{
    "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
    "inputs": [
        {
            "name": "text",
            "source": "/document/content"
        },
        {
            "name": "languageCode",
            "source": "/document/languagecode"
        }
    ],
    "outputs": [
        {
            "name": "score",
            "targetName": "mySentiment"
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
                "text": "I had a terrible time at the hotel. The staff was rude and the food was awful.",
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
                "score": 0.01
            }
        }
    ]
}
```

## <a name="notes"></a>メモ
空の場合は、これらのレコードに対してセンチメント スコアは返されません。

## <a name="error-cases"></a>エラーになる場合
言語がサポートされていない場合、エラーが生成され、センチメント スコアは返されません。

## <a name="see-also"></a>関連項目

+ [定義済みのスキル](cognitive-search-predefined-skills.md)
+ [スキルセットの定義方法](cognitive-search-defining-skillset.md)