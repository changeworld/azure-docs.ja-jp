<properties 
    pageTitle="Service Bus トピックの使用方法 (.NET) - Azure" 
    description="Azure での Service Bus のトピックとサブスクリプションの使用方法について学習します。コード サンプルは .NET アプリケーション向けに作成されています。" 
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
    ms.date="02/26/2015" 
    ms.author="sethm"/>





# サービス バス トピック/サブスクリプションの使用方法

このガイドでは、Service Bus のトピックとサブスクリプションの使用方法について説明します。サンプルは C\# で記述され、.NET API を利用しています。ここでは、**トピックとサブスクリプションの作成、サブスクリプション フィルターの作成、トピックへのメッセージの送信**、**サブスクリプションからのメッセージの受信**、**トピックとサブスクリプションの削除**などのシナリオについて説明します。トピックとサブスクリプションの詳細については、「[次のステップ][]」をご覧ください。

[AZURE.INCLUDE [create-account-note](../includes/create-account-note.md)]

[AZURE.INCLUDE [howto-service-bus-topics](../includes/howto-service-bus-topics.md)]

## サービス バスを使用するためのアプリケーションの構成

Service Bus を使用するアプリケーションを作成するときには、Service Bus アセンブリに対する参照を追加して、対応する名前空間を含める必要があります。

## サービス バス NuGet パッケージの取得

Service Bus **NuGet** パッケージは、
Service Bus API を取得し、Service Bus の依存関係をすべて備えたアプリケーションを構成する
最も簡単な方法です。NuGet Visual Studio 拡張機能を使用すると、Visual Studio と Visual Studio Express でのライブラリやツールのインストールと更新を簡単に行うことができます。
Visual Studio Express をインストールすることもできます。サービス バス NuGet パッケージは、Service Bus API を取得し、サービス バス依存関係をすべて備えたアプリケーションを構成する最も簡単な方法です。

アプリケーションに NuGet パッケージをインストールするには、次のステップを行います。

1.  ソリューション エクスプローラーで、[**参照**] を右クリックし、
    [**NuGet パッケージの管理**] をクリックします。
2.  "Service Bus" を検索し、[**Microsoft Azure Service Bus**] 項目をクリックします。[**インストール**] をクリックしてインストールを完了し、
    このダイアログを閉じます。

    ![][7]

これで、Service Bus に対応するコードを作成する準備ができました。

## Service Bus の接続文字列の設定方法

Service Bus では、接続文字列を使用してエンドポイントと資格情報を格納します。接続文字列は、コード内にハードコーディングするのではなく、構成ファイルの中で指定します。

- Azure のクラウド サービスを使用するときには、Azure サービス構成システム (`*.csdef` と `*.cscfg` ファイル) を使用して接続文字列を格納することをお勧めします。
- Azure の Web サイトまたは Azure の仮想マシンを使用する場合には、.NET 構成システム ( `web.config` ファイルなど) を使用して接続文字列を格納することをお勧めします。

いずれの場合でも、このガイドで後ほど説明する  `CloudConfigurationManager.GetSetting` メソッドを使用して接続文字列を取得できます。

### クラウド サービスを使用する場合の接続文字列の構成

サービス構成メカニズムは、Azure Cloud Services プロジェクトに特有のものであり、これを使用すると、アプリケーションを再デプロイしなくても Azure 管理ポータルから構成設定を動的に変更できます。たとえば、サービス定義 (`*.csdef`) ファイルに設定を追加するには、以下のようにします。

    <ServiceDefinition name="Azure1">
    ...
        <WebRole name="MyRole" vmsize="Small">
            <ConfigurationSettings>
                <Setting name="Microsoft.ServiceBus.ConnectionString" />
            </ConfigurationSettings>
        </WebRole>
    ...
    </ServiceDefinition>

次に、サービス構成 (`*.cscfg`) ファイルで値を指定します。

    <ServiceConfiguration serviceName="Azure1">
    ...
        <Role name="MyRole">
            <ConfigurationSettings>
                <Setting name="Microsoft.ServiceBus.ConnectionString" 
                         value="Endpoint=sb://yourServiceNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey" />
            </ConfigurationSettings>
        </Role>
    ...
    </ServiceConfiguration>

前のセクションで説明したように管理ポータルから取得したキーの値を使用します。

### Web サイトまたは仮想マシンを使用する場合の接続文字列の構成

