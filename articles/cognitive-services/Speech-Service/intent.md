---
title: 意図認識のサンプル | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: 意図認識のサンプルを次に示します。
services: cognitive-services
author: wolfma61
manager: onano
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 07/16/2018
ms.author: wolfma
ms.openlocfilehash: 3e9afc990d6bfa73eb045e7ed76dfd194df309c6
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2018
ms.locfileid: "39213203"
---
# <a name="sample-for-intent-recognition"></a>意図認識のサンプル

まずはサブスクリプション キーを取得してください。 Cognitive Service の Speech SDK でサポートされているその他のサービスとは対照的に、意図認識サービスには特定のサブスクリプション キーが必要です。 [こちら](https://www.luis.ai)に意図認識テクノロジに関する追加情報のほか、サブスクリプション キーを取得する方法が見つかります。 独自の Language Understanding サブスクリプション キー、[サブスクリプションのリージョン](regions.md)、および意図モデルの AppId をサンプル内の適切な場所で置き換えます。

## <a name="top-level-declarations"></a>最上位の宣言

以下のすべてのサンプルについて、次の最上位レベルの宣言を配置してください。

[!code-csharp[Top-level declarations](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#toplevel)]

[!code-cpp[Top-level declarations](~/samples-cognitive-services-speech-sdk/samples/cpp/windows/console/samples/intent_recognition_samples.cpp#toplevel)]

[!code-java[Top-level declarations](~/samples-cognitive-services-speech-sdk/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/IntentRecognitionSamples.java#toplevel)]

## <a name="intent-recognition-using-microphone"></a>マイクを使用した意図認識

以下のコード スニペットは、既定の言語 (`en-US`) でマイク入力から意図を認識する方法を示します。

[!code-csharp[Intent Recognition Using Microphone](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#intentRecognitionWithMicrophone)]

[!code-cpp[Intent Recognition Using Microphone](~/samples-cognitive-services-speech-sdk/samples/cpp/windows/console/samples/intent_recognition_samples.cpp#IntentRecognitionWithMicrophone)]

[!code-java[Intent Recognition Using Microphone](~/samples-cognitive-services-speech-sdk/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/IntentRecognitionSamples.java#IntentRecognitionWithMicrophone)]

## <a name="intent-recognition-using-microphone-in-a-specified-language"></a>マイクを使用した指定の言語での意図認識

以下のコード スニペットは、指定の言語 (ここではドイツ語 (`de-de`)) でマイク入力から意図を認識する方法を示します。

[!code-csharp[Intent Recognition Using Microphone In A Specified Language](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#intentRecognitionWithLanguage)]

[!code-cpp[Intent Recognition Using Microphone In A Specified Language](~/samples-cognitive-services-speech-sdk/samples/cpp/windows/console/samples/intent_recognition_samples.cpp#IntentRecognitionWithLanguage)]

[!code-java[Intent Recognition Using Microphone In A Specified Language](~/samples-cognitive-services-speech-sdk/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/IntentRecognitionSamples.java#IntentRecognitionWithLanguage)]

## <a name="intent-recognition-from-a-file-using-events"></a>イベントを使用した、ファイルからの意図認識

このコード スニペットは、継続的な方法で既定の言語 (`en-US`) で意図を認識する方法を示します。 このコードは、中間の結果など追加情報へのアクセスを許可します。 入力はオーディオ ファイルから取得されます。サポートされている形式は単一チャネル (モノラル) の WAV/PCM で、サンプリング レートは 16 KHz です。

[!code-csharp[Intent Recognition Using Events From a File](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#intentContinuousRecognitionWithFile)]

[!code-cpp[Intent Recognition Using Events From a File](~/samples-cognitive-services-speech-sdk/samples/cpp/windows/console/samples/intent_recognition_samples.cpp#IntentContinuousRecognitionWithFile)]

[!code-java[Intent Recognition Using Events From a File](~/samples-cognitive-services-speech-sdk/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/IntentRecognitionSamples.java#IntentContinuousRecognitionWithFile)]

[!include[Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]

## <a name="next-steps"></a>次の手順

- [音声認識](./speech-to-text-sample.md)

- [翻訳](./translation.md)
