---
title: Analyzers メソッド - Lingistic Analysis API
titlesuffix: Azure Cognitive Services
description: アナライザの REST API は、Linguistic Analysis API が現在サポートしているアナライザーの一覧を表示します。
services: cognitive-services
author: RichardSunMS
manager: cgronlun
ms.service: cognitive-services
ms.component: linguistic-analysis
ms.topic: conceptual
ms.date: 06/30/2016
ms.author: lesun
ROBOTS: NOINDEX
ms.openlocfilehash: 762ebf50999a88251dcd05824f2ed450cec97f04
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/03/2018
ms.locfileid: "48237400"
---
# <a name="analyzers-method"></a>Analyzers メソッド

> [!IMPORTANT]
> Linguistic Analysis プレビューは、2018 年 8 月 9 日に使用停止になりました。 テキスト処理と分析には、[Azure Machine Learning テキスト解析モジュール](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/text-analytics)の使用をお勧めします。

**analyzers** REST API は、サービスによって現在サポートされているアナライザーの一覧を提供します。
応答には、それらの[名前](Analyzer-Names.md)とそれぞれでサポートされる言語 (英語の場合は "en" など) が含まれます。

## <a name="request-parameters"></a>要求パラメーター
なし

<br>

## <a name="response-parameters"></a>応答パラメーター
Name | type | 説明
-----|------|--------------
languages | string のリスト | このアナライザーを使用できる 2 文字の ISO 言語コードの一覧。
id   | string | このアナライザーの一意の ID
kind | string | アナライザーの大まかな種類
specification | string | このアナライザーに使用される仕様の名前
implementation | string | このアナライザーの背後にあるモデルやアルゴリズムの説明

<br>
## <a name="example"></a>例
GET /analyzers

応答: JSON
```json
[
    {
        "id": "22A6B758-420F-4745-8A3C-46835A67C0D2",
        "languages": ["en"],
        "kind": "Constituency_Tree",  
        "specification": "PennTreebank3",
        "implementation": "SplitMerge"
    },
    {
        "id" : "4FA79AF1-F22C-408D-98BB-B7D7AEEF7F04",
        "languages": ["en"],
        "kind": "POS_Tags",
        "specification": "PennTreebank3",
        "implementation": "cmm"
    },
    {
        "id" : "08EA174B-BFDB-4E64-987E-602F85DA7F72",
        "languages": ["en"],
        "kind": "Tokens",
        "specification":"PennTreebank3",
        "implementation": "regexes"
    }
]
```
