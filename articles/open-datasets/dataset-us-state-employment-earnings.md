---
title: US State Employment Hours and Earnings (米国州労働時間および賃金)
titleSuffix: Azure Open Datasets
description: Azure Open Datasets の US State Employment Hours and Earnings (米国州労働時間および賃金) データセットを使用する方法について説明します。
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: a1cb79e704eeae13e8794cad7409e0b945889d22
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/11/2021
ms.locfileid: "112038912"
---
# <a name="us-state-employment-hours-and-earnings"></a>US State Employment Hours and Earnings (米国州労働時間および賃金)

Current Employment Statistics (CES) プログラムでは、米国の給与支払い名簿を基に、非農業部門雇用者数、労働時間、賃金の詳細な業界推定値を生成します。

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

このデータセットは、[米国労働統計局 (BLS)](https://www.bls.gov/) によって公開されている [State and Metro Area Employment](https://www.bls.gov/sae/), [Hours & Earnings data](https://www.bls.gov/sae/) (州および大都市圏の雇用、労働時間、賃金) のデータをソースとしています。 このデータセットの使用に関する諸条件については、「[Linking and Copyright Information (リンクおよび著作権情報)](https://www.bls.gov/bls/linksite.htm)」と「[Important Web Site Notices (Web サイトに関する重要な通知)](https://www.bls.gov/bls/website-policies.htm)」を確認してください。

## <a name="storage-location"></a>保存先

このデータセットは、米国東部 Azure リージョンに保存されています。 アフィニティのために、米国東部でコンピューティング リソースを割り当てることをお勧めします。

## <a name="related-datasets"></a>関連データセット

- [US National Employment Hours and Earnings (全米労働時間および賃金)](dataset-us-national-employment-earnings.md)
- [US Local Area Unemployment Statistics (米国地域別失業統計)](dataset-us-local-unemployment.md)
- [US Labor Force Statistics (米国労働力統計)](dataset-us-labor-force.md)

## <a name="columns"></a>[列]

| Name | データ型 | 一意 | 値 (サンプル) |
|-|-|-|-|
| area_code | string | 446 | 0 31084 |
| area_name | string | 446 | Statewide Los Angeles-Long Beach-Glendale, CA Metropolitan Division (州全体 ロサンゼルス - ロング ビーチ - カリフォルニア州グレンデール 大都市圏) |
| data_type_code | string | 9 | 1 3 |
| data_type_text | string | 9 | All Employees, In Thousands Average Weekly Hours of All Employees (全従業員、千単位 全従業員の平均週時間) |
| footnote_codes | string | 3 | nan P |
| industry_code | string | 343 | 0 5000000 |
| industry_name | string | 343 | Total Nonfarm Total Private (非農業の合計 民営の合計) |
| period | string | 13 | M04 M05 |
| 季節 | string | 2 | U S |
| series_id | string | 23,853 | SMU12000000000000001 SMU36000000000000001 |
| state_code | string | 53 | 6 48 |
| state_name | string | 53 | California Texas (カリフォルニア テキサス) |
| supersector_code | string | 22 | 90 60 |
| supersector_name | string | 22 | Government Professional and Business Services (統治機関の専門職とビジネス サービス) |
| value | float | 132,565 | 0.30000001192092896 0.10000000149011612 |
| year | INT | 81 | 2014 2018 |

## <a name="preview"></a>プレビュー

| area_code | state_code | data_type_code | industry_code | supersector_code | series_id | year | period | value | footnote_codes | 季節 | supersector_name | industry_name | data_type_text | state_name | area_name |
|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|
| 13460 | 41 | 26 | 0 | 0 | SMS41134600000000026 | 1990 | M04 | 0.2 | nan | S | Total Nonfarm (非農業の合計) | Total Nonfarm (非農業の合計) | ALL EMPLOYEES, 3-MONTH AVERAGE CHANGE, SEASONALLY ADJUSTED, THOUSANDS (全従業員、3 か月の平均変化、季節調整済み、千単位) | オレゴン | Bend-Redmond, OR (オレゴン州ベンド - レドモンド) |
| 13460 | 41 | 26 | 0 | 0 | SMS41134600000000026 | 1990 | M05 | 0.2 | nan | S | Total Nonfarm (非農業の合計) | Total Nonfarm (非農業の合計) | ALL EMPLOYEES, 3-MONTH AVERAGE CHANGE, SEASONALLY ADJUSTED, THOUSANDS (全従業員、3 か月の平均変化、季節調整済み、千単位) | オレゴン | Bend-Redmond, OR (オレゴン州ベンド - レドモンド) |
| 13460 | 41 | 26 | 0 | 0 | SMS41134600000000026 | 1990 | M06 | 0.1 | nan | S | Total Nonfarm (非農業の合計) | Total Nonfarm (非農業の合計) | ALL EMPLOYEES, 3-MONTH AVERAGE CHANGE, SEASONALLY ADJUSTED, THOUSANDS (全従業員、3 か月の平均変化、季節調整済み、千単位) | オレゴン | Bend-Redmond, OR (オレゴン州ベンド - レドモンド) |
| 13460 | 41 | 26 | 0 | 0 | SMS41134600000000026 | 1990 | M07 | 0.1 | nan | S | Total Nonfarm (非農業の合計) | Total Nonfarm (非農業の合計) | ALL EMPLOYEES, 3-MONTH AVERAGE CHANGE, SEASONALLY ADJUSTED, THOUSANDS (全従業員、3 か月の平均変化、季節調整済み、千単位) | オレゴン | Bend-Redmond, OR (オレゴン州ベンド - レドモンド) |
| 13460 | 41 | 26 | 0 | 0 | SMS41134600000000026 | 1990 | M08 | 0.2 | nan | S | Total Nonfarm (非農業の合計) | Total Nonfarm (非農業の合計) | ALL EMPLOYEES, 3-MONTH AVERAGE CHANGE, SEASONALLY ADJUSTED, THOUSANDS (全従業員、3 か月の平均変化、季節調整済み、千単位) | オレゴン | Bend-Redmond, OR (オレゴン州ベンド - レドモンド) |
| 13460 | 41 | 26 | 0 | 0 | SMS41134600000000026 | 1990 | M09 | 0.2 | nan | S | Total Nonfarm (非農業の合計) | Total Nonfarm (非農業の合計) | ALL EMPLOYEES, 3-MONTH AVERAGE CHANGE, SEASONALLY ADJUSTED, THOUSANDS (全従業員、3 か月の平均変化、季節調整済み、千単位) | オレゴン | Bend-Redmond, OR (オレゴン州ベンド - レドモンド) |
| 13460 | 41 | 26 | 0 | 0 | SMS41134600000000026 | 1990 | M10 | 0.1 | nan | S | Total Nonfarm (非農業の合計) | Total Nonfarm (非農業の合計) | ALL EMPLOYEES, 3-MONTH AVERAGE CHANGE, SEASONALLY ADJUSTED, THOUSANDS (全従業員、3 か月の平均変化、季節調整済み、千単位) | オレゴン | Bend-Redmond, OR (オレゴン州ベンド - レドモンド) |

## <a name="data-access"></a>データ アクセス

### <a name="azure-notebooks"></a>Azure Notebooks

# <a name="azureml-opendatasets"></a>[azureml-opendatasets](#tab/azureml-opendatasets)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=us-employment-hours-earnings-state -->

> [!TIP]
> **[代わりにノートブックをダウンロードします](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=us-employment-hours-earnings-state)** 。

```python
# This is a package in preview.
from azureml.opendatasets import UsLaborEHEState

usLaborEHEState = UsLaborEHEState()
usLaborEHEState_df = usLaborEHEState.to_pandas_dataframe()
```

```python
usLaborEHEState_df.info()
```

<!-- nbend -->

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=us-employment-hours-earnings-state -->

> [!TIP]
> **[代わりにノートブックをダウンロードします](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=us-employment-hours-earnings-state)** 。

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
folder_name = "ehe_state/"
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

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=azureml-opendatasets&registryId=us-employment-hours-earnings-state -->

> [!TIP]
> **[代わりにノートブックをダウンロードします](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=azureml-opendatasets&registryId=us-employment-hours-earnings-state)** 。

```python
# This is a package in preview.
from azureml.opendatasets import UsLaborEHEState

usLaborEHEState = UsLaborEHEState()
usLaborEHEState_df = usLaborEHEState.to_spark_dataframe()
```

```python
display(usLaborEHEState_df.limit(5))
```

<!-- nbend -->
 
# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

このプラットフォームとパッケージの組み合わせでは、サンプルは利用できません。

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=us-employment-hours-earnings-state -->

> [!TIP]
> **[代わりにノートブックをダウンロードします](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=us-employment-hours-earnings-state)** 。

```python
# Azure storage access info
blob_account_name = "azureopendatastorage"
blob_container_name = "laborstatisticscontainer"
blob_relative_path = "ehe_state/"
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

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=us-employment-hours-earnings-state -->

> [!TIP]
> **[代わりにノートブックをダウンロードします](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=us-employment-hours-earnings-state)** 。

```python
# Azure storage access info
blob_account_name = "azureopendatastorage"
blob_container_name = "laborstatisticscontainer"
blob_relative_path = "ehe_state/"
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