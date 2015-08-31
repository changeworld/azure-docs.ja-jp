<properties 
	pageTitle="HDInsight の Hadoop クラスターに Giraph をインストールして使用する | Microsoft Azure" 
	description="Giraph を使用して HDInsight クラスターをカスタマイズする方法について説明します。スクリプトを使用して Giraph をインストールするために、Script Action の構成オプションを使用します。" 
	services="hdinsight" 
	documentationCenter="" 
	authors="nitinme" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/07/2015" 
	ms.author="nitinme"/>

# HDInsight Hadoop クラスターに Giraph をインストールし、Giraph を使用して大規模なグラフを処理する

**Script Action** クラスター カスタマイズを使用して、Azure HDInsight の Hadoop の任意の種類のクラスターに Giraph をインストールできます。Script Action では、クラスターの作成時のみ、スクリプトを実行してクラスターをカスタマイズできます。詳細については、「[Script Action を使って HDInsight クラスターをカスタマイズする][hdinsight-cluster-customize]」をご覧ください。

このトピックでは、Script Action を使用して Giraph をインストールする方法を学習します。Giraph をインストールした後は、大規模なグラフを処理する多くの一般的なアプリケーションで Giraph を使用する方法についても説明します。

[AZURE.INCLUDE [hdinsight-azure-portal](../../includes/hdinsight-azure-portal.md)]

* [HDInsight クラスターでの Giraph のインストール](hdinsight-hadoop-giraph-install.md)

## <a name="whatis"></a>Giraph とは

<a href="http://giraph.apache.org/" target="_blank">Apache Giraph</a> は、Hadoop でグラフの処理を実行するために使用でき、Azure HDInsight で使用できます。グラフでは、オブジェクト間の関係 (インターネットのような大規模ネットワークでのルーター間の接続など) や、ソーシャル ネットワークでの人々の関係 (ソーシャル グラフとも呼ばれる) をモデル化します。グラフの処理により、次のようなグラフ内のオブジェクト間の関係について推論できます。

- 現在の関係に基づいて潜在的な友人を識別する
- ネットワーク内の 2 台のコンピューター間の最短ルートを識別する
- Web ページのページ順位を計算する

   
## <a name="install"></a>Giraph のインストール方法

HDInsight クラスターに Giraph をインストールするサンプル スクリプトは、[https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1) の読み取り専用の Azure ストレージ BLOB から入手できます。このセクションでは、Azure ポータルを使用してクラスターをプロビジョニングする際に、サンプル スクリプトを使用する方法について説明します。

> [AZURE.NOTE]サンプル スクリプトは、HDInsight クラスター version 3.1 でのみ機能します。HDInsight クラスター バージョンの詳細については、「[HDInsight クラスター バージョン](hdinsight-component-versioning.md)」をご覧ください。

