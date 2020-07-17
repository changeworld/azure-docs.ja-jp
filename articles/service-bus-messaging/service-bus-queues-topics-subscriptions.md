---
title: Azure Service Bus のメッセージング - キュー、トピック、およびサブスクリプション
description: この記事では、Azure Service Bus メッセージング エンティティ (キュー、トピック、サブスクリプション) の概要について説明します。
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: deeebf56d6e2f4ccfac37c70170a0d1cb4d272a9
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2020
ms.locfileid: "86119175"
---
# <a name="service-bus-queues-topics-and-subscriptions"></a>Service Bus のキュー、トピック、サブスクリプション

Microsoft Azure Service Bus は、信頼性の高いメッセージ キュー機能や永続的なパブリッシュ/サブスクライブ メッセージング機能など、クラウドベースのメッセージ指向ミドルウェアの一連のテクノロジをサポートしています。 このような "仲介型" メッセージング機能は、分離されたメッセージング機能と考えることができ、Service Bus メッセージング ワークロードを使用するパブリッシュ/サブスクライブ、一時的な切り離し、負荷分散のシナリオをサポートしています。 分離型通信には、クライアントとサーバーを必要に応じて接続し、非同期に操作を実行できるなど多数の利点があります。

Service Bus のメッセージング機能の中核を形成するメッセージング エンティティは、キュー、トピックおよびサブスクリプション、ルール/アクション、ルール/アクションです。

## <a name="queues"></a>キュー

キューでは、コンシューマーが競合している場合のメッセージ配信に*先入れ先出し法 (FIFO)* を使用します。 つまり、受信者は、通常はキューに追加された順序でメッセージを受信して処理します。このとき、1 つのメッセージ コンシューマーだけが、各メッセージを受信して処理します。 キューを使用する主な利点は、アプリケーション コンポーネントの "一時的な切り離し" を達成することです。 言い換えると、メッセージはキューに永続的に格納されるため、プロデューサー (送信者) とコンシューマー (受信者) が同時にメッセージを送受信する必要はありません。 さらに、プロデューサーは、メッセージの処理と送信を続ける場合、メッセージ コンシューマーからの応答を待つ必要がありません。

関連する利点として "負荷平準化" があります。これにより、プロデューサーとコンシューマーは異なるレートでメッセージを送受信できます。 多くのアプリケーションでは、システム負荷が時間の経過とともに変化しますが、各作業単位に必要な処理時間は通常一定に保たれます。 仲介のメッセージ プロデューサーとコンシューマーでキューを使用する場合、コンシューマー側アプリケーションに必要なのは、ピーク時の負荷ではなく平均的な負荷を処理できるようにプロビジョニングしておくことだけです。 キューの深さは、受信の負荷の変化に応じて増減します。 この機能は、アプリケーション負荷への対応に必要なインフラストラクチャに関する費用を直接節約できます。 負荷の増大に合わせて、キューからの読み取りのためにワーカー プロセスを追加できます。 各メッセージは、ワーカー プロセスの中の 1 つのプロセスによって処理されます。 さらに、このプルベースの負荷分散では、各ワーカー コンピューターがそれぞれ独自の最大レートでメッセージをプルするため、それらのコンピューターの処理能力が異なる場合であっても使用を最適化できます。 このパターンは、"競合コンシューマー" パターンと呼ばれることもあります。

キューを使用してメッセージ プロデューサーとメッセージ コンシューマーの間を仲介すると、必然的にコンポーネント間の結び付きは緩くなります。 プロデューサーとコンシューマーは相互に認識しないため、プロデューサーに影響することなく、コンシューマーをアップグレードできます。

### <a name="create-queues"></a>キューの作成

キューは、[Azure Portal](service-bus-quickstart-portal.md)、[PowerShell](service-bus-quickstart-powershell.md)[CLI](service-bus-quickstart-cli.md)、または [Resource Manager テンプレート](service-bus-resource-manager-namespace-queue.md)を使用して作成します。 その後、[QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient) オブジェクトを使用して、メッセージの送信と受信を行います。

