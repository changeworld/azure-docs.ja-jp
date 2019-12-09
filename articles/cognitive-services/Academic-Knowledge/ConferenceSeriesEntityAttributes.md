---
title: 会議 (シリーズ) エンティティの属性 - Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: 会議 (シリーズ) エンティティで使用できる属性について説明します。
services: cognitive-services
author: darrine
manager: kuansanw
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: darrine
ROBOTS: NOINDEX
ms.openlocfilehash: ecd8042212987849d9d302642f03e59493235599
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2019
ms.locfileid: "74143913"
---
# <a name="conference-series-entity"></a>会議 (シリーズ) エンティティ

> [!NOTE]
> 次の属性は、会議 (シリーズ) エンティティに固有です。 (Ty = '3')

名前 | 説明 | 種類 | Operations
--- | --- | --- | ---
CC      |会議 (シリーズ) の引用の総数         |Int32      |なし  
CN      |会議 (シリーズ) の標準化名      |string     |等しい
DCN     |会議 (シリーズ) の表示名         |string     |なし
ECC     |会議 (シリーズ) の引用の推定総数   |Int32      |なし
F.FId   |会議 (シリーズ) に関連付けられている研究分野エンティティ ID |Int64  | 等しい
F.FN    |会議 (シリーズ) に関連付けられている研究分野名  | Equals、<br/>StartsWith
Id      |エンティティ ID                              |Int64      |等しい
PC    |会議 (シリーズ) の発行総数 |Int32 | なし
