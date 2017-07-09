---
title: Affiliation entity attributes in the Academic Knowledge API | Microsoft Docs
description: Learn the attributes you can use with the Affiliation entity in the Academic Knowledge API in Cognitive Services.
services: cognitive-services
author: alch-msft
manager: kuansanw
ms.service: cognitive-services
ms.technology: academic-knowledge
ms.topic: article
ms.date: 03/23/2017
ms.author: alch
ms.translationtype: Human Translation
ms.sourcegitcommit: 245ce9261332a3d36a36968f7c9dbc4611a019b2
ms.openlocfilehash: 4fd06c7df4fd59ea815207a55ebc269453493de6
ms.contentlocale: ja-jp
ms.lasthandoff: 06/09/2017

---

# <a name="affiliation-entity"></a>Affiliation Entity

<sub> *Following attributes are specific to affiliation entity. (Ty = '5') </sub>

Name    |Description                            |Type       | Operations
------- | ------------------------------------- | --------- | ----------------------------
Id      |Entity ID                              |Int64      |Equals
AfN     |Affiliation normalized name        |String     |Equals
DAfN    |Affiliation display name       |String     |none
CC      |Affiliation total citation count           |Int32      |none  
ECC     |Affiliation total estimated citation count |Int32      |none

## <a name="extended-metadata-attributes"></a>Extended Metadata Attributes ##

Name    | Description               
--------|---------------------------    
PC      |Affiliation's paper count
