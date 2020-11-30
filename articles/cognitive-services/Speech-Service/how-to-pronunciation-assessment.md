---
title: 発音評価に Speech SDK を使用する方法
titleSuffix: Azure Cognitive Services
description: Speech SDK は、音声入力の発音の品質を正確性、流暢性、完全性などの指標で評価する発音評価をサポートしています。
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/29/2020
ms.author: yulili
ms.custom: references_regions
zone_pivot_groups: programming-languages-set-nineteen
ms.openlocfilehash: 1cc313daf0e76ddd14865959410b07d9bdc189d7
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/20/2020
ms.locfileid: "94984923"
---
# <a name="pronunciation-assessment"></a>発音評価

発音評価ではスピーチの発音を評価し、話された音声の正確性と流暢性に関するフィードバックを話者に提供します。
言語学習者は、発音評価を使用して練習を行い、即座にフィードバックを得て、発音を改善することができます。そのため、自信を持って話し、発表することができます。
教師がこの機能を使用すれば、複数の話者の発音をリアルタイムに評価することができます。

この記事では、`PronunciationAssessmentConfig` を設定し、Speech SDK を利用して `PronunciationAssessmentResult` を取得する方法について説明します。

> [!NOTE]
> 発音評価機能は現在、`westus`、`eastasia`、`centralindia` でのみ利用できます。また、言語 `en-US` のみがサポートされています。

## <a name="pronunciation-assessment-with-the-speech-sdk"></a>Speech SDK による発音評価

下のサンプルでは、`PronunciationAssessmentConfig` を作成し、それを `SpeechRecognizer` に適用します。

次のスニペットからは、自分のアプリで自動言語検出を使用する方法を確認できます。

::: zone pivot="programming-language-csharp"

```csharp
var pronunciationAssessmentConfig = new PronunciationAssessmentConfig(
    "reference text", GradingSystem.HundredMark, Granularity.Phoneme);

using (var recognizer = new SpeechRecognizer(
    speechConfig,
    audioConfig))
{
    // apply the pronunciation assessment configuration to the speech recognizer
    pronunciationAssessmentConfig.ApplyTo(recognizer);
    var speechRecognitionResult = await recognizer.RecognizeOnceAsync();
    var pronunciationAssessmentResult =
        PronunciationAssessmentResult.FromResult(speechRecognitionResult);
    var pronunciationScore = pronunciationAssessmentResult.PronunciationScore;
}
```

::: zone-end

::: zone pivot="programming-language-cpp"

```cpp
auto pronunciationAssessmentConfig =
    PronunciationAssessmentConfig::Create("reference text",
        PronunciationAssessmentGradingSystem::HundredMark,
        PronunciationAssessmentGranularity::Phoneme);

auto recognizer = SpeechRecognizer::FromConfig(
    speechConfig,
    audioConfig);

// apply the pronunciation assessment configuration to the speech recognizer
pronunciationAssessmentConfig->ApplyTo(recognizer);
speechRecognitionResult = recognizer->RecognizeOnceAsync().get();
auto pronunciationAssessmentResult =
    PronunciationAssessmentResult::FromResult(speechRecognitionResult);
auto pronunciationScore = pronunciationAssessmentResult->PronunciationScore;
```

::: zone-end

::: zone pivot="programming-language-java"

```java
PronunciationAssessmentConfig pronunciationAssessmentConfig =
    new PronunciationAssessmentConfig("reference text", 
        PronunciationAssessmentGradingSystem.HundredMark,
        PronunciationAssessmentGranularity.Phoneme);

SpeechRecognizer recognizer = new SpeechRecognizer(
    speechConfig,
    audioConfig);

// apply the pronunciation assessment configuration to the speech recognizer
pronunciationAssessmentConfig.applyTo(recognizer);
Future<SpeechRecognitionResult> future = recognizer.recognizeOnceAsync();
SpeechRecognitionResult result = future.get(30, TimeUnit.SECONDS);
PronunciationAssessmentResult pronunciationAssessmentResult =
    PronunciationAssessmentResult.fromResult(result);
Double pronunciationScore = pronunciationAssessmentResult.getPronunciationScore();

recognizer.close();
speechConfig.close();
audioConfig.close();
pronunciationAssessmentConfig.close();
result.close();
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
pronunciation_assessment_config = \
        speechsdk.PronunciationAssessmentConfig(reference_text='reference text',
                grading_system=msspeech.PronunciationAssessmentGradingSystem.HundredMark,
                granularity=msspeech.PronunciationAssessmentGranularity.Phoneme)
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, \
        audio_config=audio_config)

# apply the pronunciation assessment configuration to the speech recognizer
pronunciation_assessment_config.apply_to(speech_recognizer)
result = speech_recognizer.recognize_once()
pronunciation_assessment_result = speechsdk.PronunciationAssessmentResult(result)
pronunciation_score = pronunciation_assessment_result.pronunciation_score
```

