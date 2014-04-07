<properties linkid="dev-net-2-how-to-queue-service" urlDisplayName="キュー サービス (2.0)" pageTitle="キュー ストレージ サービスを使用する方法 - Windows Azure" metaKeywords="Azure キューの概要   Azure の非同期処理   Azure キュー   Azure キュー ストレージ   Azure キュー .NET   Azure キュー ストレージ .NET   Azure キュー C#   Azure キュー ストレージ C#" description="Windows Azure キュー ストレージ サービスを使用して、キューの作成および削除、キュー メッセージの挿入、ピーク、取得、および削除を行う方法を学習します。" metaCanonical="" disqusComments="1" umbracoNaviHide="1" title="キュー ストレージ サービスを使用する方法" />



# キュー ストレージ サービスを使用する方法

<div class="dev-center-tutorial-selector">
<a href="/ja-jp/develop/net/how-to-guides/queue-service-v17/" title="バージョン 1.7">バージョン 1.7</a>
<a href="/ja-jp/develop/net/how-to-guides/queue-service/" title="バージョン 2.0" class="current">バージョン 2.0</a> 
</div>


このガイドでは、Windows Azure キュー ストレージ サービスを使用して
一般的なシナリオを実行する方法について説明します。サンプルは C\# コードで記述され、Windows Azure .NET 用ストレージ クライアント (バージョン 2.0) を利用しています。キュー メッセージの**挿入**、**ピーク**、
**取得**、および**削除**と、**キューの作成および削除**の各シナリオに
ついて説明します。キューの詳細については、「[次の手順][]」の
セクションを参照してください。

<h2>目次</h2>

-   [キュー ストレージとは][]
-   [概念][]
-   [Windows Azure のストレージ アカウントの作成][]
-   [Windows Azure のストレージ接続文字列の設定][]
-   [方法: .NET を使用してキューにプログラムでアクセスする][]
-   [方法: キューを作成する][]
-   [方法: メッセージをキューに挿入する][]
-   [方法: 次のメッセージをピークする][]
-   [方法: キューに配置されたメッセージの内容を変更する][]
-   [方法: 次のメッセージをデキューする][]
-   [方法: 追加オプションを利用してメッセージをデキューする][]
-   [方法: キューの長さを取得する][]
-   [方法: キューを削除する][]
-   [次の手順][]

[WACOM.INCLUDE [howto-queue-storage](../includes/howto-queue-storage.md)]

<h2><a name="create-account"></a><span  class="short-header">アカウントの作成</span>Windows Azure のストレージ アカウントの作成</h2>

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

<h2><a name="setup-connection-string"></a><span  class="short-header">接続文字列の設定</span>Windows Azure のストレージ接続文字列の設定</h2>

Windows Azure .NET 用ストレージ クライアント ライブラリでは、ストレージ接続文字列を
使用して、ストレージ サービスにアクセスするためのエンドポイント
および資格情報を構成できます。ストレージ接続文字列は、コード内にハードコーディングするのではなく、構成ファイルの中で指定します。

- Windows Azure のクラウド サービスを使用するときには、Windows Azure サービス構成システム ('*.csdef' ファイルおよび '*.cscfg' ファイル) を使用して接続文字列を格納することをお勧めします。
- Windows Azure の Web サイトまたは Windows Azure の仮想マシンを使用する場合や、Windows Azure の外部で実行される .NET アプリケーションを作成する場合は、.NET 構成システム ('web.config' ファイル、'app.config' ファイルなど) を使用して接続文字列を格納することをお勧めします。

いずれの場合でも、このガイドで後ほど説明する `CloudConfigurationManager.GetSetting` メソッドを使用して接続文字列を取得できます。

### クラウド サービスを使用する場合の接続文字列の構成

サービス構成メカニズムは、Windows Azure のクラウド サービスのプロジェクトに特有のものであり、これを使用すると、アプリケーションを再展開することなく Windows Azure の管理ポータルから構成設定を動的に変更できます。

Windows Azure サービス構成で接続文字列を構成するには:

1. Visual Studio のソリューション エクスプローラーで、Windows Azure 展開プロジェクトの **[ロール]** フォルダー内の Web ロールまたは Worker ロールを右クリックし、**[プロパティ]** をクリックします。

	![Blob5](./media/storage-dotnet-how-to-use-queues-20/blob5.png)

2. **[設定]** タブをクリックし、**[設定の追加]** をクリックします。
 
	![Blob6](./media/storage-dotnet-how-to-use-queues-20/blob6.png)

	新しい **[Setting1]** エントリが設定グリッドに表示されます。

3. 新しい **[Setting1]** エントリの **[種類]** ボックスの一覧で、**[接続文字列]** をクリックします。

	![Blob7](./media/storage-dotnet-how-to-use-queues-20/blob7.png)

