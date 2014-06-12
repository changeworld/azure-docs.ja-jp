<properties linkid="dev-net-how-to-service-bus-queues" urlDisplayName="サービス バス キュー" pageTitle="サービス バス キュー (.NET) の使用方法 - Azure" metaKeywords="Azure のサービス バス キュー, Azure のキュー, Azure のメッセージング, Azure キュー C#, Azure キュー .NET" description="Azure でのサービス バス キューの使用方法を学習します。.NET API を使用して C# で記述されたコード サンプル。" metaCanonical="" services="service-bus" documentationCenter=".NET" title="サービス バス キューの使用方法" authors=""  solutions="" writer="sethm" manager="dwrede" editor="mattshel"  />





# サービス バス キューの使用方法

<span>このガイドでは、サービス バス キューの使用方法について説明します。サンプルは C\# で記述され、.NET API を利用しています。紹介するシナリオは、
**キューの作成、メッセージの送受信**、および**キューの削除**です。キューの詳細については、「[次のステップ]」のセクションを参照してください。</span>

[WACOM.INCLUDE [create-account-note](../includes/create-account-note.md)]

[WACOM.INCLUDE [howto-service-bus-queues](../includes/howto-service-bus-queues.md)]

<h2><span class="short-header">アプリケーションの構成</span>サービス バスを使用するためにアプリケーションを構成する</h2>

サービス バスを使用するアプリケーションを作成するときには、
サービス バス アセンブリに対する参照を追加して、対応する名前空間を
含める必要があります。

<h2><span class="short-header">NuGet パッケージの取得</span>サービス バス NuGet パッケージの取得</h2>

サービス バス **NuGet** パッケージは、サービス バス API を取得し、
サービス バス依存関係をすべて備えたアプリケーションを構成する最も
簡単な方法です。NuGet Visual Studio 拡張機能を使用すると、
Visual Studio および Visual Studio Express 2012 for Web での
ライブラリやツールのインストールと更新を簡単に行うことができます。サービス バス NuGet パッケージは、サービス バス API を
取得し、サービス バス依存関係をすべて備えたアプリケーションを構成する最も
簡単な方法です。

アプリケーションに NuGet パッケージをインストールするには、次のステップを行います。

1. ソリューション エクスプローラーで **[参照]** を右クリックし、
    **[NuGet パッケージの管理]** をクリックします。
2. "WindowsAzure" を検索し、**[Azure のサービス バス]**
    項目をクリックします。**[インストール]** をクリックし、インストールが
    完了したら、このダイアログを閉じます。

    ![][7]

これで、サービス バスに対応するコードを作成する準備ができました。

<h2><span class="short-header">接続文字列の設定</span>サービス バスの接続文字列の設定方法</h2>

サービス バスでは、接続文字列を使用してエンドポイントと資格情報を保存します。接続文字列は、コード内にハードコーディングするのではなく、構成ファイルの中で指定します。

- Azure のクラウド サービスを使用するときには、Azure サービス構成システム (`*.csdef` ファイルおよび `*.cscfg` ファイル) を使用して接続文字列を格納することをお勧めします。
- Azure の Web サイトまたは Azure の仮想マシンを使用する場合には、.NET 構成システム (`web.config` ファイルなど) を使用して接続文字列を格納することをお勧めします。

いずれの場合でも、このガイドで後ほど説明する `CloudConfigurationManager.GetSetting` メソッドを使用して接続文字列を取得できます。

### <a name="config-connstring"></a>クラウド サービスを使用する場合の接続文字列の構成

サービス構成メカニズムは、Azure のクラウド サービスのプロジェクトに
特有のものであり、これを使用すると、アプリケーションを
再展開することなく Azure の管理ポータルから構成設定を
動的に変更できます。たとえば、サービス定義 (`*.csdef`) ファイルに設定を追加するには、以下のようにします。

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

前のセクションで説明したように管理ポータルから取得した発行者およびキーの
値を使用します。

### Web サイトまたは仮想マシンを使用する場合の接続文字列の構成

Web サイトまたは仮想マシンを使用する場合には、.NET 構成システム (`web.config` など) を使用することをお勧めします。`<appSettings>` 要素を使用して接続文字列を格納します。

	<configuration>
	    <appSettings>
		    <add key="Microsoft.ServiceBus.ConnectionString"
			     value="Endpoint=sb://[yourServiceNamespace].servicebus.windows.net/;SharedSecretIssuer=[issuerName];SharedSecretValue=[yourDefaultKey]" />
		</appSettings>
	</configuration>

