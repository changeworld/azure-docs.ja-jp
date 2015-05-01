<properties 
	pageTitle="Web ジョブ SDK で Azure Service Bus を使用する方法" 
	description="Web ジョブ SDK を使用して Azure Service Bus キューとトピックを操作する方法について説明します。" 
	services="app-service\web, service-bus" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="04/03/2015" 
	ms.author="tdykstra"/>

# Web ジョブ SDK で Azure Service Bus を使用する方法

## 概要

このガイドでは、Azure BLOB が作成または更新されたときにプロセスをトリガーする方法を示す C# コード サンプルについて説明します。コード サンプルでは [Web ジョブ SDK](websites-dotnet-webjobs-sdk.md)  バージョン 1.x を使用しています。

このガイドは、[Visual Studio でストレージ アカウントを指定する接続文字列を使って Web ジョブ プロジェクトを作成する方法](websites-dotnet-webjobs-sdk-get-started.md) を理解していることを前提としています。

コード スニペットは、この例のように  `JobHost` オブジェクトを作成するコードではなく、関数のみを示しています。

		static void Main(string[] args)
		{
		    JobHost host = new JobHost();
		    host.RunAndBlock();
		}
		
## <a id="prerequisites"></a>前提条件

Service Bus を使用するには、他の Web ジョブ SDK パッケージに加えて、[Microsoft.Azure.WebJobs.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus/) NuGet パッケージをインストールする必要があります。 

また、ストレージ接続文字列に加えて、AzureWebJobsServiceBus 接続文字列を設定する必要もあります。次の例で示すように Web.config ファイルの  `connectionStrings` セクションでこれを行うことができます。

		<connectionStrings>
		    <add name="AzureWebJobsDashboard" connectionString="DefaultEndpointsProtocol=https;AccountName=[accountname];AccountKey=[accesskey]"/>
		    <add name="AzureWebJobsStorage" connectionString="DefaultEndpointsProtocol=https;AccountName=[accountname];AccountKey=[accesskey]"/>
		    <add name="AzureWebJobsServiceBus" connectionString="Endpoint=sb://[yourServiceNamespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[yourKey]"/>
		</connectionStrings>

