---
title: 定期刊行物エンティティの属性 - Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Cognitive Services の Academic Knowledge API で定期刊行物エンティティに使用できる属性について説明します。
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: ffb159dc684b4b6663dcb966706d4745ab88a403
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "61337807"
---
# <a name="journal-entity"></a>定期刊行物エンティティ

<sub> *次の属性は、定期刊行物エンティティに固有です。(Ty = '2') </sub>

Name    |説明                            |Type       | Operations
------- | ------------------------------------- | --------- | ----------------------------
Id      |エンティティ ID                              |Int64      |等しい
DJN     |定期刊行物の標準化名                |string     |なし
JN      |定期刊行物の表示名                   |string     |等しい
CC      |定期刊行物の引用の総数           |Int32      |なし  
ECC     |定期刊行物の引用の推定総数 |Int32      |なし
