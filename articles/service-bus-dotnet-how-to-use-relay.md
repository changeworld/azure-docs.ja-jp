<properties urlDisplayName="Service Bus Relay" pageTitle="Service Bus リレーの使用 (.NET) - Azure" metaKeywords="Azure Service Bus Relay の使用 C# " description="Learn how to use the Azure Service Bus relay service to connect two applications hosted in different locations." metaCanonical="" services="service-bus" documentationCenter=".NET" title="How to Use the Service Bus Relay Service" authors="sethm" solutions="" manager="timlt" editor="mattshel" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="09/24/2014" ms.author="sethm" />







# サービス バス リレー サービスの使用方法

このガイドでは、Service Bus Relay サービスの使用方法について説明します。サンプルは、C# で記述され、Windows Communication Foundation API と、Azure 用 .NET ライブラリの一部である Service Bus アセンブリに含まれている拡張機能を使用しています。Service Bus リレーの詳細については、「[次のステップ][]」セクションを参照してください。

[WACOM.INCLUDE [create-account-note](../includes/create-account-note.md)]

<h2>サービス バス リレーとは</h2>

Service Bus **Relay** サービスを使用して、Azure データセンターと独自の内部設置型エンタープライズ環境の両方で動作する**ハイブリッド アプリケーション**を構築できます。Service Bus リレーを使用することで、ファイアウォール接続を開放せず、または企業ネットワークのインフラストラクチャ内部を変更せずに、企業のエンタープライズ ネットワーク内部にある Windows Communication Foundation (WCF) サービスを安全にパブリック クラウドに公開することができます。

![Relay Concepts](./media/service-bus-dotnet-how-to-use-relay/sb-relay-01.png)

Service Bus リレーを使用して、既存のエンタープライズ環境内の WCF サービスをホストできます。その後で、その WCF サービスに対する受信セッションおよび受信要求のリッスンを Azure 内で動作する Service Bus にデリゲートできます。これにより、Azure で実行されるアプリケーション コードや、モバイル ワーカー環境またはエクストラネット パートナー環境に対して、これらのサービスを公開できます。Service Bus を使用すると、これらのサービスにアクセスできるユーザーを詳細なレベルで安全に制御できます。これは、既存のエンタープライズ ソリューションのデータおよびアプリケーション機能を公開してクラウドで活用するため、強力で安全な手段となります。

このガイドでは、Service Bus リレーを使用して、2 者間の安全な対話を実現する TCP チャネル バインドを使用して公開される WCF Web サービスの作成方法を示します。

<h2>サービス名前空間の作成</h2>

Azure で Service Bus リレーを使用するには、最初にサービス名前空間を作成する必要があります。サービス名前空間は、アプリケーション内で Service Bus リソースをアドレス指定するためのスコープ コンテナーを提供します。

サービス名前空間を作成するには:

1.  [Azure の管理ポータル][]にログオンします。

2.  管理ポータルの左のナビゲーション ウィンドウで
    **[Service Bus]** をクリックします。

3.  管理ポータルの下のウィンドウの **[作成]** をクリックします。   

	![](./media/service-bus-dotnet-how-to-use-relay/sb-queues-13.png)

4.  **[新しい名前空間を追加する]** ダイアログで、名前空間の名前を入力します。
    その名前が使用できるかどうかがすぐに自動で確認されます。   

	![](./media/service-bus-dotnet-how-to-use-relay/sb-queues-04.png)


5.  入力した名前が利用できることを確認できたら、名前空間を
 ホストする国または地域を選択します (コンピューティング リソースを配置する同じ国/地域を必ず使用します)。

重要:アプリケーションをデプロイする予定の国またはリージョンと**同じ国/リージョン**を選択してください。そうすることで、最大限のパフォーマンスを引き出すことができます。

6.	チェック マークをクリックします。これで、システムによってサービス名前空間が    作成および有効化されます。システムがアカウントのリソースを準備し終わるまでに、数分間かかる場合があります。

	![](./media/service-bus-dotnet-how-to-use-relay/getting-started-multi-tier-27.png)

作成した名前空間が管理ポータルに表示され、有効化されます。有効化には少し時間がかかります。ステータスが **[アクティブ]** になるのを待ってから、次に進みます。

<h2>名前空間の既定の管理資格情報の取得</h2>

新規作成した名前空間に対してリレー接続の作成などの管理操作を実行するには、名前空間の管理資格情報を取得する必要があります。

1.  左側のナビゲーション ウィンドウで、**[Service Bus]** ノードをクリックして、
    使用できる名前空間の一覧を表示します。   
	![](./media/service-bus-dotnet-how-to-use-relay/sb-queues-13.png)


