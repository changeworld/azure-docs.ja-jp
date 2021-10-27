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
ms.openlocfilehash: 3f3b673da2809e45af88ae887eb289edb4e5c65f
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130050735"
---
## <a name="sample-code"></a>サンプル コード
このクイックスタートの最終的なコードは [GitHub](https://github.com/Azure-Samples/communication-services-dotnet-quickstarts/tree/main/ServerRecording) にあります。

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウントを作成します。 詳細については、[アカウントの無料作成](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)に関するページを参照してください。
- [Node.js (12.18.4 以上)](https://nodejs.org/en/download/)。
- [Visual Studio (2019 以上)](https://visualstudio.microsoft.com/vs/)。
- [.NET Core 3.1](https://dotnet.microsoft.com/download/dotnet-core/3.1) (使用する Visual Studio インスタンス (32 または 64 ビット) に対応するバージョンを必ずインストールしてください)。
- Azure Communication Services リソースを作成します。 詳細については、[Azure Communication Services リソースの作成](../../../create-communication-resource.md)に関するページを参照してください。 このクイックスタートで使用するリソースの **接続文字列** を記録する必要があります。
- Azure ストレージ アカウントとコンテナーの詳細については、「[ストレージ アカウントを作成する](../../../../../storage/common/storage-account-create.md?tabs=azure-portal)」を参照してください。 ご利用のストレージ **接続文字列** と **コンテナー名** は、このクイックスタートで使用するので、記録しておく必要があります。
- [Azure Event Grid](../../../../../event-grid/overview.md) Web hook。

## <a name="object-model"></a>オブジェクト モデル

次のクラスによって、C# のレコーディング サーバー アプリの主要な機能の一部が処理されます。

| 名前                                  | 説明                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| CallingServerClient | このクラスはレコーディング機能に必要です。 ACS リソース接続文字列を使用して CallingServerClient のインスタンスを作成し、それを使用して通話レコーディングの開始/停止および一時停止/再開を行います。 |

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

呼び出し元サーバー クライアントを作成するには、Azure Communication Services 接続文字列を使用し、それを呼び出し元サーバー クライアント オブジェクトに渡します。

```csharp
CallingServerClient callingServerClient = new CallingServerClient("<Resource_Connection_String>");
```

## <a name="start-recording-session-using-startrecordingasync-server-api"></a>'StartRecordingAsync' サーバー API を使用してレコーディング セッションを開始する

通話の開始中に受信したサーバー通話 ID を使用します。

```csharp
var startRecordingResponse = await callingServerClient.InitializeServerCall("<servercallid>").StartRecordingAsync("<callbackuri>").ConfigureAwait(false);
```
`StartRecordingAsync` API 応答には、レコーディング セッションのレコーディング ID が含まれています。

## <a name="start-recording-session-with-options-using-startrecordingasync-server-api"></a>'StartRecordingAsync' サーバー API を使用して、オプションを指定してレコーディング セッションを開始する

通話の開始中に受信したサーバー通話 ID を使用します。

- RecordingContent は、レコーディング コンテンツ タイプを渡すために使用されます。 例: audio または audiovideo。
- RecordingChannel は、レコーディング チャネルの種類を渡すために使用されます。 例: mixed または unmixed。
- RecordingFormat は、レコーディングの形式を渡すために使用されます。 例: mp4、mp3、または wav。

```csharp
var startRecordingResponse = await callingServerClient.InitializeServerCall("<servercallid>").StartRecordingAsync("<callbackuri>","<RecordingContent>","<RecordingChannel>","<RecordingFormat>").ConfigureAwait(false);
```
`StartRecordingAsync` API 応答には、レコーディング セッションのレコーディング ID が含まれています。

## <a name="stop-recording-session-using-stoprecordingasync-server-api"></a>'StopRecordingAsync' サーバー API を使用してレコーディング セッションを停止する

`StartRecordingAsync` の応答として受信したレコーディング ID を使用します。

```csharp
 var stopRecording = await callingServerClient.InitializeServerCall("<servercallid>").StopRecordingAsync("<recordingid>").ConfigureAwait(false);
```

## <a name="pause-recording-session-using-pauserecordingasync-server-api"></a>'PauseRecordingAsync' サーバー API を使用してレコーディング セッションを一時停止する

`StartRecordingAsync` の応答として受信したレコーディング ID を使用します。

```csharp
var pauseRecording = await callingServerClient.InitializeServerCall("<servercallid>").PauseRecordingAsync("<recordingid>");
```

## <a name="resume-recording-session-using-resumerecordingasync-server-api"></a>'ResumeRecordingAsync' サーバー API を使用してレコーディング セッションを再開する

`StartRecordingAsync` の応答として受信したレコーディング ID を使用します。

```csharp
var resumeRecording = await callingServerClient.InitializeServerCall("<servercallid>").ResumeRecordingAsync("<recordingid>");
```

## <a name="download-recording-file-using-downloadstreamingasync-server-api"></a>'DownloadStreamingAsync' サーバー API を使用してレコーディング ファイルをダウンロードする

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

記録されたメディアをダウンロードするには、`DownloadStreamingAsync` API を使用します。

```csharp
var recordingDownloadUri = new Uri(downloadLocation);
var response = callingServerClient.DownloadStreamingAsync(recordingDownloadUri);
```
レコーディング用の downloadLocation は、`recordingChunk` の `contentLocation` 属性から取り込むことができます。 `DownloadStreamingAsync` メソッドから、ダウンロードしたコンテンツを含む `Response<Stream>` 型の応答が返されます。
