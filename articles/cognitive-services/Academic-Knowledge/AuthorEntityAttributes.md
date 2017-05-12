---
title: Author entity attributes in the Academic Knowledge API | Microsoft Docs
description: Learn the attributes you can use with the Author entity in the Academic Knowledge API in Cognitive Services.
services: cognitive-services
author: alch-msft
manager: kuansanw
ms.service: cognitive-services
ms.technology: academic-knowledge
ms.topic: article
ms.date: 03/23/2017
ms.author: alch
ms.translationtype: Human Translation
ms.sourcegitcommit: 7c4d5e161c9f7af33609be53e7b82f156bb0e33f
ms.openlocfilehash: 30b53524834e7e83edfa917b9ec3329cc34354dd
ms.contentlocale: ja-jp
ms.lasthandoff: 05/04/2017

---

# <a name="author-entity"></a>Author Entity
<sub> *Following attributes are specific to author entity. (Ty = '1') </sub>

Name    |Description                            |Type       | Operations
------- | ------------------------------------- | --------- | ----------------------------
Id        |Entity ID                                |Int64        |Equals
AuN        |Author normalized name                    |String        |Equals
DAuN    |Author display name                    |String        |none
CC        |Author total citation count            |Int32        |none  
ECC        |Author total estimated citation count    |Int32        |none
E        |Extended metadata (see "Extended Meta Attributes" table )     |String     |none  


## <a name="extended-metadata-attributes"></a>Extended Metadata Attributes ##

Name    | Description               
--------|---------------------------    
LKA.Afn        | affiliation's display name associated with the author  
LKA.AfId        | affiliation's entity ID associated with the author
