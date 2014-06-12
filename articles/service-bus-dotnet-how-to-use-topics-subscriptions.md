<properties linkid="dev-net-how-to-service-bus-topics" urlDisplayName="サービス バス トピック" pageTitle="サービス バス トピック (.NET) を使用する方法 - Azure" metaKeywords="Azure サービス バス トピックの作業を開始する, Azure 発行サブスクライブ メッセージング, Azure メッセージング トピックとサブスクリプション C#" description="Azure でサービス バス トピックとサブスクリプションを使用する方法について説明します。コード サンプルは .NET アプリケーション向けに作成されています。" metaCanonical="" services="service-bus" documentationCenter=".NET" title="サービス バス トピック/サブスクリプションの使用方法" authors=""  solutions="" writer="sethm" manager="dwrede" editor="mattshel"  />





# サービス バス トピック/サブスクリプションの使用方法

<span>このガイドでは、サービス バスのトピックとサブスクリプションの
使用方法について説明します。サンプルは C\# で記述され、.NET API を利用しています。ここでは、
**トピックとサブスクリプションの作成、サブスクリプション フィルターの作成、
トピックへのメッセージの送信**、**サブスクリプションからのメッセージの受信**、
**トピックとサブスクリプションの削除**などのシナリオについて説明します。トピックとサブスクリプションの詳細については、
「[次のステップ][]」を参照してください。</span>

[WACOM.INCLUDE [create-account-note](../includes/create-account-note.md)]

[WACOM.INCLUDE [howto-service-bus-topics](../includes/howto-service-bus-topics.md)]

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

<h2><span class="short-header">接続文字列の設定方法</span>サービス バス接続文字列の設定方法</h2>

サービス バスでは、接続文字列を使用してエンドポイントと資格情報を保存します。接続文字列は、コード内にハードコーディングするのではなく、構成ファイルの中で指定します。

- Azure のクラウド サービスを使用するときには、Azure サービス構成システム (`*.csdef` ファイルおよび `*.cscfg` ファイル) を使用して接続文字列を格納することをお勧めします。
- Azure の Web サイトまたは Azure の仮想マシンを使用する場合には、.NET 構成システム (`web.config` ファイルなど) を使用して接続文字列を格納することをお勧めします。

いずれの場合でも、このガイドで後ほど説明する `CloudConfigurationManager.GetSetting` メソッドを使用して接続文字列を取得できます。

### <a name="config-connstring"></a>クラウド サービスを使用する場合の接続文字列の構成

サービス構成メカニズムは、Azure のクラウド サービスのプロジェクトに
特有のものであり、これを使用すると、アプリケーションを
再展開することなく Azure の管理ポータルから構成設定を
動的に変更できます。たとえば、サービス定義 (`**.csdef`) ファイルに設定を追加するには、以下のようにします。

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

<h2><span class="short-header">トピックの作成方法</span>トピックの作成方法</h2>

**NamespaceManager** クラスによってサービス バスのトピックとサブスクリプションに対する管理操作を実行できます。**NamespaceManager** クラスには、キューの作成、列挙、および削除のためのメソッドが用意されています。

この例では、Azure の **CloudConfigurationManager** クラスと接続文字列を使用して **NamespaceManager** オブジェクトを
作成します。この接続文字列は、サービス バス サービス名前空間のベース アドレスと、それを管理する権限を備えた適切な
資格情報で構成されます。この接続文字列は、次のようになっています。

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

**CreateTopic** メソッドにはオーバーロードがあり、トピックのプロパティを調整できるように
なっています。たとえば、トピックに送信されるメッセージに対して既定の "Time to Live" 値が
適用されるように設定することができます。このような設定は、
**TopicDescription** クラスを使用して適用します。次の例では、名前が "TestTopic"、最大サイズが 5 GB、既定のメッセージの Time to Live が 1 分のトピックを作成する方法を示します。

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

**注:** **NamespaceManager** オブジェクトで **TopicExists** メソッドを
使用することで、指定した名前のトピックがサービス名前空間に既に存在するかどうかを
確認できます。

<h2><span class="short-header">サブスクリプションを作成する方法</span>サブスクリプションを作成する方法</h2>

トピック サブスクリプションは、**NamespaceManager** クラスでも作成できます。サブスクリプションを指定し、サブスクリプションの仮想キューに渡す
メッセージを制限するフィルターを設定することができます。

### 既定の (MatchAll) フィルターを適用したサブスクリプションの作成

**MatchAll** フィルターは、新しいサブスクリプションが作成されたときに
フィルターが指定されていない場合に使用される既定のフィルターです。**MatchAll** フィルターを
使用すると、トピックに発行されたすべてのメッセージがサブスクリプションの
仮想キューに置かれます。次の例では、"AllMessages" という
名前のサブスクリプションを作成し、既定の **MatchAll** フィルターを
使用します。

	string connectionString = 
	    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

	var namespaceManager = 
		NamespaceManager.CreateFromConnectionString(connectionString);

    if (!namespaceManager.SubscriptionExists("TestTopic", "AllMessages"))
    {
        namespaceManager.CreateSubscription("TestTopic", "AllMessages");
    }

