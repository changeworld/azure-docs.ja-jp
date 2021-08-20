---
title: ニューヨーク市のタクシーおよびリムジンのハイヤー車両データセット
titleSuffix: Azure Open Datasets
description: Azure Open Datasets でニューヨーク市のタクシーおよびリムジン のハイヤー車両 (VHF) データセットを使用する方法について説明します。
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: 2875ddfa1bf94121bea9038b576035042accbcfe
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/11/2021
ms.locfileid: "112038716"
---
# <a name="nyc-taxi--limousine-commission---for-hire-vehicle-fhv-trip-records"></a>NYC タクシー & リムジン協会 - ハイヤー車両 (FHV) 運行記録

ハイヤー ("FHV") の乗車記録には、配車側のベース ライセンス番号、乗車の日時、タクシー ゾーンの場所 ID (下記のシェープ ファイル) が入力されたフィールドが含まれています。 これらの記録は、ベースによって提出された FHV 乗車記録から生成されます。

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

## <a name="volume-and-retention"></a>ボリュームとデータ保持期間

このデータセットは Parquet 形式で保存されています。 2018 年時点で約 5 億行 (5 GB) あります。

このデータセットには、2009 年から 2018 年までに蓄積された過去の記録が含まれます。 SDK でパラメーター設定を使用して、特定の時間範囲内のデータをフェッチできます。

## <a name="storage-location"></a>保存先

このデータセットは、米国東部 Azure リージョンに保存されています。 アフィニティのために、米国東部でコンピューティング リソースを割り当てることをお勧めします。

## <a name="additional-information"></a>追加情報

ニューヨーク市タクシー & リムジン委員会 (TLC):

データは、Taxicab & Livery Passenger Enhancement Programs (TPEP/LPEP) の下で承認されたテクノロジ プロバイダーによって収集され、ニューヨーク市タクシー & リムジン委員会 (TLC) に提供されました。 乗車データは TLC によって作成されたものではなく、TLC はこれらのデータの正確性に関して一切の表明を行いません。

