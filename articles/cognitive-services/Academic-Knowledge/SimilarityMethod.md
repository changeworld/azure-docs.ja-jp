---
title: Similarity method in the Academic Knowledge API | Microsoft Docs
description: Use the Similarity method to calculate the academic similarity of two strings in Microsoft Cognitive Services.
services: cognitive-services
author: alch-msft
manager: kuansanw
ms.service: cognitive-services
ms.technology: academic-knowledge
ms.topic: article
ms.date: 01/18/2017
ms.author: alch
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 49b715be436fcc44db0c151773fcc3daf0c50163
ms.lasthandoff: 04/12/2017

---

# <a name="similarity-method"></a>Similarity Method

The **similarity** REST API is used to calculate the academic similarity between two strings. 
<br>

**REST endpoint:**
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/similarity?
```

## <a name="request-parameters"></a>Request Parameters
Parameter        |Data Type      |Required | Description
----------|----------|----------|------------
**s1**        |String   |Yes  |String* to be compared
**s2**        |String   |Yes  |String* to be compared
<sub> *Strings to compare have a maxium length of 1MB. </sub>
<br>
## <a name="response"></a>Response
Name | Description
--------|---------
**SimilarityScore**        |A floating point value representing the cosine similarity of s1 and s2, with values closer to 1.0 meaning more similar and values closer to -1.0 meaning less
<br>

## <a name="successerror-conditions"></a>Success/Error Conditions
HTTP Status | Reason | Response
-----------|----------|--------
**200**         |Success | Floating point number
**400**         | Bad request or request invalid | Error message      
**500**         |Internal server error | Error message
**Timed out**     | Request timed out.  | Error message
<br>
##<a name="example-calculate-similarity-of-two-partial-abstracts"></a>Example: Calculate similarity of two partial abstracts
####<a name="request"></a>Request:
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/similarity?s1=Using complementary priors, we derive a fast greedy algorithm that can learn deep directed belief networks one layer at a time, provided the top two layers form an undirected associative memory
&s2=Deepneural nets with a large number of parameters are very powerful machine learning systems. However, overfitting is a serious problem in such networks
```
In this example, we generate the similarity score between two partial abstracts using the **similarity** API.
####<a name="response"></a>Response:
```
0.520
```
####<a name="remarks"></a>Remarks:
The similarity score is determined by assessing the academic concepts through word embedding. In this example, 0.52 means that the two partial abstracts are somewhat similar.
<br>