Web サイトまたは仮想マシンを使用する場合には、.NET 構成システム ( `web.config` など) を使用することをお勧めします。`<appSettings>` 要素を使用して接続文字列を格納します。

    <configuration>
        <appSettings>
            <add key="Microsoft.ServiceBus.ConnectionString"
                 value="Endpoint=sb://yourServiceNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey" />
        </appSettings>
    </configuration>

前のセクションで説明したように管理ポータルから取得した発行者とキーの値を使用します。

## トピックの作成方法

**NamespaceManager** クラスによって Service Bus のトピックとサブスクリプションに対する管理操作を実行できます。**NamespaceManager** クラスには、キューの作成、列挙、削除のためのメソッドが用意されています。 

この例では、Azure の **CloudConfigurationManager** クラスと接続文字列を使用して **NamespaceManager** オブジェクトを作成します。この接続文字列は、サービス バス サービス名前空間のベース アドレスと、それを管理する権限を備えた適切な資格情報で構成されます。この接続文字列は、次のようになっています。

    Endpoint=sb://<yourServiceNamespace>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey

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

**CreateTopic** メソッドにはオーバーロードがあり、トピックのプロパティを調整できるようになっています。たとえば、トピックに送信されるメッセージに対して既定の "有効期間" 値が適用されるように設定できます。このような設定は、**TopicDescription** クラスを使用して適用します。次の例では、名前が "TestTopic"、最大サイズが 5 GB、既定のメッセージ有効期間が 1 分であるトピックを作成する方法を示します。

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

**注:** 指定した名前のトピックがサービス名前空間に既に存在するかどうかを確認するには、**NamespaceManager** オブジェクトで **TopicExists** メソッドを使用できます。

<h2>サブスクリプションの作成方法</h2>

トピック サブスクリプションは、**NamespaceManager** クラスでも作成できます。サブスクリプションを指定し、サブスクリプションの仮想キューに渡すメッセージを制限するフィルターを設定できます。

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

サブスクリプションでサポートされるフィルターのうち、最も柔軟性の高いものが、
SQL92 のサブセットを実装する **SqlFilter** です。SQL フィルターは、トピックに発行されるメッセージのプロパティに対して適用されます。SQL フィルターで使用できる式の詳細については、[SqlFilter.SqlExpression][] 構文の説明をご覧ください。

次の例では、"HighMessages" という名前のサブスクリプションを作成し、
**SqlFilter** を適用します。このフィルターでは、カスタム プロパティ
**MessageNumber** が 3 を超えるメッセージのみが選択されます。

     // Create a "HighMessages" filtered subscription
     SqlFilter highMessagesFilter = 
        new SqlFilter("MessageNumber > 3");

     namespaceManager.CreateSubscription("TestTopic", 
        "HighMessages", 
        highMessagesFilter);

同様に、次の例では、
"LowMessages" という名前のサブスクリプションを作成し、**SqlFilter** を適用します。このフィルターでは、**MessageNumber** プロパティが 3 以下のメッセージのみが選択されます。

     // Create a "LowMessages" filtered subscription
     SqlFilter lowMessagesFilter = 
        new SqlFilter("MessageNumber <= 3");

     namespaceManager.CreateSubscription("TestTopic", 
        "LowMessages", 
        lowMessagesFilter);

メッセージが "TestTopic" に送信されると、そのメッセージは "AllMessages" トピック サブスクリプションにサブスクライブされた受信者に必ず配信され、さらにメッセージの内容に応じて、
"HighMessages" と "LowMessages" トピック サブスクリプションにサブスクライブされている受信者に対して選択的に配信されます。

## メッセージをトピックに送信する方法

アプリケーションでサービス バス トピックにメッセージを送信するには、
接続文字列を使用して **TopicClient** オブジェクトを作成します。

次のコードでは、前の例で **CreateFromConnectionString** API 呼び出しを使用して作成した "TestTopic" トピックのために **TopicClient** オブジェクトを作成する方法を示しています。

    string connectionString = 
        CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

    TopicClient Client = 
        TopicClient.CreateFromConnectionString(connectionString, "TestTopic");

    Client.Send(new BrokeredMessage());
 

