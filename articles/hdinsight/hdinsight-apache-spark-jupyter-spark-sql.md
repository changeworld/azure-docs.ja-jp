<properties
	pageTitle="HDInsight Linux での Spark クラスターの作成と、Jupyter から Spark SQL を使用した対話型の分析 | Microsoft Azure"
	description="HDInsight に Apache Spark クラスターをすばやく作成し、Jupyter Notebook から Spark SQL を使用して対話型クエリを実行する手順を説明します。"
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


# 概要: Azure HDInsight (Linux) の Apache Spark クラスターの作成と Spark SQL を使用した対話型クエリの実行

HDInsight で Apache Spark クラスターを作成し、Spark クラスターで [Jupyter](https://jupyter.org) Notebook を使用して Spark SQL の対話型クエリを実行する方法について説明します。

   ![HDInsight で Apache Spark を使用します](./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.getstartedflow.png "HDInsight チュートリアルで Apache Spark を使用します手順: ストレージ アカウントを作成し、クラスターを作成して、Spark SQL ステートメントを実行します")

**前提条件:**

- **Azure サブスクリプション**。このチュートリアルを開始する前に、Azure サブスクリプションが必要です。[Azure 無料試用版の取得](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)に関するページを参照してください。

- **Secure Shell (SSH) クライアント**: Linux、Unix、OS X システムには、`ssh` コマンドで SSH クライアントを提供していました。Windows システムの場合は [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) をお勧めします。
    
- **Secure Shell (SSH) キー** (省略可能): クラスターへの接続に使用する SSH アカウントは、パスワードまたはパブリック キーを使用してセキュリティで保護できます。パスワードを使用すると、すぐに始められます。すばやくクラスターを作成し、テスト ジョブをいくつか実行する場合に、このオプションを使用してください。キーのほうが安全ですが、追加の設定が必要です。運用環境のクラスターを作成するとき、この手法を利用することがあります。この記事では、パスワード手法を使用します。HDInsight で SSH キーを作成して使用する手順については、次の記事を参照してください。

	-  Linux コンピューターの場合 - [Linux、Unix、OS X から HDInsight 上の Linux ベースの Hadoop で SSH を使用する](hdinsight-hadoop-linux-use-ssh-unix.md)
    
	-  Windows コンピューターの場合 - [HDInsight の Linux ベースの Hadoop で Windows から SSH を使用する](hdinsight-hadoop-linux-use-ssh-windows.md)


## HDInsight Linux での Spark クラスターの作成

このセクションでは、Spark バージョン 1.5.1 に基づいて HDInsight バージョン 3.3 クラスターを作成します。HDInsight バージョンとその SLA については、「[HDInsight コンポーネントのバージョン](hdinsight-component-versioning.md)」をご覧ください。

>[AZURE.NOTE] この記事の手順では、基本の構成設定を使用して HDInsight に Apache Spark クラスターを作成します。追加ストレージ、Azure Virtual Network、Hive のメタストアなどを使用した、その他のクラスターの構成設定については、[カスタム オプションを使用した HDInsight Spark クラスターの作成](hdinsight-hadoop-provision-linux-clusters.md)に関するページを参照してください。


**Spark クラスターを作成するには**

1. [Azure プレビュー ポータル](https://ms.portal.azure.com/)にサインインします。

2. **[新規]**、**[データ分析]**、**[HDInsight]** の順にクリックします。

    ![Azure プレビュー ポータルでの新しいクラスターの作成](./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.createcluster.1.png "Azure プレビュー ポータルでの新しいクラスターの作成")

3. **[クラスター名]** を入力し、**[クラスターの種類]** で **[Hadoop]** を選択し、**[クラスターのオペレーティング システム]** ドロップダウン メニューから **[Ubuntu]** を選択して、Spark のバージョンを選択します。クラスターを使用できる場合は、クラスター名の横に緑色のチェック マークが表示されます。

	![クラスターの名前と種類の入力](./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.createcluster.2.png "クラスターの名前と種類の入力")

4. 複数のサブスクリプションがある場合は、**[サブスクリプション]** エントリをクリックし、クラスターで使用する Azure サブスクリプションを選択します。

5. **[リソース グループ]** をクリックして既存のリソース グループの一覧を表示し、クラスターを作成する場所を選択します。または、**[新規作成]** をクリックし、新しいリソース グループの名前を入力します。新しいグループ名を使用できる場合は、緑のチェック マークが表示されます。

	> [AZURE.NOTE] このエントリには、既存のリソース グループを使用できる場合は、そのうちの 1 つが既定値として設定されます。

6. **[資格情報]** をクリックし、管理ユーザーのパスワードを入力します。**[SSH ユーザー名]** も入力する必要があります。**[SSH 認証の種類]** については、**[パスワード]** をクリックし、SSH ユーザーのパスワードを指定します。下部にある **[選択]** をクリックして資格情報の構成を保存します。

	![クラスターの資格情報の指定](./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.createcluster.3.png "クラスターの資格情報の指定")

    > [AZURE.NOTE] SSH はコマンドラインで HDInsight クラスターにリモート アクセスするために使用されます。ここで使用するユーザー名とパスワードは、SSH でクラスターに接続するときに使用されます。また、SSH ユーザー名は一意にする必要があります。この名前により、すべての HDInsight クラスター ノードでユーザー アカウントが作成されます。次はクラスターのサービスのために予約されている名前の一部であり、SSH ユーザー名として使用できません。
    >
    > root、hdiuser、storm、hbase、ubuntu、zookeeper、hdfs、yarn、mapred、hbase、hive、oozie、falcon、sqoop、admin、tez、hcat、hdinsight-zookeeper

	HDInsight での SSH の使用方法の詳細については、次の記事をご覧ください。

	* [Linux、Unix、OS X から HDInsight 上の Linux ベースの Hadoop で SSH キーを使用する](hdinsight-hadoop-linux-use-ssh-unix.md)
	* [HDInsight の Linux ベースの Hadoop で Windows から SSH を使用する](hdinsight-hadoop-linux-use-ssh-windows.md)


7. **[データ ソース]** をクリックし、クラスターの既存のデータ ソースを選択するか、新しいデータ ソースを作成します。HDInsight で Hadoop クラスターを作成するときに、Azure Storage アカウントを指定します。Hadoop 分散ファイルシステム (HDFS) と同様、このアカウントの特定の Blob Storage コンテナーが、既定のファイル システムとして設定されます。既定では、HDInsight クラスターは、指定されたストレージ アカウントと同じデータ センターに作成されます。詳細については、「[HDInsight での Azure Blob Storage の使用][hdinsight-storage]」をご覧ください。

	![[データ ソース] ブレード](./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.createcluster.4.png "データ ソース構成の指定")

	現在、HDInsight クラスターのデータ ソースとして Azure Storage アカウントを選択できます。次の説明を参照して、**[データ ソース]** ブレードのエントリを理解してください。

	- **選択方法**: すべてのサブスクリプションのストレージ アカウントを参照できるようにする場合は、**[すべてのサブスクリプションから]** を設定します。既存のストレージ アカウントの **[ストレージ名]** と **[アクセス キー]** を入力する場合は、**[アクセス キー]** を設定します。

	- **ストレージ アカウントの選択/新規作成**: クラスターに関連付ける既存のストレージ アカウントを参照して選択する場合は **[ストレージ アカウントの選択]** をクリックします。新しいストレージ アカウントを作成する場合は **[新規作成]** をクリックします。表示されたフィールドに、ストレージ アカウントの名前を入力します。名前を使用できる場合は、緑色のチェック マークが表示されます。

	- **既定のコンテナーの選択**。これを使用して、クラスターで使用する既定のコンテナーの名前を入力します。任意の名前を入力できますが、コンテナーが特定のクラスターで使用されていることを簡単に認識できるように、クラスターと同じ名前を使用することをお勧めします。

	- **場所**: ストレージ アカウントが存在するリージョン、またはその中にストレージ アカウントが作成されるリージョン。

		> [AZURE.IMPORTANT] 既定のデータ ソースの場所を選択すると、HDInsight クラスターの場所も設定されます。クラスターと既定のデータ ソースは、同じリージョンに存在する必要があります。

	**[選択]** をクリックしてデータ ソースの構成を保存します。

8. **[ノード価格レベル]** をクリックして、このクラスターのために作成されるノードに関する情報を表示します。クラスターで必要な worker ノードの数を設定します。クラスターの推定コストがブレード内に表示されます。

	![[ノード価格レベル] ブレード](./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.createcluster.5.png "クラスター ノード数の指定")

	**[選択]** をクリックして、ノードの価格構成を保存します。

9. **[新しい HDInsight クラスター]** ブレードで、**[スタート画面にピン留めする]** が選択されていることを確認し、**[作成]** をクリックします。これでクラスターが作成され、Azure ポータルのスタート画面にクラスター用のタイルが追加されます。アイコンはクラスターが作成中であることを示し、作成が完了すると、[HDInsight] アイコンを表示するように変化します。

	| 作成中 | 作成の完了時 |
	| ------------------ | --------------------- |
	| ![Creating indicator on startboard](./media/hdinsight-apache-spark-jupyter-spark-sql/provisioning.png) | ![プロビジョニングされたクラスターのタイル](./media/hdinsight-apache-spark-jupyter-spark-sql/provisioned.png) |

	> [AZURE.NOTE] クラスターが作成されるまで、通常は約 15 分かかります。作成プロセスをチェックするには、スタート画面のタイル、またはページの左側にある **[通知]** エントリを使用してください。

10. 作成が完了したら、スタート画面で Spark クラスター用のタイルをクリックして、クラスター ブレードを起動します。



## <a name="jupyter"></a>Jupyter Notebook を使用して Spark SQL クエリを実行する

このセクションでは、Jupyter Notebook を使用して、Spark クラスターに対して Spark SQL クエリを実行します。

1. [Azure プレビュー ポータル](https://portal.azure.com/)のスタート画面で Spark クラスターのタイルをクリックします (スタート画面にピン留めしている場合)。**[すべて参照]** > **[HDInsight クラスター]** でクラスターに移動することもできます。   

2. Spark クラスター ブレードで、**[クイック リンク]** をクリックし、**[クラスター ダッシュボード]** ブレードで **[Jupyter Notebook]** をクリックします。入力を求められたら、クラスターの管理者資格情報を入力します。

	> [AZURE.NOTE] ブラウザーで次の URL を開き、クラスターの Jupyter Notebook にアクセスすることもできます。__CLUSTERNAME__ をクラスターの名前に置き換えます。
	>
	> `https://CLUSTERNAME.azurehdinsight.net/jupyter`

2. 新しい Notebook を作成します。**[新規]** をクリックし、**[Python2]** をクリックします。

	![新しい Jupyter Notebook を作成します](./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.note.jupyter.createnotebook.png "新しい Jupyter Notebook を作成します")

3. Untitled.pynb という名前の新しい Notebook が作成されて開かれます。上部の Notebook 名をクリックし、わかりやすい名前を入力します。

	![Notebook の名前を指定します](./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.note.jupyter.notebook.name.png "Notebook の名前を指定します")

4. 必要なモジュールをインポートし、Spark コンテキストと SQL コンテキストを作成します。次のコード サンプルを空のセルに貼り付けて、**Shift + Enter** キーを押します。

		from pyspark import SparkContext
		from pyspark.sql import SQLContext
		from pyspark.sql.types import *
		
		# Create Spark and SQL contexts
		sc = SparkContext('yarn-client')
		sqlContext = SQLContext(sc)

	Jupyter でジョブを実行するたびに、Web ブラウザー ウィンドウのタイトルに **[(ビジー)]** ステータスと Notebook のタイトルが表示されます。また、右上隅にある **Python 2** というテキストの横に塗りつぶされた円も表示されます。ジョブが完了すると、白抜きの円に変化します。

	 ![Jupyter Notebook ジョブのステータス](./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.jupyter.job.status.png "Jupyter Notebook ジョブのステータス")

4. サンプル データを一時テーブルに読み込みます。HDInsight の Spark クラスターを作成すると、サンプル データ ファイル **hvac.csv** が関連するストレージ アカウントの **\\HdiSamples\\HdiSamples\\SensorSampleData\\hvac** にコピーされます。

	次のコード サンプルを空のセルに貼り付けて、**Shift + Enter** キーを押します。このコード サンプルは、**hvac** という一時テーブルにデータを登録します。

		# Load the data
		hvacText = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
		
		# Create the schema
		hvacSchema = StructType([StructField("date", StringType(), False),StructField("time", StringType(), False),StructField("targettemp", IntegerType(), False),StructField("actualtemp", IntegerType(), False),StructField("buildingID", StringType(), False)])
		
		# Parse the data in hvacText
		hvac = hvacText.map(lambda s: s.split(",")).filter(lambda s: s[0] != "Date").map(lambda s:(str(s[0]), str(s[1]), int(s[2]), int(s[3]), str(s[6]) ))
		
		# Create a data frame
		hvacdf = sqlContext.createDataFrame(hvac,hvacSchema)
		
		# Register the data fram as a table to run queries against
		hvacdf.registerTempTable("hvac")
		
		# Run queries against the table and display the data
		data = sqlContext.sql("select buildingID, (targettemp - actualtemp) as temp_diff, date from hvac where date = "6/1/13"")
		data.show()

5. ジョブが正常に完了すると、次の出力が表示されます。

		+----------+---------+------+
		|buildingID|temp_diff|  date|
		+----------+---------+------+
		|         4|        8|6/1/13|
		|         3|        2|6/1/13|
		|         7|      -10|6/1/13|
		|        12|        3|6/1/13|
		|         7|        9|6/1/13|
		|         7|        5|6/1/13|
		|         3|       11|6/1/13|
		|         8|       -7|6/1/13|
		|        17|       14|6/1/13|
		|        16|       -3|6/1/13|
		|         8|       -8|6/1/13|
		|         1|       -1|6/1/13|
		|        12|       11|6/1/13|
		|         3|       14|6/1/13|
		|         6|       -4|6/1/13|
		|         1|        4|6/1/13|
		|        19|        4|6/1/13|
		|        19|       12|6/1/13|
		|         9|       -9|6/1/13|
		|        15|      -10|6/1/13|
		+----------+---------+------+


6. アプリケーションの実行が完了したら、Notebook をシャットダウンしてリソースを解放する必要があります。そのためには、Notebook の **[ファイル]** メニューの **[閉じて停止]** をクリックします。これにより、Notebook がシャットダウンされ、閉じられます。


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

* [HDInsight の Spark クラスターで Zeppelin Notebook を使用する](hdinsight-apache-spark-use-zeppelin-notebook.md)

* [HDInsight 用の Spark クラスターの Jupyter Notebook で使用可能なカーネル](hdinsight-apache-spark-jupyter-notebook-kernels.md)

### リソースの管理

* [Azure HDInsight での Apache Spark クラスターのリソースの管理](hdinsight-apache-spark-resource-manager.md)

### 既知の問題

* [Azure HDInsight (Linux) の Apache Spark の既知の問題](hdinsight-apache-spark-known-issues.md)


[hdinsight-versions]: ../hdinsight-component-versioning/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-storage]: ../hdinsight-use-blob-storage/

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: ../storage-create-storage-account/

<!---HONumber=AcomDC_0211_2016-->