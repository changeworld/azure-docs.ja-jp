---
title: Computer Vision API Python tutorial | Microsoft Docs
description: Learn how to use the Computer Vision API with Python by using Jupyter notebooks in Microsoft Cognitive Services. Visualize your results using popular libraries.
services: cognitive-services
author: JuliaNik
manager: ytkuo
ms.service: cognitive-services
ms.technology: computer-vision
ms.topic: article
ms.date: 02/25/2017
ms.author: juliakuz
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: b7b8e196865bed2a7862129e701f07805f103972
ms.contentlocale: ja-jp
ms.lasthandoff: 05/10/2017

---

# <a name="computer-vision-api-python-tutorial"></a>Computer Vision API Python Tutorial

This tutorial shows you how to use the Computer Vision API in Python and how to visualize your results using some popular libraries. Use Jupyter to run the tutorial. To learn how to get started with interactive Jupyter notebooks, refer to: [Jupyter Documementation](http://jupyter.readthedocs.io/en/latest/index.html). 

### <a name="opening-the-tutorial-notebook-in-jupyter"></a>Opening the Tutorial Notebook in Jupyter 

1. Navigate to the [tutorial notebook in GitHub](https://github.com/Microsoft/Cognitive-Vision-Python). 
2. Click on the green button to clone or download the tutorial. 
3. Open a command prompt and go to the folder _Cognitive-Vision-Python-master\Jupyter Notebook_. 
4. Run the command **jupyter notebook** from the command prompt. This will start Jupyter.
5. In the Jupyter window, click on _Computer Vision API Example.ipynb_ to open the tutorial notebook 

### <a name="running-the-tutorial"></a>Running the Tutorial

To use this notebook, you will need a subscription key for the Computer Vision API. Visit the [Subscription page](https://azure.microsoft.com/en-us/try/cognitive-services/) to sign up. On the “Sign in” page, use your Microsoft account to sign in and you will be able to subscribe and get free keys. After completing the sign-up process, paste your key into the variables section of the notebook (reproduced below). Either the primary or the secondary key works. Make sure to enclose the key in quotes to make it a string.

```python
# Variables

_url = 'https://westus.api.cognitive.microsoft.com/vision/v1/analyses'
_key = None #Here you have to paste your primary key
_maxNumRetries = 10
```

