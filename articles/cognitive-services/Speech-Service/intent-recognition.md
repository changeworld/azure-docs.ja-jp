---
title: 意図認識の概要 - Speech サービス
titleSuffix: Azure Cognitive Services
description: 意図認識を使用すると、事前に定義したユーザーの目的を認識することができます。 この記事は、意図認識サービスの利点と機能の概要です。
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/13/2020
keywords: 意図認識
ms.openlocfilehash: 221f5f0f022cba85df72979ed47b4033b2c3fdd1
ms.sourcegitcommit: 5af89a2a7b38b266cc3adc389d3a9606420215a9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/08/2021
ms.locfileid: "131988485"
---
# <a name="what-is-intent-recognition"></a>意図認識とは

この概要では、意図認識の利点と機能について説明します。 Cognitive Services Speech SDK には意図を認識する 2 つの方法があり、以下ではその両方について説明します。 意図は、航空機の予約や天気のチェック、あるいは電話を掛けるなどのユーザーが実行したい行動です。 意図認識を使用すると、アプリケーション、ツール、デバイスで、意図認識エンジンまたは LUIS で定義されているオプションに基づいて、ユーザーが始めたいことや行いたいことを特定できます。

## <a name="pattern-matching"></a>パターン マッチング
SDK に用意されている埋め込みパターン マッチャーを使用すると、非常に厳密な方法で意図を認識できます。 これは、簡単なオフライン ソリューションが必要な場合に便利です。 これは、ユーザーが何らかの方法でトレーニングされる場合、または特定のフレーズを使用して意図をトリガーすると予想される場合に、特に適しています。 たとえば、"Go to floor seven" (7 階に行く) や "Turn on the lamp" (ランプを点ける) などです。この方法から始めて、ニーズに合わなくなったら、LUIS または 2 つの組み合わせの使用に切り替えることをお勧めします。 

## <a name="luis-language-understanding-intent-service"></a>LUIS (Language Understanding Intent Service)
Microsoft LUIS サービスは完全な AI 意図サービスとして利用でき、可能性のある意図の領域が大規模で、ユーザーが実際にどのようなことを言うのかはっきりしない場合に適しています。 多くの複雑なシナリオ、意図、エンティティがサポートされています。

### <a name="luis-key-required"></a>必要な LUIS キー

* LUIS は音声から意図を認識するために Speech サービスと統合しています。 Speech サービスのサブスクリプションは不要で、LUIS だけでかまいません。
* 音声意図認識は、Speech SDK と統合されています。 Speech Service では LUIS キーを利用できます。
* Speech SDK を使用した意図認識は、[LUIS によってサポートされるリージョンのサブセットで提供](./regions.md#intent-recognition)されています。

## <a name="get-started"></a>はじめに
パターン マッチングを使い始めるには、こちらの[使用方法](how-to-use-simple-language-pattern-matching.md)を参照してください。

LUIS の意図認識を使い始めるには、こちらの[クイックスタート](get-started-intent-recognition.md)を参照してください。

## <a name="sample-code"></a>サンプル コード

意図認識のサンプル コードについては、次を参照してください。

* [クイック スタート:事前構築済みの Home Automation アプリを使用する](../luis/luis-get-started-create-app.md)
* [C# 用の Speech SDK を使用して音声の意図を認識する](./how-to-recognize-intents-from-speech-csharp.md)
* [C# の Unity を使用した意図認識およびその他の Speech サービス](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/unity/speechrecognizer)
* [Python 用 Speech SDK を使用して意図を認識する](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/python/console)
* [Windows で C++ 用 Speech SDK を使用した意図認識およびその他の Speech サービス](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/cpp/windows/console)
* [Windows または Linux で Java 用 Speech SDK を使用した意図認識およびその他の Speech サービス](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/java/jre/console)
* [Web ブラウザーで JavaScript 用 Speech SDK を使用した意図認識およびその他の Speech サービス](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/js/browser)

## <a name="reference-docs"></a>リファレンス ドキュメント

* [Speech SDK](./speech-sdk.md)

## <a name="next-steps"></a>次のステップ

* 意図認識の[クイックスタート](get-started-intent-recognition.md)を完了する
* [Speech サービスのサブスクリプション キーを無料で取得する](overview.md#try-the-speech-service-for-free)
* [Speech SDK を取得する](speech-sdk.md)
