---
title: Journal entity attributes in the Academic Knowledge API | Microsoft Docs
description: Learn the attributes you can use with the Journal entity in the Academic Knowledge API in Cognitive Services.
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
ms.openlocfilehash: de742252de1101eb4e963c7f75b012e4a5ea49a3
ms.contentlocale: ja-jp
ms.lasthandoff: 05/04/2017

---

# <a name="journal-entity"></a>Journal Entity

<sub> *Following attributes are specific to journal entity. (Ty = '2') </sub>

Name    |Description                            |Type       | Operations
------- | ------------------------------------- | --------- | ----------------------------
Id        |Entity ID                                |Int64        |Equals
DJN        |Journal normalized name                |String        |none
JN        |Journal display name                    |String        |Equals
CC        |Journal total citation count            |Int32        |none  
ECC        |Journal total estimated citation count    |Int32        |none
