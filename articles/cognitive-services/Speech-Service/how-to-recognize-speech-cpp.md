---
title: Speech SDK for C++ を使用して音声を認識する
titleSuffix: Azure Cognitive Services
description: Speech SDK for C++ を使用して音声を認識するさまざまな方法を示します (ファイルから、マイクから、カスタマイズしたモデルで、継続的に、1 回限りで)。
services: cognitive-services
author: wolfma61
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: wolfma
ms.openlocfilehash: 2289a41fad552082e93f4d0d51ff76c6f4c241da
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/19/2018
ms.locfileid: "49470531"
---
# <a name="recognize-speech-by-using-the-speech-sdk-for-c"></a>Speech SDK for C++ を使用して音声を認識する

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-how-to-recognize-speech-selector.md)]

[!INCLUDE [Introduction](../../../includes/cognitive-services-speech-service-how-to-recognize-speech-intro.md)]

[!INCLUDE [Introduction for top-level declarations](../../../includes/cognitive-services-speech-service-how-to-toplevel-declarations.md)]

[!code-cpp[Top-level declarations](~/samples-cognitive-services-speech-sdk/samples/cpp/windows/console/samples/speech_recognition_samples.cpp#toplevel)]

[!INCLUDE [Introduction to using a microphone](../../../includes/cognitive-services-speech-service-how-to-recognize-speech-microphone.md)]

[!code-cpp[Speech recognition by using a microphone](~/samples-cognitive-services-speech-sdk/samples/cpp/windows/console/samples/speech_recognition_samples.cpp#SpeechRecognitionWithMicrophone)]

[!INCLUDE [Introduction to using customized recognition](../../../includes/cognitive-services-speech-service-how-to-recognize-speech-customized.md)]

[!code-cpp[Speech recognition by using a customized model](~/samples-cognitive-services-speech-sdk/samples/cpp/windows/console/samples/speech_recognition_samples.cpp#SpeechRecognitionUsingCustomizedModel)]

[!INCLUDE [Introduction to using a continuous file](../../../includes/cognitive-services-speech-service-how-to-recognize-speech-continuous.md)]

[!code-cpp[Continuous speech recognition](~/samples-cognitive-services-speech-sdk/samples/cpp/windows/console/samples/speech_recognition_samples.cpp#SpeechContinuousRecognitionWithFile)]

[!INCLUDE [Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
この記事で使用されているコードを samples/cpp/windows/console フォルダーから探します。

## <a name="next-steps"></a>次の手順

- [音声から意図を認識する方法](how-to-recognize-intents-from-speech-cpp.md)
- [音声を翻訳する方法](how-to-translate-speech-cpp.md)

