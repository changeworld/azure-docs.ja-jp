---
title: The Analyzers method in the Lingistic Analysis API  | Microsoft Docs
description: The analyzers REST API provides a list of analyzers that are currently supported by the service in Microsoft Cognitive Services.
services: cognitive-services
author: RichardSunMS
manager: wkwok
ms.service: cognitive-services
ms.technology: linguistic-analysis-api
ms.topic: article
ms.date: 06/30/2016
ms.author: lesun
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: da74b5fbbb9726805def9cedd297fc1fa5944d75
ms.lasthandoff: 04/12/2017

---

# <a name="analyzers-method"></a>Analyzers Method

The **analyzers** REST API provides a list of analyzers currently supported by the service.
The response includes their [names](Analyzer-Names.md) and the languages supported by each (such as "en" for English).

## <a name="request-parameters"></a>Request parameters
None

<br>

## <a name="response-parameters"></a>Response parameters
Name | Type | Description
-----|------|--------------
languages | list of strings | list of two letter ISO language codes for which this analyzer can be used.
id   | string | unique ID for this analyzer
kind | string | the broad type of analyzer here
specification | string | the name of the specification used for this analyzer
implementation | string | description of the model and/or algorithm behind this analyzer

<br>
## <a name="example"></a>Example
GET /analyzers

Response: JSON
```json
[
    {
        "id": "22A6B758-420F-4745-8A3C-46835A67C0D2",
        "languages": ["en"],
        "kind": "Constituency_Tree",  
        "specification": "PennTreebank3", 
        "implementation": "SplitMerge"
    }, 
    {
        "id" : "4FA79AF1-F22C-408D-98BB-B7D7AEEF7F04",
        "languages": ["en"],
        "kind": "POS_Tags", 
        "specification": "PennTreebank3", 
        "implementation": "cmm"
    },
    {
        "id" : "08EA174B-BFDB-4E64-987E-602F85DA7F72",
        "languages": ["en"],
        "kind": "Tokens", 
        "specification":"PennTreebank3", 
        "implementation": "regexes"
    } 
]
```

