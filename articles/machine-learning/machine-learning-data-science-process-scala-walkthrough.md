<properties
	pageTitle="Azure 上の Spark での Scala を使用したデータ サイエンス | Microsoft Azure"
	description="Azure HDInsight Spark クラスターで Spark のスケーラブルな Machine Learning ライブラリ (MLlib) と SparkML パッケージを使用して、教師あり機械学習タスクに Scala を使用する方法を説明します。"  
	services="machine-learning"
	documentationCenter=""
	authors="bradsev,deguhath"
	manager="paulettm"
	editor="cgronlun" />

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/01/2016"
	ms.author="bradsev;"/>


# Azure 上の Spark での Scala を使用したデータ サイエンス

このトピックでは、Azure HDInsight Spark クラスターで Spark のスケーラブルな Machine Learning ライブラリ (MLlib) と SparkML パッケージを使用して、教師あり機械学習タスクに Scala を使用する方法を説明します。また、[データ サイエンス プロセス](http://aka.ms/datascienceprocess) (データの取り込みと探索、視覚化、特徴エンジニアリング、モデリング、モデルの使用) を構成するタスクについても説明します。構築されるモデルには、ロジスティック回帰と線形回帰、ランダム フォレスト、勾配ブースティング ツリーなどがあります。次の 2 つの一般的な教師あり機械学習タスクを取り上げます。

- 回帰問題: チップの金額 (ドル) の予測
- 二項分類: タクシーの乗車でチップが支払われるかどうか (1/0) の予測

モデリング プロセスには、適切な精度評価基準を使用したテスト データ セットでのトレーニングと評価が必要です。これらのモデルを Azure BLOB ストレージ (WASB) に保存する方法と、その予測パフォーマンスをスコア付けして評価する方法についても説明します。クロス検証とハイパーパラメーター スイープを使用してモデルを最適化する方法については、高度なトピックで取り上げています。ここで使用しているデータは、2013 年における NYC タクシーの乗車と料金のデータセットから抽出したサンプルです。

[Scala](http://www.scala-lang.org/) は、オブジェクト指向言語と関数型言語の概念を統合した Java 仮想マシン ベースの言語です。クラウドでの分散処理に適したスケーラブルな言語であり、Azure Spark クラスターで実行されます。

[Spark](http://spark.apache.org/) は、ビッグ データ分析アプリケーションのパフォーマンスを高めるメモリ内処理をサポートする、オープン ソースの並列処理フレームワークです。Spark 処理エンジンは、速度、使いやすさ、高度な分析用に作成されています。Spark のメモリ内の分散計算機能により、機械学習とグラフ計算における反復的なアルゴリズムに対して、Spark は適切な選択肢となります。[spark.ml](http://spark.apache.org/docs/latest/ml-guide.html) パッケージは、ユーザーが実用的な機械学習パイプラインを作成および調整するのに役立つ DataFrames を基盤とする高レベルの API セットを提供します。[MLlib](http://spark.apache.org/mllib/) は、この分散環境にモデリング機能を提供する、Spark のスケーラブルな機械学習ライブラリです。

[HDInsight Spark](../hdinsight/hdinsight-apache-spark-overview.md) は、Azure でホストされるオープン ソース Spark サービスです。HDInsight Spark では、Azure BLOB (WASB) に保存されているデータを変換、フィルター処理、視覚化するために、Spark クラスターで Spark SQL の対話型クエリを実行できる **Jupyter Scala Notebook** もサポートしています。この記事でソリューションを提供したり、データを視覚化するために関連するプロットを表示したりする Scala コード スニペットは、Spark クラスターにインストールされた Jupyter Notebook で実行されます。これらのトピックにあるモデリング手順には、各種モデルをトレーニング、評価、保存、および使用する方法を示すコードも含まれています。

セットアップ手順とこのチュートリアルで示すコードは、HDInsight 3.4 Spark 1.6 向けです。ただし、ここで示すコードとこのチュートリアルの [Scala Jupyter Notebook](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Scala/Exploration%20Modeling%20and%20Scoring%20using%20Scala.ipynb) のコードは汎用的であり、すべての Spark クラスターで機能します。HDInsight Spark を使用していない場合、クラスターのセットアップと管理の手順は、ここに記載されている内容と若干異なります。

> [AZURE.NOTE] Scala ではなく Python を使用して、エンド ツー エンドのデータ サイエンス プロセスのタスクを実行する方法については、[Azure HDInsight での Spark を使用したデータ サイエンス](machine-learning-data-science-spark-overview.md)に関する記事をご覧ください。


## 前提条件

1\. これらのトピックを開始する前に、Azure サブスクリプションが必要です。Azure サブスクリプションがない場合は、[Azure 無料試用版の入手](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)に関するページをご覧ください。

2\. このチュートリアルを実行するためには HDInsight 3.4 Spark 1.6 クラスターが必要です。クラスターを作成するには、[Azure HDInsight での Apache Spark クラスターの作成](../hdinsight/hdinsight-apache-spark-jupyter-spark-sql.md)に関するページに記載された手順を参照してください。クラスターの種類とバージョンは、**[クラスターの種類の選択]** メニューから指定します。

![](./media/machine-learning-data-science-process-scala-walkthrough/spark-cluster-on-portal.png)


>[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]


NYC タクシー乗車データの説明と、Spark クラスターで Jupyter Notebook のコードを実行する方法については、「[Azure HDInsight 上の Spark を使用したデータ サイエンスの概要](machine-learning-data-science-spark-overview.md)」をご覧ください。


## Spark クラスターで Jupyter Notebook の Scala コードを実行する 

Jupyter Notebook は Azure ポータルから起動できます。ダッシュボードでご利用の Spark クラスターを見つけてクリックし、クラスターの管理ページにアクセスします。**[クラスター ダッシュボード]**、**[Jupyter Notebook]** の順にクリックして、Spark クラスターに関連付けられている Notebook を開きます。

![](./media/machine-learning-data-science-process-scala-walkthrough/spark-jupyter-on-portal.png)

***https://CLUSTERNAME.azurehdinsight.net/jupyter*** を参照して、Jupyter Notebook にアクセスすることもできます。この URL の CLUSTERNAME の部分は、実際のクラスターの名前に置き換えてください。ノートブックにアクセスするには、管理者アカウントのパスワードが必要です。

![](./media/machine-learning-data-science-process-scala-walkthrough/spark-jupyter-notebook.png)

[Scala] を選択すると表示されるディレクトリには、PySpark API を使用する、あらかじめパッケージ化された Notebook の例が含まれています。Spark に関連する一連のトピックのコード サンプルが含まれた **Exploration Modeling and Scoring using Scala.ipynb** Notebook は [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/Scala) で入手できます。


Github から Spark クラスター上の Jupyter Notebook サーバーに Notebook を直接アップロードできます。Jupyter のホーム ページで、画面の右側にある **[アップロード]** ボタンをクリックします。ファイル エクスプローラーが開きます。ここで、Scala Notebook の GitHub (raw コンテンツ) URL を貼り付け、**[開く]** をクリックします。Scala Notebook は次の URL で入手できます。

[Exploration-Modeling-and-Scoring-using-Scala.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Scala/Exploration-Modeling-and-Scoring-using-Scala.ipynb)


## セットアップ: 事前に設定されたコンテキスト、Spark マジック、ライブラリ

### 事前に設定された Spark および Hive コンテキスト

	# SET START TIME
	import java.util.Calendar
	val beginningTime = Calendar.getInstance().getTime()


Jupyter Notebook で提供される Spark カーネルではコンテキストが事前に設定されているので、開発中のアプリケーションの操作を開始する前に、Spark または Hive コンテキストを明示的に設定しておく必要はありません。これらのコンテキストは既定で使用できます。各コンテキストは次のとおりです。

- sc: SparkContext の場合
- sqlContext: HiveContext の場合


### Spark マジック

Spark カーネルには定義済みの "マジック" が用意されています。マジックは、%% で呼び出すことができる特殊なコマンドです。そのようなコマンドが、以降のコード サンプルでは 2 つ使用されています。

- **%%local**: 後続行のコードをローカルで実行することを指定します。コードは有効な Scala コードである必要があります。
- **%%sql -o <変数名>** sqlContext に対して Hive クエリを実行します。-o パラメーターを渡すと、クエリの結果が Spark データ フレームとして %%local Scala コンテキストで永続化されます。

Jupyter Notebook のカーネルと、%% で呼び出される定義済みの "マジック" (例: %%local) の詳細については、[HDInsight の HDInsight Spark Linux クラスターと Jupyter Notebook で使用可能なカーネル](../hdinsight/hdinsight-apache-spark-jupyter-notebook-kernels.md)に関する記事をご覧ください。


### ライブラリのインポート

次のコードを使用して、Spark、MLlib、必要な他のライブラリをインポートします。

	# IMPORT SPARK AND JAVA LIBRARIES 
	import org.apache.spark.sql.SQLContext
	import org.apache.spark.sql.functions._
	import java.text.SimpleDateFormat
	import java.util.Calendar
	import sqlContext.implicits._
	import org.apache.spark.sql.Row
	
	# SPARK SQL FUNCTIONS
	import org.apache.spark.sql.types.{StructType, StructField, StringType, IntegerType, FloatType, DoubleType}
	import org.apache.spark.sql.functions.rand
	
	# SPARK ML FUNCTIONS
	import org.apache.spark.ml.Pipeline
	import org.apache.spark.ml.feature.{StringIndexer, VectorAssembler, OneHotEncoder, VectorIndexer, Binarizer}
	import org.apache.spark.ml.tuning.{ParamGridBuilder, TrainValidationSplit, CrossValidator}
	import org.apache.spark.ml.regression.{LinearRegression, LinearRegressionModel, RandomForestRegressor, RandomForestRegressionModel, GBTRegressor, GBTRegressionModel}
	import org.apache.spark.ml.classification.{LogisticRegression, LogisticRegressionModel, RandomForestClassifier, RandomForestClassificationModel, GBTClassifier, GBTClassificationModel}
	import org.apache.spark.ml.evaluation.{BinaryClassificationEvaluator, RegressionEvaluator, MulticlassClassificationEvaluator}
	
	# SPARK MLLIB FUNCTIONS
	import org.apache.spark.mllib.linalg.{Vector, Vectors}
	import org.apache.spark.mllib.util.MLUtils
	import org.apache.spark.mllib.classification.{LogisticRegressionWithLBFGS, LogisticRegressionModel}
	import org.apache.spark.mllib.regression.{LabeledPoint, LinearRegressionWithSGD, LinearRegressionModel}
	import org.apache.spark.mllib.tree.{GradientBoostedTrees, RandomForest}
	import org.apache.spark.mllib.tree.configuration.BoostingStrategy
	import org.apache.spark.mllib.tree.model.{GradientBoostedTreesModel, RandomForestModel, Predict}
	import org.apache.spark.mllib.evaluation.{BinaryClassificationMetrics, MulticlassMetrics, RegressionMetrics}
	
	# SPECIFY SQL CONTEXT
	val sqlContext = new SQLContext(sc)


## データの取り込み
 
データ サイエンス プロセスでは、まず、分析するデータを外部のソースまたはシステムからデータ探索およびモデリング環境に取り込みます。このチュートリアルでは、タクシーの乗車データおよび料金ファイル (.tsv ファイルとして保存) の結合された 0.1% サンプルを読み取ります。データ探索およびモデリング環境は Spark です。このセクションでは、次の一連のタスクを実行するコードを示します。

- データおよびモデル ストレージのディレクトリ パスを設定する
- 入力データセット (.tsv ファイルとして保存) の読み取り
- データのスキーマを定義し、データをクリーニングする
- クリーニングされたデータ フレームを作成し、メモリにキャッシュする
- SQL コンテキストでデータ フレームを一時テーブルとして登録する
- テーブルに対してクエリを実行し、結果をデータ フレームにインポートする


### WASB のストレージの場所となるディレクトリ パスの設定

Spark は、Azure Storage BLOB (WASB とも呼ばれます) に対する読み取りと書き込みを実行できます。そのため、WASB に保存されている既存のデータは Spark を使用して処理することができ、結果も WASB に保存できます。

WASB にモデルやファイルを保存するには、パスを正しく指定する必要があります。Spark クラスターに接続されている既定のコンテナーは、"wasb///" で始まるパスを使用して参照できます。他の場所は "wasb://" で参照します。

次のコード サンプルでは、読み取る入力データの場所と、Spark クラスターに接続された、モデルの保存先となる Azure BLOB のパスを指定しています。

	# SET PATHS TO DATA AND MODEL FILE LOCATIONS: 
	# INGEST DATA DATA AND SPECIFY HEADERS FOR COLUMNS
	val taxi_train_file = sc.textFile("wasb://mllibwalkthroughs@cdspsparksamples.blob.core.windows.net/Data/NYCTaxi/JoinedTaxiTripFare.Point1Pct.Train.tsv")
	val header = taxi_train_file.first;
	
	# SET THE MODEL STORAGE DIRECTORY PATH 
	# NOTE THAT THE FINAL BACKSLASH IN THE PATH IS NEEDED.
	val modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/";


### データをインポートし、RDD を作成して、スキーマに従ってデータ フレームを定義する 

	# RECORD START TIME
	val starttime = Calendar.getInstance().getTime()
	
	# DEFINE SCHEMA BASED ON HEADER OF THE FILE
	val sqlContext = new SQLContext(sc)
	val taxi_schema = StructType(
	    Array(
	        StructField("medallion", StringType, true), 
	        StructField("hack_license", StringType, true),
	        StructField("vendor_id", StringType, true), 
	        StructField("rate_code", DoubleType, true),
	        StructField("store_and_fwd_flag", StringType, true),
	        StructField("pickup_datetime", StringType, true),
	        StructField("dropoff_datetime", StringType, true),
	        StructField("pickup_hour", DoubleType, true),
	        StructField("pickup_week", DoubleType, true),
	        StructField("weekday", DoubleType, true),
	        StructField("passenger_count", DoubleType, true),
	        StructField("trip_time_in_secs", DoubleType, true),
	        StructField("trip_distance", DoubleType, true),
	        StructField("pickup_longitude", DoubleType, true),
	        StructField("pickup_latitude", DoubleType, true),
	        StructField("dropoff_longitude", DoubleType, true),
	        StructField("dropoff_latitude", DoubleType, true),
	        StructField("direct_distance", StringType, true),
	        StructField("payment_type", StringType, true),
	        StructField("fare_amount", DoubleType, true),
	        StructField("surcharge", DoubleType, true),
	        StructField("mta_tax", DoubleType, true),
	        StructField("tip_amount", DoubleType, true),
	        StructField("tolls_amount", DoubleType, true),
	        StructField("total_amount", DoubleType, true),
	        StructField("tipped", DoubleType, true),
	        StructField("tip_class", DoubleType, true)
	        )
	    )
	
	# CAST VARIABLES ACCORDING TO SCHEMA
	val taxi_temp = (taxi_train_file.map(_.split("\t"))
	                        .filter((r) => r(0) != "medallion")
	                        .map(p => Row(p(0), p(1), p(2),
	                            p(3).toDouble, p(4), p(5), p(6), p(7).toDouble, p(8).toDouble, p(9).toDouble, p(10).toDouble,
	                            p(11).toDouble, p(12).toDouble, p(13).toDouble, p(14).toDouble, p(15).toDouble, p(16).toDouble,
	                            p(17), p(18), p(19).toDouble, p(20).toDouble, p(21).toDouble, p(22).toDouble,
	                            p(23).toDouble, p(24).toDouble, p(25).toDouble, p(26).toDouble)))
	
	
	# CREATE INITIAL DATA-FRAME, DROP COLUMNS, AND CREATE CLEANED DATA-FRAME BY FILTERING FOR UNDESIRED VALUES OR OUTLIERS
	val taxi_train_df = sqlContext.createDataFrame(taxi_temp, taxi_schema)
	
	val taxi_df_train_cleaned = (taxi_train_df.drop(taxi_train_df.col("medallion"))
	        .drop(taxi_train_df.col("hack_license")).drop(taxi_train_df.col("store_and_fwd_flag"))
	        .drop(taxi_train_df.col("pickup_datetime")).drop(taxi_train_df.col("dropoff_datetime"))
	        .drop(taxi_train_df.col("pickup_longitude")).drop(taxi_train_df.col("pickup_latitude"))
	        .drop(taxi_train_df.col("dropoff_longitude")).drop(taxi_train_df.col("dropoff_latitude"))
	        .drop(taxi_train_df.col("surcharge")).drop(taxi_train_df.col("mta_tax"))
	        .drop(taxi_train_df.col("direct_distance")).drop(taxi_train_df.col("tolls_amount"))
	        .drop(taxi_train_df.col("total_amount")).drop(taxi_train_df.col("tip_class"))
	        .filter("passenger_count > 0 and passenger_count < 8 AND payment_type in ('CSH', 'CRD') AND tip_amount >= 0 AND tip_amount < 30 AND fare_amount >= 1 AND fare_amount < 150 AND trip_distance > 0 AND trip_distance < 100 AND trip_time_in_secs > 30 AND trip_time_in_secs < 7200"));
	
	# CACHE AND MATERIALIZE CLEANED DATA-FRAME IN MEMORY
	taxi_df_train_cleaned.cache()
	taxi_df_train_cleaned.count()
	
	# REGISTER DATA-FRAME AS A TEMP-TABLE IN SQL-CONTEXT
	taxi_df_train_cleaned.registerTempTable("taxi_train")
	
	# GET TIME TO RUN THE CELL
	val endtime = Calendar.getInstance().getTime()
	val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
	println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**出力:**

上記のセルの実行に要した時間: 8 秒


### テーブルに対してクエリを実行し、結果をデータ フレームにインポートする 

テーブルに対してクエリを実行して、料金、乗客、チップのデータを取得し、破損したデータや範囲外のデータを除外して、複数の行を出力します。

	# QUERY THE DATA
	val sqlStatement = """
	    SELECT fare_amount, passenger_count, tip_amount, tipped
	    FROM taxi_train 
	    WHERE passenger_count > 0 AND passenger_count < 7
	    AND fare_amount > 0 AND fare_amount < 200
	    AND payment_type in ('CSH', 'CRD')
	    AND tip_amount > 0 AND tip_amount < 25
	"""
	val sqlResultsDF = sqlContext.sql(sqlStatement)
	
	# SHOW ONLY THE TOP THREE ROWS
	sqlResultsDF.show(3)

**出力:**

fare\_amount|passenger\_count|tip\_amount|tipped
-----------|---------------|----------|------
 13\.5| 1\.0| 2\.9| 1\.0
 16\.0| 2\.0| 3\.4| 1\.0
 10\.5| 2\.0| 1\.0| 1\.0


## データの探索と視覚化 

データが Spark に取り込まれたら、次に、探索と視覚化によってデータの理解を深めます。このセクションでは、SQL クエリを使用してタクシー データを調べます。次に、結果をデータ フレームにインポートし、Jupyter の自動視覚化機能を使用して視覚化するためにターゲット変数と予想される特徴をプロットします。

### ローカル マジックと SQL マジックを使用してデータをプロットする

既定では、Jupyter Notebook から実行するコード スニペットの出力は、ワーカー ノードで永続化されるセッションのコンテキスト内で使用できます。計算を実行するたびに乗車データをワーカー ノードに保存する場合や、計算に必要なすべてのデータが Jupyter サーバー ノード (ヘッド ノード) でローカルで使用可能な場合は、%%local マジックを使用して Jupyter サーバー上でコード スニペットを実行できます。

- **SQL マジック (`%%sql`)** HDInsight Spark カーネルは、sqlContext に対する簡単なインライン HiveQL クエリをサポートしています。引数 (-o VARIABLE\_NAME) を指定すると、SQL クエリの出力結果が Pandas データフレームとして Jupyter サーバー上に永続化されます。つまり、出力結果をローカルから使用できるようになります。
- **`%%local` マジック**は、Jupyter サーバー (HDInsight クラスターのヘッド ノード) 上でコードをローカルで実行するときに使用します。通常、`%%local` マジックは、-o パラメーターを指定した `%%sql` マジックと組み合わせて使用します。SQL クエリの出力結果を -o パラメーターでローカルに永続化したうえで、%%local マジックを使用すると、それに続く一連のコード スニペットが、ローカルに永続化されている SQL クエリの出力結果に対してローカルに実行されます。

### SQL を使用してデータを照会する
このクエリでは、料金、乗客数、チップの金額を取得します。

	# SQL QUERY
	%%sql -q -o sqlResults
	SELECT fare_amount, passenger_count, tip_amount, tipped FROM taxi_train WHERE passenger_count > 0 AND passenger_count < 7 AND fare_amount > 0 AND fare_amount < 200 AND payment_type in ('CSH', 'CRD') AND tip_amount > 0 AND tip_amount < 25

次のコードでは、%%local マジックでローカル データ フレーム (sqlResults) を作成します。sqlResults は、matplotlib を使用したプロットに使用できます。

> [AZURE.TIP] ローカル マジックは、このチュートリアルで何度も使用しています。データ量が多い場合は、サンプリングしてローカル メモリに収まるデータ フレームを作成してください。

### データをプロットする

データ フレームを pandas データ フレームとしてローカル コンテキストに配置したら、Python コードを使用してプロットできます。

	# RUN THE CODE LOCALLY ON THE JUPYTER SERVER
	%%local
	
	# USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES. 
	# CLICK ON THE TYPE OF PLOT TO BE GENERATED (E.G. LINE, AREA, BAR ETC.)
	sqlResults


 コードの実行後、Spark カーネルによって SQL (HiveQL) クエリの出力が自動的に視覚化されます。次のような数種類の視覚化の中から選択できます。

- テーブル
- 円グラフ
- 折れ線グラフ
- 領域
- 棒グラフ

データをプロットするコードを次に示します。

	# RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
	%%local
	import matplotlib.pyplot as plt
	%matplotlib inline
	
	# TIP BY PAYMENT TYPE AND PASSENGER COUNT
	ax1 = sqlResults[['tip_amount']].plot(kind='hist', bins=25, facecolor='lightblue')
	ax1.set_title('Tip amount distribution')
	ax1.set_xlabel('Tip Amount ($)')
	ax1.set_ylabel('Counts')
	plt.suptitle('')
	plt.show()
	
	# TIP BY PASSENGER COUNT
	ax2 = sqlResults.boxplot(column=['tip_amount'], by=['passenger_count'])
	ax2.set_title('Tip amount by Passenger count')
	ax2.set_xlabel('Passenger count')
	ax2.set_ylabel('Tip Amount ($)')
	plt.suptitle('')
	plt.show()
	
	# TIP AMOUNT BY FARE AMOUNT, POINTS ARE SCALED BY PASSENGER COUNT
	ax = sqlResults.plot(kind='scatter', x= 'fare_amount', y = 'tip_amount', c='blue', alpha = 0.10, s=5*(sqlResults.passenger_count))
	ax.set_title('Tip amount by Fare amount')
	ax.set_xlabel('Fare Amount ($)')
	ax.set_ylabel('Tip Amount ($)')
	plt.axis([-2, 80, -2, 20])
	plt.show()


**出力:**

![チップの金額のヒストグラム](./media/machine-learning-data-science-process-scala-walkthrough/plot-tip-amount-histogram.png)

![乗客数別チップの金額](./media/machine-learning-data-science-process-scala-walkthrough/plot-tip-amount-by-passenger-count.png)

![料金別チップの金額](./media/machine-learning-data-science-process-scala-walkthrough/plot-tip-amount-by-fare-amount.png)



## モデリング関数に使用する特徴を作成し、データを準備する 

Spark ML や MLlib のツリーベースのモデリング関数を使用する場合、ビン分割、インデックス作成、ワンホット エンコード、ベクター化などの適切な手法を使用してターゲットと特徴を準備する必要があります。このセクションでは、次の手順について説明します。

- 時間を乗車時間のバケットに**ビン分割**して新しい特徴を作成する
- カテゴリの特徴の**インデックス作成とワンホット エンコード**を実行する
- **データセットのサンプリングを作成し、トレーニング用とテスト用に分ける**
- **トレーニング変数と特徴を指定**し、インデックス付きまたはワンホット エンコードされたトレーニングおよびテストの入力ラベル付きポイントの RDD またはデータ フレームを作成する
- ML モデルの入力として使用する**特徴とターゲットの分類およびベクター化**を自動的に実行する


### 時間を乗車時間のバケットにビン分割して新しい特徴を作成する 

次のコードでは、時間を乗車時間のバケットにビン分割して新しい特徴を作成する方法と、結果として生成されたデータ フレームをメモリにキャッシュする方法を示します。Resilient Distributed Dataset (RDD) とデータ フレームを繰り返し使用する場合、キャッシュによって実行時間が短縮されます。そのため、このチュートリアルでは複数の段階で RDD とデータ フレームをキャッシュしています。

	# CREATE FOUR BUCKETS FOR TRAFFIC TIMES
	val sqlStatement = """
	    SELECT *,
	    CASE
	     WHEN (pickup_hour <= 6 OR pickup_hour >= 20) THEN "Night" 
	     WHEN (pickup_hour >= 7 AND pickup_hour <= 10) THEN "AMRush" 
	     WHEN (pickup_hour >= 11 AND pickup_hour <= 15) THEN "Afternoon"
	     WHEN (pickup_hour >= 16 AND pickup_hour <= 19) THEN "PMRush"
	    END as TrafficTimeBins
	    FROM taxi_train 
	"""
	val taxi_df_train_with_newFeatures = sqlContext.sql(sqlStatement)
	
	# CACHE DATA-FRAME IN MEMORY & MATERIALIZE DF IN MEMORY 
	taxi_df_train_with_newFeatures.cache()
	taxi_df_train_with_newFeatures.count()


### カテゴリの特徴のインデックス作成とワンホット エンコードを実行する 

ここでは、モデリング関数への入力用に、カテゴリの特徴のインデックスを作成し、特徴をエンコードする方法について説明します。MLlib のモデリング関数と予測関数では、特徴のカテゴリ入力データを使用する前に、データのインデックスを作成するか、データをエンコードする必要があります。

モデルに応じて、さまざまな方法でカテゴリ入力データのインデックス作成またはエンコードを実行する必要があります。例を挙げると、ロジスティック回帰モデルと線形回帰モデルでは、ワンホット エンコードが必要です。たとえば、3 つのカテゴリを持つ特徴は、観察のカテゴリに応じてそれぞれ 0 または 1 が含まれた 3 つの特徴列に展開できます。MLlib には、ワンホット エンコードを実行する [OneHotEncoder](http://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) 関数が用意されています。このエンコーダーは、ラベル インデックスの列をバイナリ ベクトルの列にマップします。この列に含まれる値は最大でも 1 つだけです。このエンコードにより、数値を持つ特徴を必要とするアルゴリズム (ロジスティック回帰など) をカテゴリの特徴に適用できます。

ここでは、例を示すために文字列である 4 つの変数だけを変換します。数値で表される他の変数 (曜日など) で、カテゴリ変数としてインデックスを作成することもできます。

インデックス作成には MLlib の `StringIndexer()` 関数を使用し、ワンホット エンコードには `OneHotEncoder()` 関数を使用しました。カテゴリの特徴のインデックス作成とエンコードを実行するコードを次に示します。


	# HERE WE CREATE INDEXES, AND ONE-HOT ENCODED VECTORS FOR SEVERAL CATEGORICAL FEATURES

	# RECORD START TIME
	val starttime = Calendar.getInstance().getTime()
	
	# INDEX AND ENCODE VENDOR_ID
	val stringIndexer = new StringIndexer().setInputCol("vendor_id").setOutputCol("vendorIndex").fit(taxi_df_train_with_newFeatures)
	val indexed = stringIndexer.transform(taxi_df_train_with_newFeatures)
	val encoder = new OneHotEncoder().setInputCol("vendorIndex").setOutputCol("vendorVec")
	val encoded1 = encoder.transform(indexed)
	
	# INDEX AND ENCODE RATE_CODE
	val stringIndexer = new StringIndexer().setInputCol("rate_code").setOutputCol("rateIndex").fit(encoded1)
	val indexed = stringIndexer.transform(encoded1)
	val encoder = new OneHotEncoder().setInputCol("rateIndex").setOutputCol("rateVec")
	val encoded2 = encoder.transform(indexed)
	
	# INDEX AND ENCODE PAYMENT_TYPE
	val stringIndexer = new StringIndexer().setInputCol("payment_type").setOutputCol("paymentIndex").fit(encoded2)
	val indexed = stringIndexer.transform(encoded2)
	val encoder = new OneHotEncoder().setInputCol("paymentIndex").setOutputCol("paymentVec")
	val encoded3 = encoder.transform(indexed)
	
	# INDEX AND TRAFFIC TIME BINS
	val stringIndexer = new StringIndexer().setInputCol("TrafficTimeBins").setOutputCol("TrafficTimeBinsIndex").fit(encoded3)
	val indexed = stringIndexer.transform(encoded3)
	val encoder = new OneHotEncoder().setInputCol("TrafficTimeBinsIndex").setOutputCol("TrafficTimeBinsVec")
	val encodedFinal = encoder.transform(indexed)
	
	# GET TIME TO RUN THE CELL 
	val endtime = Calendar.getInstance().getTime()
	val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
	println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**出力:**

上記のセルの実行に要した時間: 4 秒



### データセットのサンプリングを作成し、トレーニング用とテスト用に分ける

次のコードでは、データのランダム サンプリングを作成します (ここでは 25% を使用)。この例のデータセットのサイズでは、ランダム サンプリングを作成する必要はありませんが、必要に応じて独自の問題にランダム サンプリングを使用する方法がわかるように、ここではサンプリングの方法を示します。サンプル数が多い場合、ランダム サンプリングを作成することで、モデルのトレーニング時に時間を大幅に節約できます。次に、分類および回帰モデリングで使用するために、サンプルをトレーニング用 (ここでは 75%) とテスト用 (ここでは 25%) に分けます。

トレーニング中にクロス検証のフォールドの選択に使用できる ("rand" 列の) 行に到達するための乱数 (0 ～ 1) を追加します。


	# RECORD START TIME
	val starttime = Calendar.getInstance().getTime()
	
	# SPECIFY SAMPLING AND SPLITTING FRACTIONS
	val samplingFraction = 0.25;
	val trainingFraction = 0.75; 
	val testingFraction = (1-trainingFraction);
	val seed = 1234;
	val encodedFinalSampledTmp = encodedFinal.sample(withReplacement = false, fraction = samplingFraction, seed = seed)
	val sampledDFcount = encodedFinalSampledTmp.count().toInt
	
	val generateRandomDouble = udf(() => {
	    scala.util.Random.nextDouble
	})
	
	# ADD RANDOM NUMBER FOR CV
	val encodedFinalSampled = encodedFinalSampledTmp.withColumn("rand", generateRandomDouble());
	
	# SPLIT SAMPLED DATA-FRAME INTO TRAIN/TEST, WITH A RANDOM COLUMN ADDED FOR DOING CV (SHOWN LATER)
	# INCLUDE RAND COLUMN FOR CREATING CROSS-VALIDATION FOLDS
	val splits = encodedFinalSampled.randomSplit(Array(trainingFraction, testingFraction), seed = seed)
	val trainData = splits(0)
	val testData = splits(1)
	
	# GET TIME TO RUN THE CELL 
	val endtime = Calendar.getInstance().getTime()
	val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
	println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**出力:**

上記のセルの実行に要した時間: 2 秒


### トレーニング変数と特徴を指定し、インデックス付きまたはワンホット エンコードされたトレーニングおよびテストの入力ラベル付きポイントの RDD またはデータ フレームを作成する 

このセクションのコードでは、ラベル付きポイント データ型としてカテゴリ テキスト データのインデックスを作成し、MLlib ロジスティック回帰モデルや他の分類モデルのトレーニングとテストに使用できるように、カテゴリ テキスト データをエンコードする方法を示します。ラベル付きポイント オブジェクトは、MLlib のほとんどの ML アルゴリズムで入力データとして必要とされる方法でフォーマットされた Resilient Distributed Dataset (RDD) です。[ラベル付きポイント](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point)は、ラベル/応答に関連付けられたローカル ベクトル (密または疎) です。

次のコードでは、トレーニングに使用されるターゲット (従属) 変数と特徴を指定し、インデックス付きまたはワンホット エンコードされたトレーニングおよびテストの入力ラベル付きポイントの RDD またはデータ フレームを作成します。


	# RECORD START TIME
	val starttime = Calendar.getInstance().getTime()
	
	# MAP NAMES OF FEATURES AND TARGETS FOR CLASSIFICATION AND REGRESSION PROBLEMS.
	val featuresIndOneHot = List("paymentVec", "vendorVec", "rateVec", "TrafficTimeBinsVec", "pickup_hour", "weekday", "passenger_count", "trip_time_in_secs", "trip_distance", "fare_amount").map(encodedFinalSampled.columns.indexOf(_))
	val featuresIndIndex = List("paymentIndex", "vendorIndex", "rateIndex", "TrafficTimeBinsIndex", "pickup_hour", "weekday", "passenger_count", "trip_time_in_secs", "trip_distance", "fare_amount").map(encodedFinalSampled.columns.indexOf(_))
	
	# SPECIFY THE TARGET FOR CLASSIFICATION ('tipped') AND REGRESSION ('tip_amount') PROBLEMS
	val targetIndBinary = List("tipped").map(encodedFinalSampled.columns.indexOf(_))
	val targetIndRegression = List("tip_amount").map(encodedFinalSampled.columns.indexOf(_))
	
	# INDEXED LAELEDPOINT RDD OBJECTS
	val indexedTRAINbinary = trainData.rdd.map(r => LabeledPoint(r.getDouble(targetIndBinary(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))
	val indexedTESTbinary = testData.rdd.map(r => LabeledPoint(r.getDouble(targetIndBinary(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))
	val indexedTRAINreg = trainData.rdd.map(r => LabeledPoint(r.getDouble(targetIndRegression(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))
	val indexedTESTreg = testData.rdd.map(r => LabeledPoint(r.getDouble(targetIndRegression(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))
	
	# Indexed DFs that can be used for training using Spark ML functions
	val indexedTRAINbinaryDF = indexedTRAINbinary.toDF()
	val indexedTESTbinaryDF = indexedTESTbinary.toDF()
	val indexedTRAINregDF = indexedTRAINreg.toDF()
	val indexedTESTregDF = indexedTESTreg.toDF()
	
	# One-hot encoded (vectorized) DFs that can be used for training using Spark ML functions
	val assemblerOneHot = new VectorAssembler().setInputCols(Array("paymentVec", "vendorVec", "rateVec", "TrafficTimeBinsVec", "pickup_hour", "weekday", "passenger_count", "trip_time_in_secs", "trip_distance", "fare_amount")).setOutputCol("features")
	val OneHotTRAIN = assemblerOneHot.transform(trainData) 
	val OneHotTEST = assemblerOneHot.transform(testData)
	
	# GET TIME TO RUN THE CELL
	val endtime = Calendar.getInstance().getTime()
	val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
	println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**出力:**

上記のセルの実行に要した時間: 4 秒


### ML モデルの入力として使用する特徴とターゲットの分類およびベクター化を自動的に実行する

Spark ML のツリーベースのモデリング関数で使用するターゲットと特徴を適切に分類します。コードでは次の 2 つのタスクを実行します。

1. しきい値として 0.5 を使用して、0 ～ 1 の各データ ポイントに値 0 または 1 を割り当てることで、分類の二項ターゲットを作成します。
2. 特徴を自動的に分類します。特徴の異なる数値の数が 32 個未満の場合、その特徴は分類されます。

この 2 つのタスクのコードを次に示します。

	# CATEGORIZE FEATURES AND BINARIZE TARGET FOR BINARY CLASSIFICATION PROBLEM

	# TRAIN DATA
	val indexer = new VectorIndexer().setInputCol("features").setOutputCol("featuresCat").setMaxCategories(32)
	val indexerModel = indexer.fit(indexedTRAINbinaryDF)
	val indexedTrainwithCatFeat = indexerModel.transform(indexedTRAINbinaryDF)
	val binarizer: Binarizer = new Binarizer().setInputCol("label").setOutputCol("labelBin").setThreshold(0.5)
	val indexedTRAINwithCatFeatBinTarget = binarizer.transform(indexedTrainwithCatFeat)
	
	# TEST DATA
	val indexerModel = indexer.fit(indexedTESTbinaryDF)
	val indexedTrainwithCatFeat = indexerModel.transform(indexedTESTbinaryDF)
	val binarizer: Binarizer = new Binarizer().setInputCol("label").setOutputCol("labelBin").setThreshold(0.5)
	val indexedTESTwithCatFeatBinTarget = binarizer.transform(indexedTrainwithCatFeat)
	
	# CATEGORIZE FEATURES FOR REGRESSION PROBLEM
	# CREATE PROPERLY INDEXED AND CATEGORIED DFS FOR TREE-BASED MODELS

	# TRAIN DATA
	val indexer = new VectorIndexer().setInputCol("features").setOutputCol("featuresCat").setMaxCategories(32)
	val indexerModel = indexer.fit(indexedTRAINregDF)
	val indexedTRAINwithCatFeat = indexerModel.transform(indexedTRAINregDF)
	
	# TEST DATA
	val indexerModel = indexer.fit(indexedTESTbinaryDF)
	val indexedTESTwithCatFeat = indexerModel.transform(indexedTESTregDF)


## 二項分類: チップが支払われるかどうかを予測する

このセクションでは、チップが支払われるかどうかを予測する、次の 3 種類の二項分類モデルを作成します。

- SparkML の `LogisticRession()` 関数を使用した**ロジスティック回帰モデル**
- Spark ML の `RandomForestClassifier()` 関数を使用した**ランダム フォレスト分類モデル**
- MLlib の `GradientBoostedTrees()` 関数を使用した**勾配ブースティング ツリー分類モデル**

### ロジスティック回帰モデルを作成する

ここでは、SparkML の `LogisticRession()` 関数を使用して、ロジスティック回帰モデルを作成します。このセクションのモデル構築コードは、次の一連の手順で構成されます。

1. 1 つのパラメーター セットを使用する**モデルのトレーニング** データ
2. メトリックを含むテスト データ セットでの**モデルの評価**
3. 今後使用できるようにするための BLOB への**モデルの保存**
4. テスト データに対する**モデルのスコア付け**
5. **結果のプロット** - ROC 曲線

これらの手順のコードを次に示します。

	# CREATE LOGISTIC REGRESSION MODEL 
	val lr = new LogisticRegression().setLabelCol("tipped").setFeaturesCol("features").setMaxIter(10).setRegParam(0.3).setElasticNetParam(0.8)
	val lrModel = lr.fit(OneHotTRAIN)
	
	# PREDICT ON TEST DATA SET
	val predictions = lrModel.transform(OneHotTEST)
	
	# SELECT BinaryClassificationEvaluator() TO COMPUTE TEST ERROR
	val evaluator = new BinaryClassificationEvaluator().setLabelCol("tipped").setRawPredictionCol("probability").setMetricName("areaUnderROC")
	val ROC = evaluator.evaluate(predictions)
	println("ROC on test data = " + ROC)
	
	# SAVE THE MODEL
	val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
	val modelName = "LogisticRegression__"
	val filename = modelDir.concat(modelName).concat(datestamp)
	lrModel.save(filename);

次に、結果を読み込み、スコアを付けて保存します。

	# RECORD START TIME 
	val starttime = Calendar.getInstance().getTime()

	# LOAD SAVED MODEL AND SCORE THE TEST DATA SET
	val savedModel = org.apache.spark.ml.classification.LogisticRegressionModel.load(filename)
	println(s"Coefficients: ${savedModel.coefficients} Intercept: ${savedModel.intercept}")
	
	# SCORE THE MODEL ON THE TEST DATA
	val predictions = savedModel.transform(OneHotTEST).select("tipped","probability","rawPrediction")
	predictions.registerTempTable("testResults")
	
	# SELECT BinaryClassificationEvaluator() TO COMPUTE TEST ERROR
	val evaluator = new BinaryClassificationEvaluator().setLabelCol("tipped").setRawPredictionCol("probability").setMetricName("areaUnderROC")
	val ROC = evaluator.evaluate(predictions)
	
	# GET TIME TO RUN THE CELL
	val endtime = Calendar.getInstance().getTime()
	val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
	println("Time taken to run the above cell: " + elapsedtime + " seconds.");
	
	# PRINT THE ROC RESULTS
	println("ROC on test data = " + ROC)


**出力:**

テスト データの ROC = 0.9827381497557599


ローカルの pandas データ フレームで Python を使用して、ROC 曲線をプロットします。


	# QUERY RESULTS
	%%sql -q -o sqlResults
	SELECT tipped, probability from testResults


	# RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
	%%local
	%matplotlib inline
	from sklearn.metrics import roc_curve,auc
	
	sqlResults['probFloat'] = sqlResults.apply(lambda row: row['probability'].values()[0][1], axis=1)
	predictions_pddf = sqlResults[["tipped","probFloat"]]
	
	# ROC CURVE
	# predictions_pddf = sqlResults.rename(columns={'_1': 'probability', 'tipped': 'label'})
	prob = predictions_pddf["probFloat"] 
	fpr, tpr, thresholds = roc_curve(predictions_pddf['tipped'], prob, pos_label=1);
	roc_auc = auc(fpr, tpr)
	
	#PLOT
	plt.figure(figsize=(5,5))
	plt.plot(fpr, tpr, label='ROC curve (area = %0.2f)' % roc_auc)
	plt.plot([0, 1], [0, 1], 'k--')
	plt.xlim([0.0, 1.0])
	plt.ylim([0.0, 1.05])
	plt.xlabel('False Positive Rate')
	plt.ylabel('True Positive Rate')
	plt.title('ROC Curve')
	plt.legend(loc="lower right")
	plt.show()


**出力:**

![チップが支払われるかどうかの ROC 曲線](./media/machine-learning-data-science-process-scala-walkthrough/plot-roc-curve-tip-or-not.png)


### ランダム フォレスト分類モデルを作成する

ここでは、Spark ML の `RandomForestClassifier()` 関数を使用してランダム フォレスト分類モデルを作成し、テスト データでモデルを評価します。


	# RECORD START TIME 
	val starttime = Calendar.getInstance().getTime()
	
	# CREATE THE RANDOM FOREST CLASSIFIER MODEL
	val rf = new RandomForestClassifier().setLabelCol("labelBin").setFeaturesCol("featuresCat").setNumTrees(10).setSeed(1234)
	
	# FIT THE MODEL
	val rfModel = rf.fit(indexedTRAINwithCatFeatBinTarget)
	val predictions = rfModel.transform(indexedTESTwithCatFeatBinTarget)
	
	# EVALUATE THE MODEL
	val evaluator = new MulticlassClassificationEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("f1")
	val Test_f1Score = evaluator.evaluate(predictions)
	println("F1 score on test data: " + Test_f1Score);
	
	# GET TIME TO RUN THE CELL
	val endtime = Calendar.getInstance().getTime()
	val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
	println("Time taken to run the above cell: " + elapsedtime + " seconds.");
	
	# CALCULATE BINARY CLASSIFICATION EVALUATION METRICSS
	val evaluator = new BinaryClassificationEvaluator().setLabelCol("label").setRawPredictionCol("probability").setMetricName("areaUnderROC")
	val ROC = evaluator.evaluate(predictions)
	println("ROC on test data = " + ROC)


**出力:**

テスト データの ROC = 0.9847103571552683


### 勾配ブースティング ツリー分類モデルを作成する

ここでは、MLlib の `GradientBoostedTrees()` 関数を使用して勾配ブースティング ツリー分類モデルを作成し、テスト データでモデルを評価します。


	# TRAIN A GBT CLASSIFICATION MODEL USING MLIB AND LABELEDPOINT

	# RECORD START TIME
	val starttime = Calendar.getInstance().getTime()
	
	# DEFINE THE GBT CLASSIFICATION MODEL
	val boostingStrategy = BoostingStrategy.defaultParams("Classification")
	boostingStrategy.numIterations = 20
	boostingStrategy.treeStrategy.numClasses = 2
	boostingStrategy.treeStrategy.maxDepth = 5
	boostingStrategy.treeStrategy.categoricalFeaturesInfo = Map[Int, Int]((0,2),(1,2),(2,6),(3,4))
	
	# TRAIN THE MODEL
	val gbtModel = GradientBoostedTrees.train(indexedTRAINbinary, boostingStrategy)
	
	# SAVE THE MODEL IN BLOB STORAGE
	val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
	val modelName = "GBT_Classification__"
	val filename = modelDir.concat(modelName).concat(datestamp)
	gbtModel.save(sc, filename);
	
	# EVALUATE THE MODEL ON TEST INSTANCES AND THE COMPUTE TEST ERROR
	val labelAndPreds = indexedTESTbinary.map { point =>
	  val prediction = gbtModel.predict(point.features)
	  (point.label, prediction)
	}
	val testErr = labelAndPreds.filter(r => r._1 != r._2).count.toDouble / indexedTRAINbinary.count()
	//println("Learned classification GBT model:\n" + gbtModel.toDebugString)
	println("Test Error = " + testErr)
	
	# USE BINARY AND MULTICLASS METRICS TO EVALUATE THE MODEL ON THE TEST DATA
	val metrics = new MulticlassMetrics(labelAndPreds)
	println(s"Precision: ${metrics.precision}")
	println(s"Recall: ${metrics.recall}")
	println(s"F1 Score: ${metrics.fMeasure}")
	
	val metrics = new BinaryClassificationMetrics(labelAndPreds)
	println(s"Area under PR curve: ${metrics.areaUnderPR}")
	println(s"Area under ROC curve: ${metrics.areaUnderROC}")
	
	# GET TIME TO RUN THE CELL
	val endtime = Calendar.getInstance().getTime()
	val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
	println("Time taken to run the above cell: " + elapsedtime + " seconds.");
	
	# PRINT THE ROC METRIC
	println(s"Area under ROC curve: ${metrics.areaUnderROC}")


**出力:**

ROC 曲線下面積: 0.9846895479241554


## 回帰: チップの金額を予測する 

このセクションでは、チップの金額を予測する、次の 2 種類の回帰モデルを作成します。

- Spark ML の `LinearRegression()` 関数を使用した**正規化線形回帰モデル**。モデルを保存し、テスト データでモデルを評価します。
- Spark ML の `GBTRegressor() ` 関数を使用した**勾配ブースティング ツリー回帰モデル**。


### 正規化線形回帰を作成する

	# RECORD START TIME
	val starttime = Calendar.getInstance().getTime()
	
	# CREATE REGULARIZED LINEAR REGRESSION MODEL USING SPARK ML FUNCTION AND DATA-FRAME
	val lr = new LinearRegression().setLabelCol("tip_amount").setFeaturesCol("features").setMaxIter(10).setRegParam(0.3).setElasticNetParam(0.8)
	
	# FIT THE MODEL USING DATA-FRAME
	val lrModel = lr.fit(OneHotTRAIN)
	println(s"Coefficients: ${lrModel.coefficients} Intercept: ${lrModel.intercept}")
	
	# SUMMARIZE THE MODEL OVER THE TRAIINNG SET AND PRINT OUT SOME METRICS
	val trainingSummary = lrModel.summary
	println(s"numIterations: ${trainingSummary.totalIterations}")
	println(s"objectiveHistory: ${trainingSummary.objectiveHistory.toList}")
	trainingSummary.residuals.show()
	println(s"RMSE: ${trainingSummary.rootMeanSquaredError}")
	println(s"r2: ${trainingSummary.r2}")
	
	# SAVE THE MODEL IN BLOB STORAGE
	val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
	val modelName = "LinearRegression__"
	val filename = modelDir.concat(modelName).concat(datestamp)
	lrModel.save(filename);
	
	# PRINT COEFFICIENTS
	println(s"Coefficients: ${lrModel.coefficients} Intercept: ${lrModel.intercept}")
	
	# SCORE THE MODEL ON TEST DATA
	val predictions = lrModel.transform(OneHotTEST)
	
	# EVALUATE THE MODEL ON TEST DATA
	val evaluator = new RegressionEvaluator().setLabelCol("tip_amount").setPredictionCol("prediction").setMetricName("r2")
	val r2 = evaluator.evaluate(predictions)
	println("R-sqr on test data = " + r2)
	
	# GET TIME TO RUN THE CELL
	val endtime = Calendar.getInstance().getTime()
	val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
	println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**出力:**

上記のセルの実行に要した時間: 13 秒


	# LOAD A SAVED LINEARREGRESSION MODEL FROM BLOB STORAGE AND SCORE A TEST DATA SET. 

	# RECORD START TIME
	val starttime = Calendar.getInstance().getTime()
	
	# LOAD A SAVED LINEARREGRESSION MODEL FROM BLOB STORAGE
	val savedModel = org.apache.spark.ml.regression.LinearRegressionModel.load(filename)
	println(s"Coefficients: ${savedModel.coefficients} Intercept: ${savedModel.intercept}")
	
	# SCORE THE MODEL ON TEST DATA
	val predictions = savedModel.transform(OneHotTEST).select("tip_amount","prediction")
	predictions.registerTempTable("testResults")
	
	# EVALUATE THE MODEL ON TEST DATA
	val evaluator = new RegressionEvaluator().setLabelCol("tip_amount").setPredictionCol("prediction").setMetricName("r2")
	val r2 = evaluator.evaluate(predictions)
	println("R-sqr on test data = " + r2)
	
	# GET TIME TO RUN THE CELL
	val endtime = Calendar.getInstance().getTime()
	val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
	println("Time taken to run the above cell: " + elapsedtime + " seconds.");
	
	# PRINT RESULTS
	println("R-sqr on test data = " + r2)


**出力:**

テスト データの R-sqr = 0.5960320470835743


次に、データ フレームとしてテスト結果を照会し、Jupyter autoviz と Python matplotlib を使用して視覚化します。


	# SQL QUERY
	%%sql -q -o sqlResults
	select * from testResults

	# RUN THE CODE LOCALLY ON THE JUPYTER SERVER
	%%local

	# USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES. 
	# CLICK ON THE TYPE OF PLOT TO BE GENERATED (E.G. LINE, AREA, BAR ETC.)
	sqlResults

このコードでは、クエリの出力からローカル データ フレームを作成し、データをプロットします。`%%local` マジックでローカル データフレーム (`sqlResults`) を作成し、そのデータを matplotlib でプロットします。

>[AZURE.NOTE] この Spark マジックは、このチュートリアルで何度も使用しています。データの量が大きい場合はサンプリングして、ローカル メモリに収まるようにデータフレームを作成する必要があります。

Python matplotlib を使用してプロットを作成します。

	# RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
	%%local
	sqlResults
	%matplotlib inline
	import numpy as np
	
	# PLOT THE RESULTS
	ax = sqlResults.plot(kind='scatter', figsize = (6,6), x='tip_amount', y='prediction', color='blue', alpha = 0.25, label='Actual vs. predicted');
	fit = np.polyfit(sqlResults['tip_amount'], sqlResults['prediction'], deg=1)
	ax.set_title('Actual vs. Predicted Tip Amounts ($)')
	ax.set_xlabel("Actual")
	ax.set_ylabel("Predicted")
	#ax.plot(sqlResults['tip_amount'], fit[0] * sqlResults['prediction'] + fit[1], color='magenta')
	plt.axis([-1, 15, -1, 8])
	plt.show(ax)

**出力:**

![チップの金額: 実際の金額と予測金額](./media/machine-learning-data-science-process-scala-walkthrough/plot-actual-vs-predicted-tip-amount.png)


### 勾配ブースティング ツリー回帰モデルを作成する

ここでは、Spark の `ML GBTRegressor()` 関数を使用して勾配ブースティング ツリー回帰モデルを作成し、テスト データでモデルを評価します。

[勾配ブースティング ツリー](http://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) (GBT) は、複数のデシジョン ツリーをまとめたものです。GBT は、デシジョン ツリーを繰り返しトレーニングすることで損失関数を最小限に抑えます。GBT は回帰と分類に使用されます。カテゴリの特徴を処理できますが、特徴のスケーリングは不要であり、非線形性や特徴の相互作用をキャプチャできます。また、多クラス分類の設定にも使用できます。


	# RECORD START TIME
	val starttime = Calendar.getInstance().getTime()
	
	# TRAIN A GBT REGRESSION MODEL
	val gbt = new GBTRegressor().setLabelCol("label").setFeaturesCol("featuresCat").setMaxIter(10)
	val gbtModel = gbt.fit(indexedTRAINwithCatFeat)
	
	# MAKE PREDICTIONS
	val predictions = gbtModel.transform(indexedTESTwithCatFeat)
	
	# COMPUTE TEST SET R2
	val evaluator = new RegressionEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("r2")
	val Test_R2 = evaluator.evaluate(predictions)
	
	
	# GET TIME TO RUN THE CELL
	val endtime = Calendar.getInstance().getTime()
	val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
	println("Time taken to run the above cell: " + elapsedtime + " seconds.");
	
	# PRINT RESULTS
	println("Test R-sqr is: " + Test_R2);


**出力:**

テストの R-sqr = 0.7655383534596654



## 最適化のための高度なモデリング ユーティリティ

このセクションでは、モデルの最適化に頻繁に使用する ML ユーティリティの使用方法を説明します。具体的には、パラメーター スイープとクロス検証を使用して ML モデルを最適化する 3 種類の方法を紹介します。

1\. データをトレーニング セットと検証セットに分け、トレーニング セットではハイパーパラメーター スイープを使用してモデルを最適化し、検証セットでは評価を最適化する (線形回帰)

2\. Spark ML の CrossValidator 関数を使用し、クロス検証とハイパーパラメーター スイープを使用してモデルを最適化する (二項分類)

3\. ML 関数とパラメーター セットを利用する、クロス検証とパラメーター スイープのカスタム コードを使用してモデルを最適化する (線形回帰)


**クロス検証 (CV)** は、既知のデータセットでトレーニングされたモデルが、トレーニングに使用されていないデータセットの特徴の予測に対してどの程度汎用化されるかを評価する手法です。この手法のベースにある基本的な考え方は、既知のデータセットでモデルをトレーニングし、それとは別のデータセットで予測の精度をテストするというものです。この記事で使用する一般的な実装では、データセットを K 個のフォールドに分割し、1 つを除くすべてのフォールドを使用してラウンドロビン方式でモデルをトレーニングします。

**ハイパーパラメーター最適化**では、学習アルゴリズムでどのようなハイパーパラメーターのセットを選択するかを検討します。通常は、独立したデータセットでアルゴリズムのパフォーマンスの測定を最適化することを目的としています。**ハイパーパラメーター**の値は、モデルのトレーニング手順以外で指定する必要があります。この値に関する仮定によって、モデルの柔軟性と精度が影響を受けます。たとえば、デシジョン ツリーには、必要なツリーの深さやリーフの数といったハイパーパラメーターがあります。サポート ベクター マシン (SVM) の場合は、誤分類ペナルティ項を設定する必要があります。

ここでは、ハイパーパラメーターの最適化の実行に、グリッド探索または**パラメーター スイープ**と呼ばれる一般的な手法を使用します。この手法では、学習アルゴリズムのハイパーパラメーター空間のうち、指定されたサブセットを対象として、しらみつぶしに値が探索されます。クロス検証のパフォーマンス メトリックを使用すると、グリッド探索アルゴリズムによって生成される結果から最適なものを選別できます。CV をハイパーパラメーター スイープと組み合わせて使用することで、モデルがトレーニング データに過剰適合するなどの問題を抑制でき、結果として、トレーニング データの抽出元であるデータセット全般にモデルを適用するための容量が保持されます。

### ハイパーパラメーター スイープを使用して線形回帰モデルを最適化する

データをトレーニング セットと検証セットに分け、トレーニング セットではハイパーパラメーター スイープを使用してモデルを最適化し、検証セットでは評価を最適化します (線形回帰)。

	# RECORD START TIME
	val starttime = Calendar.getInstance().getTime()
	
	# RENAME tip_amount AS LABEL
	val OneHotTRAINLabeled = OneHotTRAIN.select("tip_amount","features").withColumnRenamed(existingName="tip_amount",newName="label") 
	val OneHotTESTLabeled = OneHotTEST.select("tip_amount","features").withColumnRenamed(existingName="tip_amount",newName="label")
	OneHotTRAINLabeled.cache()
	OneHotTESTLabeled.cache()
	
	# DEFINE THE ESTIMATOR FUNCTION: THE LinearRegression() FUNCTION
	val lr = new LinearRegression().setLabelCol("label").setFeaturesCol("features").setMaxIter(10)
	
	# DEFINE THE PARAMETER GRID
	val paramGrid = new ParamGridBuilder().addGrid(lr.regParam, Array(0.1, 0.01, 0.001)).addGrid(lr.fitIntercept).addGrid(lr.elasticNetParam, Array(0.1, 0.5, 0.9)).build()
	
	# DEFINE PIPELINE WITH TRAIN-TEST VALIDATION SPLIT, WITH 75% IN THE TRAIING SET, SPECIFY ESTIMATOR, EVALUATOR, AND PARAMETER GRID
	val trainPct = 0.75
	val trainValidationSplit = new TrainValidationSplit().setEstimator(lr).setEvaluator(new RegressionEvaluator).setEstimatorParamMaps(paramGrid).setTrainRatio(trainPct)
	
	# RUN THE TRAIN VALIDATION SPLIT AND CHOOSE THE BEST SET OF PARAMETERS
	val model = trainValidationSplit.fit(OneHotTRAINLabeled)
	
	# MAKE PREDICTIONS ON THE TEST DATA USING THE MODEL WITH THE COMBINATION OF PARAMETERS THAT PERFORM THE BEST
	val testResults = model.transform(OneHotTESTLabeled).select("label", "prediction")
	
	# COMPUTE TEST SET R2
	val evaluator = new RegressionEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("r2")
	val Test_R2 = evaluator.evaluate(testResults)
	
	# GET TIME TO RUN THE CELL
	val endtime = Calendar.getInstance().getTime()
	val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
	println("Time taken to run the above cell: " + elapsedtime + " seconds.");
	
	println("Test R-sqr is: " + Test_R2);


**出力:**

テストの R-sqr = 0.6226484708501209


### クロス検証とハイパーパラメーター スイープを使用して二項分類モデルを最適化する

ここでは、クロス検証とハイパーパラメーター スイープを使用して二項分類モデルを最適化する方法を示します。Spark ML の CrossValidator 関数を使用します。

	# RECORD START TIME
	val starttime = Calendar.getInstance().getTime()
	
	# CREATE DATA-FRAMES WITH PROPERLY LABELED COLUMNS FOR USE WITH TRAIN/TEST SPLIT
	val indexedTRAINwithCatFeatBinTargetRF = indexedTRAINwithCatFeatBinTarget.select("labelBin","featuresCat").withColumnRenamed(existingName="labelBin",newName="label").withColumnRenamed(existingName="featuresCat",newName="features")
	val indexedTESTwithCatFeatBinTargetRF = indexedTESTwithCatFeatBinTarget.select("labelBin","featuresCat").withColumnRenamed(existingName="labelBin",newName="label").withColumnRenamed(existingName="featuresCat",newName="features")
	indexedTRAINwithCatFeatBinTargetRF.cache()
	indexedTESTwithCatFeatBinTargetRF.cache()
	
	# DEFINE THE ESTIMATOR FUNCTION
	val rf = new RandomForestClassifier().setLabelCol("label").setFeaturesCol("features").setImpurity("gini").setSeed(1234).setFeatureSubsetStrategy("auto").setMaxBins(32)
	
	# DEFINE THE PARAMETER GRID
	val paramGrid = new ParamGridBuilder().addGrid(rf.maxDepth, Array(4,8)).addGrid(rf.numTrees, Array(5,10)).addGrid(rf.minInstancesPerNode, Array(100,300)).build()
	
	# SPECIFY THE NUMBER OF FOLDS
	val numFolds = 3
	
	# DEFINE THE TRAIN-TEST VALIDATION SPLIT WITH 75% IN THE TRAIING SET
	val CrossValidator = new CrossValidator().setEstimator(rf).setEvaluator(new BinaryClassificationEvaluator).setEstimatorParamMaps(paramGrid).setNumFolds(numFolds)
	
	# RUN THE TRAIN VALIDATION SPLIT AND CHOOSE THE BEST SET OF PARAMETERS
	val model = CrossValidator.fit(indexedTRAINwithCatFeatBinTargetRF)
	
	# MAKE PREDICTIONS ON THE TEST DATA USING THE MODEL WITH THE COMBINATION OF PARAMETERS THAT PERFORM THE BEST
	val testResults = model.transform(indexedTESTwithCatFeatBinTargetRF).select("label", "prediction")
	
	# COMPUTE TEST F1 SCORE
	val evaluator = new MulticlassClassificationEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("f1")
	val Test_f1Score = evaluator.evaluate(testResults)
	
	# GET TIME TO RUN THE CELL
	val endtime = Calendar.getInstance().getTime()
	val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
	println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**出力:**

上記のセルの実行に要した時間: 33 秒


### クロス検証とパラメーター スイープのカスタム コードを使用して線形回帰モデルを最適化する

ここでは、カスタム コードを使用してモデルを最適化し、最高精度の基準を使用して最適なモデル パラメーターを特定します。次に、最終的なモデルを作成し、テスト データでモデルを評価して、BLOB ストレージにモデルを保存します。最後に、モデルを読み込み、テスト データをスコア付けして、モデルの精度を評価します。

	# RECORD START TIME
	val starttime = Calendar.getInstance().getTime()
	
	# DEFINE PARAMETER GRID AND THE NUMBER OF FOLDS
	val paramGrid = new ParamGridBuilder().addGrid(rf.maxDepth, Array(5,10)).addGrid(rf.numTrees, Array(10,25,50)).build()
	
	val nFolds = 3
	val numModels = paramGrid.size
	val numParamsinGrid = 2
	
	# SPECIFY THE NUMBER OF CATEGORIES FOR THE CATEGORIAL VARIBLES
	val categoricalFeaturesInfo = Map[Int, Int]((0,2),(1,2),(2,6),(3,4))
	
	var maxDepth = -1
	var numTrees = -1
	var param = ""
	var paramval = -1
	var validateLB = -1.0
	var validateUB = -1.0
	val h = 1.0 / nFolds;
	val RMSE  = Array.fill(numModels)(0.0)
	
	# CREATE K FOLDS
	val splits = MLUtils.kFold(indexedTRAINbinary, numFolds = nFolds, seed=1234)
	
	
	# LOOP THROUGH K-FOLDS AND THE PARAMETER GRID TO GET AND IDENTIFY THE BEST PARAMETER SET BY ACCURACY
	for (i <- 0 to (nFolds-1)) {
	    validateLB = i * h
	    validateUB = (i + 1) * h
	    val validationCV = trainData.filter($"rand" >= validateLB  && $"rand" < validateUB)
	    val trainCV = trainData.filter($"rand" < validateLB  || $"rand" >= validateUB)
	    val validationLabPt = validationCV.rdd.map(r => LabeledPoint(r.getDouble(targetIndRegression(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)));
	    val trainCVLabPt = trainCV.rdd.map(r => LabeledPoint(r.getDouble(targetIndRegression(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)));
	    validationLabPt.cache()
	    trainCVLabPt.cache()
	
	    for (nParamSets <- 0 to (numModels-1)) {
	        for (nParams <- 0 to (numParamsinGrid-1)) {
	            param = paramGrid(nParamSets).toSeq(nParams).param.toString.split("__")(1)
	            paramval = paramGrid(nParamSets).toSeq(nParams).value.toString.toInt
	            if (param == "maxDepth") {maxDepth = paramval}
	            if (param == "numTrees") {numTrees = paramval}
	        }
	        val rfModel = RandomForest.trainRegressor(trainCVLabPt, categoricalFeaturesInfo=categoricalFeaturesInfo,
	                                                  numTrees=numTrees, maxDepth=maxDepth,
	                                                  featureSubsetStrategy="auto",impurity="variance", maxBins=32)
	        val labelAndPreds = validationLabPt.map { point =>
	                                                 val prediction = rfModel.predict(point.features)
	                                                 ( prediction, point.label )
	                                                }
	        val validMetrics = new RegressionMetrics(labelAndPreds)
	        val rmse = validMetrics.rootMeanSquaredError
	        RMSE(nParamSets) += rmse
	    }
	    validationLabPt.unpersist();
	    trainCVLabPt.unpersist();
	}
	val minRMSEindex = RMSE.indexOf(RMSE.min)
	
	# GET BEST PARAMETERS FROM CV AND PARAMETER SWEEP 
	var best_maxDepth = -1
	var best_numTrees = -1
	for (nParams <- 0 to (numParamsinGrid-1)) {
	    param = paramGrid(minRMSEindex).toSeq(nParams).param.toString.split("__")(1)
	    paramval = paramGrid(minRMSEindex).toSeq(nParams).value.toString.toInt
	    if (param == "maxDepth") {best_maxDepth = paramval}
	    if (param == "numTrees") {best_numTrees = paramval}
	}
	
	# CREATE BEST MODEL WITH BEST PARAMETERS AND FULL TRAIING DATASET
	val best_rfModel = RandomForest.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo,
	                                                  numTrees=best_numTrees, maxDepth=best_maxDepth,
	                                                  featureSubsetStrategy="auto",impurity="variance", maxBins=32)
	
	# SAVE BEST RF MODEL IN BLOB STORAGE
	val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
	val modelName = "BestCV_RF_Regression__"
	val filename = modelDir.concat(modelName).concat(datestamp)
	best_rfModel.save(sc, filename);
	
	# PREDICT ON TRAINING SET WITH BEST MODEL AND EVALUATE
	val labelAndPreds = indexedTESTreg.map { point =>
	                                        val prediction = best_rfModel.predict(point.features)
	                                        ( prediction, point.label )
	                                       }
	
	val test_rmse = new RegressionMetrics(labelAndPreds).rootMeanSquaredError
	val test_rsqr = new RegressionMetrics(labelAndPreds).r2
	
	# GET TIME TO RUN THE CELL
	val endtime = Calendar.getInstance().getTime()
	val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
	println("Time taken to run the above cell: " + elapsedtime + " seconds.");


	# LOAD THE MODEL
	val savedRFModel = RandomForestModel.load(sc, filename)
	
	val labelAndPreds = indexedTESTreg.map { point =>
	                                        val prediction = savedRFModel.predict(point.features)
	                                        ( prediction, point.label )
	                                       }
	# TEST THE MODEL
	val test_rmse = new RegressionMetrics(labelAndPreds).rootMeanSquaredError
	val test_rsqr = new RegressionMetrics(labelAndPreds).r2


**出力:**

上記のセルの実行に要した時間: 61 秒


## Scala を使用して Spark で構築した ML モデルを自動的に使用する

Scala コードを使用して、Spark で構築され、Azure BLOB に保存されている ML モデルで新しいデータセットを自動的に読み込んでスコア付けする手順は、「[Spark で構築した機械学習モデルのスコア付け](machine-learning-data-science-spark-model-consumption.md)」に記載されています。ユーザーは、そこに記載されている手順に従い、Python コードを前述の Scala コードに置き換えるだけで使用を自動化できます。

## 参照トピック

Azure でのデータ サイエンス プロセスを構成するタスクについて説明したトピックの概要については、[Team Data Science Process](http://aka.ms/datascienceprocess) に関するページをご覧ください。

それぞれ**特定のシナリオ**の Team Data Science Process の手順を示す、他のエンド ツー エンド チュートリアルの説明と、クラウドおよびオンプレミスのツールとサービスをワークフローまたはパイプラインに組み込んで、インテリジェントなアプリケーションを作成する方法については、「[Team Data Science Process のチュートリアル](data-science-process-walkthroughs.md)」をご覧ください。

<!---HONumber=AcomDC_0803_2016-->