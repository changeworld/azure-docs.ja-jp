---
title: クイック スタート:音声をオーディオ ファイルに合成する、C# (.NET) - Speech サービス
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/28/2019
ms.author: erhopf
ms.openlocfilehash: dfe2d5925e19697a2e3ed4491958da1325df5574
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/25/2019
ms.locfileid: "75468798"
---
## <a name="prerequisites"></a>前提条件

開始する前に、必ず次のことを行ってください。

> [!div class="checklist"]
> * [Azure Speech リソースを作成する](../../../../get-started.md)
> * [使用する開発環境を設定する](../../../../quickstarts/setup-platform.md?tabs=dotnet)
> * [空のサンプル プロジェクトを作成する](../../../../quickstarts/create-project.md?tabs=dotnet)

## <a name="open-your-project-in-visual-studio"></a>Visual Studio でプロジェクトを開きます。

最初の手順として、ご利用のプロジェクトを Visual Studio で開いていることを確認します。

1. Visual Studio 2019 を起動します。
2. プロジェクトを読み込んで `Program.cs` を開きます。

## <a name="start-with-some-boilerplate-code"></a>定型コードを使用して開始する

このプロジェクトのスケルトンとして機能するコードを追加しましょう。 `SynthesisToAudioFileAsync()` という非同期メソッドを作成済みであることを確認します。

````C#

using System;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;

namespace helloworld
{
    class Program
    {
        public static async Task SynthesisToAudioFileAsync()
        {
        }

        static void Main()
        {
            SynthesisToAudioFileAsync().Wait();
        }
    }
}

````

## <a name="create-a-speech-configuration"></a>Speech 構成を作成する

`SpeechSynthesizer` オブジェクトを初期化するには、サブスクリプション キーとサブスクリプション リージョンを使用する構成を作成する必要があります。 このコードを `SynthesisToAudioFileAsync()` メソッドに挿入します。

````C#
var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
````

## <a name="create-an-audio-configuration"></a>オーディオ構成を作成する

ここで、使用するオーディオ ファイルを指し示す ````AudioConfig```` オブジェクトを作成する必要があります。 アンマネージド リソースが確実に正しく解放されるように、このオブジェクトは using ステートメント内に作成されます。 このコードを Speech 構成のすぐ下にある `SynthesisToAudioFileAsync()` メソッドに挿入します。

````C#
var fileName = "helloworld.wav";
using (var fileOutput = AudioConfig.FromWavFileOutput(fileName))
{
}
````

## <a name="initialize-a-speechsynthesizer"></a>SpeechSynthesizer を初期化する

ここで、前に作成した `SpeechConfig` オブジェクトと `AudioConfig` オブジェクトを使用して `SpeechSynthesizer` オブジェクトを作成しましょう。 アンマネージド リソースが確実に正しく解放されるように、このオブジェクトは using ステートメント内にも作成されます。 ````AudioConfig```` オブジェクトをラップする using ステートメント内の `SynthesisToAudioFileAsync()` メソッドに、このコードを挿入します。

````C#
using (var synthesizer = new SpeechSynthesizer(config, fileOutput))
{
}
````

## <a name="synthesize-text-using-speaktextasync"></a>SpeakTextAsync を使用してテキストを合成する

`SpeechSynthesizer` オブジェクトから、`SpeakTextAsync()` メソッドを呼び出します。 このメソッドは、音声に変換するテキストを Speech サービスに送信します。 `config.VoiceName` が明示的に指定されていない場合、`SpeechSynthesizer` は既定の音声を使用します。

using ステートメント内に、このコードを追加します。
````C#
var text = "Hello world!";
var result = await synthesizer.SpeakTextAsync(text);
````

## <a name="check-for-errors"></a>エラーを確認する

合成結果がSpeech Service によって返されるときに、テキストが正常に合成されたことを確認するためのチェックを行う必要があります。

using ステートメント内の `SpeakTextAsync()` の下に、このコードを追加します。
````C#
if (result.Reason == ResultReason.SynthesizingAudioCompleted)
{
    Console.WriteLine($"Speech synthesized to [{fileName}] for text [{text}]");
}
else if (result.Reason == ResultReason.Canceled)
{
    var cancellation = SpeechSynthesisCancellationDetails.FromResult(result);
    Console.WriteLine($"CANCELED: Reason={cancellation.Reason}");

    if (cancellation.Reason == CancellationReason.Error)
    {
        Console.WriteLine($"CANCELED: ErrorCode={cancellation.ErrorCode}");
        Console.WriteLine($"CANCELED: ErrorDetails=[{cancellation.ErrorDetails}]");
        Console.WriteLine($"CANCELED: Did you update the subscription info?");
    }
}
````

## <a name="check-your-code"></a>コードを確認する

この時点で、コードは次のようになります。

````C#
//
// Copyright (c) Microsoft. All rights reserved.
// Licensed under the MIT license. See LICENSE.md file in the project root for full license information.
//

using System;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;

namespace helloworld
{
    class Program
    {
        public static async Task SynthesisToAudioFileAsync()
        {
            var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");

            var fileName = "helloworld.wav";
            using (var fileOutput = AudioConfig.FromWavFileOutput(fileName))
            {
                using (var synthesizer = new SpeechSynthesizer(config, fileOutput))
                {
                    var text = "Hello world!";
                    var result = await synthesizer.SpeakTextAsync(text);

                    if (result.Reason == ResultReason.SynthesizingAudioCompleted)
                    {
                        Console.WriteLine($"Speech synthesized to [{fileName}] for text [{text}]");
                    }
                    else if (result.Reason == ResultReason.Canceled)
                    {
                        var cancellation = SpeechSynthesisCancellationDetails.FromResult(result);
                        Console.WriteLine($"CANCELED: Reason={cancellation.Reason}");

                        if (cancellation.Reason == CancellationReason.Error)
                        {
                            Console.WriteLine($"CANCELED: ErrorCode={cancellation.ErrorCode}");
                            Console.WriteLine($"CANCELED: ErrorDetails=[{cancellation.ErrorDetails}]");
                            Console.WriteLine($"CANCELED: Did you update the subscription info?");
                        }
                    }
                }
            }
        }

        static void Main()
        {
            SynthesisToAudioFileAsync().Wait();
        }
    }
}
````

## <a name="build-and-run-your-app"></a>アプリをビルドして実行する

これで、アプリをビルドし、Speech Service を使用して音声認識をテストする準備ができました。

1. **コード をコンパイルする** - Visual Studio のメニュー バーから **[ビルド]**  >  **[ソリューションのビルド]** の順に選択します。
2. **アプリを起動する** - メニュー バーから **[デバッグ]**  >  **[デバッグの開始]** の順に選択するか、**F5** キーを押します。
3. **合成を開始する** - テキストは音声に変換され、指定されたオーディオ データで保存されます。

   ```text
   Speech synthesized to [helloworld.wav] for text [Hello world!]
   ```

## <a name="next-steps"></a>次のステップ

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>参照

- [カスタム音声を作成する](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [カスタム音声サンプルを録音する](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
