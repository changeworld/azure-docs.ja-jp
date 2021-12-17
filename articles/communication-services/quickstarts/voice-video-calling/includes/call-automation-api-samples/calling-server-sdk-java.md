---
title: インクルード ファイル
description: Java 呼び出しの自動化
services: azure-communication-services
author: ravithanneeru
manager: joseys
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 06/30/2021
ms.topic: include
ms.custom: include file
ms.author: joseys
ms.openlocfilehash: 7253be0e9d7723bdb018b53e3ee655188bb3d679
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121802762"
---
## <a name="prerequisites"></a>前提条件
開始する前に、必ず次のことを行ってください。
- アクティブなサブスクリプションがある Azure アカウントを作成します。 詳細については、[アカウントの無料作成](https://azure.microsoft.com/free/)に関するページを参照してください。
- [Java Development Kit (JDK)](/azure/developer/java/fundamentals/java-jdk-install) バージョン 11 以降。
- [Apache Maven](https://maven.apache.org/download.cgi)。
- Azure Communication Services リソースを作成します。 詳細については、[Azure Communication Services リソースの作成](../../../create-communication-resource.md)に関するページを参照してください。 このサンプルで使用するリソースの **接続文字列** を記録する必要があります。
- 新しい Azure Communication Services リソースの電話番号を取得します。 詳細については、[電話番号の取得](../../../telephony-sms/get-phone-number.md?pivots=platform-azp)に関するページを参照してください。
- [ngrok](https://www.ngrok.com/download) をダウンロードしてインストールします。 サンプルはローカルで実行されるため、ngrok によってすべてのイベントの受信が有効にされます。
- (省略可能) アプリケーションで再生されるカスタム メッセージを生成するための Azure Speech リソースを作成します。 [こちらのガイダンス](../../../../../cognitive-services/speech-service/overview.md#try-the-speech-service-for-free)に従ってリソースを作成します。

> [!NOTE]
> このクイックスタートの最終的なコードは [GitHub](https://github.com/Azure-Samples/communication-services-java-quickstarts/tree/main/OutboundCallReminder) にあります。 このサンプルでは、Microsoft Cognitive Services Speech SDK が使用されます。 Azure Cognitive Services Speech SDK をダウンロードすることで、その[ライセンス](https://aka.ms/csspeech/license201809)に同意したものと見なされます。

## <a name="add-the-package-references-for-the-calling-server-sdk"></a>呼び出し元サーバー SDK のパッケージ参照を追加する

POM ファイルで、呼び出し元 API を使用して `azure-communication-callingserver` パッケージを参照します。

```java
<dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-communication-callingserver</artifactId>
      <version>1.0.0-beta.2</version>
</dependency>
```

ユーザー ID を作成するには、アプリケーションで `azure-communication-identity` パッケージを参照する必要があります。

```java
  <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-communication-identity</artifactId>
      <version>1.1.1</version>
      <exclusions>
        <exclusion>
          <groupId>com.azure</groupId>
          <artifactId>azure-communication-common</artifactId>
        </exclusion>
      </exclusions>
    </dependency>
```

## <a name="object-model"></a>オブジェクト モデル

Java 用 Azure Communication Services Calling Server SDK が備える主な機能のいくつかは、次のクラスにより処理されます。

| 名前                                  | 説明                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| CallingServerClientBuilder | このクラスは、CallingServerClient のインスタンスを作成するために使用されます。|
| CallingServerClient | このクラスは通話機能に必要です。 CallingServerClientBuilder を介してインスタンスを取得し、それを使用して通話の開始/切断、オーディオの再生/キャンセル、参加者の追加/削除を行います |
| CommunicationIdentityClient | このクラスは、通信ユーザー ID を作成または削除するために必要です。 |

## <a name="create-a-call-client"></a>通話クライアントを作成する

Communication Services 接続文字列と httpClient オブジェクトは、それぞれ `connectionString()` および `httpClient()` 関数を介して `CallingServerClientBuilder` に渡す必要があります。 通話クライアント オブジェクトを作成するには、`CallingServerClientBuilder` オブジェクトの `buildClient()` 関数を使用します。

```java
NettyAsyncHttpClientBuilder httpClientBuilder = new NettyAsyncHttpClientBuilder();
CallingServerClientBuilder callClientBuilder = new CallingServerClientBuilder().httpClient(httpClientBuilder.build())
        .connectionString(this.callConfiguration.ConnectionString);

this.callingServerClient = callClientBuilder.buildClient();
```

## <a name="create-user-identity"></a>ユーザー ID を作成する

`CommunicationIdentityClientBuilder` オブジェクトを使用し、`connectionString()` 関数を介して Communication Services リソースの接続文字列を設定して `CommunicationIdentityClient` オブジェクトを作成し、`createUser` 関数を使用してソース ユーザー ID を作成します。

```java
CommunicationIdentityClient client = new CommunicationIdentityClientBuilder()
        .connectionString("<Connection_String>")
        .buildClient();
CommunicationUserIdentifier user = client.createUser();
```

## <a name="delete-user-identity"></a>ユーザー ID を削除する

`CommunicationIdentityClient` オブジェクトの `deleteUser` 関数を使用して、ユーザー ID を削除します。

```java
CommunicationIdentityClient client = new CommunicationIdentityClientBuilder()
        .connectionString("<Connection_String>")
        .buildClient();
client.deleteUser(new CommunicationUserIdentifier(source));    
```

## <a name="create-a-call"></a>通話を作成する

`CallingServerClient` オブジェクトの `createCallConnectionWithResponse` メソッドを使用して、通話を開始します。 `createCallConnectionWithResponse` メソッドでは、次のパラメーターがサポートされています。
- `source` は、`CommunicationUserIdentifier` 型の発信者のソース ユーザー ID です。
- `targets` は、`Iterable<CommunicationIdentifier>` 型のターゲットID の一覧です。
- `options` は、`CreateCallOptions` 型の通話のオプションです。 `CreateCallOptions` メソッドのパラメーターを次に示します。

    - `callbackUri` は、アプリケーションのコールバック URI です。
    - `requestedModalities` は、要求通話のモダリティです。 種類は `Iterable<MediaType>` です。 値は、Audio または Video です。
    - `requestedCallEvents` は、`Iterable<EventSubscriptionType>` 型の要求されたコールバック イベントです。 `EventSubscriptionType.PARTICIPANTS_UPDATED` および `EventSubscriptionType.DTMF_RECEIVED` イベントをサブスクライブするには、このパラメーターを使用します

- `CreateCallOptions` オブジェクトの `setAlternateCallerId()` 関数を使用して、ソースの呼び出し元 ID を代替の呼び出し元 ID として設定します。

```java
CommunicationUserIdentifier source = new CommunicationUserIdentifier(this.callConfiguration.SourceIdentity);
PhoneNumberIdentifier target = new PhoneNumberIdentifier(targetPhoneNumber);
List<MediaType> callModality = new ArrayList<>() { {add(MediaType.AUDIO);} };
List<EventSubscriptionType> eventSubscriptionType = new ArrayList<>() {
        {add(EventSubscriptionType.PARTICIPANTS_UPDATED); add(EventSubscriptionType.DTMF_RECEIVED);}};

CreateCallOptions createCallOption = new CreateCallOptions(this.callConfiguration.appCallbackUrl,
        callModality, eventSubscriptionType);
createCallOption.setAlternateCallerId(new PhoneNumberIdentifier(this.callConfiguration.sourcePhoneNumber));

Logger.logMessage(Logger.MessageType.INFORMATION,"Performing CreateCall operation");

List<CommunicationIdentifier> targets = new ArrayList<>() { {add(target);} };

Response<CallConnection> response = this.callingServerClient.createCallConnectionWithResponse(source, targets, createCallOption, null);
callConnection = response.getValue();
```

`createCallConnectionWithResponse` メソッドは `Response<CallConnection>` のオブジェクトを返します。これは、音声を再生する、音声を取り消す、電話を切るなどの他の通話操作に使用できます。

## <a name="play-audio"></a>オーディオの再生

通話が作成されると、`CallConnection` オブジェクトの `playAudioWithResponse` メソッドを使用して、被発信者に対する音声メッセージを再生できます。 `playAudioWithResponse` メソッドでは、次のパラメーターがサポートされています。

- `audioFileUri` は、再生されるメッセージが含まれている音声ファイルの URI です。
- `playAudioOptions` は、音声の再生オプションです。 種類は `PlayAudioOptions` です。 `PlayAudioOptions` オブジェクトでサポートされているパラメーターを次に示します。
    - `loop` は、音声メッセージを繰り返すには、true に設定します。
    - `audioFileId` は、AudioFileUri 内のメディアの ID。メディアをキャッシュします。
    - `operationContext` は、要求コンテキストの一意の ID です。

```java
// Preparing data for request
String audioFileUri = callConfiguration.audioFileUrl;
Boolean loop = true;
String operationContext = UUID.randomUUID().toString();
String audioFileId = UUID.randomUUID().toString();
PlayAudioOptions playAudioOptions = new PlayAudioOptions();
playAudioOptions.setLoop(loop);
playAudioOptions.setAudioFileId(audioFileId);
playAudioOptions.setOperationContext(operationContext);

Logger.logMessage(Logger.MessageType.INFORMATION, "Performing PlayAudio operation");
Response<PlayAudioResult> playAudioResponse = this.callConnection.playAudioWithResponse(audioFileUri, playAudioOptions, null);

PlayAudioResult response = playAudioResponse.getValue();
```

`playAudio` メソッドは `Response<PlayAudioResult>` を返します。これを使用して、`getStatus()` メソッド で `OperationStatus` を取得できます。 `OperationStatus` の値は次のとおりです: `NotStarted`、`Running`、`Completed`、または `Failed`。

```java
Logger.logMessage(Logger.MessageType.INFORMATION, "playAudioWithResponse -- > " + GetResponse(playAudioResponse) +
", Id: " + response.getOperationId() + ", OperationContext: " + response.getOperationContext() + ", OperationStatus: " +
response.getStatus().toString());
```

## <a name="add-a-participant-to-the-call"></a>通話に参加者を追加する

`CallConnection` オブジェクトの `addParticipantWithResponse` メソッドを使用して、通話に参加者を追加します。 `addParticipantWithResponse` メソッドでは、次のパラメーターがサポートされています。

- `participant` は、`CommunicationUserIdentifier` または `PhoneNumberIdentifier` の型の参加者識別子です。
- `alternateCallerId` は、ソース発信者 ID です。
- `operationContext` は、要求コンテキストの一意の ID です。

```java
// Preparing data for request
CommunicationIdentifier participant = null;
String operationContext = UUID.randomUUID().toString();

if (identifierKind == CommunicationIdentifierKind.UserIdentity) {
    participant = new CommunicationUserIdentifier(addedParticipant);

} else if (identifierKind == CommunicationIdentifierKind.PhoneIdentity) {
    participant = new PhoneNumberIdentifier(addedParticipant);
}

Response<AddParticipantResult> response = callConnection.addParticipantWithResponse(participant, this.callConfiguration.sourcePhoneNumber, operationContext, null);
```

## <a name="cancel-media-processing"></a>メディア処理を取り消す

`CallConnection` オブジェクトの `cancelAllMediaOperationsWithResponse` メソッドを使用して、音声の再生操作を取り消します。 `cancelAllMediaOperationsWithResponse` メソッドでは、次のパラメーターがサポートされています。

- `operationContext` は、要求コンテキストの一意の ID です。

```java
String operationContext = UUID.randomUUID().toString();
Response<CancelAllMediaOperationsResult> cancelmediaresponse = this.callConnection.cancelAllMediaOperationsWithResponse(operationContext, null);
```

## <a name="hang-up-the-call"></a>電話を切る

`CallConnection` オブジェクトの `hangupWithResponse` メソッドを使用して、電話を切ります。

```java
Response<Void> response = this.callConnection.hangupWithResponse(null);
```