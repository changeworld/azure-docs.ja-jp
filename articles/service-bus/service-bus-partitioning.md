<properties 
   pageTitle="パーティション分割されたメッセージング エンティティ | Microsoft Azure"
   description="複数のメッセージ ブローカーを使用したメッセージング エンティティをパーティション分割する方法について説明します。"
   services="service-bus"
   documentationCenter="na"
   authors="sethmanheim"
   manager="timlt"
   editor="tysonn" /> 
<tags 
   ms.service="service-bus"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/18/2015"
   ms.author="sethm" />

# パーティション分割されたメッセージング エンティティ

Azure Service Bus では、メッセージを処理する複数のメッセージ ブローカーとメッセージを格納する複数のメッセージング ストアを採用しています。従来のキューまたはトピックは、単一のメッセージ ブローカーで処理されて 1 つのメッセージング ストアに格納されます。Service Bus では、キューまたはトピックを複数のメッセージ ブローカーとメッセージング ストアにパーティション分割することもできます。そのため、パーティション分割されたキューまたはトピックの全体のスループットは、単一のメッセージ ブローカーまたはメッセージング ストアのパフォーマンスによって制限されなくなりました。また、1 つのメッセージング ストアが一時的に停止しても、パーティション分割されたキューまたはトピックは使用することができます。パーティション分割されたキューとトピックには、トランザクションやセッションのサポートなど、あらゆる高度な Service Bus 機能を含めることができます。

Service Bus 内部の詳細については、「[Service Bus アーキテクチャ][]」のトピックを参照してください。

## パーティション分割されたキューとトピック

パーティション分割されたキューまたはトピックはそれぞれ、複数のフラグメントで構成されます。フラグメントはそれぞれ別々のメッセージング ストアに格納され、別々のメッセージ ブローカーで処理されます。パーティション分割されたキューまたはトピックにメッセージが送信されると、Service Bus はそのメッセージをいずれかのフラグメントに割り当てます。対象のフラグメントは、Service Bus によってランダムに選択されるか、送信側で指定できるパーティション キーによって選択されます。

クライアントがパーティション分割されたキューまたはパーティション分割されたトピックのサブスクリプションからメッセージを受信する場合、Service Bus はすべてのフラグメントを照会してメッセージを探し、いずれかのメッセージング ストアから返された最初のメッセージを受信側に返します。Service Bus は、追加の受信要求を受信すると、他のメッセージをキャッシュして返します。受信側クライアントはパーティション分割を認識しません。パーティション分割されたキューやトピックの動作 (読み込み、完了、遅延、配信不能、プリフェッチなど) は、クライアント側には通常のエンティティの動作と同一に見えます。

パーティション分割されたキューやトピックとのメッセージの送受信に追加費用は発生しません。

## パーティション分割の有効化

Microsoft Azure Service Bus でパーティション分割されたキューとトピックを使用するには、Azure SDK Version 2.2 以降を使用するか、HTTP 要求で `api-version=2013-10` を指定します。

Service Bus のキューとトピックは、1 GB、2 GB、3 GB、4 GB、5 GB で作成できます (既定値は 1 GB)。パーティション分割が有効な場合、Service Bus は指定された GB 数に対して 1 GB ごとに 16 個のパーティションを作成します。そのため、サイズが 5 GB のキューを作成すると、パーティションが 16 個であるため、最大キュー サイズは 5 x 16 = 80 GB になります。パーティション分割したキューまたはトピックの最大サイズは、[Azure クラシック ポータル][]の各エントリで確認できます。

パーティション分割されたキューまたはトピックを作成する方法は複数あります。ご使用のアプリケーションからキューまたはトピックを作成する際に、[QueueDescription.EnablePartitioning][] プロパティまたは [TopicDescription.EnablePartitioning][] プロパティをそれぞれ **true** に設定することで、キューまたはトピックのパーティション分割を有効にできます。これらのプロパティは、キューまたはトピックの作成時に設定する必要があります。既存のキューまたはトピックでこれらのプロパティを変更することはできません。次に例を示します。

```
// Create partitioned topic
NamespaceManager ns = NamespaceManager.CreateFromConnectionString(myConnectionString);
TopicDescription td = new TopicDescription(TopicName);
td.EnablePartitioning = true;
ns.CreateTopic(td);
```

また、Visual Studio または [Azure クラシック ポータル][]で、パーティション分割されたキューまたはトピックを作成することもできます。ポータルで新しいキューまたはトピックを作成する場合は、キューまたはトピック ウィンドウの **[構成]** タブにある **[パーティション分割の有効化]** チェック ボックスをオンにします。Visual Studio で作成する場合は、**[新しいキュー]** または **[新しいトピック]** ダイアログ ボックスの **[パーティション分割の有効化]** チェック ボックスをオンにします。

