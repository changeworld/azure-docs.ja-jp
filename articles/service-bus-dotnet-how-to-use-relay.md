<properties linkid="dev-net-how-to-service-bus-relay" urlDisplayName="Service Bus Relay" pageTitle="How to use Service Bus relay (.NET) - Azure" metaKeywords="get started azure Service Bus Relay C# " description="Learn how to use the Azure Service Bus relay service to connect two applications hosted in different locations." metaCanonical="" services="service-bus" documentationCenter=".NET" title="How to Use the Service Bus Relay Service" authors="sethm" solutions="" manager="timlt" editor="mattshel" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="09/24/2014" ms.author="sethm" />

# サービス バス リレー サービスの使用方法

このガイドでは、サービス バス リレー サービスの使用方法について説明します。サンプルは、C# で記述され、Windows CommunicationFoundation API と、Azure 用 .NET ライブラリの一部であるサービス バス アセンブリに含まれている拡張機能を使用しています。サービス バス リレーの詳細については、「[次のステップ][次のステップ]」を参照してください。

[WACOM.INCLUDE [create-account-note](../includes/create-account-note.md)]

## <span class="short-header">サービス バス リレーとは</span>サービス バス リレーとは

サービス バス **リレー** サービスを使用して、Azure データセンターと独自の内部設置型エンタープライズ環境の両方で動作する**ハイブリッド アプリケーション**を構築できます。サービス バス リレーを使用することで、ファイアウォール接続を開放せず、さらに企業ネットワークのインフラストラクチャ内部を変更せずに、企業のエンタープライズ ネットワーク内部にあるWindows Communication Foundation (WCF) サービスを安全にパブリック クラウドに公開することができます。

![リレーの概念][リレーの概念]

サービス バス リレーを使用して、既存のエンタープライズ環境内のWCF サービスをホストできます。その後で、その WCF サービスに対する受信セッションおよび受信要求のリッスンを Azure 内で動作する サービス バスにデリゲートできます。これにより、Azure で実行されるアプリケーション コードや、モバイル ワーカー環境またはエクストラネット パートナー環境に対して、これらのサービスを公開できます。サービス バスを使用すると、これらのサービスにアクセスできるユーザーを詳細なレベルで安全に制御できます。これは、既存のエンタープライズソリューションのデータおよびアプリケーション機能を公開してクラウドで活用するため、強力で安全な手段となります。

このガイドでは、サービス バス リレーを使用して、2 者間の安全な対話を実現する TCP チャネル バインドを使用して公開される WCF Web サービスの作成方法を示します。

## <span class="short-header">サービス名前空間の作成</span>サービス名前空間の作成

Azure でサービス バス リレーを使用するには、最初にサービス名前空間を作成する必要があります。サービス名前空間は、アプリケーション内でService Bus リソースをアドレス指定するためのスコープ コンテナーを提供します。

サービス名前空間を作成するには:

1.  [Azure 管理ポータル][Azure 管理ポータル]へのログオン

2.  管理ポータルの左のナビゲーション ウィンドウで、**[サービス バス]** をクリックします。

3.  管理ポータルの下のウィンドウの **[作成]** をクリックします。

    ![][0]

4.  **[新しい名前空間を追加する]** ダイアログで、名前空間の名前を入力します。その名前が使用できるかどうかがすぐに自動で確認されます。

    ![][1]

5.  入力した名前が利用できることを確認できたら、名前空間をホストする国またはリージョンを選択します (コンピューティング リソースのデプロイ先となる国またはリージョンと同じ国またはリージョンを必ず使用してください)。

    重要: アプリケーションをデプロイする予定の国またはリージョンと**同じ国/リージョン**を選択してください。そうすることで、パフォーマンスが最高になります。

6.  チェック マークをクリックします。これで、システムによってサービス名前空間が 作成および有効化されます。システムがアカウントのリソースを準備し 終わるまでに、数分間かかる場合があります。

    ![][2]

    作成した名前空間が管理ポータルに表示され、アクティブになります。これには少し時間がかかります。ステータスが **[アクティブ]** になるのを待ってから、次に進みます。

## <span class="short-header">管理資格情報の取得</span>名前空間の既定の管理資格情報の取得

新規作成した名前空間に対してリレー接続の作成などの管理操作を実行するには、名前空間の管理資格情報を取得する必要があります。

1.  左側のナビゲーション ウィンドウで **[サービス バス]** ノードをクリックして、利用可能な名前空間の一覧を表示します。
    ![][0]

