---
title: Seattle Safety Data
titleSuffix: Azure Open Datasets
description: Azure Open Datasets で Seattle Safety (シアトル安全) データセットを使用する方法について説明します。
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: c865e09a6d4591b1f80ddd55c2a260b6e06f154a
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/11/2021
ms.locfileid: "112038804"
---
# <a name="seattle-safety-data"></a>Seattle Safety Data

シアトル消防局 (911) からの派遣。

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

## <a name="volume-and-retention"></a>ボリュームとデータ保有期間

このデータセットは Parquet 形式で保存されています。 毎日更新されており、2019 年には約 800,000 行 (20 MB) が含まれています。

このデータセットには、2010 年から現在までに蓄積された過去の記録が含まれます。 SDK でパラメーター設定を使用して、特定の時間範囲内のデータをフェッチできます。

## <a name="storage-location"></a>保存先

このデータセットは、米国東部 Azure リージョンに保存されています。 アフィニティのため、米国東部にコンピューティング リソースを割り当てることをお勧めします。

## <a name="additional-information"></a>追加情報

このデータセットはシアトル市政府から提供されています。 詳細については、[シアトル市の Web サイト](http://web5.seattle.gov/MNM/incidentresponse.aspx)を参照してください。 [このデータセットの利用規約については、ライセンスと帰属](https://creativecommons.org/publicdomain/zero/1.0/legalcode)に関するページを参照してください。 データ ソースについてご不明な点があれば、open.data@seattle.gov までメールでお問い合わせください。

## <a name="columns"></a>[列]

| Name | データ型 | 一意 | 値 (サンプル) | 説明 |
|-|-|-|-|-|
| address | string | 196,965 | 517 3rd Av 318 2nd Av Et S | インシデントの場所。 |
| category | string | 232 | Aid Response (救援対応) Medic Response (医療対応) | 応答の種類。 |
| dataSubtype | string | 1 | 911_Fire | "911_Fire" |
| dataType | string | 1 | 安全性 | "Safety" |
| dateTime | timestamp | 1,533,401 | 2020-11-04 06:49:00 2019-06-19 13:49:00 | 通報の日時。 |
| 緯度 (latitude) | double | 94,332 | 47.602172 47.600194 | これは緯度値です。 緯線は赤道に平行です。 |
| 経度 (longitude) | double | 79,492 | -122.330863 -122.330541 | これは経度値です。 経線は緯線に垂直に走り、すべて両極を通ります。 |

## <a name="preview"></a>プレビュー

| dataType | dataSubtype | dateTime | category | subcategory | status | address | 緯度 (latitude) | 経度 (longitude) | source | extendedProperties |
|-|-|-|-|-|-|-|-|-|-|-|
| 安全性 | 911_Fire | 4/28/2021 5:22:00 AM | Rubbish Fire (小規模火災) | null | null | 200 University St | 47.607299 | -122.337087 | null |  |
| 安全性 | 911_Fire | 4/28/2021 5:15:00 AM | Triaged Incident (トリアージ インシデント) | null | null | 6th Ave / Olive Way | 47.61313 | -122.336282 | null |  |
| 安全性 | 911_Fire | 4/28/2021 5:12:00 AM | Aid Response (救援対応) | null | null | 4th Ave S / Seattle Blvd S | 47.596486 | -122.329046 | null |  |
| 安全性 | 911_Fire | 4/28/2021 5:09:00 AM | Rubbish Fire (小規模火災) | null | null | 3rd Ave / University St | 47.607763 | -122.335976 | null |  |
| 安全性 | 911_Fire | 4/28/2021 4:57:00 AM | Low Acuity Response (緊急度の低い対応) | null | null | 533 3rd Ave W | 47.623717 | -122.360635 | null |  |
| 安全性 | 911_Fire | 4/28/2021 4:57:00 AM | Trans to AMR (AMR に転送) | null | null | 4638 S Austin St | 47.534702 | -122.274812 | null |  |
| 安全性 | 911_Fire | 4/28/2021 4:55:00 AM | Triaged Incident (トリアージ インシデント) | null | null | 8th Ave N / Harrison St | 47.622051 | -122.341066 | null |  |

## <a name="data-access"></a>データ アクセス

### <a name="azure-notebooks"></a>Azure Notebooks

# <a name="azureml-opendatasets"></a>[azureml-opendatasets](#tab/azureml-opendatasets)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=city_safety_seattle -->

> [!TIP]
> _DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=city_safety_seattle_ のコンテンツ。 **[GitHub で開きます](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=city_safety_seattle)** 。

```
# This is a package in preview.
from azureml.opendatasets import SeattleSafety

from datetime import datetime
from dateutil import parser


end_date = parser.parse('2016-01-01')
start_date = parser.parse('2015-05-01')
safety = SeattleSafety(start_date=start_date, end_date=end_date)
safety = safety.to_pandas_dataframe()
```

```
safety.info()
```

<!-- nbend -->

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=city_safety_seattle -->

> [!TIP]
> **[代わりにノートブックをダウンロードします](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=city_safety_seattle)** 。

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
folder_name = "Safety/Release/city=Seattle"
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

# <a name="sql"></a>[SQL](#tab/sql)

このプラットフォームとパッケージの組み合わせでは、サンプルは利用できません。

---

### <a name="azure-databricks"></a>Azure Databricks

# <a name="azureml-opendatasets"></a>[azureml-opendatasets](#tab/azureml-opendatasets)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=azureml-opendatasets&registryId=city_safety_seattle -->

> [!TIP]
> **[代わりにノートブックをダウンロードします](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=azureml-opendatasets&registryId=city_safety_seattle)** 。

```
# This is a package in preview.
# You need to pip install azureml-opendatasets in Databricks cluster. https://docs.microsoft.com/en-us/azure/data-explorer/connect-from-databricks#install-the-python-library-on-your-azure-databricks-cluster
from azureml.opendatasets import SeattleSafety

from datetime import datetime
from dateutil import parser


end_date = parser.parse('2016-01-01')
start_date = parser.parse('2015-05-01')
safety = SeattleSafety(start_date=start_date, end_date=end_date)
safety = safety.to_spark_dataframe()
```

```
display(safety.limit(5))
```

<!-- nbend -->

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

このプラットフォームとパッケージの組み合わせでは、サンプルは利用できません。

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=city_safety_seattle -->

> [!TIP]
> **[代わりにノートブックをダウンロードします](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=city_safety_seattle)** 。

```python
# Azure storage access info
blob_account_name = "azureopendatastorage"
blob_container_name = "citydatacontainer"
blob_relative_path = "Safety/Release/city=Seattle"
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

# <a name="sql"></a>[SQL](#tab/sql)

このプラットフォームとパッケージの組み合わせでは、サンプルは利用できません。

---

### <a name="azure-synapse"></a>Azure Synapse

# <a name="azureml-opendatasets"></a>[azureml-opendatasets](#tab/azureml-opendatasets)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=azureml-opendatasets&registryId=city_safety_seattle -->

> [!TIP]
> **[代わりにノートブックをダウンロードします](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=azureml-opendatasets&registryId=city_safety_seattle)** 。

```python
# This is a package in preview.
from azureml.opendatasets import SeattleSafety

from datetime import datetime
from dateutil import parser


end_date = parser.parse('2016-01-01')
start_date = parser.parse('2015-05-01')
safety = SeattleSafety(start_date=start_date, end_date=end_date)
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

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=city_safety_seattle -->

> [!TIP]
> **[代わりにノートブックをダウンロードします](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=city_safety_seattle)** 。

```python
# Azure storage access info
blob_account_name = "azureopendatastorage"
blob_container_name = "citydatacontainer"
blob_relative_path = "Safety/Release/city=Seattle"
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

# <a name="sql"></a>[SQL](#tab/sql)

```sql
SELECT
    TOP 100 *
FROM
    OPENROWSET(
        BULK             'https://azureopendatastorage.blob.core.windows.net/citydatacontainer/Safety/Release/city=Seattle/*.parquet',
        FORMAT         = 'parquet'
    ) AS [r];
```

---

## <a name="examples"></a>例

- GitHub の[都市の安全性分析](https://github.com/scottcounts/CitySafety)の例を参照してください。


## <a name="next-steps"></a>次のステップ

[Open Datasets カタログ](dataset-catalog.md)の残りのデータセットを表示します。