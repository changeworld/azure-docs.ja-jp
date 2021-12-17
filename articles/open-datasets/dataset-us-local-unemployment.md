---
title: US Local Area Unemployment Statistics (米国地域別失業統計)
titleSuffix: Azure Open Datasets
description: Azure Open Datasets で US Local Area Unemployment Statistics (米国地域別失業統計) データセットを使用する方法について説明します。
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: 13af967282d1f9be8f289612936ab7d33a9fce11
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/11/2021
ms.locfileid: "112038780"
---
# <a name="us-local-area-unemployment-statistics"></a>US Local Area Unemployment Statistics (米国地域別失業統計)

Local Area Unemployment Statistics (LAUS) プログラムでは、米国の国勢調査地域/区分、州、郡、大都市圏、および多数の都市の雇用、失業、労働力に関する月次および年次データを生成します。

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

このデータセットの詳細情報が含まれた [README](https://download.bls.gov/pub/time.series/la/la.txt) ファイルは、[データセットの元の場所](https://download.bls.gov/pub/time.series/la/)で入手できます。

このデータセットは、[米国労働統計局 (BLS)](https://www.bls.gov/) によって公開されている [Local Area Unemployment Statistics (地域別失業統計) のデータ](https://www.bls.gov/lau/)をソースとしています。 このデータセットの使用に関する諸条件については、「[Linking and Copyright Information (リンクおよび著作権情報)](https://www.bls.gov/bls/linksite.htm)」と「[Important Web Site Notices (Web サイトに関する重要な通知)](https://www.bls.gov/bls/website-policies.htm)」を確認してください。

## <a name="storage-location"></a>保存先

このデータセットは、米国東部 Azure リージョンに保存されています。 アフィニティのために、米国東部でコンピューティング リソースを割り当てることをお勧めします。

## <a name="related-datasets"></a>関連データセット

- [US National Employment Hours and Earnings (全米労働時間および賃金)](dataset-us-national-employment-earnings.md)
- [US State Employment Hours and Earnings (米国州労働時間および賃金)](dataset-us-state-employment-earnings.md)
- [US Labor Force Statistics (米国労働力統計)](dataset-us-labor-force.md)

## <a name="columns"></a>[列]

| Name | データ型 | 一意 | 値 (サンプル) | 説明 |
|-|-|-|-|-|
| area_code | string | 8,290 | ST3400000000000 RD8200000000000 | 地理的地域を識別するコード。 以下を参照してください。https://download.bls.gov/pub/time.series/la/la.area |
| area_text | string | 8,238 | District of Columbia Oregon (オレゴン州コロンビア特別区) | 地理的地域の名前。 「https://download.bls.gov/pub/time.series/la/la.area」を参照してください。 |
| area_type_code | string | 14 | F G | 地域の種類を識別する一意のコード。 「https://download.bls.gov/pub/time.series/la/la.area_type」を参照してください。 |
| areatype_text | string | 14 | Counties and equivalents Cities and towns above 25,000 population (人口 25,000 人以上の郡、相当する市、および町) | 地域の種類の名前。 |
| footnote_codes | string | 5 | nan P |  |
| measure_code | string | 4 | 5 4 | 測定された要素を識別するコード。 03: 失業率、04: 失業、05: 雇用、06: 労働力。 以下を参照してください。https://download.bls.gov/pub/time.series/la/la.measure |
| measure_text | string | 4 | employment (雇用) unemployment (失業) | 測定された要素の名前。 「https://download.bls.gov/pub/time.series/la/la.measure」を参照してください。 |
| period | string | 13 | M07 M05 | 期間を識別 (通常は月)。 「https://download.bls.gov/pub/time.series/la/la.period」を参照してください。 |
| 季節 | string | 2 | U S |  |
| series_id | string | 33,476 | LASST160000000000006 LASST200000000000006 | 系列を識別するコード。 系列の完全なリストについては、 https://download.bls.gov/pub/time.series/la/la.series をご覧ください。 |
| series_title | string | 33,268 | Employment: Philadelphia County/city, PA (U) (雇用: ペンシルベニア州フィラデルフィア郡/市 (U)) Labor Force: Manassas city, VA (U) (労働力: バージニア州マナサス市 (U)) | 系列を識別するタイトル。 系列の完全なリストについては、 https://download.bls.gov/pub/time.series/la/la.series をご覧ください。 |
| srd_code | string | 53 | 48 23 | 州、地域、または区域コード。 |
| srd_text | string | 53 | Texas (テキサス州) Maine (メイン州) |  |
| value | float | 600,099 | 4.0 5.0 | 特定の測定の値。 |
| year | INT | 44 | 2009 2008 |  |

## <a name="preview"></a>プレビュー

| area_code | area_type_code | srd_code | measure_code | series_id | year | period | value | footnote_codes | 季節 | series_title | measure_text | srd_text | areatype_text | area_text |
|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|
| CA3653200000000 | E | 36 | 3 | LAUCA365320000000003 | 2000 | M01 | 4.7 | nan | U | Unemployment Rate: Syracuse-Auburn, NY Combined Statistical Area (U) (失業率: ニューヨーク州シラキュース-オーバーン統合統計圏 (U)) | unemployment rate (失業率) | ニューヨーク | Combined areas (統合圏) | Syracuse-Auburn, NY Combined Statistical Area (ニューヨーク州シラキュース-オーバーン統合統計圏) |
| CA3653200000000 | E | 36 | 3 | LAUCA365320000000003 | 2000 | M02 | 4.7 | nan | U | Unemployment Rate: Syracuse-Auburn, NY Combined Statistical Area (U) (失業率: ニューヨーク州シラキュース-オーバーン統合統計圏 (U)) | unemployment rate (失業率) | ニューヨーク | Combined areas (統合圏) | Syracuse-Auburn, NY Combined Statistical Area (ニューヨーク州シラキュース-オーバーン統合統計圏) |
| CA3653200000000 | E | 36 | 3 | LAUCA365320000000003 | 2000 | M03 | 4.2 | nan | U | Unemployment Rate: Syracuse-Auburn, NY Combined Statistical Area (U) (失業率: ニューヨーク州シラキュース-オーバーン統合統計圏 (U)) | unemployment rate (失業率) | ニューヨーク | Combined areas (統合圏) | Syracuse-Auburn, NY Combined Statistical Area (ニューヨーク州シラキュース-オーバーン統合統計圏) |
| CA3653200000000 | E | 36 | 3 | LAUCA365320000000003 | 2000 | M04 | 3.6 | nan | U | Unemployment Rate: Syracuse-Auburn, NY Combined Statistical Area (U) (失業率: ニューヨーク州シラキュース-オーバーン統合統計圏 (U)) | unemployment rate (失業率) | ニューヨーク | Combined areas (統合圏) | Syracuse-Auburn, NY Combined Statistical Area (ニューヨーク州シラキュース-オーバーン統合統計圏) |
| CA3653200000000 | E | 36 | 3 | LAUCA365320000000003 | 2000 | M05 | 3.6 | nan | U | Unemployment Rate: Syracuse-Auburn, NY Combined Statistical Area (U) (失業率: ニューヨーク州シラキュース-オーバーン統合統計圏 (U)) | unemployment rate (失業率) | ニューヨーク | Combined areas (統合圏) | Syracuse-Auburn, NY Combined Statistical Area (ニューヨーク州シラキュース-オーバーン統合統計圏) |
| CA3653200000000 | E | 36 | 3 | LAUCA365320000000003 | 2000 | M06 | 3.6 | nan | U | Unemployment Rate: Syracuse-Auburn, NY Combined Statistical Area (U) (失業率: ニューヨーク州シラキュース-オーバーン統合統計圏 (U)) | unemployment rate (失業率) | ニューヨーク | Combined areas (統合圏) | Syracuse-Auburn, NY Combined Statistical Area (ニューヨーク州シラキュース-オーバーン統合統計圏) |
| CA3653200000000 | E | 36 | 3 | LAUCA365320000000003 | 2000 | M07 | 3.6 | nan | U | Unemployment Rate: Syracuse-Auburn, NY Combined Statistical Area (U) (失業率: ニューヨーク州シラキュース-オーバーン統合統計圏 (U)) | unemployment rate (失業率) | ニューヨーク | Combined areas (統合圏) | Syracuse-Auburn, NY Combined Statistical Area (ニューヨーク州シラキュース-オーバーン統合統計圏) |

## <a name="data-access"></a>データ アクセス

### <a name="azure-notebooks"></a>Azure Notebooks

# <a name="azureml-opendatasets"></a>[azureml-opendatasets](#tab/azureml-opendatasets)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=us-local-area-unemployment-statistics -->

> [!TIP]
> **[代わりにノートブックをダウンロードします](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=us-local-area-unemployment-statistics)** 。

```python
# This is a package in preview.
from azureml.opendatasets import UsLaborLAUS

usLaborLAUS = UsLaborLAUS()
usLaborLAUS_df = usLaborLAUS.to_pandas_dataframe()
```

```python
usLaborLAUS_df.info()
```

<!-- nbend -->

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=us-local-area-unemployment-statistics -->

> [!TIP]
> **[代わりにノートブックをダウンロードします](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=us-local-area-unemployment-statistics)** 。

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
folder_name = "laus/"
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

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=azureml-opendatasets&registryId=us-local-area-unemployment-statistics -->

> [!TIP]
> **[代わりにノートブックをダウンロードします](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=azureml-opendatasets&registryId=us-local-area-unemployment-statistics)** 。

```python
# This is a package in preview.
from azureml.opendatasets import UsLaborLAUS

usLaborLAUS = UsLaborLAUS()
usLaborLAUS_df = usLaborLAUS.to_spark_dataframe()
```

```python
display(usLaborLAUS_df.limit(5))
```

<!-- nbend -->

 
# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

このプラットフォームとパッケージの組み合わせでは、サンプルは利用できません。

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=us-local-area-unemployment-statistics -->

> [!TIP]
> **[代わりにノートブックをダウンロードします](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=us-local-area-unemployment-statistics)** 。

```python
# Azure storage access info
blob_account_name = "azureopendatastorage"
blob_container_name = "laborstatisticscontainer"
blob_relative_path = "laus/"
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

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=us-local-area-unemployment-statistics -->

> [!TIP]
> **[代わりにノートブックをダウンロードします](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=us-local-area-unemployment-statistics)** 。

```python
# Azure storage access info
blob_account_name = "azureopendatastorage"
blob_container_name = "laborstatisticscontainer"
blob_relative_path = "laus/"
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