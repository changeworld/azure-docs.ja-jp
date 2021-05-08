---
title: リップシンクのために頭部姿勢イベントを取得する方法
titleSuffix: Azure Cognitive Services
description: Speech SDK は、音声合成における口形素イベントをサポートしています。このイベントは、特定の音素を生成するときの唇、顎、舌の位置など、観察されたスピーチにおける主要な姿勢を表すために使用されます。
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/03/2021
ms.author: yulili
ms.custom: references_regions
zone_pivot_groups: programming-languages-speech-services-nomore-variant
ms.openlocfilehash: e97c48d4e42627d0fc2caaa4f66e81b9a0cafa86
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105643902"
---
# <a name="get-facial-pose-events"></a>表情イベントを取得する

> [!NOTE]
> 口形素は現在、`en-US-AriaNeural` 音声に対してのみ機能します。

口形素は、音声言語での音素を視覚的に描写したものです。
言葉を話すときの顔と口の位置を定義します。
各口形素は、音素の特定のセットに対する主要な頭部姿勢を表します。
口形素と音素の間に 1 対 1 対応の関係はありません。
多くの場合、音素は 1 つの口形素に対応しますが、`s` や `z` など、いくつかの音素のみためは顔に同じように現れます。
[口形素と音素の間のマッピング テーブル](#map-phonemes-to-visemes)を参照してください。

口形素を使用すると、より自然でインテリジェントなニュース放送アシスタント、より対話的なゲームや漫画文字、より直感的な言語の教育ビデオを作成できます。 聴覚障碍のある方も、音を視覚的に認識し、アニメーション化された顔で口形素を表した音声コンテンツを "読唇" できます。

## <a name="get-viseme-events-with-the-speech-sdk"></a>Speech SDK を使用して口形素イベントを取得する

口形素イベントを作成するには、入力テキストを一連の音素シーケンスとそれに対応する口形素シーケンスに変換します。 音声オーディオの各口形素の開始時間を推定します。 口形素イベントには、口形素 ID のシーケンスが含まれており、それぞれに口形素が現れるオーディオへのオフセットがあります。 これらのイベントは、入力テキストを話す人をシミュレートする、口のアニメーションを駆動できます。

| パラメーター | 説明 |
|-----------|-------------|
| 口形素 ID | 口形素を指定する整数値。 英語 (米国) では、音素の特定のセットに対する口の形状を表すために、22 種類の異なる口形素が用意されています。 [口形素 ID と音素の間のマッピング表](#map-phonemes-to-visemes)を参照してください。  |
| オーディオ オフセット | タイマー刻み (100 ナノ秒) での各口形素の開始時間。 |

口形素イベントを取得するには、Speech SDK で `VisemeReceived` イベントをサブスクライブします。
次のスニペットは、口形素イベントをサブスクライブする方法を示しています。

::: zone pivot="programming-language-csharp"

```csharp
using (var synthesizer = new SpeechSynthesizer(speechConfig, audioConfig))
{
    // Subscribes to viseme received event
    synthesizer.VisemeReceived += (s, e) =>
    {
        Console.WriteLine($"Viseme event received. Audio offset: " +
            $"{e.AudioOffset / 10000}ms, viseme id: {e.VisemeId}.");
    };

    var result = await synthesizer.SpeakSsmlAsync(ssml));
}

```

::: zone-end

::: zone pivot="programming-language-cpp"

```cpp
auto synthesizer = SpeechSynthesizer::FromConfig(speechConfig, audioConfig);

// Subscribes to viseme received event
synthesizer->VisemeReceived += [](const SpeechSynthesisVisemeEventArgs& e)
{
    cout << "viseme event received. "
        // The unit of e.AudioOffset is tick (1 tick = 100 nanoseconds), divide by 10,000 to convert to milliseconds.
        << "Audio offset: " << e.AudioOffset / 10000 << "ms, "
        << "viseme id: " << e.VisemeId << "." << endl;
};

auto result = synthesizer->SpeakSsmlAsync(ssml).get();
```

::: zone-end

::: zone pivot="programming-language-java"

```java
SpeechSynthesizer synthesizer = new SpeechSynthesizer(speechConfig, audioConfig);

// Subscribes to viseme received event
synthesizer.VisemeReceived.addEventListener((o, e) -> {
    // The unit of e.AudioOffset is tick (1 tick = 100 nanoseconds), divide by 10,000 to convert to milliseconds.
    System.out.print("Viseme event received. Audio offset: " + e.getAudioOffset() / 10000 + "ms, ");
    System.out.println("viseme id: " + e.getVisemeId() + ".");
});

SpeechSynthesisResult result = synthesizer.SpeakSsmlAsync(ssml).get();
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
speech_synthesizer = speechsdk.SpeechSynthesizer(speech_config=speech_config, audio_config=audio_config)

# Subscribes to viseme received event
speech_synthesizer.viseme_received.connect(lambda evt: print(
    "Viseme event received: audio offset: {}ms, viseme id: {}.".format(evt.audio_offset / 10000, evt.viseme_id)))

result = speech_synthesizer.speak_ssml_async(ssml).get()
```

::: zone-end

::: zone pivot="programming-language-javascript"

```Javascript
var synthesizer = new SpeechSDK.SpeechSynthesizer(speechConfig, audioConfig);

// Subscribes to viseme received event
synthesizer.visemeReceived = function (s, e) {
    window.console.log("(Viseme), Audio offset: " + e.audioOffset / 10000 + "ms. Viseme ID: " + e.visemeId);
}

synthesizer.speakSsmlAsync(ssml);
```

::: zone-end

::: zone pivot="programming-language-objectivec"

```Objective-C
SPXSpeechSynthesizer *synthesizer =
    [[SPXSpeechSynthesizer alloc] initWithSpeechConfiguration:speechConfig
                                           audioConfiguration:audioConfig];

// Subscribes to viseme received event
[synthesizer addVisemeReceivedEventHandler: ^ (SPXSpeechSynthesizer *synthesizer, SPXSpeechSynthesisVisemeEventArgs *eventArgs) {
    NSLog(@"Viseme event received. Audio offset: %fms, viseme id: %lu.", eventArgs.audioOffset/10000., eventArgs.visemeId);
}];

[synthesizer speakSsml:ssml];
```

::: zone-end

## <a name="map-phonemes-to-visemes"></a>音素を口形素にマップする

口形素は言語によって異なります。 各言語には、特定の音素に対応する一連の口形素があります。 次の表は、英語 (米国) の国際音声記号 (IPA) 音素と口形素 ID の対応付けを示しています。

| IPA | 例 | 口形素 ID |
|-----|---------|-----------|
| i   | **ea** t   | 6 |
| ɪ   | **i** f | 6 |
| eɪ  | **a** te | 4 |
| ɛ | **e** very | 4 |
|æ  |   **a** ctive        |1|
|ɑ  |   **o** bstinate     |2|
|ɔ  |   c **au** se         |3|
|ʊ  |   b **oo** k          |4|
|oʊ |   **o** ld           |8|
|u  |   **U** ber          |7|
|ʌ  |   **u** ncle         |1|
|aɪ |   **i** ce           |11|
|aʊ |   **ou** t           |9|
|ɔɪ |   **oi** l           |10|
|ju |   **Yu** ma          |[6, 7]|
|ə  |   **a** go           |1|
|ɪɹ |   **ear** s          |[6, 13]|
|ɛɹ |   **air** plane      |[4, 13]|
|ʊɹ |   c **ur** e          |[4, 13]|
|aɪ(ə)ɹ |   **Ire** land   |[11, 13]|
|aʊ(ə)ɹ |   **hour** s     |[9, 13]|
|ɔɹ |   **or** ange        |[3, 13]|
|ɑɹ |   **ar** tist        |[2, 13]|
|ɝ  |   **ear** th         |[5, 13]|
|ɚ  |   all **er** gy       |[1, 13]|
|。  |   **w** ith、s **ue** de   |7|
|j  |   **y** ard、f **e** w     |6|
|p  |   **p** ut           |21|
|b  |   **b** ig           |21|
|t  |   **t** alk          |19|
|d  |   **d** ig           |19|
|k  |   **c** ut           |20|
|G  |   **g** o            |20|
|m  |   **m** at, s **m** ash    |21|
|n  |   **n** o、s **n** ow      |19|
|ŋ  |   li **n** k          |20|
|f  |   **f** ork          |18|
|v  |   **v** alue         |18|
|θ  |   **th** in          |17|
|ð  |   **th** en          |17|
|s  |   **s** it           |15|
|z  |   **z** ap           |15|
|ʃ  |   **sh** e           |16|
|ʒ  |   **J** acques       |16|
|h  |   **h** elp          |12|
|tʃ |   **ch** in          |16|
|dʒ |   **j** oy           |16|
|l  |   **l** id、g **l** ad     |14|
|ɹ  |   **r** ed、b **r** ing    |13|


## <a name="next-steps"></a>次のステップ

* [Speech SDK のリファレンス ドキュメント](speech-sdk.md)
