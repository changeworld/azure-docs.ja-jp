---
title: リップシンクのために頭部姿勢イベントを取得する方法
titleSuffix: Azure Cognitive Services
description: Speech SDK では、音声合成中の口形素イベントがサポートされています。これにより、特定の音素を生成するときの唇、顎、舌の位置など、観察されたスピーチにおける主要な姿勢が表されます。
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
ms.openlocfilehash: 3601cd6f7580a4d87dda7488826e25ca85b233c9
ms.sourcegitcommit: 1b698fb8ceb46e75c2ef9ef8fece697852c0356c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/28/2021
ms.locfileid: "110654198"
---
# <a name="get-facial-pose-events"></a>表情イベントを取得する

> [!NOTE]
> Viseme イベントは、現在、`en-US` 英語 (米国) の[ニューラル音声](language-support.md#text-to-speech)でのみ使用できます。

"_口形素_" は、音声言語での音素を視覚的に描写したものです。
言葉を話すときの顔と口の位置を定義します。
各口形素は、音素の特定のセットに対する主要な頭部姿勢を表します。
口形素は、2D および 3D のアバター モデルの動きを制御するために使用でき、合成音声に口の動きを完全に一致させます。

口形素により、アバターの使用と制御が容易になります。 口形素を使用すると、次のことができるようになります。

 * インテリジェント キオスク用の **アニメーション化された仮想音声アシスタント** を作成し、顧客向けのマルチモード統合サービスを構築します。
 * **イマーシブなニュース放送** を構築し、自然な顔と口の動きで視聴者体験を向上させます。
 * 動的な内容を話すことができる **対話的なゲーム アバターおよびマンガのキャラクター** を生成します。
 * 言語学習者が各単語と音素の口の動作を理解するのに役立つ、より **効果的な言語教育動画** を作成します。
 * 聴覚障碍のある方も、音を視覚的に認識し、アニメーション化された顔で口形素を表した音声コンテンツを **"読唇"** できます。

口形素の[紹介動画](https://youtu.be/ui9XT47uwxs)を参照してください。
> [!VIDEO https://www.youtube.com/embed/ui9XT47uwxs]

## <a name="azure-neural-tts-can-produce-visemes-with-speech"></a>Azure ニューラル TTS で音声を使用して口形素を生成する

ニューラル音声では、入力テキストまたは SSML (音声合成マークアップ言語) が合成音声に変換されます。 音声オーディオ出力には、口形素 ID とそのオフセット タイムスタンプを付けることができます。 各口形素 ID は、特定の音素を生成するときの唇、顎、舌の位置など、観察されたスピーチにおける特定の姿勢を指定します。 2D または3D レンダリング エンジンを使用すると、これらの口形素イベントを使用してアバターをアニメーション化できます。

口形素の全体的なワークフローを下のフローチャートに示します。

![口形素の全体的なワークフロー](media/text-to-speech/viseme-structure.png)

| パラメーター | 説明 |
|-----------|-------------|
| 口形素 ID | 口形素を指定する整数値。 英語 (米国) では、音素の特定のセットに対する口の形状を表すために、22 種類の異なる口形素が用意されています。 口形素と音素の間に 1 対 1 対応の関係はありません。 多くの場合、音素は 1 つの口形素に対応しますが、`s` や `z` など、いくつかの音素のみためは顔に同じように現れます。 [口形素 ID と音素の間のマッピング表](#map-phonemes-to-visemes)を参照してください。  |
| オーディオ オフセット | タイマー刻み (100 ナノ秒) での各口形素の開始時間。 |

## <a name="get-viseme-events-with-the-speech-sdk"></a>Speech SDK を使用して口形素イベントを取得する

合成音声を使用して口形素を取得するには、Speech SDK で `VisemeReceived` イベントをサブスクライブします。
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

口形素の出力の例を以下に示します。

```text
(Viseme), Viseme ID: 1, Audio offset: 200ms.

(Viseme), Viseme ID: 5, Audio offset: 850ms.

……

(Viseme), Viseme ID: 13, Audio offset: 2350ms.
```

口形素の出力を取得した後は、これらのイベントを使用して、キャラクター アニメーションを作成できます。 独自のキャラクターを作成し、自動的にキャラクターをアニメーション化することができます。

2D キャラクターの場合、シナリオに適したキャラクターを設計し、口形素 ID ごとにスケーラブル ベクター グラフィックス (SVG) を使用して、時間ベースの顔の位置を取得できます。 口形素イベントに含まれるテンポラル タグを使用すると、これらの適切に設計された SVG はスムージング変更によって処理され、途切れのないアニメーションがユーザーに提供されます。 たとえば下の図は、言語学習用に設計された赤い唇のキャラクターを示しています。

![2D レンダリングの例](media/text-to-speech/viseme-demo-2D.png)

3D キャラクターの場合、キャラクターを操り人形だと考えてください。 人形遣いがある状態から別の状態に糸を動かすと、あとは物理的な法則に従って人形が滑らかに動きます。 口形素の出力は、動作タイムラインを提供する人形遣いとして機能します。 アニメーション エンジンによって、動作の物理的な法則が定義されます。 イージング アルゴリズムを使用してフレームを補間することで、エンジンではさらに高品質のアニメーションを生成できます。

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
