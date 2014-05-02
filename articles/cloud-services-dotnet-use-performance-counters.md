<properties linkid="dev-net-commons-tasks-profiling" urlDisplayName="パフォーマンスのプロファイル" pageTitle="Azure でのパフォーマンス カウンターの使用 (.NET)" metaKeywords="Azure パフォーマンス カウンター, Azure パフォーマンス プロファイル, Azure パフォーマンス カウンター C#, Azure パフォーマンス プロファイル C#" description="Azure アプリケーションで、パフォーマンス カウンターを有効にしてデータを収集する方法を説明します。" metaCanonical="" services="cloud-services" documentationCenter=".NET" title="Azure でのパフォーマンス カウンターの使用" authors="ryanwi" solutions="" manager="" editor="" />





# Azure でのパフォーマンス カウンターの使用

Azure アプリケーションでパフォーマンス カウンターを使用してデータを収集すると、システムのボトルネックを特定したり、システムやアプリケーションのパフォーマンスを調整したりするのに役立ちます。Windows Server 2008、Windows Server 2012、IIS、ASP.NET で使用可能なパフォーマンス カウンターは、Azure アプリケーションの状態を把握するために収集して使用できます。

このトピックでは、diagnostics.wadcfg 構成ファイルを使用してアプリケーションでパフォーマンス カウンターを有効にする方法について説明します。[Azure 管理ポータル][]でアプリケーションのパフォーマンスを監視する方法については、「[クラウド サービスの監視方法][]」を参照してください。ログとトレース戦略を作成し、診断機能やその他の手法で問題のトラブルシューティングを行って Azure アプリケーションを最適化する方法の詳細なガイダンスについては、「[Troubleshooting Best Practices for Developing Azure Applications (Azure アプリケーション開発時のトラブルシューティングのベスト プラクティス)][]」を参照してください。

このタスクの手順は次のとおりです。

-   [前提条件][]
-   [手順 1. パフォーマンス カウンターからデータを収集して保存する][]
-   [手順 2. (省略可能) カスタム パフォーマンス カウンターを作成する][]
-   [手順 3. パフォーマンス カウンターのデータのクエリを実行する][]
-   [次のステップ][]
-   [その他のリソース][]

## <a name="prereqs"> </a>前提条件

この記事では、アプリケーションに診断モニターをインポートし、Visual Studio ソリューションに diagnostics.wadcfg 構成ファイルを追加していることを前提としています。詳細については、「[Enabling Diagnostics in Azure (Azure の診断機能)][]」を参照してください。

## <a name="step1"> </a>手順 1. パフォーマンス カウンターからデータを収集して保存する

Visual Studio ソリューションに diagnostics.wadcfg ファイルを追加すると、Azure アプリケーションでパフォーマンス カウンター データの収集とストレージを構成できます。これは、diagnostics.wadcfg ファイルにパフォーマンス カウンターを追加することで行うことができます。パフォーマンス カウンターを含む診断データは、まずインスタンスで収集されます。その後、データが Azure テーブル サービスの **WADPerformanceCountersTable** テーブルに保存されるため、アプリケーションでストレージ アカウントを指定する必要もあります。コンピューティング エミュレーターでアプリケーションをローカルにテストする場合、ストレージ エミュレーターで診断データをローカルに保存することもできます。診断データを保存する前に、まず [Azure 管理ポータル][] に移動し、ストレージ アカウントを作成する必要があります。ベスト プラクティスとして、Azure アプリケーションと同じ地理的な場所にあるストレージ アカウントを特定することにより、外部帯域幅のコストが生じないようにしたり、遅延時間を短縮することができます。

### diagnostics.wadcfg ファイルにパフォーマンス カウンターを追加する

収集できるパフォーマンス カウンターは多くあります。次の例に、Web ロールおよびワーカー ロールの監視に推奨されるいくつかのパフォーマンス カウンターを示します。