[元のデータセットの場所](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page)と[元の使用条件](https://www1.nyc.gov/home/terms-of-use.page)を表示します。

## <a name="columns"></a>[列]

| Name            | データ型 | 一意      | 値 (サンプル)                         | 説明                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
|-----------------|-----------|-------------|-----------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| dispatchBaseNum | string    | 1,144       | B02510 B02764                           | 配車を行ったベースの TLC ベース ライセンス番号                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| doLocationId    | string    | 267         | 265 132                                 | 乗車が終了した TLC タクシー ゾーン。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| dropOffDateTime | timestamp | 57,110,352  | 2017-07-31 23:59:00 2017-10-15 00:44:34 | 降車の日時。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| pickupDateTime  | timestamp | 111,270,396 | 2016-08-16 00:00:00 2016-08-17 00:00:00 | 乗車の日時。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| puLocationId    | string    | 266         | 79 161                                  | 乗車開始された TLC タクシー ゾーン。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| puMonth         | INT       | 12          | 1 12                                    |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| puYear          | INT       | 5           | 2018 2017                               |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| srFlag          | string    | 44          | 1 2                                     | 乗車が、大規模 FHV 企業 (Uber Pool、Lyft Line など) によって提供されるライドシェア チェーンの一部であったかどうかを示します。 相乗りの場合、値は 1 です。 相乗りではない場合、このフィールドは null です。 注:ほとんどの大規模 FHV 企業の場合、要求され、なおかつ移動中に別のライドシェア要求と一致したライドシェアにのみフラグが設定されます。 ただし、Lyft (ベース ライセンス番号 B02510 + B02844) では、ライドシェアが要求されたが、別の乗客の相乗りのマッチングに失敗した乗車にもフラグを設定します。そのため、この 2 つのベースの SR_Flag が 1 の乗車記録は、ライドシェア チェーンの最初の乗車、またはライドシェアが要求されたがマッチングされなかった乗車のいずれかを示している可能性があります。 ユーザーは、Lyft が正常に完了した相乗りが過大評価されている可能性があることを考慮しておく必要があります。 |

## <a name="preview"></a>プレビュー

| dispatchBaseNum | pickupDateTime        | dropOffDateTime      | puLocationId | doLocationId | srFlag | puYear | puMonth |
|-----------------|-----------------------|----------------------|--------------|--------------|--------|--------|---------|
| B03157          | 6/30/2019 11:59:57 PM | 7/1/2019 12:07:21 AM | 264          | null         | null   | 2019   | 6       |
| B01667          | 6/30/2019 11:59:56 PM | 7/1/2019 12:28:06 AM | 264          | null         | null   | 2019   | 6       |
| B02849          | 6/30/2019 11:59:55 PM | 7/1/2019 12:14:10 AM | 264          | null         | null   | 2019   | 6       |
| B02249          | 6/30/2019 11:59:53 PM | 7/1/2019 12:15:53 AM | 264          | null         | null   | 2019   | 6       |
| B00887          | 6/30/2019 11:59:48 PM | 7/1/2019 12:29:29 AM | 264          | null         | null   | 2019   | 6       |
| B01626          | 6/30/2019 11:59:45 PM | 7/1/2019 12:18:20 AM | 264          | null         | null   | 2019   | 6       |
| B01259          | 6/30/2019 11:59:44 PM | 7/1/2019 12:03:15 AM | 264          | null         | null   | 2019   | 6       |
| B01145          | 6/30/2019 11:59:43 PM | 7/1/2019 12:11:15 AM | 264          | null         | null   | 2019   | 6       |
| B00887          | 6/30/2019 11:59:42 PM | 7/1/2019 12:34:21 AM | 264          | null         | null   | 2019   | 6       |
| B00821          | 6/30/2019 11:59:40 PM | 7/1/2019 12:02:57 AM | 264          | null         | null   | 2019   | 6       |

## <a name="data-access"></a>データ アクセス

### <a name="azure-notebooks"></a>Azure Notebooks

# <a name="azureml-opendatasets"></a>[azureml-opendatasets](#tab/azureml-opendatasets)

```python
# This is a package in preview.
from azureml.opendatasets import NycTlcFhv

from datetime import datetime
from dateutil import parser


end_date = parser.parse('2018-06-06')
start_date = parser.parse('2018-05-01')
nyc_tlc = NycTlcFhv(start_date=start_date, end_date=end_date)
nyc_tlc_df = nyc_tlc.to_pandas_dataframe()

nyc_tlc_df.info()
```

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

```python
# Pip install packages
import os, sys

!{sys.executable} -m pip install azure-storage-blob
!{sys.executable} -m pip install pyarrow
!{sys.executable} -m pip install pandas

# Azure storage access info
azure_storage_account_name = "azureopendatastorage"
azure_storage_sas_token = r""
container_name = "nyctlc"
folder_name = "fhv"

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

# Read the parquet file into Pandas data frame
import pandas as pd

print('Reading the parquet file into Pandas data frame')
df = pd.read_parquet(filename)

# you can add your filter at below
print('Loaded as a Pandas data frame: ')
df
```

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

このプラットフォームとパッケージの組み合わせでは、サンプルは利用できません。

---

### <a name="azure-databricks"></a>Azure Databricks

# <a name="azureml-opendatasets"></a>[azureml-opendatasets](#tab/azureml-opendatasets)

```python
# This is a package in preview.
# You need to pip install azureml-opendatasets in Databricks cluster. https://docs.microsoft.com/en-us/azure/data-explorer/connect-from-databricks#install-the-python-library-on-your-azure-databricks-cluster
from azureml.opendatasets import NycTlcFhv

from datetime import datetime
from dateutil import parser


end_date = parser.parse('2018-06-06')
start_date = parser.parse('2018-05-01')
nyc_tlc = NycTlcFhv(start_date=start_date, end_date=end_date)
nyc_tlc_df = nyc_tlc.to_spark_dataframe()

display(nyc_tlc_df.limit(5))
```

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

このプラットフォームとパッケージの組み合わせでは、サンプルは利用できません。

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

```python
# Azure storage access info
blob_account_name = "azureopendatastorage"
blob_container_name = "nyctlc"
blob_relative_path = "fhv"
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

# <a name="azureml-opendatasets"></a>[azureml-opendatasets](#tab/azureml-opendatasets)

```python
# This is a package in preview.
from azureml.opendatasets import NycTlcFhv

from datetime import datetime
from dateutil import parser


end_date = parser.parse('2018-06-06')
start_date = parser.parse('2018-05-01')
nyc_tlc = NycTlcFhv(start_date=start_date, end_date=end_date)
nyc_tlc_df = nyc_tlc.to_spark_dataframe()

# Display top 5 rows
display(nyc_tlc_df.limit(5))
```

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

このプラットフォームとパッケージの組み合わせでは、サンプルは利用できません。

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

```python
# Azure storage access info
blob_account_name = "azureopendatastorage"
blob_container_name = "nyctlc"
blob_relative_path = "fhv"
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

## <a name="next-steps"></a>次のステップ

[Open Datasets カタログ](dataset-catalog.md)の残りのデータセットを表示します。