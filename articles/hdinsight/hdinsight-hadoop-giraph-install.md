<properties
	pageTitle="HDInsight の Hadoop クラスターに Giraph をインストールして使用する | Microsoft Azure"
	description="Giraph を使用して HDInsight クラスターをカスタマイズする方法について説明します。スクリプトを使用して Giraph をインストールするために、Script Action の構成オプションを使用します。"
	services="hdinsight"
	documentationCenter=""
	authors="nitinme"
	manager="paulettm"
	editor="cgronlun"
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/11/2015"
	ms.author="nitinme"/>

# HDInsight Hadoop クラスターに Giraph をインストールし、Giraph を使用して大規模なグラフを処理する

**Script Action** クラスター カスタマイズを使用して、Azure HDInsight の Hadoop の任意の種類のクラスターに Giraph をインストールできます。Script Action では、クラスターの作成時のみ、スクリプトを実行してクラスターをカスタマイズできます。詳細については、「[Script Action を使って HDInsight クラスターをカスタマイズする][hdinsight-cluster-customize]」をご覧ください。

> [AZURE.NOTE]この記事の情報は、Windows ベースの HDInsight クラスターに固有のものです。Linux ベースのクラスターの操作については、[HDInsight Hadoop クラスターへの Giraph のインストール (Linux)](hdinsight-hadoop-giraph-install-linux.md) に関するページを参照してください。

このトピックでは、Script Action を使用して Giraph をインストールする方法を学習します。Giraph をインストールした後は、大規模なグラフを処理する多くの一般的なアプリケーションで Giraph を使用する方法についても説明します。

[AZURE.INCLUDE [hdinsight-azure-preview-portal](../../includes/hdinsight-azure-preview-portal.md)]

* [HDInsight クラスターでの Giraph のインストール](hdinsight-hadoop-giraph-install-v1.md)

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

	![Script Action を使ってクラスターをカスタマイズする](./media/hdinsight-hadoop-giraph-install/hdi-script-action-giraph.png "Script Action を使ってクラスターをカスタマイズする")

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

	![tiny\_graph.txt drawn as circles with lines of varying distance between](./media/hdinsight-hadoop-giraph-install/giraph-graph.png)



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

	![Drawing of objects as circles with shortest paths drawn between](./media/hdinsight-hadoop-giraph-install/giraph-graph-out.png)



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

<!---HONumber=Sept15_HO2-->