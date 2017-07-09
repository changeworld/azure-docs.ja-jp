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
ms.translationtype: Human Translation
ms.sourcegitcommit: 245ce9261332a3d36a36968f7c9dbc4611a019b2
ms.openlocfilehash: df6bf2fe4cdf1bc7554669c9931145d7397f1c36
ms.contentlocale: ja-jp
ms.lasthandoff: 06/09/2017

---

# <a name="field-of-study-entity"></a>Field Of Study Entity

<sub> *Following attributes are specific to field of study entity. (Ty = '6') </sub>

Name    |Description                            |Type       | Operations
------- | ------------------------------------- | --------- | ----------------------------
Id      |Entity ID                              |Int64      |Equals
FN      |Field of study normalized name         |String     |Equals
DFN     |Field of study display name            |String     |none
CC      |Field of study total citation count    |Int32      |none  
ECC     |Field of total estimated citation count|Int32      |none
FL      |Level in fields of study hierarchy     |Int32      |Equals, <br/>IsBetween
FP.FN   |Parent field of study name             |String     |Equals
FP.FId  |Parent field of study ID               |Int64      |Equals
FC.FN   |Child field of study name              |String     |Equals
FC.FId  |Child field of study ID                |Int64      |Equals
