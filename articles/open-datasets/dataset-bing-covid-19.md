---
title: Bing COVID-19
titleSuffix: Azure Open Datasets
description: Azure Open Datasets で Bing COVID-19 データセットを使用する方法について説明します。
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: 7d797e169a0f1fbeeceaf377e731a051112c68e3
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/22/2021
ms.locfileid: "114452301"
---
# <a name="bing-covid-19"></a>Bing COVID-19

Bing COVID-19 データには、すべてのリージョンからの確定、重症、回復した症例が含まれ、毎日更新されます。
このデータは [Bing COVID-19 トラッカー](https://bing.com/covid)で反映されています。

Bing は、[世界保健機関 (WHO)](https://www.who.int/emergencies/diseases/novel-coronavirus-2019)、[アメリカ疾病予防管理センター (CDC)](https://www.cdc.gov/coronavirus/2019-ncov/index.html)、国家および州の公的医療部門[、BNO News](https://bnonews.com/index.php/2020/04/the-latest-coronavirus-cases/)、[24/7 Wall St.](https://247wallst.com/)、[Wikipedia](https://en.wikipedia.org/wiki/2019%E2%80%9320_coronavirus_pandemic) など、複数の信頼できるソースからデータを収集しています。

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

## <a name="datasets"></a>データセット
編集されたデータセットは、CSV、JSON、JSON-Lines、Parquet で提供されます。

- https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/bing_covid-19_data/latest/bing_covid-19_data.csv
- https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/bing_covid-19_data/latest/bing_covid-19_data.json
- https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/bing_covid-19_data/latest/bing_covid-19_data.jsonl
- https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/bing_covid-19_data/latest/bing_covid-19_data.parquet

すべての編集済みデータセットには ISO 3166 下位コードと読み込み時間が追加されており、小文字の列名とアンダースコアの区切り文字が使用されています。

生データ: https://pandemicdatalake.blob.core.windows.net/public/raw/covid-19/bing_covid-19_data/latest/Bing-COVID19-Data.csv

以前のバージョンの編集済み、および生データ: https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/bing_covid-19_data/

## <a name="data-volume"></a>データ ボリューム
すべてのデータセットは毎日更新されます。 2020 年 5 月 11 日時点で、125,576 行 (CSV 16.1 MB, JSON 40.0 MB, JSONL 39.6 MB, Parquet 1.1 MB) が含まれています。

## <a name="license-and-use-rights-attribution"></a>ライセンスと使用権の帰属
本データは、[ご契約条件](https://github.com/microsoft/Bing-COVID-19-Data/blob/master/LICENSE.txt)に従い、医学研究、政府機関、学術機関などの教育および学術目的に限定して使用できます。

出版物で使用または引用されているデータには、'Bing COVID-19 トラッカー' の帰属と www.bing.com/covid のリンクを含める必要があります。

## <a name="contact"></a>Contact
ご質問がある場合や、このデータセットおよび COVID-19 Data Lake の別のデータセットについてのご意見がある場合は、お問い合わせくださいaskcovid19dl@microsoft.com。

## <a name="columns"></a>[列]

| Name             | データ型 | 一意    | 値 (サンプル)                    | 説明                                                          |
|------------------|-----------|-----------|------------------------------------|----------------------------------------------------------------------|
| admin_region_1   | string    | 864       | Texas Georgia                      | country_region 内のリージョン                                         |
| admin_region_2   | string    | 3,143     | Washington County Jefferson County | admin_region_1 内のリージョン                                         |
| confirmed        | INT       | 120,692   | 1 2                                | リージョンの確定者数                                  |
| confirmed_change | INT       | 12,120    | 1 2                                | 前日からの確定者数の変更                 |
| country_region   | string    | 237       | United States India                | 国/リージョン                                                       |
| deaths           | INT       | 20,616    | 1 2                                | リージョンの死亡者数                                      |
| deaths_change    | smallint  | 1,981     | 1 2                                | 前日からの死亡者の変更                          |
| id               | INT       | 1,783,534 | 742546 69019298                    | 一意識別子                                                    |
| iso_subdivision  | string    | 484       | US-TX US-GA                        | 2 つの部分から成る ISO 下位コード                                        |
| iso2             | string    | 226       | US IN                              | 2 文字の国番号識別子                                     |
| iso3             | string    | 226       | USA IND                            | 3 文字の国番号識別子                                     |
| 緯度 (latitude)         | double    | 5,675     | 42.28708 19.59852                  | リージョンの中心の緯度                               |
| load_time        | timestamp | 1         | 2021-04-26 00:06:34.719000         | ファイルが GitHub 上の Bing ソースから読み込まれた日時 |
| 経度 (longitude)        | double    | 5,693     | -2.5396 -155.5186                  | リージョンの中心の経度                              |
| recovered        | INT       | 73,287    | 1 2                                | リージョンの回復者数                                       |
| recovered_change | INT       | 10,441    | 1 2                                | 前日からの回復者数の変更                 |
| 更新済み          | date      | 457       | 2021-04-23 2021-04-22              | レコードの現時点                                        |

## <a name="preview"></a>プレビュー

| id     | 更新済み    | confirmed | deaths | iso2 | iso3 | country_region | admin_region_1 | iso_subdivision | admin_region_2 | load_time             | confirmed_change | deaths_change |
|--------|------------|-----------|--------|------|------|----------------|----------------|-----------------|----------------|-----------------------|------------------|---------------|
| 338995 | 2020-01-21 | 262       | 0      | null | null | 世界全域      | null           | null            | null           | 4/26/2021 12:06:34 AM |                  |               |
| 338996 | 2020-01-22 | 313       | 0      | null | null | 世界全域      | null           | null            | null           | 4/26/2021 12:06:34 AM | 51               | 0             |
| 338997 | 2020-01-23 | 578       | 0      | null | null | 世界全域      | null           | null            | null           | 4/26/2021 12:06:34 AM | 265              | 0             |
| 338998 | 2020-01-24 | 841       | 0      | null | null | 世界全域      | null           | null            | null           | 4/26/2021 12:06:34 AM | 263              | 0             |
| 338999 | 2020-01-25 | 1320      | 0      | null | null | 世界全域      | null           | null            | null           | 4/26/2021 12:06:34 AM | 479              | 0             |
| 339000 | 2020-01-26 | 2014      | 0      | null | null | 世界全域      | null           | null            | null           | 4/26/2021 12:06:34 AM | 694              | 0             |
| 339001 | 2020-01-27 | 2798      | 0      | null | null | 世界全域      | null           | null            | null           | 4/26/2021 12:06:34 AM | 784              | 0             |
| 339002 | 2020-01-28 | 4593      | 0      | null | null | 世界全域      | null           | null            | null           | 4/26/2021 12:06:34 AM | 1795             | 0             |
| 339003 | 2020-01-29 | 6065      | 0      | null | null | 世界全域      | null           | null            | null           | 4/26/2021 12:06:34 AM | 1472             | 0             |
| 339004 | 2020-01-30 | 7818      | 0      | null | null | 世界全域      | null           | null            | null           | 4/26/2021 12:06:34 AM | 1753             | 0             |

## <a name="data-access"></a>データ アクセス

### <a name="azure-notebooks"></a>Azure Notebooks

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=bing-covid-19-data -->

> [!TIP]
> **[代わりにノートブックをダウンロードします](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=bing-covid-19-data)** 。

#### <a name="this-notebook-documents-the-urls-and-sample-code-to-access-the-bing-covid-19-dataset"></a>このノートブックには、[Bing COVID-19 データセット](https://github.com/microsoft/Bing-COVID-19-Data)にアクセスするための URL とサンプル コードが記載されています。

Azure Blob Storage にホストされている特定のファイル形式を取得するには、次の URL を使用します。

CSV: https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/bing_covid-19_data/latest/bing_covid-19_data.csv

JSON: https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/bing_covid-19_data/latest/bing_covid-19_data.json

JSONL: https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/bing_covid-19_data/latest/bing_covid-19_data.jsonl

Parquet: https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/bing_covid-19_data/latest/bing_covid-19_data.parquet

Pandas の組み込みのダウンロード機能を使用して、データセット ファイルを http URL からダウンロードします。 Pandas には、次のようなさまざまなファイル形式のリーダーがあります。

https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.read_parquet.html

https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.read_csv.html


```python
import pandas as pd
import numpy as np
%matplotlib inline
import matplotlib.pyplot as plt

df = pd.read_parquet("https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/bing_covid-19_data/latest/bing_covid-19_data.parquet")
df.head(10)
```

さまざまなフィールドのデータ型を確認し、更新された列が datetime 形式であることを確認してみましょう

```python
df.dtypes
```

次に、世界規模のデータを調べ、簡単なグラフをプロットしてデータを可視化します

```python
df_Worldwide=df[df['country_region']=='Worldwide']
```

```python
df_Worldwide_pivot=df_Worldwide.pivot_table(df_Worldwide, index=['country_region','updated'])

df_Worldwide_pivot
```

```python
df_Worldwide.plot(kind='line',x='updated',y="confirmed",grid=True)
df_Worldwide.plot(kind='line',x='updated',y="deaths",grid=True)
df_Worldwide.plot(kind='line',x='updated',y="confirmed_change",grid=True)
df_Worldwide.plot(kind='line',x='updated',y="deaths_change",grid=True)
```

<!-- nbend -->

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

このプラットフォームとパッケージの組み合わせでは、サンプルは利用できません。

---

### <a name="azure-databricks"></a>Azure Databricks

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

このプラットフォームとパッケージの組み合わせでは、サンプルは利用できません。

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=bing-covid-19-data -->

> [!TIP]
> **[代わりにノートブックをダウンロードします](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=bing-covid-19-data)** 。

```python
# Azure storage access info
blob_account_name = "pandemicdatalake"
blob_container_name = "public"
blob_relative_path = "curated/covid-19/bing_covid-19_data/latest/bing_covid-19_data.parquet"
blob_sas_token = r""
```

```python
# Allow SPARK to read from Blob remotely
wasbs_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)
spark.conf.set(
  'fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name),
  blob_sas_token)
print('Remote blob path: ' + wasbs_path)
```

```python
# SPARK read parquet, note that it won't load any data yet by now
df = spark.read.parquet(wasbs_path)
print('Register the DataFrame as a SQL temporary view: source')
df.createOrReplaceTempView('source')
```

```python
# Display top 10 rows
print('Displaying top 10 rows: ')
display(spark.sql('SELECT * FROM source LIMIT 10'))
```

<!-- nbend -->

---

### <a name="azure-synapse"></a>Azure Synapse

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

このプラットフォームとパッケージの組み合わせでは、サンプルは利用できません。

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=bing-covid-19-data -->

> [!TIP]
> **[代わりにノートブックをダウンロードします](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=bing-covid-19-data)** 。

```python
# Azure storage access info
blob_account_name = "pandemicdatalake"
blob_container_name = "public"
blob_relative_path = "curated/covid-19/bing_covid-19_data/latest/bing_covid-19_data.parquet"
blob_sas_token = r""
```

```python
# Allow SPARK to read from Blob remotely
wasbs_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)
spark.conf.set(
  'fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name),
  blob_sas_token)
print('Remote blob path: ' + wasbs_path)
```

```python
# SPARK read parquet, note that it won't load any data yet by now
df = spark.read.parquet(wasbs_path)
print('Register the DataFrame as a SQL temporary view: source')
df.createOrReplaceTempView('source')
```

```python
# Display top 10 rows
print('Displaying top 10 rows: ')
display(spark.sql('SELECT * FROM source LIMIT 10'))
```

<!-- nbend -->

---

## <a name="next-steps"></a>次のステップ

[Open Datasets カタログ](dataset-catalog.md)の残りのデータセットを表示します。
