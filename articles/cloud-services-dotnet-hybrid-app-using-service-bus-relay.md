<properties linkid="dev-net-tutorials-hybrid-solution" urlDisplayName="Hybrid Application" pageTitle="Hybrid On-Premises/ Cloud Application (.NET) - Azure" metaKeywords="Azure Service Bus tutorial,hybrid .NET" description="Learn how to create a .NET On-Premises/Cloud Hybrid Application Using the Azure Service Bus Relay." metaCanonical="" services="service-bus" documentationCenter=".NET" title=".NET On-Premises/Cloud Hybrid Application Using Service Bus Relay" authors="sethm" solutions="" manager="dwrede" editor="mattshel" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="sethm"></tags>

# Service Bus Relay を使用する .NET 内部設置型/クラウド ハイブリッド アプリケーション

## <span class="short-header">はじめに</span>はじめに

Azure を利用するハイブリッド クラウド アプリケーションは、
Visual Studio 2013 および無料の Azure SDK for .NET を使用して簡単に開発できます。このガイドは、
Azure を初めて使用するユーザーを対象としています。複数の
Windows Azure リソースをクラウドで運用するアプリケーションを
 30 分未満で作成できます。

学習内容:

-   Web ソリューションによって使用される Web サービスを作成するか、
    既存の Web サービスを適合させる方法。
-   Azure アプリケーションと別の場所でホストされている Web サービスの間で、
    Azure の Service Bus Relay を使用してデータを共有する方法。

[WACOM.INCLUDE [create-account-note][]]

### Service Bus Relay がハイブリッド ソリューションで役立つ理由

ビジネス ソリューションは、通常、新しい独自のビジネス要件に
対処するために記述されるカスタム コードと、既に配置されている
ソリューションおよびシステムによって提供される既存の機能の
組み合わせによって構成されます。

ソリューション アーキテクトたちは、スケーラビリティが高く運用コストが
低いクラウドを使い始めています。その中で、
ソリューションの構成要素として利用する既存のサービス資産は
企業ファイアウォールの内部にあり、クラウド ソリューションからのアクセスが
難しいということがわかってきました。多くの内部サービスは、
企業ネットワークと外部との境界で簡単に公開できるような方法では
構築およびホストされていません。

*Service Bus Relay* は、既存の Windows Communication Foundation (WCF)
Web サービスを使用し、企業ネットワークのインフラストラクチャの
内部を変更せずに、企業の境界の外部にあるソリューションから
それらのサービスに安全にアクセスできる使用事例として
設計されています。このような Service Bus Relay サービスは
既存の環境内でもホストされていますが、受信セッションや要求の
リッスンは、クラウドでホストされているサービス バスにデリゲートしています。さらに
Service Bus は、Azure Active Directory Access Control を使用して、
これらのサービスを未承認のアクセスから保護します。

### ソリューション シナリオ

このチュートリアルでは、商品在庫ページに商品の一覧を表示する
ASP.NET MVC 4 Web サイトを作成します。

![][]

このチュートリアルでは、既存の内部設置型システムに商品情報が
格納されているものとし、Service Bus Relay を使用してそのシステムに
アクセスします。これを、単純なコンソール アプリケーションで実行され、
メモリ内の商品のセットが基盤となっている Web サービスでシミュレートします。この
コンソール アプリケーションをユーザー自身のコンピューターで実行し、
Web ロールを Azure にデプロイすることができます。そうすることで、
Azure データセンターで実行されている Web ロールが実際にどのように
コンピューターを呼び出すかを確認できます。
ただし、ユーザーのコンピューターは、ほぼ確実に少なくとも 1 つの
ファイアウォールとネットワーク アドレス変換 (NAT) レイヤーの背後に配置されます。

次に、完成した Web アプリケーションの開始ページのスクリーンショットを示します。

![][1]

## <span class="short-header">環境の設定</span>開発環境の設定

Azure アプリケーションを開発する前に、ツールを入手して、
開発環境を設定する必要があります。

