---
title: Azure Cognitive Services、Microsoft Translator Speech API の C# のクイック スタート | Microsoft Docs
description: Azure 上の Microsoft Cognitive Services で Microsoft Translator Speech API の使用をすぐに開始するために役立つ情報とコード サンプルを提供します。
services: cognitive-services
documentationcenter: ''
author: v-jaswel
ms.service: cognitive-services
ms.component: translator-speech
ms.topic: article
ms.date: 3/5/2018
ms.author: v-jaswel
ms.openlocfilehash: 0a6d3775238512cacc21fb3e47f395e37c3fd29b
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/10/2018
ms.locfileid: "41936739"
---
# <a name="quickstart-for-microsoft-translator-speech-api-with-c"></a>C# での Microsoft Translator Speech API のクイック スタート 
<a name="HOLTop"></a>

この記事では、Microsoft Translator Speech API を使用して、.wav ファイルで話されている言葉を翻訳する方法について説明します。

## <a name="prerequisites"></a>前提条件

このコードを Windows 上で実行するには、[Visual Studio 2017](https://www.visualstudio.com/downloads/) が必要です  (無料の Community Edition でかまいません)。

下記のコードからコンパイルする実行可能ファイルと同じフォルダーに、"speak.wav" という名前の .wav ファイルを置く必要があります。 この .wav ファイルは、標準の PCM (16 ビット、16 kHz、モノラル形式) である必要があります。 このような .wav ファイルは、[Text to Speech API](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/rest-apis#text-to-speech) から取得できます。

[Cognitive Services API アカウント](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)と **Microsoft Translator Speech API** を用意している必要があります。 [Azure ダッシュボード](https://portal.azure.com/#create/Microsoft.CognitiveServices)の有料サブスクリプション キーが必要です。

## <a name="translate-speech"></a>音声を翻訳する

次のコードは、音声の言語を別の言語に翻訳するものです。

1. 普段使用している IDE で新しい C# プロジェクトを作成します。
2. 次に示すコードを追加します。
3. `key` の値を、お使いのサブスクリプションで有効なアクセス キーに置き換えます。
4. プログラムを実行します。

```csharp
using System;
using System.IO;
using System.Net.WebSockets;
using System.Text;
using System.Threading;
using System.Threading.Tasks;

namespace TranslateSpeechQuickStart
{
    class Program
    {
        static string host = "wss://dev.microsofttranslator.com";
        static string path = "/speech/translate";

        // NOTE: Replace this example key with a valid subscription key.
        static string key = "ENTER KEY HERE";

        async static Task Send (ClientWebSocket client, string input_path)
        {
            var audio = File.ReadAllBytes(input_path);
            var audio_out_buffer = new ArraySegment<byte>(audio);
            Console.WriteLine("Sending audio.");
            await client.SendAsync(audio_out_buffer, WebSocketMessageType.Binary, true, CancellationToken.None);

            /* Make sure the audio file is followed by silence.
             * This lets the service know that the audio input is finished. */
            var silence = new byte[3200000];
            var silence_buffer = new ArraySegment<byte>(silence);
            await client.SendAsync(silence_buffer, WebSocketMessageType.Binary, true, CancellationToken.None);

            Console.WriteLine("Done sending.");
            await client.CloseAsync(WebSocketCloseStatus.NormalClosure, "", CancellationToken.None);
        }

        async static Task Receive(ClientWebSocket client, string output_path)
        {
            var inbuf = new byte[102400];
            var segment = new ArraySegment<byte>(inbuf);
            var stream = new FileStream(output_path, FileMode.Create);

            Console.WriteLine("Awaiting response.");
            while (client.State == WebSocketState.Open)
            {
                var result = await client.ReceiveAsync(segment, CancellationToken.None);
                switch (result.MessageType)
                {
                    case WebSocketMessageType.Close:
                        Console.WriteLine("Received close message. Status: " + result.CloseStatus + ". Description: " + result.CloseStatusDescription);
                        await client.CloseAsync(WebSocketCloseStatus.NormalClosure, string.Empty, CancellationToken.None);
                        break;
                    case WebSocketMessageType.Text:
                        Console.WriteLine("Received text.");
                        Console.WriteLine(Encoding.UTF8.GetString(inbuf).TrimEnd('\0'));
                        break;
                    case WebSocketMessageType.Binary:
                        Console.WriteLine("Received binary data: " + result.Count + " bytes.");
                        stream.Write(inbuf, 0, result.Count);
                        break;
                }
            }

            stream.Close();
            stream.Dispose();
        }

        async static void TranslateSpeech()
        {
            var client = new ClientWebSocket();
            client.Options.SetRequestHeader ("Ocp-Apim-Subscription-Key", key);

            string from = "en-US";
            string to = "it-IT";
            string features = "texttospeech";
            string voice = "it-IT-Elsa";
            string api = "1.0";

            string input_path = "speak.wav";
            string output_path = "speak2.wav";

            string uri = host + path +
                "?from=" + from +
                "&to=" + to +
                "&api-version=" + api +
                "&features=" + features +
                "&voice=" + voice;

            Console.WriteLine("uri: " + uri);
            Console.WriteLine("Opening connection.");
            await client.ConnectAsync(new Uri(uri), CancellationToken.None);
            Console.WriteLine("Connection open.");
            Task.WhenAll(Send(client, input_path), Receive(client, output_path)).Wait();
        }

        static void Main()
        {
            TranslateSpeech();
            Console.ReadLine();
        }

    }
}
```

**音声翻訳の結果**

正常に実行されると、"speak2.wav" という名前のファイルが作成されます。 このファイルには、"speak.wav" で話されてる言葉の翻訳が含まれています。

[先頭に戻る](#HOLTop)

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Translator Speech のチュートリアル](../tutorial-translator-speech-csharp.md)

## <a name="see-also"></a>関連項目 

[Translator Speech の概要](../overview.md)
[API リファレンス](https://docs.microsoft.com/azure/cognitive-services/translator-speech/reference)
