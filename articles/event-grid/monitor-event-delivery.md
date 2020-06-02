---
title: Azure Event Grid メッセージ配信の監視
description: この記事では、Azure portal を使用して、Azure Event Grid メッセージの配信状態を確認する方法について説明します。
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 7a01ab91fe84aaa1fe55018754eddbf8b8f89643
ms.sourcegitcommit: b396c674aa8f66597fa2dd6d6ed200dd7f409915
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/07/2020
ms.locfileid: "82890849"
---
# <a name="monitor-event-grid-message-delivery"></a>Event Grid メッセージ配信の監視 

この記事では、ポータルを使用してイベント配信の状態を表示する方法について説明します。

Event Grid は、持続性のある配信を提供します。 各サブスクリプションに対して、最低 1 回は各メッセージを配信します。 イベントは、各サブスクリプションの登録済みの Webhook にすぐに送信されます。 1 回目の配信で、Webhook がイベントの受信を 60 秒以内に確認しなかった場合、Event Grid はそのイベントの配信を再試行します。

イベント配信と再試行については、「[Event Grid によるメッセージ配信と再試行](delivery-and-retry.md)」をご覧ください。

## <a name="delivery-metrics"></a>配信のメトリック

ポータルでは、イベント メッセージ配信の状態のメトリックを表示します。

トピックについて、メトリックの一部を次に示します。

* **[発行成功]** : イベントは正常にトピックに送信され、2xx 応答で処理されました。
* **[発行失敗]** : イベントはトピックに送信されましたが、エラー コードにより拒否されました。
* **[一致しない]** : イベントはトピックに正常に発行されましたが、イベント サブスクリプションと一致しませんでした。 イベントは破棄されました。

サブスクリプションについて、メトリックの一部を次に示します。

* **[配信成功]** : イベントは正常にサブスクリプションのエンドポイントに配信され、2xx 応答を受信しました。
* **[配信失敗]** : サービスで配信が試行され、イベント ハンドラーから成功の 2xx コードを返されないたびに、 **[配信失敗]** カウンターが増加します。 同じイベントを複数回配信しようとして失敗した場合は、エラーが発生するたびに、 **[配信失敗]** カウンターが増加します。
* **[期限切れのイベント]** : イベントは配信されず、すべての再試行が送信済みになりました。 イベントは破棄されました。
* **[一致するイベント]** : トピックにあるイベントがイベント サブスクリプションと一致しました。

    > [!NOTE]
    > メトリックの完全な一覧については、「[Azure Event Grid でサポートされているメトリック](metrics.md)」を参照してください。

## <a name="event-subscription-status"></a>イベント サブスクリプションの状態

イベント サブスクリプションのメトリックを表示するには、サブスクリプションの種類または特定のリソースのサブスクリプションによって検索できます。

イベント サブスクリプションの種類による検索は、 **[すべてのサービス]** を選択します。

![[すべてのサービス] を選択する](./media/monitor-event-delivery/all-services.png)

**イベント グリッド** を検索し、利用可能なオプションから **[Event Grid サブスクリプション]** を選択します。

![イベント サブスクリプションの検索](./media/monitor-event-delivery/search-and-select.png)

イベントの種類、サブスクリプション、および場所別にフィルター処理します。 表示するサブスクリプションの **[メトリック]** を選択します。

![イベント サブスクリプションのフィルター処理](./media/monitor-event-delivery/filter-events.png)

イベント トピックおよびサブスクリプションのメトリックを表示します。

![イベント メトリックの表示](./media/monitor-event-delivery/subscription-metrics.png)

特定のリソースのメトリックを検索するには、そのリソースを選択します。 次に**イベント** を選択します。

![リソースのイベントを選択します](./media/monitor-event-delivery/select-events.png)

サブスクリプションのリソースのメトリックが表示されます。

## <a name="custom-event-status"></a>イベントの状態のカスタマイズ

カスタム トピックを発行済みの場合は、そのトピックのメトリックを表示できます。 トピックが含まれているリソース グループを選択して、トピックを選択します。

![カスタム トピックの選択](./media/monitor-event-delivery/select-custom-topic.png)

カスタム イベント トピックのメトリックを表示します。

![イベント メトリックの表示](./media/monitor-event-delivery/custom-topic-metrics.png)

## <a name="set-alerts"></a>アラートの設定

アラートは、カスタム トピックやイベント ドメインのトピックおよびドメイン レベルのメトリックに設定できます。 [概要] ブレードで、アラート ルールを表示、管理、および作成するために、左側にある **[アラート]** を選択します。 [Azure Monitor アラートの詳細情報](../azure-monitor/platform/alerts-overview.md)

![イベント メトリックの表示](./media/monitor-event-delivery/select-alerts.png)

## <a name="next-steps"></a>次のステップ

* イベント配信と再試行については、「[Event Grid によるメッセージ配信と再試行](delivery-and-retry.md)」をご覧ください。
* Event Grid の概要については、[Event Grid の紹介](overview.md)に関する記事を参照してください。
* Event Grid の使用をすぐに開始するには、[Azure Event Grid でのカスタム イベントの作成とルーティング](custom-event-quickstart.md)に関する記事を参照してください。
