---
title: azureml-datasets を使用したトレーニング
titleSuffix: Azure Machine Learning
description: トレーニングでデータセットを使用する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 09/25/2019
ms.openlocfilehash: f87dbedb1428b5884e20a9f7daabea792387fe88
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/23/2020
ms.locfileid: "76543309"
---
# <a name="train-with-datasets-in-azure-machine-learning"></a>Azure Machine Learning でデータセットを使用してトレーニングする
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

この記事では、接続文字列やデータ パスを気にせずに、リモート実験トレーニングの実行時に [Azure Machine Learning データセット](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py)を使用する 2 つの方法について説明します。

- オプション 1: 構造化データがある場合、TabularDataset を作成し、トレーニング スクリプトで直接それを使用します。

- オプション 2:非構造化データがある場合、FileDataset を作成し、トレーニングのためにリモート コンピューティングにファイルをマウントまたはダウンロードします。

Azure Machine Learning データセットにより、[ScriptRun](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrun?view=azure-ml-py)、[Estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py)、[HyperDrive](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive?view=azure-ml-py) などの Azure Machine Learning トレーニング製品とのシームレスな統合が提供されます。

## <a name="prerequisites"></a>前提条件

データセットを作成し、それを使用してトレーニングするには、以下が必要です。

* Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、開始する前に無料アカウントを作成してください。 [無料版または有料版の Azure Machine Learning](https://aka.ms/AMLFree) を今すぐお試しください。

* [Azure Machine Learning ワークスペース](how-to-manage-workspace.md)。

* [Azure Machine Learning SDK for Python がインストール済み](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) (これには azureml-datasets パッケージが含まれています)。

> [!Note]
> 一部の Dataset クラスは、[azureml-dataprep](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) パッケージに依存しています。 Linux ユーザーの場合、これらのクラスは次のディストリビューションでのみサポートされています。Red Hat Enterprise Linux、Ubuntu、Fedora、および CentOS。

## <a name="option-1-use-datasets-directly-in-training-scripts"></a>オプション 1: トレーニング スクリプトでデータセットを直接使用する

この例では、[TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) を作成し、それをトレーニング用の `estimator` オブジェクトへの直接入力として使用します。 

### <a name="create-a-tabulardataset"></a>TabularDataset を作成する

次のコードでは、Web URL から未登録の TabularDataset を作成します。 データストアのローカル ファイルまたはローカル パスからデータセットを作成することもできます。 [データセットを作成する方法](https://aka.ms/azureml/howto/createdatasets)の詳細をご覧ください。

```Python
from azureml.core.dataset import Dataset

web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path)
```

### <a name="access-the-input-dataset-in-your-training-script"></a>トレーニング スクリプトで入力データセットにアクセスする

TabularDataset オブジェクトを使用すると、使い慣れたデータ準備とトレーニングのライブラリを操作できるように、pandas または spark DataFrame にデータを読み込むことができます。 この機能を利用するには、トレーニング構成で TabularDataset を入力として渡し、それをスクリプトで取得します。

これを行うには、トレーニング スクリプト内の [`Run`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py) オブジェクトを使用して入力データセットにアクセスし、[`to_pandas_dataframe()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset#to-pandas-dataframe-on-error--null---out-of-range-datetime--null--) メソッドを使用します。 

```Python
%%writefile $script_folder/train_titanic.py

from azureml.core import Dataset, Run

run = Run.get_context()
# get the input dataset by name
dataset = run.input_datasets['titanic_ds']
# load the TabularDataset to pandas DataFrame
df = dataset.to_pandas_dataframe()
```

### <a name="configure-the-estimator"></a>エスティメーターを構成する

実験実行の送信には、[エスティメーター](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) オブジェクトが使用されます。 Azure Machine Learning には、一般的な機械学習フレームワーク用に事前に構成されたエスティメーターと、汎用的なエスティメーターがあります。

このコードでは、以下を指定して `est` という汎用エスティメーター オブジェクトを作成しています。

* 使用するスクリプトのスクリプト ディレクトリ。 このディレクトリ内のすべてのファイルは、実行のためにクラスター ノード内にアップロードされます。
* トレーニング スクリプト *train_titanic.py*。
* トレーニングの入力データセット `titanic`。 `as_named_input()` は、トレーニング スクリプトで割り当てられた名前によって入力データセットを参照できるようにするために必要です。 
* 実験のコンピューティング先。
* 実験の環境定義。

```Python
est = Estimator(source_directory=script_folder,
                entry_script='train_titanic.py',
                # pass dataset object as an input with name 'titanic'
                inputs=[titanic_ds.as_named_input('titanic')],
                compute_target=compute_target,
                environment_definition= conda_env)

# Submit the estimator as part of your experiment run
experiment_run = experiment.submit(est)
experiment_run.wait_for_completion(show_output=True)
```

## <a name="option-2--mount-files-to-a-remote-compute-target"></a>オプション 2:リモート コンピューティング先にファイルをマウントする

データ ファイルをトレーニング用のコンピューティング先で使用できるようにする場合は、[FileDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py) を使用して、それによって参照されているファイルをマウントまたはダウンロードします。

### <a name="mount-vs-download"></a>マウントと ダウンロード
データセットをマウントする場合は、データセットによって参照されているファイルをディレクトリ (マウント ポイント) に接続し、コンピューティング先で使用できるようにします。 Azure Machine Learning コンピューティング、仮想マシン、HDInsight など、Linux ベースのコンピューティングでは、マウントがサポートされています。 データ サイズがコンピューティング ディスクのサイズを超えている場合、または読み込み対象がスクリプト内のデータセットの一部のみの場合は、マウントをお勧めします。 ディスク サイズよりも大きいデータセットのダウンロードは失敗し、マウントしても、処理時にスクリプトで使用されるデータの一部しか読み込まれません。 

データセットをダウンロードするとき、データセットによって参照されるすべてのファイルが、コンピューティング先にダウンロードされます。 すべてのコンピューティングの種類でダウンロードがサポートされています。 データセットによって参照されるファイルすべてのがスクリプトで処理され、コンピューティング ディスクが完全なデータセットに収まる場合は、ダウンロードによって、ストレージ サービスからのデータ ストリーミングのオーバーヘッドを回避することをお勧めします。

任意の形式のファイルをダウンロードしたりマウントしたりすることは、Azure BLOB ストレージ、Azure Files、Azure Data Lake Storage Gen1、Azure Data Lake Storage Gen2、Azure SQL Database、および Azure Database for PostgreSQL から作成されたデータセットに対してサポートされています。 

### <a name="create-a-filedataset"></a>FileDataset を作成する

次の例では、未登録の FileDataset を Web URL から作成します。 他のソースから[データセットを作成する方法](https://aka.ms/azureml/howto/createdatasets)の詳細を参照してください。

```Python
from azureml.core.dataset import Dataset

web_paths = [
            'http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz'
            ]
mnist_ds = Dataset.File.from_files(path = web_paths)
```

### <a name="configure-the-estimator"></a>エスティメーターを構成する

エスティメーターで `inputs` パラメーターを使用してデータセットを渡すほか、`script_params` を使用してデータセットを渡し、引数を使用してトレーニング スクリプト内でデータ パス (マウント ポイント) を取得することもできます。 こうすると、azureml-sdk とは無関係にトレーニング スクリプトを保持できます。 つまり、任意のクラウド プラットフォームで、ローカル デバッグとリモート トレーニングに、同じトレーニング スクリプトを使用できるようになります。

scikit-learn 実験の実行の送信には、[SKLearn](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) エスティメーター オブジェクトが使用されます。 SKlearn エスティメーターによるトレーニングの詳細については[こちら](how-to-train-scikit-learn.md)を参照してください。

```Python
from azureml.train.sklearn import SKLearn

script_params = {
    # mount the dataset on the remote compute and pass the mounted path as an argument to the training script
    '--data-folder': mnist_ds.as_named_input('mnist').as_mount(),
    '--regularization': 0.5
}

est = SKLearn(source_directory=script_folder,
              script_params=script_params,
              compute_target=compute_target,
              environment_definition=env,
              entry_script='train_mnist.py')

# Run the experiment
run = experiment.submit(est)
run.wait_for_completion(show_output=True)
```

### <a name="retrieve-the-data-in-your-training-script"></a>トレーニング スクリプトでデータを取得する

実行を送信すると、`mnist` データセットによって参照されるデータ ファイルがコンピューティング先にマウントされます。 次のコードは、スクリプトでデータを取得する方法を示しています。

```Python
%%writefile $script_folder/train_mnist.py

import argparse
import os
import numpy as np
import glob

from utils import load_data

# retrieve the 2 arguments configured through script_params in estimator
parser = argparse.ArgumentParser()
parser.add_argument('--data-folder', type=str, dest='data_folder', help='data folder mounting point')
parser.add_argument('--regularization', type=float, dest='reg', default=0.01, help='regularization rate')
args = parser.parse_args()

data_folder = args.data_folder
print('Data folder:', data_folder)

# get the file paths on the compute
X_train_path = glob.glob(os.path.join(data_folder, '**/train-images-idx3-ubyte.gz'), recursive=True)[0]
X_test_path = glob.glob(os.path.join(data_folder, '**/t10k-images-idx3-ubyte.gz'), recursive=True)[0]
y_train_path = glob.glob(os.path.join(data_folder, '**/train-labels-idx1-ubyte.gz'), recursive=True)[0]
y_test = glob.glob(os.path.join(data_folder, '**/t10k-labels-idx1-ubyte.gz'), recursive=True)[0]

# load train and test set into numpy arrays
X_train = load_data(X_train_path, False) / 255.0
X_test = load_data(X_test_path, False) / 255.0
y_train = load_data(y_train_path, True).reshape(-1)
y_test = load_data(y_test, True).reshape(-1)
```

## <a name="notebook-examples"></a>ノートブックの例

[データセット ノートブック](https://aka.ms/dataset-tutorial)では、この記事の概念を示し、さらに詳しく説明します。

## <a name="next-steps"></a>次のステップ

* TabularDatasets を使用して[機械学習モデルを自動的にトレーニングする](how-to-auto-train-remote.md)

* FileDatasets を使用して[画像分類モデルをトレーニングする](https://aka.ms/filedataset-samplenotebook)

* [トレーニングとデプロイのための環境の作成と管理](how-to-use-environments.md)
