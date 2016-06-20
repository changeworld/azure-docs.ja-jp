<properties 
	pageTitle="Linux 上の HDInsight Spark クラスターの Jupyter Notebook で使用可能なカーネル | Microsoft Azure" 
	description="HDInsight Linux 上の Spark クラスターで使用できる追加の Jupyter Notebook カーネルについて説明します。" 
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
	ms.date="06/06/2016" 
	ms.author="nitinme"/>


# HDInsight Linux の Apache Spark クラスターと Jupyter Notebook で使用可能なカーネル

HDInsight (Linux) の Apache Spark クラスターには、アプリケーションのテストに使用できる Jupyter Notebook が含まれています。カーネルは、コードを実行し、解釈するプログラムです。HDInsight の Spark クラスターには、Jupyter Notebook で使用できる 2 つのカーネルが用意されています。次のとおりです。

1. **PySpark** (Python で記述されたアプリケーション用)
2. **Spark** (Scala で記述されたアプリケーション用)

この記事では、これらのカーネルの使い方と、これらを使用する利点について説明します。

**前提条件:**

次のものが必要です。

- Azure サブスクリプション。[Azure 無料試用版の取得](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)に関するページを参照してください。
- HDInsight Linux での Apache Spark クラスター。手順については、[Azure HDInsight での Apache Spark クラスターの作成](hdinsight-apache-spark-jupyter-spark-sql.md)に関するページを参照してください。

## カーネルの使い方 

