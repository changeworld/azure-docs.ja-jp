<properties urlDisplayName="Queue Service" pageTitle=".NET からキュー ストレージを使用する方法 | Microsoft Azure" metaKeywords="Get started Azure queue   Azure asynchronous processing   Azure queue   Azure queue storage   Azure queue .NET   Azure queue storage .NET   Azure queue C#   Azure queue storage C#" description="Microsoft Azure Queue ストレージを使用して、キューの作成と削除のほか、キュー メッセージの挿入、ピーク、取得、および削除を行う方法について説明します。" metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="storage" documentationCenter=".NET" title="Microsoft Azure キュー ストレージを使用する方法" authors="tamram" manager="adinah" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="tamram" />

# How to use Queue Storage from .NET (.NET からキュー ストレージを使用する方法)

このガイドでは、Azure キュー ストレージ サービスを使用して一般的な
シナリオを実行する方法について説明します。例は C# のコードで記述され、
Azure .NET 用ストレージ クライアントを利用しています。キュー メッセージの**挿入**、
**ピーク**、**取得**、および**削除**と、
**キューの作成および削除**の各シナリオについて説明します。キューの詳細については、
「[次のステップ][次のステップ]」のセクションを参照してください。

> [WACOM.NOTE] このガイドは、Azure .NET 用ストレージ クライアント ライブラリ 2.x 以上を対象としています。推奨されるバージョンはストレージ クライアント ライブラリ 4.x です。これは、[NuGet][NuGet] から、または [Azure SDK for .NET][Azure SDK for .NET] の一部として提供されています。ストレージ クライアント ライブラリの入手方法の詳細については、「[方法: プログラムでキュー ストレージにアクセスする][方法: プログラムでキュー ストレージにアクセスする]」を参照してください。

## 目次

-   [キュー ストレージとは][キュー ストレージとは]
-   [概念][概念]
-   [Azure のストレージ アカウントの作成][Azure のストレージ アカウントの作成]
-   [Azure のストレージ接続文字列の設定][Azure のストレージ接続文字列の設定]
-   [方法: プログラムでキュー ストレージにアクセスする][方法: プログラムでキュー ストレージにアクセスする]
-   [方法: キューを作成する][方法: キューを作成する]
-   [方法: メッセージをキューに挿入する][方法: メッセージをキューに挿入する]
-   [方法: 次のメッセージをピークする][方法: 次のメッセージをピークする]
-   [方法: キューに配置されたメッセージの内容を変更する][方法: キューに配置されたメッセージの内容を変更する]
-   [方法: 次のメッセージをデキューする][方法: 次のメッセージをデキューする]
-   [方法: 追加オプションを利用してメッセージをデキューする][方法: 追加オプションを利用してメッセージをデキューする]
-   [方法: キューの長さを取得する][方法: キューの長さを取得する]
-   [方法: キューを削除する][方法: キューを削除する]
-   [次のステップ][次のステップ]

[WACOM.INCLUDE [howto-queue-storage](../includes/howto-queue-storage.md)]

## 

## <a name="create-account"></a><span class="short-header">アカウントの作成</span>Azure の Storage アカウントの作成

