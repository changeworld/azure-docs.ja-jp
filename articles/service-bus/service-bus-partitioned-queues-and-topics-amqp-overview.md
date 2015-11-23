<properties 
	pageTitle="パーティション分割された Service Bus のキューおよびトピックでの AMQP 1.0 のサポート |Microsoft Azure" 
	description="Advanced Message Queuing Protocol (AMQP) 1.0 を Service Bus のパーティション分割されたキューおよびトピックで使用する方法を学習します。" 
	services="service-bus" 
	documentationCenter=".net" 
	authors="hillaryc" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="service-bus" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="11/05/2015" 
	ms.author="hillaryc"/>

# パーティション分割された Service Bus のキューおよびトピックでの AMQP 1.0 のサポート 

Azure Service Bus では、Service Bus の**パーティション分割されたキューおよびトピック**で Advanced Message Queuing Protocol (**AMQP**) 1.0 をサポートするようになりました。

**AMQP** とは、さまざまなプログラミング言語を使用して、クロス プラットフォーム アプリケーションを開発できるようにするオープン標準のメッセージ キューイング プロトコルです。Service Bus での AMQP のサポートの概要については、「[Service Bus での AMQP 1.0 サポート](service-bus-amqp-overview.md)」を参照してください。

*パーティション分割されたエンティティ*とも呼ばれる**パーティション分割されたキューおよびトピック**は、従来のパーティション分割されていないキューおよびトピックよりも、高い可用性、信頼性およびスループットを提供します。パーティション分割されたエンティティの詳細については、「[パーティション分割されたメッセージング エンティティ](service-bus-partitioning.md)」を参照してください。

パーティション分割されたキューおよびトピックと通信する AMQP 1.0 プロトコルの追加により、Service Bus のパーティション分割されたエンティティが提供する、高い可用性、信頼性およびスループットが使用された、相互運用性のあるアプリケーションの構築が可能になります。

## パーティション分割されたキューでの AMQP の使用

キューは、一時的な切り離し、負荷平準化、負荷分散、および疎結合を必要とするシナリオに便利です。キューでは、パブリッシャーがキューにメッセージを送信し、コンシューマーがキューで 1 回のみ受信できるメッセージを受信します。これの良い例が、在庫管理システムに直接ではなく、キューにデータを発行する POS 端末による在庫システムです。在庫管理システムはその後、在庫補充を管理するために任意の時間にデータを消費します。これには、在庫管理システムを常にオンラインにする必要がないなど、いくつかの利点があります。Service Bus キューの詳細については、「[Service Bus キューを使用するアプリケーションを作成する](service-bus-create-queues.md)」を参照してください。

アプリケーションの可用性、信頼性、およびスループットは、複数のメッセージ ブローカーおよびメッセージング ストアにパーティション分割されたキューによりさらに向上します。

### パーティション分割されたキューの作成

パーティション分割されたキューは、Azure クラシック ポータルと Service Bus SDK で作成できます。パーティション分割されたキューを作成するには、[QueueDescription](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.aspx) インスタンスで [EnablePartitioning](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.enablepartitioning.aspx) プロパティを **true** に設定します。次のコードでは Service Bus SDK を使用してパーティション分割されたキューを作成する方法を示します。
 
```
// Create partitioned queue
var nm = NamespaceManager.CreateFromConnectionString(myConnectionString);
var queueDescription = new QueueDescription("myQueue");
queueDescription.EnablePartitioning = true;
nm.CreateQueue(queueDescription);
```

### AMQP を使用したメッセージの送受信

