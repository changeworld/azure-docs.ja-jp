<properties
	pageTitle="Azure HDInsight 上の Spark を使用したデータ サイエンスの概要| Microsoft Azure"
	description="Spark MLlib ツールキットによって、かなりの Machine Learning モデリング機能がこの分散 HDInsight 環境にもたらされます。"
	services="machine-learning"
	documentationCenter=""
	authors="bradsev"
	manager="jhubbard"
	editor="cgronlun"  />

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/14/2016"
	ms.author="deguhath;bradsev;gokuma" />

# Azure HDInsight 上の Spark を使用したデータ サイエンスの概要

[AZURE.INCLUDE [machine-learning-spark-modeling](../../includes/machine-learning-spark-modeling.md)]

この一連のトピックでは、データの取り込み、特徴エンジニアリング、モデリング、モデル評価など、データ サイエンスの一般的な作業を HDInsight Spark を使用して行う方法について説明しています。ここで使用しているデータは、2013 年における NYC タクシーの乗車と料金のデータセットから抽出したサンプルです。構築されるモデルには、ロジスティック回帰と線形回帰、ランダム フォレスト、勾配ブースティング ツリーなどがあります。これらのモデルを Azure Blob Storage (WASB) に保存する方法や、その予測パフォーマンスをスコア付けして評価する方法も、一連のトピックの中で紹介します。クロス検証とハイパーパラメーター スイープを使用してモデルをトレーニングする方法については、高度な内容を扱った別のトピックで説明します。また後続の 3 つのチュートリアルの手順を踏襲するために必要な Spark クラスターのセットアップ方法についても、こちらの「概要」のトピックで取り上げます。

