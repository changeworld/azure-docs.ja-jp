---
author: amitkumarshukla
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: amishu
ms.custom: devx-track-csharp
ms.openlocfilehash: 0ab175d122da379e0b0d45465837cdebeb8ec8bb
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2021
ms.locfileid: "106450359"
---
## <a name="upload-the-audio"></a>オーディオをアップロードする

非同期での文字起こしの最初の手順は、Speech SDK (バージョン 1.13.0 以上) を使用して音声を会話の文字起こしサービスに送信することです。

このコード例では、非同期モード専用に `ConversationTranscriber` を作成する方法を示します。 オーディオを文字起こし機能にストリーミングするには、[Speech SDK を使ってリアルタイムで会話の文字起こしを行う方法](../../../../how-to-use-conversation-transcription.md)に関する記事で作成したオーディオ ストリーミング コードを追加します。 

```csharp
async Task CompleteContinuousRecognition(ConversationTranscriber recognizer, string conversationId)
{
    var finishedTaskCompletionSource = new TaskCompletionSource<int>();

    recognizer.SessionStopped += (s, e) =>
    {
        finishedTaskCompletionSource.TrySetResult(0);
    };
    string canceled = string.Empty;

    recognizer.Canceled += (s, e) => {
        canceled = e.ErrorDetails;
        if (e.Reason == CancellationReason.Error)
        {
            finishedTaskCompletionSource.TrySetResult(0);
        }
    };

    await recognizer.StartTranscribingAsync().ConfigureAwait(false);
    await Task.WhenAny(finishedTaskCompletionSource.Task, Task.Delay(TimeSpan.FromSeconds(10)));
    await recognizer.StopTranscribingAsync().ConfigureAwait(false);
}

async Task<List<string>> GetRecognizerResult(ConversationTranscriber recognizer, string conversationId)
{
    List<string> recognizedText = new List<string>();
    recognizer.Transcribed += (s, e) =>
    {
        if (e.Result.Text.Length > 0)
        {
            recognizedText.Add(e.Result.Text);
        }
    };

    await CompleteContinuousRecognition(recognizer, conversationId);

    recognizer.Dispose();
    return recognizedText;
}

async Task UploadAudio()
{
    // Create the speech config object
    // Substitute real information for "YourSubscriptionKey" and "Region"
    SpeechConfig speechConfig = SpeechConfig.FromSubscription("YourSubscriptionKey", "Region");
    speechConfig.SetProperty("ConversationTranscriptionInRoomAndOnline", "true");

    // Set the property for asynchronous transcription
    speechConfig.SetServiceProperty("transcriptionMode", "async", ServicePropertyChannel.UriQueryParameter);

    // Alternatively: set the property for real-time plus asynchronous transcription
    // speechConfig.setServiceProperty("transcriptionMode", "RealTimeAndAsync", ServicePropertyChannel.UriQueryParameter);

    // Create an audio stream from a wav file or from the default microphone if you want to stream live audio from the supported devices
    // Replace with your own audio file name and Helper class which implements AudioConfig using PullAudioInputStreamCallback
    PullAudioInputStreamCallback wavfilePullStreamCallback = Helper.OpenWavFile("16kHz16Bits8channelsOfRecordedPCMAudio.wav");
    // Create an audio stream format assuming the file used above is 16kHz, 16 bits and 8 channel pcm wav file
    AudioStreamFormat audioStreamFormat = AudioStreamFormat.GetWaveFormatPCM(16000, 16, 8);
    // Create an input stream
    AudioInputStream audioStream = AudioInputStream.CreatePullStream(wavfilePullStreamCallback, audioStreamFormat);

    // Ensure the conversationId for a new conversation is a truly unique GUID
    String conversationId = Guid.NewGuid().ToString();

    // Create a Conversation
    using (var conversation = await Conversation.CreateConversationAsync(speechConfig, conversationId))
    {
        using (var conversationTranscriber = new ConversationTranscriber(AudioConfig.FromStreamInput(audioStream)))
        {
            await conversationTranscriber.JoinConversationAsync(conversation);
            // Helper function to get the real time transcription results
            var result = await GetRecognizerResult(conversationTranscriber, conversationId);
        }
    }
}
```

リアルタイム "_かつ_" 非同期で行う場合は、次のように、適切なコード行をコメント化およびコメント解除します。

```csharp
// Set the property for asynchronous transcription
// speechConfig.SetServiceProperty("transcriptionMode", "async", ServicePropertyChannel.UriQueryParameter);

// Alternatively: set the property for real-time plus asynchronous transcription
speechConfig.SetServiceProperty("transcriptionMode", "RealTimeAndAsync", ServicePropertyChannel.UriQueryParameter);
```

## <a name="get-transcription-results"></a>文字起こしの結果を取得する

NuGet を使用して **Microsoft.CognitiveServices.Speech.Remoteconversation バージョン 1.13.0 以降** をインストールします。

### <a name="sample-transcription-code"></a>サンプルの文字起こしコード

`conversationId` を取得した後、クライアント アプリケーションでリモート会話の文字起こしクライアント **RemoteConversationTranscriptionClient** を作成して、非同期での文字起こしの状態を照会します。 **RemoteConversationTranscriptionOperation** のオブジェクトを作成して、実行時間の長い [Operation](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/core/Azure.Core#consuming-long-running-operations-using-operationt) オブジェクトを取得します。 操作の状態を確認することも、完了するまで待機することもできます。 

```csharp
// Create the speech config
SpeechConfig config = SpeechConfig.FromSubscription("YourSpeechKey", "YourSpeechRegion");
// Create the speech client
RemoteConversationTranscriptionClient client = new RemoteConversationTranscriptionClient(config);
// Create the remote operation
RemoteConversationTranscriptionOperation operation = 
                            new RemoteConversationTranscriptionOperation(conversationId, client);

// Wait for operation to finish
await operation.WaitForCompletionAsync(TimeSpan.FromSeconds(10), CancellationToken.None);
// Get the result of the long running operation
var val = operation.Value.ConversationTranscriptionResults;
// Print the fields from the results
foreach (var item in val)
{
    Console.WriteLine($"{item.Text}, {item.ResultId}, {item.Reason}, {item.UserId}, {item.OffsetInTicks}, {item.Duration}");
    Console.WriteLine($"{item.Properties.GetProperty(PropertyId.SpeechServiceResponse_JsonResult)}");
}
Console.WriteLine("Operation completed");

```
