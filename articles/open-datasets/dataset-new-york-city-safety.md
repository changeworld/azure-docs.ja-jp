---
title: New York City Safety Data
titleSuffix: Azure Open Datasets
description: Azure Open Datasets で New York City Safety (ニューヨーク市安全) データセットを使用する方法について説明します。
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: d8efbd7469ed92b4c323ed3d94315ec8f0f4dc5c
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/11/2021
ms.locfileid: "112038739"
---
# <a name="new-york-city-safety-data"></a>New York City Safety Data

2010 年から現在までのすべてのニューヨーク市 311 サービス要求。

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]


## <a name="volume-and-retention"></a>ボリュームとデータ保持期間

このデータセットは Parquet 形式で保存されています。 毎日更新されており、2019 年時点で合計約 1200 万行 (500 MB) が含まれています。

このデータセットには、2010 年から現在までに蓄積された過去の記録が含まれます。 SDK でパラメーター設定を使用して、特定の時間範囲内のデータをフェッチできます。

## <a name="storage-location"></a>保存先

このデータセットは、米国東部 Azure リージョンに保存されています。 アフィニティのために、米国東部でコンピューティング リソースを割り当てることをお勧めします。

## <a name="additional-information"></a>追加情報

このデータセットはニューヨーク市政府から提供されています。詳細については、[ニューヨーク市の Web サイト](https://data.cityofnewyork.us/Social-Services/311-Service-Requests-from-2010-to-Present/erm2-nwe9)を参照してください。 [このデータセットの利用規約](https://www1.nyc.gov/home/terms-of-use.page)を参照してください。

## <a name="columns"></a>[列]

| Name | データ型 | 一意 | 値 (サンプル) | 説明 |
|-|-|-|-|-|
| address | string | 1,536,593 | 655 EAST 230 STREET 78-15 PARSONS BOULEVARD | 提出者から提供されたインシデントの住所の家屋番号。 |
| category | string | 446 | Noise - Residential HEAT/HOT WATER | これは、インシデントまたは状況のトピックを識別する階層の最初のレベルです (苦情の種類)。 対応するサブカテゴリ (記述子) がある場合もあれば、スタンドアロンの場合もあります。 |
| dataSubtype | string | 1 | 311_All | "311_All" |
| dataType | string | 1 | 安全性 | "Safety" |
| dateTime | timestamp | 17,332,609 | 2013-01-24 00:00:00 2015-01-08 00:00:00 | サービス要求が作成された日付。 |
| 緯度 (latitude) | double | 1,513,691 | 40.89187241649303 40.72195913199264 | インシデントの場所の地理ベースの緯度。 |
| 経度 (longitude) | double | 1,513,713 | -73.86016845296459 -73.80969682426189 | インシデントの場所の地理ベースの経度。 |
| status | string | 13 | Closed Pending | 送信されたサービス要求の状態。 |
| subcategory | string | 1,716 | Loud Music/Party ENTIRE BUILDING | これはカテゴリ (苦情の種類) に関連付けられており、インシデントまたは状況の詳細を示します。 値は苦情の種類によって異なり、サービス要求で必ずしも必要とされるわけではありません。 |

## <a name="preview"></a>プレビュー

| dataType | dataSubtype | dateTime | category | subcategory | status | address | 緯度 (latitude) | 経度 (longitude) | source | extendedProperties |
|-|-|-|-|-|-|-|-|-|-|-|
| 安全性 | 311_All | 4/25/2021 2:05:05 AM | Noise - Street/Sidewalk | Loud Music/Party | 進行中 | 2766 BATH AVENUE | 40.5906129741766 | -73.9847949011337 | null |  |
| 安全性 | 311_All | 4/25/2021 2:04:33 AM | Noise - Commercial | Loud Music/Party | 進行中 | 1033 WEBSTER AVENUE | 40.8285784533256 | -73.9117746958432 | null |  |
| 安全性 | 311_All | 4/25/2021 2:04:27 AM | Noise - Residential | Loud Music/Party | 進行中 | 620 WEST 141 STREET | 40.8241726554395 | -73.9530069547366 | null |  |
| 安全性 | 311_All | 4/25/2021 2:04:04 AM | Noise - Residential | Loud Music/Party | 進行中 | 1647 64 STREET | 40.6218907202382 | -73.9931125332078 | null |  |
| 安全性 | 311_All | 4/25/2021 2:04:01 AM | Noise - Residential | Loud Music/Party | 進行中 | 30 LENOX AVENUE | 40.7991622274945 | -73.9517496365803 | null |  |
| 安全性 | 311_All | 4/25/2021 2:03:40 AM | Illegal Parking | Double Parked Blocking Traffic | 進行中 | 304 WEST 148 STREET | 40.8248229687124 | -73.940696262361 | null |  |
| 安全性 | 311_All | 4/25/2021 2:03:31 AM | Noise - Street/Sidewalk | Loud Music/Party | 進行中 | ADEE AVENUE | 40.8708386263454 | -73.8382363208686 | null |  |
| 安全性 | 311_All | 4/25/2021 2:03:18 AM | Noise - Residential | Loud Music/Party | 進行中 | 340 EVERGREEN AVENUE | 40.6947512704197 | -73.9248330229197 | null |  |
| 安全性 | 311_All | 4/25/2021 2:03:13 AM | Noise - Residential | Banging/Pounding | 進行中 | 25 REMSEN STREET | 40.6948938116483 | -73.9973494607802 | null |  |

## <a name="data-access"></a>データ アクセス

### <a name="azure-notebooks"></a>Azure Notebooks

# <a name="azureml-opendatasets"></a>[azureml-opendatasets](#tab/azureml-opendatasets)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=city_safety_sanfrancisco -->

> [!TIP]
> **[代わりにノートブックをダウンロードします](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=city_safety_sanfrancisco)** 。

```
# This is a package in preview.
from azureml.opendatasets import SanFranciscoSafety

from datetime import datetime
from dateutil import parser


end_date = parser.parse('2016-01-01')
start_date = parser.parse('2015-05-01')
safety = SanFranciscoSafety(start_date=start_date, end_date=end_date)
safety = safety.to_pandas_dataframe()
```

```
safety.info()
```

<!-- nbend -->

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=city_safety_sanfrancisco -->

> [!TIP]
> **[代わりにノートブックをダウンロードします](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=city_safety_sanfrancisco)** 。

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
container_name = "citydatacontainer"
folder_name = "Safety/Release/city=SanFrancisco"
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

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=azureml-opendatasets&registryId=city_safety_sanfrancisco -->

> [!TIP]
> **[代わりにノートブックをダウンロードします](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=azureml-opendatasets&registryId=city_safety_sanfrancisco)** 。

```
# This is a package in preview.
# You need to pip install azureml-opendatasets in Databricks cluster. https://docs.microsoft.com/en-us/azure/data-explorer/connect-from-databricks#install-the-python-library-on-your-azure-databricks-cluster
from azureml.opendatasets import SanFranciscoSafety

from datetime import datetime
from dateutil import parser


end_date = parser.parse('2016-01-01')
start_date = parser.parse('2015-05-01')
safety = SanFranciscoSafety(start_date=start_date, end_date=end_date)
safety = safety.to_spark_dataframe()
```

```
display(safety.limit(5))
```

<!-- nbend -->

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

このプラットフォームとパッケージの組み合わせでは、サンプルは利用できません。

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=city_safety_sanfrancisco -->

> [!TIP]
> **[代わりにノートブックをダウンロードします](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=city_safety_sanfrancisco)** 。

```python
# Azure storage access info
blob_account_name = "azureopendatastorage"
blob_container_name = "citydatacontainer"
blob_relative_path = "Safety/Release/city=SanFrancisco"
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

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=azureml-opendatasets&registryId=city_safety_sanfrancisco -->

> [!TIP]
> **[代わりにノートブックをダウンロードします](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=azureml-opendatasets&registryId=city_safety_sanfrancisco)** 。

```python
# This is a package in preview.
from azureml.opendatasets import SanFranciscoSafety

from datetime import datetime
from dateutil import parser


end_date = parser.parse('2016-01-01')
start_date = parser.parse('2015-05-01')
safety = SanFranciscoSafety(start_date=start_date, end_date=end_date)
safety = safety.to_spark_dataframe()
```

```python
# Display top 5 rows
display(safety.limit(5))
```

<!-- nbend -->

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

このプラットフォームとパッケージの組み合わせでは、サンプルは利用できません。

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=city_safety_sanfrancisco -->

> [!TIP]
> **[代わりにノートブックをダウンロードします](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=city_safety_sanfrancisco)** 。

```python
# Azure storage access info
blob_account_name = "azureopendatastorage"
blob_container_name = "citydatacontainer"
blob_relative_path = "Safety/Release/city=SanFrancisco"
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

## <a name="examples"></a>例

- GitHub の[都市の安全性分析](https://github.com/scottcounts/CitySafety)の例を参照してください。


## <a name="next-steps"></a>次のステップ

[Open Datasets カタログ](dataset-catalog.md)の残りのデータセットを表示します。