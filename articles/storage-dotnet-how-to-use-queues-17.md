<properties linkid="dev-net-2-how-to-queue-service" urlDisplayName="キュー サービス (2.0)" pageTitle="キュー ストレージ サービスを使用する方法 | Azure" metaKeywords="Azure キューの概要, Azure の非同期処理, Azure キュー, Azure キュー ストレージ, Azure キュー .NET, Azure キュー ストレージ .NET, Azure キュー C#, Azure キュー ストレージ C#" description="Azure キュー ストレージ サービスを使用して、キューの作成と削除、キュー メッセージの挿入、ピーク、取得、および削除を行う方法を学習します。" metaCanonical="" services="storage" documentationCenter=".NET" title="キュー ストレージ サービスを使用する方法" authors="" solutions="" manager="paulettm" editor="cgronlun" />





# キュー ストレージ サービスを使用する方法

<div class="dev-center-tutorial-selector">
<a href="/ja-jp/develop/net/how-to-guides/queue-service-v17/" title="バージョン 1.7" class="current">バージョン 1.7</a>
<a href="/ja-jp/develop/net/how-to-guides/queue-service/" title="バージョン 2.0">バージョン 2.0</a> 
</div>


このガイドでは、Azure キュー ストレージ サービスを使用して
一般的なシナリオを実行する方法について説明します。サンプルは C\# コードで記述され、
.NET API を利用しています。キュー メッセージの**挿入**、
**ピーク**、**取得**、および**削除**と、
**キューの作成と削除**の各シナリオについて説明します。キューの詳細については、
「[次のステップ][次のステップ]」のセクションを参照してください。

<h2>目次</h2>

-   [キュー ストレージとは][キュー ストレージとは]
-   [概念][概念]
-   [Azure ストレージ アカウントの作成][Azure ストレージ アカウントの作成]
-   [Azure のストレージ接続文字列の設定][Azure のストレージ接続文字列の設定]
-   [方法: .NET を使用してキューにプログラムでアクセスする][方法: .NET を使用してキューにプログラムでアクセスする]
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

<h2><a name="create-account"></a><span  class="short-header">アカウントの作成</span>Azure のストレージ アカウントの作成</h2>

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

<h2><a name="setup-connection-string"></a><span  class="short-header">接続文字列の設定</span>Azure のストレージ接続文字列の設定</h2>

Azure .NET ストレージ API では、ストレージ接続文字列を使用して、
ストレージ サービスにアクセスするためのエンドポイントおよび資格情報を
構成できます。ストレージ接続文字列は、コード内にハードコーディングするのではなく、構成ファイルの中で指定します。

- Azure のクラウド サービスを使用するときには、Azure サービス構成システム (`*.csdef` ファイルおよび `*.cscfg` ファイル) を使用して接続文字列を格納することをお勧めします。
- Azure の Web サイトまたは Azure の仮想マシンを使用する場合には、.NET 構成システム (`web.config` ファイルなど) を使用して接続文字列を格納することをお勧めします。

いずれの場合でも、このガイドで後ほど説明する `CloudConfigurationManager.GetSetting` メソッドを使用して接続文字列を取得できます。

### クラウド サービスを使用する場合の接続文字列の構成

サービス構成メカニズムは、Azure のクラウド サービスのプロジェクトに
特有のものであり、これを使用すると、アプリケーションを再展開する
ことなく Azure の管理ポータルから構成設定を動的に変更
できます。

Azure サービス構成で接続文字列を
構成するには:

1. Visual Studio のソリューション エクスプローラーで、Azure 展開プロジェクトの **[ロール]** フォルダー内の Web ロールまたは worker ロールを右クリックし、**[プロパティ]** をクリックします。
	![BLOB5](./media/storage-dotnet-how-to-use-queues-17/blob5.png)

2. **[設定]** タブをクリックし、**[設定の追加]** をクリックします。
	![BLOB6](./media/storage-dotnet-how-to-use-queues-17/blob6.png)

	新しい **[Setting1]** エントリが設定グリッドに表示されます。

3. 新しい **[Setting1]** エントリの **[種類]** ボックスの一覧で、**[接続文字列]** をクリックします。
	![BLOB7](./media/storage-dotnet-how-to-use-queues-17/blob7.png)

4. **[Setting1]** エントリの右端にある **[...]** をクリックします。**[ストレージ アカウント接続文字列]** ダイアログ ボックスが開きます。

5. ストレージ エミュレーター (ローカル コンピューターでシミュレートされた Azure ストレージ) をターゲットとするか、クラウド内の実際のストレージ アカウントをターゲットとするかを選択します。このガイドに記載されているコードは、どちらのオプションにも対応しています。以前に Azure で作成したストレージ アカウントに BLOB データを格納する場合は、このチュートリアルの前の手順からコピーした**プライマリ アクセス キー**の値を入力します。

	![BLOB8](./media/storage-dotnet-how-to-use-queues-17/blob8.png)

6. エントリの **[名前]** を **Setting1** から **StorageConnectionString** などの "わかりやすい" 名前に変更します。この接続文字列は、このガイドの後半のコードで参照します。

	![BLOB9](./media/storage-dotnet-how-to-use-queues-17/blob9.png)
	
