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
	ms.date="07/08/2016" 
	ms.author="hillaryc;sethm"/>

# パーティション分割された Service Bus のキューとトピックにおける AMQP 1.0 のサポート 

Azure Service Bus では、**パーティション分割された Service Bus のキューとトピック**で Advanced Message Queuing Protocol (**AMQP**) 1.0 がサポートされるようになりました。

**AMQP** とは、さまざまなプログラミング言語を使用して、クロス プラットフォーム アプリケーションを開発できるようにするオープン標準のメッセージ キューイング プロトコルです。Service Bus での AMQP のサポートの概要については、「[Service Bus での AMQP 1.0 サポート](service-bus-amqp-overview.md)」を参照してください。

**パーティション分割されたキューとトピック**は、*パーティション分割されたエンティティ*とも呼ばれます。これらは、パーティション分割されていない従来のキューとトピックよりも、可用性、信頼性、スループットが優れています。パーティション分割されたエンティティの詳細については、「[パーティション分割されたメッセージング エンティティ](service-bus-partitioning.md)」を参照してください。

パーティション分割されたキューおよびトピックと通信するための AMQP 1.0 プロトコルの追加により、Service Bus のパーティション分割されたエンティティによって提供される高い可用性、信頼性、スループットを活用できる、相互運用性のあるアプリケーションの構築が可能になります。

OASIS AMQP 技術仕様に基づいて Service Bus が実装および構築される方法について説明している、詳細な回線レベルの AMQP 1.0 プロトコル ガイドについては、「[Azure Service Bus と Event Hubs における AMQP 1.0 プロトコル ガイド](service-bus-amqp-protocol-guide.md)」を参照してください。

## パーティション分割されたキューでの AMQP の使用

キューは、一時的な切り離し、負荷平準化、負荷分散、および疎結合を必要とするシナリオに便利です。キューでは、パブリッシャーがキューにメッセージを送信します。コンシューマーは、メッセージが 1 回しか受信できない状況でキューからのメッセージを受信します。この良い例が、POS 端末が在庫管理システムにデータを直接発行するのではなく、キューにデータを発行する在庫システムです。その後、在庫管理システムは在庫補充を管理するためにいつでもデータを使用できます。これには、在庫管理システムを常にオンラインにしておく必要がないなど、いくつかの利点があります。Service Bus キューの詳細については、「[Service Bus キューを使用するアプリケーションを作成する](service-bus-create-queues.md)」を参照してください。

アプリケーションの可用性、信頼性、スループットは、複数のメッセージ ブローカーとメッセージング ストアにパーティション分割されたキューによりさらに向上します。

### パーティション分割されたキューの作成

パーティション分割されたキューは、[Azure クラシック ポータル][]と Service Bus SDK を使用して作成できます。パーティション分割されたキューを作成するには、[QueueDescription](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.aspx) インスタンスの [EnablePartitioning](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.enablepartitioning.aspx) プロパティを **true** に設定します。次のコードでは Service Bus SDK を使用してパーティション分割されたキューを作成する方法を示します。
 
```
// Create partitioned queue
var nm = NamespaceManager.CreateFromConnectionString(myConnectionString);
var queueDescription = new QueueDescription("myQueue");
queueDescription.EnablePartitioning = true;
nm.CreateQueue(queueDescription);
```

### AMQP を使用したメッセージの送受信

AMQP をプロトコルとして使用して、パーティション分割されたキューとの間でメッセージを送受信するには、次のコードのように [TransportType](https://msdn.microsoft.com/library/azure/microsoft.servicebus.servicebusconnectionstringbuilder.transporttype.aspx) プロパティを [TransportType.Amqp](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.transporttype.aspx) に設定します。

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

トピックは概念的にはキューと類似したものですが、同じメッセージのコピーを複数の "*サブスクリプション*" にルーティングできます。トピックでは、パブリッシャーはトピックにメッセージを送信し、コンシューマーはサブスクリプションからメッセージを受信します。在庫システムの POS のシナリオでは、端末がトピックにデータを発行します。その後、在庫管理システムがサブスクリプションからメッセージを受信します。さらに、監視システムが別のサブスクリプションから、同じメッセージを受信します。Service Bus のトピックとサブスクリプションの詳細については、「[Service Bus のトピックとサブスクリプションを使用するアプリケーションを作成する](service-bus-create-topics-subscriptions.md)」を参照してください。

キューと同様に、これらのトピックとそのサブスクリプションは複数のメッセージ ブローカーとメッセージング ストアでパーティション分割されているため、アプリケーションの可用性、信頼性、およびスループットは、パーティション分割されたトピックによりさらに向上します。

### パーティション分割されたトピックの作成

パーティション分割されたトピックは、[Azure クラシック ポータル][]と Service Bus SDK を使用して作成できます。パーティション分割されたトピックを作成するには、[TopicDescription](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicdescription.aspx) インスタンスで [EnablePartitioning](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicdescription.enablepartitioning.aspx) プロパティを **true** に設定します。次のコードでは、Service Bus SDK を使用してパーティション分割されたトピックを作成する方法を示します。
	
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

AMQP をプロトコルとして使用して、パーティション分割されたトピックのサブスクリプションとの間でメッセージを送受信するには、次のコードのように [TransportType](https://msdn.microsoft.com/library/azure/microsoft.servicebus.servicebusconnectionstringbuilder.transporttype.aspx) プロパティを [TransportType.Amqp](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.transporttype.aspx) に設定します。

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

AMQP のほか、パーティション分割されたメッセージング エンティティの詳細については、次のページを参照してください。

*    [パーティション分割されたメッセージング エンティティ](service-bus-partitioning.md)
*    [OASIS Advanced Message Queuing Protocol (AMQP) バージョン 1.0](http://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf)
*    [Service Bus での AMQP 1.0 サポート](service-bus-amqp-overview.md)
*    [Azure Service Bus と Event Hubs における AMQP 1.0 プロトコル ガイド](service-bus-amqp-protocol-guide.md)
*    [Service Bus と AMQP 1.0 で Java Message Service (JMS) API を使用する方法に関するページ](service-bus-java-how-to-use-jms-api-amqp.md)
*    [Service Bus .NET API で AMQP 1.0 を使用する方法](service-bus-dotnet-advanced-message-queuing.md)

[Azure クラシック ポータル]: http://manage.windowsazure.com

<!---HONumber=AcomDC_0713_2016-->