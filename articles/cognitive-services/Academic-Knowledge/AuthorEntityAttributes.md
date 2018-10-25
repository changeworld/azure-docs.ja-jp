---
title: 作成者エンティティの属性 - Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Academic Knowledge API で作成者エンティティに使用できる属性について説明します。
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: 62e6da6d558a0494fb83115b1e307081099399d4
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/10/2018
ms.locfileid: "48900691"
---
# <a name="author-entity"></a>著者エンティティ
<sub> *次の属性は、著者エンティティに固有です。(Ty = '1') </sub>

Name    |説明                            |type       | [操作]
------- | ------------------------------------- | --------- | ----------------------------
ID      |エンティティ ID                              |Int64      |等しい
AuN     |著者の標準化名                 |String     |等しい
DAuN    |著者の表示名                    |String     |なし
CC      |著者の引用の総数            |Int32      |なし  
ECC     |著者の引用の推定総数  |Int32      |なし
E       |拡張メタデータ (「拡張メタデータの属性」の表をご覧ください)  |String     |なし  


## <a name="extended-metadata-attributes"></a>拡張メタデータの属性 ##

Name    | 説明               
--------|---------------------------    
LKA.Afn     | 著者に関連付けられている所属の表示名  
LKA.AfId        | 著者に関連付けられている所属のエンティティ ID