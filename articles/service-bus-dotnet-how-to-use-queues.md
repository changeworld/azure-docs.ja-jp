<properties linkid="dev-net-how-to-service-bus-queues" urlDisplayName="Service Bus Queues" pageTitle="How to use Service Bus queues (.NET) - Azure" metaKeywords="Azure Service Bus queues, Azure queues, Azure messaging, Azure queues C#, Azure queues .NET" description="Learn how to use Service Bus queues in Azure. Code samples written in C# using the .NET API." metaCanonical="" services="service-bus" documentationCenter=".NET" title="How to Use Service Bus Queues" authors="sethm" solutions="" manager="timlt" editor="mattshel" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="09/24/2014" ms.author="sethm" />

# サービス バス キューの使用方法

<span>このガイドでは、サービス バス キューの使用方法について説明します。サンプルは C# で記述され、.NET API を利用しています。
紹介するシナリオは、
**キューの作成、メッセージの送受信、**
**およびキューの削除です**。キューの詳細については、「[次のステップ][次のステップ]」のセクションを参照してください。 </span>

[WACOM.INCLUDE [create-account-note](../includes/create-account-note.md)]

[WACOM.INCLUDE [howto-service-bus-queues](../includes/howto-service-bus-queues.md)]

## <span class="short-header">アプリケーションの構成</span>サービス バスを使用するためにアプリケーションを構成する

サービス バスを使用するアプリケーションを作成するときには、
サービス バス アセンブリに対する参照を追加して、対応する名前空間を
含める必要があります。

## <span class="short-header">NuGet パッケージの取得</span>サービス バス NuGet パッケージの取得

サービス バス **NuGet** パッケージは、Service Bus API を
取得し、サービス バス依存関係をすべて備えたアプリケーションを
構成する最も簡単な方法です。NuGet Visual Studio 拡張機能を使用すると、
Visual Studio および Visual Studio Express 2012 for Web でのライブラリや
ツールのインストールと更新を簡単に行うことができます。

アプリケーションに NuGet パッケージをインストールするには、次のステップを行います。

1.  ソリューション エクスプローラーで **[参照]** を右クリックし、
    **[NuGet パッケージの管理]** をクリックします。
2.  "WindowsAzure" を検索し、**[Azure の
    サービス バス]** 項目をクリックします。**[インストール]** をクリックし、インストールが
    完了したら、このダイアログを閉じます。

    ![][0]

これで、サービス バスに対応するコードを作成する準備ができました。

## <span class="short-header">接続文字列の設定</span>サービス バスの接続文字列の設定方法

サービス バスでは、接続文字列を使用してエンドポイントと資格情報を保存します。接続文字列は、コード内にハードコーディングするのではなく、構成ファイルの中で指定します。

-   Azure のクラウド サービスを使用するときには、Azure サービス構成システム (`*.csdef` および `*.cscfg` ファイル) を使用して接続文字列を格納することをお勧めします。
-   Azure の Web サイトまたは Azure の仮想マシンを使用する場合には、.NET 構成システム (`web.config` ファイルなど) を使用して接続文字列を格納することをお勧めします。

いずれの場合でも、このガイドで後ほど説明する `CloudConfigurationManager.GetSetting` メソッドを使用して接続文字列を取得できます。

### <a name="config-connstring"> </a> Cloud Services を使用する場合の接続文字列の構成

サービス構成メカニズムは、Azure クラウド サービスのプロジェクトに
特有のものであり、これを使用すると、アプリケーションを再デプロイ
することなく Azure 管理ポータルから構成設定を動的に
変更できます。たとえば、サービス定義 (`*.csdef`) ファイルに設定を追加するには、以下のようにします。

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

前のセクションで説明したように管理ポータルから取得した発行者および
キーの値を使用します。

### Web サイトまたは仮想マシンを使用する場合の接続文字列の構成

Web サイトまたは仮想マシンを使用する場合には、.NET 構成システム (`web.config` など) を使用することをお勧めします。`<appSettings>` 要素を使用して接続文字列を格納します。

    <configuration>
        <appSettings>
            <add key="Microsoft.ServiceBus.ConnectionString"
                 value="Endpoint=sb://[yourServiceNamespace].servicebus.windows.net/;SharedSecretIssuer=[issuerName];SharedSecretValue=[yourDefaultKey]" />
        </appSettings>
    </configuration>

前のセクションで説明したように管理ポータルから取得した発行者および
キーの値を使用します。

## <span class="short-header">キューの作成方法</span>キューの作成方法

**NamespaceManager** クラスによってサービス バス キューに対する管理操作を実行できます。**NamespaceManager** クラスには、キューの作成、列挙、および削除のためのメソッドが用意されています。

