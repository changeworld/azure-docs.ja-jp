<properties
   pageTitle="Service Fabric によって提供される既定の通信スタック"
   description="この記事では、サービスとクライアントが通信するために Reliable Services のフレームワークによって提供される既定の通信スタックについて説明します。"
   services="service-fabric"
   documentationCenter=".net"
   authors="BharatNarasimman"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required"
   ms.date="08/27/2015"
   ms.author="bharatn@microsoft.com"/>

# Reliable Services フレームワークによって提供される既定の通信スタック
通信スタック (WebAPI、WCF など) の特定の実装に拘束されないサービス作成者向けに、このフレームワークでは、サービスとクライアント間の通信を設定するために使用できるクライアントとサービス側の通信部分を提供します。

> [AZURE.NOTE]以下に説明する機能を取得するには、最新の NuGet パッケージに更新してください。

## サービス通信リスナー
サービスの既定の通信リスナーは、`ServiceCommunicationListener` クラスに実装されています。

```csharp

public class ServiceCommunicationListener<TServiceImplementation> : ICommunicationListener where TServiceImplementation : class
{
    public ServiceCommunicationListener(TServiceImplementation serviceImplementationType);
    public ServiceCommunicationListener(TServiceImplementation serviceImplementationType, string endpointResourceName);

    public void Abort();
    public Task CloseAsync(CancellationToken cancellationToken);
    public void Initialize(ServiceInitializationParameters serviceInitializationParameters);
    public Task<string> OpenAsync(CancellationToken cancellationToken);
}

```
サービスで実装し、そのクライアントに公開したいと考えるメソッドは、`IService` インターフェイスから継承するインターフェイスに非同期メソッドとして定義します。これにより、サービスは `ServiceCommunicationListener` オブジェクトを単にインスタンス化し、[`CreateCommunicationListener` メソッド](service-fabric-reliable-services-communication.md)でそれを返すことができます。たとえば、この通信スタックをセットアップする HelloWorld サービスのコードは、次のように定義できます。

```csharp

[DataContract]
public class Message
{
    [DataMember]
    public string Content;
}

public interface IHelloWorld : IService
{
    Task<Message> GetGreeting();
}

public class HelloWorldService : StatelessService, IHelloWorld
{
    public const string ServiceTypeName = "HelloWorldUsingDefaultCommunicationType";

    private Message greeting = new Message() { Content = "Default greeting" };

    protected override ICommunicationListener CreateCommunicationListener()
    {
        return new ServiceCommunicationListener<HelloWorldService>(this);
    }

    public Task<Message> GetGreeting()
    {
        return Task.FromResult(this.greeting);
    }
}

```
> [AZURE.NOTE]先述の Message クラスなど、サービス インターフェイスの引数と戻り値の型は、.net [DataContractSerializer](https://msdn.microsoft.com/library/ms731923.aspx) によってシリアル化可能にすることが期待されます。


## ServiceCommunicationListener と通信するクライアントの作成
`ServiceCommunicationListener` を使用して、サービスと通信するクライアント向けに、フレームワークでは `ServiceProxy` クラスを提供しています。

```csharp

public abstract class ServiceProxy : IServiceProxy
{
...

    public static TServiceInterface Create<TServiceInterface>(Uri serviceName);

...
}

```

クライアントは、対応するサービス インターフェイスを実装するサービス プロキシ オブジェクトをインスタンス化し、プロキシ オブジェクトでメソッドを呼び出すことができます。

```csharp

var helloWorldClient = ServiceProxy.Create<IHelloWorld>(helloWorldServiceName);

var message = await helloWorldClient.GetGreeting();

Console.WriteLine("Greeting is {0}", message.Content);


```

>[AZURE.NOTE]通信フレームワークは、サービスでスローされた例外のクライアントへの伝播を担当します。そのため、ServiceProxy を使用したクライアントでの例外処理ロジックでは、サービスがスローする可能性がある例外を直接処理できます。
 

<!---HONumber=Oct15_HO3-->