<properties 
	pageTitle="HDInsight の Apache Spark での BI ツールの使用 | Microsoft Azure" 
	description="Apache Spark で Notebook を使用して生データのスキーマを作成し、Hive テーブルとして保存した後、BI ツールを Hive テーブルに使用してデータを分析する手順を説明します" 
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
	ms.date="04/08/2016" 
	ms.author="nitinme"/>


# Azure HDInsight での BI ツールと Apache Spark の使用 (Linux)

Azure HDInsight の Apache Spark を使用して以下のことを行う方法を説明します。

* 生のサンプル データを取得し、Hive テーブルとして保存します
* Power BI や Tableau などの BI ツールを使用して、データを分析および視覚化します

> [AZURE.TIP] このチュートリアルは、HDInsight で作成する Spark (Linux) クラスター上の Jupyter Notebook としても利用できます。Notebook エクスペリエンスにより、Notebook 自体から Python のスニペットを実行することができます。Notebook からチュートリアルを実行するには、Spark クラスターを作成し、Jupyter Notebook (`https://CLUSTERNAME.azurehdinsight.net/jupyter`) を起動し、**Python** フォルダーにある**Use BI tools with Apache Spark on HDInsight.ipynb (HDInsight.ipynb に対して BI ツールと Apache Spark を使用する)** Notebook を実行します。

**前提条件:**

次のものが必要です。

