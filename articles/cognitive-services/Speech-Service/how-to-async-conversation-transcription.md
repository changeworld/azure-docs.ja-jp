---
title: 非同期での会話の文字起こし (プレビュー) - 音声サービス
titleSuffix: Azure Cognitive Services
description: 音声サービスを使って非同期での会話の文字起こしを使用する方法について説明します。 Java でのみ使用できます。
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: amishu
ms.openlocfilehash: 57543f4a3779145ce66259eec1abac195b63c7ba
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2020
ms.locfileid: "80384298"
---
# <a name="asynchronous-conversation-transcription-preview"></a>非同期での会話の文字起こし (プレビュー)

この記事では、**RemoteConversationTranscriptionClient** API を使って、非同期での会話の文字起こしを行う方法を示します。 非同期で文字起こしを行うように会話の文字起こしを構成してあり、`conversationId` がある場合は、**RemoteConversationTranscriptionClient** API を使って、その `conversationId` に関連付けられている文字起こしを取得できます。

## <a name="asynchronous-vs-real-time--asynchronous"></a>非同期またはリアルタイムと非同期

非同期での文字起こしでは、会話のオーディオをストリーミングしますが、文字起こしをリアルタイムで受け取る必要はありません。 代わりに、オーディオを送信した後、`Conversation` の `conversationId` を使って、非同期での文字起こしの状態を照会します。 非同期での文字起こしの準備が完了していると、`RemoteConversationTranscriptionResult` を受け取ります。

リアルタイムと非同期では、リアルタイムで文字起こしを受け取りますが、`conversationId` で照会して文字起こしを取得することもできます (非同期のシナリオと同様)。

非同期での文字起こしを実行するには、2 つのステップが必要です。 最初のステップでは、非同期のみまたはリアルタイムと非同期のどちらかを選択して、オーディオをアップロードします。 2 番目のステップでは、文字起こしの結果を取得します。

## <a name="upload-the-audio"></a>オーディオをアップロードする

非同期での文字起こしを実行する前に、Microsoft Cognitive Speech クライアント SDK (バージョン1.8.0 以降) を使って、会話の文字起こしサービスにオーディオを送信する必要があります。

このコード例では、非同期モード専用に会話の文字起こし機能を作成する方法を示します。 オーディオを文字起こし機能にストリーミングするには、[Speech SDK を使ってリアルタイムで会話の文字起こしを行う方法](how-to-use-conversation-transcription-service.md)に関する記事で作成したオーディオ ストリーミング コードを追加する必要があります。 そのトピックの**制限事項**に関するセクションを参照し、サポートされているプラットフォームと言語 API を確認してください。

```java
// Create the speech config object
// Substitute real information for "YourSubscriptionKey" and "Region"
SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "Region");
speechConfig.setProperty("ConversationTranscriptionInRoomAndOnline", "true");

// Set the property for asynchronous transcription
speechConfig.setServiceProperty("transcriptionMode", "async", ServicePropertyChannel.UriQueryParameter);

// Set the property for real-time plus asynchronous transcription
//speechConfig.setServiceProperty("transcriptionMode", "RealTimeAndAsync", ServicePropertyChannel.UriQueryParameter);

// pick a conversation Id that is a GUID.
String conversationId = UUID.randomUUID().toString();

// Create a Conversation
Future<Conversation> conversationFuture = Conversation.createConversationAsync(speechConfig, conversationId);
Conversation conversation = conversationFuture.get();

// Create an audio stream from a wav file or from the default microphone if you want to stream live audio from the supported devices
// Replace with your own audio file name and Helper class which implements AudioConfig using PullAudioInputStreamCallback
PullAudioInputStreamCallback wavfilePullStreamCallback = Helper.OpenWavFile("16kHz16Bits8channelsOfRecordedPCMAudio.wav");
// Create an audio stream format assuming the file used above is 16kHz, 16 bits and 8 channel pcm wav file
AudioStreamFormat audioStreamFormat = AudioStreamFormat.getWaveFormatPCM((long)16000, (short)16,(short)8);
// Create an input stream
AudioInputStream audioStream = AudioInputStream.createPullStream(wavfilePullStreamCallback, audioStreamFormat);

// Create a conversation transcriber
ConversationTranscriber transcriber = new ConversationTranscriber(AudioConfig.fromStreamInput(audioStream));

// join a conversation
transcriber.joinConversationAsync(conversation);

// Add the event listener for the realtime events
transcriber.transcribed.addEventListener((o, e) -> {
    System.out.println("Conversation transcriber Recognized:" + e.toString());
});

transcriber.canceled.addEventListener((o, e) -> {
    System.out.println("Conversation transcriber canceled:" + e.toString());
    try {
        transcriber.stopTranscribingAsync().get();
    } catch (InterruptedException ex) {
        ex.printStackTrace();
    } catch (ExecutionException ex) {
        ex.printStackTrace();
    }
});

transcriber.sessionStopped.addEventListener((o, e) -> {
    System.out.println("Conversation transcriber stopped:" + e.toString());

    try {
        transcriber.stopTranscribingAsync().get();
    } catch (InterruptedException ex) {
        ex.printStackTrace();
    } catch (ExecutionException ex) {
        ex.printStackTrace();
    }
});

// start the transcription.
Future<?> future = transcriber.startTranscribingAsync();
...
```