::: zone-end

::: zone pivot="programming-language-objectivec"

```Objective-C
SPXPronunciationAssessmentConfiguration* pronunciationAssessmentConfig =
    [[SPXPronunciationAssessmentConfiguration alloc]init:@"reference text"
                                           gradingSystem:SPXPronunciationAssessmentGradingSystem_HundredMark
                                             granularity:SPXPronunciationAssessmentGranularity_Phoneme];

SPXSpeechRecognizer* speechRecognizer = \
        [[SPXSpeechRecognizer alloc] initWithSpeechConfiguration:speechConfig
                                              audioConfiguration:audioConfig];

// apply the pronunciation assessment configuration to the speech recognizer
[pronunciationAssessmentConfig applyToRecognizer:speechRecognizer];

SPXSpeechRecognitionResult *result = [speechRecognizer recognizeOnce];
SPXPronunciationAssessmentResult* pronunciationAssessmentResult = [[SPXPronunciationAssessmentResult alloc] init:result];
double pronunciationScore = pronunciationAssessmentResult.pronunciationScore;
```

::: zone-end

### <a name="pronunciation-assessment-configuration-parameters"></a>発音評価構成パラメーター

発音評価の構成パラメーターを次の表に示します。

| パラメーター | 説明 | 必須 |
|-----------|-------------|---------------------|
| ReferenceText | 発音が評価されるテキスト。 | 必須 |
| GradingSystem | スコア調整用のポイント システム。 `FivePoint` システムによって 0 から 5 の浮動小数点スコアが付与され、`HundredMark` によって 0 から 100 の浮動小数点スコアが付与されます。 既定値:`FivePoint`。 | 省略可能 |
| 粒度 | 評価の粒度。 指定できる値は、全文、単語、音素レベルのスコアを示す `Phoneme`、全文と単語レベルのスコアを示す `Word`、および全文レベルのみのスコアを示す `FullText` です。 既定値:`Phoneme`。 | 省略可能 |
| EnableMiscue | 誤りの計算を有効にします。 これを有効にすると、発音された単語が参照テキストと比較され、比較に基づいて省略または挿入がマークされます。 指定できる値は、`False` と `True` です。 既定値:`False`。 | 省略可能 |
| ScenarioId | カスタマイズされたポイント システムを示す GUID。 | 省略可能 |

### <a name="pronunciation-assessment-result-parameters"></a>発音評価の結果パラメーター

発音評価の結果パラメーターを次の表に示します。

| パラメーター | 説明 |
|-----------|-------------|
| `AccuracyScore` | 音声の発音精度。 精度は、音素がネイティブ スピーカーの発音とどれだけ厳密に一致しているかを示します。 単語およびフル テキスト レベルの精度スコアは、音素レベルの精度スコアから集計されます。 |
| `FluencyScore` | 指定された音声の流暢性。 流暢性は、音声がネイティブ スピーカーによる単語間の間の取り方にどれだけ厳密に一致しているかを示します。 |
| `CompletenessScore` | 音声の完全性。テキスト入力を参照するために発音された単語の比率を算出することで判断されます。 |
| `PronunciationScore` | 特定の音声の発音品質を示す全体的なスコア。 これは、重み付きの `AccuracyScore`、`FluencyScore`、および `CompletenessScore` から集計されます。 |
| `ErrorType` | この値は、`ReferenceText` と比較して、単語が省略されているか、挿入されているか、発音が正しくないかを示します。 指定できる値は、`None` (この単語にエラーがないことを意味します)、`Omission`、`Insertion`、および `Mispronunciation` です。 |

## <a name="next-steps"></a>次のステップ

<!-- TODO: update the sample links after release -->

<!-- ::: zone pivot="programming-language-csharp"
* See the [sample code](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/speech_recognition_samples.cs#L741) on GitHub for automatic language detection
::: zone-end

::: zone pivot="programming-language-cpp"
* See the [sample code](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/cpp/windows/console/samples/speech_recognition_samples.cpp#L507) on GitHub for automatic language detection
::: zone-end

::: zone pivot="programming-language-java"
* See the [sample code](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/SpeechRecognitionSamples.java#L521) on GitHub for automatic language detection
::: zone-end

::: zone pivot="programming-language-python"
* See the [sample code](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/python/console/speech_synthesis_sample.py#L434) on GitHub for automatic language detection
::: zone-end

::: zone pivot="programming-language-objectivec"
* See the [sample code](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/objective-c/ios/speech-samples/speech-samples/ViewController.m#L494) on GitHub for automatic language detection
::: zone-end -->

* [Speech SDK のリファレンス ドキュメント](speech-sdk.md)
