---
title: "Azure Service Bus のメッセージ数 | Microsoft Docs"
description: "Azure Service Bus のメッセージ数の取得"
services: service-bus-messaging
documentationcenter: 
author: clemensv
manager: timlt
editor: 
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: sethm
ms.openlocfilehash: e6524fe056ee2a1d81c9cccf257008b2369352b1
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2018
---
# <a name="message-counters"></a>メッセージ カウンター

.NET Framework SDK の Azure Resource Manager および Service Bus [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) API を使用して、キュー、およびサブスクリプションに保持されているメッセージの数を取得できます。

PowerShell を使用して、次のように、メッセージ数を取得できます。

```powershell
(Get-AzureRmServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue).CountDetails
```

## <a name="message-count-details"></a>メッセージ数の詳細

現在展開されているものより、処理により多くのリソースを必要とするバックログをキューが生成するかどうか判断するために、アクティブなメッセージの数を把握することが有効です。 次のカウンターの詳細は、[MessageCountDetails](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails) クラスにあります。

-   [ActiveMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.activemessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_ActiveMessageCount): キューまたはサブスクリプションにある、アクティブな状態および配信の準備ができているメッセージの数。
-   [DeadLetterMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.deadlettermessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_DeadLetterMessageCount): 配信不能キュー内のメッセージの数。
-   [ScheduledMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.scheduledmessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_ScheduledMessageCount): スケジュールされた状態のメッセージの数。
-   [TransferDeadLetterMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.transferdeadlettermessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_TransferDeadLetterMessageCount): 別のキューまたはトピックへの転送に失敗し、転送配信不能キューに移動されたメッセージの数。
-   [TransferMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.transfermessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_TransferMessageCount): 別のキューまたはトピックへの転送が保留されたメッセージの数。

アプリケーションで、キューの長さに基づいてリソースを拡張する必要がある場合は、正確に測定された間隔で実行する必要があります。 メッセージ カウンターの取得は、メッセージ ブローカー内で多くのリソースを必要とする操作であり、頻繁に実行すると、エンティティ パフォーマンスに直接的な悪影響を及ぼします。

## <a name="next-steps"></a>次の手順

Service Bus メッセージングの詳細については、次のトピックをご覧ください。

* [Service Bus の基礎](service-bus-fundamentals-hybrid-solutions.md)
* [Service Bus のキュー、トピック、サブスクリプション](service-bus-queues-topics-subscriptions.md)
* [Service Bus キューの使用](service-bus-dotnet-get-started-with-queues.md)
* [Service Bus のトピックとサブスクリプションの使用方法](service-bus-dotnet-how-to-use-topics-subscriptions.md)