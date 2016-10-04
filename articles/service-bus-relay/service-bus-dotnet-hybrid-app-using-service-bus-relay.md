<properties
	pageTitle="ハイブリッド オンプレミス/クラウド アプリケーション (.NET) | Microsoft Azure"
	description="Azure Service Bus Relay を使用して .NET オンプレミス/クラウド ハイブリッド アプリケーションを作成する方法について説明します。"
	services="service-bus-relay"
	documentationCenter=".net"
	authors="sethmanheim"
	manager="timlt"
	editor=""/>

<tags
	ms.service="service-bus-relay"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="09/16/2016"
	ms.author="sethm"/>

# Azure Service Bus Relay を使用した .NET オンプレミス/クラウド ハイブリッド アプリケーション

## はじめに

この記事では、Microsoft Azure と Visual Studio を使ってハイブリッド クラウド アプリケーションを作成する方法について説明します。このチュートリアルは、Azure を使用した経験がない読者を対象に作成されています。複数の Azure リソースをクラウドで運用するアプリケーションを 30 分未満で作成できます。

学習内容:

-   Web ソリューションによって使用される Web サービスを作成、または既存の Web サービスを適合させる方法。
-   Azure アプリケーションと別の場所でホストされている Web サービスの間で、Azure Service Bus Relay サービスを使用してデータを共有する方法。

[AZURE.INCLUDE [アカウント作成メモ](../../includes/create-account-note.md)]

## Service Bus Relay がハイブリッド ソリューションで役立つ理由

ビジネス ソリューションは、通常、新しい独自のビジネス要件に対処するために記述されるカスタム コードと、既に配置されているソリューションとシステムによって提供される既存の機能の組み合わせによって構成されます。

ソリューション アーキテクトたちは、スケーラビリティが高く運用コストが低いクラウドを使い始めています。その中で、ソリューションの構成要素として利用する既存のサービス資産は企業ファイアウォールの内部にあり、クラウド ソリューションからのアクセスが難しいということがわかってきました。多くの内部サービスは、企業ネットワークと外部との境界で簡単に公開できるような方法では構築されたり、ホストされたりしていません。

Service Bus Relay は、既存の Windows Communication Foundation (WCF) Web サービスを使用し、企業ネットワークのインフラストラクチャを大幅に変更することなく、企業の境界の外部にあるソリューションからそれらのサービスに安全にアクセスできる使用事例として設計されています。このような Service Bus Relay サービスは既存の環境内でもホストされていますが、受信セッションや要求のリッスンは、クラウドでホストされている Service Bus にデリゲートしています。さらに Service Bus は、[Shared Access Signature](../service-bus/service-bus-sas-overview.md) (SAS) 認証を使用して、これらのサービスを未承認のアクセスから保護します。

## ソリューション シナリオ

このチュートリアルでは、商品在庫ページに商品の一覧を表示する ASP.NET Web サイトを作成します。

![][0]

このチュートリアルでは、既存のオンプレミスのシステムに商品情報が格納されているものとし、Service Bus Relay を使用してそのシステムにアクセスします。これを、単純なコンソール アプリケーションで実行された、メモリ内の商品のセットが基盤となっている Web サービスでシミュレートします。このコンソール アプリケーションをユーザー自身のコンピューターで実行し、Web ロールを Azure にデプロイできます。そうすることで、Azure データセンターで実行されている Web ロールが実際にどのようにコンピューターを呼び出すかを確認できます。ただし、ユーザーのコンピューターは、ほぼ確実に少なくとも 1 つのファイアウォールとネットワーク アドレス変換 (NAT) レイヤーの背後に配置されます。

次に、完成した Web アプリケーションの開始ページのスクリーンショットを示します。

![][1]

## 開発環境を設定する

Azure アプリケーションの開発を開始する前に、ツールを入手して、開発環境を設定します。

1.  [ツールと SDK の入手][]に関するページから、Azure SDK for .NET をインストールします。

2. 	使用している Visual Studio のバージョンに対応する **[SDK のインストール]** をクリックします。このチュートリアルの手順では、Visual Studio 2015 を使用します。

4.  インストーラーの実行や保存を求めるメッセージが表示されたら、**[実行]** をクリックします。

