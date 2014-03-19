<properties linkid="dev-net-e2e-multi-tier" urlDisplayName="多層アプリケーション" pageTitle=".NET 多層アプリケーション - Windows Azure チュートリアル" metaKeywords="Azure のサービス バス キュー チュートリアル, Azure キュー チュートリアル, Azure Worker ロール チュートリアル, Azure .NET キュー チュートリアル, Azure C# キュー チュートリアル, Azure C# Worker ロール チュートリアル" description="サービス バス キューを使用して階層間の通信を行う Windows Azure の多層アプリケーション開発に役立つチュートリアルです。サンプルは .NET で作成されています。" metaCanonical="" services="cloud-services,service-bus" documentationCenter=".NET" title="サービス バス キューを使用する .NET 多層アプリケーション" authors=""  solutions="" writer="sethm" manager="dwrede" editor="mattshel"  />





# サービス バス キューを使用する .NET 多層アプリケーション

Windows Azure 向けアプリケーションは、Visual Studio 2012 および無料の 
Windows Azure SDK for .NET を使用して簡単に開発できます。Visual Studio 2012 を所有していない
場合でも、SDK をインストールすると自動的に Visual Studio Express 2012 for Web がインストール
されるため、Windows Azure 向けの開発を完全に無料で始めることができます。このガイドは、
Windows Azure を初めて使用するユーザーを対象としています。このガイドを最後まで読むと、ローカル環境で実行され、
多層アプリケーションの動作のしくみを理解できる、複数の 
Windows Azure リソースを使用するアプリケーションを作成できます。

学習内容:

-   1 回のダウンロードとインストールで、Windows Azure 開発用に
    コンピューターを準備する方法
-   Windows Azure 開発のための Visual Studio の使用方法
-   Web ロールおよび Worker ロールを使用して Windows Azure に
    多層アプリケーションを作成する方法
-   サービス バス キューを使用して各層間で通信する方法

