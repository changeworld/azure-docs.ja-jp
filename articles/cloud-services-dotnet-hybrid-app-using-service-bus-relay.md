<properties 
	pageTitle="ハイブリッド内部設置型/クラウド アプリケーション (.NET) - Azure" 
	description="Azure Service Bus Relay を使用して .NET 内部設置型/クラウド ハイブリッド アプリケーションを作成する方法について説明します。" 
	services="service-bus" 
	documentationCenter=".net" 
	authors="sethmanheim" 
	manager="timlt" 
	editor="mattshel"/>

<tags 
	ms.service="service-bus" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="hero-article" 
	ms.date="02/10/2015" 
	ms.author="sethm"/>






# Service Bus Relay を使用する .NET 内部設置型/クラウド ハイブリッド アプリケーション

## はじめに

次を使用して Azure でハイブリッド クラウドのアプリケーションを簡単に開発できます。
Visual Studio 2013 と無料の Azure SDK for .NET。このガイドは、Azure を初めて使用するユーザーを対象としています。複数の Azure リソースをクラウドで運用するアプリケーションを 30 分未満で作成できます。

学習内容:

-   Web ソリューションによって使用される Web サービスを作成、または既存の Web サービスを適合させる方法。
-   Azure アプリケーションと別の場所でホストされている Web サービスの間で、Azure の Service Bus Relay を使用してデータを共有する方法。

[AZURE.INCLUDE [create-account-note](../includes/create-account-note.md)]

## Service Bus Relay がハイブリッド ソリューションで役立つ理由

ビジネス ソリューションは、通常、新しい独自のビジネス要件に対処するために記述されるカスタム コードと、既に配置されているソリューションとシステムによって提供される既存の機能の組み合わせによって構成されます。

ソリューション アーキテクトたちは、スケーラビリティが高く運用コストが低いクラウドを使い始めています。その中で、ソリューションの構成要素として利用する既存のサービス資産は企業ファイアウォールの内部にあり、クラウド ソリューションからのアクセスが難しいということがわかってきました。多くの内部サービスは、企業ネットワークと外部との境界で簡単に公開できるような方法では構築されたり、ホストされたりしていません。

 *Service Bus Relay* は、既存の
Windows Communication Foundation (WCF) web サービスを使用するユースケース向けに設計されており、それらのサービスから企業ネットワークの外部にあるソリューションへ、企業のネットワーク インフラストラクチャを大幅に変更することなく安全にアクセスできるようにします。このような Service Bus Relay サービスは既存の環境内でもホストされていますが、受信セッションや要求のリッスンは、クラウドでホストされている Service Bus にデリゲートしています。
さらに Service Bus は、Azure Active Directory Access Control を使用して、これらのサービスを未承認のアクセスから保護します。

## ソリューション シナリオ

このチュートリアルでは、商品在庫ページに商品の一覧を表示する ASP.NET MVC 4 Web サイトを作成します。

![][0]

このチュートリアルでは、既存の内部設置型システムに商品情報が格納されているものとし、Service Bus Relay を使用してそのシステムにアクセスします。これを、単純なコンソール アプリケーションで実行された、メモリ内の商品のセットが基盤となっている Web サービスでシミュレートします。このコンソール アプリケーションをユーザー自身のコンピューターで実行し、Web ロールを Azure にデプロイできます。そうすることで、Azure データセンターで実行されている Web ロールが実際にどのようにコンピューターを呼び出すかを確認できます。ただし、ユーザーのコンピューターは、ほぼ確実に少なくとも 1 つのファイアウォールとネットワーク アドレス変換 (NAT) レイヤーの背後に配置されます。

次に、完成した Web アプリケーションの開始ページのスクリーンショットを示します。

![][1]

## 開発環境を設定する

Azure アプリケーションを開発する前に、ツールを入手して、開発環境を設定する必要があります。

1.  Azure SDK for .NET をインストールするには、次のリンクをクリックします。

    [ツールと SDK の入手][]

2. 	使用している Visual Studio のバージョンで **[SDK のインストール]** をクリックします。このチュートリアルの手順では、Visual Studio 2013 を使用します。

	![][42]

4.  インストール ファイルの実行や保存を求めるメッセージが表示されたら、**[実行]** をクリックします。

    ![][2]