### Web サイトまたは仮想マシンを使用する場合の接続文字列の構成

Web サイトまたは仮想マシンを使用する場合には、.NET 構成システム (`web.config` など) を使用することをお勧めします。`<appSettings>` 要素を使用して接続文字列を格納します。

	<configuration>
	    <appSettings>
		    <add key="StorageConnectionString"
			     value="DefaultEndpointsProtocol=https;AccountName=[AccountName];AccountKey=[AccountKey]" />
		</appSettings>
	</configuration>

ストレージ接続文字列の詳細については、「[接続文字列の構成][接続文字列の構成]」を参照してください。
	
これで、このガイドのハウツー タスクを実行する準備が整いました。

<h2><a name="access"></a><span  class="short-header">プログラムでのアクセス</span>方法: .NET を使用してキューにプログラムでアクセスする</h2>

プログラムを使用して Azure ストレージにアクセスするすべての C\# ファイルの
冒頭部分に、名前空間を宣言する次のコードを追加します。

    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.StorageClient;

**CloudStorageAccount** 型と
**CloudConfigurationManager** 型を
使用すると、Azure サービス構成からストレージ接続文字列と
ストレージ アカウント情報を取得できます。

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

<h2><a name="create-queue"></a><span  class="short-header">キューを作成する</span>方法: キューを作成する</h2>

**CloudQueueClient** オブジェクトを使用すると、キューの参照オブジェクトを取得できます。
次のコードでは、**CloudQueueClient** オブジェクトを作成します。このガイドのすべてのコードでは、
Azure アプリケーションのサービス構成に格納されている
ストレージ接続文字列を使用します。**CloudStorageAccount** オブジェクトを作成する方法は他にもあります。詳細については、[CloudStorageAccount][CloudStorageAccount]
のドキュメントを参照してください。

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
    queue.CreateIfNotExist();

<h2><a name="insert-message"> </a><span  class="short-header">メッセージを挿入する</span>方法: メッセージをキューに挿入する</h2>

既存のキューにメッセージを挿入するには、最初に新しい 
**CloudQueueMessage** を作成します。次に、**AddMessage** メソッドを呼び出します。A
**CloudQueueMessage** は、文字列 (UTF-8 形式) または**バイト**
配列から作成できます。次のコードでは、キューが存在しない場合は
作成し、メッセージ 'Hello, World' を挿入します。

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Create the queue if it doesn't already exist
    queue.CreateIfNotExist();

    // Create a message and add it to the queue
    CloudQueueMessage message = new CloudQueueMessage("Hello, World");
    queue.AddMessage(message);

<h2><a name="peek-message"></a><span  class="short-header">次のメッセージのピーク</span>方法: 次のメッセージをピークする</h2>

**PeekMessage** メソッドを呼び出すと、キューの先頭にあるメッセージを
キューから削除せずにピークできます。

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Peek at the next message
    CloudQueueMessage peekedMessage = queue.PeekMessage();

<h2><a name="change-contents"></a><span  class="short-header">メッセージの内容を変更する</span>方法: キューに配置されたメッセージの内容を変更する</h2>

キュー内のメッセージの内容をインプレースで変更できます。メッセージが
作業タスクを表している場合は、この機能を使用して、作業タスクの状態を
更新できます。次のコードでは、キュー メッセージを
新しい内容に更新し、表示タイムアウトを設定して、
60 秒延長します。これにより、メッセージに関連付けられている作業の状態が保存され、クライアントにメッセージの操作を続行する時間が 1 分与えられます。この方法を使用すると、キュー メッセージに対する複数の手順から成る
ワークフローを追跡でき、ハードウェアまたはソフトウェアの問題が
原因で処理手順が失敗した場合に最初からやり直す必要がなくなります。通常は、
さらに再試行回数を保持し、メッセージの再試行回数が *n* 回を超えた場合は
メッセージを削除するようにします。こうすることで、処理するたびに
アプリケーション エラーをトリガーするメッセージから保護されます。

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    CloudQueueMessage message = queue.GetMessage();
    message.SetMessageContent("Updated contents.") ;
    queue.UpdateMessage(message, 
        TimeSpan.FromSeconds(0.0),  // visible immediately
        MessageUpdateFields.Content | MessageUpdateFields.Visibility);

<h2><a name="get-message"></a><span  class="short-header">次のメッセージをデキューする</span>方法: 次のメッセージをデキューする</h2>

コードでは、2 つの手順でキューからメッセージをデキューします。
**GetMessage** を呼び出すと、キュー内の次のメッセージが取得されます。**GetMessage** から
返されたメッセージは、このキューからメッセージを読み取る他のコードから
参照できなくなります。既定では、このメッセージを参照できない状態は 30 秒間続きます。また、キューからのメッセージの削除を完了するには、
**DeleteMessage** を呼び出す必要があります。このようにメッセージを 2 つの手順で削除することで、
ハードウェアまたはソフトウェアの問題が原因でコードによるメッセージの処理が
失敗した場合に、コードの別のインスタンスで同じメッセージを取得し、もう一度
処理することができます。コードでは、メッセージが処理された直後に **DeleteMessage** を
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

