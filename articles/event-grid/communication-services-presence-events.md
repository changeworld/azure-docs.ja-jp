---
title: Azure Communication Services - プレゼンス イベント
description: この記事では、ユーザー プレゼンス イベントの Event Grid イベント ソースとして Azure Communication Services を使用する方法について説明します。
ms.topic: conceptual
ms.date: 10/15/2021
ms.author: vikramdh
ms.openlocfilehash: 75e23d5de55ab205cc35bf6e7496413f4dd4be9a
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/18/2021
ms.locfileid: "130134286"
---
# <a name="azure-communication-services---presence-events"></a>Azure Communication Services - プレゼンス イベント

この記事では、コミュニケーション サービスのプレゼンス イベントのプロパティとスキーマを取り上げます。  イベント スキーマの概要については、「[Azure Event Grid イベント スキーマ](event-schema.md)」を参照してください。

## <a name="events-types"></a>イベントの種類

Azure Communication Services から出力されるユーザー プレゼンス イベントの種類は次のとおりです。

| イベントの種類                                                  | 説明                                                                                    |
| ----------------------------------------------------------- | ---------------------------------------------------------------------------------------------- |
| Microsoft.Communication.UserDisconnected | Communication Services ユーザーが Communication Services から切断されたと見なされた後に発行されます |

## <a name="event-responses"></a>イベント応答

イベントがトリガーされると、Event Grid サービスにより、そのイベントに関するデータがサブスクライブしているエンドポイントに送信されます。

このセクションには、各イベントでそのデータがどのように見えるかの例が含まれています。

> [!IMPORTANT]
> Microsoft.Communication.UserDisconnected イベントは、パブリック プレビュー段階にあります。 このプレビュー期間中は、ユーザーの切断状態に関連付けられているログがグローバルにレプリケートされる場合があります。 Event Grid を介してこのイベントをサブスクライブすることで切断状態を取得できます。

> [!NOTE]
> Microsoft.Communication.UserDisconnected イベントは、チャットのコンテキストでのみ適用されます。
 
### <a name="microsoftcommunicationuserdisconnected"></a>Microsoft.Communication.UserDisconnected

```json
[
 {
  "id": "8f60490d-0719-4d9d-a1a6-835362fb752e",
  "topic": "/subscriptions/{subscription-id}/resourcegroups/}{group-name}/providers/microsoft.communication/communicationservices/{communication-services-resource-name}",
  "subject": "user/{rawId}",
  "data": {
    "userCommunicationIdentifier": {
      "rawId": "8:acs:3d703c91-9657-4b3f-b19c-ef9d53f99710_0000000b-d198-0d50-84f5-084822008d40",
      "communicationUser": {
        "id": "8:acs:3d703c91-9657-4b3f-b19c-ef9d53f99710_0000000b-d198-0d50-84f5-084822008d40"
      }
    }
  },
  "eventType": "Microsoft.Communication.UserDisconnected",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2021-08-10T20:25:38Z"
 }
]
```
