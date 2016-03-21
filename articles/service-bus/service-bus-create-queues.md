<properties 
   pageTitle="Service Bus キューを使用するアプリケーションを作成する | Microsoft Azure"
   description="Service Bus を使用する簡単なキュー ベースのアプリケーションを作成する方法。"
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
   ms.date="12/28/2015"
   ms.author="sethm" />

# Service Bus キューを使用するアプリケーションを作成する

このトピックでは、Service Bus キューについて説明し、Service Bus を使用する簡単なキュー ベースのアプリケーションを作成する方法を示します。

個々 の Point of Sale (POS) 端末から売上データを在庫管理システムにルーティングし、在庫管理システムではそのデータを使用して在庫を補充する時期を決定する、小売業界のシナリオについて検討します。このソリューションでは、次の図に示すように、端末と在庫管理システムの間の通信に Service Bus メッセージングを使用します。

![Service-Bus-Queues-Img1](./media/service-bus-create-queues/IC657161.gif)

各 POS 端末は、**DataCollectionQueue** にメッセージを送信することによって、売上データを報告します。これらのメッセージは、在庫管理システムによって取り出されるまでこのキューに残っています。POS 端末は処理を続行するために在庫管理システムからの応答を待機する必要がないため、このパターンは*非同期メッセージング*と呼ばれることがよくあります。

## キューを使用する理由

このアプリケーションを設定するために必要なコードを見る前に、POS 端末が在庫管理システムと直接 (同期的に) 対話するのではなく、キューをこのシナリオで使用することの利点について検討します。

### 一時的な結合の解除

非同期メッセージング パターンでは、プロデューサーとコンシューマーが同時にオンラインになっている必要はありません。このメッセージング インフラストラクチャは、コンシューマーがメッセージを受信する準備ができるまで、メッセージを確実に格納します。これにより、分散型アプリケーションのコンポーネントをメンテナンスやコンポーネント クラッシュの場合でもシステム全体に影響を与えずに自動的に切断できます。さらに、コンシューマー アプリケーションがオンラインになっている必要がある時間は、1 日のうち一定の時間だけで済みます。たとえば、この小売業のシナリオでは、在庫管理システムは営業時間の終了後にオンラインになるだけで済むかもしれません。

### 負荷平準化

多くのアプリケーションでは、システム負荷が時間の経過とともに変化しますが、各作業単位に必要な処理時間は 通常一定に保たれます。メッセージ プロデューサーとメッセージ コンシューマーの間をキューで仲介することは、コンシューマー側アプリケーション (worker) はピーク時ではなく平均時の負荷に対応できるようにプロビジョニングすればいいということを意味します。キューの深さは、受信する負荷の変化に合わせて増減します。このため、アプリケーション負荷への対応に必要なインフラストラクチャに関して直接費用を節約できます。

![Service-Bus-Queues-Img2](./media/service-bus-create-queues/IC657162.gif)

### 負荷分散

負荷の増大に合わせて、worker キューからの読み取りのために worker プロセスを追加できます。各メッセージは、ワーカー プロセスの中の 1 つのプロセスによって処理されます。さらに、このプルベースの負荷分散では、各 worker コンピューターがそれぞれ独自の最大レートでメッセージをプルするため、それらのコンピューターの処理能力が異なる場合であっても使用を最適化できます。このパターンは、競合コンシューマー パターンと 呼ばれることもあります。

![Service-Bus-Queues-Img3](./media/service-bus-create-queues/IC657163.gif)

### 疎結合

メッセージ キューを使用してメッセージ プロデューサーとメッセージ コンシューマーの間を仲介すると、必然的にコンポーネント間の結び付きは緩くなります。プロデューサーとコンシューマーは相互に認識しないため、プロデューサーに影響することなく、コンシューマーをアップグレードできます。さらに、既存のエンドポイントに影響を与えずメッセージング トポロジを拡張できます。これについては、パブリッシュ/サブスクライブのところでさらに詳しく説明します。

## コード

以下では、Service Bus を使用してこのアプリケーションを作成する方法を示します。

### Service Bus のアカウントとサブスクリプションにサインアップします。

Service Bus の使用を開始するには、Azure アカウントが必要です。アカウントがまだない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A85619ABF)で無料試用版にサインアップできます。

### サービス名前空間の作成

