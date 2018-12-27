---
title: Academic Knowledge API の所属エンティティの属性
titlesuffix: Azure Cognitive Services
description: Academic Knowledge API で所属エンティティに使用できる属性について説明します。
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: 177fe9da8bbe821a69eae02d89a225e5d4009331
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/10/2018
ms.locfileid: "48900480"
---
# <a name="affiliation-entity"></a>所属エンティティ

<sub> *次の属性は、所属エンティティに固有です。(Ty = '5') </sub>

Name    |説明                            |type       | [操作]
------- | ------------------------------------- | --------- | ----------------------------
ID      |エンティティ ID                              |Int64      |等しい
AfN     |所属の標準化名        |String     |等しい
DAfN    |所属の表示名       |String     |なし
CC      |所属の引用の総数           |Int32      |なし  
ECC     |所属の引用の推定総数 |Int32      |なし

## <a name="extended-metadata-attributes"></a>拡張メタデータの属性 ##

Name    | 説明               
--------|---------------------------    
PC      |所属の論文数