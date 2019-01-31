---
title: 会議 (シリーズ) エンティティの属性 - Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: 会議 (シリーズ) エンティティで使用できる属性について説明します。
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: ff71b489cce01d8d6ea29e09905d7d3ac8429e34
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2019
ms.locfileid: "55155606"
---
# <a name="conference-series-entity"></a>会議 (シリーズ) エンティティ

<sub> *次の属性は、会議 (シリーズ) エンティティに固有です。(Ty = '3') </sub>

Name    |説明                            |type       | 操作
------- | ------------------------------------- | --------- | ----------------------------
Id      |エンティティ ID                              |Int64      |等しい
CN      |会議 (シリーズ) の標準化名      |String     |等しい
DCN     |会議 (シリーズ) の表示名         |String     |なし
CC      |会議 (シリーズ) の引用の総数         |Int32      |なし  
ECC     |会議 (シリーズ) の引用の推定総数   |Int32      |なし
F.FId   |会議 (シリーズ) に関連付けられている研究分野エンティティ ID |Int64  | 等しい
F.FN    |会議 (シリーズ) に関連付けられている研究分野名  | Equals、<br/>StartsWith
