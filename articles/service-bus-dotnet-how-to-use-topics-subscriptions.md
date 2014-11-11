<properties linkid="dev-net-how-to-service-bus-topics" urlDisplayName="Service Bus Topics" pageTitle="How to use Service Bus topics (.NET) - Azure" metaKeywords="Get started Azure Service Bus topics, Azure publish subscribe messaging, Azure messaging topics and subscriptions C# " description="Learn how to use Service Bus topics and subscriptions in Azure. Code samples are written for .NET applications. " metaCanonical="" services="service-bus" documentationCenter=".NET" title="How to Use Service Bus Topics/Subscriptions" authors="sethm" solutions="" manager="timlt" editor="mattshel" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="09/24/2014" ms.author="sethm" />

# サービス バス トピック/サブスクリプションの使用方法

<span>このガイドでは、サービス バスのトピックとサブスクリプションの使用方法について説明します。サンプルは C# で記述され、.NET API を利用しています。ここでは、**トピックとサブスクリプションの作成、サブスクリプション フィルターの作成**、トピックへの**メッセージの送信、サブスクリプションからのメッセージの受信**、**トピックとサブスクリプションの削除**などのシナリオについて説明します。トピックとサブスクリプションの詳細については、「[次のステップ][次のステップ]」を参照してください。 </span>

[WACOM.INCLUDE [create-account-note](../includes/create-account-note.md)]

[WACOM.INCLUDE [howto-service-bus-topics](../includes/howto-service-bus-topics.md)]

## <span class="short-header">アプリケーションの構成</span>サービス バスを使用するためにアプリケーションを構成する

サービス バスを使用するアプリケーションを作成するときには、サービス バス アセンブリに対する参照を追加して、対応する名前空間を含める必要があります。

## <span class="short-header">NuGet パッケージの取得</span>サービス バス NuGet パッケージの取得

サービス バス **NuGet** パッケージは、Service Bus API を取得し、サービス バス依存関係をすべて備えたアプリケーションを構成する最も簡単な方法です。NuGet Visual Studio 拡張機能を使用すると、Visual Studio および Visual Studio Express 2012 for Web でのライブラリやツールのインストールと更新を簡単に行うことができます。サービス バス NuGet パッケージは、Service Bus API を取得し、サービス バス依存関係をすべて備えたアプリケーションを構成する最も簡単な方法です。

アプリケーションに NuGet パッケージをインストールするには、次のステップを行います。

1.  ソリューション エクスプローラーで **[参照]** を右クリックし、
    **[NuGet パッケージの管理]** をクリックします。
2.  "WindowsAzure" を検索し、**[Azure のサービス バス]** 項目をクリックします。**[インストール]** をクリックし、インストールが完了したら、このダイアログを閉じます。

    ![][0]

これで、サービス バスに対応するコードを作成する準備ができました。

## <span class="short-header">接続文字列の設定方法</span>サービス バス接続文字列の設定方法

サービス バスでは、接続文字列を使用してエンドポイントと資格情報を保存します。接続文字列は、コード内にハードコーディングするのではなく、構成ファイルの中で指定します。

-   Azure のクラウド サービスを使用するときには、Azure サービス構成システム (`*.csdef` および `*.cscfg` ファイル) を使用して接続文字列を格納することをお勧めします。
-   Azure の Web サイトまたは Azure の仮想マシンを使用する場合には、.NET 構成システム (`web.config` ファイルなど) を使用して接続文字列を格納することをお勧めします。

いずれの場合でも、このガイドで後ほど説明する `CloudConfigurationManager.GetSetting` メソッドを使用して接続文字列を取得できます。

### <a name="config-connstring"> </a> Cloud Services を使用する場合の接続文字列の構成

