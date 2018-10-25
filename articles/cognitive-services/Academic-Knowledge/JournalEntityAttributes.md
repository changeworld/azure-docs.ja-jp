---
title: 定期刊行物エンティティの属性 - Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Cognitive Services の Academic Knowledge API で定期刊行物エンティティに使用できる属性について説明します。
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: 45b2860b56e79c197d96998350131a142326157a
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/10/2018
ms.locfileid: "48902809"
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