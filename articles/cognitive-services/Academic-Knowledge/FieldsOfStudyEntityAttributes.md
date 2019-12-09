---
title: 研究分野エンティティの属性 - Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Academic Knowledge API で研究分野エンティティに使用できる属性について説明します。
services: cognitive-services
author: darrine
manager: kuansanw
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: darrine
ROBOTS: NOINDEX
ms.openlocfilehash: b6bb5e0fd822541a0a4c10ef5f275d46664f8e10
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2019
ms.locfileid: "74146478"
---
# <a name="field-of-study-entity"></a>研究分野エンティティ

> [!NOTE]
> 次の属性は、研究分野エンティティに固有です。 (Ty = '6')

名前 | 説明 | 種類 | Operations
--- | --- | --- | ---
CC      |研究分野の引用の総数    |Int32      |なし  
DFN     |研究分野の表示名            |string     |なし
ECC     |研究分野の引用の推定総数|Int32      |なし
FL      |研究分野階層のレベル     |Int32      |Equals、IsBetween
FN      |研究分野の標準化名         |string     |等しい
FC.FId  |子研究分野の ID                |Int64      |等しい
FC.FN   |子研究分野の名前              |string     |等しい
FP.FId  |親研究分野の ID               |Int64      |等しい
FP.FN   |親研究分野の名前             |string     |等しい
Id      |エンティティ ID                              |Int64      |等しい
PC    | 研究分野の発行総数 | Int32 | なし
