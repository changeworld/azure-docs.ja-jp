<properties
	pageTitle="チュートリアル - .NET 向け Azure Batch ライブラリの概要"
	description="Azure Batch の基本的な概念と、単純なシナリオで Batch サービスを開発する方法について説明します。"
	services="batch"
	documentationCenter=".net"
	authors="yidingzhou"
	manager="timlt"
	editor=""/>

<tags
	ms.service="batch"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.tgt_pltfrm="na"
	ms.workload="big-compute"
	ms.date="05/04/2015"
	ms.author="yidingz"/>

#.NET 向け Azure Batch ライブラリの概要  

この記事には、Azure Batch サービス用 .NET ライブラリを使用した開発を始めるのに役立つ次の 2 つのチュートリアルが含まれています。

-	[チュートリアル 1: .NET 向け Azure Batch ライブラリ](#tutorial1)
-	[チュートリアル 2: .NET 向け Azure Batch Apps ライブラリ](#tutorial2)  


Azure Batch の背景情報とシナリオについては、[Azure Batch の技術概要に関するページ](batch-technical-overview.md)をご覧ください。

##<a name="tutorial1"></a>チュートリアル 1: .NET 向け Azure Batch ライブラリ

このチュートリアルでは、Azure Batch サービスを使用してプール内の仮想マシン間で分散計算をセットアップするコンソール アプリケーションを作成する方法を示します。このチュートリアルで作成するタスクは、Azure ストレージ内のファイルのテキストを評価して、最もよく使用される単語を返します。例は C# のコードで記述され、.NET 向け Azure Batch ライブラリを利用しています。


>[AZURE.NOTE]このチュートリアルを完了するには、Azure アカウントが必要です。数分で無料試用版のアカウントを作成することができます。詳細については、[Azure の無料評価版サイト](http://azure.microsoft.com/pricing/free-trial/)を参照してください。
>
>NuGet を使用して **Microsoft.Azure.Batch.dll** アセンブリを取得する必要があります。Visual Studio でプロジェクトを作成した後、**ソリューション エクスプローラー**でプロジェクトを右クリックし、**[NuGet パッケージの管理]** を選択します。"**Azure.Batch**" をオンラインで検索し、[インストール] をクリックして Azure Batch のパッケージと依存関係をインストールします。
>
>Nuget パッケージ マネージャーのバージョンが 2.8 以降であることを確認してください。バージョン番号は、Visual Studio の [ヘルプ] の [Microsoft Visual Studio のバージョン情報] ダイアログで確認できます。古いバージョンの Nuget パッケージ マネージャーを使用している場合は Visual Studio を更新する必要があります。または、Nuget 依存関係の正しいバージョンがダウンロードされていない可能性があります。
>
>また、ここで説明したコードに類似するサンプルについては、MSDN の [Azure Batch Hello World サンプル](https://code.msdn.microsoft.com/Azure-Batch-Sample-Hello-6573967c)をご覧ください。



###概念
Batch サービスは、スケーラブルな分散計算をスケジュールするために使用します。Batch サービスを使用すると、複数の仮想マシン間で分散される大規模なワークロードを実行できます。これらのワークロードは、集中的な計算を行うための効率的なサポートを提供します。Batch サービスを使用する際は次のリソースを使用します。

-	**アカウント** - サービス内の一意に識別されるエンティティ。すべての処理は、Batch アカウントを介して行われます。
-	**プール** - タスク アプリケーションが実行されるタスク仮想マシンのコレクション。
-	**タスク仮想マシン** - プールに割り当てられ、プールで実行されるジョブに割り当てられたタスクによって使用されるマシン。
-	**タスク仮想マシン ユーザー** - タスク仮想マシン上のユーザー アカウント。
-	**作業項目** - プール内のタスク仮想マシンで計算を実行する方法を指定します。
-	**ジョブ** - 作業項目の実行中のインスタンス。タスクのコレクションで構成されます。
-	**タスク** - ジョブに関連付けられ、タスク仮想マシン上で実行されるアプリケーション。
-	**ファイル** - タスクによって処理される情報を含みます。  

それでは、最も基本的な使用法から始めましょう。

###Azure Batch アカウントの作成
管理ポータルを使用して、Batch アカウントを作成できます。アカウントが作成されると、キーが支給されます。詳細については、[Azure Batch の技術概要に関するページ](batch-technical-overview.md)をご覧ください。

###方法: アカウントにプールを追加する
タスク仮想マシンのプールは、タスクを実行するときに最初に作成する必要があるリソースのセットです。

1.	Microsoft Visual Studio 2013 で、**[ファイル]** メニューの **[新規作成]** をクリックし、**[プロジェクト]** をクリックします。

2.	**[Windows]** で、**[Visual C#]** の **[コンソール アプリケーション]** をクリックします。プロジェクト名として「**GettingStarted**」、ソリューション名として「**AzureBatch**」と入力し、**[OK]** をクリックします。

3.	Program.cs の先頭部分に次の名前空間宣言を追加します。

		using Microsoft.Azure.Batch;
		using Microsoft.Azure.Batch.Auth;
		using Microsoft.Azure.Batch.Common;

	Microsoft.Azure.Batch.dll アセンブリを参照していることを確認してください。

4.	次の変数を Program クラスに追加します。

		private const string PoolName = "[name-of-pool]";
		private const int NumOfMachines = 3;
		private const string AccountName = "[name-of-batch-account]";
		private const string AccountKey = "[key-of-batch-account]";
		private const string Uri = "https://batch.core.windows.net";
	次の値を置き換えます: - **[name-of-pool]** - プールに使用する名前。 - **[name-of-batch-account]** - Batch アカウントの名前。 - **[key-of-batch-account]** - Batch アカウントに対して提供されたキー。
5.	次のコードを Main に追加します。このコードは、使用する資格情報を定義します。

		BatchCredentials cred = new BatchCredentials(AccountName, AccountKey);
6.	次のコードを Main に追加します。このコードは、処理を実行するために使うクライアントを作成します。

		IBatchClient client = BatchClient.Connect(Uri, cred);
7.	次のコードを Main に追加します。このコードは、プールが存在しない場合にプールを作成します。

		using (IPoolManager pm = client.OpenPoolManager())
		{
		   IEnumerable<ICloudPool> pools = pm.ListPools();
		   if (!pools.Select(pool => pool.Name).Contains(PoolName))
		   {
		      Console.WriteLine("The pool does not exist, creating now...");
		      ICloudPool newPool = pm.CreatePool(
		         PoolName,
		         osFamily: "3",
		         vmSize: "small",
		         targetDedicated: NumOfMachines);
		       newPool.Commit();
		    }
		}
		Console.WriteLine("Created pool {0}", PoolName);
		Console.ReadLine();
8.	プログラムを保存し、実行します。正常に追加されたプールのステータスが**アクティブ**になります。  

###方法: アカウントのプールを一覧表示する
既にあるプールの名前がわからない場合は、アカウントのプールの一覧を取得できます。

1.	次のコードを Main に追加します。このコードは、使用する資格情報を定義します。  

		BatchCredentials cred = new BatchCredentials(AccountName, AccountKey);
2.	次のコードを Main に追加します。このコードは、処理を実行するために使うクライアントを作成します。

		IBatchClient client = BatchClient.Connect(Uri, cred);

3.	Main プロシージャを次のコードに更新します。このコードは、アカウントのすべてのプールの名前と状態を書き込み、プールが存在しない場合はプールを作成します。

		BatchCredentials cred = new BatchCredentials(AccountName, AccountKey);
		IBatchClient client = BatchClient.Connect(Uri, cred);

		using (IPoolManager pm = client.OpenPoolManager())
		{
		    IEnumerable<ICloudPool> pools = pm.ListPools();

		    Console.WriteLine("Listing Pools\n=================");
		    foreach (var p in pools)
		    {
		        Console.WriteLine("Pool: " + p.Name + " State:" + p.State);
		    }  

		    if (!pools.Select(pool => pool.Name).Contains(PoolName))
		    {
		        Console.WriteLine("The pool does not exist, creating now...");
		        ICloudPool newPool = pm.CreatePool(
		           PoolName,
		           osFamily: "3",
		           vmSize: "small",
		           targetDedicated: NumOfMachines);
		        newPool.Commit();
		    }
		}
		Console.WriteLine("Created pool {0}. Press <Enter> to continue.", PoolName);
		Console.ReadLine();

4.	プログラムを保存し、実行します。次の出力が表示されます。

		Listing Pools
		=================
		Pool: gettingstarted State:Active
		Created pool gettingstarted. Press <Enter> to continue.

###方法: アカウントの作業項目を一覧表示する
既にある作業項目の名前がわからない場合は、アカウントの作業項目の一覧を取得できます。

1.	次のコードを Main の末尾に追加します。このコードは、アカウントのすべての作業項目の名前と状態を書き込みます。

		using (IWorkItemManager wm = client.OpenWorkItemManager())
		{
		   Console.WriteLine("Listing Workitems\n=================");
		   IEnumerable<ICloudWorkItem> workitems = wm.ListWorkItems();
		   foreach (var w in workitems)
		   {
		      Console.WriteLine("Workitem: " + w.Name + " State:" + w.State);
		   }
		}
		Console.WriteLine("Press <Enter> to continue.");
		Console.ReadLine();
7.	プログラムを保存し、実行します。この段階では作業項目を送信していないため、何も表示されません。作業項目の追加については、次のセクションで説明します。  

##方法: アカウントに作業項目を追加する
プール内でタスクを実行する方法を定義するには、作業項目を作成する必要があります。

1.	次の変数を Program クラスに追加します。

		private static readonly string WorkItemName = Environment.GetEnvironmentVariable("USERNAME") + DateTime.Now.ToString("yyyyMMdd-HHmmss");

2.	作業項目が作成されると、ジョブも作成されます。作業項目には名前を指定することができますが、ジョブには常に **job-0000000001** 形式の名前が割り当てられます。次のコードを Main (の作業項目を一覧表示するコードの前) に追加します。このコードは、作業項目を追加します。

		using (IWorkItemManager wm = client.OpenWorkItemManager())
		{
		   ICloudWorkItem cloudWorkItem = wm.CreateWorkItem(WorkItemName);
           cloudWorkItem.JobExecutionEnvironment = new JobExecutionEnvironment() {PoolName = PoolName};
		   cloudWorkItem.Commit();
		}
		Console.WriteLine("Workitem successfully added. Press <Enter> to continue.");
		Console.ReadLine();
3.	プログラムを保存し、実行します。正常に追加された作業項目のステータスが**アクティブ**になります。次の出力が表示されます。

		Listing Pools
		=================
		Pool: gettingstarted State:Active
		Created pool gettingstarted. Press <Enter> to continue.

		Workitem successfully added. Press <Enter> to continue.

		Listing Workitems
		=================
		Workitem: yidingz20141106-111211 State:Active
		Press <Enter> to continue.

###方法: ジョブにタスクを追加する
タスクが割り当てられていない作業項目は何も行いません。作業項目を作成し、ジョブが作成されたら、ジョブにタスクを追加できます。それでは、ジョブに簡単なタスクを追加してみましょう。

1.	次の変数を Program クラスに追加します。

		private const int Count = 4; // number of tasks that will run
		private const string JobName = "job-0000000001";

2.	次のコードを Main に追加します。このコードは、タスクをジョブに追加した後、その実行を待機し、結果を報告します。

		using (IWorkItemManager wm = client.OpenWorkItemManager())
		{
		    string taskName;
		    ICloudJob job = wm.GetJob(WorkItemName, JobName);
		    for (int i = 1; i <= Count; ++i)
		    {
		        taskName = "taskdata" + i;
		        string commandLine = String.Format("cmd /c echo I am {0}", taskName);
		        ICloudTask taskToAdd = new CloudTask(taskName, commandLine);
		        job.AddTask(taskToAdd);
		        job.Commit();
		        job.Refresh();
		    }

		    ICloudJob listjob = wm.GetJob(WorkItemName, JobName);
		    client.OpenToolbox().CreateTaskStateMonitor().WaitAll(listjob.ListTasks(),
		       TaskState.Completed, new TimeSpan(0, 30, 0));
		    Console.WriteLine("The tasks completed successfully. Terminating the workitem...");
		    wm.GetWorkItem(WorkItemName).Terminate();
		    foreach (ICloudTask task in listjob.ListTasks())
		    {
		        Console.WriteLine("Task " + task.Name + " says:\n" + task.GetTaskFile(Constants.StandardOutFileName).ReadAsString());
		    }
		    Console.ReadLine();
		}

3.	プログラムを保存し、実行します。結果は次のようになります。

		The tasks completed successfully. Terminating the workitem...
		Task taskdata1 says:
		I am taskdata1

		Task taskdata2 says:
		I am taskdata2

		Task taskdata3 says:
		I am taskdata3

###タスク処理プログラムを作成する
Hello World を VM 上で実行できるようにした後は、現実的な操作を試してみましょう。ここでは、タスク処理プログラムを作成し、タスクを実行するタスク仮想マシンにアップロードします。

1.	ソリューション エクスプローラーで、**AzureBatch** ソリューション内に **ProcessTaskData** という名前の新しいコンソール アプリケーション プロジェクトを作成します。

2.	Program.cs の先頭部分に次の名前空間宣言を追加します。

		using System.Net;

3.	次のコードを Main に追加します。このコードは、データを処理します。

		string blobName = args[0];
		int numTopN = int.Parse(args[1]);

		WebClient myWebClient = new WebClient();
		string content = myWebClient.DownloadString(blobName);

		string[] words = content.Split(' ');
		var topNWords =
		   words.
		   Where(word => word.Length > 0).
		   GroupBy(word => word, (key, group) => new KeyValuePair<String, long>(key, group.LongCount())).
		   OrderByDescending(x => x.Value).
		   Take(numTopN).
		   ToList();

		foreach (var pair in topNWords)
		{
		    Console.WriteLine("{0} {1}", pair.Key, pair.Value);
		}

4.	プロジェクトを保存した後、ビルドします。

###タスクを実行するためのリソースの準備

Batch サービスによる分散計算シナリオを作成する場合は、次の基本的なワークフローを使います。

1.	Azure ストレージ アカウントに分散計算シナリオで使用するファイルをアップロードします。これらのファイルは、Batch サービスがアクセスできるように、ストレージ アカウントに配置する必要があります。これらのファイルは、タスクの実行時にタスク仮想マシンに読み込まれます。
2.	依存バイナリ ファイルをストレージ アカウントにアップロードします。バイナリ ファイルとは、タスクによって実行されるプログラムと依存アセンブリを表します。これらのファイルはストレージからもアクセスできる必要があります。これらのファイルは、タスク仮想マシンに読み込まれます。
3.	タスク仮想マシンのプールを作成します。プールの作成時に、使用するタスク仮想マシンのサイズを割り当てることができます。タスクを実行すると、このプールから仮想マシンが割り当てられます。
4.	作業項目を作成します。作業項目を使って、タスクのジョブを管理できます。作業項目を作成すると、ジョブが自動的に作成されます。
5.	ジョブにタスクを追加します。各タスクは、アップロードされたプログラムを使用して、アップロードされたファイルの情報を処理します。
6.	出力の結果を監視します。  

手順 3. ～ 6. については既に説明しました。以降では、タスクを実行するために Azure Storage を準備する方法について説明します。

####ストレージ アカウントの取得
このチュートリアルの残りの部分を行うには、ストレージ アカウントが必要になります。ストレージ アカウントの取得方法については、「[Azure ストレージ アカウントの作成](#tutorial1_storage)」を参照してください

####データのアップロード

1. Azure Storage アカウントに "gettingstarted" という名前のコンテナーを作成します。この操作は、Azure ポータルで行うことができます。[アクセス] フィールドを必ず [パブリック コンテナー] に設定します。

2. "ProcessTaskData.exe" をコンテナーにアップロードします。

3. それぞれ次の段落の 1 つを含む 3 つのテキスト ファイル (taskdata1.txt、taskdata2.txt、taskdata3.txt) を作成し、コンテナーにアップロードします。

		You can use Azure Virtual Machines to provision on-demand, scalable compute infrastructure when you need flexible resources for your business needs. From the gallery, you can create virtual machines that run Windows, Linux, and enterprise applications such as SharePoint and SQL Server. Or, you can capture and use your own images to create customized virtual machines.

		Quickly deploy and manage powerful applications and services with Azure Cloud Services. Simply upload your application and Azure handles the deployment details - from provisioning and load balancing to health monitoring for continuous availability. Your application is backed by an industry leading 99.95% monthly SLA. You just focus on the application and not the infrastructure.

		Azure Web Sites provide a scalable, reliable, and easy-to-use environment for hosting web applications. Select from a range of frameworks and templates to create a web site in seconds. Use any tool or OS to develop your site with .NET, PHP, Node.js or Python. Choose from a variety of source control options including TFS, GitHub, and BitBucket to set up continuous integration and develop as a team. Expand your site functionality over time by leveraging additional Azure managed services like storage, CDN, and SQL Database.


>[AZURE.NOTE]運用環境では、共有アクセス署名を使用することを勧めします。


>[AZURE.NOTE]Azure Storage チームによる[ブログ記事](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx)に、ファイルをアップロードする場合に役立つ Azure Storage エクスプローラーの一覧が紹介されています。



###タスク送信コードの更新

1.	次の変数を Program クラスに追加します。

		private const string BlobPath = "[storage-path]";
	次の値を置き換えます: - **[storage-path]** - ストレージ内の BLOB へのパス。例: http://yiding.blob.core.windows.net/gettingstarted/

2. タスク送信コードを次のように更新します。

		string taskName;
		ICloudJob job = wm.GetJob(WorkItemName, JobName);
		for (int i = 1; i <= Count; ++i)
		{
		    taskName = "taskdata" + i;
		    IResourceFile programFile = new ResourceFile(BlobPath + "ProcessTaskData.exe", "ProcessTaskData.exe");
		    IResourceFile supportFile = new ResourceFile(BlobPath + taskName + ".txt", taskName);
		    string commandLine = String.Format("ProcessTaskData.exe {0} {1}", BlobPath + taskName + ".txt", 3);
		    ICloudTask taskToAdd = new CloudTask(taskName, commandLine);
		    taskToAdd.ResourceFiles = new List<IResourceFile>();
		    taskToAdd.ResourceFiles.Add(programFile);
		    taskToAdd.ResourceFiles.Add(supportFile);
		    job.AddTask(taskToAdd);
		}
		job.Commit();

3. プログラムを保存し、実行します。次のような結果が表示されます。

		Listing Pools
		=================
		Pool: gettingstarted State:Active
		Created pool gettingstarted. Press <Enter> to continue.

		Workitem successfully added. Press <Enter> to continue.

		Listing Workitems
		=================
		Workitem: yidingz20141106-132140 State:Active
		Press <Enter> to continue.

		The tasks completed successfully. Terminating the workitem...
		Task taskdata1 says:
		can 3
		you 3
		and 3

		Task taskdata2 says:
		and 5
		application 3
		the 3

		Task taskdata3 says:
		a 5
		and 5
		to 3

###方法: プールと作業項目を削除する
作業項目が処理された後は、プールと作業項目を削除することによってリソースを解放できます。

####プールの削除
1.	次のコードを Main の末尾に追加します。このコードは、プールを削除します。

		using (IPoolManager pm = client.OpenPoolManager())
		{
		   pm.DeletePool(PoolName);
		}
		Console.WriteLine("Pool successfully deleted.");
		Console.ReadLine();
2.	プログラムを保存し、実行します。


####作業項目の削除
1.	次のコードを Main に追加します。このコードは、作業項目を削除します。

		using (IWorkItemManager wm = client.OpenWorkItemManager())
		{
		   wm.DeleteWorkItem(WorkItemName);
		}
		Console.WriteLine("Workitem successfully deleted.");
		Console.ReadLine();
2.	プログラムを保存し、実行します。

###<a name="tutorial1_storage"></a>付録: Azure ストレージ アカウントの作成
このチュートリアルのコードを実行する前に、Azure のストレージ アカウントにアクセスできるようにしておく必要があります。

1.	[Azure の管理ポータル](http://manage.windowsazure.com/)にログインします。
2.	ナビゲーション ウィンドウの下部にある **[新規]** をクリックします。![][1]
3.	**[DATA　SERVICES]**、**[ストレージ]**、**[簡易作成]** の順にクリックします。![][2]

4.	**[URL]** に、ストレージ アカウントの URI で使用するサブドメイン名を入力します。文字数は 3 ～ 24 文字で、アルファベット小文字と数字を使用できます。この名前は、対応するサブスクリプションの BLOB リソース、キュー リソース、またはテーブル リソースのアドレス指定に使用される URI のホスト名になります。
5.	ストレージの配置先となる**場所またはアフィニティ グループ**を選択します。
6.	必要に応じて、Geo レプリケーションを有効にできます。
7.	**[ストレージ アカウントの作成]** をクリックします。  

Azure Storage の詳細については、[.NET から Azure BLOB ストレージを使用する方法](http://azure.microsoft.com/develop/net/how-to-guides/blob-storage/)に関するページをご覧ください。


##<a name="tutorial2"></a>チュートリアル 2: .NET 向け Azure Batch Apps ライブラリ
このチュートリアルでは、Batch Apps サービスを使って Azure Batch 上で並列計算ワークロードを実行する方法を示します。

Batch Apps は、Batch のワークロードを管理および実行するためのアプリケーション中心の方法を提供する Azure Batch の機能です。Batch Apps SDK を使用すると、パッケージを Batch 対応アプリケーションとして作成し、これらを自分のアカウントにデプロイしたり、他の Batch ユーザーが使用できるようにしたりできます。Batch には、データ管理、ジョブ監視、組み込みの診断とログ記録、およびタスク間依存関係のサポートも用意されています。さらに、ジョブの管理、ログの表示、出力のダウンロードを行える管理ポータルも用意されているため、自分でクライアントを記述する必要はありません。

Batch Apps シナリオでは、Batch Apps Cloud SDK を使用して、コードを記述します。コードでは、ジョブを並列タスクに分割し、これらのタスク間の依存関係を記述し、各タスクを実行する方法を指定します。このコードは、Batch アカウントにデプロイされます。クライアントは、ジョブの種類と REST API への入力ファイルを指定するだけで、ジョブを実行できます。

>[AZURE.NOTE]このチュートリアルを完了するには、Azure アカウントが必要です。数分で無料試用版のアカウントを作成することができます。詳細については、[Azure の無料評価版サイト](http://azure.microsoft.com/pricing/free-trial/)を参照してください。NuGet を使用すると、<a href="http://www.nuget.org/packages/Microsoft.Azure.Batch.Apps.Cloud/">Batch Apps Cloud</a> アセンブリと <a href="http://www.nuget.org/packages/Microsoft.Azure.Batch.Apps/">Batch Apps Client</a> アセンブリの両方を取得できます。Visual Studio でプロジェクトを作成した後、**ソリューション エクスプローラー**でプロジェクトを右クリックし、**[NuGet パッケージの管理]** を選択します。アプリケーションをクラウド対応にするプロジェクト テンプレートとアプリケーションをデプロイする機能が含まれた Batch Apps 用 Visual Studio 拡張機能は、<a href="https://visualstudiogallery.msdn.microsoft.com/8b294850-a0a5-43b0-acde-57a07f17826a">ここ</a>からダウンロードすることもできます。また、Visual Studio の [拡張機能と更新プログラム] で "**Batch Apps**" を検索することもできます。また、<a href="https://go.microsoft.com/fwLink/?LinkID=512183&clcid=0x409">MSDN でエンド ツー エンドのサンプル</a>を見つけることもできます。
>

###Azure Batch Apps の基礎
Batch は、既存のコンピューティング集中型アプリケーションに対応するように設計されています。Batch は、既存のアプリケーション コードを使用して、動的な仮想化された汎用環境でアプリケーションを実行します。アプリケーションを Batch Apps に対応させるには、いくつかの操作を行う必要があります。

1.	既存のアプリケーションの実行可能ファイル (従来のサーバー ファームまたはクラスターで実行するのと同じ実行可能ファイル) と必要なすべてのサポート ファイルを含む zip ファイルを作成します。この zip ファイルを、管理ポータルまたは REST API を使用して Batch アカウントにアップロードします。
2.	ワークロードをアプリケーションにディスパッチする "クラウド アセンブリ" の zip ファイルを作成し、管理ポータルまたは REST API を使用してアップロードします。クラウド アセンブリには、Cloud SDK 用の 2 つのコンポーネントが含まれています。
	1.	ジョブ スプリッター - ジョブを個別に処理できるタスクに分割します。たとえば、アニメーション シナリオで、ジョブ スプリッターは、ムービー ジョブを受け取って個々のフレームに分割します。
	2.	タスク プロセッサ - 特定のタスクに対してアプリケーション実行可能ファイルを呼び出します。たとえば、アニメーション シナリオで、タスク プロセッサは、レンダリング プログラムを起動して、現在のタスクで指定された 1 つのフレームをレンダリングします。
3.	Azure で有効になっているアプリケーションにジョブを送信する方法を提供します。これには、アプリケーションの UI のプラグインや Web ポータルを使用できます。また、実行パイプラインの一部としての自動サービスを使用することもできます。例については、MSDN の<a href="https://go.microsoft.com/fwLink/?LinkID=512183&clcid=0x409">サンプル</a>をご覧ください。



###Batch Apps の主要な概念
Batch Apps のプログラミングと使用モデルに含まれる主要な概念を次に示します。

####ジョブ
**ジョブ**は、ユーザーによって送信される 1 つの処理です。ユーザーは、ジョブを送信するときに、ジョブの種類、ジョブの設定、ジョブに必要なデータを指定します。これらの詳細は、ユーザーの代わりに、有効になっている実装から提供できます。また、場合によっては、ユーザーがクライアントを介してこの情報を明示的に提供することができます。ジョブでは結果が返されます。各ジョブは、プライマリ出力とオプションのプレビュー出力を持ちます。ジョブは、必要に応じて追加の出力を返すことができます。

####タスク
**タスク**は、ジョブの一部として実行される 1 つの処理です。ユーザーがジョブを送信すると、ジョブはより小さなタスクに分割されます。サービスによってこれらの個々のタスクが処理された後、タスクの結果から全体的なジョブの出力が生成されます。タスクの特性は、ジョブの種類に依存します。ジョブ スプリッターは、アプリケーションが対象としている処理に関する情報に基づいて、ジョブをどのようにタスクに分類するかを定義します。タスクの出力も個別にダウンロードできます。たとえば、この機能は、ユーザーがアニメーション ジョブから個々のタスクをダウンロードできるようにする場合に便利です。

####マージ タスク
**マージ タスク**は、個々のタスクの結果から最終的なジョブの結果を生成する特殊なタスクです。たとえば、ムービー レンダリング ジョブの場合、マージ タスクは、レンダリングされたフレームからムービーを生成したり、すべてのレンダリングされたフレームを 1 つの zip ファイルに圧縮したりします。実際の "マージ" 操作が必要でない場合でも、すべてのジョブはマージ タスクを持ちます。

####ファイル
**ファイル**は、ジョブへの入力として使用されるデータです。ジョブには、入力ファイルを関連付けないことも、1 つまたは複数の入力ファイルを関連付けることもできます。また、同じファイルを複数のジョブで使用することもできます。たとえば、ムービー レンダリング ジョブに対しては、テクスチャやモデルを含むファイルを使用します。データ分析ジョブに対しては、一式の観測値または測定値を含むファイルを使用します。

###クラウド アプリケーションを有効にする
アプリケーションには、アプリケーションのすべての詳細を含む静的フィールドまたはプロパティを含める必要があります。ここでは、アプリケーションの名前とアプリケーションによって処理されるジョブの種類を指定します。これは、Visual Studio ギャラリーからダウンロードできる SDK に含まれるテンプレートを使用するときに提供されます。

並列ワークロードのためのクラウド アプリケーション宣言の例を次に示します。

	public static class ApplicationDefinition
	{
	    public static readonly CloudApplication App = new ParallelCloudApplication
	    {
	        ApplicationName = "ApplicationName",
	        JobType = "ApplicationJobType",
	        JobSplitterType = typeof(MyJobSplitter),
	        TaskProcessorType = typeof(MyTaskProcessor),
	    };
	}

####ジョブ スプリッターとタスク プロセッサの実装
並列性がきわめて高いワークロードに対しては、ジョブ スプリッターとタスク プロセッサを実装する必要があります。

####JobSplitter.SplitIntoTasks の実装
SplitIntoTasks 実装では、タスクのシーケンスを返す必要があります。各タスクは、計算ノードによる処理のキューに配置されるそれぞれの処理を表します。各タスクは自己完結型である必要があり、タスク プロセッサが必要とするすべての情報を使ってセットアップする必要があります。

ジョブ スプリッターで指定されたタスクは TaskSpecifier オブジェクトとして表されます。TaskSpecifier にはいくつかのプロパティがあります。これらのプロパティは、タスクを返す前に設定できます。


-	TaskIndex は無視されますが、タスク プロセッサはこれを使用できます。TaskIndex を使用して、タスク プロセッサにインデックスを渡すことができます。インデックスを渡す必要がない場合は、このプロパティを設定する必要はありません。
-	Parameters は、既定では空のコレクションです。これに項目を追加したり、新しいコレクションで置き換えたりすることができます。WithJobParameters または WithAllJobParameters メソッドを使用してジョブ パラメーター コレクションからエントリをコピーすることができます。  


RequiredFiles は、既定では空のコレクションです。これに項目を追加したり、新しいコレクションで置き換えたりすることができます。RequiringJobFiles メソッドまたは RequiringAllJobFiles メソッドを使用して、ジョブ ファイル コレクションからエントリをコピーすることができます。

他の特定のタスクに依存するタスクを指定できます。そのためには、TaskSpecifier.DependsOn プロパティを設定し、依存するタスクの ID を渡します。

	new TaskSpecifier {
	    DependsOn = TaskDependency.OnId(5)
	}

タスクは、依存先のタスクの出力が使用可能になるまで実行されません。

また、タスクのグループ全体の処理を別のグループが完全に完了するまで開始しないように指定することもできます。その場合は、TaskSpecifier.Stage プロパティを設定できます。特定の Stage 値が指定されたタスクは、それよりも Stage 値が低いすべてのタスクが完了するまで処理を開始しません。たとえば、Stage 3 のタスクは、Stage 0、1、または 2 のタスクがすべて終了するまで処理を開始しません。Stage 値は 0 から始まる必要があり、欠番があってはいけません。SplitIntoTasks は、Stage 値の順序でタスクを返す必要があります。たとえば、Stage 1 のタスクの後に Stage 0 のタスクを返すとエラーになります。

すべての並列ジョブは、マージ タスクと呼ばれる特殊なタスクで終了します。マージ タスクの役割は、並列処理タスクの結果から最終結果を生成することです。マージ タスクは、Batch Apps によって自動的に作成されます。

まれに、パラメーターをカスタマイズする場合など、マージ タスクを明示的に制御する必要がある場合があります。その場合、IsMerge プロパティを true に設定した TaskSpecifier を返すことによって、マージ タスクを指定できます。これにより、自動マージ タスクが上書きされます。明示的なマージ タスクを作成する場合は、次の条件が適用されます。

-	明示的なマージ タスクは 1 つだけ作成できます。
-	明示的なマージ タスクは、シーケンスの最後のタスクである必要があります。
-	明示的なマージ タスクでは IsMerge プロパティが true に設定されている必要があります。他のすべてのタスクでは、IsMerge が false に設定されている必要があります。  


ただし、通常はマージ タスクを明示的に作成する必要はありません。

次のコードに、SplitIntoTasks の単純な実装を示します。

	protected override IEnumerable<TaskSpecifier> SplitIntoTasks(
	    IJob job,
	    JobSplitSettings settings)
	{
	    int start = Int32.Parse(job.Parameters["startIndex"]);
	    int end = Int32.Parse(job.Parameters["endIndex"]);
	    int count = (end - start) + 1;

	    // Processing tasks
	    for (int i = 0; i < count; ++i)
	    {
	        yield return new TaskSpecifier
	        {
	            TaskIndex = start + i,
	        }.RequiringAllJobFiles();
	    }
	}
####ParallelTaskProcessor.RunExternalTaskProcess の実装

ジョブ スプリッターから返される非マージ タスクのそれぞれに対して、RunExternalTaskProcess が呼び出されます。これにより、適切な引数を使用してアプリケーションが呼び出され、出力のコレクションが返されます。このコレクションは、後で使用するために保持しておく必要があります。

次に、application.exe という名前のプログラムを呼び出す方法を示します。絶対ファイル パスの作成には、ExecutablePath のヘルパー メソッドを使用できます。

Cloud SDK の ExternalProcess クラスは、アプリケーションの実行可能ファイルを実行するのに便利なヘルパー ロジックを提供します。ExternalProcess は、キャンセル操作、終了コードの例外への変換、標準出力と標準エラーのキャプチャ、および環境変数の設定を処理します。さらに、必要に応じて、直接 .NET Process クラスを使用してプログラムを実行することもできます。

RunExternalTaskProcess メソッドは、出力ファイルの一覧が含まれた TaskProcessResult を返します。これには、最低限、マージに必要なすべてのファイルが含まれている必要があります。また、このメソッドは、必要に応じて、ログ ファイル、プレビュー ファイル、および中間ファイル (たとえば、タスクが失敗した場合の診断用) を返すことができます。このメソッドはファイルの内容ではなくファイル パスを返すことに注意してください。

それぞれのファイルは、格納している出力の種類で識別する必要があります。出力の種類には、出力 (最終的なジョブ出力の一部)、プレビュー ファイル、ログ ファイル、または中間ファイルがあります。これらの値は、TaskOutputFileKind 列挙型から取得します。次のコードは、1 つのタスク出力を返し、プレビューやログを返しません。TaskProcessResult.FromExternalProcessResult メソッドを使用すると、終了コード、プロセッサ出力、およびコマンド ライン プログラムからの出力ファイルをキャプチャする一般的なシナリオを簡略化できます。

次のコードに、ParallelTaskProcessor.RunExternalTaskProcess の単純な実装を示します。

	protected override TaskProcessResult RunExternalTaskProcess(
	    ITask task,
	    TaskExecutionSettings settings)
	{
	    var inputFile = LocalPath(task.RequiredFiles[0].Name);
	    var outputFile = LocalPath(task.TaskId.ToString() + ".jpg");

	    var exePath = ExecutablePath(@"application\application.exe");
	    var arguments = String.Format("-in:{0} -out:{1}", inputFile, outputFile);

	    var result = new ExternalProcess {
	        CommandPath = exePath,
	        Arguments = arguments,
	        WorkingDirectory = LocalStoragePath,
	        CancellationToken = settings.CancellationToken
	    }.Run();

	    return TaskProcessResult.FromExternalProcessResult(result, outputFile);
	}
####ParallelTaskProcessor.RunExternalMergeProcess の実装

これは、マージ タスク用に呼び出されます。このメソッドは、アプリケーションを呼び出して、前のタスクの出力をアプリケーションに適した方法で結合し、結合した出力を返します。

RunExternalMergeProcess の実装は RunExternalTaskProcess によく似ていますが、次の点が異なります。

-	RunExternalMergeProcess は、ユーザーの入力ファイルではなく前のタスクの出力を消費します。そのため、処理するファイルの名前を Task.RequiredFiles コレクションから取得する代わりに、タスク ID に基づいて指定する必要があります。
-	RunExternalMergeProcess は、JobOutput ファイル (および必要に応じて JobPreview ファイル) を返します。


次のコードに、ParallelTaskProcessor.RunExternalMergeProcess の単純な実装を示します。

	protected override JobResult RunExternalMergeProcess(
	    ITask mergeTask,
	    TaskExecutionSettings settings)
	{
	    var outputFile =  "output.zip";

	    var exePath =  ExecutablePath(@"application\application.exe");
	    var arguments = String.Format("a -application {0} *.jpg", outputFile);

	    new ExternalProcess {
	        CommandPath = exePath,
	        Arguments = arguments,
	        WorkingDirectory = LocalStoragePath
	    }.Run();

	    return new JobResult {
	        OutputFile = outputFile
	    };
	}

###Batch Apps へのジョブの送信
ジョブは、実行する必要があるワークロードを記述します。ジョブには、ファイルの内容を除いたワークロードに関するすべての情報を含める必要があります。たとえば、クライアントからジョブ スプリッターを介してタスクに至るまでの処理の構成設定をジョブに含めます。MSDN で提供されているサンプルには、ジョブを送信する方法や、Web ポータルとコマンド ライン クライアントを含む複数のクライアントを提供する方法が示されています。


<!--Anchors-->


<!--Image references-->
[1]: ./media/batch-dotnet-get-started/batch-dotnet-get-started-01.jpg
[2]: ./media/batch-dotnet-get-started/batch-dotnet-get-started-02.jpg
[3]: ./media/batch-dotnet-get-started/batch-dotnet-get-started-03.jpg
[4]: ./media/batch-dotnet-get-started/batch-dotnet-get-started-04.jpg

<!---HONumber=July15_HO3-->