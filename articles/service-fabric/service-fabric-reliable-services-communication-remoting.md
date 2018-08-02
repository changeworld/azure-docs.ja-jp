---
title: Service Fabric での C# を使用したサービスのリモート処理 | Microsoft Docs
description: Service Fabric のリモート処理では、クライアントとサービスがリモート プロシージャ コールを使用して C# サービスと通信できるようにします。
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: BharatNarasimman
ms.assetid: abfaf430-fea0-4974-afba-cfc9f9f2354b
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 09/20/2017
ms.author: vturecek
ms.openlocfilehash: e7652fe1b211e6811a4a3aa61bc2aa9d2f529dda
ms.sourcegitcommit: 30221e77dd199ffe0f2e86f6e762df5a32cdbe5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2018
ms.locfileid: "39205818"
---
# <a name="service-remoting-in-c-with-reliable-services"></a>C# での Reliable Services を使用したサービスのリモート処理
> [!div class="op_single_selector"]
> * [Windows での C#](service-fabric-reliable-services-communication-remoting.md)
> * [Linux での Java](service-fabric-reliable-services-communication-remoting-java.md)
>
>

WebAPI や Windows Communication Foundation (WCF) など、特定の通信プロトコルやスタックに関連付けられていないサービスでは、サービスのリモート プロシージャ コールを迅速かつ簡単に設定するためのリモート処理メカニズムを Reliable Services フレームワークが提供します。 この記事では、C# で作成されたサービス向けにリモート プロシージャ コールを設定する方法について説明します。

## <a name="set-up-remoting-on-a-service"></a>サービスでのリモート処理の設定
サービスのリモート処理の設定は、次の 2 つの簡単な手順で行われます。

1. 実装するサービスのインターフェイスを作成します。 このインターフェイスは、サービスのリモート プロシージャ コールで使用できるメソッドを定義します。 このメソッドはタスクを返す非同期メソッドである必要があります。 インターフェイスは `Microsoft.ServiceFabric.Services.Remoting.IService` を実装し、そのサービスにリモート処理インターフェイスがあることを示す必要があります。
2. サービスでリモート処理リスナーを使用します。 RemotingListener は、リモート処理機能を提供する `ICommunicationListener` の実装です。 `Microsoft.ServiceFabric.Services.Remoting.Runtime` 名前空間には、ステートレス サービスとステートフル サービスの拡張メソッド `CreateServiceRemotingListener` が含まれています。このメソッドは、既定のリモート処理トランスポート プロトコルを使用してリモート処理リスナーを作成するときに使用できます。

>[!NOTE]
>`Remoting` 名前空間は、`Microsoft.ServiceFabric.Services.Remoting` という別の NuGet パッケージとして利用できます。

たとえば、次のステートレス サービスでは、リモート プロシージャ コールで "Hello World" を取得する 1 つのメソッドを公開します。

```csharp
using Microsoft.ServiceFabric.Services.Communication.Runtime;
using Microsoft.ServiceFabric.Services.Remoting;
using Microsoft.ServiceFabric.Services.Remoting.Runtime;
using Microsoft.ServiceFabric.Services.Runtime;

public interface IMyService : IService
{
    Task<string> HelloWorldAsync();
}

class MyService : StatelessService, IMyService
{
    public MyService(StatelessServiceContext context)
        : base (context)
    {
    }

    public Task<string> HelloWorldAsync()
    {
        return Task.FromResult("Hello!");
    }

    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
     return this.CreateServiceRemotingInstanceListeners();
    }
}
```
> [!NOTE]
> サービス インターフェイスの引数と戻り値の型は、単純型、複合型、またはカスタム型のいずれかにできますが、.NET の [DataContractSerializer](https://msdn.microsoft.com/library/ms731923.aspx)によってシリアル化できる必要があります。
>
>

## <a name="call-remote-service-methods"></a>リモート サービス メソッドの呼び出し
リモート処理スタックを使用したサービスでのメソッドの呼び出しは、 `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxy` クラスによるサービスへのローカル プロキシを使用して実行されます。 `ServiceProxy` メソッドは、サービスに実装されている同じインターフェイスを使用してローカル プロキシを作成します。 そのプロキシを使用して、インターフェイス上のメソッドをリモートで呼び出すことができます。

```csharp

IMyService helloWorldClient = ServiceProxy.Create<IMyService>(new Uri("fabric:/MyApplication/MyHelloWorldService"));

string message = await helloWorldClient.HelloWorldAsync();

```

リモート処理フレームワークは、サービスでスローされた例外をクライアントに伝達します。 結果として、`ServiceProxy` を使うときは、サービスによってスローされた例外をクライアントが処理する必要があります。

## <a name="service-proxy-lifetime"></a>サービス プロキシの有効期間
ServiceProxy の作成は負荷の低い処理であり、必要に応じていくつでも ServiceProxy を作成できます。 サービス プロキシ インスタンスは、それらが必要とされる間、再利用することができます。 リモート プロシージャ コールから例外がスローされても、同じプロキシ インスタンスを再利用できます。 各 ServiceProxy は、メッセージをネットワーク経由で送信するための通信クライアントを含んでいます。 リモート呼び出しが実行されると、通信クライアントが有効かどうかを調べる内部チェックが実行されます。 それらのチェックの結果に基づき、必要に応じて通信クライアントが再作成されます。 したがって例外が発生しても、`ServiceProxy` を自分で再作成する必要はありません。

### <a name="serviceproxyfactory-lifetime"></a>ServiceProxyFactory の有効期間
[ServiceProxyFactory](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.client.serviceproxyfactory) は、さまざまなリモート処理インターフェイスのプロキシ インスタンスを作成するファクトリです。 プロキシの作成に api `ServiceProxy.Create` を使用する場合、フレームワークによってシングルトン ServiceProxy が作成されます。
手動での作成は、[IServiceRemotingClientFactory](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v1.client.iserviceremotingclientfactory) プロパティをオーバーライドする必要があるときに効果的です。
ファクトリの作成は負荷の高い操作です。 ServiceProxyFactory は通信クライアントの内部キャッシュを保持します。
ServiceProxyFactory はできるだけ長くキャッシュすることをお勧めします。

## <a name="remoting-exception-handling"></a>リモート処理の例外処理
サービス API によってスローされるリモート処理の例外はすべて、AggregateException としてクライアントに返されます。 RemoteExceptions は DataContract シリアル化可能である必要があります。そうでない場合、プロキシ API は内部のシリアル化エラーで [ServiceException](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.communication.serviceexception) をスローします。

ServiceProxy は、それが作成されたサービス パーティションのすべてのフェールオーバー例外を処理します。 フェールオーバー例外 (一時的ではない例外) が発生した場合、エンドポイントを再度解決し、正しいエンドポイントでの呼び出しを再試行します。 フェールオーバー例外の再試行回数に上限はありません。
一時的な例外が発生した場合、プロキシは呼び出しを再試行します。

既定の再試行パラメーターは、[OperationRetrySettings](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.communication.client.operationretrysettings) で指定します。

ユーザーは、ServiceProxyFactory コンストラクターに OperationRetrySettings オブジェクトを渡すことによって、これらの値を構成できます。

## <a name="how-to-use-the-remoting-v2-stack"></a>リモート処理 V2 スタックを使用する方法

NuGet リモート処理パッケージ バージョン 2.8 では、リモート処理 V2 スタックを使用するオプションを選択できます。 リモート処理 V2 スタックは高性能で、カスタムのシリアル化などの機能と、より多くのプラグ可能な API を提供します。
テンプレート コードでは、引き続きリモート処理 V1 スタックが使用されます。
リモート処理 V2 は V1 (以前のリモート処理スタック) と互換性がないため、サービスの可用性に影響を与えずに [V1 から V2 にアップグレードする方法](#how-to-upgrade-from-remoting-v1-to-remoting-v2)に関する下記の手順に従ってください。

V2 スタックを有効にするには、次の方法を使用できます。

### <a name="using-an-assembly-attribute-to-use-the-v2-stack"></a>V2 スタックを使用するためのアセンブリ属性の使用

これらの手順では、V2 スタックを使用するために、アセンブリ属性を使ってテンプレート コードを変更します。

1. サービス マニフェストで `"ServiceEndpoint"` から `"ServiceEndpointV2"` にエンドポイント リソースを変更します。

  ```xml
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2" />
    </Endpoints>
  </Resources>
  ```

2. `Microsoft.ServiceFabric.Services.Remoting.Runtime.CreateServiceRemotingInstanceListeners` 拡張メソッドを使用して、リモート処理リスナーを作成します (V1 と V2 の両方で同一)。

  ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
  ```

3. `FabricTransportServiceRemotingProvider` 属性でリモート処理インターフェイスが含まれているアセンブリをマークします。

  ```csharp
  [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2, RemotingClientVersion = RemotingClientVersion.V2)]
  ```

クライアント プロジェクトでは、コードの変更は必要ありません。
上記のアセンブリ属性が確実に使用されるように、インターフェイス アセンブリを使用してクライアント アセンブリをビルドします。

### <a name="using-explicit-v2-classes-to-use-the-v2-stack"></a>V2 スタックを使用するための明示的な V2 クラスの使用

アセンブリ属性を使用する代わりに、明示的な V2 クラスを使用して V2 スタックを有効にすることもできます。

これらの手順では、V2 スタックを使用するために、明示的な V2 クラスを使ってテンプレート コードを変更します。

1. サービス マニフェストで `"ServiceEndpoint"` から `"ServiceEndpointV2"` にエンドポイント リソースを変更します。

  ```xml
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2" />
    </Endpoints>
  </Resources>
  ```

2. `Microsoft.ServiceFabric.Services.Remoting.V2.FabricTransport.Runtime` 名前空間から [FabricTransportServiceRemotingListener](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.runtime.fabrictransportserviceremotingListener?view=azure-dotnet) を使用します。

  ```csharp
  protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return new[]
        {
            new ServiceInstanceListener((c) =>
            {
                return new FabricTransportServiceRemotingListener(c, this);

            })
        };
    }
  ```

3. `Microsoft.ServiceFabric.Services.Remoting.V2.FabricTransport.Client` 名前空間から [FabricTransportServiceRemotingClientFactory](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.client.fabrictransportserviceremotingclientfactory?view=azure-dotnet) を使用して、クライアントを作成します。

  ```csharp
  var proxyFactory = new ServiceProxyFactory((c) =>
          {
              return new FabricTransportServiceRemotingClientFactory();
          });
  ```

## <a name="how-to-upgrade-from-remoting-v1-to-remoting-v2"></a>リモート処理 V1 からリモート処理 V2 にアップグレードする方法
V1 から V2 にアップグレードするには、2 段階のアップグレードが必要です。 記載した順序で次の手順を実行します。

1. 次の属性を使用して、V1 サービスを V2 サービスにアップグレードします。
この変更により、サービスは確実に V1 および V2 リスナーでリッスンします。

    a) サービス マニフェストで "ServiceEndpointV2" という名前を持つエンドポイント リソースを追加します。
      ```xml
      <Resources>
        <Endpoints>
          <Endpoint Name="ServiceEndpointV2" />  
        </Endpoints>
      </Resources>
      ```

    b) 次の拡張メソッドを使用して、リモート処理リスナーを作成します。

    ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
    ```

    c) リモート処理インターフェイスで Assembly 属性を追加して、V1 および V2 リスナーと V2 クライアントを使用します。
    ```csharp
    [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2|RemotingListenerVersion.V1, RemotingClientVersion = RemotingClientVersion.V2)]

      ```
