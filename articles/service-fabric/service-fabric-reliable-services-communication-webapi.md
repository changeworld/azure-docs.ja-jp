<properties
   pageTitle="ASP.NET Web API によるサービス通信 | Microsoft Azure"
   description="Reliable Services API で OWIN 自己ホストと ASP.NET Web API を使用して、サービスの通信を実装する方法を説明します。"
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required"
   ms.date="11/13/2015"
   ms.author="vturecek"/>

# はじめに: OWIN 自己ホストによる Service Fabric Web API サービス

Azure Service Fabric は、サービスとユーザーとの通信および各ユーザーとの通信の方法を決定する際に役立ちます。このチュートリアルでは、Service Fabric の Reliable Services API で、ASP.NET Web API と Open Web Interface for .NET (OWIN) 自己ホストを使用して、サービスの通信を実装することに焦点を合わせています。Reliable Services のプラグ可能な通信 API について詳しく説明します。また、Web API を手順を追った例で使用し、カスタム通信リスナーを設定する方法を説明しします。


## Service Fabric での Web API の概要

ASP.NET Web API は、.NET Framework に基づいて HTTP API を構築するための人気のある強力なフレームワークです。このフレームワークにまだ詳しくない場合、「[ASP.NET Web API 2 の概要](http://www.asp.net/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api)」を参照してください。

Service Fabric の Web API は、馴染みのある同じ ASP.NET Web API です。違いは、Web API アプリケーションをホストする方法です。Microsoft インターネット インフォメーション サービスは使用しません。違いを深く理解するために、2 つの部分に分けてみましょう。

 1. Web API アプリケーション (コント ローラーとモデルをなど)
 2. ホスト (Web サーバー、通常 IIS)

Web API アプリケーション自体には違いはありません。これまでに作成したことがある Web API アプリケーションと違いはなく、アプリケーション コードのほとんどを単純にそっくり移動できます。ただし、IIS でホストしていた場合、慣れているものとはアプリケーションをホストする場所が多少異なる場合があります。ホストの部分に入る前に、馴染みのある部分である Web API アプリケーションから始めましょう。


## アプリケーションを作成する

Visual Studio 2015 で、1 つのステートレス サービスと新しい Service Fabric アプリケーションを作成することから始めます。

![Create a new Service Fabric application](media/service-fabric-reliable-services-communication-webapi/webapi-newproject.png)

![Create a single stateless service](media/service-fabric-reliable-services-communication-webapi/webapi-newproject2.png)

これにより、Web API アプリケーションをホストする空のステートレス サービスが得られます。アプリケーションを最初からセットアップして、どのようにそれをまとめるかを見ていきます。

最初の手順は、Web API のいくつかの NuGet パッケージを取得することです。使用するパッケージは Microsoft.AspNet.WebApi.OwinSelfHost です。このパッケージには、必要なすべての Web API パッケージと、ホスト パッケージが含まれます。これは後で重要になります。

![NuGet パッケージ マネージャーを使用した Web API の作成](media/service-fabric-reliable-services-communication-webapi/webapi-nuget.png)

パッケージがインストールされたら、基本的な Web API プロジェクト構造の構築を開始できます。Web API を使用していた場合、プロジェクトの構造は非常に馴染みがあるように見えるはずです。基本的な Web API ディレクトリの作成から始めます。

 + App\_Start
 + コントローラー
 + モデル

App\_Start ディレクトリに基本的な Web API 構成クラスを追加します。ここでは、単に空のメディア タイプ フォーマッタ構成を追加します。

**FormatterConfig.cs**

```csharp

namespace WebApiService
{
    using System.Net.Http.Formatting;

    public static class FormatterConfig
    {
        public static void ConfigureFormatters(MediaTypeFormatterCollection formatters)
        {
        }
    }
}

```

Controllers ディレクトリに既定のコントローラーを追加します。

**DefaultController.cs**

```csharp

namespace WebApiService.Controllers
{
    using System.Collections.Generic;
    using System.Web.Http;

    [RoutePrefix("api")]
    public class DefaultController : ApiController
    {
        // GET api/values
        [Route("values")]
        public IEnumerable<string> Get()
        {
            return new string[] { "value1", "value2" };
        }

        // GET api/values/5
        [Route("values/{id}")]
        public string Get(int id)
        {
            return "value";
        }

        // POST api/values
        [Route("values")]
        public void Post([FromBody]string value)
        {
        }

        // PUT api/values/5
        [Route("values/{id}")]
        public void Put(int id, [FromBody]string value)
        {
        }

        // DELETE api/values/5
        [Route("values/{id}")]
        public void Delete(int id)
        {
        }
    }
}

```

最後に、プロジェクト ルートにルーティング、フォーマッタ、およびその他の構成セットアップを登録するスタートアップ クラスを追加します。これは、Web API がホストにプラグインする場所でもあり、後で再度参照します。スタートアップ クラスのセットアップ中に、構成メソッドを定義するスタートアップ クラスに IOwinAppBuilder というインターフェイスを作成します。Web API が機能するために技術的に必須ではありませんが、後でスタートアップ クラスを柔軟に使用できるようになります。

**Startup.cs**

```csharp

namespace WebApiService
{
    using Owin;
    using System.Web.Http;

    public class Startup : IOwinAppBuilder
    {
        public void Configuration(IAppBuilder appBuilder)
        {
            HttpConfiguration config = new HttpConfiguration();

            config.MapHttpAttributeRoutes();
            FormatterConfig.ConfigureFormatters(config.Formatters);

            appBuilder.UseWebApi(config);
        }
    }
}

```

**IOwinAppBuilder.cs**

```csharp

namespace WebApiService
{
    using Owin;

    public interface IOwinAppBuilder
    {
        void Configuration(IAppBuilder appBuilder);
    }
}

```

以上が、アプリケーション部分です。この時点では、ごく基本的な Web API プロジェクト レイアウトをセットアップしただけです。過去に作成した Web API プロジェクトや基本的な Web API テンプレートと、今のところそれほど大きく違うように見えないはずです。ビジネス ロジックは、通常どおりに、コント ローラーとモデルに進みます。

実際にそれを実行できるように、ホストをどのように処理すればよいでしょうか。


## サービスのホスト

Service Fabric では、サービスはサービス ホスト プロセス (サービス コードを実行する実行可能ファイル) で実行します。Reliable Services API を使用してサービスを作成する場合、サービス プロジェクトは、サービスの種類を登録して、コードを実行する実行可能ファイルにコンパイルされるだけです。.NET で Service Fabric のサービスを記述する場合、ほとんどこのようになります。ステートレス サービス プロジェクトの Program.cs を開いた場合、次が表示されます。

```csharp

public class Program
{
    public static void Main(string[] args)
    {
        try
        {
            using (FabricRuntime fabricRuntime = FabricRuntime.Create())
            {
                fabricRuntime.RegisterServiceType("WebApiServiceType", typeof(Service));

                Thread.Sleep(Timeout.Infinite);
            }
        }
        catch (Exception e)
        {
            ServiceEventSource.Current.ServiceHostInitializationFailed(e);
            throw;
        }
    }
}

```

コンソール アプリケーションへのエントリ ポイントのように見える場合は、それが次のようになっているためです。

サービス ホスト プロセスとサービス登録の詳細については、この記事では取り上げません。しかしここでは、サービス コードが、独自のプロセスで実行されていることを理解しておくことは重要です。

## OWIN ホストによる自己ホスト型 Web API

Web API アプリケーション コードが、その独自のプロセスでホストされている場合、それをどのようにして Web サーバーに接続するのでしょうか。 [OWIN](http://owin.org/) に入ります。OWIN は .NET Web アプリケーションと Web サーバー間の単なるコントラクトです。従来、ASP.NET (MVC 5 まで) を使用した場合、Web アプリケーションは System.Web 経由で IIS に厳密に結合されていました。しかし、Web API は OWIN を実装しているため、ホストされる Web サーバーから分離した Web アプリケーションを書くことができます。これにより、独自のプロセスで起動できる自己ホスト OWIN Web サーバーを使用できます。これは、前述の Service Fabric ホスティング モデルに適しています。

この記事では、Web API アプリケーションの OWIN ホストとして Katana を使用します。Katana は、オープン ソース OWIN ホスト実装です。

> [AZURE.NOTE]Katana の詳細については、「[Katana サイト](http://www.asp.net/aspnet/overview/owin-and-katana/an-overview-of-project-katana)」を参照してください。Katana を使用して、Web API を自己ホストする方法の概要については、「[OWIN を使用して ASP.NET Web API 2 を自己ホストする](http://www.asp.net/web-api/overview/hosting-aspnet-web-api/use-owin-to-self-host-web-api)」を参照してください。


## Web サーバーのセットアップ

Reliable Services API が提供する通信エントリ ポイントに通信スタックを接続し、ユーザーおよびクライアントがサービスに接続できるようにすることができます。

```csharp

protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    ...
}

```

Web サーバー (および Websocket などの今後使用する可能性のある他の任意の通信スタック) は、システムに正しく統合されるように ICommunicationListener インターフェイスを使用する必要があります。この理由は、次の手順で明らかになります。

まず ICommunicationListener を実装する OwinCommunicationListener というクラスを作成します。

**OwinCommunicationListener.cs**

```csharp

namespace WebApi
{
    using System;
    using System.Fabric;
    using System.Fabric.Description;
    using System.Globalization;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Owin.Hosting;
    using Microsoft.ServiceFabric.Services.Communication.Runtime;

    public class OwinCommunicationListener : ICommunicationListener
    {
        public void Abort()
        {
        }

        public Task CloseAsync(CancellationToken cancellationToken)
        {
        }

        public Task<string> OpenAsync(CancellationToken cancellationToken)
        {
        }
    }
}

```

ICommunicationListener インターフェイスは、サービスの通信リスナーを管理する 3 つのメソッドを提供します。

 - OpenAsync: 要求のリッスンを開始します。
 - CloseAsync：要求のリッスンを停止し、実行中の要求を完了して、正常にシャット ダウンします。
 - Abort：すべてのものをキャンセルし、ただちに停止します。

最初に、URL パス プレフィックスのプライベート クラス メンバーと以前に作成した Startup クラスを追加します。これらは、コンストラクターによって初期化され、後でリッスンする URL を設定するときに使用されます。さらに、初期化時と、後でサーバーが起動したときに、それぞれ作成されるリッスンしているアドレスとサーバー ハンドルを保存するためのプライベート クラス メンバーを追加します。

```csharp

public class OwinCommunicationListener : ICommunicationListener
{
    private readonly IOwinAppBuilder startup;
    private readonly string appRoot;
    private IDisposable serverHandle;
    private string listeningAddress;
    private readonly ServiceInitializationParameters serviceInitializationParameters;

    public OwinCommunicationListener(string appRoot, IOwinAppBuilder startup, ServiceInitializationParameters serviceInitializationParameters)
    {
        this.startup = startup;
        this.appRoot = appRoot;
        this.serviceInitializationParameters = serviceInitializationParameters;
    }        

    ...

```

## OpenAsync の実装

Web サーバーをセットアップするには、2 つの情報が必要です。

 - URL パス プレフィックス。オプションですが、アプリケーションで複数の Web サービスを安全にホストできるように、ここでこれを設定することをお勧めします。
 - ポート。

Web サーバーのポートを取得する前に、Service Fabric が、アプリケーションとそれが実行される基盤のオペレーティング システム間のバッファーとして機能するアプリケーション層を提供することを理解しておくことが重要です。そのために Service Fabric は、サービスのエンドポイントを構成する方法を提供します。Service Fabric では、サービスでエンドポイントを確実に使用できるようにします。そのため、ユーザーは基盤の OS 環境で、自分でそれを構成する必要はありません。これにより、さまざまな環境で、Service Fabric アプリケーションを変更する必要なく簡単にホストできます。(たとえば、Azure や独自のデータ センターで、同じアプリケーションをホストできます。)

PackageRoot\\ServiceManifest.xml で HTTP エンドポイントを構成します。

```xml

<Resources>
    <Endpoints>
        <Endpoint Name="ServiceEndpoint" Type="Input" Protocol="http" Port="80" />
    </Endpoints>
</Resources>

```

サービス ホスト プロセスは制限付き資格情報 (Windows 上のネットワーク サービス) で実行されるため、この手順が重要になります。つまり、サービスには自分で HTTP エンドポイントを設定するためのアクセスがないことを意味します。エンドポイント構成を使用することによって、Service Fabric は、サービスがリッスンする URL の正しい ACL を設定することを認識します。Service Fabric は、エンドポイントを構成する標準の場所も提供します。


OwinCommunicationListener.cs に戻り、OpenAsync の実装を始めることができます。ここで Web サーバーを開始します。最初に、エンドポイントの情報を取得し、サービスがリッスンする URL を作成します。

```csharp

public Task<string> OpenAsync(CancellationToken cancellationToken)
{
    EndpointResourceDescription serviceEndpoint = serviceInitializationParameters.CodePackageActivationContext.GetEndpoint("ServiceEndpoint");
    int port = serviceEndpoint.Port;

    this.listeningAddress = String.Format(
        CultureInfo.InvariantCulture,
        "http://+:{0}/{1}",
        port,
        String.IsNullOrWhiteSpace(this.appRoot)
            ? String.Empty
            : this.appRoot.TrimEnd('/') + '/');
    ...

```

ここで "http://+" が使われていることに注意してください。これは、Web サーバーが、localhost、FQDN、マシン IP など、使用可能なすべてのアドレスで確実にリッスンするようにするためです。

OpenAsync を実装することは、Web サーバー (または任意の通信スタック) が、サービスで `RunAsync()` から直接開かれるのではなく、ICommunicationListener として実装されている最も重要な理由の 1 つです。OpenAsync からの戻り値は、Web サーバーがリッスンしているアドレスです。このアドレスがシステムに返されると、システムはアドレスをサービスに登録します。Service Fabric は、クライアントおよびその他のサービスがサービス名でこのアドレスを要求できるようにする API を提供します。これが重要なのは、サービスのアドレスが静的でないためです。リソースの分散と可用性のために、サービスはクラスター内を移動します。これは、クライアントがサービスのリッスンしているアドレスを解決できるようにするメカニズムです。

それを考慮して、OpenAsync は Web サーバーを起動し、リッスンしているアドレスを返します。それは "http://+" でリッスンしますが、OpenAsync がアドレスを返す前に、"+" は現在存在しているノードの IP または FQDN に置き換えられます。この方法で返されるアドレスは、システムに登録されているものです。これは、サービスのアドレスを求めた際にクライアントや他のサービスに示されるものでもあります。クライアントがそれに正しく接続するためには、アドレスの実際の IP または FQDN が必要です。

```csharp

    ...

    this.serverHandle = WebApp.Start(this.listeningAddress, appBuilder => this.startup.Configuration(appBuilder));
    string publishAddress = this.listeningAddress.Replace("+", FabricRuntime.GetNodeContext().IPAddressOrFQDN);

    ServiceEventSource.Current.Message("Listening on {0}", publishAddress);

    return Task.FromResult(publishAddress);
}

```

これは、コンストラクターで OwinCommunicationListener に渡された Startup クラスを参照します。この Startup インスタンスは、Web サーバーが Web API アプリケーションを起動するために使用します。

後でアプリケーションを実行したときに、Web サーバーが正常に起動していることを知らせるため、診断イベント ウィンドウに `ServiceEventSource.Current.Message()` 行が表示されます。

## CloseAsync と Abort の実装

最後に、Web サーバーを停止する　CloseAsync と Abort の両方を実装します。Web サーバーを停止するには、OpenAsync 時に作成されたサーバー ハンドルを破棄します。

```csharp

public Task CloseAsync(CancellationToken cancellationToken)
{
    this.StopWebServer();

    return Task.FromResult(true);
}

public void Abort()
{
    this.StopWebServer();
}

private void StopWebServer()
{
    if (this.serverHandle != null)
    {
        try
        {
            this.serverHandle.Dispose();
        }
        catch (ObjectDisposedException)
        {
            // no-op
        }
    }
}

```

この例の実装では、CloseAsync と Abort のどちらも単に Web サーバーを停止するだけです。CloseAsync を選択した場合、Web サーバーをより適切に調整してシャット ダウンできます。たとえば、返される前、実行中の要求が完了するまで、シャット ダウンを待機させることができます。

## Web サーバーの起動

これで、OwinCommunicationListener のインスタンスを作成して、返し、Web サーバーを起動する準備が整いました。サービス クラス (Service.cs) に戻り、 `CreateServiceInstanceListeners()` メソッドをオーバーライドします。

```csharp

protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    return new[]
    {
        new ServiceInstanceListener(initParams => new OwinCommunicationListener("webapp", new Startup(), initParams))
    };
}

```

ここで、Web API アプリケーションと OWIN ホストが最終的に接触します。ホスト (OwinCommunicationListener) にアプリケーション (Startup 経由の Web API) のインスタンスが与えられます。その後、Service Fabric がそのライフサイクルを管理します。この同じパターンは、一般に通信スタックでも従うことができます。

## すべてをまとめた配置

この例では、`RunAsync()` メソッドで何も実行する必要はないため、オーバーライドを単純に削除できます。

最後のサービス実装は、きわめて単純なはずです。通信リスナーのみ作成する必要があります。

```csharp

namespace WebApiService
{
    using System.Collections.Generic;
    using Microsoft.ServiceFabric.Services.Communication.Runtime;
    using Microsoft.ServiceFabric.Services.Runtime;

    public class WebApiService : StatelessService
    {
        protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
        {
            return new[]
            {
                new ServiceInstanceListener(initParams => new OwinCommunicationListener("webapp", new Startup(), initParams))
            };
        }
    }
}

```

完全な `OwinCommunicationListener` クラス:

```csharp

namespace WebApiService
{
    using System;
    using System.Fabric;
    using System.Fabric.Description;
    using System.Globalization;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Owin.Hosting;
    using Microsoft.ServiceFabric.Services.Communication.Runtime;

    public class OwinCommunicationListener : ICommunicationListener
    {
        private readonly IOwinAppBuilder startup;
        private readonly string appRoot;
        private readonly ServiceInitializationParameters serviceInitializationParameters;
        private IDisposable serverHandle;
        private string listeningAddress;

        public OwinCommunicationListener(string appRoot, IOwinAppBuilder startup, ServiceInitializationParameters serviceInitializationParameters)
        {
            this.startup = startup;
            this.appRoot = appRoot;
            this.serviceInitializationParameters = serviceInitializationParameters;
        }

        public Task<string> OpenAsync(CancellationToken cancellationToken)
        {
            EndpointResourceDescription serviceEndpoint = serviceInitializationParameters.CodePackageActivationContext.GetEndpoint("ServiceEndpoint");
            int port = serviceEndpoint.Port;

            this.listeningAddress = String.Format(
                CultureInfo.InvariantCulture,
                "http://+:{0}/{1}",
                port,
                String.IsNullOrWhiteSpace(this.appRoot)
                    ? String.Empty
                    : this.appRoot.TrimEnd('/') + '/');

            this.serverHandle = WebApp.Start(this.listeningAddress, appBuilder => this.startup.Configuration(appBuilder));
            string publishAddress = this.listeningAddress.Replace("+", FabricRuntime.GetNodeContext().IPAddressOrFQDN);

            ServiceEventSource.Current.Message("Listening on {0}", publishAddress);

            return Task.FromResult(publishAddress);
        }

        public Task CloseAsync(CancellationToken cancellationToken)
        {
            ServiceEventSource.Current.Message("Close");

            this.StopWebServer();

            return Task.FromResult(true);
        }

        public void Abort()
        {
            ServiceEventSource.Current.Message("Abort");

            this.StopWebServer();
        }

        private void StopWebServer()
        {
            if (this.serverHandle != null)
            {
                try
                {
                    this.serverHandle.Dispose();
                }
                catch (ObjectDisposedException)
                {
                    // no-op
                }
            }
        }
    }
}

```

すべての部分を適切に配置したら、プロジェクトは、Reliable Services API エントリ ポイントと OWIN ホストのある一般的な Web API アプリケーションのようになったはずです。


![Reliable Services API エントリ ポイントと OWIN ホストを使用した Web API](media/service-fabric-reliable-services-communication-webapi/webapi-projectstructure.png)

## Web ブラウザーによる実行と接続

[開発環境を設定](service-fabric-get-started.md)していない場合は、設定します。


これでサービスを構築し、デプロイできます。Visual Studio で **F5** キーを押して、アプリケーションを構築し、デプロイします。[診断イベント] ウィンドウに、Web サーバーが http://localhost:80/webapp/api で開かれたことを示すメッセージが表示されます。


![Visual Studio 診断イベント ウィンドウ](media/service-fabric-reliable-services-communication-webapi/webapi-diagnostics.png)

> [AZURE.NOTE]ポートが既に、コンピューターの別のプロセスによって開かれている場合、ここでエラーが表示されます。これは、リスナーを開けなかったことを示します。その場合は、ServiceManifest.xml のエンドポイント構成で、別のポートを使用してください。


サービスが実行されたら、ブラウザーを開いて、[http://localhost/webapp/api/values](http://localhost/webapp/api/values) に移動して、それをテストします。

## スケール アウト

ステートレス Web アプリをスケール アウトすることは、コンピューターを追加して、それらで Web アプリを実行することを意味します。Service Fabric のオーケストレーション エンジンは、新しいノードがクラスターに追加されるたびに、自動的にこれを実行します。ステートレス サービスのインスタンスを作成する場合、作成するインスタンスの数を指定できます。Service Fabric は、クラスターのノードにその数のインスタンスを配置します。そして 1 つのノードに複数のインスタンスを作成しないようにします。インスタンス数に **-1** を指定して、常にすべてのノードにインスタンスを作成するように、Service Fabric に指示することもできます。これにより、クラスターにノードを追加して、クラスターをスケールアウトするたびに、新しいノードにステートレス サービスのインスタンスが作成されることが保証されます。この値はサービス インスタンスのプロパティであるため、サービス インスタンスを作成するときに設定されます。これは PowerShell を使用して、実行できます。

```powershell

New-ServiceFabricService -ApplicationName "fabric:/WebServiceApplication" -ServiceName "fabric:/WebServiceApplication/WebService" -ServiceTypeName "WebServiceType" -Stateless -PartitionSchemeSingleton -InstanceCount -1

```

これは、Visual Studio ステートレス サービス プロジェクトで既定のサービスを定義するときも設定できます。

```xml

<DefaultServices>
  <Service Name="WebService">
    <StatelessService ServiceTypeName="WebServiceType" InstanceCount="-1">
      <SingletonPartition />
    </StatelessService>
  </Service>
</DefaultServices>

```

アプリケーションおよびサービス インスタンスの作成方法の詳細については、「[アプリケーションのデプロイ](service-fabric-deploy-remove-applications.md)」を参照してください。

## ASP.NET 5

ASP.NET 5 でも、Web アプリケーションでホストからアプリケーションを分離する概念とプログラミング モデルは同じです。それは他の形式の通信にも適用できます。さらに、ASP.NET 5 ではホストが異なることがありますが、Web API アプリケーション層は同じままです。そこにアプリケーション ロジックの大半が実際に存在します。

## 次のステップ

[Visual Studio による Service Fabric アプリケーションのデバッグ](service-fabric-debugging-your-application.md)

<!---HONumber=AcomDC_0121_2016-->