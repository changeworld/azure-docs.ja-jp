---
title: US National Employment Hours and Earnings (全米労働時間および賃金)
titleSuffix: Azure Open Datasets
description: Azure Open Datasets で US National Employment Hours and Earnings (全米労働時間および賃金) データセットを使用する方法について説明します。
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: ffbd29a3aa19b999c9202a0cea36cafee6a4c5f4
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/11/2021
ms.locfileid: "112038775"
---
# <a name="us-national-employment-hours-and-earnings"></a>US National Employment Hours and Earnings (全米労働時間および賃金)

Current Employment Statistics (CES) プログラムでは、米国の給与支払い名簿を基に、非農業部門雇用者数、労働時間、賃金の詳細な業界推定値を生成します。

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

このデータセットの詳細情報が含まれた [README](https://download.bls.gov/pub/time.series/ce/ce.txt) ファイルは、[データセットの元の場所](https://download.bls.gov/pub/time.series/ce/)で入手できます。

このデータセットは、[米国労働統計局 (BLS)](https://www.bls.gov/) によって公開されている [「Current Employment Statistics - CES (National)」のデータ](https://www.bls.gov/ces/)をソースとしています。 このデータセットの使用に関する諸条件については、「[Linking and Copyright Information (リンクおよび著作権情報)](https://www.bls.gov/bls/linksite.htm)」と「[Important Web Site Notices (Web サイトに関する重要な通知)](https://www.bls.gov/bls/website-policies.htm)」を確認してください。

## <a name="storage-location"></a>保存先

このデータセットは、米国東部 Azure リージョンに保存されています。 アフィニティのために、米国東部でコンピューティング リソースを割り当てることをお勧めします。

## <a name="related-datasets"></a>関連データセット

- [US State Employment Hours and Earnings (米国州労働時間および賃金)](dataset-us-state-employment-earnings.md)
- [US Local Area Unemployment Statistics (米国地域別失業統計)](dataset-us-local-unemployment.md)
- [US Labor Force Statistics (米国労働力統計)](dataset-us-labor-force.md)

## <a name="columns"></a>[列]

| Name | データ型 | 一意 | 値 (サンプル) | 説明 |
|-|-|-|-|-|
| data_type_code | string | 37 | 1 10 | 「https://download.bls.gov/pub/time.series/ce/ce.datatype」を参照してください。 |
| data_type_text | string | 37 | ALL EMPLOYEES, THOUSANDS (全従業員、数千人) WOMEN EMPLOYEES, THOUSANDS (女性従業員、数千人) | 「https://download.bls.gov/pub/time.series/ce/ce.datatype」を参照してください。 |
| footnote_codes | string | 2 | nan P |  |
| industry_code | string | 902 | 30000000 32000000 | 含まれる異業種。 「https://download.bls.gov/pub/time.series/ce/ce.industry」を参照してください。 |
| industry_name | string | 895 | Nondurable goods (非耐久消費財) Durable goods (耐久消費財) | 含まれる異業種。 「https://download.bls.gov/pub/time.series/ce/ce.industry」を参照してください。 |
| period | string | 13 | M03 M06 | 「https://download.bls.gov/pub/time.series/ce/ce.period」を参照してください。 |
| 季節 | string | 2 | U S |  |
| series_id | string | 26,021 | CEU3100000008 CEU9091912001 | 異なる種類のデータ系列がデータセットで入手できます。 「https://download.bls.gov/pub/time.series/ce/ce.series」を参照してください。 |
| series_title | string | 25,685 | All employees, thousands, durable goods, not seasonally adjusted (全従業員、数千人、耐久消費財、季節調整なし) All employees, thousands, nondurable goods, not seasonally adjusted (全従業員、数千人、非耐久消費財、季節調整なし) | 異なる種類のデータ系列のタイトルがデータセットで入手できます。 「https://download.bls.gov/pub/time.series/ce/ce.series」を参照してください。 |
| supersector_code | string | 22 | 31 60 | より上位レベルの業界またはセクターの分類。 「https://download.bls.gov/pub/time.series/ce/ce.supersector」を参照してください。 |
| supersector_name | string | 22 | Durable Goods (耐久消費財) Professional and business services (プロフェッショナルおよびビジネス サービス) | より上位レベルの業界またはセクターの分類。 「https://download.bls.gov/pub/time.series/ce/ce.supersector」を参照してください。 |
| value | float | 572,372 | 38.5 38.400001525878906 |  |
| year | INT | 81 | 2017 2012 |  |

## <a name="preview"></a>プレビュー

| data_type_code | industry_code | supersector_code | series_id | year | period | value | footnote_codes | 季節 | series_title | supersector_name | industry_name | data_type_text |
|-|-|-|-|-|-|-|-|-|-|-|-|-|
| 26 | 5000000 | 5 | CES0500000026 | 1939 | M04 | 52 | nan | S | All employees, 3-month average change, seasonally adjusted, thousands, total private, seasonally adjusted (全従業員、3 か月の平均変化、季節調整済み、数千人、民間合計、季節調整済み) | Total private (民間合計) | Total private (民間合計) | ALL EMPLOYEES, 3-MONTH AVERAGE CHANGE, SEASONALLY ADJUSTED, THOUSANDS (全従業員、3 か月の平均変化、季節調整済み、数千人) |
| 26 | 5000000 | 5 | CES0500000026 | 1939 | M05 | 65 | nan | S | All employees, 3-month average change, seasonally adjusted, thousands, total private, seasonally adjusted (全従業員、3 か月の平均変化、季節調整済み、数千人、民間合計、季節調整済み) | Total private (民間合計) | Total private (民間合計) | ALL EMPLOYEES, 3-MONTH AVERAGE CHANGE, SEASONALLY ADJUSTED, THOUSANDS (全従業員、3 か月の平均変化、季節調整済み、数千人) |
| 26 | 5000000 | 5 | CES0500000026 | 1939 | M06 | 74 | nan | S | All employees, 3-month average change, seasonally adjusted, thousands, total private, seasonally adjusted (全従業員、3 か月の平均変化、季節調整済み、数千人、民間合計、季節調整済み) | Total private (民間合計) | Total private (民間合計) | ALL EMPLOYEES, 3-MONTH AVERAGE CHANGE, SEASONALLY ADJUSTED, THOUSANDS (全従業員、3 か月の平均変化、季節調整済み、数千人) |
| 26 | 5000000 | 5 | CES0500000026 | 1939 | M07 | 103 | nan | S | All employees, 3-month average change, seasonally adjusted, thousands, total private, seasonally adjusted (全従業員、3 か月の平均変化、季節調整済み、数千人、民間合計、季節調整済み) | Total private (民間合計) | Total private (民間合計) | ALL EMPLOYEES, 3-MONTH AVERAGE CHANGE, SEASONALLY ADJUSTED, THOUSANDS (全従業員、3 か月の平均変化、季節調整済み、数千人) |
| 26 | 5000000 | 5 | CES0500000026 | 1939 | M08 | 108 | nan | S | All employees, 3-month average change, seasonally adjusted, thousands, total private, seasonally adjusted (全従業員、3 か月の平均変化、季節調整済み、数千人、民間合計、季節調整済み) | Total private (民間合計) | Total private (民間合計) | ALL EMPLOYEES, 3-MONTH AVERAGE CHANGE, SEASONALLY ADJUSTED, THOUSANDS (全従業員、3 か月の平均変化、季節調整済み、数千人) |
| 26 | 5000000 | 5 | CES0500000026 | 1939 | M09 | 152 | nan | S | All employees, 3-month average change, seasonally adjusted, thousands, total private, seasonally adjusted (全従業員、3 か月の平均変化、季節調整済み、数千人、民間合計、季節調整済み) | Total private (民間合計) | Total private (民間合計) | ALL EMPLOYEES, 3-MONTH AVERAGE CHANGE, SEASONALLY ADJUSTED, THOUSANDS (全従業員、3 か月の平均変化、季節調整済み、数千人) |
| 26 | 5000000 | 5 | CES0500000026 | 1939 | M10 | 307 | nan | S | All employees, 3-month average change, seasonally adjusted, thousands, total private, seasonally adjusted (全従業員、3 か月の平均変化、季節調整済み、数千人、民間合計、季節調整済み) | Total private (民間合計) | Total private (民間合計) | ALL EMPLOYEES, 3-MONTH AVERAGE CHANGE, SEASONALLY ADJUSTED, THOUSANDS (全従業員、3 か月の平均変化、季節調整済み、数千人) |
| 26 | 5000000 | 5 | CES0500000026 | 1939 | M11 | 248 | nan | S | All employees, 3-month average change, seasonally adjusted, thousands, total private, seasonally adjusted (全従業員、3 か月の平均変化、季節調整済み、数千人、民間合計、季節調整済み) | Total private (民間合計) | Total private (民間合計) | ALL EMPLOYEES, 3-MONTH AVERAGE CHANGE, SEASONALLY ADJUSTED, THOUSANDS (全従業員、3 か月の平均変化、季節調整済み、数千人) |

## <a name="data-access"></a>データ アクセス

### <a name="azure-notebooks"></a>Azure Notebooks

# <a name="azureml-opendatasets"></a>[azureml-opendatasets](#tab/azureml-opendatasets)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=us-employment-hours-earnings-national -->

> [!TIP]
> **[代わりにノートブックをダウンロードします](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=us-employment-hours-earnings-national)** 。

```python
# This is a package in preview.
from azureml.opendatasets import UsLaborEHENational

usLaborEHENational = UsLaborEHENational()
usLaborEHENational_df = usLaborEHENational.to_pandas_dataframe()
```

```python
usLaborEHENational_df.info()
```

<!-- nbend -->

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=us-employment-hours-earnings-national -->

> [!TIP]
> **[代わりにノートブックをダウンロードします](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=us-employment-hours-earnings-national)** 。

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
folder_name = "ehe_national/"
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

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=azureml-opendatasets&registryId=us-employment-hours-earnings-national -->

> [!TIP]
> **[代わりにノートブックをダウンロードします](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=azureml-opendatasets&registryId=us-employment-hours-earnings-national)** 。

```python
# This is a package in preview.
from azureml.opendatasets import UsLaborEHENational

usLaborEHENational = UsLaborEHENational()
usLaborEHENational_df = usLaborEHENational.to_spark_dataframe()
```

```python
display(usLaborEHENational_df.limit(5))
```

<!-- nbend -->
 
# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

このプラットフォームとパッケージの組み合わせでは、サンプルは利用できません。

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=us-employment-hours-earnings-national -->

> [!TIP]
> **[代わりにノートブックをダウンロードします](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=us-employment-hours-earnings-national)** 。

```python
# Azure storage access info
blob_account_name = "azureopendatastorage"
blob_container_name = "laborstatisticscontainer"
blob_relative_path = "ehe_national/"
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

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=us-employment-hours-earnings-national -->

> [!TIP]
> **[代わりにノートブックをダウンロードします](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=us-employment-hours-earnings-national)** 。

```python
# Azure storage access info
blob_account_name = "azureopendatastorage"
blob_container_name = "laborstatisticscontainer"
blob_relative_path = "ehe_national/"
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

## <a name="next-steps"></a>次の手順

[Open Datasets カタログ](dataset-catalog.md)の残りのデータセットを表示します。