このチュートリアルでは、Windows Azure のクラウド サービスで多層アプリケーションを構築して実行します。フロントエンドは ASP.NET MVC Web ロール、バックエンドはワーカー ロールになります。同じ多層アプリケーションを作成するときに、クラウド サービスではなく Windows Azure の Web サイトに展開される Web プロジェクトとしてフロントエンドを実装することもできます。Windows Azure の Web サイトのフロントエンドの場合の手順については、「[次の手順](#nextsteps)」を参照してください。

完成したアプリケーションのスクリーンショットは次のようになります。

![][0]

**注:** Windows Azure にも、ストレージ キュー機能が用意されています。Windows Azure のストレージ キューとサービス バス キューの詳細については、「[Windows Azure キューと Windows Azure サービス バス キューの比較][sbqueuecomparison]」を参照してください。

[WACOM.INCLUDE [create-account-note](../includes/create-account-note.md)]

<h2><span class="short-header">ロール間通信</span>シナリオの概要: ロール間通信</h2>

処理を要求するため、Web ロールで実行されているフロントエンド 
UI コンポーネントは、Worker ロールで実行されている中間層ロジックと
対話する必要があります。この例では、各層間での通信にサービス バスで仲介された
メッセージングが使用されています。

Web 層と中間層との間で仲介されたメッセージングを使用すると、2 つの
コンポーネントの結合が解除されます。直接メッセージング (TCP または HTTP) 
とは異なり、Web 層は中間層に直接接続しません。その代わりに、作業を
メッセージとしてサービス バスにプッシュします。サービス バスは、この
メッセージを中間層が受け取り、処理する用意ができるまで、メッセージを
確実に保持します。

サービス バスには、仲介されたメッセージングをサポートする、キューと
トピックという 2 つのエンティティがあります。キューでは、各メッセージは
単一のレシーバーが使用するキューに送信されます。トピックはパブリッシュ/サブスクライブ 
パターンをサポートし、パブリッシュされた各メッセージを、トピックに
登録されている各サブスクリプションが使用します。各サブスクリプションは、
独自のメッセージ キューを論理的に管理しています。また、サブスクリプションに
フィルター ルールを構成し、サブスクリプション キューに渡すメッセージを
フィルターに一致するメッセージのみに制限することができます。この例では、
サービス バス キューが使用されています。

![][1]

この通信メカニズムには、直接メッセージングと比較した場合に、
次のような利点があります。

-   **一時的な結合の解除**: 非同期メッセージング パターンでは、プロデューサーと
    コンシューマーが同時にオンラインになっている必要はありません。コンシューマーが
    メッセージを受け取る用意ができるまで、サービス バスが確実に
    メッセージを保持します。このため、保守などのために、分散型アプリケーションの
    各コンポーネントの接続を自主的に解除できます。また、コンポーネントの
    クラッシュによって接続が解除されても問題なく、接続解除の際にシステム
    全体に影響が及ぶことがありません。さらに、コンシューマー側アプリケーションが
    オンラインになっている時間は、1 日のうち一定の時間だけで済みます。

-   **負荷平準化**: 多くのアプリケーションでは、システム負荷が時間の
    経過と共に変化する一方で、一定量の作業に必要な処理時間は一般的に
    一定です。メッセージ プロデューサーとメッセージ コンシューマーの間を
    キューが仲介することによって、コンシューマー側アプリケーション (ワーカー) の
    プロビジョニングでは平均負荷に対応すればよく、ピーク時の負荷に対応する
    必要はありません。キューの深さは、受信する負荷の変化に合わせて増減します。このため、アプリケーションの負荷に対応するために必要な
    インフラストラクチャの量の面で、料金を抑えることができます。

-   **負荷分散**: 負荷の増大に合わせて、キューからの読み取りのために
    ワーカー プロセスを追加できます。各メッセージは、ワーカー プロセスの中の
    1 つのプロセスによって処理されます。また、このプルベースの負荷分散では、
    各ワーカー マシンがそれぞれ独自の最大レートでメッセージをプルする
    ため、ワーカー マシンの処理能力が異なる場合であっても使用率を
    最適化できます。このパターンは、競合コンシューマー パターンと
    呼ばれることもあります。

    ![][2]

以降のセクションでは、このアーキテクチャを実装するコードについて説明します。

<h2><span class="short-header">環境の設定</span>開発環境の設定</h2>

Windows Azure アプリケーションを開発する前に、ツールを入手して、開発環境
を設定する必要があります。

1.  Windows Azure SDK for .NET をインストールするには、次のボタンをクリックします。

    [ツールと SDK の入手][]

2. 	**[SDK のインストール]** をクリックします。

3. 	使用している Visual Studio のバージョンに対応するリンクを選択します。このチュートリアルの手順では、Visual Studio 2012 を使用します。

	![][32]

4. 	インストール ファイルの実行または保存を求める
    メッセージが表示されたら、**[実行]** をクリックします。

    ![][3]

5.  Web Platform Installer の **[インストール]** をクリックし、インストールの手順を進めます。

    ![][33]

6.  インストールが完了すると、開発に必要なツールがすべて
    揃います。SDK には、Visual Studio で Windows Azure アプリケーションを
    簡単に開発するためのツールが用意されています。Visual Studio 
    がインストールされていない場合、無料の Visual Studio 
    Express for Web もインストールされます。

<h2><span class="short-header">名前空間の設定</span>サービス バス名前空間の設定</h2>

次の手順では、サービス名前空間を作成し、共有秘密キーを取得します。サービス名前空間は、サービス バスによって公開される
各アプリケーションのアプリケーション境界を提供します。共有秘密キーは、
サービス名前空間が作成されるときにシステムによって自動的に生成
されます。サービス名前空間と共有秘密キーの組み合わせが、
アプリケーションへのアクセスをサービス バスが認証するための
資格情報になります。

1.  [Windows Azure の管理ポータル][]にログインします。

2.  管理ポータルの左のナビゲーション ウィンドウで、
**[サービス バス]** をクリックします。

3.  管理ポータルの下のウィンドウの **[作成]** をクリックします。

    ![][6]

4.  **[新しい名前空間を追加する]** ダイアログで、名前空間の名前を入力します。
    その名前が使用できるかどうかがすぐに自動で確認されます。
    ![][7]

5.  入力した名前が利用できることを確認できたら、名前空間を
    ホストする国またはリージョンを選択します (コンピューティング 
    リソースを展開する国またはリージョンと同じ国またはリージョンを
    必ず使用してください)。

    重要: アプリケーションを展開する予定の国またはリージョンと**同じ国/リージョン**
    を選択してください。そうすることで、パフォーマンスが最高になります。

6.  チェック マークをクリックします。これで、システムによってサービス名前空間が
    作成および有効化されます。システムがアカウントのリソースを準備し
    終わるまでに、数分間かかる場合があります。

	![][27]

7.  メイン ウィンドウで、使用するサービス名前空間の名前をクリックします。

	![][30]

8. **[接続情報]** をクリックします。

	![][31]

9.  **[接続情報へのアクセス]** ウィンドウで、**[既定の発行者]** と **[既定のキー]** のエントリを探します。

10.  キーを書き留めておくか、クリップボードにコピーしておいてください。

<h2><span class="short-header">Web ロールの作成</span>Web ロールの作成</h2>

このセクションでは、アプリケーションのフロンドエンドを作成します。最初に、
アプリケーションで表示するさまざまなページを作成します。
その後、サービス バス キューに項目を送信し、キューに関するステータス
情報を表示するコードを追加します。

### プロジェクトを作成する

1.  管理者特権を使用して、Microsoft Visual Studio 2012 または 
    Microsoft Visual Studio Express for Web を起動します。管理特権で 
    Visual Studio を起動するには、**[Microsoft Visual Studio 2012 
    (または Microsoft Visual Studio Express for Web)]** を右クリックし、
    **[管理者として実行]** をクリックします。Windows Azure コンピューティング 
    エミュレーター (このガイドで解説します) を使用するには、管理者特権で 
    Visual Studio を起動する必要があります。

    Visual Studio で、**[ファイル]** メニューの **[新規作成]** を
クリックした後、**[プロジェクト]** をクリックします。

    ![][8]

2.  **[インストールされたテンプレート]** の **[Visual C#]** の下にある [クラウド] を
クリックし、**[Windows Azure プロジェクト]** をクリックします。プロジェクトの名前を 
**"MultiTierApp"** にします。次に、**[OK]** をクリックします。

    ![][9]

3.  **.NET Framework 4** ロールの **[ASP.NET MVC 3 Web ロール]** を
    ダブルクリックします。

    ![][10]

4.  **[Windows Azure クラウド サービス ソリューション]** の **[MvcWebRole1]** をポイントし、
    鉛筆のアイコンをクリックして、Web ロールの名前を **FrontendWebRole** に変更します。次に、**[OK]** をクリックします (「FrontEnd」ではなく「Frontend」と入力してください。小文字の "e" です)。

    ![][11]

5.  **[テンプレートの選択]** ボックスの一覧で **[インターネット 
    アプリケーション]** をクリックし、**[OK]** をクリックします。

    ![][12]

6.  **ソリューション エクスプローラー**で **[参照設定]** を右クリックし、
    **[NuGet パッケージの管理]** または **[ライブラリ パッケージ参照の追加]** をクリックします。

7.  ダイアログの左側の **[オンライン]** をクリックします。
    "**WindowsAzure**" を検索し、**[Windows Azure のサービス 
    バス]** 項目をクリックします。次に、インストールを実行し、このダイアログを閉じます。

    ![][13]

8.  これで、必要なクライアント アセンブリを参照できるようになり、
    新しいコード ファイルがいくつか追加されました。

9.  **ソリューション エクスプローラー**で **[Models]** を右クリックし、
    **[追加]**、**[クラス]** の順にクリックします。[名前] ボックスに
    「**OnlineOrder.cs**」と入力します。**[追加]** をクリックします。

### Web ロール用のコードの作成

このセクションでは、アプリケーションで表示するさまざまなページを作成します。

1.  Visual Studio で、**OnlineOrder.cs** ファイルの既存の
    名前空間定義を、次のコードに置き換えます。

        namespace FrontendWebRole.Models
        {
            public class OnlineOrder
            {
                public string Customer { get; set; }
                public string Product { get; set; }
            }
        }

2.  **ソリューション エクスプローラー**で、
    **Controllers\HomeController.cs** をダブルクリックします。先ほど作成した
    モデルの名前空間とサービス バスをインクルードするため、ファイルの
    先頭に次の **using** ステートメントを追加します。

        using FrontendWebRole.Models;
        using Microsoft.ServiceBus.Messaging;
        using Microsoft.ServiceBus;

3.  また、Visual Studio で、**HomeController.cs** ファイルの既存の
名前空間定義を次のコードに置き換えます。このコードには、
    キューへの項目の送信を処理するメソッドが含まれています。

        namespace FrontendWebRole.Controllers
        {
            public class HomeController : Controller
            {
                public ActionResult Index()
                {
                    // 単純な Submit へのリダイレクト (Submit が
                    // このアプリケーションの最初のページの役割を果たすため)
                    return RedirectToAction("Submit");
                }

                public ActionResult About()
                {
                    return View();
                }

                // GET: /Home/Submit
                // 送信フォームとして作成するビューのコントローラー 
                // メソッド
                public ActionResult Submit()
                {
                    //  キューのメッセージ数を表示するためのコードをここに記述します。

                    return View();
                }

                // POST: /Home/Submit
                // 送信フォームからの送信を処理するコントローラー 
                // メソッド
                [HttpPost]
				// クロス サイト スクリプト攻撃やクロスサイト リクエスト フォージェリを
				// 防ぐための属性
    			[ValidateAntiForgeryToken] 
                public ActionResult Submit(OnlineOrder order)
                {
                    if (ModelState.IsValid)
                    {
                        //  キューに送信するコードをここに記述
                    
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
**[ビューの追加]** を選択します。

    ![][14]

6.  ビューを作成するためのダイアログが表示されます。**[厳密に型指定された
    ビューを作成する]** チェック ボックスをオンにします。さらに、**[モデル クラス]** 
    ボックスの一覧の **[OnlineOrder]** をクリックし、**[スキャフォールディング  
    ビュー テンプレート]** ボックスの一覧の **[Create]** をクリックします。

    ![][15]

7.  **[追加]** をクリックします。

8.  次に、アプリケーションの表示名を変更します。
    **ソリューション エクスプローラー**で 
    **Views\Shared\\_Layout.cshtml** ファイルをダブルクリックし、
    Visual Studio エディターで開きます。

9.  **My ASP.NET MVC Application** となっている箇所をすべて 
    **LITWARE'S Awesome Products** に置き換えます。

10.	**"your logo here"** を **LITWARE'S Awesome Products** に置き換えます。

	![][16]

11. **Home**、**About**、および **Contact** の各リンクを削除します。以下の強調表示されたコードを削除してください。

	![][28]
  

12. 最後に、キューに関する情報を表示できるように、送信ページを
    修正します。**ソリューション エクスプローラー**で 
    **Views\Home\Submit.cshtml** ファイルをダブルクリックし、
    Visual Studio エディターで開きます。**&lt;h2>Submit&lt;/h2>** という行の下に、次の行を追加します。この時点では、
    **ViewBag.MessageCount** は空の状態です。この値は後で入力します。

        <p>Current Number of Orders in Queue Waiting to be Processed:@ViewBag.MessageCount</p>
             

13. これで、UI の実装が終わりました。**F5** キーを押して
    アプリケーションを実行し、想定どおりに表示されることを確認します。

    ![][17]

### サービス バス キューに項目を送信するためのコードの作成

次に、項目をキューに送信するためのコードを追加します。最初に、
サービス バス キューの接続情報を含むクラスを作成します。次に、
**Global.aspx.cs** から接続を初期化します。最後に、サービス バス キューに
実際に項目を送信できるように、**HomeController.cs** 内に作成してある
送信用のコードを更新します。

1.  ソリューション エクスプローラーで、**FrontendWebRole** を右クリックします (ロールではなくプロジェクトを右クリック)。**[追加]**、**[クラス]** の順にクリックします。

2.  クラスに **QueueConnector.cs** という名前を付けます。**[追加]** をクリックしてクラスを作成します。

3.  次に、接続情報をカプセル化し、サービス バス キューへの接続を
    初期化するメソッドを含むコードを貼り付けます。
    QueueConnector.cs に次のコードを貼り付け、**Namespace**、
**IssuerName**、および **IssuerKey** の値を入力します。これらの値は、
    [管理ポータル][Windows Azure Management Portal]で調べることができます。

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
                // スレッド セーフ。要求のたびに作成し直すのではなく、
                // キャッシュすることをお勧めします。
                public static QueueClient OrdersQueueClient;

                // これらの値は管理ポータルで確認
                public const string Namespace = "your service bus namespace";
                public const string IssuerName = "issuer name";
                public const string IssuerKey = "issuer key";

                // キューの名前
                public const string QueueName = "OrdersQueue";

                public static NamespaceManager CreateNamespaceManager()
                {
                    // 管理操作にアクセスするための
                    // 名前空間マネージャーを作成
                    var uri = ServiceBusEnvironment.CreateServiceUri(
                        "sb", Namespace, String.Empty);
                    var tP = TokenProvider.CreateSharedSecretTokenProvider(
                        IssuerName, IssuerKey);
                    return new NamespaceManager(uri, tP);
                }

                public static void Initialize()
                {
                    // Http を使用して、送信ファイアウォールに対応
                    ServiceBusEnvironment.SystemConnectivity.Mode = 
                        ConnectivityMode.Http;

                    // 管理操作にアクセスするための
                    // 名前空間マネージャーを作成
                    var namespaceManager = CreateNamespaceManager();

                    // キューがなければ作成します
                    if (!namespaceManager.QueueExists(QueueName))
                    {
                        namespaceManager.CreateQueue(QueueName);
                    }

                    //  キューのクライアントを取得
                    var messagingFactory = MessagingFactory.Create(
                        namespaceManager.Address, 
                        namespaceManager.Settings.TokenProvider);
                    OrdersQueueClient = messagingFactory.CreateQueueClient(
                        "OrdersQueue");
                }
            }
        }

4.  次に、**Initialize** メソッドが呼び出されるようにします。**ソリューション エクスプローラー**で、
    **Global.asax\Global.asax.cs** をダブルクリックします。

5.  次のコードを **Application_Start** メソッドの最終行に
    追加します。

        FrontendWebRole.QueueConnector.Initialize();

6.  最後に、項目をキューに送信するため、前の手順で作成した 
    Web コードを更新します。**ソリューション エクスプローラー**で、
    前の手順で作成した **Controllers\HomeController.cs** をダブル
    クリックします。

7.  **Submit()** メソッドを次のように更新し、キューのメッセージ数を
    取得できるようにします。

        public ActionResult Submit()
        {            
            // サービス バス キューで管理操作および診断操作を実行するための 
            //NamespaceManager を取得
            var namespaceManager = QueueConnector.CreateNamespaceManager();

            // キューを取得し、メッセージ数を取得
            var queue = namespaceManager.GetQueue(QueueConnector.QueueName);
            ViewBag.MessageCount = queue.MessageCount;

            return View();
        }

8.  **Submit(OnlineOrder order)** メソッドを次のように更新し、
    キューに注文情報を送信できるようにします。

        public ActionResult Submit(OnlineOrder order)
        {
            if (ModelState.IsValid)
            {
                // 注文からメッセージを作成
                var message = new BrokeredMessage(order);
                
                // 注文を送信
                QueueConnector.OrdersQueueClient.Send(message);
                return RedirectToAction("Submit");
            }
            else
            {
                return View(order);
            }
        }

9.  ここで再び、アプリケーションを実行します。注文を
    送信するたびに、メッセージ数が増えていきます。

    ![][18]

<h2><span class="short-header">構成マネージャー</span>クラウド構成マネージャー</h2>

Windows Azure は、サービス バスなど、Windows Azure サービス クライアントの新しいインスタンスを Microsoft のクラウド サービス全体で一貫した方法で作成できる一連のマネージ API をサポートしています。アプリケーションがホストされている場所 (オンプレミス、Microsoft クラウド サービス、Web サイト、永続的な VM ロールなど) に関係なく、この API を通じて、これらのクライアント (**CloudBlobClient**、**QueueClient**、**TopicClient** など) をインスタンス化することができます。また、API を使用することで、こうしたクライアントをインスタンス化するために必要な構成情報を取得でき、また、構成に変更を加える際も、呼び出し元のアプリケーションを展開し直さずに済みます。この API は、[Microsoft.WindowsAzure.Configuration.CloudConfigurationManager][] クラスに存在します。API はクライアント側にも存在します。

### 接続文字列

クライアント (たとえば、サービス バスの **QueueClient**) をインスタンス化するための構成情報は、接続文字列として表すことができます。その接続文字列を使用してクライアントの型をインスタンス化する **CreateFromConnectionString()** メソッドがクライアント側には用意されています。たとえば、次の構成セクションがあるとします。

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

	// サービス バス キューへの接続を初期化
	Client = QueueClient.CreateFromConnectionString(connectionString, QueueName);

次のセクションのコードでは、これらの構成管理 API を使用しています。

<h2><span class="short-header">ワーカー ロールの作成</span>ワーカー ロールの作成</h2>

次に、送信された注文を処理する Worker ロールを
作成します。この例では、Visual Studio プロジェクト テンプレートの **Worker Role with Service Bus Queue** を使用します。まず、Visual Studio のサーバー エクスプローラーを使用して必要な資格情報を取得します。

1. Visual Studio のメニュー バーで **[表示]** を選択し、**[サーバー エクスプローラー]** をクリックします。サーバー エクスプローラーの階層内で **[Windows Azure]** に **[サービス バス]** ノードが表示されます (次の図を参照)。

	![][21]

2. サーバー エクスプローラーで **[Windows Azure]** を展開し、**[サービス バス]** を右クリックしてから、**[新しい接続の追加]** をクリックします。

3. **[接続の追加]** ダイアログで、サービスの名前空間名、発行者名、および発行者キーを入力します。続けて **[OK]** をクリックして接続します。

	![][22]

4.  Visual Studio の**ソリューション エクスプローラー**で、
    **MultiTierApp** プロジェクト内の **ロール** フォルダーを右クリックします。

5.  **[追加]** をクリックし、**[新しいワーカー ロール プロジェクト]** をクリックします。**[新しいロール プロジェクトの追加]** ダイアログが表示されます。

	![][26]

6.  **[新しいロール プロジェクトの追加]** ダイアログの **[Worker Role with Service Bus Queue]** をクリックします (次の図を参照)。

	![][23]

7.  **[名前]** ボックスに、プロジェクト名として「**OrderProcessingRole**」を入力します。**[追加]** をクリックします。

8.  サーバー エクスプローラーで、サービスの名前空間名を右クリックし、**[プロパティ]** をクリックします。Visual Studio の **[プロパティ]** ウィンドウの 1 つ目の項目には、接続文字列が表示され、必要な承認の資格情報を含んだサービスの名前空間のエンドポイントが設定されています。たとえば、次の図を見てください。[**ConnectionString**] をダブルクリックし、**Ctrl + C** キーを押すと、この文字列がクリップボードにコピーされます。

	![][24]

9.  ソリューション エクスプローラーで、手順 7. で作成した **OrderProcessingRole** を右クリックします (右クリックするのは、**[ロール]** の **OrderProcessingRole** です。クラスではありません)。**[プロパティ]** をクリックします。

10.  **[プロパティ]** ダイアログの **[設定]** タブで、**Microsoft.ServiceBus.ConnectionString** の **[値]** ボックス内をクリックし、手順 8. でコピーしたエンドポイントの値を貼り付けます。

	![][25]

11.  キューからの注文を処理するときの注文を表す **OnlineOrder** クラスを作成します。作成済みのクラスを再利用することができます。ソリューション エクスプローラーで、**OrderProcessingRole** プロジェクトを右クリックします (ロールではなくプロジェクトを右クリック)。**[追加]** をクリックし、**[既存の項目]** をクリックします。

12. **FrontendWebRole\Models** のサブフォルダーに移動し、**OnlineOrder.cs** をダブルクリックしてこのプロジェクトに追加します。

13. **WorkerRole.cs** 内の **QueueName** 変数の値を `"ProcessingQueue"` から `"OrdersQueue"` に変更します (次のコードを参照)。

		// キューの名前
		const string QueueName = "OrdersQueue";

14. WorkerRole.cs ファイルの先頭に次の using ステートメントを追加します。

		using FrontendWebRole.Models;

15. `Run()` 関数の `OnMessage` の呼び出し内で、`try` 句に次のコードを追加します。

		Trace.WriteLine("Processing", receivedMessage.SequenceNumber.ToString());
		// メッセージを OnlineOrder として表示
		OnlineOrder order = receivedMessage.GetBody<OnlineOrder>();
		Trace.WriteLine(order.Customer + ": " + order.Product, "ProcessingMessage");
		receivedMessage.Complete();

16.  これでアプリケーションが完成しました。前の手順で実行したように 
    F5 キーを押して、完成したアプリケーションをテストします。メッセージ数が増えないことに注意してください。これは、ワーカー ロールがキューの項目を処理し、完了としてマークしているためです。Windows Azure コンピューティング 
    エミュレーターの UI を表示すると、Worker ロールのトレース出力を確認できます。これを
実行するには、タスク バーの通知領域のエミュレーター アイコンを右クリックし、
**[コンピューティング エミュレーター UI の表示]** をクリックします。

    ![][19]

    ![][20]

<h2><a name="nextsteps"></a><span class="short-header">次の手順</span>次の手順</h2>  

サービス バスの詳細については、次のリソースを参照してください。
  
* [Windows Azure のサービス バス][sbmsdn]  
* [サービス バスの利用方法に関するページ][sbwacom]  
* [サービス バス キューの使用方法][sbwacomqhowto]  

多層シナリオの詳細またはクラウド サービスにアプリケーションを展開する方法については、以下を参照してください。

* [ストレージ テーブル、キュー、および BLOB を使用する .NET 多層アプリケーション][mutitierstorage]  

Windows Azure のクラウド サービスではなく、Windows Azure の Web サイトに多層アプリケーションのフロントエンドを実装することもできます。Web サイトとクラウド サービスの違いについては、「[Windows Azure Execution Models (Windows Azure 実行モデル)][executionmodels]」を参照してください。

このチュートリアルで作成したアプリケーションをクラウド サービス Web ロールではなく標準的な Web プロジェクトとして実装するには、次の相違点に注意しつつ、このチュートリアルの手順に従ってください。

1. プロジェクトを作成する際に、**[クラウド]** カテゴリの **[クラウド サービス]** テンプレートではなく、**[Web]** カテゴリの **[ASP.NET MVC 4 Web アプリケーション]** プロジェクト テンプレートを選択します。そのうえで、MVC アプリケーションを作成するときと同じ手順に従います (「**クラウド構成マネージャー**」セクションの手前まで)。

2. Worker ロールを作成する際には、Web ロールのときの手順と同様に、新しい個別のソリューションに作成します。ただし、クラウド サービス プロジェクトにワーカー ロールのみを作成します。そのうえで、Worker ロールを作成するときと同じ手順に従います。

3. フロントエンドとバックエンドは個別にテストできるほか、個別の Visual Studio インスタンスで両方を同時にテストすることもできます。

Windows Azure の Web サイトにフロントエンドを展開する方法については、「[Deploying an ASP.NET Web Application to a Windows Azure Web Site (ASP.NET Web アプリケーションを Windows Azure の Web サイトに展開する)](http://www.windowsazure.com/en-us/develop/net/tutorials/get-started/)」を参照してください。Windows Azure のクラウド サービスにバックエンドを展開する方法については、「[.NET Multi-Tier Application Using Storage Tables, Queues, and Blobs (ストレージ テーブル、キュー、および BLOB を使用する .NET 多層アプリケーション)][mutitierstorage]」を参照してください。


  [0]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-01.png
  [1]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-100.png
  [sbqueuecomparison]: http://msdn.microsoft.com/en-us/library/windowsazure/hh767287.aspx
  [2]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-101.png
  [ツールと SDK の入手]: http://go.microsoft.com/fwlink/?LinkId=271920
  [3]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-3.png
  
  
  
  [Windows Azure の管理ポータル]: http://manage.windowsazure.com
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
  [sbmsdn]: http://msdn.microsoft.com/en-us/library/windowsazure/ee732537.aspx  
  [sbwacom]: /en-us/manage/services/service-bus/  
  [sbwacomqhowto]: /en-us/develop/net/how-to-guides/service-bus-queues/  
  [mutitierstorage]: /en-us/develop/net/tutorials/multi-tier-web-site/1-overview/ 
  [executionmodels]: http://www.windowsazure.com/en-us/develop/net/fundamentals/compute/


