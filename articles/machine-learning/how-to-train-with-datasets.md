---
title: Machine learning データセットを使用したトレーニング
titleSuffix: Azure Machine Learning
description: Azure Machine Learning データセットを使用してモデルをトレーニングするために、ローカルまたはリモート コンピューティングで独自のデータを使用できるようにする方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: mldata
ms.author: yogipandey
author: ynpandey
ms.reviewer: nibaccam
ms.date: 10/21/2021
ms.topic: how-to
ms.custom: devx-track-python, data4ml
ms.openlocfilehash: 761b78c3485adf2827e1733011d6513ed77e2ba5
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131557302"
---
# <a name="train-models-with-azure-machine-learning-datasets"></a>Azure Machine Learning データセットを使用してモデルをトレーニングする 

この記事では、[Azure Machine Learning データセット](/python/api/azureml-core/azureml.core.dataset%28class%29)を使用して、機械学習モデルのトレーニングを行う方法について説明します。  接続文字列やデータ パスを気にすることなく、ローカルまたはリモートのコンピューティング先でデータセットを使用することができます。 

Azure Machine Learning データセットにより、[ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig)、[HyperDrive](/python/api/azureml-train-core/azureml.train.hyperdrive)、[Azure Machine Learning パイプライン](./how-to-create-machine-learning-pipelines.md)などの Azure Machine Learning トレーニング機能とのシームレスな統合が提供されます。

