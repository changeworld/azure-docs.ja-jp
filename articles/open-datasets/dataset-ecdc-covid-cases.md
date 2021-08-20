---
title: 欧州疾病予防管理センター (ECDC) の COVID-19 のケース
titleSuffix: Azure Open Datasets
description: Azure Open Datasets 内の欧州疾病予防管理センター (ECDC) の COVID-19 のケースのデータセットの使用方法について説明します。
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: 31868ee27e531e70df4c89944f0baf888527a190
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/22/2021
ms.locfileid: "114447602"
---
# <a name="european-centre-for-disease-prevention-and-control-ecdc-covid-19-cases"></a>欧州疾病予防管理センター (ECDC) の COVID-19 のケース

欧州疾病予防管理センター (ECDC) が提供する、COVID-19 のケースの感染者の世界の地理的分布の[最新の公開データ](https://www.ecdc.europa.eu/en/publications-data/download-todays-data-geographic-distribution-covid-19-cases-worldwide)です。 各行/エントリには、日あたり、国または地域あたりの新規感染者数が含まれています。

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

## <a name="datasets"></a>データセット

データセットの編集版は、CSV、JSON、JSON-Lines、Parquet 形式で入手でき、毎日更新されます。
- https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.csv
- https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.json
- https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.jsonl
- https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.parquet

すべての編集済みバージョンには iso_country_region コードと読み込み時間が追加されており、小文字の列名とアンダースコアの区切り文字が使用されています。

生データ: https://pandemicdatalake.blob.core.windows.net/public/raw/covid-19/ecdc_cases/latest/ECDCCases.csv

以前のバージョンの編集済み、および生データ: https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/ https://pandemicdatalake.blob.core.windows.net/public/raw/covid-19/ecdc_cases/

## <a name="data-volume"></a>データ ボリューム
2020 年 5 月 28 日時点で、19,876 行 (CSV 1.5 MB、JSON 4.9 MB、JSONL 4.9 MB、Parquet 54.1 KB) が含まれています。

## <a name="data-source"></a>データ ソース

生データは、毎日 [ECDC の csv ファイル](https://opendata.ecdc.europa.eu/covid19/casedistribution/csv)から取り込まれます。 このデータセットのオリジンを含む詳細については、[ECDC のデータ収集](https://www.ecdc.europa.eu/en/covid-19/data-collection)に関するページを参照してください。

## <a name="data-quality"></a>データ品質
ECDC はデータの正確性と適時性については保証しません。 [免責事項をお読みください](https://www.ecdc.europa.eu/en/covid-19/data-collection)。

## <a name="license-and-use-rights-attribution"></a>ライセンスと使用権、帰属

本データは、こちらの ECDC 著作権ポリシーで許可された範囲内で利用することができます。 著作権が第三者に帰属する文書の複製許可については、著作権者の許諾を得る必要があります。

このデータの提供元として ECDC が常に明示されている必要があります。 この明示は、資料の各コピーに含める必要があります。

## <a name="contact"></a>Contact

ご質問がある場合や、このデータセットおよび COVID-19 Data Lake の別のデータセットについてのご意見がある場合は、お問い合わせくださいaskcovid19dl@microsoft.com。

## <a name="columns"></a>[列]

| Name                      | データ型 | 一意 | 値 (サンプル)            | 説明                            |
|---------------------------|-----------|--------|----------------------------|----------------------------------------|
| cases                     | smallint  | 5,515  | 1 2                        | 報告された感染事例の数               |
| continent_exp             | string    | 6      | ヨーロッパ アフリカ              | 大陸名                         |
| countries_and_territories | string    | 214    | カナダ ベルギー             | 国または準州の名前              |
| country_territory_code    | string    | 213    | KOR ISL                    | 3 文字の国または準州のコード |
| date_rep                  | date      | 350    | 2020-12-11 2020-11-22      | レポートの日付                     |
| day                       | smallint  | 31     | 14 13                      | 月の日付                           |
| deaths                    | smallint  | 1,049  | 1 2                        | 報告された死亡者数              |
| geo_id                    | string    | 214    | CA SE                      | geo 識別子                         |
| iso_country               | string    | 214    | SE US                      | ISO 3166 国または地域コード        |
| load_date                 | timestamp | 1      | 2021-04-26 00:06:22.123000 | データが Azure に読み込まれた日付      |
| month                     | smallint  | 12     | 10 8                       | 月の番号                           |
| year                      | smallint  | 2      | 2020 2019                  | Year                                   |

## <a name="preview"></a>プレビュー

| date_rep   | day | month | year | cases | deaths | countries_and_territories | geo_id | country_territory_code | continent_exp | load_date             | iso_country |
|------------|-----|-------|------|-------|--------|---------------------------|--------|------------------------|---------------|-----------------------|-------------|
| 2020-12-14 | 14  | 12    | 2020 | 746   | 6      | アフガニスタン               | AF     | AFG                    | Asia          | 2021/4/26 午前 12:06:22 | AF          |
| 2020-12-13 | 13  | 12    | 2020 | 298   | 9      | アフガニスタン               | AF     | AFG                    | Asia          | 2021/4/26 午前 12:06:22 | AF          |
| 2020-12-12 | 12  | 12    | 2020 | 113   | 11     | アフガニスタン               | AF     | AFG                    | Asia          | 2021/4/26 午前 12:06:22 | AF          |
| 2020-12-11 | 11  | 12    | 2020 | 63    | 10     | アフガニスタン               | AF     | AFG                    | Asia          | 2021/4/26 午前 12:06:22 | AF          |
| 2020-12-10 | 10  | 12    | 2020 | 202   | 16     | アフガニスタン               | AF     | AFG                    | Asia          | 2021/4/26 午前 12:06:22 | AF          |
| 2020-12-09 | 9   | 12    | 2020 | 135   | 13     | アフガニスタン               | AF     | AFG                    | Asia          | 2021/4/26 午前 12:06:22 | AF          |
| 2020-12-08 | 8   | 12    | 2020 | 200   | 6      | アフガニスタン               | AF     | AFG                    | Asia          | 2021/4/26 午前 12:06:22 | AF          |
| 2020-12-07 | 7   | 12    | 2020 | 210   | 26     | アフガニスタン               | AF     | AFG                    | Asia          | 2021/4/26 午前 12:06:22 | AF          |
| 2020-12-06 | 6   | 12    | 2020 | 234   | 10     | アフガニスタン               | AF     | AFG                    | Asia          | 2021/4/26 午前 12:06:22 | AF          |
| 2020-12-05 | 5   | 12    | 2020 | 235   | 18     | アフガニスタン               | AF     | AFG                    | Asia          | 2021/4/26 午前 12:06:22 | AF          |

## <a name="data-access"></a>データ アクセス

### <a name="azure-notebooks"></a>Azure Notebooks

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

このノートブックには、Azure Blob Storage にホストされているさまざまなデータセット ファイル形式の欧州疾病予防管理センター (ECDC) の COVID-19 のケースのデータセット URL にアクセスするための URL とサンプル コードが記載されています。¶ CSV: https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.csv

JSON: https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.json

JSONL: https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.jsonl

Parquet: https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.parquet

Pandas の組み込みのダウンロード機能を使用して、データセット ファイルを http URL からダウンロードします。 Pandas には、次のようなさまざまなファイル形式のリーダーがあります。

https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.read_parquet.html

https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.read_csv.html


```python
import pandas as pd
import numpy as np
%matplotlib inline
import matplotlib.pyplot as plt

df = pd.read_parquet("https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.parquet")
df.head(10)

df.dtypes

df.groupby('countries_and_territories').first().filter(['continent_exp','cases', 'deaths','date_rep'])

df.groupby('continent_exp').agg({'countries_and_territories': 'count','cases': 'count','deaths': 'count'})

import plotly.graph_objects as go
import plotly.express as px
import matplotlib.pyplot as plt

df.loc[: , ['countries_and_territories', 'cases', 'deaths']].groupby(['countries_and_territories'
         ]).max().sort_values(by='cases',ascending=False).reset_index()[:15].style.background_gradient(cmap='rainbow')

df_Worldwide=df[df['countries_and_territories']=='United_States_of_America']

df.plot(kind='line',x='date_rep',y="cases",grid=True)
df.plot(kind='line',x='date_rep',y="deaths",grid=True)
#df_Worldwide.plot(kind='line',x='date_rep',y="confirmed_change",grid=True)
#df_Worldwide.plot(kind='line',x='date_rep',y="deaths_change",grid=True)

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
blob_relative_path = "curated/covid-19/ecdc_cases/latest/ecdc_cases.parquet"
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
blob_relative_path = "curated/covid-19/ecdc_cases/latest/ecdc_cases.parquet"
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

## <a name="examples"></a>例

このデータセットの使用例を参照してください。

- [Synapse SQL サーバーレス エンドポイントを使用して COVID データを分析する](https://techcommunity.microsoft.com/t5/azure-synapse-analytics/how-azure-synapse-analytics-helps-you-analyze-covid-data-with/ba-p/1457836)
- [Azure Synapse Analytics での SQL エンドポイントを使用した COVID データの線形回帰分析](https://techcommunity.microsoft.com/t5/azure-synapse-analytics/linear-regression-analysis-on-covid-data-using-sql-endpoint-in/ba-p/1468697)

## <a name="next-steps"></a>次のステップ

[Open Datasets カタログ](dataset-catalog.md)の残りのデータセットを表示します。
