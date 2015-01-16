<properties linkid="" pageTitle="Web ジョブ SDK を使用して Azure キューストレージを扱う方法" metaKeywords="Web ジョブ SDK Azure キュー ストレージ .NET C#" description="Web ジョブ SDK を使用して Azure キュー ストレージを操作する方法について説明します。キューの作成と削除のほか、キュー メッセージの挿入、ピーク、取得、削除を行います。" metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="web-sites,storage" documentationCenter=".NET" title="How to work with Azure queue storage using the WebJobs SDK" authors="tdykstra" manager="wpickett" editor="jimbe" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="10/13/2014" ms.author="tdykstra" />

# Web ジョブ SDK を使用して Azure キュー ストレージを扱う方法

このガイドでは、Azure キュー ストレージ サービスおよび Azure Web ジョブ SDK version 1.0.0
を使用して一般的なシナリオの C# コードを記述する方法について説明します。

このガイドでは、Web ジョブ SDK NuGet パッケージのインストールや、Azure ストレージ アカウントの作成、ストレージ アカウントを示す Web ジョブ SDK 用の接続文字列の作成など、[Web ジョブ SDK とは何か](../websites-webjobs-sdk-storage-queues-how-to)、および[基本的なタスクの実行方法](../websites-dotnet-webjobs-sdk-get-started/)について既に理解していることを前提としています。

コードの大部分は、この例で示すような `JobHost` オブジェクトの作成ではなく、関数のみを表しています。

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
	- キューまたはキュー メッセージ メタデータの取得
	- グレースフル シャットダウン
