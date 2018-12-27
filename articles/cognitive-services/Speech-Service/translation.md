---
title: 翻訳のサンプル | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: 音声翻訳のサンプルは次のとおりです。
services: cognitive-services
author: wolfma61
manager: onano
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 07/16/2018
ms.author: wolfma
ms.openlocfilehash: 66d26181334a71578f1a94000cb942a6a87398bc
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/17/2018
ms.locfileid: "39070793"
---
# <a name="sample-for-translation"></a>翻訳のサンプル

[!include[Get a Subscription Key](../../../includes/cognitive-services-speech-service-get-subscription-key.md)]

## <a name="top-level-declarations"></a>最上位の宣言

以下のすべてのサンプルについて、次の最上位レベルの宣言を配置してください。

[!code-csharp[Top-level declarations](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/translation_samples.cs#toplevel)]

[!code-cpp[Top-level declarations](~/samples-cognitive-services-speech-sdk/samples/cpp/windows/console/samples/translation_samples.cpp#toplevel)]

[!code-java[Top-level declarations](~/samples-cognitive-services-speech-sdk/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/TranslationSamples.java#toplevel)]

## <a name="translation-using-the-microphone"></a>マイクを使用した翻訳

次のコード スニペットは、音声入力を英語からドイツ語に翻訳する方法と、翻訳済みテキストの音声出力を取得する方法を示したものです。 これにはマイクが使用されます。

[!code-csharp[Translation Using Microphone](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/translation_samples.cs#TranslationWithMicrophoneAsync)]

[!code-cpp[Translation Using Microphone](~/samples-cognitive-services-speech-sdk/samples/cpp/windows/console/samples/translation_samples.cpp#TranslationWithMicrophone)]

[!code-java [Translation Using Microphone](~/samples-cognitive-services-speech-sdk/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/TranslationSamples.java#TranslationWithMicrophoneAsync)]

## <a name="translation-using-file-input"></a>ファイル入力を使用した翻訳

次のコード スニペットは、音声入力を英語からドイツ語とフランス語に翻訳する方法を示したものです。
入力にはファイルが使用されます。

[!code-csharp[Translation Using File Input](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/translation_samples.cs#TranslationWithFileAsync)]

[!code-java [Translation Using File Input](~/samples-cognitive-services-speech-sdk/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/TranslationSamples.java#TranslationWithFileAsync)]

[!include[Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]

## <a name="next-steps"></a>次の手順

- [音声認識](./speech-to-text-sample.md)

- [意図認識](./intent.md)
