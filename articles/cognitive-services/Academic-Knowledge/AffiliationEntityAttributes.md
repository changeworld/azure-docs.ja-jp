---
title: Academic Knowledge API の所属エンティティの属性
titlesuffix: Azure Cognitive Services
description: Academic Knowledge API で所属エンティティに使用できる属性について説明します。
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: 344b26b16f74cd44982e3c93fa69295792daa9a0
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2019
ms.locfileid: "55190641"
---
# <a name="affiliation-entity"></a>所属エンティティ

<sub> *次の属性は、所属エンティティに固有です。(Ty = '5') </sub>

Name    |説明                            |type       | 操作
------- | ------------------------------------- | --------- | ----------------------------
Id      |エンティティ ID                              |Int64      |等しい
AfN     |所属の標準化名        |String     |等しい
DAfN    |所属の表示名       |String     |なし
CC      |所属の引用の総数           |Int32      |なし  
ECC     |所属の引用の推定総数 |Int32      |なし

## <a name="extended-metadata-attributes"></a>拡張メタデータの属性 ##

Name    | 説明               
--------|---------------------------    
PC      |所属の論文数
