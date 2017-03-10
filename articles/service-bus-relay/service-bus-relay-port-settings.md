---
title: "Azure Service Bus の WCF リレー ポート設定 | Microsoft Docs"
description: "Service Bus の WCF リレー ポート値の詳細を示します。"
services: service-bus-relay
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/08/2017
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: f222caa272b86ff42873df09561ca4f6c6e6aa26
ms.openlocfilehash: ba499a98960b0cf2f865e2d349003cd8cb65b526
ms.lasthandoff: 11/28/2016


---

# <a name="azure-relay-port-settings"></a>Azure リレー ポート設定

Service Bus の WCF リレー バインドのポート値に必要な構成を次の表に示します。

## <a name="ports"></a>ポート
  
|バインド|トランスポート セキュリティ|ポート|  
|-------------|------------------------|----------|  
|[BasicHttpRelayBinding クラス](/dotnet/api/microsoft.servicebus.basichttprelaybinding) (クライアント)|はい|HTTPS| 
| |" |なし|HTTP|  
|[BasicHttpRelayBinding クラス](/dotnet/api/microsoft.servicebus.basichttprelaybinding) (サービス)|使用できるのは|9351/HTTP|  
|[NetEventRelayBinding クラス](/dotnet/api/microsoft.servicebus.neteventrelaybinding) (クライアント)|はい|9351/HTTPS|  
||" |なし|9350/HTTP|  
|[NetEventRelayBinding クラス](/dotnet/api/microsoft.servicebus.neteventrelaybinding) (サービス)|使用できるのは|9351/HTTP|  
|[NetTcpRelayBinding クラス](/dotnet/api/microsoft.servicebus.nettcprelaybinding) (クライアント/サービス)|使用できるのは|5671/9352/HTTP (ハイブリッドを使用している場合は 9352/9353)|  
|[NetOnewayRelayBinding クラス](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) (クライアント)|はい|9351/HTTPS|  
||" |なし|9350/HTTP|  
|[NetOnewayRelayBinding クラス](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) (サービス)|使用できるのは|9351/HTTP|  
|[WebHttpRelayBinding クラス](/dotnet/api/microsoft.servicebus.webhttprelaybinding) (クライアント)|はい|HTTPS|  
||" |なし|HTTP|  
|[WebHttpRelayBinding クラス](/dotnet/api/microsoft.servicebus.webhttprelaybinding) (サービス)|使用できるのは|9351/HTTP|  
|[WS2007HttpRelayBinding クラス](/dotnet/api/microsoft.servicebus.ws2007httprelaybinding) (クライアント)|はい|HTTPS|  
||" |なし|HTTP|  
|[WS2007HttpRelayBinding クラス](/dotnet/api/microsoft.servicebus.ws2007httprelaybinding) (サービス)|使用できるのは|9351/HTTP|

## <a name="next-steps"></a>次のステップ
Service Bus メッセージングの詳細については、次のトピックをご覧ください。

* [Service Bus の基礎](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md)
* [Service Bus のキュー、トピック、サブスクリプション](../service-bus-messaging/service-bus-queues-topics-subscriptions.md)
* [Service Bus キューの使用方法](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md)
* [Service Bus のトピックとサブスクリプションの使用方法](../service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions.md)