### フィルターを適用したサブスクリプションの作成

トピックに送信されたメッセージのうち、特定のトピック サブスクリプション内に
表示されるメッセージを指定できるフィルターを設定することもできます。

サブスクリプションでサポートされるフィルターのうち、最も柔軟性の高いものが、
SQL92 のサブセットを実装する **SqlFilter** です。SQL フィルターは、
トピックに発行されるメッセージのプロパティに対して適用されます。
SQL フィルターで使用できる式の詳細については、
[SqlFilter.SqlExpression][] 構文の説明を参照してください。

次の例では、"HighMessages" という名前のサブスクリプションを作成し、
**SqlFilter** を適用します。このフィルターでは、カスタム プロパティ 
**MessageNumber** が 3 を超えるメッセージのみが選択されます。

     // Create a "HighMessages" filtered subscription
     SqlFilter highMessagesFilter = 
		new SqlFilter("MessageNumber > 3");

     namespaceManager.CreateSubscription("TestTopic", 
		"HighMessages", 
		highMessagesFilter);

同様に、次の例では "LowMessages" という名前の
サブスクリプションを作成し、**SqlFilter** を適用します。このフィルターでは、
**MessageNumber** プロパティが 3 以下のメッセージのみが選択されます。

     // Create a "LowMessages" filtered subscription
     SqlFilter lowMessagesFilter = 
		new SqlFilter("MessageNumber <= 3");

     namespaceManager.CreateSubscription("TestTopic", 
		"LowMessages", 
		lowMessagesFilter);

メッセージが "TestTopic" に送信されると、そのメッセージは
AllMessages トピック サブスクリプションにサブスクライブされた受信者に
必ず配信され、さらにメッセージの内容に応じて、"HighMessages" および
"LowMessages" トピック サブスクリプションにサブスクライブされている受信者に対して
選択的に配信されます。

<h2><span class="short-header">トピックへのメッセージの送信</span>トピックにメッセージを送信する方法</h2>

アプリケーションでサービス バス トピックにメッセージを送信するには、
接続文字列を使用して **TopicClient** オブジェクトを作成します。

次のコードでは、前の例で **CreateFromConnectionString** API 呼び出しを
使用して作成した "TestTopic" トピックのために **TopicClient** オブジェクトを作成する方法を示しています。

	string connectionString = 
	    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

   	TopicClient Client = 
		TopicClient.CreateFromConnectionString(connectionString, "TestTopic");

	Client.Send(new BrokeredMessage());
 

サービス バス トピックに送信されたメッセージは、
**BrokeredMessage** クラスのインスタンスです。
**BrokeredMessage** オブジェクトには、(**Label** や **TimeToLive**
などの) 標準的なプロパティ、アプリケーションに特有のカスタム プロパティの保持に
使用するディクショナリ、および任意のアプリケーション データの本体が
備わっています。アプリケーションでは、
**BrokeredMessage** のコンストラクターにシリアル化可能なオブジェクトを渡すことによってメッセージの本文を設定できます。その後で、適切な **DataContractSerializer** を使用してオブジェクトをシリアル化します。この方法に代わって、
**System.IO.Stream** を使用できます。

次の例では、前に示したコード スニペットで取得した "TestTopic" **TopicClient** に
テスト メッセージを 5 通送信する方法を示しています。
各メッセージの **MessageNumber** プロパティの値がループの反復回数に応じて
どのように変化するのかに注目してください (これによってメッセージを受信する
サブスクリプションが決定されます)。

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

サービス バス トピックでは、最大 256 Kb のメッセージがサポートされます (標準と
カスタムのアプリケーション プロパティが含まれるヘッダーの
最大サイズは 64 Kb です)。トピックで保持されるメッセージ数には
上限がありませんが、1 つのトピックで保持できるメッセージの合計サイズには
上限があります。このキューのサイズはキューの作成時に定義します。
上限は 5 GB です。

<h2><span class="short-header">サブスクリプションからのメッセージの受信</span>サブスクリプションからメッセージを受信する方法</h2>

最も簡単にサブスクリプションからメッセージを受信する方法は、
**SubscriptionClient** オブジェクトを使用する方法です。**SubscriptionClient** オブジェクトには、2 つの異なる動作モードがあります。**ReceiveAndDelete** と **PeekLock** です。

**ReceiveAndDelete** モードを使用する場合、受信が 1 回ずつの
動作になります。つまり、サービス バスはサブスクリプション内のメッセージに対する
読み取り要求を受け取ると、メッセージを読み取り中としてマークし、
アプリケーションに返します。**ReceiveAndDelete** モードは最もシンプルな
モデルであり、障害発生時にアプリケーション側でメッセージを処理しないことを
許容できるシナリオに最適です。このことを理解するために、
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
マークされ、サブスクリプションから削除されます。

