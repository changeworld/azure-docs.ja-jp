---
title: Academic Graph entity attributes for the Academic Knowledge API | Microsoft Docs
description: Learn about the entity attributes you can use with the Academic Graph in the Academic Knowledge API.
services: cognitive-services
author: alch-msft
manager: kuansanw
ms.service: cognitive-services
ms.technology: academic-knowledge
ms.topic: article
ms.date: 03/27/2017
ms.author: alch
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 9240dda95ce06d9f31340afe9ae8fc48593f211c
ms.lasthandoff: 04/12/2017

---

# <a name="entity-attributes"></a>Entity Attributes

The academic graph is composed of 7 types of entity. All entities will have a Entity ID and a Entity type.

## <a name="common-entity-attributes"></a>Common Entity Attributes
Name    |Description                |Type       | Operations
------- | ------------------------- | --------- | ----------------------------
Id        |Entity ID                    |Int64        |Equals
Ty         |Entity type                 |enum    |Equals

## <a name="entity-type-enum"></a>Entity type enum
Name                                                             |value
----------------------------------------------------------------|-----
[Paper](PaperEntityAttributes.md)                                |0
[Author](AuthorEntityAttributes.md)                                |1
[Journal](JournalEntityAttributes.md)                             |2
[Conference Series](JournalEntityAttributes.md)                    |3
[Conference Instance](ConferenceInstanceEntityAttributes.md)    |4
[Affiliation](AffiliationEntityAttributes.md)                    |5
[Field Of Study](FieldsOfStudyEntityAttributes.md)                        |6