この例では、Azure の **CloudConfigurationManager** クラスと接続文字列を使用して **NamespaceManager** オブジェクトを作成します。
この接続文字列は、サービス バス サービス名前空間のベース アドレスと、それを管理する権限を
備えた適切な資格情報で構成されます。この接続文字列は、次のようになっています。

    Endpoint=sb://[yourServiceNamespace].servicebus.windows.net/;SharedSecretIssuer=[issuerName];SharedSecretValue=[yourDefaultKey]

たとえば、前のセクションの構成設定であれば、次のようになります。

    // Create the queue if it does not exist already
    string connectionString = 
        CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

    var namespaceManager = 
        NamespaceManager.CreateFromConnectionString(connectionString);

    if (!namespaceManager.QueueExists("TestQueue"))
    {
        namespaceManager.CreateQueue("TestQueue");
    }

**CreateQueue** メソッドにはオーバーロードがあり、(キューに送信されるメッセージに
対して既定の "time-to-live" 値が適用されるように設定するなど、) キューのプロパティを調整できるようになっています。このような
設定は、**QueueDescription** クラスを使用して適用します。次の例では、
名前が "TestQueue"、最大サイズが 5 GB、メッセージの既定の有効期間が 1 分である
キューを作成する方法を示しています。

    // Configure Queue Settings
    QueueDescription qd = new QueueDescription("TestQueue");
    qd.MaxSizeInMegabytes = 5120;
    qd.DefaultMessageTimeToLive = new TimeSpan(0, 1, 0);

    // Create a new Queue with custom settings
    string connectionString = 
        CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

    var namespaceManager = 
        NamespaceManager.CreateFromConnectionString(connectionString);

    if (!namespaceManager.QueueExists("TestQueue"))
    {
        namespaceManager.CreateQueue(qd);
    }

**注:** 指定した名前のキューがサービス名前空間に既に存在するかどうかを確認するには、
**NamespaceManager** オブジェクトで
**QueueExists** メソッドを使用できます。

## <span class="short-header">キューにメッセージを送信する</span>

アプリケーションではサービス バス キューにメッセージを送信するにあたって、接続文字列を使用して
**QueueClient** オブジェクトが作成されます。

以下のコードでは、上で **CreateFromConnectionString** API 呼び出しを
使用して作成した "TestQueue" というキューに **QueueClient** オブジェクトを作成する方法を示しています。

    string connectionString = 
        CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

    QueueClient Client = 
        QueueClient.CreateFromConnectionString(connectionString, "TestQueue");

    Client.Send(new BrokeredMessage());

サービス バス キューに送信されたメッセージ (およびサービス バス キューから
受信したメッセージ) は、**BrokeredMessage** クラスのインスタンスになります。**BrokeredMessage** オブジェクトには、
(**Label**、**TimeToLive** などの) 標準的なプロパティ、
アプリケーションに特有のカスタム プロパティの保持に使用するディクショナリ、
および任意のアプリケーション データの本体が備わっています。アプリケーションでは、
**BrokeredMessage** のコンストラクターにシリアル化可能な
オブジェクトを渡すことによってメッセージの本文を設定できます。その後で、
適切な **DataContractSerializer** を使用してオブジェクトをシリアル化します。この方法に代わって、
**System.IO.Stream** を使用できます。

以下の例では、上のコード スニペットで取得した "TestQueue" の
**QueueClient** にテスト メッセージを 5 件送信する方法を示しています。

     for (int i=0; i<5; i++)
     {
       // Create message, passing a string message for the body
       BrokeredMessage message = new BrokeredMessage("Test message " + i);

       // Set some addtional custom app-specific properties
       message.Properties["TestProperty"] = "TestValue";
       message.Properties["Message number"] = i;   

       // Send message to the queue
       Client.Send(message);
     }

サービス バス キューでは、最大 256 Kb までのメッセージをサポートしています (標準と
カスタムのアプリケーション プロパティが含まれるヘッダーは、
64 Kb が最大サイズです)。キューで保持されるメッセージ数には上限が
ありませんが、キュー 1 つあたりが保持できるメッセージの
合計サイズには上限があります。このキューのサイズはキューの作成時に定義します。
上限は 5 GB です。

## <span class="short-header">キューからメッセージを受信する</span>

キューからメッセージを受信する方法の中で最も簡単な方法は、
**QueueClient** オブジェクトを使用することです。このオブジェクトには、
2 つの異なる動作モードがあります。**ReceiveAndDelete** と **PeekLock** です。

