<properties
   pageTitle="OWIN 自己ホストによる Service Fabric Web API |Microsoft Azure"
   description="この Service Fabric の記事では、Reliable Services で OWIN 自己ホストと ASP.NET Web API を使用して、サービスの通信を実装する方法を説明しています。"
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
   ms.date="07/23/2015"
   ms.author="vturecek"/>

# OWIN 自己ホストによる Microsoft Azure Service Fabric Web API の概要

Service Fabric は、サービスがユーザーや相互に通信する方法を決定する際に役立ちます。このチュートリアルでは、Service Fabric の*Reliable Services* API で、ASP.NET Web API と OWIN 自己ホストを使用して、サービスの通信を実装することに焦点を合わせています。*Reliable Services *のプラグ可能な通信 API について詳しく説明し、例として使用する Web API によってサービスのカスタム通信リスナーを設定する方法を手順を追って示します。Web API 通信リスナーの完全な例を参照するには、[GitHub の Service Fabric WebApplication のサンプル](https://github.com/Azure/servicefabric-samples/tree/master/samples/Services/VS2015/WebApplication)を確認してください。


## Service Fabric の Web API の概要

ASP.NET Web API は、.NET Framework に基づいて HTTP API を構築するための人気のある強力なフレームワークです。Web API に詳しくない場合は、[www.asp.net/webapi](http://www.asp.net/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api) に移動して、詳細について参照してください。

Service Fabric の Web API は、馴染みのある同じ ASP.NET Web API です。違いは Web API アプリケーションの*ホスト*の方法にあります (ヒント: IIS を使用しません)。違いを深く理解するために、2 つの部分に分けてみましょう。

 1. Web API アプリケーション (コント ローラー、モデルなど)
 2. ホスト (Web サーバー、通常 IIS)

Web API アプリケーション自体はここでは変わりありません。これまでに作成したことがある Web API アプリケーションと違いはなく、アプリケーション コードのほとんどを単純にそっくり移動できるはずです。アプリケーションのホストは、IIS でホストしていた場合はこれまでと多少異なる場合があります。ただし、ホスト部分に入る前に、馴染みのある部分である Web API アプリケーションから始めましょう。


## Web API アプリケーションの設定

Visual Studio 2015 で、1 つのステートレス サービスと新しいアプリケーションを作成することから始めます。

![Create a new Service Fabric application](media/service-fabric-reliable-services-communication-webapi/webapi-newproject.png)

![Create a single stateless service](media/service-fabric-reliable-services-communication-webapi/webapi-newproject2.png)

これにより、Web API アプリケーションをホストする空のステートレス サービスが得られます。アプリケーションを最初からセットアップして、どのようにそれをまとめるかを見ていきます。

最初の手順は、Web API のいくつかの NuGet パッケージを取得することです。使用するパッケージは **Microsoft.AspNet.WebApi.OwinSelfHost** です。このパッケージには、必要なすべての Web API パッケージと、後で重要になる*ホスト* パッケージが含まれます。

![](media/service-fabric-reliable-services-communication-webapi/webapi-nuget.png)

パッケージがインストールされたら、基本的な Web API プロジェクト構造の構築を開始できます。Web API を使用していた場合、プロジェクトの構造は非常に馴染みがあるように見えるはずです。基本的な Web API ディレクトリの作成から始めます。

 + App\_Start
 + コントローラー
 + モデル

App\_Start ディレクトリに基本的な Web API 構成クラスを追加します。

 + FormatterConfig.cs

```csharp

namespace WebApi
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

 + RouteConfig.cs

```csharp

namespace WebApi
{
    using System.Web.Http;

    public static class RouteConfig
    {
        public static void RegisterRoutes(HttpRouteCollection routes)
        {
            routes.MapHttpRoute(
                    name: "DefaultApi",
                    routeTemplate: "api/{controller}/{id}",
                    defaults: new { controller = "Default", id = RouteParameter.Optional }
                );
        }
    }
}

```

Controllers ディレクトリに既定のコントローラーを追加します。

 + DefaultController.cs

```csharp

namespace WebApi.Controllers
{
    using System.Collections.Generic;
    using System.Web.Http;

    public class DefaultController : ApiController
    {
        // GET api/values
        public IEnumerable<string> Get()
        {
            return new string[] { "value1", "value2" };
        }

        // GET api/values/5
        public string Get(int id)
        {
            return "value";
        }

        // POST api/values
        public void Post([FromBody]string value)
        {
        }

        // PUT api/values/5
        public void Put(int id, [FromBody]string value)
        {
        }

        // DELETE api/values/5
        public void Delete(int id)
        {
        }
    }
}

```

最後に、プロジェクト ルートにルーティング、フォーマッタ、およびその他の構成セットアップを登録するスタートアップ クラスを追加します。これは、Web API が*ホスト*にプラグインする場所でもあり、後で再度参照します。スタートアップ クラスのセットアップ中に、構成メソッドを定義するスタートアップ クラスに *IOwinAppBuilder* というインターフェイスを作成します。Web API が機能するために技術的に必須ではありませんが、後でスタートアップ クラスを柔軟に使用できるようになります。

 + Startup.cs

```csharp

namespace WebApi
{
    using Owin;
    using System.Web.Http;

    public class Startup : IOwinAppBuilder
    {
        public void Configuration(IAppBuilder appBuilder)
        {
            HttpConfiguration config = new HttpConfiguration();

            FormatterConfig.ConfigureFormatters(config.Formatters);
            RouteConfig.RegisterRoutes(config.Routes);

            appBuilder.UseWebApi(config);
        }
    }
}

```

 + IOwinAppBuilder.cs

```csharp

namespace WebApi
{
    using Owin;

    public interface IOwinAppBuilder
    {
        void Configuration(IAppBuilder appBuilder);
    }
}

```

以上が、アプリケーション部分です。この時点では、ごく基本的な Web API プロジェクト レイアウトをセットアップしただけです。過去に作成した Web API プロジェクトや基本的な Web API テンプレートと比較して、今までのところそれほど大きく違うように見えないはずです。ビジネス ロジックは、通常どおりに、コント ローラーとモデルに進みます。

実際にそれを実行できるように、ホストをどのように処理すればよいでしょうか。


## サービスのホスト

Service Fabric では、サービスは*サービス ホスト プロセス* (サービス コードを実行する実行可能ファイル) で実行します。Reliable Services API を使用してサービスを作成する場合、および実際に .NET の Service Fabric 上のサービスを作成するほとんどの場合に、サービス プロジェクトは、サービスの種類を登録して、コードを実行する .EXE にコンパイルするだけです。実際に、ステートレス サービス プロジェクトの **Program.cs** を開いた場合、次が表示されるはずです。

```csharp

public class Program
{
    public static void Main(string[] args)
    {
        try
        {
            using (FabricRuntime fabricRuntime = FabricRuntime.Create())
            {
                fabricRuntime.RegisterServiceType(Service.ServiceTypeName, typeof(Service));

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

![](media/service-fabric-reliable-services-communication-webapi/webapi-projectproperties.png)

サービス ホスト プロセスとサービス登録の詳細についてはここで取り上げませんが、今のところ、**サービス コードが、独自のプロセスで実行している**ことを知っておくことが重要です。

## OWIN ホストによる自己ホスト型 Web API

Web API アプリケーション コードが、その独自のプロセスでホストされている場合、それをどのようにして Web サーバーに接続するのでしょうか。 [OWIN](http://owin.org/) に入ります。OWIN は .NET Web アプリケーションと Web サーバー間の単なるコントラクトです。従来、ASP.NET (MVC 5 まで) では、Web アプリケーションは System.Web 経由で IIS に厳密に結合されていました。しかし、Web API は OWIN を実装しているため、ホストされる Web サーバーから分離した Web アプリケーションを書くことができます。これにより、独自のプロセスで起動でき、先述の Service Fabric ホスティング モデルに完全に適合する*自己ホスト* OWIN Web サーバーを使用できます。

この記事では、Web API アプリケーションの OWIN ホストとして Katana を使用します。Katana は、オープン ソース OWIN ホスト実装です。

> [AZURE.NOTE]Katana の詳細については、[Katana サイト](http://www.asp.net/aspnet/overview/owin-and-katana/an-overview-of-project-katana)に移動し、Katana を使用して、Web API を自己ホストする方法の簡単な概要については、この記事の[OWIN を使用して ASP.NET Web API 2 を自己ホストする](http://www.asp.net/web-api/overview/hosting-aspnet-web-api/use-owin-to-self-host-web-api)方法を確認してください。


## Web サーバーのセットアップ

Reliable Services API は、ビジネス ロジックに 2 つのエントリ ポイントを提供します。

 + 主に実行時間の長いコンピューティング ワークロード用に意図された、任意のワークロードの実行を開始できる無期限のエントリ ポイント メソッド。

```csharp

protected override async Task RunAsync(CancellationToken cancellationToken)
{
    ...
}

```

 + 選択した通信スタックをプラグインできる通信エントリ ポイント。

```csharp

protected override ICommunicationListener CreateCommunicationListener()
{
    ...
}

```

Web サーバーおよび Websocket などの今後使用する可能性のある他の任意の通信スタックは、システムに正しく統合されるように ICommunicationListener インターフェイスを使用する必要があります。この理由は、次の手順で明らかになります。

まず ICommunicationListener を実装する OwinCommunicationListener というクラスを作成します。

 + OwinCommunicationListener.cs:

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
    using Microsoft.ServiceFabric.Services;

    public class OwinCommunicationListener : ICommunicationListener
    {
        public void Abort()
        {
        }

        public Task CloseAsync(CancellationToken cancellationToken)
        {
        }

        public void Initialize(ServiceInitializationParameters serviceInitializationParameters)
        {
        }

        public Task<string> OpenAsync(CancellationToken cancellationToken)
        {
        }
    }
}

```

ICommunicationListener インターフェイスは、サービスの通信リスナーを管理する 4 つのメソッドを提供します。

 + **Initialize**: ここでは通常、サービスがリッスンするアドレスを設定します。Web サーバーの場合、ここで URL を設定します。
 + **OpenAsync**: 要求のリッスンを開始します。
 + **CloseAsync**: 要求のリッスンを停止し、実行中の要求を完了して、正常にシャット ダウンします。
 + **Abort**: すべてのものをキャンセルし、ただちに停止します。

最初に、URL パス プレフィックスのプライベート クラス メンバーと以前に作成した **Startup** クラスを追加します。これらは、コンストラクターによって初期化され、後でリッスンする URL を設定するときに使用されます。さらに、初期化時と、後でサーバーが起動したときに、それぞれ作成されるリッスンしているアドレスとサーバー ハンドルを保存するためのプライベート クラス メンバーを追加します。

```csharp

public class OwinCommunicationListener : ICommunicationListener
{
    private readonly IOwinAppBuilder startup;
    private readonly string appRoot;
    private IDisposable serverHandle;
    private string listeningAddress;

    public OwinCommunicationListener(string appRoot, IOwinAppBuilder startup)
    {
        this.startup = startup;
        this.appRoot = appRoot;
    }

    ...

```

### Initialize

ここで、Web サーバーの URL を設定します。これを実行するには、いくつかの情報が必要です。

 + **URL パス プレフィックス**。オプションですが、アプリケーションで複数の Web サービスを安全にホストできるように、ここでこれを設定することをお勧めします。
 + **ポート**。

Web サーバーのポートを取得する前に、Service Fabric が、アプリケーションとそれが実行される基盤のオペレーティング システム間のバッファーとして機能するアプリケーション層を提供することを理解しておくことが重要です。そのために Service Fabric は、サービスの*エンドポイント*を構成する方法を提供します。Service Fabric は、サービスでエンドポイントを確実に使用できるように準備するため、ユーザーは基盤の OS 環境で、自分でそれを構成する必要はありません。これにより、さまざまな環境で、Service Fabric アプリケーションを変更する必要なく簡単にホストできます (たとえば、Azure や独自のデータ センターで、同じアプリケーションをホストできます)。

PackageRoot\\ServiceManifest.xml で HTTP エンドポイントを構成します。

```xml

<Resources>
    <Endpoints>
        <Endpoint Name="ServiceEndpoint" Type="Input" Protocol="http" Port="80" />
    </Endpoints>
</Resources>

```

サービス ホスト プロセスは制限付き資格情報 (Windows 上のネットワーク サービス) で実行されますが、これはサービスが独自の HTTP エンドポイントを設定するためにアクセスできないことを意味するため、この手順が重要になります。エンドポイント構成を使用することによって、Service Fabric は、エンドポイントを構成する標準の場所を提供しながら、サービスがリッスンする URL の正しい ACL を設定することを認識します。

OwinCommunicationListener.cs に戻り、Initialize メソッドでエンドポイント情報を取得し、ポートを取得します。サービスがリッスンする URL を作成し、以前に作成したクラス メンバー変数にそれを保存します。これは、OpenAsync で Web サーバーを起動するために使われます。

```csharp

public void Initialize(ServiceInitializationParameters serviceInitializationParameters)
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
}

```

ここで "Http://+" が使われていることに注意してください。これは、Web サーバーが、localhost、FQDN、マシン IP など、使用可能なすべてのアドレスで確実にリッスンするようにするためです。

### OpenAsync

OpenAsync は Initialize の後に、プラットフォームによって呼び出されます。ここでは、Initialize で作成されたアドレスを使用して、Web サーバーを開きます。

OpenAsync を実装することは、Web サーバー (または任意の通信スタック) が、サービスで RunAsync() から直接開かれるのではなく、ICommunicationListener として実装されている最も重要な理由の 1 つです。OpenAsync からの戻り値は、Web サーバーがリッスンしているアドレスです。このアドレスがシステムに返されると、システムはアドレスをサービスに登録します。Service Fabric は、クライアントやその他のサービスがサービス名でこのアドレスを要求できるようにする API を提供します。これが重要であるのは、リソースの分散と可用性のために、サービスがクラスター内を移動し、サービスのアドレスが静的でないためです。これは、クライアントがサービスのリッスンしているアドレスを解決できるようにするメカニズムです。

それを考慮して、OpenAsync は Web サーバーを起動し、リッスンしているアドレスを返します。それは "http://+" でリッスンしていますが、アドレスを返す前に、それが現在存在しているノードの IP または FQDN で "+" を置き換えます。この理由は、メソッドによって返されるこのアドレスが、システムに登録されたものであり、クライアントやその他のサービスがサービスのアドレスを要求したときに表示されるものであるためです。クライアントがそれに正しく接続するためには、アドレスの実際の IP または FQDN が必要です。

```csharp

public Task<string> OpenAsync(CancellationToken cancellationToken)
{
    this.serverHandle = WebApp.Start(this.listeningAddress, appBuilder => this.startup.Configuration(appBuilder));

    string resultAddress = this.listeningAddress.Replace("+", FabricRuntime.GetNodeContext().IPAddressOrFQDN);

    ServiceEventSource.Current.Message("Listening on {0}", resultAddress);

    return Task.FromResult(resultAddress);
}

```

これは、コンストラクターで OwinCommunicationListener に渡された **Startup** クラスを参照します。この Startup インスタンスは、Web サーバーが Web API アプリケーションを起動するために使用します。

後でアプリケーションを実行したときに、Web サーバーが正常に起動していることを知らせるため、診断イベント ウィンドウに ServiceEventSource.Current.Message() 行が表示されます。

### CloseAsync と Abort

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

この例の実装では、CloseAsync と Abort のどちらも単に Web サーバーを停止するだけです。CloseAsync で、実行中の要求の完了を待ってから戻るなど、Web サーバーのより適切に調整されたシャットダウンを実行することもできます。

## Web サーバーの起動

これで、OwinCommunicationListener のインスタンスを作成して、返し、Web サーバーを起動する準備が整いました。サービス クラス (Service.cs) に戻り、**CreateCommunicationListener()** メソッドをオーバーライドします。

```csharp

protected override ICommunicationListener CreateCommunicationListener()
{
	return new OwinCommunicationListener("api", new Startup());
}

```

ここで、Web API *アプリケーション*と OWIN *ホスト*が最終的に接触します。*ホスト* (\* \* OwinCommunicationListener \* \*) に*アプリケーション* (**Startup** 経由の Web API) のインスタンスが与えられ、Service Fabric がそのライフサイクルを管理します。この同じパターンは、一般に通信スタックでも従うことができます。

## まとめ

この例では、RunAsync() メソッドで何も実行する必要はないため、オーバーライドを単純に削除できます。

最後のサービス実装は、通信リスナーを作成する必要があるだけであるため、きわめて単純なはずです。

```csharp

namespace WebApi
{
    using Microsoft.ServiceFabric.Services;

    public class Service : StatelessService
    {
        public const string ServiceTypeName = "WebApiType";

        protected override ICommunicationListener CreateCommunicationListener()
        {
            return new OwinCommunicationListener("api", new Startup());
        }
    }
}

```

さらに、OwinCommunicationListener クラスを完成させます。

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
    using Microsoft.ServiceFabric.Services;

    public class OwinCommunicationListener : ICommunicationListener
    {
        private readonly IOwinAppBuilder startup;
        private readonly string appRoot;
        private IDisposable serverHandle;
        private string listeningAddress;

        public OwinCommunicationListener(string appRoot, IOwinAppBuilder startup)
        {
            this.startup = startup;
            this.appRoot = appRoot;
        }

        public void Initialize(ServiceInitializationParameters serviceInitializationParameters)
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
        }

        public Task<string> OpenAsync(CancellationToken cancellationToken)
        {
            this.serverHandle = WebApp.Start(this.listeningAddress, appBuilder => this.startup.Configuration(appBuilder));

            string resultAddress = this.listeningAddress.Replace("+", FabricRuntime.GetNodeContext().IPAddressOrFQDN);

            ServiceEventSource.Current.Message("Listening on {0}", resultAddress);

            return Task.FromResult(resultAddress);
        }

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
    }
}

```

すべての部分を適切に配置したら、プロジェクトは、Reliable Services API エントリ ポイントと OWIN ホストのある一般的な Web API アプリケーションのようになったはずです。


![](media/service-fabric-reliable-services-communication-webapi/webapi-projectstructure.png)

## Web ブラウザーによる実行と接続

[開発環境を設定](service-fabric-get-started.md)していない場合は、設定します。


これでサービスを構築し、デプロイできます。Visual Studio で **F5** キーを押して、アプリケーションを構築し、デプロイします。[診断イベント] ウィンドウに、Web サーバーが **http://localhost:80/api** で開かれたことを示すメッセージが表示されるはずです。


![](media/service-fabric-reliable-services-communication-webapi/webapi-diagnostics.png)

> [AZURE.NOTE]ポートが既に、コンピューターの別のプロセスによって開かれている場合、リスナーを開けなかったことを示すエラーが表示されることがあります。その場合は、ServiceManifest.xml のエンドポイント構成で、別のポートを使用してみてください。


サービスが実行したら、ブラウザーを開いて、[http://localhost/api](http://localhost/api) に移動して、それをテストします。

## スケール アウト

ステートレス Web アプリをスケール アウトすることは、コンピューターを追加して、それらで Web アプリを実行することを意味します。Service Fabric のオーケストレーション エンジンは、新しいノードがクラスターに追加されるたびに、自動的にこれを実行します。ステートレス サービスのインスタンスを作成する場合、作成するインスタンスの数を指定できます。Service Fabric はそれに従って、クラスターのノードにその数のインスタンスを配置し、1 つのノードに複数のインスタンスを作成しないようにします。インスタンス数に「-1」を指定して、常にすべてのノードにインスタンスを作成するように、Service Fabric に指示することもできます。これにより、クラスターにノードを追加して、クラスターをスケールアウトするたびに、新しいノードにステートレス サービスのインスタンスが作成されることが保証されます。この値はサービス インスタンスのプロパティであるため、PowerShell によってサービス インスタンスを作成するときに設定します。

```powershell

New-ServiceFabricService -ApplicationName "fabric:/WebServiceApplication" -ServiceName "fabric:/WebServiceApplication/WebService" -ServiceTypeName "WebServiceType" -Stateless -PartitionSchemeSingleton -InstanceCount -1

```

または Visual Studio ステートレス サービス プロジェクトで既定のサービスを定義するときに設定します。

```xml

<DefaultServices>
  <Service Name="WebService">
    <StatelessService ServiceTypeName="WebServiceType" InstanceCount="-1">
      <SingletonPartition />
    </StatelessService>
  </Service>
</DefaultServices>

```

アプリケーションとサービス インスタンスの作成の詳細については、[アプリケーションをデプロイおよび削除する方法](service-fabric-deploy-remove-applications.md)を参照してください。

## ASP.NET 5

ASP.NET 5 でも、Web アプリケーションで*ホスト*から*アプリケーション*を分離する概念とプログラミング モデルは同じです。それは他の形式の通信にも適用できます。さらに、ASP.NET 5 では*ホスト*は異なることがありますが、Web API *アプリケーション*層は同じままで、そこにアプリケーション ロジックの大半が実際に存在します。

## 次のステップ

[Visual Studio での Service Fabric アプリケーションのデバッグ](service-fabric-debugging-your-application.md)

<!---HONumber=July15_HO5-->