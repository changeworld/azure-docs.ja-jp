---
title: 米国消費者物価指数
titleSuffix: Azure Open Datasets
description: Azure Open Datasets で米国消費者物価指数データセットを使用する方法について学習します。
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: fffe6b1521f3f9ab4973a64f03ef52e4a9019658
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/11/2021
ms.locfileid: "112038786"
---
# <a name="us-consumer-price-index"></a>米国消費者物価指数

消費者物価指数 (CPI) は、都市の消費者が商品やサービスを実際に購入する際に支払う価格の経時的な平均的変動を測定するものです。

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

このデータセットの詳細情報が含まれた [README](https://download.bls.gov/pub/time.series/cu/cu.txt) ファイルは、[データセットの元の場所](https://download.bls.gov/pub/time.series/cu/)で入手できます。

このデータセットは、[米国労働統計局 (BLS)](https://www.bls.gov/)によって公開される、[消費者物価指数データ](https://www.bls.gov/cpi/)から生成されます。 使用条件については、「[Linking and Copyright Information](https://www.bls.gov/bls/linksite.htm)」(リンクと著作権に関する情報) と「[Important Web Site Notices](https://www.bls.gov/bls/website-policies.htm)」(重要な Web サイトの通知) を確認してください。

## <a name="storage-location"></a>保存先

このデータセットは、米国東部 Azure リージョンに保存されています。 アフィニティのため、米国東部にコンピューティング リソースを割り当てることをお勧めします。

## <a name="related-datasets"></a>関連データセット

- [米国生産者物価指数 - 業種](dataset-us-producer-price-index-industry.md)
- [米国生産者物価指数 - 商品](dataset-us-producer-price-index-commodities.md)

## <a name="columns"></a>[列]

| Name | データ型 | 一意 | 値 (サンプル) | 説明 |
|-|-|-|-|-|
| area_code | string | 70 | 0000 0300 | 特定の地域の識別に使用される一意のコード。 すべての地域コードはこちら: http://download.bls.gov/pub/time.series/cu/cu.area |
| area_name | string | 69 | 米国の都市平均 南部 | 特定の地域の名前。 すべての地域名やコードについては、 https://download.bls.gov/pub/time.series/cu/cu.area をご覧ください。 |
| footnote_codes | string | 3 | nan U | データ系列の脚注を示します。 ほとんどの値が null です。 |
| item_code | string | 515 | SA0E SAF11 | データ観測の対象となる品目を示します。 すべての品目名とコードについては、 https://download.bls.gov/pub/time.series/cu/cu.item をご覧ください。 |
| item_name | string | 515 | 自宅のエネルギー食品 | 品目のフル ネーム。 品目名とコードについては、 https://download.bls.gov/pub/time.series/cu/cu.txt をご覧ください。 |
| period | string | 16 | S01 S02 | データが観測される期間を示します。 形式:M01-M13 または S01-S03 (M = 毎月、M13 = 年平均、S = 半年ごと)。 例:M06 = June。 期間名とコードについては、 https://download.bls.gov/pub/time.series/cu/cu.period をご覧ください。 |
| periodicity_code | string | 3 | R S | データ観測の頻度。 S = 半年に 1 回、R = 定期的 |
| 季節 | string | 1,043 | U S | データが季節調整されているかどうかを識別するコード。 S = 季節調整済み、U = 未調整 |
| series_id | string | 16,683 | CWURS400SA0E CWUR0100SA0E | 特定の系列を識別するコード。 時系列とは、一定の時間間隔 (つまり、月 1 回、四半期に 1 回、半年に 1 回、年 1 回) で長期間にわたって観測された一連のデータを指します。 通常、BLS 時系列データは 1 か月間隔で生成され、特定の地域の価格が毎月収集される特定の消費財から、雇用率が毎月記録されている特定の業種の労働者のカテゴリまで、さまざまなデータを表します。 詳細については、https://download.bls.gov/pub/time.series/cu/cu.txt を参照してください。 |
| series_title | string | 8,336 | 米国の都市平均のアルコール飲料、すべての都市部消費者、季節未調整 Los Angeles-Long Beach-Anaheim (CA) における輸送、すべての都市部消費者、季節未調整 | 対応する series_id の系列名。 系列 ID と系列名については、 https://download.bls.gov/pub/time.series/cu/cu.series をご覧ください。 |
| value | float | 310,603 | 100.0 101.0999984741211 | 商品の物価指数。 |
| year | INT | 25 | 2018 2017 | 観測の年を示します。 |

## <a name="preview"></a>プレビュー

| area_code | item_code | series_id | year | period | value | footnote_codes | 季節 | periodicity_code | series_title | item_name | area_name |
|-|-|-|-|-|-|-|-|-|-|-|-|
| S49E | SEHF01 | CUURS49ESEHF01 | 2017 | M12 | 279.974 | nan | U | R | San Diego-Carlsbad (CA) における電力、すべての都市部消費者、季節未調整 | 電力 | San Diego-Carlsbad (CA) |
| S49E | SEHF01 | CUURS49ESEHF01 | 2017 | M12 | 279.974 | nan | U | R | San Diego-Carlsbad (CA) における電力、すべての都市部消費者、季節未調整 | 電力 | San Diego-Carlsbad (CA) |
| S49E | SEHF01 | CUURS49ESEHF01 | 2017 | M12 | 279.974 | nan | U | R | San Diego-Carlsbad (CA) における電力、すべての都市部消費者、季節未調整 | 電力 | San Diego-Carlsbad (CA) |
| S49E | SEHF01 | CUURS49ESEHF01 | 2017 | M12 | 279.974 | nan | U | R | San Diego-Carlsbad (CA) における電力、すべての都市部消費者、季節未調整 | 電力 | San Diego-Carlsbad (CA) |
| S49E | SEHF01 | CUURS49ESEHF01 | 2017 | M12 | 279.974 | nan | U | R | San Diego-Carlsbad (CA) における電力、すべての都市部消費者、季節未調整 | 電力 | San Diego-Carlsbad (CA) |
| S49E | SEHF01 | CUURS49ESEHF01 | 2017 | M12 | 279.974 | nan | U | R | San Diego-Carlsbad (CA) における電力、すべての都市部消費者、季節未調整 | 電力 | San Diego-Carlsbad (CA) |

## <a name="data-access"></a>データ アクセス

### <a name="azure-notebooks"></a>Azure Notebooks

# <a name="azureml-opendatasets"></a>[azureml-opendatasets](#tab/azureml-opendatasets)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=us-consumer-price-index -->

> [!TIP]
> **[代わりにノートブックをダウンロードします](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=us-consumer-price-index)** 。

```python
# This is a package in preview.
from azureml.opendatasets import UsLaborCPI

usLaborCPI = UsLaborCPI()
usLaborCPI_df = usLaborCPI.to_pandas_dataframe()
```

```python
usLaborCPI_df.info()
```

<!-- nbend -->

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=us-consumer-price-index -->

> [!TIP]
> **[代わりにノートブックをダウンロードします](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=us-consumer-price-index)** 。

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
folder_name = "cpi/"
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

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=azureml-opendatasets&registryId=us-consumer-price-index -->

> [!TIP]
> **[代わりにノートブックをダウンロードします](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=azureml-opendatasets&registryId=us-consumer-price-index)** 。

```python
# This is a package in preview.
from azureml.opendatasets import UsLaborCPI

usLaborCPI = UsLaborCPI()
usLaborCPI_df = usLaborCPI.to_spark_dataframe()
```

```python
display(usLaborCPI_df.limit(5))
```

<!-- nbend -->
 
# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

このプラットフォームとパッケージの組み合わせでは、サンプルは利用できません。

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=us-consumer-price-index -->

> [!TIP]
> **[代わりにノートブックをダウンロードします](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=us-consumer-price-index)** 。

```python
# Azure storage access info
blob_account_name = "azureopendatastorage"
blob_container_name = "laborstatisticscontainer"
blob_relative_path = "cpi/"
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

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=us-consumer-price-index -->

> [!TIP]
> **[代わりにノートブックをダウンロードします](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=us-consumer-price-index)** 。

```python
# Azure storage access info
blob_account_name = "azureopendatastorage"
blob_container_name = "laborstatisticscontainer"
blob_relative_path = "cpi/"
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