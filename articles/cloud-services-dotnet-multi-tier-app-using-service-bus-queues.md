<properties linkid="dev-net-e2e-multi-tier" urlDisplayName="Multi-Tier Application" pageTitle=".NET Multi-Tier Application - Azure Tutorial" metaKeywords="Azure Service Bus queue tutorial, Azure queue tutorial, Azure worker role tutorial, Azure .NET queue tutorial, Azure C# queue tutorial, Azure C# worker role tutorial" description="A tutorial that helps you develop a multi-tier app in Azure that uses Service Bus queues to communicate between tiers. Samples in .NET." metaCanonical="" services="cloud-services,service-bus" documentationCenter=".NET" title=".NET Multi-Tier Application Using Service Bus Queues" authors="sethm" solutions="" manager="timlt" editor="mattshel" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="09/15/2014" ms.author="sethm"></tags>

# Service Bus キューを使用する .NET 多層アプリケーション

Azure 向けアプリケーションは、Visual Studio 2013 および無料の Azure SDK for .NET を使用して簡単に開発できます。 Visual Studio 2013 を所有していない場合でも、SDK をインストールすると自動的に Visual Studio Express 2013 がインストールされるため、Azure 向けの開発を完全に無料で始めることができます。 このガイドは、Windows Azure を初めて使用するユーザーを対象としています。このガイドを最後まで読むと、ローカル環境で実行され、多層アプリケーションの動作のしくみを理解できる、複数の Azure リソースを使用するアプリケーションを作成できます。

学習内容:

-   1 回のダウンロードとインストールで、Azure 開発用にコンピューターを準備する方法
-   Azure 開発のための Visual Studio の使用方法
-   Web ロールおよび Worker ロールを使用して Azure に多層アプリケーションを作成する方法
-   Service Bus キューを使用して各層間で通信する方法

[WACOM.INCLUDE [create-account-note][create-account-note]]

このチュートリアルでは、Azure のクラウド サービスで多層アプリケーションを構築して実行します。フロントエンドは ASP.NET MVC Web ロール、バックエンドは Worker ロールになります。同じ多層アプリケーションを作成するときに、クラウド サービスではなく Azure Web サイトに展開される Web プロジェクトとしてフロントエンドを実装することもできます。Azure Web サイトのフロントエンドの場合の手順については、「[次のステップ][次のステップ]」を参照してください。

完成したアプリケーションのスクリーンショットは次のようになります。

![][]

**注:** Azure にもストレージ キュー機能が用意されています。Azure のストレージ キューと Service Bus キューの詳細については、「[Azure キューと Azure Service Bus キューの比較][Azure キューと Azure Service Bus キューの比較]」を参照してください。

## <span class="short-header">ロール間通信</span>シナリオの概要: ロール間通信

処理を要求するため、Web ロールで実行されているフロントエンド UI コンポーネントは、Worker ロールで実行されている中間層ロジックと対話する必要があります。この例では、各層間での通信にサービス バスで仲介されたメッセージングが使用されています。

Web 層と中間層との間で仲介されたメッセージングを使用すると、2 つのコンポーネントの結合が解除されます。直接メッセージング (TCP または HTTP) とは異なり、Web 層は中間層に直接接続しません。その代わりに、作業をメッセージとしてサービス バスにプッシュします。サービス バスは、このメッセージを中間層が受け取り、処理する用意ができるまで、メッセージを確実に保持します。

サービス バスには、仲介されたメッセージングをサポートする、キューとトピックという 2 つのエンティティがあります。キューでは、各メッセージは単一のレシーバーが使用するキューに送信されます。トピックはパブリッシュ/サブスクライブ パターンをサポートし、パブリッシュされた各メッセージをトピックに登録されている各サブスクリプションで使用します。各サブスクリプションは、独自のメッセージ キューを論理的に管理しています。また、サブスクリプションにフィルター ルールを構成し、サブスクリプション キューに渡すメッセージをフィルターに一致するメッセージのみに制限することができます。この例では、Service Bus キューを使用します。

![][1]