</h2>
[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

## 

## <a name="setup-connection-string"></a><span class="short-header">接続文字列の設定</span>Azure のストレージ接続文字列の設定

</h2>
[WACOM.INCLUDE [storage-configure-connection-string](../includes/storage-configure-connection-string.md)]

## <a name="configure-access"> </a><span class="short-header">プログラムでのアクセス</span>方法: プログラムでキュー ストレージにアクセスする

### アセンブリの取得

NuGet を使用して `Microsoft.WindowsAzure.Storage.dll` アセンブリを取得できます。**ソリューション エクスプローラー**でプロジェクトを右クリックし、**[NuGet パッケージの管理]** をクリックします。"WindowsAzure.Storage" をオンライン検索し、**[インストール]** をクリックして Azure Storage のパッケージと依存関係をインストールします。

`Microsoft.WindowsAzure.Storage.dll` は、[.NET デベロッパー センター][.NET デベロッパー センター]からダウンロードできる Azure SDK for .NET にも含まれています。このアセンブリは `%Program Files%\Microsoft SDKs\Windows Azure\.NET SDK%Program Files%\Microsoft SDKs\Windows Azure\.NET SDK\<sdk-version>\ref\`lt;sdk-version\>\\ref\\</code> ディレクトリにインストールされます。

### 名前空間宣言

プログラムを使用して Azure ストレージにアクセスするすべての C#
ファイルの冒頭部分に、次の名前空間宣言を追加します。

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Queue;

必ず `Microsoft.WindowsAzure.Storage.dll` アセンブリを参照してください。

### 接続文字列の取得

**CloudStorageAccount** 型を使用してストレージ アカウント
情報を表すことができます。Windows Azure プロジェクト
テンプレートを使用している場合や、
Microsoft.WindowsAzure.CloudConfigurationManager
への参照がある場合は、**CloudConfigurationManager** 型
を使用して Azure サービス構成からストレージ接続文字列
とストレージ アカウント情報を取得できます。

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

作成しているアプリケーションに Microsoft.WindowsAzure.CloudConfigurationManager への参照が含まれておらず、接続文字列が前に示したとおり `web.config` または `app.config` に格納されている場合は、**ConfigurationManager** を使用して接続文字列を取得できます。System.Configuration.dll への参照をプロジェクトに追加し、対応する名前空間宣言を追加する必要があります。

    using System.Configuration;
    ...
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString);

### ODataLib 依存

.NET 用ストレージ クライアント ライブラリの ODataLib 依存は、WCF Data Services ではなく、NuGet から入手できる ODataLib (バージョン 5.0.2) パッケージで解決されます。ODataLib ライブラリは、直接ダウンロードすることも、NuGet を使用してコード プロジェクトで参照することもできます。具体的な ODataLib パッケージは、[OData][OData]、[Edm][Edm]、および [Spatial][Spatial] です。

## <a name="create-queue"></a><span class="short-header">キューの作成</span>方法: キューを作成する

**CloudQueueClient** オブジェクトを使用すると、キューの参照オブジェクトを取得できます。
次のコードでは、**CloudQueueClient** オブジェクトを作成します。このガイドの
すべてのコードでは、Azure アプリケーションのサービス構成に
格納されているストレージ接続文字列を使用します。**CloudStorageAccount** オブジェクト
を作成する方法は他にもあります。詳細については、「[CloudStorageAccount][CloudStorageAccount]
」を参照してください。

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

**queueClient** オブジェクトを使用して、使用するキューへの参照を
取得します。キューが存在しない場合は作成できます。

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Create the queue if it doesn't already exist
    queue.CreateIfNotExists();

## <a name="insert-message"> </a><span class="short-header">メッセージの挿入</span>方法: メッセージをキューに挿入する

既存のキューにメッセージを挿入するには、最初に新しい
**CloudQueueMessage** を作成します。次に、**AddMessage** メソッドを呼び出します。
**CloudQueueMessage** は、文字列 (UTF-8 形式) または
**バイト**配列から作成できます。次のコードでは、キューが存在しない
場合は作成し、メッセージ "Hello, World" を挿入します。

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

## <a name="peek-message"></a><span class="short-header">次のメッセージのピーク</span>方法: 次のメッセージをピークする

**PeekMessage** メソッドを呼び出すと、キューの先頭にある
メッセージをキューから削除せずにピークできます。

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

## <a name="change-contents"></a><span class="short-header">メッセージの内容の変更</span>方法: キューに配置されたメッセージの内容を変更する

キュー内のメッセージの内容をインプレースで変更できます。メッセージ
が作業タスクを表している場合は、この機能を使用して、作業タスクの
状態を更新できます。次のコードでは、キュー メッセージを
新しい内容に更新し、表示タイムアウトを設定して、60 秒延長
します。これにより、メッセージに関連付けられている作業の状態が保存され、
クライアントにメッセージの操作を続行する時間が 1 分与えられます。この
方法を使用すると、キュー メッセージに対する複数の手順から成る
ワークフローを追跡でき、ハードウェアまたは
ソフトウェアの問題が原因で処理手順が失敗した場合に最初からやり直す必要がなくなります。通常は、
さらに再試行回数を保持し、メッセージの再試行回数が
*n* 回を超えた場合はメッセージを削除するようにします。こうすることで、処理するたびに
アプリケーション エラーをトリガーするメッセージから保護されます。

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

## <a name="get-message"></a><span class="short-header">次のメッセージのデキュー</span>方法: 次のメッセージをデキューする

コードでは、2 つの手順でキューからメッセージをデキューします。
**GetMessage** を呼び出すと、キュー内の次のメッセージを取得します。**GetMessage** から
返されたメッセージは、このキューからメッセージを読み取る他のコードから
参照できなくなります。既定では、このメッセージを参照できない
状態は 30 秒間続きます。キューからのメッセージの削除を完了するには、
**DeleteMessage** も呼び出す必要があります。このようにメッセージを 2 つの手順で削除
することで、ハードウェアまたはソフトウェアの問題が原因で
コードによるメッセージの処理が失敗した場合に、コードの別のインスタンスで
同じメッセージを取得し、もう一度処理することができます。コードでは、メッセージが処理された直後に **DeleteMessage** を
呼び出します。

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

## <a name="advanced-get"></a><span class="short-header">その他のデキュー オプション</span>方法: 追加オプションを利用してメッセージをデキューする

キューからのメッセージの取得をカスタマイズする方法は 2 つあります。
1 つ目の方法では、(最大 32 個の) メッセージのバッチを取得できます。2 つ目の方法では、
コードで各メッセージを完全に処理できるように、非表示タイムアウトの
設定を長くまたは短くすることができます。次のコード例では、
**GetMessages** メソッドを使用して、1 回の呼び出しで 20 個のメッセージを取得します。その後、
**foreach** ループを使用して、各メッセージを処理します。また、各メッセージの
非表示タイムアウトを 5 分に設定します。この 5 分の非表示期間は、
すべてのメッセージに対して同時に開始します。そのため、
**GetMessages** の呼び出しから 5 分が経過すると、削除されていないすべての
メッセージが再び表示されます。

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

## <a name="get-queue-length"></a><span class="short-header">キューの長さの取得</span>方法: キューの長さを取得する

キュー内のメッセージの概数を取得できます。
**FetchAttributes** メソッドは、メッセージ数などの
キューの属性を取得するようキュー サービスに要求します。**ApproximateMethodCount**
 プロパティは、キュー サービスを呼び出さずに、
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

## <a name="delete-queue"></a><span class="short-header">キューの削除</span>方法: キューを削除する

キューおよびキューに格納されているすべてのメッセージを削除するには、キュー オブジェクトの
**Delete** メソッドを呼び出します。

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Delete the queue.
    queue.Delete();

## <a name="next-steps"></a>次のステップ

これで、キュー ストレージの基本を学習できました。さらに複雑な
ストレージ タスクを実行する方法については、次のリンク先を参照してください。

-   利用可能な API の詳細については、キュー サービスのリファレンス ドキュメントを参照してください。
    -   [.NET 用ストレージ クライアント ライブラリ リファレンス][.NET 用ストレージ クライアント ライブラリ リファレンス]
    -   [REST API リファレンス][REST API リファレンス]
-   Azure Storage を使用して実行できるさらに高度なタスクについては、「[Azure のデータの格納とアクセス][Azure のデータの格納とアクセス]」を参照してください。
-   Azure Websites のバックエンド プロセスで Azure Storage を使用する方法については、「[Get Started with the Azure WebJobs SDK (Azure Web ジョブ SDK の概要)][Get Started with the Azure WebJobs SDK (Azure Web ジョブ SDK の概要)]」を参照してください。
-   Azure でデータを格納するための追加のオプションについては、他の機能ガイドも参照してください。
    -   構造化データの格納には、[テーブル ストレージ][テーブル ストレージ]を使用します。
    -   非構造化データの格納には、[BLOB ストレージ][BLOB ストレージ]を使用します。
    -   リレーショナル データの格納には、[SQL データベース][SQL データベース]を使用します。

  [次のステップ]: #next-steps
  [NuGet]: https://www.nuget.org/packages/WindowsAzure.Storage/
  [Azure SDK for .NET]: /ja-jp/downloads/
  [方法: プログラムでキュー ストレージにアクセスする]: #configure-access
  [キュー ストレージとは]: #what-is
  [概念]: #concepts
  [Azure のストレージ アカウントの作成]: #create-account
  [Azure のストレージ接続文字列の設定]: #setup-connection-string
  [方法: キューを作成する]: #create-queue
  [方法: メッセージをキューに挿入する]: #insert-message
  [方法: 次のメッセージをピークする]: #peek-message
  [方法: キューに配置されたメッセージの内容を変更する]: #change-contents
  [方法: 次のメッセージをデキューする]: #get-message
  [方法: 追加オプションを利用してメッセージをデキューする]: #advanced-get
  [方法: キューの長さを取得する]: #get-queue-length
  [方法: キューを削除する]: #delete-queue
  [.NET デベロッパー センター]: http://www.windowsazure.com/ja-jp/develop/net/#
  [OData]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
  [Edm]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
  [Spatial]: http://nuget.org/packages/System.Spatial/5.0.2
  [CloudStorageAccount]: http://msdn.microsoft.com/ja-jp/library/microsoft.windowsazure.cloudstorageaccount_methods.aspx
  [.NET 用ストレージ クライアント ライブラリ リファレンス]: http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
  [REST API リファレンス]: http://msdn.microsoft.com/ja-jp/library/windowsazure/dd179355
  [Azure のデータの格納とアクセス]: http://msdn.microsoft.com/ja-jp/library/windowsazure/gg433040.aspx
  [Get Started with the Azure WebJobs SDK (Azure Web ジョブ SDK の概要)]: /ja-jp/documentation/articles/websites-dotnet-webjobs-sdk-get-started/
  [テーブル ストレージ]: /ja-jp/documentation/articles/storage-dotnet-how-to-use-tables/
  [BLOB ストレージ]: /ja-jp/documentation/articles/storage-dotnet-how-to-use-blobs/
  [SQL データベース]: /ja-jp/documentation/articles/sql-database-dotnet-how-to-use/