diagnostics.wadcfg ファイルを開き、次の内容を **DiagnosticMonitorConfiguration** 要素に追加します。

		<PerformanceCounters bufferQuotaInMB="0" scheduledTransferPeriod="PT30M">
		<PerformanceCounterConfiguration counterSpecifier="\Memory\Available Bytes" sampleRate="PT30S" />
		<PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT30S" />
		<!-- Use the Process(w3wp) category counters in a web role -->
		<PerformanceCounterConfiguration counterSpecifier="\Process(w3wp)\% Processor Time" sampleRate="PT30S" />
		<PerformanceCounterConfiguration counterSpecifier="\Process(w3wp)\Private Bytes" sampleRate="PT30S" />
		<PerformanceCounterConfiguration counterSpecifier="\Process(w3wp)\Thread Count" sampleRate="PT30S" />
		<!-- Use the Process(WaWorkerHost) category counters in a worker role.
		<PerformanceCounterConfiguration counterSpecifier="\Process(WaWorkerHost)\% Processor Time" sampleRate="PT30S" />
		<PerformanceCounterConfiguration counterSpecifier="\Process(WaWorkerHost)\Private Bytes" sampleRate="PT30S" />
		<PerformanceCounterConfiguration counterSpecifier="\Process(WaWorkerHost)\Thread Count" sampleRate="PT30S" /> 
 		-->
		<PerformanceCounterConfiguration counterSpecifier="\.NET CLR Interop(_Global_)\# of marshalling" sampleRate="PT30S" />
		<PerformanceCounterConfiguration counterSpecifier="\.NET CLR Loading(_Global_)\% Time Loading" sampleRate="PT30S" />
		<PerformanceCounterConfiguration counterSpecifier="\.NET CLR LocksAndThreads(_Global_)\Contention Rate / sec" sampleRate="PT30S" />
		<PerformanceCounterConfiguration counterSpecifier="\.NET CLR Memory(_Global_)\# Bytes in all Heaps" sampleRate="PT30S" />
		<PerformanceCounterConfiguration counterSpecifier="\.NET CLR Networking(_Global_)\Connections Established" sampleRate="PT30S" />
		<PerformanceCounterConfiguration counterSpecifier="\.NET CLR Remoting(_Global_)\Remote Calls/sec" sampleRate="PT30S" />
		<PerformanceCounterConfiguration counterSpecifier="\.NET CLR Jit(_Global_)\% Time in Jit" sampleRate="PT30S" />
		</PerformanceCounters>    

**bufferQuotaInMB** 属性: データ コレクション タイプに使用可能なファイル システム ストレージの最大量を指定します (Azure ログ、IIS ログなど)。既定値は 0 です。クォータに達すると、新しいデータが追加されたときに最も古いデータが削除されます。0. すべての **bufferQuotaInMB** プロパティの合計が、**OverallQuotaInMB** 属性の値より大きくなる必要があります。診断データの収集に必要なストレージの量を判断する方法の詳細については、「[Troubleshooting Best Practices for Developing Azure Applications (Azure アプリケーションの開発に関するトラブルシューティングのベスト プラクティス)][]」の WAD の設定に関するセクションを参照してください。

**scheduledTransferPeriod** 属性: 分単位で四捨五入したデータ転送のスケジュール間隔を指定します。次の例では、PT30M (30 分) に設定されます。転送期間を小さい値 (1 分など) に設定すると、運用環境のアプリケーションのパフォーマンスにマイナスの影響が及びますが、テスト時に診断が機能するかをすぐに確認する場合に役立つことがあります。スケジュールされた転送期間は、インスタンスで診断データが上書きされないような小さい値にし、かつアプリケーションのパフォーマンスに影響を与えないような大きい値にしてください。

**counterSpecifier** 属性: 収集するパフォーマンス カウンターを指定します。

**sampleRate** 属性は、パフォーマンス カウンターをサンプリングする間隔 (この場合は 30 秒) を指定します。

収集するパフォーマンス カウンターを追加したら、変更内容を diagnostics.wadcfg ファイルに保存します。次に、診断データを保存するストレージ アカウントを指定する必要があります。

### ストレージ アカウントを指定する

