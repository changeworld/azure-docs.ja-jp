---
title: Azure Service Bus - メッセージの参照
description: Service Bus メッセージを参照してピークすると、Azure Service Bus クライアントは、キューまたはサブスクリプションのすべてのメッセージを列挙できます。
ms.topic: article
ms.date: 03/29/2021
ms.openlocfilehash: f4943685f03eccb1c3b8da079973cf083bdcc416
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/31/2021
ms.locfileid: "106090309"
---
# <a name="message-browsing"></a>メッセージの読み取り

Service Bus クライアントは、メッセージを読み取るか、ピークすることにより、診断とデバッグの目的で、キューまたはサブスクリプションのすべてのメッセージを列挙できます。

**キュー** に対するピーク操作では、即時取得に使用できるものだけでなく、キュー内のすべてのメッセージが返されます。 **サブスクリプション** に対するピーク操作では、サブスクリプション メッセージ ログ内のスケジュール済みメッセージ以外のすべてのメッセージが返されます。 

消費され、有効期限の切れたメッセージは、非同期 "ガベージ コレクション" を実行することよって消去されます。 この手順は、メッセージの有効期限が切れた後にただちに実行されるとは限りません。 そのため、ピーク操作は有効期限が既に切れているメッセージを返す可能性があります。 これらのメッセージは、次回キューまたはサブスクリプションで受信操作が呼び出されたときに削除されるか、または配信不能となります。 キューから遅延メッセージを回復しようとする場合、この動作に留意してください。 期限切れのメッセージは、ピークによって返された場合でも、他の方法では通常の取得ができなくなります。 ピークはログの現在の状態を反映する診断ツールであるため、これらのメッセージが返されるのは設計によるものです。

ピークでは、他の受信者が現在処理しているロックされたメッセージも返されます。 ただし、ピークでは切断されたスナップショットが返されるため、メッセージのロック状態はピーク メッセージでは確認できません。

## <a name="peek-apis"></a>Peek API
## <a name="azuremessagingservicebus"></a>[Azure.Messaging.ServiceBus](#tab/dotnet)
[PeekMessageAsync](/dotnet/api/azure.messaging.servicebus.servicebusreceiver.peekmessageasync) および [PeekMessagesAsync](/dotnet/api/azure.messaging.servicebus.servicebusreceiver.peekmessagesasync) メソッドは、受信オブジェクト `ServiceBusReceiver`、`ServiceBusSessionReceiver` に存在します。 ピークは、キュー、サブスクリプション、およびそれらの各配信不能キューで動作します。

繰り返し呼び出されると、`PeekMessageAsync` は、キューまたはサブスクリプション ログ内のすべてのメッセージを、使用可能なシーケンス番号が最も低いものから最も高いものの順に列挙します。 これは、メッセージがエンキューされた順序であり、メッセージが最終的に取得される可能性のある順序ではありません。
PeekMessagesAsync は、複数のメッセージを取得し、列挙型として返します。 使用可能なメッセージがない場合は、列挙型オブジェクトは、null ではなく、空になります。

また、[fromSequenceNumber](/dotnet/api/microsoft.servicebus.messaging.eventposition.fromsequencenumber) パラメーターに開始地点となるシーケンス番号を設定し、さらに列挙するためのパラメーターを指定せずにメソッドを再度呼び出すこともできます。 `PeekMessagesAsync` 関数も同様に機能しますが、一度にすべてのメッセージ セットを取得します。


## <a name="microsoftazureservicebus"></a>[Microsoft.Azure.ServiceBus](#tab/dotnetold)
[Peek/PeekAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.peekasync#Microsoft_Azure_ServiceBus_Core_MessageReceiver_PeekAsync) および [PeekBatch/PeekBatchAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatchasync#Microsoft_ServiceBus_Messaging_QueueClient_PeekBatchAsync_System_Int64_System_Int32_) メソッドは、受信オブジェクト `MessageReceiver`、`MessageSession` に存在します。 ピークは、キュー、サブスクリプション、およびそれらの各配信不能キューで動作します。

繰り返し呼び出されると、`Peek` は、キューまたはサブスクリプション ログ内のすべてのメッセージを、使用可能なシーケンス番号が最も低いものから最も高いものの順に列挙します。 これは、メッセージがエンキューされた順序であり、メッセージが最終的に取得される可能性のある順序ではありません。

[PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch#Microsoft_ServiceBus_Messaging_QueueClient_PeekBatch_System_Int32_) は、複数のメッセージを取得し、列挙型として返します。 使用可能なメッセージがない場合は、列挙型オブジェクトは、null ではなく、空になります。

[SequenceNumber](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.sequencenumber#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_SequenceNumber) を設定してメソッドのオーバーロードを使用し、パラメーターのないメソッドのオーバーロードを呼び出して、さらに列挙することもできます。 **PeekBatch** 関数も同様に機能しますが、一度にすべてのメッセージ セットを取得します。


---

## <a name="next-steps"></a>次のステップ

Service Bus メッセージングの詳細については、次のトピックをご覧ください。

* [Service Bus のキュー、トピック、サブスクリプション](service-bus-queues-topics-subscriptions.md)
* [Service Bus キューの使用](service-bus-dotnet-get-started-with-queues.md)
* [Service Bus のトピックとサブスクリプションの使用方法](service-bus-dotnet-how-to-use-topics-subscriptions.md)
