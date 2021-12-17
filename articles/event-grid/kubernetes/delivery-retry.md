---
title: Kubernetes 上の Azure Event Grid - イベント配信と再試行
description: この記事では、Kubernetes 上の Event Grid を Azure Arc で使用して、イベントを配信する方法と、配信されていないメッセージを処理する方法について説明します。
author: jfggdl
ms.subservice: kubernetes
ms.author: jafernan
ms.date: 05/25/2021
ms.topic: conceptual
ms.openlocfilehash: 6a37945461a61167e2fee7d7d3ef6a8fbccf3372
ms.sourcegitcommit: e7d500f8cef40ab3409736acd0893cad02e24fc0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "122066755"
---
# <a name="event-grid-on-kubernetes---event-delivery-and-retry"></a>Kubernetes 上の Event Grid - イベント配信と再試行
Kubernetes 上の Event Grid を Azure Arc で使用すると、一致するサブスクリプションごとに少なくとも 1 回、各メッセージの配信が即座に試行されます。 Kubernetes 上の Event Grid では、サブスクライバーから成功の HTTP 200 応答が返らない場合、または何らかのエラーが発生した場合、固定の再試行スケジュールと再試行ポリシーに基づいて配信が再試行されます。 

既定では、Kubernetes 上の Event Grid によって、サブスクライバーに一度に 1 つのイベントが配信されます。 ただし、配信要求のペイロードは、1 つのイベントを含む配列です。 出力バッチ処理機能を有効にすると、複数のイベントを同時に配信することができます。 この機能の詳細については、[バッチ イベントの配信](batch-event-delivery.md)に関する記事を参照してください。

[!INCLUDE [event-grid-preview-feature-note.md](../includes/event-grid-preview-feature-note.md)]

> [!NOTE]
> プレビュー期間中、Kubernetes 上の Event Grid は、API バージョン [2020-10-15-preview](/rest/api/eventgrid/version2021-06-01-preview/event-subscriptions/create-or-update) を通じてサポートされます。 


## <a name="retry-schedule"></a>再試行のスケジュール
Kubernetes 上の Event Grid では、イベントの配信後、応答を最大 60 秒間待機します。 サブスクライバーのエンドポイントから成功応答 (HTTP 200 など) が送信されない場合、イベントの送信が再試行されます。 しくみは次のとおりです。 

1. メッセージが Kubernetes 上の Event Grid に到着します。 すぐに配信しようとします。
1. 配信に失敗した場合、メッセージは 1 分のキューにエンキューされ、1 分後に再試行されます。
1. 配信の失敗が続く場合、メッセージは 10 分のキューにエンキューされ、10 分ごとに再試行されます。
1. 成功するか、再試行ポリシーの制限に達するまで、配信が試行されます。
 
## <a name="retry-policy"></a>再試行ポリシー
再試行ポリシーを決定する構成は、2 つあります。 次のとおりです。

- 最大試行回数
- イベントの Time-To-Live (TTL)

再試行ポリシーの制限のいずれかに達すると、イベントは削除されます。 これらの制限の構成は、サブスクリプションごとに行われます。 次のセクションでは、それぞれについて詳しく説明します。

### <a name="configuring-defaults-per-subscriber"></a>サブスクライバーごとの既定値の構成
また、サブスクリプションごとに再試行ポリシーの制限を指定することもできます。 サブスクライバーごとの既定値構成の詳細については、[API のドキュメント](/rest/api/eventgrid/version2021-06-01-preview/event-subscriptions/create-or-update)を参照してください。 サブスクリプション レベルの既定値は、Kubernetes レベルの構成の Event Grid モジュールよりも優先されます。

次の例では、`maxNumberOfAttempts` を 3 分、`eventTimeToLiveInMinutes` を 30 分で Web hook サブスクリプションを設定します。

```json
{
 "properties": {
  "destination": {
   "endpointType": "WebHook",
   "properties": {
    "endpointUrl": "<your_webhook_url>",
    "eventDeliverySchema": " CloudEventSchemaV1_0"
   }
  },
  "retryPolicy": {
   "eventTimeToLiveInMinutes": 30,
   "maxDeliveryAttempts": 3
  }
 }
```

## <a name="next-steps"></a>次の手順
Azure Arc for Kubernetes 上の Event Grid でサポートされる宛先とハンドラーについて詳しくは、[Kubernetes 上の Event Grid - イベント ハンドラー](event-handlers.md)に関する記事を参照してください。
