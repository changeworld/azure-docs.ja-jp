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
ms.date: 06/07/2018
ms.author: wolfma
ms.openlocfilehash: 38f7f038a803546adb83245519efc5de0c0d1599
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/27/2018
ms.locfileid: "37045012"
---
# <a name="sample-for-intent-recognition"></a>意図認識のサンプル

> [!NOTE]
> このサンプルなどをダウンロードする手順については、[Speech SDK のサンプル](samples.md)に関するページをご覧ください。

> [!NOTE]
> まずはサブスクリプション キーを取得してください。 Cognitive Service の Speech SDK でサポートされているその他のサービスとは対照的に、意図認識サービスには特定のサブスクリプション キーが必要です。 [こちら](https://www.luis.ai)に意図認識テクノロジに関する追加情報のほか、サブスクリプション キーを取得する方法が見つかります。 独自のサブスクリプション キー、サービスのリージョン、お使いの意図モデルの AppId をサンプル内の適切な場所に置き換えます。

> [!NOTE]
> 以下のすべてのサンプルについて、次の最上位レベルの宣言を配置してください。
>
> [!code-cpp[](~/samples-cognitive-services-speech-sdk/Windows/cxx_samples/intent_recognition_samples.cpp#toplevel)]
>
> - - -

## <a name="intent-recognition-using-microphone"></a>マイクを使用した意図認識

以下のコード スニペットは、既定の言語 (`en-US`) でマイク入力から意図を認識する方法を示します。

[!code-cpp[Intent Recognition Using Microphone](~/samples-cognitive-services-speech-sdk/Windows/cxx_samples/intent_recognition_samples.cpp#IntentRecognitionWithMicrophone)]

- - -

## <a name="intent-recognition-using-microphone-in-a-specified-language"></a>マイクを使用した指定の言語での意図認識

以下のコード スニペットは、指定の言語 (ここではドイツ語 (`de-de`)) でマイク入力から意図を認識する方法を示します。

[!code-cpp[Intent Recognition Using Microphone In A Specified Language](~/samples-cognitive-services-speech-sdk/Windows/cxx_samples/intent_recognition_samples.cpp#IntentRecognitionWithLanguage)]

- - -

## <a name="intent-recognition-from-a-file"></a>ファイルからの意図認識

次のコード スニペットは、既定の言語 (`en-US`) でオーディオ ファイルから意図を認識する方法を示します。サポートされている形式は単一チャネル (モノラル) の WAV/PCM で、サンプリング レートは 16 KHz です。

[!include[Sample Audio](includes/sample-audio.md)]

[!code-cpp[Intent Recognition From a File](~/samples-cognitive-services-speech-sdk/Windows/cxx_samples/intent_recognition_samples.cpp#IntentRecognitionWithFile)]

- - -

## <a name="intent-recognition-using-events"></a>イベントを使用した意図認識

このコード スニペットは、継続的な方法で意図を認識する方法を示します。 このコードは、中間の結果など追加情報へのアクセスを許可します。 

[!code-cpp[Intent Recognition Using Events](~/samples-cognitive-services-speech-sdk/Windows/cxx_samples/intent_recognition_samples.cpp#IntentContinuousRecognitionUsingEvents)]

- - -

## <a name="sample-source-code"></a>サンプルのソース コード

最新のサンプルのセットについては、[Cognitive Services の Speech SDK のサンプルの GitHub リポジトリ](https://aka.ms/csspeech/samples)をご覧ください。

## <a name="next-steps"></a>次の手順

- [音声認識](./speech-to-text-sample.md)

- [翻訳](./translation.md)