この通信メカニズムには、直接メッセージングと比較した場合に、次のような利点があります。

-   **一時的な結合の解除:**非同期メッセージング パターンでは、プロデューサーとコンシューマーが同時にオンラインになっている必要はありません。コンシューマーがメッセージを受け取る用意ができるまで、サービス バスが確実にメッセージを保持します。このため、保守などのために、分散型アプリケーションの各コンポーネントの接続を自主的に解除できます。また、コンポーネントのクラッシュによって接続が解除されても問題なく、接続解除の際にシステム全体に影響が及ぶことがありません。さらに、コンシューマー側アプリケーションがオンラインになっている時間は、1 日のうち一定の時間だけで済みます。

-   **負荷平準化**: 多くのアプリケーションでは、システム負荷が時間の経過と共に変化する一方で、一定量の作業に必要な処理時間は一般的に一定です。メッセージ プロデューサーとメッセージ コンシューマーの間をキューが仲介することによって、コンシューマー側アプリケーション (ワーカー) のプロビジョニングでは平均負荷に対応すればよく、ピーク時の負荷に対応する必要はありません。キューの深さは、受信する負荷の変化に合わせて増減します。このため、アプリケーションの負荷に対応するために必要なインフラストラクチャの量の面で、料金を抑えることができます。

-   **負荷分散:**負荷の増大に合わせて、キューからの読み取りのためにワーカー プロセスを追加できます。各メッセージは、ワーカー プロセスの中の 1 つのプロセスによって処理されます。また、このプルベースの負荷分散では、各ワーカー マシンがそれぞれ独自の最大レートでメッセージをプルするため、ワーカー マシンの処理能力が異なる場合であっても使用率を最適化できます。このパターンは、競合コンシューマー パターンと呼ばれることもあります。

    ![][2]

以降のセクションでは、このアーキテクチャを実装するコードについて説明します。

## <span class="short-header">環境の設定</span>開発環境の設定

Azure アプリケーションを開発する前に、ツールを入手して、開発環境を設定する必要があります。

1.  Azure SDK for .NET をインストールするには、次のボタンをクリックします。

    [ツールと SDK の入手][ツールと SDK の入手]

2.  **[SDK のインストール]** をクリックします。

3.  使用している Visual Studio のバージョンに対応するリンクを選択します。このチュートリアルの手順では、Visual Studio 2013 を使用します。

    ![][3]

4.  インストール ファイルの実行または保存を求める メッセージが表示されたら、**[実行]** をクリックします。

    ![][4]

5.  Web Platform Installer の **[インストール]** をクリックし、インストールの手順を進めます。

    ![][5]

6.  インストールが完了すると、開発に必要なツールがすべて揃います。SDK には、Visual Studio で Azure アプリケーションを簡単に開発するためのツールが用意されています。Visual Studio がインストールされていない場合、無料の Visual Studio Express for Web もインストールされます。

## <span class="short-header">名前空間の設定</span>サービス バス名前空間の設定

次のステップでは、サービス名前空間を作成し、共有秘密キーを取得します。サービス名前空間は、サービス バスによって公開される各アプリケーションのアプリケーション境界を提供します。共有秘密キーは、サービス名前空間が作成されるときにシステムによって自動的に生成されます。サービス名前空間と共有秘密キーの組み合わせが、アプリケーションへのアクセスをサービス バスが認証するための資格情報になります。

Visual Studio サーバー エクスプローラーを使用しても名前空間と Service Bus メッセージング エンティティを管理できますが、新しい名前空間を作成できるのはポータルでのみです。

### 管理ポータルを使用して名前空間を設定する

1.  [Azure の管理ポータル][Azure の管理ポータル]にログインします。

2.  管理ポータルの左のナビゲーション ウィンドウで、**[サービス バス]** をクリックします。

3.  管理ポータルの下のウィンドウの **[作成]** をクリックします。

    ![][6]

4.  **[新しい名前空間を追加する]** ダイアログで、名前空間の名前を入力します。その名前が使用できるかどうかがすぐに自動で確認されます。
    ![][7]