サービス構成メカニズムは、Azure クラウド サービスのプロジェクトに特有のものであり、これを使用すると、アプリケーションを再デプロイすることなく Azure 管理ポータルから構成設定を動的に変更できます。たとえば、サービス定義 (`*.csdef`) ファイルに設定を追加するには、以下のようにします。

    <ServiceDefinition name="WindowsAzure1">
    ...
        <WebRole name="MyRole" vmsize="Small">
            <ConfigurationSettings>
                <Setting name="Microsoft.ServiceBus.ConnectionString" />
            </ConfigurationSettings>
        </WebRole>
    ...
    </ServiceDefinition>

次に、サービス構成 (`*.cscfg`) ファイルで値を指定します。

    <ServiceConfiguration serviceName="WindowsAzure1">
    ...
        <Role name="MyRole">
            <ConfigurationSettings>
                <Setting name="Microsoft.ServiceBus.ConnectionString" 
                         value="Endpoint=sb://[yourServiceNamespace].servicebus.windows.net/;SharedSecretIssuer=[issuerName];SharedSecretValue=[yourDefaultKey]" />
            </ConfigurationSettings>
        </Role>
    ...
    </ServiceConfiguration>

前のセクションで説明したように管理ポータルから取得した発行者およびキーの値を使用します。

### Web サイトまたは仮想マシンを使用する場合の接続文字列の構成

Web サイトまたは仮想マシンを使用する場合には、.NET 構成システム (`web.config` など) を使用することをお勧めします。`<appSettings>` 要素を使用して接続文字列を格納します。

    <configuration>
        <appSettings>
            <add key="Microsoft.ServiceBus.ConnectionString"
                 value="Endpoint=sb://[yourServiceNamespace].servicebus.windows.net/;SharedSecretIssuer=[issuerName];SharedSecretValue=[yourDefaultKey]" />
        </appSettings>
    </configuration>

前のセクションで説明したように管理ポータルから取得した発行者およびキーの値を使用します。

## <span class="short-header">トピックの作成方法</span>トピックの作成方法

**NamespaceManager** クラスによってサービス バスのトピックとサブスクリプションに対する管理操作を実行できます。**NamespaceManager** クラスには、キューの作成、列挙、および削除のためのメソッドが用意されています。

この例では、Azure の **CloudConfigurationManager** クラスと接続文字列を使用して **NamespaceManager** オブジェクトを作成します。
この接続文字列は、サービス バス サービス名前空間のベース アドレスと、それを管理する権限を
備えた適切な資格情報で構成されます。この接続文字列は、次のようになっています。

    Endpoint=sb://<yourServiceNamespace>.servicebus.windows.net/;SharedSecretIssuer=<issuerName>;SharedSecretValue=<yourDefaultKey>

たとえば、前のセクションの構成設定であれば、次のようになります。

    // Create the topic if it does not exist already
    string connectionString = 
        CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

    var namespaceManager = 
        NamespaceManager.CreateFromConnectionString(connectionString);

    if (!namespaceManager.TopicExists("TestTopic"))
    {
        namespaceManager.CreateTopic("TestTopic");
    }

**CreateTopic** メソッドにはオーバーロードがあり、トピックのプロパティを調整できるようになっています。たとえば、トピックに送信されるメッセージに対して既定の "有効期間" 値が適用されるように設定することができます。このような設定は、**TopicDescription** クラスを使用して適用します。次の例では、名前が "TestTopic"、最大サイズが 5 GB、既定のメッセージ有効期間が 1 分であるトピックを作成する方法を示します。

    // Configure Topic Settings
    TopicDescription td = new TopicDescription("TestTopic");
    td.MaxSizeInMegabytes = 5120;
    td.DefaultMessageTimeToLive = new TimeSpan(0, 1, 0);

    // Create a new Topic with custom settings
    string connectionString = 
        CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

    var namespaceManager = 
        NamespaceManager.CreateFromConnectionString(connectionString);

    if (!namespaceManager.TopicExists("TestTopic"))
    {
        namespaceManager.CreateTopic(td);
    }

**注:** **NamespaceManager** オブジェクトで **TopicExists** メソッドを使用することで、指定した名前のトピックがサービス名前空間に既に存在するかどうかを確認できます。

