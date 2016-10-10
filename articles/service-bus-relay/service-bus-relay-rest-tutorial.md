<properties
    pageTitle="リレー型メッセージングを使用した Service Bus REST チュートリアル | Microsoft Azure"
    description="REST ベースのインターフェイスを表示する簡易な Service Bus Relay ホスト アプリケーションを構築します。"
    services="service-bus-relay"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" />
<tags
    ms.service="service-bus-relay"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="09/27/2016"
    ms.author="sethm" />

# Service Bus Relay REST のチュートリアル

このチュートリアルでは、REST ベースのインターフェイスを表示する簡易な Service Bus ホスト アプリケーションを構築する方法について説明します。REST を使用すると、Web ブラウザーなどの Web クライアントから HTTP 要求を介して Service Bus API にアクセスできるようになります。

このチュートリアルでは、Windows Communication Foundation (WCF) REST プログラミング モデルを使用して、Service Bus に REST サービスを構築します。詳細については、WCF ドキュメントの「[WCF Web HTTP プログラミング モデル](https://msdn.microsoft.com/library/bb412169.aspx)」と「[サービスの設計と実装](https://msdn.microsoft.com/library/ms729746.aspx)」を参照してください。

## 手順 1: サービス名前空間の作成

最初の手順では、名前空間を作成し、Shared Access Signature (SAS) キーを取得します。名前空間は、Service Bus によって公開される各アプリケーションのアプリケーション境界を提供します。サービス名前空間が作成された時点で、SAS キーが生成されます。サービス名前空間と SAS キーの組み合わせが、アプリケーションへのアクセスを Service Bus が認証する資格情報になります。

[AZURE.INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## 手順 2: Service Bus で使用する REST ベースの WCF サービス コントラクトを定義する

他の Service Bus Service と同様に、REST スタイルのサービスを作成するときは、コントラクトを定義する必要があります。コントラクトには、ホストがサポートする操作を指定します。サービス操作は、Web サービス メソッドと考えることができます。コントラクトを作成するには、C++、C#、または Visual Basic インターフェイスを定義します。インターフェイスの各メソッドは、特定のサービス操作に対応しています。[ServiceContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicecontractattribute.aspx) 属性を各インターフェイスに適用する必要があります。また、[OperationContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx) 属性を各操作に適用する必要があります。[ServiceContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicecontractattribute.aspx) があるインターフェイスのメソッドに [OperationContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx) がない場合、そのメソッドは公開されません。これらのタスクに使用されるコードの例を手順に従って説明します。

基本の Service Bus コントラクトと REST スタイルのコントラクトの主な違いは、REST スタイルの [OperationContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx) にプロパティ [WebGetAttribute](https://msdn.microsoft.com/library/system.servicemodel.web.webgetattribute.aspx) がある点です。このプロパティを使用すると、インターフェイス内のメソッドを相手側のインターフェイスのメソッドにマップすることができます。ここでは、[WebGetAttribute](https://msdn.microsoft.com/library/system.servicemodel.web.webgetattribute.aspx) を使用してメソッドを HTTP GET にリンクします。その結果、Service Bus は、インターフェイスに送信されたコマンドをより正確に取得および解釈できるようになります。

### インターフェイスを使用して Service Bus を作成するには

1. 管理者として Visual Studio を開きます。**[スタート]** メニューの[Visual Studio] を右クリックし、**[管理者として実行]** をクリックします。

2. 新しいコンソール アプリケーション プロジェクトを作成します。**[ファイル]** メニューをクリックし、**[新規作成]**、**[プロジェクト]** の順に選択します。**[新しいプロジェクト]** ダイアログ ボックスで **[Visual C#]** をクリックします。**[コンソール アプリケーション]** テンプレートを選択し、「**ImageListener**」と名前を付けます。既定の **[場所]** を使用します。**[OK]** をクリックしてプロジェクトを作成します。

3. C# プロジェクトの場合、`Program.cs` ファイルが作成されます。このクラスには、空の `Main()` メソッドが含まれています。このメソッドは、コンソール アプリケーション プロジェクトを正常にビルドするために必要です。

4. Service Bus NuGet パッケージをインストールして、Service Bus と **System.ServiceModel.dll** への参照をプロジェクトに追加します。WCF の **System.ServiceModel** と Service Bus ライブラリへの参照が、このパッケージによって自動的に追加されます。ソリューション エクスプローラーで **ImageListener** プロジェクトを右クリックし、**[NuGet パッケージの管理]** をクリックします。**[参照]** タブをクリックして、`Microsoft Azure Service Bus` を検索します。**[インストール]** をクリックして、使用条件に同意します。

5. **System.ServiceModel.Web.dll** への参照をプロジェクトに明示的に追加する必要があります。

	a.ソリューション エクスプローラーでプロジェクト フォルダーの **[参照]** フォルダーを右クリックし、**[参照の追加]** をクリックします。

	b.**[参照の追加]** ダイアログ ボックスで、左側にある **[フレームワーク]** タブをクリックし、**[検索]** ボックスに、「**System.ServiceModel.Web**」と入力します。**[System.ServiceModel.Web]** チェック ボックスをオンにし、**[OK]** をクリックします。

6. Program.cs ファイルの先頭に次の `using` ステートメントを追加します。

	```
	using System.ServiceModel;
	using System.ServiceModel.Channels;
	using System.ServiceModel.Web;
	using System.IO;
	```

	[System.ServiceModel](https://msdn.microsoft.com/library/system.servicemodel.aspx) は、WCF の基本機能にプログラムでアクセスできる名前空間です。Service Bus は、サービス コントラクトの定義に WCF の多くのオブジェクトと属性を使用します。ほとんどの場合、Service Bus Relay アプリケーションにはこの名前空間を使用することになります。同様に、[System.ServiceModel.Channels](https://msdn.microsoft.com/library/system.servicemodel.channels.aspx) はチャネルの定義に役立ちます。チャネルは、Service Bus とクライアント Web ブラウザーとの通信を経由するオブジェクトです。最後に、[System.ServiceModel.Web](https://msdn.microsoft.com/library/system.servicemodel.web.aspx) には、Web ベースのアプリケーションを作成できる型が含まれています。

7. `ImageListener` 名前空間の名前を **Microsoft.ServiceBus.Samples** に変更します。

 	```
	namespace Microsoft.ServiceBus.Samples
	{
		...
	```

8. 名前空間の宣言の左中かっこの直後に、**IImageContract** という新しいインターフェイスを定義し、値が `http://samples.microsoft.com/ServiceModel/Relay/` の **ServiceContractAttribute** 属性をインターフェイスに適用します。この名前空間の値は、コードのスコープ全体で使用する名前空間とは異なります。この名前空間の値は、このコントラクトの一意の識別子として使用されます。値にはバージョン情報が含まれています。詳細については、「[サービスのバージョン管理](http://go.microsoft.com/fwlink/?LinkID=180498)」を参照してください。名前空間を明示的に指定すると、既定の名前空間値がコントラクト名に追加されなくなります。

	```
	[ServiceContract(Name = "ImageContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/RESTTutorial1")]
	public interface IImageContract
	{
	}
	```

9. `IImageContract` インターフェイス内で、`IImageContract` コントラクトがインターフェイスで公開している単一操作のメソッドを宣言し、パブリック Service Bus コントラクトの一部として公開するメソッドに `OperationContractAttribute` 属性を適用します。

	```
	public interface IImageContract
	{
		[OperationContract]
		Stream GetImage();
	}
	```

10. **OperationContract** 属性に、**WebGet** 値を追加します。

	```
	public interface IImageContract
	{
		[OperationContract, WebGet]
		Stream GetImage();
	}
	```

	追加後は、Service Bus から HTTP GET 要求を `GetImage` にルーティングし、`GetImage` の戻り値を HTTP GETRESPONSE 応答に変換できるようになります。このチュートリアルの後半で、Web ブラウザーを使用してこのメソッドにアクセスし、ブラウザーに画像を表示します。

11. `IImageContract` 定義の直後に、`IImageContract` インターフェイスと `IClientChannel` インターフェイスの両方から継承するチャネルを宣言します。

	```
	public interface IImageChannel : IImageContract, IClientChannel { }
	```

	チャネルは、サービスとクライアントが相互に情報を渡すときに経由する WCF オブジェクトです。後述の手順でホスト アプリケーションでチャネルを作成します。Service Bus は、ブラウザーからの HTTP GET 要求を **GetImage** 実装に渡すために、このチャネルを使用します。また、**GetImage** の戻り値を取得して、クライアント ブラウザーの HTTP GETRESPONSE に変換するためにも、このチャネルを使用します。

12. **[ビルド]** メニューの **[ソリューションのビルド]** をクリックして、ここまでの作業に問題がないことを確認します。

### 例

次のコードは、Service Bus コントラクトを定義する基本的なインターフェイスを示しています。

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.ServiceModel;
using System.ServiceModel.Channels;
using System.ServiceModel.Web;
using System.IO;

namespace Microsoft.ServiceBus.Samples
{

    [ServiceContract(Name = "IImageContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }

    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

## 手順 3: Service Bus を使用する REST ベースの WCF サービス コントラクトを実装する

REST スタイルの Service Bus Service を作成するには、まずコントラクトを作成する必要があります。コントラクトは、インターフェイスを使用して定義します。次の手順はインターフェイスの実装です。ユーザー定義の **IImageContract** インターフェイスを実装する **ImageService** というクラスを作成します。コントラクトを実装したら、App.config ファイルを使用してインターフェイスを構成します。構成ファイルには、サービス名、コントラクト名、Service Bus との通信に使用するプロトコルの種類など、アプリケーションに必要な情報が含まれています。以下の手順では、これらのタスクに使用するコード例を示します。

これまでの手順と同様に、REST スタイルのコントラクトと基本の Service Bus コントラクトの実装にはほとんど違いがありません。

### REST スタイルの Service Bus コントラクトを実装するには

1. **IImageContract** インターフェイスの定義の直後に、**ImageService** という新しいクラスを作成します。**ImageService** クラスで **IImageContract** インターフェイスを実装します。

	```
	class ImageService : IImageContract
	{
	}
	```
	他のインターフェイス実装と同様に、別のファイルに指定した定義を実装することができます。ただし、このチュートリアルでは、インターフェイス定義や `Main()` メソッドと同じファイルで実装します。

2. [ServiceBehaviorAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicebehaviorattribute.aspx) 属性を **IImageService** クラスに適用して、クラスが WCF コントラクトの実装であることを示します。

	```
	[ServiceBehavior(Name = "ImageService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
	class ImageService : IImageContract
	{
	}
	```

	既に説明したように、この名前空間は従来の名前空間ではありません。この名前空間は、コントラクトを特定する WCF アーキテクチャの一部です。詳細については、WCF ドキュメントの「[データ コントラクト名](https://msdn.microsoft.com/library/ms731045.aspx)」をご覧ください。

3. .jpg 画像をプロジェクトに追加します。

	これは、サービスが受信側ブラウザーに表示する画像です。プロジェクトを右クリックし、**[追加]** をクリックします。**[既存の項目]** をクリックします。**[既存項目の追加]** ダイアログ ボックスで、使用する .jpg を参照して選択し、**[追加]** をクリックします。

	ファイルを追加するときに、**[ファイル名:]** フィールドの横にあるドロップダウン リストで **[すべてのファイル]** が選択されていることを確認します。以降、このチュートリアルでは、画像名が "image.jpg" という前提で説明します。別のファイルを使用する場合は、画像のファイル名を変更するか、ファイル名に合わせてコードを変更します。

4. 実行中のサービスから画像ファイルを検出できるようにするには、**ソリューション エクスプローラー**で画像ファイルを右クリックし、**[プロパティ]** をクリックします。**[プロパティ]** ウィンドウの **[出力ディレクトリにコピー]** を **[新しい場合はコピーする]** に設定します。

5. **System.Drawing.dll** アセンブリへの参照をプロジェクトに追加し、次の関連する `using` ステートメントも追加します。

	```
	using System.Drawing;
	using System.Drawing.Imaging;
	using Microsoft.ServiceBus;
	using Microsoft.ServiceBus.Web;
	```

6. ビットマップを読み込む次のコンストラクターを **ImageService** クラスに追加し、クライアント ブラウザーに送信する準備をします。

	```
	class ImageService : IImageContract
	{
		const string imageFileName = "image.jpg";

		Image bitmap;

		public ImageService()
		{
			this.bitmap = Image.FromFile(imageFileName);
		}
	}
	```

7. 前のコードの直後に、次のように、画像を含む HTTP メッセージを返す **GetImage** メソッドを **ImageService** クラスに追加します。

	```
	public Stream GetImage()
	{
		MemoryStream stream = new MemoryStream();
		this.bitmap.Save(stream, ImageFormat.Jpeg);

		stream.Position = 0;
		WebOperationContext.Current.OutgoingResponse.ContentType = "image/jpeg";

		return stream;
	}
	```

	この実装では、**MemoryStream** を使用して画像を取得し、ブラウザーにストリーミングする準備をします。ゼロ位置からストリーミングを開始し、ストリーム コンテンツを jpeg 形式と宣言し、情報をストリーミングします。

8. **[ビルド]** メニューの **[ソリューションのビルド]** をクリックします。

### Service Bus で Web サービスを実行するための構成を定義するには

1. **ソリューション エクスプローラー**で、**App.config** をダブルクリックして、Visual Studio エディターでそのファイルを開きます。

	**App.config** ファイルは WCF 構成ファイルと似ており、サービス名、エンドポイント (つまり、Service Bus がクライアントおよびホストと相互に通信するために公開している場所)、バインディング (通信に使用されているプロトコルの種類) が含まれています。主な違いは、構成されているサービス エンドポイントが、.NET Framework の一部ではない [WebHttpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.webhttprelaybinding.aspx) バインディングを参照している点です。

2. `<system.serviceModel>` XML 要素は 1 つ以上のサービスを定義する WCF 要素です。この要素は、サービス名とエンドポイントの定義に使用されます。`<system.serviceModel>` 要素の下 (ただし、`<system.serviceModel>` 内) に、次の内容の `<bindings>` 要素を追加します。ここでアプリケーションに使用するバインドを定義します。複数のバインドを定義できますが、このチュートリアルで定義するバインドは 1 つのみです。

	```
	<bindings>
		<!-- Application Binding -->
		<webHttpRelayBinding>
			<binding name="default">
				<security relayClientAuthenticationType="None" />
			</binding>
		</webHttpRelayBinding>
	</bindings>
	```

	この手順では、**relayClientAuthenticationType** を **None** に設定して Service Bus [WebHttpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.webhttprelaybinding.aspx) バインドを定義します。この設定は、このバインドを使用するエンドポイントは、クライアントの資格情報を必要としないことを示します。

3. `<bindings>` 要素の後に、`<services>` 要素を追加します。バインドと同様に、1 つの構成ファイルで複数のサービスを定義できます。ただし、このチュートリアルで定義するサービスは 1 つのみです。

	```
	<services>
		<!-- Application Service -->
		<service name="Microsoft.ServiceBus.Samples.ImageService"
             behaviorConfiguration="default">
			<endpoint name="RelayEndpoint"
					contract="Microsoft.ServiceBus.Samples.IImageContract"
					binding="webHttpRelayBinding"
					bindingConfiguration="default"
					behaviorConfiguration="sbTokenProvider"
					address="" />
		</service>
	</services>
	```

	この手順では、以前に定義した既定の **webHttpRelayBinding** を使用するサービスを構成します。また、既定の **sbTokenProvider** を使用します。この値は次の手順で定義します。

4. `<services>` 要素の後に、次の内容の `<behaviors>` 要素を作成します。このとき、"SAS\_KEY" キーを、手順 1 で [Azure Portal][] から取得した *Shared Access Signature* (SAS) に置き換えます。

	```
	<behaviors>
		<endpointBehaviors>
			<behavior name="sbTokenProvider">
				<transportClientEndpointBehavior>
					<tokenProvider>
						<sharedAccessSignature keyName="RootManageSharedAccessKey" key="SAS_KEY" />
					</tokenProvider>
				</transportClientEndpointBehavior>
			</behavior>
			</endpointBehaviors>
			<serviceBehaviors>
				<behavior name="default">
					<serviceDebug httpHelpPageEnabled="false" httpsHelpPageEnabled="false" />
				</behavior>
			</serviceBehaviors>
	</behaviors>
	```

5. 引き続き App.config 内で、`<appSettings>` 要素内の接続文字列の値全体を、前の手順でポータルから取得した接続文字列に置き換えます。

	```
	<appSettings>
   	<!-- Service Bus specific app settings for messaging connections -->
   	<add key="Microsoft.ServiceBus.ConnectionString"
	       value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey"/>
	</appSettings>
	```

6. **[ビルド]** メニューの **[ソリューションのビルド]** をクリックしてソリューション全体をビルドします。

### 例

次のコードは、**WebHttpRelayBinding** バインドを使用して Service Bus で実行する REST ベース サービスのコントラクトとサービスの実装を示しています。

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.ServiceModel;
using System.ServiceModel.Channels;
using System.ServiceModel.Web;
using System.IO;
using System.Drawing;
using System.Drawing.Imaging;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Web;

namespace Microsoft.ServiceBus.Samples
{


    [ServiceContract(Name = "ImageContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }

    [ServiceBehavior(Name = "ImageService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    class ImageService : IImageContract
    {
        const string imageFileName = "image.jpg";

        Image bitmap;

        public ImageService()
        {
            this.bitmap = Image.FromFile(imageFileName);
        }

        public Stream GetImage()
        {
            MemoryStream stream = new MemoryStream();
            this.bitmap.Save(stream, ImageFormat.Jpeg);

            stream.Position = 0;
            WebOperationContext.Current.OutgoingResponse.ContentType = "image/jpeg";

            return stream;
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

次の例は、サービスに関連付けられている App.config ファイルです。

```
<?xml version="1.0" encoding="utf-8"?>
<configuration>
    <startup> 
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5.2"/>
    </startup>
    <system.serviceModel>
        <extensions>
            <!-- In this extension section we are introducing all known service bus extensions. User can remove the ones they don't need. -->
            <behaviorExtensions>
                <add name="connectionStatusBehavior"
                    type="Microsoft.ServiceBus.Configuration.ConnectionStatusElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="transportClientEndpointBehavior"
                    type="Microsoft.ServiceBus.Configuration.TransportClientEndpointBehaviorElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="serviceRegistrySettings"
                    type="Microsoft.ServiceBus.Configuration.ServiceRegistrySettingsElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
            </behaviorExtensions>
            <bindingElementExtensions>
                <add name="netMessagingTransport"
                    type="Microsoft.ServiceBus.Messaging.Configuration.NetMessagingTransportExtensionElement, Microsoft.ServiceBus,  Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="tcpRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.TcpRelayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="httpRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.HttpRelayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="httpsRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.HttpsRelayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="onewayRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.RelayedOnewayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
            </bindingElementExtensions>
            <bindingExtensions>
                <add name="basicHttpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.BasicHttpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="webHttpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.WebHttpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="ws2007HttpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.WS2007HttpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netTcpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netOnewayRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetOnewayRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netEventRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetEventRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netMessagingBinding"
                    type="Microsoft.ServiceBus.Messaging.Configuration.NetMessagingBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
            </bindingExtensions>
        </extensions>
      <bindings>
        <!-- Application Binding -->
        <webHttpRelayBinding>
          <binding name="default">
            <security relayClientAuthenticationType="None" />
          </binding>
        </webHttpRelayBinding>
      </bindings>
      <services>
        <!-- Application Service -->
        <service name="Microsoft.ServiceBus.Samples.ImageService"
             behaviorConfiguration="default">
          <endpoint name="RelayEndpoint"
                  contract="Microsoft.ServiceBus.Samples.IImageContract"
                  binding="webHttpRelayBinding"
                  bindingConfiguration="default"
                  behaviorConfiguration="sbTokenProvider"
                  address="" />
        </service>
      </services>
      <behaviors>
        <endpointBehaviors>
          <behavior name="sbTokenProvider">
            <transportClientEndpointBehavior>
              <tokenProvider>
                <sharedAccessSignature keyName="RootManageSharedAccessKey" key="[SAS_KEY]" />
              </tokenProvider>
            </transportClientEndpointBehavior>
          </behavior>
        </endpointBehaviors>
        <serviceBehaviors>
          <behavior name="default">
            <serviceDebug httpHelpPageEnabled="false" httpsHelpPageEnabled="false" />
          </behavior>
        </serviceBehaviors>
      </behaviors>
    </system.serviceModel>
    <appSettings>
        <!-- Service Bus specific app setings for messaging connections -->
        <add key="Microsoft.ServiceBus.ConnectionString"
            value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey"/>
    </appSettings>
</configuration>
```

## 手順 4: Service Bus を使用する REST ベースの WCF サービスをホストする

この手順では、Service Bus でコンソール アプリケーションを使用して、Web サービスを実行する方法について説明します。この手順で作成するコードの詳細については、手順に従って例を使用して説明します。

### サービスのベース アドレスを作成するには

1. `Main()`関数の宣言で、Service Bus プロジェクトの名前空間を格納する変数を作成します。`yourNamespace` は前に作成したサービス名前空間の名前に置き換えてください。

	```
	string serviceNamespace = "yourNamespace";
	```
	Service Bus は、名前空間の名前を使用して一意の URI を作成します。

2. 名前空間に基づくサービスのベース アドレスの `Uri` インスタンスを作成します。

	```
	Uri address = ServiceBusEnvironment.CreateServiceUri("https", serviceNamespace, "Image");
	```

### Web サービス ホストを作成および構成するには

- このセクションで作成した URI アドレスを使用して、Web サービス ホストを作成します。

	```
	WebServiceHost host = new WebServiceHost(typeof(ImageService), address);
	```
	サービス ホストは、ホスト アプリケーションをインスタンス化する WCF オブジェクトです。この例では、作成するホストの種類 (**ImageService**) と、ホスト アプリケーションを公開するアドレスを渡します。

### Web サービス ホストを実行するには

1. サービスを開きます。

	```
	host.Open();
	```
	サービスが実行されます。

2. サービスが実行中であることと、サービスの停止方法を示すメッセージが表示されます。

	```
	Console.WriteLine("Copy the following address into a browser to see the image: ");
	Console.WriteLine(address + "GetImage");
	Console.WriteLine();
	Console.WriteLine("Press [Enter] to exit");
	Console.ReadLine();
	```

3. 完了したら、サービス ホストを閉じます。

	```
	host.Close();
	```

## 例

次の例では、チュートリアルの前の手順で説明したサービス コントラクトと実装が含まれています。ここでは、コンソール アプリケーションでサービスをホストします。次のコードをコンパイルして、ImageListener.exe という実行可能ファイルを作成します。

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.ServiceModel;
using System.ServiceModel.Channels;
using System.ServiceModel.Web;
using System.IO;
using System.Drawing;
using System.Drawing.Imaging;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Web;

namespace Microsoft.ServiceBus.Samples
{

    [ServiceContract(Name = "ImageContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }

    [ServiceBehavior(Name = "ImageService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    class ImageService : IImageContract
    {
        const string imageFileName = "image.jpg";

        Image bitmap;

        public ImageService()
        {
            this.bitmap = Image.FromFile(imageFileName);
        }

        public Stream GetImage()
        {
            MemoryStream stream = new MemoryStream();
            this.bitmap.Save(stream, ImageFormat.Jpeg);

            stream.Position = 0;
            WebOperationContext.Current.OutgoingResponse.ContentType = "image/jpeg";

            return stream;
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
            string serviceNamespace = "InsertServiceNamespaceHere";
            Uri address = ServiceBusEnvironment.CreateServiceUri("https", serviceNamespace, "Image");

            WebServiceHost host = new WebServiceHost(typeof(ImageService), address);
            host.Open();

            Console.WriteLine("Copy the following address into a browser to see the image: ");
            Console.WriteLine(address + "GetImage");
            Console.WriteLine();
            Console.WriteLine("Press [Enter] to exit");
            Console.ReadLine();

            host.Close();
        }
    }
}
```

### コードのコンパイル

ソリューションをビルドしたら、次の手順でアプリケーションを実行します。

1. **F5** キーを押すか、実行可能ファイルの場所 (ImageListener\\bin\\Debug\\ImageListener.exe) を参照し、サービスを実行します。次の手順を実行するために必要なため、アプリケーションを実行したままにします。

2. コマンド プロンプトのアドレスをコピーし、ブラウザーに貼り付けて画像を確認します。

3. 完了したら、コマンド プロンプト ウィンドウで **Enter** キーを押して、アプリを閉じます。

## 次のステップ

ここでは、Service Bus Relay サービスを使用するアプリケーションを構築しました。リレー型メッセージングの詳細については、次の記事を参照してください。

- [Azure Service Bus アーキテクチャの概要](../service-bus/service-bus-fundamentals-hybrid-solutions.md#relays)

- [Service Bus Relay サービスの使用方法](service-bus-dotnet-how-to-use-relay.md)

[Azure portal]: https://portal.azure.com

<!---HONumber=AcomDC_0928_2016-->