-   [キュー メッセージの処理中にキュー メッセージを作成する方法](#createqueue)
	- 文字列のキュー メッセージ
	- POCO オブジェクトのキュー メッセージ
	- 複数のメッセージの作成
	- 関数の本体で Queue 属性を使用する
-   [キュー メッセージの処理中に、BLOB の読み書きをする方法](#blobs)
	- 文字列のキュー メッセージ
	- POCO オブジェクトのキュー メッセージ
	- 関数の本体で BLOB 属性を使用する
-   [有害メッセージの処理方法](#poison)
	- 有害メッセージの自動処理
	- 有害メッセージの手動処理
-   [構成オプションの設定方法](#config)
	- コード内で SDK の接続文字列を設定する
	- QueueTrigger 設定の構成
	- 構成からキューの名前を取得する
-   [関数を手動でトリガーする方法](#manual)
-   [ログを書き込む方法](#logs)
-   [次のステップ](#nextsteps)

## <a id="trigger"></a> キュー メッセージを受信したときに関数をトリガーする方法

キュー メッセージの受信時に SDK が呼び出す関数を記述するには、予想されるメッセージの内容に応じて文字列または POCO パラメーターを持つ `QueueTrigger` 属性を使用します。属性コンストラクターは、ポーリングのためにキューの名前を指定する文字列パラメーター受け取ります。また、[キューの名前を動的に設定する](#config)ことも可能です。

Web サイトが複数の VM 上で稼働している場合、Web ジョブは各マシンで実行され、各マシンは関数を実行するためにトリガーを待ちます。一部のシナリオでは、これによっていくつかの関数が同じデータを 2 回処理する場合があるため、関数をべき等にする (同じ入力データで関数を繰り返し呼び出しても重複した結果を生成しないように記述する) 必要があります。  

### 文字列のキュー メッセージ

次の例では、キューに文字列のメッセージが含まれているので、`QueueTrigger` がキュー メッセージの内容を含む `logMessage` という名前の文字列パラメーターに適用されます。この関数は[ダッシュボードにログ メッセージを書き込みます](#logs)。
 

		public static void ProcessQueueMessage([QueueTrigger("logqueue")] string logMessage, TextWriter logger)
		{
		    logger.WriteLine(logMessage);
		}

`string` に加え、パラメーターはバイト配列、`CloudQueueMessage` オブジェクト、またはユーザーが定義する POCO オブジェクトになる場合もあります。

### POCO オブジェクトのキュー メッセージ

次の例では、`BlobName` プロパティを持つ `BlobInformation` オブジェクトの JSON がキュー メッセージに含まれています。SDK はこのオブジェクトを自動的に逆シリアル化します。

		public static void WriteLogPOCO([QueueTrigger("logqueue")] BlobInformation blobInfo, TextWriter logger)
		{
		    logger.WriteLine("Queue message refers to blob:" + blobInfo.BlobName);
		}

次の例では、SDK によって解析可能な Web ジョブ SDK を使用せずに、POCO キュー メッセージを作成する方法について示します。The SDK は [Newtonsoft.Json NuGet パッケージ](http://www.nuget.org/packages/Newtonsoft.Json)を使用してメッセージのシリアル化と逆シリアル化を行います。

		BlobInformation blobInfo = new BlobInformation() { BlobName = "log.txt" };
		var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
		logQueue.AddMessage(queueMessage);

### Async 関数

次の async 関数は[ダッシュボードにログを書き込みます](#logs)。

		public async static Task ProcessQueueMessageAsync([QueueTrigger("logqueue")] string logMessage, TextWriter logger)
		{
		    await logger.WriteLineAsync(logMessage);
		}

Async 関数は、BLOB をコピーする次の例が示すように、キャンセル トークンを必要とする場合があります。(`queueTrigger` プレースホルダーの説明については、[BLOB](#blobs) のセクションを参照してください。)

		public async static Task ProcessQueueMessageAsyncCancellationToken(
		    [QueueTrigger("blobcopyqueue")] string blobName, 
		    [Blob("textblobs/{queueTrigger}",FileAccess.Read)] Stream blobInput,
		    [Blob("textblobs/{queueTrigger}-new",FileAccess.Write)] Stream blobOutput,
		    CancellationToken token)
		{
		    await blobInput.CopyToAsync(blobOutput, 4096, token);
		}

### ポーリング アルゴリズム

SDK は、アイドル キューによるポーリングがストレージ トランザクションのコストに与える影響を軽減するため、ランダム指数バックオフ アルゴリズムを実装しています。メッセージが見つかった場合、SDK は 2 秒間待機した後、別のメッセージをチェックします。 メッセージが見つからない場合は約 4 秒間待機してから再試行します。その後もキュー メッセージの取得失敗が続けば、待機時間は規定値として 1 分間に設定されている最大待機時間に達するまで増え続けます。[この最大待機時間の設定は変更可能です](#config)。

### 並列実行

異なるキューをリッスンしている複数の関数がある場合、メッセージを同時に受信すると、SDK はそれらの関数を並列で呼び出します。 

1 つのキューに対して複数のメッセージを受信した場合も同じです。既定では、SDK は一度にキュー メッセージ 16 個のバッチを取得し、それらを並列処理する関数を実行します。[バッチ サイズの設定は変更可能です](#config)。処理中のメッセージの数がバッチ サイズの半分まで減少すると、SDK は別のバッチを取得し、そのメッセージの処理を開始します。そのため、1 つの関数につき同時に処理されるメッセージの最大数は、バッチ サイズの 1.5 倍です。この上限は、`QueueTrigger` 属性を持つ各関数に対して個別に適用されます。1 つのキューで受信したメッセージを並列実行したくない場合、バッチ サイズを 1 に設定します。

### <a id="queuemetadata"></a>キューまたはキュー メッセージ メタデータの取得

メソッド シグネチャにパラメーターを追加することで、次のメッセージ プロパティを取得できます。

* `DateTimeOffset` expirationTime
* `DateTimeOffset` insertionTime
* `DateTimeOffset` nextVisibleTime
* `string` queueTrigger (メッセージ テキストを含む)
* `string` id
* `string` popReceipt
* `int` dequeueCount

Azure ストレージ API を直接扱う場合は、`CloudStorageAccount` パラメーターも追加できます。

次の例では、このメタデータすべてを INFO アプリケーション ログに書き込みます。この例では、logMessage と queueTrigger の両方にキュー メッセージの内容が含まれます。

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
		        "logMessage={0}\n"
				nexpirationTime={1}\ninsertionTime={2}\n" +
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

サンプル コードによって書き込まれたサンプル ログを次に示します。

		logMessage=Hello world!
		expirationTime=10/14/2014 10:31:04 PM +00:00
		insertionTime=10/7/2014 10:31:04 PM +00:00
		nextVisibleTime=10/7/2014 10:41:23 PM +00:00
		id=262e49cd-26d3-4303-ae88-33baf8796d91
		popReceipt=AgAAAAMAAAAAAAAAfc9H0n/izwE=
		dequeueCount=1
		queue endpoint=https://contosoads.queue.core.windows.net/
		queueTrigger=Hello world!

### <a id="graceful"></a>グレースフル シャットダウン

連続した Web ジョブで実行されている関数は、Web ジョブが中断しそうな場合にオペレーティング システムから通知を受けられる `CancellationToken` パラメーターを受け取ることができます。この通知を使用して、データが不整合な状態のまま関数が予期せず終了することがないようにできます。

次の例では、関数内で Web ジョブの終了が迫っているか確認する方法を示します。

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

**注: ** ダッシュボードはシャット ダウンされた関数の状態および出力を正しく表示しない場合があります。
 
詳細については、「[WebJobs Graceful Shutdown (Web ジョブのグレースフル シャットダウン)](http://blog.amitapple.com/post/2014/05/webjobs-graceful-shutdown/#.VCt1GXl0wpR)」を参照してください。   

## <a id="createqueue"></a> キュー メッセージの処理中にキュー メッセージを作成する方法

新しいキュー メッセージを作成する関数を記述するには、出力文字列または POCO パラメーターで `Queue` 属性を使用します。`QueueTrigger` と同様、キューの名前を文字列として渡すか、または[キューの名前を動的に設定](#config)できます。

### 文字列のキュー メッセージ

次の例では、"inputqueue" という名前のキューに受信したキュー メッセージと同じ内容で、"outputqueue" という名前のキューに新しいキュー メッセージを作成しています。

		public static void CreateQueueMessage(
		    [QueueTrigger("inputqueue")] string queueMessage,
		    [Queue("outputqueue")] out string outputQueueMessage )
		{
		    outputQueueMessage = queueMessage;
		}

出力パラメーターの型が次のいずれかである場合、かつ関数の終了時にオブジェクトが null でない場合、SDK はキュー メッセージを作成します。

* `string` 
* `byte[]`
* ユーザーが定義するシリアル化可能な POCO 型
* `CloudQueueMessage`

複数のメッセージを作成するには、このセクションの後半にある「**複数のメッセージの作成**」を参照してください。

手動でメッセージを送信する場合は、`CloudQueue` を出力パラメーター型として使用することも可能です。

### POCO オブジェクトのキュー メッセージ

文字列ではなく POCO オブジェクトを含むキュー メッセージを作成するには、POCO 型を出力パラメーターとして `Queue` 属性コンストラクターに渡します。 

		public static void CreateQueueMessage(
		    [QueueTrigger("inputqueue")] BlobInformation blobInfoInput,
		    [Queue("outputqueue")] out BlobInformation blobInfoOutput )
		{
		    blobInfoOutput = blobInfoInput;
		}

SDK は、オブジェクトを自動で JSON にシリアル化します。キュー メッセージは、オブジェクトが null の場合でも常に作成されます。

### 複数のメッセージの作成

複数のメッセージを作成するには、次の例に示すように出力キューのパラメーター型を `ICollector<T>` または `IAsyncCollector` にします。

		public static void CreateQueueMessages(
		    [QueueTrigger("inputqueue")] string queueMessage,
		    [Queue("outputqueue")] ICollector<string> outputQueueMessage,
		    TextWriter logger)
		{
		    logger.WriteLine("Creating 2 messages in outputqueue");
		    outputQueueMessage.Add(queueMessage + "1");
		    outputQueueMessage.Add(queueMessage + "2");
		}

`Add` メソッドが呼び出されると、すぐに各キュー メッセージが作成されます。

### <a id="queuebody"></a>関数の本体で Queue 属性を使用する

`Queue` 属性を使用してキュー メッセージを作成する前に関数内でいくつかの処理を行う必要がある場合、`IBinder` インターフェイスを使用すれば `CloudQueue` オブジェクトでキューを直接操作することが可能になります。 

次の例では、入力キューのメッセージを受け取り、同じ内容を持つ新しいメッセージを出力キュー内に作成します。関数の本体内のコードによって、出力キューの名前が設定されます。

		public static void CreateQueueMessage(
		    [QueueTrigger("inputqueue")] string queueMessage,
		    IBinder binder)
		{
		    string outputQueueName = "outputqueue" + DateTime.Now.Month.ToString();
		    QueueAttribute queueAttribute = new QueueAttribute(outputQueueName);
		    CloudQueue outputQueue = binder.Bind<CloudQueue>(queueAttribute);
		    outputQueue.AddMessage(new CloudQueueMessage(queueMessage));
		}

## <a id="blobs"></a> キュー メッセージの処理中に BLOB およびテーブルの読み書きを行う方法

`Blob` および `Table` 属性によって、BLOB とテーブルの読み書きが可能になります。このセクションのサンプルは、BLOB に適用します。

`Blob` 属性と共に使用できる一部の型には、`Stream`、`TextReader`、`TextWriter`、および `CloudBlockBlob` が含まれます。属性コンストラクターは、コンテナーおよび BLOB の名前を指定する `blobPath` パラメーターを受け取ります。属性が `Stream` オブジェクトを装飾すると、別のコンストラクター パラメーターが `FileAccess` モードを読み込み、書き込み、または読み書きとして指定します。BLOB をオブジェクトにバインドする前に関数内でいくつかの処理を行う必要がある場合、[Queue 属性について前述したように](#queuebody)関数の本体で属性を使用することが可能です。

### 文字列のキュー メッセージ

文字列を含むキュー メッセージの場合、`queueTrigger` はメッセージの内容を含む `Blob` 属性の `blobPath` パラメーター内で使用できるプレースホルダーです。 

次の例では、`Stream` オブジェクトを使用して BLOB の読み書きを行っています。キュー メッセージは textblobs コンテナーにある BLOB の名前を与えます。名前に "-new" を加えた BLOB のコピーが同じコンテナー内に作成されます。 

		public static void ProcessQueueMessage(
		    [QueueTrigger("blobcopyqueue")] string blobName, 
		    [Blob("textblobs/{queueTrigger}",FileAccess.Read)] Stream blobInput,
		    [Blob("textblobs/{queueTrigger}-new",FileAccess.Write)] Stream blobOutput)
		{
		    blobInput.CopyTo(blobOutput, 4096);
		}

次の例では、`CloudBlockBlob` オブジェクトを使用して BLOB を削除します。

		public static void DeleteBlob(
		    [QueueTrigger("deleteblobqueue")] string blobName,
		    [Blob("textblobs/{queueTrigger}")] CloudBlockBlob blobToDelete)
		{
		    blobToDelete.Delete();
		}

### POCO オブジェクトのキュー メッセージ

キュー メッセージ内に JSON として格納される POCO オブジェクトについては、プレースホルダーを使用して、`Queue` 属性の `blobPath` パラメーター内でオブジェクトのプロパティを指定できます。また、[キュー メタデータのプロパティ名](#queuemetadata)もプレースホルダーとして使用できます。 

次の例では、`BlobInformation` オブジェクトのプロパティを使用して入力および出力 BLOB の名前を指定し、BLOB を異なる拡張子を持つ新しい BLOB にコピーします。 
 
		public static void CopyBlobPOCO(
		    [QueueTrigger("copyblobqueue")] BlobInformation blobInfo,
		    [Blob("textblobs/{BlobName}", FileAccess.Read)] Stream blobInput,
		    [Blob("textblobs/{BlobNameWithoutExtension}.txt", FileAccess.Write)] Stream blobOutput)
		{
		    blobInput.CopyTo(blobOutput, 4096);
		}

次の例では、SDK によって解析可能な Web ジョブ SDK を使用せずに、POCO キュー メッセージを作成する方法について示します。The SDK は [Newtonsoft.Json NuGet パッケージ](http://www.nuget.org/packages/Newtonsoft.Json)を使用してメッセージのシリアル化と逆シリアル化を行います。

		BlobInformation blobInfo = new BlobInformation() { BlobName = "boot.log", BlobNameWithoutExtension = "boot" };
		var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
		logQueue.AddMessage(queueMessage);

## <a id="poison"></a> 有害メッセージの処理方法

関数の失敗を引き起こす内容を含むメッセージは*有害メッセージ*と呼ばれます。関数が失敗してもキュー メッセージは削除されず、最終的には回収されて、このサイクルを繰り返します。SDK は、繰り返し回数が制限を超えると自動的にこのサイクルを中断できますが、手動で中断することも可能です。

### 有害メッセージの自動処理

SDK は関数の呼び出しを 5 回まで行い、キュー メッセージを処理します。5 回目の実行に失敗した場合、メッセージは有害キューに移動されます。[再試行回数の最大値の設定は変更可能です。](#config). 

有害キューには *{originalqueuename}*-poison という名前が付けられます。メッセージのログを取得するか、手動での対処が必要であるという通知を送信することにより有害キューからのメッセージを処理する関数が記述できます。 

次の例では、キュー メッセージが存在しない BLOB の名前を含んでいると、`CopyBlob` 関数が失敗します。その場合、メッセージは copyblobqueue キューから copyblobqueue-poison キューへと移動されます。その後、`ProcessPoisonMessage` が有害メッセージのログを取得します。

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

次の画像は、これらの関数が有害メッセージを処理したときのコンソール出力を表しています。

![Console output for poison message handling](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/poison.png)

### 有害メッセージの手動処理

`dequeueCount` という名前の `int` パラメーターを関数に加えることで、処理のためにメッセージが取得された回数が得られます。その後、関数コード内でデキューの回数を確認し、その数値がしきい値を超えた場合は、次の例に示すように独自の有害メッセージ処理を実施できます。

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

このコードを想定どおりに動作させるには、有害メッセージの自動処理の最大再試行回数を増やす必要があります。そうしなければ、この例ではデキューの回数が 5 を超えることはありません。

## <a id="config"></a> 構成オプションの設定方法

`JobHostConfiguration` 型を使用して次の構成オプションを設定できます。

* コード内で SDK の接続文字列を設定する。
* 最大デキュー回数などの `QueueTrigger` 設定を構成する。
* 構成からキューの名前を取得する。

### <a id="setconnstr"></a>コード内で SDK の接続文字列を設定する

コード内で SDK の接続文字列を設定することにより、次の例に示すように、構成ファイルまたは環境変数に独自の接続文字列の名前を使用できます。

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

キュー メッセージの処理に適用される次の設定を構成できます。

- 並列実行のために同時に取得するキュー メッセージの最大数 (既定値は 16)。
- キュー メッセージが有害キューに送られるまでの最大再試行回数 (既定値は 5)。
- キューが空の場合に再度ポーリングを実行するまでの最大待機時間 (既定値は 1 分)。

次の例は、これらの設定の構成方法を示しています。

		static void Main(string[] args)
		{
		    JobHostConfiguration config = new JobHostConfiguration();
		    config.Queues.BatchSize = 8;
		    config.Queues.MaxDequeueCount = 4;
		    config.Queues.MaxPollingInterval = TimeSpan.FromMinutes(10);
		    JobHost host = new JobHost(config);
		    host.RunAndBlock();
		}

### <a id="configqueuenames"></a>構成からキューの名前を取得する

`JobHostConfiguration` 型では、`QueueTrigger` および `Queue` 属性のために SDK にキューの名前を与える `NameResolver` オブジェクトを渡すことができます。 

たとえば、logqueuetest という名前のキューをテスト環境で、logqueueprod という名前のキューを実稼働環境で使用すると仮定します。ハードコーディングされたキューの名前ではなく、実際のキューの名前を持つであろう、`appSettings` コレクション内のエントリの名前を指定する必要があります。`appSettings` キーが logqueue の場合、関数は次の例のようになります。

		public static void WriteLog([QueueTrigger("%logqueue%")] string logMessage)
		{
		    Console.WriteLine(logMessage);
		}

`NameResolver` クラスは、次の例に示すように `appSettings` からキューの名前を取得できます。

		public class QueueNameResolver :INameResolver
		{
		    public string Resolve(string name)
		    {
		        return ConfigurationManager.AppSettings[name].ToString();
		    }
		}

次の例に示すように、`NameResolver` クラスを `JobHost` オブジェクトに渡します。

		static void Main(string[] args)
		{
		    JobHostConfiguration config = new JobHostConfiguration();
		    config.NameResolver = new QueueNameResolver();
		    JobHost host = new JobHost(config);
		    host.RunAndBlock();
		}
 

## <a id="manual"></a>関数を手動でトリガーする方法

関数を手動でトリガーするには、次の例に示すように `JobHost` オブジェクトで `Call` または `CallAsync` メソッドを使用し、関数では `NoAutomaticTrigger` 属性を使用します。  

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
		        logger.WriteLine("Creating queue message:", message);
		    }
		}

## <a id="logs"></a>ログを書き込む方法

特定の関数呼び出しにリンクされた Web ジョブのダッシュボード ページに表示されるログを書き込むには、メソッド シグネチャ内のパラメーターから取得する `TextWriter` オブジェクトを使用します。

[アプリケーション トレース ログ](../web-sites-dotnet-troubleshoot-visual-studio/#logsoverview)を書き込むには、`Console.Out` (INFO としてマークされたログを作成) および `Console.Error` (ERROR としてマークされたログを作成) を使用します。他には、[Trace または TraceSource](http://blogs.msdn.com/b/mcsuksoldev/archive/2014/09/04/adding-trace-to-azure-web-sites-and-web-jobs.aspx) を使用する方法もあります。

アプリケーション ログは、Azure Web サイトの構成方法によって Web サイトのログ ファイル、Azure テーブル、または Azure BLOB 上に表示されます。 Azure Web ジョブでプログラムが実行中の場合、最新のアプリケーション ログ 100 件もダッシュボードに表示されます。(ローカルまたは他の一部の環境で実行しているプログラムからは、ダッシュボードにアプリケーション ログは表示されません。)   

[Dashboard の接続文字列を null に設定](#config)することでログを無効にできます。

次の例では、ログを書き込むいくつかの方法を示しています。

		public static void WriteLog(
		    [QueueTrigger("logqueue")] string logMessage,
		    TextWriter logger)
		{
		    Console.WriteLine("Console.Write - " + logMessage);
		    Console.Out.WriteLine("Console.Out - " + logMessage);
		    Console.Error.WriteLine("Console.Error - " + logMessage);
		    logger.WriteLine("TextWriter - " + logMessage);
		}

Web ジョブ SDK ダッシュボードで特定の関数呼び出しのページに移動し、**[出力切り替え]** をクリックすると、`TextWriter` オブジェクトからの出力が表示されます。

![Click function invocation link](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardinvocations.png)

![Logs in function invocation page](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardlogs.png)

Web ジョブ SDK ダッシュボードで、Web ジョブのページ (関数呼び出しのページではない) に移動し、**[出力切り替え]** をクリックすると、アプリケーション ログの最新 100 行が表示されます。
 
![Click Toggle Output](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardapplogs.png)

連続的な Web ジョブでは、Web サイトのファイル システム内 /data/jobs/continuous/*{webjobname}*/job_log.txt にアプリケーション ログが表示されます。

		[09/26/2014 21:01:13 > 491e54:INFO] Console.Write - Hello world!
		[09/26/2014 21:01:13 > 491e54:ERR ] Console.Error - Hello world!
		[09/26/2014 21:01:13 > 491e54:INFO] Console.Out - Hello world!

Azure BLOB では、アプリケーション ログは次のようになります。
		2014-09-26T21:01:13,Information,contosoadsnew,491e54,635473620738373502,0,17404,17,Console.Write - Hello world!,
		2014-09-26T21:01:13,Error,contosoadsnew,491e54,635473620738373502,0,17404,19,Console.Error - Hello world!,
		2014-09-26T21:01:13,Information,contosoadsnew,491e54,635473620738529920,0,17404,17,Console.Out - Hello world!,

さらに Azure テーブル `Console.Out` および `Console.Error` のログは次のようになります。

![Info log in table](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/tableinfo.png)

![Error log in table](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/tableerror.png)

## <a id="nextsteps"></a> 次のステップ

このトピックでは、Azure キューを扱うために一般的なシナリオを処理する方法について学べるコード サンプルを用意しています。Azure Web ジョブ および Web ジョブ SDK の使用方法の詳細については、「[Azure Web ジョブの推奨リソース](http://go.microsoft.com/fwlink/?linkid=390226)」を参照してください。
