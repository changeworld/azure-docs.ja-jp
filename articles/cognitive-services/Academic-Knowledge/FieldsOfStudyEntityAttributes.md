---
title: Academic Knowledge API の研究分野エンティティの属性 | Microsoft Docs
description: Cognitive Services の Academic Knowledge API で研究分野エンティティに使用できる属性について説明します。
services: cognitive-services
author: alch-msft
manager: kuansanw
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: article
ms.date: 03/31/2017
ms.author: alch
ms.openlocfilehash: a8176370f5b2f63b7741f7e892ed5a8c775f19c1
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35372709"
---
# <a name="field-of-study-entity"></a>研究分野エンティティ

<sub> *次の属性は、研究分野エンティティに固有です。(Ty = '6') </sub>

Name    |説明                            |type       | [操作]
------- | ------------------------------------- | --------- | ----------------------------
ID      |エンティティ ID                              |Int64      |等しい
FN      |研究分野の標準化名         |String     |等しい
DFN     |研究分野の表示名            |String     |なし
CC      |研究分野の引用の総数    |Int32      |なし  
ECC     |研究分野の引用の推定総数|Int32      |なし
FL      |研究分野階層のレベル     |Int32      |Equals、 <br/>IsBetween
FP.FN   |親研究分野の名前             |String     |等しい
FP.FId  |親研究分野の ID               |Int64      |等しい
FC.FN   |子研究分野の名前              |String     |等しい
FC.FId  |子研究分野の ID                |Int64      |等しい