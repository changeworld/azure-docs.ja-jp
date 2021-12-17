---
title: Chicago Safety Data
titleSuffix: Azure Open Datasets
description: Azure Open Datasets で Chicago Safety Data (シカゴ安全データ) データセットを使用する方法について説明します。
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: d39c89308f0c33f98f1c5d074746a2008317472a
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/11/2021
ms.locfileid: "112038871"
---
# <a name="chicago-safety-data"></a>Chicago Safety Data

公衆衛生規則に関連する過去の苦情、道路の穴の報告、街灯の問題など、シカゴ市の 311 サービス要求

311 に寄せられた公衆衛生規則に関連する対応中のすべての苦情と、2011 年 1 月 1 日以降に完了したすべての要求。 道路/公衆衛生局では、シカゴの公衆衛生規則に対する報告された違反を調査し、是正しています。 住民は、ゴミであふれたゴミ箱や路地のゴミなどの違反に対してサービスを要求できます。 311 では、公衆衛生規則に関連する重複する苦情を受けることがあります。 重複としてラベル付けされた要求は、以前の要求と同じ地域で発生し、311 のカスタマー サービス要求 (CSR) システムにほぼ同時に入力されたものです。 重複する苦情は、状態フィールドで "Open - Dup" または "Completed - Dup" としてラベル付けされます。

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

シカゴ運輸局 (CDOT) では、シカゴの 4,000 マイル以上の幹線道路と住宅街の道路にできた穴の補修を監督しています。 CDOT は、311 コール センターを通じて穴の報告を受け取ります。 CDOT は、マッピングおよび追跡システムを使用して、穴の場所を特定し、作業員をスケジュールします。

311 への 1 つのコールで複数の穴の修復を行うことができます。 到着した作業チームは、311 に報告された穴を修復するときに、そのブロック内の他のすべての穴を補修します。 穴の修復は、311 に最初に穴が報告されてから 7 日以内に完了します。 気象条件 (極寒の気温と降雨) が、修復に要する時間に影響します。 天候に恵まれ、雨が降らない日には、複数の作業チームが数千個の穴を補修できます。 

4 つの住所のバッファーに対して以前の要求が既に開かれている場合、要求の状態が "Duplicate (Open)" になります。 たとえば、6535 N Western に対する既存の CSR があり、(元の CSR の 4 つの住所に含まれる) 6531 N Western に対する新しい要求を受け取った場合、その新しい要求の状態は "Duplicate (Open)" になります。 道路が修復されると、CSR の状態は、元の要求では "Completed" になり、重複する要求では "Duplicate (Closed)" になります。 報告された住所を調査しても穴が見つからないか、既に補修されている場合にも、サービス要求の状態が "Completed" になります。 "陥没" や "ユーティリティの切断の失敗" など、道路で別の問題が見つかった場合は、適切な部門または請負業者に送られます。

311 に寄せられた "Street Lights - All Out" (3 つ以上の街灯の故障) に関する対応中のすべての報告と、2011 年 1 月 1 日以降に完了したすべての要求。 シカゴ運輸局 (CDOT) では、シカゴの幹線道路と住宅街を照らす約 250,000 個の街灯を管理しています。 CDOT では、街灯の故障に関する住民の報告に対し、修理と電球交換を行っています。 CDOT が "All Out" の報告を受けるたびに、修理に割り当てられた電気技師がその回路の街灯 (回路ごとに 8 から 16 個の街灯があります) を調べて、正常に機能していることを確認します。 元の要求から 4 暦日以内に、同じ回路の街灯の故障に関する別の要求が行われた場合、その最新の要求の状態が自動的に "Duplicate (Open)" になります。 CDOT の電気技師は回路内の街灯を調べて、機能していることを確認するため、"Duplicate (Open)" のすべての住所が自動的に点検され、修理されることになります。 街灯が修理されると、CSR の状態は、元の要求では "Completed" になり、重複する要求では "Duplicate (Closed)" になります。 報告された街灯を調査したときに、街灯が適切に修理され、機能していることがわかった場合、サービス要求が存在しない住所に対するものである場合、または請負業者によって街灯のメンテナンスが行われている場合にも、サービス要求の状態が "Completed" になります。 データは毎日更新されます。

