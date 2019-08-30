---
title: azureml-datasets でデータセットを作成してデータにアクセスする
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
ms.date: 05/21/2019
ms.openlocfilehash: 67dda1ab56c6a706a9fdbef45fabdae9167ffe2b
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2019
ms.locfileid: "69616335"
---
# <a name="create-and-access-datasets-preview-in-azure-machine-learning"></a>Azure Machine Learning でデータ セット (プレビュー) を作成してアクセスする

この記事では、Azure Machine Learning データセット (プレビュー) を作成する方法と、ローカルまたはリモートの実験からデータにアクセスする方法について説明します。

Azure Machine Learning データセットを使用すると、次のことを実行できます。 

* データセットから参照される**データの 1 つのコピーをストレージに保存する**。 

* 接続文字列やデータ パスを気にせずに、**モデルのトレーニング中にデータに簡単にアクセスする**。

* 他のユーザーと**データ共有と共同作業を行う**。

## <a name="prerequisites"></a>前提条件

データセットを作成して操作するには、以下が必要です。

* Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、開始する前に無料アカウントを作成してください。 [無料版または有料版の Azure Machine Learning service](https://aka.ms/AMLFree) を今日からお試しいただけます。

* [Azure Machine Learning service ワークスペース](how-to-manage-workspace.md)

* [Azure Machine Learning SDK for Python がインストール済み](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) (これには azureml-datasets パッケージが含まれています)。

> [!Note]
> 一部の Dataset クラス (プレビュー) は [azureml-dataprep](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) パッケージに依存しています。 Linux ユーザーの場合、これらのクラスは次のディストリビューションでのみサポートされています。Red Hat Enterprise Linux、Ubuntu、Fedora、および CentOS。

## <a name="dataset-types"></a>データセットの種類
データセットは、ユーザーがトレーニングでそれらを使用する方法に基づいて、さまざまな種類に分類されます。 現時点では、指定されたファイルまたはファイルのリストを解析して、データを表形式で表す TabularDataset がサポートされています。 これにより、データを pandas の DataFrame で具体化することができます。 TabularDataset は、csv ファイル、tsv ファイル、parquet ファイル、SQL クエリの結果などから作成できます。完全な一覧については、ドキュメントを参照してください。

今後の API の変更の詳細については、[Azure Machine Learning service の説明](https://aka.ms/tabular-dataset)に関するページを参照してください 

## <a name="create-datasets"></a>データセットを作成する 

データセットを作成することにより、データ ソースの場所への参照とそのメタデータのコピーを作成します。 データは既存の場所に残るので、追加のストレージ コストは発生しません。

データを Azure Machine Learning service からアクセスできるようにするには、[Azure データストア](how-to-access-data.md)またはパブリック Web URL のパスからデータセットを作成する必要があります。

[Azure データストア](how-to-access-data.md)からデータセットを作成するには、以下のようにします。

* 登録された Azure データストアへの `contributor` または `owner` アクセス権を持っていることを確認します。

* データストア内のパスを参照してデータセットを作成します。

```Python
from azureml.core.workspace import Workspace
from azureml.core.datastore import Datastore
from azureml.core.dataset import Dataset

datastore_name = 'your datastore name'

# get existing workspace
workspace = Workspace.from_config()

# retrieve an existing datastore in the workspace by name
datastore = Datastore.get(workspace, datastore_name)
```
### <a name="create-tabulardatasets"></a>TabularDataset を作成する

csv 形式または tsv 形式のファイルを読み取り、登録されていない TabularDataset を作成するには、`TabularDatasetFactory` クラスの `from_delimited_files()` メソッドを使います。 複数のファイルから読み取る場合、結果は 1 つの表形式に集計されます。

```Python
# create a TabularDataset from multiple paths in datastore
datastore_paths = [
                  (datastore, 'weather/2018/11.csv'),
                  (datastore, 'weather/2018/12.csv'),
                  (datastore, 'weather/2019/*.csv')
                 ]
weather_ds = Dataset.Tabular.from_delimited_files(path=datastore_paths)

# create a TabularDataset from a delimited file behind a public web url
web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path)

# preview the first 3 rows of titanic_ds
titanic_ds.take(3).to_pandas_dataframe()
```

| |PassengerId|Survived|Pclass|Name|Sex|Age|SibSp|Parch|Ticket|Fare|Cabin|Embarked
-|-----------|--------|------|----|---|---|-----|-----|------|----|-----|--------|
0|1|0|3|Braund, Mr. Owen Harris|male|22.0|1|0|A/5 21171|7.2500||S
1|2|1|1|Cumings, Mrs. John Bradley (Florence Briggs Th...|female|38.0|1|0|PC 17599|71.2833|C85|C
2|3|1|3|Heikkinen, Miss. Laina|female|26.0|0|0|STON/O2. 3101282|7.9250||S

## <a name="register-datasets"></a>データセットを登録する

作成プロセスを完了するには、ワークスペースにデータセットを登録します。

他のユーザーと共有したり、さまざまな実験で再利用できるように、データセットをワークスペースに登録するには、`register()` メソッドを使います。

```Python
titanic_ds = titanic_ds.register(workspace = workspace,
                                 name = 'titanic_ds',
                                 description = 'titanic training data')
```

## <a name="version-datasets"></a>データセットをバージョン管理する

新しいバージョンを作成すると、新しいデータセットを同じ名前で登録できます。 データセットのバージョンは、データの状態をブックマークする方法なので、実験または将来の複製に対して特定のバージョンのデータセットを適用できます。 バージョン管理を検討する一般的なシナリオは次のとおりです。 
* 再トレーニングのために新しいデータを使用できるとき。
* 異なるデータ準備または特徴エンジニアリング アプローチを適用するとき。

```Python
# create a TabularDataset from new Titanic training data
web_paths = [
            'https://dprepdata.blob.core.windows.net/demo/Titanic.csv',
            'https://dprepdata.blob.core.windows.net/demo/Titanic2.csv'
           ]          
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_paths)

# create a new version of titanic_ds
titanic_ds = titanic_ds.register(workspace = workspace,
                                 name = 'titanic_ds',
                                 description = 'new titanic training data',
                                 create_new_version = True)
```


## <a name="access-your-data-during-training"></a>トレーニング中にデータにアクセスする

登録されたデータセットは、ローカル、リモート、コンピューティング クラスター (Azure Machine Learning コンピューティングなど) でアクセスできます。 実験で登録済みデータセットにアクセスするには、次のコードを使用してワークスペースと登録済みデータセットを名前で取得します。 `Dataset` クラスの `get_by_name` メソッドからは、既定では、ワークスペースに登録されているデータセットの最新バージョンが返されます。

```Python
%%writefile $script_folder/train.py

from azureml.core import Dataset, Run

run = Run.get_context()
workspace = run.experiment.workspace

dataset_name = 'titanic_ds'

# Get a dataset by name
titanic_ds = Dataset.get_by_name(workspace=workspace, name=dataset_name)

# Load a TabularDataset into pandas DataFrame
df = titanic_ds.to_pandas_dataframe()
```

## <a name="next-steps"></a>次の手順

* 自動機械学習を使用し、[TabularDataset でトレーニング](https://aka.ms/automl-dataset)を行います。
* データセットでのトレーニングの他の例については、[サンプル ノートブック](https://aka.ms/dataset-tutorial)を参照してください。
