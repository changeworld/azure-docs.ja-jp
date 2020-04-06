---
title: 音声テキスト変換のソース言語を指定する方法
titleSuffix: Azure Cognitive Services
description: Speech SDK を使うと、音声をテキストに変換するときのソース言語を指定できます。 この記事では、FromConfig メソッドと SourceLanguageConfig メソッドを使って、Speech Service がソース言語を認識し、カスタム モデル ターゲットを提供できるようにする方法について説明します。
services: cognitive-services
author: susanhu
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/07/2020
ms.author: qiohu
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: f0723534d9d2187593cb73f058ffea62473b80a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235969"
---
# <a name="specify-source-language-for-speech-to-text"></a>音声テキスト変換のソース言語を指定する

この記事では、音声認識用に Speech SDK に渡されるオーディオ入力のソース言語を指定する方法について説明します。 さらに、認識を改善するためにカスタム音声モデルを指定するコード例も提供します。

::: zone pivot="programming-language-csharp"

## <a name="how-to-specify-source-language-in-c"></a>C# でソース言語を指定する方法

この例では、`SpeechRecognizer` コンストラクトを使って、ソース言語をパラメーターとして明示的に指定します。

```csharp
var recognizer = new SpeechRecognizer(speechConfig, "de-DE", audioConfig);
```

この例では、`SourceLanguageConfig` を使ってソース言語を提供します。 次に、`sourceLanguageConfig` をパラメーターとして `SpeechRecognizer` コンストラクトに渡します。

```csharp
var sourceLanguageConfig = SourceLanguageConfig.FromLanguage("de-DE");
var recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

この例では、`SourceLanguageConfig` を使ってソース言語とカスタム エンドポイントを提供します。 次に、`sourceLanguageConfig` をパラメーターとして `SpeechRecognizer` コンストラクトに渡します。

```csharp
var sourceLanguageConfig = SourceLanguageConfig.FromLanguage("de-DE", "The Endpoint ID for your custom model.");
var recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> `SpeechRecognitionLanguage` および `EndpointId` の set メソッドは、C# の `SpeechConfig` クラスでは非推奨になっています。 これらのメソッドの使用は推奨されておらず、`SpeechRecognizer` を構築するときは使わないでください。

::: zone-end

::: zone pivot="programming-language-cpp"


## <a name="how-to-specify-source-language-in-c"></a>C++ でソース言語を指定する方法

この例では、`FromConfig` メソッドを使って、ソース言語をパラメーターとして明示的に指定します。

```C++
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, "de-DE", audioConfig);
```

この例では、`SourceLanguageConfig` を使ってソース言語を提供します。 次に、`recognizer` を作成するときに、`sourceLanguageConfig` をパラメーターとして `FromConfig` に渡します。

```C++
auto sourceLanguageConfig = SourceLanguageConfig::FromLanguage("de-DE");
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, sourceLanguageConfig, audioConfig);
```

この例では、`SourceLanguageConfig` を使ってソース言語とカスタム エンドポイントを提供します。 `recognizer` を作成するときに、`sourceLanguageConfig` をパラメーターとして `FromConfig` に渡します。

```C++
auto sourceLanguageConfig = SourceLanguageConfig::FromLanguage("de-DE", "The Endpoint ID for your custom model.");
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> `SetSpeechRecognitionLanguage` および `SetEndpointId` メソッドは、C++ と Java の `SpeechConfig` クラスでは非推奨になっています。 これらのメソッドの使用は推奨されておらず、`SpeechRecognizer` を構築するときは使わないでください。

::: zone-end

::: zone pivot="programming-language-java"

## <a name="how-to-specify-source-language-in-java"></a>Java でソース言語を指定する方法

この例では、新しい `SpeechRecognizer` を作成するときに、ソース言語を明示的に指定します。

```Java
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, "de-DE", audioConfig);
```

この例では、`SourceLanguageConfig` を使ってソース言語を提供します。 次に、新しい `SpeechRecognizer` を作成するときに、`sourceLanguageConfig` をパラメーターとして渡します。

```Java
SourceLanguageConfig sourceLanguageConfig = SourceLanguageConfig.fromLanguage("de-DE");
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