次の例では、既定の **PeekLock** モードを使用したメッセージの受信
および処理の方法を示しています。別の **ReceiveMode** 値を指定する場合には、**CreateFromConnectionString** に別のオーバーロードを使用できます。この例では、無限ループを作成し、"HighMessages" サブスクリプションにメッセージが到着したときにメッセージを処理しています。"HighMessages" サブスクリプションのパスが、
"<*topicpath*\>/subscriptions/<*subscription name*\>" という
形式で指定されていることに注意してください。

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

<h2><span class="short-header">アプリケーションのクラッシュと読み取り不能のメッセージ</span>アプリケーションのクラッシュおよび読み取り不能のメッセージを処理する方法</h2>

サービス バスには、アプリケーションにエラーが発生した場合や、メッセージの処理に
問題がある場合に復旧を支援する機能が備わっています。受信側の
アプリケーションがなんらかの理由によってメッセージを処理できない場合には、
受信したメッセージで (**Complete** メソッドの代わりに) **Abandon** 
メソッドを呼び出すことができます。このメソッドが呼び出されると、サブスクリプション内の
メッセージのロックが解除され、メッセージが再度受信できる状態に変わります。
このメッセージは、同じコンシューマー側アプリケーションまたは他のコンシューマー側
アプリケーションで受信されます。

サブスクリプション内でロックされているメッセージにはタイムアウトも設定されています。
アプリケーションがクラッシュした場合など、ロックがタイムアウトになる前に
アプリケーションがメッセージの処理に失敗した場合には、メッセージのロックが自動的に解除され、
再度受信できる状態に変わります。

メッセージが処理された後、**Complete** 要求が発行される前に
アプリケーションがクラッシュした場合は、アプリケーションが再起動する際に
メッセージが再配信されます。一般的に、
この動作は **"1 回以上の処理"** と呼ばれます。つまり、すべての
メッセージが 1 回以上処理されますが、特定の状況では、同じメッセージが
再配信される可能性があります。重複処理が許されないシナリオの場合、重複メッセージの配信を
扱うロジックをアプリケーションに追加する必要があります。通常、この問題はメッセージの 
**MessageId** プロパティを使用して対処します。このプロパティは配信が
試行された後も同じ値を保持します。

<h2><span class="short-header">トピックとサブスクリプションの削除</span>トピックとサブスクリプションを削除する方法</h2>

次の例では、
**TestTopic** という名前のトピックを **HowToSample** サービス名前空間から削除する方法を示しています。

     // Delete Topic
     namespaceManager.DeleteTopic("TestTopic");

トピックを削除すると、そのトピックに登録されたサブスクリプションも
すべて削除されます。サブスクリプションは、個別に削除することもできます。次のコードでは、
**HighMessages** という名前のサブスクリプションを "**TestTopic**" トピックから削除する方法を示しています。

      namespaceManager.DeleteSubscription("TestTopic", "HighMessages");

<h2><span class="short-header">次のステップ</span>次のステップ</h2>

これで、サービス バスのトピックとサブスクリプションの基本を学習できました。さらに詳細な情報が
必要な場合は、次のリンク先を参照してください。

-   MSDN リファレンス: [キュー、トピック、およびサブスクリプション][]
-   [SqlFilter][] の API リファレンス
-   サービス バス キューとの間でメッセージを送受信する実用アプリケーションの
    作成: [サービス バスが仲介するメッセージングに関する .NET
    チュートリアル][]

  [次のステップ]: #nextsteps
  [サービス バス トピックとサブスクリプションとは]: #what-is
  [サービス名前空間の作成]: #create-namespace
  [名前空間の既定の管理資格情報の取得]: #obtain-creds
  [サービス バスを使用するようにアプリケーションを構成する]: #configure-app
  [How to: サービス バス接続文字列の設定]: #set-up-connstring
  [How to: 接続文字列を構成する]: #config-connstring
  [How to: トピックを作成する]: #create-topic
  [How to: サブスクリプションを作成する]: #create-subscriptions
  [How to: メッセージをトピックに送信する]: #send-messages
  [How to: サブスクリプションからメッセージを受信する]: #receive-messages
  [How to: アプリケーションのクラッシュと読み取り不能のメッセージを処理する]: #handle-crashes
  [How to: トピックとサブスクリプションを削除する]: #delete-topics
  
  [トピックの概念]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/sb-topics-01.png
  [Azure の管理ポータル]: http://manage.windowsazure.com
  
  
  
  
  
  
  [7]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/getting-started-multi-tier-13.png
  
  [キュー、トピック、およびサブスクリプション]: http://msdn.microsoft.com/ja-jp/library/hh367516.aspx
  [SqlFilter]: http://msdn.microsoft.com/ja-jp/library/microsoft.servicebus.messaging.sqlfilter.aspx
  [サービス バスが仲介するメッセージングに関する .NET チュートリアル]: http://msdn.microsoft.com/ja-jp/library/windowsazure/hh367512.aspx

