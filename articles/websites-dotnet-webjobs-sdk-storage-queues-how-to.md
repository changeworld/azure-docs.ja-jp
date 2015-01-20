<properties linkid="" pageTitle="Web ジョブ SDK を使用して Azure キュー ストレージを操作する方法" metakeywords プロパティ ="Web ジョブ SDK Azure キュー ストレージ .NET C#" description="Web ジョブ SDK を使用して Azure キュー ストレージを操作する方法について説明します。キューの作成と削除のほか、キュー メッセージの挿入、ピーク、取得、削除を行います。" metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="web-sites,storage" documentationCenter=".NET" title="How to work with Azure queue storage using the WebJobs SDK" authors="tdykstra" manager="wpickett" editor="jimbe" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="10/13/2014" ms.author="tdykstra" />

# Web ジョブ SDK を使用して Azure キュー ストレージを操作する方法

このガイドは、Azure キュー ストレージ サービスと Azure の web ジョブ SDK、バージョン 1.0.0 を使用して、
C# コードを記述する一般的なシナリオを示しています。

前提として [Web ジョブ SDK の概要]と(../websites-webjobs-sdk-storage-queues-how-to) [基本的なタスクを実行する方法]は既に習得しているものとします。(../websites-dotnet-webjobs-sdk-get-started/)これには、Web ジョブ SDK の NuGet パッケージのインストール、Azure ストレージ アカウントの作成、ストレージ アカウントを参照する Web ジョブ SDK の接続文字列の作成などが含まれます。

ほとんどのコード スニペットは、この例のように JobHost オブジェクトを作成するコードではなく、関数のみを示しています。

		static void Main(string[] args)
		{
		    JobHost host = new JobHost();
		    host.RunAndBlock();
		}
		
## 目次

