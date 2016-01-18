<properties
   pageTitle="Service Fabric でのサービスのリモート処理 | Microsoft Azure"
   description="Service Fabric のリモート処理では、クライアントとサービスがリモート プロシージャ コールを使用してサービスと通信できるようにします。"
   services="service-fabric"
   documentationCenter=".net"
   authors="BharatNarasimman"
   manager="timlt"
   editor="vturecek"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required"
   ms.date="11/12/2015"
   ms.author="bharatn@microsoft.com"/>

# Reliable Services によるサービスのリモート処理
WebAPI や Windows Communication Foundation (WCF) など、特定の通信プロトコルやスタックに関連付けられていないサービスでは、サービスのリモート プロシージャ コールを迅速かつ簡単に設定するためのリモート処理のメカニズムをフレームワークが提供します。

## サービスでのリモート処理の設定
サービスのリモート処理の設定は、次の 2 つの簡単な手順で行われます。

1. 実装するサービスのインターフェイスを作成します。このインターフェイスはサービスのリモート プロシージャ コールで使用できるメソッドを定義します。このメソッドはタスクを返す非同期メソッドである必要があります。インターフェイスは `Microsoft.ServiceFabric.Services.Remoting.IService` を実装し、そのサービスにリモート処理インターフェイスがあることを示す必要があります。
2. `Microsoft.ServiceFabric.Services.Remoting.Runtime.ServiceRemotingListener` をサービスに使用します。これは、リモート処理機能を提供する `ICommunicationListener` の実装です。

たとえば、この Hello World サービスでは、リモート プロシージャ コールで "Hello World" を取得する 1 つのメソッドを公開します。

```csharp
public interface IHelloWorldStateful : IService
{
    Task<string> GetHelloWorld();
}

internal class HelloWorldStateful : StatefulService, IHelloWorldStateful
{
    protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
    {
        return new[] { new ServiceReplicaListener(parameters => new ServiceRemotingListener<HelloWorldStateful>(parameters, this)) };
    }

    public Task<string> GetHelloWorld()
    {
        return Task.FromResult("Hello World!");
    }
}

```
> [AZURE.NOTE]サービス インターフェイスの引数と戻り値の型は、単純型、複合型、またはカスタム型のいずれかにできますが、.NET [DataContractSerializer](https://msdn.microsoft.com/library/ms731923.aspx) によってシリアル化できる必要があります。


## リモート サービス メソッドの呼び出し
リモート処理スタックを使用したサービスでのメソッドの呼び出しは、サービスに対して `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxy` クラスを介し、ローカル プロキシを使用して行われます。`ServiceProxy` メソッドは、サービスに実装されている同じインターフェイスを使用してローカル プロキシを作成します。そのプロキシを使用して、インターフェイス上のメソッドをリモートで簡単に呼び出すことができます。


```csharp

IHelloWorldStateful helloWorldClient = ServiceProxy.Create<IHelloWorldStateful>(new Uri("fabric:/MyApplication/MyHelloWorldService"));

string message = await helloWorldClient.GetHelloWorld();

```

リモート処理フレームワークは、サービスでスローされた例外をクライアントに伝達します。そのため、`ServiceProxy` を使用したクライアントでの例外処理ロジックでは、サービスがスローする例外を直接処理できます。

## 次のステップ

* [Reliable Services の OWIN 対応 Web API](service-fabric-reliable-services-communication-webapi.md)

* [Reliable Services との WCF 通信](service-fabric-reliable-services-communication-wcf.md)

<!---HONumber=AcomDC_0107_2016-->