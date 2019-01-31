---
title: 定期刊行物エンティティの属性 - Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Cognitive Services の Academic Knowledge API で定期刊行物エンティティに使用できる属性について説明します。
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: 397293c17320ef75daa5c31af76d49183b6c435c
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2019
ms.locfileid: "55193830"
---
# <a name="journal-entity"></a>定期刊行物エンティティ

<sub> *次の属性は、定期刊行物エンティティに固有です。(Ty = '2') </sub>

Name    |説明                            |type       | 操作
------- | ------------------------------------- | --------- | ----------------------------
Id      |エンティティ ID                              |Int64      |等しい
DJN     |定期刊行物の標準化名                |String     |なし
JN      |定期刊行物の表示名                   |String     |等しい
CC      |定期刊行物の引用の総数           |Int32      |なし  
ECC     |定期刊行物の引用の推定総数 |Int32      |なし