2.  表示された一覧から先ほど作成した名前空間を選択します。   
	![](./media/service-bus-dotnet-how-to-use-relay/sb-queues-09.png)


3.  **[接続情報]** をクリックします。   
	![](./media/service-bus-dotnet-how-to-use-relay/sb-queues-06.png)
 
4.  **[接続情報へのアクセス]** ダイアログで、**[既定の発行者]** と **[既定のキー]** のエントリを探します。その値を書き留めておきます。この情報は、この後に名前空間に対して操作を実行するときに使用します。

<h2>サービス バス NuGet パッケージの取得</h2>

Service Bus **NuGet** パッケージは、Service Bus API を取得し、Service Bus 依存関係をすべて備えたアプリケーションを構成する最も簡単な方法です。NuGet Visual Studio 拡張機能を使用すると、Visual Studio および Visual Studio Express 2012 for Web でのライブラリやツールのインストールと更新を簡単に行うことができます。Service Bus NuGet パッケージは、Service Bus API を取得し、Service Bus 依存関係をすべて備えたアプリケーションを構成する最も簡単な方法です。

アプリケーションに NuGet パッケージをインストールするには、次のステップを行います。

1.  ソリューション エクスプローラーで **[参照]** を右クリックし、**[NuGet パッケージの管理]** をクリックします。
2.  "Microsoft Azure" を検索し、**[Azure Service Bus]** 項目をクリックします。**[インストール]** をクリックし、インストールが完了したら、このダイアログを閉じます。

	![](./media/service-bus-dotnet-how-to-use-relay/getting-started-multi-tier-13.png)
  

<h2>Service Bus を使用して SOAP Web サービスを TCP で公開および使用する方法</h2>

既存の WCF SOAP Web サービスを外部で使用するために公開するには、サービスのバインドおよびアドレスに変更を加える必要があります。WCF サービスの設定や構成によっては、構成ファイルを変更したり、コードを変更したりする必要があります。WCF を使用すると同じサービス上で複数のネットワーク エンドポイントを持つことができるので、既存の内部エンドポイントを保持しながら、同時に外部アクセス用の Service Bus エンドポイントも追加できます。

このタスクでは、単純な WCF サービスを構築し、そのサービスに Service Bus リスナーを追加します。この演習はある程度 Visual Studio 2012 に習熟したユーザーを対象としているため、プロジェクトの作成手順の詳しい内容については説明していません。代わりに、コードに重点を置いています。

次の手順を完了して環境を設定してから、後の手順を開始してください。

1.  Visual Studio 内で、2 つのプロジェクト、"Client" と "Service" を
    含むコンソール アプリケーションを作成します。
2.  両方のプロジェクトのターゲット フレームワークを .NET Framework 4 に設定します。
3.  **Azure Service Bus NuGet** パッケージを両方のプロジェクトに追加します。
    これにより、プロジェクトへの必要なアセンブリ参照がすべて追加されます。

### サービスの作成方法

最初に、サービス自体を作成します。すべての WCF サービスは、少なくとも次の 3 つの部分で構成されます。

-   交換されるメッセージと呼び出される操作を指定する
    コントラクトの定義。 
-   そのコントラクトの実装。
-   サービスをホストして複数のエンドポイントを
    公開するホスト。

このセクションでは、これらの各コンポーネントを処理するコード例を示します。

このコントラクトでは、2 つの数値を足して結果を返す **AddNumbers** という 1 つの処理を定義します。**IProblemSolverChannel** インターフェイスは、クライアントでプロキシの有効期間を簡単に管理できるようにします。このようなインターフェイスを作成することがベスト プラクティスと考えられます。このコントラクト定義を別のファイルに格納しておき、そのファイルを "Client" プロジェクトと "Service" プロジェクトの両方から参照できようにすることをお勧めしますが、コードを両方のプロジェクトにコピーしてもよいでしょう。

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

コントラクトと実装が適切に記述されたので、次にサービスをホストします。ホスティングは **System.ServiceModel.ServiceHost** オブジェクトの内部で行い、サービスのインスタンスを管理し、メッセージをリッスンするエンドポイントをホストします。次のコードでは、サービスに通常のローカル エンドポイントと Service Bus エンドポイントの両方を構成して、内部エンドポイントと外部エンドポイントの外観を並べて指定しています。文字列 "\*\*namespace\*\*" は実際の名前空間の名前に置き換え、"\*\*key\*\*" は前のセットアップ手順で取得した発行者キーに置き換えてください。 

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