- Azure サブスクリプション。[Azure 無料試用版の取得](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)に関するページを参照してください。
- HDInsight Linux での Apache Spark クラスター。手順については、「[HDInsight での Apache Spark クラスターの作成](hdinsight-apache-spark-jupyter-spark-sql.md)」を参照してください。
- Microsoft Spark ODBC ドライバー (HDInsight の Spark で Tableau を使用するために必要) がインストールされたコンピューター。ドライバーは[ここ](http://go.microsoft.com/fwlink/?LinkId=616229)からインストールできます。
- [Power BI](http://www.powerbi.com/) や [Tableau Desktop](http://www.tableau.com/products/desktop) などの BI ツール。Power BI の無料プレビュー サブスクリプションは [http://www.powerbi.com/](http://www.powerbi.com/) から入手できます。

##<a name="hivetable"></a>生データを Hive テーブルとして保存する

このセクションでは、HDInsight の Apache Spark クラスターと関連付けられた [Jupyter](https://jupyter.org) Notebook を使用して、生のサンプル データを処理して Hive テーブルとして保存するジョブを実行します。サンプル データは、すべてのクラスターにおいて既定で使用できる .csv ファイル (hvac.csv) です。

データを Hive テーブルとして保存した後、次のセクションでは、Power BI や Tableau などの BI ツールを使用して Hive テーブルに接続します。

1. [Azure ポータル](https://portal.azure.com/)のスタート画面で Spark クラスターのタイルをクリックします (スタート画面にピン留めしている場合)。**[すべて参照]** > **[HDInsight クラスター]** でクラスターに移動することもできます。   

2. Spark クラスター ブレードで、**[クイック リンク]** をクリックし、**[クラスター ダッシュボード]** ブレードで **[Jupyter Notebook]** をクリックします。入力を求められたら、クラスターの管理者資格情報を入力します。

	> [AZURE.NOTE] ブラウザーで次の URL を開き、クラスターの Jupyter Notebook にアクセスすることもできます。__CLUSTERNAME__ をクラスターの名前に置き換えます。
	>
	> `https://CLUSTERNAME.azurehdinsight.net/jupyter`

2. 新しい Notebook を作成します。**[新規]** をクリックし、**[PySpark]** をクリックします。

	![新しい Jupyter Notebook を作成します](./media/hdinsight-apache-spark-use-bi-tools/hdispark.note.jupyter.createnotebook.png "新しい Jupyter Notebook を作成します")

3. Untitled.pynb という名前の新しい Notebook が作成されて開かれます。上部の Notebook 名をクリックし、わかりやすい名前を入力します。

	![Notebook の名前を指定します](./media/hdinsight-apache-spark-use-bi-tools/hdispark.note.jupyter.notebook.name.png "Notebook の名前を指定します")

4. PySpark カーネルを使用して Notebook を作成したため、コンテキストを明示的に作成する必要はありません。最初のコード セルを実行すると、Spark および Hive コンテキストが自動的に作成されます。このシナリオに必要な種類をインポートすることから始めることができます。これを行うには、セルにカーソルを置き、**SHIFT + ENTER** キーを押します。

		from pyspark.sql import *
		
	
5. サンプル データを一時テーブルに読み込みます。HDInsight の Spark クラスターを作成すると、サンプル データ ファイル **hvac.csv** が関連するストレージ アカウントの **\\HdiSamples\\HdiSamples\\SensorSampleData\\hvac** にコピーされます。

	次のスニペットを空のセルに貼り付けて、**Shift + Enter** キーを押します。このスニペットは、**hvac** という Hive テーブルにデータを登録します。


		# Create an RDD from sample data
		hvacText = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
		
		# Create a schema for our data
		Entry = Row('Date', 'Time', 'TargetTemp', 'ActualTemp', 'BuildingID')
		
		# Parse the data and create a schema
		hvacParts = hvacText.map(lambda s: s.split(',')).filter(lambda s: s[0] != 'Date')
		hvac = hvacParts.map(lambda p: Entry(str(p[0]), str(p[1]), int(p[2]), int(p[3]), int(p[6])))
		
		# Infer the schema and create a table       
		hvacTable = hiveContext.createDataFrame(hvac)
		hvacTable.registerTempTable('hvactemptable')
		dfw = DataFrameWriter(hvacTable)
		dfw.saveAsTable('hvac')

5. テーブルが正常に作成されたことを確認します。`%%sql` マジックを使用して、Hive クエリを直接実行できます。`%%sql` マジックと、PySpark カーネルで使用できるその他のマジックの詳細については、「[HDInsight (Linux) の Spark クラスターと Jupyter Notebook で使用可能なカーネル](hdinsight-apache-spark-jupyter-notebook-kernels.md#why-should-i-use-the-new-kernels)」をご覧ください。

		%%sql
		SHOW TABLES

	出力は次のようになります。

		+-----------+---------------+
		|isTemporary|tableName		| 
		+-----------+---------------+
		|       true|hvactemptable  |
		|      false|hivesampletable|
		|      false|hvac			|
		+-----------+---------------+


	**isTemporary** 列が false の Hive テーブルだけがメタストアに格納されて、BI ツールからアクセスできるようになります。このチュートリアルでは、作成したばかりの **hvac** テーブルに接続します。

6. テーブルに目的のデータが含まれることを確認します。Notebook の空のセルに次のスニペットをコピーして、**Shift + Enter** キーを押します。

		%%sql
		SELECT * FROM hvac LIMIT 10
	
7. ここで、リソースを解放するために Notebook をシャットダウンできます。そのためには、Notebook の **[ファイル]** メニューの **[閉じて停止]** をクリックします。これにより、Notebook がシャットダウンされ、閉じられます。

##<a name="powerbi"></a>Power BI を使用して Hive テーブル内のデータを分析する

Hive テーブルとしてデータを保存した後は、Power BI を使用してデータに接続し、視覚化してレポートやダッシュボードなどを作成できます。

1. [Power BI](http://www.powerbi.com/) にサインインします。

2. [ようこそ] 画面で、**[データベースとその他]** をクリックします。

	![Power BI にデータを取得します](./media/hdinsight-apache-spark-use-bi-tools/hdispark.powerbi.get.data.png "Power BI にデータを取得します")

3. 次の画面で、**[Azure HDInsight 上の Spark]**、**[接続]** の順にクリックします。メッセージが表示されたら、クラスターの URL (`mysparkcluster.azurehdinsight.net`) と、クラスターに接続するための資格情報を入力します。

	接続が確立されると、Power BI は HDInsight の Spark クラスターからデータのインポートを開始します。

5. Power BI がデータをインポートし、**[データセット]** 見出しの下に新しい Spark データセットが追加されます。新しいワークシートを開いてデータを表示するには、データ セットをクリックします。ワークシートはレポートとして保存することもできます。ワークシートを保存するには、**[ファイル]** メニューの **[保存]** をクリックします。

	![Power BI ダッシュボードの Spark タイル](./media/hdinsight-apache-spark-use-bi-tools/hdispark.powerbi.tile.png "Power BI ダッシュボードの Spark タイル")

6. 右側の **[フィールド]** 一覧に、前に作成した **hvac** テーブルが含まれることに注意してください。テーブルを展開し、Notebook で定義したフィールドを表示します。

	  ![Hive テーブルを一覧表示します](./media/hdinsight-apache-spark-use-bi-tools/hdispark.powerbi.display.tables.png "Hive テーブルを一覧表示します")

7. 各ビルの目標温度と実際の温度の差を示す表示を作成します。データを表示するには、**[面グラフ]** (赤で囲まれています) を選択します。軸を定義するために、**BuildingID** フィールドを **[軸]** に、**ActualTemp**/**TargetTemp** フィールドを **[値]** にドラッグ アンド ドロップします。

	![グラフを作成します](./media/hdinsight-apache-spark-use-bi-tools/hdispark.powerbi.visual1.png "グラフを作成します")


8. 既定では、**ActualTemp** および **TargetTemp** の合計が表示されます。どちらのフィールドについても、ドロップダウンから **[平均]** を選択して、両方のビルの実際温度と目標温度の平均を表示します。

	![グラフを作成します](./media/hdinsight-apache-spark-use-bi-tools/hdispark.powerbi.visual2.png)
    
9. データの表示は次のようになります。グラフの上にカーソルを移動すると、関連データを含むツール ヒントが表示されます。

	![グラフを作成します](./media/hdinsight-apache-spark-use-bi-tools/hdispark.powerbi.visual3.png)

10. 上部メニューの **[保存]** をクリックし、レポート名を指定します。表示を固定することもできます。表示を固定するとダッシュボードに保存されて、最新の値を一目で追跡できるようになりす。

	同じデータセットの表示をいくつでも追加して、データのスナップショット用にダッシュボードに固定できます。また、HDInsight の Spark クラスターは Power BI に直接接続されます。つまり、Power BI には常にクラスターの最新データが提供され、データセットを定期的に更新する必要はありません。

##<a name="tableau"></a>Tableau Desktop を使用して Hive テーブル内のデータを分析する
	
1. Tableau Desktop を起動します。左側のウィンドウの接続先サーバー一覧で **[Spark SQL]** をクリックします。Spark SQL が既定で左側のウィンドウに表示されない場合は、**[その他のサーバー]**をクリックして検索できます。 

2. Spark SQL 接続ダイアログ ボックスで、次の値を指定して、**[OK]** をクリックします。

	![Spark クラスターに接続します](./media/hdinsight-apache-spark-use-bi-tools/hdispark.tableau.connect.png "Spark クラスターに接続します")

	[Microsoft Spark ODBC](http://go.microsoft.com/fwlink/?LinkId=616229) ドライバーをコンピューターにインストールしてある場合にのみ、認証ドロップダウンに **[Microsoft Azure HDInsight サービス]** がオプションとして表示されます。

3. 次の画面で、**[スキーマ]** ドロップダウンの **[検索]** アイコンをクリックし、**[デフォルト]** をクリックします。

	![スキーマを検索します](./media/hdinsight-apache-spark-use-bi-tools/hdispark.tableau.find.schema.png "スキーマを検索します")

4. **[テーブル]** フィールドで **[検索]** アイコンをクリックし、クラスターで使用可能なすべての Hive テーブルを一覧表示します。Notebook を使用して前に作成した **hvac** テーブルが表示されます。

	![テーブルを検索します](./media/hdinsight-apache-spark-use-bi-tools/hdispark.tableau.find.table.png "テーブルを検索します")

5. テーブルをドラッグして右側上部のボックスにドロップします。Tableau はデータをインポートし、赤い四角で強調表示されているようにスキーマを表示します。

	![Tableau にテーブルを追加します](./media/hdinsight-apache-spark-use-bi-tools/hdispark.tableau.drag.table.png "Tableau にテーブルを追加します")

6. 左下の **[Sheet1]** タブをクリックします。すべてのビルの各日の目標温度と実際の温度の平均を表示するグラフを作成します。**[日付]** と **[ビル ID]** を **[列]** に、**[実際の温度]**/**[目標温度]** を **[行]** にドラッグします。**[マーク]** で **[領域]** を選択して領域マップ グラフを使用します。

	 ![グラフのフィールドを追加します](./media/hdinsight-apache-spark-use-bi-tools/hdispark.tableau.drag.fields.png "グラフのフィールドを追加します")

7. 既定では、温度フィールドは集計として表示されます。代わりに平均温度を表示する場合は、次のようにしてドロップダウンから行うことができます。

	![平均温度を取得します](./media/hdinsight-apache-spark-use-bi-tools/hdispark.tableau.temp.avg.png "平均温度を取得します")

8. 一方の温度を他の温度にスーパーインポーズして、温度の違いを見やすくすることもできます。マウスを下部領域マップの隅に移動し、赤い丸で囲んだハンドル形状にします。マップを上部の他のマップにドラッグし、マウスが赤い四角で囲んだ形状になったら放します。

	![マップをマージします](./media/hdinsight-apache-spark-use-bi-tools/hdispark.tableau.merge.png "マップをマージします")

	 グラフが次のように変わります。

	![グラフ](./media/hdinsight-apache-spark-use-bi-tools/hdispark.tableau.final.visual.png "グラフ")
	 
9. **[保存]** をクリックしてワークシートを保存します。ダッシュボードを作成して 1 つまたは複数のシートを追加できます。

##<a name="seealso"></a>関連項目

* [概要: Azure HDInsight での Apache Spark](hdinsight-apache-spark-overview.md)

### シナリオ

* [Spark と Machine Learning: HDInsight で Spark を使用して HVAC データを基に建物の温度を分析する](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

* [Spark と Machine Learning: HDInsight で Spark を使用して食品の検査結果を予測する](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

* [Spark ストリーミング: リアルタイム ストリーミング アプリケーションを作成するための HDInsight での Spark の使用](hdinsight-apache-spark-eventhub-streaming.md)

* [Website log analysis using Spark in HDInsight (HDInsight での Spark を使用した Web サイト ログ分析)](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### アプリケーションの作成と実行

* [Scala を使用してスタンドアロン アプリケーションを作成する](hdinsight-apache-spark-create-standalone-application.md)

* [Livy を使用して Spark クラスターでジョブをリモートで実行する](hdinsight-apache-spark-livy-rest-interface.md)

### ツールと拡張機能

* [IntelliJ IDEA 用の HDInsight Tools プラグインを使用して Spark Scala アプリケーションを作成し、送信する (Linux)](hdinsight-apache-spark-intellij-tool-plugin.md)

* [HDInsight の Spark クラスターで Zeppelin Notebook を使用する](hdinsight-apache-spark-use-zeppelin-notebook.md)

* [HDInsight 用の Spark クラスターの Jupyter Notebook で使用可能なカーネル](hdinsight-apache-spark-jupyter-notebook-kernels.md)

### リソースの管理

* [Azure HDInsight での Apache Spark クラスターのリソースの管理](hdinsight-apache-spark-resource-manager.md)

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md


[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: storage-create-storage-account.md

<!---HONumber=AcomDC_0413_2016-->