## パーティション キーの使用

パーティション分割されたキューまたはトピックにメッセージがエンキューされると、Service Bus はパーティション キーが存在するかどうかを調べます。パーティション キーが見つかった場合は、そのキーに基づいてフラグメントを選択します。パーティション キーが見つからない場合は、内部アルゴリズムに基づいてフラグメントを選択します。

### パーティション キーを使用する場合

セッションやトランザクションなどの一部のシナリオでは、メッセージを特定のフラグメントに格納する必要があります。このようなシナリオでは必ず、パーティション キーを使用する必要があります。同じパーティション キーを使用するメッセージはすべて、同じフラグメントに割り当てられます。そのフラグメントが一時的に使用できなくなると、Service Bus はエラーを返します。

以下に示すように、シナリオに応じて、さまざまなメッセージ プロパティがパーティション キーとして使用されます。

**SessionId**: メッセージで [BrokeredMessage.SessionId][] プロパティが設定されている場合、Service Bus はこのプロパティをパーティション キーとして使用します。このように、同じセッションに属するメッセージはすべて、同じメッセージ ブローカーによって処理されます。この方法により、Service Bus はメッセージの順序と、セッション状態の整合性を保証することができます。

**PartitionKey**: メッセージで [BrokeredMessage.PartitionKey][] プロパティは設定されているが、[BrokeredMessage.SessionId][] プロパティは設定されていない場合、Service Bus は [PartitionKey][] プロパティをパーティション キーとして使用します。メッセージで [SessionId][] プロパティと [PartitionKey][] プロパティが両方とも設定されている場合、この 2 つのプロパティの値は同じである必要があります。[PartitionKey][] プロパティが [SessionId][] プロパティとは異なる値に設定されている場合、Service Bus は **InvalidOperationException** 例外を返します。セッションを認識しないトランザクション メッセージを送信側が送信する場合は、[PartitionKey][] プロパティを使用する必要があります。パーティション キーを使用することにより、トランザクション内で送信されるすべてのメッセージを同じメッセージング ブローカーで処理することができます。

**MessageId**: キューまたはトピックで [QueueDescription.RequiresDuplicateDetection][] プロパティが **true** に設定され、[BrokeredMessage.SessionId][] プロパティまたは [BrokeredMessage.PartitionKey][] プロパティが設定されていない場合は、[BrokeredMessage.MessageId][] プロパティがパーティション キーとしての役割を果たします(送信元のアプリケーションでメッセージ ID が割り当てられていない場合は、Microsoft .NET および AMQP のライブラリによって自動的にメッセージ ID が割り当てられます)。 この場合は、同じメッセージのすべてのコピーが同じメッセージ ブローカーによって処理されます。これにより、Service Bus は重複したメッセージを検出し削除することができるようになります。[QueueDescription.RequiresDuplicateDetection][] プロパティが **true** に設定されていない場合、Service Bus は [MessageId][] プロパティをパーティション キーと見なしません。

### パーティション キーを使用しない場合

パーティション キーが存在しない場合、Service Bus は、ラウンドロビン方式で、パーティション分割されたキューまたはトピックのすべてのフラグメントにメッセージを配信します。選択されたフラグメントが使用できない場合、Service Bus はメッセージを別のフラグメントに割り当てます。このように、メッセージング ストアが一時的に使用できなくても送信操作は成功します。

Service Bus が別のフラグメントにメッセージをエンキューする時間を確保するには、メッセージの送信元のクライアントによって指定される [MessagingFactorySettings.OperationTimeout][] 値が 15 秒を超える必要があります。[OperationTimeout][] プロパティを既定値の 60 秒に設定することをお勧めします。

パーティション キーは、メッセージを特定のフラグメントに "ピン留め" します。このフラグメントを保持しているメッセージング ストアを使用できない場合、Service Bus はエラーを返します。パーティション キーが存在しない場合、Service Bus は異なるフラグメントを選択できるので、操作は成功します。したがって、パーティション キーが必要でない場合は、パーティション キーを指定しないことをお勧めします。

## 高度なトピック: パーティション分割されたエンティティでのトランザクションの使用

トランザクションの一部として送信されるメッセージでは、パーティション キーを指定する必要があります。指定できるプロパティは、[BrokeredMessage.SessionId][]、[BrokeredMessage.PartitionKey][]、[BrokeredMessage.MessageId][] のいずれかです。同じトランザクションの一部として送信されるすべてのメッセージで、同じパーティション キーを指定する必要があります。トランザクション内でパーティション キーなしのメッセージを送信しようとすると、Service Bus は **InvalidOperationException** 例外を返します。同じトランザクション内で異なるパーティション キーを持つ複数のメッセージを送信しようとすると、Service Bus は **InvalidOperationException** 例外を返します。次に例を示します。

