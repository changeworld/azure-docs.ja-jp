---
title: Academic Knowledge API の著者エンティティの属性 | Microsoft Docs
description: Cognitive Services の Academic Knowledge API で著者エンティティに使用できる属性について説明します。
services: cognitive-services
author: alch-msft
manager: kuansanw
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: article
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: afcc3257c49522a4631c4aae0e0c2b88373f9af1
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35372701"
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