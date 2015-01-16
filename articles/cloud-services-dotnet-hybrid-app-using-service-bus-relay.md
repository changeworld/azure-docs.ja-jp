<properties urlDisplayName="Hybrid Application" pageTitle="ハイブリッド内部設置型/クラウド アプリケーション (.NET) - Azure" metaKeywords="Azure Service Bus tutorial,hybrid .NET" description="Azure Service Bus Relay を使用して .NET 内部設置型/クラウド ハイブリッド アプリケーションを作成する方法について説明します。" metaCanonical="" services="service-bus" documentationCenter=".NET" title=".NET On-Premises/Cloud Hybrid Application Using Service Bus Relay" authors="sethm" solutions="" manager="timlt" editor="mattshel" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="09/15/2014" ms.author="sethm" />






# Service Bus Relay を使用する .NET 内部設置型/クラウド ハイブリッド アプリケーション

<h2>はじめに</h2>

次を使用して Azure でハイブリッド クラウドのアプリケーションを簡単に開発できます。
Visual Studio 2013 および無料の Azure SDK for .NET。このガイドは、
Azure を初めて使用するユーザーを対象としています。30 分以内に
複数の Windows Azure リソースを使用するアプリケーションを
クラウドで実行できるようになります。

学習内容:

-   Web ソリューションで使用する Web サービスを作成するか、または
    既存のものを適合させる方法
-   Azure Service Bus リレーを使用して、Azure アプリケーションと、
    別の場所でホストされる Web サービスの間でデータを共有する方法

[WACOM.INCLUDE [アカウント作成メモ](../includes/create-account-note.md)]

### Service Bus Relay がハイブリッド ソリューションで役立つ理由

ビジネス ソリューションは、カスタム コードの組み合わせで構成されます。
これらのコードは、独自の新たなビジネス要件や、
デプロイ済みのソリューションおよびシステムで提供されている
既存の機能に対応するように記述されています。

ソリューション設計者は、スケーリング要件への対応を容易にして
運用コストを削減するために、クラウドを使い始めています。設計者はこの過程で
ソリューションの構成要素として活用しようとしている既存のサービス資産が、
企業のファイアウォールの内側にあり、クラウド ソリューションから
簡単に到達してアクセスできないことに気付きました。内部サービスの多くは、
企業のネットワーク エッジで簡単に公開できるような形で
作成またはホストされていません。

*Service Bus Relay* は、既存の
Windows Communication Foundation (WCF) web サービスを取得するユースケース、および
それらのサービスから企業ネットワークの外部にあるソリューションへ、
企業のネットワーク インフラストラクチャを大幅に変更することなく
安全にアクセスできるようにするユースケース向けに設計されています。このような Service Bus Relay サービスは依然として
既存の内部環境でホストされていますが、
クラウドでホストされる Service Bus の着信セッションおよび要求のリッスンを委任されています。
また Service Bus では、Azure Active Directory Access Control を使用して、
これらのサービスを不正アクセスから保護します。

### ソリューション シナリオ

このチュートリアルでは、ASP.NET MVC 4 Web サイトを作成します。
このサイトでは、製品インベントリのページに製品の一覧が表示されます。

![][0]

このチュートリアルは、製品情報が既存の内部設置型システムにあり、
Service Bus Relay を使用してそのシステムに到達することを
前提としています。これは、単純なコンソール アプリケーションで実行される web サービスでシミュレートされ、
メモリ内の一連の製品で返されます。この
コンソール アプリケーションを自分のコンピューターで実行できるようになり、
Web ロールを Azure にデプロイできるようになります。この方法により、
Azure データ センターで実行される Web ロールが、
自分のコンピューターがほぼ確実に、1 つ以上のファイアウォールおよび
ネットワーク アドレス変換 (NAT) 層の内側にある場合でも、
そのコンピューターに呼び出されます。

次に、完成した Web アプリケーションの開始ページのスクリーンショットを示します。

![][1]

<h2>開発環境の設定</h2>

Azure アプリケーションを開発する前に、ツールを入手して
開発環境をセットアップする必要があります。

1.  Azure SDK for .NET をインストールするには、次のボタンをクリックします。

    [ツールと SDK の入手][]

2. 	**[SDK のインストール]** をクリックします。

3. 	使用している Visual Studio のバージョンに対応するリンクを選択します。このチュートリアルの手順では、Visual Studio 2013 を使用します。

	![][42]