キューの簡単な作成方法と、キューに対するメッセージの送信と受信を行う方法については、各メソッドの[クイック スタート](service-bus-quickstart-portal.md)を参照してください。 キューの使用方法の詳細なチュートリアルについては、「[Service Bus のキューの使用](service-bus-dotnet-get-started-with-queues.md)」を参照してください。

作業用サンプルについては、GitHub の [BasicSendReceiveUsingQueueClient サンプル](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/Microsoft.Azure.ServiceBus/BasicSendReceiveUsingQueueClient)を参照してください。

### <a name="receive-modes"></a>受信モード

Service Bus がメッセージを受信する 2 つの異なるモードを指定できます。*ReceiveAndDelete* または *PeekLock* です。 [ReceiveAndDelete](/dotnet/api/microsoft.azure.servicebus.receivemode) モードでは、受信は単発の操作です。つまり、Service Bus はコンシューマーからの要求を受信すると、メッセージを読み取り中としてマークしてコンシューマー アプリケーションに返します。 **ReceiveAndDelete** モードは最もシンプルなモデルであり、障害発生時にアプリケーション側でメッセージを処理しないことを許容できるシナリオに最適です。 このシナリオを理解するために、コンシューマーが受信要求を発行した後で、メッセージを処理する前にクラッシュしたというシナリオを考えてみましょう。 Service Bus がメッセージを読み取り中としてマークするため、アプリケーションは、再起動してメッセージの読み取りを再開すると、クラッシュ前に読み取られていたメッセージを見落とすことになります。

[PeekLock](/dotnet/api/microsoft.azure.servicebus.receivemode) モードでは、メッセージの受信処理は 2 段階になります。これにより、メッセージが失われることを許容できないアプリケーションに対応することができます。 Service Bus は要求を受け取ると、次に読み取られるメッセージを検索して、他のコンシューマーが受信できないようロックしてから、アプリケーションにそのメッセージを返します。 アプリケーションがメッセージの処理を終えた後 (または後で処理するために確実に保存した後)、受信したメッセージに対して [CompleteAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) を呼び出して受信処理の第 2 段階を完了します。 Service Bus は、**CompleteAsync** の呼び出しを確認すると、メッセージを読み取り済みとしてマークします。

アプリケーションがなんらかの理由によってメッセージを処理できない場合には、受信したメッセージに対して ([CompleteAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) の代わりに) [AbandonAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.abandonasync) メソッドを呼び出すことができます。 このメソッドが呼び出されると、Service Bus によってメッセージのロックが解除され、同じコンシューマーまたは競合する別のコンシューマーが再度そのメッセージを受信できるようになります。 第二に、ロックに関連付けられたタイムアウトがあります。アプリケーションがクラッシュした場合など、ロックがタイムアウトになる前にアプリケーションがメッセージの処理に失敗した場合は、Service Bus によってメッセージのロックが解除され、再度受信できるようになります (基本的に既定で [AbandonAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.abandonasync) 操作を実行します)。

メッセージが処理された後、**CompleteAsync** 要求が発行される前にアプリケーションがクラッシュした場合は、アプリケーションが再起動する際にメッセージが再配信されます。 多くの場合、このプロセスは "*少なくとも 1 回*" の処理と呼ばれます。つまり、各メッセージは少なくとも 1 回は処理されますが、 特定の状況では、同じメッセージが再配信される可能性があります。 重複した処理を許容できないシナリオでは、メッセージの [MessageId](/dotnet/api/microsoft.azure.servicebus.message.messageid) プロパティに基づいて実現可能な重複を検出するための追加のロジックがアプリケーションで必要になります。このプロパティはメッセージが再配信されても変わりません。 この機能は "*厳密に 1 回*" の処理と呼ばれます。

## <a name="topics-and-subscriptions"></a>トピックとサブスクリプション