5.  **Web Platform Installer** の **[インストール]** をクリックし、インストールの手順を進めます。

    ![][3]

6.  インストールが完了すると、開発に必要なツールがすべて揃います。SDK には、Visual Studio で Azure アプリケーションを簡単に開発するためのツールが用意されています。Visual Studio がインストールされていない場合、無料の Visual Studio Express もインストールされます。

## サービス名前空間の作成

Azure で Service Bus 機能を使用するには、最初にサービス名前空間を作成する必要があります。名前空間は、アプリケーション内で Service Bus リソースをアドレス指定するためのスコープ コンテナーを提供します。 

[Azure 管理ポータル][]または Visual Studio のサーバー エクスプローラー を使用して名前空間と Service Bus のメッセージ エンティティを管理できますが、新しい名前空間を作成できるのは、ポータル内のみです。

### ポータルを使用してサービス名前空間を作成するには:

1.  [Azure 管理ポータル][]にログオンします。

2.  管理ポータルの左のナビゲーション ウィンドウで
    **サービス バス**。

3.  管理ポータルの下のウィンドウの **[作成]** をクリックします。   
    ![][5]

4.  **[新しい名前空間を追加する]** ダイアログで、名前空間の名前を入力します。
    その名前が使用できるかどうかがすぐに自動で確認されます。   
    ![][6]

5.  入力した名前空間の名前が利用できることを確認できたら、名前空間をホストする国またはリージョンを選択します (コンピューティング リソースを展開する国またはリージョンと同じ国またはリージョンを必ず使用してください)。

    重要:アプリケーションをデプロイする予定の国またはリージョンと **同じ国/リージョン** を選択してください。そうすることで、パフォーマンスが最高になります。

6.	ダイアログ ボックスの他のフィールドは、既定値 (**[メッセージング]** と **[標準階層]**) のままにして、チェック マークをクリックします。これで、システムによってサービス名前空間が作成され、有効になります。システムがアカウントのリソースを準備し終わるまでに、数分間かかる場合があります。

	![][38]

作成した名前空間は管理ポータルに表示されます。 アクティブ化するまで少し時間がかかります。ステータスが **[アクティブ]** になるのを待ってから、次に進みます。

## 名前空間の既定の管理資格情報の取得

メッセージング エンティティの作成など、新しい名前空間の管理操作を実行するには、その名前空間の資格情報を取得する必要があります。

1.  メイン ウィンドウで、使用するサービス名前空間の名前をクリックします。   

	![][39]
  

2.  **[接続情報]** をクリックします。   

	![][40]


3.  **[接続情報へのアクセス]** ウィンドウで、SAS キーとキー名を含む接続文字列を見つけます。   

	![][45]
    

4.  キーを書き留めておくか、クリップボードにコピーしておいてください。

## Visual Studio のサーバー エクスプローラーを使用してサービス名前空間を管理する