```
CommittableTransaction committableTransaction = new CommittableTransaction();
using (TransactionScope ts = new TransactionScope(committableTransaction))
{
    BrokeredMessage msg = new BrokeredMessage("This is a message");
    msg.PartitionKey = "myPartitionKey";
    messageSender.Send(msg); 
    ts.Complete();
}
committableTransaction.Commit();
```

パーティション キーとなるプロパティが設定されている場合、Service Bus はメッセージを特定のフラグメントに "ピン留め" します。この動作は、トランザクションが使用されているかどうかにかかわらず起こります。必要でない限り、パーティション キーを指定しないことをお勧めします。

## パーティション分割されたエンティティでのセッションの使用

トランザクション メッセージを、セッションを認識するトピックまたはキューに送信するには、メッセージで [BrokeredMessage.SessionId][] プロパティが設定されている必要があります。[BrokeredMessage.PartitionKey][] プロパティも指定されている場合は、このプロパティの値が [SessionId][] プロパティの値と同じである必要があります。これらが異なる場合、Service Bus は **InvalidOperationException** 例外を返します。

通常の (パーティション分割されていない) キューまたはトピックと異なり、単一のトランザクションを使用して複数のメッセージを別々のセッションに送信することはできません。これを試みると、Service Bus は **InvalidOperationException** 例外を返します。次に例を示します。

```
CommittableTransaction committableTransaction = new CommittableTransaction();
using (TransactionScope ts = new TransactionScope(committableTransaction))
{
    BrokeredMessage msg = new BrokeredMessage("This is a message");
    msg.SessionId = "mySession";
    messageSender.Send(msg); 
    ts.Complete();
}
committableTransaction.Commit();
```

## パーティション分割されたエンティティでのメッセージの自動転送

Azure Service Bus では、パーティション分割されたエンティティを送信元または送信先とするメッセージの自動転送、およびパーティション分割されたエンティティ間でのメッセージの自動転送をサポートしています。メッセージの自動転送を有効にするには、ソース キューまたはサブスクリプションで [QueueDescription.ForwardTo][] プロパティを設定する必要があります。メッセージでパーティション キー ([SessionId][]、[PartitionKey][]、[MessageId][]) が指定されている場合、そのパーティション キーが出力先エンティティで使用されます。

## パーティション分割されたエンティティの制限事項

現行の実装において、Service Bus はパーティション分割されたキューまたはトピックに以下の制限を適用します。

-   パーティション分割されたキューまたはトピックは、AMQP だけでなく、SBMP または HTTP/HTTPS を介して使用できます。

-   パーティション分割されたキューまたはトピックは、単一のトランザクションの別々のセッションに属するメッセージの送信はサポートしていません。

-   Service Bus は、現在、名前空間あたり最大 100 のパーティション分割されたキューまたはトピックをサポートします。パーティション分割された各キューまたはトピックは、名前空間あたり 10,000 エンティティのクォータに対してカウントされます。

-   パーティション分割されたキューとトピックは、Service Bus for Windows Server Version 1.0 および 1.1 ではサポートされていません。

## 次のステップ

メッセージング エンティティのパーティション分割の詳細については、Service Bus のパーティション分割されたキューとトピックの AMQP 1.0 のサポートについての説明 (近日公開予定!) を参照してください。

  [Service Bus アーキテクチャ]: service-bus-architecture.md
  [Azure クラシック ポータル]: http://manage.windowsazure.com
  [QueueDescription.EnablePartitioning]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.enablepartitioning.aspx
  [TopicDescription.EnablePartitioning]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicdescription.enablepartitioning.aspx
  [BrokeredMessage.SessionId]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.sessionid.aspx
  [BrokeredMessage.PartitionKey]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.partitionkey.aspx
  [SessionId]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.sessionid.aspx
  [PartitionKey]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.partitionkey.aspx
  [QueueDescription.RequiresDuplicateDetection]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.requiresduplicatedetection.aspx
  [BrokeredMessage.MessageId]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx
  [MessageId]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx
  [QueueDescription.RequiresDuplicateDetection]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.requiresduplicatedetection.aspx
  [MessagingFactorySettings.OperationTimeout]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout.aspx
  [OperationTimeout]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout.aspx
  [QueueDescription.ForwardTo]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.forwardto.aspx
  [AMQP 1.0 support for Service Bus partitioned queues and topics]: service-bus-partitioned-entities-amqp-overview.md

<!---HONumber=AcomDC_1203_2015-->