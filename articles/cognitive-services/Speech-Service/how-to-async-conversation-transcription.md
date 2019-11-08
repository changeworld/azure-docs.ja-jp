---
title: 非同期での会話の文字起こし (プレビュー) - Speech Service
titleSuffix: Azure Cognitive Services
description: Speech Service で非同期での会話の文字起こしを使う方法について説明します。 Java でのみ使用できます。
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: amishu
ms.openlocfilehash: 433ea2778d99de39991565b2529e2f8eab4e13df
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2019
ms.locfileid: "73506494"
---
# <a name="asynchronous-conversation-transcription-preview"></a>非同期での会話の文字起こし (プレビュー)

この記事では、**RemoteConversationTranscriptionClient** API を使って、非同期での会話の文字起こしを行う方法を示します。 非同期で文字起こしを行うように会話の文字起こしを構成してあり、`conversationId` がある場合は、**RemoteConversationTranscriptionClient** API を使って、その `conversationId` に関連付けられている文字起こしを取得できます。

## <a name="asynchronous-vs-real-time--asynchronous"></a>非同期またはリアルタイムと非同期

非同期での文字起こしでは、会話のオーディオをストリーミングしますが、文字起こしをリアルタイムで受け取る必要はありません。 代わりに、オーディオを送信した後、`ConversationTranscriber` の `conversationId` を使って、非同期での文字起こしの状態を照会します。 非同期での文字起こしの準備が完了していると、`RemoteConversationTranscriptionResult` を受け取ります。

リアルタイムと非同期では、リアルタイムで文字起こしを受け取りますが、`conversationId` で照会して文字起こしを取得することもできます (非同期のシナリオと同様)。

非同期での文字起こしを実行するには、2 つのステップが必要です。 最初のステップでは、非同期のみまたはリアルタイムと非同期のどちらかを選択して、オーディオをアップロードします。 2 番目のステップでは、文字起こしの結果を取得します。

## <a name="upload-the-audio"></a>オーディオをアップロードする

非同期での文字起こしを実行する前に、Microsoft Cognitive Speech クライアント SDK (バージョン1.8.0 以降) を使って、会話の文字起こしにオーディオを送信する必要があります。

このコード例では、非同期モード専用に会話の文字起こし機能を作成する方法を示します。 オーディオを文字起こし機能にストリーミングするには、[Speech SDK を使ってリアルタイムで会話の文字起こしを行う方法](how-to-use-conversation-transcription-service.md)に関する記事で作成したオーディオ ストリーミング コードを追加する必要があります。 そのトピックの**制限事項**に関するセクションを参照し、サポートされているプラットフォームと言語 API を確認してください。

```java
// Create the speech config object
// Substitute real information for "YourSubscriptionKey" and "Region"
SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "Region");
speech_config.setProperty("ConversationTranscriptionInRoomAndOnline", "true");

// Set the property for asynchronous transcription
speechConfig.setServiceProperty("transcriptionMode", "Async", ServicePropertyChannel.UriQueryParameter);

// Set the property for real-time plus asynchronous transcription
//speechConfig.setServiceProperty("transcriptionMode", "RealTimeAndAsync", ServicePropertyChannel.UriQueryParameter);

// Do rest of the things as explained in how to use Conversation Transcription

// pick a conversation Id that is a GUID.
Conversation conversation = new Conversation(speechConfig, conversationId);

// Create a conversation transcriber
ConversationTranscriber transcriber = new ConversationTranscriber(AudioConfig.fromDefaultMicrophoneInput());

// join a conversation
transcriber.joinConversationAsync(conversation).get();

// stream audio as shown in “Transcribe conversations in real time”
...
```

リアルタイム "_かつ_" 非同期で行う場合は、次のように、適切なコード行をコメント化およびコメント解除します。

```java
// Set the property for asynchronous transcription
//speechConfig.setServiceProperty("transcriptionMode", "Async", ServicePropertyChannel.UriQueryParameter);

// Set the property for real-time plus asynchronous transcription
speechConfig.setServiceProperty("transcriptionMode", "RealTimeAndAsync", ServicePropertyChannel.UriQueryParameter);
```

## <a name="get-transcription-results"></a>文字起こしの結果を取得する

このステップでは、非同期での文字起こしの結果を取得しますが、リアルタイムの処理が必要な場合は、他の場所で行っているものとします。 詳細については、[Speech SDK を使ってリアルタイムで会話の文字起こしを行う方法](how-to-use-conversation-transcription-service.md)に関する記事を参照してください。

ここで示すコードで必要な **remoteconversation-client-sdk バージョン 1.0.0** は、Windows、Linux、Android (API レベル 26 以上) での Java (1.8 以降) に対してのみサポートされています。

### <a name="obtaining-the-client-sdk"></a>クライアント SDK を取得する

次のように pom.xml ファイルを編集することによって、**remoteconversation-client-sdk** を取得できます。

- ファイルの最後にある、終了タグ `</project>` の前に、Speech SDK 用の Maven リポジトリへの参照を含む `repositories` 要素を作成します。

  ```xml
  <repositories>
    <repository>
      <id>maven-cognitiveservices-speech</id>
      <name>Microsoft Cognitive Services Speech Maven Repository</name>
      <url>https://csspeechstorage.blob.core.windows.net/maven/</url>
    </repository>
  </repositories>
  ```

- また、依存関係として remoteconversation-client-sdk 1.0.0 を指定して、`dependencies` 要素を追加します。

  ```xml
  <dependencies>
    <dependency>
      <groupId>com.microsoft.cognitiveservices.speech.remoteconversation</groupId>
      <artifactId>remoteconversation-client-sdk</artifactId>
      <version>1.0.0</version>
    </dependency>
  </dependencies>
  ```

- 変更を保存します

### <a name="sample-transcription-code"></a>サンプルの文字起こしコード

`conversationId` を取得した後、クライアントでリモート操作オブジェクト **RemoteConversationTranscriptionOperation** を作成して、非同期での文字起こしの状態を照会します。 **RemoteConversationTranscriptionOperation** は、[ポーラー](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/core/azure-core/src/main/java/com/azure/core/util/polling/Poller.java)から拡張されています。 ポーラーが完了したら、ポーラーをサブスクライブし、オブジェクトを照会することにより、**RemoteConversationTranscriptionResult** を取得します。 このコードでは、結果の内容をシステム出力に単に出力します。

```java
// Create the speech config object
SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "Region");

// Create a remote Conversation Transcription client
RemoteConversationTranscriptionClient client = new RemoteConversationTranscriptionClient(speechConfig);

// Create a remote Conversation Transcription operation
RemoteConversationTranscriptionOperation operation = new RemoteConversationTranscriptionOperation(conversationId, client);

// Operation identifier now be the value of `conversationId`
System.out.println("Operation Identifier:" + operation.getId());

// Get the observer (which is a Flux) and subscribe to it for the response
operation.getObserver()
                .subscribe(
                        pollResponse -> {
                            System.out.println("Poll response status : " + pollResponse.getStatus());
                            if(pollResponse.getValue().getConversationTranscriptionResults() != null) {
                                for (int i = 0; i < pollResponse.getValue().getConversationTranscriptionResults().size(); i++) {
                                    ConversationTranscriptionResult result = pollResponse.getValue().getConversationTranscriptionResults().get(i);
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
                );
// Block on the operation till it is finished
operation.block();

System.out.println("Operation finished");
```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [GitHub でサンプルを詳しく見てみる](https://aka.ms/csspeech/samples)
