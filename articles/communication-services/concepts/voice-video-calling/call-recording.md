---
title: Azure Communication Services の通話録音の概要
titleSuffix: An Azure Communication Services concept document
description: 通話録音機能と API の概要について説明します。
author: joseys
manager: anvalent
services: azure-communication-services
ms.author: joseys
ms.date: 04/13/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: df9638588b4d677a7ceb80bafbe7157bb5c2df42
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/19/2021
ms.locfileid: "107730329"
---
# <a name="calling-recording-overview"></a>通話録音の概要

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

> [!NOTE]
> 多くの国や州には、PSTN、音声、およびビデオ通話の録音に適用される法律と規制があり、多くの場合は、ユーザーが通信の録音に同意する必要があります。 法令に従って通話録音機能を使用することは、お客様の責任です。 各参加者に適用される法律を遵守した方法で、録音対象の通信の当事者から同意を得る必要があります。

> [!NOTE]
> 個人データの管理に関する規制では、ユーザー データをエクスポートできることが求められます。 これらの要件をサポートするために、録音メタデータ ファイルでは、`participants` 配列に各通話参加者の participantId が格納されています。 `participants` 配列内の MRI を内部ユーザー ID と相互参照して、通話の参加者を識別することができます。 録音メタデータ ファイルの例は、参照用に以下に示します。

通話録音機能には、録音を開始、停止、一時停止、再開するための一連の API が用意されています。 これらの API には、サーバー側ビジネス ロジックから、またはユーザーの操作によってトリガーされるイベントを介してアクセスできます。 録音されたメディアの出力は `MP4 Audio+Video` 形式で行われます。これは Teams がメディアの記録に使用するのと同じ形式です。 メディアとメタデータに関連した通知は Event Grid から出力されます。 録音は、任意の長期保管用ソリューションに取得および移動するために、組み込みの一時ストレージに 48 時間保存されます。 

## <a name="run-time-control-apis"></a>ランタイム コントロール API
ランタイム コントロール API を使用すると、内部ビジネス ロジック トリガー (アプリケーションによるグループ通話の作成や会話の録音など) またはユーザーによってトリガーされるアクション (サーバー アプリケーションへの録音開始の指示) によって録音を管理できます。 どちらのシナリオでも、特定の会議または通話を録音するには `<conversation-id>` が必要です。 

#### <a name="getting-conversation-id-from-a-server-initiated-call"></a>サーバーで開始された通話から会話 ID を取得する

`ConversationId` は `Microsoft.Communication.CallLegStateChanged` イベントを介して返されます。 このイベント通知は、通話が確立された後に生成されます。 これは `data.ConversationId` フィールドで確認できます。 この値は、ランタイム コントロール API で `{conversationId}` パラメーターとして直接使用できます。
```
      {
        "id": null,
        "topic": null,
        "subject": "callLeg/<callLegId>/callState",
        "data": {
---->       "ConversationId": "<conversation-id>",    <----
            "CallLegId": "<callLegId>",
            "CallState": "Established"
        },
        "eventType": "Microsoft.Communication.CallLegStateChanged",
        "eventTime": "2021-04-14T16:32:34.1115003Z",
        "metadataVersion": null,
        "dataVersion": null
    }
```
                                                            
#### <a name="getting-the-conversation-id-from-a-user-triggered-event-on-the-client"></a>クライアント側でユーザーによってトリガーされたイベントから会話 ID を取得する

通話が確立した後に、JavaScript の `@azure/communication-calling` ライブラリから `let result = call.info.getConversationUrl()` を呼び出して `conversationUrl` を取得し、 **`conversationUrl` を Base64Url でエンコードして、ランタイム コントロール API で使用する `{conversationId}` を取得します**。 エンコードは、クライアントでサーバーにイベントを送信する前に行うことも、サーバー側で行うこともできます。

`conversationUrl` は Base64Url でエンコードする "*必要があります*"。Base64 エンコード (つまり btoa) と混同しないように注意してください。                                                            

### <a name="start-recording"></a>録画を開始して

#### <a name="request"></a>Request

**HTTP**
<!-- {
  "blockType": "request",
  "name": "start-recording"
}-->
```
POST /conversations/{conversationId}/Recordings
Content-Type: application/json

{
  "operationContext": "string", // developer provided string for correlation context on each operation
  "recordingStateCallbackUri": "string"
}
```
**C# SDK**
<!-- {
  "blockType": "request",
  "name": "start-recording"
}-->
```C#
string connectionString = "YOUR_CONNECTION_STRING";
ConversationClient conversationClient = new ConversationClient(connectionString);

/// start call recording
StartRecordingResponse startRecordingResponse = await conversationClient.StartRecordingAsync(
    conversationId: "<conversation-id>"
    operationContext: "<operation-context>", /// developer provided string for correlation context on each operation
    recordingStateCallbackUri: "<recording-state-callback-uri>").ConfigureAwait(false);

string recordingId = startRecordingResponse.RecordingId;
```

