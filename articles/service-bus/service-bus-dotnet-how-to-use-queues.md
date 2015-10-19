<properties
    pageTitle="Service Bus キューの使用方法 (.NET) | Microsoft Azure"
    description="Azure での Service Bus キューの使用方法を学習します。コード サンプルは .NET API を使用して C# で記述されています。"
    services="service-bus"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="get-started-article"
    ms.date="10/07/2015"
    ms.author="sethm"/>

# Azure Service Bus キューの使用方法

この記事では、Service Bus キューの使用方法について説明します。サンプルは C# で記述され、.NET API を利用しています。紹介するシナリオはキューの作成とメッセージの送受信です。キューの詳細については、「[次のステップ](#next-steps)」セクションを参照してください。

[AZURE.INCLUDE [アカウント作成メモ](../../includes/create-account-note.md)]

[AZURE.INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

## Service Bus を使用するためのアプリケーションの構成

Service Bus を使用するアプリケーションを作成するときには、Service Bus アセンブリに対する参照を追加して、対応する名前空間を含める必要があります。

## Service Bus NuGet パッケージの追加

Service Bus **NuGet** パッケージは、Service Bus API を取得し、Service Bus 依存関係をすべて備えたアプリケーションを構成する最も簡単な方法です。NuGet Visual Studio 拡張機能を使用すると、Visual Studio や Visual Studio Express でのライブラリやツールのインストールと更新を簡単に行うことができます。Service Bus NuGet パッケージは、Service Bus API を取得し、Service Bus 依存関係をすべて備えたアプリケーションを構成する最も簡単な方法です。

アプリケーションに NuGet パッケージをインストールするには、次のステップを行います。

1.  ソリューション エクスプローラーで **[参照]** を右クリックし、 **[NuGet パッケージの管理]** をクリックします。
2.  "Service Bus" を検索して、**[Microsoft Azure Service Bus]** 項目を選択します。**[インストール]** をクリックし、インストールが完了したら、このダイアログ ボックスを閉じます。

    ![][7]

これで、Service Bus に対応するコードを作成する準備ができました。

## Service Bus の接続文字列の設定

Service Bus では、接続文字列を使用してエンドポイントと資格情報を格納します。接続文字列は、ハードコーディングするのではなく、構成ファイルの中で指定します。

- Azure Cloud Services を使用するときには、Azure サービス構成システム (.csdef ファイルと .cscfg ファイル) を使用して接続文字列を格納することをお勧めします。
- Azure Websites または Azure Virtual Machines を使用する場合には、.NET 構成システム (Web.config ファイルなど) を使用して接続文字列を格納することをお勧めします。

いずれの場合も、この記事で後ほど説明する [CloudConfigurationManager.GetSetting][GetSetting] メソッドを使用して接続文字列を取得できます。

### Cloud Services を使用する場合の接続文字列の構成

サービス構成メカニズムは、Azure Cloud Services プロジェクトに特有のものであり、これを使用すると、アプリケーションを再デプロイしなくても Azure ポータルから構成設定を動的に変更できます。たとえば、次の例に示すように、サービス定義 (.csdef) ファイルに `Setting` ラベルを追加します。

```
<ServiceDefinition name="Azure1">
...
    <WebRole name="MyRole" vmsize="Small">
        <ConfigurationSettings>
            <Setting name="Microsoft.ServiceBus.ConnectionString" />
        </ConfigurationSettings>
    </WebRole>
...
</ServiceDefinition>
```
次の例に示すように、サービス構成 (.cscfg) ファイルで値を指定します。

```
<ServiceConfiguration serviceName="Azure1">
...
    <Role name="MyRole">
        <ConfigurationSettings>
            <Setting name="Microsoft.ServiceBus.ConnectionString"
                     value="Endpoint=sb://yourServiceNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey" />
        </ConfigurationSettings>
    </Role>
...
</ServiceConfiguration>
```

前のセクションで説明したように、Azure ポータルから取得した Shared Access Signature (SAS) のキー名とキー値を使用します。

### Websites または Azure Virtual Machines を使用する場合の接続文字列の構成

Websites または Virtual Machines を使用する場合には、.NET 構成システム (**Web.config** など) を使用することをお勧めします。`<appSettings>` 要素を使用して接続文字列を格納します。

```
<configuration>
    <appSettings>
        <add key="Microsoft.ServiceBus.ConnectionString"
             value="Endpoint=sb://yourServiceNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey" />
    </appSettings>
</configuration>
```

前のセクションで説明したように、Azure ポータルから取得した SAS 名とキー値を使用します。

## キューを作成する

[NamespaceManager][] クラスを使用して Service Bus キューに対する管理操作を実行できます。このクラスにはキューの作成、列挙、削除のためのメソッドが用意されています。

この例では、Azure の [CloudConfigurationManager][] クラスと接続文字列を使用して [NamespaceManager][] オブジェクトを作成します。この接続文字列は、Service Bus サービス名前空間のベース アドレスと、それを管理する権限を備えた適切な SAS 資格情報で構成されます。この接続文字列は、次の例に示すような形式になっています。

````
Endpoint=sb://yourServiceNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedSecretValue=yourKey
````

前のセクションの構成設定であれば、次の例を使用します。

```
// Create the queue if it does not exist already.
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

var namespaceManager =
    NamespaceManager.CreateFromConnectionString(connectionString);

if (!namespaceManager.QueueExists("TestQueue"))
{
    namespaceManager.CreateQueue("TestQueue");
}
```

[CreateQueue](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.createqueue.aspx) メソッドにはオーバーロードがあり、(キューに送信されるメッセージに対して既定の time-to-live (TTL) 値が適用されるように設定するなど) キューのプロパティを調整できるようになっています。このような設定は、[QueueDescription](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.aspx) クラスを使用して適用します。次の例では、名前が `TestQueue`、最大サイズが 5 GB、メッセージの既定の TTL が 1 分であるキューを作成する方法を示しています。

```
// Configure queue settings.
QueueDescription qd = new QueueDescription("TestQueue");
qd.MaxSizeInMegabytes = 5120;
qd.DefaultMessageTimeToLive = new TimeSpan(0, 1, 0);

// Create a new queue with custom settings.
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

var namespaceManager =
    NamespaceManager.CreateFromConnectionString(connectionString);

if (!namespaceManager.QueueExists("TestQueue"))
{
    namespaceManager.CreateQueue(qd);
}
```

> [AZURE.NOTE]指定した名前のキューがサービス空間に既に存在するかどうかを確認するには、[NamespaceManager][] オブジェクトで [QueueExists](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.queueexists.aspx) メソッドを使用できます。

## メッセージをキューに送信する

アプリケーションでは Service Bus キューにメッセージを送信するにあたって、接続文字列を使用して [QueueClient][] オブジェクトが作成されます。

以下のコードでは、[CreateFromConnectionString](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.createfromconnectionstring.aspx) API 呼び出しを使用して先に作成した `TestQueue` というキューに [QueueClient][] オブジェクトを作成する方法を示しています。

```
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

QueueClient Client =
    QueueClient.CreateFromConnectionString(connectionString, "TestQueue");

Client.Send(new BrokeredMessage());
```

Service Bus キューに送信されたメッセージおよび Service Bus キューから受信したメッセージは、[BrokeredMessage][] クラスのインスタンスになります。[BrokeredMessage][] オブジェクトには、([Label](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.label.aspx)、[TimeToLive](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.timetolive.aspx) などの) 標準的なプロパティ、アプリケーションに特有のカスタム プロパティの保持に使用するディクショナリ、任意のアプリケーション データの本体が備わっています。アプリケーションでは、[BrokeredMessage][] オブジェクトのコンストラクターにシリアル化可能なオブジェクトを渡すことによってメッセージの本文を設定できます。その後で、適切な **DataContractSerializer** を使用してオブジェクトをシリアル化します。または、**System.IO.Stream** オブジェクトを提供することもできます。

以下の例では、上のコード例で取得した `TestQueue` [QueueClient][] オブジェクトにテスト メッセージを 5 件送信する方法を示しています。

```
for (int i=0; i<5; i++)
{
  // Create message, passing a string message for the body.
  BrokeredMessage message = new BrokeredMessage("Test message " + i);

  // Set some addtional custom app-specific properties.
  message.Properties["TestProperty"] = "TestValue";
  message.Properties["Message number"] = i;

  // Send message to the queue.
  Client.Send(message);
}
```

Service Bus キューでは、[最大 256 Kb までのメッセージ](service-bus-quotas.md)をサポートしています (標準とカスタムのアプリケーション プロパティが含まれるヘッダーの最大サイズは 64 KB です)。キューで保持されるメッセージ数には上限がありませんが、キュー 1 つあたりが保持できるメッセージの合計サイズには上限があります。このキュー サイズは作成時に定義され、上限は 5 GB です。パーティション分割が有効な場合、上限が高くなります。詳細については、「[メッセージング エンティティのパーティション分割](service-bus-partitioning.md)」を参照してください。

## キューからメッセージを受信する方法

キューからメッセージを受信する方法の中で推奨される方法は、[QueueClient][] オブジェクトを使用することです。[QueueClient][] オブジェクトを使用する方法です。 オブジェクトには、2 つの異なる動作モードがあります。[ReceiveAndDelete と PeekLock](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx) です。

**ReceiveAndDelete** モードを使用する場合、受信が 1 回ずつの動作になります。つまり、Service Bus はキュー内のメッセージに対する読み取り要求を受け取ると、メッセージを読み取り中としてマークし、アプリケーションに返します。**ReceiveAndDelete** は最もシンプルなモデルであり、障害発生時にアプリケーション側でメッセージを処理しないことを許容できるシナリオに最適です。このことを理解するために、コンシューマーが受信要求を発行した後で、メッセージを処理する前にクラッシュしたというシナリオを考えてみましょう。Service Bus はメッセージを読み取り済みとしてマークするため、アプリケーションが再起動してメッセージの読み取りを再開すると、クラッシュ前に読み取られていたメッセージは見落とされることになります。

**PeekLock** モード (既定のモード) では、受信処理が 2 段階の動作になり、メッセージが失われることが許容できないアプリケーションに対応することができます。Service Bus は要求を受け取ると、次に読み取られるメッセージを検索して、他のコンシューマーが受信できないようロックしてから、アプリケーションにメッセージを返します。アプリケーションがメッセージの処理を終えた後 (または後で処理するために確実に保存した後)、受信したメッセージに対して [Complete][] を呼び出して受信処理の第 2 段階を完了します。Service Bus が [Complete][] の呼び出しを確認すると、メッセージが読み取り中としてマークされ、キューから削除されます。

次の例では、既定の **PeekLock** モードを使用したメッセージの受信および処理の方法を示しています。別の [ReceiveMode](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx) 値を指定する場合には、[CreateFromConnectionString](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.createfromconnectionstring.aspx) の別のオーバーロードを使用できます。この例では、[OnMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.onmessage.aspx) コールバックを使用して、`TestQueue` に到着したメッセージを処理します。

```
string connectionString =
  CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");
QueueClient Client =
  QueueClient.CreateFromConnectionString(connectionString, "TestQueue");

// Configure the callback options.
OnMessageOptions options = new OnMessageOptions();
options.AutoComplete = false;
options.AutoRenewTimeout = TimeSpan.FromMinutes(1);

// Callback to handle received messages.
Client.OnMessage((message) =>
{
    try
    {
        // Process message from queue.
        Console.WriteLine("Body: " + message.GetBody<string>());
        Console.WriteLine("MessageID: " + message.MessageId);
        Console.WriteLine("Test Property: " +
        message.Properties["TestProperty"]);

        // Remove message from queue.
        message.Complete();
    }
    catch (Exception)
    {
        // Indicates a problem, unlock message in queue.
        message.Abandon();
    }
}, options);
```

この例では、[OnMessageOptions](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.onmessageoptions.aspx) オブジェクトを使用して、[OnMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.onmessage.aspx) コールバックを構成します。[AutoComplete](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.onmessageoptions.autocomplete.aspx) は **false** に設定され、受信したメッセージで [Complete][] を呼び出すタイミングを手動で制御できるようになります。[AutoRenewTimeout](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.onmessageoptions.autorenewtimeout.aspx) は 1 分に設定され、クライアントは呼び出しがタイムアウトになるまで最大で 1 分間メッセージを待ち、新しい呼び出しを行ってメッセージを確認します。このプロパティ値により、メッセージを取得しない課金対象呼び出しをクライアントが行う回数が減ります。

## アプリケーションのクラッシュと読み取り不能のメッセージを処理する方法

Service Bus には、アプリケーションにエラーが発生した場合や、メッセージの処理に問題がある場合に復旧を支援する機能が備わっています。受信側のアプリケーションがなんらかの理由によってメッセージを処理できない場合には、受信したメッセージについて ([Complete][] メソッドの代わりに) [Abandon](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.abandon.aspx) メソッドを呼び出すことができます。このメソッドが呼び出されると、Service Bus によってキュー内のメッセージのロックが解除され、メッセージが再度受信できる状態に変わります。メッセージを受信するアプリケーションは、以前と同じものでも、別のものでもかまいません。

キュー内でロックされているメッセージにはタイムアウトも設定されています。アプリケーションがクラッシュした場合など、ロックがタイムアウトになる前にアプリケーションがメッセージの処理に失敗した場合には、Service Bus によってメッセージのロックが自動的に解除され、再度受信できる状態に変わります。

メッセージが処理された後、[Complete][] 要求が発行される前にアプリケーションがクラッシュした場合は、アプリケーションが再起動する際にメッセージが再配信されます。一般的に、この動作は **1 回以上の処理**と呼ばれます。つまり、すべてのメッセージが 1 回以上処理されますが、特定の状況では、同じメッセージが再配信される可能性があります。重複処理が許されないシナリオの場合、重複メッセージの配信を扱うロジックをアプリケーションに追加する必要があります。通常、この問題はメッセージの [MessageId](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx) プロパティを使用して対処します。このプロパティは配信が試行された後も同じ値を保持します。

## 次のステップ

これで、Service Bus キューの基本を学習できました。さらに詳細な情報が必要な場合は、次のリンク先を参照してください。

-   [キュー、トピック、サブスクリプション][]での Service Bus メッセージング エンティティについて読んでください。
-   [Service Bus が仲介するメッセージングに関する .NET チュートリアル][]で、Service Bus キューとの間でメッセージを送受信する実用アプリケーションを作成してください。
-   Service Bus のサンプルを [Azure のサンプル][]からダウンロードするか、[Service Bus サンプルの概要][]を参照してください。

  [Azure portal]: http://manage.windowsazure.com
  [7]: ./media/service-bus-dotnet-how-to-use-queues/getting-started-multi-tier-13.png
  [キュー、トピック、サブスクリプション]: service-bus-queues-topics-subscriptions.md
  [Service Bus が仲介するメッセージングに関する .NET チュートリアル]: service-bus-brokered-tutorial-dotnet.md
  [Azure のサンプル]: https://code.msdn.microsoft.com/site/search?query=service%20bus&f%5B0%5D.Value=service%20bus&f%5B0%5D.Type=SearchText&ac=2
  [Service Bus サンプルの概要]: service-bus-samples.md
  [GetSetting]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.cloudconfigurationmanager.getsetting.aspx
  [CloudConfigurationManager]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.cloudconfigurationmanager
  [NamespaceManager]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx
  [BrokeredMessage]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx
  [QueueClient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx
  [Complete]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx

<!---HONumber=Oct15_HO2-->