-   [キュー メッセージを受信したときに関数をトリガーする方法](#trigger)
	- 文字列のキュー メッセージ
	- POCO オブジェクトのキュー メッセージ
	- Async 関数
	- ポーリング アルゴリズム
	- 並列実行
	- キューまたはキュー メッセージのメタデータの取得
	- 正常なシャットダウン
-   [キュー メッセージの処理中にキュー メッセージを作成する方法](#createqueue)
	- 文字列のキュー メッセージ
	- POCO オブジェクトのキュー メッセージ
	- 複数のメッセージの作成
	- 関数本体でのキュー属性の使用
-   [キュー メッセージの処理中に Blob を読み書きする方法](#blobs)
	- 文字列のキュー メッセージ
	- POCO オブジェクトのキュー メッセージ
	- 関数本体での Blob 属性の使用
-   [有害メッセージの処理方法](#poison)
	- 有害メッセージの自動処理
	- 有害メッセージの手動処理
-   [構成オプションの設定方法](#config)
	- コード内での SDK 接続文字列の設定
	- QueueTrigger 設定の構成
	- 構成からのキューの名前の取得
-   [関数を手動でトリガーする方法](#manual)
-   [ログの記述方法](#logs)
-   [次のステップ](#nextsteps)

## <a id="trigger"></a> キュー メッセージを受信したときに関数をトリガーする方法

キュー メッセージを受信したときに SDK が呼び出す関数を記述するには、メッセージから何を取得したいかによって、文字列か POCO パラメーターと共にQueueTrigger 属性を使用します。属性コンストラクターが、ポーリングするキューの名前を指定する文字列パラメーターを取得します。[キューの名前を動的に設定]することもできます(#config)。

Web サイトが複数の VM 上で稼働している場合、Web ジョブは各マシン上で実行され、各マシンがトリガーを待って関数の実行を試行します。一部のシナリオでは、これによっていくつかの関数が同じデータを 2 回処理する場合があるため、関数をべき等にする (同じ入力データで関数を繰り返し呼び出しても重複した結果を生成しないように記述する) 必要があります。  

### 文字列のキュー メッセージ

次の例では、キューに文字列メッセージが含まれます。キューメッセージの内容が含まれる logMessage と呼ばれる文字列パラメーターに QueueTrigger が適用されます。関数は[ダッシュボードにログ メッセージを書き込み]ます(#logs)。
 

		public static void ProcessQueueMessage([QueueTrigger("logqueue")] string logMessage, TextWriter logger)
		{
		    logger.WriteLine(logMessage);
		}

文字列だけでなく、パラメーターにはバイト配列、CloudQueueMessage オブジェクト、または自分で定義した POCO オブジェクトがあります。

### POCO オブジェクトのキュー メッセージ

次の例では、キュー メッセージには BlobName プロパティを含む BlobInformation オブジェクトの JSON が含まれます。SDK は自動的にオブジェクトを逆シリアル化します。

		public static void WriteLogPOCO([QueueTrigger("logqueue")] BlobInformation blobInfo, TextWriter logger)
		{
		    logger.WriteLine("Queue message refers to blob: " + blobInfo.BlobName);
		}

次の例では、Web ジョブ SDK を使用せずに、SDK が解析できる POCO キュー メッセージを作成する方法を示します。SDK では [Newtonsoft.Json NuGet パッケージ](http://www.nuget.org/packages/Newtonsoft.Json)を使用してメッセージをシリアル化したり逆シリアル化したりします。

		BlobInformation blobInfo = new BlobInformation() { BlobName = "log.txt" };
		var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
		logQueue.AddMessage(queueMessage);

### Async 関数

次の async 関数は[ダッシュボードにログを書き込み]ます(#logs)。

		public async static Task ProcessQueueMessageAsync([QueueTrigger("logqueue")] string logMessage, TextWriter logger)
		{
		    await logger.WriteLineAsync(logMessage);
		}

Blob をコピーした次の例に示すように、Async 関数はキャンセル トークンを取得する場合があります。(QueueTrigger プレースホルダーの詳細については、「[Blobs](#blobs)  セクション」を参照してください。

		public async static Task ProcessQueueMessageAsyncCancellationToken(
		    [QueueTrigger("blobcopyqueue")] string blobName, 
		    [Blob("textblobs/{queueTrigger}",FileAccess.Read)] Stream blobInput,
		    [Blob("textblobs/{queueTrigger}-new",FileAccess.Write)] Stream blobOutput,
		    CancellationToken token)
		{
		    await blobInput.CopyToAsync(blobOutput, 4096, token);
		}

### ポーリング アルゴリズム

SDK はランダムな指数バックオフ アルゴリズムを実装することで、ストレージ トランザクション コストにおけるアイドル状態のキューのポーリングの影響を軽減しています。SDK はメッセージを見つけると 2 秒間待ってから別のメッセージを確認します。メッセージが見つからなかった場合は、約 4 秒間待ってから再試行します。再試行後もキュー メッセージが取得できなかった場合、待ち時間が最大になるまで再試行が続けられます。既定の最大待ち時間は 1 分間です。[最大待ち時間は構成可能です](#config)。

### 並列実行

異なるキューをリッスンする複数の関数を使用している場合、複数のメッセージを同時に受信したとき、SDK では並行してそれらを呼び出します。 

1 つのキューに対して複数のメッセージが受信される場合も同様に処理されます。既定では、SDK では一度に 16 個のキュー メッセージがバッチとして取得され、それらを並列で処理する関数が実行されます。[バッチのサイズは構成可能です](#config)。SDK では、処理されるメッセージがバッチのサイズの半数を下回ると、別のバッチを取得してそれらの処理が開始されます。そのため、関数ごとに処理される同時実行のメッセージの最大数は、バッチのサイズの 1.5 倍になります。この制限は、QueueTrigger 属性を持つ各関数に個別に適用されます。1 つのキューで受信した複数のメッセージを並列に実行したくない場合は、バッチ サイズを 1 に設定します。

### <a id="queuemetadata"></a>キューまたはキュー メッセージのメタデータの取得

メソッド シグネチャにパラメーターを追加することで、次のメッセージ プロパティを取得できます。

* `DateTimeOffset` expirationTime
* `DateTimeOffset` insertionTime
* `DateTimeOffset` nextVisibleTime
* `string` queueTrigger (メッセージのテキストが含まれます)
* `string` id
* `string` popReceipt
* `int` dequeueCount

Azure のストレージ API を直接操作する場合は、CloudStorageAccount パラメーターを追加することもできます。

次の例では、このメタデータをすべて INFO アプリケーション ログに書き込みます。この例では logMessage と queueTrigger の両方にキュー メッセージの内容が含まれます。

		public static void WriteLog([QueueTrigger("logqueue")] string logMessage,
		    DateTimeOffset expirationTime,
		    DateTimeOffset insertionTime,
		    DateTimeOffset nextVisibleTime,
		    string id,
		    string popReceipt,
		    int dequeueCount,
		    string queueTrigger,
		    CloudStorageAccount cloudStorageAccount,
		    TextWriter logger)
		{
		    logger.WriteLine(
		        "logMessage={0}\n" +
			"expirationTime={1}\ninsertionTime={2}\n" +
		        "nextVisibleTime={3}\n" +
		        "id={4}\npopReceipt={5}\ndequeueCount={6}\n" +
		        "queue endpoint={7} queueTrigger={8}",
		        logMessage, expirationTime,
		        insertionTime,
		        nextVisibleTime, id,
		        popReceipt, dequeueCount,
		        cloudStorageAccount.QueueEndpoint,
		        queueTrigger);
		}

サンプル コードで記述されたログのサンプルを次に示します。

		logMessage=Hello world!
		expirationTime=10/14/2014 10:31:04 PM +00:00
		insertionTime=10/7/2014 10:31:04 PM +00:00
		nextVisibleTime=10/7/2014 10:41:23 PM +00:00
		id=262e49cd-26d3-4303-ae88-33baf8796d91
		popReceipt=AgAAAAMAAAAAAAAAfc9H0n/izwE=
		dequeueCount=1
		queue endpoint=https://contosoads.queue.core.windows.net/
		queueTrigger=Hello world!

### <a id="graceful"></a>正常なシャットダウン

継続的な Web ジョブで実行されている関数は、CancellationToken パラメーターを受け取ることができます。これは、Web ジョブの停止がオペレーティング システムによってその関数に通知されるものです。この通知を使用すれば、関数が予期せず終了してデータが不整合な状態になることを防止できます。

次の例では、1 つの関数において Web ジョブの停止が確認される方法を示します。

	public static void GracefulShutdownDemo(
	            [QueueTrigger("inputqueue")] string inputText,
	            TextWriter logger,
	            CancellationToken token)
	{
	    for (int i = 0; i < 100; i++)
	    {
	        if (token.IsCancellationRequested)
	        {
	            logger.WriteLine("Function was cancelled at iteration {0}", i);
	            break;
	        }
	        Thread.Sleep(1000);
	        logger.WriteLine("Normal processing for queue message={0}", inputText);
	    }
	}

**注:** このダッシュボードではシャットダウンされた関数のステータスや出力が正しく示されていない可能性があります。
 
詳細については、[Web ジョブの正常なシャット ダウン](http://blog.amitapple.com/post/2014/05/webjobs-graceful-shutdown/#.VCt1GXl0wpR)を参照してください。   

## <a id="createqueue"></a> キュー メッセージの処理中にキュー メッセージを作成する方法

新しいキュー メッセージを作成する関数を記述するには、出力文字列または POCO のパラメーターで、Queue 属性を使用します。QueueTrigger のように、キューの名前を文字列として渡すことも、[キューの名前を動的に設定]することも可能です(#config)。

### 文字列のキュー メッセージ

次の例では、inputqueue という名前で受信したキュー メッセージと同じ内容で、outputqueue という名前のキュー メッセージを新しく作成します。

		public static void CreateQueueMessage(
		    [QueueTrigger("inputqueue")] string queueMessage,
		    [Queue("outputqueue")] out string outputQueueMessage )
		{
		    outputQueueMessage = queueMessage;
		}

出力パラメーターの種類が次のいずれかである場合、関数の終了時にオブジェクトは null にはならず、SDK でキュー メッセージが作成されます。

* `string` 
* `byte[]`
* CloudQueueMessage を定義するシリアル化可能な
*  POCO 型

複数のメッセージを作成するには、このセクションで後述する**複数のメッセージの作成**を参照してください。

手動でメッセージを送信する場合は、出力パラメーターの種類として CloudQueue を使用することもできます。

### POCO オブジェクトのキュー メッセージ

文字列ではなく POCO オブジェクトを含むキュー メッセージを作成するには、出力パラメーターとして POCO 型を Queue 属性のコンス トラクターに渡します。 

		public static void CreateQueueMessage(
		    [QueueTrigger("inputqueue")] BlobInformation blobInfoInput,
		    [Queue("outputqueue")] out BlobInformation blobInfoOutput )
		{
		    blobInfoOutput = blobInfoInput;
		}

SDK はオブジェクトを JSON に自動的にシリアル化します。オブジェクトが null の場合でもキュー メッセージは常に作成されます。

### 複数のメッセージの作成

複数のメッセージを作成するには、次の例に示すように、パラメーターの種類を出力キュー ICollector<T> または IAsyncCollector としてください。

		public static void CreateQueueMessages(
		    [QueueTrigger("inputqueue")] string queueMessage,
		    [Queue("outputqueue")] ICollector<string> outputQueueMessage,
		    TextWriter logger)
		{
		    logger.WriteLine("Creating 2 messages in outputqueue");
		    outputQueueMessage.Add(queueMessage + "1");
		    outputQueueMessage.Add(queueMessage + "2");
		}

Add メソッドが呼び出されると、すぐに各キュー メッセージが作成されます。

### <a id="queuebody"></a>関数本体でのキュー属性の使用

キュー メッセージを作成する Queue 属性を使用する前に、関数で何らかの処理を行う必要がある場合は、IBinder インターフェイスを使用して CloudQueue オブジェクトを取得することでキューを直接操作できるようになります。 

次の例では、入力キュー メッセージを取得して同じ内容の新しい出力キュー メッセージを作成します。出力キュー名は、関数本体のコードによって設定されます。

		public static void CreateQueueMessage(
		    [QueueTrigger("inputqueue")] string queueMessage,
		    IBinder binder)
		{
		    string outputQueueName = "outputqueue" + DateTime.Now.Month.ToString();
		    QueueAttribute queueAttribute = new QueueAttribute(outputQueueName);
		    CloudQueue outputQueue = binder.Bind<CloudQueue>(queueAttribute);
		    outputQueue.AddMessage(new CloudQueueMessage(queueMessage));
		}

## <a id="blobs"></a> キュー メッセージの処理中に Blob を読み書きする方法

Blob と Table 属性を持つと、Blob および Table を読み書きすることができます。このセクションのサンプルでは、Blob に適用されます。

Blob 属性は、Stream、TextReader、TextWriter、CloudBlockBlob などと共に使用できます。属性コンス トラクターは、コンテナーと Blob 名を指定する blobPath パラメーターを受け取ります。属性が Stream オブジェクトをデコレートしている場合は、もう 1 つのコンス トラクターのパラメーターが FileAccess モードを読み取り、書き込み、または読み取り/書き込みとして指定します。オブジェクトに Blob をバインドする前に関数で何らかの処理を行う必要がある場合は、[キュー属性について前述したように]関数本体で属性を使用できます(#queuebody)。

### 文字列のキュー メッセージ

文字列を含むキュー メッセージでは、queueTrigger は、メッセージの内容を含む Blob 属性の BlobPath パラメーターで使用できるプレースホルダーです。 

次の例では、Blob の読み取りと書き込みに Stream オブジェクトを使用しています。textblobs コンテナーにある Bob の名前がキュー メッセージで提供されます。名前の末尾に「-new」 が追加された Blob のコピーが同じコンテナー内に作成されます。 

		public static void ProcessQueueMessage(
		    [QueueTrigger("blobcopyqueue")] string blobName, 
		    [Blob("textblobs/{queueTrigger}",FileAccess.Read)] Stream blobInput,
		    [Blob("textblobs/{queueTrigger}-new",FileAccess.Write)] Stream blobOutput)
		{
		    blobInput.CopyTo(blobOutput, 4096);
		}

次の例では、CloudBlockBlob オブジェクトを使用して Blob の削除しています。

		public static void DeleteBlob(
		    [QueueTrigger("deleteblobqueue")] string blobName,
		    [Blob("textblobs/{queueTrigger}")] CloudBlockBlob blobToDelete)
		{
		    blobToDelete.Delete();
		}

### POCO オブジェクトのキュー メッセージ

キュー メッセージの JSON として格納された POCP オブジェクトでは、Queue 属性の blobPath パラメーターでオブジェクトのプロパティを指定するプレースホルダーを使用できます。プレースホルダーとして[キュー メタデータ プロパティの名前](#queuemetadata) も使用できます。 

次の例では、入出力 Blob の名前を指定する BlobInformation オブジェクトのプロパティを使用して、別の拡張子でBlob を新しい Blob にコピーします。 
 
		public static void CopyBlobPOCO(
		    [QueueTrigger("copyblobqueue")] BlobInformation blobInfo,
		    [Blob("textblobs/{BlobName}", FileAccess.Read)] Stream blobInput,
		    [Blob("textblobs/{BlobNameWithoutExtension}.txt", FileAccess.Write)] Stream blobOutput)
		{
		    blobInput.CopyTo(blobOutput, 4096);
		}

次の例では、Web ジョブ SDK を使用せずに、SDK が解析できる POCO キュー メッセージを作成する方法を示します。SDK では [Newtonsoft.Json NuGet パッケージ](http://www.nuget.org/packages/Newtonsoft.Json)を使用してメッセージをシリアル化したり逆シリアル化したりします。

		BlobInformation blobInfo = new BlobInformation() { BlobName = "boot.log", BlobNameWithoutExtension = "boot" };
		var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
		logQueue.AddMessage(queueMessage);

## <a id="poison"></a> 有害メッセージの処理方法

関数を失敗させる内容を含むメッセージを、*有害メッセージ* と呼びます。関数が失敗してもキュー メッセージは削除されず、もう一度送信され、その後繰り返し送信され続けます。SDK では一定数繰り返し送信されると自動的にそのサイクルを中断します。また手動でも処理できます。

### 有害メッセージの自動処理

SDKでは、キュー メッセージを処理する関数を最大 5 回呼び出します。5 回目が失敗すると、メッセージは有害メッセージ キューに移動します。[再試行の最大回数は構成可能です](#config)。 

有害メッセージ キューの名前は *{originalqueuename}*-poison です。有害メッセージを記録したり手動の処理が必要という通知を送信したりして、有害メッセージ キューからメッセージを処理する関数を記述できます。 

次の例では、CopyBlob 関数の失敗例を示します。失敗の原因は、存在しない Blob の名前がキュー メッセージに含まれるためです。この場合、メッセージは copyblobqueue キューから copyblobqueue-poison queue に移動します。その後、ProcessPoisonMessage が有害メッセージを記録されます。

		public static void CopyBlob(
		    [QueueTrigger("copyblobqueue")] string blobName,
		    [Blob("textblobs/{queueTrigger}", FileAccess.Read)] Stream blobInput,
		    [Blob("textblobs/{queueTrigger}-new", FileAccess.Write)] Stream blobOutput)
		{
		    blobInput.CopyTo(blobOutput, 4096);
		}
		
		public static void ProcessPoisonMessage(
		    [QueueTrigger("copyblobqueue-poison")] string blobName, TextWriter logger)
		{
		    logger.WriteLine("Failed to copy blob, name=" + blobName);
		}

次の図は、有害メッセージが処理されたときのコンソール出力を表します。

![Console output for poison message handling](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/poison.png)

### 有害メッセージの手動処理

関数に「dequeueCount」という名の int パラメーターを加えることで、メッセージが処理のために検知された回数を取得できます。関数コードの dequeue 回数を確認し、特定の閾値を超えたときにメッセージを処理する、独自の有害メッセージの処理を実行できます。具体例を次に示します。

		public static void CopyBlob(
		    [QueueTrigger("copyblobqueue")] string blobName, int dequeueCount,
		    [Blob("textblobs/{queueTrigger}", FileAccess.Read)] Stream blobInput,
		    [Blob("textblobs/{queueTrigger}-new", FileAccess.Write)] Stream blobOutput,
		    TextWriter logger)
		{
		    if (dequeueCount > 5)
		    {
		        logger.WriteLine("Failed to copy blob, name=" + blobName);
		    }
		    else
		    {
		    blobInput.CopyTo(blobOutput, 4096);
		    }
		}

このコードを想定どおりに動作させるためには、自動有害メッセージ処理の最大再試行回数を増やすか、この例における dequeue 回数を 5 未満になるようにする必要があります。

## <a id="config"></a> 構成オプションの設定方法

JobHostConfiguration 型を使用して、次の構成オプションを設定できます。

* コード内での SDK 接続文字列の設定
* 最大 dequeue 回数など QueueTrigger 設定の構成
* 構成からのキュー名の取得

### <a id="setconnstr"></a>コード内での SDK 接続文字列の設定

コードで SDK の接続文字列を設定すると、構成ファイルや環境変数で、独自の接続文字列を使用できるようになります。具体例を次に示します。

		static void Main(string[] args)
		{
		    var _storageConn = ConfigurationManager
		        .ConnectionStrings["MyStorageConnection"].ConnectionString;
		
		    var _dashboardConn = ConfigurationManager
		        .ConnectionStrings["MyDashboardConnection"].ConnectionString;
		
		    var _serviceBusConn = ConfigurationManager
		        .ConnectionStrings["MyServiceBusConnection"].ConnectionString;
		
		    JobHostConfiguration config = new JobHostConfiguration();
		    config.StorageConnectionString = _storageConn;
		    config.DashboardConnectionString = _dashboardConn;
		    config.ServiceBusConnectionString = _serviceBusConn;
		    JobHost host = new JobHost(config);
		    host.RunAndBlock();
		}

### <a id="configqueue"></a>キュー設定の構成

キュー メッセージの処理に適用される次のような設定を構成できます。

- 同時に検出され並行して処理されるキュー メッセージの最大数 (既定は16)。
- 有害メッセージ キューに送られるまでの最大再試行回数 (既定は 5)。
- キューが空の場合に再度ポーリングが開始されるまでの最大待ち時間 (既定は 1 分間)。

これらの設定を構成する方法について次に示します。

		static void Main(string[] args)
		{
		    JobHostConfiguration config = new JobHostConfiguration();
		    config.Queues.BatchSize = 8;
		    config.Queues.MaxDequeueCount = 4;
		    config.Queues.MaxPollingInterval = TimeSpan.FromMinutes(10);
		    JobHost host = new JobHost(config);
		    host.RunAndBlock();
		}

### <a id="configqueuenames"></a>構成からのキューの名前の取得

JobHostConfiguration 型を使えば、SDK の QueueTrigger や Queue 属性に、キュー名を提供する NameResolver オブジェクトを渡すことができます。

たとえば、テスト環境で「logqueuetest」という名前のキューを、実行環境で「logqueueprod」という名前のキューを使用したいとします。キュー名をハード コードする代わりに、実際のキュー名が保持される appSettings コレクションのエントリの名前を指定できます。appSettings キーが logqueue であれば、関数は次の例のようになります。

		public static void WriteLog([QueueTrigger("%logqueue%")] string logMessage)
		{
		    Console.WriteLine(logMessage);
		}

次の例のように、NameResolver クラスが appSettings からキュー名を取得します。

		public class QueueNameResolver : INameResolver
		{
		    public string Resolve(string name)
		    {
		        return ConfigurationManager.AppSettings[name].ToString();
		    }
		}

次の例のように、NameResolver クラスを JobHost オブジェクトに渡します。

		static void Main(string[] args)
		{
		    JobHostConfiguration config = new JobHostConfiguration();
		    config.NameResolver = new QueueNameResolver();
		    JobHost host = new JobHost(config);
		    host.RunAndBlock();
		}
 

## <a id="manual"></a>関数を手動でトリガーする方法

関数を手動でトリガーするには、JobHost オブジェクトで Call または CallAsync メソッドを使用し、その関数で NoAutomaticTrigger 属性を使用します。具体例を次に示します。 

		public class Program
		{
		    static void Main(string[] args)
		    {
		        JobHost host = new JobHost();
		        host.Call(typeof(Program).GetMethod("CreateQueueMessage"), new { value = "Hello world!" });
		    }
		
		    [NoAutomaticTrigger]
		    public static void CreateQueueMessage(
		        TextWriter logger, 
		        string value, 
		        [Queue("outputqueue")] out string message)
		    {
		        message = value;
		        logger.WriteLine("Creating queue message: ", message);
		    }
		}

## <a id="logs"></a>ログの記述方法

特定の関数呼び出しにリンクした、Web ジョブ ダッシュボード ページに表示されるログを記述するには、メソッド シグネチャでのパラメーターから取得した TextWriter オブジェクトを使用します。

[アプリケーション トレース ログ]に書き込むには、(../web-sites-dotnet-troubleshoot-visual-studio/#logsoverview)Console.Out (INFO としてマークされたログの作成) や Console.Error (ERROR としてマークされたログの作成) を使用します。代替として、[Trace or TraceSource](http://blogs.msdn.com/b/mcsuksoldev/archive/2014/09/04/adding-trace-to-azure-web-sites-and-web-jobs.aspx)を使用する方法もあります。

アプリケーションのログは、Azure Web サイトをどのように構成しているかによって、Web サイトのログファイル、Azure タブレット、または Azure Blob に表示されます。プログラムが Azure Web ジョブを実行している場合、最新のアプリケーションのログ 100 件がダッシュボードにも表示されます (ローカルで実行されているプログラムや別の環境で実行されているプログラムではアプリケーション ログがダッシュボードに表示されることはありません)。   

[ダッシュボードの接続文字列を null に設定]してログを無効にすることができます(#config)。

ログを書き込むいくつかの方法について次に示します。

		public static void WriteLog(
		    [QueueTrigger("logqueue")] string logMessage,
		    TextWriter logger)
		{
		    Console.WriteLine("Console.Write - " + logMessage);
		    Console.Out.WriteLine("Console.Out - " + logMessage);
		    Console.Error.WriteLine("Console.Error - " + logMessage);
		    logger.WriteLine("TextWriter - " + logMessage);
		}

特定の関数呼び出しのページに移動して **Toggle Output** をクリックすると、Web ジョブ SDK のダッシュボードで TextWriter オブジェクトからの出力が表示されます。

![Click function invocation link](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardinvocations.png)

![Logs in function invocation page](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardlogs.png)

Web ジョブのページ (特定の関数呼び出しのページではなく) に移動して **Toggle Output** をクリックすると、Web ジョブ SDK のダッシュボードでアプリケーション ログの最新 100 件が表示されます。
 
![Click Toggle Output](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardapplogs.png)

継続的な Web ジョブのページでは、Web サイトのファイルシステムの /data/jobs/continuous/*{webjobname}*/job_log.txt にアプリケーション ログが表示されます。

		[09/26/2014 21:01:13 > 491e54: INFO] Console.Write - Hello world!
		[09/26/2014 21:01:13 > 491e54: ERR ] Console.Error - Hello world!
		[09/26/2014 21:01:13 > 491e54: INFO] Console.Out - Hello world!

Azure Blob では、アプリケーション ログが次のように表示されます。
		2014-09-26T21:01:13,Information,contosoadsnew,491e54,635473620738373502,0,17404,17,Console.Write - Hello world!,
		2014-09-26T21:01:13,Error,contosoadsnew,491e54,635473620738373502,0,17404,19,Console.Error - Hello world!,
		2014-09-26T21:01:13,Information,contosoadsnew,491e54,635473620738529920,0,17404,17,Console.Out - Hello world!,

Azure テーブルでは Console.Out および Console.Error ログが次のように表示されます。

![Info log in table](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/tableinfo.png)

![Error log in table](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/tableerror.png)

## <a id="nextsteps"></a> 次のステップ

このトピックでは、Azure キューを操作するための一般的なシナリオの処理方法を示すコードのサンプルを提供しました。Azure Web ジョブと Web ジョブ SDK の使用方法の詳細については、「[Azure Web Jobs Recommended Resources (Azure Web ジョブの推奨リソース)](http://go.microsoft.com/fwlink/?linkid=390226)」を参照してください。

<!--HONumber=35.2-->
