---
title: Apache Spark を使用したデータの視覚化
description: Apache Spark と Azure Synapse Analytics Notebooks を使用して高度なデータの視覚化を作成する
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: machine-learning
ms.date: 10/20/2020
ms.author: midesa
ms.openlocfilehash: 820dd8b607f5ec2fdc44d25063e0a941f76237ad
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/25/2020
ms.locfileid: "95919204"
---
# <a name="analyze-data-with-apache-spark"></a>Apache Spark を使用してデータを分析する

このチュートリアルでは、Azure Open Datasets と Apache Spark を使用して探索的データ分析を実行し、Azure Synapse Studio ノートブックで結果を視覚化する方法について説明します。

特に、[ニューヨーク市 (NYC) のタクシー](https://azure.microsoft.com/en-us/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/)のデータセットを分析します。 データは Azure Open Datasets から入手できます。 このデータセットのサブセットには、イエロー タクシー乗車に関する情報が格納され、各乗車、出発と到着の時刻、場所、料金、その他の関心の高い属性に関する情報が含まれます。
  
## <a name="before-you-begin"></a>開始する前に
- [Apache Spark プールの作成チュートリアル](../articles/../quickstart-create-apache-spark-pool-studio.md)に従って、Apache Spark プールを作成します 

## <a name="download-and-prepare-the-data"></a>データのダウンロードと準備
1. PySpark カーネルを使用してノートブックを作成します。 手順については、「[ノートブックを作成する](https://docs.microsoft.com/azure/synapse-analytics/quickstart-apache-spark-notebook#create-a-notebook)」を参照してください。 
   
> [!Note]
> 
> PySpark カーネルであるため、コンテキストを明示的に作成する必要はありません。 最初のコード セルを実行すると、Spark コンテキストが自動的に作成されます。
>

2. このチュートリアルでは、データセットの視覚化に役立ついくつかの異なるライブラリを使用します。 この分析を行うには、次のライブラリをインポートする必要があります。 

```python
import matplotlib.pyplot as plt
import seaborn as sns
import pandas as pd
```

3. 生データは Parquet 形式であるため、Spark コンテキストを使用して、ファイルをデータフレームとして、直接メモリにプルできます。 Open Datasets API を使用してデータを取得することにより、Spark データフレームを作成します。 ここでは、Spark データフレームの "*読み取り時のスキーマ*" プロパティを使用してデータ型とスキーマを推論します。

```python
from azureml.opendatasets import NycTlcYellow
from datetime import datetime
from dateutil import parser

end_date = parser.parse('2018-06-06')
start_date = parser.parse('2018-05-01')
nyc_tlc = NycTlcYellow(start_date=start_date, end_date=end_date)
df = nyc_tlc.to_spark_dataframe()
```

4. データが読み込まれたら、データセットの不要な部分を取り除くために、初期フィルター処理を実行します。 不要な列を削除したり、重要な情報を抽出する列を追加したりする場合があります。 また、データセット内の異常をフィルターで除去します。

```python
# Filter the dataset 
from pyspark.sql.functions import *

filtered_df = df.select('vendorID', 'passengerCount', 'tripDistance','paymentType', 'fareAmount', 'tipAmount'\
                                , date_format('tpepPickupDateTime', 'hh').alias('hour_of_day')\
                                , dayofweek('tpepPickupDateTime').alias('day_of_week')\
                                , dayofmonth(col('tpepPickupDateTime')).alias('day_of_month'))\
                            .filter((df.passengerCount > 0)\
                                & (df.tipAmount >= 0)\
                                & (df.fareAmount >= 1) & (df.fareAmount <= 250)\
                                & (df.tripDistance > 0) & (df.tripDistance <= 200))

filtered_df.createOrReplaceTempView("taxi_dataset")
```

## <a name="analyze-data"></a>データを分析する
 データ アナリストとして、データからの分析情報の抽出に役立つ幅広いツールを利用できます。 チュートリアルのこの部分では、Azure Synapse Analytics Notebooks 内で利用できる便利なツールをいくつか紹介します。 この分析では、選択した期間についてより多くのタクシーのチップが提供される要因を把握します。

###  <a name="apache-spark-sql-magic"></a>Apache Spark SQL マジック 
まず、Synapse Notebook で Apache Spark SQL とマジック コマンドによって探索的データ分析を実行します。 クエリを実行したら、組み込みの ```chart options``` 機能を使用して結果を視覚化します。 

1. ノートブック内に新しいセルを作成し、下のコードをコピーします。 このクエリを使用して、選択した期間にわたって、平均チップ金額がどのように変化したかを把握します。 また、このクエリを使用すると、1 日あたりの最低および最高のチップ金額や平均運賃など、その他の有用な分析情報を特定することもできます。
   
```sql
%%sql
SELECT 
    day_of_month
    , MIN(tipAmount) AS minTipAmount
    , MAX(tipAmount) AS maxTipAmount
    , AVG(tipAmount) AS avgTipAmount
    , AVG(fareAmount) as fareAmount
FROM taxi_dataset 
GROUP BY day_of_month
ORDER BY day_of_month ASC
```

2. クエリの実行が完了したら、**グラフ ビュー** に切り替えて結果を視覚化できます。 この例では、```day_of_month``` フィールドを **[キー]** として、```avgTipAmount``` を **[値]** として指定して、**折れ線グラフ** を作成します。 選択を行ったら、 **[適用]** をクリックしてグラフを更新します。 
   
## <a name="visualize-data"></a>データの視覚化
組み込みの Notebook グラフ作成オプションに加えて、人気のあるオープンソース ライブラリを使用して独自の視覚化を作成することもできます。 次の例では、データの視覚化に一般的に使用される Python ライブラリである Seaborn と Matplotlib を使用します。 

> [!Note]
> 
> 既定では、どの Azure Synapse Analytics Apache Spark プールにも、一般的に使用されるライブラリと既定のライブラリのセットが含まれています。 [Azure Synapse ランタイム](../spark/apache-spark-version-support.md)に関する記事でライブラリの全一覧を表示できます。 ドキュメントを参照してください。 さらに、サード パーティのコードまたはローカル環境でビルドされたコードをアプリケーションで使用できるようにするには、いずれかの Spark プール (プレビュー) に[ライブラリをインストール](../spark/apache-spark-azure-portal-add-libraries.md)します。
>

1. 開発を容易にし、コストを削減するために、データセットをダウンサンプリングします。 組み込みの Apache Spark サンプリング機能を使用します。 さらに、Seaborn と Matplotlib の両方に、Pandas データフレームまたは Numpy 配列が必要です。 Pandas データフレームを取得するには、```toPandas()``` コマンドを使用してデータフレームを変換します。

```python
# To make development easier, faster and less expensive down sample for now
sampled_taxi_df = filtered_df.sample(True, 0.001, seed=1234)

# The charting package needs a Pandas dataframe or numpy array do the conversion
sampled_taxi_pd_df = sampled_taxi_df.toPandas()
```

1. まず、データセット内のチップの分布について把握します。 Matplotlib を使用して、チップの金額と数の分布を示すヒストグラムを作成します。 分布に基づき、チップが 10 ドル以下の金額に偏っていることがわかります。

```python
# Look at tips by count histogram using Matplotlib

ax1 = sampled_taxi_pd_df['tipAmount'].plot(kind='hist', bins=25, facecolor='lightblue')
ax1.set_title('Tip amount distribution')
ax1.set_xlabel('Tip Amount ($)')
ax1.set_ylabel('Counts')
plt.suptitle('')
plt.show()
```

![チップのヒストグラム](./media/apache-spark-machine-learning-mllib-notebook/histogram.png)

1. 次に、特定の乗車におけるチップと曜日の関係を把握します。 Seaborn を使用して、各曜日の傾向をまとめた箱ひげ図を作成します。 

```python
# View the distribution of tips by day of week using Seaborn
ax = sns.boxplot(x="day_of_week", y="tipAmount",data=sampled_taxi_pd_df, showfliers = False)
ax.set_title('Tip amount distribution per day')
ax.set_xlabel('Day of Week')
ax.set_ylabel('Tip Amount ($)')
plt.show()

```
![曜日ごとのチップの分布](./media/apache-spark-data-viz/data-analyst-tutorial-per-day.png)

4. ここで、もう一つの仮説として、乗客数とタクシーの合計チップ金額の間に正の関係があるということが考えられます。 この関係を検証するために、次のコードを実行して、乗客数ごとのチップの分布を示す箱ひげ図を生成します。 
   
```python
# How many passengers tipped by various amounts 
ax2 = sampled_taxi_pd_df.boxplot(column=['tipAmount'], by=['passengerCount'])
ax2.set_title('Tip amount by Passenger count')
ax2.set_xlabel('Passenger count')
ax2.set_ylabel('Tip Amount ($)')
ax2.set_ylim(0,30)
plt.suptitle('')
plt.show()
```
![箱ひげ図](./media/apache-spark-machine-learning-mllib-notebook/box-whisker-plot.png)

1. 最後に、運賃とチップ金額の関係を把握します。 結果に基づき、乗客がチップを払わないケースが複数あることを確認できます。 しかし、運賃全体とチップ金額の間に正の関係があることも確認できます。
   
```python
# Look at the relationship between fare and tip amounts

ax = sampled_taxi_pd_df.plot(kind='scatter', x= 'fareAmount', y = 'tipAmount', c='blue', alpha = 0.10, s=2.5*(sampled_taxi_pd_df['passengerCount']))
ax.set_title('Tip amount by Fare amount')
ax.set_xlabel('Fare Amount ($)')
ax.set_ylabel('Tip Amount ($)')
plt.axis([-2, 80, -2, 20])
plt.suptitle('')
plt.show()
```
![散布図](./media/apache-spark-machine-learning-mllib-notebook/scatter.png)

## <a name="shut-down-the-spark-instance"></a>Spark インスタンスをシャットダウンする

アプリケーションの実行が完了したら、ノートブックをシャットダウンして、リソースを解放します。そのためには、タブを閉じるか、ノートブックの下部にある状態パネルから **[セッションの終了]** を選択します。

## <a name="see-also"></a>関連項目

- [概要:Azure Synapse Analytics での Apache Spark](apache-spark-overview.md)
- [Apache SparkML を使用して機械学習モデルを構築する](../spark/apache-spark-machine-learning-mllib-notebook.md)

## <a name="next-steps"></a>次のステップ

- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
- [Apache Spark 公式ドキュメント](https://spark.apache.org/docs/latest/)