<properties
   pageTitle="Service Fabric のサービスのセキュリティで保護された通信 | Microsoft Azure"
   description="Service Fabric クラスターで実行されている信頼性の高いサービスの通信をセキュリティで保護する方法の概要です。"
   services="service-fabric"
   documentationCenter=".net"
   authors="punewa"
   manager="timlt"
   editor="vturecek"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required"
   ms.date="03/22/2016"
   ms.author="punewa"/>

# Service Fabric のサービスのセキュリティで保護された通信
セキュリティは、通信の最も重要な側面の 1 つです。Reliable Services アプリケーション フレームワークには、使用可能な構築済みの通信スタックとツールがいくつかあります。この記事では、サービスのリモート処理と WCF の通信スタックを使用する際にセキュリティを設定する方法について説明します。

## サービスのリモート処理を使用する際にサービスをセキュリティで保護する
サービスのセキュリティ保護されたリモート処理の設定は、次の手順に従って実行します。

1. 信頼性の高いサービスのリモート処理を設定する方法について説明した、前の[例](service-fabric-reliable-services-communication-remoting.md)を使用しましょう。まず、サービスのリモート プロシージャ コールに使用できるメソッドを定義するインターフェイス `IHelloWorldStateful` を使用します。さらに、そのサービスでは、`Microsoft.ServiceFabric.Services.Remoting.FabricTransport.Runtime` 名前空間で宣言されている `FabricTransportServiceRemotingListener` を使用します。これは、リモート処理機能を提供する `ICommunicationListener` の実装です。

    ```csharp
    public interface IHelloWorldStateful : IService
    {
        Task<string> GetHelloWorld();
    }

    internal class HelloWorldStateful : StatefulService, IHelloWorldStateful
    {
        protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
        {
            return new[]{
                    new ServiceReplicaListener(
                        (context) => new FabricTransportServiceRemotingListener(context,this))};
        }

        public Task<string> GetHelloWorld()
        {
            return Task.FromResult("Hello World!");
        }
    }
    ```

2. リスナー設定とセキュリティ資格情報を追加します。

    サービスの通信のセキュリティ保護に使用する証明書は、クラスター内のすべてのノードにインストールされることが想定されています。リスナー設定とセキュリティ資格情報を指定する方法は 2 とおりあります。

    1. サービス コードで直接指定します。

        ```csharp
        protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
        {
            FabricTransportListenerSettings listnerSettings = new FabricTransportListenerSettings
            {
                MaxMessageSize = 10000000,
                SecurityCredentials = GetSecurityCredentials()
            };
            return new[]
            {
                new ServiceReplicaListener(
                    (context) => new FabricTransportServiceRemotingListener(context,this,listnerSettings))
            };
        }

        private static SecurityCredentials GetSecurityCredentials()
        {
            // Provide certificate details.
            var x509Credentials = new X509Credentials
            {
                FindType = X509FindType.FindByThumbprint,
                FindValue = "4FEF3950642138446CC364A396E1E881DB76B48C",
                StoreLocation = StoreLocation.LocalMachine,
                StoreName = "My",
                ProtectionLevel = ProtectionLevel.EncryptAndSign
            };
            x509Credentials.RemoteCommonNames.Add("ServiceFabric-Test-Cert");
            return x509Credentials;
        }
        ```
    2. [構成パッケージ](service-fabric-application-model.md)を使用します。

        settings.xml に `TransportSettings` セクションを追加します。

        ```xml
        <!--Section name should always end with "TransportSettings"-->
        <!--Here we are using a prefix "HelloWorldStateful"-->
        <Section Name="HelloWorldStatefulTransportSettings">
            <Parameter Name="MaxMessageSize" Value="10000000" />
            <Parameter Name="SecurityCredentialsType" Value="X509" />
            <Parameter Name="CertificateFindType" Value="FindByThumbprint" />
            <Parameter Name="CertificateFindValue" Value="4FEF3950642138446CC364A396E1E881DB76B48C" />
            <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
            <Parameter Name="CertificateStoreName" Value="My" />
            <Parameter Name="CertificateProtectionLevel" Value="EncryptAndSign" />
            <Parameter Name="CertificateRemoteCommonNames" Value="ServiceFabric-Test-Cert" />
        </Section>
        ```

        この場合、`CreateServiceReplicaListeners` メソッドは次のようになります。

        ```csharp
        protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
        {
            return new[]
            {
                new ServiceReplicaListener(
                    (context) => new FabricTransportServiceRemotingListener(
                        context,this,FabricTransportListenerSettings.LoadFrom("HelloWorldStateful")))
            };
        }
        ```

         プレフィックスを指定せずに settings.xml に `TransportSettings` セクションを追加すると、`FabricTransportListenerSettings` は、既定で、このセクションからすべての設定を読み込みます。

         ```xml
         <!--"TransportSettings" section without any prefix-->
         <Section Name="TransportSettings">
             ...
         </Section>
         ```
         この場合、`CreateServiceReplicaListeners` メソッドは次のようになります。

         ```csharp
         protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
         {
             return new[]
             {
                 return new[]{
                         new ServiceReplicaListener(
                             (context) => new FabricTransportServiceRemotingListener(context,this))};
             };
         }
         ```