サービス バス トピックに送信されたメッセージは、
**BrokeredMessage** クラスのインスタンスです。**BrokeredMessage** オブジェクトには (**Label**、**TimeToLive** などの) 標準的なプロパティ、アプリケーションに特有のカスタム プロパティの保持に使用するディクショナリ、任意のアプリケーション データの本体が備わっています。アプリケーションでは、
**BrokeredMessage** のコンストラクターにシリアル化可能なオブジェクトを渡すことによってメッセージの本文を設定できます。その後で、適切な**DataContractSerializer** を使用してオブジェクトをシリアル化します。この方法に代わって、
**System.IO.Stream** を使用できます。

以下の例では、前に示したコード スニペットで取得した
"TestTopic" の **TopicClient** にテスト メッセージを 5 件送信する方法を示しています。
各メッセージの **MessageNumber** プロパティの値がループの反復回数に応じてどのように変化するのかに注目してください (これによってメッセージを受信するサブスクリプションが決定されます)。

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

Service Bus トピックでは、最大 256 Kb のメッセージがサポートされます (標準とカスタムのアプリケーション プロパティが含まれるヘッダーの最大サイズは 64 Kb です)。トピックで保持されるメッセージ数には上限がありませんが、1 つのトピックで保持できるメッセージの合計サイズには上限があります。このキュー サイズは作成時に定義され、上限は 5 GB です。

## サブスクリプションからメッセージを受信する方法

サブスクリプションからメッセージを受信する場合、
**SubscriptionClient** オブジェクトを使用することをお勧めします。**SubscriptionClient** オブジェクトには、2 つの異なる動作モードがあります。**ReceiveAndDelete** と **PeekLock** です。

**ReceiveAndDelete** モードを使用する場合、受信が 1 回ずつの動作になります。つまり、Service Bus はサブスクリプション内のメッセージに対する読み取り要求を受け取ると、メッセージを読み取り中としてマークし、アプリケーションに返します。**ReceiveAndDelete** モードは最もシンプルなモデルであり、障害発生時にアプリケーション側でメッセージを処理しないことを許容できるシナリオに最適です。このことを理解するために、コンシューマーが受信要求を発行した後で、メッセージを処理する前にクラッシュしたというシナリオを考えてみましょう。Service Bus はメッセージを読み取り済みとしてマークするため、アプリケーションが再起動してメッセージの読み取りを再開すると、クラッシュ前に読み取られていたメッセージは見落とされることになります。

**PeekLock** モード (既定のモード) では、受信処理が 2 段階の動作になり、メッセージが失われることが許容できないアプリケーションに対応できます。サービス バスは要求を受け取ると、次に読み取られるメッセージを検索して、他のコンシューマーが受信できないようロックしてから、アプリケーションにメッセージを返します。アプリケーションがメッセージの処理を終えた後 (または後で処理するために確実に保存した後)、受信したメッセージに対して **Complete** を呼び出して受信処理の第 2 段階を完了します。Service
Bus が **Complete** の呼び出しを確認すると、メッセージが読み取り中としてマークされ、サブスクリプションから削除されます。

次の例では、 
既定の **PeekLock** モードを使用したメッセージの受信と処理の方法を示しています。別の **ReceiveMode** 値を指定する場合には、 
**CreateFromConnectionString** に別のオーバーロードを使用できます。この 
例では、**OnMessage** コールバックを使用して、 
"HighMessages" サブスクリプションにメッセージが到着するごとに処理しています。

    string connectionString = 
        CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

    SubscriptionClient Client = 
        SubscriptionClient.CreateFromConnectionString
                (connectionString, "TestTopic", "HighMessages");

    // Configure the callback options
    OnMessageOptions options = new OnMessageOptions();
    options.AutoComplete = false;
    options.AutoRenewTimeout = TimeSpan.FromMinutes(1);

    subscriptionClientHigh.OnMessage((message) =>
    {
        try
        {
            // Process message from subscription
            Console.WriteLine("\n**High Messages**");
            Console.WriteLine("Body: " + message.GetBody<string>());
            Console.WriteLine("MessageID: " + message.MessageId);
            Console.WriteLine("Message Number: " +
                message.Properties["MessageNumber"]);

            // Remove message from subscription
            message.Complete();
        }
        catch (Exception)
        {
            // Indicates a problem, unlock message in subscription
            message.Abandon();
        }
    }, options);