診断情報を Azure ストレージ アカウントに保存するには、サービス構成 (ServiceConfiguration.cscfg) ファイルで接続文字列を指定する必要があります。Azure Tools for Visual Studio バージョン 1.4 (2011 年 8 月) 以降では、ローカルとクラウドに異なる構成ファイル (ServiceConfiguration.cscfg) を使用することができます。ローカル テストにはストレージ エミュレーターを無料で使用しながら、運用環境向けに別個の構成ファイルを維持することができるため、複数のサービス構成があると診断に役立ちます。

接続文字列を設定するには、次の手順に従います。

1.  任意のテキスト エディターを使用して ServiceConfiguration.Cloud.cscfg ファイルを開き、ストレージ アカウントの接続文字列を設定します。

        <ConfigurationSettings>
          <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=<name>;AccountKey=<key>"/>
        </ConfigurationSettings>

    ストレージ アカウント ダッシュボードの管理ポータルの **[キーの管理]** の下に、**AccountName** 値と **AccountKey** 値があります。

2.  ServiceConfiguration.Cloud.cscfg ファイルを保存します。
3.  ServiceConfiguration.Local.cscfg ファイルを開き、**UseDevelopmentStorage** が true に設定されている (既定値) ことを確認します。

		<ConfigurationSettings>
      	   <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
		</ConfigurationSettings>

	これで、接続文字列が設定され、アプリケーションのデプロイ時にアプリケーションで診断データがストレージ アカウントに保存されるようになります。
4. プロジェクトを保存してビルドし、アプリケーションを展開します。

## <a name="step2"> </a>手順 2. (省略可能) カスタム パフォーマンス カウンターを作成する

事前定義されたパフォーマンス カウンターに加えて、独自のカスタム パフォーマンス カウンターを追加して Web ロールまたはワーカー ロールを監視できます。カスタム パフォーマンス カウンターを使用すると、アプリケーション固有の動作を追跡して監視することができます。また、カスタム パフォーマンス カウンターは、昇格されたアクセス許可を持つスタートアップ タスク、Web ロール、ワーカー ロールで作成または削除できます。

次の手順を実行して、"\MyCustomCounterCategory\MyButton1Counter" という簡単なカスタム パフォーマンス カウンターを作成します。

1.  アプリケーションのサービス定義ファイル (CSDEF) を開きます。
2.  **Runtime** 要素を **WebRole** または **WorkerRole** 要素に追加して、
    昇格された権限で実行できるようにします。

        <Runtime executionContext="elevated" />

3.  ファイルを保存します。
4.  diagnostics.wadcfg ファイルを開き、次の内容を **DiagnosticMonitorConfiguration** 要素に追加します。

		<PerformanceCounters bufferQuotaInMB="0" scheduledTransferPeriod="PT30M">
		<PerformanceCounterConfiguration counterSpecifier="\MyCustomCounterCategory\MyButton1Counter" sampleRate="PT30S"/>
		</PerformanceCounters>		

5. 	ファイルを保存します。
6.  **base.OnStart** を呼び出す前に、ロールの **OnStart** メソッドで、
    カスタム パフォーマンス カウンター カテゴリを作成します。次の C# の例では、まだ存在しない場合にカスタム カテゴリを作成します。

		public override bool OnStart()
        {
        if (!PerformanceCounterCategory.Exists("MyCustomCounterCategory"))
        {
           CounterCreationDataCollection counterCollection = new CounterCreationDataCollection();

           // add a counter tracking user button1 clicks
           CounterCreationData operationTotal1 = new CounterCreationData();
           operationTotal1.CounterName = "MyButton1Counter";
           operationTotal1.CounterHelp = "My Custom Counter for Button1";
           operationTotal1.CounterType = PerformanceCounterType.NumberOfItems32;
           counterCollection.Add(operationTotal1);

           PerformanceCounterCategory.Create(
             "MyCustomCounterCategory",
             "My Custom Counter Category",
             PerformanceCounterCategoryType.SingleInstance, counterCollection);

           Trace.WriteLine("Custom counter category created.");
        }
        else{
           Trace.WriteLine("Custom counter category already exists.");
        }

		return base.OnStart();
        }