3. サービス プロキシを作成するための `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxy` クラスを使用する代わりに、リモート処理スタックを使用して、セキュリティで保護されたサービスでメソッドを呼び出すときに、`Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxyFactory` を使用して、`SecurityCredentials` を含む `FabricTransportSettings` を渡します。

    ```csharp

    var x509Credentials = new X509Credentials
    {
        FindType = X509FindType.FindByThumbprint,
        FindValue = "4FEF3950642138446CC364A396E1E881DB76B48C",
        StoreLocation = StoreLocation.LocalMachine,
        StoreName = "My",
        ProtectionLevel = ProtectionLevel.EncryptAndSign
    };
    x509Credentials.RemoteCommonNames.Add("ServiceFabric-Test-Cert");

    FabricTransportSettings transportSettings = new FabricTransportSettings
    {
        SecurityCredentials = x509Credentials,
    };

    ServiceProxyFactory serviceProxyFactory = new ServiceProxyFactory(
        (c) => new FabricTransportServiceRemotingClientFactory(transportSettings));

    IHelloWorldStateful client = serviceProxyFactory.CreateServiceProxy<IHelloWorldStateful>(
        new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```

    クライアント コードがサービスの一部として実行されている場合は、settings.xml から `FabricTransportSettings` を読み込むことができます。上記のように、サービス コードに似た TransportSettings セクションを作成します。クライアント コードを次のように変更します。

    ```csharp

    ServiceProxyFactory serviceProxyFactory = new ServiceProxyFactory(
        (c) => new FabricTransportServiceRemotingClientFactory(FabricTransportSettings.LoadFrom("TransportSettingsPrefix")));

    IHelloWorldStateful client = serviceProxyFactory.CreateServiceProxy<IHelloWorldStateful>(
        new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```

    クライアントがサービスの一部として実行されていない場合は、client\_name.exe と同じ場所に client\_name.settings.xml ファイルを作成し、そのファイル内に TransportSettings セクションを作成できます。

    サービスと同様、クライアントの settings.xml/client\_name.settings.xml でも、*プレフィックス*を指定せずに `TransportSettings` セクションを追加すると、`FabricTransportSettings` は、既定で、このセクションからすべての設定を読み込みます。

    その場合は、上記のコードがさらに簡略化されます。

    ```csharp

    IHelloWorldStateful client = ServiceProxy.Create<IHelloWorldStateful>(
                 new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```

## WCF ベースの通信スタックを使用する場合にサービスをセキュリティで保護する

ここでは、信頼性の高いサービスの Web ベースの通信を設定し、それをセキュリティで保護されるように拡張する方法について説明した、前の[例](service-fabric-reliable-services-communication-wcf.md)を使用します。