2.  表示された一覧から先ほど作成した名前空間を選択します。
    ![][3]

3.  **[接続情報]** をクリックします。
    ![][4]

4.  **[接続情報へのアクセス]** ダイアログで、**[既定の発行者]** と **[既定のキー]** のエントリを探します。その値を書き留めておきます。この情報は、この後に名前空間に対して操作を実行するときに使用します。

## <span class="short-header">NuGet パッケージの取得</span>サービス バス NuGet パッケージの取得

サービス バス **NuGet** パッケージは、Service Bus API を取得し、サービス バス依存関係をすべて備えたアプリケーションを構成する最も簡単な方法です。NuGet Visual Studio 拡張機能を使用すると、Visual Studio および Visual Studio Express 2012 for Web でのライブラリやツールのインストールと更新を簡単に行うことができます。サービス バス NuGet パッケージは、Service Bus API を取得し、サービス バス依存関係をすべて備えたアプリケーションを構成する最も簡単な方法です。

アプリケーションに NuGet パッケージをインストールするには、次のステップを行います。

1.  ソリューション エクスプローラーで **[参照]** を右クリックし、 **[NuGet パッケージの管理]** をクリックします。
2.  "WindowsAzure" を検索し、**[Azure のサービス バス]** 項目をクリックします。**[インストール]** をクリックし、インストールが 完了したら、このダイアログを閉じます。

    ![][5]

## <span class="short-header">SOAP Web サービスの公開と使用</span>サービス バスを使用して SOAP Web サービスを TCP で公開および使用する方法

既存の WCF SOAP Web サービスを外部で使用するために公開するには、サービスのバインドおよびアドレスに変更を加える必要があります。WCF サービスの設定や構成によっては、構成ファイルを変更したり、コードを変更したりする必要があります。WCF を使用すると同じサービス上で複数のネットワーク エンドポイントを持つことができるので、既存の内部エンドポイントを保持しながら、同時に外部アクセス用のサービス バス エンドポイントも追加できます。

このタスクでは、単純な WCF サービスを構築し、そのサービスにサービス バス リスナーを追加します。この演習はある程度 Visual Studio 2012 に習熟したユーザーを対象としているため、プロジェクトの作成手順の詳しい内容については説明していません。代わりに、コードに重点を置いています。

次のステップを完了して環境を設定してから、後の手順を開始してください。

1.  Visual Studio 内で、"Client" と "Service" という 2 つのプロジェクトを含むコンソール アプリケーションをソリューション内に作成します。
2.  両方のプロジェクトのターゲット フレームワークを .NET Framework 4 に設定します。
3.  **Azure のサービス バス NuGet** パッケージを両方のプロジェクトに追加します。これにより、プロジェクトへの必要なアセンブリ参照がすべて追加されます。

### サービスの作成方法

最初に、サービス自体を作成します。すべての WCF サービスは、少なくとも次の 3 つの部分で構成されます。

-   交換されるメッセージと呼び出される操作を指定するコントラクトの定義。
-   そのコントラクトの実装。
-   サービスをホストして複数のエンドポイントを公開するホスト。

このセクションでは、これらの各コンポーネントを処理するコード例を示します。

このコントラクトでは、2 つの数値を足して結果を返す **AddNumbers** という 1 つの処理を定義します。**IProblemSolverChannel** インターフェイスは、クライアントでプロキシの有効期間を簡単に管理できるようにするインターフェイスです。このようなインターフェイスを作成することがベスト プラクティスと考えられます。このコントラクト定義を別のファイルに格納しておき、そのファイルを "Client" プロジェクトと "Service" プロジェクトの両方から参照できようにすることをお勧めしますが、コードを両方のプロジェクトにコピーしてもよいでしょう。

        using System.ServiceModel;
     
        [ServiceContract(Namespace = "urn:ps")]
        interface IProblemSolver
        {
            [OperationContract]
            int AddNumbers(int a, int b);
        }
     
        interface IProblemSolverChannel : IProblemSolver, IClientChannel {}

コントラクトが適切に記述されたら、実装は簡単です。

        class ProblemSolver : IProblemSolver
        {
            public int AddNumbers(int a, int b)
            {
                return a + b;
            }
        }

**サービス ホストをプログラムで構成する方法**

