<properties 
	pageTitle="Service Bus リレーの使用方法 (.NET) - Azure" 
	description="Azure Service Bus リレー サービスを使用して、別の場所にホストされた 2 つのアプリケーションに接続する方法について説明します。" 
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
	ms.topic="article" 
	ms.date="02/10/2015" 
	ms.author="sethm"/>







# サービス バス リレー サービスの使用方法

このガイドでは、Service Bus リレー サービスの使用方法について説明します。
サンプルは、C# で記述され、Windows Communication
Foundation API と、.NET Azure SDK の一部であるサービス バス アセンブリに含まれている拡張機能を使用しています。サービス バス リレーの詳細については、次のステップ セクションをご覧ください。

[AZURE.INCLUDE [create-account-note](../includes/create-account-note.md)]

## Service Bus リレーとは

Service Bus **リレー** サービスを使用して、Azure データセンターと独自の内部設置型エンタープライズ環境の両方で動作する**ハイブリッド アプリケーション**を構築できます。Service Bus リレーを使用することで、ファイアウォール接続を開放せず、
さらに企業ネットワークのインフラストラクチャ内部を変更せずに、企業のエンタープライズ ネットワーク内部にある Windows Communication Foundation (WCF) サービスを安全にパブリック クラウドに公開できます。

![Relay Concepts](./media/service-bus-dotnet-how-to-use-relay/sb-relay-01.png)

Service Bus リレーを使用して、既存のエンタープライズ環境内の WCF サービスをホストできます。その後で、その WCF サービスに対する受信セッションと受信要求のリッスンを Azure 内で動作する サービス バスにデリゲートできます。これにより、Azure で実行されるアプリケーション コードや、モバイル ワーカー環境またはエクストラネット パートナー環境に対して、これらのサービスを公開できます。サービス バスを使用すると、これらのサービスにアクセスできるユーザーを詳細なレベルで安全に制御できます。これは、既存のエンタープライズ ソリューションのデータとアプリケーション機能を公開してクラウドで活用するため、強力で安全な手段となります。

このガイドでは、サービス バス リレーを使用して、2 者間の安全な対話を実現する TCP チャネル バインドを使用して公開される WCF Web サービスの作成方法を示します。

## サービス名前空間の作成

Azure でサービス バス リレーを使用するには、最初にサービス名前空間を作成する必要があります。名前空間は、アプリケーション内で Service Bus リソースをアドレス指定するためのスコープ コンテナーを提供します。

サービス名前空間を作成するには:

1.  [Azure 管理ポータル][]へのログオン

2.  管理ポータルの左のナビゲーション ウィンドウで
    **[サービス バス]** をクリックします。

3.  管理ポータルの下のウィンドウの **[作成]** をクリックします。   

	![](./media/service-bus-dotnet-how-to-use-relay/sb-queues-13.png)

4.  **[新しい名前空間を追加する]** ダイアログで、名前空間の名前を入力します。その名前が使用できるかどうかがすぐに自動で確認されます。   

	![](./media/service-bus-dotnet-how-to-use-relay/sb-queues-04.png)


5.  入力した名前空間の名前が利用できることを確認できたら、名前空間をホストする国またはリージョンを選択します (コンピューティング リソースを展開する国またはリージョンと同じ国またはリージョンを必ず使用してください)。

	重要:アプリケーションをデプロイする予定の国またはリージョンと **同じ国/リージョン** を選択してください。そうすることで、パフォーマンスが最高になります。

6.	ダイアログ ボックスの他のフィールドは、既定値 (**[メッセージング]** と **[標準階層]**) のままにして、チェック マークをクリックします。これで、システムによってサービス名前空間が作成され、有効になります。システムがアカウントのリソースを準備し終わるまでに、数分間かかる場合があります。

	![](./media/service-bus-dotnet-how-to-use-relay/getting-started-multi-tier-27.png)

	作成した名前空間が管理ポータルに表示され、アクティブになります。これには少し時間がかかります。ステータスが **[アクティブ]** になるのを待ってから、次に進みます。

## 名前空間の既定の管理資格情報の取得

新規作成した名前空間に対してリレー接続の作成などの管理操作を実行するには、名前空間の共有アクセス署名 (SAS) の承認規則を構成する必要があります。SAS の詳細については、「[Service Bus による共有アクセス署名認証][]」をご覧ください。

