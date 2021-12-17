---
title: 米国生産者物価指数 (業種)
titleSuffix: Azure Open Datasets
description: Azure Open Datasets で米国生産者物価指数 (業種) データセットを使用する方法について説明します。
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: cd0662fc4990e6a2baf0a29ba2e96cec50509a05
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/11/2021
ms.locfileid: "112038769"
---
# <a name="us-consumer-price-index-industry"></a>米国消費者物価指数 (業種)

生産者物価指数 (PPI) は、国内生産者がその生産物に対して得た販売価格の経時的な平均的変動を測定するものです。 PPI に含まれる価格は、対象となる製品およびサービスの最初の商取引時点での価格です。

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

「Producer Price Index Revision-Current Series (生産者物価指数の改定 - 現在の系列)」の指数は、北米産業分類体系 (NAICS) に従って整理された生産者の純生産高の価格変動を反映しています。 PC データセットは、NAICS ベースの各種経済時系列 (生産性、生産、雇用、賃金、収益など) に対応しています。

PPI の分野は、米国経済の製造部門 (鉱業、製造業、農業、漁業、林業) のあらゆる業種の生産高と、天然ガス、電気、建設、および製造部門で製造されるものに対する競争力があるもの (廃棄物や廃材など) の生産高で構成されます。 さらに、2011 年 1 月時点で、PPI プログラムはサービス部門の生産高の 4 分の 3 以上を対象とするようになり、卸売/小売、輸送/倉庫、情報、金融/保険、不動産仲介/レンタル/リース、専門的/科学的/技術的サービス、管理/サポート/廃棄物管理サービス、医療/社会的支援、宿泊施設の各産業部門の特定の業種のデータを公開しています。

