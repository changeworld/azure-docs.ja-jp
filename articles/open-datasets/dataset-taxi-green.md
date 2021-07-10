---
title: NYC タクシーおよびリムジン green データセット
titleSuffix: Azure Open Datasets
description: Azure Open Datasets で NYC タクシーおよびリムジン green データセットを使用する方法について説明します。
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: 058969168b09c1eb394b6fc7a9f06c401c7f9763
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/11/2021
ms.locfileid: "112038798"
---
# <a name="nyc-taxi--limousine-commission---green-taxi-trip-records"></a>NYC タクシー & リムジン協会 - グリーン タクシー運行記録

グリーン タクシー乗車記録には、乗車と降車の日時、乗車と降車の場所、移動距離、料金明細、料金の種類、支払いの種類、運転手から報告された乗車人数が入力されたフィールドが含まれています。

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

## <a name="volume-and-retention"></a>ボリュームとデータ保持期間

このデータセットは Parquet 形式で保存されています。 2018 年時点で合計約 8,000 万行 (2 GB) あります。

このデータセットには、2009 年から 2018 年までに蓄積された過去の記録が含まれます。 SDK でパラメーター設定を使用して、特定の時間範囲内のデータをフェッチできます。

## <a name="storage-location"></a>保存先

このデータセットは、米国東部 Azure リージョンに保存されています。 アフィニティのために、米国東部でコンピューティング リソースを割り当てることをお勧めします。

## <a name="additional-information"></a>追加情報

ニューヨーク市タクシー & リムジン委員会 (TLC):

データは、Taxicab & Livery Passenger Enhancement Programs (TPEP/LPEP) の下で承認されたテクノロジ プロバイダーによって収集され、ニューヨーク市タクシー & リムジン委員会 (TLC) に提供されました。 乗車データは TLC によって作成されたものではなく、TLC はこれらのデータの正確性に関して一切の表明を行いません。