この例では、**OnMessageOptions** オブジェクトを使用して、 
**OnMessage** コールバックを構成します。**AutoComplete** は **false** に設定され、  受信したメッセージで **Complete** を呼び出すときに手動で制御できるようになります。
**AutoRenewTimeout** は 1 分に設定され、これによりクライアントは呼び出しがタイムアウトなるまで最大でメッセージを 1 分間待ってから、新しい呼び出しを行って メッセージを確認します。これにより、クライアントがメッセージを取得しない 課金対象の呼び出しを行う回数が減ります。

## アプリケーションのクラッシュと読み取り不能のメッセージを処理する方法

サービス バスには、アプリケーションにエラーが発生した場合や、メッセージの処理に問題がある場合に復旧を支援する機能が備わっています。受信側のアプリケーションが何らかの理由によってメッセージを処理できない場合には、受信したメッセージで (**Complete** メソッドの代わりに) **Abandon** メソッドを呼び出すことができます。このメソッドが呼び出されると、サブスクリプション内のメッセージのロックが解除され、メッセージが再度受信できる状態に変わります。このメッセージは、同じコンシューマー側アプリケーションまたは他のコンシューマー側アプリケーションで受信されます。

サブスクリプション内でロックされているメッセージにはタイムアウトも設定されています。アプリケーションがクラッシュした場合など、ロックがタイムアウトになる前にアプリケーションがメッセージの処理に失敗した場合には、メッセージのロックが自動的に解除され、再度受信できる状態に変わります。

メッセージが処理された後、**Complete** 要求が発行される前にアプリケーションがクラッシュした場合は、アプリケーションが再起動する際にメッセージが再配信されます。一般的に、この動作は **"1 回以上の処理"** と呼ばれます。つまり、すべてのメッセージが 1 回以上処理されますが、特定の状況では、同じメッセージが再配信される可能性があります。重複処理が許されないシナリオの場合、重複メッセージの配信を扱うロジックをアプリケーションに追加する必要があります。通常、この問題はメッセージの **MessageId** プロパティを使用して対処します。このプロパティは配信が試行された後も同じ値を保持します。

## トピックとサブスクリプションを削除する方法

次の例では、**TestTopic** という名前のトピックを **HowToSample** サービス名前空間から削除する方法を示しています。

     // Delete Topic
     namespaceManager.DeleteTopic("TestTopic");

トピックを削除すると、そのトピックに登録されたサブスクリプションもすべて削除されます。サブスクリプションは、個別に削除することもできます。次のコードでは、
**HighMessages** という名前のサブスクリプションを **TestTopic** トピックから削除する方法を示しています。

      namespaceManager.DeleteSubscription("TestTopic", "HighMessages");

## 次のステップ

これで、Service Bus のトピックとサブスクリプションの基本を学習できました。さらに詳細な情報が必要な場合は、次のリンク先をご覧ください。

-   MSDN リファレンス:[キュー、トピック、サブスクリプション][]
-   [SqlFilter][] の API のリファレンス
-   Service Bus キューとの間でメッセージを送受信する 実用アプリケーションの作成:[Service Bus が仲介するメッセージングに関する .NET チュートリアル][]。

  [次のステップ]: #nextsteps
  [サービス バス トピックとサブスクリプションとは]: #what-is
  [サービス名前空間の作成]: #create-namespace
  [名前空間の既定の管理資格情報の取得]: #obtain-creds
  [Service Bus を使用するようにアプリケーションを構成する]: #configure-app
  [方法:Service Bus の接続文字列を設定する]: #set-up-connstring
  [方法:接続文字列を構成する]: #config-connstring
  [方法:トピックを作成する]: #create-topic
  [方法:サブスクリプションを作成する]: #create-subscriptions
  [方法:メッセージをトピックに送信する]: #send-messages
  [方法:サブスクリプションからメッセージを受信する]: #receive-messages
  [方法:アプリケーションのクラッシュと読み取り不能のメッセージを処理する]: #handle-crashes
  [方法:トピックとサブスクリプションを削除する]: #delete-topics
  
  [トピックの概念]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/sb-topics-01.png
  [Azure 管理ポータル]: http://manage.windowsazure.com
  
  
  
  
  
  
  [7]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/getting-started-multi-tier-13.png
  
  [キュー、トピック、サブスクリプション]: http://msdn.microsoft.com/library/hh367516.aspx
  [SqlFilter]: http://msdn.microsoft.com/library/microsoft.servicebus.messaging.sqlfilter.aspx
  [Service Bus が仲介するメッセージングに関する .NET チュートリアル]: http://msdn.microsoft.com/library/hh367512.aspx

<!--HONumber=47-->
