---
title: 音声テキスト変換に自動言語検出を使用する方法
titleSuffix: Azure Cognitive Services
description: Speech SDK では、音声テキスト変換の自動言語検出をサポートしています。 この機能を使用すると、入力された音声が提供された言語一覧で照合され、可能性が最も高い一致が決定されます。 値が返されたらそれを利用し、音声テキスト変換に使用する言語モデルを選択できます。
services: cognitive-services
author: susanhu
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/26/2019
ms.author: qiohu
ms.openlocfilehash: a72f477e64c856c545801533c131c397de627c00
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2019
ms.locfileid: "74110185"
---
# <a name="automatic-language-detection-for-speech-to-text"></a>音声テキスト変換の自動言語検出

自動言語検出を使用して、提供された言語一覧に対して Speech SDK に渡されたオーディオを照合し、可能性が最も高い一致を判断します。 自動言語検出から値が返されたら、その値を利用し、音声テキスト変換の言語モデルが選択され、文字起こしの精度が上がります。 利用できる言語については、[言語サポート](language-support.md)に関するページを参照してください。

この記事では、`AutoDetectSourceLanguageConfig` を利用して `SpeechRecognizer` オブジェクトを構築し、検出された言語を取得する方法について説明します。

> [!IMPORTANT]
> この機能は Speech SDK for C++ と Speech SDK for Java でのみ利用できます。

## <a name="automatic-language-detection-with-the-speech-sdk"></a>Speech SDK による自動言語検出

自動言語検出には現在、1 回の検出で 2 言語というサービス側の制限があります。 `AudoDetectSourceLanguageConfig` オブジェクトを構築するとき、この上限を念頭に置いてください。 下のサンプルでは、`AutoDetectSourceLanguageConfig` を作成し、それを利用して `SpeechRecognizer` を構築します。

> [!TIP]
> 音声テキスト変換時に使用するカスタム モデルを指定することもできます。 詳細については、「[自動言語検出にカスタム モデルを使用する](#use-a-custom-model-for-automatic-language-detection)」を参照してください。

次のスニペットからは、自分のアプリで自動言語検出を使用する方法を確認できます。

```C++
auto autoDetectSourceLanguageConfig = AutoDetectSourceLanguageConfig::FromLanguages({ "en-US", "de-DE" });
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, autoDetectSourceLanguageConfig, audioConfig);
speechRecognitionResult = recognizer->RecognizeOnceAsync().get();
auto autoDetectSourceLanguageResult = AutoDetectSourceLanguageResult::FromResult(speechRecognitionResult);
auto detectedLanguage = autoDetectSourceLanguageResult->Language;
```

```Java
AutoDetectSourceLanguageConfig autoDetectSourceLanguageConfig = AutoDetectSourceLanguageConfig.fromLanguages(Arrays.asList("en-US", "de-DE"));
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, autoDetectSourceLanguageConfig, audioConfig);
Future<SpeechRecognitionResult> future = recognizer.recognizeOnceAsync();
SpeechRecognitionResult result = future.get(30, TimeUnit.SECONDS);
AutoDetectSourceLanguageResult autoDetectSourceLanguageResult = AutoDetectSourceLanguageResult.fromResult(result);
String detectedLanguage = autoDetectSourceLanguageResult.getLanguage();

recognizer.close();
speechConfig.close();
autoDetectSourceLanguageConfig.close();
audioConfig.close();
result.close();
```

## <a name="use-a-custom-model-for-automatic-language-detection"></a>自動言語検出にカスタム モデルを使用する

Speech サービス モデルを利用した言語検出に加え、認識を強化するためのカスタム モデルを指定できます。 カスタム モデルが指定されていない場合、このサービスでは既定の言語モデルが使用されます。

下のスニペットからは、Speech サービスの呼び出しにカスタム モデルを指定する方法を確認できます。 検出された言語が `en-US` の場合、既定のモデルが使用されます。 検出された言語が `fr-FR` の場合、カスタム モデルのエンドポイントが使用されます。

```C++
std::vector<std::shared_ptr<SourceLanguageConfig>> sourceLanguageConfigs;
sourceLanguageConfigs.push_back(SourceLanguageConfig::FromLanguage("en-US"));
sourceLanguageConfigs.push_back(SourceLanguageConfig::FromLanguage("fr-FR", "The Endpoint Id for custom model of fr-FR"));
auto autoDetectSourceLanguageConfig = AutoDetectSourceLanguageConfig::FromSourceLanguageConfigs(sourceLanguageConfigs);
```

```Java
List sourceLanguageConfigs = new ArrayList<SourceLanguageConfig>();
sourceLanguageConfigs.add(SourceLanguageConfig.fromLanguage("en-US"));
sourceLanguageConfigs.add(SourceLanguageConfig.fromLanguage("fr-FR", "The Endpoint Id for custom model of fr-FR"));
AutoDetectSourceLanguageConfig autoDetectSourceLanguageConfig = AutoDetectSourceLanguageConfig.fromSourceLanguageConfigs(sourceLanguageConfigs);
```

## <a name="next-steps"></a>次の手順

- [Speech SDK のリファレンス ドキュメント](speech-sdk.md)
