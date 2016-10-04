<properties
	pageTitle=".NET を使用して Azure Queue Storage を使用する | Microsoft Azure"
	description="Azure Queue は、アプリケーション コンポーネント間の信頼性の高い非同期メッセージングを提供します。クラウド メッセージングにより、アプリケーション コンポーネントのスケールを個別に変更できます。"
	services="storage"
	documentationCenter=".net"
	authors="robinsh"
	manager="carmonm"
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="07/26/2016"
	ms.author="cbrooks;robinsh"/>

# .NET を使用して Azure Queue Storage を使用する

[AZURE.INCLUDE [storage-selector-queue-include](../../includes/storage-selector-queue-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## Overview

Azure Queue Storage は、アプリケーション コンポーネント間のクラウド メッセージングを提供します。拡張性を重視してアプリケーションを設計する場合、通常、アプリケーション コンポーネントを個別に拡張できるように分離します。Queue Storage は、アプリケーション コンポーネントがクラウド、デスクトップ、オンプレミスのサーバー、モバイル デバイスのいずれで実行されている場合でも、アプリケーション コンポーネント間の通信に非同期メッセージングを提供します。Queue Storage ではまた、非同期タスクの管理とプロセス ワークフローの構築もサポートします。

### このチュートリアルについて

このチュートリアルでは、Azure Queue Storage を使用していくつかの一般的なシナリオの .NET コードを記述する方法を示します。紹介するシナリオは、キューの作成と削除、およびキュー メッセージの追加、読み取り、削除です。

**推定所要時間:** 45 分

**前提条件:**

- [Microsoft Visual Studio](https://www.visualstudio.com/ja-JP/visual-studio-homepage-vs.aspx)
- [.NET 用 Azure Storage クライアント ライブラリ](https://www.nuget.org/packages/WindowsAzure.Storage/)
- [.NET 用 Azure Configuration Manager](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/)
- [Azure ストレージ アカウント](storage-create-storage-account.md#create-a-storage-account)


[AZURE.INCLUDE [storage-dotnet-client-library-version-include](../../includes/storage-dotnet-client-library-version-include.md)]

[AZURE.INCLUDE [storage-queue-concepts-include](../../includes/storage-queue-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

[AZURE.INCLUDE [storage-development-environment-include](../../includes/storage-development-environment-include.md)]

### 名前空間宣言の追加

次の `using` ステートメントを `program.cs` ファイルの先頭に追加します。

	using Microsoft.Azure; // Namespace for CloudConfigurationManager
	using Microsoft.WindowsAzure.Storage; // Namespace for CloudStorageAccount
    using Microsoft.WindowsAzure.Storage.Queue; // Namespace for Queue storage types

### 接続文字列を解析する

[AZURE.INCLUDE [storage-cloud-configuration-manager-include](../../includes/storage-cloud-configuration-manager-include.md)]

### Queue サービス クライアントを作成する

**CloudQueueClient** クラスを使用すると、Queue Storage に格納されているキューを取得できます。サービス クライアントを作成する方法の 1 つを次に示します。

    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

これで、Queue Storage に対してデータの読み取りと書き込みを実行するコードを記述する準備が整いました。

## キューを作成する

この例は、キューがない場合に、キューを作成する方法を示しています。

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a container.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Create the queue if it doesn't already exist
    queue.CreateIfNotExists();

## メッセージをキューに挿入する

既存のキューにメッセージを挿入するには、最初に新しい **CloudQueueMessage** を作成します。次に、**AddMessage** メソッドを呼び出します。**CloudQueueMessage** は、文字列 (UTF-8 形式) または**バイト**配列から作成できます。次のコードでは、キューが存在しない場合は作成し、メッセージ "Hello, World" を挿入します。

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Create the queue if it doesn't already exist.
    queue.CreateIfNotExists();

    // Create a message and add it to the queue.
    CloudQueueMessage message = new CloudQueueMessage("Hello, World");
    queue.AddMessage(message);

## 次のメッセージをピークする

**PeekMessage** メソッドを呼び出すと、キューの先頭にあるメッセージをキューから削除せずにピークできます。

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Peek at the next message
    CloudQueueMessage peekedMessage = queue.PeekMessage();

	// Display message.
	Console.WriteLine(peekedMessage.AsString);

## キューに配置されたメッセージの内容を変更する

キュー内のメッセージの内容をインプレースで変更できます。メッセージが作業タスクを表している場合は、この機能を使用して、作業タスクの状態を更新できます。次のコードでは、キュー メッセージを新しい内容に更新し、表示タイムアウトを設定して、60 秒延長します。これにより、メッセージに関連付けられている作業の状態が保存され、クライアントにメッセージの操作を続行する時間が 1 分与えられます。この方法を使用すると、キュー メッセージに対する複数の手順から成るワークフローを追跡でき、ハードウェアまたはソフトウェアの問題が原因で処理手順が失敗した場合に最初からやり直す必要がなくなります。通常は、さらに再試行回数を保持し、メッセージの再試行回数が *n* 回を超えた場合はメッセージを削除するようにします。こうすることで、処理するたびにアプリケーション エラーをトリガーするメッセージから保護されます。

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

	// Get the message from the queue and update the message contents.
    CloudQueueMessage message = queue.GetMessage();
    message.SetMessageContent("Updated contents.");
    queue.UpdateMessage(message,
        TimeSpan.FromSeconds(60.0),  // Make it visible for another 60 seconds.
        MessageUpdateFields.Content | MessageUpdateFields.Visibility);

## 次のメッセージをデキューする

コードでは、2 つの手順でキューからメッセージをデキューします。**GetMessage** を呼び出すと、キュー内の次のメッセージを取得します。**GetMessage** から返されたメッセージは、このキューからメッセージを読み取る他のコードから参照できなくなります。既定では、このメッセージを参照できない状態は 30 秒間続きます。また、キューからのメッセージの削除を完了するには、**DeleteMessage** を呼び出す必要があります。このようにメッセージを 2 つの手順で削除することで、ハードウェアまたはソフトウェアの問題が原因でコードによるメッセージの処理が失敗した場合に、コードの別のインスタンスで同じメッセージを取得し、もう一度処理することができます。コードでは、メッセージが処理された直後に **DeleteMessage** を呼び出します。

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Get the next message
    CloudQueueMessage retrievedMessage = queue.GetMessage();

    //Process the message in less than 30 seconds, and then delete the message
    queue.DeleteMessage(retrievedMessage);

## Async-Await パターンを一般的なキュー ストレージ API で使用する

この例では、Async-Await パターンを一般的なキュー ストレージ API で使用する方法を示します。このサンプルは、特定のメソッドの非同期バージョンをそれぞれ呼び出しています。これは、各メソッドの *Async* 接尾辞によって確認できます。非同期のメソッドを使用する場合、async-await パターンは、呼び出しが完了するまでローカルでの実行を中断します。この動作により、現在のスレッドで別の作業を実行できるようになるため、パフォーマンスのボトルネックを回避し、アプリケーションの全体的な応答性を向上させることができます。.NET での Async-Await パターンの使用方法の詳細については、[Async と Await (C# と Visual Basic)](https://msdn.microsoft.com/library/hh191443.aspx) に関するページを参照してください。

    // Create the queue if it doesn't already exist
    if(await queue.CreateIfNotExistsAsync())
    {
        Console.WriteLine("Queue '{0}' Created", queue.Name);
    }
    else
    {
        Console.WriteLine("Queue '{0}' Exists", queue.Name);
    }

    // Create a message to put in the queue
    CloudQueueMessage cloudQueueMessage = new CloudQueueMessage("My message");

    // Async enqueue the message
    await queue.AddMessageAsync(cloudQueueMessage);
    Console.WriteLine("Message added");

    // Async dequeue the message
    CloudQueueMessage retrievedMessage = await queue.GetMessageAsync();
    Console.WriteLine("Retrieved message with content '{0}'", retrievedMessage.AsString);

    // Async delete the message
    await queue.DeleteMessageAsync(retrievedMessage);
    Console.WriteLine("Deleted message");

## 追加オプションを利用してメッセージをデキューする

キューからのメッセージの取得をカスタマイズする方法は 2 つあります。1 つ目の方法では、(最大 32 個の) メッセージのバッチを取得できます。2 つ目の方法では、コードで各メッセージを完全に処理できるように、非表示タイムアウトの設定を長くまたは短くすることができます。次のコード例では、**GetMessages** メソッドを使用して、1 回の呼び出しで 20 個のメッセージを取得します。その後、**foreach** ループを使用して、各メッセージを処理します。また、各メッセージの非表示タイムアウトを 5 分に設定します。この 5 分の非表示期間は、すべてのメッセージに対して同時に開始します。そのため、**GetMessages** の呼び出しから 5 分が経過すると、削除されていないすべてのメッセージが再び表示されます。

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    foreach (CloudQueueMessage message in queue.GetMessages(20, TimeSpan.FromMinutes(5)))
    {
        // Process all messages in less than 5 minutes, deleting each message after processing.
        queue.DeleteMessage(message);
    }

## キューの長さを取得する

キュー内のメッセージの概数を取得できます。**FetchAttributes** メソッドは、メッセージ数などのキューの属性を取得するよう Queue サービスに要求します。**ApproximateMessageCount** プロパティは、Queue サービスを呼び出さずに、**FetchAttributes** メソッドによって取得された最後の値を返します。

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

	// Fetch the queue attributes.
	queue.FetchAttributes();

    // Retrieve the cached approximate message count.
    int? cachedMessageCount = queue.ApproximateMessageCount;

	// Display number of messages.
	Console.WriteLine("Number of messages in queue: " + cachedMessageCount);

## キューを削除する

キューおよびキューに格納されているすべてのメッセージを削除するには、キュー オブジェクトの **Delete** メソッドを呼び出します。

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Delete the queue.
    queue.Delete();

## 次のステップ

これで、Queue ストレージの基本を学習できました。さらに複雑なストレージ タスクを実行するには、次のリンク先を参照してください。

- 利用可能な API の詳細については、Queue サービスのリファレンス ドキュメントを参照してください。
    - [.NET 用ストレージ クライアント ライブラリ リファレンス](http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409)
    - [REST API リファレンス](http://msdn.microsoft.com/library/azure/dd179355)
- Azure Storage で作業するために記述したコードを簡略化する方法については、「[Azure WebJobs SDK とは](../app-service-web/websites-dotnet-webjobs-sdk.md)」をご覧ください。
- Azure でデータを格納するための追加のオプションについては、他の機能ガイドも参照してください。
    - 構造化データを格納するには、「[.NET を使用して Azure Table Storage を使用する](storage-dotnet-how-to-use-tables.md)」を参照してください。
    - 非構造化データを格納するには、「[.NET を使用して Azure Blob Storage を使用する](storage-dotnet-how-to-use-blobs.md)」を参照してください。
    - リレーショナル データを格納するには、[.NET アプリケーションで Azure SQL Database を使用する方法](sql-database-dotnet-how-to-use.md)についてのページを参照してください。

  [Download and install the Azure SDK for .NET]: /develop/net/
  [.NET client library reference]: http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
  [Creating a Azure Project in Visual Studio]: http://msdn.microsoft.com/library/azure/ee405487.aspx
  [Azure Storage Team Blog]: http://blogs.msdn.com/b/windowsazurestorage/
  [OData]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
  [Edm]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
  [Spatial]: http://nuget.org/packages/System.Spatial/5.0.2

<!----HONumber=AcomDC_0921_2016-->