## <span class="short-header">サブスクリプションを作成する方法</span>サブスクリプションを作成する方法

トピック サブスクリプションは、**NamespaceManager** クラスでも作成できます。サブスクリプションを指定し、サブスクリプションの仮想キューに渡すメッセージを制限するフィルターを設定することができます。

### 既定の (MatchAll) フィルターを適用したサブスクリプションの作成

**MatchAll** フィルターは、新しいサブスクリプションの作成時にフィルターが指定されていない場合に使用される既定のフィルターです。**MatchAll** フィルターを使用すると、トピックに発行されたすべてのメッセージがサブスクリプションの仮想キューに置かれます。次の例では、"AllMessages" という名前のサブスクリプションを作成し、既定の **MatchAll** フィルターを使用します。

    string connectionString = 
        CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

    var namespaceManager = 
        NamespaceManager.CreateFromConnectionString(connectionString);

    if (!namespaceManager.SubscriptionExists("TestTopic", "AllMessages"))
    {
        namespaceManager.CreateSubscription("TestTopic", "AllMessages");
    }

### フィルターを適用したサブスクリプションの作成

トピックに送信されたメッセージのうち、特定のトピック サブスクリプション内に表示されるメッセージを指定できるフィルターを設定することもできます。

サブスクリプションでサポートされるフィルターのうち、最も柔軟性の高いものが、SQL92 のサブセットを実装する**SqlFilter** です。SQL フィルターは、トピックに発行されるメッセージのプロパティに対して適用されます。SQL フィルターで使用できる式の詳細については、[SqlFilter.SqlExpression][SqlFilter.SqlExpression] 構文の説明を参照してください。

次の例では、"HighMessages" という名前のサブスクリプションを作成し、**SqlFilter** を適用します。このフィルターでは、カスタム プロパティ**MessageNumber** が 3 を超えるメッセージのみが選択されます。

     // Create a "HighMessages" filtered subscription
     SqlFilter highMessagesFilter = 
        new SqlFilter("MessageNumber > 3");

     namespaceManager.CreateSubscription("TestTopic", 
        "HighMessages", 
        highMessagesFilter);

同様に、次の例では "LowMessages" という名前のサブスクリプションを作成し、**SqlFilter** を適用します。このフィルターでは、**MessageNumber** プロパティが 3 以下のメッセージのみが選択されます。

     // Create a "LowMessages" filtered subscription
     SqlFilter lowMessagesFilter = 
        new SqlFilter("MessageNumber <= 3");

     namespaceManager.CreateSubscription("TestTopic", 
        "LowMessages", 
        lowMessagesFilter);

メッセージが "TestTopic" に送信されると、そのメッセージは "AllMessages" トピック サブスクリプションにサブスクライブされた受信者に必ず配信され、さらにメッセージの内容に応じて、"HighMessages" および "LowMessages" トピック サブスクリプションにサブスクライブされている受信者に対して選択的に配信されます。

## <span class="short-header">トピックへのメッセージの送信</span>トピックにメッセージを送信する方法

アプリケーションではサービス バス トピックにメッセージを送信するにあたって、接続文字列を使用して **TopicClient** オブジェクトが作成されます。

次のコードでは、前の例で **CreateFromConnectionString** API 呼び出しを使用して作成した "TestTopic" トピックのために **TopicClient** オブジェクトを作成する方法を示しています。

    string connectionString = 
        CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

    TopicClient Client = 
        TopicClient.CreateFromConnectionString(connectionString, "TestTopic");

    Client.Send(new BrokeredMessage());

サービス バス トピックに送信されたメッセージは、**BrokeredMessage** クラスのインスタンスです。**BrokeredMessage** オブジェクトには、(**Label**、**TimeToLive** などの) 標準的なプロパティ、アプリケーションに特有のカスタム プロパティの保持に使用するディクショナリ、および任意のアプリケーション データの本体が備わっています。アプリケーションでは、**BrokeredMessage** のコンストラクターにシリアル化可能なオブジェクトを渡すことによってメッセージの本文を設定できます。その後で、適切な **DataContractSerializer** を使用してオブジェクトをシリアル化します。この方法に代わって、**System.IO.Stream** を使用できます。