AMQP をプロトコルとして使用し、パーティション分割されたキューへメッセージを送受信するには、次のコードのとおり、[TransportType](https://msdn.microsoft.com/library/azure/microsoft.servicebus.servicebusconnectionstringbuilder.transporttype.aspx) プロパティを [TransportType.Amqp](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.transporttype.aspx) に設定します。

```
// Sending and receiving messages to and from a queue
var myConnectionStringBuilder = new ServiceBusConnectionStringBuilder(myConnectionString);
myConnectionStringBuilder.TransportType = TransportType.Amqp;
string amqpConnectionString = myConnectionStringBuilder.ToString();
var queueClient = QueueClient.CreateFromConnectionString(amqpConnectionString, "myQueue");

BrokeredMessage message = new BrokeredMessage("Hello AMQP");
Console.WriteLine("Sending message {0}...", message.MessageId);
queueClient.Send(message);

var receivedMessage = queueClient.Receive();
Console.WriteLine("Received message: {0}", receivedMessage.GetBody<string>());
receivedMessage.Complete();
```

## パーティション分割されたトピックでの AMQP の使用

キューと似たトピックは、一時的な切り離し、負荷平準化、負荷分散、および疎結合を必要とするシナリオに便利です。トピックはキューとは異なり、同じメッセージのコピーを複数のサブスクライバーにルーティングできます。トピックでは、パブリッシャーはトピックにメッセージを送信し、コンシューマーはサブスクリプションからメッセージを受信します。在庫システムの例では、POS 端末がトピックにデータを発行します。その後、在庫管理システムが、サブスクリプションからメッセージを受信します。さらに、監視システムが別のサブスクリプションから、同じメッセージを受信します。Service Bus のトピックの詳細については、「[Service Bus トピックとサブスクリプションを使用するアプリケーションの作成](service-bus-create-topics-subscriptions.md)」を参照してください。

これらのトピックおよびそのサブスクリプションは、複数のメッセージ ブローカーおよびメッセージング ストアでパーティション分割されているため、アプリケーションの可用性、信頼性、およびスループットは、パーティション分割されたトピックによりさらに向上します。

### パーティション分割されたトピックの作成

パーティション分割されたトピックは、Azure クラシック ポータルと Service Bus SDK で作成できます。パーティション分割されたトピックを作成するには、[TopicDescription](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicdescription.aspx) インスタンスで [EnablePartitioning](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicdescription.enablepartitioning.aspx) プロパティを **true** に設定します。次のコードでは、Service Bus SDK を使用してパーティション分割されたトピックを作成する方法を示します。
	
```
// Create partitioned topic
var nm = NamespaceManager.CreateFromConnectionString(myConnectionString);
var topicDescription = new TopicDescription("myTopic");
topicDescription.EnablePartitioning = true;
nm.CreateTopic(topicDescription);

var subscriptionDescription = new SubscriptionDescription("myTopic", "mySubscription");
nm.CreateSubscription(subscriptionDescription);
```

### AMQP を使用したメッセージの送受信

AMQP をプロトコルとして使用し、パーティション分割されたトピックのサブスクリプションへメッセージを送受信するには、次のコード スニペットのとおり、[TransportType](https://msdn.microsoft.com/library/azure/microsoft.servicebus.servicebusconnectionstringbuilder.transporttype.aspx) プロパティを [TransportType.Amqp](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.transporttype.aspx) に設定します。

```
// Sending and receiving messages to a topic and from a subscription
var myConnectionStringBuilder = new ServiceBusConnectionStringBuilder(myConnectionString);
myConnectionStringBuilder.TransportType = TransportType.Amqp;
string amqpConnectionString = myConnectionStringBuilder.ToString();
	
var topicClient = TopicClient.CreateFromConnectionString(amqpConnectionString, "myTopic");
BrokeredMessage message = new BrokeredMessage("Hello AMQP");
Console.WriteLine("Sending message {0}...", message.MessageId);
topicClient.Send(message);
	
var subcriptionClient = SubscriptionClient.CreateFromConnectionString(amqpConnectionString, "myTopic", "mySubscription");
var receivedMessage = subcriptionClient.Receive();
Console.WriteLine("Received message: {0}", receivedMessage.GetBody<string>());
receivedMessage.Complete();
```

## 次のステップ

パーティション分割されたメッセージング エンティティの詳細については、次を参照してください。

*    [パーティション分割されたメッセージング エンティティ](service-bus-partitioning.md)
*    [OASIS Advanced Message Queuing Protocol (AMQP) バージョン 1.0](http://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf)
*    [Service Bus での AMQP 1.0 サポート](service-bus-amqp-overview.md)
*    [Service Bus と AMQP 1.0 で Java Message Service (JMS) API を使用する方法に関するページ](service-bus-java-how-to-use-jms-api-amqp.md)
*    [Service Bus .NET API で AMQP 1.0 を使用する方法](service-bus-dotnet-advanced-message-queuing.md)

<!---HONumber=Nov15_HO3-->