4. **[Setting1]** エントリの右端にある **[...]** をクリックします。**[ストレージ アカウント接続文字列]** ダイアログ ボックスが開きます。

5. ストレージ エミュレーター (ローカル コンピューターでシミュレートされた Windows Azure のストレージ) をターゲットとするか、クラウド内の実際のストレージ アカウントをターゲットとするかを選択します。このガイドに記載されているコードは、どちらのオプションにも対応しています。以前に Windows Azure で作成したストレージ アカウントにキュー データを格納する場合は、このチュートリアルの前の手順からコピーした**プライマリ アクセス キー**の値を入力します。

	![Blob8](./media/storage-dotnet-how-to-use-queues-20/blob8.png)

6. エントリの **[名前]** を **Setting1** から **StorageConnectionString** などの "わかりやすい" 名前に変更します。この接続文字列は、このガイドの後半のコードで参照します。

	![Blob9](./media/storage-dotnet-how-to-use-queues-20/blob9.png)
	
### .NET 構成を使用した接続文字列の構成

Windows Azure のクラウド サービスではないアプリケーション (前のセクションを参照) を記述する場合は、.NET 構成システム ('web.config'、'app.config' など) を使用することをお勧めします。これには、Windows Azure の Web サイトや Windows Azure の仮想マシンのほか、Windows Azure の外部で実行されるアプリケーションも含まれます。次のように、'<appSettings>' 要素を使用して接続文字列を格納します。

	<configuration>
  		<appSettings>
    		<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=[AccountName];AccountKey=[AccountKey" />
  		</appSettings>
	</configuration>

ストレージ接続文字列の詳細については、「[接続文字列の構成方法][]」を参照してください。
	
これで、このガイドのハウツー タスクを実行する準備が整いました。

<h2><a name="access"></a><span  class="short-header">プログラムでのアクセス</span>方法: .NET を使用してキューにプログラムでアクセスする</h2>

<h3>アセンブリの取得</h3>
NuGet を使用して 'Microsoft.WindowsAzure.Storage.dll' アセンブリを取得できます。**ソリューション エクスプローラー**でプロジェクトを右クリックし、**[NuGet パッケージの管理]** をクリックします。"WindowsAzure.Storage" をオンライン検索し、**[インストール]** をクリックして Windows Azure のストレージのパッケージと依存関係をインストールします。

'Microsoft.WindowsAzure.Storage.dll' は、<a href="http://www.windowsazure.com/ja-jp/develop/net/#">.NET デベロッパー センター</a>からダウンロードできる Windows Azure SDK for .NET 2.0 にも含まれています。アセンブリは、'%Program Files%\Microsoft SDKs\Windows Azure\.NET SDK\v2.0\ref\' ディレクトリにインストールされます。

<h3>名前空間宣言</h3>
プログラムを使用して Windows Azure のストレージにアクセスするすべての C\# ファイルの冒頭部分に、名前空間を宣言する次のコードを追加します。

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
	using Microsoft.WindowsAzure.Storage.Queue;

'Microsoft.WindowsAzure.Storage.dll' アセンブリを参照してください。

<h3>接続文字列の取得</h3>
**CloudStorageAccount** 型を使用してストレージ アカウント情報を表すことができます。Windows Azure プロジェクト 
テンプレートを使用している場合や、
Microsoft.WindowsAzure.CloudConfigurationManager への
参照がある場合は、**CloudConfigurationManager** 型を
使用して Windows Azure サービス構成からストレージ接続文字列と
ストレージ アカウント情報を取得できます。

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

作成しているアプリケーションに Microsoft.WindowsAzure.CloudConfigurationManager への参照が含まれておらず、接続文字列が web.config または app.config に格納されている場合は、**ConfigurationManager** を使用して接続文字列を取得できます。System.Configuration.dll への参照をプロジェクトに追加し、対応する名前空間宣言を追加する必要があります。

	using System.Configuration;
	...
	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
		ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString);

<h3>ODataLib 依存</h3>
.NET 用ストレージ クライアント ライブラリの ODataLib 依存は、WCF Data Services ではなく、NuGet から入手できる ODataLib (バージョン 5.0.2) パッケージで解決されます。ODataLib ライブラリは、直接ダウンロードすることも、NuGet を使用してコード プロジェクトで参照することもできます。具体的な ODataLib パッケージは、[OData]、[Edm]、および [Spatial] です。

<h2><a name="create-queue"></a><span  class="short-header">キューを作成する</span>方法: キューを作成する</h2>

**CloudQueueClient** オブジェクトを使用すると、キューの参照オブジェクトを取得できます。
次のコードでは、**CloudQueueClient** オブジェクトを作成します。このガイドのすべてのコードでは、Windows Azure アプリケーションのサービス構成に格納されているストレージ接続文字列を使用します。**CloudStorageAccount** オブジェクトを作成する方法は他にもあります。詳細については、[CloudStorageAccount][] のドキュメントを参照してください。

    // 接続文字列からストレージ アカウントを取得します
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // キュー クライアントを作成します
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