1. 「[カスタム オプションを使用したクラスターのプロビジョニング](hdinsight-provision-clusters.md#portal)」に関するページの説明に従い、**[カスタム作成]** オプションを使用してプロビジョニングを開始します。 
2. ウィザードの **[スクリプトのアクション]** ページで、**[スクリプト アクションの追加]** をクリックし、次に示すように、スクリプト アクションの詳細を指定します。

	![Script Action を使ってクラスターをカスタマイズする](./media/hdinsight-hadoop-giraph-install-v1/hdi-script-action-giraph.png "Script Action を使ってクラスターをカスタマイズする")
	
	<table border='1'>
	<tr><th>プロパティ</th><th>値</th></tr>
	<tr><td>名前</td>
		<td>スクリプト アクションの名前を指定します。たとえば、「<b>Install Giraph</b>」と入力します。</td></tr>
	<tr><td>スクリプト URI</td>
		<td>クラスターをカスタマイズするために呼び出すスクリプトの Uniform Resource Identifier (URI) を指定します。たとえば、<i>https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1</i> のように指定します。</td></tr>
	<tr><td>ノードの種類</td>
		<td>カスタマイズ スクリプトが実行されるノードを指定します。<b>[すべてのノード]</b>、<b>[ヘッド ノードのみ]</b>、<b>[ワーカー ノードのみ]</b> から選択できます。
	<tr><td>パラメーター</td>
		<td>スクリプトで必要な場合は、パラメーターを指定します。Giraph をインストールするスクリプトではパラメーターが必要ないため、空白のままにすることができます。</td></tr>
</table>複数のスクリプト操作を追加して、クラスターに複数のコンポーネントをインストールすることができます。スクリプトの追加後、チェックマークをクリックしてクラスターのプロビジョニングを開始します。

Azure PowerShell または HDInsight .NET SDK を使用して、HDInsight に Giraph をインストールするためにスクリプトを使用することもできます。これらの手順については、このトピックの後半で説明します。

## <a name="usegiraph"></a>HDInsight で Giraph を使用する方法

SimpleShortestPathsComputation サンプルを使用して、グラフのオブジェクト間の最短パスを見つけるための基本的な <a href = "http://people.apache.org/~edwardyoon/documents/pregel.pdf">Pregel</a> の実装を示します。次の手順を使用して、サンプル データとサンプル jar ファイルをアップロードし、SimpleShortestPathsComputation サンプルでジョブを実行して、結果を表示します。

1. サンプル データ ファイルを Azure Blob ストレージにアップロードします。ローカル ワークステーションに、**tiny\_graph.txt** という名前の新しいファイルを作成します。このファイルには、次の行を含めます。

		[0,0,[[1,1],[3,3]]]
		[1,0,[[0,1],[2,2],[3,1]]]
		[2,0,[[1,2],[4,4]]]
		[3,0,[[0,3],[1,1],[4,4]]]
		[4,0,[[3,4],[2,4]]]

	tiny\_graph.txt ファイルを HDInsight クラスターのプライマリ ストレージにアップロードします。データをアップロードする方法の詳細については、「[HDInsight での Hadoop ジョブ用データのアップロード](hdinsight-upload-data.md)」をご覧ください。

	このデータは、[source\_id, source\_value,[[dest\_id], [edge\_value],...]] の形式を使用して有向グラフ内のオブジェクト間の関係を示しています。各行は、**source\_id** オブジェクトと 1 つ以上の **dest\_id** オブジェクトとの関係を表現しています。**edge\_value** (重み) は、**source\_id** と **dest\_id** の間のつながりの強さまたは距離であると考えられます。

	この値 (重み) を使用し、オブジェクト間の距離に応じて線を引くと、先のデータは次の図のようになります。

	![tiny\_graph.txt drawn as circles with lines of varying distance between](./media/hdinsight-hadoop-giraph-install-v1/giraph-graph.png)

	

4. SimpleShortestPathsComputation サンプルを実行します。tiny\_graph.txt ファイルを入力として使用し、次の Azure PowerShell コマンドレットを使用してサンプルを実行します。これには、[Azure PowerShell][powershell-install] がインストール済みおよび構成済みであることが必要です。

		$clusterName = "clustername"
		# Giraph examples jar
		$jarFile = "wasb:///example/jars/giraph-examples.jar"
		# Arguments for this job
		$jobArguments = "org.apache.giraph.examples.SimpleShortestPathsComputation",
		                "-ca", "mapred.job.tracker=headnodehost:9010",
		                "-vif", "org.apache.giraph.io.formats.JsonLongDoubleFloatDoubleVertexInputFormat",
		                "-vip", "wasb:///example/data/tiny_graph.txt",
		                "-vof", "org.apache.giraph.io.formats.IdWithValueTextOutputFormat",
		                "-op",  "wasb:///example/output/shortestpaths",
		                "-w", "2"
		# Create the definition
		$jobDefinition = New-AzureHDInsightMapReduceJobDefinition
		  -JarFile $jarFile
		  -ClassName "org.apache.giraph.GiraphRunner"
		  -Arguments $jobArguments
		
		# Run the job, write output to the Azure PowerShell window
		$job = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $jobDefinition
		Write-Host "Wait for the job to complete ..." -ForegroundColor Green
		Wait-AzureHDInsightJob -Job $job
		Write-Host "STDERR"
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardError
		Write-Host "Display the standard output ..." -ForegroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardOutput

	この例では、**clustername** を、Giraph をインストールした HDInsight クラスターの名前で置き換えます。

5. 結果を表示します。ジョブが完了すると、結果が \_\___wasb:///example/out/shotestpaths__ フォルダーの 2 つの出力ファイルに格納されます。この 2 つのファイルは __part-m-00001__ と __part-m-00002__ という名前です。出力をダウンロードして表示するには、次の手順を実行します。

		$subscriptionName = "<SubscriptionName>"       # Azure subscription name
		$storageAccountName = "<StorageAccountName>"   # Azure Storage account name
		$containerName = "<ContainerName>"             # Blob storage container name

		# Select the current subscription
		Select-AzureSubscription $subscriptionName
		
		# Create the Storage account context object
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
		$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey

		# Download the job output to the workstation
		Get-AzureStorageBlobContent -Container $containerName -Blob example/output/shortestpaths/part-m-00001 -Context $storageContext -Force
		Get-AzureStorageBlobContent -Container $containerName -Blob example/output/shortestpaths/part-m-00002 -Context $storageContext -Force

	これにより、コンピューターの現在のディレクトリに __example/output/shortestpaths__ ディレクトリ構造が作成され、この場所に 2 つの出力ファイルがダウンロードされます。

	__Cat__ コマンドレットを使用して、ファイルの内容を表示します。

		Cat example/output/shortestpaths/part*

	出力は次のようになります。


		0	1.0
		4	5.0
		2	2.0
		1	0.0
		3	1.0

	SimpleShortestPathComputation サンプルは、オブジェクト ID 1 から開始して他のオブジェクトへの最短パスを見つけるようにハードコーディングされています。したがって、出力は `destination_id distance` として読み取られます。distance はオブジェクト ID 1 とターゲット ID の間を結ぶエッジの値 (重み) です。
	
	これを視覚化して、ID 1 と他のすべてのオブジェクトの間で最短パスを結ぶことにより、結果を検証できます。ID 1 と ID 4 の最短パスは 5 です。これは、<span style="color:orange">ID 1 と 3</span>、<span style="color:red">ID 3 と 4</span> を結ぶ距離の合計です。

	![Drawing of objects as circles with shortest paths drawn between](./media/hdinsight-hadoop-giraph-install-v1/giraph-graph-out.png)


## <a name="usingPS"></a>Azure PowerShell を使用した HDInsight Hadoop クラスターでの Giraph のインストール

このセクションでは、**<a href = "http://msdn.microsoft.com/library/dn858088.aspx" target="_blank">Add-AzureHDInsightScriptActionScript Action</a>** コマンドレットで、スクリプト アクションを使用し、クラスターのカスタマイズを行うスクリプトを呼び出します。次に進む前に、Azure PowerShell をインストールして構成したことを確認します。コンピューターを構成して HDInsight Powershell コマンドレットを実行する方法については、「[Azure PowerShell のインストールおよび構成][powershell-install]」をご覧ください。

次の手順に従います。

1. Azure PowerShell ウィンドウを開き、次の変数を宣言します。

		# Provide values for these variables
		$subscriptionName = "<SubscriptionName>"		# Name of the Azure subscription
		$clusterName = "<HDInsightClusterName>"			# HDInsight cluster name
		$storageAccountName = "<StorageAccountName>"	# Azure Storage account that hosts the default container
		$storageAccountKey = "<StorageAccountKey>"      # Key for the Storage account
		$containerName = $clusterName
		$location = "<MicrosoftDataCenter>"				# Location of the HDInsight cluster. It must be in the same data center as the Storage account.
		$clusterNodes = <ClusterSizeInNumbers>			# Number of nodes in the HDInsight cluster
		$version = "<HDInsightClusterVersion>"          # For example, "3.1"
	
2. クラスター内のノードや使用する既定のストレージなどの構成値を指定します。

		# Specify the configuration options
		Select-AzureSubscription $subscriptionName
		$config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes
		$config.DefaultStorageAccount.StorageAccountName="$storageAccountName.blob.core.windows.net"
		$config.DefaultStorageAccount.StorageAccountKey=$storageAccountKey
		$config.DefaultStorageAccount.StorageContainerName=$containerName
	
3. **Add-AzureHDInsightScriptAction** コマンドレットを使用して、スクリプト アクションをクラスター構成に追加します。その後、クラスターの作成時に、このスクリプト アクションが実行されます。

		# Add a script action to the cluster configuration
		$config = Add-AzureHDInsightScriptAction -Config $config -Name "Install Giraph" -ClusterRoleCollection HeadNode -Uri https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1

	**Add-AzureHDInsightScriptAction** コマンドレットは、次のパラメーターを受け取ります。

	<table style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
<tr>
<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">パラメーター</th>
<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:550px; padding-left:5px; padding-right:5px;">定義</th></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Config</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">スクリプト アクション情報が追加される構成オブジェクト。</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">名前</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">スクリプト アクションの名前。</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">ClusterRoleCollection</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">カスタマイズ スクリプトが実行されるノード。有効な値は HeadNode (ヘッド ノードにインストールする場合) または DataNode (すべてのデータ ノードにインストールする場合) です。いずれかまたは両方の値を使用することができます。</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Uri</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">実行されるスクリプトへの URI。</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">パラメーター</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">スクリプトで必要なパラメーター。このトピックで使用するサンプル スクリプトでは、パラメーターは必要ありません。そのため、このパラメーターは上記のスニペットに含まれません。
</td></tr>
</table>
	
4. 最後に、Giraph がインストールされているカスタマイズされたクラスターのプロビジョニングを開始します。
	
		# Start provisioning a cluster with Giraph installed
		New-AzureHDInsightCluster -Config $config -Name $clusterName -Location $location -Version $version 

入力を求められたら、クラスターの資格情報を入力します。クラスターが作成されるまでに数分かかる場合があります。


## <a name="usingSDK"></a>.NET SDK を使用した HDInsight Hadoop クラスターでの Giraph のインストール

HDInsight .NET SDK は、.NET Framework アプリケーションから HDInsight を簡単に操作できる .NET クライアント ライブラリを提供します。このセクションでは、SDK からの Script Action を使用して、Giraph がインストールされているクラスターをプロビジョニングする方法について説明します。次の手順を実行する必要があります。

- HDInsight .NET SDK のインストール
- 自己署名証明書の作成
- コンソール アプリケーションの作成
- アプリケーションの実行


**HDInsight .NET SDK をインストールするには**

公開されている SDK の最新のビルドを [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started) からインストールできます。次の手順で、具体的な方法を説明します。

**自己署名証明書を取得するには**

自己署名証明書を作成し、それをコンピューターにインストールして、さらに、Azure サブスクリプションにアップロードします。手順については、「[Create a self-signed certificate (自己署名証明書の作成)](http://go.microsoft.com/fwlink/?LinkId=511138)」を参照してください。


**Visual Studio コンソール アプリケーションを作成するには**

1. Visual Studio 2013 を開きます。

2. **[ファイル]** メニューの **[新規作成]** をクリックし、**[プロジェクト]** をクリックします。

3. **[新しいプロジェクト]** で、次の値を入力するか、選択します。
	
	<table style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
<tr>
<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">プロパティ</th>
<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">値</th></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">カテゴリ</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">テンプレート/Visual C#/Windows</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">テンプレート</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">コンソール アプリケーション</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">名前</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">CreateGiraphCluster</td></tr>
</table>

4. **[OK]** をクリックしてプロジェクトを作成します。

5. **[ツール]** メニューで **[NuGet パッケージ マネージャー]**、**[パッケージ マネージャー コンソール]** の順にクリックします。

6. コンソールで次のコマンドを実行して、パッケージをインストールします。

		Install-Package Microsoft.WindowsAzure.Management.HDInsight

	このコマンドは、.NET ライブラリおよび .NET ライブラリへの参照を現在の Visual Studio プロジェクトから追加します。

7. ソリューション エクスプローラーで **Program.cs** をダブルクリックして、このファイルを開きます。

8. 次の using ステートメントをファイルの先頭に追加します。

		using System.Security.Cryptography.X509Certificates;
		using Microsoft.WindowsAzure.Management.HDInsight;
		using Microsoft.WindowsAzure.Management.HDInsight.ClusterProvisioning;
		using Microsoft.WindowsAzure.Management.HDInsight.Framework.Logging;
	
9. 次のコードをコピーして Main() 関数に貼り付けた後、変数の値を指定します。
		
        var clusterName = args[0];

        // Provide values for the variables
        string thumbprint = "<CertificateThumbprint>";  
        string subscriptionId = "<AzureSubscriptionID>";
        string location = "<MicrosoftDataCenterLocation>";
        string storageaccountname = "<AzureStorageAccountName>.blob.core.windows.net";
        string storageaccountkey = "<AzureStorageAccountKey>";
        string username = "<HDInsightUsername>";
        string password = "<HDInsightUserPassword>";
        int clustersize = <NumberOfNodesInTheCluster>;

        // Provide the certificate thumbprint to retrieve the certificate from the certificate store 
        X509Store store = new X509Store();
        store.Open(OpenFlags.ReadOnly);
        X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.Thumbprint == thumbprint);

        // Create an HDInsight client object
        HDInsightCertificateCredential creds = new HDInsightCertificateCredential(new Guid(subscriptionId), cert);
        var client = HDInsightClient.Connect(creds);
		client.IgnoreSslErrors = true;
        
        // Provide the cluster information
		var clusterInfo = new ClusterCreateParameters()
        {
            Name = clusterName,
            Location = location,
            DefaultStorageAccountName = storageaccountname,
            DefaultStorageAccountKey = storageaccountkey,
            DefaultStorageContainer = clusterName,
            UserName = username,
            Password = password,
            ClusterSizeInNodes = clustersize,
            Version = "3.1"
        };        

10. [ScriptAction](http://msdn.microsoft.com/library/microsoft.windowsazure.management.hdinsight.clusterprovisioning.data.scriptaction.aspx) クラスを使用して Giraph をインストールするカスタム スクリプトを呼び出すために、次のコードを Main() 関数に追加します。

		// Add the script action to install Giraph
        clusterInfo.ConfigActions.Add(new ScriptAction(
          "Install Giraph", // Name of the config action
          new ClusterNodeType[] { ClusterNodeType.HeadNode }, // List of nodes to install Giraph on
          new Uri("https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1"), // Location of the script to install Giraph
		  null //Because the script used does not require any parameters
        ));

11. 最後に、クラスターを作成します。

		client.CreateCluster(clusterInfo);

12. アプリケーションに変更を保存し、ソリューションをビルドします。

**アプリケーションを実行するには**

Azure PowerShell コンソールを開き、Visual Studio プロジェクトを保存した場所に移動し、プロジェクト内の \\bin\\debug ディレクトリに移動して、次のコマンドを実行します。

	.\CreateGiraphCluster <cluster-name>

クラスター名を指定し、Enter を押して、Giraph がインストールされているクラスターをプロビジョニングします。


## 関連項目##
- 「[HDInsight クラスターでの Spark のインストールと使用][hdinsight-install-spark]」に関するページでは、HDInsight Hadoop クラスターでの Spark のインストールと使用を行うためのクラスター カスタマイズの使用法に関する手順が説明されています。Spark はビッグ データ分析アプリケーションのパフォーマンスを向上するメモリ内の処理をサポートする、オープン ソースの並列処理のフレームワークです。
- 「[HDInsight Hadoop クラスターでの R のインストール][hdinsight-install-r]」に関するページでは、HDInsight Hadoop クラスターで R をインストールして使用するためのクラスター カスタマイズの使用法に関する手順が説明されています。R は、統計計算用のオープン ソースの言語および環境です。R は、数百の組み込み統計関数と、関数型プログラミングとオブジェクト指向のプログラミングの特徴を結合した独自のプログラミング言語を提供します。また、広範なグラフィカル機能も提供します。
- [HDInsight クラスターでの Solr のインストール](hdinsight-hadoop-solr-install.md):クラスターのカスタマイズを使用して、HDInsight Hadoop クラスターに Solr をインストールします。Solr は、格納されたデータに対して強力な検索操作を実行することができます。



[tools]: https://github.com/Blackmist/hdinsight-tools
[aps]: http://azure.microsoft.com/documentation/articles/install-configure-powershell/

[powershell-install]: ../powershell-install-configure.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-install-r]: hdinsight-hadoop-r-scripts.md
[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster.md
 

<!---HONumber=August15_HO8-->