1.  Azure SDK for .NET をインストールするには、次のボタンをクリックします。

    [ツールと SDK の入手][]

2.  **[SDK のインストール]** をクリックします。

3.  使用している Visual Studio のバージョンに対応するリンクを選択します。このチュートリアルの手順では、Visual Studio 2013 を使用します。

    ![][2]

4.  **WindowsAzureSDKForNet.exe** を実行するか、保存するかを確認するメッセージが表示されたら、
    **[実行]** をクリックします。

    ![][3]

5.  Web Platform Installer の **[インストール]** をクリックし、インストールの手順を進めます。

    ![][4]

6.  インストールが完了すると、開発に必要なツールが
    すべて揃います。SDK には、Visual Studio で
    Azure アプリケーションを簡単に開発するためのツールが用意されています。Visual Studio が
    インストールされていない場合は、無料の
    Visual Studio Express もインストールされます。

## <span class="short-header">名前空間の作成</span>サービス名前空間の作成

Azure で Service Bus 機能を使用するには、最初に
サービス名前空間を作成する必要があります。サービス名前空間は、アプリケーション内で
Service Bus リソースをアドレス指定するためのスコープ コンテナーを提供します。

[Azure 管理ポータル][]または Visual Studio のサーバー エクスプローラー を使用して名前空間と Service Bus のメッセージ エンティティを管理できますが、新しい名前空間を作成できるのは、ポータル内のみです。

### ポータルを使用してサービス名前空間を作成するには:

1.  [Azure 管理ポータル][]へのログオン

2.  管理ポータルの左のナビゲーション ウィンドウで、
    **[サービス バス]** をクリックします。

3.  管理ポータルの下のウィンドウの **[作成]** をクリックします。
    ![][5]

4.  **[新しい名前空間を追加する]** ダイアログで、名前空間の名前を入力します。
    その名前が使用できるかどうかがすぐに自動で確認されます。
    ![][6]

5.  入力した名前が利用できることを確認できたら、名前空間をホスト
    する国またはリージョンを選択します (コンピューティング リソース
    のデプロイ先となる国またはリージョンと同じ国またはリージョンを
    必ず使用してください)。

    重要: アプリケーションをデプロイする予定の国またはリージョンと**同じ国/リージョン**を選択
    してください。そうすることで、パフォーマンスが最高になります。

6.  チェック マークをクリックします。これで、システムによってサービス名前空間が
    作成され、有効になります。システムがアカウントのリソースを準備し
    終わるまでに、数分間かかる場合があります。

    ![][7]

作成した名前空間が管理ポータルに表示され、アクティブになります。
これには少し時間がかかります。ステータスが **[アクティブ]** になるのを待ってから、
次に進みます。

## <span class="short-header">管理資格情報の取得</span>名前空間の既定の管理資格情報の取得

新規作成した名前空間に対してキューの作成などの管理操作を実行するには、
名前空間の管理資格情報を取得する必要があります。

1.  メイン ウィンドウで、使用するサービス名前空間の名前をクリックします。

    ![][8]

2.  **[接続情報]** をクリックします。

    ![][9]

3.  **[接続情報へのアクセス]** ウィンドウで、**[既定の発行者]** と **[既定のキー]** のエントリを探します。

4.  キーを書き留めておくか、クリップボードにコピーしておいてください。

### Visual Studio のサーバー エクスプローラーを使用したサービス名前空間の管理:

管理ポータルの代わりに Visual Studio を使用して名前空間を管理し、接続情報を取得するには、「[Azure Tools for Visual Studio の概要][]」の「**Visual Studio から Azure に接続するには**」で説明されている手順に従ってください。Azure にサインインすると、サーバー エクスプローラーの **[Microsoft Azure]** ツリーの下にある **[サービス バス]** ノードが、既に作成したすべての名前空間を使用して自動的に設定されます。いずれかの名前空間を右クリックし、**[プロパティ]** をクリックします。Visual Studio の **[プロパティ]** ウィンドウで表示される、この名前空間に関連付けられている接続文字列と他のメタデータを確認できます。

