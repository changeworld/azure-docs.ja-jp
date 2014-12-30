<properties title="How to use Giraph with HDInsight" pageTitle="Apache Giraph を Azure HDInsight で使用する方法 " description="Apache Giraph を使用して Azure HDInsight でグラフ処理を実行する方法について説明します" metaKeywords="Azure HDInsight Apache Giraph, hdinsight giraph, hdinsight graph, hadoop giraph, azure hadoop, hadoop graph" services="hdinsight" solutions="big-data" documentationCenter="" authors="larryfr" videoId="" scriptId="" manager="paulettm" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/14/2014" ms.author="larryfr" />

#Apache Giraph を Azure HDInsight (Hadoop) で使用する方法

[Apache Giraph][giraph] は、Hadoop でグラフの処理を実行するために使用でき、Azure HDInsight で使用できます。 

グラフでは、オブジェクト間の関係 (インターネットのような大規模ネットワークでのルーター間の接続など) や、ソーシャル ネットワークでの人々の関係 (ソーシャル グラフとも呼ばれる) をモデル化します。グラフの処理により、次のようなグラフ内のオブジェクト間の関係について推論できます。

* 現在の関係に基づいて潜在的な友人を識別する

* ネットワーク内の 2 台のコンピューター間の最短ルートを識別する

* Web ページのページ順位を計算する

##学習内容