リアルタイム "_かつ_" 非同期で行う場合は、次のように、適切なコード行をコメント化およびコメント解除します。

```java
// Set the property for asynchronous transcription
//speechConfig.setServiceProperty("transcriptionMode", "async", ServicePropertyChannel.UriQueryParameter);

// Set the property for real-time plus asynchronous transcription
speechConfig.setServiceProperty("transcriptionMode", "RealTimeAndAsync", ServicePropertyChannel.UriQueryParameter);
```

## <a name="get-transcription-results"></a>文字起こしの結果を取得する

このステップでは、非同期での文字起こしの結果を取得しますが、リアルタイムの処理が必要な場合は、他の場所で行っているものとします。 詳細については、[Speech SDK を使ってリアルタイムで会話の文字起こしを行う方法](how-to-use-conversation-transcription-service.md)に関する記事を参照してください。

ここで示すコードで必要な **remote-conversation バージョン 1.8.0** は、Windows、Linux、Android (API レベル 26 以上のみ) での Java (1.8.0 以降) に対してのみサポートされています。

### <a name="obtaining-the-client-sdk"></a>クライアント SDK を取得する

次のように pom.xml ファイルを編集することによって、**remote-conversation** を取得できます。

1. ファイルの最後にある、終了タグ `</project>` の前に、Speech SDK 用の Maven リポジトリへの参照を含む `repositories` 要素を作成します。

   ```xml
   <repositories>
     <repository>
       <id>maven-cognitiveservices-speech</id>
       <name>Microsoft Cognitive Services Speech Maven Repository</name>
       <url>https://csspeechstorage.blob.core.windows.net/maven/</url>
     </repository>
   </repositories>
   ```

2. また、依存関係として remoteconversation-client-sdk 1.8.0 を指定して、`dependencies` 要素を追加します。

   ```xml
   <dependencies>
     <dependency>
       <groupId>com.microsoft.cognitiveservices.speech.remoteconversation</groupId>
       <artifactId>remote-conversation</artifactId>
       <version>1.8.0</version>
     </dependency>
   </dependencies>
   ```

3. 変更を保存します

### <a name="sample-transcription-code"></a>サンプルの文字起こしコード

`conversationId` を取得した後、クライアント アプリケーションでリモート会話の文字起こしクライアント **RemoteConversationTranscriptionClient** を作成して、非同期での文字起こしの状態を照会します。 **RemoteConversationTranscriptionClient** の **getTranscriptionOperation** メソッドを使用して、[PollerFlux](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/core/azure-core/src/main/java/com/azure/core/util/polling/PollerFlux.java) オブジェクトを取得します。 PollerFlux オブジェクトには、リモート操作の状態 **RemoteConversationTranscriptionOperation** に関する情報と最終的な結果 **RemoteConversationTranscriptionResult** が格納されます。 操作が完了したら、[SyncPoller](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/core/azure-core/src/main/java/com/azure/core/util/polling/SyncPoller.java) の **getFinalResult** を呼び出して、**RemoteConversationTranscriptionResult** を取得します。 このコードでは、結果の内容をシステム出力に単に出力します。

```java
// Create the speech config object
SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "Region");

// Create a remote Conversation Transcription client
RemoteConversationTranscriptionClient client = new RemoteConversationTranscriptionClient(speechConfig);

// Get the PollerFlux for the remote operation
PollerFlux<RemoteConversationTranscriptionOperation, RemoteConversationTranscriptionResult> remoteTranscriptionOperation = client.getTranscriptionOperation(conversationId);

// Subscribe to PollerFlux to get the remote operation status
remoteTranscriptionOperation.subscribe(
        pollResponse -> {
            System.out.println("Poll response status : " + pollResponse.getStatus());
            System.out.println("Poll response status : " + pollResponse.getValue().getServiceStatus());
        }
);

// Obtain the blocking operation using getSyncPoller
SyncPoller<RemoteConversationTranscriptionOperation, RemoteConversationTranscriptionResult> blockingOperation =  remoteTranscriptionOperation.getSyncPoller();

// Wait for the operation to finish
blockingOperation.waitForCompletion();

// Get the final result response
RemoteConversationTranscriptionResult resultResponse = blockingOperation.getFinalResult();

// Print the result
if(resultResponse != null) {
    if(resultResponse.getConversationTranscriptionResults() != null) {
        for (int i = 0; i < resultResponse.getConversationTranscriptionResults().size(); i++) {
            ConversationTranscriptionResult result = resultResponse.getConversationTranscriptionResults().get(i);
            System.out.println(result.getProperties().getProperty(PropertyId.SpeechServiceResponse_JsonResult.name()));
            System.out.println(result.getProperties().getProperty(PropertyId.SpeechServiceResponse_JsonResult));
            System.out.println(result.getOffset());
            System.out.println(result.getDuration());
            System.out.println(result.getUserId());
            System.out.println(result.getReason());
            System.out.println(result.getResultId());
            System.out.println(result.getText());
            System.out.println(result.toString());
        }
    }
}

System.out.println("Operation finished");
```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [GitHub でサンプルを詳しく見てみる](https://aka.ms/csspeech/samples)
