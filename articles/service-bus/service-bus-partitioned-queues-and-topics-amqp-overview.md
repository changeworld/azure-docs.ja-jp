<properties 
	pageTitle="パーティション分割された Service Bus のキューおよびトピックでの AMQP 1.0 のサポート |Microsoft Azure" 
	description="Advanced Message Queuing Protocol (AMQP) 1.0 を Service Bus のパーティション分割されたキューおよびトピックで使用する方法を学習します。" 
	services="service-bus" 
	documentationCenter=".net" 
	authors="hillaryc" 
	manager="hillaryc" 
	editor="hillaryc"/>

<tags 
	ms.service="service-bus" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="07/21/2015" 
	ms.author="hillaryc"/>



# パーティション分割された Service Bus のキューおよびトピックでの AMQP 1.0 のサポート 

Azure Service Bus では、Azure Service Bus の**パーティション分割されたキューおよびトピック**で Advanced Message Queuing Protocol (**AMQP**) 1.0 をサポートするようになりました。

**AMQP** とは、さまざまなプログラミング言語を使用して、クロス プラットフォーム アプリケーションを開発できるようにするオープン標準のメッセージ キューイング プロトコルです。Service Bus での AMQP の一般的なサポートの詳細については、「[Service Bus での AMQP 1.0 サポート](service-bus-amqp-overview.md)」を参照してください。

パーティション分割されたエンティティとも呼ばれる**パーティション分割されたキューおよびトピック**は、従来のパーティション分割されていないキューおよびトピックよりも、高い可用性、信頼性およびスループットを提供します。パーティション分割されたエンティティの詳細については、「[メッセージング エンティティのパーティション分割](https://msdn.microsoft.com/library/azure/dn520246.aspx)」を参照してください。

パーティション分割されたキューおよびトピックとの通信が可能な AMQP 1.0 プロトコルの追加により、Service Bus のパーティション分割されたエンティティが提供する、高い可用性、信頼性およびスループットが使用された、相互運用性のあるアプリケーションの構築が可能になります。

### パーティション分割されたキューでの AMQP の使用

キューは、一時的な切り離し、負荷平準化、負荷分散、および疎結合を必要とするシナリオに便利です。キューでは、パブリッシャーがキューにメッセージを送信し、コンシューマーがメッセージを 1 回のみ受信できるキューでメッセージを受信します。これの典型例が、在庫管理システムに直接ではなく、キューにデータを発行する POS 端末による在庫システムです。在庫管理システムはその後、在庫補充を管理するために任意の時間にデータを消費します。これには、在庫管理システムを常にオンラインにする必要がないなど、いくつかの利点があります。Service Bus キューの詳細については、「[Service Bus キューを使用するアプリケーションを作成する](https://msdn.microsoft.com/library/azure/hh689723.aspx)」を参照してください。

アプリケーションの可用性、信頼性、およびスループットは、複数のメッセージ ブローカーおよびメッセージング ストアにパーティション分割されたキューによりさらに向上します。

#### パーティション分割されたキューの作成

パーティション分割されたキューは、Azure ポータルと Service Bus SDK で作成できます。パーティション分割されたキューを作成するには、QueueDescription インスタンスで EnablePartitioning プロパティを true に設定します。次のコード スニペットでは、Service Bus SDK を使用してそれを作成する方法を示します。
 
	// Create partitioned queue
	var nm = NamespaceManager.CreateFromConnectionString(myConnectionString);
	var queueDescription = new QueueDescription("myQueue");
	queueDescription.EnablePartitioning = true;
	nm.CreateQueue(queueDescription);

#### AMQP を使用したメッセージの送受信

AMQP をプロトコルとして使用したパーティション分割されたキューへのメッセージの送受信は、次のコード スニペットのとおり、TransportType を TransportType.Amqp に設定して行います。

	// Sending and receiving messages to and from a Queue
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


### パーティション分割されたトピックでの AMQP の使用

キューと似たトピックは、一時的な切り離し、負荷平準化、負荷分散、および疎結合を必要とするシナリオに便利です。トピックはキューとは異なり、同じメッセージのコピーを複数のサブスクライバーにルーティングできます。トピックでは、パブリッシャーはトピックにメッセージを送信し、コンシューマーはサブスクリプションからメッセージを受信します。在庫システムの例では、POS 端末がトピックにデータを発行します。その後、在庫管理システムが、サブスクリプションからメッセージを受信します。さらに、監視システムが別のサブスクリプションから、同じメッセージを受信します。Service Bus のトピックの詳細については、「[Service Bus トピックとサブスクリプションを使用するアプリケーションの作成](https://msdn.microsoft.com/library/azure/hh699844.aspx)」を参照してください。

これらのトピックおよびそのサブスクリプションは、複数のメッセージ ブローカーおよびメッセージング ストアでパーティション分割されているため、アプリケーションの可用性、信頼性、およびスループットは、パーティション分割されたトピックによりさらに向上します。

#### パーティション分割されたトピックの作成

パーティション分割されたトピックは、Azure ポータルと Service Bus SDK で作成できます。パーティション分割されたトピックを作成するには、TopicDescription インスタンスで EnablePartitioning プロパティを true に設定します。次のコード スニペットでは、Service Bus SDK を使用してそれを作成する方法を示します。
	
	// Create partitioned topic
	var nm = NamespaceManager.CreateFromConnectionString(myConnectionString);
	var topicDescription = new TopicDescription("myTopic");
	topicDescription.EnablePartitioning = true;
	nm.CreateTopic(topicDescription);

	var subscriptionDescription = new SubscriptionDescription("myTopic", "mySubscription");
	nm.CreateSubscription(subscriptionDescription);

#### AMQP を使用したメッセージの送受信

AMQP をプロトコルとして使用したパーティション分割されたトピックへのメッセージの送信と、パーティション分割されたトピックのサブスクリプションからのメッセージの受信は、次のコード スニペットのとおり、TransportType を TransportType.Amqp に設定して行います。

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


## 参照

*    [メッセージング エンティティのパーティション分割](https://msdn.microsoft.com/library/azure/dn520246.aspx)
*    [OASIS Advanced Message Queuing Protocol (AMQP) バージョン 1.0](http://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf)
*    [Service Bus での AMQP 1.0 サポート](service-bus-amqp-overview.md)
*    [Service Bus AMQP: 開発者ガイド]("https://msdn.microsoft.com/library/azure/jj841071.aspx")
*    [Service Bus と AMQP 1.0 で Java Message Service (JMS) API を使用する方法に関するページ](service-bus-java-how-to-use-jms-api-amqp.md)
*    [Service Bus .NET API で AMQP 1.0 を使用する方法](service-bus-dotnet-advanced-message-queuing.md)

<!---HONumber=Sept15_HO4-->