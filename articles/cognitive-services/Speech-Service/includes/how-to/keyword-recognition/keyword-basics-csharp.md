---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 11/03/2020
ms.author: trbye
ms.openlocfilehash: 040ffea69f76255dcb1bfc6787cad45a95baa904
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2020
ms.locfileid: "93305907"
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
> ここで示す例では、ローカル キーワード認識を使用しています。これは、認証コンテキストに `SpeechConfig` オブジェクトを必要とせず、バックエンドに接続しないためです。 ただし、[継続的なバックエンド接続を利用](https://docs.microsoft.com/azure/cognitive-services/speech-service/tutorial-voice-enable-your-bot-speech-sdk#view-the-source-code-that-enables-keyword)することで、キーワード認識と検証の両方を実行することができます。