<properties
	pageTitle="HDInsight の Hadoop クラスターに Giraph をインストールして使用する | Microsoft Azure"
	description="Giraph を使用して HDInsight クラスターをカスタマイズする方法と Giraph の使用方法について説明します。"
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
	ms.date="02/05/2016"
	ms.author="nitinme"/>

# HDInsight に Giraph をインストールし、使用する


Script Action を使用して Windows ベースの HDInsight クラスターを Giraph でカスタマイズする方法と、Giraph を使用して大規模なグラフを処理する方法について説明します。Linux ベースのクラスターでの Giraph の操作については、「[HDInsight Hadoop クラスターに Giraph をインストールする (Linux)](hdinsight-hadoop-giraph-install-linux.md)」を参照してください。
 
*スクリプト アクション*を使用し、Azure HDInsight の任意の種類のクラスター (Hadoop、Storm、HBase、Spark) に Giraph をインストールできます。HDInsight クラスターに Giraph をインストールするサンプル スクリプトは、[https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1) の読み取り専用の Azure ストレージ BLOB から入手できます。サンプル スクリプトは、HDInsight クラスター version 3.1 でのみ機能します。HDInsight クラスター バージョンの詳細については、「[HDInsight クラスター バージョン](hdinsight-component-versioning.md)」をご覧ください。

**関連記事:**

- [HDInsight Hadoop クラスターに Giraph をインストールする (Linux)](hdinsight-hadoop-giraph-install-linux.md)
- [HDInsight で Hadoop クラスターを作成する](hdinsight-provision-clusters.md): HDInsight クラスターの作成に関する一般情報。
- [Script Action を使用して HDInsight クラスターをカスタマイズする][hdinsight-cluster-customize]\: Script Action を使用した HDInsight クラスターのカスタマイズに関する一般情報。
- [HDInsight 用の Script Action スクリプトの開発](hdinsight-hadoop-script-actions.md)

## Giraph とは

<a href="http://giraph.apache.org/" target="_blank">Apache Giraph</a> は、Hadoop でグラフの処理を実行するために使用でき、Azure HDInsight で使用できます。グラフでは、オブジェクト間の関係 (インターネットのような大規模ネットワークでのルーター間の接続など) や、ソーシャル ネットワークでの人々の関係 (ソーシャル グラフとも呼ばれる) をモデル化します。グラフの処理により、次のようなグラフ内のオブジェクト間の関係について推論できます。

- 現在の関係に基づいて潜在的な友人を識別する
- ネットワーク内の 2 台のコンピューター間の最短ルートを識別する
- Web ページのページ順位を計算する


## ポータルを使用して Giraph をインストールする

1. 「[HDInsight で Hadoop クラスターを作成する](hdinsight-provision-clusters.md#portal)」の説明に基づき、**CUSTOM CREATE** オプションを使用してクラスターの作成を開始します。
2. ウィザードの **[スクリプトのアクション]** ページで、**[スクリプト アクションの追加]** をクリックし、次に示すように、スクリプト アクションの詳細を指定します。

	![Script Action を使ってクラスターをカスタマイズする](./media/hdinsight-hadoop-giraph-install/hdi-script-action-giraph.png "スクリプト アクションを使ってクラスターをカスタマイズする")

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
	</table>

	複数のスクリプト操作を追加して、クラスターに複数のコンポーネントをインストールすることができます。スクリプトの追加後、チェックマークをクリックしてクラスターの作成を開始します。

## Giraph の使用

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

	![円で表した tiny\_graph.txt (線はオブジェクト間の距離)](./media/hdinsight-hadoop-giraph-install/giraph-graph.png)



4. SimpleShortestPathsComputation サンプルを実行します。tiny\_graph.txt ファイルを入力として使用し、次の Azure PowerShell コマンドレットを使用してサンプルを実行します。

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

		$clusterName = "clustername"
		# Giraph examples jar
		$jarFile = "wasbs:///example/jars/giraph-examples.jar"
		# Arguments for this job
		$jobArguments = "org.apache.giraph.examples.SimpleShortestPathsComputation",
		                "-ca", "mapred.job.tracker=headnodehost:9010",
		                "-vif", "org.apache.giraph.io.formats.JsonLongDoubleFloatDoubleVertexInputFormat",
		                "-vip", "wasbs:///example/data/tiny_graph.txt",
		                "-vof", "org.apache.giraph.io.formats.IdWithValueTextOutputFormat",
		                "-op",  "wasbs:///example/output/shortestpaths",
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

5. 結果を表示します。ジョブが完了すると、結果が __wasbs:///example/out/shotestpaths__ フォルダーの 2 つの出力ファイルに格納されます。この 2 つのファイルは __part-m-00001 と __part-m-00002__ という名前です。出力をダウンロードして表示するには、次の手順を実行します。

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

	![オブジェクトを円で表し、最短パスで結んだ図](./media/hdinsight-hadoop-giraph-install/giraph-graph-out.png)

## Aure PowerShell を使用して Giraph をインストールする

「[Script Action を使用して HDInsight クラスターをカスタマイズする](hdinsight-hadoop-customize-cluster.md#call_scripts_using_powershell)」を参照してください。このサンプルでは、Azure PowerShell を使用して Spark をインストールする方法を示します。スクリプトをカスタマイズし、[https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1) を使用する必要があります。

## .NET SDK を使用して Giraph をインストールする

「[Script Action を使用して HDInsight クラスターをカスタマイズする](hdinsight-hadoop-customize-cluster.md#call_scripts_using_azure_powershell)」を参照してください。このサンプルでは、.NET SDK を使用して Spark をインストールする方法を示します。スクリプトをカスタマイズし、[https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1) を使用する必要があります。


## 関連項目

- [HDInsight Hadoop クラスターに Giraph をインストールする (Linux)](hdinsight-hadoop-giraph-install-linux.md)
- [HDInsight で Hadoop クラスターを作成する](hdinsight-provision-clusters.md): HDInsight クラスターの作成に関する一般情報。
- [Script Action を使用して HDInsight クラスターをカスタマイズする][hdinsight-cluster-customize]\: Script Action を使用した HDInsight クラスターのカスタマイズに関する一般情報。
- [HDInsight 用の Script Action スクリプトの開発](hdinsight-hadoop-script-actions.md)
- [HDInsight クラスターに Spark をインストールし、使用する][hdinsight-install-spark]\: Spark のインストールに関する Script Action サンプル。
- [HDInsight クラスターに R をインストールし、使用する][hdinsight-install-r]\: R のインストールに関する Script Action サンプル。
- [HDInsight クラスターに Solr をインストールし、使用する](hdinsight-hadoop-solr-install.md): Solr のインストールに関する Script Action サンプル。



[tools]: https://github.com/Blackmist/hdinsight-tools
[aps]: http://azure.microsoft.com/documentation/articles/install-configure-powershell/

[powershell-install]: ../powershell-install-configure.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-install-r]: hdinsight-hadoop-r-scripts.md
[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster.md

<!---HONumber=AcomDC_0727_2016-->