[元のデータセットの場所](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page)と[元の使用条件](https://www1.nyc.gov/home/terms-of-use.page)を表示します。

## <a name="columns"></a>[列]

| Name                 | データ型 | 一意     | 値 (サンプル)                         | 説明                                                                                                                                                                                                                                          |
|----------------------|-----------|------------|-----------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| doLocationId         | string    | 264        | 74 42                                   | タクシーメーターが解除された DOLocationID TLC タクシー ゾーン。                                                                                                                                                                                    |
| dropoffLatitude      | double    | 109,721    | 40.7743034362793 40.77431869506836      | 2016 年 7 月以降は非推奨                                                                                                                                                                                                                       |
| dropoffLongitude     | double    | 75,502     | -73.95272827148438 -73.95274353027344   | 2016 年 7 月以降は非推奨                                                                                                                                                                                                                       |
| extra                | double    | 202        | 0.5 1.0                                 | その他の割増料金と追加料金。 現在、これには 0.50 ドルおよび 1 ドルのラッシュアワー料金と夜間料金のみが含まれます。                                                                                                                                 |
| fareAmount           | double    | 10,367     | 6.0 5.5                                 | メーターによって計算された時間距離併用運賃。                                                                                                                                                                                                  |
| improvementSurcharge | string    | 92         | 0.3 0                                   | 初乗り運賃での乗車に課される 0.30 ドルの改善追加料金。 改善追加料金は 2015 年に徴収が開始されました。                                                                                                                         |
| lpepDropoffDatetime  | timestamp | 58,100,713 | 2016-05-22 00:00:00 2016-05-09 00:00:00 | メーターが解除された日時。                                                                                                                                                                                                     |
| lpepPickupDatetime   | timestamp | 58,157,349 | 2013-10-22 12:40:36 2014-08-09 15:54:25 | メーターが作動し始めた日時。                                                                                                                                                                                                        |
| mtaTax               | double    | 34         | 0.5 -0.5                                | 使用中のメーター制料金に基づいて自動的にトリガーされる 0.50 ドルの MTA 税。                                                                                                                                                                      |
| passengerCount       | INT       | 10         | 1 2                                     | 乗車人数。 これは運転手が入力した値です。                                                                                                                                                                             |
| paymentType          | INT       | 5          | 2 1                                     | 乗客が乗車料金をどのように支払ったかを示す数値コード。 1= クレジット カード 2= 現金 3= 無料 4= 争議 5= 不明 6= 無効な乗車                                                                                                              |
| pickupLatitude       | double    | 95,110     | 40.721351623535156 40.721336364746094   | 2016 年 7 月以降は非推奨                                                                                                                                                                                                                       |
| pickupLongitude      | double    | 55,722     | -73.84429931640625 -73.84429168701172   | 2016 年 7 月以降は非推奨                                                                                                                                                                                                                       |
| puLocationId         | string    | 264        | 74 41                                   | タクシーメーターが作動し始めた TLC タクシー ゾーン。                                                                                                                                                                                                    |
| puMonth              | INT       | 12         | 3 5                                     |                                                                                                                                                                                                                                                      |
| puYear               | INT       | 14         | 2015 2016                               |                                                                                                                                                                                                                                                      |
| rateCodeID           | INT       | 7          | 1 5                                     | 乗車終了時に適用される最終的な料金コード。 1= 標準料金 2= JFK 3= Newark 4= Nassau または Westchester 5= ネゴシエート料金 6= グループ乗車                                                                                                    |
| storeAndFwdFlag      | string    | 2          | N Y                                     | このフラグは、車両がサーバーに接続されていないため、乗車記録がベンダーに送信される前に車両のメモリに保持されていたどうか ("ストア アンド フォワード" とも呼ばれます) を示します。 Y= ストア アンド フォワード乗車 N= ストア アンド フォワード乗車ではない |
| tipAmount            | double    | 6,206      | 1.0 2.0                                 | チップの金額 - このフィールドは、クレジット カードのチップの場合に自動的に入力されます。 現金のチップは含まれません。                                                                                                                                                 |
| tollsAmount          | double    | 2,150      | 5.54 5.76                               | 乗車中に支払われたすべての通行料金の合計金額。                                                                                                                                                                                                              |
| totalAmount          | double    | 20,188     | 7.8 6.8                                 | 乗客に請求される合計金額。 現金のチップは含まれません。                                                                                                                                                                                  |
| tripDistance         | double    | 7,060      | 0.9 1.0                                 | タクシーメーターによって報告された走行距離 (マイル単位)。                                                                                                                                                                                        |
| tripType             | INT       | 3          | 1 2                                     | 乗車が乗客の呼び止めと配車のどちらであるかを示すコード。配車は、使用中のメーター料金に基づいて自動的に割り当てられますが、運転手が変更できます。 1= 通りで拾う 2= 配車                                                      |
| vendorID             | INT       | 2          | 2 1                                     | レコードを提供した LPEP プロバイダーを示すコード。 1= Creative Mobile Technologies, LLC、2= VeriFone Inc.                                                                                                                                 |

## <a name="preview"></a>プレビュー

| vendorID | lpepPickupDatetime     | lpepDropoffDatetime    | passengerCount | tripDistance | puLocationId | doLocationId | rateCodeID | storeAndFwdFlag | paymentType | fareAmount | extra | mtaTax | improvementSurcharge | tipAmount | tollsAmount | totalAmount | tripType | puYear | puMonth |
|----------|------------------------|------------------------|----------------|--------------|--------------|--------------|------------|-----------------|-------------|------------|-------|--------|----------------------|-----------|-------------|-------------|----------|--------|---------|
| 2        | 2081/6/24 午後 5:40:37   | 2081/6/24 午後 6:42:47   | 1              | 16.95        | 93           | 117          | 1          | N               | 1           | 52         | 1     | 0.5    | 0.3                  | 0         | 2.16        | 55.96       | 1        | 2081   | 6       |
| 2        | 2030/11/28 午前 12:19:29 | 2030/11/28 午前 12:25:37 | 1              | 1.08         | 42           | 247          | 1          | N               | 2           | 6.5        | 0     | 0.5    | 0.3                  | 0         | 0           | 7.3         | 1        | 2030   | 11      |
| 2        | 2030/11/28 午前 12:14:50 | 2030/11/28 午前 12:14:54 | 1              | 0.03         | 42           | 42           | 5          | N               | 2           | 5          | 0     | 0      | 0                    | 0         | 0           | 5           | 2        | 2030   | 11      |
| 2        | 2020/11/14 午前 11:38:07 | 2020/11/14 午前 11:42:22 | 1              | 0.63         | 129          | 129          | 1          | N               | 2           | 4.5        | 1     | 0.5    | 0.3                  | 0         | 0           | 6.3         | 1        | 2020   | 11      |
| 2        | 2020/11/14 午前 9:55:36  | 2020/11/14 午前 10:04:54 | 1              | 3.8          | 82           | 138          | 1          | N               | 2           | 12.5       | 1     | 0.5    | 0.3                  | 0         | 0           | 14.3        | 1        | 2020   | 11      |
| 2        | 2019/8/26 午後 4:18:37   | 2019/8/26 午後 4:19:35   | 1              | 0            | 264          | 264          | 1          | N               | 2           | 1          | 0     | 0.5    | 0.3                  | 0         | 0           | 1.8         | 1        | 2019   | 8       |
| 2        | 2019/7/1 午前 8:28:33    | 2019/7/1 午前 8:32:33    | 1              | 0.71         | 7            | 7            | 1          | N               | 1           | 5          | 0     | 0.5    | 0.3                  | 1.74      | 0           | 7.54        | 1        | 2019   | 7       |
| 2        | 2019/7/1 午前 12:04:53   | 2019/7/1 午前 12:21:56   | 1              | 2.71         | 223          | 145          | 1          | N               | 2           | 13         | 0.5   | 0.5    | 0.3                  | 0         | 0           | 14.3        | 1        | 2019   | 7       |
| 2        | 2019/7/1 午前 12:04:11   | 2019/7/1 午前 12:21:15   | 1              | 3.14         | 166          | 142          | 1          | N               | 2           | 14.5       | 0.5   | 0.5    | 0.3                  | 0         | 0           | 18.55       | 1        | 2019   | 7       |
| 2        | 2019/7/1 午前 12:03:37   | 2019/7/1 午前 12:09:27   | 1              | 0.78         | 74           | 74           | 1          | N               | 1           | 6          | 0.5   | 0.5    | 0.3                  | 1.46      | 0           | 8.76        | 1        | 2019   | 7       |

## <a name="data-access"></a>データ アクセス

### <a name="azure-notebooks"></a>Azure Notebooks

# <a name="azureml-opendatasets"></a>[azureml-opendatasets](#tab/azureml-opendatasets)

```python
# This is a package in preview.
from azureml.opendatasets import NycTlcGreen

from datetime import datetime
from dateutil import parser


end_date = parser.parse('2018-06-06')
start_date = parser.parse('2018-05-01')
nyc_tlc = NycTlcGreen(start_date=start_date, end_date=end_date)
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
folder_name = "green"

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
from azureml.opendatasets import NycTlcGreen

from datetime import datetime
from dateutil import parser


end_date = parser.parse('2018-06-06')
start_date = parser.parse('2018-05-01')
nyc_tlc = NycTlcGreen(start_date=start_date, end_date=end_date)
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
blob_relative_path = "green"
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
from azureml.opendatasets import NycTlcGreen

from datetime import datetime
from dateutil import parser


end_date = parser.parse('2018-06-06')
start_date = parser.parse('2018-05-01')
nyc_tlc = NycTlcGreen(start_date=start_date, end_date=end_date)
nyc_tlc_df = nyc_tlc.to_spark_dataframe()

# Display top 5 rows
display(nyc_tlc_df.limit(5))

# Display data statistic information
display(nyc_tlc_df, summary = True)
```

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

このプラットフォームとパッケージの組み合わせでは、サンプルは利用できません。

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

```python
# Azure storage access info
blob_account_name = "azureopendatastorage"
blob_container_name = "nyctlc"
blob_relative_path = "green"
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