前のセクションで説明したように管理ポータルから取得した発行者およびキーの
値を使用します。

<h2><span class="short-header">キューの作成方法</span>キューの作成方法</h2>

**NamespaceManager** クラスによってサービス バス キューに対する管理操作を実行できます。**NamespaceManager** クラスには、キューの作成、列挙、および削除のためのメソッドが用意されています。

この例では、Azure の **CloudConfigurationManager** クラスと接続文字列を使用して **NamespaceManager** オブジェクトを
作成します。この接続文字列は、サービス バス サービス名前空間のベース アドレスと、それを管理する権限を備えた適切な
資格情報で構成されます。この接続文字列は、次のようになっています。

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

**CreateQueue** メソッドにはオーバーロードがあり、(キューに送信されるメッセージに対して
既定の "Time to Live" 値が適用されるように設定するなど) キューのプロパティを調整できるようになっています。このような
設定は、**QueueDescription** クラスを使用して適用します。次の
例では、最大サイズが 5 GB で既定のメッセージの Time to Live が 1 分の
"TestQueue" という名前のキューを作成する方法を示します。

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

**注:** **NamespaceManager** オブジェクトの **QueueExists** メソッドを
使用すると、指定した名前のキューがサービス名前空間に既に存在するか
どうかを確認できます。

<h2><span class="short-header">キューにメッセージを送信する</span>キューにメッセージを送信する方法</h2>

アプリケーションではサービス バス キューにメッセージを送信するにあたって、
接続文字列を使用して **QueueClient** オブジェクトが作成されます。

以下のコードでは、上で **CreateFromConnectionString** API 呼び出しを
使用して作成した "TestQueue" というキューに **QueueClient** オブジェクトを作成する方法を示しています。

	string connectionString = 
	    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

    QueueClient Client = 
		QueueClient.CreateFromConnectionString(connectionString, "TestQueue");

	Client.Send(new BrokeredMessage());

サービス バス キューに送信された (およびキューから受信された) メッセージは
**BrokeredMessage** クラスのインスタンスです。**BrokeredMessage** オブジェクトには、
(**Label** や **TimeToLive** などの) 標準的なプロパティ、アプリケーションに特有の
カスタム プロパティの保持に使用するディクショナリ、および任意の
アプリケーション データの本体が備わっています。アプリケーションでは、**BrokeredMessage** のコンストラクターに
シリアル化可能なオブジェクトを渡すことによってメッセージの本文を設定できます。
その後で、適切な **DataContractSerializer** を使用して
オブジェクトをシリアル化します。この方法に代わって、
**System.IO.Stream** を使用できます。

次の例では、前に示したコード スニペットで取得した
"TestQueue" **QueueClient** にテスト メッセージを 5 通送信する方法を示しています。

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

サービス バス キューでは、最大 256 KB までのメッセージをサポートしています (標準と
カスタムのアプリケーション プロパティが含まれるヘッダーは、64 KB が最大サイズです)。キューで保持されるメッセージ数には上限が
ありませんが、キュー 1 つあたりが保持できるメッセージの合計サイズには
上限があります。このキューのサイズはキューの作成時に定義します。
上限は 5 GB です。

<h2><span class="short-header">キューからメッセージを受信する</span>キューからメッセージを受信する方法</h2>

キューからメッセージを受信する方法の中で最も簡単であるのは、
**QueueClient** オブジェクトを使用する方法です。これらのオブジェクトには、**ReceiveAndDelete**
および **PeekLock** の、2 つの異なる動作モードがあります。

**ReceiveAndDelete** モードを使用する場合、受信はシングル ショットの操作で、つまり、サービス バスがキュー内のメッセージの読み取り要求を受け取る場合、メッセージを消費済みとしてマークし、アプリケーションに返すことを意味します。**ReceiveAndDelete** モードは、最も単純なモデルで、障害発生時にメッセージを処理しないことをアプリケーションが許容できる場合に最適のシナリオです。このことを理解するために、
コンシューマーが受信要求を発行した後で、メッセージを処理する前に
クラッシュしたというシナリオを考えてみましょう。サービス バスはメッセージを読み取り済みとして
マークするため、アプリケーションが再起動してメッセージの読み取りを再開すると、
クラッシュ前に読み取られていたメッセージは見落とされることになります。

