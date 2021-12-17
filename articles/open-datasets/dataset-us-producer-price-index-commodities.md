---
title: 米国生産者物価指数 - 商品
titleSuffix: Azure Open Datasets
description: Azure Open Datasets で米国生産者物価指数 - 商品データセットを使用する方法について説明します。
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: 224a5d0278237d5a7d87ee8e3c9adaedb71f193a
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/11/2021
ms.locfileid: "112038913"
---
# <a name="us-producer-price-index---commodities"></a>米国生産者物価指数 - 商品

生産者物価指数 (PPI) は、国内生産者がその生産物に対して得た販売価格の経時的な平均的変動を測定するものです。 PPI に含まれる価格は、対象となる製品およびサービスの最初の商取引時点での価格です。

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

個々の製品と製品グループの約 10,000 の PPI が毎月発表されます。 PPI は、米国経済の製造部門 (鉱業、製造業、農業、漁業、林業) のほぼあらゆる業種の生産高と、天然ガス、電気、建設、および製造部門で製造されるものに対する競争力があるもの (廃棄物や廃材など) の生産高に利用できます。 PPI プログラムは、サービス部門の生産高の約 72% を対象としています。この評価は、2007 年の経済国勢調査で報告された収益に基づいています。 データには、卸売/小売、輸送/倉庫、情報、金融/保険、不動産仲介/レンタル/リース、専門的/科学的/技術的サービス、管理/サポート/廃棄物管理サービス、医療/社会的支援、宿泊施設の各部門の業種が含まれます。