管理ポータルの代わりに Visual Studio を使用して名前空間を管理し、接続情報を取得するには、「[Azure Tools for Visual Studio の概要](http://http://msdn.microsoft.com/library/ff687127.aspx)」の「**Visual Studio から Azure に接続するには**」で説明されている 手順に従ってください。Azure にサインインすると、サーバー エクスプローラーの **[Microsoft Azure]** ツリーの下にある **[サービス バス]** ノードが、既に作成したすべての名前空間を使用して自動的に設定されます。いずれかの名前空間を右クリックし、**[プロパティ]** をクリックします。Visual Studio の **[プロパティ]** ウィンドウで表示される、この名前空間に関連付けられている接続文字列と他のメタデータを確認できます。 

![][44]

**SharedAccessKey** の値を書き留めておくか、クリップボードにコピーしておいてください。


## 内部設置型サーバーを作成する

まず、仮の内部設置型商品カタログ システムを構築します。かなり単純なものですが、これがこれから統合しようとしている完全なサービス機能を備えた実際の内部設置型商品カタログ システムであると考えてください。

このプロジェクトは、Visual Studio コンソール アプリケーションとして開始します。Service Bus NuGet パッケージを使用して、Service Bus ライブラリと構成設定を組み込みます。NuGet Visual Studio 拡張機能を使用すると、Visual Studio や Visual Studio Express でのライブラリやツールのインストールと更新を簡単に行うことができます。サービス バス NuGet パッケージは、Service Bus API を取得し、サービス バス依存関係をすべて備えたアプリケーションを構成する最も簡単な方法です。NuGet や Service Bus パッケージの使用方法の詳細については、「[0NuGet Service Bus パッケージの使用][]」をご覧ください。

### プロジェクトを作成する

1.  管理者特権を使用して、Microsoft Visual Studio 2013 または Microsoft Visual Studio Express を起動します。管理特権で Visual Studio を起動するには、**[Microsoft Visual Studio 2013 (または Microsoft Visual Studio Express)]** を右クリックし、**[管理者として実行]** をクリックします。
2.  Visual Studio で、**[ファイル]** メニューの **[新規作成]** をクリックした後、**[プロジェクト]** をクリックします。

    ![][10]

3.  **[インストールされたテンプレート]** で **[Visual C#]** をクリックし、**[コンソール アプリケーション]** をクリックします。**[名前]** ボックスに名前として「**ProductsServer**」と入力します。

    ![][11]

4.  **[OK]** をクリックして **ProductsServer** プロジェクトを作成します。

5.  **ソリューション エクスプローラー**で **[ProductsServer]** を右クリックし、**[プロパティ]** をクリックします。
6.  画面左側で **[アプリケーション]** タブをクリックし、**[ターゲット フレームワーク]**ドロップダウンで **[.NET Framework 4]** または **[.NET Framework 4.5]** が表示されていることを確認します。表示されていない場合は、ドロップダウンから選択し、プロジェクトを読み込み直すかどうかを確認するメッセージが表示されたら、**[はい]** をクリックします。

    ![][12]

7.  既に Visual Studio 用の NuGet パッケージ マネージャーをインストールしている場合は、次のステップに進みます。まだインストールしていない場合は、[NuGet][] にアクセスし、[[Install NuGet]](http://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c) をクリックします。メッセージに従って NuGet パッケージ マネージャーをインストールし、Visual Studio を再起動します。

7.  **[ソリューション エクスプローラー]** で、**[参照]** を右クリックし、**[NuGet パッケージの管理]** をクリックします。
8.  NuGet ダイアログ ボックスの左側の列で、**[オンライン]** をクリックします。

9. 	右側の列で、**[検索]** ボックスをクリックして「**Service Bus**」と入力し、**[Microsoft Azure の Service Bus]** 項目を選択します。**[インストール]** をクリックし、インストールが完了したら、このダイアログを閉じます。

    ![][13]

    これで、必要なクライアント アセンブリを参照できるようになりました。

9.  商品のコントラクト用に新しいクラスを追加します。**ソリューション エクスプローラー**で、**[ProductsServer]** プロジェクトを右クリックし、**[追加]** をクリックしてから **[クラス]** をクリックします。

    ![][14]

10. **[名前]** ボックスに「**ProductsContract.cs**」と入力します。**[追加]** をクリックします。
11. **ProductsContract.cs** で、名前空間の定義を次のコードに書き換えます。このコードはサービスのコントラクトを定義します。

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

12. Program.cs で、名前空間の定義を次のコードに書き換えます。このコードは、プロファイル サービスとそのホストを追加します。

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

13. **ソリューション エクスプローラー**で **app.config** ファイルをダブルクリックします。**Visual Studio** エディターが開き、このファイルが表示されます。**&lt;system.ServiceModel&gt;** の内容を次の XML コードに書き換えます。 *yourServiceNamespace* は実際のサービス名前空間の名前に置き換え、 *yourKey* は、前の手順で Azure 管理ポータルから取得した SAS キーに置き換えてください。

        <system.serviceModel>
          <extensions>
             <behaviorExtensions>
                <add name="transportClientEndpointBehavior" type="Microsoft.ServiceBus.Configuration.TransportClientEndpointBehaviorElement, Microsoft.ServiceBus, Version=2.5.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
              </behaviorExtensions>
              <bindingExtensions>
                 <add name="netTcpRelayBinding" type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Version=2.5.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
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
                       <sharedAccessSignature keyName="RootManageSharedAccessKey" key="yourKey" />
                    </tokenProvider>
                 </transportClientEndpointBehavior>
               </behavior>
             </endpointBehaviors>
          </behaviors>
        </system.serviceModel>

14. **F6** キーを押すか、**[ビルド]** メニューの **[ソリューションのビルド]** をクリックしてアプリケーションをビルドし、ここまでの作業に問題がないことを確認します。

## ASP.NET MVC アプリケーションを作成する

このセクションでは、商品サービスから取得したデータを表示する単純な ASP.NET アプリケーションを構築します。

### プロジェクトを作成する

1.  Microsoft Visual Studio 2013 が管理者特権で実行されていることを確認します。管理者特権で Visual Studio を起動するには、**[Microsoft Visual Studio 2013 (または Microsoft Visual Studio Express)]** を右クリックし、**[管理者として実行]** をクリックします。Azure コンピューティング エミュレーター (後ほどこのガイドで解説) を使用するには、管理者特権で Visual Studio を開始する必要があります。

2.  Visual Studio で、**[ファイル]** メニューの **[新規作成]** をクリックした後、**[プロジェクト]** をクリックします。

3.  **[インストールされたテンプレート]** で **[Visual C#]** をクリックし、**[ASP.NET MVC 4 Web アプリケーション]** をクリックします。プロジェクト名として、「**ProductsPortal**」と入力します。次に、**[OK]** をクリックします。

    ![][15]

4.  **[テンプレートの選択]** の一覧で **[MVC]** をクリックし、**[OK]** をクリックします。

    ![][16]

5.  **ソリューション エクスプローラー**で **[Models]** を右クリックし、**[追加]**、**[クラス]** の順にクリックします。**[名前]** ボックスに「**Product.cs**」と入力します。**[追加]** をクリックします。

    ![][17]

### Web アプリケーションを変更する

1.  Visual Studio で、Product.cs ファイルの既存の名前空間定義を次のコードに書き換えます。

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

2.  Visual Studio で、HomeController.cs ファイルの既存の名前空間定義を次のコードに書き換えます。

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

6. **Home**、**About**、**Contact** の各リンクを削除します。以下の強調表示されたコードを削除してください。

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


9.  ここまでの作業が正確にできていることを確認するために、**F6** キーか **Ctrl + Shift + B** キーを押して、プロジェクトをビルドします。


### ローカルでのアプリケーションの実行

アプリケーションを実行して、動作を確認します。

1.  **ProductsPortal** がアクティブなプロジェクトであることを確認します。**ソリューション エクスプローラー**で、プロジェクト名を右クリックして **[スタートアップ プロジェクトに設定]** をクリックします。
2.  **Visual Studio** で、**F5** キーを押します。
3.  アプリケーションがブラウザーに表示され、実行されます。

    ![][21]

## アプリケーションを AZURE にデプロイする準備を完了する

アプリケーションは、Azure クラウド サービスや Azure Websites にデプロイできます。Azure クラウド サービスと Azure Websites の違いについては、「[Azure 実行モデル][executionmodels]」をご覧ください。Azure の Web サイトにアプリケーションをデプロイする方法については、「[Deploying an ASP.NET Web Application to an Azure Website (ASP.NET Web アプリケーションを Azure の Web サイトに展開する)](http://azure.microsoft.com/develop/net/tutorials/get-started/)」をご覧ください。このセクションには、アプリケーションを Azure クラウド サービスにデプロイする詳細な手順が記載されています。

アプリケーションをクラウド サービスにデプロイするには、クラウド サービス プロジェクトのデプロイ プロジェクトをソリューションに追加します。このデプロイ プロジェクトには、アプリケーションをクラウドで適切に実行するために必要な構成情報が含まれています。

1.  アプリケーションをクラウドにデプロイできるようにするには、**ソリューション エクスプローラー**で **ProductsPortal** プロジェクトを右クリックし、**[変換]**、**[Azure クラウド サービス プロジェクトへの変換]** の順にクリックします。

    ![][22]

2.  アプリケーションをテストするには、**F5** キーを押します。

3.  これにより、Azure コンピューティング エミュレーターが起動します。コンピューティング エミュレーターでは、Azure でのアプリケーションの実行をローカル コンピューターでエミュレートします。エミュレーターが起動されたことは、システム トレイの表示によって確認できます。

       ![][23]

4.  この場合も、ブラウザーにはローカルで実行中のアプリケーションが表示されます。これは、先ほど通常の ASP.NET MVC 4 アプリケーションとして実行したときと同じように表示され、動作します。

## 統合する

次の手順では、内部設置型の商品サーバーと ASP.NET MVC アプリケーションを連結します。

1.  「ASP.NET MVC アプリケーションの作成」セクションで作成した **ProductsPortal** プロジェクトを閉じている場合は、Visual Studio でもう一度開きます。

2.  「内部設置型サーバーの作成」セクションに記載されている手順に従い、NuGet パッケージを Reference プロジェクトに追加します。[ソリューション エクスプローラー] で、**[参照]** を右クリックし、**[NuGet パッケージの管理]** をクリックします。

3.  "Service Bus"を検索して、**[Microsoft Azure の Service Bus]** 項目を選択します。次に、インストールを完了し、このダイアログを閉じます。

4.  ソリューション エクスプローラーで **ProductsPortal** プロジェクトを右クリックし、**[追加]** をクリックしてから **[既存の項目]** をクリックします。

5.  **ProductsServer** コンソール プロジェクトの **ProductsContract.cs** ファイルに移動します。ProductsContract.cs をクリックして強調表示します。[**追加**] の横の下向き矢印をクリックし、[**リンクとして追加**] をクリックします。

	![][24]

6.  次に、Visual Studio エディターで **HomeController.cs** ファイルを開き、名前空間定義を次のコードに書き換えます。 *yourServiceNamespace* は実際のサービス名前空間の名前、 *yourKey* は SAS キーに置き換えてください。これで、クライアントから内部設置型サービスを呼び出し、その結果を返すことができます。

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
                            TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider(
                                "RootManageSharedAccessKey", "yourKey") });
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
7.  ソリューション エクスプローラーで **ProductsPortal** ソリューションを右クリックし、**[追加]**、**[既存のプロジェクト]** の順にクリックします。

8.  **ProductsServer** プロジェクトに移動し、**ProductsServer.csproj** ソリューション ファイルをダブルクリックして追加します。

9.  ソリューション エクスプローラーで **ProductsPortal** ソリューションを右クリックし、**[プロパティ]** をクリックします。

10. 左側で、**[スタートアップ プロジェクト]** をクリックします。右側で、**[マルチ スタートアップ プロジェクト]** をクリックします。**ProductsServer**、**ProductsPortal.Azure**、**ProductsPortal** がこの順序で表示され、**ProductsServer** と **ProductsPortal.Azure** の動作に **[開始]** が設定され、**ProductsPortal** の動作に **[なし]** が設定されているようにしてください。次に例を示します。

      ![][25]

11. 引き続き、[プロパティ] ダイアログ ボックスの左側で、**[ProjectDependencies]** をクリックします。

12. **[プロジェクト]** ドロップダウンから **[ProductsServer]** をクリックします。**[ProductsPortal]** がオフ、**[ProductsPortal.Azure]** がオンになっていることを確認します。次に、**[OK]** をクリックします。

    ![][26]

## アプリケーションの実行

1.  Visual Studio の **[ファイル]** メニューで **[すべて保存]** をクリックします。

2.  **F5** キーを押して、アプリケーションをビルドして実行します。次のスクリーンショットのように、まず内部設置型サーバー (**ProductsServer** コンソール アプリケーション) を開始し、次に **ProductsWeb** アプリケーションをブラウザー ウィンドウで開始する必要があります。この時点で、内部設置型システムの商品サービスから取得された商品在庫一覧データが表示されます。

    ![][1]

## Azure へのアプリケーションのデプロイ

1.  **ソリューション エクスプローラー**で **ProductsPortal** プロジェクトを右クリックし、**[Microsoft Azure への発行]** をクリックします。

2.  すべてのサブスクリプションを表示するために、サインインする必要が生じることがあります。

    **[他のサブスクリプションを表示するためにサインイン]** をクリックします。

    ![][27]

3.  Microsoft アカウントを使用してサインインします。


8.  **[次へ]** をクリックします。サブスクリプションにホステッド サービスがまだ含まれていない場合は、ホステッド サービスの作成を求めるメッセージが表示されます。ホステッド サービスは、Azure サブスクリプションにおけるアプリケーションのコンテナーとして機能します。アプリケーションを識別する名前を入力し、アプリケーションが最適化される対象リージョンを選択します(このリージョンからアプリケーションにアクセスするユーザーは、読み込み時間が短くなることが期待できます)。

9.  アプリケーションの発行先のホステッド サービスを選択します。その他の設定については、次の図に示す既定のままにします。**[次へ]** をクリックします。

    ![][33]

10. 最後のページで **[発行]** をクリックして、展開プロセスを開始します。

    ![][34]

これには約 5 ～ 7 分かかります。これは最初の発行であるため、Azure が仮想マシン (VM) を準備して、セキュリティを強化し、アプリケーションをホストするためのWeb ロールを VM 上に作成して、その Web ロールにコードを展開します。最後に、ロード バランサーとネットワークを構成して、アプリケーションを公開します。

11. 発行の処理中は、**[Azure のアクティビティ ログ]** ウィンドウでアクティビティを監視できます。通常、このウィンドウは Visual Studio または Visual Web Developer の下部にドッキングされています。

    ![][35]

12. デプロイメントが完了した後、監視ウィンドウで **[Web サイトの URL]** リンクをクリックすると、作成した Web サイトが表示されます。

    ![][36]

    Web サイトは内部設置型サーバーに依存するため、この Web サイトを正しく機能させるには、**ProductsServer** アプリケーションをローカルに実行する必要があります。クラウドの Web サイトで要求を実行すると、次のスクリーンショットにある "GetProducts" の出力が示すように、内部設置型コンソール アプリケーションで要求が呼び出されることを確認できます。

    ![][37]

Azure クラウド サービスと Azure Websites の違いについては、「[Azure 実行モデル][executionmodels]」をご覧ください。

## アプリケーションの停止と削除

アプリケーションのデプロイ後に、無料サーバー時間である 750 時間/月 (31 日/月) の範囲内で他のアプリケーションを作成してデプロイするには、そのアプリケーションを無効にする必要があります。

Azure では、消費されたサーバー時間の 1 時間単位の料金が Web ロール インスタンスに課金されます。インスタンスが実行されていない場合や停止状態の場合でも、アプリケーションを展開した直後からサーバー時間が消費されます。
無料アカウントには、専用仮想マシンで Web ロール インスタンスをホストするサーバー時間として 1 か月あたり 750 時間 (1 か月は 31 日換算) が含まれています。

次の手順では、アプリケーションの停止と削除の方法を示します。

1.  [Azure 管理ポータル]にログインし、[クラウド サービス] をクリックして、クラウド サービス名をクリックします。

2.  **[ダッシュボード]** タブをクリックし、**[停止]** をクリックすると、アプリケーションが一時的に停止されます。[開始] をクリックするだけで、再度開始できます。**[削除]** をクリックすると、アプリケーションが Azure から完全に削除されます。復元することはできません。

	![][43]

## 次のステップ  

サービス バスの詳細については、次のリソースをご覧ください。  
  
* [Azure Service Bus][sbmsdn]  
* [Service Busのドキュメント][sbwacom]  
* [サービス バス キューの使用方法][sbwacomqhowto]  


  [0]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hybrid.png
  [1]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/App2.png
  [ツールと SDK の入手]: http://go.microsoft.com/fwlink/?LinkId=271920
  [NuGet]: http://nuget.org
  [2]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-3.png
  [3]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-4-2-WebPI.png
  
  
  [Azure 管理ポータル]: http://manage.windowsazure.com
  [5]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/sb-queues-03.png
  [6]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/sb-queues-04.png
  
  
  
  [0NuGet Service Bus パッケージの使用]: http://go.microsoft.com/fwlink/?LinkId=234589
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
  [45]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-45.png

  [sbmsdn]: http://msdn.microsoft.com/library/azure/ee732537.aspx  
  [sbwacom]: /documentation/services/service-bus/  
  [sbwacomqhowto]: /develop/net/how-to-guides/service-bus-queues/
  [executionmodels]: http://azure.microsoft.com/develop/net/fundamentals/compute/

<!--HONumber=47-->
