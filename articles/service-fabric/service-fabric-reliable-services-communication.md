<properties
   pageTitle="サービス通信モデルの概要"
	description="この記事では、Reliable Service の API でサポートされる通信モデルの基本について説明します。"
	services="service-fabric"
	documentationCenter=".net"
	authors="BharatNarasimman"
	manager="vipulm"
	editor=""/>

<tags
   ms.service="service-fabric"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="required"
	ms.date="08/27/2015"
	ms.author="bharatn@microsoft.com"/>

# サービス通信モデル

Reliable Services プログラミング モデルにより、サービス作成者はサービス エンドポイントを公開するために使用する通信メカニズムを指定でき、クライアントがサービス エンドポイントを検出して、通信するために使用できる抽象も提供されます。

## サービス通信スタックの設定

Reliable Services API より、サービス作成者は、サービスで次のメソッドを実装して、選択した通信スタックをサービスにプラグインできます。

```csharp

protected override ICommunicationListener CreateCommunicationListener()
{
    ...
}

```

`ICommunicationListener` インターフェイスは、サービスの通信リスナーによって実装される必要があるインターフェイスを定義します。

```csharp

public interface ICommunicationListener
{
    void Initialize(ServiceInitializationParameters serviceInitializationParameters);

    Task<string> OpenAsync(CancellationToken cancellationToken);

    Task CloseAsync(CancellationToken cancellationToken);

    void Abort();
}

```
サービスに必要なエンドポイントは、[サービス マニフェスト](service-fabric-application-model.md)を介して、Endpoints セクションに記述します。

```xml

<Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpoint" Protocol="http" Type="Input" />
    <Endpoints>
</Resources>

```

通信リスナーは、`ServiceInitializationParameters` の `CodePackageActivationContext` から、割り当てられているエンドポイント リソースにアクセスでき、開かれたときに、要求のリッスンを開始できます。

```csharp

var codePackageActivationContext = this.serviceInitializationParameters.CodePackageActivationContext;
var port = codePackageActivationContext.GetEndpoint("ServiceEndpoint").Port;

```

> [AZURE.NOTE]エンドポイント リソースはサービス パッケージ全体に共通であり、サービス パッケージがアクティブ化されたときに Service Fabric によって割り当てられます。そのため、同じ ServiceHost 共有でホストされているすべてのレプリカは、同じポートを共有します。これは、通信リスナーがポート共有をサポートする必要があることを意味します。これを実行する推奨される方法は、通信リスナーが、リッスン アドレスを生成する際に、パーティション ID とレプリカ/インスタンス ID を使用することです。

```csharp

var replicaOrInstanceId = 0;
var parameters = this.serviceInitializationParameters as StatelessServiceInitializationParameters;
if (parameters != null)
{
  replicaOrInstanceId = parameters.InstanceId;
}
else
{
  replicaOrInstanceId = ((StatefulServiceInitializationParameters) this.serviceInitializationParameters).ReplicaId;
}

var nodeContext = FabricRuntime.GetNodeContext();

var listenAddress = new Uri(
    string.Format(
        CultureInfo.InvariantCulture,
        "{0}://{1}:{2}/{5}/{3}-{4}",
        scheme,
        nodeContext.IPAddressOrFQDN,
        port,
        this.serviceInitializationParameters.PartitionId,
        replicaOrInstanceId,
        Guid.NewGuid().ToString()));

```

## クライアントとサービス間の通信
Reliable Services API は、サービスと通信するクライアントの作成を簡単にする次の抽象化を提供します。

## ServicePartitionResolver
ServicePartitionResolver クラスは、クライアントが実行時に Service Fabric サービスのエンドポイントを特定するために役立ちます。

> [AZURE.TIP]サービスのエンドポイントを特定するプロセスは、Service Fabric の用語で、サービス エンドポイント解決と呼ばれます。

```csharp

public delegate FabricClient CreateFabricClientDelegate();

// ServicePartitionResolver methods

public ServicePartitionResolver(CreateFabricClientDelegate createFabricClient);

Task<ResolvedServicePartition> ResolveAsync(Uri serviceName,
    long partitionKey,
    CancellationToken cancellationToken);

Task<ResolvedServicePartition> ResolveAsync(ResolvedServicePartition previousRsp,
    CancellationToken cancellationToken);


```
> [AZURE.NOTE]FabricClient は、Service Fabric クラスターに対するさまざまな管理操作のために、Service Fabric クラスターとの通信に使用されるオブジェクトです。

