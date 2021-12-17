---
title: NYC タクシーおよびリムジン イエロー データセット
titleSuffix: Azure Open Datasets
description: Azure Open Datasets で NYC タクシーおよびリムジン イエロー データセットを使用する方法について説明します。
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: 5bf2a3b363c7d8a1cd0b10b1c958c4cfbb567deb
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/11/2021
ms.locfileid: "112038924"
---
# <a name="nyc-taxi--limousine-commission---yellow-taxi-trip-records"></a>NYC タクシー & リムジン協会 - イエロー タクシー運行記録

イエロー タクシー乗車記録には、乗車と降車の日時、乗車と降車の場所、移動距離、料金明細、料金の種類、支払いの種類、運転手から報告された乗車人数が入力されたフィールドが含まれています。

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

## <a name="volume-and-retention"></a>ボリュームとデータ保持期間

このデータセットは Parquet 形式で保存されています。 2018 年時点で合計約 15 億行 (50 GB) あります。

このデータセットには、2009 年から 2018 年までに蓄積された過去の記録が含まれます。 SDK でパラメーター設定を使用して、特定の時間範囲内のデータをフェッチできます。

## <a name="storage-location"></a>保存先

このデータセットは、米国東部 Azure リージョンに保存されています。 アフィニティのために、米国東部でコンピューティング リソースを割り当てることをお勧めします。

## <a name="additional-information"></a>追加情報

ニューヨーク市タクシー & リムジン委員会 (TLC):

データは、Taxicab & Livery Passenger Enhancement Programs (TPEP/LPEP) の下で承認されたテクノロジ プロバイダーによって収集され、ニューヨーク市タクシー & リムジン委員会 (TLC) に提供されました。 乗車データは TLC によって作成されたものではなく、TLC はこれらのデータの正確性に関して一切の表明を行いません。