**queueClient** オブジェクトを使用して、使用するキューへの参照を取得します。キューが存在しない場合は作成できます。

    // キューへの参照を取得します
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // キューが存在しない場合は作成します
    queue.CreateIfNotExists();

<h2><a name="insert-message"> </a><span  class="short-header">メッセージを挿入する</span>方法: メッセージをキューに挿入する</h2>

既存のキューにメッセージを挿入するには、最初に新しい 
**CloudQueueMessage** を作成します。次に、**AddMessage** メソッドを呼び出します。A
**CloudQueueMessage** は、文字列 (UTF-8 形式) または**バイト**配列から作成できます。次のコードでは、キューが存在しない場合は
作成し、メッセージ 'Hello, World' を挿入します。

    // 接続文字列からストレージ アカウントを取得します。
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // キュー クライアントを作成します。
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // キューへの参照を取得します。
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // キューが存在しない場合は作成します。
    queue.CreateIfNotExists();

    // メッセージを作成して、キューに追加します。
    CloudQueueMessage message = new CloudQueueMessage("Hello, World");
    queue.AddMessage(message);

<h2><a name="peek-message"></a><span  class="short-header">次のメッセージのピーク</span>方法: 次のメッセージをピークにする</h2>

**PeekMessage** メソッドを呼び出すと、キューの先頭にあるメッセージをキューから削除せずにピークできます。

    // 接続文字列からストレージ アカウントを取得します
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // キュー クライアントを作成します
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // キューへの参照を取得します
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // 次のメッセージをピークします
    CloudQueueMessage peekedMessage = queue.PeekMessage();

	// 詳細を表示します。
	Console.WriteLine(peekedMessage.AsString);

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

    // 接続文字列からストレージ アカウントを取得します。
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // キュー クライアントを作成します。
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // キューへの参照を取得します。
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

	// キューからメッセージを取得し、メッセージ内容を更新できるようにします。
    CloudQueueMessage message = queue.GetMessage();
    message.SetMessageContent("Updated contents.") ;
    queue.UpdateMessage(message, 
        TimeSpan.FromSeconds(0.0),  // Make it visible immediately.
        MessageUpdateFields.Content | MessageUpdateFields.Visibility);

<h2><a name="get-message"></a><span  class="short-header">次のメッセージをデキューする</span>方法: 次のメッセージをデキューする</h2>

コードでは、2 つの手順でキューからメッセージをデキューします。**GetMessage** を呼び出すと、キュー内の次のメッセージを取得します。**GetMessage** から返されたメッセージは、このキューからメッセージを読み取る他のコードから参照できなくなります。既定では、このメッセージを参照できない状態は 30 秒間続きます。また、キューからのメッセージの削除を完了するには、**DeleteMessage** を呼び出す必要があります。このようにメッセージを 2 つの手順で削除することで、
ハードウェアまたはソフトウェアの問題が原因でコードによるメッセージの処理が
失敗した場合に、コードの別のインスタンスで同じメッセージを取得し、もう一度
処理することができます。コードでは、メッセージが処理された直後に **DeleteMessage** を呼び出します。

    // 接続文字列からストレージ アカウントを取得します
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // キュー クライアントを作成します
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // キューへの参照を取得します
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // 次のメッセージを取得します
    CloudQueueMessage retrievedMessage = queue.GetMessage();

    // 30 秒未満でメッセージを処理した後、メッセージを削除します
    queue.DeleteMessage(retrievedMessage);

<h2><a name="advanced-get"></a><span  class="short-header">その他のデキュー オプション</span>方法: 追加オプションを利用してメッセージをデキューする</h2>

キューからのメッセージの取得をカスタマイズする方法は 2 つあります。
1 つ目の方法では、(最大 32 個の) メッセージのバッチを取得できます。2 つ目の方法では、
コードで各メッセージを完全に処理できるように、非表示タイムアウトの設定を
長くまたは短くすることができます。次のコード例では、**GetMessages** メソッドを使用して、1 回の呼び出しで 20 個のメッセージを取得します。その後、**foreach** ループを使用して、各メッセージを処理します。また、各メッセージの
非表示タイムアウトを 5 分に設定します。この 5 分の非表示期間は、すべてのメッセージに対して同時に開始します。そのため、**GetMessages** の呼び出しから 5 分が経過すると、削除されていないすべてのメッセージが再び表示されます。

    // 接続文字列からストレージ アカウントを取得します。
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // キュー クライアントを作成します。
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // キューへの参照を取得します。
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    foreach (CloudQueueMessage message in queue.GetMessages(20, TimeSpan.FromMinutes(5)))
    {
        // Process all messages in less than 5 minutes, deleting each message after processing.
        queue.DeleteMessage(message);
    }

