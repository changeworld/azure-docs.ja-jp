<properties
	pageTitle="Azure HDInsight 上の Spark を使用したデータ サイエンスの概要| Microsoft Azure"
	description="Spark MLlib ツールキットによって、かなりの Machine Learning モデリング機能がこの分散 HDInsight 環境にもたらされます。"
	services="machine-learning"
	documentationCenter=""
	authors="bradsev"
	manager="paulettm"
	editor="cgronlun"  />

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/18/2016"
	ms.author="bradsev" />

# Azure HDInsight 上の Spark を使用したデータ サイエンスの概要

[AZURE.INCLUDE [machine-learning-spark-modeling](../../includes/machine-learning-spark-modeling.md)]

## はじめに

Azure HDInsight 上の Spark は、メモリ内のデータを操作しながら、そのデータを HDInsight (Hadoop) クラスターで分散して処理できます。したがって、この Spark クラスターは、速さと容量を兼ね備えています。さらに、Azure Blob (WASB) に格納されているデータを変換、フィルター処理、および視覚化するために Spark クラスターで Spark SQL の対話型クエリを実行できる Jupyter Notebook のサポートも含まれます。

[Spark](http://spark.apache.org/) はビッグ データ分析アプリケーションのパフォーマンスを向上するメモリ内の処理をサポートする、オープン ソースの並列処理のフレームワークです。Spark 処理エンジンは、速度、使いやすさ、高度な分析用に作成されています。Spark のメモリ内の計算能力により、機械学習とグラフ計算における反復的なアルゴリズムに対して、Spark は適切な選択肢となります。[MLlib](http://spark.apache.org/mllib/) は Spark のスケーラブルな Machine Learning ライブラリです。

HDInsight Spark は、Azure でホストされるオープン ソースの Spark オファリングです。セットアップ手順とこのチュートリアルで示すコードは、HDInsight Spark 向けです。ただし、コードはジェネリックであり、すべての Spark クラスター上で動作します。HDInsight Spark を使用していない場合、クラスターのセットアップと管理の手順は、ここに記載されている内容と若干異なります。

Spark MLlib ツールキットによって、かなりの Machine Learning (ML) モデリング機能がこの分散環境にもたらされます。メニューでリンクされている一連のトピックでは、2013 年 NYC タクシー乗車および料金データセットのサンプルでデータ探索を実行し、二項分類および回帰モデリング タスクを実行して、結果を WASB に格納する処理を紹介しています。構築するモデルは、ロジスティック回帰と線形回帰、ランダム フォレスト、勾配ブースティング ツリーです。さらに、これらのモデル結果をどのように使用して、WASB に格納されている他のデータセットに対してこれらにスコアを付け、評価するかの方法についても紹介しています。より高度なトピックでは、クロス検証とハイパー パラメーター スイープを使用してモデルをトレーニングする方法について説明しています。

これらのトピックにあるモデリング手順には、各種モデルをトレーニング、評価、保存、および使用する方法を示すコードも含まれています。ソリューションのコーディングと関連するプロットの表示には Python が使用されており、Spark クラスターにインストールされている Jupyter Notebook で実行できる Python Notebook が提供されています。


## 前提条件

1\. これらのトピックを開始する前に、Azure サブスクリプションが必要です。Azure サブスクリプションがない場合は、[Azure 無料試用版の取得](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)に関するページを参照してください。

2\. HDInsight Spark クラスターを作成するには、「[概要: Azure HDInsight での Apache Spark の作成](../hdinsight/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql.md)」を参照してください。


[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]


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

Azure ポータルから Jupyter Notebook を起動することができます。 Spark クラスターを見つけてクリックし、詳細なクラスター管理ページに移動します。**[クラスター ダッシュ ボード]** をクリックします。ここでは、Jupyter Notebook のアイコンは Spark クラスターに関連付けられています。

![](./media/machine-learning-data-science-spark-overview/spark-jupyter-on-portal.png)

******https://CLUSTERNAME.azurehdinsight.net/jupyter*** を参照して、Jupyter Notebook にアクセスすることもできます。ノートブックにアクセスするには、管理者アカウントのパスワードが必要です。

![](./media/machine-learning-data-science-spark-overview/spark-jupyter-notebook.png)

Python に移動して、Python スクリプトを実行する既存のノートブックを確認します。パッケージ済みのノートブックのサンプルがいくつか格納されているディレクトリが表示されます。このトピックのコード サンプルが含まれるノートブックは、[Github](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/Python) で利用できます。

Github から、直接ノートブックを Spark クラスター上の Jupyter Notebook サーバーにアップロードできます。Jupyter のホーム ページで、画面の右側にある **[アップロード]** ボタンをクリックします。ファイル エクスプローラーが開きます。ここで、ノートブックの Github (raw コンテンツ) URL を貼り付けることができます。次に、**[開く]** をクリックします。Jupyter ファイルの一覧にファイル名が **[アップロード]** ボタンとともに表示されます。この **[アップロード]** ボタンをクリックします。これで、ノートブックがインポートされます。このチュートリアルの次のノートブックをアップロードするには、この手順を繰り返します。

1.	machine-learning-data-science-spark-data-exploration-modeling.ipynb
2.	machine-learning-data-science-spark-model-consumption.ipynb
3.	machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb

ここでは、次の操作を実行できます。

- Notebook をクリックしてコードを参照する
- **Shift キーを押しながら Enter キー**を押すことで各セルを実行する
- **[Cell]** (セル)、**[Run]** (実行) の順にクリックして Notebook 全体を実行する


## 次の手順

HDInsight Spark クラスターが設定され、Jupyter Notebook がアップロードされると、これら 3 つのノートブックに関連するトピック （クロス検証を使用した作成、使用、モデル方法およびハイパー パラメーター スイープ） を処理できます。

**モデルの作成:**「[Create binary classification and regression models for data with the Spark MLlib toolkit (Spark MLlib ツールキットを使用したデータの二項分類と回帰モデルの作成)](machine-learning-data-science-spark-data-exploration-modeling.md)」で、スコア付けおよび評価される Machine Learning モデルを作成します。

**モデルの使用:** このトピックで作成した分類モデルと回帰モデルにスコアを付け、評価する方法については、「[Score and evaluate Spark-built machine learning models (Spark で構築した機械学習モデルのスコア付けと評価)](machine-learning-data-science-spark-model-consumption.md)」参照してください。

<!---HONumber=AcomDC_0420_2016-->