#### <a name="response"></a>Response

**HTTP**
<!-- {
  "blockType": "response",
  "truncated": true,
} -->

```http
HTTP/1.1 200 Success
Content-Type: application/json

{
  "recordingId": "string"
}
```
```
HTTP/1.1 400 Bad request
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```
```
HTTP/1.1 404 Not found
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```
```
HTTP/1.1 500    Internal server error
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```

### <a name="get-call-recording-state"></a>通話録音の状態を取得する

#### <a name="request"></a>Request

**HTTP**
<!-- {
  "blockType": "request",
  "name": "get-recording-state"
}-->
```http
GET /conversations/{conversationId}/recordings/{recordingId}
Content-Type: application/json

{
}
```
**C# SDK**
<!-- {
  "blockType": "request",
  "name": "start-recording"
}-->
```C#
string connectionString = "YOUR_CONNECTION_STRING";
ConversationClient conversationClient = new ConversationClient(connectionString);

/// get recording state
GetCallRecordingStateResponse recordingState = await conversationClient.GetRecordingStateAsync(
    conversationId: "<conversation-id>",
    recordingId: <recordingId>).ConfigureAwait(false);
```
#### <a name="response"></a>Response

**HTTP**
<!-- {
  "blockType": "response",
  "truncated": true,
} -->

```http
HTTP/1.1 200 Success
Content-Type: application/json

{
  "recordingState": "active"
}
```
```
HTTP/1.1 400 Bad request
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```
```
HTTP/1.1 500    Internal server error
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```

### <a name="stop-recording"></a>記録の停止
#### <a name="request"></a>Request
**HTTP**
<!-- {
  "blockType": "request",
  "name": "stop-recording"
}-->
```
DELETE /conversations/{conversationId}/recordings/{recordingId}
Content-Type: application/json

{
  "operationContext": "string" // developer provided string for correlation context on each operation
}
```
**C# SDK**
<!-- {
  "blockType": "request",
  "name": "start-recording"
}-->
```C#
string connectionString = "YOUR_CONNECTION_STRING";
ConversationClient conversationClient = new ConversationClient(connectionString);

/// stop recording
StopRecordingResponse response = conversationClient.StopRecordingAsync(
    conversationId: "<conversation-id>",
    recordingId: <recordingId>,
    operationContext: "<operation-context>").ConfigureAwait(false);
```
#### <a name="response"></a>Response
**HTTP**
<!-- {
  "blockType": "response",
  "truncated": true,
} -->

```http
HTTP/1.1 200 Success
Content-Type: application/json

{
}
```
```
HTTP/1.1 400 Bad request
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```
```
HTTP/1.1 500    Internal server error
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```

### <a name="pause-recording"></a>レコーディングの一時停止
通話録音の一時停止と再開により、通話または会議の一部の録音をスキップし、1 つのファイルへの録音を再開することができます。 
#### <a name="request"></a>Request
**HTTP**
<!-- {
  "blockType": "request",
  "name": "pause-recording"
}-->
```
POST /conversations/{conversationId}/recordings/{recordingId}/Pause
Content-Type: application/json

{
  "operationContext": "string" // developer provided string for correlation context on each operation
}
```
**C# SDK**
<!-- {
  "blockType": "request",
  "name": "start-recording"
}-->
```C#
string connectionString = "YOUR_CONNECTION_STRING";
ConversationClient conversationClient = new ConversationClient(connectionString);

/// pause recording
PauseRecordingResponse response = conversationClient.PauseRecordingAsync(
    conversationId: "<conversation-id>",
    recordingId: <recordingId>,
    operationContext: "<operation-context>").ConfigureAwait(false);
```
#### <a name="response"></a>Response
**HTTP**
<!-- {
  "blockType": "response",
  "truncated": true,
} -->

```http
HTTP/1.1 200 Success
Content-Type: application/json

{
}
```
```
HTTP/1.1 400 Bad request
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```
```
HTTP/1.1 500    Internal server error
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```

### <a name="resume-recording"></a>録音を再開する
#### <a name="request"></a>Request
**HTTP**
<!-- {
  "blockType": "request",
  "name": "resume-recording"
}-->
```
POST /conversations/{conversationId}/recordings/{recordingId}/Resume
Content-Type: application/json

{
  "operationContext": "string" // developer provided string for correlation context on each operation
}
```
**C# SDK**
<!-- {
  "blockType": "request",
  "name": "start-recording"
}-->
```C#
string connectionString = "YOUR_CONNECTION_STRING";
ConversationClient conversationClient = new ConversationClient(connectionString);

/// resume recording
ResumeRecordingResponse response = conversationClient.ResumeRecordingAsync(
    conversationId: "<conversation-id>",
    recordingId: <recordingId>,
    operationContext: "<operation-context>").ConfigureAwait(false);
```
#### <a name="response"></a>Response
**HTTP**
<!-- {
  "blockType": "response",
  "truncated": true,
} -->

