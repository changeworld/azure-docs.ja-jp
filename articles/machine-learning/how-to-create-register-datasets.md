---
title: Azure Machine Learning データセットを作成してデータにアクセスする
titleSuffix: Azure Machine Learning
description: 機械学習の実験を実行するために Azure Machine Learning データセットを作成してデータにアクセスする方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 11/04/2019
ms.openlocfilehash: b9060823c997391d02eae61911f8aa748f191657
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/17/2020
ms.locfileid: "76260856"
---
# <a name="create-azure-machine-learning-datasets"></a>Azure Machine Learning データセットを作成する

[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

この記事では、Azure Machine Learning データセットを作成し、ローカルまたはリモートの実験のデータにアクセスする方法について説明します。

Azure Machine Learning データセットを使用すると、次のことを実行できます。

* データセットから参照されるデータの 1 つのコピーをストレージに保存する。

* 接続文字列やデータ パスを気にすることなく、モデルのトレーニング中にデータにシームレスにアクセスする。

* 他のユーザーとデータを共有し、共同作業を行う。

## <a name="prerequisites"></a>前提条件

データセットを作成して操作するには、以下が必要です。

* Azure サブスクリプション。 まだ持っていない場合は、開始する前に無料アカウントを作成してください。 [無料版または有料版の Azure Machine Learning](https://aka.ms/AMLFree) をお試しください。

* [Azure Machine Learning ワークスペース](how-to-manage-workspace.md)。

* [Azure Machine Learning SDK for Python がインストール済み](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) (これには azureml-datasets パッケージが含まれています)。

> [!NOTE]
> 一部の Dataset クラスは、[azureml-dataprep](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) パッケージに依存しています。 Linux ユーザーの場合、これらのクラスは次のディストリビューションでのみサポートされています。Red Hat Enterprise Linux、Ubuntu、Fedora、および CentOS。

## <a name="dataset-types"></a>データセットの種類

データセットは、ユーザーがトレーニングでそれらを使用する方法に基づいて、2 つの種類に分類されます。

* [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) は、指定されたファイルまたはファイルのリストを解析して、データを表形式で表します。 これにより、データを pandas または spark の DataFrame で具体化することができます。 `TabularDataset` オブジェクトは、.csv ファイル、.tsv ファイル、.parquet ファイル、.jsonl ファイル、SQL クエリ結果から作成できます。 完全な一覧については、「[TabularDatasetFactory クラス](https://aka.ms/tabulardataset-api-reference)」を参照してください。

* [FileDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py) クラスは、データストア内の 1 つまたは複数のファイル、またはパブリック URL を参照します。 このメソッドによって、ファイルを FileDataset オブジェクトとしてコンピューターにダウンロードまたはマウントできます。 任意の形式のファイルを使用できます。これにより、ディープ ラーニングなどの幅広い機械学習シナリオに対応します。

今後の API の変更点の詳細については、「[Dataset API の変更通知](https://aka.ms/tabular-dataset)」を参照してください。

## <a name="create-datasets"></a>データセットを作成する

データセットを作成することにより、データ ソースの場所への参照とそのメタデータのコピーを作成します。 データは既存の場所に残るため、追加のストレージ コストは発生しません。 Python SDK またはワークスペースのランディング ページ (プレビュー) を使用して、`TabularDataset` と `FileDataset` の両方のデータ セットを作成できます。

データを Azure Machine Learning からアクセスできるようにするには、[Azure データストア](how-to-access-data.md)またはパブリック Web URL のパスからデータセットを作成する必要があります。

### <a name="use-the-sdk"></a>SDK を使用する

Python SDK を使用して [Azure データストア](how-to-access-data.md)からデータセットを作成するには、次の手順に従います。

1. 登録された Azure データストアへの `contributor` または `owner` アクセス権を持っていることを確認します。

2. データストア内のパスを参照してデータセットを作成します。
> [!Note]
> 複数のデータストア内の複数のパスからデータセットを作成できます。 データセットの作成元として使用できるファイルの数やデータ サイズには、厳密な制限はありません。 ただし、データ パスごとに、いくつかの要求がストレージ サービスに送信され、ファイルまたはフォルダーを指しているかどうかが確認されます。 このオーバーヘッドによって、パフォーマンスが低下したり、エラーが発生したりする可能性があります。 1000 個のファイルを含む 1 つのフォルダーを参照するデータセットは、1 つのデータ パスを参照していると見なされます。 最適なパフォーマンスを得るために、データストア内の 100 未満のパスを参照するデータセットを作成することをお勧めします。


#### <a name="create-a-tabulardataset"></a>TabularDataset を作成する

TabularDatasets を作成するには、SDK を使用するか、または Azure Machine Learning Studio を使用します。 

.csv 形式または .tsv 形式のファイルを読み取り、登録されていない TabularDataset を作成するには、`TabularDatasetFactory` クラスの [`from_delimited_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-delimited-files-path--validate-true--include-path-false--infer-column-types-true--set-column-types-none--separator------header-true--partition-format-none-) メソッドを使用します。 複数のファイルから読み取る場合、結果は 1 つの表形式に集計されます。

```Python
from azureml.core import Workspace, Datastore, Dataset

datastore_name = 'your datastore name'

# get existing workspace
workspace = Workspace.from_config()
    
# retrieve an existing datastore in the workspace by name
datastore = Datastore.get(workspace, datastore_name)

# create a TabularDataset from 3 paths in datastore
datastore_paths = [(datastore, 'ather/2018/11.csv'),
                   (datastore, 'weather/2018/12.csv'),
                   (datastore, 'weather/2019/*.csv')]
weather_ds = Dataset.Tabular.from_delimited_files(path=datastore_paths)
```

既定では、TabularDataset を作成するときに、列のデータ型は自動的に推論されます。 推論された型が期待どおりでない場合は、次のコードを使用して列の型を指定できます。 [サポートされているデータ型の詳細を参照](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.datatype?view=azure-ml-py)することもできます。

```Python
from azureml.data.dataset_factory import DataType

# create a TabularDataset from a delimited file behind a public web url and convert column "Survived" to boolean
web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path, set_column_types={'Survived': DataType.to_bool()})

# preview the first 3 rows of titanic_ds
titanic_ds.take(3).to_pandas_dataframe()
```

| |PassengerId|Survived|Pclass|Name|Sex|Age|SibSp|Parch|Ticket|Fare|Cabin|Embarked
-|-----------|--------|------|----|---|---|-----|-----|------|----|-----|--------|
0|1|False|3|Braund, Mr. Owen Harris|male|22.0|1|0|A/5 21171|7.2500||S
1|2|True|1|Cumings, Mrs. John Bradley (Florence Briggs Th...|female|38.0|1|0|PC 17599|71.2833|C85|C
2|3|True|3|Heikkinen, Miss. Laina|female|26.0|0|0|STON/O2. 3101282|7.9250||S

Azure SQL Database から読み取るには、`TabularDatasetFactory` クラスの [`from_sql_query()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-sql-query-query--validate-true--set-column-types-none-) メソッドを使用します。

```Python

from azureml.core import Dataset, Datastore

# create tabular dataset from a SQL database in datastore
sql_datastore = Datastore.get(workspace, 'mssql')
sql_ds = Dataset.Tabular.from_sql_query((sql_datastore, 'SELECT * FROM my_table'))
```

TabularDatasets では、データの列から、またはデータが格納されている任意のパス パターンからタイム スタンプを指定して、時系列特性を有効にすることができます。 この指定により、時間による簡単かつ効率的なフィルター処理が可能になります。

タイムスタンプの列を指定し、時間でのフィルター処理を有効にするには、`TabularDataset` クラスの [`with_timestamp_columns()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#with-timestamp-columns-fine-grain-timestamp--coarse-grain-timestamp-none--validate-false-) メソッドを使用します。 詳細については、「[NOAA 気象データを使用した表形式の時系列関連の API のデモ](https://aka.ms/azureml-tsd-notebook)」を参照してください。

```Python
# create a TabularDataset with time series trait
datastore_paths = [(datastore, 'weather/*/*/*/data.parquet')]

# get a coarse timestamp column from the path pattern
dataset = Dataset.Tabular.from_parquet_files(path=datastore_path, partition_format='weather/{coarse_time:yyy/MM/dd}/data.parquet')

# set coarse timestamp to the virtual column created, and fine grain timestamp from a column in the data
dataset = dataset.with_timestamp_columns(fine_grain_timestamp='datetime', coarse_grain_timestamp='coarse_time')

# filter with time-series-trait-specific methods
data_slice = dataset.time_before(datetime(2019, 1, 1))
data_slice = dataset.time_after(datetime(2019, 1, 1))
data_slice = dataset.time_between(datetime(2019, 1, 1), datetime(2019, 2, 1))
data_slice = dataset.time_recent(timedelta(weeks=1, days=1))
```

#### <a name="create-a-filedataset"></a>FileDataset を作成する

任意の形式のファイルを読み込み、登録されていない FileDataset を作成するには、`FileDatasetFactory` クラスの [`from_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py#from-files-path--validate-true-) メソッドを使用します。

```Python
# create a FileDataset pointing to files in 'animals' folder and its subfolders recursively
datastore_paths = [(datastore, 'animals')]
animal_ds = Dataset.File.from_files(path=datastore_paths)

# create a FileDataset from image and label files behind public web urls
web_paths = ['https://azureopendatastorage.blob.core.windows.net/mnist/train-images-idx3-ubyte.gz',
             'https://azureopendatastorage.blob.core.windows.net/mnist/train-labels-idx1-ubyte.gz']
mnist_ds = Dataset.File.from_files(path=web_paths)
```

#### <a name="on-the-web"></a>Web 上 
次の手順とアニメーションは、Azure Machine Learning Studio (https://ml.azure.com ) でデータセットを作成する方法を示しています。

![UI を使用してデータセットを作成する](./media/how-to-create-register-datasets/create-dataset-ui.gif)

Studio でデータセットを作成するには、次の手順を実行します。
1. [https://ml.azure.com](https://ml.azure.com ) でサインインします。
1. 左側のウィンドウの **[アセット]** セクションで **[データセット]** を選択します。 
1. **[データセットの作成]** を選択して、データセットのソースを選択します。 このソースには、ローカル ファイル、データストア、またはパブリック URL を指定できます。
1. データセットの種類として **[表形式]** または **[ファイル]** を選択します。
1. **[次へ]** を選択して、 **[Settings and preview]\(設定とプレビュー\)** 、 **[スキーマ]** 、および **[詳細の確認]** フォームを確認します。これらはファイルの種類に基づいてインテリジェントに設定されます。 これらのフォームを使用して、選択内容を確認し、作成前にデータセットをさらに構成します。  
1. **[作成]** を選択して、データセットの作成を完了します。

## <a name="register-datasets"></a>データセットを登録する

作成プロセスを完了するには、ワークスペースにデータセットを登録します。 データセットを他のユーザーと共有したり、さまざまな実験で再利用できるように、ご使用のワークスペースに登録するには、[`register()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#register-workspace--name--description-none--tags-none--visible-true--exist-ok-false--update-if-exist-false-) メソッドを使用します。

```Python
titanic_ds = titanic_ds.register(workspace=workspace,
                                 name='titanic_ds',
                                 description='titanic training data')
```

> [!Note]
> Azure Machine Learning Studio を介して作成されたデータセットは、自動的にワークスペースに登録されます。

## <a name="create-datasets-with-azure-open-datasets"></a>Azure Open Datasets を使用してデータセットを作成する

[Azure オープン データセット](https://azure.microsoft.com/services/open-datasets/)は選別されたパブリック データセットであり、機械学習ソリューションにシナリオ固有の機能を追加してモデルの精度を上げるために使用できます。 データセットには、機械学習モデルのトレーニングと予測ソリューションのエンリッチメントに役立つ天気、国勢調査、祝日、公共の安全、場所に関するパブリック ドメイン データが含まれます。 Open Datasets は Microsoft Azure 上のクラウドにあり、SDK とワークスペース UI の両方に含まれています。

### <a name="use-the-sdk"></a>SDK を使用する

SDK から Azure Open Datasets を使用してデータセットを作成するには、`pip install azureml-opendatasets` を使用してパッケージがインストールされていることを確認してください。 個々のデータセットは、SDK で独自のクラスによって表され、特定のクラスは `TabularDataset`、`FileDataset`、またはその両方として使用できます。 すべてのクラスの一覧については、[リファレンス ドキュメント](https://docs.microsoft.com/python/api/azureml-opendatasets/azureml.opendatasets?view=azure-ml-py)を参照してください。

特定のクラスは、`TabularDataset` または `FileDataset` として取得できます。こうすることで、ファイルを直接操作したり、ダウンロードしたりできます。 他のクラスは、`get_tabular_dataset()` 関数または`get_file_dataset()` 関数を使用する場合に**のみ**、データセットを取得できます。 次のコード サンプルは、これらの種類のクラスのいくつかの例を示しています。

```python
from azureml.opendatasets import MNIST

# MNIST class can return either TabularDataset or FileDataset
tabular_dataset = MNIST.get_tabular_dataset()
file_dataset = MNIST.get_file_dataset()

from azureml.opendatasets import Diabetes

# Diabetes class can return ONLY return TabularDataset and must be called from the static function
diabetes_tabular = Diabetes.get_tabular_dataset()
```

Open Datasets から作成されたデータセットを登録すると、データはすぐにはダウンロードされませんが、中央の保存場所から要求したとき (たとえばトレーニング中に) にデータにアクセスできます。

### <a name="use-the-ui"></a>UI を使用する

また、UI を使用して、Open Datasets のクラスからデータセットを作成することもできます。 ワークスペースで、 **[資産]** の下にある **[データセット]** タブを選択します。 **[データセットの作成]** ドロップダウンをクリックし、 **[From Open Datasets]\(Open Datasets から\)** を選択します。

![UI と Open Dataset](./media/how-to-create-register-datasets/open-datasets-1.png)

データセットを、そのタイルを選択して選択します (検索バーを使用してフィルター処理をするオプションがあります)。 **[次へ]** を選択します。

![データセットを選択する](./media/how-to-create-register-datasets/open-datasets-2.png)

データセットの登録先の名前を選択し、必要に応じて、使用可能なフィルターを使用してデータをフィルター処理します。 この例では、public holidays データセットについて、期間を 1 年に、国番号を米国のみにしてフィルター処理します。 **作成** を選択します。

![データセット パラメーターを設定してデータセットを作成する](./media/how-to-create-register-datasets/open-datasets-3.png)

この時点で、データセットはワークスペース内の **[データセット]** の下で利用できます。 これを、自分で作成した他のデータセットと同じ方法で使用できます。

## <a name="version-datasets"></a>データセットをバージョン管理する

新しいバージョンを作成すると、新しいデータセットを同じ名前で登録できます。 データセットのバージョンは、実験または将来の複製に対して特定のバージョンのデータセットを適用できるように、データの状態をブックマークする方法です。 [データセットのバージョンの詳細](how-to-version-track-datasets.md)を参照してください。
```Python
# create a TabularDataset from Titanic training data
web_paths = ['https://dprepdata.blob.core.windows.net/demo/Titanic.csv',
             'https://dprepdata.blob.core.windows.net/demo/Titanic2.csv']
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_paths)

# create a new version of titanic_ds
titanic_ds = titanic_ds.register(workspace = workspace,
                                 name = 'titanic_ds',
                                 description = 'new titanic training data',
                                 create_new_version = True)
```

## <a name="access-datasets-in-your-script"></a>スクリプトでデータセットにアクセスする

登録されたデータセットは、Azure Machine Learning コンピューティングなどのコンピューティング クラスター上で、ローカルまたはリモートでアクセスできます。 実験で登録済みデータセットにアクセスするには、次のコードを使用して、ワークスペースと登録済みデータセットに名前でアクセスします。 既定では、`Dataset` クラスの [`get_by_name()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-by-name-workspace--name--version--latest--) メソッドからは、ワークスペースに登録されているデータセットの最新バージョンが返されます。

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

## <a name="next-steps"></a>次のステップ

* [データセットを使ってトレーニングする方法](how-to-train-with-datasets.md)をご覧ください。
* 自動機械学習を使用し、[TabularDataset でトレーニング](https://aka.ms/automl-dataset)を行います。
* データセットのトレーニングのその他の例については、[サンプル ノートブック](https://aka.ms/dataset-tutorial)を参照してください。
