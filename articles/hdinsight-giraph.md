<properties title="Giraph を HDInsight で使用する方法" pageTitle="Apache Giraph を HDInsight で使用する方法" description="Apache Giraph を使って Azure HDInsight でグラフ処理を実行する方法について説明します。" metaKeywords="Azure HDInsight Apache Giraph, hdinsight giraph, hdinsight graph, hadoop giraph, azure hadoop, hadoop graph" services="hdinsight" solutions="big-data" documentationCenter="" authors="larryfr" videoId="" scriptId="" manager="paulettm" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/14/2014" ms.author="larryfr" />

## Apache Giraph を Azure HDInsight (Hadoop) で使用する方法

[Apache Giraph][Apache Giraph] は、Hadoop でグラフの処理を実行するために使用でき、Azure HDInsight で使用できます。

グラフでは、オブジェクト間の関係 (インターネットのような大規模ネットワークでのルーター間の接続など) や、ソーシャル ネットワークでの人々の関係 (ソーシャル グラフとも呼ばれる) をモデル化します。グラフの処理により、次のようなグラフ内のオブジェクト間の関係について推論できます。

-   現在の関係に基づいて潜在的な友人を識別する

-   ネットワーク内の 2 台のコンピューター間の最短ルートを識別する

-   Web ページのページ順位を計算する

## 学習内容

-   [Apache Giraph をビルドして HDInsight クラスターにデプロイする][Apache Giraph をビルドして HDInsight クラスターにデプロイする]

-   [SimpleShortestPathsComputation サンプルを実行する][SimpleShortestPathsComputation サンプルを実行する]

    Giraph で提供されるその他のサンプルの一覧については、「[Package org.apache.giraph.examples (パッケージ org.apache.giraph.examples)][Package org.apache.giraph.examples (パッケージ org.apache.giraph.examples)]」を参照してください。

-   [発生する可能性のある問題のトラブルシューティングを行う][発生する可能性のある問題のトラブルシューティングを行う]

## 要件

-   Azure HDInsight クラスター、Version 3.0 または 3.1

-   [Git][Git]

-   Java 1.6

-   [Maven][Maven] 3 以降

## <span id="build"></span></a>Giraph のビルドとデプロイ

Giraph は、HDInsight クラスターの一部としては提供されないため、ソースからビルドする必要があります。Giraph のビルドの詳細については、[Giraph リポジトリ][Giraph リポジトリ]を参照してください。

1.  現在 (2014 年 7 月 14 日)、Giraph では、HDInsight で使用される WASB ファイル ストレージを操作するためにパッチが必要です。このパッチは、Apache Giraph プロジェクトに提出済みですが、まだ承認されていません。[GIRAPH-930][GIRAPH-930] の **Attachments (添付ファイル)** のセクションからパッチをダウンロードし、**giraph-930.diff** としてローカル ドライブに保存します。

2.  コマンド ラインから、次の Git コマンドを使用して Giraph リポジトリのクローンを作成します。

        git clone https://github.com/apache/giraph.git

3.  ディレクトリを、手順 2. の複製操作で作成した **giraph** ディレクトリに変更します。

        cd giraph

4.  次のコマンドを使用して、パッチをローカル リポジトリにマージします。

        git apply giraph-930.diff

    **giraph-930.diff** を、手順 1. で作成したファイルへのパスで置き換えます。

5.  次のコマンドのいずれかを使用して、自分の HDInsight クラスター バージョンの Giraph をビルドします。

    -   **HDInsight 3.0** (Hadoop 2.2) の場合

            mvn package -Phadoop_0.20.203 - DskipTests

    -   **HDInsight 3.1** (Hadoop 2.4) の場合

            mvn package -Phadoop_0.23 -DskipTests

    ビルドが完了すると、サンプルの JAR ファイルが **\\giraph\\giraph-examples\\target** に保存されます。

6.  [Azure PowerShell][Azure PowerShell] および [HDInsight-Tools][HDInsight-Tools] を使用して、サンプルの JAR ファイルを HDInsight クラスターのプライマリ ストレージにアップロードします。

        Add-HDInsightFile giraph-examples-1.1.0-SNAPSHOT-for-hadoop-0.23.1-jar-with-dependencies.jar example/jars/giraph.jar clustername

    **giraph-examples-1.1.0-SNAPSHOT-for-hadoop-0.23.1-jar-with-dependencies.jar** を、前の手順で生成した JAR ファイルのパスと名前で置き換え、**clustername** を HDInsight クラスターの名前で置き換えます。たとえば、パッケージを `-Phadoop_0.20.203` パラメーターでビルドする場合は、JAR のファイル名に **hadoop-0.20.203** が含まれます。

    コマンドが完了すると、JAR ファイルが wasb:///example/jars/giraph.jar にアップロードされています。

    > [WACOM.NOTE] HDInsight へのファイルのアップロードに使用できるユーティリティの一覧については、「[データを HDInsight にアップロードする方法][データを HDInsight にアップロードする方法]」を参照してください。

