<properties 
   pageTitle="Service Bus のトピックとサブスクリプションを使用するアプリケーションを作成する | Microsoft Azure"
   description="Service Bus のトピックとサブスクリプションによって提供されるパブリッシュ/サブスクライブ機能について説明します。"
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
   ms.date="09/16/2015"
   ms.author="sethm" />

# Service Bus のトピックとサブスクリプションを使用するアプリケーションを作成する

Azure Service Bus は、信頼性の高いメッセージ キュー機能や永続的なパブリッシュ/サブスクライブ メッセージング機能など、クラウドベースのメッセージ指向ミドルウェアの一連のテクノロジをサポートしています。この記事では、「[Service Bus キューを使用するアプリケーションを作成する](service-bus-create-queues.md)」の内容を基にして、Service Bus トピックによって提供されるパブリッシュ/サブスクライブ機能について説明します。

## 小売りシナリオの発展

この記事では、引き続き「[Service Bus キューを使用するアプリケーションを作成する](service-bus-create-queues.md)」で使用した小売業のシナリオを使用します。個々 の Point of Sale (POS) 端末から売上データを在庫管理システムにルーティングし、在庫管理システムではそのデータを使用して在庫を補充する時期を決定するというシナリオです。各 POS 端末は、**DataCollectionQueue** キューにメッセージを送信することによって売上データを報告します。次の図で示すように、メッセージは在庫管理システムが受け取るまでキューに留まっています。

![Service-Bus1](./media/service-bus-create-topics-subscriptions/IC657161.gif)

このシナリオを発展させるため、システムに新しい要件を追加します。つまり、店の所有者は、店の売上状況をリアルタイムで監視することを望んでいます。

この要件に対応するには、システムは売上データ ストリームを取り出す必要があります。POS 端末によって送信された各メッセージはこれまでと同じように在庫管理システムに送信しますが、各メッセージのコピーを作成し、店の所有者にダッシュボード ビューを提供するために使用できるようにします。

このように各メッセージを複数のユーザーが使用できるようにする必要がある場合は、Service Bus の*トピック*を使用できます。トピックはパブリッシュ/サブスクライブ パターンを提供し、発行された各メッセージをトピックに登録されている 1 つ以上のサブスクリプションで使用します。これに対し、キューでは、各メッセージは 1 つのコンシューマーによって受信されます。

メッセージは、キューに送信される場合と同じ方法でトピックに送信されます。ただし、メッセージをトピックから直接受信することはなく、サブスクリプションから受信します。トピックにとってのサブスクリプションは、そのトピックに送信されたメッセージのコピーを受け取る仮想キューと考えることができます。メッセージは、キューから受信する場合と同じ方法でサブスクリプションから受信します。

小売業のシナリオに戻り、キューをトピックに置き換えて、在庫管理システム コンポーネントによって使用されるサブスクリプションを追加します。システムは次のようになります。

![Service-Bus2](./media/service-bus-create-topics-subscriptions/IC657165.gif)

ここでの構成は、前のキュー ベースの設計と同じように動作します。つまり、トピックに送信されたメッセージは **Inventory** サブスクリプションにルーティングされ、そこから**在庫管理システム**がメッセージを使用します。

管理ダッシュボードをサポートするため、次に示すようにトピックに第 2 のサブスクリプションを作成します。

![Service-Bus3](./media/service-bus-create-topics-subscriptions/IC657166.gif)

この構成では、POS 端末からの各メッセージは、**Dashboard** サブスクリプションと **Inventory** サブスクリプションの両方で使用できます。

## コード

