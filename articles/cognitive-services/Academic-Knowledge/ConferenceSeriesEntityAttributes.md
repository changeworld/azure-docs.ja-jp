---
title: 会議 (シリーズ) エンティティの属性 - Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: 会議 (シリーズ) エンティティで使用できる属性について説明します。
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: 38b4aa4c899668a68041f042ce6981ddd8c58219
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "61340198"
---
# <a name="conference-series-entity"></a>会議 (シリーズ) エンティティ

<sub> *次の属性は、会議 (シリーズ) エンティティに固有です。(Ty = '3') </sub>

Name    |説明                            |Type       | Operations
------- | ------------------------------------- | --------- | ----------------------------
Id      |エンティティ ID                              |Int64      |等しい
CN      |会議 (シリーズ) の標準化名      |string     |等しい
DCN     |会議 (シリーズ) の表示名         |string     |なし
CC      |会議 (シリーズ) の引用の総数         |Int32      |なし  
ECC     |会議 (シリーズ) の引用の推定総数   |Int32      |なし
F.FId   |会議 (シリーズ) に関連付けられている研究分野エンティティ ID |Int64  | 等しい
F.FN    |会議 (シリーズ) に関連付けられている研究分野名  | Equals、<br/>StartsWith
