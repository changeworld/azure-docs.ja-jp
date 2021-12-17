---
title: Azure Event Grid イベント ハンドラー
description: Azure Event Grid でサポートされるイベント ハンドラーについて説明します。 Azure Automation、Functions、Event Hubs、ハイブリッド接続、Logic Apps、Service Bus、Queue storage、Webhook。
ms.topic: conceptual
ms.date: 09/15/2021
ms.openlocfilehash: e741a3500c9b6a0ecda9a6dcc6e2d3514df45edc
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128659193"
---
# <a name="event-handlers-in-azure-event-grid"></a>Azure Event Grid のイベント ハンドラー
イベント ハンドラーは、イベントの送信先となる場所です。 ハンドラーは、さらにいくつかのアクションを行ってイベントを処理します。 一部の Azure サービスは、イベントを処理するように自動的に構成されます。 イベント処理のための Webhook を使うこともできます。 Webhook は、イベント処理用に Azure でホストする必要はありません。 Event Grid では、HTTPS webhook エンドポイントのみがサポートされます。

## <a name="supported-event-handlers"></a>サポートされているイベント ハンドラー
サポートされているイベント ハンドラーを次に示します。 

- [Webhook](handler-webhooks.md)。 Azure Automation Runbook と Logic Apps は Webhook を介してサポートされます。 
- [Azure Functions](handler-functions.md)
- [Event Hubs](handler-event-hubs.md)
- [Service Bus のキューとトピック](handler-service-bus.md)
- [ハイブリッド接続のリレー](handler-relay-hybrid-connections.md)
- [Storage キュー](handler-storage-queues.md)

## <a name="next-steps"></a>次のステップ
- Event Grid の概要については、[Event Grid の紹介](overview.md)に関する記事を参照してください。
