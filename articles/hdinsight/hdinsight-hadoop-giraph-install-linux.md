<properties
	pageTitle="Linux ベースの HDInsight (Hadoop) に Giraph をインストールして使用する | Microsoft Azure"
	description="スクリプト アクションを使用して Linux ベースの HDInsight クラスターに Giraph をインストールする方法について説明します。スクリプト アクションでは、クラスター構成を変更するか、サービスとユーティリティをインストールすることで、クラスターを作成時にカスタマイズできます。"
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	manager="paulettm"
	editor="cgronlun"
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/17/2016"
	ms.author="larryfr"/>

# HDInsight Hadoop クラスターに Giraph をインストールし、Giraph を使用して大規模なグラフを処理する

**Script Action** を使用してクラスターをカスタマイズして、Azure HDInsight の Hadoop の任意の種類のクラスターに Giraph をインストールできます。

このトピックでは、Script Action を使用して Giraph をインストールする方法を学習します。Giraph をインストールした後は、大規模なグラフを処理する多くの一般的なアプリケーションで Giraph を使用する方法についても説明します。

> [AZURE.NOTE] この記事の情報は、Linux ベースの HDInsight クラスターに固有のものです。Windows ベースのクラスターの操作については、[HDInsight Hadoop クラスターへの Giraph のインストール (Windows)](hdinsight-hadoop-giraph-install.md) に関するページを参照してください。

## <a name="whatis"></a>Giraph とは