<h2><a name="get-queue-length"></a><span  class="short-header">キューの長さを取得する</span>方法: キューの長さを取得する</h2>

キュー内のメッセージの概数を取得できます。**FetchAttributes** メソッドは、メッセージ数などのキューの属性を取得するようにキュー サービスに要求します。**ApproximateMethodCount** プロパティは、キュー サービスを呼び出さずに、**FetchAttributes** メソッドによって取得された最後の値を返します。

    // 接続文字列からストレージ アカウントを取得します。
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // キュー クライアントを作成します。
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // キューへの参照を取得します。
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

	// キューの属性を取得します。
	queue.FetchAttributes();

    // キャッシュされているメッセージの概数を取得します。
    int? cachedMessageCount = queue.ApproximateMessageCount;

	// メッセージの数を表示します。
	Console.WriteLine("Number of messages in queue: " + cachedMessageCount);

<h2><a name="delete-queue"></a><span  class="short-header">キューを削除する</span>方法: キューを削除する</h2>

キューおよびキューに格納されているすべてのメッセージを削除するには、キュー オブジェクトの **Delete** メソッドを呼び出します。

    // 接続文字列からストレージ アカウントを取得します。
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // キュー クライアントを作成します。
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // キューへの参照を取得します。
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // キューを削除します。
    queue.Delete();

<h2><a name="next-steps"></a>次の手順</h2>

これで、キュー ストレージの基本を学習できました。さらに複雑な
ストレージ タスクを実行する方法については、次のリンク先を参照してください。

<ul>
<li>利用可能な API の詳細については、キュー サービスのリファレンス ドキュメントを参照してください。
  <ul>
    <li><a href="http://msdn.microsoft.com/ja-jp/library/windowsazure/wa_storage_api_ref_reference_home.aspx">.NET 用ストレージ クライアント ライブラリ リファレンス</a>
    </li>
    <li><a href="http://msdn.microsoft.com/ja-jp/library/windowsazure/dd179355">REST API リファレンス</a></li>
  </ul>
</li>
<li>Windows Azure のストレージを使用して実行できるさらに高度なタスクについては、「<a href="http://msdn.microsoft.com/ja-jp/library/windowsazure/gg433040.aspx">Windows Azure のデータの格納とアクセス</a>」を参照してください。</li>
<li>Windows Azure でデータを格納するための追加のオプションについては、他の機能ガイドも参照してください。
  <ul>
    <li>構造化データの格納には、<a href="/ja-jp/develop/net/how-to-guides/table-services/">テーブル ストレージ</a>を使用します。</li>
    <li>非構造化データの格納には、<a href="/ja-jp/develop/net/how-to-guides/blob-storage/">BLOB ストレージ</a>を使用します。</li>
    <li>リレーショナル データの格納には、<a href="/ja-jp/develop/net/how-to-guides/sql-database/">SQL データベース</a>を使用します。</li>
  </ul>
</li>
</ul>



  [次の手順]: #next-steps
  [キュー ストレージとは]: #what-is
  [概念]: #concepts
  [Windows Azure のストレージ アカウントの作成]: #create-account
  [Windows Azure のストレージ接続文字列の設定]: #setup-connection-string
  [方法: .NET を使用してキューにプログラムでアクセスする]: #access
  [方法: キューを作成する]: #create-queue
  [方法: メッセージをキューに挿入する]: #insert-message
  [方法: 次のメッセージをピークする]: #peek-message
  [方法: キューに配置されたメッセージの内容を変更する]: #change-contents
  [方法: 次のメッセージをデキューする]: #get-message
  [方法: 追加オプションを利用してメッセージをデキューする]: #advanced-get
  [方法: キューの長さを取得する]: #get-queue-length
  [方法: キューを削除する]: #delete-queue
  [Windows Azure SDK for .NET をダウンロードしてインストールする]: /ja-jp/develop/net/
  [Visual Studio で Windows Azure プロジェクトを作成する]: http://msdn.microsoft.com/ja-jp/library/windowsazure/ee405487.aspx 
  [CloudStorageAccount]: http://msdn.microsoft.com/ja-jp/library/microsoft.windowsazure.cloudstorageaccount_methods.aspx
  [Windows Azure のデータの格納とアクセス]: http://msdn.microsoft.com/ja-jp/library/windowsazure/gg433040.aspx
  [Windows Azure のストレージ チーム ブログ (このページは英語の場合があります)]: http://blogs.msdn.com/b/windowsazurestorage/
  [接続文字列の構成]: http://msdn.microsoft.com/ja-jp/library/windowsazure/ee758697.aspx
  [OData]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
  [Edm]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
  [空間]: http://nuget.org/packages/System.Spatial/5.0.2