5.  入力した名前が利用できることを確認できたら、名前空間をホストする国またはリージョンを選択します (コンピューティング リソースのデプロイ先となる国またはリージョンと同じ国またはリージョンを必ず使用してください)。

    重要: アプリケーションをデプロイする予定の国またはリージョンと**同じ国/リージョン**を選択してください。そうすることで、パフォーマンスが最高になります。

6.  チェック マークをクリックします。これで、システムによってサービス名前空間が作成され、有効になります。システムがアカウントのリソースを準備し終わるまでに、数分間かかる場合があります。

    ![][8]

7.  メイン ウィンドウで、使用するサービス名前空間の名前をクリックします。

    ![][9]

8.  **[接続情報]** をクリックします。

    ![][10]

9.  **[接続情報へのアクセス]** ウィンドウで、**[既定の発行者]** と **[既定のキー]** の値を探します。

10. キーを書き留めておくか、クリップボードにコピーしておいてください。

### Visual Studio サーバー エクスプローラーを使用して名前空間とメッセージング エンティティを管理する

管理ポータルの代わりに Visual Studio を使用して名前空間を管理し、接続情報を取得するには、「[Azure Tools for Visual Studio の概要][Azure Tools for Visual Studio の概要]」の「**Visual Studio から Azure に接続するには**」で説明されている手順に従ってください。Azure にサインインすると、サーバー エクスプローラーの **[Microsoft Azure]** ツリーの下にある **[サービス バス]** ノードが、既に作成したすべての名前空間を使用して自動的に設定されます。いずれかの名前空間を右クリックし、**[プロパティ]** をクリックします。Visual Studio の **[プロパティ]** ウィンドウで表示される、この名前空間に関連付けられている接続文字列と他のメタデータを確認できます。

**SharedAccessKey** の値を書き留めておくか、クリップボードにコピーしておいてください。

![][11]

## <span class="short-header">Web ロールの作成</span>Web ロールの作成

このセクションでは、アプリケーションのフロンドエンドを作成します。最初に、アプリケーションで表示するさまざまなページを作成します。
その後、Service Bus キューに項目を送信し、キューに関するステータス情報を表示するコードを追加します。

### プロジェクトを作成する

1.  管理者特権で Microsoft Visual Studio 2013 または Microsoft Visual Studio Express を開始します。
    管理者特権で Visual Studio を開始するには、**[Microsoft Visual Studio 2013 (または Microsoft Visual Studio Express)]** を右クリックし、 **[管理者として実行]** をクリックします。Azure コンピューティング エミュレーター (このガイドで解説) を使用するには、管理者特権で Visual Studio を開始する必要があります。

    Visual Studio で、**[ファイル]** メニューの **[新規作成]** をクリックした後、**[プロジェクト]** をクリックします。

    ![][12]

