---
author: eric-urban
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: eur
ms.custom: devx-track-csharp
ms.openlocfilehash: 811e4f0463579945f24ac9e4742b48afb471de07
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131520504"
---
Speech SDK で圧縮されたオーディオ入力を受け入れるように構成するには、`PullAudioInputStream` または `PushAudioInputStream` を作成します。 次に、ストリームの圧縮形式を指定して、ストリーム クラスのインスタンスから `AudioConfig` を作成します。 関連するサンプル コード スニペットについては、「[Speech SDK のオーディオ入力ストリーム API について](../../../../how-to-use-audio-input-streams.md)」を参照してください。

`pushStream` という入力ストリーム クラスがあり、OPUS/OGG を使用しているものと想定します。 その場合のコードは次のようになります。

```csharp
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

// ... omitted for brevity

var speechConfig =
    SpeechConfig.FromSubscription(
        "YourSubscriptionKey",
        "YourServiceRegion");

// Create an audio config specifying the compressed
// audio format and the instance of your input stream class.
var audioFormat =
    AudioStreamFormat.GetCompressedFormat(
        AudioStreamContainerFormat.OGG_OPUS);
var audioConfig =
    AudioConfig.FromStreamInput(
        pushStream,
        audioFormat);

using var recognizer = new SpeechRecognizer(speechConfig, audioConfig);
var result = await recognizer.RecognizeOnceAsync();

var text = result.Text;
```
