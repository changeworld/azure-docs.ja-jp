<properties
	pageTitle="Azure HDInsight での Spark クラスターの作成と、Zeppelin および Jupyter から Spark SQL を使用した対話型の分析 | Microsoft Azure"
	description="HDInsight に Apache Spark クラスターをすばやく作成し、Zeppelin および Jupyter Notebook から Spark SQL を使用して対話型クエリを実行する手順を説明します。"
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
	ms.date="12/22/2015"
	ms.author="nitinme"/>


# 概要: Azure HDInsight での Apache Spark の作成と Spark SQL を使用した対話型クエリの実行

> [AZURE.NOTE] HDInsight は Linux 上に Spark クラスターを提供するようになりました。HDInsight Linux 上で Spark クラスターを開始する方法については、「[概要: Azure HDInsight での Apache Spark の作成 (Linux) ](hdinsight-apache-spark-jupyter-spark-sql.md)」を参照してください。

簡易作成オプションを使用して HDInsight に Apache Spark クラスターを作成した後、Web ベースの [Zeppelin](https://zeppelin.incubator.apache.org) と [Jupyter](https://jupyter.org) Notebook を使用して Spark クラスターに対して Spark SQL 対話型クエリを実行する方法を説明します。


   ![HDInsight で Apache Spark を使用します](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql/hdispark.getstartedflow.png "HDInsight チュートリアルで Apache Spark を使用します手順: ストレージ アカウントを作成し、クラスターを作成して、Spark SQL ステートメントを実行します")

**前提条件:**

このチュートリアルを開始する前に、Azure サブスクリプションが必要です。[Azure 無料試用版の取得](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)に関するページを参照してください。


## HDInsight Spark クラスターを作成する

このセクションでは、Spark バージョン 1.3.1 に基づいて HDInsight バージョン 3.2 クラスターを作成します。HDInsight バージョンとその SLA については、「[HDInsight コンポーネントのバージョン](hdinsight-component-versioning.md)」をご覧ください。

>[AZURE.NOTE] この記事の手順では、基本の構成設定を使用して HDInsight に Apache Spark クラスターを作成します。その他のクラスター構成設定 (追加ストレージ、Azure 仮想ネットワーク、Hive 用のメタストアの使用など) については、[カスタム オプションを使用した HDInsight クラスターの作成](hdinsight-apache-spark-provision-clusters.md)に関するページを参照してください。


**Spark クラスターを作成するには**

1. [Azure ポータル](https://ms.portal.azure.com/)にサインインします。

2. **[新規]**、**[データ分析]**、**[HDInsight]** の順にクリックします。

    ![Azure ポータルでの新しいクラスターの作成](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql/hdispark.createcluster.1.png "Azure ポータルでの新しいクラスターの作成")

3. **[クラスター名]** を入力し、**[クラスターの種類]** で **[Spark]** を選択し、**[クラスターのオペレーティング システム]** ドロップダウン メニューから **[Windows Server 2012 R2 Datacenter]** を選択します。クラスターを使用できる場合は、クラスター名の横に緑色のチェック マークが表示されます。

	![クラスターの名前と種類の入力](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql/hdispark.createcluster.2.png "クラスターの名前と種類の入力")

4. 複数のサブスクリプションがある場合は、**[サブスクリプション]** エントリをクリックし、クラスターで使用する Azure サブスクリプションを選択します。

5. **[リソース グループ]** をクリックして既存のリソース グループの一覧を表示し、クラスターを作成する場所を選択します。または、**[新規作成]** をクリックし、新しいリソース グループの名前を入力します。新しいグループ名を使用できる場合は、緑のチェック マークが表示されます。

	> [AZURE.NOTE] このエントリには、既存のリソース グループを使用できる場合は、そのうちの 1 つが既定値として設定されます。

6. **[資格情報]** をクリックし、**[クラスターのユーザー名]** と **[クラスターのログイン パスワード]** を入力します。クラスター ノードでリモート デスクトップを有効にする場合は、**[リモート デスクトップを有効にする]** で **[はい]** をクリックし、必要な値を指定します。このチュートリアルではリモート デスクトップは必要ないため、この手順はスキップできます下部にある **[選択]** をクリックして資格情報の構成を保存します。

	![クラスターの資格情報の指定](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql/hdispark.createcluster.3.png "クラスターの資格情報の指定")

7. **[データ ソース]** をクリックし、クラスターの既存のデータ ソースを選択するか、新しいデータ ソースを作成します。HDInsight で Hadoop クラスターを作成するときに、Azure Storage アカウントを指定します。Hadoop 分散ファイルシステム (HDFS) と同様、このアカウントの特定の Blob Storage コンテナーが、既定のファイル システムとして設定されます。既定では、HDInsight クラスターは、指定されたストレージ アカウントと同じデータ センターに作成されます。詳細については、「[HDInsight での Azure Blob Storage の使用][hdinsight-storage]」をご覧ください。

	![[データ ソース] ブレード](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql/hdispark.createcluster.4.png "データ ソース構成の指定")

	現在、HDInsight クラスターのデータ ソースとして Azure Storage アカウントを選択できます。次の説明を参照して、**[データ ソース]** ブレードのエントリを理解してください。

	- **選択方法**: すべてのサブスクリプションのストレージ アカウントを参照できるようにする場合は、**[すべてのサブスクリプションから]** を設定します。既存のストレージ アカウントの **[ストレージ名]** と **[アクセス キー]** を入力する場合は、**[アクセス キー]** を設定します。

	- **ストレージ アカウントの選択/新規作成**: クラスターに関連付ける既存のストレージ アカウントを参照して選択する場合は **[ストレージ アカウントの選択]** をクリックします。新しいストレージ アカウントを作成する場合は **[新規作成]** をクリックします。表示されたフィールドに、ストレージ アカウントの名前を入力します。名前を使用できる場合は、緑色のチェック マークが表示されます。

	- **既定のコンテナーの選択**。これを使用して、クラスターで使用する既定のコンテナーの名前を入力します。任意の名前を入力できますが、コンテナーが特定のクラスターで使用されていることを簡単に認識できるように、クラスターと同じ名前を使用することをお勧めします。

	- **場所**: ストレージ アカウントが存在するリージョン、またはその中にストレージ アカウントが作成されるリージョン。

		> [AZURE.IMPORTANT] 既定のデータ ソースの場所を選択すると、HDInsight クラスターの場所も設定されます。クラスターと既定のデータ ソースは、同じリージョンに存在する必要があります。

	**[選択]** をクリックしてデータ ソースの構成を保存します。

8. **[ノード価格レベル]** をクリックして、このクラスターのために作成されるノードに関する情報を表示します。クラスターで必要な worker ノードの数を設定します。クラスターの推定コストがブレード内に表示されます。

	![[ノード価格レベル] ブレード](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql/hdispark.createcluster.5.png "クラスター ノード数の指定")

	**[選択]** をクリックして、ノードの価格構成を保存します。

9. **[新しい HDInsight クラスター]** ブレードで、**[スタート画面にピン留めする]** が選択されていることを確認し、**[作成]** をクリックします。これでクラスターが作成され、Azure ポータルのスタート画面にクラスター用のタイルが追加されます。アイコンはクラスターが作成中であることを示し、作成が完了すると、[HDInsight] アイコンを表示するように変化します。

	| 作成中 | 作成の完了時 |
	| ------------------ | --------------------- |
	| ![スタート画面の作成中インジケーター](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql/provisioning.png) | ![作成されたクラスター タイル](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql/provisioned.png) |

	> [AZURE.NOTE] クラスターが作成されるまで、通常は約 15 分かかります。作成プロセスをチェックするには、スタート画面のタイル、またはページの左側にある **[通知]** エントリを使用してください。

10. 作成が完了したら、スタート画面で Spark クラスター用のタイルをクリックして、クラスター ブレードを起動します。


## <a name="zeppelin"></a>Zeppelin Notebook を使用して対話型 Spark SQL クエリを実行する

クラスターを作成した後、Web ベースの Zeppelin Notebook を使用して、Spark HDInsight クラスターに対して Spark SQL の対話型クエリを実行できます。このセクションでは、クラスターにおいて既定で使用できるサンプル データ ファイル (hvac.csv) を使用していくつかの対話型 Spark SQL クエリを実行します。

>[AZURE.NOTE] 以下の説明に従って作成した Notebook も、クラスターにおいて既定で利用できます。Zeppelin を起動した後、**Zeppelin HVAC tutorial** という名前でこの Notebook を検索します。

1. [Azure ポータル](https://portal.azure.com/)のスタート画面で Spark クラスターのタイルをクリックします (スタート画面にピン留めしている場合)。**[すべて参照]** > **[HDInsight クラスター]** でクラスターに移動することもできます。   

2. Spark クラスター ブレードで、**[クイック リンク]** をクリックし、**[クラスター ダッシュボード]** ブレードで **[Zeppelin Notebook]** をクリックします。入力を求められたら、クラスターの管理者資格情報を入力します。

	> [AZURE.NOTE] ブラウザーで次の URL を開き、クラスターの Zeppelin Notebook にアクセスすることもできます。__CLUSTERNAME__ をクラスターの名前に置き換えます。
	>
	> `https://CLUSTERNAME.azurehdinsight.net/zeppelin`

2. 新しい Notebook を作成します。ヘッダー ウィンドウで **[Notebook]** をクリックし、**[Note の新規作成]** をクリックします。

	![新しい Zeppelin Notebook を作成します](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql/hdispark.createnewnote.png "新しい Zeppelin Notebook を作成します")

	同じページの **[Notebook]** 見出しの下に、**Note XXXXXXXXX** で名前が始まる新しい Notebook が表示されます。新しい Notebook をクリックします。

3. 新しい Notebook の Web ページで、見出しをクリックし、必要に応じて Notebook の名前を変更します。Enter キーを押して名前の変更を保存します。また、Notebook のヘッダーの右上隅に **[接続]** というステータスが表示されることを確認します。

	![Zeppelin Notebook のステータス](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql/hdispark.newnote.connected.png "Zeppelin Notebook のステータス")

4. サンプル データを一時テーブルに読み込みます。HDInsight の Spark クラスターを作成すると、サンプル データ ファイル **hvac.csv** が、関連するストレージ アカウントの **\\HdiSamples\\SensorSampleData\\hvac** にコピーされます。

	新しい Notebook に既定で作成される空の段落に、次のコードを貼り付けます。

		// Create an RDD using the default Spark context, sc
		val hvacText = sc.textFile("wasb:///HdiSamples/SensorSampleData/hvac/HVAC.csv")

		// Define a schema
		case class Hvac(date: String, time: String, targettemp: Integer, actualtemp: Integer, buildingID: String)

		// Map the values in the .csv file to the schema
		val hvac = hvacText.map(s => s.split(",")).filter(s => s(0) != "Date").map(
    		s => Hvac(s(0),
            		s(1),
            		s(2).toInt,
            		s(3).toInt,
            		s(6)
        	)
		).toDF()

		// Register as a temporary table called "hvac"
		hvac.registerTempTable("hvac")

	キーボードで **Shift + Enter** キーを押すか、段落の **[プレイ]** ボタンをクリックして、コードを実行します。段落の右上隅にあるステータスが、[準備完了]、[保留中]、[実行中]、[完了] の順に進行します。出力が同じ段落の下に表示されます。スクリーンショットは次のようになります。

	![生データから一時テーブルを作成します](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql/hdispark.note.loaddataintotable.png "生データから一時テーブルを作成します")

	各段落にタイトルを指定することもできます。右上隅の **[設定]** アイコンをクリックし、**[タイトルの表示]** をクリックします。

5. **hvac** テーブルに対して Spark SQL ステートメントを実行できます。次のクエリを新しい段落に貼り付けます。このクエリは、ビル ID と、特定の日の各ビルの目標温度と実温度の差を取得します。**Shift + Enter** キーを押します。

		%sql
		select buildingID, (targettemp - actualtemp) as temp_diff, date
		from hvac
		where date = "6/1/13"

	先頭にある **%sql** ステートメントは、Spark SQL インタープリターを使用するように Notebook に指示します。定義済みのインタープリターは、Notebook ヘッダーの **[インタープリター]** タブで見ることができます。

	次のスクリーンショットでは出力を示します。

	![Notebook を使用して Spark SQL ステートメントを実行します](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql/hdispark.note.sparksqlquery1.png "Notebook を使用して Spark SQL ステートメントを実行します")

	表示オプション (四角で囲ってある部分) をクリックして、同じ出力の異なる表現に切り替えることができます。**[設定]** をクリックして、出力のキーと値の構成を選択します。上記の画面キャプチャでは、**buildingID** をキーとして使用し、**temp\_diff** の平均を値として使用しています。

6. クエリの変数を使用して Spark SQL ステートメントを実行することもできます。次のコード サンプルでは、クエリで変数 **Temp** と照会できる値を定義する方法を示します。初めてクエリを実行すると、変数に指定した値がドロップダウンに自動的に設定されます。

		%sql
		select buildingID, date, targettemp, (targettemp - actualtemp) as temp_diff
		from hvac
		where targettemp > "${Temp = 65,65|75|85}"

	このコード サンプルを新しい段落に貼り付けて、**Shift + Enter** キーを押します。次のスクリーンショットでは出力を示します。

	![Notebook を使用して Spark SQL ステートメントを実行します](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql/hdispark.note.sparksqlquery2.png "Notebook を使用して Spark SQL ステートメントを実行します")

	後続のクエリでは、ドロップダウンから新しい値を選択し、クエリを再実行できます。**[設定]** をクリックして、出力のキーと値の構成を選択します。上の画面キャプチャでは、**buildingID** をキーとして、**temp\_diff** の平均を値として、**targettemp** をグループとして使用しています。

7. Spark SQL インタープリターを再起動して、アプリケーションを終了します。上部の **[インタープリター]** タブをクリックし、Spark インタープリターの **[再起動]** をクリックします。

	![Zeppelin インタープリターを再起動します](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql/hdispark.zeppelin.restart.interpreter.png "Zeppelin インタープリターを再起動します")

## <a name="jupyter"></a>Jupyter Notebook を使用して Spark SQL クエリを実行する

このセクションでは、Jupyter Notebook を使用して、Spark クラスターに対して Spark SQL クエリを実行します。

>[AZURE.NOTE] 以下の説明に従って作成した Notebook も、クラスターにおいて既定で利用できます。Jupyter を起動した後、**HVACTutorial.ipynb** という名前でこの Notebook を検索します。

1. [Azure ポータル](https://portal.azure.com/)のスタート画面で Spark クラスターのタイルをクリックします (スタート画面にピン留めしている場合)。**[すべて参照]** > **[HDInsight クラスター]** でクラスターに移動することもできます。   

2. Spark クラスター ブレードで、**[クイック リンク]** をクリックし、**[クラスター ダッシュボード]** ブレードで **[Jupyter Notebook]** をクリックします。入力を求められたら、クラスターの管理者資格情報を入力します。

	> [AZURE.NOTE] ブラウザーで次の URL を開き、クラスターの Jupyter Notebook にアクセスすることもできます。__CLUSTERNAME__ をクラスターの名前に置き換えます。
	>
	> `https://CLUSTERNAME.azurehdinsight.net/jupyter`

2. 新しい Notebook を作成します。**[新規]** をクリックし、**[Python2]** をクリックします。

	![新しい Jupyter Notebook を作成します](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql/hdispark.note.jupyter.createnotebook.png "新しい Jupyter Notebook を作成します")

3. Untitled.pynb という名前の新しい Notebook が作成されて開かれます。上部の Notebook 名をクリックし、わかりやすい名前を入力します。

	![Notebook の名前を指定します](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql/hdispark.note.jupyter.notebook.name.png "Notebook の名前を指定します")

4. 必要なモジュールをインポートし、Spark コンテキストと SQL コンテキストを作成します。次のコード サンプルを空のセルに貼り付けて、**Shift + Enter** キーを押します。

		from pyspark import SparkContext
		from pyspark.sql import SQLContext
		from pyspark.sql.types import *

		# Create Spark and SQL contexts
		sc = SparkContext('spark://headnodehost:7077', 'pyspark')
		sqlContext = SQLContext(sc)

	Jupyter でジョブを実行するたびに、Web ブラウザー ウィンドウのタイトルに **[(ビジー)]** ステータスと Notebook のタイトルが表示されます。また、右上隅にある **Python 2** というテキストの横に塗りつぶされた円も表示されます。ジョブが完了すると、白抜きの円に変化します。

	 ![Jupyter Notebook ジョブのステータス](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql/hdispark.jupyter.job.status.png "Jupyter Notebook ジョブのステータス")

4. サンプル データを一時テーブルに読み込みます。HDInsight の Spark クラスターを作成すると、サンプル データ ファイル **hvac.csv** が、関連するストレージ アカウントの **\\HdiSamples\\SensorSampleData\\hvac** にコピーされます。

	次のコード サンプルを空のセルに貼り付けて、**Shift + Enter** キーを押します。このコード サンプルは、**hvac** という一時テーブルにデータを登録します。

		# Load the data
		hvacText = sc.textFile("wasb:///HdiSamples/SensorSampleData/hvac/HVAC.csv")

		# Create the schema
		hvacSchema = StructType([StructField("date", StringType(), False),StructField("time", StringType(), False),StructField("targettemp", IntegerType(), False),StructField("actualtemp", IntegerType(), False),StructField("buildingID", StringType(), False)])

		# Parse the data in hvacText
		hvac = hvacText.map(lambda s: s.split(",")).filter(lambda s: s[0] != "Date").map(lambda s:(str(s[0]), str(s[1]), int(s[2]), int(s[3]), str(s[6]) ))

		# Create a data frame
		hvacdf = sqlContext.createDataFrame(hvac,hvacSchema)

		# Register the data fram as a table to run queries against
		hvacdf.registerAsTable("hvac")

		# Run queries against the table and display the data
		data = sqlContext.sql("select buildingID, (targettemp - actualtemp) as temp_diff, date from hvac where date = "6/1/13"")
		data.show()

5. ジョブが正常に完了すると、次の出力が表示されます。

		buildingID temp_diff date  
		4          8         6/1/13
		3          2         6/1/13
		7          -10       6/1/13
		12         3         6/1/13
		7          9         6/1/13
		7          5         6/1/13
		3          11        6/1/13
		8          -7        6/1/13
		17         14        6/1/13
		16         -3        6/1/13
		8          -8        6/1/13
		1          -1        6/1/13
		12         11        6/1/13
		3          14        6/1/13
		6          -4        6/1/13
		1          4         6/1/13
		19         4         6/1/13
		19         12        6/1/13
		9          -9        6/1/13
		15         -10       6/1/13

6. カーネルを再起動してアプリケーションを終了します。上部のメニュー バーから、**[カーネル]** をクリックし、**[再起動]** をクリックして、プロンプトで再び **[再起動]** をクリックします。

	![Jupyter カーネルを再起動します](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql/hdispark.jupyter.restart.kernel.png "Jupyter カーネルを再起動します")


## <a name="seealso"></a>関連項目


* [概要: Azure HDInsight での Apache Spark](hdinsight-apache-spark-overview.md)
* [HDInsight クラスターでの Spark の作成](hdinsight-apache-spark-provision-clusters.md)
* [HDInsight と BI ツールで Spark を使用した対話型データ分析の実行](hdinsight-apache-spark-use-bi-tools.md)
* [Machine Learning アプリケーションを作成するための HDInsight での Spark の使用](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [リアルタイム ストリーミング アプリケーションを作成するための HDInsight での Spark の使用](hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming.md)
* [Azure HDInsight での Apache Spark クラスターのリソースの管理](hdinsight-apache-spark-resource-manager.md)


[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: storage-create-storage-account.md

<!---HONumber=AcomDC_0218_2016-->