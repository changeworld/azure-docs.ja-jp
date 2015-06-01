<properties 
	pageTitle=".NET から Queue ストレージを使用する方法 | Microsoft Azure" 
	description="Microsoft Azure Queue ストレージを使用して、キューの作成と削除のほか、キュー メッセージの挿入、ピーク、取得、および削除を行う方法について説明します。" 
	services="storage" 
	documentationCenter=".net" 
	authors="tamram" 
	manager="adinah" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="03/11/2015" 
	ms.author="tamram"/>

# .NET から Queue ストレージを使用する方法

[AZURE.INCLUDE [storage-selector-queue-include](../includes/storage-selector-queue-include.md)]

## 概要

このガイドでは、Azure Queue ストレージ サービスを使用して
一般的なシナリオを実行する方法について説明します。例は C# のコードで記述され、Azure .NET 用ストレージ クライアントを利用しています。キュー メッセージの**挿入**、
**ピーク**、**取得**、および**削除**と、
**キューの作成と削除**の各シナリオについて説明します。

> [AZURE.NOTE] このガイドは、Azure .NET 用ストレージ クライアント ライブラリ 2.x 以上を対象としています。推奨されるバージョンは、ストレージ クライアント ライブラリ 4.x です。これは [NuGet](https://www.nuget.org/packages/WindowsAzure.Storage/) から、または [Azure SDK for .NET](/downloads/) の一部として提供されています。ストレージ クライアント ライブラリの取得の詳細については、「[プログラムでキュー ストレージにアクセスする]」(#programmatically-access-queue-storage) を参照してください。

[AZURE.INCLUDE [storage-queue-concepts-include](../includes/storage-queue-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../includes/storage-create-account-include.md)]

[AZURE.INCLUDE [storage-configure-connection-string-include](../includes/storage-configure-connection-string-include.md)]

## プログラムでキュー ストレージにアクセスする

### アセンブリの取得
NuGet を使用して  `Microsoft.WindowsAzure.Storage.dll` アセンブリを取得できます。**ソリューション エクスプローラー**でプロジェクトを右クリックし、**[NuGet パッケージの管理]** をクリックします。"WindowsAzure.Storage" をオンライン検索し、**[インストール]** をクリックして、Azure Storage パッケージと依存関係をインストールします。

`Microsoft.WindowsAzure.Storage.dll` は、<a href="http://azure.microsoft.com/develop/net/#">.NET デベロッパー センター</a>からダウンロードできる Azure SDK for .NET にも含まれています。このアセンブリは  `%Program Files%\Microsoft SDKs\Azure.NET SDK<sdk-version>\ref` ディレクトリにインストールされます。

### 名前空間宣言
プログラムを使用して Azure Storage にアクセスするすべての C# ファイルの冒頭部分に、次の名前空間宣言コードを追加します。

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
	using Microsoft.WindowsAzure.Storage.Queue;

必ず  `Microsoft.WindowsAzure.Storage.dll` アセンブリを参照してください。

### 接続文字列の取得
**CloudStorageAccount** 型を使用してストレージ アカウント情報を表すことができます。Microsoft Azure プロジェクトのテンプレートを 
使用している場合や、 
Microsoft.WindowsAzure.CloudConfigurationManager への参照を使用している場合、**CloudConfigurationManager** 型を使用して、Azure サービス構成からストレージ接続文字列とストレージ アカウント情報を取得できます。

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

作成しているアプリケーションに Microsoft.WindowsAzure.CloudConfigurationManager への参照が含まれておらず、接続文字列が前に示したとおり `web.config` または `app.config` に格納されている場合は、**ConfigurationManager** を使用して接続文字列を取得できます。System.Configuration.dll への参照をプロジェクトに追加し、対応する名前空間宣言を追加する必要があります。

	using System.Configuration;
	...
	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
		ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString);

### ODataLib 依存
.NET 用ストレージ クライアント ライブラリの ODataLib 依存は、WCF Data Services ではなく、NuGet から入手できる ODataLib (バージョン 5.0.2) パッケージで解決されます。ODataLib ライブラリは、直接ダウンロードすることも、NuGet を使用してコード プロジェクトで参照することもできます。具体的な ODataLib パッケージは [OData]、[Edm]、および [Spatial] です。

## キューを作成する

**CloudQueueClient** オブジェクトを使用すると、キューの参照オブジェクトを取得できます。
次のコードでは、**CloudQueueClient** オブジェクトを作成します。このガイドのすべてのコードでは、Azure アプリケーションのサービス構成に格納されているストレージ接続文字列を使用します。**CloudStorageAccount** オブジェクトを作成する方法は他にもあります。詳細については、[CloudStorageAccount][] に関するページを参照してください。

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

**queueClient** オブジェクトを使用して、使用するキューへの参照を取得します。キューが存在しない場合は作成できます。

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Create the queue if it doesn't already exist
    queue.CreateIfNotExist();

## メッセージをキューに挿入する