サンプル プロジェクトについては、「[Service Bus の使用例](https://github.com/Azure/azure-webjobs-sdk-samples/tree/master/BasicSamples/ServiceBus)」をご覧ください。詳細については、「[Get Started with the Azure WebJobs SDK (Azure Web ジョブ SDK の概要)](websites-dotnet-webjobs-sdk-get-started.md)」をご覧ください。

## <a id="trigger"></a>Service Bus キュー メッセージを受信したときに関数をトリガーする方法

キュー メッセージを受信したときに Web ジョブ SDK が呼び出す関数を記述するには、 `ServiceBusTrigger` 属性を使用します。この属性コンストラクターは、ポーリングするキューの名前を指定するパラメーターを受け取ります。

### ServicebusTrigger のしくみ

この SDK では、 `PeekLock` モードでメッセージを受信して、関数が正常に終了した場合はメッセージの  `Complete` を呼び出し、関数が失敗した場合は、 `Abandon` を呼び出します。関数の実行時間が  `PeekLock` タイムアウトよりも長くなると、ロックが自動的に更新されます。

有害キュー処理は Service Bus 自体が行うため、Web ジョブ SDK で制御することや、構成することはできません。 

### 文字列のキュー メッセージ

以下のコード サンプルでは、文字列を含むキュー メッセージを読み取り、Web ジョブ SDK ダッシュボードにその文字列を書き込みます。

		public static void ProcessQueueMessage([ServiceBusTrigger("inputqueue")] string message, 
		    TextWriter logger)
		{
		    logger.WriteLine(message);
		}

**注:** Web ジョブ SDK を使用しないアプリケーションでキュー メッセージを作成する場合は、[BrokeredMessage.ContentType](http://msdn.microsoft.com/library/microsoft.servicebus.messaging.brokeredmessage.contenttype.aspx) を必ず "text/plain" に設定してください。

### POCO キュー メッセージ

この SDK では、POCO ([Plain Old CLR Object](http://en.wikipedia.org/wiki/Plain_Old_CLR_Object)) 型の JSON を含むキュー メッセージを自動的に逆シリアル化します。以下のコード サンプルでは、 `BlobName` プロパティを持つ  `BlobInformation` オブジェクトを含むキュー メッセージを読み取ります。

		public static void WriteLogPOCO([ServiceBusTrigger("inputqueue")] BlobInformation blobInfo,
		    TextWriter logger)
		{
		    logger.WriteLine("Queue message refers to blob: " + blobInfo.BlobName);
		}

POCO のプロパティを使って同じ関数の BLOB やテーブルを操作する方法を示すコード サンプルについては、[この記事のストレージ キュー バージョン](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#pocoblobs) に関するページをご覧ください。

### ServiceBusTrigger で使用できる型

 `string` と POCO の型に加えて、バイト配列か  `BrokeredMessage` オブジェクトで  `ServiceBusTrigger` 属性を使用できます。

## <a id="create"></a>Service Bus キュー メッセージの作成方法

新しいキュー メッセージを作成する関数を記述するには、 `ServiceBus` 属性を使用し、属性コンストラクターにキュー名を渡します。 


### 非同期以外の関数での単一のキュー メッセージの作成

次のコード サンプルでは、出力パラメーターを使用し、"inputqueue" という名前のキューで受信したメッセージと同じ内容で、"outputqueue" という名前のキューに新しいメッセージを作成します。

		public static void CreateQueueMessage(
		    [ServiceBusTrigger("inputqueue")] string queueMessage,
		    [ServiceBus("outputqueue")] out string outputQueueMessage)
		{
		    outputQueueMessage = queueMessage;
		}

単一のキュー メッセージを作成するための出力パラメーターは、以下のどの型でもかまいません。

* `string`
* `byte[]`
* `BrokeredMessage`
* ユーザーが定義するシリアル化可能な POCO 型。 JSON として自動的にシリアル化されます。

POCO 型のパラメーターの場合は、関数が終了すると、常にキュー メッセージが作成されます。パラメーターが null の場合は、メッセージを受信し、逆シリアル化すると null を返すキュー メッセージが作成されます。他の型では、パラメーターが null の場合、キュー メッセージは作成されません。

### (非同期関数での) 複数のキュー メッセージの作成

複数のメッセージを作成するには、以下のコード サンプルに示されているように、 `ServiceBus` 属性に  `ICollector<T>` または  `IAsyncCollector<T>` を指定します。

		public static void CreateQueueMessages(
		    [ServiceBusTrigger("inputqueue")] string queueMessage,
		    [ServiceBus("outputqueue")] ICollector<string> outputQueueMessage,
		    TextWriter logger)
		{
		    logger.WriteLine("Creating 2 messages in outputqueue");
		    outputQueueMessage.Add(queueMessage + "1");
		    outputQueueMessage.Add(queueMessage + "2");
		}

 `Add` メソッドが呼び出されると、すぐに各キュー メッセージが作成されます。

## <a id="topics"></a>Service Bus トピックの操作方法

Service Bus トピックにメッセージを受信したときに SDK が呼び出す関数を記述するには、以下のコード サンプルに示されているように、 `ServiceBusTrigger` 属性にトピック名とサブスクリプション名を受け取るコンストラクターを指定します。

		public static void WriteLog([ServiceBusTrigger("outputtopic","subscription1")] string message,
		    TextWriter logger)
		{
		    logger.WriteLine("Topic message: " + message);
		}

トピックにメッセージを作成するには、キュー名の場合と同様に、 `ServiceBus` 属性にトピック名を指定します。

## <a id="queues"></a>ストレージ キューのハウツー記事で紹介されている関連トピック

Service Bus 以外の Web ジョブ SDK シナリオについては、「[Web ジョブ SDK を使用して Azure キュー ストレージを操作する方法](websites-dotnet-webjobs-sdk-storage-queues-how-to.md)」をご覧ください。 

その記事では、以下のようなトピックが紹介されています。

* Async 関数
* 複数のインスタンス
* 正常なシャットダウン
* 関数本体での Web ジョブ SDK 属性の使用
* コード内での SDK 接続文字列の設定
* コードの Web ジョブ SDK コンストラクター パラメーター値の設定
* 手動での関数のトリガー
* ログの書き込み

## <a id="nextsteps"></a>次のステップ

このガイドでは、Azure Service Bus を操作する一般的なシナリオの対応方法について示すコード サンプルを紹介してきました。Azure Web ジョブと Web ジョブ SDK の使用方法の詳細については、[Azure Web ジョブの推奨リソース](http://go.microsoft.com/fwlink/?linkid=390226)に関するページをご覧ください。


<!--HONumber=52-->