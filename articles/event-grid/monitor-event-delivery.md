---
title: "Azure Event Grid メッセージ配信の監視"
description: "Azure Event Grid メッセージの配信を監視する方法について説明します。"
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 01/10/2018
ms.author: tomfitz
ms.openlocfilehash: 1552174589e91c370c3b85a9af7b5cc1106cbb90
ms.sourcegitcommit: 71fa59e97b01b65f25bcae318d834358fea5224a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/11/2018
---
# <a name="monitor-event-grid-message-delivery"></a>Event Grid メッセージ配信の監視 

この記事では、ポータルを使用してイベント配信の状態を表示する方法について説明します。

Event Grid は、持続性のある配信を提供します。 各サブスクリプションに対して、最低 1 回は各メッセージを配信します。 イベントは、各サブスクリプションの登録済みの Webhook にすぐに送信されます。 1 回目の配信で、Webhook がイベントの受信を 60 秒以内に確認しなかった場合、Event Grid はそのイベントの配信を再試行します。

イベント配信と再試行については、「[Event Grid によるメッセージ配信と再試行](delivery-and-retry.md)」をご覧ください。

## <a name="delivery-metrics"></a>配信のメトリック

ポータルでは、イベント メッセージ配信の状態のメトリックを表示します。

トピックの場合、メトリックは次のようになります。

* **[発行成功]**: イベントは正常にトピックに送信され、2xx 応答で処理されました。
* **[発行失敗]**: イベントはトピックに送信されましたが、エラー コードにより拒否されました。
* **[一致しない]**: イベントはトピックに正常に発行されましたが、イベント サブスクリプションと一致しませんでした。 イベントは破棄されました。

サブスクリプションの場合、メトリックは次のようになります。

* **[配信成功]**: イベントは正常にサブスクリプションのエンドポイントに配信され、2xx 応答を受信しました。
* **[配信失敗]**: イベントはサブスクリプションのエンドポイントに送信されましたが、4xx または 5xx 応答を受信しました。
* **[Expired Events]\(イベントの有効期限切れ\)**: イベントは配信されず、すべての再試行が送信済みになりました。 イベントは破棄されました。
* **[Matched Events]\(イベント照合済み\)**: トピックのイベントがイベント サブスクリプションと一致しました。

## <a name="event-subscription-status"></a>イベント サブスクリプションの状態

イベント サブスクリプションのメトリックを表示するには、使用可能なサービスの **[Event Grid サブスクリプション]** を検索して選択します。

![イベント サブスクリプションの検索](./media/monitor-event-delivery/select-event-subscriptions.png)

イベントの種類、サブスクリプション、および場所別にフィルター処理します。 表示するサブスクリプションの **[メトリック]** を選択します。

![イベント サブスクリプションのフィルター処理](./media/monitor-event-delivery/filter-events.png)

イベント トピックおよびサブスクリプションのメトリックを表示します。

![イベント メトリックの表示](./media/monitor-event-delivery/subscription-metrics.png)

## <a name="custom-event-status"></a>イベントの状態のカスタマイズ

カスタム トピックを発行済みの場合は、そのトピックのメトリックを表示できます。 トピックが含まれているリソース グループを選択して、トピックを選択します。

![カスタム トピックの選択](./media/monitor-event-delivery/select-custom-topic.png)

カスタム イベント トピックのメトリックを表示します。

![イベント メトリックの表示](./media/monitor-event-delivery/custom-topic-metrics.png)

## <a name="next-steps"></a>次の手順

* イベント配信と再試行については、「[Event Grid によるメッセージ配信と再試行](delivery-and-retry.md)」をご覧ください。
* Event Grid の概要については、[Event Grid の紹介](overview.md)に関する記事を参照してください。
* Event Grid の使用をすぐに開始するには、[Azure Event Grid でのカスタム イベントの作成とルーティング](custom-event-quickstart.md)に関する記事を参照してください。
