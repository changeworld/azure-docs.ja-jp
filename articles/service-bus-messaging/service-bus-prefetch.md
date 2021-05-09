---
title: Azure Service Bus プリフェッチ メッセージ | Microsoft Docs
description: Azure Service Bus メッセージをプリフェッチすることでパフォーマンスを向上します。 メッセージは、アプリケーションが要求する前に、ローカルですぐに取得できるようになっています。
ms.topic: article
ms.date: 04/23/2021
ms.openlocfilehash: 992fa9228d3cf8bafb3d2ea9110b515ff4b56498
ms.sourcegitcommit: aba63ab15a1a10f6456c16cd382952df4fd7c3ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/25/2021
ms.locfileid: "107989092"
---
# <a name="prefetch-azure-service-bus-messages"></a>Azure Service Bus メッセージのプリフェッチ
公式の Service Bus クライアントのいずれかに対して "*プリフェッチ*" 機能を有効にすると、受信側は、アプリケーションが最初に要求したものよりも多くのメッセージを取得します (最大で指定したプリフェッチ数)。 JavaScript および TypeScript クライアントでは、この機能はまだサポートされていないことに注意してください。

メッセージがアプリケーションに返されると、クライアントはさらにメッセージをバックグラウンドで取得し、プリフェッチ バッファーに格納します。

## <a name="enabling-prefetch"></a>プリフェッチの有効化
プリフェッチ機能を有効にするには、キューまたはサブスクリプション クライアントのプリフェッチ数を 0 より大きい数値に設定します。 値を 0 に設定すると、プリフェッチが解除されます。 