```http
HTTP/1.1 200 Success
Content-Type: application/json

{
}
```
```
HTTP/1.1 400 Bad request
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```
```
HTTP/1.1 500    Internal server error
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```

## <a name="media-output-types"></a>メディア出力の種類
通話録音では、現在、オーディオとビデオが混在する MP4 出力形式がサポートされています。 この出力メディアは、Microsoft Teams のレコーディングによって生成された会議の録音と一致します。

| チャネルの型 | コンテンツ形式 | ビデオ | オーディオ |
| :----------- | :------------- | :---- | :--------------------------- |
| audioVideo | mp4 | 既定のタイル配置での参加者全員の 1920 x 1080 8 FPS ビデオ | 参加者全員の 16 kHz mp4a 混合オーディオ |

## <a name="event-grid-notifications"></a>Event Grid の通知
Event Grid の通知 `Microsoft.Communication.RecordingFileStatusUpdated` は、録音を取得する準備ができたときに発行されます。通常は、録音プロセスが完了 (たとえば、会議が終了して録音が停止) してから 1、2 分後になります。 録音のイベント通知にはドキュメント ID が含まれます。この ID は、録音されたメディアと録音メタデータ ファイルの両方を取得するために使用できます。

- <Azure_Communication_Service_Endpoint>/recording/download/{documentId}
- <Azure_Communication_Service_Endpoint>/recording/download/{documentId}/metadata

Event Grid の通知を処理し、録音およびメタデータ ファイルをダウンロードするためのサンプル コードについては、[こちら](../../quickstarts/voice-video-calling/download-recording-file-sample.md)を参照してください。 

### <a name="notification-schema"></a>通知スキーマ
```
{
    "id": string, // Unique guid for event
    "topic": string, // Azure Communication Services resource id
    "subject": string, // /recording/call/{call-id}
    "data": {
        "recordingStorageInfo": {
            "recordingChunks": [
                {
                    "documentId": string, // Document id for retrieving from storage
                    "index": int, // Index providing ordering for this chunk in the entire recording
                    "endReason": string, // Reason for chunk ending: "SessionEnded", "ChunkMaximumSizeExceeded”, etc.
                }
            ]
        },
        "recordingStartTime": string, // ISO 8601 date time for the start of the recording
        "recordingDurationMs": int, // Duration of recording in milliseconds
        "sessionEndReason": string // Reason for call ending: "CallEnded", "InitiatorLeft", etc.
    },
    "eventType": string, // "Microsoft.Communication.RecordingFileStatusUpdated"
    "dataVersion": string, // "1.0"
    "metadataVersion": string, // "1"
    "eventTime": string // ISO 8601 date time for when the event was created
}
```
## <a name="file-download"></a>ファイルのダウンロード

> Azure Communication Services には、録音用に短期のメディア ストレージが用意されています。 **保持する必要のある録音済みコンテンツは、48 時間以内にエクスポートしてください。** 48 時間が経過すると、録音は使用できなくなります。

### <a name="download-recording"></a>記録のダウンロード
#### <a name="request"></a>Request
**HTTP**
<!-- {
  "blockType": "request",
  "name": "download-recording"
}-->
```http
GET /recording/download/{documentId}
Content-Type: application/json

{
}
```
#### <a name="response"></a>Response
**HTTP**
<!-- {
  "blockType": "response",
  "truncated": true,
} -->

```
HTTP/1.1 200 Success
Content-Type: video/mp4

{
string // Recording file bytes
}
```
```
HTTP/1.1 400 Bad request
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```
```
HTTP/1.1 500    Internal server error
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```
### <a name="download-recording-metadata"></a>録音メタデータのダウンロード
#### <a name="request"></a>Request
**HTTP**
<!-- {
  "blockType": "request",
  "name": "download-recording-metadata"
}-->
```http
GET /recording/download/{documentId}/metadata
Content-Type: application/json

{
}
```
#### <a name="response"></a>Response
**HTTP**
<!-- {
  "blockType": "response",
  "truncated": true,
} -->

```http
HTTP/1.1 200 Success
Content-Type: application/json

{
  "resourceId": "string",
  "callId": "string",
  "chunkDocumentId": "string",
  "chunkIndex": 0,
  "chunkStartTime": "string",
  "chunkDuration": 0,
  "pauseResumeIntervals": [
    {
      "startTime": "string",
      "duration": 0
    }
  ],
  "recordingInfo": {
    "contentType": "string",
    "channelType": "string",
    "format": "string",
    "audioConfiguration": {
      "sampleRate": "string",
      "bitRate": 0,
      "channels": 0
    },
    "videoConfiguration": {
      "longerSideLength": 0,
      "shorterSideLength": 0,
      "framerate": 0,
      "bitRate": 0
    }
  },
  "participants": [
    {
      "participantId": "string"
    }
  ]
}
```
```
HTTP/1.1 400 Bad request
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```
```
HTTP/1.1 500    Internal server error
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```