2. V2 クライアント属性を使用して、V1 クライアントを V2 クライアントにアップグレードします。
この手順により、クライアントは確実に V2 スタックを使用します。
クライアント プロジェクト/サービスの変更は必要ありません。 更新されたインターフェイス アセンブリを使用してクライアント プロジェクトをビルドするだけで十分です。

3. この手順は省略可能です。 V2Listener 属性を使用して、V2 サービスをアップグレードします。
この手順により、サービスは確実に V2 リスナーでのみリッスンします。

```csharp
[assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2, RemotingClientVersion = RemotingClientVersion.V2)]
```


## <a name="how-to-use-remoting-v2interfacecompatible-stack"></a>リモート処理 V2(InterfaceCompatible) スタックを使用する方法
 リモート処理 V2(InterfaceCompatible、別名 V2_1) スタックは、V2 リモート処理スタックのすべての機能を備えています。リモート処理 V1 スタックに対するインターフェイス互換スタックですが、V2 および V1 との後方互換性はありません。 サービスの可用性に影響を与えずに V1 から V2_1 へのアップグレードを行うには、[V1 から V2(InterfaceCompatible) にアップグレードする方法](#how-to-upgrade-from-remoting-v1-to-remoting-v2interfacecompatible)に関する下記の手順に従ってください。


### <a name="using-assembly-attribute-to-use-remoting-v2interfacecompatible-stack"></a>アセンブリ属性を使用してリモート処理 V2(InterfaceCompatible) スタックを使用する

V2_1 スタックへの変更に必要な手順は次のとおりです。

1. サービス マニフェストで "ServiceEndpointV2_1" という名前を持つエンドポイント リソースを追加します。

  ```xml
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2_1" />  
    </Endpoints>
  </Resources>
  ```

2.  リモート処理リスナーを作成するには、リモート処理拡張メソッドを使用します。

  ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
  ```

3.  リモート処理インターフェイスで[アセンブリ属性](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.fabrictransport.fabrictransportserviceremotingproviderattribute?view=azure-dotnet)を追加します。

  ```csharp
    [assembly:  FabricTransportServiceRemotingProvider(RemotingListenerVersion=  RemotingListenerVersion.V2_1, RemotingClientVersion= RemotingClientVersion.V2_1)]

  ```
クライアント プロジェクトでは、変更は必要ありません。
上記の Assembly 属性が確実に使用されるように、インターフェイス アセンブリを使用してクライアント アセンブリをビルドします。

### <a name="using-explicit-remoting-classes-to-create-listener-clientfactory-for-v2interfacecompatible-version"></a>明示的リモート処理クラスを使用して V2(InterfaceCompatible) バージョンの Listener/ClientFactory を作成する
実行する手順は次のとおりです。
1.  サービス マニフェストで "ServiceEndpointV2_1" という名前を持つエンドポイント リソースを追加します。

  ```xml
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2_1" />  
    </Endpoints>
  </Resources>
  ```

2. [リモート処理 V2 リスナー](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.runtime.fabrictransportserviceremotinglistener?view=azure-dotnet)を使用します。 使用される既定のサービス エンドポイント リソース名は、"ServiceEndpointV2_1" であり、サービス マニフェストで定義する必要があります。

  ```csharp
  protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return new[]
        {
            new ServiceInstanceListener((c) =>
            {
                var settings = new FabricTransportRemotingListenerSettings();
                settings.UseWrappedMessage = true;
                return new FabricTransportServiceRemotingListener(c, this,settings);

            })
        };
    }
  ```

3. V2 [クライアント ファクトリ](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.client.fabrictransportserviceremotingclientfactory?view=azure-dotnet)を使用します。
  ```csharp
  var proxyFactory = new ServiceProxyFactory((c) =>
          {
            var settings = new FabricTransportRemotingSettings();
            settings.UseWrappedMessage = true;
            return new FabricTransportServiceRemotingClientFactory(settings);
          });
  ```

## <a name="how-to-upgrade-from-remoting-v1-to-remoting-v2interfacecompatible"></a>リモート処理 V1 からリモート処理 V2(InterfaceCompatible) にアップグレードする方法
V1 から V2(InterfaceCompatible、別名 V2_1) にアップグレードするには、2 段階のアップグレードが必要です。 記載した順序で次の手順を実行します。

1. 次の属性を使用して、V1 サービスを V2_1 サービスにアップグレードします。
この変更により、サービスは確実に V1 および V2_1 リスナーでリッスンします。

    a) サービス マニフェストで "ServiceEndpointV2_1" という名前を持つエンドポイント リソースを追加します。
      ```xml
      <Resources>
        <Endpoints>
          <Endpoint Name="ServiceEndpointV2_1" />  
        </Endpoints>
      </Resources>
      ```

    b) 次の拡張メソッドを使用して、リモート処理リスナーを作成します。

    ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
    ```

    c) リモート処理インターフェイスで Assembly 属性を追加して、V1、V2_1 リスナー、V2_1 クライアントを使用します。
    ```csharp
   [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1 | RemotingListenerVersion.V1, RemotingClientVersion = RemotingClientVersion.V2_1)]

      ```
