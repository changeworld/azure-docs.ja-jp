---
title: Academic Knowledge API の定期刊行物エンティティの属性 | Microsoft Docs
description: Cognitive Services の Academic Knowledge API で定期刊行物エンティティに使用できる属性について説明します。
services: cognitive-services
author: alch-msft
manager: kuansanw
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: article
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: e782c57b8ac57028e070c16382c53ec76666e94d
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35372688"
---
# <a name="journal-entity"></a>定期刊行物エンティティ

<sub> *次の属性は、定期刊行物エンティティに固有です。(Ty = '2') </sub>

Name    |説明                            |type       | [操作]
------- | ------------------------------------- | --------- | ----------------------------
ID      |エンティティ ID                              |Int64      |等しい
DJN     |定期刊行物の標準化名                |String     |なし
JN      |定期刊行物の表示名                   |String     |等しい
CC      |定期刊行物の引用の総数           |Int32      |なし  
ECC     |定期刊行物の引用の推定総数 |Int32      |なし