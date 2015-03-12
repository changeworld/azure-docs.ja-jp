<properties 
	pageTitle=".NET 多層アプリケーション - Azure チュートリアル" 
	description="Service Bus キューを使用して層間で通信する多層アプリケーションを Azure で開発するのに役立つチュートリアルです。.NET でのサンプル。" 
	services="cloud-services, service-bus" 
	documentationCenter=".net" 
	authors="sethmanheim" 
	manager="timlt" 
	editor="mattshel"/>

<tags 
	ms.service="service-bus" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/10/2015" 
	ms.author="sethm"/>





# Service Bus キューを使用する .NET 多層アプリケーション

Azure の開発は、Visual Studio 2013 および無料の  
Azure SDK for .NET を使用すれば簡単に行えます。Visual Studio
2013 を所有していない場合、SDK により Visual Studio Express 2013 が自動的にインストールされるため、無料で Azure 向けの開発を
開始できます。このガイドは、Windows Azure の使用経験がないことを前提としています。
 Azure。このガイドを最後まで読むと、ローカル環境で実行され、
多階層アプリケーションのしくみを実証する、
複数の Azure リソースを使用するアプリケーションを実行できます。

学習内容:

-   1 回のダウンロードとインストールで、コンピューターを
    Azure 開発用にする方法
-   Azure 開発のための Visual Studio の使用方法
-   Web ロールおよび Worker ロールを使用して
    多階層アプリケーションを作成する方法
-   Service Bus キューを使用して各層間で通信する方法

[WACOM.INCLUDE [アカウント作成メモ](../includes/create-account-note.md)]