## <a name="volume-and-retention"></a>ボリュームとデータ保持期間

このデータセットは Parquet 形式で保存されています。 毎日更新されており、2018 年時点で合計約 100 万行 (80 MB) が含まれています。

このデータセットには、2011 年から 2018 年までに蓄積された過去の記録が含まれます。 SDK でパラメーター設定を使用して、特定の時間範囲内のデータをフェッチできます。

## <a name="storage-location"></a>保存先

このデータセットは、米国東部 Azure リージョンに保存されています。 アフィニティのために、米国東部でコンピューティング リソースを割り当てることをお勧めします。

## <a name="related-datasets"></a>関連データセット

- [シカゴのゴミ処理](https://data.cityofchicago.org/Service-Requests/311-Service-Requests-Sanitation-Code-Complaints-Hi/me59-5fac)
- [シカゴの穴](https://data.cityofchicago.org/Service-Requests/311-Service-Requests-Pot-Holes-Reported-Historical/7as2-ds3y)
- [シカゴの街灯](https://data.cityofchicago.org/Service-Requests/311-Service-Requests-Street-Lights-All-Out-Histori/zuxi-7xem)

## <a name="additional-information"></a>関連情報

このデータセットはシカゴ市政府から提供されています。

このデータセットの使用条件については、こちらをご覧ください。 データ ソースについてご不明な点があれば、dataportal@cityofchicago.org までメールでお問い合わせください。

## <a name="columns"></a>[列]

| Name | データ型 | 一意 | 値 (サンプル) | 説明 |
|-|-|-|-|-|
| address | string | 140,612 | \" \" 1 City Hall Plz Boston MA 02108 | 場所。 |
| category | string | 54 | Street Cleaning Sanitation | サービス要求の理由。 |
| dataSubtype | string | 1 | 311_All | "311_All" |
| dataType | string | 1 | 安全性 | "Safety" |
| dateTime | timestamp | 1,529,075 | 2015-07-23 10:51:00 2015-07-23 10:47:00 | サービス要求を開いた日時。 |
| 緯度 (latitude) | double | 1,622 | 42.3594 42.3603 | これは緯度値です。 緯線は赤道に平行です。 |
| 経度 (longitude) | double | 1,806 | -71.0587 -71.0583 | これは経度値です。 経線は緯線に垂直に走り、すべて両極を通ります。 |
| ソース | string | 7 | Constituent Call Citizens Connect App | ケースの元のソース。 |
| status | string | 2 | Closed Open | ケースの状態。 |
| subcategory | string | 209 | Parking Enforcement Requests for Street Cleaning | サービス要求の種類。 |

## <a name="preview"></a>プレビュー

| dataType | dataSubtype | dateTime | category | subcategory | status | address | 緯度 (latitude) | 経度 (longitude) | source | extendedProperties |
|-|-|-|-|-|-|-|-|-|-|-|
| 安全性 | 311_All | 4/25/2021 11:55:04 PM | Street Light Out Complaint | null | [ファイル] | 4800 W WASHINGTON BLVD | 41.882148426 | -87.74556256 | null |  |
| 安全性 | 311_All | 4/25/2021 11:54:31 PM | 311 INFORMATION ONLY CALL | null | 完了 | 2111 W Lexington ST |  |  | null |  |
| 安全性 | 311_All | 4/25/2021 11:52:11 PM | 311 INFORMATION ONLY CALL | null | 完了 | 2111 W Lexington ST |  |  | null |  |
| 安全性 | 311_All | 4/25/2021 11:49:56 PM | 311 INFORMATION ONLY CALL | null | 完了 | 2111 W Lexington ST |  |  | null |  |
| 安全性 | 311_All | 4/25/2021 11:48:53 PM | Garbage Cart Maintenance | null | [ファイル] | 3409 E 106TH ST | 41.702545562 | -87.540917602 | null |  |
| 安全性 | 311_All | 4/25/2021 11:46:01 PM | 311 INFORMATION ONLY CALL | null | 完了 | 2111 W Lexington ST |  |  | null |  |
| 安全性 | 311_All | 4/25/2021 11:45:46 PM | Aircraft Noise Complaint | null | 完了 | 10510 W ZEMKE RD |  |  | null |  |
| 安全性 | 311_All | 4/25/2021 11:45:02 PM | 311 INFORMATION ONLY CALL | null | 完了 | 2111 W Lexington ST |  |  | null |  |
| 安全性 | 311_All | 4/25/2021 11:44:24 PM | Sewer Cave-In Inspection Request | null | [ファイル] | 7246 W THORNDALE AVE | 41.987984339 | -87.808702917 | null |  |

## <a name="data-access"></a>データ アクセス

### <a name="azure-notebooks"></a>Azure Notebooks

# <a name="azureml-opendatasets"></a>[azureml-opendatasets](#tab/azureml-opendatasets)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=city_safety_chicago -->

> [!TIP]
> **[代わりにノートブックをダウンロードします](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=city_safety_chicago)** 。

```
# This is a package in preview.
from azureml.opendatasets import ChicagoSafety

from datetime import datetime
from dateutil import parser


end_date = parser.parse('2016-01-01')
start_date = parser.parse('2015-05-01')
safety = ChicagoSafety(start_date=start_date, end_date=end_date)
safety = safety.to_pandas_dataframe()
```

```
safety.info()
```

<!-- nbend -->

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=city_safety_chicago -->

> [!TIP]
> **[代わりにノートブックをダウンロードします](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=city_safety_chicago)** 。

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
folder_name = "Safety/Release/city=Chicago"
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

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=azureml-opendatasets&registryId=city_safety_chicago -->

> [!TIP]
> **[代わりにノートブックをダウンロードします](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=azureml-opendatasets&registryId=city_safety_chicago)** 。

```
# This is a package in preview.
# You need to pip install azureml-opendatasets in Databricks cluster. https://docs.microsoft.com/en-us/azure/data-explorer/connect-from-databricks#install-the-python-library-on-your-azure-databricks-cluster
from azureml.opendatasets import ChicagoSafety

from datetime import datetime
from dateutil import parser


end_date = parser.parse('2016-01-01')
start_date = parser.parse('2015-05-01')
safety = ChicagoSafety(start_date=start_date, end_date=end_date)
safety = safety.to_spark_dataframe()
```

```
display(safety.limit(5))
```

<!-- nbend -->

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

このプラットフォームとパッケージの組み合わせでは、サンプルは利用できません。

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=city_safety_chicago -->

> [!TIP]
> **[代わりにノートブックをダウンロードします](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=city_safety_chicago)** 。

```python
# Azure storage access info
blob_account_name = "azureopendatastorage"
blob_container_name = "citydatacontainer"
blob_relative_path = "Safety/Release/city=Chicago"
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

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=azureml-opendatasets&registryId=city_safety_chicago -->

> [!TIP]
> **[代わりにノートブックをダウンロードします](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=azureml-opendatasets&registryId=city_safety_chicago)** 。

```python
# This is a package in preview.
from azureml.opendatasets import ChicagoSafety

from datetime import datetime
from dateutil import parser

end_date = parser.parse('2016-01-01')
start_date = parser.parse('2015-05-01')
safety = ChicagoSafety(start_date=start_date, end_date=end_date)
safety = safety.to_spark_dataframe()
```

```python
# Display top 5 rows
display(safety.limit(5))
```

```python
# Display data statistic information
display(safety, summary = True)
```

<!-- nbend -->

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

このプラットフォームとパッケージの組み合わせでは、サンプルは利用できません。

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=city_safety_chicago -->

> [!TIP]
> **[代わりにノートブックをダウンロードします](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=city_safety_chicago)** 。

```python
# Azure storage access info
blob_account_name = "azureopendatastorage"
blob_container_name = "citydatacontainer"
blob_relative_path = "Safety/Release/city=Chicago"
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