[元のデータセットの場所](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page)と[元の使用条件](https://www1.nyc.gov/home/terms-of-use.page)を表示します。

## <a name="columns"></a>[列]
| Name                 | データ型 | 一意      | 値 (サンプル)                         | 説明                                                                                                                                                                                                                                            |
|----------------------|-----------|-------------|-----------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| doLocationId         | string    | 265         | 161 236                                 | タクシーメーターが解除された TLC タクシー ゾーン。                                                                                                                                                                                                   |
| endLat               | double    | 961,994     | 41.366138 40.75                         |                                                                                                                                                                                                                                                        |
| endLon               | double    | 1,144,935   | -73.137393 -73.9824                     |                                                                                                                                                                                                                                                        |
| extra                | double    | 877         | 0.5 1.0                                 | その他の割増料金と追加料金。 現在、これには 0.50 ドルおよび 1 ドルのラッシュアワー料金と夜間料金のみが含まれます。                                                                                                                                   |
| fareAmount           | double    | 18,935      | 6.5 4.5                                 | メーターによって計算された時間距離併用運賃。                                                                                                                                                                                                    |
| improvementSurcharge | string    | 60          | 0.3 0                                   | 初乗り運賃での乗車に課される 0.30 ドルの改善追加料金。 改善追加料金は 2015 年に徴収が開始されました。                                                                                                                                     |
| mtaTax               | double    | 360         | 0.5 -0.5                                | 使用中のメーター制料金に基づいて自動的にトリガーされる 0.50 ドルの MTA 税。                                                                                                                                                                        |
| passengerCount       | INT       | 64          | 1 2                                     | 乗車人数。 これは運転手が入力した値です。                                                                                                                                                                               |
| paymentType          | string    | 6,282       | CSH CRD                                 | 乗客が乗車料金をどのように支払ったかを示す数値コード。 1= クレジット カード 2= 現金 3= 無料 4= 争議 5= 不明 6= 無効な乗車                                                                                                          |
| puLocationId         | string    | 266         | 237 161                                 | タクシーメーターが作動し始めた TLC タクシー ゾーン。                                                                                                                                                                                                      |
| puMonth              | INT       | 12          | 3 5                                     |                                                                                                                                                                                                                                                        |
| puYear               | INT       | 29          | 2012 2011                               |                                                                                                                                                                                                                                                        |
| rateCodeId           | INT       | 56          | 1 2                                     | 乗車終了時に適用される最終的な料金コード。 1= 標準料金 2= JFK 3= ニューアーク 4= ナッソーまたはウエストチェスター 5= ネゴシエート料金 6= グループ乗車                                                                                                |
| startLat             | double    | 833,016     | 41.366138 40.7741                       |                                                                                                                                                                                                                                                        |
| startLon             | double    | 957,428     | -73.137393 -73.9821                     |                                                                                                                                                                                                                                                        |
| storeAndFwdFlag      | string    | 8           | N 0                                     | このフラグは、車両がサーバーに接続されていないため、乗車記録がベンダーに送信される前に車両のメモリに保持されていたどうか ("ストア アンド フォワード" とも呼ばれます) を示します。 Y= ストア アンド フォワード乗車 N= ストア アンド フォワード乗車ではない |
| tipAmount            | double    | 12,121      | 1.0 2.0                                 | このフィールドは、クレジット カードのチップの場合に自動的に入力されます。 現金のチップは含まれません。                                                                                                                                                                |
| tollsAmount          | double    | 6,634       | 5.33 4.8                                | 乗車中に支払われたすべての通行料金の合計金額。                                                                                                                                                                                                                |
| totalAmount          | double    | 39,707      | 7.0 7.8                                 | 乗客に請求される合計金額。 現金のチップは含まれません。                                                                                                                                                                                    |
| tpepDropoffDateTime  | timestamp | 290,185,010 | 2010-11-07 01:29:00 2013-11-03 01:22:00 | メーターが解除された日時。                                                                                                                                                                                                       |
| tpepPickupDateTime   | timestamp | 289,948,585 | 2010-11-07 01:00:00 2009-11-01 01:05:00 | メーターが作動し始めた日時。                                                                                                                                                                                                          |
| tripDistance         | double    | 14,003      | 1.0 0.9                                 | タクシーメーターによって報告された走行距離 (マイル単位)。                                                                                                                                                                                          |
| vendorID             | string    | 7           | VTS CMT                                 | レコードを提供した TPEP プロバイダーを示すコード。 1= Creative Mobile Technologies, LLC、2= VeriFone Inc.                                                                                                                                   |
| vendorID             | INT       | 2           | 2 1                                     | レコードを提供した LPEP プロバイダーを示すコード。 1= Creative Mobile Technologies, LLC、2= VeriFone Inc.                                                                                                                                   |

## <a name="preview"></a>プレビュー

| vendorID | tpepPickupDateTime    | tpepDropoffDateTime   | passengerCount | tripDistance | puLocationId | doLocationId | rateCodeId | storeAndFwdFlag | paymentType | fareAmount | extra | mtaTax | improvementSurcharge | tipAmount | tollsAmount | totalAmount | puYear | puMonth |
|----------|-----------------------|-----------------------|----------------|--------------|--------------|--------------|------------|-----------------|-------------|------------|-------|--------|----------------------|-----------|-------------|-------------|--------|---------|
| 2        | 1/24/2088 12:25:39 AM | 1/24/2088 7:28:25 AM  | 1              | 4.05         | 24           | 162          | 1          | N               | 2           | 14.5       | 0     | 0.5    | 0.3                  | 0         | 0           | 15.3        | 2088   | 1       |
| 2        | 1/24/2088 12:15:42 AM | 1/24/2088 12:19:46 AM | 1              | 0.63         | 41           | 166          | 1          | N               | 2           | 4.5        | 0     | 0.5    | 0.3                  | 0         | 0           | 5.3         | 2088   | 1       |
| 2        | 11/4/2084 12:32:24 PM | 11/4/2084 12:47:41 PM | 1              | 1.34         | 238          | 236          | 1          | N               | 2           | 10         | 0     | 0.5    | 0.3                  | 0         | 0           | 10.8        | 2084   | 11      |
| 2        | 11/4/2084 12:25:53 PM | 11/4/2084 12:29:00 PM | 1              | 0.32         | 238          | 238          | 1          | N               | 2           | 4          | 0     | 0.5    | 0.3                  | 0         | 0           | 4.8         | 2084   | 11      |
| 2        | 11/4/2084 12:08:33 PM | 11/4/2084 12:22:24 PM | 1              | 1.85         | 236          | 238          | 1          | N               | 2           | 10         | 0     | 0.5    | 0.3                  | 0         | 0           | 10.8        | 2084   | 11      |
| 2        | 11/4/2084 11:41:35 AM | 11/4/2084 11:59:41 AM | 1              | 1.65         | 68           | 237          | 1          | N               | 2           | 12.5       | 0     | 0.5    | 0.3                  | 0         | 0           | 13.3        | 2084   | 11      |
| 2        | 11/4/2084 11:27:28 AM | 11/4/2084 11:39:52 AM | 1              | 1.07         | 170          | 68           | 1          | N               | 2           | 9          | 0     | 0.5    | 0.3                  | 0         | 0           | 9.8         | 2084   | 11      |
| 2        | 11/4/2084 11:19:06 AM | 11/4/2084 11:26:44 AM | 1              | 1.3          | 107          | 170          | 1          | N               | 2           | 7.5        | 0     | 0.5    | 0.3                  | 0         | 0           | 8.3         | 2084   | 11      |
| 2        | 11/4/2084 11:02:59 AM | 11/4/2084 11:15:51 AM | 1              | 1.85         | 113          | 137          | 1          | N               | 2           | 10         | 0     | 0.5    | 0.3                  | 0         | 0           | 10.8        | 2084   | 11      |
| 2        | 11/4/2084 10:46:05 AM | 11/4/2084 10:50:09 AM | 1              | 0.62         | 231          | 231          | 1          | N               | 2           | 4.5        | 0     | 0.5    | 0.3                  | 0         | 0           | 5.3         | 2084   | 11      |

## <a name="data-access"></a>データ アクセス

### <a name="azure-notebooks"></a>Azure Notebooks

# <a name="azureml-opendatasets"></a>[azureml-opendatasets](#tab/azureml-opendatasets)

```python
# This is a package in preview.
from azureml.opendatasets import NycTlcYellow

from datetime import datetime
from dateutil import parser


end_date = parser.parse('2018-06-06')
start_date = parser.parse('2018-05-01')
nyc_tlc = NycTlcYellow(start_date=start_date, end_date=end_date)
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
folder_name = "yellow"

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
from azureml.opendatasets import NycTlcYellow

from datetime import datetime
from dateutil import parser


end_date = parser.parse('2018-06-06')
start_date = parser.parse('2018-05-01')
nyc_tlc = NycTlcYellow(start_date=start_date, end_date=end_date)
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
blob_relative_path = "yellow"
blob_sas_token = r"

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
from azureml.opendatasets import NycTlcYellow

from datetime import datetime
from dateutil import parser

end_date = parser.parse('2018-06-06')
start_date = parser.parse('2018-05-01')
nyc_tlc = NycTlcYellow(start_date=start_date, end_date=end_date)
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
blob_relative_path = "yellow"
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