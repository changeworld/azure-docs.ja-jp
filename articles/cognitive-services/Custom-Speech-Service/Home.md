---
title: Custom Speech Service overview | Microsoft Docs
description: The Custom Speech Service is a cloud-based service that enables users to customize speech models for speech-to-text transcription.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.technology: custom-speech-service
ms.topic: article
ms.date: 02/07/2017
ms.author: panosper
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: f0016d1313af0f7e8a339b382282c5e93e00c27c
ms.lasthandoff: 04/12/2017

---

# <a name="custom-speech-service"></a>Custom Speech Service

Welcome to Microsoft's Custom Speech Service. Custom Speech Service is a cloud-based service that provides users with the ability to customize speech models for Speech-to-Text transcription.
To use the Custom Speech Service refer to the [Custom Speech Service Portal](https://cris.ai)

## <a name="what-is-the-custom-speech-service"></a>What is the Custom Speech Service
The Custom Speech Service enables you to create customized language models and acoustic models tailored to your application and your users. By uploading your specific speech and/or text data to Custom Speech Service, you can create custom models that can be used in conjunction with Microsoft’s existing state-of-the-art speech models.

For example, if you’re adding voice interaction to a mobile phone, tablet or PC app, you can create a custom language model that can be combined with Microsoft’s acoustic model to create a speech-to-text endpoint designed especially for your app. If your application is designed for use in a particular environment or by a particular user population, you can also create and deploy a custom acoustic model with the Custom Speech Service.

## <a name="why-use-the-custom-speech-service"></a>Why use the Custom Speech Service
While the Microsoft Speech-To-Text engine is world-class, it is targeted toward the scenarios described above. However, if you expect voice queries to your application to contain particular vocabulary items, such as product names or jargon that rarely occur in typical speech, it is likely that you can obtain improved performance by customizing the language model.

For example, if you were building an app to search MSDN by voice, it’s likely that terms like “object-oriented” or “namespace” or “dot net” will appear more frequently than in typical voice applications. Customizing the language model will enable the system to learn this.

## <a name="how-does-it-work"></a>How does it work
Speech recognition systems are composed of several components that work together. Two of the most important components are the acoustic model and the language model.

The acoustic model is a classifier that labels short fragments of audio into one of a number of phonemes, or sound units, in a given language. For example, the word “speech” is comprised of four phonemes “s p iy ch”. These classifications are made on the order of 100 times per second.

The language model is a probability distribution over sequences of words. The language model helps the system decide among sequences of words that sound similar, based on the likelihood of the word sequences themselves. For example, “recognize speech” and “wreck a nice beach” sound alike but the first hypothesis is far more likely to occur, and therefore will be assigned a higher score by the language model.

For more details about how to use the Custom Speech Service, refer to the [Custom Speech Service Portal] (https://cris.ai).

 * [Get Started](GetStarted.md)
 * [FAQ](FAQ.md)
 * [Glossary](Glossary.md)

