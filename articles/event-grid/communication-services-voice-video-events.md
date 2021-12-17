---
title: Azure Communication Services - 音声およびビデオ通話イベント
description: この記事では、音声およびビデオ通話のイベントの Event Grid イベント ソースとして Azure Communication Services を使用する方法について説明します。
ms.topic: conceptual
ms.date: 10/15/2021
ms.author: vikramdh
ms.openlocfilehash: 9259a5ce7151197f1155bfb91f23f3c98afca9b4
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/18/2021
ms.locfileid: "130134282"
---
# <a name="azure-communication-services---voice-and-video-calling-events"></a>Azure Communication Services - 音声およびビデオ通話イベント

この記事では、コミュニケーション サービスの音声およびビデオ通話のイベントのプロパティとスキーマを取り上げます。  イベント スキーマの概要については、「[Azure Event Grid イベント スキーマ](event-schema.md)」を参照してください。

## <a name="events-types"></a>イベントの種類

Azure Communication Services は、次の音声およびビデオ通話のイベントの種類を出力します。

| イベントの種類                                                  | 説明                                                                                    |
| ----------------------------------------------------------- | ---------------------------------------------------------------------------------------------- |
| Microsoft.Communication.RecordingFileStatusUpdated | ファイルの記録が可能なときに発行されます |

## <a name="event-responses"></a>イベント応答

イベントがトリガーされると、Event Grid サービスにより、そのイベントに関するデータがサブスクライブしているエンドポイントに送信されます。

このセクションには、各イベントでそのデータがどのように見えるかの例が含まれています。

> [!IMPORTANT]
> 通話レコーディング機能はまだパブリック プレビューの段階にあります

### <a name="microsoftcommunicationrecordingfilestatusupdated"></a>Microsoft.Communication.RecordingFileStatusUpdated

```json
[
 {
  "id": "7283825e-f8f1-4c61-a9ea-752c56890500",
  "topic": "/subscriptions/{subscription-id}/resourcegroups/}{group-name}/providers/microsoft.communication/communicationservices/{communication-services-resource-name}",
  "subject": "/recording/call/{call-id}/recordingId/{recording-id}",
  "data": {
    "recordingStorageInfo": {
      "recordingChunks": [
        {
          "documentId": "0-eus-d12-801b3f3fc462fe8a01e6810cbff729b8",
          "index": 0,
          "endReason": "SessionEnded",
          "contentLocation": "https://storage.asm.skype.com/v1/objects/0-eus-d12-801b3f3fc462fe8a01e6810cbff729b8/content/video",
          "metadataLocation": "https://storage.asm.skype.com/v1/objects/0-eus-d12-801b3f3fc462fe8a01e6810cbff729b8/content/acsmetadata"
        }
      ]
    },
    "recordingStartTime": "2021-07-27T15:20:23.6089755Z",
    "recordingDurationMs": 6620,
    "sessionEndReason": "CallEnded"
  },
  "eventType": "Microsoft.Communication.RecordingFileStatusUpdated",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2021-07-27T15:20:34.2199328Z"
 }
]
```