このデータセットの詳細情報が含まれた [README](https://download.bls.gov/pub/time.series/wp/wp.txt) ファイルは、[データセットの元の場所](https://download.bls.gov/pub/time.series/wp/)で入手できます。 追加情報は [FAQ](https://www.bls.gov/ppi/ppifaq.htm) で提供されています。

このデータセットは、[米国労働統計局 (BLS)](https://www.bls.gov/) によって公開されている [Producer Price Indexes (生産者物価指数) のデータ](https://www.bls.gov/ppi/)から生成されています。 このデータセットの使用に関する諸条件については、「[Linking and Copyright Information (リンクおよび著作権情報)](https://www.bls.gov/bls/linksite.htm)」と「[Important Web Site Notices (Web サイトに関する重要な通知)](https://www.bls.gov/bls/website-policies.htm)」を確認してください。

## <a name="storage-location"></a>保存先

このデータセットは、米国東部 Azure リージョンに保存されています。 アフィニティのために、米国東部でコンピューティング リソースを割り当てることをお勧めします。

## <a name="related-datasets"></a>関連データセット

- [米国消費者物価指数](dataset-us-consumer-price-index.md)
- [米国生産者物価指数 - 商品](dataset-us-producer-price-index-commodities.md)

## <a name="columns"></a>[列]

| Name | データ型 | 一意 | 値 (サンプル) | 説明 |
|-|-|-|-|-|
| footnote_codes | string | 3 | nan P | データ系列の脚注を示します。 ほとんどの値が null です。 以下を参照してください。https://download.bls.gov/pub/time.series/pc/pc.footnote |
| industry_code | string | 1,064 | 221122 325412 | その業種の NAICS コード。 コードと名前については、 https://download.bls.gov/pub/time.series/pc/pc.industry をご覧ください。 |
| industry_name | string | 842 | Electric power distribution Pharmaceutical preparation manufacturing | その業種のコードに対応する名前。 コードと名前については、 https://download.bls.gov/pub/time.series/pc/pc.industry をご覧ください。 |
| period | string | 13 | M06 M07 | データが観測される期間を示します。 完全なリストについては、 https://download.bls.gov/pub/time.series/pc/pc.period をご覧ください。 |
| product_code | string | 4,822 | 324121P 316--- | データ観測で参照する製品を識別するコード。 業界コード、製品コード、製品名の対応については、 https://download.bls.gov/pub/time.series/pc/pc.product を参照してください。 |
| product_name | string | 3,313 | Primary products Secondary products | データ観測で参照する製品の名前。 業界コード、製品コード、製品名の対応については、 https://download.bls.gov/pub/time.series/pc/pc.product を参照してください。 |
| 季節 | string | 1 | U | データが季節調整されているかどうかを識別するコード。 S = 季節調整済み、U = 未調整 |
| series_id | string | 4,822 | PCU332323332323M PCU333415333415C | 特定の系列を識別するコード。 時系列とは、一定の時間間隔で長期間にわたって観測された一連のデータを指します。 コード、名前、開始年、終了年などの系列の詳細については、 https://download.bls.gov/pub/time.series/pc/pc.series をご覧ください。 |
| series_title | string | 4,588 | PPI industry data for Electric power distribution-West South Central, not seasonally adjusted PPI industry data for Electric power distribution-Pacific, not seasonally adjusted |  |
| value | float | 7,658 | 100.0 100.4000015258789 | 商品の物価指数。 |
| year | INT | 22 | 2015 2017 | 観測の年を示します。 |

## <a name="preview"></a>プレビュー

| product_code | industry_code | series_id | year | period | value | footnote_codes | 季節 | series_title | industry_name | product_name |
|-|-|-|-|-|-|-|-|-|-|-|
| 2123240 | 212324 | PCU2123242123240 | 1998 | M01 | 117 | nan | U | PPI industry data for Kaolin and ball clay mining-Kaolin and ball clay, not seasonally adjusted | Kaolin and ball clay mining | Kaolin and ball clay |
| 2123240 | 212324 | PCU2123242123240 | 1998 | M02 | 116.9 | nan | U | PPI industry data for Kaolin and ball clay mining-Kaolin and ball clay, not seasonally adjusted | Kaolin and ball clay mining | Kaolin and ball clay |
| 2123240 | 212324 | PCU2123242123240 | 1998 | M03 | 116.3 | nan | U | PPI industry data for Kaolin and ball clay mining-Kaolin and ball clay, not seasonally adjusted | Kaolin and ball clay mining | Kaolin and ball clay |
| 2123240 | 212324 | PCU2123242123240 | 1998 | M04 | 116 | nan | U | PPI industry data for Kaolin and ball clay mining-Kaolin and ball clay, not seasonally adjusted | Kaolin and ball clay mining | Kaolin and ball clay |
| 2123240 | 212324 | PCU2123242123240 | 1998 | M05 | 116.2 | nan | U | PPI industry data for Kaolin and ball clay mining-Kaolin and ball clay, not seasonally adjusted | Kaolin and ball clay mining | Kaolin and ball clay |
| 2123240 | 212324 | PCU2123242123240 | 1998 | M06 | 116.3 | nan | U | PPI industry data for Kaolin and ball clay mining-Kaolin and ball clay, not seasonally adjusted | Kaolin and ball clay mining | Kaolin and ball clay |
| 2123240 | 212324 | PCU2123242123240 | 1998 | M07 | 116.6 | nan | U | PPI industry data for Kaolin and ball clay mining-Kaolin and ball clay, not seasonally adjusted | Kaolin and ball clay mining | Kaolin and ball clay |

## <a name="data-access"></a>データ アクセス

### <a name="azure-notebooks"></a>Azure Notebooks

# <a name="azureml-opendatasets"></a>[azureml-opendatasets](#tab/azureml-opendatasets)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=us-producer-price-index-industry -->

> [!TIP]
> **[代わりにノートブックをダウンロードします](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=us-producer-price-index-industry)** 。

```python
# This is a package in preview.
from azureml.opendatasets import UsLaborPPIIndustry

labor = UsLaborPPIIndustry()
labor_df = labor.to_pandas_dataframe()
```

```python
labor_df.info()
```

<!-- nbend -->

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=us-producer-price-index-industry -->

> [!TIP]
> **[代わりにノートブックをダウンロードします](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=us-producer-price-index-industry)** 。

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
folder_name = "ppi_industry/"
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

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=azureml-opendatasets&registryId=us-producer-price-index-industry -->

> [!TIP]
> **[代わりにノートブックをダウンロードします](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=azureml-opendatasets&registryId=us-producer-price-index-industry)** 。

```python
# This is a package in preview.
from azureml.opendatasets import UsLaborPPIIndustry

labor = UsLaborPPIIndustry()
labor_df = labor.to_spark_dataframe()
```

```python
display(labor_df.limit(5))
```

<!-- nbend -->
 
# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

このプラットフォームとパッケージの組み合わせでは、サンプルは利用できません。

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=us-producer-price-index-industry -->

> [!TIP]
> **[代わりにノートブックをダウンロードします](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=us-producer-price-index-industry)** 。

```python
# Azure storage access info
blob_account_name = "azureopendatastorage"
blob_container_name = "laborstatisticscontainer"
blob_relative_path = "ppi_industry/"
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

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=us-producer-price-index-industry -->

> [!TIP]
> **[代わりにノートブックをダウンロードします](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=us-producer-price-index-industry)** 。

```python
# Azure storage access info
blob_account_name = "azureopendatastorage"
blob_container_name = "laborstatisticscontainer"
blob_relative_path = "ppi_industry/"
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