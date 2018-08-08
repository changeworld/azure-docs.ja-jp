---
title: Speech SDK for Java を使用して音声から意図を認識する
titleSuffix: Microsoft Cognitive Services
description: >
  Speech SDK for Java を使用して音声から意図を認識するさまざまな方法を示します (ファイルから、マイクから)。
services: cognitive-services
author: wolfma61
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 07/16/2018
ms.author: wolfma
ms.openlocfilehash: 5ff0346d48386740f2b3cf4d40aa7e71219219fc
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/27/2018
ms.locfileid: "39331397"
---
# <a name="recognize-intents-from-speech-by-using-the-speech-sdk-for-java"></a>Speech SDK for Java を使用して音声から意図を認識する

[!include[Selector](../../../includes/cognitive-services-speech-service-how-to-recognize-intents-from-speech-selector.md)]

[!include[Intro](../../../includes/cognitive-services-speech-service-how-to-recognize-intents-from-speech-intro.md)]

[!include[Intro - top-level declarations](../../../includes/cognitive-services-speech-service-how-to-toplevel-declarations.md)]

[!code-java[Top-level declarations](~/samples-cognitive-services-speech-sdk/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/IntentRecognitionSamples.java#toplevel)]

[!include[Intro - using microphone](../../../includes/cognitive-services-speech-service-how-to-recognize-intents-from-speech-microphone.md)]

[!code-java[Intent Recognition Using Microphone](~/samples-cognitive-services-speech-sdk/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/IntentRecognitionSamples.java#IntentRecognitionWithMicrophone)]

[!include[Intro - using microphone and language](../../../includes/cognitive-services-speech-service-how-to-recognize-intents-from-speech-microphone-language.md)]

[!code-java[Intent Recognition Using Microphone In A Specified Language](~/samples-cognitive-services-speech-sdk/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/IntentRecognitionSamples.java#IntentRecognitionWithLanguage)]

[!include[Intro - continuous file](../../../includes/cognitive-services-speech-service-how-to-recognize-intents-from-speech-continuous.md)]

[!code-java[Intent Recognition Using Events From a File](~/samples-cognitive-services-speech-sdk/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/IntentRecognitionSamples.java#IntentContinuousRecognitionWithFile)]

[!include[Get the samples](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]

## <a name="next-steps"></a>次の手順

- [音声を認識する方法](how-to-recognize-speech-java.md)
- [音声を翻訳する方法](how-to-translate-speech-java.md)