コントラクトと実装が適切に記述されたので、次にサービスをホストします。ホスティングは**System.ServiceModel.ServiceHost** オブジェクトの内部で行います。このオブジェクトは、サービスのインスタンスを管理し、メッセージをリッスンするエンドポイントをホストします。次のコードでは、サービスに通常のローカル エンドポイントとサービス バス エンドポイントの両方を構成して、内部エンドポイントと外部エンドポイントの外観を並べて指定しています。文字列 "namespace" は実際の名前空間の名前に置き換え、"key" は前のセットアップ手順で取得した発行者キーに置き換えてください。

    ServiceHost sh = new ServiceHost(typeof(ProblemSolver));

    sh.AddServiceEndpoint(
       typeof (IProblemSolver), new NetTcpBinding(), 
       "net.tcp://localhost:9358/solver");

    sh.AddServiceEndpoint(
       typeof(IProblemSolver), new NetTcpRelayBinding(), 
       ServiceBusEnvironment.CreateServiceUri("sb", "**namespace**", "solver"))
        .Behaviors.Add(new TransportClientEndpointBehavior {
              TokenProvider = TokenProvider.CreateSharedSecretTokenProvider( "owner", "**key**")});

    sh.Open();

    Console.WriteLine("Press ENTER to close");
    Console.ReadLine();

    sh.Close();

この例では、同じコントラクト実装にある 2 つのエンドポイントを作成します。1 つはローカルで、もう 1 つはサービス バスを経由して提示されます。大きな違いはバインドです。ローカルには**NetTcpBinding** を使用し、サービス バス エンドポイントとアドレスには **NetTcpRelayBinding** を使用します。ローカル エンドポイントは、個々のポートを含むローカル ネットワーク アドレスを持ちます。サービス バス エンドポイントは、文字列 "sb"、使用する名前空間の名前、およびパス "solver" で構成されるエンドポイント アドレスを持ちます。全体としては"sb://[serviceNamespace].servicebus.windows.net/solver" という URI になり、このサービス エンドポイントは完全修飾された外部 DNS 名を持つサービス バス TCP エンドポイントとして識別されます。前に説明したようにプレースホルダーを置き換えたコードを"Service" アプリケーションの **Main** 関数に配置すると、このサービスが実際に機能します。サービスでサービス バスを排他的にリッスンするには、ローカル エンドポイントの宣言を削除します。

**サービス ホストを App.config ファイルで構成する方法**

App.config ファイルを使用してホストを構成することもできます。この場合のサービス ホスト用コードは次のとおりです。

    ServiceHost sh = new ServiceHost(typeof(ProblemSolver));
    sh.Open();
    Console.WriteLine("Press ENTER to close");
    Console.ReadLine();
    sh.Close();

エンドポイントの定義は App.config ファイルに移動します。**NuGet** パッケージは、既に定義の範囲を App.config ファイルに追加したことに注意してください。これはサービス バスの必要な構成の拡張機能です。次のコード スニペットは (先に示したコードと同じです)、**system.serviceModel** 要素のすぐ下に配置する必要があります。このスニペットでは、プロジェクト C# 名前空間の名前が "Service" であると想定しています。
プレースホルダーを実際のサービス バス サービスの名前空間とキーに置き換えてください。

    <services>
        <service name="Service.ProblemSolver">
            <endpoint contract="Service.IProblemSolver"
                      binding="netTcpBinding"
                      address="net.tcp://localhost:9358/solver"/>
            <endpoint contract="Service.IProblemSolver"
                      binding="netTcpRelayBinding"
                      address="sb://**namespace**.servicebus.windows.net/solver"
                      behaviorConfiguration="sbTokenProvider"/>
        </service>
    </services>
    <behaviors>
        <endpointBehaviors>
            <behavior name="sbTokenProvider">
                <transportClientEndpointBehavior>
                    <tokenProvider>
                        <sharedSecret issuerName="owner" issuerSecret="**key**" />
                    </tokenProvider>
                </transportClientEndpointBehavior>
            </behavior>
        </endpointBehaviors>
    </behaviors>

以上の変更を加えたサービスは、以前と同じように開始されます。2 つのアクティブなエンドポイントは、1 つはローカルに存在し、もう 1 つはクラウド内でリッスンします。

### クライアントの作成方法

**クライアントをプログラムで構成する方法**

サービスを使用するために、**ChannelFactory** オブジェクトを使用して WCF クライアントを作成できます。サービス バスでは、Access Control サービス (ACS) を使用して実装されたクレームベースのセキュリティ モデルを使用します。**TokenProvider** クラスは、いくつかの既知のトークン プロバイダーを返すファクトリ メソッドが組み込まれたセキュリティ トークン プロバイダーを表します。次の例では、**SharedSecretTokenProvider** を使用して共有シークレット資格情報を保持し、Access Control サービスからの適切なトークンの取得を処理します。名前およびキーは、前のセクションで説明したようにポータルから取得されます。

