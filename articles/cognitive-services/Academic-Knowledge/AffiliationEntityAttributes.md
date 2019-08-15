---
title: Academic Knowledge API の所属エンティティの属性
titlesuffix: Azure Cognitive Services
description: Academic Knowledge API で所属エンティティに使用できる属性について説明します。
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ROBOTS: NOINDEX
ms.openlocfilehash: 333875472d9b859196c4d828061301b9d32c4d5a
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705111"
---
# <a name="affiliation-entity"></a>所属エンティティ

<sub> *次の属性は、所属エンティティに固有です。(Ty = '5') </sub>

名前    |説明                            |Type       | Operations
------- | ------------------------------------- | --------- | ----------------------------
Id      |エンティティ ID                              |Int64      |等しい
AfN     |所属の標準化名        |string     |等しい
DAfN    |所属の表示名       |string     |なし
CC      |所属の引用の総数           |Int32      |なし  
ECC     |所属の引用の推定総数 |Int32      |なし

## <a name="extended-metadata-attributes"></a>拡張メタデータの属性 ##

名前    | 説明               
--------|---------------------------    
PC      |所属の論文数
