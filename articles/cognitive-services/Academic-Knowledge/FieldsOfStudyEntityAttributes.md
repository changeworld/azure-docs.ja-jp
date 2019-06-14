---
title: 研究分野エンティティの属性 - Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Academic Knowledge API で研究分野エンティティに使用できる属性について説明します。
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/31/2017
ms.author: alch
ms.openlocfilehash: e9d6badf76efd03c0520a728af7b3e47b25f200a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "61339587"
---
# <a name="field-of-study-entity"></a>研究分野エンティティ

<sub> *次の属性は、研究分野エンティティに固有です。(Ty = '6') </sub>

Name    |説明                            |Type       | Operations
------- | ------------------------------------- | --------- | ----------------------------
Id      |エンティティ ID                              |Int64      |等しい
FN      |研究分野の標準化名         |string     |等しい
DFN     |研究分野の表示名            |string     |なし
CC      |研究分野の引用の総数    |Int32      |なし  
ECC     |研究分野の引用の推定総数|Int32      |なし
FL      |研究分野階層のレベル     |Int32      |Equals、 <br/>IsBetween
FP.FN   |親研究分野の名前             |string     |等しい
FP.FId  |親研究分野の ID               |Int64      |等しい
FC.FN   |子研究分野の名前              |string     |等しい
FC.FId  |子研究分野の ID                |Int64      |等しい
