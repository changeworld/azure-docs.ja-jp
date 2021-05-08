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
ms.date: 01/12/2021
ms.author: yulili
ms.custom: references_regions
zone_pivot_groups: programming-languages-speech-services-nomore-variant
ms.openlocfilehash: 2d1b5e490b7c8212e6103e3d169c1b5491d01dde
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/01/2021
ms.locfileid: "106167432"
---
# <a name="pronunciation-assessment"></a>発音評価

発音評価ではスピーチの発音を評価し、話された音声の正確性と流暢性に関するフィードバックを話者に提供します。
言語学習者は、発音評価を使用して練習を行い、即座にフィードバックを得て、発音を改善することができます。そのため、自信を持って話し、発表することができます。
教師がこの機能を使用すれば、複数の話者の発音をリアルタイムに評価することができます。

この記事では、`PronunciationAssessmentConfig` を設定し、Speech SDK を利用して `PronunciationAssessmentResult` を取得する方法について説明します。

> [!NOTE]
> 発音評価機能では現在、すべての[音声テキスト変換のリージョン](regions.md#speech-to-text-text-to-speech-and-translation)で使用できる `en-US` 言語がサポートされています。 `en-GB` および `zh-CN` 言語のサポートはプレビュー段階です。これは `westus`、`eastasia`、`centralindia` の各リージョンで利用できます。

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

::: zone pivot="programming-language-javascript"

```Javascript
var pronunciationAssessmentConfig = new SpeechSDK.PronunciationAssessmentConfig("reference text",
    PronunciationAssessmentGradingSystem.HundredMark,
    PronunciationAssessmentGranularity.Word, true);
var speechRecognizer = SpeechSDK.SpeechRecognizer.FromConfig(speechConfig, audioConfig);
// apply the pronunciation assessment configuration to the speech recognizer
pronunciationAssessmentConfig.applyTo(speechRecognizer);

speechRecognizer.recognizeOnceAsync((result: SpeechSDK.SpeechRecognitionResult) => {
        var pronunciationAssessmentResult = SpeechSDK.PronunciationAssessmentResult.fromResult(result);
        var pronunciationScore = pronunciationAssessmentResult.pronunciationScore;
        var wordLevelResult = pronunciationAssessmentResult.detailResult.Words;
},
{});
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

### <a name="sample-responses"></a>応答のサンプル

JSON での一般的な発音評価の結果:

```json
{
  "RecognitionStatus": "Success",
  "Offset": "400000",
  "Duration": "11000000",
  "NBest": [
      {
        "Confidence" : "0.87",
        "Lexical" : "good morning",
        "ITN" : "good morning",
        "MaskedITN" : "good morning",
        "Display" : "Good morning.",
        "PronunciationAssessment":
        {
            "PronScore" : 84.4,
            "AccuracyScore" : 100.0,
            "FluencyScore" : 74.0,
            "CompletenessScore" : 100.0,
        },
        "Words": [
            {
              "Word" : "Good",
              "Offset" : 500000,
              "Duration" : 2700000,
              "PronunciationAssessment":
              {
                "AccuracyScore" : 100.0,
                "ErrorType" : "None"
              }
            },
            {
              "Word" : "morning",
              "Offset" : 5300000,
              "Duration" : 900000,
              "PronunciationAssessment":
              {
                "AccuracyScore" : 100.0,
                "ErrorType" : "None"
              }
            }
        ]
      }
  ]
}
```

## <a name="next-steps"></a>次のステップ

<!-- TODO: update JavaScript sample links after release -->

* 発音評価の[ビデオによる紹介](https://www.youtube.com/watch?v=cBE8CUHOFHQ)と[ビデオ チュートリアル](https://www.youtube.com/watch?v=zFlwm7N4Awc)を視聴する

* [発音評価のデモ](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/PronunciationAssessment/BrowserJS)を試す

::: zone pivot="programming-language-csharp"
* 読み方の評価については、GitHub の[サンプル コード](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/speech_recognition_samples.cs#L949)を参照してください。
::: zone-end

::: zone pivot="programming-language-cpp"
* 読み方の評価については、GitHub の[サンプル コード](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/cpp/windows/console/samples/speech_recognition_samples.cpp#L633)を参照してください。
::: zone-end

::: zone pivot="programming-language-java"
* 読み方の評価については、GitHub の[サンプル コード](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/SpeechRecognitionSamples.java#L697)を参照してください。
::: zone-end

::: zone pivot="programming-language-python"
* 読み方の評価については、GitHub の[サンプル コード](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/python/console/speech_sample.py#L576)を参照してください。
::: zone-end

::: zone pivot="programming-language-objectivec"
* 読み方の評価については、GitHub の[サンプル コード](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/objective-c/ios/speech-samples/speech-samples/ViewController.m#L642)を参照してください。
::: zone-end

* [Speech SDK のリファレンス ドキュメント](speech-sdk.md)

* [無料の Azure アカウントを作成](https://azure.microsoft.com/free/cognitive-services/)してください