5.  **Web Platform Installer** の **[インストール]** をクリックし、インストールの手順を進めます。

6.  インストールが完了すると、アプリケーションの開発に必要なツールがすべて揃います。SDK には、Visual Studio で Azure アプリケーションを簡単に開発するためのツールが用意されています。Visual Studio がインストールされていない場合、無料の Visual Studio Express もインストールされます。

## 名前空間の作成

Azure で Service Bus 機能を使用するには、最初にサービス名前空間を作成する必要があります。名前空間は、アプリケーション内で Service Bus リソースをアドレス指定するためのスコープ コンテナーを提供します。

[AZURE.INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## オンプレミスのサーバーを作成する

まず、仮のオンプレミスの商品カタログ システムを構築します。かなり単純なものですが、これがこれから統合しようとしている完全なサービス機能を備えた実際のオンプレミスの商品カタログ システムであると考えてください。

このプロジェクトは Visual Studio コンソール アプリケーションです。[Azure Service Bus NuGet パッケージ](https://www.nuget.org/packages/WindowsAzure.ServiceBus/)を使用して、Service Bus ライブラリと構成設定を組み込みます。

### プロジェクトを作成する

1.  管理者特権を使用して、Microsoft Visual Studio を起動します。管理者特権で Visual Studio を起動するには、**Visual Studio** のプログラム アイコンを右クリックし、**[管理者として実行]** をクリックします。

2.  Visual Studio で、**[ファイル]** メニューの **[新規作成]** をクリックした後、**[プロジェクト]** をクリックします。

3.  **[インストールされたテンプレート]** で **[Visual C#]** をクリックし、**[コンソール アプリケーション]** をクリックします。**[名前]** ボックスに、名前として「**ProductsServer**」と入力します。

    ![][11]

4.  **[OK]** をクリックして **ProductsServer** プロジェクトを作成します。

7.  既に Visual Studio 用の NuGet パッケージ マネージャーをインストールしている場合は、次のステップに進みます。まだインストールしていない場合は、[NuGet][] にアクセスし、[[Install NuGet]](http://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c) をクリックします。メッセージに従って NuGet パッケージ マネージャーをインストールし、Visual Studio を再起動します。

7.  ソリューション エクスプローラーで **ProductsServer** プロジェクトを右クリックし、**[NuGet パッケージの管理]** をクリックします。

8.  **[参照]** タブをクリックして、`Microsoft Azure Service Bus` を検索します。**[インストール]** をクリックし、使用条件に同意します。

    ![][13]

    これで、必要なクライアント アセンブリを参照できるようになりました。

9.  商品のコントラクト用に新しいクラスを追加します。ソリューション エクスプローラーで、**[ProductsServer]** プロジェクトを右クリックし、**[追加]** をクリックしてから **[クラス]** をクリックします。

10. **[名前]** ボックスに「**ProductsContract.cs**」と入力します。**[追加]** をクリックします。

11. **ProductsContract.cs** で、名前空間の定義を次のコードに置き換えます。このコードはサービスのコントラクトを定義します。

	```
	namespace ProductsServer
	{
	    using System.Collections.Generic;
	    using System.Runtime.Serialization;
	    using System.ServiceModel;
	
	    // Define the data contract for the service
	    [DataContract]
	    // Declare the serializable properties.
	    public class ProductData
	    {
	        [DataMember]
	        public string Id { get; set; }
	        [DataMember]
	        public string Name { get; set; }
	        [DataMember]
	        public string Quantity { get; set; }
	    }
	
	    // Define the service contract.
	    [ServiceContract]
	    interface IProducts
	    {
	        [OperationContract]
	        IList<ProductData> GetProducts();
	
	    }
	
	    interface IProductsChannel : IProducts, IClientChannel
	    {
	    }
	}
	```

12. Program.cs で、名前空間の定義を次のコードに書き換えます。このコードは、プロファイル サービスとそのホストを追加します。

	```
	namespace ProductsServer
	{
	    using System;
	    using System.Linq;
	    using System.Collections.Generic;
	    using System.ServiceModel;
	
	    // Implement the IProducts interface.
	    class ProductsService : IProducts
	    {
	
	        // Populate array of products for display on website
	        ProductData[] products =
	            new []
	                {
	                    new ProductData{ Id = "1", Name = "Rock",
	                                     Quantity = "1"},
	                    new ProductData{ Id = "2", Name = "Paper",
	                                     Quantity = "3"},
	                    new ProductData{ Id = "3", Name = "Scissors",
	                                     Quantity = "5"},
	                    new ProductData{ Id = "4", Name = "Well",
	                                     Quantity = "2500"},
	                };
	
	        // Display a message in the service console application
	        // when the list of products is retrieved.
	        public IList<ProductData> GetProducts()
	        {
	            Console.WriteLine("GetProducts called.");
	            return products;
	        }
	
	    }
	
	    class Program
	    {
	        // Define the Main() function in the service application.
	        static void Main(string[] args)
	        {
	            var sh = new ServiceHost(typeof(ProductsService));
	            sh.Open();
	
	            Console.WriteLine("Press ENTER to close");
	            Console.ReadLine();
	
	            sh.Close();
	        }
	    }
	}
	```

13. ソリューション エクスプローラーで、**App.config** ファイルをダブルクリックして、Visual Studio エディターでそのファイルを開きます。**&lt;system.ServiceModel&gt;** 要素の下 (まだ &lt;system.ServiceModel&gt; 内であることに注意) に、次の XML コードを追加します。*yourServiceNamespace* は実際の名前空間の名前に置き換え、*yourKey* は前の手順でポータルから取得した SAS キーに置き換えてください。

    ```
    <system.serviceModel>
	...
      <services>
         <service name="ProductsServer.ProductsService">
           <endpoint address="sb://yourServiceNamespace.servicebus.windows.net/products" binding="netTcpRelayBinding" contract="ProductsServer.IProducts" behaviorConfiguration="products"/>
         </service>
      </services>
      <behaviors>
         <endpointBehaviors>
           <behavior name="products">
             <transportClientEndpointBehavior>
                <tokenProvider>
                   <sharedAccessSignature keyName="RootManageSharedAccessKey" key="yourKey" />
                </tokenProvider>
             </transportClientEndpointBehavior>
           </behavior>
         </endpointBehaviors>
      </behaviors>
    </system.serviceModel>
    ```
14. 引き続き App.config 内で、**&lt;appSettings&gt;** 要素内の接続文字列の値を、前の手順でポータルから取得した接続文字列に置き換えます。

	```
	<appSettings>
   	<!-- Service Bus specific app settings for messaging connections -->
   	<add key="Microsoft.ServiceBus.ConnectionString"
	       value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey"/>
	</appSettings>
	```

14. **Ctrl + Shift + B** キーを押すか、**[ビルド]** メニューの **[ソリューションのビルド]** をクリックしてアプリケーションをビルドし、ここまでの作業に問題がないことを確認します。

## ASP.NET アプリケーションを作成する

このセクションでは、商品サービスから取得したデータを表示する単純な ASP.NET アプリケーションを構築します。

### プロジェクトを作成する

1.  Microsoft Visual Studio 2013 が管理者特権で実行されていることを確認します。

2.  Visual Studio で、**[ファイル]** メニューの **[新規作成]** をクリックした後、**[プロジェクト]** をクリックします。

3.  **[インストールされたテンプレート]** で **[Visual C#]** をクリックし、**[ASP.NET Web アプリケーション]** をクリックします。プロジェクト名として、「**ProductsPortal**」と入力します。次に、 **[OK]** をクリックします

    ![][15]

4.  **[テンプレートの選択]** の一覧で **[MVC]** をクリックします。

6.  **[Host in the cloud (クラウドにホストする)]** チェック ボックスをオンにします。

    ![][16]

5. **[認証の変更]** ボタンをクリックします。**[認証の変更]** ダイアログ ボックスで、**[認証なし]** をクリックし、**[OK]** をクリックします。このチュートリアルでは、ユーザー ログインの必要がないアプリケーションをデプロイします。

	![][18]

6. 	**[新しい ASP.NET プロジェクト]** ダイアログ ボックスの **[Microsoft Azure]** セクションで、**[Host in the cloud (クラウドにホストする)]** が選択されていることと、ドロップダウン リストで **[App Service]** が選択されていることを確認します。

	![][19]

7. **[OK]** をクリックします。

8. 次に、新しい Web アプリの Azure リソースを構成する必要があります。「[新しい Web アプリの Azure リソースの構成](../app-service-web/web-sites-dotnet-get-started.md#configure-azure-resources-for-a-new-web-app)」セクションのすべての手順を実行してください。完了したら、このチュートリアルに戻り、次の手順に進んでください。

5.  ソリューション エクスプローラーで **[Models]** を右クリックし、**[追加]**、**[クラス]** の順にクリックします。**[名前]** ボックスに、名前として「**Product.cs**」と入力します。**[追加]** をクリックします。

    ![][17]

### Web アプリケーションを変更する

1.  Visual Studio で、Product.cs ファイルの既存の名前空間定義を次のコードに書き換えます。

	```
	// Declare properties for the products inventory.
 	namespace ProductsWeb.Models
	{
    	public class Product
    	{
    	    public string Id { get; set; }
    	    public string Name { get; set; }
    	    public string Quantity { get; set; }
    	}
	}
	```

2.  ソリューション エクスプローラーで **Controllers** フォルダーを展開し、**HomeController.cs** ファイルをダブルクリックして Visual Studio で開きます。

3. **HomeController.cs** の既存の名前空間の定義を次のコードに書き換えます。

	```
	namespace ProductsWeb.Controllers
	{
	    using System.Collections.Generic;
	    using System.Web.Mvc;
	    using Models;
	
	    public class HomeController : Controller
	    {
	        // Return a view of the products inventory.
	        public ActionResult Index(string Identifier, string ProductName)
	        {
	            var products = new List<Product>
	                {new Product {Id = Identifier, Name = ProductName}};
	            return View(products);
	        }
	     }
	}
	```

3.  ソリューション エクスプローラーで、Views\\Shared フォルダーを展開し、**\_Layout.cshtml** をダブルクリックして Visual Studio エディターで開きます。

5.  "**My ASP.NET Application**" となっている箇所をすべて "**LITWARE's Products**" に置き換えます。

6. **Home**、**About**、および **Contact** の各リンクを削除します。次の例では、強調表示されたコードを削除します。

	![][41]

7.  ソリューション エクスプローラーで、Views\\Home フォルダーを展開し、**Index.cshtml** をダブルクリックして Visual Studio エディターで開きます。ファイルの内容全体を次のコードに置き換えます。

	```
	@model IEnumerable<ProductsWeb.Models.Product>
	
	@{
	 		ViewBag.Title = "Index";
	}
	
	<h2>Prod Inventory</h2>
	
	<table>
	  		<tr>
	      		<th>
	          		@Html.DisplayNameFor(model => model.Name)
	      		</th>
	              <th></th>
	      		<th>
	          		@Html.DisplayNameFor(model => model.Quantity)
	      		</th>
	  		</tr>
	
	@foreach (var item in Model) {
	  		<tr>
	      		<td>
	          		@Html.DisplayFor(modelItem => item.Name)
	      		</td>
	      		<td>
	          		@Html.DisplayFor(modelItem => item.Quantity)
	      		</td>
	  		</tr>
	}
	
	</table>
	```

9.  ここまでの作業が正確にできていることを確認するために、**Ctrl + Shift + B** キーを押してプロジェクトをビルドします。


### アプリをローカルで実行する

アプリケーションを実行して、動作を確認します。

1.  **ProductsPortal** がアクティブなプロジェクトであることを確認します。ソリューション エクスプローラーで、プロジェクト名を右クリックして **[スタートアップ プロジェクトに設定]** を選択します。
2.  Visual Studio で F5 キーを押します。
3.  アプリケーションがブラウザーに表示され、実行されます。

    ![][21]

## 統合する

次の手順では、オンプレミスの商品サーバーと ASP.NET アプリケーションを連結します。

1.  「[ASP.NET アプリケーションを作成する](#create-an-aspnet-application)」で作成した **ProductsPortal** プロジェクトが閉じている場合は、Visual Studio でもう一度開きます。

2.  「オンプレミスのサーバーを作成する」に記載されている手順に従い、NuGet パッケージを Reference プロジェクトに追加します。ソリューション エクスプローラーで **ProductsPortal** プロジェクトを右クリックし、**[NuGet パッケージの管理]** をクリックします。

3.  "Service Bus" を検索して、**[Microsoft Azure Service Bus]** 項目を選択します。次に、インストールを完了し、このダイアログ ボックスを閉じます。

4.  ソリューション エクスプローラーで **ProductsPortal** プロジェクトを右クリックし、**[追加]** をクリックしてから **[既存の項目]** をクリックします。

5.  **ProductsServer** コンソール プロジェクトの **ProductsContract.cs** ファイルに移動します。ProductsContract.cs をクリックして強調表示します。**[追加]** の横の下向き矢印をクリックしてから、**[リンクとして追加]** をクリックします。

	![][24]

6.  次に、Visual Studio エディターで **HomeController.cs** ファイルを開き、名前空間定義を次のコードに置き換えます。*yourServiceNamespace* は実際のサービス名前空間の名前、*yourKey* は SAS キーに置き換えてください。これで、クライアントからオンプレミスのサービスを呼び出し、その結果を返すことができます。

	```
	namespace ProductsWeb.Controllers
	{
	    using System.Linq;
	    using System.ServiceModel;
	    using System.Web.Mvc;
	    using Microsoft.ServiceBus;
	    using Models;
	    using ProductsServer;
	
	    public class HomeController : Controller
	    {
	        // Declare the channel factory.
	        static ChannelFactory<IProductsChannel> channelFactory;
	
	        static HomeController()
	        {
	            // Create shared access signature token credentials for authentication.
	            channelFactory = new ChannelFactory<IProductsChannel>(new NetTcpRelayBinding(),
	                "sb://yourServiceNamespace.servicebus.windows.net/products");
	            channelFactory.Endpoint.Behaviors.Add(new TransportClientEndpointBehavior {
	                TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider(
	                    "RootManageSharedAccessKey", "yourKey") });
	        }
	
	        public ActionResult Index()
	        {
	            using (IProductsChannel channel = channelFactory.CreateChannel())
	            {
	                // Return a view of the products inventory.
	                return this.View(from prod in channel.GetProducts()
	                                 select
	                                     new Product { Id = prod.Id, Name = prod.Name,
	                                         Quantity = prod.Quantity });
	            }
	        }
	    }
	}
	```

7.  ソリューション エクスプローラーで、**ProductsPortal** ソリューションを右クリックします (必ずプロジェクトではなくソリューションを右クリックしてください)。**[追加]**、**[既存のプロジェクト]** の順にクリックします。

8.  **ProductsServer** プロジェクトに移動し、**ProductsServer.csproj** ソリューション ファイルをダブルクリックして追加します。

9.  **ProductsPortal** にデータを表示するために、**ProductsServer** が実行されている必要があります。ソリューション エクスプローラーで **ProductsPortal** ソリューションを右クリックして、**[プロパティ]** をクリックします。**[プロパティ ページ]** ダイアログ ボックスが表示されます。

10. 左側で、**[スタートアップ プロジェクト]** をクリックします。左側で、**[マルチ スタートアップ プロジェクト]** をクリックします。**[ProductsServer]** と **[ProductsPortal]** がこの順で表示されていることと、その両方にアクションとして **[開始]** が設定されていることを確認します。

      ![][25]

11. 引き続き **[プロパティ]** ダイアログ ボックスで、左側の **[プロジェクトの依存関係]** をクリックします。

12. **[プロジェクト]** リストで **[ProductsServer]** をクリックします。**[ProductsPortal]** は選択されて**いない**ことを確認してください。

14. **[プロジェクト]** ボックスの一覧で **[ProductsPortal]** をクリックします。**[ProductsServer]** が選択されていることを確認してください。

    ![][26]

15. **[プロパティ ページ]** ダイアログ ボックスで **[OK]** をクリックします。

## プロジェクトをローカルで実行する

アプリケーションをローカルでテストするには、Visual Studio で **F5** キーを押します。まず、オンプレミスのサーバー (**ProductsServer**) を起動し、次に **ProductsPortal** アプリケーションをブラウザー ウィンドウで起動する必要があります。この時点で、オンプレミスのシステムの商品サービスから取得された商品在庫一覧データが表示されます。

![][10]

**ProductsPortal** ページで **[最新の情報に更新]** をクリックします。ページを更新するたびに、**ProductsServer** から `GetProducts()` が呼び出され、サーバー アプリにメッセージが表示されます。

次の手順に進む前に、両方のアプリケーションを終了します。

## Azure Web アプリに ProductsPortal プロジェクトをデプロイする

次に、**ProductsPortal** フロントエンドを Azure Web アプリに変換します。最初に、「[Azure Web アプリに Web プロジェクトをデプロイする](../app-service-web/web-sites-dotnet-get-started.md#deploy-the-web-project-to-the-azure-web-app)」セクションのすべての手順を実行して **ProductsPortal** プロジェクトをデプロイします。デプロイが完了したら、このチュートリアルに戻り、次の手順に進んでください。

> [AZURE.NOTE] デプロイの後で **ProductsPortal** Web プロジェクトが自動的に起動するとき、ブラウザー ウィンドウにエラー メッセージが表示されることがあります。これは想定されたエラーです。**ProductsServer** アプリケーションがまだ実行されていないため、このようなエラーが発生します。

デプロイされた Web アプリの URL をコピーしてください。次の手順でこの URL が必要になります。この URL は、Visual Studio の [Azure App Service のアクティビティ] ウィンドウからも入手できます。

![][9]

### ProductsPortal を Web アプリとして設定する

アプリケーションをクラウドで実行する前に、**ProductsPortal** が Visual Studio で Web アプリとして起動していることを確認する必要があります。

1. Visual Studio で **ProjectsPortal** プロジェクトを右クリックし、**[プロパティ]** をクリックします。

3. 左側の列で、**[Web]** をクリックします。

5. **[開始動作]** セクションで **[URL の開始]** ボタンをクリックし、前の手順でデプロイした Web アプリの URL をテキスト ボックスに入力します (例: `http://productsportal1234567890.azurewebsites.net/`)。

	![][27]

6. Visual Studio の **[ファイル]** メニューで **[すべて保存]** をクリックします。

7. Visual Studio の [ビルド] メニューで **[ソリューションのリビルド]** をクリックします。

## アプリケーションの実行

2.  F5 キーを押して、アプリケーションをビルドして実行します。次のスクリーンショットのように、まず、オンプレミスのサーバー (**ProductsServer** コンソール アプリケーション) を起動し、次に **ProductsPortal** アプリケーションをブラウザー ウィンドウで起動する必要があります。オンプレミスのシステムの商品サービスから取得された商品在庫一覧データが Web アプリ上に表示されることに、再度注目してください。URL を確認し、**ProductsPortal** が Azure Web アプリとしてクラウドで実行されていることを確かめます。

    ![][1]

	> [AZURE.IMPORTANT] **ProductsServer** コンソール アプリケーションが実行されており、**ProductsPortal** アプリケーションにデータを提供できる状態になっている必要があります。ブラウザーにエラーが表示された場合は、**ProductsServer** が次のメッセージを読み込んで表示するまで数秒待ってください。その後で、ブラウザーで **[最新の情報に更新]** をクリックします。

	![][37]

3. ブラウザーに戻り、**ProductsPortal** ページで **[最新の情報に更新]** をクリックします。ページを更新するたびに、**ProductsServer** から `GetProducts()` が呼び出され、サーバー アプリにメッセージが表示されます。

	![][38]

## 次のステップ  

Service Bus の詳細については、次のリソースを参照してください。

* [Azure の Service Bus][sbwacom]
* [Service Bus キューの使用方法][sbwacomqhowto]


  [0]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hybrid.png
  [1]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App2.png
  [ツールと SDK の入手]: http://go.microsoft.com/fwlink/?LinkId=271920
  [NuGet]: http://nuget.org
  
  [11]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-con-1.png
  [13]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-13.png
  [15]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-2.png
  [16]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-4.png
  [17]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-7.png
  [18]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-5.png
  [19]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-6.png
  [9]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-9.png
  [10]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App3.png

  [21]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App1.png
  [24]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-12.png
  [25]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-13.png
  [26]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-14.png
  [27]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-8.png
  
  [36]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App2.png
  [37]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-service1.png
  [38]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-service2.png
  [41]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-40.png
  [43]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-43.png


  [sbwacom]: /documentation/services/service-bus/
  [sbwacomqhowto]: ../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md

<!---HONumber=AcomDC_0928_2016-->