既存のキューにメッセージを挿入するには、最初に新しい
**CloudQueueMessage** を作成します。次に、**AddMessage** メソッドを呼び出します。
**CloudQueueMessage** は、文字列 (UTF-8 形式) または**バイト**配列から作成できます。次のコードでは、キューが存在しない場合は作成し、メッセージ  'Hello, World' を挿入します。

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Create the queue if it doesn't already exist.
    queue.CreateIfNotExist();

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
    message.SetMessageContent("Updated contents.") ;
    queue.UpdateMessage(message, 
        TimeSpan.FromSeconds(0.0),  // Make it visible immediately.
        MessageUpdateFields.Content | MessageUpdateFields.Visibility);

## 次のメッセージをデキューする

コードでは、2 つの手順でキューからメッセージをデキューします。既定では、
**GetMessage** を呼び出すと、キュー内の次のメッセージが取得されます。**GetMessage** から返されたメッセージは、このキューからメッセージを読み取る他のコードから参照できなくなります。既定では、このメッセージを参照できない状態は 30 秒間続きます。また、キューからのメッセージの削除を完了するには、**deleteMessage** を呼び出す必要があります。このようにメッセージを 2 つの手順で削除することで、ハードウェアまたはソフトウェアの問題が原因でコードによるメッセージの処理が失敗した場合に、コードの別のインスタンスで同じメッセージを取得し、もう一度処理することができます。コードでは、メッセージが処理された直後に **DeleteMessage** を呼び出します。

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

## 追加オプションを利用してメッセージをデキューする

キューからのメッセージの取得をカスタマイズする方法は 2 つあります。
1 つ目の方法では、(最大 32 個の) メッセージのバッチを取得できます。2 つ目の方法では、コードで各メッセージを完全に処理できるように、非表示タイムアウトの設定を長くまたは短くすることができます。次のコード例では、
**GetMessages** メソッドを使用して、1 回の呼び出しで 20 個のメッセージを取得します。その後、**foreach** ループを使用して、各メッセージを処理します。また、各メッセージの非表示タイムアウトを 5 分に設定します。この 5 分の非表示期間は、すべてのメッセージに対して同時に開始します。そのため、**GetMessages** の呼び出しから 5 分が経過すると、削除されていないすべてのメッセージが再び表示されます。

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

キュー内のメッセージの概数を取得できます。
**FetchAttributes** メソッドは、メッセージ数などのキューの属性を取得するように Queue サービスに要求します。**ApproximateMethodCount** プロパティは、Queue サービスを呼び出さずに、
**FetchAttributes** メソッドによって取得された最後の値を返します。

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

キューおよびキューに含まれているすべてのメッセージを削除するには、
キュー オブジェクトの **Delete** メソッドを呼び出します。

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

<ul>
<li>利用可能な API の詳細については、キュー サービスのリファレンス ドキュメントを参照してください。
  <ul>
    <li><a href="http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409">.NET 用ストレージ クライアント ライブラリ リファレンス</a>
    </li>
    <li><a href="http://msdn.microsoft.com/library/azure/dd179355">REST API リファレンス</a></li>
  </ul>
</li>
<li>Azure Storage を使用して実行できるさらに高度なタスクについては、<a href="http://msdn.microsoft.com/library/azure/gg433040.aspx">Azure のデータの格納とアクセス</a>に関するページを参照してください。</li>
<li><a href="../websites-dotnet-webjobs-sdk/">Azure WebJobs SDK を使用して Azure Storage で作業するためのコードの記述を簡略化する方法を説明します。</li>
<li>Azure でデータを格納するための追加のオプションについては、他の機能ガイドも参照してください。
  <ul>
    <li>構造化データの格納には、<a href="/documentation/articles/storage-dotnet-how-to-use-tables/">テーブル ストレージ</a>を使用します。</li>
    <li>非構造化データの格納には、<a href="/documentation/articles/storage-dotnet-how-to-use-blobs/">BLOB ストレージ</a>を使用します。</li>
    <li>リレーショナル データの格納には、<a href="/documentation/articles/sql-database-dotnet-how-to-use/">SQL データベース</a>を使用します。</li>
  </ul>
</li>
</ul>

  [Azure SDK for .NET のダウンロードとインストール]: /develop/net/
  [.NET クライアント ライブラリ リファレンス]: http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
  [Visual Studio での Azure プロジェクトの作成]: http://msdn.microsoft.com/library/azure/ee405487.aspx 
  [CloudStorageAccount]: http://msdn.microsoft.com/library/azure/microsoft.windowsazure.cloudstorageaccount_methods.aspx
  [Azure でのデータの格納とアクセス]: http://msdn.microsoft.com/library/azure/gg433040.aspx
  [Azure のストレージ チーム ブログ]: http://blogs.msdn.com/b/windowsazurestorage/
  [接続文字列の構成]: http://msdn.microsoft.com/library/azure/ee758697.aspx
  [OData]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
  [Edm]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
  [Spatial]: http://nuget.org/packages/System.Spatial/5.0.2

<!--HONumber=49-->