---
title: インクルード ファイル
description: インクルード ファイル
services: azure-communication-services
author: ravithanneeru
manager: joseys
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 06/30/2021
ms.topic: include
ms.custom: include file
ms.author: joseys
ms.openlocfilehash: b71ae256329d9fe8d28f0b5154f0b732de61009f
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130050783"
---
## <a name="sample-code"></a>サンプル コード
このクイックスタートの最終的なコードは [GitHub](https://github.com/Azure-Samples/communication-services-java-quickstarts/tree/main/ServerRecording) にあります。

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウントを作成します。 詳細については、[無料アカウントの作成](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)に関するページをご覧ください。
- [Node.js (12.18.4 以上)](https://nodejs.org/en/download/)
- [Java Development Kit (JDK)](/azure/developer/java/fundamentals/java-jdk-install) バージョン 11 以降。
- [Apache Maven](https://maven.apache.org/download.cgi)。
- [Spring Boot Framework バージョン 2.5.0](https://spring.io/projects/spring-boot)
- Azure Communication Services リソースを作成します。 詳細については、[Azure Communication Services リソースの作成](../../../create-communication-resource.md)に関するページを参照してください。 このクイックスタートで使用するリソースの **接続文字列** を記録する必要があります。
- Azure ストレージ アカウントとコンテナーの詳細については、「[ストレージ アカウントを作成する](../../../../../storage/common/storage-account-create.md?tabs=azure-portal)」を参照してください。 ご利用の **接続文字列** と **コンテナー名** は、このクイックスタートで使用するので、記録しておく必要があります。
- [Azure Event Grid](../../../../../event-grid/overview.md) Web hook。

## <a name="object-model"></a>オブジェクト モデル

次のクラスによって、Java のレコーディング サーバー アプリの主要な機能の一部が処理されます。

| 名前                                  | 説明                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| CallingServerClientBuilder | このクラスは、CallingServerClient のインスタンスを作成するために使用されます。|
| CallingServerClient | このクラスは通話機能に必要です。 CallingServerClientBuilder を介してインスタンスを取得し、それを使用して通話の開始/切断、オーディオの再生/キャンセル、参加者の追加/削除を行います |

## <a name="getting-servercallid-as-a-requirement-for-call-recording-server-apis"></a>通話レコーディング サーバー API の要件として serverCallId を取得する

> [!NOTE]
> この API は開発者向けにプレビューとして提供されており、寄せられたフィードバックにもとづいて変更される場合があります。 この API は運用環境で使用しないでください。 この API を使用するには、ACS Calling Web SDK の "ベータ" リリースを使用してください レコーディング フローを含むクライアント サンプルは、[GitHub](https://github.com/Azure-Samples/communication-services-web-calling-hero/tree/public-preview) で入手できます。


通話レコーディングは、コアとなる通話 API の拡張機能です。 まず、記録機能 API オブジェクトを取得する必要があります。

```JavaScript
const callRecordingApi = call.api(Features.Recording);
```

レコーディングに関する変更をサブスクライブします。

```JavaScript
const isRecordingActiveChangedHandler = () => {
  console.log(callRecordingApi.isRecordingActive);
};

callRecordingApi.on('isRecordingActiveChanged', isRecordingActiveChangedHandler);
```

レコーディング セッションの開始、停止、一時停止、再開に使用する通話 ID を取得します。

通話が接続されたら、`getServerCallId` メソッドを使用してサーバー通話 ID を取得します。

```JavaScript
callAgent.on('callsUpdated', (e: { added: Call[]; removed: Call[] }): void => {
    e.added.forEach((addedCall) => {
        addedCall.on('stateChanged', (): void => {
            if (addedCall.state === 'Connected') {
                addedCall.info.getServerCallId().then(result => {
                    dispatch(setServerCallId(result));
                }).catch(err => {
                    console.log(err);
                });
            }
        });
    });
});
```

## <a name="create-a-calling-server-client"></a>呼び出し元サーバー クライアントを作成する

呼び出し元サーバー クライアントを作成するには、Communication Services 接続文字列を使用し、それを呼び出し元サーバー クライアント オブジェクトに渡します。

```java
NettyAsyncHttpClientBuilder httpClientBuilder = new NettyAsyncHttpClientBuilder();
CallingServerClientBuilder builder = new CallingServerClientBuilder().httpClient(httpClientBuilder.build())
 .connectionString(connectionString);
 callingServerClient = builder.buildClient();
```

## <a name="start-recording-session-using-startrecordingwithresponse-server-api"></a>'startRecordingWithResponse' サーバー API を使用してレコーディング セッションを開始する

通話の開始中に受信したサーバー通話 ID を使用します。

```java
URI recordingStateCallbackUri = new URI("<CallbackUri>");

Response<StartCallRecordingResult> response = this.callingServerClient.initializeServerCall("<serverCallId>")
.startRecordingWithResponse(String.valueOf(recordingStateCallbackUri), null, null);
```
`startRecordingWithResponse` API 応答には、レコーディング セッションのレコーディング ID が含まれています。

## <a name="start-recording-session-with-startrecordingoptions-using-startrecordingwithresponse-server-api"></a>'startRecordingWithResponse' サーバー API を使用して StartRecordingOptions でレコーディング セッションを開始する

通話の開始中に受信したサーバー通話 ID を使用します。

- RecordingContent は、レコーディング コンテンツ タイプを渡すために使用されます。 例: AUDIO/AUDIO_VIDEO。
- RecordingChannel は、レコーディング チャネルの種類を渡すために使用されます。 例: MIXED/UNMIXED。
- RecordingFormat は、レコーディングの形式を渡すために使用されます。 例: MP4/MP3/WAV。

```java
URI recordingStateCallbackUri = new URI("<CallbackUri>");
StartRecordingOptions recordingOptions = new StartRecordingOptions();
recordingOptions.setRecordingContent(RecordingContent.AUDIO_VIDEO);
recordingOptions.setRecordingChannel(RecordingChannel.MIXED);
recordingOptions.setRecordingFormat(RecordingFormat.MP4);
Response<StartCallRecordingResult> response = this.callingServerClient.initializeServerCall("<serverCallId>")
.startRecordingWithResponse(String.valueOf(recordingStateCallbackUri), recordingOptions, null);
```

## <a name="stop-recording-session-using-stoprecordingwithresponse-server-api"></a>'stopRecordingWithResponse' サーバー API を使用してレコーディング セッションを停止する

`startRecordingWithResponse` の応答として受信したレコーディング ID を使用します。

```java
Response<Void> response = this.callingServerClient.initializeServerCall(serverCallId)
.stopRecordingWithResponse(recordingId, null);
```

## <a name="pause-recording-session-using-pauserecordingwithresponse-server-api"></a>'pauseRecordingWithResponse' サーバー API を使用してレコーディング セッションを一時停止する

`startRecordingWithResponse` の応答として受信したレコーディング ID を使用します。

```java
Response<Void> response = this.callingServerClient.initializeServerCall(serverCallId)
.pauseRecordingWithResponse(recordingId, null);
```

## <a name="resume-recording-session-using-resumerecordingwithresponse-server-api"></a>'resumeRecordingWithResponse' サーバー API を使用してレコーディング セッションを再開する

`startRecordingWithResponse` の応答として受信したレコーディング ID を使用します。

```java
Response<Void> response = this.callingServerClient.initializeServerCall(serverCallId)
.resumeRecordingWithResponse(serverCallId, null);
```

## <a name="download-recording-file-using-downloadtowithresponse-server-api"></a>'downloadToWithResponse' サーバー API を使用してレコーディング ファイルをダウンロードする

記録されたメディアをダウンロードする準備ができたときにサービスに通知するために、[Azure Event Grid](../../../../../event-grid/overview.md) Web hook または他のトリガーされたアクションを使用する必要があります。

イベント スキーマの例を次に示します。

```
{
    "id": string, // Unique guid for event
    "topic": string, // Azure Communication Services resource id
    "subject": string, // /recording/call/{call-id}
    "data": {
        "recordingStorageInfo": {
            "recordingChunks": [
                {
                    "documentId": string, // Document id for retrieving from AMS storage
                    "contentLocation": string, //ACS URL where the content is located
                    "metadataLocation": string, // ACS URL where the metadata for this chunk is located
                    "index": int, // Index providing ordering for this chunk in the entire recording
                    "endReason": string, // Reason for chunk ending: "SessionEnded", "ChunkMaximumSizeExceeded”, etc.
                }
            ]
        },
        "recordingStartTime": string, // ISO 8601 date time for the start of the recording
        "recordingDurationMs": int, // Duration of recording in milliseconds
        "sessionEndReason": string // Reason for call ending: "CallEnded", "InitiatorLeft”, etc.
    },
    "eventType": string, // "Microsoft.Communication.RecordingFileStatusUpdated"
    "dataVersion": string, // "1.0"
    "metadataVersion": string, // "1"
    "eventTime": string // ISO 8601 date time for when the event was created
}
```
記録されたメディアをダウンロードするには、`CallingServerClient` クラスの `downloadToWithResponse` メソッドを使用します。 `downloadToWithResponse` メソッドでサポートされているパラメーターを次に示します。

- `contentLocation`: コンテンツが置かれている ACS URL。
- `destinationPath`: ファイルの場所。
- `parallelDownloadOptionss`: 並列ダウンロードの動作方法を変更するための省略可能な ParallelDownloadOptions オブジェクト。
- `overwrite`: True にすると、ファイルが存在する場合、それは上書きされます。
- `context`: 要求コンテキストを表すコンテキスト。

```Java
Boolean overwrite = true;
ParallelDownloadOptions parallelDownloadOptions = null;
Context context = null;

String filePath = String.format(".\\%s.%s", documentId, fileType);
Path destinationPath = Paths.get(filePath);

Response<Void> downloadResponse = callingServerClient.downloadToWithResponse(contentLocation, destinationPath, parallelDownloadOptions, overwrite, context);
```
レコーディング ファイルのコンテンツの場所とドキュメント ID は、`recordingChunk` ごとに、`contentLocation` および `documentId` フィールドからそれぞれ取得できます。