サブスクリプションを作成した後は、新しい名前空間を作成できます。新しい名前空間には、すべての Service Bus アカウントについて一意の名前を指定する必要があります。各名前空間は、一連の Service Bus エンティティに対するコンテナーの役割を果たします。詳細については、「[方法: Service Bus Service 名前空間を作成または変更する](https://msdn.microsoft.com/library/azure/hh690931.aspx)」をご覧ください。

### NuGet パッケージのインストール

Service Bus サービス名前空間を使用するには、アプリケーションは Service Bus アセンブリ (具体的には Microsoft.ServiceBus.dll) を参照する必要があります。このアセンブリは Microsoft Azure SDK に含まれ、[Azure SDK のダウンロード ページ](https://azure.microsoft.com/downloads/)からダウンロードできます。ただし、Service Bus API を取得し、Service Bus 依存関係をすべて備えたアプリケーションを構成する最も簡単な方法は、Service Bus NuGet パッケージです。NuGet や Service Bus パッケージの使用方法の詳細については、「[NuGet Service Bus パッケージの使用](https://msdn.microsoft.com/library/dn741354.aspx)」を参照してください。

### キューの作成

Service Bus メッセージング エンティティ (キューおよびトピックのパブリッシュ/サブスクライブ) の管理は、[NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) クラスを使用して行われます。Service Bus は、[Shared Access Signature (SAS)](service-bus-sas-overview.md) ベースのセキュリティ モデルを使用します。[TokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.aspx) クラスは、いくつかの既知のトークン プロバイダーを返すファクトリ メソッドが組み込まれたセキュリティ トークン プロバイダーを表します。SAS の資格情報を保持するには、[CreateSharedAccessSignatureTokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.createsharedaccesssignaturetokenprovider.aspx) メソッドを使用します。その後、Service Bus 名前空間のベース アドレスとトークン プロバイダーを使用して、[NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) インスタンスが作成されます。

[NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) クラスには、メッセージング エンティティの作成、列挙、削除を実行するためのメソッドが用意されています。ここでは、[NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) インスタンスを作成し、それを使用して **DataCollectionQueue** キューを作成するコードを示します。

```
Uri uri = ServiceBusEnvironment.CreateServiceUri("sb", 
                "test-blog", string.Empty);
string name = "RootManageSharedAccessKey";
string key = "abcdefghijklmopqrstuvwxyz";
 
TokenProvider tokenProvider = 
    TokenProvider.CreateSharedAccessSignatureTokenProvider(name, key);
NamespaceManager namespaceManager = 
    new NamespaceManager(uri, tokenProvider);
namespaceManager.CreateQueue("DataCollectionQueue");
```

キューのプロパティを調整できる [CreateQueue](https://msdn.microsoft.com/library/azure/hh322663.aspx) メソッドのオーバーロードがあることに注意してください。たとえば、キューに送信されるメッセージの既定の有効期限 (TTL) の値を設定できます。

### キューへのメッセージの送信

Service Bus のエンティティに対して実行時の操作を行う場合 (メッセージの送受信など)、アプリケーションではまず [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx) オブジェクトを作成する必要があります。[NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) クラスと同じように、[MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx) インスタンスもサービスの名前空間のベース アドレスとトークン プロバイダーから作成されます。

```
 BrokeredMessage bm = new BrokeredMessage(salesData);
 bm.Label = "SalesReport";
 bm.Properties["StoreName"] = "Redmond";
 bm.Properties["MachineID"] = "POS_1";
```

Service Bus キューに送信されたメッセージ (および Service Bus キューから受信したメッセージ) は、[BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx) クラスのインスタンスになります。このクラスは、標準的なプロパティのセット ([Label](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.label.aspx)、[TimeToLive](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.timetolive.aspx) などの)、アプリケーションのプロパティの保持に使用するディクショナリ、任意のアプリケーション データの本体で構成されます。アプリケーションでは、任意のシリアル化可能なオブジェクトを渡すことによって本体を設定できます (次の例では、POS 端末からの売上データを表す **SalesData** オブジェクトを渡しています)。オブジェクトをシリアル化するには、[DataContractSerializer](https://msdn.microsoft.com/library/azure/system.runtime.serialization.datacontractserializer.aspx) を使用します。この方法に代わって、[Stream](https://msdn.microsoft.com/library/azure/system.io.stream.aspx) オブジェクトを提供することもできます。

特定のキュー (この例では **DataCollectionQueue**) にメッセージを送信する最も簡単な方法は、[CreateMessageSender](https://msdn.microsoft.com/library/azure/hh322659.aspx) を使用して [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx) インスタンスから [MessageSender](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesender.aspx) オブジェクトを直接作成することです。

```
MessageSender sender = factory.CreateMessageSender("DataCollectionQueue");
sender.Send(bm);
```

### キューからのメッセージの受信

キューからメッセージを受け取る最も簡単な方法は、[MessageReceiver](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagereceiver.aspx) オブジェクトを使用することです。このオブジェクトは、[CreateMessageReceiver](https://msdn.microsoft.com/library/azure/hh322642.aspx) を使用して [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx) から直接作成できます。メッセージ レシーバーは、**ReceiveAndDelete** と **PeekLock** という 2 つの異なるモードで動作できます。[ReceiveMode](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx) は、[CreateMessageReceiver](https://msdn.microsoft.com/library/azure/hh322642.aspx) の呼び出しへのパラメーターとして、メッセージ レシーバーの作成時に設定されます。


**ReceiveAndDelete** モードでは、受信は単発の操作です。つまり、Service Bus は要求を受信すると、メッセージを読み取り中としてマークしてアプリケーションに返します。**ReceiveAndDelete** モードは最もシンプルなモデルであり、障害が発生した場合にアプリケーション側でメッセージを処理しないことを許容できるシナリオに最適です。このことを理解するために、コンシューマーが受信要求を発行した後で、メッセージを処理する前にクラッシュしたというシナリオを考えてみましょう。Service Bus がメッセージを読み取り中としてマークするため、アプリケーションは、再起動してメッセージの読み取りを再開すると、クラッシュ前に読み取られていたメッセージを見落とすことになります。

**PeekLock** モードでは、メッセージの受信処理が 2 段階の動作になり、メッセージが失われることが許容できないアプリケーションに対応することができます。Service Bus は要求を受け取ると、次に読み取られるメッセージを検索して、他のコンシューマーが受信できないようロックしてから、アプリケーションにメッセージを返します。アプリケーションがメッセージの処理を終えた後 (または後で処理するために確実に保存した後)、受信したメッセージに対して [Complete](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) を呼び出して受信処理の第 2 段階を完了します。Service Bus は、[Complete](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) の呼び出しを確認すると、メッセージを読み取り済みとしてマークします。

結果としては他に 2 つの可能性があります。第 1 に、アプリケーションがなんらかの理由によってメッセージを処理できない場合には、受信したメッセージに対して ([Complete](https://msdn.microsoft.com/library/azure/hh181837.aspx) の代わりに) [Abandon](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) を呼び出すことができます。このメソッドが呼び出されると、Service Bus によってメッセージのロックが解除され、同じコンシューマーまたは競合する別のコンシューマーが再度そのメッセージを受信できるようになります。第 2 に、ロックに関連付けられたタイムアウトがあります。アプリケーションがクラッシュした場合など、ロックがタイムアウトになる前にアプリケーションがメッセージの処理できない場合は、Service Bus によってメッセージのロックが解除され、再度受信できるようになります。

メッセージが処理された後、[Complete](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) 要求が発行される前にアプリケーションがクラッシュした場合は、アプリケーションが再起動する際にメッセージが再配信されます。一般に、この動作は "1 回以上" の処理と呼ばれます。つまり、すべてのメッセージが 1 回以上処理されますが、特定の状況では、同じメッセージが再配信される可能性があります。重複した処理を許されないシナリオの場合は、アプリケーションで重複を検出する追加ロジックが必要です。これは、メッセージの [MessageId](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx) プロパティを使用して実現できます。このプロパティの値は、配信の試行を通じて変化しません。これは "*厳密に 1 回*" の処理と呼ばれます。

次に示すコードは、**PeekLock** モードを使用してメッセージを受信および処理します。このモードは、[ReceiveMode](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx) の値が明示的に提供されない場合の既定値です。

```
MessageReceiver receiver = factory.CreateMessageReceiver("DataCollectionQueue");
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

### キュー クライアントの使用

このセクションのこれまでの例では、[MessageSender](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesender.aspx) および [MessageReceiver](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagereceiver.aspx) オブジェクトを [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx) から直接作成し、キューとの間でメッセージを送受信しました。他の方法として、[QueueClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx) クラスを使用するものがあります。このクラスは、送信と受信両方の操作に加えて、セッションなどのさらに高度な機能をサポートします。

```
QueueClient queueClient = factory.CreateQueueClient("DataCollectionQueue");
queueClient.Send(bm);
            
BrokeredMessage message = queueClient.Receive();

try
{
    ProcessMessage(message);
    message.Complete();
}
catch (Exception e)
{
    message.Abandon();
} 
```

## 次のステップ

このトピックではキューの基本を説明しました。次に、「[Service Bus のトピックとサブスクリプションを使用するアプリケーションを作成する](service-bus-create-topics-subscriptions.md)」で Service Bus の仲介型メッセージングのパブリッシュ/サブスクライブ機能の使用を学習してください。

<!---HONumber=AcomDC_0128_2016-->