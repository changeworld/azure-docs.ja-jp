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
ms.openlocfilehash: 473bf87e1961c3c7687b0867885adef40c14d71f
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2019
ms.locfileid: "68694323"
---
# <a name="create-and-access-datasets-preview-in-azure-machine-learning"></a>Azure Machine Learning でデータ セット (プレビュー) を作成してアクセスする

この記事では、Azure Machine Learning データセット (プレビュー) を作成する方法と、ローカルまたはリモートの実験からデータにアクセスする方法について説明します。

Azure Machine Learning データセットを使用すると、次のことを実行できます。 

* データセットから参照される**データの 1 つのコピーをストレージに保存する**

* 探索的データ分析を使用して**データを分析する** 

* 接続文字列やデータ パスを気にせずに、**モデルのトレーニング中にデータに簡単にアクセスする**。

* 他のユーザーと**データ共有と共同作業を行う**

## <a name="prerequisites"></a>前提条件

データセットを作成して操作するには、以下が必要です。

* Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、開始する前に無料アカウントを作成してください。 [無料版または有料版の Azure Machine Learning service](https://aka.ms/AMLFree) を今日からお試しいただけます。

* [Azure Machine Learning service ワークスペース](https://docs.microsoft.com/azure/machine-learning/service/setup-create-workspace)

* [Azure Machine Learning SDK for Python がインストール済み](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) (これには azureml-datasets パッケージが含まれています)。

> [!Note]
> 一部の Dataset クラス (プレビュー) は [azureml-dataprep](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) パッケージ (GA) に依存しています。 Linux ユーザーの場合、これらのクラスは次のディストリビューションでのみサポートされています。Red Hat Enterprise Linux、Ubuntu、Fedora、および CentOS。

## <a name="data-formats"></a>データ形式

Azure Machine Learning Dataset は次の形式から作成できます。
+ [delimited](/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-delimited-files-path--separator------header--promoteheadersbehavior-all-files-have-same-headers--3---encoding--fileencoding-utf8--0---quoting-false--infer-column-types-true--skip-rows-0--skip-mode--skiplinesbehavior-no-rows--0---comment-none--include-path-false--archive-options-none--partition-format-none-)
+ [json](/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-json-files-path--encoding--fileencoding-utf8--0---flatten-nested-arrays-false--include-path-false--partition-format-none-)
+ [Excel](/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-excel-files-path--sheet-name-none--use-column-headers-false--skip-rows-0--include-path-false--infer-column-types-true--partition-format-none-)
+ [Parquet](/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-parquet-files-path--include-path-false--partition-format-none-)
+ [pandas DataFrame](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-pandas-dataframe-dataframe--path-none--in-memory-false-)
+ [SQL query](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-sql-query-data-source--query-)
+ [binary](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-binary-files-path-)

## <a name="create-datasets"></a>データセットを作成する 

データセットを作成することにより、データ ソースの場所への参照とそのメタデータのコピーを作成します。 データは既存の場所に残るので、追加のストレージ コストは発生しません。

### <a name="create-from-local-files"></a>ローカル ファイルから作成する

`Dataset` クラスの [`auto_read_files()`](/python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#auto-read-files-path--include-path-false--partition-format-none-) メソッドを使用してファイルまたはフォルダーのパスを指定し、ローカル コンピューターからファイルを読み込みます。  このメソッドは、ファイルの種類の指定や引数の解析を必要とせずに、次の手順を実行します。

* 区切り記号の推測と設定。
* ファイルの先頭にある空のレコードのスキップ。
* ヘッダー行の推測と設定。
* 列のデータ型の推測と変換。

```Python
from azureml.core.dataset import Dataset

dataset = Dataset.auto_read_files('./data/crime.csv')
```

または、ファイル固有の関数を使用して、ファイルの解析を明示的に制御します。 


### <a name="create-from-azure-datastores"></a>Azure データストアから作成する

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
dstore = Datastore.get(workspace, datastore_name)
```

`from_delimited_files()` メソッドを使用して [DataReference](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py) から区切られたファイルを読み取り、未登録のデータセットを作成します。

```Python
# create an in-memory Dataset on your local machine
dataset = Dataset.from_delimited_files(dstore.path('data/src/crime.csv'))

# returns the first 5 rows of the Dataset as a pandas Dataframe.
dataset.head(5)
```

## <a name="register-datasets"></a>データセットを登録する

作成プロセスを完了するには、ワークスペースにデータセットを登録します。

他のユーザーと共有したり、さまざまな実験で再利用できるようにしたりするために、[`register()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#register-workspace--name--description-none--tags-none--visible-true--exist-ok-false--update-if-exist-false-) メソッドを使用してデータセットをワークスペースに登録します。

```Python
dataset = dataset.register(workspace = workspace,
                           name = 'dataset_crime',
                           description = 'Training data',
                           exist_ok = False
                           )
```

>[!NOTE]
> `exist_ok = False` (既定) の場合、別のものと同じ名前でデータセットを登録しようとすると、エラーが発生します。 既存のものを上書きするには `True` に設定してください。

## <a name="access-data-in-datasets"></a>データセット内のデータにアクセスする

登録されたデータセットは、ローカル、リモート、コンピューティング クラスター (Azure Machine Learning コンピューティングなど) でアクセスできます。 実験で登録済みデータセットにアクセスするには、次のコードを使用してワークスペースと登録済みデータセットを名前で取得します。

```Python
workspace = Workspace.from_config()

# See list of datasets registered in workspace.
print(Dataset.list(workspace))

# Get dataset by name
dataset = Dataset.get(workspace, 'dataset_crime')

# Load data into pandas DataFrame
dataset.to_pandas_dataframe()
```

## <a name="next-steps"></a>次の手順

* [データセットを探索および準備します](how-to-explore-prepare-data.md)。
* Datasets の使用例については、[サンプル ノートブック](https://aka.ms/dataset-tutorial)を参照してください。