1.  左のナビゲーション ウィンドウで、**[Service Bus]** ノードをクリックして、
    使用できる名前空間の一覧を表示します。   
	![](./media/service-bus-dotnet-how-to-use-relay/sb-queues-13.png)


2.  表示される一覧から先ほど作成した名前空間の名前をダブルクリックします。   
	![](./media/service-bus-dotnet-how-to-use-relay/sb-queues-09.png)


3.  ページの上部にある **[構成]** タブをクリックします。   
 
4.  サービス バス名前空間がプロビジョニングされると、**RootManageSharedAccessKey** に設定された **SharedAccessAuthorizationRule** と**KeyName** が既定で作成されます。このページには、そのキーと、既定のルールのプライマリ キーとセカンダリ キーが表示されます。 

## サービス バス NuGet パッケージの取得

サービス バス **NuGet** パッケージは、Service Bus API を取得し、サービス バス依存関係をすべて備えたアプリケーションを構成する最も簡単な方法です。NuGet Visual Studio 拡張機能を使用すると、Visual Studio と Visual Studio Express でのライブラリやツールのインストールと更新を簡単に行うことができます。サービス バス NuGet パッケージは、Service Bus API を取得し、サービス バス依存関係をすべて備えたアプリケーションを構成する最も簡単な方法です。

アプリケーションに NuGet パッケージをインストールするには、次のステップを行います。

1.  [ソリューション エクスプローラー] で、**[参照]** を右クリックし、**[NuGet パッケージの管理]** をクリックします。
2.  "Service Bus" を検索し、**[Microsoft Azure Service Bus]** 項目をクリックします。**[インストール]** をクリックし、インストールが完了したら、このダイアログを閉じます。

	![](./media/service-bus-dotnet-how-to-use-relay/getting-started-multi-tier-13.png)
  

## Service Bus を使用して SOAP Web サービスを TCP で公開、使用する方法

既存の WCF SOAP Web サービスを外部で使用するために公開するには、サービスのバインドとアドレスに変更を加える必要があります。WCF サービスの設定や構成によっては、構成ファイルを変更したり、コードを変更したりする必要があります。WCF を使用すると同じサービス上で複数のネットワーク エンドポイントを持つことができるので、既存の内部エンドポイントを保持しながら、同時に外部アクセス用のサービス バス エンドポイントも追加できます。

このタスクでは、単純な WCF サービスを構築し、そのサービスにサービス バス リスナーを追加します。この演習はある程度 Visual Studio に習熟したユーザーを対象としているため、プロジェクトの作成手順の詳しい内容については説明していません。代わりに、コードに重点を置いています。

次のステップを完了して環境を設定してから、後の手順を開始してください。

1.  Visual Studio 内で、"Client" と "Service" という 2 つのプロジェクトを含むコンソール アプリケーションをソリューション内に作成します。
2.  **Azure のサービス バス NuGet** パッケージを両方のプロジェクトに追加します。これにより、プロジェクトへの必要なアセンブリ参照がすべて追加されます。

### サービスの作成方法

最初に、サービス自体を作成します。すべての WCF サービスは、少なくとも次の 3 つの部分で構成されます。

-   交換されるメッセージと呼び出される操作を指定するコントラクトの定義。 
-   そのコントラクトの実装。
-   サービスをホストして複数のエンドポイントを公開するホスト。

このセクションでは、これらの各コンポーネントを処理するコード例を示します。

このコントラクトでは、2 つの数値を足して結果を返す**AddNumbers** という 1 つの処理を定義します。**IProblemSolverChannel** インターフェイスは、クライアントでプロキシの有効期間を簡単に管理できるようにするインターフェイスです。このようなインターフェイスを作成することがベスト プラクティスと考えられます。このコントラクト定義を別のファイルに格納しておき、そのファイルを "Client" プロジェクトと "Service" プロジェクトの両方から参照できようにすることをお勧めしますが、コードを両方のプロジェクトにコピーしてもよいでしょう。

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

### サービス ホストをプログラムで構成する方法

