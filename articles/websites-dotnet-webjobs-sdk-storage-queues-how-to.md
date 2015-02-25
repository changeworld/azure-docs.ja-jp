<properties 
	pageTitle="Web ジョブ SDK を使用して Azure キュー ストレージを操作する方法" 
	description="Web ジョブ SDK を使用して Azure キュー ストレージを操作する方法について説明します。キューの作成と削除のほか、キュー メッセージの挿入、ピーク、取得、削除を行います。" 
	services="web-sites, storage" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="12/15/2014" 
	ms.author="tdykstra"/>

# Web ジョブ SDK を使用して Azure キュー ストレージを操作する方法

このガイドは、Azure キュー ストレージ サービスで Azure の Web ジョブ SDK のバージョン 1.x を使用する方法を示す c# コード サンプルについて説明します。

このガイドでは、読者に[ストレージ アカウントを示す接続文字列を使用して Visual Studio に Web ジョブ プロジェクトを作成する方法](../websites-dotnet-webjobs-sdk-get-started/)についての知識があることを前提に説明します。

ほとんどのコード スニペットは、この例のように  `JobHost` オブジェクトを作成するコードではなく、関数のみを示しています。 

		static void Main(string[] args)
		{
		    JobHost host = new JobHost();
		    host.RunAndBlock();
		}
		
## 目次