このデータセットの詳細情報が含まれた [README](https://download.bls.gov/pub/time.series/wp/wp.txt) ファイルは、[データセットの元の場所](https://download.bls.gov/pub/time.series/wp/)で入手できます。 追加情報は [FAQ](https://www.bls.gov/ppi/ppifaq.htm) で提供されています。

このデータセットは、[米国労働統計局 (BLS)](https://www.bls.gov/) によって公開されている [Producer Price Indexes (生産者物価指数) のデータ](https://www.bls.gov/ppi/)から生成されています。 このデータセットの使用に関する諸条件については、「[Linking and Copyright Information (リンクおよび著作権情報)](https://www.bls.gov/bls/linksite.htm)」と「[Important Web Site Notices (Web サイトに関する重要な通知)](https://www.bls.gov/bls/website-policies.htm)」を確認してください。

## <a name="storage-location"></a>保存先

このデータセットは、米国東部 Azure リージョンに保存されています。 アフィニティのために、米国東部でコンピューティング リソースを割り当てることをお勧めします。

## <a name="related-datasets"></a>関連データセット

- [米国消費者物価指数](dataset-us-consumer-price-index.md)
- [米国生産者物価指数 - 業種](dataset-us-producer-price-index-industry.md)

## <a name="columns"></a>[列]

| Name | データ型 | 一意 | 値 (サンプル) | 説明 |
|-|-|-|-|-|
| footnote_codes | string | 3 | nan P | データ系列の脚注を示します。 ほとんどの値が null です。 以下を参照してください。https://download.bls.gov/pub/time.series/wp/wp.footnote |
| group_code | string | 56 | 02 01 | インデックスの対象となる主要な商品グループを識別するコード。 グループ コードと名前については、 https://download.bls.gov/pub/time.series/wp/wp.group をご覧ください。 |
| group_name | string | 56 | Processed foods and feeds Farm products | インデックスの対象となる主要な商品グループの名前。 グループ コードと名前については、 https://download.bls.gov/pub/time.series/wp/wp.group をご覧ください。 |
| item_code | string | 2,949 | 1 11 | データ観測の対象となる品目を示します。 品目コードと品目名については、 https://download.bls.gov/pub/time.series/wp/wp.item をご覧ください。 |
| item_name | string | 3,410 | Warehousing, storage, and related services Passenger car rental | 品目のフル ネーム。 品目コードと品目名については、 https://download.bls.gov/pub/time.series/wp/wp.item をご覧ください。 |
| period | string | 13 | M06 M07 | データが観測される期間を示します。 期間の値の一覧は、 https://download.bls.gov/pub/time.series/wp/wp.period をご覧ください。 |
| 季節 | string | 2 | U S | データが季節調整されているかどうかを識別するコード。 S = 季節調整済み、U = 未調整 |
| series_id | string | 5,458 | WPU101 WPU091 | 特定の系列を識別するコード。 時系列とは、一定の時間間隔で長期間にわたって観測された一連のデータを指します。 コード、名前、開始年、終了年などの系列の詳細については、 https://download.bls.gov/pub/time.series/wp/wp.series をご覧ください。 |
| series_title | string | 4,379 | PPI Commodity data for Mining services, not seasonally adjusted PPI Commodity data for Metal treatment services, not seasonally adjusted | 特定の系列のタイトル。 時系列とは、一定の時間間隔で長期間にわたって観測された一連のデータを指します。 ID、名前、開始年、終了年などの系列の詳細については、 https://download.bls.gov/pub/time.series/wp/wp.series を参照してください。 |
| value | float | 6,788 | 100.0 99.0999984741211 | 商品の物価指数。 |
| year | INT | 26 | 2018 2017 | 観測の年を示します。 |

## <a name="preview"></a>プレビュー

| item_code | group_code | series_id | year | period | value | footnote_codes | 季節 | series_title | group_name | item_name |
|-|-|-|-|-|-|-|-|-|-|-|
| 120922 | 05 | WPU05120922 | 2008 | M06 | 100 | nan | U | PPI Commodity data for Fuels and related products and power-Prepared bituminous coal underground mine, mechanically crushed/screened/sized only, not seasonally adjusted | Fuels and related products and power | Prepared bituminous coal underground mine, mechanically crushed/screened/sized only |
| 120922 | 05 | WPU05120922 | 2008 | M07 | 104.6 | nan | U | PPI Commodity data for Fuels and related products and power-Prepared bituminous coal underground mine, mechanically crushed/screened/sized only, not seasonally adjusted | Fuels and related products and power | Prepared bituminous coal underground mine, mechanically crushed/screened/sized only |
| 120922 | 05 | WPU05120922 | 2008 | M08 | 104.4 | nan | U | PPI Commodity data for Fuels and related products and power-Prepared bituminous coal underground mine, mechanically crushed/screened/sized only, not seasonally adjusted | Fuels and related products and power | Prepared bituminous coal underground mine, mechanically crushed/screened/sized only |
| 120922 | 05 | WPU05120922 | 2008 | M09 | 98.3 | nan | U | PPI Commodity data for Fuels and related products and power-Prepared bituminous coal underground mine, mechanically crushed/screened/sized only, not seasonally adjusted | Fuels and related products and power | Prepared bituminous coal underground mine, mechanically crushed/screened/sized only |
| 120922 | 05 | WPU05120922 | 2008 | M10 | 101.5 | nan | U | PPI Commodity data for Fuels and related products and power-Prepared bituminous coal underground mine, mechanically crushed/screened/sized only, not seasonally adjusted | Fuels and related products and power | Prepared bituminous coal underground mine, mechanically crushed/screened/sized only |
| 120922 | 05 | WPU05120922 | 2008 | M11 | 95.2 | nan | U | PPI Commodity data for Fuels and related products and power-Prepared bituminous coal underground mine, mechanically crushed/screened/sized only, not seasonally adjusted | Fuels and related products and power | Prepared bituminous coal underground mine, mechanically crushed/screened/sized only |

## <a name="data-access"></a>データ アクセス

### <a name="azure-notebooks"></a>Azure Notebooks

# <a name="azureml-opendatasets"></a>[azureml-opendatasets](#tab/azureml-opendatasets)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=us-producer-price-index-commodities -->

> [!TIP]
> **[代わりにノートブックをダウンロードします](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=us-producer-price-index-commodities)** 。

```python
# This is a package in preview.
from azureml.opendatasets import UsLaborPPICommodity

labor = UsLaborPPICommodity()
labor_df = labor.to_pandas_dataframe()
```

```python
labor_df.info()
```

<!-- nbend -->


# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=us-producer-price-index-commodities -->

> [!TIP]
> **[代わりにノートブックをダウンロードします](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=us-producer-price-index-commodities)** 。

```python
# Pip install packages
import os, sys

!{sys.executable} -m pip install azure-storage-blob
!{sys.executable} -m pip install pyarrow
!{sys.executable} -m pip install pandas
```

```python
# Azure storage access info
azure_storage_account_name = "azureopendatastorage"
azure_storage_sas_token = r""
container_name = "laborstatisticscontainer"
folder_name = "ppi_commodity/"
```

```python
from azure.storage.blob import BlockBlobServicefrom azure.storage.blob import BlobServiceClient, BlobClient, ContainerClient

if azure_storage_account_name is None or azure_storage_sas_token is None:
    raise Exception(
        "Provide your specific name and key for your Azure Storage account--see the Prerequisites section earlier.")

print('Looking for the first parquet under the folder ' +
      folder_name + ' in container "' + container_name + '"...')
container_url = f"https://{azure_storage_account_name}.blob.core.windows.net/"
blob_service_client = BlobServiceClient(
    container_url, azure_storage_sas_token if azure_storage_sas_token else None)

container_client = blob_service_client.get_container_client(container_name)
blobs = container_client.list_blobs(folder_name)
sorted_blobs = sorted(list(blobs), key=lambda e: e.name, reverse=True)
targetBlobName = ''
for blob in sorted_blobs:
    if blob.name.startswith(folder_name) and blob.name.endswith('.parquet'):
        targetBlobName = blob.name
        break

print('Target blob to download: ' + targetBlobName)
_, filename = os.path.split(targetBlobName)
blob_client = container_client.get_blob_client(targetBlobName)
with open(filename, 'wb') as local_file:
    blob_client.download_blob().download_to_stream(local_file)
```

```python
# Read the parquet file into Pandas data frame
import pandas as pd

print('Reading the parquet file into Pandas data frame')
df = pd.read_parquet(filename)
```

```python
# you can add your filter at below
print('Loaded as a Pandas data frame: ')
df
```

<!-- nbend -->

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

このプラットフォームとパッケージの組み合わせでは、サンプルは利用できません。

---

### <a name="azure-databricks"></a>Azure Databricks

# <a name="azureml-opendatasets"></a>[azureml-opendatasets](#tab/azureml-opendatasets)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=azureml-opendatasets&registryId=us-producer-price-index-commodities -->

> [!TIP]
> **[代わりにノートブックをダウンロードします](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=azureml-opendatasets&registryId=us-producer-price-index-commodities)** 。

```python
# This is a package in preview.
from azureml.opendatasets import UsLaborPPICommodity

labor = UsLaborPPICommodity()
labor_df = labor.to_spark_dataframe()
```

```python
display(labor_df.limit(5))
```

<!-- nbend -->
 
# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

このプラットフォームとパッケージの組み合わせでは、サンプルは利用できません。

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=us-producer-price-index-commodities -->

> [!TIP]
> **[代わりにノートブックをダウンロードします](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=us-producer-price-index-commodities)** 。

```python
# Azure storage access info
blob_account_name = "azureopendatastorage"
blob_container_name = "laborstatisticscontainer"
blob_relative_path = "ppi_commodity/"
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

# <a name="azureml-opendatasets"></a>[azureml-opendatasets](#tab/azureml-opendatasets)

このプラットフォームとパッケージの組み合わせでは、サンプルは利用できません。

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

このプラットフォームとパッケージの組み合わせでは、サンプルは利用できません。

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=us-producer-price-index-commodities -->

> [!TIP]
> **[代わりにノートブックをダウンロードします](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=us-producer-price-index-commodities)** 。

```python
# Azure storage access info
blob_account_name = "azureopendatastorage"
blob_container_name = "laborstatisticscontainer"
blob_relative_path = "ppi_commodity/"
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