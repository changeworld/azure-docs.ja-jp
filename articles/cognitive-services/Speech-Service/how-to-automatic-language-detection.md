---
title: 言語識別の使用方法
titleSuffix: Azure Cognitive Services
description: 言語識別を音声認識と組み合わせることで、認識中の会話音声で話されている言語を特定できます。
services: cognitive-services
author: eric-urban
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/21/2021
ms.author: eur
zone_pivot_groups: programming-languages-speech-services-nomore-variant
ms.openlocfilehash: fc011db6e36ab3d9f6ae2c3e7dff0e08b6048172
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132716597"
---
# <a name="how-to-use-language-identification"></a>言語識別の使用方法

言語識別は、提供された言語一覧と照合する際に、Speech SDK に渡されたオーディオで話されている言語を判断するために使用されます。 言語識別によって返される値を使用して、音声テキスト変換の言語モデルが選択されます。これにより、文字起こしの精度が上がります。 

言語識別は、[音声翻訳](./get-started-speech-translation.md?pivots=programming-language-csharp&tabs=script%2cwindowsinstall#multi-lingual-translation-with-language-identification)の実行中、または[スタンドアロン識別](./language-identification.md)を実行する際に使用できます。 

利用できる言語については、[言語サポート](language-support.md)に関するページを参照してください。

## <a name="prerequisites"></a>前提条件

この記事は、ユーザーが Azure サブスクリプションと音声リソースを所有しており、音声認識の基本的な知識もお持ちであることを前提としています。 [クイックスタート](get-started-speech-to-text.md)を完了していない場合は、先に完了してください。

## <a name="language-identification-with-speech-to-text"></a>音声テキスト変換を使用した言語識別

現在、言語識別は、開始時の認識の場合 **4 言語**、継続認識の場合 **10 言語** までに制限されています。 `AutoDetectSourceLanguageConfig` オブジェクトを構築するとき、この上限を念頭に置いてください。 次のサンプルでは、`AutoDetectSourceLanguageConfig` を使用して、識別する言語の候補となる言語一覧を定義し、音声認識を実行するときにそれらの言語を参照します。

> [!IMPORTANT]
> 継続的な言語識別は、C#、C++、Python でのみサポートされています。

::: zone pivot="programming-language-csharp"

次の例では、`Latency` を優先して開始時の認識を実行します。 このプロパティは、ユースケースの優先順位に応じて、`Accuracy` に設定することもできます。 `Latency` は、待機時間の短い結果が必要なのに (ライブ ストリーミング シナリオなど)、オーディオ サンプルの言語がわからない場合に最適なオプションです。 

`Accuracy` は、オーディオ品質が低い可能性があるものの、長い待機時間を許容できるシナリオで使用する必要があります。 たとえば、ボイスメールに背景ノイズがある、または先頭に一定の無音があるが、エンジンでの処理時間を長くすると認識結果が向上するといったケースです。

どちらの場合も、次に示すような開始時の認識は、同じオーディオ サンプル内で言語が変更される可能性があるシナリオには **使用できません**。 これらタイプのシナリオに適した継続認識については、以下を参照してください。

```csharp
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

var speechConfig = SpeechConfig.FromSubscription("<paste-your-subscription-key>","<paste-your-region>");
// can switch "Latency" to "Accuracy" depending on priority
speechConfig.SetProperty(PropertyId.SpeechServiceConnection_SingleLanguageIdPriority, "Latency");

var autoDetectSourceLanguageConfig =
    AutoDetectSourceLanguageConfig.FromLanguages(
        new string[] { "en-US", "de-DE", "ja-JP", "de-DE" });

using var audioConfig = AudioConfig.FromDefaultMicrophoneInput();
using (var recognizer = new SpeechRecognizer(
    speechConfig,
    autoDetectSourceLanguageConfig,
    audioConfig))
{
    var speechRecognitionResult = await recognizer.RecognizeOnceAsync();
    var autoDetectSourceLanguageResult =
        AutoDetectSourceLanguageResult.FromResult(speechRecognitionResult);
    var detectedLanguage = autoDetectSourceLanguageResult.Language;
}
```

次の例には、多言語シナリオでの連続音声認識の設定が示されています。 この例では、言語構成で `en-US` と `ja-JP` のみを使用しますが、この設計パターンに最大 **10 の言語** を使用できます。 音声が検出されるたびに、ソース言語が識別され、オーディオもテキスト出力に変換されます。 この例では、応答時間を優先する `Latency` モードが使用されています。

```csharp
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

var region = "<paste-your-region>";
// currently the v2 endpoint is required for this design pattern
var endpointString = $"wss://{region}.stt.speech.microsoft.com/speech/universal/v2";
var endpointUrl = new Uri(endpointString);

var config = SpeechConfig.FromEndpoint(endpointUrl, "<paste-your-subscription-key>");
// can switch "Latency" to "Accuracy" depending on priority
config.SetProperty(PropertyId.SpeechServiceConnection_ContinuousLanguageIdPriority, "Latency");

var autoDetectSourceLanguageConfig = AutoDetectSourceLanguageConfig.FromLanguages(new string[] { "en-US", "ja-JP" });

var stopRecognition = new TaskCompletionSource<int>();
using (var audioInput = AudioConfig.FromWavFileInput(@"path-to-your-audio-file.wav"))
{
    using (var recognizer = new SpeechRecognizer(config, autoDetectSourceLanguageConfig, audioInput))
    {
        // Subscribes to events.
        recognizer.Recognizing += (s, e) =>
        {
            if (e.Result.Reason == ResultReason.RecognizingSpeech)
            {
                Console.WriteLine($"RECOGNIZING: Text={e.Result.Text}");
                var autoDetectSourceLanguageResult = AutoDetectSourceLanguageResult.FromResult(e.Result);
                Console.WriteLine($"DETECTED: Language={autoDetectSourceLanguageResult.Language}");
            }
        };

        recognizer.Recognized += (s, e) =>
        {
            if (e.Result.Reason == ResultReason.RecognizedSpeech)
            {
                Console.WriteLine($"RECOGNIZED: Text={e.Result.Text}");
                var autoDetectSourceLanguageResult = AutoDetectSourceLanguageResult.FromResult(e.Result);
                Console.WriteLine($"DETECTED: Language={autoDetectSourceLanguageResult.Language}");
            }
            else if (e.Result.Reason == ResultReason.NoMatch)
            {
                Console.WriteLine($"NOMATCH: Speech could not be recognized.");
            }
        };

        recognizer.Canceled += (s, e) =>
        {
            Console.WriteLine($"CANCELED: Reason={e.Reason}");

            if (e.Reason == CancellationReason.Error)
            {
                Console.WriteLine($"CANCELED: ErrorCode={e.ErrorCode}");
                Console.WriteLine($"CANCELED: ErrorDetails={e.ErrorDetails}");
                Console.WriteLine($"CANCELED: Did you update the subscription info?");
            }

            stopRecognition.TrySetResult(0);
        };

        recognizer.SessionStarted += (s, e) =>
        {
            Console.WriteLine("\n    Session started event.");
        };

        recognizer.SessionStopped += (s, e) =>
        {
            Console.WriteLine("\n    Session stopped event.");
            Console.WriteLine("\nStop recognition.");
            stopRecognition.TrySetResult(0);
        };

        // Starts continuous recognition. Uses StopContinuousRecognitionAsync() to stop recognition.
        await recognizer.StartContinuousRecognitionAsync().ConfigureAwait(false);

        // Waits for completion.
        // Use Task.WaitAny to keep the task rooted.
        Task.WaitAny(new[] { stopRecognition.Task });

        // Stops recognition.
        await recognizer.StopContinuousRecognitionAsync().ConfigureAwait(false);
    }
}
```

> [!NOTE]
> `Latency` と `Accuracy` モード、および多言語の継続認識は、現在、C#、C++、Python でのみサポートされています。
 
::: zone-end

::: zone pivot="programming-language-cpp"

次の例では、`Latency` を優先して開始時の認識を実行します。 このプロパティは、ユースケースの優先順位に応じて、`Accuracy` に設定することもできます。 `Latency` は、待機時間の短い結果が必要なのに (ライブ ストリーミングのケースなど)、オーディオ サンプルの言語がわからない場合に最適なオプションです。 

`Accuracy` は、オーディオ品質が低い可能性があるものの、長い待機時間を許容できるシナリオで使用する必要があります。 たとえば、ボイスメールに背景ノイズがある、または先頭に一定の無音があるが、エンジンでの処理時間を長くすると認識結果が向上するといったケースです。

どちらの場合も、次に示すような開始時の認識は、同じオーディオ サンプル内で言語が変更される可能性があるシナリオには **使用できません**。 これらタイプのシナリオに適した継続認識については、以下を参照してください。

```cpp
using namespace std;
using namespace Microsoft::CognitiveServices::Speech;
using namespace Microsoft::CognitiveServices::Speech::Audio;

auto speechConfig = SpeechConfig::FromSubscription("<paste-your-subscription-key>","<paste-your-region>");
speechConfig->SetProperty(PropertyId::SpeechServiceConnection_SingleLanguageIdPriority, "Latency");

auto autoDetectSourceLanguageConfig =
    AutoDetectSourceLanguageConfig::FromLanguages({ "en-US", "de-DE", "ja-JP", "de-DE" });

auto recognizer = SpeechRecognizer::FromConfig(
    speechConfig,
    autoDetectSourceLanguageConfig
    );

speechRecognitionResult = recognizer->RecognizeOnceAsync().get();
auto autoDetectSourceLanguageResult =
    AutoDetectSourceLanguageResult::FromResult(speechRecognitionResult);
auto detectedLanguage = autoDetectSourceLanguageResult->Language;
```

次の例には、多言語シナリオでの連続音声認識の設定が示されています。 この例では、言語構成で `en-US` と `ja-JP` のみを使用しますが、この設計パターンに最大 **10 の言語** を使用できます。 音声が検出されるたびに、ソース言語が識別され、オーディオもテキスト出力に変換されます。 この例では、応答時間を優先する `Latency` モードが使用されています。

```cpp
using namespace std;
using namespace Microsoft::CognitiveServices::Speech;
using namespace Microsoft::CognitiveServices::Speech::Audio;

auto region = "<paste-your-region>";
// currently the v2 endpoint is required for this design pattern
auto endpointString = std::format("wss://{}.stt.speech.microsoft.com/speech/universal/v2", region);
auto config = SpeechConfig::FromEndpoint(endpointString, "<paste-your-subscription-key>");

config->SetProperty(PropertyId::SpeechServiceConnection_ContinuousLanguageIdPriority, "Latency");
auto autoDetectSourceLanguageConfig = AutoDetectSourceLanguageConfig::FromLanguages({ "en-US", "ja-JP" });

auto audioInput = AudioConfig::FromWavFileInput("path-to-your-audio-file.wav");
auto recognizer = SpeechRecognizer::FromConfig(config, autoDetectSourceLanguageConfig, audioInput);

// promise for synchronization of recognition end.
promise<void> recognitionEnd;

// Subscribes to events.
recognizer->Recognizing.Connect([](const SpeechRecognitionEventArgs& e)
    {
        auto lidResult = AutoDetectSourceLanguageResult::FromResult(e.Result);
        cout << "Recognizing in " << lidResult->Language << ": Text =" << e.Result->Text << std::endl;
    });

recognizer->Recognized.Connect([](const SpeechRecognitionEventArgs& e)
    {
        if (e.Result->Reason == ResultReason::RecognizedSpeech)
        {
            auto lidResult = AutoDetectSourceLanguageResult::FromResult(e.Result);
            cout << "RECOGNIZED in " << lidResult->Language << ": Text=" << e.Result->Text << "\n"
                << "  Offset=" << e.Result->Offset() << "\n"
                << "  Duration=" << e.Result->Duration() << std::endl;
        }
        else if (e.Result->Reason == ResultReason::NoMatch)
        {
            cout << "NOMATCH: Speech could not be recognized." << std::endl;
        }
    });

recognizer->Canceled.Connect([&recognitionEnd](const SpeechRecognitionCanceledEventArgs& e)
    {
        cout << "CANCELED: Reason=" << (int)e.Reason << std::endl;

        if (e.Reason == CancellationReason::Error)
        {
            cout << "CANCELED: ErrorCode=" << (int)e.ErrorCode << "\n"
                << "CANCELED: ErrorDetails=" << e.ErrorDetails << "\n"
                << "CANCELED: Did you update the subscription info?" << std::endl;

            recognitionEnd.set_value(); // Notify to stop recognition.
        }
    });

recognizer->SessionStopped.Connect([&recognitionEnd](const SessionEventArgs& e)
    {
        cout << "Session stopped.";
        recognitionEnd.set_value(); // Notify to stop recognition.
    });

recognizer->StartContinuousRecognitionAsync().get();

// Waits for recognition end.
recognitionEnd.get_future().get();

// Stops recognition.
recognizer->StopContinuousRecognitionAsync().get();
```

> [!NOTE]
> `Latency` と `Accuracy` モード、および多言語の継続認識は、現在、C#、C++、Python でのみサポートされています。

::: zone-end

::: zone pivot="programming-language-java"

```java
AutoDetectSourceLanguageConfig autoDetectSourceLanguageConfig =
    AutoDetectSourceLanguageConfig.fromLanguages(Arrays.asList("en-US", "de-DE"));

SpeechRecognizer recognizer = new SpeechRecognizer(
    speechConfig,
    autoDetectSourceLanguageConfig,
    audioConfig);

Future<SpeechRecognitionResult> future = recognizer.recognizeOnceAsync();
SpeechRecognitionResult result = future.get(30, TimeUnit.SECONDS);
AutoDetectSourceLanguageResult autoDetectSourceLanguageResult =
    AutoDetectSourceLanguageResult.fromResult(result);
String detectedLanguage = autoDetectSourceLanguageResult.getLanguage();

recognizer.close();
speechConfig.close();
autoDetectSourceLanguageConfig.close();
audioConfig.close();
result.close();
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
auto_detect_source_language_config = \
        speechsdk.languageconfig.AutoDetectSourceLanguageConfig(languages=["en-US", "de-DE"])
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, 
        auto_detect_source_language_config=auto_detect_source_language_config, 
        audio_config=audio_config)
result = speech_recognizer.recognize_once()
auto_detect_source_language_result = speechsdk.AutoDetectSourceLanguageResult(result)
detected_language = auto_detect_source_language_result.language
```

::: zone-end

::: zone pivot="programming-language-objectivec"

```Objective-C
NSArray *languages = @[@"zh-CN", @"de-DE"];
SPXAutoDetectSourceLanguageConfiguration* autoDetectSourceLanguageConfig = \
        [[SPXAutoDetectSourceLanguageConfiguration alloc]init:languages];
SPXSpeechRecognizer* speechRecognizer = \
        [[SPXSpeechRecognizer alloc] initWithSpeechConfiguration:speechConfig
                           autoDetectSourceLanguageConfiguration:autoDetectSourceLanguageConfig
                                              audioConfiguration:audioConfig];
SPXSpeechRecognitionResult *result = [speechRecognizer recognizeOnce];
SPXAutoDetectSourceLanguageResult *languageDetectionResult = [[SPXAutoDetectSourceLanguageResult alloc] init:result];
NSString *detectedLanguage = [languageDetectionResult language];
```

::: zone-end

::: zone pivot="programming-language-javascript"

```Javascript
var autoDetectConfig = SpeechSDK.AutoDetectSourceLanguageConfig.fromLanguages(["en-US", "de-DE"]);
var speechRecognizer = SpeechSDK.SpeechRecognizer.FromConfig(speechConfig, autoDetectConfig, audioConfig);
speechRecognizer.recognizeOnceAsync((result: SpeechSDK.SpeechRecognitionResult) => {
        var languageDetectionResult = SpeechSDK.AutoDetectSourceLanguageResult.fromResult(result);
        var detectedLanguage = languageDetectionResult.language;
},
{});
```

::: zone-end


## <a name="use-language-detection-with-a-custom-speech-to-text-model"></a>カスタム音声テキスト変換モデルで言語検出を使用する

Speech サービスのベース モデルを利用した言語識別に加え、認識を強化するためのカスタム モデルも指定できます。 カスタム モデルが指定されていない場合、このサービスでは既定の言語モデルが使用されます。

下のスニペットからは、Speech サービスの呼び出しにカスタム モデルを指定する方法を確認できます。 検出された言語が `en-US` の場合、既定のモデルが使用されます。 検出された言語が `fr-FR` の場合、カスタム モデルのエンドポイントが使用されます。

::: zone pivot="programming-language-csharp"

```csharp
var sourceLanguageConfigs = new SourceLanguageConfig[]
{
    SourceLanguageConfig.FromLanguage("en-US"),
    SourceLanguageConfig.FromLanguage("fr-FR", "The Endpoint Id for custom model of fr-FR")
};
var autoDetectSourceLanguageConfig =
    AutoDetectSourceLanguageConfig.FromSourceLanguageConfigs(
        sourceLanguageConfigs);
```

::: zone-end

::: zone pivot="programming-language-cpp"

```cpp
std::vector<std::shared_ptr<SourceLanguageConfig>> sourceLanguageConfigs;
sourceLanguageConfigs.push_back(
    SourceLanguageConfig::FromLanguage("en-US"));
sourceLanguageConfigs.push_back(
    SourceLanguageConfig::FromLanguage("fr-FR", "The Endpoint Id for custom model of fr-FR"));

auto autoDetectSourceLanguageConfig =
    AutoDetectSourceLanguageConfig::FromSourceLanguageConfigs(
        sourceLanguageConfigs);
```

::: zone-end

::: zone pivot="programming-language-java"

```java
List sourceLanguageConfigs = new ArrayList<SourceLanguageConfig>();
sourceLanguageConfigs.add(
    SourceLanguageConfig.fromLanguage("en-US"));
sourceLanguageConfigs.add(
    SourceLanguageConfig.fromLanguage("fr-FR", "The Endpoint Id for custom model of fr-FR"));

AutoDetectSourceLanguageConfig autoDetectSourceLanguageConfig =
    AutoDetectSourceLanguageConfig.fromSourceLanguageConfigs(
        sourceLanguageConfigs);
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
 en_language_config = speechsdk.languageconfig.SourceLanguageConfig("en-US")
 fr_language_config = speechsdk.languageconfig.SourceLanguageConfig("fr-FR", "The Endpoint Id for custom model of fr-FR")
 auto_detect_source_language_config = speechsdk.languageconfig.AutoDetectSourceLanguageConfig(
        sourceLanguageConfigs=[en_language_config, fr_language_config])
```

::: zone-end

::: zone pivot="programming-language-objectivec"

```Objective-C
SPXSourceLanguageConfiguration* enLanguageConfig = [[SPXSourceLanguageConfiguration alloc]init:@"en-US"];
SPXSourceLanguageConfiguration* frLanguageConfig = \
        [[SPXSourceLanguageConfiguration alloc]initWithLanguage:@"fr-FR"
                                                     endpointId:@"The Endpoint Id for custom model of fr-FR"];
NSArray *languageConfigs = @[enLanguageConfig, frLanguageConfig];
SPXAutoDetectSourceLanguageConfiguration* autoDetectSourceLanguageConfig = \
        [[SPXAutoDetectSourceLanguageConfiguration alloc]initWithSourceLanguageConfigurations:languageConfigs];
```

::: zone-end

::: zone pivot="programming-language-javascript"

```Javascript
var enLanguageConfig = SpeechSDK.SourceLanguageConfig.fromLanguage("en-US");
var frLanguageConfig = SpeechSDK.SourceLanguageConfig.fromLanguage("fr-FR", "The Endpoint Id for custom model of fr-FR");
var autoDetectConfig = SpeechSDK.AutoDetectSourceLanguageConfig.fromSourceLanguageConfigs([enLanguageConfig, frLanguageConfig]);
```

::: zone-end


## <a name="next-steps"></a>次のステップ

::: zone pivot="programming-language-csharp"
* 言語識別については、GitHub の[サンプル コード](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/speech_recognition_samples.cs#L741)を参照してください
::: zone-end

::: zone pivot="programming-language-cpp"
* 言語識別については、GitHub の[サンプル コード](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/cpp/windows/console/samples/speech_recognition_samples.cpp#L507)を参照してください
::: zone-end

::: zone pivot="programming-language-java"
* 言語識別については、GitHub の[サンプル コード](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/SpeechRecognitionSamples.java#L521)を参照してください
::: zone-end

::: zone pivot="programming-language-python"
* 言語識別については、GitHub の[サンプル コード](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/python/console/speech_sample.py#L458)を参照してください
::: zone-end

::: zone pivot="programming-language-objectivec"
* 言語識別については、GitHub の[サンプル コード](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/objective-c/ios/speech-samples/speech-samples/ViewController.m#L525)を参照してください
::: zone-end
