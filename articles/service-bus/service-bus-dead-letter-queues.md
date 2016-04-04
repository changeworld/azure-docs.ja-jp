<properties 
    pageTitle="Service Bus の配信不能キュー | Microsoft Azure" 
    description="Azure Service Bus の配信不能キューの概要" 
    services="service-bus" 
    documentationCenter=".net" 
    authors="sethmanheim" 
    manager="timlt" 
    editor=""/>

<tags
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na" 
    ms.date="03/16/2016"
    ms.author="clemensv;sethm"/>

# Service Bus の配信不能キューの概要

Service Bus キューおよびトピック サブスクリプションでは、*配信不能キュー* (DLQ) と呼ばれるセカンダリ サブキューが提供されます。配信不能キューを明示的に作成する必要はなく、削除したり、メイン エンティティとは別に管理したりすることはできません。

配信不能キューの目的は、受信者に配信できないメッセージ、または単に処理できなかったメッセージを保持することです。したがって、メッセージを DLQ から取り出して、検査することができます。アプリケーションは演算子を利用して、問題を修正してメッセージを再送信し、エラーが発生していたという事実をログに記録してから修正処置を行います。

API とプロトコルの観点では、DLQ は他のキューとほとんど同じですが、メッセージを再送信できるのは親エンティティの配信不能ジェスチャでのみである点が異なります。また、有効期間は監視されず、DLQ のメッセージを配信不能にすることはできません。配信不能キューでは、ピーク ロック配信やトランザクション操作が完全にサポートされます。

DLQ は自動的にクリーンアップされないことに注意してください。DLQ から明示的に取得し、配信不能メッセージに対して [Complete()](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.completeasync.aspx) を呼び出すまで、メッセージは DLQ に残ります。

## DLQ にメッセージを移動する

Service Bus には、メッセージがメッセージング エンジン自体から DLQ にプッシュされる原因となるアクティビティがいくつかあります。アプリケーションは明示的にメッセージを DLQ にプッシュすることもできます。

ブローカーによってメッセージが移動され、ブローカーがメッセージに対して内部バージョンの [DeadLetter](https://msdn.microsoft.com/library/azure/hh291941.aspx) メソッドを呼び出すと、`DeadLetterReason` および `DeadLetterErrorDescription` という 2 つのプロパティが追加されます。

アプリケーションは `DeadLetterReason` プロパティに対して独自のコードを定義できますが、システムでは以下の値が設定されます。

| 条件 | DeadLetterReason | DeadLetterErrorDescription |
|---------------------------------------------------------------------------------------------------------------------------------------|-----------------------------|----------------------------------------------------------------------------------|
| Always (常に) | HeaderSizeExceeded | このストリームのサイズ クォータを超えています。 |
| !TopicDescription.<br />EnableFilteringMessagesBeforePublishing および SubscriptionDescription.<br />EnableDeadLetteringOnFilterEvaluationExceptions | exception.GetType().Name | exception.Message |
| EnableDeadLetteringOnMessageExpiration | TTLExpiredException | メッセージの有効期限が切れているため、配信不能です。 |
| SubscriptionDescription.RequiresSession | セッション id は Null です。 | セッションが有効なエンティティではセッション ID が Null のメッセージは許可されません。 |
| !dead letter queue | MaxTransferHopCountExceeded | Null |
| Application での明示的な配信不能処理 | アプリケーションで指定 | アプリケーションで指定 |

## MaxDeliveryCount の超過

キューおよびサブスクリプションにはそれぞれ [QueueDescription.MaxDeliveryCount](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.maxdeliverycount.aspx) と [SubscriptionDescription.MaxDeliveryCount](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptiondescription.maxdeliverycount.aspx) プロパティがあり、既定値は 10 です。メッセージがロック状態で配信されたが ([ReceiveMode.PeekLock](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx))、明示的に破棄されたか、ロックが期限切れになった場合は常に、メッセージの [BrokeredMessage.DeliveryCount](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.deliverycount.aspx) が増えます。[DeliveryCount](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.deliverycount.aspx) が [MaxDeliveryCount](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.maxdeliverycount.aspx) を超えると、メッセージは DLQ に移動にされ、`MaxDeliveryCountExceeded` 理由コードが示されます。

この動作を無効にすることはできませんが、[MaxDeliveryCount](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.maxdeliverycount.aspx) を非常に大きい数に設定することはできます。

## TimeToLive の超過

[QueueDescription.EnableDeadLetteringOnMessageExpiration](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.enabledeadletteringonmessageexpiration.aspx) または [SubscriptionDescription.EnableDeadLetteringOnMessageExpiration](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptiondescription.enabledeadletteringonmessageexpiration.aspx) プロパティが **true** に設定されている場合 (既定値は **false**)、期限が切れるメッセージはすべて DLQ に移動され、`TTLExpiredException` 理由コードが示されます。

期限切れメッセージのみが消去され、メイン キューまたはサブスクリプションに対してプルを実行している 1 つ以上のアクティブな受信者が存在する場合は DLQ に移動される (この動作は仕様によるものです) ことに注意してください。

## サブスクリプション ルールの処理中のエラー

[SubscriptionDescription.EnableDeadLetteringOnFilterEvaluationExceptions](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptiondescription.enabledeadletteringonfilterevaluationexceptions.aspx) プロパティがサブスクリプションに対して有効になっている場合、サブスクリプションの SQL フィルター ルールの実行時に発生したエラーはすべて問題のメッセージと共に DLQ にキャプチャされます。

## アプリケーション レベルの配信不能処理

システム指定の配信不能処理機能に加え、アプリケーションでは DLQ を使用して許容できないメッセージを明示的に拒否できます。これには、何らかのシステムの問題により適切に処理できないメッセージ、誤った形式のペイロードを保持するメッセージ、メッセージ レベルのセキュリティ スキームの使用時に認証に失敗したメッセージが含まれる場合があります。

## 例

次のコード スニペットではメッセージの受信者を作成します。メイン キューの受信ループで、コードは [Receive(TimeSpan.Zero)](https://msdn.microsoft.com/library/azure/dn130350.aspx) のメッセージを取得します。この場合、ブローカーはすぐに使用できるメッセージをすぐに返すか、結果なしで返すように求められます。コードはメッセージを受信すると、すぐに破棄し、`DeliveryCount` が増えます。システムが DLQ にメッセージを移動すると、メイン キューは空になり、ループが終了し、[ReceiveAsync](https://msdn.microsoft.com/library/azure/dn130350.aspx) は **null** を返します。

```
var receiver = await receiverFactory.CreateMessageReceiverAsync(queueName, ReceiveMode.PeekLock);
while(true)
{
    var msg = await receiver.ReceiveAsync(TimeSpan.Zero);
    if (msg != null)
    {
        Console.WriteLine("Picked up message; DeliveryCount {0}", msg.DeliveryCount);
        await msg.AbandonAsync();
    }
    else
    {
        break;
    }
}
```

## 次のステップ

Service Bus キューの詳細については、次の記事を参照してください。

- [Azure キューと Service Bus キューの比較](service-bus-azure-and-service-bus-queues-compared-contrasted.md)
- [Service Bus キュー](service-bus-dotnet-how-to-use-queues.md)の使用方法

<!---HONumber=AcomDC_0323_2016-->