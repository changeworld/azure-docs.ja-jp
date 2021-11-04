---
title: 言語識別 - Speech サービス
titleSuffix: Azure Cognitive Services
description: 言語識別は、提供された言語一覧と照合する際に、Speech SDK に渡されたオーディオで話されている言語を判断するために使用されます。
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 08/27/2021
ms.author: lajanuar
zone_pivot_groups: programming-languages-cs-cpp-py
ms.openlocfilehash: d840ddce060e2ada6a0a237a896201dafdc90fac
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131511249"
---
# <a name="language-identification-preview"></a>言語識別 (プレビュー)

言語識別は、提供された言語一覧と照合する際に、Speech SDK に渡されたオーディオで話されている言語を判断するために使用されます。 

言語識別は、[音声翻訳](./get-started-speech-translation.md#multi-lingual-translation-with-language-identification)の実行中に使用できるほか、[音声認識時に言語識別](./how-to-automatic-language-detection.md)を行う際にも使用できます。 

利用できる言語については、[言語サポート](language-support.md)に関するページを参照してください。

## <a name="prerequisites"></a>前提条件

この記事は、ユーザーが Azure サブスクリプションと音声リソースを所有しており、音声認識の基本的な知識もお持ちであることを前提としています。

## <a name="standalone-language-identification"></a>スタンドアロン言語識別

話されているソース言語の検出のみが必要な場合、次のコード サンプルに示すように、スタンドアロン言語識別を使用できます。 `SourceLanguageRecognizer` は、継続認識のシナリオでも使用できます。

::: zone pivot="programming-language-csharp"

```csharp
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

var speechConfig = SpeechConfig.FromSubscription("<paste-your-subscription-key>","<paste-your-region>");
// can switch "Latency" to "Accuracy" depending on priority
speechConfig.SetProperty(PropertyId.SpeechServiceConnection_SingleLanguageIdPriority, "Latency");

var autoDetectSourceLanguageConfig =
    AutoDetectSourceLanguageConfig.FromLanguages(
        new string[] { "en-US", "de-DE" });

using (var recognizer = new SourceLanguageRecognizer(speechConfig, autoDetectSourceLanguageConfig))
{
    var result = await recognizer.RecognizeOnceAsync();
    if (result.Reason == ResultReason.RecognizedSpeech)
    {
        var lang = AutoDetectSourceLanguageResult.FromResult(result).Language;
        Console.WriteLine($"DETECTED: Language={lang}");
    }
}
```

スタンドアロン言語識別の例 (継続識別の例を含む) については、[GitHub のサンプル](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/standalone_language_detection_samples.cs)を参照してください。

::: zone-end

::: zone pivot="programming-language-cpp"

```cpp
using namespace std;
using namespace Microsoft::CognitiveServices::Speech;
using namespace Microsoft::CognitiveServices::Speech::Audio;

auto config = SpeechConfig::FromSubscription("<paste-your-subscription-key>","<paste-your-region>");
config->SetProperty(PropertyId::SpeechServiceConnection_SingleLanguageIdPriority, "Latency");

auto autoDetectSourceLanguageConfig = AutoDetectSourceLanguageConfig::FromLanguages({ "en-US", "de-DE" });

auto recognizer = SourceLanguageRecognizer::FromConfig(config, autoDetectSourceLanguageConfig);
cout << "Say something...\n";

auto result = recognizer->RecognizeOnceAsync().get();
if (result->Reason == ResultReason::RecognizedSpeech)
{
    auto lidResult = AutoDetectSourceLanguageResult::FromResult(result);
    cout << "DETECTED: Language="<< lidResult->Language << std::endl;
}
```

スタンドアロン言語識別の例 (継続識別の例を含む) については、[GitHub のサンプル](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/cpp/windows/console/samples/standalone_language_detection_samples.cpp)を参照してください。

::: zone-end

::: zone pivot="programming-language-python"

```python
import azure.cognitiveservices.speech as speechsdk

speech_config = speechsdk.SpeechConfig(subscription=speech_key, region=service_region)
    
speech_config.set_property(property_id=speechsdk.PropertyId.SpeechServiceConnection_SingleLanguageIdPriority, value='Accuracy')

speech_language_detection = speechsdk.SourceLanguageRecognizer(speech_config=speech_config, auto_detect_source_language_config=auto_detect_source_language_config)

result = speech_language_detection.recognize_once()

# Check the result
if result.reason == speechsdk.ResultReason.RecognizedSpeech:
    print("RECOGNIZED: {}".format(result))
    detectedSrcLang = result.properties[speechsdk.PropertyId.SpeechServiceConnection_AutoDetectSourceLanguageResult]
    print("Detected Language: {}".format(detectedSrcLang))
elif result.reason == speechsdk.ResultReason.NoMatch:
    print("No speech could be recognized")
elif result.reason == speechsdk.ResultReason.Canceled:
    cancellation_details = result.cancellation_details
    print("Speech Recognition canceled: {}".format(cancellation_details.reason))
    if cancellation_details.reason == speechsdk.CancellationReason.Error:
        print("Error details: {}".format(cancellation_details.error_details))
```

スタンドアロン言語識別の例 (継続識別の例を含む) については、[GitHub のサンプル](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/python/console/speech_language_detection_sample.py)を参照してください。

::: zone-end