[Spark](http://spark.apache.org/) はビッグ データ分析アプリケーションのパフォーマンスを向上するメモリ内の処理をサポートする、オープン ソースの並列処理のフレームワークです。Spark 処理エンジンは、速度、使いやすさ、高度な分析用に作成されています。Spark のメモリ内の分散計算機能により、機械学習とグラフ計算における反復的なアルゴリズムに対して、Spark は適切な選択肢となります。[MLlib](http://spark.apache.org/mllib/) はスケーラブルな Spark の Machine Learning ライブラリであり、これによってモデリング機能がこの分散環境にもたらされます。

[HDInsight Spark](../hdinsight/hdinsight-apache-spark-overview.md) は、Azure でホストされるオープンソースの Spark サービスです。さらに、Azure BLOB (WASB) に格納されているデータを変換、フィルター処理、および視覚化するために Spark クラスターで Spark SQL の対話型クエリを実行できる **Jupyter PySpark ノートブック**のサポートも含まれます。PySpark は、Spark の Python API です。ここで各種の手法を紹介したりデータを視覚化するためのプロットを表示したりするために使用しているコード スニペットは、Spark クラスターにインストールされた Jupyter Notebook で実行しています。これらのトピックにあるモデリング手順には、各種モデルをトレーニング、評価、保存、および使用する方法を示すコードも含まれています。

セットアップ手順とこのチュートリアルで示すコードは、HDInsight 3.4 Spark 1.6 向けです。ただし、このページとノートブックに記載しているコードは汎用性があり、すべての Spark クラスター上で動作します。HDInsight Spark を使用していない場合、クラスターのセットアップと管理の手順は、ここに記載されている内容と若干異なります。

## 前提条件

1\. これらのトピックを開始する前に、Azure サブスクリプションが必要です。Azure サブスクリプションがない場合は、[Azure 無料試用版の取得](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)に関するページを参照してください。

2\. このチュートリアルを実行するためには HDInsight 3.4 Spark 1.6 クラスターが必要です。クラスターを作成するには、[Azure HDInsight での Apache Spark クラスターの作成](../hdinsight/hdinsight-apache-spark-jupyter-spark-sql.md)に関するページの手順を参照してください。クラスターの種類とバージョンは、**[クラスターの種類の選択]** メニューから指定します。


![](./media/machine-learning-data-science-spark-overview/spark-cluster-on-portal.png)

<!-- -->

> [AZURE.NOTE] Python ではなく Scala を使用して、エンド ツー エンドのデータ サイエンス プロセスのタスクを実行する方法については、「[Scala および Azure 上の Spark を使用したデータ サイエンス](machine-learning-data-science-process-scala-walkthrough.md)」をご覧ください。

<!-- -->

>[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]


## 2013 年 NYC タクシー データ

NYC タクシー乗車データは、約 20 GB の圧縮されたコンマ区切り値 (CSV) ファイル (非圧縮で最大 48 GB) です。1 億 7300 万以上の個々の乗車と、各乗車に支払われた料金で構成されています。各旅行レコードには、pickup (乗車) と drop-off (降車) の場所と時間、匿名化されたタクシー運転手の (運転) 免許番号、および medallion (タクシーの一意の ID) 番号が含まれています。データには 2013 年のすべての乗車が含まれ、データは月ごとに次の 2 つのデータセットに用意されています。

1. 'trip\_data' CSV ファイルには、乗車の詳細 (乗客数、乗車地点、降車地点、乗車時間、乗車距離など) が含まれています。いくつかのサンプル レコードを次に示します。

		medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
		89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868

2. 'trip\_fare' CSV ファイルには、各乗車に対して支払われた料金の詳細 (支払いの種類、料金、追加料金、税、チップ、道路などの通行料、合計支払金額など) が含まれます。いくつかのサンプル レコードを次に示します。

		medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
		89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5


trip\_data と trip\_fare を結合するための一意のキーは medallion、hack\_licence、pickup\_datetime の各フィールドで構成されています。


これらのファイルから 0.1% のサンプルを取得し、これらのサンプルを、このチュートリアルの入力データセットとして使用される 1 つのデータセットに結合します。trip\_data と trip\_fare を結合するための一意のキーは medallion、hack\_licence、pickup\_datetime の各フィールドで構成されています。データセットの各レコードには、NYC タクシー乗車を表す次の属性が含まれています。


|フィールド| 簡単な説明
|------|---------------------------------
| medallion |匿名化されたタクシーのメダリオン (タクシーの一意の ID)
| hack\_license |	匿名化された Hackney Carriage の免許番号
| vendor\_id |	タクシー ベンダーの ID
| rate\_code | NYC タクシーの料金レート
| store\_and\_fwd\_flag | 格納および転送フラグ
| pickup\_datetime |	乗車の日付と時間
| dropoff\_datetime | 降車の日付と時間
| pickup\_hour |	乗車の時間帯
| pickup\_week |	乗車の年の通算週
| weekday |	曜日 (1 - 7 の範囲)
| passenger\_count |	タクシー乗車の乗客数
| trip\_time\_in\_secs | 乗車時間 (秒)
| trip\_distance | 乗車距離 (マイル)
| pickup\_longitude | 乗車経度
| pickup\_latitude |	乗車緯度
| dropoff\_longitude | 降車経度
| dropoff\_latitude | 降車緯度
| direct\_distance |	乗車場所と降車場所間の直線距離
| payment\_type | 支払の種類 (cas、クレジット カードなど)
| fare\_amount | 料金
| surcharge | 追加料金
| mta\_tax |	MTA 税
| tip\_amount | チップの金額
| tolls\_amount | 通行料金
| total\_amount | 合計金額
| tipped | チップ (ない場合は 0、ある場合は 1)
| tip\_class | チップのクラス (0: $0、1: $0 - 5、2: $6 - 10、3: $11 - 20、4: $20 以上)


## Spark クラスターで、Jupyter Notebook からのコードを実行する 

Jupyter Notebook は Azure ポータルから起動できます。ダッシュボードでご利用の Spark クラスターを見つけてクリックし、クラスターの管理ページにアクセスします。**[クラスター ダッシュボード]**、**[Jupyter Notebook]** の順にクリックして、Spark クラスターに関連付けられている Notebook を開きます。

![](./media/machine-learning-data-science-spark-overview/spark-jupyter-on-portal.png)

***https://CLUSTERNAME.azurehdinsight.net/jupyter*** を参照して、Jupyter Notebook にアクセスすることもできます。この URL の CLUSTERNAME の部分は、実際のクラスターの名前に置き換えてください。ノートブックにアクセスするには、管理者アカウントのパスワードが必要です。

![](./media/machine-learning-data-science-spark-overview/spark-jupyter-notebook.png)

[PySpark] を選択すると表示されるディレクトリには、PySpark API を使用する、あらかじめパッケージ化された Notebook の例が含まれています。Spark に関連する一連のトピックのコード サンプルが含まれた Notebook は [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/pySpark) で入手できます。


Github から、直接ノートブックを Spark クラスター上の Jupyter Notebook サーバーにアップロードできます。Jupyter のホーム ページで、画面の右側にある **[アップロード]** ボタンをクリックします。ファイル エクスプローラーが開きます。ここで、Notebook の GitHub (raw コンテンツ) URL を貼り付け、**[開く]** をクリックします。PySpark ノートブックには、次の URL でアクセスできます。

1.	[pySpark-machine-learning-data-science-spark-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/pySpark-machine-learning-data-science-spark-data-exploration-modeling.ipynb)
2.	[pySpark-machine-learning-data-science-spark-model-consumption.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/pySpark-machine-learning-data-science-spark-model-consumption.ipynb)
3.	[pySpark-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/pySpark-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb)

Jupyter ファイルの一覧にファイル名が **[アップロード]** ボタンと共に表示されます。この **[アップロード]** ボタンをクリックします。これで、ノートブックがインポートされます。このチュートリアルの次のノートブックをアップロードするには、この手順を繰り返します。

> [AZURE.TIP] ブラウザーの下部にあるリンクを右クリックし、**[リンクのコピー]** を選択すると、Jupyter アップロード ファイル エクスプローラーのダイアログ ボックスに貼り付けることができる GitHub の raw コンテンツ URL を取得できます。

ここでは、次の操作を実行できます。

- Notebook をクリックしてコードを参照する
- **Shift キーを押しながら Enter キー**を押して各セルを実行する
- **[Cell]**、**[Run]** の順にクリックして Notebook 全体を実行する
- クエリの自動視覚化を使用する

> [AZURE.TIP] PySpark カーネルは、SQL (HiveQL) のクエリの出力を自動的に視覚化します。視覚化にはいくつかの種類 (表、円グラフ、折れ線グラフ、面グラフ、棒グラフ) があり、Notebook の **[Type (タイプ)]** メニュー ボタンで選択できます。

![Logistic regression ROC curve for generic approach](./media/machine-learning-data-science-spark-overview/pyspark-jupyter-autovisualization.png)

## 次の手順

HDInsight Spark クラスターが設定され、Jupyter Notebook がアップロードされると、これら 3 つの PySpark ノートブックに関連するトピック (データの探索方法、モデルの作成および使用方法) を処理できます。高度なデータの探索と Notebook のモデリングでは、クロス検証、ハイパー パラメーター スイープやモデルの評価などに深く踏み込みます。

**Spark を使用したデータ探索およびモデリング:** [Spark MLlib ツールキットを使用したデータの二項分類と回帰モデルの作成](machine-learning-data-science-spark-data-exploration-modeling.md)に関するトピックで、データセットの詳細を確認し、スコア付けおよび評価される Machine Learning モデルを作成します。

**モデルの使用:** このトピックで作成した分類モデルと回帰モデルにスコアを付ける方法については、[Spark で構築した機械学習モデルのスコア付けと評価](machine-learning-data-science-spark-model-consumption.md)に関するページをご覧ください。

**クロス検証とハイパーパラメーター スイープ**: クロス検証とハイパーパラメーター スイープを使用したモデルのトレーニング方法については、「[Spark を使用した高度なデータ探索とモデリング](machine-learning-data-science-spark-advanced-data-exploration-modeling.md)」をご覧ください。

<!---HONumber=AcomDC_0921_2016-->