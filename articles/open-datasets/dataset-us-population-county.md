---
title: US Population by County (米国の郡別人口)
titleSuffix: Azure Open Datasets
description: Azure Open Datasets で US Population by County Code (米国の郡別人口) データセットを使用する方法について説明します。
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: fe343d5d2519517653e966c3cb28b5e5f180969f
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/11/2021
ms.locfileid: "112038918"
---
# <a name="us-population-by-county"></a>US Population by County (米国の郡別人口)

2000 年および 2010 年に実施された 10 年ごとの国勢調査をソースとする、米国の郡ごとの性別および人種別の米国人口。

このデータセットは、米国国勢調査局の [Decennial Census Dataset API](https://www.census.gov/data/developers/data-sets/decennial-census.html) から提供されます。 このデータセットの使用に関する諸条件については、「[Terms of Service (サービス利用規約)](https://www.census.gov/data/developers/about/terms-of-service.html)」および「[Policies and Notices (ポリシーと通知)](https://www.census.gov/about/policies.html)」をご覧ください。

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

## <a name="volume-and-retention"></a>ボリュームとリテンション期間

このデータセットは Parquet 形式で保存されており、2000 年および 2010 年のデータが含まれています。

## <a name="storage-location"></a>保存先
このデータセットは、米国東部 Azure リージョンに保存されています。 アフィニティのために、米国東部でコンピューティング リソースを割り当てることをお勧めします。

## <a name="related-datasets"></a>関連データセット

- [US Population by ZIP Code (米国の郵便番号別人口)](dataset-us-population-zip.md)

## <a name="columns"></a>[列]

| Name | データ型 | 一意 | 値 (サンプル) | 説明 |
|-|-|-|-|-|
| countyName | string | 1,960 | Washington County Jefferson County | 郡名。 |
| decennialTime | string | 2 | 2010 2000 | 10 年ごとの国勢調査が行われた時期 (例: 2010、2000)。 |
| maxAge | INT | 23 | 9 66 | 年齢範囲の最大値。 null の場合、すべての年齢にわたるか、年齢範囲に上限がなくなります (年齢が 85 歳を上回るなど)。 |
| minAge | INT | 23 | 35 67 | 年齢範囲の最小値。 null の場合、すべての年齢にわたります。 |
| 作成 (population) | INT | 47,229 | 1 2 | このセグメントの人口。 |
| race | string | 8 | ASIAN ALONE TWO OR MORE RACES | 国勢調査データの人種カテゴリ。 null の場合、すべての人種にわたります。 |
| sex | string | 3 | Male Female | 男性または女性。 null の場合、男女両方にわたります。 |
| stateName | string | 52 | Texas Georgia | 米国の州の名前。 |
| year | INT | 2 | 2010 2000 | 10 年ごとの年 (整数)。 |

## <a name="preview"></a>プレビュー

| decennialTime | stateName | countyName | 作成 (population) | race | sex | minAge | maxAge | year |
|-|-|-|-|-|-|-|-|-|
| 2010 | テキサス | Crockett County | 123 | WHITE ALONE | Male | 5 | 9 | 2010 |
| 2010 | テキサス | Crockett County | 1 | ASIAN ALONE | Female | 67 | 69 | 2010 |
| 2010 | テキサス | Crockett County | 111 | WHITE ALONE | Female | 55 | 59 | 2010 |
| 2010 | テキサス | Crockett County | 64 | TWO OR MORE RACES | null |  |  | 2010 |
| 2010 | テキサス | Crockett County | 18 | null | Male | 85 |  | 2010 |
| 2010 | テキサス | Crockett County | 16 | AMERICAN INDIAN AND ALASKA NATIVE ALONE | Female |  |  | 2010 |
| 2010 | テキサス | Crockett County | 7 | WHITE ALONE | Male | 21 | 21 | 2010 |
| 2010 | テキサス | Crockett County | 45 | null | Female | 85 |  | 2010 |
| 2010 | テキサス | Crockett County | 0 | NATIVE HAWAIIAN AND OTHER PACIFIC ISLANDER ALONE | Female | 67 | 69 | 2010 |

## <a name="data-access"></a>データ アクセス

### <a name="azure-notebooks"></a>Azure Notebooks

# <a name="azureml-opendatasets"></a>[azureml-opendatasets](#tab/azureml-opendatasets)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=us-decennial-census-county -->

> [!TIP]
> **[代わりにノートブックをダウンロードします](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=us-decennial-census-county)** 。

```python
# This is a package in preview.
from azureml.opendatasets import UsPopulationCounty

population = UsPopulationCounty()
population_df = population.to_pandas_dataframe()
```

```python
population_df.info()
```

<!-- nbend -->

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=us-decennial-census-county -->

> [!TIP]
> **[代わりにノートブックをダウンロードします](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=us-decennial-census-county)** 。

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
container_name = "censusdatacontainer"
folder_name = "release/us_population_county/"
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

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=azureml-opendatasets&registryId=us-decennial-census-county -->

> [!TIP]
> **[代わりにノートブックをダウンロードします](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=azureml-opendatasets&registryId=us-decennial-census-county)** 。

```python
# This is a package in preview.
from azureml.opendatasets import UsPopulationCounty

population = UsPopulationCounty()
population_df = population.to_spark_dataframe()
```

```python
display(population_df.limit(5))
```

<!-- nbend -->

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

このプラットフォームとパッケージの組み合わせでは、サンプルは利用できません。

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=us-decennial-census-county -->

> [!TIP]
> **[代わりにノートブックをダウンロードします](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=us-decennial-census-county)** 。

```python
# Azure storage access info
blob_account_name = "azureopendatastorage"
blob_container_name = "censusdatacontainer"
blob_relative_path = "release/us_population_county/"
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

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=azureml-opendatasets&registryId=us-decennial-census-county -->

> [!TIP]
> **[代わりにノートブックをダウンロードします](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=azureml-opendatasets&registryId=us-decennial-census-county)** 。

```python
# This is a package in preview.
from azureml.opendatasets import UsPopulationCounty

population = UsPopulationCounty()
population_df = population.to_spark_dataframe()
```

```python
# Display top 5 rows
display(population_df.limit(5))
```

<!-- nbend -->

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

このプラットフォームとパッケージの組み合わせでは、サンプルは利用できません。

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=us-decennial-census-county -->

> [!TIP]
> **[代わりにノートブックをダウンロードします](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=us-decennial-census-county)** 。

```python
# Azure storage access info
blob_account_name = "azureopendatastorage"
blob_container_name = "censusdatacontainer"
blob_relative_path = "release/us_population_county/"
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