各メッセージが 1 つのコンシューマーによって処理されるキューとは異なり、"*トピック*" と "*サブスクリプション*" では、"*パブリッシュ/サブスクライブ*" パターンを使用した 1 対多形式の通信が行われます。 パブリッシュされた各メッセージは、これは多数の受信者を対象とする場合に便利であり、トピックに登録している各サブスクリプションで使用できます。 メッセージは、トピックに送信された後、サブスクリプションごとに設定できるフィルター規則に基づいて、関連付けられている 1 つ以上のサブスクリプションに配信されます。 サブスクリプションでは、追加のフィルターを使用して、受信するメッセージを限定できます。 メッセージは、キューに送信される場合と同じようにトピックに送信されますが、トピックからメッセージを直接受信することはありません。 代わりに、メッセージはサブスクリプションから受信します。 トピック サブスクリプションは、トピックに送信されたメッセージのコピーを受け取る仮想キューのようなものです。 メッセージは、キューから受信する場合と同じ方法でサブスクリプションから受信します。

比較として、キューのメッセージ送信機能はそのままトピックに相当し、メッセージ受信機能はサブスクリプションに相当します。 特に、この機能は、サブスクリプションでは、競合コンシューマー、一時的な切り離し、負荷平滑化、負荷分散など、キューに関してこのセクションで既に説明したのと同じパターンがサポートされることを意味します。

### <a name="create-topics-and-subscriptions"></a>トピックとサブスクリプションを作成する

トピックの作成は、前のセクションで説明したキューの作成と似ています。 メッセージの送信は、[TopicClient](/dotnet/api/microsoft.azure.servicebus.topicclient) クラスを使用して行います。 メッセージを受信するには、トピックに対して 1 つまたは複数のサブスクリプションを作成します。 キューの場合に似ていますが、[QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient) オブジェクトの代わりに [SubscriptionClient](/dotnet/api/microsoft.azure.servicebus.subscriptionclient) オブジェクトを使用して、サブスクリプションからメッセージを受信します。 サブスクリプション クライアントを作成し、トピックの名前とサブスクリプションの名前、さらに必要に応じて受信モードをパラメーターとして渡します。

完全な作業用サンプルについては、GitHub の [BasicSendReceiveUsingTopicSubscriptionClient サンプル](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/Microsoft.Azure.ServiceBus/BasicSendReceiveUsingTopicSubscriptionClient)を参照してください。

### <a name="rules-and-actions"></a>ルールとアクション

多くのシナリオでは、特性のあるメッセージは、異なる方法で処理する必要があります。 この処理を実現するために、目的のプロパティを持つメッセージを検索し、該当するプロパティに特定の変更を行うようにサブスクリプションを構成できます。 Service Bus のサブスクリプションには、トピックに送信されたすべてのメッセージが表示されますが、仮想サブスクリプション キューにコピーできるのは、これらのメッセージの一部のみです。 このフィルター処理を行うには、サブスクリプション フィルターを使用します。 このような変更は、"*フィルター アクション*" と呼ばれます。 サブスクリプションを作成する場合、メッセージのシステム プロパティ (**Label** など) とカスタム アプリケーション プロパティ (**StoreName** など) の両方で機能するフィルター式を指定できます。この場合、SQL フィルター式は省略可能です。SQL フィルター式を指定しない場合は、サブスクリプションで定義されている任意のフィルター アクションが、そのサブスクリプションのすべてのメッセージに対して実行されます。

完全な作業用サンプルについては、GitHub の [TopicSubscriptionWithRuleOperationsSample サンプル](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/Microsoft.Azure.ServiceBus/TopicSubscriptionWithRuleOperationsSample)を参照してください。

使用可能なフィルター値の詳細については、[SqlFilter](/dotnet/api/microsoft.azure.servicebus.sqlfilter) クラスと [SqlRuleAction](/dotnet/api/microsoft.azure.servicebus.sqlruleaction) クラスのドキュメントを参照してください。

## <a name="java-message-service-jms-20-entities-preview"></a>Java Message Service (JMS) 2.0 のエンティティ (プレビュー)