-   [キュー メッセージを受信したときに関数をトリガーする方法](#trigger)
	- 文字列のキュー メッセージ
	- POCO キュー メッセージ
	- Async 関数
	- QueueTrigger 属性が連携する型
	- ポーリング アルゴリズム
	- 複数のインスタンス
	- 並列実行
	- キューまたはキュー メッセージのメタデータの取得
	- 正常なシャットダウン
-   [キュー メッセージの処理中にキュー メッセージを作成する方法](#createqueue)
	- 文字列のキュー メッセージ
	- POCO キュー メッセージ
	- 複数のメッセージの作成または async 関数
	- キューの属性が連携する型
	- 関数本体での Web ジョブ SDK 属性の使用
-   [キュー メッセージの処理中に Blob を読み書きする方法](#blobs)
	- 文字列のキュー メッセージ
	- POCO キュー メッセージ
	- Blob の属性が連携する型
-   [有害メッセージの処理方法](#poison)
	- 有害メッセージの自動処理
	- 有害メッセージの手動処理
-   [構成オプションの設定方法](#config)
	- コード内での SDK 接続文字列の設定
	- QueueTrigger 設定の構成
	- コード内での Web ジョブ SDK コンストラクター パラメーターの値の設定
-   [関数を手動でトリガーする方法](#manual)
-   [ログの記述方法](#logs)
-   [次のステップ](#nextsteps)

## <a id="trigger"></a> キュー メッセージを受信したときに関数をトリガーする方法

キュー メッセージを受信したときに Web ジョブ SDK が呼び出す関数を記述するには、 `QueueTrigger` 属性を使用します。属性コンストラクターが、ポーリングするキューの名前を指定する文字列パラメーターを取得します。[キューの名前を動的に設定]することもできます(#config)。

### 文字列のキュー メッセージ

次の例では、キューに文字列メッセージが含まれます。キュー メッセージの内容が含まれる  `logMessage` と呼ばれる文字列パラメーターに  `QueueTrigger` が適用されます。関数は[ダッシュボードにログ メッセージを書き込み]ます(#logs)。
 

		public static void ProcessQueueMessage([QueueTrigger("logqueue")] string logMessage, TextWriter logger)
		{
		    logger.WriteLine(logMessage);
		}

 `string` だけでなく、パラメーターにはバイト配列、 `CloudQueueMessage` オブジェクト、または自分で定義した POCO オブジェクトがあります。

### POCO [(Plain Old CLR Object](http://en.wikipedia.org/wiki/Plain_Old_CLR_Object)) キュー メッセージ

次の例では、キュー メッセージには  `BlobName` プロパティを含む  `BlobInformation` オブジェクトの JSON が含まれます。SDK は自動的にオブジェクトを逆シリアル化します。

		public static void WriteLogPOCO([QueueTrigger("logqueue")] BlobInformation blobInfo, TextWriter logger)
		{
		    logger.WriteLine("Queue message refers to blob: " + blobInfo.BlobName);
		}

SDK では [Newtonsoft.Json NuGet パッケージ](http://www.nuget.org/packages/Newtonsoft.Json)を使用してメッセージをシリアル化したり逆シリアル化したりします。キュー メッセージを Web ジョブ SDK を使用しないプログラムを作成する場合は、SDK が解析できる POCO キュー メッセージを作成する次の例のようなコードを記述できます。 

		BlobInformation blobInfo = new BlobInformation() { BlobName = "log.txt" };
		var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
		logQueue.AddMessage(queueMessage);

### Async 関数

次の async 関数は[ダッシュボードにログを書き込み]ます(#logs)。

		public async static Task ProcessQueueMessageAsync([QueueTrigger("logqueue")] string logMessage, TextWriter logger)
		{
		    await logger.WriteLineAsync(logMessage);
		}

Blob をコピーした次の例に示すように、Async 関数は [キャンセル トークン](http://www.asp.net/mvc/overview/performance/using-asynchronous-methods-in-aspnet-mvc-4#CancelToken) を取得する場合があります。( `queueTrigger` プレースホルダーの詳細については、[Blobs] (#blobs) セクション) をご覧ください。

		public async static Task ProcessQueueMessageAsyncCancellationToken(
		    [QueueTrigger("blobcopyqueue")] string blobName, 
		    [Blob("textblobs/{queueTrigger}",FileAccess.Read)] Stream blobInput,
		    [Blob("textblobs/{queueTrigger}-new",FileAccess.Write)] Stream blobOutput,
		    CancellationToken token)
		{
		    await blobInput.CopyToAsync(blobOutput, 4096, token);
		}

### <a id="qtattributetypes"></a> QueueTrigger 属性が連携する型

次の型と  `QueueTrigger` を使用できます。

* `string`
* JSON としてシリアル化された POCO 型
* `byte[]`
* `CloudQueueMessage`

### <a id="polling"></a> ポーリング アルゴリズム

SDK はランダムな指数バックオフ アルゴリズムを実装することで、ストレージ トランザクション コストにおけるアイドル状態のキューのポーリングの影響を軽減しています。SDK はメッセージを見つけると 2 秒間待ってから別のメッセージを確認します。メッセージが見つからなかった場合は、約 4 秒間待ってから再試行します。再試行後もキュー メッセージが取得できなかった場合、待ち時間が最大になるまで再試行が続けられます。既定の最大待ち時間は 1 分間です。[最大待ち時間は構成可能です](#config)。

### <a id="instances"></a> 複数のインスタンス

Web サイトが複数のインスタンス上で稼働している場合、継続的な Web ジョブは各マシン上で実行され、各マシンがトリガーを待って関数の実行を試行します。一部のシナリオでは、これによっていくつかの関数が同じデータを 2 回処理する場合があるため、関数をべき等にする (同じ入力データで関数を繰り返し呼び出しても重複した結果を生成しないように記述する) 必要があります。  

### <a id="parallel"></a> 並列実行

異なるキューをリッスンする複数の関数を使用している場合、複数のメッセージを同時に受信したとき、SDK では並行してそれらを呼び出します。 

1 つのキューに対して複数のメッセージが受信される場合も同様に処理されます。既定では、SDK では一度に 16 個のキュー メッセージがバッチとして取得され、それらを並列で処理する関数が実行されます。[バッチのサイズは構成可能です](#config)。SDK では、処理されるメッセージがバッチのサイズの半数を下回ると、別のバッチを取得してそれらの処理が開始されます。そのため、関数ごとに処理される同時実行のメッセージの最大数は、バッチのサイズの 1.5 倍になります。この制限は、 `QueueTrigger` 属性を持つ各関数に個別に適用されます。1 つのキューで受信した複数のメッセージを並列に実行したくない場合は、バッチ サイズを 1 に設定します。

### <a id="queuemetadata"></a> キューまたはキュー メッセージのメタデータの取得

メソッド シグネチャにパラメーターを追加することで、次のメッセージ プロパティを取得できます。

* `DateTimeOffset` expirationTime
* `DateTimeOffset` insertionTime
* `DateTimeOffset` nextVisibleTime
* `string` queueTrigger (メッセージのテキストが含まれています)
* `string` ID
* `string` popReceipt
* `int` dequeueCount

Azure のストレージ API を直接操作する場合は、 `CloudStorageAccount` パラメーターを追加することもできます。

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

継続的な Web ジョブで実行されている関数は、 `CancellationToken` パラメーターを受け取ることができます。これにより、Web ジョブが停止する前に、オペレーティング システムによってその関数に通知されるようになります。この通知を使用すれば、関数が予期せず終了してデータが不整合な状態になることを防止できます。

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

**注:** このダッシュボードにはシャットダウンされた関数のステータスや出力が正しく表示されない可能性があります。
 
詳細については、[Web ジョブの正常なシャット ダウン](http://blog.amitapple.com/post/2014/05/webjobs-graceful-shutdown/#.VCt1GXl0wpR)をご覧ください。   

## <a id="createqueue"></a> キュー メッセージの処理中にキュー メッセージを作成する方法

新しいキュー メッセージを作成する関数を記述するには、出力文字列または POCO のパラメーターで、 `Queue` 属性を使用します。 `QueueTrigger` のように、キューの名前を文字列として渡すことも、 [キューの名前を動的に設定] することも可能です(#config)。

### 文字列のキュー メッセージ

次の非同期ではないコード サンプルは、"inputqueue" という名前のキューで受信したキュー メッセージと同じ内容で、"outputqueue" という名前のキュー メッセージを新しく作成します。(このセクションで後述するように、Async 関数は `IAsyncCollector<T>` を使用します)。


		public static void CreateQueueMessage(
		    [QueueTrigger("inputqueue")] string queueMessage,
		    [Queue("outputqueue")] out string outputQueueMessage )
		{
		    outputQueueMessage = queueMessage;
		}
  
### POCO [(Plain Old CLR Object](http://en.wikipedia.org/wiki/Plain_Old_CLR_Object)) キュー メッセージ

文字列ではなく POCO を含むキュー メッセージを作成するには、出力パラメーターとして POCO 型を  `Queue` 属性のコンストラクターに渡します。
 
		public static void CreateQueueMessage(
		    [QueueTrigger("inputqueue")] BlobInformation blobInfoInput,
		    [Queue("outputqueue")] out BlobInformation blobInfoOutput )
		{
		    blobInfoOutput = blobInfoInput;
		}

SDK はオブジェクトを JSON に自動的にシリアル化します。オブジェクトが null の場合でもキュー メッセージは常に作成されます。

### 複数のメッセージの作成または async 関数

複数のメッセージを作成するには、次の例に示すように、パラメーターの種類を出力キュー  `ICollector<T>` または  `IAsyncCollector<T>` としてください。

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

### キューの属性が連携する型

次のパラメーターの型で  `Queue` 属性を使用できます。

* `out string` (関数が終了すると、パラメーター値が null でない場合は、キュー メッセージを作成します)
* `out byte[]` (と同様に動作 `string`) 
* `out CloudQueueMessage` (と同様に動作 `string`) 
* `out POCO` (シリアル化可能な型の場合は、関数が終了してパラメーターが null の場合、null オブジェクトのメッセージを作成します)
* `ICollector`
* `IAsyncCollector`
* `CloudQueue` (Azure ストレージ API を直接使用して、手動でメッセージを作成するには)

### <a id="ibinder"></a>関数本体での Web ジョブ SDK 属性の使用

 `Queue`、 `Blob`、または  `Table` などの Web ジョブ SDK 属性を使用する前に、関数で何らかの処理を行う必要がある場合は、 `IBinder` インターフェイスを使用できます。

次の例では、入力キュー メッセージを取得して同じ内容の新しいメッセージを出力キューに作成します。出力キュー名は、関数本体のコードによって設定されます。

		public static void CreateQueueMessage(
		    [QueueTrigger("inputqueue")] string queueMessage,
		    IBinder binder)
		{
		    string outputQueueName = "outputqueue" + DateTime.Now.Month.ToString();
		    QueueAttribute queueAttribute = new QueueAttribute(outputQueueName);
		    CloudQueue outputQueue = binder.Bind<CloudQueue>(queueAttribute);
		    outputQueue.AddMessage(new CloudQueueMessage(queueMessage));
		}

 `IBinder` インターフェイスは  `Table` および `Blob` 属性とともに使用することもできます。

## <a id="blobs"></a> キュー メッセージの処理中に Blob とテーブルを読み書きする方法

 `Blob` および  `Table` 属性によって、BLOB とテーブルの読み書きが可能になりますこのセクションのサンプルでは、Blob に適用されます。Blob が作成または更新されるときにプロセスを開始する方法を示すコード サンプルについては、「[Azure Web ジョブ SDK を使用して Azure BLOB ストレージを使用する方法](../websites-dotnet-webjobs-sdk-storage-blobs-how-to/)」、およびテーブルを読み書きするコード サンプルについては、「[Web ジョブ SDK で Azure テーブル ストレージを使用する方法](../websites-dotnet-webjobs-sdk-storage-tables-how-to/)」をご覧ください。

### Blob の操作をトリガーする文字列のキュー メッセージ

文字列を含むキュー メッセージでは、 `queueTrigger` は、メッセージの内容を含む  `Blob` 属性の  `blobPath` パラメーターで使用できるプレースホルダーです。 

次の例では、Blob の読み取りと書き込みに  `Stream` オブジェクトを使用しています。キュー メッセージは、textblobs コンテナーにある Bob の名前です。名前の末尾に「-new」 が追加された Blob のコピーが同じコンテナー内に作成されます。 

		public static void ProcessQueueMessage(
		    [QueueTrigger("blobcopyqueue")] string blobName, 
		    [Blob("textblobs/{queueTrigger}",FileAccess.Read)] Stream blobInput,
		    [Blob("textblobs/{queueTrigger}-new",FileAccess.Write)] Stream blobOutput)
		{
		    blobInput.CopyTo(blobOutput, 4096);
		}

 `Blob` 属性コンストラクターは、コンテナーと blob の名前を指定する  `blobPath` パラメーターを受け取ります。このプレース ホルダーの詳細については、「[Web ジョブ SDK での Azure blob ストレージを使用する方法](../websites-dotnet-webjobs-sdk-storage-blobs-how-to/), 」をご覧ください。

属性が  `Stream` オブジェクトを修飾すると、もう 1 つのコンストラクターのパラメーターが読み取り、書き込み、または読み取り/書き込みモードとして  `FileAccess` モードを指定します。 

次の例では、 `CloudBlockBlob` オブジェクトを使用して Blob を削除しています。キュー メッセージは、blob の名前です。

		public static void DeleteBlob(
		    [QueueTrigger("deleteblobqueue")] string blobName,
		    [Blob("textblobs/{queueTrigger}")] CloudBlockBlob blobToDelete)
		{
		    blobToDelete.Delete();
		}

### <a id="pocoblobs"></a> POCO [(Plain Old CLR Object](http://en.wikipedia.org/wiki/Plain_Old_CLR_Object)) キュー メッセージ

キュー メッセージの JSON として格納された POCO オブジェクトでは、 `Queue` 属性の  `blobPath` パラメーターでオブジェクトのプロパティを指定するプレースホルダーを使用できます。プレースホルダーとして[キュー メタデータ プロパティの名前](#queuemetadata) も使用できます。 

次の例では、blob を別の拡張子の新しい blob にコピーします。キュー メッセージは、 `BlobName` と  `BlobNameWithoutExtension` プロパティを含む  `BlobInformation` オブジェクトです。プロパティの名前は、 `Blob` 属性の blob のパス内のプレース ホルダーとして使用されます。 
 
		public static void CopyBlobPOCO(
		    [QueueTrigger("copyblobqueue")] BlobInformation blobInfo,
		    [Blob("textblobs/{BlobName}", FileAccess.Read)] Stream blobInput,
		    [Blob("textblobs/{BlobNameWithoutExtension}.txt", FileAccess.Write)] Stream blobOutput)
		{
		    blobInput.CopyTo(blobOutput, 4096);
		}

SDK では [Newtonsoft.Json NuGet パッケージ](http://www.nuget.org/packages/Newtonsoft.Json)を使用してメッセージをシリアル化したり逆シリアル化したりします。キュー メッセージを web ジョブ SDK を使用しないプログラムを作成する場合は、SDK が解析できる POCO キュー メッセージを作成する次の例のようなコードを記述できます。

		BlobInformation blobInfo = new BlobInformation() { BlobName = "boot.log", BlobNameWithoutExtension = "boot" };
		var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
		logQueue.AddMessage(queueMessage);

オブジェクトに Blob をバインドする前に関数で何らかの処理を行う必要がある場合は、[キュー属性について前述したように]関数本体で属性を使用できます(#ibinder)。

### <a id="blobattributetypes"></a>Blob 属性とともに使用できる型
 
 `Blob` 属性は次の型と使用できます。

* `Stream` (読み取りまたは書き込み、FileAccess コンストラクターのパラメーターを使用して指定)
* `TextReader`
* `TextWriter`
* `string` (読み取り)
* `out string` (書き込みは、関数が返すときの文字列パラメーターが null 以外の場合にのみ blob を作成します)
* POCO (読み取り)
* out POCO (書き込みは、関数が返すときの POCO パラメーターが null の場合に常に null オブジェクトとして blob を作成します)
* `CloudBlobStream` (書き込み)
* `ICloudBlob` (読み取りまたは書き込み)
* `CloudBlockBlob` (読み取りまたは書き込み) 
* `CloudPageBlob` (読み取りまたは書き込み) 

## <a id="poison"></a> 有害メッセージの処理方法

関数の失敗を引き起こす内容を含むメッセージは  *poison messages* と呼ばれます。関数が失敗してもキュー メッセージは削除されず、もう一度送信され、その後繰り返し送信され続けます。SDK では一定数繰り返し送信されると自動的にそのサイクルを中断します。また手動でも処理できます。

### 有害メッセージの自動処理

SDKでは、キュー メッセージを処理する関数を最大 5 回呼び出します。5 回目が失敗すると、メッセージは有害メッセージ キューに移動します。[再試行の最大回数は構成可能です](#config)。 

有害メッセージ キューの名前は *{originalqueuename}*-poison です。有害メッセージを記録したり手動の処理が必要という通知を送信したりして、有害メッセージ キューからメッセージを処理する関数を記述できます。 

次の例では、 `CopyBlob` 関数の失敗例を示します。失敗の原因は、存在しない Blob の名前がキュー メッセージに含まれるためです。この場合、メッセージは copyblobqueue キューから copyblobqueue-poison queue に移動します。その後、 `ProcessPoisonMessage` が有害メッセージが記録されます。

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

関数に  `dequeueCount` という名の  `int` パラメーターを加えることで、メッセージが処理のために検知された回数を取得できます。関数コードの dequeue 回数を確認し、特定の閾値を超えたときにメッセージを処理する、独自の有害メッセージの処理を実行できます。具体例を次に示します。

		public static void CopyBlob(
		    [QueueTrigger("copyblobqueue")] string blobName, int dequeueCount,
		    [Blob("textblobs/{queueTrigger}", FileAccess.Read)] Stream blobInput,
		    [Blob("textblobs/{queueTrigger}-new", FileAccess.Write)] Stream blobOutput,
		    TextWriter logger)
		{
		    if (dequeueCount > 3)
		    {
		        logger.WriteLine("Failed to copy blob, name=" + blobName);
		    }
		    else
		    {
		    blobInput.CopyTo(blobOutput, 4096);
		    }
		}

## <a id="config"></a> 構成オプションの設定方法

 `JobHostConfiguration` 型を使用して、次の構成オプションを設定できます。

* コード内での SDK 接続文字列の設定
* 最大デキュー回数などの  `QueueTrigger` 設定を構成する。
* 構成からキューの名前を取得する。

### <a id="setconnstr"></a> コード内での SDK 接続文字列の設定

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

### <a id="configqueue"></a> QueueTrigger 設定の構成

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
		    config.Queues.MaxPollingInterval = TimeSpan.FromSeconds(15);
		    JobHost host = new JobHost(config);
		    host.RunAndBlock();
		}

### <a id="setnamesincode"></a> コード内での Web ジョブ SDK コンストラクター パラメーターの値の設定

ハード コーディングではなく、コードにキュー名、blob 名、コンテナー、またはテーブル名を指定することもあります。たとえば、構成ファイルまたは環境変数に  `QueueTrigger` のキュー名を指定します。 

これは  `NameResolver` オブジェクトを  `JobHostConfiguration` 型に渡すことで実行できます。Web ジョブ SDK 属性のコンストラクターのパラメーターに、パーセント (%) 記号で囲まれた、特殊なプレース ホルダーを追加します。 `NameResolver` コードはこれらのプレース ホルダーの代わりに使用する実際の値を指定します。

たとえば、logqueuetest という名前のキューをテスト環境で、logqueueprod という名前のキューを実稼働環境で使用すると仮定します。キュー名をハード コードする代わりに、実際のキュー名が保持される  `appSettings` コレクションのエントリの名前を指定できます。 `appSettings` キーが logqueue であれば、関数は次の例のようになります。

		public static void WriteLog([QueueTrigger("%logqueue%")] string logMessage)
		{
		    Console.WriteLine(logMessage);
		}

 `NameResolver` クラスは、次の例に示すように `appSettings` からキューの名前を取得できます。

		public class QueueNameResolver : INameResolver
		{
		    public string Resolve(string name)
		    {
		        return ConfigurationManager.AppSettings[name].ToString();
		    }
		}

次の例のように、 `NameResolver` クラスを  `JobHost` オブジェクトに渡します。

		static void Main(string[] args)
		{
		    JobHostConfiguration config = new JobHostConfiguration();
		    config.NameResolver = new QueueNameResolver();
		    JobHost host = new JobHost(config);
		    host.RunAndBlock();
		}
 
**注:** 関数が呼び出されるたびにキュー、テーブル、および blob の名前は解決されますが、blob のコンテナー名は、アプリケーションの起動時にのみ解決されます。ジョブの実行中は、blob コンテナーの名前を変更することはできません。

## <a id="manual"></a>関数を手動でトリガーする方法

関数を手動でトリガーするには、 `JobHost` オブジェクトで  `Call` または  `CallAsync` メソッドを使用し、その関数で  `NoAutomaticTrigger` 属性を使用します。具体例を次に示します。  

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

## <a id="logs"></a> ログの記述方法

ダッシュ ボードは次の 2 つの場所にログを表示します。Web ジョブのページ、および特定の Web ジョブ 呼び出しのページです。 

![Logs in WebJob page](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardapplogs.png)

![Logs in function invocation page](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardlogs.png)

関数または  `Main()` メソッドで呼び出すコンソール メソッドの出力は、特定のメソッドの呼び出しのページではなく、Web ジョブの [ダッシュ ボード] ページに表示されます。メソッド シグネチャのパラメーターから取得した TextWriter オブジェクトは、メソッドの呼び出しの [ダッシュボード] ページに表示されます。

コンソールはシングル スレッドで、多くのジョブ関数が同時に実行されるため、コンソール出力は特定のメソッド呼び出しにリンクすることはできません。そのため、SDK は各関数の呼び出しに独自の一意のログ ライター オブジェクトを提供します。

[アプリケーション トレース ログ](../web-sites-dotnet-troubleshoot-visual-studio/#logsoverview)を書き込むには、 `Console.Out` (INFO としてマークされたログを作成) および  `Console.Error` (ERROR としてマークされたログを作成) を使用します。[トレースまたは TraceSource](http://blogs.msdn.com/b/mcsuksoldev/archive/2014/09/04/adding-trace-to-azure-web-sites-and-web-jobs.aspx) を使用して、情報とエラーに加え、詳細、警告、および重大レベルを提供することもできますアプリケーション トレース ログは、Azure Web サイトの構成方法によって、Web サイトのログファイル、Azure テーブル、または Azure Blob に表示されます。すべてのコンソール出力の場合と同様に、最新の 100 件のアプリケーションのログも、関数呼び出しのページではなく、Web ジョブの [ダッシュ ボード] ページに表示されます。 

プログラムがローカル、またはその他の環境で実行されているのではなく、プログラムが、Azure Web ジョブで実行している場合にのみ、コンソール出力がダッシュ ボードに表示されます。

[ダッシュボードの接続文字列を null に設定]してログを無効にできます(#config)。

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

特定の関数呼び出しのページに移動して **Toggle Output** をクリックすると、Web ジョブ SDK のダッシュボードで  `TextWriter` オブジェクトからの出力が表示されます。

![Click function invocation link](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardinvocations.png)

![Logs in function invocation page](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardlogs.png)

Web ジョブのページ (特定の関数呼び出しのページではなく) に移動して **[Toggle Output]** をクリックすると、Web ジョブ SDK のダッシュボードにコンソール出力の最新 100 行が表示されます。
 
![Click Toggle Output](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardapplogs.png)

継続的な Web ジョブのページでは、Web サイトのファイルシステムの /data/jobs/continuous/*{webjobname}*/job_log.txt  にアプリケーション ログが表示されます。

		[09/26/2014 21:01:13 > 491e54: INFO] Console.Write - Hello world!
		[09/26/2014 21:01:13 > 491e54: ERR ] Console.Error - Hello world!
		[09/26/2014 21:01:13 > 491e54: INFO] Console.Out - Hello world!

Azure Blob では、アプリケーション ログが次のように表示されます。
		2014-09-26T21:01:13,Information,contosoadsnew,491e54,635473620738373502,0,17404,17,Console.Write - Hello world!,
		2014-09-26T21:01:13,Error,contosoadsnew,491e54,635473620738373502,0,17404,19,Console.Error - Hello world!,
		2014-09-26T21:01:13,Information,contosoadsnew,491e54,635473620738529920,0,17404,17,Console.Out - Hello world!,

Azure テーブルでは  `Console.Out` および  `Console.Error` ログが次のように表示されます。

![Info log in table](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/tableinfo.png)

![Error log in table](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/tableerror.png)

## <a id="nextsteps"></a> 次のステップ

このトピックでは、Azure キューを操作するための一般的なシナリオの処理方法を示すコードのサンプルを提供しました。Azure Web ジョブと Web ジョブ SDK の使用方法の詳細については、「[Azure Web Jobs Recommended Resources (Azure Web ジョブの推奨リソース)](http://go.microsoft.com/fwlink/?linkid=390226)」をご覧ください。


<!--HONumber=42-->