![][10]

**SharedAccessKey** の値を書き留めておくか、クリップボードにコピーしておいてください。

## <span class="short-header">内部設置型サーバーの作成</span>内部設置型サーバーの作成

まず、仮の内部設置型商品カタログ システムを構築します。かなり
単純なものですが、これがこれから統合しようとしている
完全なサービス機能を備えた実際の内部設置型商品カタログ システムであると
考えてください。

このプロジェクトは、Visual Studio コンソール アプリケーションとして開始します。Service Bus
NuGet パッケージを使用して、Service Bus ライブラリと構成設定を
組み込みます。NuGet Visual Studio 拡張機能を使用すると、
Visual Studio および Visual Studio Express でのライブラリや
ツールのインストールと更新を簡単に行うことができます。Service Bus NuGet パッケージは、Service Bus API を
取得し、Service Bus 依存関係をすべて備えたアプリケーションを
構成する最も簡単な方法です。NuGet および Service Bus パッケージの
使用方法の詳細については、「[NuGet Service Bus パッケージの使用][]」を参照してください。

### プロジェクトの作成

1.  管理者特権を使用して、Microsoft Visual Studio 2013 または
    Microsoft Visual Studio Express を起動します。管理者特権
    で Visual Studio を起動するには、
    **[Microsoft Visual Studio 2013] (または [Microsoft Visual Studio Express])** を右クリックし、**[管理者として実行]** をクリックします。
2.  Visual Studio で、**[ファイル]** メニューの **[新規作成]** を
    クリックした後、**[プロジェクト]** をクリックします。

    ![][11]

