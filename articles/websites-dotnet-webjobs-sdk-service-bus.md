<properties 
	pageTitle="Web ジョブ SDK で Azure Service Bus を使用する方法" 
	description="Web ジョブ SDK で Azure Service Bus キューおよびトピックを使用する方法について説明します。" 
	services="web-sites, service-bus" 
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

# Web ジョブ SDK で Azure Service Bus を使用する方法

このガイドでは、Azure BLOB が作成または更新されたときに、プロセスを開始する方法を示す C# コード サンプルを提供します。コード サンプルは [Web ジョブ SDK](websites-dotnet-webjobs-sdk.md) バージョン 1.x を使用します。

このガイドは、[ストレージ アカウントを示す接続文字列を使用して Visual Studio に Web ジョブ プロジェクトを作成する方法](websites-dotnet-webjobs-sdk-get-started.md) を理解していることを前提としています。

コード スニペットは、この例のように  `JobHost` オブジェクトを作成するコードではなく、関数のみを示しています。

		static void Main(string[] args)
		{
		    JobHost host = new JobHost();
		    host.RunAndBlock();
		}
		
## 目次

-   [前提条件](#prerequisites)
-   [キュー メッセージを受信したときに関数をトリガーする方法](#trigger)
-   [キュー メッセージを作成する方法](#create)
-   [Service Bus のトピックを操作する方法](#topics)
-   [ストレージ キューに関する記事で扱う関連トピック](#queues)
-   [次のステップ](#nextsteps)

## <a id="prerequisites"></a> 前提条件

Service Bus を使用するには、他の Web ジョブ SDK パッケージだけでなく、[Microsoft.Azure.WebJobs.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus/) NuGet パッケージをインストールする必要があります。 

また、ストレージ接続文字列だけでなく、AzureWebJobsServiceBus 接続文字列を設定する必要があります。

## <a id="trigger"></a> Service Bus キュー メッセージを受信したときに関数をトリガーする方法

キュー メッセージを受信したときに Web ジョブ SDK が呼び出す関数を記述するには、 `ServiceBusTrigger` 属性を使用します。属性コンストラクターが、ポーリングするキューの名前を指定するパラメーターを取得します。

### ServicebusTrigger のしくみ

関数が正常に終了した場合は、SDK は  `PeekLock` モードでメッセージを受信してメッセージに  `Complete` を呼び出し、関数が失敗した場合は、 `Abandon` を呼び出します。関数が  `PeekLock` タイムアウト値以上に実行されると、ロックが自動的に更新されます。

Service Bus は、Web ジョブ SDK によって制御されることも、Web ジョブ SDK で構成されることもないように有害キュー処理を実行します。 

### 文字列のキュー メッセージ

次のコード サンプルは、文字列が含まれているキュー メッセージを読み取り、Web ジョブ SDK のダッシュボードに文字列を書き込みます。

		public static void ProcessQueueMessage([ServiceBusTrigger("inputqueue")] string message, 
		    TextWriter logger)
		{
		    logger.WriteLine(message);
		}

**注:** Web ジョブ SDK を使用しないアプリケーションでキュー メッセージを作成する場合は、必ず [BrokeredMessage.ContentType](http://msdn.microsoft.com/library/microsoft.servicebus.messaging.brokeredmessage.contenttype.aspx) を "text/plain" に設定します。

### POCO キュー メッセージ

SDK は POCO [(Plain Old CLR Object](http://en.wikipedia.org/wiki/Plain_Old_CLR_Object)) 型の JSON が格納されているキュー メッセージを自動的に逆シリアル化します。次のコード サンプルは、 `BlobName` プロパティを持つ  `BlobInformation` オブジェクトが格納されているキュー メッセージを読み取ります。

		public static void WriteLogPOCO([ServiceBusTrigger("inputqueue")] BlobInformation blobInfo,
		    TextWriter logger)
		{
		    logger.WriteLine("Queue message refers to blob: " + blobInfo.BlobName);
		}

POCO のプロパティを使用して同じ関数内の BLOB およびテーブルを操作する方法を示すコード サンプルについては、 [storage queues version of this article (この記事のストレージ キューのバージョン)](../websites-dotnet-webjobs-sdk-storage-queues-how-to/#pocoblobs). をご覧ください。

### ServiceBusTrigger が連携する型

 `string` 型および POCO 型に加え、バイト配列または  `BrokeredMessage` オブジェクトで  `ServiceBusTrigger` 属性を使用できます。

## <a id="create"></a> Service Bus キューのメッセージを作成する方法

新しいキュー メッセージを作成する関数を記述するには、 `ServiceBus` 属性を使用して、キュー名を属性コンストラクターに渡します。 


### 非同期ではない関数で 1 つのキュー メッセージを作成します。

次のコード サンプルは、出力パラメーターを使用して、"inputqueue" という名前のキューで受信したメッセージと同じ内容の "outputqueue" という名前のキューに新しいメッセージを作成します。

		public static void CreateQueueMessage(
		    [ServiceBusTrigger("inputqueue")] string queueMessage,
		    [ServiceBus("outputqueue")] out string outputQueueMessage)
		{
		    outputQueueMessage = queueMessage;
		}

1 つのキュー メッセージを作成する出力パラメーターには、次の型のいずれかを使用できます。

* `string`
* `byte[]`
* `BrokeredMessage`
* ユーザーが定義するシリアル化可能な POCO 型JSON として自動的にシリアル化されています。

POCO 型のパラメーターでは、キュー メッセージは関数が終了するたびに作成されます。パラメーターが null の場合、SDK は、メッセージを受信して逆シリアル化したときに null を返すキュー メッセージを作成します。その他の型では、パラメーターが null の場合は、キュー メッセージは作成されません。

### 複数のキュー メッセージまたは async 関数の作成

複数のキュー メッセージを作成するには、次のコード サンプルに示すように  `ICollector<T>` または  `IAsyncCollector<T>` と  `ServiceBus` の属性を使用します。

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

## <a id="topics"></a>Service Bus のトピックを操作する方法

メッセージが Service Bus トピックに受信されたときに、SDK が呼び出す関数を記述するためには、次のコード サンプルに示すように、トピック名とサブスクリプション名を受け取るコンストラクターで  `ServiceBusTrigger` 属性を使用します。

		public static void WriteLog([ServiceBusTrigger("outputtopic","subscription1")] string message,
		    TextWriter logger)
		{
		    logger.WriteLine("Topic message: " + message);
		}

トピックにメッセージを作成するには、キュー名で使用するのと同じ方法で、トピック名で  `ServiceBus` 属性を使用します。

## <a id="queues"></a>ストレージ キューの操作方法に関する記事で扱う関連トピック

Service Bus に固有でない Web ジョブ SDK のシナリオについては、[Web ジョブ SDK を使用して Azure キュー ストレージを使用する方法](websites-dotnet-webjobs-sdk-storage-queues-how-to.md).  をご覧ください。

この記事で取り上げている関連のトピックは、次のとおりです。

* Async 関数
* 複数のインスタンス
* 正常なシャットダウン
* 関数本体での Web ジョブ SDK 属性の使用
* コード内での SDK 接続文字列の設定
* コード内での Web ジョブ SDK コンストラクターのパラメーター値の設定
* 手動による関数のトリガー
* ログの記述

## <a id="nextsteps"></a> 次のステップ

このガイドでは Azure Service Bus を使用して一般的なシナリオの処理方法を示すコード サンプルを提供しています。Azure の Ｗeb ジョブと、Web ジョブ SDK の使用方法の詳細については、 [Azure Web ジョブの推奨リソース](http://go.microsoft.com/fwlink/?linkid=390226) をご覧ください。



<!--HONumber=42-->