このチュートリアルでは、Azure のクラウド サービスで多層アプリケーションを構築して実行します。フロントエンドは ASP.NET MVC Web ロール、バックエンドは Worker ロールになります。同じ多層アプリケーションを作成するときに、クラウド サービスではなく Azure Web サイトに展開される Web プロジェクトとしてフロントエンドを実装することもできます。Azure Websites のフロントエンドの場合の手順については、「[次のステップ](#nextsteps)」を参照してください。

完成したアプリケーションのスクリーンショットは次のようになります。

![][0]

**注:** Azure にもストレージ キュー機能が用意されています。Azure のストレージ キューと Service Bus キューの詳細については、「[Azure キューと Azure Service Bus キューの比較][sbqueuecomparison]」を参照してください。  

<h2>シナリオの概要:ロール間通信</h2>

注文を送信して処理するには、Web ロールで実行されているフロントエンド UI コンポーネントと、
Worker ロールで実行する中間層のロジックが
対話する必要があります。この例では、Service Bus で仲介されたメッセージングを
層間の通信に使用します。

Web 層と中間層との間で仲介されたメッセージングを使用すると、
2 つのコンポーネントの結合を解除できます。直接メッセージング (TCP または HTTP) とは異なり、
Web 層と中間層は直接接続されません。代わりに
作業単位が Service Bus にメッセージとしてプッシュされます。これにより、
中間層で作業単位を使用および処理する準備ができるまで確実に保持されます。

Service Bus には、キューとトピックという 2 つのエンティティがあり、
仲介されたメッセージングをサポートします。キューの場合、キューに送信される各メッセージが
単一の受信者によって使用されます。トピックはパブリッシュ/サブスクライブ
のパターンをサポートします。これにより、公開された各メッセージが
トピックに登録された各サブスクリプションで使用可能になります。サブスクリプションごとに論理的に
固有のメッセージ キューが保持されます。また、サブスクリプションを
フィルタの規則により構成することもできます。この規則により、サブスクリプション キューに渡されるメッセージのセットが
フィルターと一致するものに制限されます。この例では
Service Bus キューを使用します。

![][1]

この通信メカニズムには、直接メッセージングと比較した場合に、
次のようないくつかの利点があります。

-   **一時的な結合の解除:** 非同期メッセージング パターンの場合、
    プロデューサーとコンシューマーを同時にオンラインにする必要がありません。Service
    コンシューマー側の受信準備が完了するまで、確実に Bus にメッセージを
    格納します。これにより、分散型アプリケーションのコンポーネントを
    メンテナンスやコンポーネント クラッシュの場合でも
    システム全体に影響を与えずに
    自動的に切断できます。さらに、コンシューマー側アプリケーションを
    1 日の特定の時間にのみオンラインにすることもできます。

-   **負荷平準化**: 多くのアプリケーションでは、システム負荷が時間の
    経過とともに変化しますが、各作業単位に必要な処理時間は
    通常一定に保たれます。メッセージ プロデューサーとメッセージ コンシューマーの間を
    キューで仲介することは、コンシューマー側アプリケーション (ワーカー) のみを
    プロビジョニングすれば、ピーク時ではなく平均時の負荷に対応できることを
    意味します。キューの深さは、受信する負荷の変化に合わせて
    増減します。このため、アプリケーション負荷への対応に必要な
    インフラストラクチャの観点から直接費用を節約できます。

-   **負荷分散:** 負荷の増大に合わせて、キューからの読み取りのために
    Worker プロセスをさらに追加できます。各メッセージは、1 つの Worker プロセスのみで
    処理されます。また、このプルベースの負荷分散では、
    Worker マシンの使用率を最適化できます。
    個々の Worker マシンが独自の最大速度でメッセージをプルするため、
    Worker マシンの処理能力が異なっていても可能です。多くの場合、このパターンを
    競合コンシューマー パターンといいます。

    ![][2]

以降のセクションでは、このアーキテクチャを実装するコードについて
説明します。

<h2>開発環境を設定する</h2>

Azure アプリケーションを開発する前に、ツールを入手して
開発環境をセットアップする必要があります。

1.  Azure SDK for .NET をインストールするには、次のボタンをクリックします。

    [ツールと SDK の入手][]

2. 	**[SDK のインストール]** をクリックします。

3. 	使用している Visual Studio のバージョンに対応するリンクを選択します。このチュートリアルの手順では、Visual Studio 2013 を使用します。

	![][32]

4. 	インストール ファイルの実行または保存を求めるメッセージが表示されたら、
    **[実行]** をクリックします。

    ![][3]

5.  Web プラットフォーム インストーラーの **[インストール]** をクリックし、インストールの手順を進めます。

    ![][33]

6.  インストールが完了すると、開発に必要なツールがすべて
    そろいます。SDK には、Visual Studio で Windows Azure アプリケーションを
    簡単に開発できるツールが含まれています。Visual Studio が
    インストールされていない場合は、無料の 
    Visual Studio Express for Web をインストールすることも可能です。

<h2>Service Bus の名前空間を設定する</h2>

次の手順では、サービス名前空間を作成し、共有秘密キーを
取得します。サービス名前空間は、Service Bus によって公開される
各アプリケーションの境界を指定します。共有秘密キーは
サービス名前空間が作成された時点で、システムによって自動的に生成
されます。サービス名前空間と共有秘密キーの組み合わせにより、
アプリケーションへのアクセスを認証する Service Bus の資格情報を提供します。
想定しています。

Visual Studio サーバー エクスプローラーを使用しても名前空間と Service Bus メッセージング エンティティを管理できますが、新しい名前空間を作成できるのはポータルでのみです。 

###管理ポータルを使用して名前空間を設定する

1.  [Azure の管理ポータル][]にログインします。

2.  管理ポータルの左のナビゲーション ウィンドウで
    **[Service Bus]** をクリックします。

3.  管理ポータルの下のウィンドウの **[作成]** をクリックします。

    ![][6]

4.  **[新しい名前空間を追加する]** ダイアログで、名前空間の名前を入力します。
    その名前が使用できるかどうかがすぐに自動で確認されます。   
    ![][7]

5.  入力した名前が利用できることを確認できたら、名前空間を
    ホストする国または地域を選択します (
    コンピューター リソースをデプロイするのと同じ国/地域を
    使用してください)。

    重要:アプリケーションをデプロイする予定の国またはリージョンと**同じ国/リージョン**を
    選択してください。そうすることで、最大限のパフォーマンスを引き出すことができます。

6.  チェック マークをクリックします。これで、システムによってサービス名前空間が
    作成され有効になります。システムでアカウントのリソースがプロビジョニングされるまで
    数分かかる場合があります。

	![][27]

7.  メイン ウィンドウで、使用するサービス名前空間の名前をクリックします。

	![][30]

8. **[接続情報]** をクリックします。

	![][31]

9.  **[接続情報へのアクセス]** ウィンドウで、**[既定の発行者]** と **[既定のキー]** の値を探します。

10.  キーを書き留めておくか、クリップボードにコピーしておいてください。

###Visual Studio サーバー エクスプローラーを使用して名前空間とメッセージング エンティティを管理する

管理ポータルの代わりに Visual Studio を使用して名前空間を管理し、接続情報を取得するには、「[Azure Tools for Visual Studio の概要](http://http://msdn.microsoft.com/library/windowsazure/ff687127.aspx)」の「**Visual Studio から Azure に接続するには**」で説明されている手順に従ってください。Azure にサインインすると、サーバー エクスプローラーの **[Microsoft Azure]** ツリーの下にある **[サービス バス]** ノードが、既に作成したすべての名前空間を使用して自動的に設定されます。いずれかの名前空間を右クリックし、**[プロパティ]** をクリックします。Visual Studio の **[プロパティ]** ウィンドウで表示される、この名前空間に関連付けられている接続文字列と他のメタデータを確認できます。 

**SharedAccessKey** の値を書き留めておくか、クリップボードにコピーしておいてください。

![][34]

<h2>Web ロールを作成する</h2>

このセクションでは、アプリケーションのフロンドエンドを作成します。まず
アプリケーションで表示するさまざまなページを作成します。
その後、Service Bus のキューに項目を送信し、
キューに関するステータス情報を送信するコードを追加します。

### プロジェクトを作成する

1.  管理者特権を使用して、Microsoft Visual Studio 
    2013 または Microsoft Visual Studio Express を起動します。管理者特権で Visual Studio を開始するには、
    **[Microsoft Visual
    Studio 2013 (または Microsoft Visual Studio Express)]** を右クリックし、
    **[管理者として実行]** をクリックします。Azure コンピューティング エミュレーター
    (このガイドで後述します) では、Visual Studio を
    管理者特権で起動する必要があります。

    Visual Studio で、**[ファイル]** メニューの **[新規作成]** をクリックし、
    **[プロジェクト]** をクリックします。

    ![][8]


2.  **[インストールされたテンプレート]** で **[Visual C#]** をクリックし、**[クラウド]** をクリックします。
    次に **[Azure Cloud Service]** をクリックします。プロジェクト名を
「    **Multitierapp**」にします。次に、**[OK]** をクリックします。

    ![][9]

3.  **.NET Framework 4.5** ロールで **[ASP.NET Web ロール] を
    ダブルクリックします**。

    ![][10]

4.  Azure Cloud Service の **WebRole1** に**マウスのカーソルを合わせて**、
    鉛筆のアイコンをクリックし、**FrontendWebRole** の Web ロール名を変更します。次に、**[OK]** をクリックします (「FrontEnd」ではなく「Frontend」と入力してください。小文字の "e" です)。

    ![][11]

5.  **[新しい ASP.NET プロジェクト]** ダイアログ ボックスの **[テンプレートの選択]** ボックスの一覧で、**[MVC]** をクリックし、
    次に **[OK]** をクリックします。

    ![][12]

6.   **ソリューション エクスプローラー**で **[参照設定]** を右クリックし、
    **[NuGet パッケージの管理]** または **[ライブラリ パッケージ参照の追加]** をクリックします。

7.  ダイアログの左側の **[オンライン]** をクリックします。
    "**WindowsAzure**" を検索し、**Azure Service
    Bus** の項目を選択します。次に、インストールを完了し、このダイアログを閉じます。

    ![][13]

8.  これで、必要なクライアント アセンブリを参照できるようになり、
    新しいコード ファイルが追加されました。

9.  **ソリューション エクスプローラー**で **[Models]** を右クリックし、**[追加]**、
    **[クラス]** の順にクリックします。[名前] ボックスに名前として
    「**OnlineOrder.cs」**と入力します。次に **[追加]** をクリックします。

### Web ロール用のコードの作成

このセクションでは、アプリケーションで表示するさまざまなページを
作成します。

1.  Visual Studio の **OnlineOrder.cs** ファイルで
    既存の名前空間の定義を次のコードに置き換えます。

        namespace FrontendWebRole.Models
        {
            public class OnlineOrder
            {
                public string Customer { get; set; }
                public string Product { get; set; }
            }
        }

2.  **ソリューション エクスプローラー**で
    **Controllers \homecontroller.cs** をダブルクリックします。次の **using** ステートメントを
    名前空間を含めるファイルの先頭に追加し、Service Bus と同様に
    先ほど作成したモデルの名前空間を追加します。

        using FrontendWebRole.Models;
        using Microsoft.ServiceBus.Messaging;
        using Microsoft.ServiceBus;

3.  また、Visual Studio の **HomeController.cs** ファイルで
    既存の名前空間の定義を次のコードに置き換えます。このコードには、
    キューへの項目の送信を処理するメソッドが含まれています。

        namespace FrontendWebRole.Controllers
        {
            public class HomeController : Controller
            {
                public ActionResult Index()
                {
                    // Simply redirect to Submit, since Submit will serve as the
                    // front page of this application
                    return RedirectToAction("Submit");
                }

                public ActionResult About()
                {
                    return View();
                }

                // GET: /Home/Submit
                // Controller method for a view you will create for the submission
                // form
                public ActionResult Submit()
                {
                    // Will put code for displaying queue message count here.

                    return View();
                }

                // POST: /Home/Submit
                // Controller method for handling submissions from the submission
                // form 
                [HttpPost]
				// Attribute to help prevent cross-site scripting attacks and 
				// cross-site request forgery  
    			[ValidateAntiForgeryToken] 
                public ActionResult Submit(OnlineOrder order)
                {
                    if (ModelState.IsValid)
                    {
                        // Will put code for submitting to queue here.
                    
                        return RedirectToAction("Submit");
                    }
                    else
                    {
                        return View(order);
                    }
                }
            }
        }

4.  **[ビルド]** メニューの **[ソリューションのビルド]** をクリックします。

5.  次に、前の手順で作成した **Submit()** メソッドのビューを
    作成します。Submit() メソッド内で右クリックし、
    **[ビューの追加] を選択します。**

    ![][14]

6.  ビューを作成するためのダイアログが表示されます。
    **[モデル クラス]** ドロップダウン リストから **OnlineOrder** を選択し、
    **[テンプレート]** ドロップダウン リストから **[作成]** を選択します。

    ![][15]

7.  **[追加]**  をクリックします。

8.  次に、アプリケーションの表示名を変更します。
    **ソリューション エクスプローラー**で、
    **Views\Shared\\_Layout.cshtml** ファイルをダブルクリックし、Visual
    Studio エディターで開きます。

9.  **My ASP.NET MVC Application** となっている箇所をすべて
    **LITWARE's Awesome Products** に置き換えます。

10.	「**your logo here**」 を **LITWARE'S Awesome Products** に置き換えます。

	![][16]

11. **[Home]**、**[About]**、**[Contact]** の各リンクを削除します。以下の強調表示されたコードを削除します。

	![][28]
  

12. 最後に、キューに関する情報を表示できるように、送信ページを
    変更します。**ソリューション エクスプローラー**で、
    **Views\Home\Submit.cshtml** ファイルをダブルクリックし、Visual
    エディターで開きます。**<h2>Submit</h2>** という行の下に、次の行を追加します。ここでは、
    **ViewBag.MessageCount** は空です。この値は後で入力します。

        <p>Current Number of Orders in Queue Waiting to be Processed: @ViewBag.MessageCount</p>
             

13. これで、UI の実装が終わりました。**F5** キーを押してアプリケーションを実行し、
    期待どおりに表示されることを確認します。

    ![][17]

### Service Bus キューに項目を送信するためのコードの作成

次に、項目をキューに送信するためのコードを追加します。最初に、
Service Bus キューの接続情報を含むクラスを
作成します。次に、
**Global.aspx.cs** から接続を初期化します。最後に、
**HomeController.cs** で作成した送信コードを更新し、項目を実際に
Service Bus キューに送信します。

1.  ソリューション エクスプローラーで、**FrontendWebRole** を右クリックします (ロールではなくプロジェクトを右クリック)。**[追加]**、**[クラス]** の順にクリックします。

2.  クラスに **QueueConnector.cs** という名前を付けます。**[追加]** をクリックしてクラスを作成します。

3.  次に、接続情報をカプセル化し、Service Bus キューへの接続を
    初期化するメソッドを含むコードを
    貼り付けます。Queueconnector.cs では、次のコードを貼り付け、
    **Namespace**、**IssuerName**、および **IssuerKey** の値を入力します。これらの値は、
    [管理ポータル][ (Azure Management Portal)]、または Visual Studio サーバー エクスプローラーの **Service Bus** ノードから取得できます。

        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Web;
        using Microsoft.ServiceBus.Messaging;
        using Microsoft.ServiceBus;

        namespace FrontendWebRole
        {
            public static class QueueConnector
            {
                // Thread-safe. Recommended that you cache rather than recreating it
                // on every request.
                public static QueueClient OrdersQueueClient;

                // Obtain these values from the Management Portal
                public const string Namespace = "your service bus namespace";
                public const string IssuerName = "issuer name";
                public const string IssuerKey = "issuer key";

                // The name of your queue
                public const string QueueName = "OrdersQueue";

                public static NamespaceManager CreateNamespaceManager()
                {
                    // Create the namespace manager which gives you access to
                    // management operations
                    var uri = ServiceBusEnvironment.CreateServiceUri(
                        "sb", Namespace, String.Empty);
                    var tP = TokenProvider.CreateSharedSecretTokenProvider(
                        IssuerName, IssuerKey);
                    return new NamespaceManager(uri, tP);
                }

                public static void Initialize()
                {
                    // Using Http to be friendly with outbound firewalls
                    ServiceBusEnvironment.SystemConnectivity.Mode = 
                        ConnectivityMode.Http;

                    // Create the namespace manager which gives you access to 
                    // management operations
                    var namespaceManager = CreateNamespaceManager();

                    // Create the queue if it does not exist already
                    if (!namespaceManager.QueueExists(QueueName))
                    {
                        namespaceManager.CreateQueue(QueueName);
                    }

                    // Get a client to the queue
                    var messagingFactory = MessagingFactory.Create(
                        namespaceManager.Address, 
                        namespaceManager.Settings.TokenProvider);
                    OrdersQueueClient = messagingFactory.CreateQueueClient(
                        "OrdersQueue");
                }
            }
        }

4.  次に、**Initialize** メソッドが呼び出されるようにします。**ソリューション エクスプローラー**で、**Global.asax\Global.asax.cs** をダブルクリックします。

5.  次の行を **Application_Start** メソッドの最終行に追加します。
     から呼び出される新しいメソッドが追加されました。

        FrontendWebRole.QueueConnector.Initialize()。

6.  最後に、前の手順で作成した Web コードを更新し、 
    アイテムをキューに送信します。**ソリューション エクスプローラ**で、
    前に作成した **Controllers\HomeController.cs** を
    ダブルクリックします。

7.  **Submit()** メソッドを次のように更新し、
    キューのメッセージ数を取得できるようにします。

        public ActionResult Submit()
        {            
            // Get a NamespaceManager which allows you to perform management and
            // diagnostic operations on your Service Bus Queues.
            var namespaceManager = QueueConnector.CreateNamespaceManager();

            // Get the queue, and obtain the message count.
            var queue = namespaceManager.GetQueue(QueueConnector.QueueName);
            ViewBag.MessageCount = queue.MessageCount;

            return View();
        }

8.  次のように **Submit (OnlineOrder order)** メソッドを更新し、
    キューに注文情報を送信します。

        public ActionResult Submit(OnlineOrder order)
        {
            if (ModelState.IsValid)
            {
                // Create a message from the order
                var message = new BrokeredMessage(order);
                
                // Submit the order
                QueueConnector.OrdersQueueClient.Send(message);
                return RedirectToAction("Submit");
            }
            else
            {
                return View(order);
            }
        }

9.  ここで再び、アプリケーションを実行します。注文を送信するたびに、
    メッセージの数が増加します。

    ![][18]

<h2>クラウド構成マネージャー</h2>

Azure は、Service Bus など、Azure サービス クライアントの新しいインスタンスを Microsoft のクラウド サービス全体で一貫した方法で作成できる、一連のマネージ API をサポートしています。アプリケーションがホストされている場所 (内部設置型、Microsoft クラウド サービス、Web サイト、永続 VM ロールなど) に関係なく、この API を通じて、これらのクライアント (**CloudBlobClient**、**QueueClient**、**TopicClient** など) をインスタンス化することができます。また、API を使用することで、こうしたクライアントをインスタンス化するために必要な構成情報を取得でき、また、構成に変更を加える際も、呼び出し元のアプリケーションを展開し直さずに済みます。この API は、[Microsoft.WindowsAzure.Configuration.CloudConfigurationManager][] クラスに存在します。API はクライアント側にも存在します。

### Connection string

クライアント (たとえば、Service Bus の **QueueClient**) をインスタンス化するための構成情報は、接続文字列として表すことができます。その接続文字列を使用してクライアントの型をインスタンス化する **CreateFromConnectionString()** メソッドがクライアント側には用意されています。たとえば、次の構成セクションがあるとします。

	<ConfigurationSettings>
    ...
    	<Setting name="Microsoft.ServiceBus.ConnectionString" value="Endpoint=sb://[yourServiceNamespace].servicebus.windows.net/;SharedSecretIssuer=[issuerName];SharedSecretValue=[yourDefaultKey]" />
	</ConfigurationSettings>

次のコードでは、接続文字列を取得し、キューを作成し、キューへの接続を初期化します。

	QueueClient Client; 

	string connectionString =
     CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");
	
    var namespaceManager =
     NamespaceManager.CreateFromConnectionString(connectionString); 

	if (!namespaceManager.QueueExists(QueueName))
    {
        namespaceManager.CreateQueue(QueueName);
    }

	// Initialize the connection to Service Bus Queue
	Client = QueueClient.CreateFromConnectionString(connectionString, QueueName);

次のセクションのコードでは、これらの構成管理 API を使用しています。

<h2>Worker ロールを作成する</h2>

次に、送信された注文を処理する Worker ロールを
作成します。この例では、Visual Studio プロジェクト テンプレートの **Worker Role with Service Bus Queue** を使用します。まず、Visual Studio のサーバー エクスプローラーを使用して必要な資格情報を取得します。

1. 「**Visual Studio サーバー エクスプローラーを使用して名前空間を設定する**」の手順に従って Visual Studio を Azure のアカウントに既に接続している場合は、手順 5 に進んでください。 

3. Visual Studio のメニュー バーで **[表示]** を選択し、**[サーバー エクスプローラー]** をクリックします。サーバー エクスプローラーの階層内で **[Azure]** に **[Service Bus]** ノードが表示されます (次の図を参照)。

	![][21]

2. サーバー エクスプローラーで **[Azure]** を展開し、**[Service Bus]** を右クリックしてから、**[新しい接続の追加]** をクリックします。

3. **[接続の追加]** ダイアログで、サービスの名前空間名、発行者名、および発行者キーを入力します。次に **[OK]** をクリックして接続します。

	![][22]

4.  Visual Studio の**ソリューション エクスプ ローラー**で、
    **MultiTierApp** プロジェクトにある **Roles** フォルダーをダブルクリックします。

5.  **[追加]** をクリックし、**[新しい Worker ロール プロジェクト]** をクリックします。**[新しいロール プロジェクトの追加]** ダイアログ ボックスが表示されます。

	![][26]

6.  **[新しいロール プロジェクトの追加]** ダイアログ ボックスの **[Worker ロールと Service Bus キュー]** をクリックします (次の図を参照)。

	![][23]

7.  **[名前]** ボックスに、プロジェクト名として「**OrderProcessingRole**」を入力します。次に **[追加]** をクリックします。

8.  サーバー エクスプローラーで、サービスの名前空間名を右クリックし、**[プロパティ]** をクリックします。Visual Studio の **[プロパティ]** ウィンドウの 1 つ目の項目には、接続文字列が表示され、必要な承認の資格情報を含んだサービスの名前空間のエンドポイントが設定されています。たとえば、次の図を見てください。**[ConnectionString]** をダブルクリックし、**Ctrl + C** を押すと、この文字列がクリップボードにコピーされます。

	![][24]

9.  ソリューション エクスプローラーで、手順 7. で作成した **[OrderProcessingRole]** を右クリックします (右クリックするのは、**[ロール]** の **[OrderProcessingRole]** です。クラスではありません)。次に **[プロパティ]** をクリックします。

10.  **[プロパティ]** ダイアログ ボックスの **[設定]** タブで、**[Microsoft.ServiceBus.ConnectionString]** の **[値]** ボックス内をクリックし、手順 8 でコピーしたエンドポイントの値を貼り付けます。

	![][25]

11.  キューからの注文を処理するときの注文を表す **OnlineOrder** クラスを作成します。作成済みのクラスを再利用することができます。ソリューション エクスプローラーで、**[OrderProcessingRole]** プロジェクトを右クリックします (ロールではなくプロジェクトを右クリック)。**[追加]**、**[既存の項目]** の順にクリックします。

12. **FrontendWebRole\Models** のサブフォルダーに移動し、**[OnlineOrder.cs]** をダブルクリックして、このプロジェクトに追加します。

13. **WorkerRole.cs** 内の **QueueName** 変数の値を `"ProcessingQueue"` から `"OrdersQueue"` に変更します (次のコードを参照)。

		// The name of your queue
		const string QueueName = "OrdersQueue";

14. WorkerRole.cs ファイルの先頭に次の using ステートメントを追加します。

		using FrontendWebRole.Models;

15. `Run()` 関数の `OnMessage` の呼び出し内で、`try` 句に次のコードを追加します。

		Trace.WriteLine("Processing", receivedMessage.SequenceNumber.ToString());
		// View the message as an OnlineOrder
		OnlineOrder order = receivedMessage.GetBody<OnlineOrder>();
		Trace.WriteLine(order.Customer + ": " + order.Product, "ProcessingMessage");
		receivedMessage.Complete();

16.  これでアプリケーションが完成しました。前の手順で実行したように 
    F5 キーを押してアプリケーション全体をテストします。メッセージ数が増えないことに注意してください。これは、Worker ロールがキューの項目を処理し、完了としてマークしているためです。Azure コンピューティング エミュレーター の UI を表示すれば、 
    Worker  ロールのトレース出力を確認できます。そのためには、
    タスク バーの通知領域にあるエミュレーター アイコンを右クリックし、
    **[コンピューティング エミュレーターの UI を表示する]** をクリックします。

    ![][19]

    ![][20]

<h2><a name="nextsteps"></a>次のステップ</h2>  

サービス バスの詳細については、次のリソースを参照してください。  
  
* [Azure Service Bus][sbmsdn]  
* [Service Bus How To's (Service Bus の利用方法)][sbwacom]  
* [How to Use Service Bus Queues (Service Bus キューの利用方法)][sbwacomqhowto]  

多層シナリオの詳細またはクラウド サービスにアプリケーションを展開する方法については、以下を参照してください。  

* [ストレージ テーブル、キュー、BLOB を使用する .NET 多層アプリケーションに関するページ ][mutitierstorage]  

Azure Cloud Service ではなく、Azure Websites に多層アプリケーションのフロントエンドを実装することもできます。Azure Cloud Service と Azure Websites の違いについては、「[Azure Execution Models (Azure 実行モデル)][」を参照してください]。

このチュートリアルで作成したアプリケーションをクラウド サービス Web ロールではなく標準的な Web プロジェクトとして実装するには、次の相違点に注意しつつ、このチュートリアルの手順に従ってください。

1. プロジェクトを作成する際に、**[クラウド]** カテゴリの **[クラウド サービス]** テンプレートではなく、**[Web]** カテゴリの **[ASP.NET MVC 4 Web アプリケーション]** プロジェクト テンプレートを選択します。そのうえで、MVC アプリケーションを作成するときと同じ手順に従います (「**クラウド構成マネージャー**」セクションの手前まで)。

2. Worker ロールを作成する際には、Web ロールのときの手順と同様に、新しい個別のソリューションに作成します。ただし、クラウド サービス プロジェクトに Worker ロールのみを作成します。そのうえで、Worker ロールを作成するときと同じ手順に従います。

3. フロントエンドとバックエンドは個別にテストできるほか、個別の Visual Studio インスタンスで両方を同時にテストすることもできます。

Azure Web サイトにフロンドエンドを展開する方法については、「[Deploying an ASP.NET Web Application to an Azure Website (ASP.NET Web アプリケーションを Azure の Web サイトに展開する)](http://azure.microsoft.com/develop/net/tutorials/get-started/)」を参照してください。Azure のクラウド サービスにバックエンドを展開する方法については、「[ストレージ テーブル、キュー、および BLOB を使用する .NET 多層アプリケーション][mutitierstorage]」を参照してください。


  [0]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-01.png
  [1]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-100.png
  [sbqueuecomparison]: http://msdn.microsoft.com/library/windowsazure/hh767287.aspx
  [2]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-101.png
  [ツールと SDK の入手]: http://go.microsoft.com/fwlink/?LinkId=271920
  [3]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-3.png
  
  
  
  [Azure 管理ポータル]: http://manage.windowsazure.com
  [6]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/sb-queues-03.png
  [7]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/sb-queues-04.png
  [8]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-09.png
  [9]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-10.jpg
  [10]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-11.png
  [11]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-02.png
  [12]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-12.png
  [13]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-13.png
  [14]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-33.png
  [15]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-34.png
  [16]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-35.png
  [17]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-36.png
  [18]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-37.png
  
  [19]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-38.png
  [20]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-39.png
  [21]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/SBExplorer.png
  [22]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/SBExplorerAddConnect.png
  [23]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/SBWorkerRole1.png
  [24]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/SBExplorerProperties.png
  [25]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/SBWorkerRoleProperties.png
  [26]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/SBNewWorkerRole.png
  [27]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-27.png
  [28]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-40.png
  [30]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/sb-queues-09.png
  [31]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/sb-queues-06.png
  [32]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-41.png
  [33]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-4-2-WebPI.png
  [34]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/VSProperties.png
  [sbmsdn]: http://msdn.microsoft.com/library/windowsazure/ee732537.aspx  
  [sbwacom]: /ja-jp/documentation/services/service-bus/  
  [sbwacomqhowto]: /ja-jp/develop/net/how-to-guides/service-bus-queues/  
  [mutitierstorage]: /ja-jp/develop/net/tutorials/multi-tier-web-site/1-overview/ 
  [executionmodels]: http://azure.microsoft.com/develop/net/fundamentals/compute/

<!--HONumber=46--> 