1. サービスでは、セキュリティで保護されている `WcfCommunicationListener` を作成する必要があります。そのためには、`CreateServiceReplicaListeners` メソッドを変更する必要があります。

    ```csharp
    protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
    {
        return new[]
        {
            new ServiceReplicaListener(
                this.CreateWcfCommunicationListener)
        };
    }

    private WcfCommunicationListener<ICalculator> CreateWcfCommunicationListener(StatefulServiceContext context)
    {
       var wcfCommunicationListner = new WcfCommunicationListener<ICalculator>(
            serviceContext:context,
            wcfServiceObject:this,
            // For this example we will be using NetTcpBinding
            listenerBinding: GetNetTcpBinding(),
            endpointResourceName:"WcfServiceEndpoint");

        // Add certificate details in the servicehost credentials.
        wcfCommunicationListner.ServiceHost.Credentials.ServiceCertificate.SetCertificate(
            StoreLocation.LocalMachine,
            StoreName.My,
            X509FindType.FindByThumbprint,
            "9DC906B169DC4FAFFD1697AC781E806790749D2F");
        return wcfCommunicationListner;
    }

    private static NetTcpBinding GetNetTcpBinding()
    {
        NetTcpBinding b = new NetTcpBinding(SecurityMode.TransportWithMessageCredential);
        b.Security.Message.ClientCredentialType = MessageCredentialType.Certificate;
        return b;
    }
    ```
    
2. クライアントでは、前の[例](service-fabric-reliable-services-communication-wcf.md)で作成した `WcfCommunicationClient` クラスは変更されません。ただし、`WcfCommunicationClientFactory` の `CreateClientAsync` メソッドをオーバーライドする必要があります。

    ```csharp
    public class SecureWcfCommunicationClientFactory<TServiceContract> : WcfCommunicationClientFactory<TServiceContract> where TServiceContract : class
    {
        private readonly Binding clientBinding;
        private readonly object callbackObject;
        public SecureWcfCommunicationClientFactory(
            Binding clientBinding,
            IEnumerable<IExceptionHandler> exceptionHandlers = null,
            IServicePartitionResolver servicePartitionResolver = null,
            string traceId = null,
            object callback = null)
            : base(clientBinding, exceptionHandlers, servicePartitionResolver,traceId,callback)
        {
            this.clientBinding = clientBinding;
            this.callbackObject = callback;
        }

        protected override Task<WcfCommunicationClient<TServiceContract>> CreateClientAsync(string endpoint, CancellationToken cancellationToken)
        {
            var endpointAddress = new EndpointAddress(new Uri(endpoint));
            ChannelFactory<TServiceContract> channelFactory;
            if (this.callbackObject != null)
            {
                channelFactory = new DuplexChannelFactory<TServiceContract>(
                this.callbackObject,
                this.clientBinding,
                endpointAddress);
            }
            else
            {
                channelFactory = new ChannelFactory<TServiceContract>(this.clientBinding, endpointAddress);
            }
            // Add certificate details to the ChannelFactory credentials.
            // These credentials will be used by the clients created by the
            // SecureWcfCommunicationClientFactory.  
            channelFactory.Credentials.ClientCertificate.SetCertificate(
                StoreLocation.LocalMachine,
                StoreName.My,
                X509FindType.FindByThumbprint,
                "9DC906B169DC4FAFFD1697AC781E806790749D2F");
            var channel = channelFactory.CreateChannel();
            var clientChannel = ((IClientChannel)channel);
            clientChannel.OperationTimeout = this.clientBinding.ReceiveTimeout;
            return Task.FromResult(this.CreateWcfCommunicationClient(channel));
        }
    }
    ```

    `SecureWcfCommunicationClientFactory` を使用して `WcfCommunicationClient` を作成します。クライアントを使用して、サービス メソッドを呼び出します。

    ```csharp
    IServicePartitionResolver partitionResolver = ServicePartitionResolver.GetDefault();

    var wcfClientFactory = new SecureWcfCommunicationClientFactory<ICalculator>(clientBinding: GetNetTcpBinding(), servicePartitionResolver: partitionResolver);

    var calculatorServiceCommunicationClient =  new WcfCommunicationClient(
        wcfClientFactory,
        ServiceUri,
        ServicePartitionKey.Singleton);

    var result = calculatorServiceCommunicationClient.InvokeWithRetryAsync(
        client => client.Channel.Add(2, 3)).Result;
    ```

## 次のステップ

* [Reliable Services の OWIN 対応 Web API](service-fabric-reliable-services-communication-webapi.md)

<!---HONumber=AcomDC_0330_2016-->