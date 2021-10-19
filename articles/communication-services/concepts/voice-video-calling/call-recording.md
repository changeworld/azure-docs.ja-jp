---
title: Azure Communication Services の通話録音の概要
titleSuffix: An Azure Communication Services concept document
description: 通話録音機能と API の概要について説明します。
author: GrantMeStrength
manager: anvalent
services: azure-communication-services
ms.author: jken
ms.date: 06/30/2021
ms.topic: conceptual
ms.custom: references_regions
ms.service: azure-communication-services
ms.subservice: calling
ms.openlocfilehash: 103ced05c6b88c5f7f60de398f78f89cc460daf9
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/07/2021
ms.locfileid: "129667309"
---
# <a name="calling-recording-overview"></a>通話録音の概要

[!INCLUDE [Public Preview](../../includes/public-preview-include-document.md)]

> [!NOTE]
> 通話レコーディングは、米国、英国、ヨーロッパ、アジア、およびオーストラリアのリージョンで作成された Communication Services リソースで使用できます。

通話録音機能には、録音を開始、停止、一時停止、再開するための一連の API が用意されています。 これらの API には、サーバー側ビジネス ロジックから、またはユーザーの操作によってトリガーされるイベントを介してアクセスできます。 録音されたメディアの出力は MP4 Audio+Video 形式で行われます。これは Teams がメディアの記録に使用するのと同じ形式です。 メディアとメタデータに関連した通知は Event Grid から出力されます。 録音は、任意の長期保管用ソリューションに取得および移動するために、組み込みの一時ストレージに 48 時間保存されます。 

![通話録音機能の概念図](../media/call-recording-concept.png)

## <a name="media-output-types"></a>メディア出力の種類
現在、通話レコーディングでは、混合オーディオ + ビデオ MP4 と混合オーディオ専用 MP3/WAV 出力形式がサポートされています。 混合オーディオ + ビデオ出力メディアは、Microsoft Teams のレコーディングによって生成された会議の録音と一致します。

| チャネルの型 | コンテンツ形式 | ビデオ | オーディオ |
| :----------- | :------------- | :---- | :--------------------------- |
| audioVideo | mp4 | 既定のタイル配置での参加者全員の 1920 x 1080 8 FPS ビデオ | 参加者全員の 16 kHz mp4a 混合オーディオ |
| audioOnly| mp3/wav | 該当なし | 参加者全員の 16 kHz mp3/wav 混合オーディオ |


## <a name="run-time-control-apis"></a>ランタイム コントロール API
ランタイム コントロール API を使用すると、内部ビジネス ロジック トリガー (アプリケーションによるグループ通話の作成や会話の録音など) またはユーザーによってトリガーされるアクション (サーバー アプリケーションへの録音開始の指示) によって録音を管理できます。 通話録音機能 API は [Out-of-Call API](./call-automation-apis.md#out-of-call-apis) です。`serverCallId` を利用して録音を開始します。 通話を作成するとき、通話の確立後、`Microsoft.Communication.CallLegStateChanged` イベント経由で `serverCallId` が返されます。 `serverCallId` は `data.serverCallId` フィールドにあります。 呼び出し元のクライアント SDK から `serverCallId` を取得する方法については、[通話録音機能のクイックスタート サンプル](../../quickstarts/voice-video-calling/call-recording-sample.md) ページをご覧ください。 録音が開始されると、`recordingOperationId` が返され、一時停止や再開など、後続の操作で利用されます。   

| 操作                            | 操作対象            | 説明                       |
| :-------------------- | :--------------------- | :----------------------------- |
| 記録の開始       | `serverCallId`         | `recordingOperationId` を返します。 | 
| 録音状態を取得する   | `recordingOperationId` | `recordingState` を返します。       | 
| 録音の一時停止       | `recordingOperationId` | 通話録音の一時停止と再開により、通話または会議の一部の録音をスキップし、1 つのファイルへの録音を再開することができます。 | 
| 録音の再開      | `recordingOperationId` | 一時停止になっている録音操作を再開します。 コンテンツは一時停止前のコンテンツと同じファイルに入ります。 | 
| 録音の停止        | `recordingOperationId` | 録音を停止し、ファイル ダウンロードのための最終メディア処理を開始します。 | 


## <a name="event-grid-notifications"></a>Event Grid の通知

> Azure Communication Services には、録音用に短期のメディア ストレージが用意されています。 **保持する必要のある録音済みコンテンツは、48 時間以内にエクスポートしてください。** 48 時間が経過すると、録音は使用できなくなります。

Event Grid の通知 `Microsoft.Communication.RecordingFileStatusUpdated` は、録音を取得する準備ができたときに発行されます。通常は、録音プロセスが完了 (たとえば、会議が終了して録音が停止) してから 1、2 分後になります。 録音のイベント通知には `contentLocation` と `metadataLocation` が含まれます。これらは、録音されたメディアと録音メタデータ ファイルの両方を取得するために使用できます。

### <a name="notification-schema-reference"></a>通知スキーマ リファレンス
```typescript
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
                    "metadataLocation": <string>, // url of the metadata for this chunk
                    "contentLocation": <string>   // url of the mp4, mp3, or wav for this chunk
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
## <a name="regulatory-and-privacy-concerns"></a>規制とプライバシーに関する考慮事項

多くの国や州には、PSTN、音声、およびビデオ通話の録音に適用される法律と規制があり、多くの場合は、ユーザーが通信の録音に同意する必要があります。 法令に従って通話録音機能を使用することは、お客様の責任です。 各参加者に適用される法律を遵守した方法で、録音対象の通信の当事者から同意を得る必要があります。

個人データの管理に関する規制では、ユーザー データをエクスポートできることが求められます。 これらの要件をサポートするために、録音メタデータ ファイルでは、`participants` 配列に各通話参加者の participantId が格納されています。 `participants` 配列内の MRI を内部ユーザー ID と相互参照して、通話の参加者を識別することができます。 録音メタデータ ファイルの例は、参照用に以下に示します。

## <a name="next-steps"></a>次のステップ
詳細については、[通話録音機能のクイックスタート](../../quickstarts/voice-video-calling/call-recording-sample.md)を参照してください。

[Call Automation API](./call-automation-apis.md) について学習してください。
