---
title: Conference Series entity attributes in the Academic Knowledge API | Microsoft Docs
description: Learn about the attributes you can use with the Conference Series entity in Cognitive Services.
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
ms.openlocfilehash: 58d63c6a93c011d9a1460f25f8cb85f8b5f1c05b
ms.lasthandoff: 04/12/2017

---

# <a name="conference-series-entity"></a>Conference Series Entity

<sub> *Below attributes are specific to conference series entity. (Ty = '3') </sub>

Name    |Description                            |Type       | Operations
------- | ------------------------------------- | --------- | ----------------------------
Id        |Entity ID                                |Int64        |Equals
CN        |Conference series normalized name        |String        |Equals
DCN        |Conference series display name         |String        |none
CC        |Conference series total citation count            |Int32        |none  
ECC        |Conference series total estimated citation count    |Int32        |none
F.FId    |Field of study entity ID associated with the conference series |Int64     | Equals
F.FN    |Field of study name associated with the conference series     | Equals,<br/>StartsWith
SSD        |Satori data                             |String        |none

