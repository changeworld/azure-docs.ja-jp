---
title: Lingistic Analysis API の Analyzers メソッド | Microsoft Docs
description: analyzers REST API は、Microsoft Cognitive Services のサービスによって現在サポートされているアナライザーの一覧を提供します。
services: cognitive-services
author: RichardSunMS
manager: wkwok
ms.service: cognitive-services
ms.component: linguistic-analysis
ms.topic: article
ms.date: 06/30/2016
ms.author: lesun
ms.openlocfilehash: 3fc243a0da77c5bae9009929f2b82e1353347752
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35372845"
---
# <a name="analyzers-method"></a>Analyzers メソッド

**analyzers** REST API は、サービスによって現在サポートされているアナライザーの一覧を提供します。
応答には、それらの[名前](Analyzer-Names.md)とそれぞれでサポートされる言語 (英語の場合は "en" など) が含まれます。

## <a name="request-parameters"></a>要求パラメーター
なし

<br>

## <a name="response-parameters"></a>応答パラメーター
Name | type | 説明
-----|------|--------------
languages | string のリスト | このアナライザーを使用できる 2 文字の ISO 言語コードの一覧。
id   | 文字列 | このアナライザーの一意の ID
kind | 文字列 | アナライザーの大まかな種類
specification | 文字列 | このアナライザーに使用される仕様の名前
implementation | 文字列 | このアナライザーの背後にあるモデルやアルゴリズムの説明

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