3.  **[インストールされたテンプレート]** で **[Visual C\#]** をクリックし、
    **[コンソール アプリケーション]** をクリックします。**[名前]** ボックスに名前として 「
    **ProductsServer**」と入力します。

    ![][12]

4.  **[OK]** をクリックして **ProductsServer** プロジェクトを作成します。

5.  **ソリューション エクスプローラー**で **[ProductsServer]** を
    右クリックし、**[プロパティ]** をクリックします。
6.  画面左側で **[アプリケーション]** タブをクリックし、
    **[ターゲット フレームワーク]** ボックスの一覧に **[.NET Framework 4]** または **[.NET Framework 4.5]** が表示されていることを確認します。表示されていない場合は、このボックスの一覧の [.NET Framework 4] を選択します。プロジェクトを読み込み直すかどうかを確認するメッセージが表示されたら、**[はい]**
     をクリックします。

    ![][13]

7.  既に Visual Studio 用の NuGet パッケージ マネージャーをインストールしている場合は、次のステップに進みます。まだインストールしていない場合は、[NuGet][] にアクセスし、[[Install NuGet]][] をクリックします。メッセージに従って NuGet パッケージ マネージャーをインストールし、Visual Studio を再起動します。

8.  **ソリューション エクスプローラー**で **[参照]** を右クリックし、
    **[NuGet パッケージの管理]** をクリックします。
9.  NuGet ダイアログ ボックスの左側の列で、**[オンライン]** をクリックします。

10. 右側の列で、**[検索]** ボックスをクリックして「**WindowsAzure**」と入力し、
    **[Windows Azure Service Bus]** 項目をクリックします。**[インストール]** をクリックし、
    インストールが完了したら、このダイアログを閉じます。

    ![][14]

    これで、必要なクライアント アセンブリを参照できるようになりました。

11. 商品のコントラクト用に新しいクラスを追加します。**ソリューション エクスプローラー**で、
    **[ProductsServer]** プロジェクトを右クリックし、**[追加]** をクリックしてから
    **[クラス]** をクリックします。

    ![][15]

12. **[名前]** ボックスに「**ProductsContract.cs**」と入力します。
    **[追加]** をクリックします。
13. **ProductsContract.cs** で、名前空間の定義を次のコードに書き換えます。
    このコードはサービスのコントラクトを定義します。

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

14. Program.cs で、名前空間の定義を次のコードに書き換えます。
    このコードは、プロファイル サービスとそのホストを追加します。

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

15. **ソリューション エクスプローラー**で **app.config** ファイルを
    ダブルクリックします。**Visual Studio** エディターが開き、このファイルが表示されます。
    **\<system.ServiceModel\>** の内容を次の XML コードに書き換えます。
    *yourServiceNamespace* は実際のサービス名前空間の名前に、
    *yourIssuerSecret* は、前の手順で Azure の管理ポータルから取得した実際のキーに
    置き換えてください。

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

16. **F6** キーを押すか、**[ビルド]** メニューの **[ソリューションのビルド]** をクリックしてアプリケーションをビルドし、ここまでの作業に問題がないことを確認します。

## <span class="short-header">ASP.NET MVC アプリケーションの作成</span>ASP.NET MVC アプリケーションの作成

このセクションでは、商品サービスから取得したデータを表示する
単純な ASP.NET アプリケーションを構築します。

### プロジェクトの作成

1.  Microsoft Visual Studio 2013 が管理者特権によって実行されていることを確認します。それ以外の場合、
    管理者特権で Visual Studio を起動するには、
    **[Microsoft Visual Studio 2013] (または [Microsoft Visual Studio Express])** を右クリックし、**[管理者として実行]** をクリックします。Windows Azure
    コンピューティング エミュレーター (このガイドで解説します) を
    使用するには、管理者特権で Visual Studio を起動する必要があります。

2.  Visual Studio で、**[ファイル]** メニューの **[新規作成]** を
    クリックした後、**[プロジェクト]** をクリックします。

3.  **[インストールされたテンプレート]** で **[Visual C\#]** をクリックし、**[ASP.NET Web アプリケーション]** をクリックします。プロジェクト名として、「**ProductsPortal**」と入力します。次に、
    **[OK]** をクリックします。

    ![][16]

4.  **[テンプレートの選択]** ボックスの一覧の **[MVC]** を
    クリックし、**[OK]** をクリックします。

    ![][17]

5.  **ソリューション エクスプローラー**で **[Models]** を右クリックし、
    **[追加]**、**[クラス]** の順にクリックします。**[名前]** ボックスに 「
    **Product.cs**」と入力します。**[追加]** をクリックします。

    ![][18]

### Web アプリケーションの変更

1.  Visual Studio で、Product.cs ファイルの既存の名前空間定義を、
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

2.  Visual Studio で、HomeController.cs ファイルの既存の名前空間定義を、
    次のコードに書き換えます。

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

3.  **ソリューション エクスプローラー**で Views\\Shared を展開します。

    ![][19]

4.  次に、\_Layout.cshtml をダブルクリックします。Visual Studio エディターが開き、このファイルが表示されます。

5.  "**My ASP.NET Application**" となっている箇所をすべて "**LITWARE's Products**" に置き換えます。

6.  **Home**、**About**、および **Contact** の各リンクを削除します。以下の強調表示されたコードを削除してください。

    ![][20]

7.  **ソリューション エクスプローラー**で Views\\Home を展開します。

    ![][21]

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

9.  ここまでの作業が正確にできていることを確認するために、**F6** キーか
    **Ctrl + Shift + B** キーを押して、プロジェクトをビルドします。

### ローカルでのアプリケーションの実行

アプリケーションを実行して、動作を確認します。

1.  **ProductsPortal** がアクティブなプロジェクトであることを確認します。
    **ソリューション エクスプローラー**で、プロジェクトを右クリックして
    **[スタートアップ プロジェクトに設定]** をクリックします。
2.  **Visual Studio** で、**F5** キーを押します。
3.  アプリケーションがブラウザーに表示され、実行されます。

    ![][22]

    ## <span class="short-header">Azure へのデプロイ</span>アプリケーションを Azure にデプロイするための準備

    アプリケーションは、Azure クラウド サービスまたは Azure の Web サイトにデプロイできます。Web サイトとクラウド サービスの違いについては、「[Azure Execution Models (Azure 実行モデル)][]」を参照してください。Azure の Web サイトにアプリケーションをデプロイする方法については、「[Deploying an ASP.NET Web Application to an Azure Website (ASP.NET Web アプリケーションを Azure の Web サイトに展開する)][]」を参照してください。このセクションには、アプリケーションを Azure クラウド サービスにデプロイする詳細な手順が記載されています。

    アプリケーションをクラウド サービスにデプロイするには、クラウド サービス プロジェクトのデプロイ プロジェクトをソリューションに追加します。
    このデプロイ プロジェクトには、アプリケーションを
    クラウドで適切に実行するために必要な構成情報が
    含まれています。

    1.  アプリケーションをクラウドにデプロイできるようにするには、
        **ソリューション エクスプローラー**で **ProductsPortal**
        プロジェクトを右クリックし、**[変換]**、**[Azure クラウド サービス プロジェクトへの変換]** の順にクリックします。

        ![][23]

    2.  アプリケーションをテストするには、**F5** キーを押します。
    3.  これにより、Azure コンピューティング エミュレーターが起動します。
        コンピューティング エミュレーターでは、Azure でのアプリケーションの
        実行をローカル コンピューターでエミュレートします。エミュレーターが起動されたことは、
        システム トレイの表示によって確認できます。

        ![][24]

    4.  この場合も、ブラウザーにはローカルで実行中のアプリケーションが
        表示されます。これは、先ほど通常の ASP.NET MVC 4
        アプリケーションとして実行したときと同じように表示され、動作します。

    ## <span class="short-header">各部分の統合</span>各部分の統合

    次の手順では、内部設置型の商品サーバーと ASP.NET MVC アプリケーションを
    連結します。

    1.  「ASP.NET MVC アプリケーションの作成」セクションで作成した
        **ProductsPortal** プロジェクトを閉じている場合は、
        Visual Studio でもう一度開きます。

    2.  「内部設置型サーバーの作成」セクションに記載されている手順に従い、
        NuGet パッケージを Reference プロジェクトに追加します。
        ソリューション エクスプローラーで **[参照]** を右クリックし、
        **[NuGet パッケージの管理]** をクリックします。

    3.  "WindowsAzure.ServiceBus" を検索し、**[Azure Service Bus]**
        項目をクリックします。次に、インストールを完了し、
        このダイアログを閉じます。

    4.  ソリューション エクスプローラーで **ProductsPortal**
        プロジェクトを右クリックし、**[追加]** をクリックしてから **[既存の項目]** をクリックします。

    5.  **ProductsServer** コンソール プロジェクトの
        **ProductsContract.cs** ファイルに移動します。ProductsContract.cs
        をクリックして強調表示します。**[追加]** の横の下向き矢印をクリックし、
        **[リンクとして追加]** をクリックします。

        ![][25]

    6.  次に、Visual Studio エディターで **HomeController.cs**
        ファイルを開き、名前空間定義を次のコードに
        書き換えます。*yourServiceNamespace* は実際のサービス名前空間の名前、
        *yourIssuerSecret* は実際のキーに置き換えてください。
        これで、クライアントから内部設置型サービスを呼び出し、
        その結果を返すことができます。

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

    7.  ソリューション エクスプローラーで **ProductsPortal**
         ソリューションを右クリックし、**[追加]**、**[既存のプロジェクト]** の順にクリックします。

    8.  **ProductsServer** プロジェクトに移動し、
        **ProductsServer.csproj** ソリューション ファイルをダブルクリックして追加します。

    9.  ソリューション エクスプローラーで **ProductsPortal**
         ソリューションを右クリックし、**[プロパティ]** をクリックします。

    10. 左側で、**[スタートアップ プロジェクト]** をクリックします。右側で、
        **[マルチ スタートアップ プロジェクト]** をクリックします。
        **ProductsServer**、**ProductsPortal.Azure**、および
        **ProductsPortal** がこの順序で表示され、
        **ProductsServer** および **ProductsPortal.Azure** の動作としては **[開始]** が設定され、
        **ProductsPortal** の動作としては **[なし]** が設定されているようにしてください。次に
        例を示します。

        ![][26]

    11. 引き続き、[プロパティ] ダイアログ ボックスの左側で、
        **[ProjectDependencies]** をクリックします。

    12. **[プロジェクト]** ボックスの一覧の
        **[ProductsServer]** をクリックします。**[ProductsPortal]** がオフ、
        **[ProductsPortal.Azure]** がオンになっていることを確認します。次に、**[OK]** をクリックします。

        ![][27]

    ## <span class="short-header">アプリケーションの実行</span>アプリケーションの実行

    1.  Visual Studio の **[ファイル]** メニューで **[すべて保存]** をクリックします。

    2.  **F5** キーを押して、アプリケーションをビルドおよび実行します。まず、
        内部設置型サーバー (**ProductsServer** コンソール アプリケーション) を開始し、
        次に **ProductsWeb** アプリケーションを
        ブラウザー ウィンドウで開始する必要があります。次に示すスクリーンショットを参照してください。この時点で、
        商品サービスの内部設置型システムから取得された
        商品在庫一覧データが表示されます。

        ![][1]

    ## <span class="short-header">アプリケーションのデプロイ</span>アプリケーションを Azure にデプロイする

    1.  **ソリューション エクスプローラー**で
        **ProductsPortal** プロジェクトを右クリックし、**[Azure への発行]** をクリックします。

    2.  すべてのサブスクリプションを表示するために、サインインする必要が生じることがあります。

        **[他のサブスクリプションを表示するためにサインイン]** をクリックします。

        ![][28]

    3.  Microsoft アカウントを使用してサインインします。

    4.  **[次へ]** をクリックします。サブスクリプションにホステッド サービスがまだ含まれていない場合は、
        ホステッド サービスの作成を求めるメッセージが表示されます。ホステッド サービスは、
        Windows Azure サブスクリプションにおけるアプリケーションの
        コンテナーとして機能します。アプリケーションを識別する名前を入力し、
        アプリケーションが最適化される対象リージョンを
        選択します (このリージョンからアプリケーションにアクセスする
        ユーザーは、読み込み時間が短くなることが期待できます)。

        ![][29]

    5.  アプリケーションの発行先のホステッド サービスを
        選択します。その他の設定については、次の図に示す
        既定のままにします。**[次へ]** をクリックします。

        ![][30]

    6.  最後のページで **[発行]** をクリックして、デプロイメント
        プロセスを開始します。

        ![][31]

        これには約 5 ～ 7 分かかります。これは最初の発行であるため、
        Azure は、仮想マシン (VM) を準備して、
        セキュリティを強化します。さらに、アプリケーションをホストするための
        Web ロールを VM 上に作成して、その Web ロールにコードを
        デプロイします。最後に、ロード バランサーと
        ネットワークを構成して、アプリケーションを公開します。

    7.  発行の処理中は、**[Azure のアクティビティ ログ]**
        ウィンドウでアクティビティを監視できます。
        通常、このウィンドウは Visual Studio または Visual Web Developer の
        下部にドッキングされています。

        ![][32]

    8.  デプロイメントが完了した後、監視ウィンドウで
        **[Web サイトの URL]** リンクをクリックすると、作成した Web サイトが表示されます。

        ![][1]

        Web サイトは内部設置型サーバーに依存するため、
        この Web サイトを正しく機能させるには、**ProductsServer** アプリケーションを
        ローカルに実行する必要があります。クラウドの Web サイトで要求を実行すると、
        次のスクリーンショットに表示された "GetProducts called" の出力に
        示すように、内部設置型コンソール アプリケーションで
        要求が呼び出されることを確認できます。

        ![][33]

Web サイトとクラウド サービスの違いについては、「[Azure Execution Models (Azure 実行モデル)][]」を参照してください。

## <span class="short-header">アプリケーションの削除</span>アプリケーションの停止と削除

アプリケーションのデプロイ後に、無料サーバー時間である
750 時間/月 (31 日/月) の範囲内で他のアプリケーションを
作成してデプロイするには、そのアプリケーションを無効にする必要があります。

Azure では、消費されたサーバー時間の 1 時間単位の
料金が Web ロール インスタンスに課金されます。アプリケーションがデプロイされた直後からサーバー時間が消費されます。
インスタンスが実行されていない場合や、停止状態の場合にも消費されます。
無料アカウントには、専用仮想マシンで Web ロール インスタンスを
ホストするサーバー時間として 1 か月あたり 750 時間 (1 か月は 31 日換算) が含まれています。

次の手順では、アプリケーションの停止と削除の方法を
示します。

1.  [Azure の管理ポータル][Azure 管理ポータル]にログインし、
    [クラウド サービス] をクリックして、クラウド サービス名をクリックします。

2.  **[ダッシュボード]** タブをクリックし、**[停止]** をクリックすると、アプリケーションが一時的に停止されます。[開始] を
    クリックするだけで、再度開始することができます。**[削除]** をクリックすると、アプリケーションが Azure から完全に削除されます。
    復元することはできません。

    ![][34]

## <a name="nextsteps"></a><span class="short-header">次のステップ</span>次のステップ

サービス バスの詳細については、次のリソースを参照してください。

-   [Azure のサービス バス][]
-   [サービス バスの利用方法に関するページ][]
-   [サービス バス キューの使用方法][]

  [create-account-note]: ../includes/create-account-note.md
  []: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hybrid.png
  [1]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/App2.png
  [ツールと SDK の入手]: http://go.microsoft.com/fwlink/?LinkId=271920
  [2]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-41.png
  [3]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-3.png
  [4]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-4-2-WebPI.png
  [Azure 管理ポータル]: http://manage.windowsazure.com
  [5]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/sb-queues-03.png
  [6]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/sb-queues-04.png
  [7]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-27.png
  [8]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/sb-queues-09.png
  [9]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/sb-queues-06.png
  [Azure Tools for Visual Studio の概要]: http://http://msdn.microsoft.com/en-us/library/windowsazure/ff687127.aspx
  [10]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/VSProperties.png
  [NuGet Service Bus パッケージの使用]: http://go.microsoft.com/fwlink/?LinkId=234589
  [11]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-1.png
  [12]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-con-1.png
  [13]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-con-3.png
  [NuGet]: http://nuget.org
  [[Install NuGet]]: http://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c
  [14]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-13.png
  [15]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-con-4.png
  [16]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-2.png
  [17]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-4.png
  [18]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-7.jpg
  [19]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-10.jpg
  [20]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-40.png
  [21]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-11.png
  [22]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/App1.png
  [Azure Execution Models (Azure 実行モデル)]: http://www.windowsazure.com/en-us/develop/net/fundamentals/compute/
  [Deploying an ASP.NET Web Application to an Azure Website (ASP.NET Web アプリケーションを Azure の Web サイトに展開する)]: http://www.windowsazure.com/en-us/develop/net/tutorials/get-started/
  [23]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-21.png
  [24]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-22.png
  [25]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-12.png
  [26]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-13.png
  [27]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-14.png
  [28]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-33.png
  [29]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-38.png
  [30]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-39.png
  [31]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-40.png
  [32]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-41.png
  [33]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-service1.png
  [34]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-43.png
  [Azure のサービス バス]: http://msdn.microsoft.com/en-us/library/windowsazure/ee732537.aspx
  [サービス バスの利用方法に関するページ]: /ja-jp/documentation/services/service-bus/
  [サービス バス キューの使用方法]: /en-us/develop/net/how-to-guides/service-bus-queues/
