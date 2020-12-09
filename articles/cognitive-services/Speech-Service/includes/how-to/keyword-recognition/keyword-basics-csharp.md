---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 11/03/2020
ms.author: trbye
ms.openlocfilehash: d71a7f6451cda5e2e50b5410140ac88361bf1735
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/02/2020
ms.locfileid: "96509468"
---
まず、`KeywordRecognitionModel` を返す `FromFile()` 静的関数を使用して、キーワード モデル ファイルを読み込みます。 Speech Studio からダウンロードした `.table` ファイルへのパスを使用します。 また、既定のマイクを使用して `AudioConfig` を作成し、オーディオ構成を使用して新しい `KeywordRecognizer` をインスタンス化します。

```csharp
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

var keywordModel = KeywordRecognitionModel.FromFile("your/path/to/Activate_device.table");
using var audioConfig = AudioConfig.FromDefaultMicrophoneInput();
using var keywordRecognizer = new KeywordRecognizer(audioConfig);
```

次に、モデル オブジェクトを渡すことによって、`RecognizeOnceAsync()` を 1 回呼び出すだけでキーワード認識が実行されます。 これにより、キーワードが認識されるまで継続されるキーワード認識セッションが開始されます。 そのため、このデザイン パターンは、一般的にはマルチスレッド アプリケーションや、ウェイクワードを無期限に待ち続けるような場合に使用します。

```csharp
KeywordRecognitionResult result = await keywordRecognizer.RecognizeOnceAsync(keywordModel);
```

> [!NOTE]
> ここで示す例では、ローカル キーワード認識を使用しています。これは、認証コンテキストに `SpeechConfig` オブジェクトを必要とせず、バックエンドに接続しないためです。 ただし、[継続的なバックエンド接続を利用](../../../tutorial-voice-enable-your-bot-speech-sdk.md#view-the-source-code-that-enables-keyword)することで、キーワード認識と検証の両方を実行することができます。