2. V2_1 クライアント属性を使用して、V1 クライアントを V2_1 クライアントにアップグレードします。
この手順により、クライアントは確実に V2_1 スタックを使用します。
クライアント プロジェクト/サービスの変更は必要ありません。 更新されたインターフェイス アセンブリを使用してクライアント プロジェクトをビルドするだけで十分です。

3. この手順は省略可能です。 属性から V1 リスナー バージョンを削除してから、V2 サービスをアップグレードします。
この手順により、サービスは確実に V2 リスナーでのみリッスンします。

```csharp
[assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1, RemotingClientVersion = RemotingClientVersion.V2_1)]
```

### <a name="how-to-use-custom-serialization-with-remoting-wrapped-message"></a>リモート処理ラップ済みメッセージを使用したカスタムのシリアル化の使用方法
リモート処理ラップ済みメッセージで、すべてのパラメーターをフィールドとして含む 1 つのラップ済みオブジェクトを作成します。
手順は次のようになります。

1. カスタムのシリアル化の実装を提供する IServiceRemotingMessageSerializationProvider インターフェイスを実装します。
    実装は、次のコード スニペットのようになります。

      ```csharp
      public class ServiceRemotingJsonSerializationProvider : IServiceRemotingMessageSerializationProvider
      {
        public IServiceRemotingMessageBodyFactory CreateMessageBodyFactory()
        {
          return new JsonMessageFactory();
        }

        public IServiceRemotingRequestMessageBodySerializer CreateRequestMessageSerializer(Type serviceInterfaceType, IEnumerable<Type> requestWrappedType, IEnumerable<Type> requestBodyTypes = null)
        {
          return new ServiceRemotingRequestJsonMessageBodySerializer();
        }

        public IServiceRemotingResponseMessageBodySerializer CreateResponseMessageSerializer(Type serviceInterfaceType, IEnumerable<Type> responseWrappedType, IEnumerable<Type> responseBodyTypes = null)
        {
          return new ServiceRemotingResponseJsonMessageBodySerializer();
        }
      }
      ```
      ```csharp
        class JsonMessageFactory : IServiceRemotingMessageBodyFactory
            {

              public IServiceRemotingRequestMessageBody CreateRequest(string interfaceName, string methodName, int numberOfParameters, object wrappedRequestObject)
              {
                return new JsonBody(wrappedRequestObject);
              }

              public IServiceRemotingResponseMessageBody CreateResponse(string interfaceName, string methodName, object wrappedRequestObject)
              {
                return new JsonBody(wrappedRequestObject);
              }
            }
      ```
      ```csharp
      class ServiceRemotingRequestJsonMessageBodySerializer : IServiceRemotingRequestMessageBodySerializer
        {
            private JsonSerializer serializer;

            public ServiceRemotingRequestJsonMessageBodySerializer()
            {
              serializer = JsonSerializer.Create(new JsonSerializerSettings()
              {
                TypeNameHandling = TypeNameHandling.All
                });
              }

              public IOutgoingMessageBody Serialize(IServiceRemotingRequestMessageBody serviceRemotingRequestMessageBody)
             {
               if (serviceRemotingRequestMessageBody == null)
               {
                 return null;
               }          
               using (var writeStream = new MemoryStream())
               {
                 using (var jsonWriter = new JsonTextWriter(new StreamWriter(writeStream)))
                 {
                   serializer.Serialize(jsonWriter, serviceRemotingRequestMessageBody);
                   jsonWriter.Flush();
                   var bytes = writeStream.ToArray();
                   var segment = new ArraySegment<byte>(bytes);
                   var segments = new List<ArraySegment<byte>> { segment };
                   return new OutgoingMessageBody(segments);
                 }
               }
              }

              public IServiceRemotingRequestMessageBody Deserialize(IIncomingMessageBody messageBody)
             {
               using (var sr = new StreamReader(messageBody.GetReceivedBuffer()))
               {
                 using (JsonReader reader = new JsonTextReader(sr))
                 {
                   var ob = serializer.Deserialize<JsonBody>(reader);
                   return ob;
                 }
               }
             }
            }
      ```
      ```csharp
      class ServiceRemotingResponseJsonMessageBodySerializer : IServiceRemotingResponseMessageBodySerializer
       {
         private JsonSerializer serializer;

        public ServiceRemotingResponseJsonMessageBodySerializer()
        {
          serializer = JsonSerializer.Create(new JsonSerializerSettings()
          {
              TypeNameHandling = TypeNameHandling.All
            });
          }

          public IOutgoingMessageBody Serialize(IServiceRemotingResponseMessageBody responseMessageBody)
          {
            if (responseMessageBody == null)
            {
              return null;
            }

            using (var writeStream = new MemoryStream())
            {
              using (var jsonWriter = new JsonTextWriter(new StreamWriter(writeStream)))
              {
                serializer.Serialize(jsonWriter, responseMessageBody);
                jsonWriter.Flush();
                var bytes = writeStream.ToArray();
                var segment = new ArraySegment<byte>(bytes);
                var segments = new List<ArraySegment<byte>> { segment };
                return new OutgoingMessageBody(segments);
              }
            }
          }

          public IServiceRemotingResponseMessageBody Deserialize(IIncomingMessageBody messageBody)
          {

             using (var sr = new StreamReader(messageBody.GetReceivedBuffer()))
             {
               using (var reader = new JsonTextReader(sr))
               {
                 var obj = serializer.Deserialize<JsonBody>(reader);
                 return obj;
               }
             }
           }
       }
    ```
    ```csharp
    class JsonBody : WrappedMessage, IServiceRemotingRequestMessageBody, IServiceRemotingResponseMessageBody
    {
          public JsonBody(object wrapped)
          {
            this.Value = wrapped;
          }

          public void SetParameter(int position, string parameName, object parameter)
          {  //Not Needed if you are using WrappedMessage
            throw new NotImplementedException();
          }

          public object GetParameter(int position, string parameName, Type paramType)
          {
            //Not Needed if you are using WrappedMessage
            throw new NotImplementedException();
          }

          public void Set(object response)
          { //Not Needed if you are using WrappedMessage
            throw new NotImplementedException();
          }

          public object Get(Type paramType)
          {  //Not Needed if you are using WrappedMessage
            throw new NotImplementedException();
          }
    }
    ```

2.    既定のシリアル化プロバイダーをリモート処理リスナー用の JsonSerializationProvider でオーバーライドします。

  ```csharp
  protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
   {
       return new[]
       {
           new ServiceInstanceListener((c) =>
           {
               return new FabricTransportServiceRemotingListener(c, this,
                   new ServiceRemotingJsonSerializationProvider());
           })
       };
   }
  ```

3.    既定のシリアル化プロバイダーをリモート処理クライアント ファクトリ用の JsonSerializationProvider でオーバーライドします。

```csharp
var proxyFactory = new ServiceProxyFactory((c) =>
{
    return new FabricTransportServiceRemotingClientFactory(
    serializationProvider: new ServiceRemotingJsonSerializationProvider());
  });
  ```
## <a name="next-steps"></a>次の手順
* [Reliable Services の OWIN 対応 Web API](service-fabric-reliable-services-communication-webapi.md)
* [Reliable Services との WCF 通信](service-fabric-reliable-services-communication-wcf.md)
* [Reliable Services の通信のセキュリティ保護](service-fabric-reliable-services-secure-communication.md)