## <span id="run"></span></a>サンプルの実行

SimpleShortestPathsComputation は、グラフのオブジェクト間の最短パスを見つけるための基本的な [Pregel][Pregel] の実装を示します。次の手順を使用して、サンプル データをアップロードし、SimpleShortestPathsComputation サンプルでジョブを実行して、結果を表示します。

> [WACOM.NOTE] このサンプルや他のサンプルのソースは、[GitHub リポジトリ][Giraph リポジトリ]の [release-1.1 branch][release-1.1 branch] で入手できます。

1.  **tiny\_graph.txt** という名前の新しいファイルを作成します。次の行が含まれます。

        [0,0,[[1,1],[3,3]]]
        [1,0,[[0,1],[2,2],[3,1]]]
        [2,0,[[1,2],[4,4]]]
        [3,0,[[0,3],[1,1],[4,4]]]
        [4,0,[[3,4],[2,4]]]

    このデータは、`[source_id,source_value,[[dest_id], [edge_value],...]]` の形式を使用して、[有向グラフ][有向グラフ]内のオブジェクト間の関係を示しています。各行は、**source\_id** オブジェクトと 1 つ以上の **dest\_id** オブジェクトとの関係を表現しています。**edge\_value** (重み) は、**source\_id** と **dest\_id** の間のつながりの強さと距離であると考えられます。

    この値 (重み) を使用し、オブジェクト間の距離に応じて線を引くと、先のデータは次の図のようになります。

    ![tiny\_graph.txt drawn as circles with lines of varying distance between][tiny\_graph.txt drawn as circles with lines of varying distance between]

2.  [Azure PowerShell][Azure PowerShell] および [HDInsight-Tools][HDInsight-Tools] を使用して、**tiny\_graph.txt** ファイルを HDInsight クラスターのプライマリ ストレージにアップロードします。

        Add-HDInsightFile tiny_graph.txt example/data/tiny_graph.txt clustername

    クラスター名を HDInsight クラスターの名前で置き換えます。

3.  **tiny\_graph.txt** ファイルを入力として使用し、次の PowerShell を使用して **SimpleShortstPathsComputation** サンプルを実行します。これには、[Azure PowerShell][Azure PowerShell] がインストール済みおよび構成済みであることが必要です。

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

    この例では、**clustername** を HDInsight クラスターの名前で置き換えます。

### View the results

ジョブが完了すると、結果が **part-m-\#\#\#\#\#** ファイルとして **wasb:///example/out/shotestpaths** フォルダーに格納されます。[Azure PowerShell][Azure PowerShell] および [HDInsight-Tools][HDInsight-Tools] を使用して、出力ファイルをダウンロードします。

    Find-HDInsightFile example/output/shortestpaths/part* clustername | foreach-object { Get-HDInsightFile $_.name .  itsfullofstorage }
    Cat example/output/shortestpaths/part*

これにより、現在のディレクトリに **example/output/shortestpaths** ディレクトリ構造が作成され、**part** で始まるファイルがダウンロードされます。**Cat** コマンドレットにより、次に示すようなファイルの内容が表示されます。

    0   1.0
    4   5.0
    2   2.0
    1   0.0
    3   1.0

SimpleShortestPathComputation サンプルは、オブジェクト ID 1 から開始して他のオブジェクトへの最短パスを見つけるようにハードコーディングされています。したがって、出力は `destination_id distance` として読み取られます。distance はオブジェクト ID 1 とターゲット ID の間を結ぶエッジの値 (重み) です。

これを視覚化して、ID 1 と他のすべてのオブジェクトの間で最短パスを結ぶことにより、結果を検証できます。ID 1 と ID 4 の最短パスは 5 です。これは、<span style="color:orange">ID 1 と 3</span>、次に <span style="color:red">ID 3 と 4</span> を結ぶ距離の合計です。

![Drawing of objects as circles with shortest paths drawn between][Drawing of objects as circles with shortest paths drawn between]

## <span id="tshoot"></span></a>トラブルシューティング

### 出力ディレクトリが既に存在している

Giraph ジョブは、指定された出力ディレクトリを実行時に作成します。ディレクトリが既に存在する場合は、出力ディレクトリが既に存在していることを示すエラーが生成されます。

ジョブを複数回実行する場合は、ジョブとジョブの間で出力ディレクトリを削除するか、各ジョブに異なる出力ディレクトリを指定します。

### <span id="cmd"></span></a>Hadoop コマンド ラインの使用

この記事では PowerShell を介して Giraph ジョブを実行する方法を示していますが、Hadoop コマンド ラインでジョブを実行することもできます。

