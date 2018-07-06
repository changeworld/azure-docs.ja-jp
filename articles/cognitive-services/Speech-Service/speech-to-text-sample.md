---
title: Speech-to-Text のサンプル | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: Speech-to-Text のサンプルは次のとおりです。
services: cognitive-services
author: wolfma61
manager: onano
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 06/07/2018
ms.author: wolfma
ms.openlocfilehash: 2a1850e6a4f3c8eebd1b947aabe1374bdaab3fc8
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/27/2018
ms.locfileid: "37030252"
---
# <a name="sample-for-speech-to-text"></a>Speech-to-Text のサンプル

> [!NOTE]
> このサンプルや他のサンプルをダウンロードする手順については、[Speech SDK のサンプル](samples.md)に関するページをご覧ください。

[!include[Get a Subscription Key](includes/get-subscription-key.md)]

> [!NOTE]
> 以下のすべてのサンプルに、次の最上位宣言を配置してください。
>
> [!code-csharp[](~/samples-cognitive-services-speech-sdk/Windows/csharp_samples/speech_recognition_samples.cs#toplevel)]
>
> [!code-cpp[](~/samples-cognitive-services-speech-sdk/Windows/cxx_samples/speech_recognition_samples.cpp#toplevel)]
>
> - - -

## <a name="speech-recognition-using-the-microphone"></a>マイクを使用した音声認識

以下のコード スニペットは、マイクから入力された音声を既定の言語 (`en-US`) で認識する方法を示します。

[!code-csharp[Speech Recognition Using Microphone](~/samples-cognitive-services-speech-sdk/Windows/csharp_samples/speech_recognition_samples.cs#recognitionWithMicrophone)]

[!code-cpp[Speech Recognition Using Microphone](~/samples-cognitive-services-speech-sdk/Windows/cxx_samples/speech_recognition_samples.cpp#SpeechRecognitionWithMicrophone)]

- - -

## <a name="speech-recognition-from-a-file"></a>ファイルからの音声認識

次のコード スニペットは、オーディオ ファイルからの音声を既定の言語 (`en-US`) で認識する方法を示します。サポートされている形式は単一チャネル (モノラル) の WAV / PCM で、サンプリング レートは 16 KHz です。

[!include[Sample Audio](includes/sample-audio.md)]

[!code-csharp[Speech Recognition From a File](~/samples-cognitive-services-speech-sdk/Windows/csharp_samples/speech_recognition_samples.cs?name=recognitionFromFile)]

[!code-cpp[Speech Recognition From a File](~/samples-cognitive-services-speech-sdk/Windows/cxx_samples/speech_recognition_samples.cpp?name=SpeechRecognitionWithFile)]

- - -

## <a name="speech-recognition-using-a-customized-model"></a>カスタム モデルを使用した音声認識

[Custom Speech Service (CRIS)](https://www.cris.ai/) を使用すると、お使いのアプリケーション用に Microsoft の Speech-to-Text エンジンをカスタマイズできます。 次のスニペットは、CRIS モデルを使用してマイクから入力された音声を認識して CRIS サブスクリプション キーとお客様固有のデプロイ ID を入力して実行する方法を示します。

[!code-csharp[Speech Recognition Using a Customized Model](~/samples-cognitive-services-speech-sdk/Windows/csharp_samples/speech_recognition_samples.cs#recognitionCustomized)]

[!code-cpp[Speech Recognition Using a Customized Model](~/samples-cognitive-services-speech-sdk/Windows/cxx_samples/speech_recognition_samples.cpp#SpeechRecognitionUsingCustomizedModel)]

- - -

## <a name="continuous-speech-recognition"></a>連続音声認識

[!code-csharp[Continuous Speech Recognition](~/samples-cognitive-services-speech-sdk/Windows/csharp_samples/speech_recognition_samples.cs#recognitionContinuous)]

[!code-cpp[Continuous Speech Recognition](~/samples-cognitive-services-speech-sdk/Windows/cxx_samples/speech_recognition_samples.cpp#SpeechContinuousRecognitionUsingEvents)]

- - -

## <a name="sample-source-code"></a>サンプル ソース コード

最新版のサンプルや、より高度なサンプルが、専用の [GitHub リポジトリ](https://github.com/Azure-Samples/cognitive-services-speech-sdk)にあります。

## <a name="next-steps"></a>次の手順

- [意図認識](./intent.md)

- [翻訳](./translation.md)