この例では、同じコントラクト実装にある 2 つのエンドポイントを作成します。1 つはローカルで、もう 1 つは Service Bus を経由して提示されます。大きな違いはバインドです。ローカル エンドポイントには **NetTcpBinding** を使用し、Service Bus エンドポイントとアドレスには **NetTcpRelayBinding** を使用します。ローカル エンドポイントは、個々のポートを含むローカル ネットワーク アドレスを持ちます。Service Bus エンドポイントは、文字列 "sb"、使用する名前空間の名前、およびパス "solver" で構成されるエンドポイント アドレスを持ちます。全体としては "sb://[ServiceNamespace].servicebus.windows.net/solver" という URI になり、このサービス エンドポイントは完全修飾された外部 DNS 名を持つ Service Bus TCP エンドポイントとして識別されます。前述のようにプレースホルダーを置き換えたコードを "Service" アプリケーションの **Main** 関数に配置すると、このサービスが実際に機能します。Service Bus でサービスを排他的にリッスンするには、ローカル エンドポイントの宣言を削除します。

**サービス ホストを App.config ファイルで構成する方法**

App.config ファイルを使用してホストを構成することもできます。この場合のサービス ホスト用コードは次のとおりです。

    ServiceHost sh = new ServiceHost(typeof(ProblemSolver));
    sh.Open();
    Console.WriteLine("Press ENTER to close");
    Console.ReadLine();
    sh.Close();

エンドポイントの定義は App.config ファイルに移動します。**NuGet** パッケージは、既に定義の範囲を App.config ファイルに追加したことに注意してください。これは Service Bus には必要な構成の拡張機能です。次のコード スニペットは (上に示したコードと同じです)、**system.serviceModel** 要素のすぐ下に配置する必要があります。このスニペットでは、プロジェクト C\# 名前空間の名前が "Service" であると想定しています。プレースホルダーを実際の Service Bus サービスの名前空間とキーに置き換えてください。

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

以上の変更を加えたサービスは、以前と同じように開始されます。ただし、2 つのアクティブなエンドポイントは、1 つはローカルに存在し、もう 1 つはクラウド内でリッスンします。

### クライアントの作成方法

**クライアントをプログラムで構成する方法**

サービスを使用するために、**ChannelFactory** オブジェクトを使用して WCF クライアントを作成できます。Service Bus では、Access Control Service (ACS) を使用して実装されたクレームベースのセキュリティ モデルを使用します。**TokenProvider** クラスは、いくつかの既知のトークン プロバイダーを返すファクトリ メソッドが組み込まれたセキュリティ トークン プロバイダーを表します。次の例では、**SharedSecretTokenProvider** を使用して共有シークレット資格情報を保持し、Access Control Service からの適切なトークンの取得を処理します。名前およびキーは、前のセクションで説明したようにポータルから取得されます。

最初に、**IProblemSolver** コントラクト コードをサービスからクライアント プロジェクトに対して参照またはコピーします。

次に、クライアントの **Main** メソッドのコードを置き換えます。ここでも、プレースホルダー テキストを実際の Service Bus サービスの名前空間およびキーに置き換えてください。

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

エンドポイントの定義は App.config ファイルに移動します。次のコード スニペットは (先に示したコードと同じです)、**system.serviceModel** 要素のすぐ下に配置する必要があります。前の例と同じように、プレースホルダーを実際の Service Bus サービスの名前空間とキーに置き換えてください。

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

<h2>次のステップ</h2>

これで、Service Bus **Relay** サービスの基本を学習できました。さらに詳細な情報が必要な場合は、次のリンク先を参照してください。

-   サービスの構築: [Service Bus 用のサービスの構築に関するページ][]。
-   クライアントの構築: [Service Bus クライアント アプリケーションの作成に関するページ][]。
-   Service Bus のサンプル: [Azure のサンプル][]からダウンロードします。

  [次のステップ]: #next_steps
  [サービス バス リレーとは]: #what-is
  [サービス名前空間の作成]: #create_namespace
  [名前空間の既定の管理資格情報の取得]: #obtain_credentials
  [サービス バス NuGet パッケージの取得]: #get_nuget_package
  [方法: Service Bus を使用して SOAP Web サービスを TCP で公開および使用する]: #how_soap
  [Azure 管理ポータル]: http://manage.windowsazure.com
   [サービス バス用のサービスの構築に関するページ]: http://msdn.microsoft.com/ja-jp/library/windowsazure/ee173564.aspx
  [サービス バス クライアント アプリケーションの作成に関するページ]: http://msdn.microsoft.com/ja-jp/library/windowsazure/ee173543.aspx
  [Azure のサンプル]: http://code.msdn.microsoft.com/windowsazure