1. [Azure ポータル](https://portal.azure.com/)のスタート画面で Spark クラスターのタイルをクリックします (スタート画面にピン留めしている場合)。**[すべて参照]** > **[HDInsight クラスター]** でクラスターに移動することもできます。   

2. Spark クラスター ブレードで、**[クイック リンク]** をクリックし、**[クラスター ダッシュボード]** ブレードで **[Jupyter Notebook]** をクリックします。入力を求められたら、クラスターの管理者資格情報を入力します。

	> [AZURE.NOTE] ブラウザーで次の URL を開き、クラスターの Jupyter Notebook にアクセスすることもできます。__CLUSTERNAME__ をクラスターの名前に置き換えます。
	>
	> `https://CLUSTERNAME.azurehdinsight.net/jupyter`

2. 新しいカーネルで、新しい Notebook を作成します。**[新規]** をクリックし、**[Pyspark]** または **[Spark]** をクリックします。Scala アプリケーションには Spark カーネルを使用し、Python アプリケーションには PySpark カーネルを使用する必要があります。

	![新しい Jupyter Notebook を作成します](./media/hdinsight-apache-spark-jupyter-notebook-kernels/jupyter-kernels.png "新しい Jupyter Notebook を作成します")

3. 選択したカーネルで、新しい Notebook が開かれます。

## PySpark または Spark カーネルを使用する理由

新しいカーネルを使用すると、いくつかの利点があります。

1. **コンテキストのプリセット**。Jupyter Notebook で提供される **PySpark** または **Spark** カーネルでは、開発しているアプリケーションの操作を開始する前に、Spark または Hive コンテキストを明示的に設定する必要がありません。これらのカーネルは、既定で利用できます。各コンテキストは次のとおりです。

	* **sc**: Spark コンテキスト用
	* **sqlContext**: Hive コンテキスト用


	そのため、コンテキストを設定するための次のようなステートメントを実行する必要はありません。

		###################################################
		# YOU DO NOT NEED TO RUN THIS WITH THE NEW KERNELS
		###################################################
		sc = SparkContext('yarn-client')
		sqlContext = HiveContext(sc)

	代わりに、事前に設定されたコンテキストをアプリケーションで直接使用できます。
	
2. **セル マジック**。PySpark カーネルには、"マジック"、つまり、`%%` で呼び出すことができる特別なコマンドがいくつか事前定義されています (`%%MAGIC` <args> など)。このマジック コマンドはコード セルの最初の単語にする必要があります。また、コンテンツの複数行に対応できる必要があります。魔法の単語はセルの最初の単語にする必要があります。その前に他の単語を追加すると、それがコメントであっても、エラーを引き起こします。マジックの詳細については、[こちら](http://ipython.readthedocs.org/en/stable/interactive/magics.html)を参照してください。

	次の表は、カーネルで使用できるさまざまなマジックを一覧にしたものです。

	| マジック | 例 | 説明 |
	|-----------|---------------------------------|--------------|
	| help | `%%help` | 利用できるすべてのマジック、その例と説明から構成されるテーブルを生成します。 |
	| info | `%%info` | 現在の Livy エンドポイントのセッション情報を出力します。 |
	| configure | `%%configure -f`<br>`{"executorMemory": "1000M"`,<br>`"executorCores": 4`} | セッションを作成するためのパラメーターを構成します。セッションが既に作成されているとき、セッションが削除され、再作成される場合、強制フラグ (-f) は必須です。有効なパラメーターの一覧については、[Livy の「POST /sessions」の「Request Body (要求本文)」](https://github.com/cloudera/livy#request-body)を参照してください。例の列で示されているように、パラメーターは JSON 文字列として渡し、マジックの後の次の行に置く必要があります。 |
	| sql | `%%sql -o <variable name>`<br> `SHOW TABLES` | sqlContext に対して Hive クエリを実行します。`-o` パラメーターが渡される場合、クエリの結果は、[Pandas](http://pandas.pydata.org/) データフレームとして %%local Python コンテキストで永続化されます。 |
	| local | `%%local`<br>`a=1` | 後続行のすべてのコードがローカルで実行されます。コードは有効な Python コードにする必要があります。 |
	| ログ | `%%logs` | 現在の Livy セッションのログを出力します。 |
	| 削除 | `%%delete -f -s <session number>` | 現在 Livy エンドポイントの特定のセッションを削除します。カーネル自体が開始したセッションを削除することはできないことに注意してください。 |
	| cleanup | `%%cleanup -f` | このノートブックのセッションを含む、現在 Livy エンドポイントのすべてのセッションを削除します。強制フラグ -f は必須です。 |

3. **自動視覚化**。**Pyspark** カーネルは、Hive と SQL のクエリの出力を自動的に視覚化します。表、円グラフ、面積グラフ、棒グラフなど、さまざまな種類の視覚化から選択できます。

## %%sql マジックでサポートされるパラメーター

%%sql マジックでは、クエリの実行時に受け取る出力の種類の制御に使用できる、さまざまなパラメーターがサポートされます。次の表に、出力を示します。

| パラメーター | 例 | 説明 |
|-----------|---------------------------------|--------------|
| -o | `-o <VARIABLE NAME>` | クエリの結果を [Pandas](http://pandas.pydata.org/) データフレームとして %%local Python コンテキストで永続化するには、このパラメーターを使用します。データ フレーム変数の名前は、指定した変数の名前です。 |
| パラメーター | `-q` | セルの視覚化をオフにするには、これを使用します。セルのコンテンツを自動的に視覚化せず、単にデータ フレームとしてキャプチャする場合は、`-q -o <VARIABLE>` を使用します。(たとえば、`CREATE TABLE` ステートメントのような、副次的作用のある SQL クエリを実行するために) 結果をキャプチャせずに、視覚化をオフにする必要がある場合、`-o` 引数を指定せずに `-q` を使用します。 |
| -m | `-m <METHOD>` | ここで **METHOD** は **take** または **sample** です (既定値は **take**)。メソッドが **take** の場合、カーネルによって、MAXROWS (この表で後述) で指定された結果のデータ セットの先頭から要素が取得されます。メソッドが **sample** の場合、カーネルによって、この表の次に説明する `-r` パラメーターに従って、データ セットの要素がランダムにサンプリングされます。 |
| -r | `-r <FRACTION>` | ここで **FRACTION** は、0.0 ～ 1.0 の浮動小数点数です。SQL クエリのサンプル メソッドが `sample` の場合、カーネルによって、結果セットの要素の指定された比率がランダムにサンプリングされます。たとえば、引数 `-m sample -r 0.01` を使用して SQL クエリを実行した場合、結果の行の 1% がランダムにサンプリングされます。 |
| -n | `-n <MAXROWS>` | **MAXROWS** は整数値です。カーネルによって、出力行の数が **MAXROWS** に制限されます。**MAXROWS** が **-1** など、負の数の場合は、結果セット内の行数は制限されません。 |

**例:**

	%%sql -q -m sample -r 0.1 -n 500 -o query2 
	SELECT * FROM hivesampletable

上記のステートメントによって、次のことが行われます。

* **hivesampletable** からすべてのレコードを選択します。
* -q を使用しているため、自動視覚化をオフにします。
* `-m sample -r 0.1 -n 500` を使用しているため、hivesampletable 内の行の 10% がランダムにサンプリングされ、結果セットのサイズが 500 行に制限されます。
* 最後に、`-o query2` を使用しているため、**query2** という名前のデータフレームにも、その出力が保存されます。
	

## 新しいカーネルを使用する場合の考慮事項

使用するカーネルが PySpark と Spark のどちらであっても、Notebook を実行したままにしておくと、クラスターのリソースが消費されます。これらのカーネルでは、コンテキストがプリセットされているため、Notebook を終了するだけではコンテキストは強制終了されません。そのため、クラスターのリソースは消費され続けます。PySpark および Spark カーネルでは、Notebook の **[ファイル]** メニューで **[閉じて停止]** オプションを使用することをお勧めします。これにより、コンテキストが強制終了され、Notebook が終了されます。


## いくつかの例

Jupyter Notebook を開くと、ルート レベルで利用可能な 2 つのフォルダーが表示されます。

* **PySpark** フォルダーには、新しい **Python** カーネルを使用するサンプル Notebook があります。
* **Scala** フォルダーには、新しい **Spark** カーネルを使用するサンプル Notebook があります。

**PySpark** または **Spark** フォルダーから **00 - [READ ME FIRST] Spark Magic Kernel Features** Notebook を開くと、利用できるさまざまなマジックを確認できます。この 2 つのフォルダーにはサンプル ノードブックが他にもあります。Jupyter ノードブックと HDInsight Spark クラスターを利用したさまざまなシナリオの実現方法について学習できます。

## Notebook の格納場所

Jupyter Notebook は、クラスターに関連付けられたストレージ アカウントの **/HdiNotebooks** フォルダーに保存されます。Notebook、テキスト ファイル、および Jupyter 内から作成したフォルダーには、WASB からアクセスできます。たとえば、Jupyter を使用してフォルダー **myfolder** と Notebook **myfolder/mynotebook.ipynb** を作成した場合、`wasb:///HdiNotebooks/myfolder/mynotebook.ipynb` でその Notebook にアクセスできます。逆の場合も同様です。つまり、Notebook を自分のストレージ アカウントの `/HdiNotebooks/mynotebook1.ipynb` に直接アップロードした場合、Jupyter からもその Notebook を表示することができます。Notebook は、クラスターが削除された後でも、ストレージ アカウントに保持されます。

Notebook がストレージ アカウントに保存される方法は、HDFS と互換性があります。そのため、クラスターに SSH で接続すると、次のようなファイル管理コマンドを使用できます。

	hdfs dfs -ls /HdiNotebooks             				  # List everything at the root directory – everything in this directory is visible to Jupyter from the home page
	hdfs dfs –copyToLocal /HdiNotebooks    				# Download the contents of the HdiNotebooks folder
	hdfs dfs –copyFromLocal example.ipynb /HdiNotebooks   # Upload a notebook example.ipynb to the root folder so it’s visible from Jupyter


クラスターのストレージ アカウントへのアクセスに問題がある場合のために、Notebook はヘッド ノード `/var/lib/jupyter` にも保存されます。

## サポートされているブラウザー
HDInsight の Spark クラスターに対して実行される Jupyter Notebook は、Google Chrome でのみサポートされます。

## フィードバック

新しいカーネルは進化の過程にあり、時間の経過と共に成熟するでしょう。カーネルが改良されるにつれて、API も変更される可能性があります。これらの新しいカーネルに関するフィードバックを、ぜひお寄せください。これらのカーネルの最終リリースの設計に、大いに役立ちます。ご意見やフィードバックは、この記事の下部にある**コメント**のセクションからお寄せください。


## <a name="seealso"></a>関連項目


* [概要: Azure HDInsight での Apache Spark](hdinsight-apache-spark-overview.md)

### シナリオ

* [Spark と BI: HDInsight と BI ツールで Spark を使用した対話型データ分析の実行](hdinsight-apache-spark-use-bi-tools.md)

* [Spark と Machine Learning: HDInsight で Spark を使用して HVAC データを基に建物の温度を分析する](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

* [Spark と Machine Learning: HDInsight で Spark を使用して食品の検査結果を予測する](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

* [Spark ストリーミング: リアルタイム ストリーミング アプリケーションを作成するための HDInsight での Spark の使用](hdinsight-apache-spark-eventhub-streaming.md)

* [Website log analysis using Spark in HDInsight (HDInsight での Spark を使用した Web サイト ログ分析)](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### アプリケーションの作成と実行

* [Scala を使用してスタンドアロン アプリケーションを作成する](hdinsight-apache-spark-create-standalone-application.md)

* [Livy を使用して Spark クラスターでジョブをリモートで実行する](hdinsight-apache-spark-livy-rest-interface.md)

### ツールと拡張機能

* [Use HDInsight Tools Plugin for IntelliJ IDEA to create and submit Spark Scala applicatons (Linux)](hdinsight-apache-spark-intellij-tool-plugin.md)

* [IntelliJ IDEA 用の HDInsight Tools プラグインを使用して Spark アプリケーションをリモートでデバッグする](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)

* [HDInsight の Spark クラスターで Zeppelin Notebook を使用する](hdinsight-apache-spark-use-zeppelin-notebook.md)

* [Jupyter Notebook で外部のパッケージを使用する](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)

* [Jupyter をコンピューターにインストールして HDInsight Spark クラスターに接続する](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### リソースの管理

* [Azure HDInsight での Apache Spark クラスターのリソースの管理](hdinsight-apache-spark-resource-manager.md)

* [HDInsight の Apache Spark クラスターで実行されるジョブの追跡とデバッグ](hdinsight-apache-spark-job-debugging.md)

<!---HONumber=AcomDC_0608_2016-->