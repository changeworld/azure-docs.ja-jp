<properties 
	pageTitle="Azure Storage の使用" 
	description="Visual Studio の ASP.NET 5 プロジェクトで Azure キュー ストレージの使用を開始する方法" 
	services="storage" 
	documentationCenter="" 
	authors="patshea123" 
	manager="douge" 
	editor="tglee"/>

<tags 
	ms.service="storage" 
	ms.workload="web" 
	ms.tgt_pltfrm="vs-getting-started" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/20/2015" 
	ms.author="patshea123"/>

# Azure Storage の使用 (ASP.NET 5 プロジェクト)

> [AZURE.SELECTOR]
> - [Getting Started](vs-storage-aspnet5-getting-started-queues.md)
> - [What Happened](vs-storage-aspnet5-what-happened.md)

> [AZURE.SELECTOR]
> - [Blobs](vs-storage-aspnet5-getting-started-blobs.md)
> - [Queues](vs-storage-aspnet5-getting-started-queues.md)
> - [Tables](vs-storage-aspnet5-getting-started-tables.md)

Azure キュー ストレージは、HTTP または HTTPS を使用した認証された呼び出しを介して世界中のどこからでもアクセスできる大量のメッセージを格納するためのサービスです。キューの 1 つのメッセージの最大サイズは 64 KB で、1 つのキューには、ストレージ アカウントの合計容量の上限に達するまで、数百万のメッセージを格納できます。詳細については、「[How to use Queue Storage from .NET (.NET からキュー ストレージを使用する方法)](storage-dotnet-how-to-use-queues.md/ "How to use Queue Storage from .NET (.NET からキュー ストレージを使用する方法)")」を参照してください。

ASP.NET 5 プロジェクトでプログラムを使用してキューにアクセスするには、次の項目を追加する必要があります (存在していない場合)。

1. プログラムを使用して Azure Storage にアクセスするすべての C# ファイルの冒頭部分に、次の名前空間宣言コードを追加します。

		using Microsoft.Framework.Configuration;
		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.Storage.Queue;
		using System.Threading.Tasks;
		using LogLevel = Microsoft.Framework.Logging.LogLevel;

2. 次のコードを使用して構成設定を取得します。

		 IConfigurationSourceRoot config = new Configuration()
                .AddJsonFile("config.json")
                .AddEnvironmentVariables();

#####ストレージ接続文字列を取得する
キューの操作を開始するには、キューを格納するストレージ アカウントの接続文字列を取得する必要があります。**CloudStorageAccount** 型を使用してストレージ アカウント情報を表すことができます。ASP.NET 5 プロジェクトを使用する場合、次のコードに示すように、構成オブジェクトの get メソッドを呼び出して Azure サービス構成からストレージ接続文字列とストレージ アカウント情報を取得することができます。

**注:** ASP.NET 5 で Azure Storage への呼び出しを実行する API は非同期です。詳細については、[Async および Await を使用した非同期プログラミング](http://msdn.microsoft.com/library/hh191443.aspx)に関するページを参照してください。次のコードでは、非同期のプログラミング方法を使用していることを前提としています。

	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
      config.Get("MicrosoftAzureStorage:<storageAccountName>_AzureStorageConnectionString"));

#####キューを作成する
**CloudQueueClient** オブジェクトを使用すると、キューの参照オブジェクトを取得できます。次のコードでは、**CloudQueueClient** オブジェクトを作成します。このトピックのすべてのコードでは、Azure アプリケーションのサービス構成に格納されているストレージ接続文字列を使用します。**CloudStorageAccount** オブジェクトを作成する方法は他にもあります。詳細については、[CloudStorageAccount メソッド](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.cloudstorageaccount_methods.aspx "CloudStorageAccount")に関するドキュメントを参照してください。

**注:** ASP.NET 5 で Azure Storage への呼び出しを実行する API は非同期です。詳細については、[Async および Await を使用した非同期プログラミング](http://msdn.microsoft.com/library/hh191443.aspx)に関するページを参照してください。次のコードでは、非同期のプログラミング方法を使用していることを前提としています。

	// Create the queue client.
	CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

**queueClient** オブジェクトを使用して、使用するキューへの参照を取得します。コードは "myqueue" という名前のキューを参照しようとします。 この名前のキューが見つからない場合は作成します。

	// Get a reference to a queue named “myqueue”.
	CloudQueue queue = queueClient.GetQueueReference("myqueue");

	// If the queue isn’t already there, then create it.
	await queue.CreateIfNotExistsAsync();

**注:** このコードはすべて、以下のセクションのコードの前に使用してください。

#####メッセージをキューに挿入する
既存のキューにメッセージを挿入するには、最初に新しい **CloudQueueMessage** オブジェクトを作成します。次に、AddMessageAsync() メソッドを呼び出します。**CloudQueueMessage** オブジェクトは、文字列 (UTF-8 形式) またはバイト配列から作成できます。次のコードでは、キューが存在しない場合は作成し、メッセージ "Hello, World" を挿入します。

	// Create a message and add it to the queue.
	CloudQueueMessage message = new CloudQueueMessage("Hello, World");
	await queue.AddMessageAsync(message);

#####次のメッセージをピークする
PeekMessageAsync() メソッドを呼び出すと、キューの先頭にあるメッセージをキューから削除せずにピークできます。

	// Peek at the next message in the queue.
	CloudQueueMessage peekedMessage = queue.PeekMessage();

	// Display the message.
	CloudQueueMessage peekedMessage = await queue.PeekMessageAsync();

#####次のメッセージを削除する
コードでは、2 つの手順でキューからメッセージを削除 (デキュー) できます。


1. GetMessageAsync() を呼び出すと、キュー内の次のメッセージが取得されます。GetMessageAsync() から返されたメッセージは、このキューからメッセージを読み取る他のコードから参照できなくなります。既定では、このメッセージを参照できない状態は 30 秒間続きます。 
2.	また、キューからのメッセージの削除を完了するには、DeleteMessageAsync() を呼び出します。 

このようにメッセージを 2 つの手順で削除することで、ハードウェアまたはソフトウェアの問題が原因でコードによるメッセージの処理が失敗した場合に、コードの別のインスタンスで同じメッセージを取得し、もう一度処理することができます。次のコードでは、メッセージが処理された直後に DeleteMessageAsync() を呼び出します。

	// Get the next message in the queue.
	CloudQueueMessage retrievedMessage = await queue.GetMessageAsync();

	// Process the message in less than 30 seconds, and then delete the message.
	await queue.DeleteMessageAsync(retrievedMessage);

[Azure Storage の詳細を確認してください](http://azure.microsoft.com/documentation/services/storage/)。[サーバー エクスプローラーでのストレージ リソースの参照](http://msdn.microsoft.com/library/azure/ff683677.aspx)に関するページと [ASP.NET 5](http://www.asp.net/vnext) に関するページも参照してください。
 

<!---HONumber=July15_HO4-->