# <a name="net"></a>[.NET](#tab/dotnet)
最新の Azure.Messaging.ServiceBus ライブラリを使用している場合、[ServiceBusReceiver](/dotnet/api/azure.messaging.servicebus.servicebusreceiver.prefetchcount#Azure_Messaging_ServiceBus_ServiceBusReceiver_PrefetchCount) および [ServiceBusProcessor](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.prefetchcount#Azure_Messaging_ServiceBus_ServiceBusProcessor_PrefetchCount) オブジェクトでプリフェッチ数プロパティを設定できます。 

Service Bus 用の古い .NET クライアント ライブラリ (Microsoft.Azure.ServiceBus) を使用している場合、[MessageReceiver](/dotnet/api/microsoft.servicebus.messaging.messagereceiver.prefetchcount)、[QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient.prefetchcount#Microsoft_Azure_ServiceBus_QueueClient_PrefetchCount)、または [SubscriptionClient](/dotnet/api/microsoft.azure.servicebus.subscriptionclient.prefetchcount) でプリフェッチ数プロパティを設定できます。
 
# <a name="java"></a>[Java](#tab/java)
最新の azure-messaging-servicebus ライブラリを使用している場合、[ServiceBusReceiver](/dotnet/api/azure.messaging.servicebus.servicebusreceiver.prefetchcount#Azure_Messaging_ServiceBus_ServiceBusReceiver_PrefetchCount) および [ServiceBusProcessor](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.prefetchcount#Azure_Messaging_ServiceBus_ServiceBusProcessor_PrefetchCount) オブジェクトでプリフェッチ数プロパティを設定できます。 

Service Bus 用の古い Java クライアント ライブラリ (azure-servicebus) を使用している場合、[MessageReceiver](/java/api/com.microsoft.azure.servicebus.imessagereceiver.setprefetchcount#com_microsoft_azure_servicebus_IMessageReceiver_setPrefetchCount_int_)、[QueueClient](/java/api/com.microsoft.azure.servicebus.queueclient.setprefetchcount#com_microsoft_azure_servicebus_QueueClient_setPrefetchCount_int_)、または [SubscriptionClient](/java/api/com.microsoft.azure.servicebus.subscriptionclient.setprefetchcount#com_microsoft_azure_servicebus_SubscriptionClient_setPrefetchCount_int_) でプリフェッチ数プロパティを設定できます。
 
# <a name="python"></a>[Python](#tab/python)

[azure.servicebus.ServiceBusReceiver](/python/api/azure-servicebus/azure.servicebus.servicebusreceiver) または [azure.servicebus.aio.ServiceBusReceiver](/python/api/azure-servicebus/azure.servicebus.aio.servicebusreceiver) で **prefetch_count** を設定できます。

---

> [!NOTE]
> JavaScript SDK は、**プリフェッチ** 機能をサポートしていません。 

メッセージがプリフェッチ バッファーに格納されると、後続の受信呼び出しは、バッファーから直ちに実行されます。 領域が使用可能になると、バッファーがバックグラウンドで補充されます。 配信するメッセージがない場合は、受信操作によってバッファーが空になり、想定どおり、ブロックを待機します。

## <a name="why-is-prefetch-not-the-default-option"></a>プリフェッチが既定のオプションではない理由
プリフェッチを使用すると、アプリケーションが要求する前にメッセージをローカルで取得できるよう用意することで、メッセージ フローを高速化できます。 このスループットの向上は、以下のメリットとデメリットを比較したうえで、アプリケーションの作成者がその使用を明示的に決定した結果として得られるものです。

[receive-and-delete](message-transfers-locks-settlement.md#receiveanddelete) モードでは、プリフェッチ バッファーに取得されたすべてのメッセージはキューで使用できなくなります。 メッセージは、アプリケーションに受信されるまで、メモリ内のプリフェッチ バッファーにのみ存在します。 アプリケーションがメッセージを受信する前に終了すると、それらのメッセージは失われ、復元できなくなります。

[peek-lock](message-transfers-locks-settlement.md#peeklock) 受信モードでは、プリフェッチ バッファーにフェッチされたメッセージが、ロック状態のバッファーに取得されます。 ロックの有効期限のためにタイムアウト クロックが設定されます。 プリフェッチ バッファーのサイズが大きく、処理に時間がかかり、その結果、メッセージがプリフェッチ バッファー内にある間、あるいはアプリケーションがメッセージを処理している間にメッセージのロックの有効期限が切れると、アプリケーションでの処理において混乱を招くイベントが発生する場合があります。

アプリケーションは、期限が切れた、またはロックの有効期限がもうすぐ切れるメッセージを取得する場合があります。 この場合、アプリケーションはメッセージを処理することもありますが、ロックの有効期限のためにメッセージを完了できないことを検出します。 アプリケーションは `LockedUntilUtc` プロパティ (ブローカーとローカル コンピューターのクロックのずれによって規定されます) をチェックします。 メッセージのロックの有効期限が切れている場合は、アプリケーションはメッセージを無視する必要があり、メッセージに対して API 呼び出しを行わないようにする必要があります。 メッセージの有効期限が切れていない一方で有効期限が近づいている場合、ロックを更新して、別の既定のロック期間まで延長できます。

プリフェッチ バッファーでロックの有効期限が自動的に切れると、メッセージは破棄済みとして扱われ、もう一度キューから取得できるようになります。 それにより、メッセージはプリフェッチ バッファーにフェッチされ、末尾に配置される場合があります。 メッセージの有効期限中、プリフェッチ バッファーが通常通り処理されない場合は、メッセージのプリフェッチが繰り返されますが、メッセージは使用可能な (ロックが有効) 状態で効果的に配信されることはなく、最終的には、最大配信回数を超えて配信不能キューに入れられます。

メッセージの処理に高い信頼性が必要で、処理にかなりの作業量と時間が必要な場合、プリフェッチ機能をまったく使用しないか、慎重に使用することをお勧めします。

高いスループットを必要としており、メッセージ処理が一般に安価である場合は、プリフェッチによって大きなスループットの利点がもたらされます。

最大プリフェッチ数とキューまたはサブスクリプションに構成されているロック期間は、ロックのタイムアウトが、プリフェッチ バッファーの最大サイズに 1 メッセージ分を足した累計予想メッセージ処理時間以上になるようにバランスを調整する必要があります。 同時に、ロック タイムアウトは、メッセージの最大存続時間を超えるような長さにしてはなりません。これは、メッセージが誤って破棄された場合、再配信前にロックの有効期限が切れるようにする必要があるためです。

## <a name="next-steps"></a>次のステップ

Azure Service Bus の機能については、使用する言語のサンプルを試してみてください。 

- [.NET 用の Azure Service Bus クライアント ライブラリのサンプル (最新)](/samples/azure/azure-sdk-for-net/azuremessagingservicebus-samples/) 
- [Java 用の Azure Service Bus クライアント ライブラリのサンプル (最新)](/samples/azure/azure-sdk-for-java/servicebus-samples/)
- [Python 用の Azure Service Bus クライアント ライブラリのサンプル](/samples/azure/azure-sdk-for-python/servicebus-samples/)
- [JavaScript 用の Azure Service Bus クライアント ライブラリのサンプル](/samples/azure/azure-sdk-for-js/service-bus-javascript/)
- [TypeScript 用の Azure Service Bus クライアント ライブラリのサンプル](/samples/azure/azure-sdk-for-js/service-bus-typescript/)

以前の .NET および Java クライアント ライブラリのサンプルについては、以下を参照してください。
- [.NET 用の Azure Service Bus クライアント ライブラリのサンプル (レガシ)](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/) - **プリフェッチ** のサンプルを参照してください。 
- [Java 用の Azure Service Bus クライアント ライブラリのサンプル (レガシ)](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/azure-servicebus) - **プリフェッチ** のサンプルを参照してください。 