[Apache Giraph](http://giraph.apache.org/) は、Hadoop でグラフの処理を実行するために使用でき、Azure HDInsight で使用できます。グラフでは、オブジェクト間の関係 (インターネットのような大規模ネットワークでのルーター間の接続など) や、ソーシャル ネットワークでの人々の関係 (ソーシャル グラフとも呼ばれる) をモデル化します。グラフの処理により、次のようなグラフ内のオブジェクト間の関係について推論できます。

- 現在の関係に基づいて潜在的な友人を識別する
- ネットワーク内の 2 台のコンピューター間の最短ルートを識別する
- Web ページのページ順位を計算する

> [AZURE.WARNING] HDInsight クラスターに用意されているコンポーネントは全面的にサポートされており、これらのコンポーネントに関連する問題の分離と解決については、Microsoft サポートが支援します。
>
> Giraph といったカスタム コンポーネントについては、問題のトラブルシューティングを進めるための支援として、商業的に妥当な範囲のサポートを受けることができます。これにより問題が解決する場合もあれば、オープン ソース テクノロジに関して、深い専門知識が入手できる場所への参加をお願いすることになる場合もあります。たとえば、[HDInsight についての MSDN フォーラム](https://social.msdn.microsoft.com/Forums/azure/ja-JP/home?forum=hdinsight)や [http://stackoverflow.com](http://stackoverflow.com) などの数多くのコミュニティ サイトを利用できます。また、Apache プロジェクトには、[http://apache.org](http://apache.org) に [Hadoop](http://hadoop.apache.org/) などのプロジェクト サイトもあります。

##スクリプトの機能

このスクリプトは、次のアクションを実行します。

* Giraph を `/usr/hdp/current/giraph` にインストールする。
* `/example/jars/giraph-examples.jar` のように、`giraph-examples.jar` ファイルをクラスターの既定のストレージ (WASB) にコピーする。

## <a name="install"></a>スクリプト アクションを使用した Giraph のインストール

HDInsight クラスターに Giraph をインストールするサンプル スクリプトは、次の場所にあります。

    https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh

このセクションでは、Azure ポータルを使用してクラスターを作成する際に、サンプル スクリプトを使用する方法について説明します。

> [AZURE.NOTE] スクリプト アクションは、Azure PowerShell、Azure CLI、HDInsight .NET SDK、または Azure Resource Manager のテンプレートを使用して適用することもできます。既に実行しているクラスターにスクリプト アクションを適用することもできます。詳細については、[スクリプト アクションを使用した HDInsight クラスターのカスタマイズ](hdinsight-hadoop-customize-cluster-linux.md)に関する記事を参照してください。

1. [Linux ベースの HDInsight クラスターの作成](hdinsight-provision-linux-clusters.md#portal)に関するページに記載されている手順を使用して、クラスターの作成を開始します。ただし、作成を完了しないでください。

2. **[オプションの構成]** ブレードで **[スクリプト アクション]** を選択し、以下の情報を指定します。

	* __[名前]__: スクリプト アクションの表示名を入力します。
	* __[スクリプト URI]__: https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh
	* __[ヘッド]__: このオプションをオンにします。
	* __[ワーカー]__: このオプションをオンにします。
	* __[Zookeeper]__: Zookeeper ノードにインストールするには、このオプションをオンにします。
	* __[パラメーター]__: このフィールドは空のままにします。

3. **[スクリプト アクション]** の下部で、**[選択]** を使用して構成を保存します。最後に、**[オプションの構成]** ブレードの下部にある **[選択]** を使用して、オプションの構成情報を保存します。

4. 「[Linux ベースの HDInsight クラスターの作成](hdinsight-hadoop-create-linux-clusters-portal.md)」の説明に従って、クラスターの作成を続行します。

## <a name="usegiraph"></a>HDInsight で Giraph を使用する方法

クラスターの作成が完了したら、次の手順を使用して、Giraph に含まれているサンプル SimpleShortestPathsComputation を実行します。これにより、グラフのオブジェクト間の最短パスを見つけるための基本的な <a href = "http://people.apache.org/~edwardyoon/documents/pregel.pdf">Pregel</a> が実装されます。

1. SSH を使用して HDInsight クラスターに接続します。

		ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

	HDInsight での SSH の使用方法の詳細については、以下の記事を参照してください。

	* [Linux、Unix、OS X から HDInsight 上の Linux ベースの Hadoop で SSH キーを使用する](hdinsight-hadoop-linux-use-ssh-unix.md)

	* [HDInsight の Linux ベースの Hadoop で Windows から SSH を使用する](hdinsight-hadoop-linux-use-ssh-windows.md)

1. 次のコマンドを使用して、__tiny\_graph.txt__ という名前の新しいファイルを作成します。

		nano tiny_graph.txt

	このファイルの内容として以下を使用します。

		[0,0,[[1,1],[3,3]]]
		[1,0,[[0,1],[2,2],[3,1]]]
		[2,0,[[1,2],[4,4]]]
		[3,0,[[0,3],[1,1],[4,4]]]
		[4,0,[[3,4],[2,4]]]

	このデータは、[source\_id, source\_value,[[dest\_id], [edge\_value],...]] の形式を使用して有向グラフ内のオブジェクト間の関係を示しています。各行は、**source\_id** オブジェクトと 1 つ以上の **dest\_id** オブジェクトとの関係を表現しています。**edge\_value** (重み) は、**source\_id** と **dest\_id** の間のつながりの強さまたは距離であると考えられます。

	この値 (重み) を使用し、オブジェクト間の距離に応じて線を引くと、先のデータは次の図のようになります。

	![円で表した tiny\_graph.txt (線はオブジェクト間の距離)](./media/hdinsight-hadoop-giraph-install-linux/giraph-graph.png)

2. ファイルを保存するには、__Ctrl キーを押しながら X キー__を押し、__Y__ キー、__Enter__ キーの順に押してファイル名を確定します。

3. 次のコマンドを使用して、HDInsight クラスターのプライマリ ストレージにデータを格納します。

		hadoop fs -copyFromLocal tiny_graph.txt /example/data/tiny_graph.txt

3. クラスターのヘッド ノードの完全修飾ドメイン名 (FQDN) を取得するには、次を使用します。

        hostname -f
        
4. 次のコマンドを使用して、サンプル SimpleShortestPathsComputation を実行します。__HEADNODE__ は、前の手順から返された FQDN に置き換えます。

		 hadoop jar /usr/hdp/current/giraph/giraph-examples.jar org.apache.giraph.GiraphRunner org.apache.giraph.examples.SimpleShortestPathsComputation -ca mapred.job.tracker=HEADNODE:9010 -vif org.apache.giraph.io.formats.JsonLongDoubleFloatDoubleVertexInputFormat -vip /example/data/tiny_graph.txt -vof org.apache.giraph.io.formats.IdWithValueTextOutputFormat -op /example/output/shortestpaths -w 2

	このコマンドで使用されるパラメーターを次の表に示します。

	| パラメーター | 実行内容 |
	| --------- | ------------ |
	| `jar /usr/hdp/current/giraph/giraph-examples.jar` | サンプルを含む jar ファイル。 |
	| `org.apache.giraph.GiraphRunner` | サンプルを開始するために使用するクラス。 |
	| `org.apache.giraph.examples.SimpleShortestPathsCoputation` | 実行されるサンプル。この場合は、ID 1 とグラフ内の他のすべての ID の間の最短パスを計算します。 |
	| `-ca mapred.job.tracker=HEADNODE:9010` | クラスターのヘッド ノード。 |
	| `-vif org.apache.giraph.io.formats.JsonLongDoubleFloatDoubleVertexInputFromat` | 入力データに使用する入力形式。 |
	| `-vip /example/data/tiny_graph.txt` | 入力データ ファイル。 |
	| `-vof org.apache.giraph.io.formats.IdWithValueTextOutputFormat` | 出力形式。この場合は、ID と値をプレーンテキストとして出力します。 |
	| `-op /example/output/shortestpaths` | 出力場所。 |
	| `-w 2` | 使用する worker の数。この場合は 2 です。 |

	これらのパラメーターと Giraph サンプルで使用されるその他のパラメーターの詳細については、[Giraph のクイックスタート](http://giraph.apache.org/quick_start.html)のページを参照してください。

5. ジョブが完了すると、結果が __wasb:///example/out/shotestpaths__ ディレクトリに格納されます。作成されたファイルの名前の先頭は "__part-m-\_\_"、末尾はファイルの順番を示す数字です。次のコマンドを使用して、出力を表示します。

		hadoop fs -text /example/output/shortestpaths/*

	出力は次のようになります。

		0	1.0
		4	5.0
		2	2.0
		1	0.0
		3	1.0

	SimpleShortestPathComputation サンプルは、オブジェクト ID 1 から開始して他のオブジェクトへの最短パスを見つけるようにハードコーディングされています。したがって、出力は `destination_id distance` として読み取られます。distance はオブジェクト ID 1 とターゲット ID の間を結ぶエッジの値 (重み) です。

	これを視覚化して、ID 1 と他のすべてのオブジェクトの間で最短パスを結ぶことにより、結果を検証できます。ID 1 と ID 4 の最短パスは 5 です。これは、<span style="color:orange">ID 1 と 3</span>、<span style="color:red">ID 3 と 4</span> を結ぶ距離の合計です。

	![オブジェクトを円で表し、最短パスで結んだ図](./media/hdinsight-hadoop-giraph-install-linux/giraph-graph-out.png)


## 次のステップ

- [HDInsight クラスターに Hue をインストールして使用する](hdinsight-hadoop-hue-linux.md)。Hue は、Pig ジョブや Hive ジョブの作成、実行、保存や、HDInsight クラスターの既定のストレージの参照を容易にする Web UI です。

- [HDInsight Hadoop クラスターで R をインストールする](hdinsight-hadoop-r-scripts-linux.md)。HDInsight Hadoop クラスターで R をインストールして使用するためのクラスター カスタマイズの使用法に関する手順が説明されています。R は、統計計算用のオープン ソースの言語および環境です。R は、数百の組み込み統計関数と、関数型プログラミングとオブジェクト指向のプログラミングの特徴を結合した独自のプログラミング言語を提供します。また、広範なグラフィカル機能も提供します。

- [HDInsight クラスターでの Solr のインストール](hdinsight-hadoop-solr-install-linux.md):クラスターのカスタマイズを使用して、HDInsight Hadoop クラスターに Solr をインストールします。Solr は、格納されたデータに対して強力な検索操作を実行することができます。

<!---HONumber=AcomDC_0720_2016-->