7.  アプリケーション内でカウンターを更新します。次の例では、
    **Button1_Click** イベントでカスタム パフォーマンス カウンターを更新しています。

        protected void Button1_Click(object sender, EventArgs e)
        {
		   PerformanceCounter button1Counter = new PerformanceCounter(
		      "MyCustomCounterCategory",
		      "MyButton1Counter",
		      string.Empty,
		      false);

		   button1Counter.Increment();
		   this.Button1.Text = "Button 1 count: " +
		      button1Counter.RawValue.ToString();
        }

8.  ファイルを保存します。

これらの操作を完了すると、Azure 診断モニターによってカスタム パフォーマンス カウンターの
データが収集されます。

## <a name="step3"> </a>手順 3. パフォーマンス カウンターのデータのクエリを実行する

アプリケーションが展開されたら、診断モニターを実行すると、パフォーマンス カウンターの収集が開始され、そのデータが Azure ストレージに保存されます。**Visual Studio のサーバー エクスプローラー**、[Azure Storage Explorer (Azure ストレージ エクスプローラー)][]、[Azure 管理ポータル][](Cerebrata)などのツールを使用して、**WADPerformanceCountersTable** テーブルにあるパフォーマンス カウンター データを確認します。[C#][]、[Java][]、[Node.js][]、[Python][]、[PHP][] を使用して、テーブル サービスをプログラムにより照会することもできます。

次の C# の例は、**WADPerformanceCountersTable** テーブルに対する簡単なクエリを示しており、CSV ファイルに診断データを保存します。パフォーマンス カウンターが CSV ファイルに保存されたら、Microsoft Excel や他のツールのグラフ作成機能を使用してデータを視覚化できます。必ず、Microsoft.WindowsAzure.Storage.dll への参照を追加してください。これは、Azure SDK for .NET (2012 年 10 月) 以降に含まれています。このアセンブリは、%Program Files%\Microsoft SDKs\Windows Azure\.NET SDK\version-num\ref\ ディレクトリにインストールされます。

		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.Storage.Auth;
		using Microsoft.WindowsAzure.Storage.Table;

		...

		// Get the connection string. When using Azure Cloud Services, it is recommended 
		// you store your connection string using the Azure service configuration
		// system (*.csdef and *.cscfg files). You can you use the CloudConfigurationManager type 
		// to retrieve your storage connection string.  If you're not using Cloud Services, it's
		// recommended that you store the connection string in your web.config or app.config file.
		// Use the ConfigurationManager type to retrieve your storage connection string.
		
		string connectionString = Microsoft.WindowsAzure.CloudConfigurationManager.GetSetting("StorageConnectionString");
		//string connectionString = System.Configuration.ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString;
            
		// Get a reference to the storage account using the connection string.  You can also use the development storage account (Storage Emulator)
		// for local debugging.              
		CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);
		//CloudStorageAccount storageAccount = CloudStorageAccount.DevelopmentStorageAccount;

		// Create the table client.
		CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

		// Create the CloudTable object that represents the "WADPerformanceCountersTable" table.
		CloudTable table = tableClient.GetTableReference("WADPerformanceCountersTable");

		// Create the table query, filter on a specific CounterName, DeploymentId and RoleInstance.
		TableQuery<PerformanceCountersEntity> query = new TableQuery<PerformanceCountersEntity>()
			.Where(
				TableQuery.CombineFilters(
					TableQuery.GenerateFilterCondition("CounterName", QueryComparisons.Equal, @"\Processor(_Total)\% Processor Time"),
					TableOperators.And,
					TableQuery.CombineFilters(                        
						TableQuery.GenerateFilterCondition("DeploymentId", QueryComparisons.Equal, "ec26b7a1720447e1bcdeefc41c4892a3"),
						TableOperators.And,
						TableQuery.GenerateFilterCondition("RoleInstance", QueryComparisons.Equal, "WebRole1_IN_0")                        
                    )
				)
			);

		// Execute the table query.
		IEnumerable<PerformanceCountersEntity> result = table.ExecuteQuery(query);

		// Process the query results and build a CSV file.
		StringBuilder sb = new StringBuilder("TimeStamp,EventTickCount,DeploymentId,Role,RoleInstance,CounterName,CounterValue\n");
            
		foreach (PerformanceCountersEntity entity in result)
		{
			sb.Append(entity.Timestamp + "," + entity.EventTickCount + "," + entity.DeploymentId + "," 
				+ entity.Role + "," + entity.RoleInstance + "," + entity.CounterName + "," + entity.CounterValue+"\n");
		}

		StreamWriter sw = File.CreateText(@"C:\temp\PerfCounters.csv");
		sw.Write(sb.ToString());
		sw.Close();

