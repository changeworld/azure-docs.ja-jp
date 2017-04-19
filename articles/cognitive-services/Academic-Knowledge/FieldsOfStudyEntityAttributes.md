---
title: Field of Study entity attributes in the Academic Knowledge API | Microsoft Docs
description: Learn the attributes you can use with the Field of Study entity in the Academic Knowledge API in Cognitive Services.
services: cognitive-services
author: alch-msft
manager: kuansanw
ms.service: cognitive-services
ms.technology: academic-knowledge
ms.topic: article
ms.date: 03/31/2017
ms.author: alch
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 27c1f15e09b80166ff3daef2899b21a9a21a1f33
ms.lasthandoff: 04/12/2017

---

# <a name="field-of-study-entity"></a>Field Of Study Entity

<sub> *Below attributes are specific to field of study entity. (Ty = '6') </sub>

Name    |Description                            |Type       | Operations
------- | ------------------------------------- | --------- | ----------------------------
Id        |Entity ID                                |Int64        |Equals
FN        |Field of study normalized name            |String        |Equals
DFN     |Field of study display name            |String        |none
CC        |Field of study total citation count    |Int32        |none  
ECC        |Field of total estimated citation count|Int32        |none
FL        |Level in fields of study hierarchy     |Int32        |Equals, <br/>IsBetween
FP.FN    |Parent field of study name             |String        |Equals
FP.FId     |Parent field of study ID                 |Int64         |Equals
SSD        |Satori data                             |String        |none