一般に、クライアント コードでは `ServicePartitionResolver` を直接操作する必要はありません。それが作成されると、Reliable Service の API の、内部でリゾルバーを使用し、サービスとのクライアント通信に役立つ他のヘルパークラスに渡されます。

## CommunicationClientFactory
`ICommunicationClientFactory` は、ServiceFabric サービスと通信できるクライアントを生成する通信クライアント ファクトリによって実装される基本インターフェイスを定義します。CommunicationClientFactory の実装は、クライアントが通信しようとする Service Fabric で使用される通信スタックによって異なります。Reliable Service の API は、この `ICommunicationClientFactory` インターフェイスの基本実装を提供し、すべての通信スタックに共通するタスク (`ServicePartitionResolver` を使用して、サービス エンドポイントを特定するなど) を実行する CommunicationClientFactoryBase<TCommunicationClient> を提供します。クライアントは、通常 CommunicationClientFactoryBase 抽象クラスを実装して、通信スタック固有ロジックを処理します。

```csharp

protected CommunicationClientFactoryBase(
    ServicePartitionResolver servicePartitionResolver = null,
    IEnumerable<IExceptionHandler> exceptionHandlers = null,
    IEnumerable<Type> doNotRetryExceptionTypes = null);


public class MyCommunicationClient : ICommunicationClient
{
    public MyCommunicationClient(MyCommunicationChannel communicationChannel)
    {
      this.CommunicationChannel = communicationChannel;
    }
    public MyCommunicationChannel CommunicationChannel { get; private set; }
    public ResolvedServicePartition ResolvedServicePartition;

}

public class MyCommunicationClientFactory : CommunicationClientFactoryBase<MyCommunicationClient>
{
    protected override void AbortClient(MyCommunicationClient1 client)
    {
        throw new NotImplementedException();
    }

    protected override Task<MyCommunicationClient> CreateClientAsync(ResolvedServiceEndpoint endpoint, CancellationToken cancellationToken)
    {
        throw new NotImplementedException();
    }

    protected override bool ValidateClient(MyCommunicationClient clientChannel)
    {
        throw new NotImplementedException();
    }

    protected override bool ValidateClient(ResolvedServiceEndpoint endpoint, MyCommunicationClient client)
    {
        throw new NotImplementedException();
    }
}

```

## ServicePartitionClient
`ServicePartitionClient` は CommunicationClientFactory (および内部で、ServicePartitionResolver) を使用し、一般的な通信および Service Fabric の一時的な例外の再試行を処理することによって、サービスとの通信に役立ちます。

```csharp

public ServicePartitionClient(
    ICommunicationClientFactory<TCommunicationClient> factory,
    Uri serviceName,
    long partitionKey);

public async Task<TResult> InvokeWithRetryAsync<TResult>(
    Func<TCommunicationClient, Task<TResult>> func,
    CancellationToken cancellationToken,
    params Type[] doNotRetryExceptionTypes)

```

一般的な使用パターンは、次のようになります

```csharp

public MyCommunicationClientFactory myCommunicationClientFactory;
public Uri myServiceUri;

... other client code ..

// Call the service corresponding to the partitionKey myKey.

var myServicePartitionClient = new ServicePartitionClient<MyCommunicationClient>(
    this.myCommunicationClientFactory,
    this.myServiceUri,
    myKey);

  var result = await myServicePartitionClient.InvokeWithRetryAsync(
      client =>
      {
        // Communicate with the service using the client.
        throw new NotImplementedException();
      },
      CancellationToken.None);


... other client code ...

```

## 次のステップ
* [Reliable Services フレームワークによって提供される既定の通信スタック](service-fabric-reliable-services-communication-default.md)

* [Reliable Services フレームワークによって提供される WCF ベースの通信スタック](service-fabric-reliable-services-communication-wcf.md)

* [WebAPI 通信スタックを使用する Reliable Services API を使用したサービスの作成](service-fabric-reliable-services-communication-webapi.md)
 

<!---HONumber=September15_HO1-->