エンティティは、**TableEntity** から派生するカスタム クラスを使用して C# オブジェクトにマップされます。次のコードは、**WADPerformanceCountersTable** テーブルのパフォーマンス カウンターを表すエンティティ クラスを定義します。

		public class PerformanceCountersEntity : TableEntity
		{
			public long EventTickCount { get; set; }
			public string DeploymentId { get; set; }
			public string Role { get; set; }
			public string RoleInstance { get; set; }
			public string CounterName { get; set; }
			public double CounterValue { get; set; }                
		}

## <a name="nextsteps"> </a>次のステップ

これで、パフォーマンス カウンターの収集の基本を学習できました。より複雑なトラブルシューティング シナリオを実装する方法については、次のリンク先を参照してください。

- [Troubleshooting Best Practices for Developing Azure Applications (Azure アプリケーション開発時のトラブルシューティングのベスト プラクティス)][]
- [クラウド サービスの監視方法][]
- [オートスケーリング アプリケーション ブロックの使用方法][]
- [伸縮性と弾力性があるクラウド アプリケーションの作成]

## <a name="additional"> </a>その他のリソース

- [Enabling Diagnostics in Azure (Azure の診断機能)][]
- [Azure 診断を使用したログ データの収集][]
- [クラウド サービスのデバッグ][]


  [Azure アプリケーションでのパフォーマンス カウンターの作成と使用の概要]: http://msdn.microsoft.com/ja-jp/library/windowsazure/hh411520.aspx
  [前提条件]: #prereqs
  [手順 1. パフォーマンス カウンターからデータを収集して保存する]: #step1
  [手順 2. (省略可能) カスタム パフォーマンス カウンターを作成する]: #step2
  [手順 3. パフォーマンス カウンターのデータのクエリを実行する]: #step3
  [次のステップ]: #nextsteps
  [その他のリソース]: #additional
  
  [Azure 診断を使用したログ データの収集]: http://msdn.microsoft.com/ja-jp/library/windowsazure/gg433048.aspx
  [クラウド サービスのデバッグ]: http://msdn.microsoft.com/ja-jp/library/windowsazure/ee405479.aspx
  [オートスケーリング アプリケーション ブロックの使用方法]: http://www.windowsazure.com/ja-jp/develop/net/how-to-guides/autoscaling/
  [Troubleshooting Best Practices for Developing Azure Applications (Azure アプリケーション開発時のトラブルシューティングのベスト プラクティス)]: http://msdn.microsoft.com/ja-jp/library/windowsazure/hh771389.aspx
  [Enabling Diagnostics in Azure (Azure の診断機能)]: https://www.windowsazure.com/ja-jp/develop/net/common-tasks/diagnostics/
  [テーブル ストレージ サービスを使用する方法]: http://www.windowsazure.com/ja-jp/develop/net/how-to-guides/table-services/
  [Azure Storage Explorer (Azure ストレージ エクスプローラー)]: http://azurestorageexplorer.codeplex.com/
  
  [Java]: http://www.windowsazure.com/ja-jp/develop/java/how-to-guides/table-service/
  [Python]: http://www.windowsazure.com/ja-jp/develop/python/how-to-guides/table-service/
  [PHP]: http://www.windowsazure.com/ja-jp/develop/php/how-to-guides/table-service/
  
  [伸縮性と弾力性があるクラウド アプリケーションの作成]: http://msdn.microsoft.com/ja-jp/library/hh680949(PandP.50).aspx
  [Azure 管理ポータル]: http://manage.windowsazure.com
  [Azure Diagnostics Manager]: http://www.cerebrata.com/Products/AzureDiagnosticsManager/Default.aspx
  [クラウド サービスの監視方法]: https://www.windowsazure.com/ja-jp/manage/services/cloud-services/how-to-monitor-a-cloud-service/