4.  **WindowsAzureSDKForNet.exe** を実行するか保存するかを確認するメッセージが表示されたら、
    **[実行]** をクリックします。

    ![][2]

5.  Web プラットフォーム インストーラーの **[インストール]** をクリックし、インストールの手順を進めます。

    ![][3]

6.  インストールが完了すると、開発に必要なツールがすべて
    そろいます。SDK には、Visual Studio で Windows Azure アプリケーションを
    簡単に開発できるツールが含まれています。Visual Studio が
    インストールされていない場合は、無料の 
    Visual Studio Express をインストールすることもできます。

<h2>サービス名前空間の作成</h2>

Azure で Service Bus の機能を使用するには、まず
サービス名前空間を作成する必要があります。サービス名前空間により、アプリケーション内で
Service Bus リソースのアドレス範囲を指定するコンテナーが提供されます。 

[Azure の管理ポータル][]または Visual Studio のサーバー エクスプローラーを使用して、名前空間と Service Bus のメッセージ エンティティを管理できますが、新しい名前空間を作成できるのは、ポータル内のみです。

###ポータルを使用してサービス名前空間を作成するには:

1.  [Azure の管理ポータル][]にログオンします。

2.  管理ポータルの左のナビゲーション ウィンドウで
    **[Service Bus]** をクリックします。

3.  管理ポータルの下のウィンドウの **[作成]** をクリックします。   
    ![][5]

4.  **[新しい名前空間を追加する]** ダイアログで、名前空間の名前を入力します。
    その名前が使用できるかどうかがすぐに自動で確認されます。   
    ![][6]

5.  入力した名前が利用できることを確認できたら、名前空間を
    ホストする国または地域を選択します (
    コンピューター リソースをデプロイするのと同じ国/地域を
    使用してください)。

    重要:アプリケーションをデプロイする予定の国またはリージョンと**同じ国/リージョン**を
    選択してください。そうすることで、最大限のパフォーマンスを引き出すことができます。

6.	チェック マークをクリックします。これで、システムによってサービス名前空間が
    作成され有効になります。システムでアカウントのリソースがプロビジョニングされるまで
    数分かかる場合があります。

	![][38]

作成した名前空間は管理ポータルに表示されます。
アクティブ化するまで少し時間がかかります。ステータスが **[アクティブ]** になるのを待ってから、
次に進みます。

<h2>名前空間の既定の管理資格情報を取得する</h2>

新しい名前空間へのキューの作成などの管理操作を実行するには、
その名前空間の管理の資格情報を取得する
必要があります。

1.  メイン ウィンドウで、使用するサービス名前空間の名前をクリックします。   

	![][39]
  

2.  **[接続情報]** をクリックします。   

	![][40]


3.  **[接続情報へのアクセス]** ウィンドウで、**[既定の発行者]** と **[既定のキー]** のエントリを探します。   
    

4.  キーを書き留めておくか、クリップボードにコピーしておいてください。

###Visual Studio のサーバー エクスプローラーを使用したサービス名前空間の管理:

管理ポータルの代わりに Visual Studio を使用して名前空間を管理し、接続情報を取得するには、「[Azure Tools for Visual Studio の概要](http://http://msdn.microsoft.com/ja-jp/library/windowsazure/ff687127.aspx)」の「**Visual Studio から Azure に接続するには**」で説明されている手順に従ってください。Azure にサインインすると、サーバー エクスプローラーの **[Microsoft Azure]** ツリーの下にある **[サービス バス]** ノードが、既に作成したすべての名前空間を使用して自動的に設定されます。いずれかの名前空間を右クリックし、**[プロパティ]** をクリックします。Visual Studio の **[プロパティ]** ウィンドウで表示される、この名前空間に関連付けられている接続文字列と他のメタデータを確認できます。 

![][44]

**SharedAccessKey** の値を書き留めておくか、クリップボードにコピーしておいてください。


<h2>内部設置型でサーバーの作成</h2>

まず、仮の内部設置型商品カタログ システムを構築します。その方法は
非常に簡単です。このサービスは、統合しようとしている
完全なサービス表示による内部設置型商品カタログ システムとして
表示できます。

このプロジェクトは、Visual Studio コンソール アプリケーションとして開始します。このプロジェクトでは、
Service Bus の NuGet パッケージを使用して、Service Bus の
ライブラリおよび構成設定を追加します。NuGet Visual Studio の拡張機能により、
ライブラリおよびツールを Visual Studio または Visual Studio Express に
簡単にインストールおよび更新できます。Service Bus NuGet パッケージは、
Service Bus API の取得およびアプリケーションの構成を
すべて Service Bus の依存関係で最も簡単に行う方法です。NuGet および Service Bus の使用については、
「[Using the NuGet Service Bus Package (NuGet サービス バス パッケージの使用)][]」参照してください。

### プロジェクトの作成

1.  管理者特権を使用して、Microsoft Visual
    Studio 2013 または Microsoft Visual Studio Express を起動します。管理者特権で
    Visual Studio を起動するには、
    **[Microsoft Visual Studio 2013] (または [Microsoft Visual Studio Express])** を右クリックし、**[管理者として実行]** をクリックします。
2.  Visual Studio で、**[ファイル]** メニューの **[新規作成]** をクリックし、
    **[プロジェクト]** をクリックします。

    ![][10]

3.   **[インストールされたテンプレート]** で **[Visual C#]** をクリックし、**[コンソール
    アプリケーション]** をクリックします。**[名前]** ボックスに名前として
    **[Productsserver]** と入力します。

    ![][11]

4.  **[OK]** をクリックして **ProductsServer** プロジェクトを作成します。

5.   **ソリューション エクスプローラー**で **[ProductsServer]** を右クリックし、
    **[プロパティ]** をクリックします。
6.  左側の **[アプリケーション]** タブをクリックし、**.NET
    Framework 4** または **.NET Framework 4.5** が **[ターゲット フレームワーク]** ドロップダウン リストに表示されていることを確認します。表示されていない場合、ドロップダウン リストから該当するプロジェクトを選択し、****
    プロジェクトの再読み込みを確認するメッセージが表示されたら [はい] をクリックします。

    ![][12]

7.   既に Visual Studio 用の NuGet パッケージ マネージャーをインストールしている場合は、次の手順に進みます。まだインストールしていない場合は、[NuGet][] にアクセスし、[[Install NuGet]](http://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c) をクリックします。メッセージに従って NuGet パッケージ マネージャーをインストールし、Visual Studio を再起動します。

7.   **ソリューション エクスプローラー**で **[参照設定]** を右クリックし、
    **[NuGet パッケージの管理]** をクリックします。
8.  NuGet ダイアログ ボックスの左側の列で、**[オンライン]** をクリックします。

9. 	右側の列で **[検索]** ボックスをクリックして、「**windows Azure**」と入力し、**Windows
    Azure Service Bus** の項目を選択します。**[インストール]** をクリックして
    インストールを完了し、このダイアログを閉じます。

    ![][13]

    これで、必要なクライアント アセンブリを参照できるようになりました。

9.   商品のコントラクト用に新しいクラスを追加します。**ソリューション エクスプローラ**で、
    **[productsserver]** プロジェクトを右クリックして、**[追加]** をクリックした後、
    **[クラス]** をクリックします。

    ![][14]

10. **[名前]** ボックスに「**ProductsContract.cs**」と入力します。次に、
    **[追加]** をクリックします。
11. **ProductsContract.cs** では、名前空間の定義を
    次のコードに置き換えます。このコードは、サービスのコントラクトを定義します。

        namespace ProductsServer
        {
            using System.Collections.Generic;
            using System.Runtime.Serialization;
            using System.ServiceModel;

            // Define the data contract for the service
            [DataContract]
            // Declare the serializable properties
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

12. Program.cs では、名前空間の定義を
    次のコードに置き換えます。このコードは、プロファイル サービスとそのホストを追加します。

        namespace ProductsServer
        {
            using System;
            using System.Linq;
            using System.Collections.Generic;
            using System.ServiceModel;

            // Implement the IProducts interface
            class ProductsService : IProducts
            {
                
                // Populate array of products for display on Website
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
                // when the list of products is retrieved
                public IList<ProductData> GetProducts()
                {
                    Console.WriteLine("GetProducts called.");
                    return products;
                }

            }

            class Program
            {
                // Define the Main() function in the service application
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

13. **ソリューション エクスプローラ**で、**app.config** ファイルをダブルクリックして、
    **Visual Studio** エディターでそのファイルを開きます。
    **<system.ServiceModel>** の内容を、次の XML コードに置き換えます。このとき、
    *yourServiceNamespace* をサービスの名前空間の名前に置き換えます。
    また、*yourIssuerSecret* を以前に
    Azure の管理ポータルから取得したキーに置き換えます。

        <system.serviceModel>
          <extensions>
             <behaviorExtensions>
                <add name="transportClientEndpointBehavior" type="Microsoft.ServiceBus.Configuration.TransportClientEndpointBehaviorElement, Microsoft.ServiceBus, Version=2.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
              </behaviorExtensions>
              <bindingExtensions>
                 <add name="netTcpRelayBinding" type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Version=2.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
              </bindingExtensions>
          </extensions>
          <services>
             <service name="ProductsServer.ProductsService">
               <endpoint address="sb://yourServiceNamespace.servicebus.windows.net/products" binding="netTcpRelayBinding" contract="ProductsServer.IProducts"
        behaviorConfiguration="products"/>
             </service>
          </services>
          <behaviors>
             <endpointBehaviors>
               <behavior name="products">
                 <transportClientEndpointBehavior>
                    <tokenProvider>
                       <sharedSecret issuerName="owner" issuerSecret="yourIssuerSecret" />
                    </tokenProvider>
                 </transportClientEndpointBehavior>
               </behavior>
             </endpointBehaviors>
          </behaviors>
        </system.serviceModel>

14. **F6** キーを押すか、**[ビルド]** メニューの **[ソリューションのビルド]** をクリックしてアプリケーションをビルドし、ここまでの作業に問題がないことを確認します。

<h2>ASP.NET MVC アプリケーションを作成する</h2>

このセクションでは、簡単な ASP.NET アプリケーションを作成します。
このアプリケーションでは、商品サービスから取得されたデータを表示します。

### プロジェクトの作成

1.   Microsoft Visual Studio 2013 が管理者特権によって実行されていることを確認します。確認できない場合は、
    管理者特権で Visual Studio を起動し、
    **[Microsoft Visual Studio 2013] (または [Microsoft Visual Studio Express])** を右クリックし、**[管理者として実行]** をクリックします。Windows
    このガイドで後述する Azure コンピューティング エミュレーターでは、
    Visual Studio を管理者特権で起動する必要があります。

2.  Visual Studio で、**[ファイル]** メニューの **[新規作成]** をクリックし、
    **[プロジェクト]** をクリックします。

3.  **[インストールされたテンプレート]** で **[Visual C#]** をクリックし、**[ASP.NET MVC 4 Web アプリケーション]** をクリックします。プロジェクト名として、「**ProductsPortal**」と入力します。次に、
    **[OK]** をクリックします。

    ![][15]

4.  **[テンプレートの選択]** ボックスの一覧で **[MVC]** をクリックし、
    次に **[OK]** をクリックします。

    ![][16]

5.  **ソリューション エクスプローラー**で **[Models]** を右クリックし、**[追加]**、
    **[クラス]** の順にクリックします。**[名前]** ボックスに名前として
    「**Product.cs**」と入力します。次に **[追加]** をクリックします。

    ![][17]

### Web アプリケーションの変更

1.  Visual Studio の Product.cs ファイルで、既存の名前空間の定義を
    次のコードに置き換えます。

        // Declare properties for the products inventory
        namespace ProductsWeb.Models
        {
            public class Product
            {
                public string Id { get; set; }
                public string Name { get; set; }
                public string Quantity { get; set; }
            }
        }

2.  In the HomeController.cs file in Visual Studio, replace the existing
    namespace definition with the following code:

        namespace ProductsWeb.Controllers
        {
            using System.Collections.Generic;
            using System.Web.Mvc;
            using Models;

            public class HomeController : Controller
            {
                // Return a view of the products inventory
                public ActionResult Index(string Identifier, string ProductName)
                {
                    var products = new List<Product> 
                        {new Product {Id = Identifier, Name = ProductName}};
                    return View(products);
                }

            }
        }

3.  **ソリューション エクスプローラー**で Views\Shared を展開します。

    ![][18]

4.  次に、_Layout.cshtml をダブルクリックします。Visual Studio エディターが開き、このファイルが表示されます。

5.  **My ASP.NET MVC Application** となっている箇所をすべて **LITWARE's Products** に置き換えます。

6. **[Home]** [About]、[Contact] の各リンクを削除します。以下の強調表示されたコードを削除します。

	![][41]

7.  **ソリューション エクスプローラー**で Views\Home を展開します。

    ![][20]

8.  Index.cshtml をダブルクリックして、Visual Studio エディターで開きます。
    ファイルの内容全体を次のコードに置き換えます。
	
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


9.  ここまでの作業の精度を確認するには、**F6** キーを押すか、または
    **Ctrl+Shift+B** を押してプロジェクトをビルドします。


### ローカルでのアプリケーションの実行

アプリケーションを実行して、動作を確認します。

1.   **ProductsPortal** がアクティブなプロジェクトであることを確認します。
    **ソリューション エクスプ ローラー**でプロジェクト名を右クリックし、
    [スタートアップ プロジェクトに設定] を選択します。
2.  **Visual Studio** で、**F5** キーを押します。
3.  アプリケーションがブラウザーに表示され、実行されます。

    ![][21]

    <h2>アプリケーションを AZURE にデプロイする準備を完了させる</h2>

    アプリケーションは、Azure Cloud Service または Azure Websites にデプロイできます。Azure Cloud Service と Azure Websites の違いについては、「[Azure Execution Models (Azure 実行モデル)][」を参照してください。]Azure の Web サイトにアプリケーションをデプロイする方法については、「[Deploying an ASP.NET Web Application to an Azure Website (ASP.NET Web アプリケーションを Azure の Web サイトに展開する)](http://www.windowsazure.com/ja-jp/develop/net/tutorials/get-started/)」を参照してください。このセクションには、アプリケーションを Azure Cloud Service にデプロイする詳細な手順が記載されています。

    アプリケーションをクラウド サービスにデプロイするには、クラウド サービス プロジェクトのデプロイ プロジェクトをソリューションに追加します。
    デプロイメント プロジェクトには、
    アプリケーションをクラウドで適切に実行するのに必要な構成情報が
    含まれています。

    1.  アプリケーションをクラウドにデプロイできるようにするには、
        **ソリューション エクスプ ローラー**で **ProductsPortal** プロジェクトを右クリックし、
        **[変換]** をクリックしてから、**[Azure Cloud Service プロジェクトに変換]** をクリックします。

        ![][22]

    2.  アプリケーションをテストするには、**F5** キーを押します。
    3.   これにより、Azure コンピューティング エミュレーターが起動します。コンピューティング
        エミュレーターでは、ローカル コンピューターを使用して
        Azure で実行するコンピューターをエミュレートします。エミュレーターの開始を確認するには、
        システム トレイで次の事項を確認します。

        ![][23]

    4.  ブラウザーに表示されるアプリケーションがローカルで実行されたままである
        アプリケーションの外観および機能が、
        以前に実行した通常の ASP.NET MVC 4 アプリケーションと同じである

    <h2>統合する</h2>

    次の手順では、内部設置型の商品サーバーと
    ASP.NET MVC アプリケーションを連結します。

    1.  Visual Studio でプロジェクトが開かれていない場合、
        「ASP.NET MVC アプリケーションを作成する」で作成した **ProductsPortal** プロジェクトを
        再度開きます。

    2.  「内部設置型サーバーの作成」と同様の手順で、
        NuGet パッケージをプロジェクトの参照に追加します。
        ソリューション エクスプ ローラーで、**[参照設定]** を右クリックし、
        **[NuGet パッケージの管理]** をクリックします。

    3.  "WindowsAzure.ServiceBus" を検索し、**[Windows
        Azure Service Bus]** の項目を選択します。インストールを完了して
        このダイアログ ボックスを閉じます。

    4.  ソリューション エクスプローラで、**[ProductsPortal]** プロジェクトを右クリックし、
        **[追加]**、**[既存の項目]** の順にクリックします。

    5.  **ProductsContract.cs** ファイルに
        **[Productsserver]** コンソール プロジェクトから移動します。ProductsContract.cs を
        クリックして強調表示します。**[追加]** の隣にある下向き矢印をクリックしてから、
        **[リンクとして追加]** をクリックします。

        ![][24]

    6.  次に、Visual Studio エディターから **HomeController.cs** ファイルを開き、
        名前空間の定義を次のコードに置き換えます。
        このとき、*yourServiceNamespace* をサービスの名前空間の名前に置き換えてください。
        また、*yourIssuerSecret* を自分のキーに置き換えてください。
        これで、クライアントから内部設置型サービスを呼び出し、
        呼び出しの結果を返すことができます。

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
                    // Declare the channel factory
                    static ChannelFactory<IProductsChannel> channelFactory;

                    static HomeController()
                    {
                        // Create shared secret token credentials for authentication 
                        channelFactory = new ChannelFactory<IProductsChannel>(new NetTcpRelayBinding(), 
                            "sb://yourServiceNamespace.servicebus.windows.net/products");
                        channelFactory.Endpoint.Behaviors.Add(new TransportClientEndpointBehavior { 
                            TokenProvider = TokenProvider.CreateSharedSecretTokenProvider(
                                "owner", "yourIssuerSecret") });
                    }

                    public ActionResult Index()
                    {
                        using (IProductsChannel channel = channelFactory.CreateChannel())
                        {
                            // Return a view of the products inventory
                            return this.View(from prod in channel.GetProducts()
                                             select
                                                 new Product { Id = prod.Id, Name = prod.Name, 
                                                     Quantity = prod.Quantity });
                        }
                    }
                }
            }

    7.  ソリューション エクスプローラで、**[ProductsPortal]** ソリューションを右クリックし、
        **[追加]**、**[既存のプロジェクト]** の順にクリックします。

    8.  **[Productsserver]** プロジェクトに移動し、
        **ProductsServer.csproj** ソリューション ファイルをダブルクリックして追加します。

    9.  ソリューション エクスプローラで、**[ProductsPortal]** ソリューションを右クリックし、
        **[プロパティ]** をクリックします。

    10. 左側で、**[スタートアップ プロジェクト]** をクリックします。右側で
        **[マルチ スタートアップ プロジェクト]** をクリックします。このとき、
        **[Productsserver]**、**[ProductsPortal.Azure]**、および
        **ProductsPortal** の順で表示されていること、**[開始]** が
        **[ProductsServer]** と **[ProductsPortal.Azure]** のアクションとして設定されていること、
        および**[なし]** が **[ProductsPortal]** のアクションとして設定されていることを確認してください。
        例:

        ![][25]

    11. [プロパティ] ダイアログ ボックスで、左側の **[projectdependencies]** を
        クリックします。

    12. **[プロジェクト]** ドロップダウン リストから
        **[Productsserver]** をクリックします。**ProductsPortal** チェック ボックスがオフになっていることを確認します。
        また、**[ProductsPortal.Azure]** チェックボックスがオンになっていることを確認します。次に、**[OK]** をクリックします。

        ![][26]

    <h2>アプリケーションを実行する</h2>

    1.  Visual Studio の **[ファイル]** メニューで **[すべて保存]** をクリックします。

    2.   **F5** キーを押して、アプリケーションをビルドおよび実行します。内部設置型
        サーバー (**[ProductsServer]** コンソール アプリケーション) を最初に開始し、
        次に **ProductsWeb** アプリケーションを
        ブラウザーのウィンドウで開始します (次のスクリーンショットを参照してください)。このとき、
        製品インベントリで、製品サービスの内部設置型システムから
        取得したデータが表示されていることを確認できます。

        ![][1]

    <h2>AZURE にアプリケーションを展開する</h2>

    1.  **ソリューション エクスプローラー**で **ProductsPortal** プロジェクトを右クリックし、
        **[Azure に発行する]** をクリックします。

    2.  すべてのサブスクリプションを表示するために、サインインする必要が生じることがあります。

        **[他のサブスクリプションを表示するためにサインイン]** をクリックします。

        ![][27]

    3.  Microsoft アカウントを使用してサインインします。


    8.  **[次へ]** をクリックします。サブスクリプションにホストされているサービスが含まれていない場合、
        サービスを作成するよう要求されます。ホストされるサービスは
        Windows Azure サブスクリプション内で、
        アプリケーションのコンテナーとして機能します。アプリケーションを識別するための名前を入力し、
        アプリケーションを最適化するためのリージョンを
        選択します(ユーザーはこのリージョンから
        短時間でアプリケーションにアクセスできます)。

        ![][32]

    9.  アプリケーションの発行先となるホステッド サービスを
        選択します。その他の設定については、次の図に示す
        既定のままにします。**[次へ]** をクリックします。

        ![][33]

    10. 最後のページで **[発行]** をクリックして、デプロイ
        プロセスを開始します。

        ![][34]

        これには約 5 ～ 7 分かかります。これは
        初回発行であるため、Azure で仮想マシン (VM) がプロビジョニングされ、
        セキュリティの強化が実行され、アプリケーションをホストする
        VM の Web ロールが作成され、自分のコードが
        その Web ロールにデプロイされ、最後にロード バランサーと
        ネットワークが構成され、アプリケーションを公開できるようになります。

    11. 発行の進行中は、発行のアクティビティを
        **[Azure のアクティビティ ログ]** ウィンドウで監視できます。このログは
        通常 Visual Studio または Visual Web Developer の下部に
        ドッキングされます。

        ![][35]

    12. デプロイメントが完了したら、監視ウィンドウで
        **[Websites URL]** リンクをクリックして Websites を表示できます。

        ![][36]

        Websites は内部設置型サーバーに依存するため、
        ローカルに **[ProductsServer]** アプリケーションを実行し、
        Websites が正常に機能するようにします。クラウドの Websites 上で要求を実行すると、
        次のスクリーンショットに表示される "GetProducts called" の出力結果が示すように、
        内部設置型のコンソール アプリケーションに送信される要求が
        表示されます。

        ![][37]

Azure Cloud Service と Azure Websites の違いについては、「[Azure Execution Models (Azure 実行モデル)][」を参照してください]。

<h2>アプリケーションの停止および削除</h2>

アプリケーションをデプロイした後、そのアプリケーションを無効にして
750 時間/月 (31 日/月) のサーバー無料期間内に
他のアプリケーションを作成およびデプロイできるようにしたい場合もあります。

Azure では、消費されたサーバー時間の 1 時間単位の 料金が Web ロール インスタンスに
課金されます。サーバー時間は、アプリケーションをデプロイした時点で消費されます。
インスタンスが実行されておらず停止の状態であっても、サーバー時間は消費されます。
無料アカウントには、これらの web ロール インスタンスをホストするための
専用 VM サーバー時間 (750 時間/月 (31 日/月)) が含まれています。

次のステップでは、アプリケーションの停止と削除の方法を
想定しています。

1.  [Azure の管理ポータル]にログインします。
        [Cloud Services] をクリックし、次にサービス名をクリックします。

2.   **[ダッシュボード]** タブをクリックし、**[停止]** をクリックすると、アプリケーションが一時的に停止されます。これで、
        [開始] をクリックするだけで、アプリケーションを再度開始できます。**[削除]** をクリックすると、アプリケーションが Azure から完全に削除され、
        復元することができなくなります。

	![][43]

<h2><a name="nextsteps"></a>次のステップ</h2>  

サービス バスの詳細については、次のリソースを参照してください。  
  
* [Azure Service Bus][sbmsdn]  
* [Service Bus How To's (Service Bus の利用方法)][sbwacom]  
* [How to Use Service Bus Queues (Service Bus キューの利用方法)][sbwacomqhowto]  


  [0]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hybrid.png
  [1]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/App2.png
  [ツールと SDK の入手]: http://go.microsoft.com/fwlink/?LinkId=271920
  [NuGet]: http://nuget.org
  [2]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-3.png
  [3]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-4-2-WebPI.png
  
  
  [Azure 管理ポータル]: http://manage.windowsazure.com
  [5]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/sb-queues-03.png
  [6]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/sb-queues-04.png
  
  
  
  [Using the NuGet Service Bus Package (NuGet サービス バス パッケージの使用)]: http://go.microsoft.com/fwlink/?LinkId=234589
  [10]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-1.png
  [11]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-con-1.png
  [12]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-con-3.png
  [13]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-13.png
  [14]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-con-4.png
  [15]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-2.png
  [16]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-4.png
  [17]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-7.jpg
  [18]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-10.jpg
  
  [20]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-11.png
  [21]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/App1.png
  [22]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-21.png
  [23]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-22.png
  [24]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-12.png
  [25]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-13.png
  [26]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-14.png
  [27]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-33.png
  
  
  [30]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-36.png
  [31]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-37.png
  [32]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-38.png
  [33]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-39.png
  [34]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-40.png
  [35]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-41.png
  [36]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/App2.png
  [37]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-service1.png
  [38]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-27.png
  [39]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/sb-queues-09.png
  [40]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/sb-queues-06.png
  [41]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-40.png
  [42]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-41.png
  [43]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-43.png
  [44]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/VSProperties.png

  [sbmsdn]: http://msdn.microsoft.com/ja-jp/library/windowsazure/ee732537.aspx  
  [sbwacom]: /ja-jp/documentation/services/service-bus/  
  [sbwacomqhowto]: /ja-jp/develop/net/how-to-guides/service-bus-queues/
  [executionmodels]: http://www.windowsazure.com/ja-jp/develop/net/fundamentals/compute/