以下の例では、上のコード スニペットで取得した "TestTopic" の**TopicClient** にテスト メッセージを 5 件送信する方法を示しています。各メッセージの **MessageNumber** プロパティの値がループの反復回数に応じてどのように変化するのかに注目してください(これによってメッセージを受信するサブスクリプションが決定されます)。

     for (int i=0; i<5; i++)
     {
       // Create message, passing a string message for the body
       BrokeredMessage message = 
        new BrokeredMessage("Test message " + i);

       // Set additional custom app-specific property
       message.Properties["MessageNumber"] = i;

       // Send message to the topic
       Client.Send(message);
     }

サービス バス トピックでは、最大 256 Kb までのメッセージをサポートしています (標準とカスタムのアプリケーション プロパティが含まれるヘッダーは、64 Kb が最大サイズです)。トピックで保持されるメッセージ数には上限がありませんが、1 つのトピックで保持できるメッセージの合計サイズには上限があります。このキューのサイズはキューの作成時に定義します。上限は 5 GB です。

## <span class="short-header">サブスクリプションからのメッセージの受信</span>サブスクリプションからメッセージを受信する方法

最も簡単にサブスクリプションからメッセージを受信する方法は、**SubscriptionClient** オブジェクトを使用する方法です。**SubscriptionClient** オブジェクトには、2 つの異なる動作モードがあります。**ReceiveAndDelete** と **PeekLock** です。

**ReceiveAndDelete** モードを使用する場合、受信が 1 回ずつの動作になります。つまり、サービス バスはサブスクリプション内のメッセージに対する読み取り要求を受け取ると、メッセージを読み取り中としてマークし、アプリケーションに返します。**ReceiveAndDelete** モードは最もシンプルなモデルであり、障害発生時にアプリケーション側でメッセージを処理しないことを許容できるシナリオに最適です。このことを理解するために、コンシューマーが受信要求を発行した後で、メッセージを処理する前にクラッシュしたというシナリオを考えてみましょう。サービス バスはメッセージを読み取り済みとしてマークするため、アプリケーションが再起動してメッセージの読み取りを再開すると、クラッシュ前に読み取られていたメッセージは見落とされることになります。

**PeekLock** モード (既定のモード) では、受信処理が 2 段階の動作になり、メッセージが失われることが許容できないアプリケーションに対応することができます。サービス バスは要求を受け取ると、次に読み取られるメッセージを検索して、他のコンシューマーが受信できないようロックしてから、アプリケーションにメッセージを返します。アプリケーションがメッセージの処理を終えた後 (または後で処理するために確実に保存した後)、受信したメッセージに対して**Complete** を呼び出して受信処理の第 2 段階を完了します。サービス バスが**Complete** の呼び出しを確認すると、メッセージが読み取り中としてマークされ、サブスクリプションから削除されます。

次の例では、既定の **PeekLock** モードを使用したメッセージの受信および処理の方法を示しています。別の **ReceiveMode** 値を指定する場合には、**CreateFromConnectionString** に別のオーバーロードを使用できます。この例では、無限ループを作成し、"HighMessages" サブスクリプションにメッセージが到着したときにメッセージを処理しています。"HighMessages" サブスクリプションのパスが、"\<*トピックのパス*\>/subscriptions/\<*サブスクリプション名*\>" という形式で指定されていることに注意してください。

    string connectionString = 
        CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

    SubscriptionClient Client = 
        SubscriptionClient.CreateFromConnectionString
                (connectionString, "TestTopic", "HighMessages");

    Client.Receive();
     
    // Continuously process messages received from the HighMessages subscription 
    while (true) 
    {  
       BrokeredMessage message = Client.Receive();

       if (message != null)
       {
          try 
          {
             Console.WriteLine("Body: " + message.GetBody<string>());
             Console.WriteLine("MessageID: " + message.MessageId);
             Console.WriteLine("MessageNumber: " + 
                message.Properties["MessageNumber"]);

             // Remove message from subscription
             message.Complete();
          }
          catch (Exception)
          {
             // Indicate a problem, unlock message in subscription
             message.Abandon();
          }
       }
    } 