> [WACOM.NOTE] Hadoop コマンド ラインは、リモート デスクトップを使用して HDInsight クラスターに接続している場合のみ利用可能です。
>
> HDInsight クラスターなどの Azure コンピューティング リソースに対するリモート デスクトップのセッションは、Windows ベースのリモート デスクトップ クライアントからのみ動作します。

HDInsight クラスターに接続するには、次の手順を実行します。

1.  [Azure 管理ポータル][Azure 管理ポータル]を使用して、HDInsight クラスターを選択し、**[構成]** を選択します。

2.  ページ下部で、**[リモートを有効にする]** を選択し、リモート デスクトップ接続のユーザー名、パスワード、および有効期限を入力します。

3.  リモート デスクトップを有効にする要求が処理された後で、**[接続]** への新しいエントリがページ下部に表示されます。これを選択して、リモート デスクトップ セッションの .RDP ファイルをダウンロードします。

4.  .RDP ファイルを保存するか、すぐに開いてリモート デスクトップ クライアントを起動することができます。接続プロセスの間に、リモート デスクトップ接続を有効にしたときに使用したユーザー名とパスワードを入力するように求められます。

5.  接続されたら、デスクトップ上の **[Hadoop コマンド ライン]** アイコンを使用して Hadoop コマンド ラインを開始します。

6.  次の例では、**giraph.jar** ファイルをクラスター ヘッド ノードにコピーし、Hadoop コマンド ラインを使用してジョブを実行する方法を示しています。

        hadoop fs -copyToLocal wasb:///example/jar/giraph.jar
        hadoop jar giraph.jar org.apache.giraph.GiraphRunner org.apache.giraph.examples.SimpleShortestPathsComputation -ca "mapred.job.tracker=headnodehost:9010" -vif org.apache.giraph.io.formats.JsonLongDoubleFloatDoubleVertexInputFormat -vip wasb:///example/data/tinygraph.txt -vof org.apache.giraph.io.formats.IdWithValueTextOutputFormat -op wasb:///example/output/shortestpaths -w 2

### 旧バージョンの HDInsight

旧バージョンの HDInsight で Giraph を使用する場合は、そのバージョンでサポートされる Hadoop バージョン用にコンパイルする必要があります。自分の HDInsight バージョンに対応する Hadoop バージョンを判断するには、「[HDInsight で提供されるクラスター バージョンの新機能][HDInsight で提供されるクラスター バージョンの新機能]」を参照してください。

さらに、旧バージョンの HDInsight では、Giraph ジョブを Hadoop コマンド ラインから実行する必要がある場合もあります。PowerShell からジョブを実行してエラーが発生した場合は、[Hadoop コマンド ライン][Hadoop コマンド ライン]からジョブを実行してみてください。

## 次のステップ

ここでは、Giraph を HDInsight で使用する方法を学習しました。次は、HDInsight で [Pig][Pig] および [Hive][Hive] を試してみてください。

  [Apache Giraph]: http://giraph.apache.org
  [Apache Giraph をビルドして HDInsight クラスターにデプロイする]: #build
  [SimpleShortestPathsComputation サンプルを実行する]: #run
  [Package org.apache.giraph.examples (パッケージ org.apache.giraph.examples)]: https://giraph.apache.org/apidocs/org/apache/giraph/examples/package-summary.html
  [発生する可能性のある問題のトラブルシューティングを行う]: #tshoot
  [Git]: http://git-scm.com/
  [Maven]: http://maven.apache.org/
  [Giraph リポジトリ]: https://github.com/apache/giraph
  [GIRAPH-930]: https://issues.apache.org/jira/browse/GIRAPH-930
  [Azure PowerShell]: http://azure.microsoft.com/ja-jp/documentation/articles/install-configure-powershell/
  [HDInsight-Tools]: https://github.com/Blackmist/hdinsight-tools
  [データを HDInsight にアップロードする方法]: http://azure.microsoft.com/ja-jp/documentation/articles/hdinsight-upload-data/
  [Pregel]: http://people.apache.org/~edwardyoon/documents/pregel.pdf
  [release-1.1 branch]: https://github.com/apache/giraph/tree/release-1.1
  [有向グラフ]: http://en.wikipedia.org/wiki/Directed_graph
  [tiny\_graph.txt drawn as circles with lines of varying distance between]: .\media\hdinsight-giraph\giraph-graph.png
  [Drawing of objects as circles with shortest paths drawn between]: .\media\hdinsight-giraph\giraph-graph-out.png
  [Azure 管理ポータル]: https://manage.windowsazure.com
  [HDInsight で提供されるクラスター バージョンの新機能]: http://azure.microsoft.com/ja-jp/documentation/articles/hdinsight-component-versioning/
  [Hadoop コマンド ライン]: #cmd
  [Pig]: http://azure.microsoft.com/ja-jp/documentation/articles/hdinsight-use-pig/
  [Hive]: http://azure.microsoft.com/ja-jp/documentation/articles/hdinsight-use-hive/