**ReceiveAndDelete** モードを使用する場合、受信が 1 回ずつの動作になります。
つまり、サービス バスはキュー内のメッセージに対する読み取り要求
を受け取ると、メッセージを読み取り中としてマークし、
アプリケーションに返します。**ReceiveAndDelete** モードは最もシンプルなモデルであり、
障害発生時にアプリケーション側でメッセージを処理しないことを
許容できるシナリオに最適です。このことを理解するために、
コンシューマーが受信要求を発行した後で、メッセージを処理する前に
クラッシュしたというシナリオを考えてみましょう。サービス バスはメッセージを読み取り済みとして
マークするため、アプリケーションが再起動してメッセージの
読み取りを再開すると、クラッシュ前に読み取られていたメッセージは
見落とされることになります。

**PeekLock** モード (既定のモード) では、受信処理が 2 段階の動作になり、メッセージが失われることが許容できないアプリケーションに
対応することができます。サービス バスは要求を受け取ると、
次に読み取られるメッセージを検索して、他のコンシューマーが
受信できないようロックしてから、アプリケーションにメッセージを返します。アプリケーションが
メッセージの処理を終えた後 (または後で処理するために
確実に保存した後)、受信したメッセージに対して
**Complete** を呼び出して受信処理の第 2 段階を完了します。サービス バスが
**Complete** の呼び出しを確認すると、メッセージが読み取り中として
マークされ、キューから削除されます。

次の例では、既定の **PeekLock** モードを使用したメッセージの受信
および処理の方法を示しています。別の **ReceiveMode** 値を指定する場合には、**CreateFromConnectionString** に別のオーバーロードを使用できます。この例では、無限ループを作成し、"TestQueue" にメッセージが到着するごとに処理しています。

    Client.Receive();
     
    // Continuously process messages sent to the "TestQueue" 
    while (true) 
    {  
       BrokeredMessage message = Client.Receive();

       if (message != null)
       {
          try 
          {
             Console.WriteLine("Body: " + message.GetBody<string>());
             Console.WriteLine("MessageID: " + message.MessageId);
             Console.WriteLine("Test Property: " + 
                message.Properties["TestProperty"]);

             // Remove message from queue
             message.Complete();
          }
          catch (Exception)
          {
             // Indicate a problem, unlock message in queue
             message.Abandon();
          }
       }
    } 

## <span class="short-header">アプリケーションのクラッシュと読み取り不能のメッセージ</span>

サービス バスには、アプリケーションにエラーが発生した場合や、
メッセージの処理に問題がある場合に復旧を支援する機能が備わっています。受信側の
アプリケーションが何らかの理由によってメッセージを処理できない場合には、
受信したメッセージで (**Complete** メソッドの代わりに)
**Abandon** メソッドを呼び出すことができます。このメソッドが呼び出されると、
サービス バスによってキュー内のメッセージのロックが解除され、メッセージが
再度受信できる状態に変わります。メッセージを受信するアプリケーションは、
以前と同じものでも、別のものでもかまいません。

キュー内でロックされているメッセージにはタイムアウトも設定されています。
アプリケーションがクラッシュした場合など、
ロックがタイムアウトになる前にアプリケーションがメッセージの処理に失敗した場合には、サービス バスによって
メッセージのロックが自動的に解除され、再度受信できる状態に
変わります。

メッセージが処理された後、**Complete** 要求が発行される前に
アプリケーションがクラッシュした場合は、アプリケーションが再起動する際に
メッセージが再配信されます。一般的に、この動作は
**"1 回以上の処理"** と呼ばれます。つまり、すべてのメッセージが 1 回以上
処理されますが、特定の状況では、同じメッセージが再配信
される可能性があります。重複処理が許されないシナリオの場合、重複メッセージの
配信を扱うロジックをアプリケーションに追加する
必要があります。通常、この問題はメッセージの
**MessageId** プロパティを使用して対処します。このプロパティは配信が試行された後も同じ値を保持します。

## <span class="short-header">次のステップ</span>次のステップ

これで、サービス バスキューの基本を学習できました。さらに詳細な情報が必要な場合は、
次のリンク先を参照してください。

-   MSDN リファレンス: [キュー、トピック、およびサブスクリプション][キュー、トピック、およびサブスクリプション]
-   サービス バス キューとの間でメッセージを送受信する実用アプリケーションの作成: <a href="http://msdn.microsoft.com/ja-jp/library/windowsazure/hh367512.aspx">サービス バスが仲介するメッセージングに関する .NET チュートリアル</a>。

  [次のステップ]: #next-steps
  [create-account-note]: ../includes/create-account-note.md
  [howto-service-bus-queues]: ../includes/howto-service-bus-queues.md
  [0]: ./media/service-bus-dotnet-how-to-use-queues/getting-started-multi-tier-13.png
  [キュー、トピック、およびサブスクリプション]: http://msdn.microsoft.com/ja-jp/library/windowsazure/hh367516.aspx
