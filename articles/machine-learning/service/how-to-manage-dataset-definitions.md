---
title: データセット定義とバージョン管理
titleSuffix: Azure Machine Learning service
description: データセット定義を更新したり、定義のライフサイクルを管理したりする方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.reviewer: larryfr
ms.date: 05/02/2019
ms.openlocfilehash: 7c861c8cdc9985caa42bd2beb5236a4f4e93e4c7
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/02/2019
ms.locfileid: "65027709"
---
# <a name="update-and-manage-the-lifecycle-of-dataset-definitions"></a>データセット定義のライフサイクルを更新、管理する

Azure Machine Learning Datasets (プレビュー) を使用してデータセット定義を更新したり管理したりする方法について説明します。

データセット定義は、必要なデータセットを準備するためのレシピであり、ソース データの参照と実行する変換ステップの両方を含んでいます。 1 つのデータセットに対して多数の定義が存在する場合もあり、また、それぞれの定義には固有のライフサイクルが存在します。

次のシナリオに、データセット定義の使用例を示します。

1. データセットの最新の定義を使用する機械学習パイプラインを作成する。
1. 基になるデータが変わり、新しい属性が追加された。
1. 新しい属性を処理するための特別な変換を追加した新しいバージョンの定義を作成する。

この例では、既存のパイプラインでそのまま元のバージョンの定義を使用します。 モデルのトレーニング、パイプラインの作成など、新しいシナリオには、新しいバージョンの定義を使用できます。

## <a name="prerequisites"></a>前提条件

データセット定義のライフサイクルを管理するためには、データセットを登録するためのワークスペースと Azure サブスクリプションが必要です。