この例では、`SourceLanguageConfig` を使ってソース言語とカスタム エンドポイントを提供します。 次に、新しい `SpeechRecognizer` を作成するときに、`sourceLanguageConfig` をパラメーターとして渡します。

```Java
SourceLanguageConfig sourceLanguageConfig = SourceLanguageConfig.fromLanguage("de-DE", "The Endpoint ID for your custom model.");
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> `setSpeechRecognitionLanguage` および `setEndpointId` メソッドは、C++ と Java の `SpeechConfig` クラスでは非推奨になっています。 これらのメソッドの使用は推奨されておらず、`SpeechRecognizer` を構築するときは使わないでください。

::: zone-end

::: zone pivot="programming-language-python"

## <a name="how-to-specify-source-language-in-python"></a>Python でソース言語を指定する方法

この例では、`SpeechRecognizer` コンストラクトを使って、ソース言語をパラメーターとして明示的に指定します。

```Python
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, language="de-DE", audio_config=audio_config)
```

この例では、`SourceLanguageConfig` を使ってソース言語を提供します。 次に、`SourceLanguageConfig` をパラメーターとして `SpeechRecognizer` コンストラクトに渡します。

```Python
source_language_config = speechsdk.languageconfig.SourceLanguageConfig("de-DE")
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, source_language_config=source_language_config, audio_config=audio_config)
```

この例では、`SourceLanguageConfig` を使ってソース言語とカスタム エンドポイントを提供します。 次に、`SourceLanguageConfig` をパラメーターとして `SpeechRecognizer` コンストラクトに渡します。

```Python
source_language_config = speechsdk.languageconfig.SourceLanguageConfig("de-DE", "The Endpoint ID for your custom model.")
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, source_language_config=source_language_config, audio_config=audio_config)
```

>[!Note]
> `speech_recognition_language` および `endpoint_id` プロパティは、Python の `SpeechConfig` クラスで非推奨になっています。 これらのプロパティの使用は推奨されていないので、`SpeechRecognizer` を構築するときに使わないでください。

::: zone-end

::: zone pivot="programming-language-more"

## <a name="how-to-specify-source-language-in-javascript"></a>JavaScript でソース言語を指定する方法

最初のステップは、`SpeechConfig` を作成することです。

```Javascript
var speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionkey", "YourRegion");
```

次に、`speechRecognitionLanguage` でオーディオのソース言語を指定します。

```Javascript
speechConfig.speechRecognitionLanguage = "de-DE";
```

認識にカスタムモデルを使っている場合は、`endpointId` でエンドポイントを指定できます。

```Javascript
speechConfig.endpointId = "The Endpoint ID for your custom model.";
```

## <a name="how-to-specify-source-language-in-objective-c"></a>Objective-C でソース言語を指定する方法

最初のステップは、`speechConfig` を作成することです。

```Objective-C
SPXSpeechConfiguration *speechConfig = [[SPXSpeechConfiguration alloc] initWithSubscription:@"YourSubscriptionkey" region:@"YourRegion"];
```

次に、`speechRecognitionLanguage` でオーディオのソース言語を指定します。

```Objective-C
speechConfig.speechRecognitionLanguage = @"de-DE";
```

認識にカスタムモデルを使っている場合は、`endpointId` でエンドポイントを指定できます。

```Objective-C
speechConfig.endpointId = @"The Endpoint ID for your custom model.";
```

::: zone-end

## <a name="see-also"></a>関連項目

* 音声テキスト変換に対してサポートされている言語とロケールの一覧については、[言語のサポート](language-support.md)に関する記事を参照してください。

## <a name="next-steps"></a>次のステップ

* [Speech SDK のリファレンス ドキュメント](speech-sdk.md)
