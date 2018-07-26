---
title: Azure Machine Learning Package for Forecasting を使用して予測モデルを構築して展開する
description: Azure Machine Learning Package for Forecasting を使用して、予測モデルを構築、トレーニング、テスト、展開する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: mattcon
author: matthewconners
ms.date: 07/13/2018
ms.openlocfilehash: 60eecf134f067d68326fc23ade8ed2a5a7ae7ac4
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/17/2018
ms.locfileid: "39070342"
---
# <a name="build-and-deploy-forecasting-models-with-azure-machine-learning"></a>Azure Machine Learning で予測モデルを構築して配置する

この記事では、**Azure Machine Learning Package for Forecasting** (AMLPF) を使用して、方法予測モデルを短時間で構築して配置する方法について説明します。 ワークフローは次のとおりです。

1. データを読み込んで探索する
2. 特徴を作成する
3. トレーニングを行って最善のモデルを選択する
4. モデルを配置して Web サービスを使用する

各モジュールとクラスの詳細なリファレンスと、トランスフォーマーとモデルの完全な一覧については、[パッケージ リファレンス ドキュメント](https://aka.ms/aml-packages/forecasting)を参照してください。

## <a name="prerequisites"></a>前提条件

1. Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

1. 次のアカウントとアプリケーションを設定してインストールする必要があります。
   - Azure Machine Learning 実験アカウント 
   - Azure Machine Learning モデル管理アカウント
   - Azure Machine Learning Workbench のインストール 

 これら 3 つが作成またはインストールされていない場合は、[Azure Machine Learning のクイックスタートとワークベンチのインストール](../service/quickstart-installation.md)に関する記事に従ってください。

1. Azure Machine Learning Package for Forecasting をインストールする必要があります。 このパッケージのインストール方法については、[こちら](https://aka.ms/aml-packages/forecasting)を参照してください。

## <a name="sample-data-and-jupyter-notebook"></a>サンプル データと Jupyter Notebook

### <a name="sample-workflow"></a>サンプル ワークフロー 
この例では、次のワークフローに従います。
 
1. **データを取り込む**: データセットを読み込んで TimeSeriesDataFrame に変換します。 このデータフレームは Azure Machine Learning Package for Forecasting が提供する時系列データ構造であり、ここでは **AMLPF** と呼びます。

2. **特徴を作成する**: AMLPF が提供するさまざまな特徴抽出トランスフォーマーを使用して、特徴を作成します。

3. **トレーニングを行って最善のモデルを選択する**: さまざまな一変量時系列モデルと機械学習モデルのパフォーマンスを比較します。 

4. **モデルをデプロイする**: トレーニング済みのモデル パイプラインを Azure Machine Learning Workbench を通して Web サービスとしてデプロイして、他のユーザーが利用できるようにします。

### <a name="get-the-jupyter-notebook"></a>Jupyter Notebook を取得する

ここで説明するサンプル コードを自分で実行するための Notebook をダウンロードします。

> [!div class="nextstepaction"]
> [Jupyter Notebook を取得する](https://aka.ms/aml-packages/forecasting/notebooks/sales_forecasting)

### <a name="explore-the-sample-data"></a>サンプル データを調べる

次のサンプル コードでの機械学習の予測例は、[シカゴ大学の Dominick's Finer Foods データセット](https://research.chicagobooth.edu/kilts/marketing-databases/dominicks)を使用して、オレンジ ジュースの売上を予測しています。 Dominick's Finer Foods は、シカゴ大都市圏のスーパー マーケット チェーンでした。

### <a name="import-any-dependencies-for-this-sample"></a>このサンプル用のすべての依存関係をインポートする

これらの依存関係を次のコード サンプル用にインポートする必要があります。


```python
import pandas as pd
import numpy as np
import math
import pkg_resources
from datetime import timedelta
import matplotlib
matplotlib.use('agg')
%matplotlib inline
from matplotlib import pyplot as plt

from sklearn.linear_model import Lasso, ElasticNet
from sklearn.ensemble import RandomForestRegressor, GradientBoostingRegressor
from sklearn.neighbors import KNeighborsRegressor

from ftk import TimeSeriesDataFrame, ForecastDataFrame, AzureMLForecastPipeline
from ftk.ts_utils import last_n_periods_split

from ftk.transforms import TimeSeriesImputer, TimeIndexFeaturizer, DropColumns
from ftk.transforms.grain_index_featurizer import GrainIndexFeaturizer
from ftk.models import Arima, SeasonalNaive, Naive, RegressionForecaster, ETS, BestOfForecaster
from ftk.models.forecaster_union import ForecasterUnion
from ftk.model_selection import TSGridSearchCV, RollingOriginValidator

from azuremltkbase.deployment import AMLSettings
from ftk.operationalization.forecast_webservice_factory import ForecastWebserviceFactory
from ftk.operationalization import ScoreContext

from ftk.data import get_a_year_of_daily_weather_data
print('imports done')
```

    imports done
    

## <a name="load-data-and-explore"></a>データを読み込んで探索する

このコード スニペットは、生データセット (ここでは [Dominick's Finer Foods のデータ](https://research.chicagobooth.edu/kilts/marketing-databases/dominicks)) から始まる一般的なプロセスを示しています。  便利な関数 [load_dominicks_oj_data](https://docs.microsoft.com/en-us/python/api/ftk.data.dominicks_oj.load_dominicks_oj_data) を使用することもできます。


```python
# Load the data into a pandas DataFrame
csv_path = pkg_resources.resource_filename('ftk', 'data/dominicks_oj/dominicks_oj.csv')
whole_df = pd.read_csv(csv_path, low_memory = False)
whole_df.head()
```




<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>store</th>
      <th>brand</th>
      <th>week</th>
      <th>logmove</th>
      <th>feat</th>
      <th>price</th>
      <th>AGE60</th>
      <th>EDUC</th>
      <th>ETHNIC</th>
      <th>INCOME</th>
      <th>HHLARGE</th>
      <th>WORKWOM</th>
      <th>HVAL150</th>
      <th>SSTRDIST</th>
      <th>SSTRVOL</th>
      <th>CPDIST5</th>
      <th>CPWVOL5</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2</td>
      <td>tropicana</td>
      <td>40</td>
      <td>9.02</td>
      <td>0</td>
      <td>3.87</td>
      <td>0.23</td>
      <td>0.25</td>
      <td>0.11</td>
      <td>10.55</td>
      <td>0.10</td>
      <td>0.30</td>
      <td>0.46</td>
      <td>2.11</td>
      <td>1.14</td>
      <td>1.93</td>
      <td>0.38</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>tropicana</td>
      <td>46</td>
      <td>8.72</td>
      <td>0</td>
      <td>3.87</td>
      <td>0.23</td>
      <td>0.25</td>
      <td>0.11</td>
      <td>10.55</td>
      <td>0.10</td>
      <td>0.30</td>
      <td>0.46</td>
      <td>2.11</td>
      <td>1.14</td>
      <td>1.93</td>
      <td>0.38</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>tropicana</td>
      <td>47</td>
      <td>8.25</td>
      <td>0</td>
      <td>3.87</td>
      <td>0.23</td>
      <td>0.25</td>
      <td>0.11</td>
      <td>10.55</td>
      <td>0.10</td>
      <td>0.30</td>
      <td>0.46</td>
      <td>2.11</td>
      <td>1.14</td>
      <td>1.93</td>
      <td>0.38</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2</td>
      <td>tropicana</td>
      <td>48</td>
      <td>8.99</td>
      <td>0</td>
      <td>3.87</td>
      <td>0.23</td>
      <td>0.25</td>
      <td>0.11</td>
      <td>10.55</td>
      <td>0.10</td>
      <td>0.30</td>
      <td>0.46</td>
      <td>2.11</td>
      <td>1.14</td>
      <td>1.93</td>
      <td>0.38</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2</td>
      <td>tropicana</td>
      <td>50</td>
      <td>9.09</td>
      <td>0</td>
      <td>3.87</td>
      <td>0.23</td>
      <td>0.25</td>
      <td>0.11</td>
      <td>10.55</td>
      <td>0.10</td>
      <td>0.30</td>
      <td>0.46</td>
      <td>2.11</td>
      <td>1.14</td>
      <td>1.93</td>
      <td>0.38</td>
    </tr>
  </tbody>
</table>



データは、ブランドと店舗別の週次売上で構成されます。 販売数量の対数が _logmove_ 列に含まれます。 データには、顧客の人口統計学的特徴もいくつか含まれています。 

時系列をモデル化するには、このデータフレームから次の要素を抽出する必要があります。 
+ 日付/時刻軸 
+ 予測される販売数量


```python
# The sales are contained in the 'logmove' column. 
# Values are logarithmic, so exponentiate and round them to get quantity sold
def expround(x):
    return math.floor(math.exp(x) + 0.5)
whole_df['Quantity'] = whole_df['logmove'].apply(expround)

# The time axis is in the 'week' column
# This is the week offset from the week of 1989-09-07 through 1989-09-13 inclusive
# Create new datetime columns containing the start and end of each week period
weekZeroStart = pd.to_datetime('1989-09-07 00:00:00')
weekZeroEnd = pd.to_datetime('1989-09-13 23:59:59')
whole_df['WeekFirstDay'] = whole_df['week'].apply(lambda n: weekZeroStart + timedelta(weeks=n))
whole_df['WeekLastDay'] = whole_df['week'].apply(lambda n: weekZeroEnd + timedelta(weeks=n))
whole_df[['store','brand','WeekLastDay','Quantity']].head()
```




<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>store</th>
      <th>brand</th>
      <th>WeekLastDay</th>
      <th>数量</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2</td>
      <td>tropicana</td>
      <td>1990-06-20 23:59:59</td>
      <td>8256</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>tropicana</td>
      <td>1990-08-01 23:59:59</td>
      <td>6144</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>tropicana</td>
      <td>1990-08-08 23:59:59</td>
      <td>3840</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2</td>
      <td>tropicana</td>
      <td>1990-08-15 23:59:59</td>
      <td>8000</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2</td>
      <td>tropicana</td>
      <td>1990-08-29 23:59:59</td>
      <td>8896</td>
    </tr>
  </tbody>
</table>




```python
nseries = whole_df.groupby(['store', 'brand']).ngroups
print('{} time series in the data frame.'.format(nseries))
```

    249 time series in the data frame.
    

データには、データフレーム内の店舗とブランドの約 250 の異なる組み合わせが含まれています。 各組み合わせは、独自の売上の時系列を定義します。 

[TimeSeriesDataFrame](https://docs.microsoft.com/en-us/python/api/ftk.dataframe_ts.timeseriesdataframe?view=azure-ml-py-latest) クラスを使用して、_粒度_を使用する 1 つのデータ構造で、複数の系列を便利にモデル化できます。 粒度は、`store` 列と `brand` 列によって指定されます。

"_粒度_" と "_グループ_" の違いは、粒度は現実の世界で常に物理的に意味がありますが、グループは必ずしもそうである必要はありません。 ユーザーがグループ化によってモデルのパフォーマンスが向上すると確信している場合、内部パッケージ関数でグループを使用して、複数の時系列から 1 つのモデルを構築します。 既定では、グループは粒度と等しくなるように設定され、粒度ごとに 1 つのモデルが構築されます。 


```python
# Create a TimeSeriesDataFrame
# Use end of period as the time index
# Store and brand combinations label the grain 
# i.e. there is one time series for each unique pair of store and grain
whole_tsdf = TimeSeriesDataFrame(whole_df, 
                                 grain_colnames=['store', 'brand'],
                                 time_colname='WeekLastDay', 
                                 ts_value_colname='Quantity',
                                 group_colnames='store')

whole_tsdf[['Quantity']].head()
```




<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th></th>
      <th></th>
      <th>数量</th>
    </tr>
    <tr>
      <th>WeekLastDay</th>
      <th>store</th>
      <th>brand</th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1990-06-20 23:59:59</th>
      <th>2</th>
      <th>tropicana</th>
      <td>8256</td>
    </tr>
    <tr>
      <th>1990-08-01 23:59:59</th>
      <th>2</th>
      <th>tropicana</th>
      <td>6144</td>
    </tr>
    <tr>
      <th>1990-08-08 23:59:59</th>
      <th>2</th>
      <th>tropicana</th>
      <td>3840</td>
    </tr>
    <tr>
      <th>1990-08-15 23:59:59</th>
      <th>2</th>
      <th>tropicana</th>
      <td>8000</td>
    </tr>
    <tr>
      <th>1990-08-29 23:59:59</th>
      <th>2</th>
      <th>tropicana</th>
      <td>8896</td>
    </tr>
  </tbody>
</table>



TimeSeriesDataFrame 表現で、時間軸と粒度がデータフレームのインデックスの一部になり、pandas 日時スライス機能への容易なアクセスが可能になります。


```python
# sort so we can slice
whole_tsdf.sort_index(inplace=True)

# Get sales of dominick's brand orange juice from store 2 during summer 1990
whole_tsdf.loc[pd.IndexSlice['1990-06':'1990-09', 2, 'dominicks'], ['Quantity']]
```




<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th></th>
      <th></th>
      <th>数量</th>
    </tr>
    <tr>
      <th>WeekLastDay</th>
      <th>store</th>
      <th>brand</th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1990-06-20 23:59:59</th>
      <th>2</th>
      <th>dominicks</th>
      <td>10560</td>
    </tr>
    <tr>
      <th>1990-08-01 23:59:59</th>
      <th>2</th>
      <th>dominicks</th>
      <td>8000</td>
    </tr>
    <tr>
      <th>1990-08-08 23:59:59</th>
      <th>2</th>
      <th>dominicks</th>
      <td>6848</td>
    </tr>
    <tr>
      <th>1990-08-15 23:59:59</th>
      <th>2</th>
      <th>dominicks</th>
      <td>2880</td>
    </tr>
    <tr>
      <th>1990-08-29 23:59:59</th>
      <th>2</th>
      <th>dominicks</th>
      <td>1600</td>
    </tr>
    <tr>
      <th>1990-09-05 23:59:59</th>
      <th>2</th>
      <th>dominicks</th>
      <td>25344</td>
    </tr>
    <tr>
      <th>1990-09-12 23:59:59</th>
      <th>2</th>
      <th>dominicks</th>
      <td>10752</td>
    </tr>
    <tr>
      <th>1990-09-19 23:59:59</th>
      <th>2</th>
      <th>dominicks</th>
      <td>6656</td>
    </tr>
    <tr>
      <th>1990-09-26 23:59:59</th>
      <th>2</th>
      <th>dominicks</th>
      <td>6592</td>
    </tr>
  </tbody>
</table>



[TimeSeriesDataFrame.ts_report](https://docs.microsoft.com/en-us/python/api/ftk.dataframe_ts.timeseriesdataframe?view=azure-ml-py-latest#ts-report) 関数は、時系列データフレームの包括的なレポートを生成します。 レポートには、全般的なデータの説明だけでなく、時系列データ固有の統計情報の両方が含まれます。 


```python
whole_tsdf.ts_report()
```

    --------------------------------  Data Overview  ---------------------------------
    <class 'ftk.time_series_data_frame.TimeSeriesDataFrame'>
    MultiIndex: 28947 entries, (1990-06-20 23:59:59, 2, dominicks) to (1992-10-07 23:59:59, 137, tropicana)
    Data columns (total 17 columns):
    week            28947 non-null int64
    logmove         28947 non-null float64
    feat            28947 non-null int64
    price           28947 non-null float64
    AGE60           28947 non-null float64
    EDUC            28947 non-null float64
    ETHNIC          28947 non-null float64
    INCOME          28947 non-null float64
    HHLARGE         28947 non-null float64
    WORKWOM         28947 non-null float64
    HVAL150         28947 non-null float64
    SSTRDIST        28947 non-null float64
    SSTRVOL         28947 non-null float64
    CPDIST5         28947 non-null float64
    CPWVOL5         28947 non-null float64
    Quantity        28947 non-null int64
    WeekFirstDay    28947 non-null datetime64[ns]
    dtypes: datetime64[ns](1), float64(13), int64(3)
    memory usage: 3.8+ MB
    --------------------------  Numerical Variable Summary  --------------------------
              week  logmove     feat    price    AGE60     EDUC   ETHNIC   INCOME  \
    count 28947.00 28947.00 28947.00 28947.00 28947.00 28947.00 28947.00 28947.00   
    mean    100.46     9.17     0.24     2.28     0.17     0.23     0.16    10.62   
    std      34.69     1.02     0.43     0.65     0.06     0.11     0.19     0.28   
    min      40.00     4.16     0.00     0.52     0.06     0.05     0.02     9.87   
    25%      70.00     8.49     0.00     1.79     0.12     0.15     0.04    10.46   
    50%     101.00     9.03     0.00     2.17     0.17     0.23     0.07    10.64   
    75%     130.00     9.76     0.00     2.73     0.21     0.28     0.19    10.80   
    max     160.00    13.48     1.00     3.87     0.31     0.53     1.00    11.24   
    
           HHLARGE  WORKWOM  HVAL150  SSTRDIST  SSTRVOL  CPDIST5  CPWVOL5  \
    count 28947.00 28947.00 28947.00  28947.00 28947.00 28947.00 28947.00   
    mean      0.12     0.36     0.34      5.10     1.21     2.12     0.44   
    std       0.03     0.05     0.24      3.47     0.53     0.73     0.22   
    min       0.01     0.24     0.00      0.13     0.40     0.77     0.09   
    25%       0.10     0.31     0.12      2.77     0.73     1.63     0.27   
    50%       0.11     0.36     0.35      4.65     1.12     1.96     0.38   
    75%       0.14     0.40     0.53      6.65     1.54     2.53     0.56   
    max       0.22     0.47     0.92     17.86     2.57     4.11     1.14   
    
           Quantity  
    count  28947.00  
    mean   17312.21  
    std    27477.66  
    min       64.00  
    25%     4864.00  
    50%     8384.00  
    75%    17408.00  
    max   716416.00  
    ------------------------  Non-Numerical Variable Summary  -----------------------
                   WeekFirstDay
    count                 28947
    unique                  121
    top     1992-03-12 00:00:00
    freq                    249
    first   1990-06-14 00:00:00
    last    1992-10-01 00:00:00
    ------------------------------  Time Series Summary  -----------------------------
    Number of time series                 249
    Minimum time                    1990-06-20 23:59:59
    Maximum time                    1992-10-07 23:59:59
    
    Inferred frequencies
    Number of ['store', 'brand']s with frequency W-WED     249
    Use get_frequency_dict() method to explore ['store', 'brand']s with unusual frequency and clean data
    
    Detected seasonalities
    Number of ['store', 'brand']s with seasonality 1         190
    Number of ['store', 'brand']s with seasonality 15        15
    Number of ['store', 'brand']s with seasonality 14        11
    Number of ['store', 'brand']s with seasonality 7         9
    Number of ['store', 'brand']s with seasonality 6         8
    Number of ['store', 'brand']s with seasonality 8         5
    Number of ['store', 'brand']s with seasonality 2         4
    Number of ['store', 'brand']s with seasonality 23        2
    Number of ['store', 'brand']s with seasonality 3         1
    Number of ['store', 'brand']s with seasonality 11        1
    Number of ['store', 'brand']s with seasonality 12        1
    Number of ['store', 'brand']s with seasonality 13        1
    Number of ['store', 'brand']s with seasonality 47        1
    Use get_seasonality_dict() method to explore ['store', 'brand']s with unusual seasonality and clean data
    -----------------------------  Value Column Summary  -----------------------------
    Value column                        Quantity
    Percentage of missing values        0.00
    Percentage of zero values           0.00
    Mean coefficient of variation       31688.52
    Median coefficient of variation     24000.20
    Minimum coefficient of variation    ['store', 'brand'] (48, 'tropicana'): 4475.53
    Maximum coefficient of variation    ['store', 'brand'] (111, 'dominicks'): 193333.55
    ------------------------------  Correlation Matrix  ------------------------------
        week  logmove  feat  price  AGE60  EDUC  ETHNIC  INCOME  HHLARGE  WORKWOM  \
    0   1.00     0.10  0.04  -0.21  -0.01  0.01    0.00    0.00     0.01    -0.00   
    1   0.10     1.00  0.54  -0.43   0.09  0.00    0.06   -0.04    -0.06    -0.08   
    2   0.04     0.54  1.00  -0.29  -0.00  0.00    0.00   -0.00    -0.00     0.00   
    3  -0.21    -0.43 -0.29   1.00   0.04  0.02    0.04   -0.03    -0.04    -0.02   
    4  -0.01     0.09 -0.00   0.04   1.00 -0.31   -0.09   -0.15    -0.32    -0.63   
    5   0.01     0.00  0.00   0.02  -0.31  1.00   -0.34    0.66    -0.39     0.56   
    6   0.00     0.06  0.00   0.04  -0.09 -0.34    1.00   -0.72     0.25    -0.29   
    7   0.00    -0.04 -0.00  -0.03  -0.15  0.66   -0.72    1.00    -0.08     0.40   
    8   0.01    -0.06 -0.00  -0.04  -0.32 -0.39    0.25   -0.08     1.00    -0.28   
    9  -0.00    -0.08  0.00  -0.02  -0.63  0.56   -0.29    0.40    -0.28     1.00   
    10  0.01     0.02  0.00   0.04  -0.11  0.89   -0.42    0.64    -0.48     0.45   
    11  0.01    -0.00  0.00   0.08   0.07 -0.12    0.54   -0.41     0.06    -0.19   
    12 -0.01    -0.09 -0.00   0.03  -0.05 -0.13    0.23   -0.26     0.04    -0.06   
    13 -0.01     0.02 -0.00  -0.06   0.09 -0.20   -0.22    0.21     0.19    -0.13   
    14 -0.00    -0.12 -0.00  -0.01  -0.09  0.28   -0.38    0.36    -0.20     0.37   
    15  0.03     0.76  0.47  -0.36   0.08 -0.04    0.11   -0.08    -0.00    -0.10   
    
        HVAL150  SSTRDIST  SSTRVOL  CPDIST5  CPWVOL5  Quantity  
    0      0.01      0.01    -0.01    -0.01    -0.00      0.03  
    1      0.02     -0.00    -0.09     0.02    -0.12      0.76  
    2      0.00      0.00    -0.00    -0.00    -0.00      0.47  
    3      0.04      0.08     0.03    -0.06    -0.01     -0.36  
    4     -0.11      0.07    -0.05     0.09    -0.09      0.08  
    5      0.89     -0.12    -0.13    -0.20     0.28     -0.04  
    6     -0.42      0.54     0.23    -0.22    -0.38      0.11  
    7      0.64     -0.41    -0.26     0.21     0.36     -0.08  
    8     -0.48      0.06     0.04     0.19    -0.20     -0.00  
    9      0.45     -0.19    -0.06    -0.13     0.37     -0.10  
    10     1.00     -0.17    -0.24    -0.22     0.27     -0.04  
    11    -0.17      1.00     0.17    -0.11    -0.40      0.06  
    12    -0.24      0.17     1.00    -0.05     0.36     -0.02  
    13    -0.22     -0.11    -0.05     1.00     0.02     -0.00  
    14     0.27     -0.40     0.36     0.02     1.00     -0.11  
    15    -0.04      0.06    -0.02    -0.00    -0.11      1.00  
    You may call TimeSeriesDataFrame.plot_scatter_matrix() to get a correlation matrix plot. However, this
    is not recommended if your data is big.
    



![png](./media/how-to-build-deploy-forecast-models/output_15_1.png)



![png](./media/how-to-build-deploy-forecast-models/output_15_2.png)



![png](./media/how-to-build-deploy-forecast-models/output_15_3.png)



![png](./media/how-to-build-deploy-forecast-models/output_15_4.png)



![png](./media/how-to-build-deploy-forecast-models/output_15_5.png)



![png](./media/how-to-build-deploy-forecast-models/output_15_6.png)


## <a name="integrate-with-external-data"></a>外部データとの統合

予測するための追加の特徴として外部データを統合すると、役に立つ場合があります。 このコード サンプルでは、TimeSeriesDataFrame を天候に関連する外部データと結合します。


```python
# Load weather data
weather_1990 = get_a_year_of_daily_weather_data(year=1990)
weather_1991 = get_a_year_of_daily_weather_data(year=1991)
weather_1992 = get_a_year_of_daily_weather_data(year=1992)

# Preprocess weather data
weather_all = pd.concat([weather_1990, weather_1991, weather_1992])
weather_all.reset_index(inplace=True)

# Only use a subset of columns
weather_all = weather_all[['TEMP', 'DEWP', 'WDSP', 'PRCP']]

# Compute the WeekLastDay column, in order to merge with sales data
weather_all['WeekLastDay'] = pd.Series(
    weather_all.time_index - weekZeroStart, 
    index=weather_all.time_index).apply(lambda n: weekZeroEnd + timedelta(weeks=math.floor(n.days/7)))

# Resample daily weather data to weekly data
weather_all = weather_all.groupby('WeekLastDay').mean()

# Set WeekLastDay as new time index
weather_all = TimeSeriesDataFrame(weather_all, time_colname='WeekLastDay')

# Merge weather data with sales data
whole_tsdf = whole_tsdf.merge(weather_all, how='left', on='WeekLastDay')
whole_tsdf.head()
```




<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th></th>
      <th></th>
      <th>week</th>
      <th>logmove</th>
      <th>feat</th>
      <th>price</th>
      <th>AGE60</th>
      <th>EDUC</th>
      <th>ETHNIC</th>
      <th>INCOME</th>
      <th>HHLARGE</th>
      <th>WORKWOM</th>
      <th>...</th>
      <th>SSTRDIST</th>
      <th>SSTRVOL</th>
      <th>CPDIST5</th>
      <th>CPWVOL5</th>
      <th>数量</th>
      <th>WeekFirstDay</th>
      <th>TEMP</th>
      <th>DEWP</th>
      <th>WDSP</th>
      <th>PRCP</th>
    </tr>
    <tr>
      <th>WeekLastDay</th>
      <th>store</th>
      <th>brand</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th rowspan="5" valign="top">1990-06-20 23:59:59</th>
      <th rowspan="3" valign="top">2</th>
      <th>dominicks</th>
      <td>40</td>
      <td>9.26</td>
      <td>1</td>
      <td>1.59</td>
      <td>0.23</td>
      <td>0.25</td>
      <td>0.11</td>
      <td>10.55</td>
      <td>0.10</td>
      <td>0.30</td>
      <td>...</td>
      <td>2.11</td>
      <td>1.14</td>
      <td>1.93</td>
      <td>0.38</td>
      <td>10560</td>
      <td>1990-06-14</td>
      <td>72.00</td>
      <td>61.87</td>
      <td>9.74</td>
      <td>0.19</td>
    </tr>
    <tr>
      <th>minute.maid</th>
      <td>40</td>
      <td>8.41</td>
      <td>0</td>
      <td>3.17</td>
      <td>0.23</td>
      <td>0.25</td>
      <td>0.11</td>
      <td>10.55</td>
      <td>0.10</td>
      <td>0.30</td>
      <td>...</td>
      <td>2.11</td>
      <td>1.14</td>
      <td>1.93</td>
      <td>0.38</td>
      <td>4480</td>
      <td>1990-06-14</td>
      <td>72.00</td>
      <td>61.87</td>
      <td>9.74</td>
      <td>0.19</td>
    </tr>
    <tr>
      <th>tropicana</th>
      <td>40</td>
      <td>9.02</td>
      <td>0</td>
      <td>3.87</td>
      <td>0.23</td>
      <td>0.25</td>
      <td>0.11</td>
      <td>10.55</td>
      <td>0.10</td>
      <td>0.30</td>
      <td>...</td>
      <td>2.11</td>
      <td>1.14</td>
      <td>1.93</td>
      <td>0.38</td>
      <td>8256</td>
      <td>1990-06-14</td>
      <td>72.00</td>
      <td>61.87</td>
      <td>9.74</td>
      <td>0.19</td>
    </tr>
    <tr>
      <th rowspan="2" valign="top">5</th>
      <th>dominicks</th>
      <td>40</td>
      <td>7.49</td>
      <td>1</td>
      <td>1.59</td>
      <td>0.12</td>
      <td>0.32</td>
      <td>0.05</td>
      <td>10.92</td>
      <td>0.10</td>
      <td>0.41</td>
      <td>...</td>
      <td>3.80</td>
      <td>0.68</td>
      <td>1.60</td>
      <td>0.74</td>
      <td>1792</td>
      <td>1990-06-14</td>
      <td>72.00</td>
      <td>61.87</td>
      <td>9.74</td>
      <td>0.19</td>
    </tr>
    <tr>
      <th>minute.maid</th>
      <td>40</td>
      <td>8.35</td>
      <td>0</td>
      <td>2.99</td>
      <td>0.12</td>
      <td>0.32</td>
      <td>0.05</td>
      <td>10.92</td>
      <td>0.10</td>
      <td>0.41</td>
      <td>...</td>
      <td>3.80</td>
      <td>0.68</td>
      <td>1.60</td>
      <td>0.74</td>
      <td>4224</td>
      <td>1990-06-14</td>
      <td>72.00</td>
      <td>61.87</td>
      <td>9.74</td>
      <td>0.19</td>
    </tr>
  </tbody>
</table>


## <a name="preprocess-data-and-impute-missing-values"></a>データを前処理して欠損値を補完する

[last_n_periods_split](https://docs.microsoft.com/en-us/python/api/ftk.ts_utils?view=azure-ml-py-latest) ユーティリティ関数を使用して、データをトレーニング セットとテスト セットに分割することから始めます。 結果のテスト セットには、各時系列の最後の 40 の観測値が含まれます。 


```python
train_tsdf, test_tsdf = last_n_periods_split(whole_tsdf, 40)
```

基本的な時系列モデルは、連続的な時系列を必要とします。 [check_regularity_by_grain](https://docs.microsoft.com/en-us/python/api/ftk.dataframe_ts.timeseriesdataframe?view=azure-ml-py-latest#check-regularity-by-grain) 関数を使用して、系列が一定であること、つまり一定間隔でサンプリングされた時間インデックスがあることを確認してください。


```python
ts_regularity = train_tsdf.check_regularity_by_grain()
print(ts_regularity[ts_regularity['regular'] == False])
```

                                              problems  regular
    store brand                                                
    2     dominicks    [Irregular datetime gaps exist]    False
          minute.maid  [Irregular datetime gaps exist]    False
          tropicana    [Irregular datetime gaps exist]    False
    5     dominicks    [Irregular datetime gaps exist]    False
          minute.maid  [Irregular datetime gaps exist]    False
          tropicana    [Irregular datetime gaps exist]    False
    8     dominicks    [Irregular datetime gaps exist]    False
          minute.maid  [Irregular datetime gaps exist]    False
          tropicana    [Irregular datetime gaps exist]    False
    9     dominicks    [Irregular datetime gaps exist]    False
          minute.maid  [Irregular datetime gaps exist]    False
          tropicana    [Irregular datetime gaps exist]    False
    12    dominicks    [Irregular datetime gaps exist]    False
          minute.maid  [Irregular datetime gaps exist]    False
          tropicana    [Irregular datetime gaps exist]    False
    14    dominicks    [Irregular datetime gaps exist]    False
          minute.maid  [Irregular datetime gaps exist]    False
          tropicana    [Irregular datetime gaps exist]    False
    18    dominicks    [Irregular datetime gaps exist]    False
          minute.maid  [Irregular datetime gaps exist]    False
          tropicana    [Irregular datetime gaps exist]    False
    21    dominicks    [Irregular datetime gaps exist]    False
          minute.maid  [Irregular datetime gaps exist]    False
          tropicana    [Irregular datetime gaps exist]    False
    28    dominicks    [Irregular datetime gaps exist]    False
          minute.maid  [Irregular datetime gaps exist]    False
          tropicana    [Irregular datetime gaps exist]    False
    33    dominicks    [Irregular datetime gaps exist]    False
          minute.maid  [Irregular datetime gaps exist]    False
          tropicana    [Irregular datetime gaps exist]    False
    ...                                            ...      ...
    119   dominicks    [Irregular datetime gaps exist]    False
          minute.maid  [Irregular datetime gaps exist]    False
          tropicana    [Irregular datetime gaps exist]    False
    121   dominicks    [Irregular datetime gaps exist]    False
          minute.maid  [Irregular datetime gaps exist]    False
          tropicana    [Irregular datetime gaps exist]    False
    123   dominicks    [Irregular datetime gaps exist]    False
          minute.maid  [Irregular datetime gaps exist]    False
          tropicana    [Irregular datetime gaps exist]    False
    126   dominicks    [Irregular datetime gaps exist]    False
          minute.maid  [Irregular datetime gaps exist]    False
          tropicana    [Irregular datetime gaps exist]    False
    128   dominicks    [Irregular datetime gaps exist]    False
          minute.maid  [Irregular datetime gaps exist]    False
          tropicana    [Irregular datetime gaps exist]    False
    129   dominicks    [Irregular datetime gaps exist]    False
          minute.maid  [Irregular datetime gaps exist]    False
          tropicana    [Irregular datetime gaps exist]    False
    130   dominicks    [Irregular datetime gaps exist]    False
          minute.maid  [Irregular datetime gaps exist]    False
          tropicana    [Irregular datetime gaps exist]    False
    131   dominicks    [Irregular datetime gaps exist]    False
          minute.maid  [Irregular datetime gaps exist]    False
          tropicana    [Irregular datetime gaps exist]    False
    134   dominicks    [Irregular datetime gaps exist]    False
          minute.maid  [Irregular datetime gaps exist]    False
          tropicana    [Irregular datetime gaps exist]    False
    137   dominicks    [Irregular datetime gaps exist]    False
          minute.maid  [Irregular datetime gaps exist]    False
          tropicana    [Irregular datetime gaps exist]    False
    
    [213 rows x 2 columns]
    

ほとんどの系列 (249 のうちの 213) が一定でないことがわかります。 欠損している販売数量の値を埋めるために、[補完変換](https://docs.microsoft.com/en-us/python/api/ftk.transforms.ts_imputer.timeseriesimputer?view=azure-ml-py-latest)が必要です。 多数の補完方法がありますが、次のサンプル コードは、線状補間を使用しています。


```python
# Use a TimeSeriesImputer to linearly interpolate missing values
imputer = TimeSeriesImputer(input_column='Quantity', 
                            option='interpolate',
                            method='linear',
                            freq='W-WED')

train_imputed_tsdf = imputer.transform(train_tsdf)
```

補完コードが実行されると、すべての系列の周期が一定になります。


```python
ts_regularity_imputed = train_imputed_tsdf.check_regularity_by_grain()
print(ts_regularity_imputed[ts_regularity_imputed['regular'] == False])
```

    Empty DataFrame
    Columns: [problems, regular]
    Index: []
    

## <a name="univariate-time-series-models"></a>一変量時系列モデル

これでデータがクリーンアップされたので、モデリングを開始できます。  "ナイーブ" モデル、"季節性ナイーブ" モデル、および "ARIMA" モデルという 3 つの一変量モデルの作成から始めます。
* ナイーブ予測アルゴリズムは、現在の期間の予測値として、最後の期間の実際のターゲット変数の値を使用します。

* 季節性ナイーブ予測アルゴリズムは、現在の時点の予測値として、前年の季節の同じ時点の実際のターゲット変数の値を使用します。 いくつかの例では、前年の月の実際の値を使用して、当年の同月の値を予測し、昨日の時間を使用して、当日の同時間の値を予測しています。 

* 指数平滑法 (ETS) アルゴリズムは、過去の観察値の重みづけされた平均を計算することで、予測を生成します。重みは、観測値が古くなるにつれて、指数関数的に減少します。 

* 自己回帰和分移動平均 (ARIMA) アルゴリズムは、時系列データの自己相関を把握します。 ARIMA の詳細については、[こちらのリンク](https://en.wikipedia.org/wiki/Autoregressive_integrated_moving_average)をご覧ください。

データの探索に基づく特定のモデル パラメーターの設定から始めます。 


```python
oj_series_freq = 'W-WED'
oj_series_seasonality = 52
```

### <a name="initialize-models"></a>モデルを初期化する


```python
# Initialize naive model.
naive_model = Naive(freq=oj_series_freq)

# Initialize seasonal naive model. 
seasonal_naive_model = SeasonalNaive(freq=oj_series_freq, 
                                     seasonality=oj_series_seasonality)

# Initialize ETS model.
ets_model = ETS(freq=oj_series_freq, seasonality=oj_series_seasonality)

# Initialize ARIMA(p,d,q) model.
arima_order = [2, 1, 0]
arima_model = Arima(oj_series_freq, arima_order)
```

### <a name="combine-multiple-models"></a>複数のモデルを組み合わせる

[ForecasterUnion](https://docs.microsoft.com/en-us/python/api/ftk.models.forecaster_union?view=azure-ml-py-latest) 推定器を使用すると、1 行のコードで、複数の推定の結合とそれらに対する適合/予測を実行できます。


```python
forecaster_union = ForecasterUnion(
    forecaster_list=[('naive', naive_model), ('seasonal_naive', seasonal_naive_model), 
                     ('ets', ets_model), ('arima', arima_model)]) 
```

### <a name="fit-and-predict"></a>適合と予測

AMLPF の推定器は、scikit-learn 推定器と同じ API に従います。モデルのトレーニングでは適合メソッドが、予測の生成では予測メソッドが使用されます。 

**モデルをトレーニングする**  
これらのモデルは、すべて一変量モデルであるため、1 つのモデルがデータの各粒度に適合します。 AMLPF を使用すると、249 のモデルのすべてを、たった 1 つの関数の呼び出しで適合させることができます。


```python
forecaster_union_fitted = forecaster_union.fit(train_imputed_tsdf)
```

**テスト データで売上を予測する**  
適合メソッドと同じように、`predict` 関数の 1 回の呼び出しで、テスト データセット内の 249 のすべての系列の予測を作成できます。 


```python
forecaster_union_prediction = forecaster_union_fitted.predict(test_tsdf, retain_feature_column=True)
```

**モデルのパフォーマンスを評価する**   

次に、テスト セットに対する予測エラーを計算できます。 ここでは、平均絶対パーセント誤差 (MAPE) を使用できます。 MAPE は、実際の売上値に対する平均絶対パーセント誤差です。 ```calc_error``` 関数には、誤差メトリックで一般的に使用されるいくつかの組み込み関数が用意されています。 MedianAPE を計算して err_fun 引数にそれを渡すカスタム エラー関数を定義することもできます。


```python
def calc_median_ape(y_true, y_pred):
    y_true = np.array(y_true).astype(float)
    y_pred = np.array(y_pred).astype(float)
    y_true_rm_na = y_true[~(np.isnan(y_true) | np.isnan(y_pred))]
    y_pred_rm_na = y_pred[~(np.isnan(y_true) | np.isnan(y_pred))]
    y_true = y_true_rm_na
    y_pred = y_pred_rm_na
    if len(y_true) == 0:
        # if there is no entries left after removing na data, return np.nan
        return(np.nan)
    y_true_rm_zero = y_true[y_true != 0]
    y_pred_rm_zero = y_pred[y_true != 0]
    if len(y_true_rm_zero) == 0:
        # if all values are zero, np.nan will be returned.
        return(np.nan)
    ape = np.abs((y_true_rm_zero - y_pred_rm_zero) / y_true_rm_zero) * 100
    median_ape = np.median(ape)
    return median_ape
```


```python
forecaster_union_MAPE = forecaster_union_prediction.calc_error(err_name='MAPE',
                                                               by='ModelName')
forecaster_union_MedianAPE = forecaster_union_prediction.calc_error(err_name='MedianAPE', 
                                                                    err_fun=calc_median_ape,
                                                                    by='ModelName')

univariate_model_errors = forecaster_union_MAPE.merge(forecaster_union_MedianAPE, on='ModelName')
univariate_model_errors
```



<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>ModelName</th>
      <th>MAPE</th>
      <th>MedianAPE</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>ARIMA</td>
      <td>126.57</td>
      <td>66.49</td>
    </tr>
    <tr>
      <th>1</th>
      <td>ets</td>
      <td>187.89</td>
      <td>75.73</td>
    </tr>
    <tr>
      <th>2</th>
      <td>naive</td>
      <td>103.57</td>
      <td>59.14</td>
    </tr>
    <tr>
      <th>3</th>
      <td>seasonal_naive</td>
      <td>180.54</td>
      <td>65.99</td>
    </tr>
  </tbody>
</table>



## <a name="build-machine-learning-models"></a>機械学習モデルを構築する

従来の一変量モデルだけでなく Azure Machine Learning Package for Forecasting を使用して、機械学習モデルを作成することもできます。

これらのモデルでは、特徴の作成から始めます。

### <a name="feature-engineering"></a>特徴エンジニアリング

**トランスフォーマー**   
パッケージには、時系列データの前処理と特徴抽出を行うための多数のトランスフォーマーが用意されています。 次の例は、前処理と特徴抽出機能の一部を示しています。


```python
# DropColumns: Drop columns that should not be included for modeling. `logmove` is the log of the number of 
# units sold, so providing this number would be cheating. `WeekFirstDay` would be 
# redundant since we already have a feature for the last day of the week.
columns_to_drop = ['logmove', 'WeekFirstDay', 'week']
column_dropper = DropColumns(columns_to_drop)

# TimeSeriesImputer: Fill missing values in the features
# First, we need to create a dictionary with key as column names and value as values used to fill missing 
# values for that column. We are going to use the mean to fill missing values for each column.
columns_with_missing_values = train_imputed_tsdf.columns[pd.DataFrame(train_imputed_tsdf).isnull().any()].tolist()
columns_with_missing_values = [c for c in columns_with_missing_values if c not in columns_to_drop]
missing_value_imputation_dictionary = {}
for c in columns_with_missing_values:
    missing_value_imputation_dictionary[c] = train_imputed_tsdf[c].mean()
fillna_imputer = TimeSeriesImputer(option='fillna', 
                                   input_column=columns_with_missing_values,
                                   value=missing_value_imputation_dictionary)

# TimeIndexFeaturizer: extract temporal features from timestamps
time_index_featurizer = TimeIndexFeaturizer(correlation_cutoff=0.1, overwrite_columns=True)

# GrainIndexFeaturizer: create indicator variables for stores and brands
grain_featurizer = GrainIndexFeaturizer(overwrite_columns=True, ts_frequency=oj_series_freq)
```

**パイプライン**   
パイプライン オブジェクトによって一連の手順を簡単に保存できるため、異なるオブジェクトに対して同じ手順を何回も適用できます。 また、デプロイのために他のマシンに簡単に移植できるように、パイプライン オブジェクトを pickle 化できます。 ここまで作成したすべてのトランスフォーマーをパイプラインを使用して連結できます。 


```python
pipeline_ml = AzureMLForecastPipeline([('drop_columns', column_dropper), 
                                       ('fillna_imputer', fillna_imputer),
                                       ('time_index_featurizer', time_index_featurizer),
                                       ('grain_featurizer', grain_featurizer)
                                      ])


train_feature_tsdf = pipeline_ml.fit_transform(train_imputed_tsdf)
test_feature_tsdf = pipeline_ml.transform(test_tsdf)

# Let's get a look at our new feature set
print(train_feature_tsdf.head())
```

    F1 2018-06-14 23:10:03,472 INFO azureml.timeseries - pipeline fit_transform started. 
    F1 2018-06-14 23:10:07,317 INFO azureml.timeseries - pipeline fit_transform finished. Time elapsed 0:00:03.845078
    F1 2018-06-14 23:10:07,317 INFO azureml.timeseries - pipeline transforms started. 
    F1 2018-06-14 23:10:16,499 INFO azureml.timeseries - pipeline transforms finished. Time elapsed 0:00:09.182314
                                           feat  price  AGE60  EDUC  ETHNIC  \
    WeekLastDay         store brand                                           
    1990-06-20 23:59:59 2     dominicks    1.00   1.59   0.23  0.25    0.11   
                              minute.maid  0.00   3.17   0.23  0.25    0.11   
                              tropicana    0.00   3.87   0.23  0.25    0.11   
                        5     dominicks    1.00   1.59   0.12  0.32    0.05   
                              minute.maid  0.00   2.99   0.12  0.32    0.05   
    
                                           INCOME  HHLARGE  WORKWOM  HVAL150  \
    WeekLastDay         store brand                                            
    1990-06-20 23:59:59 2     dominicks     10.55     0.10     0.30     0.46   
                              minute.maid   10.55     0.10     0.30     0.46   
                              tropicana     10.55     0.10     0.30     0.46   
                        5     dominicks     10.92     0.10     0.41     0.54   
                              minute.maid   10.92     0.10     0.41     0.54   
    
                                           SSTRDIST     ...       CPWVOL5  \
    WeekLastDay         store brand                     ...                 
    1990-06-20 23:59:59 2     dominicks        2.11     ...          0.38   
                              minute.maid      2.11     ...          0.38   
                              tropicana        2.11     ...          0.38   
                        5     dominicks        3.80     ...          0.74   
                              minute.maid      3.80     ...          0.74   
    
                                           Quantity  TEMP  DEWP  WDSP  PRCP  year  \
    WeekLastDay         store brand                                                 
    1990-06-20 23:59:59 2     dominicks    10560.00 72.00 61.87  9.74  0.19  1990   
                              minute.maid   4480.00 72.00 61.87  9.74  0.19  1990   
                              tropicana     8256.00 72.00 61.87  9.74  0.19  1990   
                        5     dominicks     1792.00 72.00 61.87  9.74  0.19  1990   
                              minute.maid   4224.00 72.00 61.87  9.74  0.19  1990   
    
                                           day  grain_brand  grain_store  
    WeekLastDay         store brand                                       
    1990-06-20 23:59:59 2     dominicks     20    dominicks            2  
                              minute.maid   20  minute.maid            2  
                              tropicana     20    tropicana            2  
                        5     dominicks     20    dominicks            5  
                              minute.maid   20  minute.maid            5  
    
    [5 rows x 22 columns]
    

 **RegressionForecaster**

[RegressionForecaster](https://docs.microsoft.com/en-us/python/api/ftk.models.regression_forecaster.regressionforecaster?view=azure-ml-py-latest) 関数は、sklearn 回帰推定器をラップして、TimeSeriesDataFrame でトレーニングできるようにします。 ラップされた予測器も、各グループ (ここではストア) を同じモデル内に配置します。 予測器は、類似しているとみなされ、まとめてプールできる系列のグループに対する 1 つのモデルを学習できます。 多くの場合、系列のグループに対する 1 つのモデルは、長期の系列のデータを使用して、短期の系列の予測を向上させます。 回帰をサポートするライブラリ内のその他のモデルの代わりに、これらのモデルを使用できます。 


```python
lasso_model = RegressionForecaster(estimator=Lasso(),
                                   make_grain_features=False)
elastic_net_model = RegressionForecaster(estimator=ElasticNet(),
                                         make_grain_features=False)
knn_model = RegressionForecaster(estimator=KNeighborsRegressor(),
                                 make_grain_features=False)
random_forest_model = RegressionForecaster(estimator=RandomForestRegressor(),
                                           make_grain_features=False)
boosted_trees_model = RegressionForecaster(estimator=GradientBoostingRegressor(),
                                           make_grain_features=False)

ml_union = ForecasterUnion(forecaster_list=[
    ('lasso', lasso_model), 
    ('elastic_net', elastic_net_model), 
    ('knn', knn_model), 
    ('random_forest', random_forest_model), 
    ('boosted_trees', boosted_trees_model)
]) 
```


```python
ml_union.fit(train_feature_tsdf, y=train_feature_tsdf.ts_value)
ml_results = ml_union.predict(test_feature_tsdf, retain_feature_column=True)
```


```python
ml_model_MAPE = ml_results.calc_error(err_name='MAPE', by='ModelName')
ml_model_MedianAPE = ml_results.calc_error(err_name='MedianAPE', 
                                           err_fun=calc_median_ape,
                                           by='ModelName')
ml_model_errors = ml_model_MAPE.merge(ml_model_MedianAPE, on='ModelName')
all_errors = pd.concat([univariate_model_errors, ml_model_errors])
all_errors.sort_values('MedianAPE')
```





<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>ModelName</th>
      <th>MAPE</th>
      <th>MedianAPE</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>4</th>
      <td>random_forest</td>
      <td>78.82</td>
      <td>42.81</td>
    </tr>
    <tr>
      <th>0</th>
      <td>boosted_trees</td>
      <td>78.46</td>
      <td>45.37</td>
    </tr>
    <tr>
      <th>2</th>
      <td>naive</td>
      <td>103.57</td>
      <td>59.14</td>
    </tr>
    <tr>
      <th>2</th>
      <td>knn</td>
      <td>129.85</td>
      <td>65.37</td>
    </tr>
    <tr>
      <th>1</th>
      <td>elastic_net</td>
      <td>125.11</td>
      <td>65.59</td>
    </tr>
    <tr>
      <th>3</th>
      <td>seasonal_naive</td>
      <td>180.54</td>
      <td>65.99</td>
    </tr>
    <tr>
      <th>0</th>
      <td>ARIMA</td>
      <td>126.57</td>
      <td>66.49</td>
    </tr>
    <tr>
      <th>3</th>
      <td>lasso</td>
      <td>112.87</td>
      <td>67.92</td>
    </tr>
    <tr>
      <th>1</th>
      <td>ets</td>
      <td>187.89</td>
      <td>75.73</td>
    </tr>
  </tbody>
</table>



いくつかの機械学習モデルでは、系列間の追加された特徴と類似性を活用して、より正確な予測を取得できました。

### <a name="cross-validation-parameter-and-model-sweeping"></a>クロス検証、パラメーター、モデルのスイープ    

パッケージでは、予測アプリケーション用にいくつかの従来の機械学習関数を適合させています。  [RollingOriginValidator](https://docs.microsoft.com/python/api/ftk.model_selection.cross_validation.rollingoriginvalidator?view=azure-ml-py-latest) は、一時的にクロス検証を実行して、予測フレームワークで何が認識され、何が認識されていないかを考慮します。 

次の図では、各四角形は、1 つの時点のデータを表します。 青色の四角形はトレーニングを、オレンジ色の四角形は各フォールドでのテストを表します。 テスト データは、最大のトレーニング時点より後の時点から取得する必要があります。 それ以外の場合、将来のデータがトレーニング データ内に漏出して、モデルの評価が無効になります。 
![png](./media/how-to-build-deploy-forecast-models/cv_figure.PNG)

**パラメーターのスイープ**  
[TSGridSearchCV](https://docs.microsoft.com/en-us/python/api/ftk.model_selection.search.tsgridsearchcv?view=azure-ml-py-latest) クラスは、指定されたパラメーター値を徹底的に検索し、`RollingOriginValidator` を使用してパラメーターのパフォーマンスを評価し、最適なパラメーターを見つけます。


```python
# Set up the `RollingOriginValidator` to do 2 folds of rolling origin cross-validation
rollcv = RollingOriginValidator(n_splits=2)
randomforest_model_for_cv = RegressionForecaster(estimator=RandomForestRegressor(),
                                                 make_grain_features=False)

# Set up our parameter grid and feed it to our grid search algorithm
param_grid_rf = {'estimator__n_estimators': np.array([10, 50, 100])}
grid_cv_rf = TSGridSearchCV(randomforest_model_for_cv, param_grid_rf, cv=rollcv)

# fit and predict
randomforest_cv_fitted= grid_cv_rf.fit(train_feature_tsdf, y=train_feature_tsdf.ts_value)
print('Best paramter: {}'.format(randomforest_cv_fitted.best_params_))
```

    Best paramter: {'estimator__n_estimators': 100}
    

**モデルのスイープ**  
`BestOfForecaster` クラスは、指定されたモデルのリストからパフォーマンスが最高のモデルを選択します。 `TSGridSearchCV` と同様に、クロス検証とパフォーマンス評価に RollingOriginValidator を使用します。  
ここでは、`BestOfForecaster` の使用法を示す 2 つのモデルのリストを渡します。


```python
best_of_forecaster = BestOfForecaster(forecaster_list=[('naive', naive_model), 
                                                       ('random_forest', random_forest_model)])
best_of_forecaster_fitted = best_of_forecaster.fit(train_feature_tsdf,
                                                   validator=RollingOriginValidator(n_step=20, max_horizon=40))
best_of_forecaster_prediction = best_of_forecaster_fitted.predict(test_feature_tsdf)
best_of_forecaster_prediction.head()
```




<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th>PointForecast</th>
      <th>DistributionForecast</th>
      <th>数量</th>
    </tr>
    <tr>
      <th>WeekLastDay</th>
      <th>store</th>
      <th>brand</th>
      <th>ForecastOriginTime</th>
      <th>ModelName</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1992-01-08 23:59:59</th>
      <th>2</th>
      <th>dominicks</th>
      <th>1992-01-01 23:59:59</th>
      <th>random_forest</th>
      <td>9299.20</td>
      <td>&lt;scipy.stats._distn_infrastructure.rv_frozen o...</td>
      <td>11712.00</td>
    </tr>
    <tr>
      <th>1992-01-15 23:59:59</th>
      <th>2</th>
      <th>dominicks</th>
      <th>1992-01-01 23:59:59</th>
      <th>random_forest</th>
      <td>10259.20</td>
      <td>&lt;scipy.stats._distn_infrastructure.rv_frozen o...</td>
      <td>4032.00</td>
    </tr>
    <tr>
      <th>1992-01-22 23:59:59</th>
      <th>2</th>
      <th>dominicks</th>
      <th>1992-01-01 23:59:59</th>
      <th>random_forest</th>
      <td>6828.80</td>
      <td>&lt;scipy.stats._distn_infrastructure.rv_frozen o...</td>
      <td>6336.00</td>
    </tr>
    <tr>
      <th>1992-01-29 23:59:59</th>
      <th>2</th>
      <th>dominicks</th>
      <th>1992-01-01 23:59:59</th>
      <th>random_forest</th>
      <td>16633.60</td>
      <td>&lt;scipy.stats._distn_infrastructure.rv_frozen o...</td>
      <td>13632.00</td>
    </tr>
    <tr>
      <th>1992-02-05 23:59:59</th>
      <th>2</th>
      <th>dominicks</th>
      <th>1992-01-01 23:59:59</th>
      <th>random_forest</th>
      <td>12774.40</td>
      <td>&lt;scipy.stats._distn_infrastructure.rv_frozen o...</td>
      <td>45120.00</td>
    </tr>
  </tbody>
</table>



**最後のパイプラインを構築する**   
これで、最善のモデルが識別されたため、最後のパイプラインを構築して、すべてのトランスフォーマーと最善のモデルに適合させることができます。 


```python
random_forest_model_final = RegressionForecaster(estimator=RandomForestRegressor(100),make_grain_features=False)
pipeline_ml.add_pipeline_step('random_forest_estimator', random_forest_model_final)
pipeline_ml_fitted = pipeline_ml.fit(train_imputed_tsdf)
final_prediction = pipeline_ml_fitted.predict(test_tsdf)
final_median_ape = final_prediction.calc_error(err_name='MedianAPE', err_fun=calc_median_ape)
print('Median of APE of final pipeline: {0}'.format(final_median_ape))
```

    F1 2018-05-04 11:07:04,108 INFO azureml.timeseries - pipeline fit started. 
    F1 2018-05-04 11:07:43,121 INFO azureml.timeseries - pipeline fit finished. Time elapsed 0:00:39.012880
    F1 2018-05-04 11:07:43,136 INFO azureml.timeseries - pipeline predict started. 
    F1 2018-05-04 11:08:03,564 INFO azureml.timeseries - pipeline predict finished. Time elapsed 0:00:20.428147
    Median of APE of final pipeline: 42.54336821266968
    

## <a name="visualization"></a>グラフ
`ForecastDataFrame` クラスには、予測結果を視覚化し、分析するためのプロット関数が用意されています。 データには一般的に使用されているグラフを使用します。 使用できるすべての関数のプロット関数については、以下のサンプル Notebook を参照してください。 

`show_error` 関数は、任意の列で集計されたパフォーマンス メトリックをプロットします。 既定で、`show_error` 関数は `ForecastDataFrame` の`grain_colnames` で集計されます。 多くの場合、最高のパフォーマンスまたは最低のパフォーマンスのグレイン/グループを特定することは便利です。特に、時系列が多数ある場合に便利です。 `show_error` の `performance_percent` 引数を使用すると、パフォーマンスの間隔を指定し、グレイン/グループのサブセットのエラーをプロットできます。

下位 5% のパフォーマンス (つまり上位 5% の MedianAPE) のグレインをプロットします。


```python
fig, ax = best_of_forecaster_prediction.show_error(err_name='MedianAPE', err_fun=calc_median_ape, performance_percent=(0.95, 1))
```

![png](./media/how-to-build-deploy-forecast-models/output_59_0.png)


上位 5% のパフォーマンス (つまり下位 5% の MedianAPE) のグレインをプロットします。


```python
fig, ax = best_of_forecaster_prediction.show_error(err_name='MedianAPE', err_fun=calc_median_ape, performance_percent=(0, 0.05))
```


![png](./media/how-to-build-deploy-forecast-models/output_61_0.png)


全体的なパフォーマンスの概要を把握したら、個々のグレイン、特にパフォーマンスが低いグレインを調べることができます。 `plot_forecast_by_grain` メソッドは、指定されたグレインの予測と実績をプロットします。 ここでは、`show_error` のプロットで検出された最高のパフォーマンスのグレインと、最低のパフォーマンスのグレインをプロットします。


```python
fig_ax = best_of_forecaster_prediction.plot_forecast_by_grain(grains=[(33, 'tropicana'), (128, 'minute.maid')])
```


![png](./media/how-to-build-deploy-forecast-models/output_63_0.png)



![png](./media/how-to-build-deploy-forecast-models/output_63_1.png)



## <a name="additional-notebooks"></a>その他の Notebook
AMLPF の主な機能の詳細については、次のノートブックで各機能の詳細と例を参照してください。  
[TimeSeriesDataFrame に関する Notebook](https://azuremlftkrelease.blob.core.windows.net/samples/feature_notebooks/Introduction_to_TimeSeriesDataFrames.ipynb)  
[データ ラングリングに関する Notebook](https://azuremlftkrelease.blob.core.windows.net/samples/feature_notebooks/Data_Wrangling_Sample.ipynb)  
[トランスフォーマーに関する Notebook](https://azuremlftkrelease.blob.core.windows.net/samples/feature_notebooks/Forecast_Package_Transforms.ipynb)  
[モデルに関する Notebook](https://azuremlftkrelease.blob.core.windows.net/samples/feature_notebooks/AMLPF_models_sample_notebook.ipynb)  
[クロス検証に関する Notebook](https://azuremlftkrelease.blob.core.windows.net/samples/feature_notebooks/Time_Series_Cross_Validation.ipynb)  
[ラグ トランスフォーマーと OriginTime に関する Notebook](https://azuremlftkrelease.blob.core.windows.net/samples/feature_notebooks/Constructing_Lags_and_Explaining_Origin_Times.ipynb)  
[プロット関数に関する Notebook](https://azuremlftkrelease.blob.core.windows.net/samples/feature_notebooks/Plotting_Functions_in_AMLPF.ipynb)

## <a name="operationalization"></a>運用化

このセクションでは、パイプラインを Azure Machine Learning Web サービスとして配置し、トレーニングとスコア付けを行うために使用します。
現在、配置には、適合されていないパイプラインのみがサポートされています。 デプロイされた Web サービスをスコア付けすると、モデルが再度トレーニングされ、新しいデータで予測が生成されます。

### <a name="set-model-deployment-parameters"></a>モデル配置パラメーターを設定する

次のパラメーターを独自の値に変更します。 Azure Machine Learning 環境、モデル管理アカウント、およびリソース グループが同じリージョン内にあることを確認してください。


```python
azure_subscription = '<subscription name>'

# Two deployment modes are supported: 'local' and 'cluster'. 
# 'local' deployment deploys to a local docker container.
# 'cluster' deployment deploys to a Azure Container Service Kubernetes-based cluster
deployment_type = '<deployment mode>'

# The deployment environment name. 
# This could be an existing environment or a new environment to be created automatically.
aml_env_name = '<deployment env name>'

# The resource group that contains the Azure resources related to the AML environment.
aml_env_resource_group = '<env resource group name>'

# The location where the Azure resources related to the AML environment are located at.
aml_env_location = '<env resource location>'

# The AML model management account name. This could be an existing model management account a new model management 
# account to be created automatically. 
model_management_account_name = '<model management account name>'

# The resource group that contains the Azure resources related to the model management account.
model_management_account_resource_group = '<model management account resource group>'

# The location where the Azure resources related to the model management account are located at.
model_management_account_location = '<model management account location>'

# The name of the deployment/web service.
deployment_name = '<web service name>'

# The directory to store deployment related files, such as pipeline pickle file, score script, 
# and conda dependencies file. 
deployment_working_directory = '<local working directory>'
```

### <a name="define-the-azure-machine-learning-environment-and-deployment"></a>Azure Machine Learning 環境とデプロイを定義する


```python
aml_settings = AMLSettings(azure_subscription=azure_subscription,
                     env_name=aml_env_name, 
                     env_resource_group=aml_env_resource_group,
                     env_location=aml_env_location, 
                     model_management_account_name=model_management_account_name, 
                     model_management_account_resource_group=model_management_account_resource_group,
                     model_management_account_location=model_management_account_location,
                     cluster=deployment_type)

random_forest_model_deploy = RegressionForecaster(estimator=RandomForestRegressor(),make_grain_features=False)
pipeline_deploy = AzureMLForecastPipeline([('drop_columns', column_dropper), 
                                           ('fillna_imputer', fillna_imputer),
                                           ('time_index_featurizer', time_index_featurizer),
                                           ('random_forest_estimator', random_forest_model_deploy)
                                          ])

aml_deployment = ForecastWebserviceFactory(deployment_name=deployment_name,
                                           aml_settings=aml_settings, 
                                           pipeline=pipeline_deploy,
                                           deployment_working_directory=deployment_working_directory,
                                           ftk_wheel_loc='https://azuremlftkrelease.blob.core.windows.net/dailyrelease/azuremlftk-0.1.18165.29a1-py3-none-any.whl')
```

### <a name="create-the-web-service"></a>Web サービスを作成する


```python
# This step can take 5 to 20 minutes
aml_deployment.deploy()
```

### <a name="score-the-web-service"></a>Web サービスをスコア付けする

小規模のデータセットをスコア付けするには、[score](https://docs.microsoft.com/python/api/ftk.operationalization.deployment.amlwebservice) メソッドを使用して、すべてのデータ用の 1 つの Web サービス呼び出しを送信します。


```python
# Need to add empty prediction columns to the validation data frame and create a ForecastDataFrame.
# The scoring API will be updated in later versions to take TimeSeriesDataFrame directly. 
validate_tsdf = test_tsdf.assign(PointForecast=0.0, DistributionForecast=np.nan)
validate_fcast = ForecastDataFrame(validate_tsdf, pred_point='PointForecast', pred_dist='DistributionForecast')

# Define Score Context
score_context = ScoreContext(input_training_data_tsdf=train_imputed_tsdf,
                             input_scoring_data_fcdf=validate_fcast, 
                             pipeline_execution_type='train_predict')

# Get deployed web service
aml_web_service = aml_deployment.get_deployment()

# Score the web service
results = aml_web_service.score(score_context=score_context)
```

大規模なデータセットをスコア付けするには、[並列スコアリング](https://docs.microsoft.com/python/api/ftk.operationalization.deployment.amlwebservice) モードを使用して、複数の Web サービスの呼び出し (データのグループごとに 1 つ) を送信します。


```python
results = aml_web_service.score(score_context=score_context, method='parallel')
```

## <a name="next-steps"></a>次の手順

次の記事で、Azure Machine Learning Package for Forecasting の詳細を確認します。

+ [パッケージの概要とインストール方法](https://aka.ms/aml-packages/forecasting)を確認します。

+ パッケージの[リファレンス ドキュメント](https://aka.ms/aml-packages/forecasting)を確認します。

+ [Azure Machine Learning 用の他の Python パッケージ](reference-python-package-overview.md)を確認します。
