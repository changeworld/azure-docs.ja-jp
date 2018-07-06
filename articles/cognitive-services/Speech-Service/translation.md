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
ms.date: 06/07/2018
ms.author: wolfma
ms.openlocfilehash: 1151628ed695e74e2196c20b08e33fa5eaf33282
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/27/2018
ms.locfileid: "37028389"
---
# <a name="sample-for-translation"></a>翻訳のサンプル

> [!NOTE]
> このサンプルや他のサンプルのをダウンロードする手順については、[Speech SDK のサンプル](samples.md)に関するページをご覧ください。

[!include[Get a Subscription Key](includes/get-subscription-key.md)]

> [!NOTE]
> 以下のすべてのサンプルについて、次のトップ レベルの宣言を配置してください。
>
> [!code-csharp [Using Statements](~/samples-cognitive-services-speech-sdk/Windows/csharp_samples/translation_samples.cs#toplevel)]
>
> - - -

## <a name="translation-using-the-microphone"></a>マイクを使用した翻訳

次のコード スニペットは、音声入力を英語からドイツ語に翻訳する方法と、翻訳済みテキストの音声出力を取得する方法を示したものです。 これにはマイクが使用されます。

[!code-csharp[Translation Using Microphone](~/samples-cognitive-services-speech-sdk/Windows/csharp_samples/translation_samples.cs#TranslationWithMicrophoneAsync)]

- - -

## <a name="translation-using-file-input"></a>ファイル入力を使用した翻訳

次のコード スニペットは、音声入力を英語からドイツ語とフランス語に翻訳する方法を示したものです。
入力にはファイルが使用されます。

[!code-csharp[Translation Using File Input](~/samples-cognitive-services-speech-sdk/Windows/csharp_samples/translation_samples.cs#TranslationWithFileAsync)]

- - -

## <a name="sample-source-code"></a>サンプル ソース コード

最新版のサンプルや、より高度なサンプルが、専用の [GitHub リポジトリ](https://github.com/Azure-Samples/cognitive-services-speech-sdk)にあります。

## <a name="next-steps"></a>次の手順

- [音声認識](./speech-to-text-sample.md)

- [意図認識](./intent.md)