「[Service Bus キューを使用するアプリケーションを作成する](service-bus-create-queues.md)」では、Service Bus アカウントにサインアップしてサービス名前空間を作成する方法が説明されています。Service Bus の名前空間を使用するには、アプリケーションは Service Bus アセンブリ (具体的には Microsoft.ServiceBus.dll) を参照する必要があります。Service Bus 依存関係を参照する最も簡単な方法は、Service Bus の [Nuget パッケージ](https://www.nuget.org/packages/WindowsAzure.ServiceBus/)をインストールすることです。また、アセンブリは Azure SDK にも含まれます。SDK は [Azure SDK のダウンロード ページ](http://azure.microsoft.com/downloads/)からダウンロードできます。

### トピックとサブスクリプションを作成する

Service Bus メッセージング エンティティ (キューおよびトピックのパブリッシュ/サブスクライブ) の管理は、[NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) クラスを使用して行われます。特定の名前空間の [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) インスタンスを作成するには、適切な資格情報が必要です。Service Bus は、[Shared Access Signature (SAS)](service-bus-sas-overview.md) ベースのセキュリティ モデルを使用します。[TokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.aspx) クラスは、いくつかの既知のトークン プロバイダーを返すファクトリ メソッドが組み込まれたセキュリティ トークン プロバイダーを表します。SAS の資格情報を保持するには、[CreateSharedAccessSignatureTokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.createsharedaccesssignaturetokenprovider.aspx) メソッドを使用します。その後、Service Bus 名前空間のベース アドレスとトークン プロバイダーを使用して、[NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) インスタンスが作成されます。

[NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) クラスには、メッセージング エンティティの作成、列挙、削除を実行するためのメソッドが用意されています。ここでは、[NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) インスタンスを作成し、それを使用して **DataCollectionTopic** トピックを作成するコードを示します。

```
Uri uri = ServiceBusEnvironment.CreateServiceUri("sb", "test-blog", string.Empty);
string name = "RootManageSharedAccessKey";
string key = "abcdefghijklmopqrstuvwxyz";
     
TokenProvider tokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider(name, key);
NamespaceManager namespaceManager = new NamespaceManager(uri, tokenProvider);
 
namespaceManager.CreateTopic("DataCollectionTopic");
```

トピックのプロパティを設定できる [CreateTopic](https://msdn.microsoft.com/library/azure/hh293080.aspx) メソッドのオーバーロードがあることに注意してください。たとえば、トピックに送信されるメッセージの既定の有効期限 (TTL) の値を設定できます。次に、the **Inventory** サブスクリプションと **Dashboard** サブスクリプションを追加します。

```
namespaceManager.CreateSubscription("DataCollectionTopic", "Inventory");
namespaceManager.CreateSubscription("DataCollectionTopic", "Dashboard");
```

### トピックにメッセージを送信する

Service Bus のエンティティに対して実行時の操作を行う場合 (メッセージの送受信など)、アプリケーションではまず [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx) オブジェクトを作成する必要があります。[NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) クラスと同じように、[MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx) インスタンスもサービスの名前空間のベース アドレスとトークン プロバイダーから作成されます。

```
MessagingFactory factory = MessagingFactory.Create(uri, tokenProvider);
```

Service Bus トピックに送信されたメッセージおよび Service Bus トピックから受信したメッセージは、[BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx) クラスのインスタンスになります。このクラスは、標準的なプロパティのセット ([Label](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.label.aspx)、[TimeToLive](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.timetolive.aspx) などの)、アプリケーションのプロパティの保持に使用するディクショナリ、任意のアプリケーション データの本体で構成されます。アプリケーションでは、任意のシリアル化可能なオブジェクトを渡すことによって本体を設定できます (次の例では、POS 端末からの売上データを表す **SalesData** オブジェクトを渡しています)。オブジェクトをシリアル化するには、[DataContractSerializer](https://msdn.microsoft.com/library/azure/system.runtime.serialization.datacontractserializer.aspx) を使用します。この方法に代わって、[Stream](https://msdn.microsoft.com/library/azure/system.io.stream.aspx) オブジェクトを提供することもできます。

```
BrokeredMessage bm = new BrokeredMessage(salesData);
bm.Label = "SalesReport";
bm.Properties["StoreName"] = "Redmond";
bm.Properties["MachineID"] = "POS_1";
```

トピックにメッセージを送信する最も簡単な方法は、[CreateMessageSender](https://msdn.microsoft.com/library/azure/hh322659.aspx) を使用して [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx) インスタンスから [MessageSender](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesender.aspx) オブジェクトを直接作成することです。

```
MessageSender sender = factory.CreateMessageSender("DataCollectionQueue");
sender.Send(bm);
```

### サブスクリプションからメッセージを受信する

キューの場合と同様に、サブスクリプションからメッセージを受け取るには、[MessageReceiver](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagereceiver.aspx) オブジェクトを使用できます。このオブジェクトは、[CreateMessageReceiver](https://msdn.microsoft.com/library/azure/hh322642.aspx) を使用して [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx) から直接作成します。「[Service Bus キューを使用するアプリケーションを作成する](service-bus-create-queues.md)」で説明されているように、2 種類の受信モード (**ReceiveAndDelete** と **PeekLock**) のどちらかを使用できます。

サブスクリプションの [MessageReceiver](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagereceiver.aspx) を作成するときは、*entityPath* パラメーターは `topicPath/subscriptions/subscriptionName` という形式であることに注意してください。したがって、**DataCollectionTopic** トピックの **Inventory** サブスクリプション用の [MessageReceiver](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagereceiver.aspx) を作成するには、*entityPath* を `DataCollectionTopic/subscriptions/Inventory` に設定する必要があります。コードは次のようになります。

```
MessageReceiver receiver = factory.CreateMessageReceiver("DataCollectionTopic/subscriptions/Inventory");
BrokeredMessage receivedMessage = receiver.Receive();
try
{
    ProcessMessage(receivedMessage);
    receivedMessage.Complete();
}
catch (Exception e)
{
    receivedMessage.Abandon();
}
```

## サブスクリプション フィルター

ここまで、トピックに送信されるすべてのメッセージは、登録されているすべてのサブスクリプションで使用できます。ここでの重要なフレーズは "使用可能にする" です。 Service Bus のサブスクリプションにはトピックに送信されたすべてのメッセージが含まれますが、これらのメッセージの一部のみを仮想サブスクリプション キューにコピーできます。これを行うには、サブスクリプション *フィルター*を使用します。サブスクリプションを作成するときは、メッセージのシステム プロパティ (例: [Label](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.label.aspx)) とアプリケーション プロパティの両方によって動作する SQL92 スタイルの述語の形式でフィルター式を提供できます。前のコードの **StoreName** はその例です。

これを示すためにシナリオを発展させ、2 番目の店を小売りシナリオに追加します。両店舗のすべての POS 端末からの売上データを一元的な在庫管理システムにルーティングする必要があるのは同じですが、ダッシュボード ツールを使用する店のマネージャーはその店の実績だけに関心があります。サブスクリプション フィルターを使用してこれを実現できます。POS 端末はメッセージを発行するときにメッセージの **StoreName** アプリケーション プロパティを設定することを思い出してください。たとえば **Redmond** と **Seattle** という 2 つの店があると、Redmond 店の POS 端末は売上データ メッセージの **StoreName** を **Redmond** に設定し、Seattle 店の POS 端末は **StoreName** を **Seattle** に設定します。Redmond 店のマネージャーは、Redmond 店の POS 端末のデータにだけ関心があります。システムは次のようになります。

![Service-Bus4](./media/service-bus-create-topics-subscriptions/IC657167.gif)

このルーティングを設定するには、**Dashboard** サブスクリプションを次のように作成します。

```
SqlFilter dashboardFilter = new SqlFilter("StoreName = 'Redmond'");
namespaceManager.CreateSubscription("DataCollectionTopic", "Dashboard", dashboardFilter);
```

このサブスクリプション フィルターでは、 **StoreName** プロパティが **Redmond** に設定されているメッセージだけが、**Dashboard** サブスクリプションの仮想キューにコピーされます。ただし、サブスクリプション フィルターの機能はこれだけではありません。アプリケーションでは、サブスクリプションごとに複数のフィルター ルールを使用するだけでなく、メッセージをサブスクリプションの仮想キューに渡すときにメッセージのプロパティを変更できます。

## 概要

「[Service Bus キューを使用するアプリケーションを作成する](service-bus-create-queues.md)」で説明されているキューを使用するすべての理由が、トピックにもあてはまります。具体的には次のようなことです。

- 一時的な結合の解除 – メッセージのプロデューサーとコンシューマーは、同時にオンラインである必要はありません。

- 負荷平準化 – 負荷のピークをトピックによって平準化すると、コンシューマーのアプリケーションをピーク負荷ではなく平均的な負荷に合わせてプロビジョニングできます。

- 負荷分散 – キューと同様に、1 つのサブスクリプションをリッスンする複数の競合するコンシューマーを作成し、各メッセージを 1 つのコンシューマーだけに渡すことにより、負荷を分散できます。

- 疎結合 – 既存のエンドポイントに影響を及ぼさずに、メッセージング ネットワークを発展させることができます。たとえば、サブスクリプションを追加したり、新しいコンシューマーに対応するようにトピックのフィルターを変更したりできます。

## 次のステップ

「[Service Bus のトピックとサブスクリプションを使用するアプリケーションを作成する](service-bus-create-topics-subscriptions.md)」を参照し、POS 小売りシナリオでキューを使用する方法を学習してください。

<!---HONumber=Oct15_HO3-->