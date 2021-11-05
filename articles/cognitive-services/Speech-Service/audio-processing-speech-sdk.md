---
title: Speech SDK を使用したオーディオ処理 - Speech サービス
titleSuffix: Azure Cognitive Services
description: Speech Software Development Kit (SDK) を使用したカスタム キーワードの特徴、機能、制限の概要。
services: cognitive-services
author: hasyashah
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/17/2021
ms.author: hasshah
ms.custom: devx-track-csharp, ignite-fall-2021
ms.openlocfilehash: bbf774ac1f0b91848df48366436144d36b193033
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131091961"
---
# <a name="audio-processing-with-speech-sdk"></a>Speech SDK を使用したオーディオ処理

Speech SDK は Microsoft Audio Stack (MAS) を統合し、任意のアプリケーションまたは製品が入力オーディオでオーディオ処理機能を使用できるようにしています。 [Microsoft オーディオスタックを使用するための最小要件](audio-processing-overview.md#minimum-requirements-to-use-microsoft-audio-stack)からの最小要件が適用されます。

Speech SDK API を使用して利用できる主な機能は次のとおりです。
* **リアルタイム マイク入力&amp;入力** - Microsoft Audio Stack の処理は、リアルタイムマイクの入力、ストリーム、およびファイルベースの入力に適用できます。 
* **拡張機能の選択** - シナリオを完全に制御するために、SDK を使用すると、逆サーバー、ノイズ抑制、自動ゲイン制御、音響エコー キャンセルなどの個々の機能強化を無効にできます。 たとえば、入力オーディオから抑制する必要がある出力オーディオのレンダリングがシナリオに含されていない場合は、音響エコー キャンセルを無効にするオプションがあります。
* **カスタム マイク** ジオメトリ - SDK を使用すると、線形 2 マイク、線形 4 マイク、循環 7 マイク 配列のようなプリセット ジオメトリをサポートするほか、独自のカスタム マイク ジオメトリ情報を提供できます([「マイク 配列](speech-sdk-microphone.md#microphone-geometry)の推奨事項」でサポートされているプリセット ジオメトリの詳細を参照してください)。
* **光線形成角度** - 特定の光線形成角度を指定して、マイクに対して、事前に定義された場所からのオーディオ入力を最適化できます。

処理は、Speech SDK が使用されているローカルで完全に実行されます。 Microsoft オーディオ スタックによって処理するために、オーディオ データは Microsoft のクラウド サービスにストリーミングされません。 この唯一の例外は、Conversation Transcription Service の場合です。このサービスでは、未加工のオーディオが処理のために Microsoft のクラウド サービスに送信されます。 

> [!NOTE]
> Speech Devices SDK は非推奨とされました。 [ここ](speech-devices-sdk.md)では、アーカイブされたバージョンの SPEECH Devices SDK を使用できます。対応するサンプルコードは[GitHub](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK)で入手できます。 Speech Devices SDK のすべてのユーザーは、Speech SDK v1.19 以降を使用して に移行してください。

## <a name="reference-channel-for-echo-cancellation"></a>エコー キャンセルの参照チャネル

Microsoft Audio Stack では、エコー キャンセルを実行するために参照チャネル (ループバック チャネルとも呼ばれる) が必要です。 参照チャネルのソースは、プラットフォームによって異なります。
* **Windows** - `SpeakerReferenceChannel::LastChannel` 作成時にオプションが指定されている場合、参照チャネルは Speech SDK によって自動的に収集され `AudioProcessingOptions` ます。
* **Linux** - ALSA (高度な Linux サウンド アーキテクチャ) は、使用されるオーディオ入力デバイスの最後のチャネルとして参照オーディオ ストリームを提供するように構成する必要があります。 これは作成するときにオプションを指定することに追加され `SpeakerReferenceChannel::LastChannel``AudioProcessingOptions` ます。

## <a name="language-and-platform-support"></a>言語とプラットフォームのサポート

| 言語   | /platform:    | リファレンス ドキュメント |
|------------|----------------|----------------|
| C++        | Windows、Linux | [C# ドキュメント](/cpp/cognitive-services/speech/) |
| C#         | Windows、Linux | [C# ドキュメント](/dotnet/api/microsoft.cognitiveservices.speech) |
| Java       | Windows、Linux | [Java ドキュメント](/java/api/com.microsoft.cognitiveservices.speech) |

## <a name="sample-code"></a>サンプル コード

### <a name="using-microsoft-audio-stack-with-all-default-options"></a>すべての既定のオプションで Microsoft Audio Stack を使用する

このサンプルでは、デバイスの既定のマイクからの入力に関する既定の拡張機能オプションすべてと一緒に MAS を使用する方法を示します。

#### <a name="c"></a>[C#](#tab/csharp)

```csharp
var speechConfig = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");

var audioProcessingOptions = AudioProcessingOptions.Create(AudioProcessingConstants.AUDIO_INPUT_PROCESSING_ENABLE_DEFAULT);
var audioInput = AudioConfig.FromDefaultMicrophoneInput(audioProcessingOptions);

var recognizer = new SpeechRecognizer(speechConfig, audioInput);
```

#### <a name="c"></a>[C++](#tab/cpp)

```cpp
auto speechConfig = SpeechConfig::FromSubscription("YourSubscriptionKey", "YourServiceRegion");

auto audioProcessingOptions = AudioProcessingOptions::Create(AUDIO_INPUT_PROCESSING_ENABLE_DEFAULT);
auto audioInput = AudioConfig::FromDefaultMicrophoneInput(audioProcessingOptions);

auto recognizer = SpeechRecognizer::FromConfig(speechConfig, audioInput);
```

#### <a name="java"></a>[Java](#tab/java)

```java
SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");

AudioProcessingOptions audioProcessingOptions = AudioProcessingOptions.create(AudioProcessingConstants.AUDIO_INPUT_PROCESSING_ENABLE_DEFAULT);
AudioConfig audioInput = AudioConfig.fromDefaultMicrophoneInput(audioProcessingOptions);

SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, audioInput);
```
---

### <a name="using-microsoft-audio-stack-with-a-preset-microphone-geometry"></a>Microsoft Audio Stack とプリセット マイク ジオメトリの使用

このサンプルでは、指定されたオーディオ入力デバイスで定義済みのマイク ジオメトリで MAS を使用する方法を示します。 次の点に注意してください。
* **拡張オプション** - 既定の機能強化は、入力オーディオ ストリームに適用されます。
* **プリセット ジオメトリ** - プリセット ジオメトリは、線形の 2 マイク配列を表します。
* **オーディオ入力デバイス** -オーディオ入力デバイス ID は `hw:0,1` です。 オーディオ入力デバイスを選択する方法の詳細については、「方法: Speech SDK を使用してオーディオ入力デバイスを選択する [」を参照してください](how-to-select-audio-input-devices.md)。

#### <a name="c"></a>[C#](#tab/csharp)

```csharp
var speechConfig = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");

var audioProcessingOptions = AudioProcessingOptions.Create(AudioProcessingConstants.AUDIO_INPUT_PROCESSING_ENABLE_DEFAULT, PresetMicrophoneArrayGeometry.Linear2);
var audioInput = AudioConfig.FromMicrophoneInput("hw:0,1", audioProcessingOptions);

var recognizer = new SpeechRecognizer(speechConfig, audioInput);
```

#### <a name="c"></a>[C++](#tab/cpp)

```cpp
auto speechConfig = SpeechConfig::FromSubscription("YourSubscriptionKey", "YourServiceRegion");

auto audioProcessingOptions = AudioProcessingOptions::Create(AUDIO_INPUT_PROCESSING_ENABLE_DEFAULT, PresetMicrophoneArrayGeometry::Linear2);
auto audioInput = AudioConfig::FromMicrophoneInput("hw:0,1", audioProcessingOptions);

auto recognizer = SpeechRecognizer::FromConfig(speechConfig, audioInput);
```

#### <a name="java"></a>[Java](#tab/java)

```java
SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");

AudioProcessingOptions audioProcessingOptions = AudioProcessingOptions.create(AudioProcessingConstants.AUDIO_INPUT_PROCESSING_ENABLE_DEFAULT, PresetMicrophoneArrayGeometry.Linear2);
AudioConfig audioInput = AudioConfig.fromMicrophoneInput("hw:0,1", audioProcessingOptions);

SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, audioInput);
```
---

### <a name="using-microsoft-audio-stack-with-custom-microphone-geometry"></a>Microsoft Audio Stack とプリセット マイク ジオメトリの使用

このサンプルでは、指定されたオーディオ入力デバイスで定義済みのマイク ジオメトリで MAS を使用する方法を示します。 次の点に注意してください。
* **拡張オプション** - 既定の機能強化は、入力オーディオ ストリームに適用されます。
* **カスタム ジオメトリ** - 4 マイク配列のカスタム マイク ジオメトリは、マイク座標を指定することで提供されます。 座標の単位は mm です。
* **オーディオ入力** - オーディオ入力はファイルからの入力で、ファイル内のオーディオは、指定されたカスタム ジオメトリに対応するオーディオ入力デバイスからキャプチャされる必要があります。 

#### <a name="c"></a>[C#](#tab/csharp)

```csharp
var speechConfig = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");

MicrophoneCoordinates[] microphoneCoordinates = new MicrophoneCoordinates[7]
{
    new MicrophoneCoordinates(0, 0, 0),
    new MicrophoneCoordinates(40, 0, 0),
    new MicrophoneCoordinates(20, -35, 0),
    new MicrophoneCoordinates(-20, -35, 0),
    new MicrophoneCoordinates(-40, 0, 0),
    new MicrophoneCoordinates(-20, 35, 0),
    new MicrophoneCoordinates(20, 35, 0)
};
var microphoneArrayGeometry = new MicrophoneArrayGeometry(MicrophoneArrayType.Planar, microphoneCoordinates);
var audioProcessingOptions = AudioProcessingOptions.Create(AudioProcessingConstants.AUDIO_INPUT_PROCESSING_ENABLE_DEFAULT, microphoneArrayGeometry, SpeakerReferenceChannel.LastChannel);
var audioInput = AudioConfig.FromWavFileInput("katiesteve.wav", audioProcessingOptions);

var recognizer = new SpeechRecognizer(speechConfig, audioInput);
```

#### <a name="c"></a>[C++](#tab/cpp)

```cpp
auto speechConfig = SpeechConfig::FromSubscription("YourSubscriptionKey", "YourServiceRegion");

MicrophoneArrayGeometry microphoneArrayGeometry
{
    MicrophoneArrayType::Planar,
    { { 0, 0, 0 }, { 40, 0, 0 }, { 20, -35, 0 }, { -20, -35, 0 }, { -40, 0, 0 }, { -20, 35, 0 }, { 20, 35, 0 } }
};
auto audioProcessingOptions = AudioProcessingOptions::Create(AUDIO_INPUT_PROCESSING_ENABLE_DEFAULT, microphoneArrayGeometry, SpeakerReferenceChannel::LastChannel);
auto audioInput = AudioConfig::FromWavFileInput("katiesteve.wav", audioProcessingOptions);

auto recognizer = SpeechRecognizer::FromConfig(speechConfig, audioInput);
```

#### <a name="java"></a>[Java](#tab/java)

```java
SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");

MicrophoneCoordinates[] microphoneCoordinates = new MicrophoneCoordinates[7];
microphoneCoordinates[0] = new MicrophoneCoordinates(0, 0, 0);
microphoneCoordinates[1] = new MicrophoneCoordinates(40, 0, 0);
microphoneCoordinates[2] = new MicrophoneCoordinates(20, -35, 0);
microphoneCoordinates[3] = new MicrophoneCoordinates(-20, -35, 0);
microphoneCoordinates[4] = new MicrophoneCoordinates(-40, 0, 0);
microphoneCoordinates[5] = new MicrophoneCoordinates(-20, 35, 0);
microphoneCoordinates[6] = new MicrophoneCoordinates(20, 35, 0);
MicrophoneArrayGeometry microphoneArrayGeometry = new MicrophoneArrayGeometry(MicrophoneArrayType.Planar, microphoneCoordinates);
AudioProcessingOptions audioProcessingOptions = AudioProcessingOptions.create(AudioProcessingConstants.AUDIO_INPUT_PROCESSING_ENABLE_DEFAULT, microphoneArrayGeometry, SpeakerReferenceChannel.LastChannel);
AudioConfig audioInput = AudioConfig.fromWavFileInput("katiesteve.wav", audioProcessingOptions);

SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, audioInput);
```
---

### <a name="using-microsoft-audio-stack-with-select-enhancements"></a>Microsoft Audio Stack と一部の拡張機能の使用

このサンプルでは、入力オーディオのカスタム拡張機能セットで MAS を使用する方法を示します。 既定では、すべての機能強化が有効になっていますが、 を使用して、デサーバー、ノイズ抑制、自動ゲイン制御、エコー キャンセルを個別に無効にするオプションがあります `AudioProcessingOptions` 。

次の点に注意してください。
* **拡張オプション** - エコーキャンセルとノイズ抑制は無効になりますが、その他のすべての機能強化は有効なままです。
* **オーディオ入力デバイス** - オーディオ入力デバイスは、デバイスの既定のマイクです。

#### <a name="c"></a>[C#](#tab/csharp)

```csharp
var speechConfig = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");

var audioProcessingOptions = AudioProcessingOptions.Create(AudioProcessingConstants.AUDIO_INPUT_PROCESSING_DISABLE_ECHO_CANCELLATION | AudioProcessingConstants.AUDIO_INPUT_PROCESSING_DISABLE_NOISE_SUPPRESSION | AudioProcessingConstants.AUDIO_INPUT_PROCESSING_ENABLE_DEFAULT);
var audioInput = AudioConfig.FromDefaultMicrophoneInput(audioProcessingOptions);

var recognizer = new SpeechRecognizer(speechConfig, audioInput);
```

#### <a name="c"></a>[C++](#tab/cpp)

```cpp
auto speechConfig = SpeechConfig::FromSubscription("YourSubscriptionKey", "YourServiceRegion");

auto audioProcessingOptions = AudioProcessingOptions::Create(AUDIO_INPUT_PROCESSING_DISABLE_ECHO_CANCELLATION | AUDIO_INPUT_PROCESSING_DISABLE_NOISE_SUPPRESSION | AUDIO_INPUT_PROCESSING_ENABLE_DEFAULT);
auto audioInput = AudioConfig::FromDefaultMicrophoneInput(audioProcessingOptions);

auto recognizer = SpeechRecognizer::FromConfig(speechConfig, audioInput);
```

#### <a name="java"></a>[Java](#tab/java)

```java
SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");

AudioProcessingOptions audioProcessingOptions = AudioProcessingOptions.create(AudioProcessingConstants.AUDIO_INPUT_PROCESSING_DISABLE_ECHO_CANCELLATION | AudioProcessingConstants.AUDIO_INPUT_PROCESSING_DISABLE_NOISE_SUPPRESSION | AudioProcessingConstants.AUDIO_INPUT_PROCESSING_ENABLE_DEFAULT);
AudioConfig audioInput = AudioConfig.fromDefaultMicrophoneInput(audioProcessingOptions);

SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, audioInput);
```
---

### <a name="using-microsoft-audio-stack-to-specify-beamforming-angles"></a>Microsoft Audio Stack を使用した光線形成角度の指定

このサンプルでは、指定されたオーディオ入力デバイスで定義済みのマイク ジオメトリで MAS を使用する方法を示します。 次の点に注意してください。
* **拡張オプション** - 既定の機能強化は、入力オーディオ ストリームに適用されます。
* **カスタム ジオメトリ** - 4 マイク配列のカスタム マイク ジオメトリは、マイク座標を指定することで提供されます。 座標の単位は mm です。
* **[光線の形成角度]** - その範囲で発生するオーディオに合って最適化するために、光線形成角度が指定されます。 角度の単位は度です。 次のサンプル コードでは、開始角度が 70 度に設定され、終了角度が 110 度に設定されています。
* **オーディオ入力** - オーディオ入力はファイルからの入力で、ファイル内のオーディオは、指定されたカスタム ジオメトリに対応するオーディオ入力デバイスからキャプチャされる必要があります。 

#### <a name="c"></a>[C#](#tab/csharp)

```csharp
var speechConfig = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");

MicrophoneCoordinates[] microphoneCoordinates = new MicrophoneCoordinates[4]
{
    new MicrophoneCoordinates(-60, 0, 0),
    new MicrophoneCoordinates(-20, 0, 0),
    new MicrophoneCoordinates(20, 0, 0),
    new MicrophoneCoordinates(60, 0, 0)
};
var microphoneArrayGeometry = new MicrophoneArrayGeometry(MicrophoneArrayType.Linear, 70, 110, microphoneCoordinates);
var audioProcessingOptions = AudioProcessingOptions.Create(AudioProcessingConstants.AUDIO_INPUT_PROCESSING_ENABLE_DEFAULT, microphoneArrayGeometry, SpeakerReferenceChannel.LastChannel);
var pushStream = AudioInputStream.CreatePushStream();
var audioInput = AudioConfig.FromStreamInput(pushStream, audioProcessingOptions);

var recognizer = new SpeechRecognizer(speechConfig, audioInput);
```

#### <a name="c"></a>[C++](#tab/cpp)

```cpp
auto speechConfig = SpeechConfig::FromSubscription("YourSubscriptionKey", "YourServiceRegion");

MicrophoneArrayGeometry microphoneArrayGeometry
{
    MicrophoneArrayType::Linear,
    70,
    110,
    { { -60, 0, 0 }, { -20, 0, 0 }, { 20, 0, 0 }, { 60, 0, 0 } }
};
auto audioProcessingOptions = AudioProcessingOptions::Create(AUDIO_INPUT_PROCESSING_ENABLE_DEFAULT, microphoneArrayGeometry, SpeakerReferenceChannel::LastChannel);
auto pushStream = AudioInputStream::CreatePushStream();
auto audioInput = AudioConfig::FromStreamInput(pushStream, audioProcessingOptions);

auto recognizer = SpeechRecognizer::FromConfig(speechConfig, audioInput);
```

#### <a name="java"></a>[Java](#tab/java)

```java
SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");

MicrophoneCoordinates[] microphoneCoordinates = new MicrophoneCoordinates[4];
microphoneCoordinates[0] = new MicrophoneCoordinates(-60, 0, 0);
microphoneCoordinates[1] = new MicrophoneCoordinates(-20, 0, 0);
microphoneCoordinates[2] = new MicrophoneCoordinates(20, 0, 0);
microphoneCoordinates[3] = new MicrophoneCoordinates(60, 0, 0);
MicrophoneArrayGeometry microphoneArrayGeometry = new MicrophoneArrayGeometry(MicrophoneArrayType.Planar, 70, 110, microphoneCoordinates);
AudioProcessingOptions audioProcessingOptions = AudioProcessingOptions.create(AudioProcessingConstants.AUDIO_INPUT_PROCESSING_ENABLE_DEFAULT, microphoneArrayGeometry, SpeakerReferenceChannel.LastChannel);
PushAudioInputStream pushStream = AudioInputStream.createPushStream();
AudioConfig audioInput = AudioConfig.fromStreamInput(pushStream, audioProcessingOptions);

SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, audioInput);
```
---
