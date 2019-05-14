---
title: データセットを作成してワークスペースに登録する
titleSuffix: Azure Machine Learning service
description: さまざまなソースからデータセットを作成し、ワークスペースにデータセットを登録する方法に関する説明
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 05/02/19
ms.openlocfilehash: 938f13524b22f34f4becc936885d1611cb854df1
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/09/2019
ms.locfileid: "65510506"
---
# <a name="create-and-register-azure-machine-learning-datasets-preview"></a>Azure Machine Learning Datasets (プレビュー) を作成して登録する

この記事では、データセットを作成して登録するための Azure Machine Learning ワークフローと、ローカルとリモートの実験で再利用するためのデータセットへのアクセス方法について説明します。

Azure Machine Learning Datasets (プレビュー) は、データへのアクセスと操作を容易にします。 データセットは、モデルのトレーニングやパイプラインの作成など、さまざまなシナリオでデータを管理します。 [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) を使用すると、一般的なフォーマットのデータの操作、基礎となるストレージへのアクセス、データの探索と準備、異なるデータセット定義のライフ サイクルの管理、トレーニングと運用環境で使用されるデータセット間の比較が可能になります。

## <a name="prerequisites"></a>前提条件

データセットを作成して登録するには、以下が必要です。

* Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、開始する前に無料アカウントを作成してください。 [無料版または有料版の Azure Machine Learning service](https://aka.ms/AMLFree) を今日からお試しいただけます。

* Azure Machine Learning ワークスペース。 「[Create an Azure Machine Learning service workspace](https://docs.microsoft.com/azure/machine-learning/service/setup-create-workspace)」(Azure Machine Learning service ワークスペースを作成する) を参照してください。

* Azure Machine Learning SDK for Python。 SDK の最新バージョンのインストールまたは最新バージョンへの更新を行うには、[SDK のインストールまたは更新](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)に関する記事を参照してください。

## <a name="create-datasets-from-local-files"></a>ローカル ファイルからデータセットを作成する

`Dataset` クラスの [`auto_read_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#auto-read-files-path--include-path-false-) メソッドを使用してファイルまたはフォルダーのパスを指定し、ローカル コンピューターからファイルを読み込みます。  このメソッドは、ファイルの種類の指定や引数の解析を必要とせずに、次の手順を実行します。

* 区切り記号の推測と設定。
* ファイルの先頭にある空のレコードのスキップ。
* ヘッダー行の推測と設定。
* 列のデータ型の推測と変換。

```Python
from azureml.core.dataset import Dataset

dataset = Dataset.auto_read_files('./data/crime.csv')
```

または、ファイル固有の関数を使用して、ファイルの解析を明示的に制御します。 現在、Datasets SDK は、[区切り](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-delimited-files-path--separator------header--promoteheadersbehavior-allfileshavesameheaders--3---encoding--fileencoding-utf8--0---quoting-false--infer-column-types-true--skip-rows-0--skip-mode--skiplinesbehavior-norows--0---comment-none--include-path-false--archive-options-none-)、[Excel](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-excel-files-path--sheet-name-none--use-column-headers-false--skip-rows-0--include-path-false--infer-column-types-true-)、[Parquet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-parquet-files-path--include-path-false-)、[バイナリ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-binary-files-path-)、[json](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-json-files-path--encoding--fileencoding-utf8--0---flatten-nested-arrays-false--include-path-false-) の各ファイル形式をサポートしています。

## <a name="create-datasets-from-azure-datastores"></a>Azure データストアからデータセットを作成する

Azure データストアからデータセットを作成するには、以下のことを確認してください。

* 登録された Azure データストアへの共同作成者または所有者アクセス権を持っていることを確認します。

* SDK から [`Workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py)、[`Datastore`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#definition)、`Dataset` の各パッケージをインポートします。

```Python
from azureml.core.workspace import Workspace
from azureml.core.datastore import Datastore
from azureml.core.dataset import Dataset

datastore_name = 'your datastore name'

# get existing workspace
workspace = Workspace.from_config()
```

 `get()` メソッドは、ワークスペース内の既存のデータストアを取得します。

```
dstore = Datastore.get(workspace, datastore_name)
```

`from_delimited_files()` メソッドを使用して、区切りファイルを読み取り、未登録のデータセットを作成します。

```Python
# create an in-memory Dataset on your local machine
datapath = dstore.path('data/src/crime.csv')
dataset = Dataset.from_delimited_files(datapath)

# returns the first 5 rows of the Dataset as a pandas Dataframe.
dataset.head(5)
```

||ID|事件番号|Date|ブロック|IUCR|プライマリ タイプ|説明|場所の説明|逮捕|国内|...|区|コミュニティ エリア|FBI コード|X 座標|Y 座標|年|更新日|Latitude|Longitude|Location|
|--|--|---|---|---|---|----|------|-------|------|-----|---|----|----|-----|-----|------|----|-----|----|----|-----
|0|10498554|HZ239907|4/4/2016 23:56|007XX E 111TH ST|1153|DECEPTIVE PRACTICE|FINANCIAL IDENTITY THEFT OVER $ 300|OTHER|FALSE|FALSE|...|9|50|11|1183356|1831503|2016|5/11/2016 15:48|41.69283384|-87.60431945|(41.692833841, -87.60431945)|
1|10516598|HZ258664|4/15/2016 17:00|082XX S MARSHFIELD AVE|890|THEFT| FROM BUILDING|RESIDENCE|FALSE|FALSE|...|21|71|6|1166776|1850053|2016|5/12/2016 15:48|41.74410697|-87.66449429|(41.744106973, -87.664494285)
2|10519196|HZ261252|4/15/2016 10:00|104XX S SACRAMENTO AVE|1154|DECEPTIVE PRACTICE|FINANCIAL IDENTITY THEFT $300 AND UNDER|RESIDENCE|FALSE|FALSE|...|19|74|11|||2016|5/12/2016 15:50
3|10519591|HZ261534|4/15/2016 9:00|113XX S PRAIRIE AVE|1120|DECEPTIVE PRACTICE|FORGERY|RESIDENCE|FALSE|FALSE|...|9|49|10|||2016|5/13/2016 15:51
4|10534446|HZ277630|4/15/2016 10:00|055XX N KEDZIE AVE|890|THEFT|FROM BUILDING|SCHOOL, PUBLIC, BUILDING|FALSE|FALSE|...|40|13|6|||2016|5/25/2016 15:59|

## <a name="register-your-datasets-with-workspace"></a>データセットをワークスペースに登録する

組織内およびさまざまな実験で共有および再利用するために、[`register()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#register-workspace--name--description-none--tags-none--visible-true--exist-ok-false--update-if-exist-false-) メソッドを使用してデータセットをワークスペースに登録します。

```Python
dataset = dataset.register(workspace = workspace,
                           name = 'dataset_crime',
                           description = 'Training data',
                           exist_ok = False
                           )
```

>[!NOTE]
> `register()` の既定のパラメーター設定は `exist_ok = False` です。 この設定を変更せずに同じ名前でデータセットを登録しようとすると、エラーが発生します。

`register()` メソッドは、パラメーター設定が `exist_ok = True` の既に登録されているデータセットの定義を返します。

```Python
dataset = dataset.register(workspace = workspace,
                           name = 'dataset_crime',
                           description = 'Training data',
                           exist_ok = True
                           )
```

`list()` を使用して、ワークスペースに登録されているすべてのデータセットを表示します。

```Python
Dataset.list(workspace_name)
```

上記のコードの結果は次のようになります。

```Python
[Dataset(Name: dataset_crime,
         Workspace: workspace_name)]
```

## <a name="access-datasets-in-workspace"></a>ワークスペースのデータセットにアクセスする

登録されたデータセットは、ローカル、リモート、コンピューティング クラスター (Azure Machine Learning コンピューティングなど) でアクセスおよび利用できます。 実験とコンピューティング環境で登録済みデータセットを再利用するには、次のコードを使用してワークスペースと登録済みデータセットを名前で取得します。

```Python
workspace = Workspace.from_config()

dataset = workspace.datasets['dataset_crime']
```

## <a name="next-steps"></a>次の手順

* [データセットを探索および準備します](how-to-explore-prepare-data.md)。
* [データセット定義のライフ サイクルを管理します](how-to-manage-dataset-definitions.md)。
* データセットの使用例については、[サンプル ノートブック](https://aka.ms/dataset-tutorial)を参照してください。