このドキュメントの例で使用しているサンプル ファイルは、[https://dprepdata.blob.core.windows.net/dataset-sample-files/crime.csv](https://dprepdata.blob.core.windows.net/dataset-sample-files/crime.csv) から入手できます。

## <a name="update-dataset-definitions"></a>データセット定義を更新する

まず、データセットを作成してワークスペースに登録します。

```python
from azureml.core import Workspace, Datastore, Dataset

# change the configuration for workspace and Datastore
subscription_id = 'your subscription id'
resource_group = 'your resource group name'
workspace_name = 'your workspace name'
datastore_name = 'your datastore name'

# get existing workspace
workspace = Workspace(subscription_id, resource_group, workspace_name)

# get a Datastore that has already been created in the workspace
dstore = Datastore.get(workspace, datastore_name)

# create an in-memory Dataset from Datastore
datapath = dstore.path('data/src/crime.csv')
dataset = Dataset.from_delimited_files(datapath)

# register the Dataset with the workspace. if a Dataset with the same name already exists, retrieve it by turning `exist_ok = True`
dataset_name = 'crime dataset'
dataset = dataset.register(workspace = workspace, 
                 name = dataset_name, 
                 description = 'crime dataset for demo', 
                 tags = {'year':'2019', 'month':'Apr'},
                 exist_ok = True)
```

最初のデータセット定義 (`version_id` = 1) は、データセットの作成時に作成されます。 その後、データセット定義を更新するたびに、新しい version_id が最新の定義に割り当てられます。

```python
# get the Dataset from the workspace by name
dataset = workspace.datasets['dataset_name']

# get the latest Dataset definition
ds_def = dataset.get_definition()

# update the Dataset definition to select only the columns I'm interested in
ds_def = ds_def.keep_columns(['ID', 'Arrest', 'Latitude', 'Longitude'])

# with this update, the latest definition for dataset now has `version_id = 2`
dataset = dataset.update_definition(ds_def, 'select useful column')

dataset = workspace.datasets['dataset_name']
dataset.head(5)
```
<div>
<style scoped> .dataframe tbody tr th:only-of-type { vertical-align: middle; }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>ID</th>
      <th>逮捕</th>
      <th>Latitude</th>
      <th>Longitude</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>10498554</td>
      <td>FALSE</td>
      <td>41.69283384</td>
      <td>-87.60431945</td>
    </tr>
    <tr>
      <th>1</th>
      <td>10516598</td>
      <td>FALSE</td>
      <td>41.74410697</td>
      <td>-87.66449429</td>
    </tr>
    <tr>
      <th>2</th>
      <td>10519196</td>
      <td>FALSE</td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>3</th>
      <td>10519591</td>
      <td>FALSE</td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>4</th>
      <td>10534446</td>
      <td>FALSE</td>
      <td></td>
      <td></td>
    </tr>
  </tbody>
</table>
</div>

このように定義を更新しても以前のデータセット定義が消去されることはありません。 引き続き以前の定義にアクセスして使用することができます。

```python
# specify `version_id` to get a previous definition 
ds_def_old = dataset.get_definition(version_id = 1)
ds_def_old.head(5)
```
<div>
<style scoped> .dataframe tbody tr th:only-of-type { vertical-align: middle; }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>ID</th>
      <th>事件番号</th>
      <th>Date</th>
      <th>ブロック</th>
      <th>IUCR</th>
      <th>プライマリ タイプ</th>
      <th>説明</th>
      <th>場所の説明</th>
      <th>逮捕</th>
      <th>国内</th>
      <th>...</th>
      <th>区</th>
      <th>コミュニティ エリア</th>
      <th>FBI コード</th>
      <th>X 座標</th>
      <th>Y 座標</th>
      <th>年</th>
      <th>更新日</th>
      <th>Latitude</th>
      <th>Longitude</th>
      <th>Location</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>10498554</td>
      <td>HZ239907</td>
      <td>4/4/2016 23:56</td>
      <td>007XX E 111TH ST</td>
      <td>1153</td>
      <td>DECEPTIVE PRACTICE</td>
      <td>FINANCIAL IDENTITY THEFT OVER $ 300</td>
      <td>OTHER</td>
      <td>FALSE</td>
      <td>FALSE</td>
      <td>...</td>
      <td>9</td>
      <td>50</td>
      <td>11</td>
      <td>1183356</td>
      <td>1831503</td>
      <td>2016</td>
      <td>5/11/2016 15:48</td>
      <td>41.69283384</td>
      <td>-87.60431945</td>
      <td>(41.692833841, -87.60431945)</td>
    </tr>
    <tr>
      <th>1</th>
      <td>10516598</td>
      <td>HZ258664</td>
      <td>4/15/2016 17:00</td>
      <td>082XX S MARSHFIELD AVE</td>
      <td>890</td>
      <td>THEFT</td>
      <td>FROM BUILDING</td>
      <td>RESIDENCE</td>
      <td>FALSE</td>
      <td>FALSE</td>
      <td>...</td>
      <td>21</td>
      <td>71</td>
      <td>6</td>
      <td>1166776</td>
      <td>1850053</td>
      <td>2016</td>
      <td>5/12/2016 15:48</td>
      <td>41.74410697</td>
      <td>-87.66449429</td>
      <td>(41.744106973, -87.664494285)</td>
    </tr>
    <tr>
      <th>2</th>
      <td>10519196</td>
      <td>HZ261252</td>
      <td>4/15/2016 10:00</td>
      <td>104XX S SACRAMENTO AVE</td>
      <td>1154</td>
      <td>DECEPTIVE PRACTICE</td>
      <td>FINANCIAL IDENTITY THEFT $300 AND UNDER</td>
      <td>RESIDENCE</td>
      <td>FALSE</td>
      <td>FALSE</td>
      <td>...</td>
      <td>19</td>
      <td>74</td>
      <td>11</td>
      <td></td>
      <td></td>
      <td>2016</td>
      <td>5/12/2016 15:50</td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>3</th>
      <td>10519591</td>
      <td>HZ261534</td>
      <td>4/15/2016 9:00</td>
      <td>113XX S PRAIRIE AVE</td>
      <td>1120</td>
      <td>DECEPTIVE PRACTICE</td>
      <td>FORGERY</td>
      <td>RESIDENCE</td>
      <td>FALSE</td>
      <td>FALSE</td>
      <td>...</td>
      <td>9</td>
      <td>49</td>
      <td>10</td>
      <td></td>
      <td></td>
      <td>2016</td>
      <td>5/13/2016 15:51</td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>4</th>
      <td>10534446</td>
      <td>HZ277630</td>
      <td>4/15/2016 10:00</td>
      <td>055XX N KEDZIE AVE</td>
      <td>890</td>
      <td>THEFT</td>
      <td>FROM BUILDING</td>
      <td>SCHOOL, PUBLIC, BUILDING</td>
      <td>FALSE</td>
      <td>FALSE</td>
      <td>...</td>
      <td>40</td>
      <td>13</td>
      <td>6</td>
      <td></td>
      <td></td>
      <td>2016</td>
      <td>5/25/2016 15:59</td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
  </tbody>
</table>
</div>

この結果からわかるように、最初のバージョンのデータセット定義には、直近の更新にもかかわらず、依然として犯罪データの列がすべて保持されています。 たとえば現在、最初のバージョンのデータセットを使用する機械学習モデルがあって、そのデータセットの `Date` 列を特徴の 1 つとして使用している場合でも、直近に行った定義の更新 (犯罪データから `ID`、`Arrest`、`Latitude`、`Longitude` 列だけを選択) によってエラーが生じることはありません。

## <a name="how-to-access-dataset-definitions"></a>データセット定義にアクセスする方法

すべての定義のリストを取得するには、`get_definitions()` を使用します。 特定のバージョンの定義を取得するには、`get_definition()` を使用します。 次の例では、すべての定義のリストを取得したうえで、バージョン 1 を取得しています。

```python
# list all definitions for the dataset
dataset.get_definitions()
# get version ID 1
dataset.get_definition(version_id=1)
```

`get_definitions()` の出力は、次の例のようになります。

```text
{'2': VersionID: 2, State: active, Created: 2019-04-19 16:43:52.439890+00:00, Modified: 2019-04-19 16:43:52.439890+00:00, Notes: select useful column,
 '1': VersionID: 1, State: active, Created: 2019-04-19 16:39:14.112046+00:00, Modified: 2019-04-19 16:44:12.912663+00:00, Notes: None}
```

取得した定義は、機械学習モデルまたは機械学習パイプラインで使用することができます。

## <a name="manage-lifecycle-of-dataset-definitions"></a>データセット定義のライフサイクルを管理する

データセット定義のライフサイクルは、それぞれ別々に管理することができます。 ライフサイクルには、アクティブ、非推奨、アーカイブ済みの 3 つのステージがあります。

### <a name="active"></a>アクティブ

新しく作成したデータセット定義は、既定でアクティブになります。 

### <a name="deprecate"></a>非推奨

データセット定義の使用が推奨されなくなり、代わりとなる定義が利用できるようになったら、そのデータセット定義を非推奨化できます。 非推奨化されたデータセット定義を機械学習パイプラインで使用すると、警告メッセージが返されますが、実行は拒否されません。

データセット定義を非推奨化するときは、代わりとなる定義のデータセット ID とデータセット定義のバージョン ID を指定します。 この情報は保存され、非推奨のデータセット定義を使おうとしたときの警告メッセージに使用されます。

```python
# get the definition that you want to deprecate
ds_def = dataset.get_definition(version_id = 1)

# deprecate it by providing the information for the replacement definition, which is recommended to be used in machine learning scenarios
ds_def.deprecate(deprecate_by_dataset_id=dataset.id, deprecated_by_definition_version=2)
```

### <a name="archive"></a>アーカイブ

基になるデータが利用できなくなったなど、何かの理由で使用しなくなったデータセット定義はアーカイブすることができます。 アーカイブしたデータセット定義を機械学習パイプラインで使用すると、エラーが発生して実行が拒否されます。

```python
# archive the definition with `version_id = 1`
ds_def = dataset.get_definition(version_id = 1)
ds_def.archive()
```

### <a name="reactivate"></a>再度有効にする

非推奨化またはアーカイブしたデータセット定義は、簡単に再度有効にすることができます。

```python
# reactivate Dataset definition with `version_id =1` which was archived in the previous step
ds_def = dataset.get_definition(version_id = 1)
ds_def.reactivate()
```

## <a name="next-steps"></a>次の手順

データセットを使った作業の詳細については、[Azure Machine Learning Datasets の作成と登録](how-to-create-register-datasets.md)に関するページを参照してください。

データセットの使用例については、[サンプル ノートブック](https://aka.ms/dataset-tutorial)を参照してください。