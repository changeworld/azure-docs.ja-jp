---
title: COVID 追跡プロジェクト
titleSuffix: Azure Open Datasets
description: Azure Open Datasets で COVID Tracking Project データセットを使用する方法について説明します。
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: 55814a6b1b78673c20447d6129f609058d5c794f
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/22/2021
ms.locfileid: "114453704"
---
# <a name="covid-tracking-project"></a>COVID Tracking Project

COVID Tracking Project データセットは、米国のすべての州と準州からの最新の検査数、感染者数、入院患者数、患者のアウトカムを提供しています。

このデータセットの詳細については、[プロジェクトの GitHub リポジトリ](https://github.com/COVID19Tracking/covid-tracking-data)を参照してください。

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

## <a name="datasets"></a>データセット

データセットの編集済みバージョンは、CSV、JSON、JSON-Lines、Parquet 形式で入手できます。
- https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/covid_tracking/latest/covid_tracking.csv
- https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/covid_tracking/latest/covid_tracking.json
- https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/covid_tracking/latest/covid_tracking.jsonl
- https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/covid_tracking/latest/covid_tracking.parquet

すべての編集済みバージョンには ISO 3166 下位コードと読み込み時間が追加されており、小文字の列名とアンダースコアの区切り文字が使用されています。

生データ: 'https://pandemicdatalake.blob.core.windows.net/public/raw/covid-19/covid_tracking/latest/daily.json '

以前のバージョンの編集済み、および生データ: https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/covid_tracking/

https://pandemicdatalake.blob.core.windows.net/public/raw/covid-19/covid_tracking/

## <a name="data-volume"></a>データ ボリューム
すべてのデータセットは毎日更新されます。 2020 年 5 月 13 日時点で、4,100 行 (CSV 574 KB, JSON 1.8 MB, JSONL 1.8 MB, Parquet 334 KB) が含まれています。

## <a name="data-source"></a>データ ソース

このデータの元の発行元は、Atlantic の COVID Tracking Project です。 生データは、COVID Tracking GitHub リポジトリから [states_daily_4p_et.csv ファイル](https://github.com/COVID19Tracking/covid-tracking-data/blob/master/data/states_daily_4pm_et.csv)を使用して取り込まれています。 COVID Tracking Project API の配信元を含むこのデータセットの詳細については、[プロジェクトの GitHub リポジトリ](https://github.com/COVID19Tracking/covid-tracking-data)を参照してください。

## <a name="data-quality"></a>データ品質
COVID Tracking Project により、各州のデータ品質が評価され、データ品質の評価に関する追加情報が提供されています。 詳細については、[COVID Tracking Project データ ページ](https://covidtracking.com/data)を参照してください。 GitHub リポジトリのデータは API よりも 1 時間遅れる可能性があり、最新のデータにアクセスするには API を使用する必要があります。

## <a name="license-and-use-rights-attribution"></a>ライセンスと使用権の帰属

このデータは、[Apache License 2.0](https://github.com/COVID19Tracking/covid-tracking-data/blob/master/LICENSE) のご契約条件に従ってライセンスされています。

データを使用する場合は、すべての著作権、特許、商標、帰属の表示を保持する必要があります。

## <a name="contact"></a>Contact

COVID-19 Data Lake のこのデータセットまたはその他のデータセットに関する質問またはフィードバックについては、askcovid19dl@microsoft.com にお問い合わせください。

## <a name="columns"></a>[列]

| Name                        | データ型 | 一意 | 値 (サンプル)                                                                   | 説明                                                                                                                 |
|-----------------------------|-----------|--------|-----------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------|
| date                        | date      | 420    | 2020-11-10 2021-01-30                                                             | 毎日の合計が収集された日付。                                                                             |
| date_checked                | string    | 9,487  | 2020-12-01T00:00:00Z 2020-09-01T00:00:00Z                                         | 非推奨                                                                                                                  |
| death                       | smallint  | 7,327  | 2 5                                                                               | これまでに COVID-19 が原因で死亡した人の合計数。                                                        |
| death_increase              | smallint  | 429    | 1 2                                                                               | 非推奨                                                                                                                  |
| fips                        | smallint  | 56     | 26 55                                                                             | 州の国勢調査 FIPS コード。                                                                                             |
| fips_code                   | string    | 60     | 53 25                                                                             | 州の国勢調査 FIPS コード。                                                                                             |
| hash                        | string    | 20,780 | 63df8cccd23a5476bab2d8111b138e4c9becd35e c606cd6990f16086b5382e12d84f6206172d493d | この記録のハッシュ                                                                                                      |
| hospitalized                | INT       | 7,641  | 89995 4                                                                           | 非推奨                                                                                                                  |
| hospitalized_cumulative     | INT       | 7,641  | 89995 4                                                                           | これまでに COVID-19 で入院した人の合計数。回復した、または死亡した人を含む。 |
| hospitalized_currently      | smallint  | 3,886  | 8 13                                                                              | この日に COVID-19 のために入院した人の数。                                                                      |
| hospitalized_increase       | smallint  | 615    | 1 2                                                                               | 非推奨                                                                                                                  |
| in_icu_cumulative           | smallint  | 2,295  | 990 220                                                                           | これまでに COVID-19 のために ICU に入った人の合計数。回復した、または死亡した人を含む。      |
| in_icu_currently            | smallint  | 1,643  | 2 8                                                                               | この日に COVID-19 のために ICU にいる人の合計数。                                                                 |
| iso_country                 | string    | 1      | US                                                                                | ISO 3166 国または地域コード                                                                                             |
| iso_subdivision             | string    | 57     | US-UM US-WA                                                                       | ISO 3166 下位コード                                                                                                   |
| last_update_et              | timestamp | 9,487  | 2020-12-01 00:00:00 2020-09-01 00:00:00                                           | その日のデータが更新された最終時刻                                                                                        |
| load_time                   | timestamp | 1      | 2021-04-26 00:06:49.883000                                                        | ソースから Azure にデータが読み込まれた日時                                                                  |
| 否定的                    | INT       | 10,864 | 305972 2140                                                                       | これまでに COVID-19 の検査で陰性と判定された人の合計数。                                                        |
| negative_increase           | INT       | 7,328  | 6 17                                                                              | 非推奨                                                                                                                  |
| on_ventilator_cumulative    | smallint  | 677    | 411 412                                                                           | これまでに COVID-19 のため人工呼吸器を使用した人の合計数。回復した、または死亡した人を含む。    |
| on_ventilator_currently     | smallint  | 837    | 4 10                                                                              | この日に COVID-19 で人工呼吸器を使用している人の数。                                                               |
| pending                     | smallint  | 944    | 2 17                                                                              | 結果がまだ判定されていない検査の数。                                                                    |
| pos_neg                     | INT       | 18,282 | 2140 2                                                                            | 非推奨                                                                                                                  |
| 肯定的                    | INT       | 16,837 | 2 1                                                                               | これまでに COVID-19 の検査で陽性と判定された人の合計数。                                                        |
| positive_increase           | smallint  | 4,754  | 1 2                                                                               | 非推奨                                                                                                                  |
| recovered                   | INT       | 8,286  | 29 19                                                                             | これまでに COVID-19 から回復した人の合計数。                                                             |
| state                       | string    | 56     | MI PA                                                                             | 州の 2 文字のコード                                                                                              |
| total                       | INT       | 18,283 | 2140 2                                                                            | 非推奨                                                                                                                  |
| total_test_results          | INT       | 18,648 | 2140 3                                                                            | 州が提供した検査結果の合計                                                                                    |
| total_test_results_increase | INT       | 13,463 | 1 2                                                                               | 非推奨                                                                                                                  |

## <a name="preview"></a>プレビュー

| date       | state | 肯定的 | hospitalized_currently | hospitalized_cumulative | on_ventilator_currently | data_quality_grade | last_update_et        | hash                                     | date_checked          | death | hospitalized | total   | total_test_results | pos_neg | fips | death_increase | hospitalized_increase | negative_increase | positive_increase | total_test_results_increase | fips_code | iso_subdivision | load_time             | iso_country | 否定的 | in_icu_cumulative | on_ventilator_cumulative | recovered | in_icu_currently |
|------------|-------|----------|------------------------|-------------------------|-------------------------|--------------------|-----------------------|------------------------------------------|-----------------------|-------|--------------|---------|--------------------|---------|------|----------------|-----------------------|-------------------|-------------------|-----------------------------|-----------|-----------------|-----------------------|-------------|----------|-------------------|--------------------------|-----------|------------------|
| 2021-03-07 | AK    | 56886    | 33                     | 1293                    | 2                       | null               | 3/5/2021 3:59:00 AM   | dc4bccd4bb885349d7e94d6fed058e285d4be164 | 3/5/2021 3:59:00 AM   | 305   | 1293         | 56886   | 1731628            | 56886   | 2    | 0              | 0                     | 0                 | 0                 | 0                           | 2         | US-AK           | 4/26/2021 12:06:49 AM | US          |          |                   |                          |           |                  |
| 2021-03-07 | AL    | 499819   | 494                    | 45976                   |                         | null               | 3/7/2021 11:00:00 AM  | 997207b430824ea40b8eb8506c19a93e07bc972e | 3/7/2021 11:00:00 AM  | 10148 | 45976        | 2431530 | 2323788            | 2431530 | 1    | -1             | 0                     | 2087              | 408               | 2347                        | 1         | US-AL           | 4/26/2021 12:06:49 AM | US          | 1931711  | 2676              | 1515                     | 295690    |                  |
| 2021-03-07 | AR    | 324818   | 335                    | 14926                   | 65                      | null               | 3/7/2021 12:00:00 AM  | 50921aeefba3e30d31623aa495b47fb2ecc72fae | 3/7/2021 12:00:00 AM  | 5319  | 14926        | 2805534 | 2736442            | 2805534 | 5    | 22             | 11                    | 3267              | 165               | 3380                        | 5         | US-AR           | 4/26/2021 12:06:49 AM | US          | 2480716  |                   | 1533                     | 315517    | 141              |
| 2021-03-07 | AS    | 0        |                        |                         |                         | null               | 12/1/2020 12:00:00 AM | 96d23f888c995b9a7f3b4b864de6414f45c728ff | 12/1/2020 12:00:00 AM | 0     |              | 2140    | 2140               | 2140    | 60   | 0              | 0                     | 0                 | 0                 | 0                           | 60        | US-AS           | 4/26/2021 12:06:49 AM | US          | 2140     |                   |                          |           |                  |
| 2021-03-07 | AZ    | 826454   | 963                    | 57907                   | 143                     | null               | 3/7/2021 12:00:00 AM  | 0437a7a96f4471666f775e63e86923eb5cbd8cdf | 3/7/2021 12:00:00 AM  | 16328 | 57907        | 3899464 | 7908105            | 3899464 | 4    | 5              | 44                    | 13678             | 1335              | 45110                       | 4         | US-AZ           | 4/26/2021 12:06:49 AM | US          | 3073010  |                   |                          |           | 273              |
| 2021-03-07 | CA    | 3501394  | 4291                   |                         |                         | null               | 3/7/2021 2:59:00 AM   | 63c5c0fd2daef2fb65150e9db486de98ed3f7b72 | 3/7/2021 2:59:00 AM   |       |              | 3501394 | 49646014           | 3501394 | 6    | 258            | 0                     | 0                 | 3816              | 133186                      | 6         | US-CA           | 4/26/2021 12:06:49 AM | US          |          |                   |                          |           | 1159             |
| 2021-03-07 | CO    | 436602   | 326                    | 23904                   |                         | null               | 3/7/2021 1:59:00 AM   | 444746cda3a596f183f3fa3269c8cab68704e819 | 3/7/2021 1:59:00 AM   | 5989  | 23904        | 2636060 | 6415123            | 2636060 | 8    | 3              | 18                    | 0                 | 840               | 38163                       | 8         | US-CO           | 4/26/2021 12:06:49 AM | US          | 2199458  |                   |                          |           |                  |
| 2021-03-07 | CT    | 285330   | 428                    | 12257                   |                         | null               | 3/4/2021 11:59:00 PM  | bcc0f7bc8c2bf77eec31b25f8b59d510f679d3e7 | 3/4/2021 11:59:00 PM  | 7704  | 12257        | 285330  | 6520366            | 285330  | 9    | 0              | 0                     | 0                 | 0                 | 0                           | 9         | US-CT           | 4/26/2021 12:06:49 AM | US          |          |                   |                          |           |                  |
| 2021-03-07 | DC    | 41419    | 150                    |                         | 16                      | null               | 3/6/2021 12:00:00 AM  | a3aa0d623d538807fb9577ad64354f48cf728cc8 | 3/6/2021 12:00:00 AM  | 1030  |              | 41419   | 1261363            | 41419   | 11   | 0              | 0                     | 0                 | 146               | 5726                        | 11        | US-DC           | 4/26/2021 12:06:49 AM | US          |          |                   |                          | 29570     | 38               |
| 2021-03-07 | DE    | 88354    | 104                    |                         |                         | null               | 3/6/2021 6:00:00 PM   | 059d870e689d5cc19c35f5eb398214d7d9856373 | 3/6/2021 6:00:00 PM   | 1473  |              | 633424  | 1431942            | 633424  | 10   | 9              | 0                     | 917               | 215               | 5867                        | 10        | US-DE           | 4/26/2021 12:06:49 AM | US          | 545070   |                   |                          |           | 13               |

## <a name="data-access"></a>データ アクセス

### <a name="azure-notebooks"></a>Azure Notebooks

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

Azure Blob Storage でホストされているさまざまなデータセット ファイル形式の URL:

CSV: https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/covid_tracking/latest/covid_tracking.csv

JSON: https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/covid_tracking/latest/covid_tracking.json

JSONL: https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/covid_tracking/latest/covid_tracking.jsonl

Parquet: https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/covid_tracking/latest/covid_tracking.parquet

Pandas の組み込みのダウンロード機能を使用して、データセット ファイルを http URL からダウンロードします。 Pandas には、次のようなさまざまなファイル形式のリーダーがあります。

https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.read_parquet.html

https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.read_csv.html

```python
import pandas as pd
import numpy as np
%matplotlib inline
import matplotlib.pyplot as plt

df = pd.read_parquet("https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/covid_tracking/latest/covid_tracking.parquet ")
df.head(10)

df.dtypes

df.groupby('state').first().filter(['date','positive', 'death'])

df.groupby(df.state).agg({'state': 'count','positive_increase': 'sum','death_increase': 'sum'})

df_NY=df[df['state'] == 'NY']
df_NY.plot(kind='line',x='date',y="positive",grid=True)
df_NY.plot(kind='line',x='date',y="positive_increase",grid=True)
df_NY.plot(kind='line',x='date',y="death",grid=True)
df_NY.plot(kind='line',x='date',y="death_increase",grid=True)

df_US=df.groupby(df.date).agg({'positive': 'sum','positive_increase': 'sum','death':'sum','death_increase': 'sum'}).reset_index()

df_US.plot(kind='line',x='date',y="positive",grid=True)
df_US.plot(kind='line',x='date',y="positive_increase",grid=True)
df_US.plot(kind='line',x='date',y="death",grid=True)
df_US.plot(kind='line',x='date',y="death_increase",grid=True)



```

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

このプラットフォームとパッケージの組み合わせでは、サンプルは利用できません。

---

### <a name="azure-databricks"></a>Azure Databricks

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

このプラットフォームとパッケージの組み合わせでは、サンプルは利用できません。

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

```python
# Azure storage access info
blob_account_name = "pandemicdatalake"
blob_container_name = "public"
blob_relative_path = "curated/covid-19/covid_tracking/latest/covid_tracking.parquet"
blob_sas_token = r""

# Allow SPARK to read from Blob remotely
wasbs_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)
spark.conf.set(
  'fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name),
  blob_sas_token)
print('Remote blob path: ' + wasbs_path)

# SPARK read parquet, note that it won't load any data yet by now
df = spark.read.parquet(wasbs_path)
print('Register the DataFrame as a SQL temporary view: source')
df.createOrReplaceTempView('source')

# Display top 10 rows
print('Displaying top 10 rows: ')
display(spark.sql('SELECT * FROM source LIMIT 10'))
```

---

### <a name="azure-synapse"></a>Azure Synapse

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

このプラットフォームとパッケージの組み合わせでは、サンプルは利用できません。

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

```python
# Azure storage access info
blob_account_name = "pandemicdatalake"
blob_container_name = "public"
blob_relative_path = "curated/covid-19/covid_tracking/latest/covid_tracking.parquet"
blob_sas_token = r""

# Allow SPARK to read from Blob remotely
wasbs_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)
spark.conf.set(
  'fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name),
  blob_sas_token)
print('Remote blob path: ' + wasbs_path)

# SPARK read parquet, note that it won't load any data yet by now
df = spark.read.parquet(wasbs_path)
print('Register the DataFrame as a SQL temporary view: source')
df.createOrReplaceTempView('source')

# Display top 10 rows
print('Displaying top 10 rows: ')
display(spark.sql('SELECT * FROM source LIMIT 10'))
```

---

## <a name="next-steps"></a>次のステップ

[Open Datasets カタログ](dataset-catalog.md)の残りのデータセットを表示します。