## <span class="short-header">アプリケーションのクラッシュと読み取り不能のメッセージ</span>アプリケーションのクラッシュおよび読み取り不能のメッセージを処理する方法

サービス バスには、アプリケーションにエラーが発生した場合や、メッセージの処理に問題がある場合に復旧を支援する機能が備わっています。受信側のアプリケーションが何らかの理由によってメッセージを処理できない場合には、受信したメッセージで (**Complete** メソッドの代わりに)**Abandon** メソッドを呼び出すことができます。このメソッドが呼び出されると、サブスクリプション内のメッセージのロックが解除され、メッセージが再度受信できる状態に変わります。このメッセージは、同じコンシューマー側アプリケーションまたは他のコンシューマー側アプリケーションで受信されます。

サブスクリプション内でロックされているメッセージにはタイムアウトも設定されています。アプリケーションがクラッシュした場合など、ロックがタイムアウトになる前にアプリケーションがメッセージの処理に失敗した場合には、メッセージのロックが自動的に解除され、再度受信できる状態に変わります。

メッセージが処理された後、**Complete** 要求が発行される前にアプリケーションがクラッシュした場合は、アプリケーションが再起動する際にメッセージが再配信されます。一般的に、この動作は**"1 回以上の処理"** と呼ばれます。つまり、すべてのメッセージが 1 回以上処理されますが、特定の状況では、同じメッセージが再配信される可能性があります。重複処理が許されないシナリオの場合、重複メッセージの配信を扱うロジックをアプリケーションに追加する必要があります。通常、この問題はメッセージの**MessageId** プロパティを使用して対処します。このプロパティは配信が試行された後も同じ値を保持します。

## <span class="short-header">トピックとサブスクリプションの削除</span>トピックとサブスクリプションを削除する方法

次の例では、**TestTopic** という名前のトピックを**HowToSample** サービス名前空間から削除する方法を示しています。

     // Delete Topic
     namespaceManager.DeleteTopic("TestTopic");

トピックを削除すると、そのトピックに登録されたサブスクリプションもすべて削除されます。サブスクリプションは、個別に削除することもできます。次のコードでは、**HighMessages** という名前のサブスクリプションを**TestTopic** トピックから削除する方法を示しています。

      namespaceManager.DeleteSubscription("TestTopic", "HighMessages");

## <span class="short-header">次のステップ</span>次のステップ

これで、サービス バスのトピックとサブスクリプションの基本を学習できました。さらに詳細な情報が必要な場合は、次のリンク先を参照してください。

-   MSDN リファレンス: [キュー、トピック、およびサブスクリプションに関するページ][キュー、トピック、およびサブスクリプションに関するページ]を参照してください。
-   [SqlFilter][SqlFilter] の API リファレンス。
-   サービス バス キューとの間でメッセージを送受信する実用アプリケーションの作成: [サービス バスが仲介するメッセージングに関する.NET チュートリアル][サービス バスが仲介するメッセージングに関する.NET チュートリアル]。

  [次のステップ]: #nextsteps
  [create-account-note]: ../includes/create-account-note.md
  [howto-service-bus-topics]: ../includes/howto-service-bus-topics.md
  [0]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/getting-started-multi-tier-13.png
  [キュー、トピック、およびサブスクリプションに関するページ]: http://msdn.microsoft.com/ja-jp/library/hh367516.aspx
  [SqlFilter]: http://msdn.microsoft.com/ja-jp/library/microsoft.servicebus.messaging.sqlfilter.aspx
  [サービス バスが仲介するメッセージングに関する.NET チュートリアル]: http://msdn.microsoft.com/ja-jp/library/hh367512.aspx