最初に、**IProblemSolver** コントラクト コードをサービスからクライアント プロジェクトに対して参照またはコピーします。

次に、クライアントの **Main** メソッドのコードを置き換えます。ここでも、プレースホルダー テキストを実際のサービス バス サービスの名前空間およびキーに置き換えてください。

    var cf = new ChannelFactory<IProblemSolverChannel>(
        new NetTcpRelayBinding(), 
        new EndpointAddress(ServiceBusEnvironment.CreateServiceUri("sb", "**namespace**", "solver")));

    cf.Endpoint.Behaviors.Add(new TransportClientEndpointBehavior
                { TokenProvider = TokenProvider.CreateSharedSecretTokenProvider("owner","**key**") });
     
    using (var ch = cf.CreateChannel())
    {
        Console.WriteLine(ch.AddNumbers(4, 5));
    }

これで、クライアントとサービスをコンパイルして実行できます (先にサービスを実行します)。クライアントはサービスを呼び出し、"9" を出力します。クライアントとサーバーを別々のコンピューターで実行しても (ネットワークが異なっても)、通信は維持されます。クライアント コードは、クラウド上でもローカルでも実行できます。

**App.config ファイルでクライアントを構成する方法**

App.config ファイルを使用してクライアントを構成することもできます。そのためのクライアント コードは次のとおりです。

    var cf = new ChannelFactory<IProblemSolverChannel>("solver");
    using (var ch = cf.CreateChannel())
    {
        Console.WriteLine(ch.AddNumbers(4, 5));
    }

エンドポイントの定義は App.config ファイルに移動します。次のコード スニペットは (先に示したコードと同じです)、**system.serviceModel** 要素のすぐ下に配置する必要があります。前の例と同じように、プレースホルダーを実際のサービス バス サービスの名前空間とキーに置き換えてください。

    <client>
        <endpoint name="solver" contract="Service.IProblemSolver"
                  binding="netTcpRelayBinding"
                  address="sb://**namespace**.servicebus.windows.net/solver"
                  behaviorConfiguration="sbTokenProvider"/>
    </client>
    <behaviors>
        <endpointBehaviors>
            <behavior name="sbTokenProvider">
                <transportClientEndpointBehavior>
                    <tokenProvider>
                        <sharedSecret issuerName="owner" issuerSecret="**key**" />
                    </tokenProvider>
                </transportClientEndpointBehavior>
            </behavior>
        </endpointBehaviors>
    </behaviors>

## <span class="short-header">次のステップ</span>次のステップ

これで、サービス バス **リレー** サービスの基本を学習できました。さらに詳細な情報が必要な場合は、次のリンク先を参照してください。

-   サービスの構築: [サービス バス用のサービスの構築に関するページ][サービス バス用のサービスの構築に関するページ]。
-   クライアントの作成: [サービス バス クライアント アプリケーションの作成に関するページ][サービス バス クライアント アプリケーションの作成に関するページ]。
-   サービス バスのサンプル: [Azure のサンプル][Azure のサンプル]からのダウンロード。

  [次のステップ]: #next_steps
  [create-account-note]: ../includes/create-account-note.md
  [リレーの概念]: ./media/service-bus-dotnet-how-to-use-relay/sb-relay-01.png
  [Azure 管理ポータル]: http://manage.windowsazure.com
  [0]: ./media/service-bus-dotnet-how-to-use-relay/sb-queues-13.png
  [1]: ./media/service-bus-dotnet-how-to-use-relay/sb-queues-04.png
  [2]: ./media/service-bus-dotnet-how-to-use-relay/getting-started-multi-tier-27.png
  [3]: ./media/service-bus-dotnet-how-to-use-relay/sb-queues-09.png
  [4]: ./media/service-bus-dotnet-how-to-use-relay/sb-queues-06.png
  [5]: ./media/service-bus-dotnet-how-to-use-relay/getting-started-multi-tier-13.png
  [サービス バス用のサービスの構築に関するページ]: http://msdn.microsoft.com/ja-jp/library/windowsazure/ee173564.aspx
  [サービス バス クライアント アプリケーションの作成に関するページ]: http://msdn.microsoft.com/ja-jp/library/windowsazure/ee173543.aspx
  [Azure のサンプル]: http://code.msdn.microsoft.com/windowsazure
