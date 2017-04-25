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
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: cf7a0bd3f1ece12ee2b00d32ae80e5b4c82f6bcf
ms.lasthandoff: 04/12/2017

---

# <a name="author-entity"></a>Author Entity
<sub> *Below attributes are specific to author entity. (Ty = '1') </sub>

Name    |Description                            |Type       | Operations
------- | ------------------------------------- | --------- | ----------------------------
Id        |Entity ID                                |Int64        |Equals
AuN        |Author normalized name                    |String        |Equals
DAuN    |Author display name                    |String        |none
CC        |Author total citation count            |Int32        |none  
ECC        |Author total estimated citation count    |Int32        |none
E        |Extended metadata (see table below)     |String     |none  
SSD        |Satori data                             |String        |none

## <a name="extended-metadata-attributes"></a>Extended Metadata Attributes ##

Name    | Description               
--------|---------------------------    
LKA.Afn        | affiliation's display name associated with the author  
LKA.AfId        | affiliation's entity ID associated with the author
