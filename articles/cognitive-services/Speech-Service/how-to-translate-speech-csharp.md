---
title: Speech SDK for C# を使用して音声を翻訳する
titleSuffix: Microsoft Cognitive Services
description: Speech SDK for C# を使用して音声を翻訳する方法を示します。
services: cognitive-services
author: wolfma61
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 07/16/2018
ms.author: wolfma
ms.openlocfilehash: 928d8dcf9a074eb78c17393d8dc8e11431ef3d55
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/27/2018
ms.locfileid: "39331273"
---
# <a name="translate-speech-by-using-the-speech-sdk-for-c"></a>Speech SDK for C# を使用して音声を翻訳する

[!include[Selector](../../../includes/cognitive-services-speech-service-how-to-translate-speech-selector.md)]

[!include[Intro](../../../includes/cognitive-services-speech-service-how-to-translate-speech-intro.md)]

[!include[Intro - top-level declarations](../../../includes/cognitive-services-speech-service-how-to-toplevel-declarations.md)]

[!code-csharp[Top-level declarations](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/translation_samples.cs#toplevel)]

[!include[Intro - using microphone](../../../includes/cognitive-services-speech-service-how-to-translate-speech-microphone.md)]

[!code-csharp[Translation Using Microphone](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/translation_samples.cs#TranslationWithMicrophoneAsync)]

[!include[Intro - using file](../../../includes/cognitive-services-speech-service-how-to-translate-speech-file.md)]

[!code-csharp[Translation Using File Input](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/translation_samples.cs#TranslationWithFileAsync)]

[!include[Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
この記事のコードは `samples/csharp/sharedcontent/console` フォルダーにあります。

## <a name="next-steps"></a>次の手順

- [音声を認識する方法](how-to-recognize-speech-csharp.md)
- [音声から意図を認識する方法](how-to-recognize-intents-from-speech-csharp.md)
