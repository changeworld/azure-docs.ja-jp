---
title: San Francisco Safety Data
titleSuffix: Azure Open Datasets
description: Azure Open Datasets で Francisco Safety データセットを使用する方法について説明します。
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: 4502d51054f43b7b1d876443c12f67418ba84060
ms.sourcegitcommit: 7c44970b9caf9d26ab8174c75480f5b09ae7c3d7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/27/2021
ms.locfileid: "112982458"
---
# <a name="san-francisco-safety-data"></a>San Francisco Safety Data

サンフランシスコの消防局への出動要請と 311 ケース。

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

Fire Calls-For-Service には、出動要請に対する消防隊のすべての応答が含まれています。 各記録には、要請番号、インシデント番号、住所、部隊識別子、要請の種類、措置が含まれます。 また、関連する時間間隔もすべて含まれます。 このデータセットは応答に基づいており、ほとんどの要請に複数の部隊が関与するため、要請番号ごとに複数の記録があります。 住所は、特定の住所ではなく、ブロック番号、交差点、または非常用電話に関連付けられています。

311 ケースには、場所または物 (公園、通り、建物など) に関連し、2008 年 7 月 1 日以降に作成されたケースが含まれます。 ユーザーが独自のニーズについてログするケースは除外されます。 たとえば、財産税または事業税に関する質問、駐車許可証の要求などです。 詳細については、[プログラム リンク](https://support.datasf.org/help/311-case-data-faq)に関するページを参照してください。
 
## <a name="volume-and-retention"></a>ボリュームとデータ保持期間

このデータセットは Parquet 形式で保存されています。 毎日更新されていて、2019 年時点で約 600 万行 (400 MB) が含まれています。

このデータセットには、2015 年から現在までに蓄積された過去の記録が含まれます。 SDK でパラメーター設定を使用して、特定の時間範囲内のデータをフェッチできます。

## <a name="storage-location"></a>保存先

このデータセットは、米国東部 Azure リージョンに保存されています。 アフィニティのために、米国東部でコンピューティング リソースを割り当てることをお勧めします。

## <a name="related-datasets"></a>関連データセット

- [消防署への通報](https://data.sfgov.org/Public-Safety/Fire-Department-Calls-for-Service/nuek-vuh3)
- [311 ケース](https://data.sfgov.org/City-Infrastructure/311-Cases/vw6y-z8j6)

## <a name="additional-information"></a>関連情報

このデータセットはサンフランシスコ市政府から提供されています。 [このデータセットの使用条件](https://datasf.org/opendata/terms-of-use/)を参照してください。

## <a name="columns"></a>[列]

| Name | データ型 | 一意 | 値 (サンプル) | 説明 |
|-|-|-|-|-|
| address | string | 280,652 | 特定のアドレスに関連付けられていません (6TH ST の 0 ブロック) | インシデントの住所 (注: 通報者のプライバシーを保護するために、住所と場所は通りの中間ブロック、交差点、または最寄りの非常用電話の場所に一般化されています)。 |
| category | string | 108 | 生命に関わるおそれのある通りや歩道の清掃 | 311 サービス要求の種類または 911 出動要請の通報の種類グループの人間が判読できる名前。 |
| dataSubtype | string | 2 | 911_Fire 311_All | "911_Fire" または "311_All"。 |
| dataType | string | 1 | 安全性 | "Safety" |
| dateTime | timestamp | 6,496,563 | 2020-10-19 12:28:08 2020-07-28 06:40:26 | サービス要求が作成された日時または出動要請を受けた日時。 |
| 緯度 (latitude) | double | 1,615,369 | 37.777624238929 37.786117211838 | WGS84 投影法を使用した、その場所の緯度。 |
| 経度 (longitude) | double | 1,554,612 | -122.39998111124 -122.419854245692 | WGS84 投影法を使用した、その場所の経度。 |
| ソース | string | 9 | 電話 Mobile/Open311 | サービス要求を受けたときに使用されたメカニズムまたはパス。通常は、"電話"、"テキスト/SMS"、"Web サイト"、"モバイル アプリ"、"Twitter" などですが、用語はシステムによって異なることがあります。 |
| status | string | 3 | Closed Open | サービス要求の現在の状態を一語で示すインジケーター。 (注: GeoReport V2 で使用できるのは "open" と "closed" のみ) |
| subcategory | string | 1,270 | 医療インシデント一括項目 | 311 ケースのサービス要求のサブタイプまたは 911 出動要請の通報の種類の人間が判読できる名前。 |

## <a name="preview"></a>プレビュー

| dataType | dataSubtype | dateTime | category | subcategory | status | address | 緯度 (latitude) | 経度 (longitude) | source | extendedProperties |
|-|-|-|-|-|-|-|-|-|-|-|
| 安全性 | 911_Fire | 2021/4/26 午前 2:56:13 | 生命に関わらない | 医療インシデント | null | GEARY ST の 700 ブロック | 37.7863607914647 | -122.415616900246 | null |  |
| 安全性 | 911_Fire | 2021/4/26 午前 2:56:13 | 生命に関わらない | 医療インシデント | null | GEARY ST の 700 ブロック | 37.7863607914647 | -122.415616900246 | null |  |
| 安全性 | 911_Fire | 2021/4/26 午前 2:54:03 | 生命に関わらない | 医療インシデント | null | ESSEX ST の 0 ブロック | 37.7860048266229 | -122.395077258809 | null |  |
| 安全性 | 911_Fire | 2021/4/26 午前 2:54:03 | 生命に関わらない | 医療インシデント | null | ESSEX ST の 0 ブロック | 37.7860048266229 | -122.395077258809 | null |  |
| 安全性 | 911_Fire | 2021/4/26 午前 2:52:17 | 生命に関わらない | 医療インシデント | null | 29TH AVE の 700 ブロック | 37.7751770865322 | -122.488604397217 | null |  |
| 安全性 | 911_Fire | 2021/4/26 午前 2:50:28 | 生命に関わるおそれがある | 医療インシデント | null | GEARY ST の 1000 ブロック | 37.7857350982044 | -122.420555240691 | null |  |
| 安全性 | 911_Fire | 2021/4/26 午前 2:50:28 | 生命に関わるおそれがある | 医療インシデント | null | GEARY ST の 1000 ブロック | 37.7857350982044 | -122.420555240691 | null |  |
| 安全性 | 911_Fire | 2021/4/26 午前 2:33:52 | 生命に関わらない | 医療インシデント | null | BELVEDERE ST の 100 ブロック | 37.767791696654 | -122.449332294394 | null |  |
| 安全性 | 911_Fire | 2021/4/26 午前 2:33:52 | 生命に関わらない | 医療インシデント | null | BELVEDERE ST の 100 ブロック | 37.767791696654 | -122.449332294394 | null |  |
| 安全性 | 911_Fire | 2021/4/26 午前 2:33:51 | 生命に関わるおそれがある | 医療インシデント | null | 6TH ST の 100 ブロック | 37.7807920802756 | -122.408385745499 | null |  |

## <a name="data-access"></a>データ アクセス

### <a name="azure-notebooks"></a>Azure Notebooks

# <a name="azureml-opendatasets"></a>[azureml-opendatasets](#tab/azureml-opendatasets)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=city_safety_newyork -->

> [!TIP]
> **[代わりにノートブックをダウンロードします](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=city_safety_newyork)** 。

```
# This is a package in preview.
from azureml.opendatasets import NycSafety

from datetime import datetime
from dateutil import parser


end_date = parser.parse('2016-01-01')
start_date = parser.parse('2015-05-01')
safety = NycSafety(start_date=start_date, end_date=end_date)
safety = safety.to_pandas_dataframe()
```

```
safety.info()
```

<!-- nbend -->

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=city_safety_newyork -->

> [!TIP]
> **[代わりにノートブックをダウンロードします](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=city_safety_newyork)** 。

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
folder_name = "Safety/Release/city=NewYorkCity"
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

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=azureml-opendatasets&registryId=city_safety_newyork -->

> [!TIP]
> **[代わりにノートブックをダウンロードします](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=azureml-opendatasets&registryId=city_safety_newyork)** 。

```
# This is a package in preview.
# You need to pip install azureml-opendatasets in Databricks cluster. https://docs.microsoft.com/en-us/azure/data-explorer/connect-from-databricks#install-the-python-library-on-your-azure-databricks-cluster
from azureml.opendatasets import NycSafety

from datetime import datetime
from dateutil import parser


end_date = parser.parse('2016-01-01')
start_date = parser.parse('2015-05-01')
safety = NycSafety(start_date=start_date, end_date=end_date)
safety = safety.to_spark_dataframe()
```

```
display(safety.limit(5))
```

<!-- nbend -->

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

このプラットフォームとパッケージの組み合わせでは、サンプルは利用できません。

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=city_safety_newyork -->

> [!TIP]
> **[代わりにノートブックをダウンロードします](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=city_safety_newyork)** 。

```python
# Azure storage access info
blob_account_name = "azureopendatastorage"
blob_container_name = "citydatacontainer"
blob_relative_path = "Safety/Release/city=NewYorkCity"
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

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=azureml-opendatasets&registryId=city_safety_newyork -->

> [!TIP]
> **[代わりにノートブックをダウンロードします](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=azureml-opendatasets&registryId=city_safety_newyork)** 。

```python
# This is a package in preview.
from azureml.opendatasets import NycSafety

from datetime import datetime
from dateutil import parser

end_date = parser.parse('2016-01-01')
start_date = parser.parse('2015-05-01')
safety = NycSafety(start_date=start_date, end_date=end_date)
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

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=city_safety_newyork -->

> [!TIP]
> **[代わりにノートブックをダウンロードします](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=city_safety_newyork)** 。

```python
# Azure storage access info
blob_account_name = "azureopendatastorage"
blob_container_name = "citydatacontainer"
blob_relative_path = "Safety/Release/city=NewYorkCity"
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