コントラクトと実装が適切に記述されたので、次にサービスをホストします。ホスティングは
**System.ServiceModel.ServiceHost** オブジェクトの内部で行います。このオブジェクトは、サービスのインスタンスを管理し、メッセージをリッスンするエンドポイントをホストします。次のコードでは、サービスに通常のローカル エンドポイントとサービス バス エンドポイントの両方を構成して、内部エンドポイントと外部エンドポイントの外観を並べて指定しています。文字列  *namespace* を実際の名前空間の名前に置き換え、 *yourKey* は前のセットアップ手順で取得した SAS キーに置き換えてください。 

    ServiceHost sh = new ServiceHost(typeof(ProblemSolver));

    sh.AddServiceEndpoint(
       typeof (IProblemSolver), new NetTcpBinding(), 
       "net.tcp://localhost:9358/solver");

    sh.AddServiceEndpoint(
       typeof(IProblemSolver), new NetTcpRelayBinding(), 
       ServiceBusEnvironment.CreateServiceUri("sb", "namespace", "solver"))
        .Behaviors.Add(new TransportClientEndpointBehavior {
              TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", "yourKey")});

    sh.Open();

    Console.WriteLine("Press ENTER to close");
    Console.ReadLine();

    sh.Close();

この例では、同じコントラクト実装にある 2 つのエンドポイントを作成します。1 つはローカルでもう 1 つは Service Bus を経由します。主な違いは、バインド (
ローカルの **NetTcpBinding**、Service Bus エンドポイントとそのアドレスの **NetTcpRelayBinding**) です。ローカル エンドポイントは、個々のポートを含むローカル ネットワーク アドレスを持ちます。サービス バス エンドポイントは、文字列 "sb"、使用する名前空間の名前、パス "solver" で構成されるエンドポイント アドレスを持ちます。全体としては
"sb://[serviceNamespace].servicebus.windows.net/solver" という URI になり、このサービス エンドポイントは完全修飾された外部 DNS 名を持つサービス バス TCP エンドポイントとして識別されます。
DNS 名があります。前に説明したようにプレースホルダーを置き換えたコードを "Service" アプリケーションの **Main** 関数に配置すると、このサービスが実際に機能します。サービスでサービス バスを排他的にリッスンするには、ローカル エンドポイントの宣言を削除します。

### サービス ホストを App.config ファイルで構成する方法

App.config ファイルを使用してホストを構成することもできます。この場合のサービス ホスト用コードは次のとおりです。

    ServiceHost sh = new ServiceHost(typeof(ProblemSolver));
    sh.Open();
    Console.WriteLine("Press ENTER to close");
    Console.ReadLine();
    sh.Close();

エンドポイントの定義は App.config ファイルに移動します。**NuGet** パッケージは、既に定義の範囲を App.config ファイルに追加したことに注意してください。これはサービス バスの必要な構成の拡張機能です。次のコード スニペットは (先に示したコードと同じです)、**system.serviceModel** 要素のすぐ下に配置する必要があります。このスニペットでは、プロジェクト C# 前空間の名前が "Service" であると想定しています。
プレースホルダーを実際のサービス バス サービスの名前空間と SAS キーに置き換えてください。

    <services>
        <service name="Service.ProblemSolver">
            <endpoint contract="Service.IProblemSolver"
                      binding="netTcpBinding"
                      address="net.tcp://localhost:9358/solver"/>
            <endpoint contract="Service.IProblemSolver"
                      binding="netTcpRelayBinding"
                      address="sb://namespace.servicebus.windows.net/solver"
                      behaviorConfiguration="sbTokenProvider"/>
        </service>
    </services>
    <behaviors>
        <endpointBehaviors>
            <behavior name="sbTokenProvider">
                <transportClientEndpointBehavior>
                    <tokenProvider>
                        <sharedAccessSignature keyName="RootManageSharedAccessKey" key="yourKey" />
                    </tokenProvider>
                </transportClientEndpointBehavior>
            </behavior>
        </endpointBehaviors>
    </behaviors>

以上の変更を加えたサービスは、以前と同じように開始されます。ただし、2 つのアクティブなエンドポイントは、1 つはローカルに存在し、もう 1 つはクラウド内でリッスンします。

### クライアントの作成方法

#### クライアントをプログラムで構成する方法

サービスを使用するために、
**ChannelFactory** オブジェクトを使用して WCF クライアントを作成できます。サービス バスでは、Access Control サービス (ACS) を使用して実装されたクレームベースのセキュリティ モデルを使用します。**TokenProvider** クラスは、いくつかの既知のトークン プロバイダーを返すファクトリ メソッドが組み込まれたセキュリティ トークン プロバイダーを表します。次の例では、**SharedSecretTokenProvider** を使用して共有シークレット資格情報を保持し、Access Control サービスからの適切なトークンの取得を処理します。名前とキーは、前のセクションで説明したようにポータルから取得されます。

最初に、**IProblemSolver** コントラクト コードをサービスからクライアント プロジェクトに対して参照またはコピーします。

次に、クライアントの **Main** メソッドのコードを置き換えます。ここでも、プレースホルダー テキストを実際のサービス バスの名前空間と SAS キーに置き換えてください。

    var cf = new ChannelFactory<IProblemSolverChannel>(
        new NetTcpRelayBinding(), 
        new EndpointAddress(ServiceBusEnvironment.CreateServiceUri("sb", "namespace", "solver")));

    cf.Endpoint.Behaviors.Add(new TransportClientEndpointBehavior
                { TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey","yourKey") });
     
    using (var ch = cf.CreateChannel())
    {
        Console.WriteLine(ch.AddNumbers(4, 5));
    }

これで、クライアントとサービスをコンパイルして実行できます (先にサービスを実行します)。クライアントはサービスを呼び出し、"9" を出力します。クライアントとサーバーを別々のコンピューターで実行しても (ネットワークが異なっても)、通信は維持されます。クライアント コードは、クラウド上でもローカルでも実行できます。

#### App.config ファイルでクライアントを構成する方法

App.config ファイルを使用してクライアントを構成することもできます。そのためのクライアント コードは次のとおりです。

    var cf = new ChannelFactory<IProblemSolverChannel>("solver");
    using (var ch = cf.CreateChannel())
    {
        Console.WriteLine(ch.AddNumbers(4, 5));
    }

エンドポイントの定義は App.config ファイルに移動します。次のコード スニペットは (先に示したコードと同じです)、**system.serviceModel** 要素のすぐ下に配置する必要があります。前の例と同じように、プレースホルダーを実際のサービス バスの名前空間と SAS キーに置き換えてください。

    <client>
        <endpoint name="solver" contract="Service.IProblemSolver"
                  binding="netTcpRelayBinding"
                  address="sb://namespace.servicebus.windows.net/solver"
                  behaviorConfiguration="sbTokenProvider"/>
    </client>
    <behaviors>
        <endpointBehaviors>
            <behavior name="sbTokenProvider">
                <transportClientEndpointBehavior>
                    <tokenProvider>
                        <sharedAccessSignature keyName="RootManageSharedAccessKey" key="yourKey" />
                    </tokenProvider>
                </transportClientEndpointBehavior>
            </behavior>
        </endpointBehaviors>
    </behaviors>

## 次のステップ

これで、サービス バス **リレー** サービスの基本を学習できました。さらに詳しい情報が必要な場合は、次のリンク先をご覧ください。

-   サービスの構築:[サービス バス用のサービスの構築][]。
-   クライアントの構築:[サービス バス クライアント アプリケーションの構築][]。
-   サービス バスのサンプル: [Azure のサンプル][] からのダウンロード。

  [サービス名前空間の作成]: #create_namespace
  [名前空間の既定の管理資格情報の取得]: #obtain_credentials
  [サービス バス NuGet パッケージの取得]: #get_nuget_package
  [方法:サービス バスを使用して SOAP Web サービスを  TCP で公開、使用する]: #how_soap
  [Azure 管理ポータル]: http://manage.windowsazure.com
  [Service Bus による共有アクセス署名認証]: http://msdn.microsoft.com/library/dn170477.aspx
  [サービス バス用のサービスの構築]: http://msdn.microsoft.com/library/ee173564.aspx
  [サービス バス クライアント アプリケーションの構築]: http://msdn.microsoft.com/library/ee173543.aspx
  [Azure のサンプル]: http://code.msdn.microsoft.com/windowsazure

<!--HONumber=47-->
