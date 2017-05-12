---
title: Emotion API Python quick start | Microsoft Docs
description: Get information and code samples to help you quickly get started using the Emotion API with Python in Cognitive Services.
services: cognitive-services
author: v-royhar
manager: yutkuo
ms.service: cognitive-services
ms.technology: emotion
ms.topic: article
ms.date: 02/22/2017
ms.author: anroth
ms.translationtype: Human Translation
ms.sourcegitcommit: 18d4994f303a11e9ce2d07bc1124aaedf570fc82
ms.openlocfilehash: 354c6f2fa73c30cc0ca1d575adc4de71e28d1a3f
ms.contentlocale: ja-jp
ms.lasthandoff: 05/09/2017

---

# <a name="emotion-api-python-quick-start"></a>Emotion API Python Quick Start
This article provides information and code samples to help you quickly get started using the [Emotion API Recognize method](https://dev.projectoxford.ai/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa) with Python to recognize the emotions expressed by one or more people in an image. 

## <a name="prerequisite"></a>Prerequisite
* Get your free Subscription Key [here](https://azure.microsoft.com/en-us/try/cognitive-services/)

## <a name="recognize-emotions-python-example-request"></a>Recognize Emotions Python Example Request

```Python
########### Python 2.7 #############
import httplib, urllib, base64

headers = {
    # Request headers. Replace the placeholder key below with your subscription key.
    'Content-Type': 'application/json',
    'Ocp-Apim-Subscription-Key': '13hc77781f7e4b19b5fcdd72a8df7156',
}

params = urllib.urlencode({
})

# Replace the example URL below with the URL of the image you want to analyze.
body = "{ 'url': 'http://example.com/picture.jpg' }"

try:
    conn = httplib.HTTPSConnection('westus.api.cognitive.microsoft.com')
    conn.request("POST", "/emotion/v1.0/recognize?%s" % params, body, headers)
    response = conn.getresponse()
    data = response.read()
    print(data)
    conn.close()
except Exception as e:
    print("[Errno {0}] {1}".format(e.errno, e.strerror))

####################################

########### Python 3.2 #############
import http.client, urllib.request, urllib.parse, urllib.error, base64, sys

headers = {
    # Request headers. Replace the placeholder key below with your subscription key.
    'Content-Type': 'application/json',
    'Ocp-Apim-Subscription-Key': '13hc77781f7e4b19b5fcdd72a8df7156',
}

params = urllib.parse.urlencode({
})

# Replace the example URL below with the URL of the image you want to analyze.
body = "{ 'url': 'http://example.com/picture.jpg' }"

try:
    conn = http.client.HTTPSConnection('westus.api.cognitive.microsoft.com')
    conn.request("POST", "/emotion/v1.0/recognize?%s" % params, body, headers)
    response = conn.getresponse()
    data = response.read()
    print(data)
    conn.close()
except Exception as e:
    print(e.args)
####################################
```

## <a name="recognize-emotions-sample-response"></a>Recognize Emotions Sample Response
A successful call returns an array of face entries and their associated emotion scores, ranked by face rectangle size in descending order. An empty response indicates that no faces were detected. An emotion entry contains the following fields:
* faceRectangle - Rectangle location of face in the image.
* scores - Emotion scores for each face in the image. 

```json
application/json 
[
  {
    "faceRectangle": {
      "left": 68,
      "top": 97,
      "width": 64,
      "height": 97
    },
    "scores": {
      "anger": 0.00300731952,
      "contempt": 5.14648448E-08,
      "disgust": 9.180124E-06,
      "fear": 0.0001912825,
      "happiness": 0.9875571,
      "neutral": 0.0009861537,
      "sadness": 1.889955E-05,
      "surprise": 0.008229999
    }
  }
]