**PeekLock** モード (既定のモード) では、受信処理が 2 段階の動作になり、メッセージが失われることが許容できないアプリケーションに対応することができます。サービス バスは要求を受け取ると、次に
読み取られるメッセージを検索して、他のコンシューマーが受信できないよう
ロックしてから、アプリケーションにメッセージを返します。アプリケーションが
メッセージの処理を終えた後 (または後で処理するために
確実に保存した後)、受信したメッセージに対して **Complete** を
呼び出して受信処理の第 2 段階を完了します。サービス バスが
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

<h2><span class="short-header">アプリケーションのクラッシュと読み取り不能のメッセージ</span>アプリケーションのクラッシュおよび読み取り不能のメッセージを処理する方法</h2>

サービス バスには、アプリケーションにエラーが発生した場合や、メッセージの処理に
問題がある場合に復旧を支援する機能が備わっています。受信
側のアプリケーションがなんらかの理由によってメッセージを処理できない場合には、
受信したメッセージについて (**Complete** メソッドの代わりに)
**Abandon** メソッドを呼び出すことができます。このメソッドが呼び出されると、サービス バスによって
キュー内のメッセージのロックが解除され、メッセージが再度受信できる状態に
変わります。メッセージを受信するアプリケーションは、以前と同じものでも、
別のものでもかまいません。

キュー内でロックされているメッセージにはタイムアウトも設定されています。
アプリケーションがクラッシュした場合など、ロックがタイムアウトになる前に
アプリケーションがメッセージの処理に失敗した場合には、メッセージのロックが自動的に
解除され、再度受信できる状態に変わります。

メッセージが処理された後、**Complete** 要求が発行される前に
アプリケーションがクラッシュした場合は、アプリケーションが再起動する際に
メッセージが再配信されます。一般的に、
この動作は **"1 回以上の処理"** と呼ばれます。つまり、すべての
メッセージが 1 回以上処理されますが、特定の状況では、同じメッセージが
再配信される可能性があります。重複処理が許されないシナリオの場合、重複メッセージの配信を
扱うロジックをアプリケーションに追加する必要があります。通常、この問題はメッセージの
**MessageId** プロパティを使用して対処します。このプロパティは配信が
試行された後も同じ値を保持します。

<h2><span class="short-header">次のステップ</span>次のステップ</h2>

これで、サービス バス キューの基本を学習できました。さらに詳細な情報が
必要な場合は、次のリンク先を参照してください。

-   MSDN リファレンス: [キュー、トピック、およびサブスクリプション][]
-   サービス バス キューとの間でメッセージを送受信する実用アプリケーションの
    作成: [サービス バスが仲介するメッセージングに関する .NET 
    チュートリアル]。

  [次のステップ]: #next-steps
  [サービス バス キューとは]: #what-queues
  [サービス名前空間の作成]: #create-namespace
  [名前空間の既定の管理資格情報の取得]: #obtain-creds
  [サービス バスを使用するようにアプリケーションを構成する]: #configure-app
  [How to: サービス バス接続文字列の設定]: #set-up-connstring
  [How to: 接続文字列を構成する]: #config-connstring
  [How to: キューを作成する]: #create-queue
  [How to: メッセージをキューに送信する]: #send-messages
  [How to: キューからメッセージを受信する]: #receive-messages
  [How to: アプリケーションのクラッシュと読み取り不能のメッセージを処理する]: #handle-crashes
  [キューの概念]: ./media/service-bus-dotnet-how-to-use-queues/sb-queues-08.png
  [Azure の管理ポータル]: http://manage.windowsazure.com
  
  
  
  
  
  
  [7]: ./media/service-bus-dotnet-how-to-use-queues/getting-started-multi-tier-13.png
  [キュー、トピック、およびサブスクリプション]: http://msdn.microsoft.com/ja-jp/library/windowsazure/hh367516.aspx
  [サービス バスが仲介するメッセージングに関する .NET チュートリアル]: http://msdn.microsoft.com/ja-jp/library/windowsazure/hh367512.aspx