* [Apache Giraph をビルドして HDInsight クラスターにデプロイする](#build)

* [SimpleShortestPathsComputation サンプルを実行する](#run)

	Giraph で提供されるその他のサンプルの一覧については、「[Package org.apache.giraph.examples (パッケージ org.apache.giraph.examples)]」を参照してください(https://giraph.apache.org/apidocs/org/apache/giraph/examples/package-summary.html)。

* [発生する可能性のある問題のトラブルシューティングを行う](#tshoot)

##要件

* Azure HDInsight クラスター、Version 3.0 または 3.1

* [Git](http://git-scm.com/)

* Java 1.6

* [Maven](http://maven.apache.org/) 3 以降

##<a id="build"></a>Giraph のビルドとデプロイ

Giraph は、HDInsight クラスターの一部としては提供されないため、ソースからビルドする必要があります。Giraph のビルドの詳細については、[Giraph リポジトリ]を参照してください(https://github.com/apache/giraph)。

1. 現在 (2014 年 7 月 14 日)、Giraph では、HDInsight で使用される WASB ファイル ストレージを操作するためにパッチが必要です。このパッチは、Apache Giraph プロジェクトに提出済みですが、まだ承認されていません。[GIRAPH-930] の __atachments__ セクションからパッチをダウンロードし、(https://issues.apache.org/jira/browse/GIRAPH-930) __giraph-930.diff__ としてローカル ドライブに保存します。

1. コマンド ラインから、次の Git コマンドを使用して Giraph リポジトリのクローンを作成します。

		git clone https://github.com/apache/giraph.git

2. ディレクトリを、手順 2. の複製操作で作成した __giraph__ ディレクトリに変更します。

		cd giraph

3. 次のコマンドを使用して、パッチをローカル リポジトリにマージします。

		git apply giraph-930.diff

	__giraph-930.diff__ を、手順 1. で作成したファイルへのパスで置き換えます。

3. 次のコマンドのいずれかを使用して、自分の HDInsight クラスター バージョンの Giraph をビルドします。

	* __HDInsight 3.0__ (Hadoop 2.2) の場合

			mvn package -Phadoop_0.20.203 - DskipTests

	* __HDInsight 3.1__ (Hadoop 2.4) の場合

			mvn package -Phadoop_0.23 -DskipTests

	ビルドが完了すると、サンプルの JAR ファイルが __\\giraph\\giraph-examples\\target__ に保存されます。

4. [Azure PowerShell][aps] および [HDInsight-Tools][tools] を使用して、サンプルの JAR ファイルを HDInsight クラスターのプライマリ ストレージにアップロードします。

		Add-HDInsightFile giraph-examples-1.1.0-SNAPSHOT-for-hadoop-0.23.1-jar-with-dependencies.jar example/jars/giraph.jar clustername

	__giraph-examples-1.1.0-SNAPSHOT-for-hadoop-0.23.1-jar-with-dependencies.jar__ を、前の手順で生成した JAR ファイルのパスと名前で置き換え、__clustername__ を HDInsight クラスターの名前で置き換えます。たとえば、パッケージを `-Phadoop_0.20.203` パラメーターでビルドする場合は、JAR のファイル名に __hadoop-0.20.203__ が含まれます。

	コマンドが完了すると、JAR ファイルが wasb:///example/jars/giraph.jar にアップロードされています。

	> [WACOM.NOTE] HDInsight へのファイルのアップロードに使用できるユーティリティの一覧については、「[HDInsight での Hadoop ジョブ用データのアップロード]」を参照してください(http://azure.microsoft.com/ja-jp/documentation/articles/hdinsight-upload-data/)。

##<a id="run"></a>サンプルの実行

SimpleShortestPathsComputation は、グラフのオブジェクト間の最短パスを見つけるための基本的な [Pregel] (http://people.apache.org/~edwardyoon/documents/pregel.pdf) の実装を示します。次の手順を使用して、サンプル データをアップロードし、SimpleShortestPathsComputation サンプルでジョブを実行して、結果を表示します。

> [WACOM.NOTE] このサンプルや他のサンプルのソースは、[GitHub リポジトリ]の (https://github.com/apache/giraph/tree/release-1.1) [release-1.1 branch] で入手できます(https://github.com/apache/giraph)。

1. __tiny_graph.txt__ という名前の新しいファイルを作成します。次の行が含まれます。

		[0,0,[[1,1],[3,3]]]
		[1,0,[[0,1],[2,2],[3,1]]]
		[2,0,[[1,2],[4,4]]]
		[3,0,[[0,3],[1,1],[4,4]]]
		[4,0,[[3,4],[2,4]]]

	このデータは、[有向グラフ]内のオブジェクト間の関係を(http://en.wikipedia.org/wiki/Directed_graph)`[source_id,source_value,[[dest_id], [edge_value],...]]` の形式を使用して示しています。各行は、__source_id__ オブジェクトと 1 つ以上の __dest_id__ オブジェクトとの関係を表現しています。__edge_value__ (重み) は、__source_id__ と __dest_id__ の間のつながりの強さと距離であると考えられます。 

	この値 (重み) を使用し、オブジェクト間の距離に応じて線を引くと、先のデータは次の図のようになります。

	![tiny_graph.txt drawn as circles with lines of varying distance between](.\media\hdinsight-giraph\giraph-graph.png)


2. [Azure PowerShell][aps] および [HDInsight-Tools][tools] を使用して、__tiny\_graph.txt__ ファイルを HDInsight クラスターのプライマリ ストレージにアップロードします。

		Add-HDInsightFile tiny_graph.txt example/data/tiny_graph.txt clustername

	クラスター名を HDInsight クラスターの名前で置き換えます。

3. __tiny\_graph.txt__ ファイルを入力として使用し、次の PowerShell を使用して __SimpleShortstPathsComputation__ サンプルを実行します。これには、[Azure PowerShell][aps] がインストール済みおよび構成済みであることが必要です。

		$clusterName = "clustername"
		# Giraph examples JAR
		$jarFile = "wasb:///example/jars/giraph.jar"
		# Arguments for this job
		$jobArguments = "org.apache.giraph.examples.SimpleShortestPathsComputation", `
		                "-ca", "mapred.job.tracker=headnodehost:9010", `
		                "-vif", "org.apache.giraph.io.formats.JsonLongDoubleFloatDoubleVertexInputFormat", `
		                "-vip", "wasb:///example/data/tinygraph.txt", `
		                "-vof", "org.apache.giraph.io.formats.IdWithValueTextOutputFormat", `
		                "-op",  "wasb:///example/output/shortestpaths", `
		                "-w", "2"
		# Create the definition
		$jobDefinition = New-AzureHDInsightMapReduceJobDefinition `
		  -JarFile $jarFile `
		  -ClassName "org.apache.giraph.GiraphRunner" `
		  -Arguments $jobArguments
		
		# Run the job, write output to the PowerShell window
		$job = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $jobDefinition
		Write-Host "Wait for the job to complete ..." -ForegroundColor Green
		Wait-AzureHDInsightJob -Job $job
		Write-Host "STDERR"
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardError
		Write-Host "Display the standard output ..." -ForegroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardOutput

	この例では、__clustername__ を HDInsight クラスターの名前で置き換えます。

###View the results

ジョブが完了すると、結果が __part-m-#####__ ファイルとして __wasb:///example/out/shotestpaths__ フォルダーに格納されます。[Azure PowerShell][aps] および [HDInsight-Tools][tools] を使用して、出力ファイルをダウンロードします。

	Find-HDInsightFile example/output/shortestpaths/part* clustername | foreach-object { Get-HDInsightFile $_.name .  itsfullofstorage }
	Cat example/output/shortestpaths/part*

これにより、現在のディレクトリに __example/output/shortestpaths__ ディレクトリ構造が作成され、__part__ で始まるファイルがダウンロードされます。__Cat__ コマンドレットにより、次に示すようなファイルの内容が表示されます。

	0	1.0
	4	5.0
	2	2.0
	1	0.0
	3	1.0

SimpleShortestPathComputation サンプルは、オブジェクト ID 1 から開始して他のオブジェクトへの最短パスを見つけるようにハードコーディングされています。したがって、出力は `destination_id distance` として読み取られます。distance はオブジェクト ID 1 とターゲット ID の間を結ぶエッジの値 (重み) です。

これを視覚化して、ID 1 と他のすべてのオブジェクトの間で最短パスを結ぶことにより、結果を検証できます。ID 1 と ID 4 の最短パスは 5 です。これは、 <span style="color:orange">ID 1 と ID 3</span>、 <span style="color:red">ID 3 と ID 4 </span>の間の総距離です。

![Drawing of objects as circles with shortest paths drawn between](.\media\hdinsight-giraph\giraph-graph-out.png)

##<a id="tshoot"></a>トラブルシューティング

###出力ディレクトリが既に存在している

Giraph ジョブは、指定された出力ディレクトリを実行時に作成します。ディレクトリが既に存在する場合は、出力ディレクトリが既に存在していることを示すエラーが生成されます。

ジョブを複数回実行する場合は、ジョブとジョブの間で出力ディレクトリを削除するか、各ジョブに異なる出力ディレクトリを指定します。

###<a id="cmd"></a>Hadoop コマンド ラインの使用

この記事では PowerShell を介して Giraph ジョブを実行する方法を示していますが、Hadoop コマンド ラインでジョブを実行することもできます。

> [WACOM.NOTE] Hadoop コマンド ラインは、リモート デスクトップを使用して HDInsight クラスターに接続している場合のみ利用可能です。
> 
> HDInsight クラスターなどの Azure コンピューティング リソースに対するリモート デスクトップのセッションは、Windows ベースのリモート デスクトップ クライアントからのみ動作します。

HDInsight クラスターに接続するには、次の手順を実行します。

1. [Azure 管理ポータル]を使用して、(https://manage.windowsazure.com)HDInsight クラスターを選択し、__[構成]__ を選択します。

2. ページ下部で、__[リモートを有効にする]__ を選択し、リモート デスクトップ接続のユーザー名、パスワード、および有効期限を入力します。

3. リモート デスクトップを有効にする要求が処理された後で、__[接続]__ への新しいエントリがページ下部に表示されます。これを選択して、リモート デスクトップ セッションの .RDP ファイルをダウンロードします。

4. .RDP ファイルを保存するか、すぐに開いてリモート デスクトップ クライアントを起動することができます。接続プロセスの間に、リモート デスクトップ接続を有効にしたときに使用したユーザー名とパスワードを入力するように求められます。

5. 接続されたら、デスクトップ上の __[Hadoop コマンド ライン]__ アイコンを使用して Hadoop コマンド ラインを開始します。

6. 次の例では、__giraph.jar__ ファイルをクラスター ヘッド ノードにコピーし、Hadoop コマンド ラインを使用してジョブを実行する方法を示しています。

		hadoop fs -copyToLocal wasb:///example/jar/giraph.jar
		hadoop jar giraph.jar org.apache.giraph.GiraphRunner org.apache.giraph.examples.SimpleShortestPathsComputation -ca "mapred.job.tracker=headnodehost:9010" -vif org.apache.giraph.io.formats.JsonLongDoubleFloatDoubleVertexInputFormat -vip wasb:///example/data/tinygraph.txt -vof org.apache.giraph.io.formats.IdWithValueTextOutputFormat -op wasb:///example/output/shortestpaths -w 2


###旧バージョンの HDInsight

旧バージョンの HDInsight で Giraph を使用する場合は、そのバージョンでサポートされる Hadoop バージョン用にコンパイルする必要があります。自分の HDInsight バージョンに対応する Hadoop バージョンを判断するには、「[HDInsight で提供されるクラスター バージョンの新機能]」(http://azure.microsoft.com/ja-jp/documentation/articles/hdinsight-component-versioning/) を参照してください。

さらに、旧バージョンの HDInsight では、Giraph ジョブを Hadoop コマンド ラインから実行する必要がある場合もあります。PowerShell からジョブを実行してエラーが発生した場合は、[Hadoop コマンド ライン]からジョブを実行してみてください(#cmd)。

##次のステップ

ここでは、Giraph を HDInsight で使用する方法を学習しました。次は、HDInsight で [Pig][] および [Hive][] を試してみてください。

[giraph]: http://giraph.apache.org
[tools]: https://github.com/Blackmist/hdinsight-tools
[aps]: http://azure.microsoft.com/ja-jp/documentation/articles/install-configure-powershell/
[pig]: http://azure.microsoft.com/ja-jp/documentation/articles/hdinsight-use-pig/
[hive]: http://azure.microsoft.com/ja-jp/documentation/articles/hdinsight-use-hive/

<!--HONumber=35_1-->