Azure Service Bus Premium に接続して [Azure Service Bus JMS ライブラリ](https://search.maven.org/artifact/com.microsoft.azure/azure-servicebus-jms)を利用するクライアント アプリケーションは、以下のエンティティを活用できます。

### <a name="queues"></a>キュー

JMS のキューは、上で説明した従来の Service Bus キューと意味的に比較できます。

キューを作成するには、`JMSContext` クラス内で次のメソッドを利用します。

```java
Queue createQueue(String queueName)
```

### <a name="topics"></a>トピック

JMS のトピックは、上で説明した従来の Service Bus トピックと意味的に比較できます。

トピックを作成するには、`JMSContext` クラス内で次のメソッドを利用します。

```java
Topic createTopic(String topicName)
```

### <a name="temporary-queues"></a>一時キュー

クライアント アプリケーションが、アプリケーションの有効期間の間存在する一時的なエンティティを必要とする場合は、一時キューを使用できます。 これらは、[要求-応答](https://www.enterpriseintegrationpatterns.com/patterns/messaging/RequestReply.html)パターンで利用されます。

一時キューを作成するには、`JMSContext` クラス内で次のメソッドを利用します。

```java
TemporaryQueue createTemporaryQueue()
```

### <a name="temporary-topics"></a>一時トピック

一時キューと同様に、一時トピックは、アプリケーションの有効期間の間存在する一時的なエンティティを通してパブリッシュ/サブスクライブを可能にするために存在します。

一時トピックを作成するには、`JMSContext` クラス内で次のメソッドを利用します。

```java
TemporaryTopic createTemporaryTopic()
```

### <a name="java-message-service-jms-subscriptions"></a>Java Message Service (JMS) のサブスクリプション

これは前述のサブスクリプションと意味的に似ています (つまり、トピックに関して存在してパブリッシュ/サブスクライブのセマンティクスを実現します) が、Java Message Service の仕様で、特定のサブスクリプションに対する属性として、**共有**、**非共有**、**永続的**、**非永続的**という概念が導入されています。

> [!NOTE]
> 下記のサブスクリプションは、Azure Service Bus の Premium サービス レベルで、[Azure Service Bus JMS ライブラリ](https://search.maven.org/artifact/com.microsoft.azure/azure-servicebus-jms)を使用して Azure Service Bus に接続するクライアント アプリケーションでのプレビューのために使用できます。
>
> パブリック プレビューでは、Azure portal を使用してこれらのサブスクリプションを作成することはできません。
>

#### <a name="shared-durable-subscriptions"></a>共有の永続的サブスクリプション

共有の永続的サブスクリプションは、アプリケーションがサブスクリプションからのデータを常にアクティブに使用しているかどうかにかかわらず、あるトピックに関してパブリッシュされたすべてのメッセージを受信し、アプリケーションで処理する場合に使用されます。

これは共有サブスクリプションであるため、Service Bus から受信するよう認証されたすべてのアプリケーションで、サブスクリプションからの受信が可能です。

共有の永続的サブスクリプションを作成するには、`JMSContext` クラスに対して次のメソッドを使用します。

```java
JMSConsumer createSharedDurableConsumer(Topic topic, String name)

JMSConsumer createSharedDurableConsumer(Topic topic, String name, String messageSelector)
```

`JMSContext` クラスに対して `unsubscribe` メソッドを使用して削除されていない限り、共有の永続的サブスクリプションは存在し続けます。

```java
void unsubscribe(String name)
```

#### <a name="unshared-durable-subscriptions"></a>非共有の永続的サブスクリプション

共有の永続的サブスクリプションと同様に、非共有の永続的サブスクリプションは、アプリケーションがサブスクリプションからのデータを常にアクティブに使用しているかどうかにかかわらず、あるトピックに関してパブリッシュされたすべてのメッセージを受信し、アプリケーションで処理する場合に使用されます。

ただし、これは非共有のサブスクリプションであるため、サブスクリプションを作成したアプリケーションのみがそのサブスクリプションから受信できます。

非共有の永続的サブスクリプションを作成するには、`JMSContext` クラスから以下のメソッドを使用します。 

```java
JMSConsumer createDurableConsumer(Topic topic, String name)

JMSConsumer createDurableConsumer(Topic topic, String name, String messageSelector, boolean noLocal)
```

> [!NOTE]
> `noLocal` 機能は現在サポートされておらず、無視されます。
>

`JMSContext` クラスに対して `unsubscribe` メソッドを使用して削除されていない限り、非共有の永続的サブスクリプションは存在し続けます。

```java
void unsubscribe(String name)
```

#### <a name="shared-non-durable-subscriptions"></a>共有の非永続的サブスクリプション

複数のクライアント アプリケーションが 1 つのサブスクリプションからメッセージを受信して処理する必要がある場合、サブスクリプションからのデータを積極的に使用/受信するようになるまでだけ、共有の非永続的サブスクリプションが使用されます。

サブスクリプションは永続的でないため、保持されません。 それに対するアクティブなコンシューマーが存在しない場合、メッセージはこのサブスクリプションによって受信されません。

共有の非永続的サブスクリプションを作成するには、下のメソッドに示したように、`JMSContext` クラスから `JmsConsumer` を作成します。

```java
JMSConsumer createSharedConsumer(Topic topic, String sharedSubscriptionName)

JMSConsumer createSharedConsumer(Topic topic, String sharedSubscriptionName, String messageSelector)
```

共有の非永続的サブスクリプションは、そこからデータを受信するアクティブなコンシューマーが存在するようになるまで存在し続けます。

#### <a name="unshared-non-durable-subscriptions"></a>非共有の非永続的サブスクリプション

クライアント アプリケーションがサブスクリプションからメッセージを受信して処理する必要がある場合、サブスクリプションからのデータを積極的に使用するようになるまでだけ、非共有の非永続的サブスクリプションが使用されます。 このサブスクリプションに存在できるコンシューマーは 1 つだけです。つまり、そのサブスクリプションを作成したクライアントです。

サブスクリプションは永続的でないため、保持されません。 それに対するアクティブなコンシューマーが存在しない場合、メッセージはこのサブスクリプションによって受信されません。

非共有の非永続的サブスクリプションを作成するには、下のメソッドに示したように、JMSContext クラスから `JMSConsumer` を作成します。 

```java
JMSConsumer createConsumer(Destination destination)

JMSConsumer createConsumer(Destination destination, String messageSelector)

JMSConsumer createConsumer(Destination destination, String messageSelector, boolean noLocal)
```

> [!NOTE]
> `noLocal` 機能は現在サポートされておらず、無視されます。
>

非共有の非永続的サブスクリプションは、そこからデータを受信するアクティブなコンシューマーが存在するようになるまで存在し続けます。

#### <a name="message-selectors"></a>メッセージ セレクター

通常の Service Bus サブスクリプションには**フィルターとアクション**があるのと同様に、JMS サブスクリプションには**メッセージ セレクター**があります。

メッセージ セレクターは、それぞれの JMS サブスクリプションに対して設定することができ、メッセージのヘッダー プロパティに関するフィルター条件として存在します。 メッセージ セレクターの式に一致するヘッダー プロパティを持つメッセージだけが配信されます。 値が null または空の文字列の場合、その JMS サブスクリプション/コンシューマーにはメッセージ セレクターがないことを示しています。

## <a name="next-steps"></a>次のステップ

Service Bus のメッセージングの詳細と使用例については、次の詳細トピックをご覧ください。

* [Service Bus メッセージングの概要](service-bus-messaging-overview.md)
* [クイック スタート: Microsoft Azure portal と .NET を使用してメッセージを送受信する](service-bus-quickstart-portal.md)
* [チュートリアル:Microsoft Azure portal とトピック/サブスクリプションを使用して在庫を更新する](service-bus-tutorial-topics-subscriptions-portal.md)