2.  **[インストールされたテンプレート]** の **[Visual C#]** で **[クラウド]** をクリックし、**[Azure クラウド サービス]** をクリックします。プロジェクトの名前を**"MultiTierApp"** にします。次に、 **[OK]** をクリックします

    ![][13]

3.  **.NET Framework 4.5** ロールの **[ASP.NET Web ロール]** をダブルクリックします。

    ![][14]

4.  **[Azure クラウド サービス ソリューション]** の **[WebRole1]** をポイントし、鉛筆のアイコンをクリックして、Web ロールの名前を **FrontendWebRole** に変更します。次に、**[OK]** をクリックします (「FrontEnd」ではなく「Frontend」と入力してください。小文字の "e" です)。

    ![][15]

5.  **[新しい ASP.NET プロジェクト]** ダイアログ ボックスの **[テンプレートの選択]** ボックスの一覧で、**[MVC]** をクリックし、**[OK]** をクリックします。

    ![][16]

6.  **ソリューション エクスプローラー**で **[参照設定]** を右クリックし、**[NuGet パッケージの管理]** または **[ライブラリ パッケージ参照の追加]** をクリックします。

7.  ダイアログの左側の **[オンライン]** をクリックします。"**WindowsAzure**" を検索し、**[Azure Service Bus]** 項目をクリックします。次に、インストールを完了し、 このダイアログを閉じます。

    ![][17]

8.  これで、必要なクライアント アセンブリを参照できるようになり、新しいコード ファイルがいくつか追加されました。

9.  **ソリューション エクスプローラー**で **[Models]** を右クリックし、**[追加]**、**[クラス]** の順にクリックします。[名前] ボックスに 「**OnlineOrder.cs**」と入力します。**[追加]** をクリックします。

### Web ロール用のコードの作成

このセクションでは、アプリケーションで表示するさまざまなページを作成します。

1.  Visual Studio で、**OnlineOrder.cs** ファイルの既存の名前空間定義を、次のコードに置き換えます。

        namespace FrontendWebRole.Models
        {
            public class OnlineOrder
            {
                public string Customer { get; set; }
                public string Product { get; set; }
            }
        }

2.  **ソリューション エクスプローラー**で、**Controllers\\HomeController.cs** をダブルクリックします。先ほど作成したモデルの名前空間と Service Bus をインクルードするため、ファイルの先頭に次の **using** ステートメントを追加します。

        using FrontendWebRole.Models;
        using Microsoft.ServiceBus.Messaging;
        using Microsoft.ServiceBus;

3.  次に、Visual Studio で、**HomeController.cs** ファイルの既存の名前空間定義を、次のコードに書き換えます。 このコードには、キューへの項目の送信を処理するメソッドが含まれています。

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

5.  次に、前の手順で作成した **Submit()** メソッドのビューを作成します。Submit() メソッド内で右クリックし、**[ビューの追加]** を選択します。

    ![][18]

6.  ビューを作成するためのダイアログが表示されます。**[モデル クラス]** ボックスの一覧の **[OnlineOrder]** をクリックし、**[テンプレート]** ボックスの一覧の **[作成]** をクリックします。

    ![][19]

7.  **[追加]** をクリックします。

8.  次に、アプリケーションの表示名を変更します。**ソリューション エクスプローラー**で \*\*Views\\Shared\\\_Layout.cshtml\*\* ファイルをダブルクリックし、Visual Studio エディターで開きます。

9.  **My ASP.NET MVC Application** となっている箇所をすべて **LITWARE'S Awesome Products** に置き換えます。

10. **"your logo here"** を **LITWARE'S Awesome Products** に置き換えます。

    ![][20]

11. **Home**、**About**、および **Contact** の各リンクを削除します。以下の強調表示されたコードを削除してください。

    ![][21]

12. 最後に、キューに関する情報を表示できるように、送信ページを修正します。**ソリューション エクスプローラー**で、**Views\\Home\\Submit.cshtml** ファイルをダブルクリックし、Visual Studio エディターで開きます。**\<h2\>Submit\</h2\>** の下に、次の行を追加します。この時点では、**ViewBag.MessageCount** は空の状態です。この値は後で入力します。

        <p>Current Number of Orders in Queue Waiting to be Processed: @ViewBag.MessageCount</p>

13. これで、UI の実装が終わりました。**F5** キーを押してアプリケーションを実行し、想定どおりに表示されることを確認します。

    ![][22]

### Service Bus キューに項目を送信するためのコードの作成

次に、項目をキューに送信するためのコードを追加します。最初に、Service Bus キューの接続情報を含むクラスを作成します。次に、**Global.aspx.cs** から接続を初期化します。最後に、Service Bus キューに実際に項目を送信できるように、**HomeController.cs** 内に作成してある送信用のコードを更新します。

1.  ソリューション エクスプローラーで、**FrontendWebRole** を右クリックします (ロールではなくプロジェクトを右クリック)。**[追加]**、**[クラス]** の順にクリックします。

2.  クラスに **QueueConnector.cs** という名前を付けます。**[追加]** をクリックしてクラスを作成します。

3.  次に、接続情報をカプセル化し、Service Bus キューへの接続を初期化するメソッドを含むコードを貼り付けます。QueueConnector.cs に次のコードを貼り付け、**Namespace**、**IssuerName**、**IssuerKey** の値を入力します。[管理ポータル][Azure の管理ポータル]または Visual Studio サーバー エクスプローラーの **Service Bus** ノードから、これらの値を取得できます。

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

4.  次に、**Initialize** メソッドが呼び出されるようにします。**ソリューション エクスプローラー**で、**Global.asax\\Global.asax.cs** をダブルクリックします。

5.  次の行を **Application\_Start**メソッドの最終行に追加します。

        FrontendWebRole.QueueConnector.Initialize();

6.  最後に、項目をキューに送信するため、前の手順で作成した Web コードを更新します。前の手順で作成した **Controllers\\HomeController.cs** を**ソリューション エクスプローラー**でダブルクリックします。

7.  **Submit()** メソッドを次のように更新し、キューのメッセージ数を取得できるようにします。

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

8.  **Submit(OnlineOrder order)** メソッドを次のように更新し、キューに注文情報を送信できるようにします。

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

9.  ここで再び、アプリケーションを実行します。注文を送信するたびに、メッセージ数が増えていきます。

    ![][23]

## <span class="short-header">構成マネージャー</span>クラウド構成マネージャー

Azure は、Service Bus など、Azure サービス クライアントの新しいインスタンスを Microsoft のクラウド サービス全体で一貫した方法で作成できる一連のマネージ API をサポートしています。アプリケーションがホストされている場所 (内部設置型、Microsoft クラウド サービス、Web サイト、永続 VM ロールなど) に関係なく、この API を通じて、これらのクライアント (**CloudBlobClient**、**QueueClient**、**TopicClient** など) をインスタンス化することができます。また、API を使用することで、こうしたクライアントをインスタンス化するために必要な構成情報を取得でき、また、構成に変更を加える際も、呼び出し元のアプリケーションを展開し直さずに済みます。この API は [Microsoft.WindowsAzure.Configuration.CloudConfigurationManager][] クラスに存在します。API はクライアント側にも存在します。

### Connection string

クライアント (たとえば、**Service Bus の QueueClient**) をインスタンス化するための構成情報は、接続文字列として表すことができます。その接続文字列を使用してクライアントの型をインスタンス化する **CreateFromConnectionString()** メソッドがクライアント側には用意されています。たとえば、次の構成セクションがあるとします。

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

## <span class="short-header">ワーカー ロールの作成</span>ワーカー ロールの作成

次に、送信された注文を処理する Worker ロールを作成します。
 この例では、Visual Studio プロジェクト テンプレートの **Worker Role with Service Bus Queue** を使用します。まず、Visual Studio のサーバー エクスプローラーを使用して必要な資格情報を取得します。

1.  「**Visual Studio サーバー エクスプローラーを使用して名前空間を設定する**」の手順に従って Visual Studio を Azure のアカウントに既に接続している場合は、手順 5. に進んでください。

2.  Visual Studio のメニュー バーで **[表示]** を選択し、**[サーバー エクスプローラー]** をクリックします。サーバー エクスプローラーの階層内で **[Azure]** に **[Service Bus]** ノードが表示されます (次の図を参照)。

    ![][24]

3.  サーバー エクスプローラーで **[Azure]** を展開し、**[サービス バス]** を右クリックしてから、**[新しい接続の追加]** をクリックします。

4.  **[接続の追加]** ダイアログで、サービスの名前空間名、発行者名、および発行者キーを入力します。続けて **[OK]** をクリックして接続します。

    ![][25]

5.  Visual Studio の**ソリューション エクスプローラー**で、**MultiTierApp** プロジェクト内の
    **ロール** フォルダーを右クリックします。

6.  **[追加]** をクリックし、**[新しいワーカー ロール プロジェクト]** をクリックします。**[新しいロール プロジェクトの追加]** ダイアログ ボックスが表示されます。

    ![][26]

7.  **[新しいロール プロジェクトの追加]** ダイアログの **[Worker Role with Service Bus Queue]** をクリックします (次の図を参照)。

    ![][27]

8.  **[名前]** ボックスに、プロジェクト名として「**OrderProcessingRole**」を入力します。**[追加]** をクリックします。

9.  サーバー エクスプローラーで、サービスの名前空間名を右クリックし、**[プロパティ]** をクリックします。Visual Studio の **[プロパティ]** ウィンドウの 1 つ目の項目には、接続文字列が表示され、必要な承認の資格情報を含んだサービスの名前空間のエンドポイントが設定されています。たとえば、次の図を見てください。**[ConnectionString]** をダブルクリックし、**Ctrl + C** を押すと、この文字列がクリップボードにコピーされます。

    ![][28]

10. ソリューション エクスプローラーで、手順 7. で作成した **OrderProcessingRole** を右クリックします (右クリックするのは、**[ロール]** の **OrderProcessingRole** です。クラスではありません)。**[プロパティ]** をクリックします。

11. **[プロパティ]** ダイアログの **[設定]** タブで、**Microsoft.ServiceBus.ConnectionString** の **[値]** ボックス内をクリックし、手順 8. でコピーしたエンドポイントの値を貼り付けます。

    ![][29]

12. キューからの注文を処理するときの注文を表す **OnlineOrder** クラスを作成します。作成済みのクラスを再利用することができます。ソリューション エクスプローラーで、**[OrderProcessingRole]** プロジェクトを右クリックします (ロールではなくプロジェクトを右クリック)。**[追加]**、**[既存の項目]** の順にクリックします。

13. **FrontendWebRole\\Models** のサブフォルダーに移動し、**OnlineOrder.cs** をダブルクリックしてこのプロジェクトに追加します。

14. **WorkerRole.cs** 内の **QueueName** 変数の値を `"ProcessingQueue"` から `"OrdersQueue"` に変更します (次のコードを参照)。

        // The name of your queue
        const string QueueName = "OrdersQueue";

15. WorkerRole.cs ファイルの先頭に次の using ステートメントを追加します。

        using FrontendWebRole.Models;

16. `Run()` 関数の `OnMessage` の呼び出し内で、`try` 句に次のコードを追加します。

        Trace.WriteLine("Processing", receivedMessage.SequenceNumber.ToString());
        // View the message as an OnlineOrder
        OnlineOrder order = receivedMessage.GetBody<OnlineOrder>();
        Trace.WriteLine(order.Customer + ": " + order.Product, "ProcessingMessage");
        receivedMessage.Complete();

17. これでアプリケーションが完成しました。前の手順で実行したように F5 キーを押して、完成したアプリケーションをテストします。
    メッセージ数が増えないことに注意してください。これは、Worker ロールがキューの項目を処理し、完了としてマークしているためです。Azure コンピューティング エミュレーターの UI を表示すると、
    Worker ロールのトレース出力を確認できます。これを実行するには、
    タスク バーの通知領域のエミュレーター アイコンを右クリックし、
    **[コンピューティング エミュレーター UI の表示]** をクリックします。

    ![][30]

    ![][31]

## <a name="nextsteps"></a><span class="short-header">次のステップ</span>次のステップ

サービス バスの詳細については、次のリソースを参照してください。

-   [Azure のサービス バス][Azure のサービス バス]
-   [サービス バスの利用方法に関するページ][サービス バスの利用方法に関するページ]
-   [サービス バス キューの使用方法][サービス バス キューの使用方法]

多層シナリオの詳細またはクラウド サービスにアプリケーションを展開する方法については、以下を参照してください。

-   [ストレージ テーブル、キュー、および BLOB を使用する .NET 多層アプリケーション][ストレージ テーブル、キュー、および BLOB を使用する .NET 多層アプリケーション]

Azure のクラウド サービスではなく、Azure Web サイトに多層アプリケーションのフロントエンドを実装することもできます。Web サイトとクラウド サービスの違いについては、「[Azure Execution Models (Azure 実行モデル)][Azure Execution Models (Azure 実行モデル)]」を参照してください。

このチュートリアルで作成したアプリケーションをクラウド サービス Web ロールではなく標準的な Web プロジェクトとして実装するには、次の相違点に注意しつつ、このチュートリアルの手順に従ってください。

1.  プロジェクトを作成する際に、**[クラウド]** カテゴリの **[クラウド サービス]** テンプレートではなく、**[Web]** カテゴリの **[ASP.NET MVC 4 Web アプリケーション]** プロジェクト テンプレートを選択します。そのうえで、MVC アプリケーションを作成するときと同じ手順に従います (「**クラウド構成マネージャー**」セクションの手前まで)。

2.  Worker ロールを作成する際には、Web ロールのときの手順と同様に、新しい個別のソリューションに作成します。ただし、クラウド サービス プロジェクトに Worker ロールのみを作成します。そのうえで、Worker ロールを作成するときと同じ手順に従います。

3.  フロントエンドとバックエンドは個別にテストできるほか、個別の Visual Studio インスタンスで両方を同時にテストすることもできます。

Azure Web サイトにフロンドエンドを展開する方法については、「[Deploying an ASP.NET Web Application to an Azure Website (ASP.NET Web アプリケーションを Azure の Web サイトに展開する)][Deploying an ASP.NET Web Application to an Azure Website (ASP.NET Web アプリケーションを Azure の Web サイトに展開する)]」を参照してください。Azure のクラウド サービスにバックエンドを展開する方法については、「[ストレージ テーブル、キュー、および BLOB を使用する .NET 多層アプリケーション][ストレージ テーブル、キュー、および BLOB を使用する .NET 多層アプリケーション]」を参照してください。

  [create-account-note]: ../includes/create-account-note.md
  [次のステップ]: #nextsteps
  []: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-01.png
  [Azure キューと Azure Service Bus キューの比較]: http://msdn.microsoft.com/ja-jp/library/windowsazure/hh767287.aspx
  [1]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-100.png
  [2]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-101.png
  [ツールと SDK の入手]: http://go.microsoft.com/fwlink/?LinkId=271920
  [3]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-41.png
  [4]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-3.png
  [5]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-4-2-WebPI.png
  [Azure の管理ポータル]: http://manage.windowsazure.com
  [6]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/sb-queues-03.png
  [7]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/sb-queues-04.png
  [8]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-27.png
  [9]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/sb-queues-09.png
  [10]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/sb-queues-06.png
  [Azure Tools for Visual Studio の概要]: http://http://msdn.microsoft.com/ja-jp/library/windowsazure/ff687127.aspx
  [11]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/VSProperties.png
  [12]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-09.png
  [13]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-10.jpg
  [14]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-11.png
  [15]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-02.png
  [16]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-12.png
  [17]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-13.png
  [18]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-33.png
  [19]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-34.png
  [20]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-35.png
  [21]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-40.png
  [22]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-36.png
  [23]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-37.png
  [24]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/SBExplorer.png
  [25]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/SBExplorerAddConnect.png
  [26]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/SBNewWorkerRole.png
  [27]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/SBWorkerRole1.png
  [28]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/SBExplorerProperties.png
  [29]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/SBWorkerRoleProperties.png
  [30]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-38.png
  [31]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-39.png
  [Azure のサービス バス]: http://msdn.microsoft.com/ja-jp/library/windowsazure/ee732537.aspx
  [サービス バスの利用方法に関するページ]: /ja-jp/documentation/services/service-bus/
  [サービス バス キューの使用方法]: /ja-jp/develop/net/how-to-guides/service-bus-queues/
  [ストレージ テーブル、キュー、および BLOB を使用する .NET 多層アプリケーション]: /ja-jp/develop/net/tutorials/multi-tier-web-site/1-overview/
  [Azure Execution Models (Azure 実行モデル)]: http://www.windowsazure.com/ja-jp/develop/net/fundamentals/compute/
  [Deploying an ASP.NET Web Application to an Azure Website (ASP.NET Web アプリケーションを Azure の Web サイトに展開する)]: http://www.windowsazure.com/ja-jp/develop/net/tutorials/get-started/