モデル トレーニングに向けてデータを使用できるようにする準備はできていないものの、データ探索用のノートブックにデータを読み込みたい場合は、[データセット内でデータを探索する](how-to-create-register-datasets.md#explore-data)方法に関する記事を参照してください。 

## <a name="prerequisites"></a>前提条件

データセットを作成し、それを使用してトレーニングするには、以下が必要です。

* Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、開始する前に無料アカウントを作成してください。 [無料版または有料版の Azure Machine Learning](https://azure.microsoft.com/free/) を今すぐお試しください。

* [Azure Machine Learning ワークスペース](how-to-manage-workspace.md)。

* [Azure Machine Learning SDK for Python がインストール済み](/python/api/overview/azure/ml/install) (1.13.0 以上) (これには `azureml-datasets` パッケージが含まれています)。

> [!Note]
> 一部の Dataset クラスは、[azureml-dataprep](https://pypi.org/project/azureml-dataprep/) パッケージに依存しています。 Linux ユーザーの場合、これらのクラスは次のディストリビューションでのみサポートされています。Red Hat Enterprise Linux、Ubuntu、Fedora、および CentOS。

## <a name="consume-datasets-in-machine-learning-training-scripts"></a>Machine learning トレーニング スクリプトでデータセットを使用する

データセットとしてまだ登録されていない構造化データがある場合は、TabularDataset を作成し、それをローカルまたはリモートの実験用のトレーニング スクリプトで直接使用します。

この例では、未登録の [TabularDataset](/python/api/azureml-core/azureml.data.tabulardataset) を作成し、トレーニング用の [ScriptRunConfig](/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig) オブジェクトのスクリプト引数として指定します。 この TabularDataset をワークスペースの他の実験で再利用する場合は、[データセットをワークスペースに登録する方法](how-to-create-register-datasets.md#register-datasets)に関する記事を参照してください。

### <a name="create-a-tabulardataset"></a>TabularDataset を作成する

次のコードでは、Web URL から未登録の TabularDataset を作成します。  

```Python
from azureml.core.dataset import Dataset

web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path)
```

TabularDataset オブジェクトを使用すると、TabularDataset のデータを pandas または Spark DataFrame に読み込むことができるため、お使いのノートブックを離れることなく、使い慣れたデータ準備とトレーニングのライブラリを操作することができます。

### <a name="access-dataset-in-training-script"></a>トレーニング スクリプトでデータセットにアクセスする

次のコードでは、トレーニングの実行を構成するときに指定するスクリプト引数 `--input-data` を構成します (次のセクションを参照してください)。 表形式のデータセットが引数値として渡されると、Azure ML によってデータセットの ID に解決されます。これを使用すると、トレーニング スクリプトのデータセットにアクセスできます (スクリプトにデータセットの名前や ID をハードコーディングする必要はありません)。 次に、トレーニングの前にデータをさらに調べて準備するために、[`to_pandas_dataframe()`](/python/api/azureml-core/azureml.data.tabulardataset#to-pandas-dataframe-on-error--null---out-of-range-datetime--null--) メソッドを使用して、そのデータセットを pandas データフレームに読み込みます。

> [!Note]
> 元のデータ ソースに NaN、空の文字列、または空白の値が含まれている場合、`to_pandas_dataframe()` を使用すると、それらの値は *Null* 値として置き換えられます。

準備されたデータをイン メモリの pandas データ フレームから新しいデータセットに読み込む必要がある場合は、そのデータを parquet などのローカル ファイルに書き込み、そのファイルから新しいデータセットを作成します。 [データセットを作成する方法](how-to-create-register-datasets.md)の詳細をご覧ください。

```Python
%%writefile $script_folder/train_titanic.py

import argparse
from azureml.core import Dataset, Run

parser = argparse.ArgumentParser()
parser.add_argument("--input-data", type=str)
args = parser.parse_args()

run = Run.get_context()
ws = run.experiment.workspace

# get the input dataset by ID
dataset = Dataset.get_by_id(ws, id=args.input_data)

# load the TabularDataset to pandas DataFrame
df = dataset.to_pandas_dataframe()
```

### <a name="configure-the-training-run"></a>トレーニングの実行を構成する

[ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrun) オブジェクトは、トレーニング実行を構成および送信するために使用されます。

このコードでは、以下を指定する ScriptRunConfig オブジェクト `src` を作成します

* 使用するスクリプトのスクリプト ディレクトリ。 このディレクトリ内のすべてのファイルは、実行のためにクラスター ノード内にアップロードされます。
* トレーニング スクリプト *train_titanic.py*。
* スクリプト引数としてのトレーニング用の入力データセット `titanic_ds`。 これがスクリプトに渡されると、Azure ML によってデータセットの対応する ID に解決されます。
* 実行のコンピューティング先。
* 実行の環境。

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory=script_folder,
                      script='train_titanic.py',
                      # pass dataset as an input with friendly name 'titanic'
                      arguments=['--input-data', titanic_ds.as_named_input('titanic')],
                      compute_target=compute_target,
                      environment=myenv)
                             
# Submit the run configuration for your training run
run = experiment.submit(src)
run.wait_for_completion(show_output=True)                             
```

## <a name="mount-files-to-remote-compute-targets"></a>リモート コンピューティング先にファイルをマウントする

非構造化データがある場合は、[FileDataset](/python/api/azureml-core/azureml.data.filedataset) を作成し、データ ファイルをマウントまたはダウンロードして、トレーニング用にリモート コンピューティング先でそれを使用できるようにします。 リモート トレーニング実験用に[マウントまたはダウンロード](#mount-vs-download)を使用する状況について説明します。 

次に例を示します。 

* トレーニング データ用の入力 FileDataset、`mnist_ds` を作成する。
* トレーニング結果の書き込み先と、それらの結果を FileDataset として昇格させることを指定する。
* 入力データセットをコンピューティング先にマウントする。

> [!Note]
> カスタム Docker ベース イメージを使用している場合は、データセットのマウントを機能させるための依存関係として、`apt-get install -y fuse` 経由で fuse をインストールする必要があります。 [カスタム ビルド イメージを構築](./how-to-deploy-custom-container.md)する方法を確認してください。

ノートブックの例については、[データ入出力を使用してトレーニング実行を構成する方法](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/scriptrun-with-data-input-output/how-to-use-scriptrun.ipynb)に関する記事を参照してください。

### <a name="create-a-filedataset"></a>FileDataset を作成する

次の例では、未登録の FileDataset、`mnist_data` を Web URL から作成します。 この FileDataset は、トレーニング実行用の入力データです。

他のソースから[データセットを作成する方法](how-to-create-register-datasets.md)の詳細を参照してください。

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
### <a name="where-to-write-training-output"></a>トレーニング出力の書き込み先

[OutputFileDatasetConfig オブジェクト](/python/api/azureml-core/azureml.data.output_dataset_config.outputfiledatasetconfig)を使用して、トレーニング結果の書き込み先を指定できます。 

OutputFileDatasetConfig オブジェクトを使用すると、次のことができます。 

* 指定したクラウド ストレージに実行の出力をマウントまたはアップロードする。
* 出力を FileDataset として、これらのサポートされているストレージの種類に保存する。
    * Azure BLOB
    * Azure ファイル共有
    * Azure Data Lake Storage Gen 1 と 2
* トレーニング実行間のデータ系列を追跡する。

次のコードでは、トレーニング結果を FileDataset として既定の BLOB データストア `def_blob_store` の `outputdataset` フォルダーに保存するように指定します。 

```python
from azureml.core import Workspace
from azureml.data import OutputFileDatasetConfig

ws = Workspace.from_config()

def_blob_store = ws.get_default_datastore()
output = OutputFileDatasetConfig(destination=(def_blob_store, 'sample/outputdataset'))
```

### <a name="configure-the-training-run"></a>トレーニングの実行を構成する

`ScriptRunConfig` コンストラクターの `arguments` パラメーターを介してマウントする場合は、データセットを引数として渡すことをお勧めします。 そうすることで、引数を介してトレーニング スクリプトのデータ パス (マウント ポイント) を取得します。 これにより、任意のクラウド プラットフォームで、ローカル デバッグとリモート トレーニングに、同じトレーニング スクリプトを使用できます。

次の例では、`arguments` を介して FileDataset を渡す ScriptRunConfig を作成します。 実行を送信すると、データセット `mnist_ds` によって参照されるデータ ファイルがコンピューティング先にマウントされ、トレーニング結果が既定のデータストア内の指定された `outputdataset` フォルダーに保存されます。

```python
from azureml.core import ScriptRunConfig

input_data= mnist_ds.as_named_input('input').as_mount()# the dataset will be mounted on the remote compute 

src = ScriptRunConfig(source_directory=script_folder,
                      script='dummy_train.py',
                      arguments=[input_data, output],
                      compute_target=compute_target,
                      environment=myenv)

# Submit the run configuration for your training run
run = experiment.submit(src)
run.wait_for_completion(show_output=True)
```

### <a name="simple-training-script"></a>単純なトレーニング スクリプト

次のスクリプトは、ScriptRunConfig を介して送信されます。 `mnist_ds ` データセットを入力として読み取って、既定の BLOB データストア `def_blob_store` 内の `outputdataset` フォルダーにファイルを書き込みます。

```Python
%%writefile $source_directory/dummy_train.py

# Copyright (c) Microsoft Corporation. All rights reserved.
# Licensed under the MIT License.
import sys
import os

print("*********************************************************")
print("Hello Azure ML!")

mounted_input_path = sys.argv[1]
mounted_output_path = sys.argv[2]

print("Argument 1: %s" % mounted_input_path)
print("Argument 2: %s" % mounted_output_path)
    
with open(mounted_input_path, 'r') as f:
    content = f.read()
    with open(os.path.join(mounted_output_path, 'output.csv'), 'w') as fw:
        fw.write(content)
```

## <a name="mount-vs-download"></a>マウントまたはダウンロード

任意の形式のファイルをダウンロードしたりマウントしたりすることは、Azure BLOB ストレージ、Azure Files、Azure Data Lake Storage Gen1、Azure Data Lake Storage Gen2、Azure SQL Database、および Azure Database for PostgreSQL から作成されたデータセットに対してサポートされています。 

データセットを **マウント** する場合は、データセットによって参照されているファイルをディレクトリ (マウント ポイント) に接続し、コンピューティング先で使用できるようにします。 Azure Machine Learning コンピューティング、仮想マシン、HDInsight など、Linux ベースのコンピューティングでは、マウントがサポートされています。 データ サイズがコンピューティング ディスクのサイズを超えると、ダウンロードできません。 このシナリオでは、処理時にスクリプトで使用されるデータ ファイルのみが読み込まれるため、マウントすることをお勧めします。

データセットを **ダウンロード** するとき、データセットによって参照されるすべてのファイルが、コンピューティング先にダウンロードされます。 すべてのコンピューティングの種類でダウンロードがサポートされています。 データセットによって参照されるファイルのすべてがスクリプトで処理され、コンピューティング ディスクが完全なデータセットに収まる場合は、ダウンロードによって、ストレージ サービスからのデータ ストリーミングのオーバーヘッドを回避することをお勧めします。 マルチノードのダウンロードについては、[調整を回避する方法](#troubleshooting)を参照してください。 

> [!NOTE]
> ダウンロード パス名は、Windows OS の場合は英数字で 255 文字を超えないようにしてください。 Linux OS の場合、ダウンロード パス名は英数字で 4,096 文字を超えないようにしてください。 また、Linux OS の場合、ファイル名 (ダウンロード パス `/path/to/file/{filename}` の最後のセグメント) は英数字で 255 文字を超えないようにしてください。

次のコードを実行すると、`dataset` が `mounted_path` の一時ディレクトリにマウントされます

```python
import tempfile
mounted_path = tempfile.mkdtemp()

# mount dataset onto the mounted_path of a Linux-based compute
mount_context = dataset.mount(mounted_path)

mount_context.start()

import os
print(os.listdir(mounted_path))
print (mounted_path)
```

## <a name="get-datasets-in-machine-learning-scripts"></a>Machine learning スクリプトでデータセットを取得する

登録されたデータセットは、Azure Machine Learning コンピューティングなどのコンピューティング クラスター上で、ローカルまたはリモートでアクセスできます。 複数の実験で登録済みデータセットにアクセスするには、以下のコードを使用してワークスペースにアクセスし、以前送信した実行で使用されたデータセットを取得します。 既定では、`Dataset` クラスの [`get_by_name()`](/python/api/azureml-core/azureml.core.dataset.dataset#get-by-name-workspace--name--version--latest--) メソッドからは、ワークスペースに登録されているデータセットの最新バージョンが返されます。

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

## <a name="access-source-code-during-training"></a>トレーニング中のソース コードへのアクセス

Azure BLOB ストレージは、スループット速度が Azure ファイル共有よりも高く、並列で開始される多数のジョブに対応します。 このため、ソース コード ファイルの転送については、BLOB ストレージを使用するように実行を構成することをお勧めします。

次のコード例では、実行構成で、ソース コード転送に使用する BLOB データストアを指定します。

```python 
# workspaceblobstore is the default blob storage
src.run_config.source_directory_data_store = "workspaceblobstore" 
```

## <a name="notebook-examples"></a>ノートブックの例

+ その他のデータセット例と概念については、[データセット ノートブック](https://aka.ms/dataset-tutorial)に関する記事を参照してください。
+ [ML パイプラインでデータセットをパラメーター化する](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-showcasing-dataset-and-pipelineparameter.ipynb)方法を参照してください。

## <a name="troubleshooting"></a>トラブルシューティング

**データセットの初期化に失敗しました:マウント ポイントの準備が完了するまで待っていましたがタイムアウトになりました**: 
  * アウトバウンド [ネットワーク セキュリティ グループ](../virtual-network/network-security-groups-overview.md)規則がなく、`azureml-sdk>=1.12.0` を使用している場合は、`azureml-dataset-runtime` とその依存関係を更新して、特定のマイナー バージョンの最新版にします。または、それを実行で使用している場合は、修正プログラムを含む最新パッチが適用されるように、お使いの環境を再作成します。 
  * `azureml-sdk<1.12.0` を使用している場合は、最新バージョンにアップグレードします。
  * アウトバウンド NSG 規則がある場合は、サービス タグ `AzureResourceMonitor` のすべてのトラフィックを許可するアウトバウンド規則があることを確認します。

**データセットの初期化に失敗しました: ThrottlingException によって StreamAccessException が発生しました**

マルチノード ファイルのダウンロードの場合は、すべてのノードが Azure Storage サービスからファイル データセット内のすべてのファイルをダウンロードしようとする可能性があり、それによって調整エラーが発生します。 調整を回避するには、まず環境変数 `AZUREML_DOWNLOAD_CONCURRENCY` を、CPU コアの数を 8 倍してノードの数で割った値に設定します。 この環境変数の値を設定するには何らかの実験が必要になる可能性があるため、上記のガイダンスは開始点にすぎません。

次の例では、32 個のコアと 4 つのノードを前提にしています。

```python
from azureml.core.environment import Environment 
myenv = Environment(name="myenv")
myenv.environment_variables = {"AZUREML_DOWNLOAD_CONCURRENCY":64}
```

### <a name="azurefile-storage"></a>AzureFile ストレージ

**Unable to upload project files to working directory in AzureFile because the storage is overloaded (ストレージが過負荷になっているため、プロジェクト ファイルを AzureFile 内の作業ディレクトリにアップロードできません)** :

* データ転送などの他のワークロードにファイル共有を使用している場合は、BLOB を使用して、ファイル共有を実行の送信のために自由に使用できるようにすることをお勧めします。

* もう 1 つの選択肢は、2 つの異なるワークスペース間でワークロードを分割することです。

**ConfigException: Could not create a connection to the AzureFileService due to missing credentials. Either an Account Key or SAS token needs to be linked the default workspace blob store. (ConfigException: 資格情報が見つからないので、AzureFileService への接続を作成できませんでした。アカウント キーまたは SAS トークンを、既定のワークスペース BLOB ストアにリンクする必要があります。)**

ストレージ アクセス資格情報がワークスペースおよび関連するファイル データストアにリンクされていることを確認するには、次の手順を実行します。

1. [Azure portal](https://ms.portal.azure.com) でワークスペースに移動します。
1. ワークスペースの **[概要]** ページでストレージ リンクを選択 します。
1. ストレージ ページの左側のメニューで **[アクセス キー]** を選択します。 
1. キーをコピーします。
1. ワークスペースの [Azure Machine Learning スタジオ](https://ml.azure.com)に移動します。
1. スタジオで、認証資格情報を指定するファイル データストアを選択します。 
1. **[Update authentication]\(認証の更新\)** を選択します。
1. 前の手順のキーを貼り付けます。 
1. **[保存]** を選択します。 

### <a name="passing-data-as-input"></a>入力としてのデータの引き渡し

**TypeError:FileNotFound:そのようなファイルまたはディレクトリはありません**:このエラーは、指定したファイル パスにファイルがない場合に発生します。 ファイルを参照する方法が、コンピューティング先でデータセットをマウントした場所と一致していることを確認する必要があります。 確定的な状態を確保するには、データセットをコンピューティング先にマウントするときに抽象パスを使用することをお勧めします。 たとえば、次のコードでは、コンピューティング先のファイルシステムのルート `/tmp` にデータセットをマウントしています。 
    
```python
# Note the leading / in '/tmp/dataset'
script_params = {
    '--data-folder': dset.as_named_input('dogscats_train').as_mount('/tmp/dataset'),
} 
```

先頭のスラッシュ "/" を含めない場合は、データセットをマウントする場所を示すために、コンピューティング先の作業ディレクトリをプレフィックスとして付ける必要があります (たとえば、`/mnt/batch/.../tmp/dataset`)。


## <a name="next-steps"></a>次のステップ

* TabularDatasets を使用して[機械学習モデルを自動的にトレーニングする](how-to-configure-auto-train.md#data-source-and-format)。

* FileDatasets を使用して[画像分類モデルをトレーニングする](https://aka.ms/filedataset-samplenotebook)。

* [パイプラインを使用してデータセットをトレーニングする](./how-to-create-machine-learning-pipelines.md)。