<h2><a name="advanced-get"></a><span  class="short-header">その他のデキュー オプション</span>方法: 追加オプションを利用してメッセージをデキューする</h2>

キューからのメッセージの取得をカスタマイズする方法は 2 つあります。
1 つ目の方法では、(最大 32 個の) メッセージのバッチを取得できます。2 つ目の方法では、
コードで各メッセージを完全に処理できるように、非表示タイムアウトの設定を
長くまたは短くすることができます。次のコード例では、
**GetMessages** メソッドを使用して、1 回の呼び出しで 20 個のメッセージを取得します。その後、**foreach** ループを使用して、各メッセージを処理します。また、各メッセージの
非表示タイムアウトを 5 分に設定します。この 5 分の非表示期間は、
すべてのメッセージに対して同時に開始します。そのため、**GetMessages** の
呼び出しから 5 分が経過すると、削除されていないすべてのメッセージが
再び表示されます。

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    foreach (CloudQueueMessage message in queue.GetMessages(20, TimeSpan.FromMinutes(5)))
    {
        // Do processing for all messages in less than 5 minutes, deleting each message after processing
        queue.DeleteMessage(message);
    }

<h2><a name="get-queue-length"></a><span  class="short-header">キューの長さを取得する</span>方法: キューの長さを取得する</h2>

キュー内のメッセージの概数を取得できます。
**RetrieveApproximateMessageCount** メソッドは、キューにあるメッセージの数を
カウントするようキュー サービスに要求します。キュー サービスが
要求に応答した後にメッセージが追加または削除される可能性があるため、
カウントは概数にすぎません。**ApproximateMethodCount**
プロパティは、キュー サービスを呼び出さずに、
**RetrieveApproximateMessageCount** によって取得された最後の値を返します。

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Retrieve the approximate message count
    int freshMessageCount = queue.RetrieveApproximateMessageCount();

    // Retrieve the cached approximate message count
    int? cachedMessageCount = queue.ApproximateMessageCount;

<h2><a name="delete-queue"></a><span  class="short-header">キューを削除する</span>方法: キューを削除する</h2>

キューおよびキューに含まれているすべてのメッセージを削除するには、
キュー オブジェクトの **Delete** メソッドを呼び出します。

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Delete the queue
    queue.Delete();

<h2><a name="next-steps"></a>次のステップ</h2>

これで、キュー ストレージの基本を学習できました。さらに複雑な
ストレージ タスクを実行する方法については、次のリンク先を参照してください。

<ul>
<li>利用可能な API の詳細については、キュー サービスのリファレンス ドキュメントを参照してください。
  <ul>
    <li><a href="http://msdn.microsoft.com/ja-jp/library/windowsazure/wl_svchosting_mref_reference_home">.NET クライアント ライブラリ リファレンス</a>
    </li>
    <li><a href="http://msdn.microsoft.com/ja-jp/library/windowsazure/dd179355">REST API リファレンス</a></li>
  </ul>
</li>
<li>Azure のストレージを使用して実行できるさらに高度なタスクについては、「<a href="http://msdn.microsoft.com/ja-jp/library/windowsazure/gg433040.aspx">Azure のデータの格納とアクセス</a>」を参照してください。</li>
<li>Azure でデータを格納するための追加のオプションについては、他の機能ガイドも参照してください。
  <ul>
    <li>構造化データの格納には、<a href="/ja-jp/develop/net/how-to-guides/table-services/">テーブル ストレージ</a>を使用します。</li>
    <li>非構造化データの格納には、<a href="/ja-jp/develop/net/how-to-guides/blob-storage/">BLOB ストレージ</a>を使用します。</li>
    <li>リレーショナル データの格納には、<a href="/ja-jp/develop/net/how-to-guides/sql-database/">SQL データベース</a>を使用します。</li>
  </ul>
</li>
</ul>



  [次のステップ]: #next-steps
  [キュー ストレージとは]: #what-is
  [概念]: #concepts
  [Azure ストレージ アカウントの作成]: #create-account
  [Azure のストレージ接続文字列の設定]: #setup-connection-string
  [方法: .NET を使用してキューにプログラムでアクセスする]: #access
  [方法: キューを作成する]: #create-queue
  [方法: メッセージをキューに挿入する]: #insert-message
  [方法: 次のメッセージをピークする]: #peek-message
  [方法: キューに配置されたメッセージの内容を変更する]: #change-contents
  [方法: 次のメッセージをデキューする]: #get-message
  [方法: 追加オプションを利用してメッセージをデキューする]: #advanced-get
  [方法: キューの長さを取得する]: #get-queue-length
  [方法: キューを削除する]: #delete-queue
  

  
  [CloudStorageAccount]: http://msdn.microsoft.com/ja-jp/library/microsoft.windowsazure.cloudstorageaccount_methods.aspx
  [接続文字列の構成]: http://msdn.microsoft.com/ja-jp/library/windowsazure/ee758697.aspx

