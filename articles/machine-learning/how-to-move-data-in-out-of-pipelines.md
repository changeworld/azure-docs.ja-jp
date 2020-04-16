---
title: ML パイプラインでのデータの入力と出力
titleSuffix: Azure Machine Learning
description: Azure Machine Learning パイプラインでデータを準備、使用、および生成する
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: laobri
author: lobrien
ms.date: 04/01/2020
ms.openlocfilehash: 3dd1a82bf7fad1f201f5c0f52af944ef44a3fdf9
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879664"
---
# <a name="moving-data-into-and-between-ml-pipeline-steps-python"></a>ML パイプラインのステップ間でのデータの移動 (Python)

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

データは機械学習パイプラインの中心となるものです。 この記事では、Azure Machine Learning パイプラインのステップ間でデータをインポート、変換、および移動するためのコードを示します。 Azure Machine Learning でデータがどのように動作するかの概要については、[Azure ストレージ サービスのデータへのアクセス](how-to-access-data.md)に関する記事を参照してください。 Azure Machine Learning パイプラインの利点と構造については、「[Azure Machine Learning パイプラインとは](concept-ml-pipelines.md)」を参照してください。

この記事では、次の操作方法について説明します。

- 既存のデータに `Dataset` オブジェクトを使用する
- ステップ内でデータにアクセスする
- トレーニングや検証のサブセットなど、`Dataset` データをサブセットに分割する
- 次のパイプライン ステップにデータを転送する `PipelineData` オブジェクトを作成する
- パイプライン ステップへの入力として `PipelineData` オブジェクトを使用する
- 永続化する `PipelineData` から新しい `Dataset` オブジェクトを作成する

## <a name="prerequisites"></a>前提条件

必要なものは次のとおりです。

- Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、開始する前に無料アカウントを作成してください。 [無料版または有料版の Azure Machine Learning](https://aka.ms/AMLFree) をお試しください。

- [Azure Machine Learning SDK for Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)、または [Azure Machine Learning Studio](https://ml.azure.com/) へのアクセス。

- Azure Machine Learning ワークスペース。
  
  [Azure Machine Learning ワークスペースを作成](how-to-manage-workspace.md)するか、Python SDK を介して既存のワークスペースを使用します。 `Workspace` および `Datastore` クラスをインポートし、関数 `from_config()` を使用してファイル `config.json` からサブスクリプション情報を読み込みます。 この関数により、既定で現在のディレクトリ内の JSON ファイルが検索されますが、`from_config(path="your/file/path")` を使用してパス パラメーターを指定してファイルを指すこともできます。

   ```python
   import azureml.core
   from azureml.core import Workspace, Datastore
        
   ws = Workspace.from_config()
   ```

- 一部の既存のデータ。 この記事では、[Azure Blob コンテナー](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview)の使用方法について簡単に説明します。

- 省略可能:既存の機械学習パイプライン ([Azure Machine Learning SDK を使用した機械学習パイプラインの作成および実行](how-to-create-your-first-pipeline.md)に関する記事に記載されているものなど)。

## <a name="use-dataset-objects-for-pre-existing-data"></a>既存のデータに `Dataset` オブジェクトを使用する 

パイプラインにデータを取り込む方法としては、[データセット](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py) オブジェクトを使用することをお勧めします。 `Dataset` オブジェクトは、ワークスペース全体で使用できる永続データを表します。

`Dataset` オブジェクトを作成および登録する方法は多数あります。 表形式データセットは、1つまたは複数のファイルで使用できる区切られたデータ用です。 ファイル データセットは、バイナリ データ (画像など) または解析するデータ用です。 `Dataset` オブジェクトを作成する最も簡単なプログラム的方法は、ワークスペース ストレージまたはパブリック URL で既存の Blob を使用することです。

```python
datastore = Datastore.get(workspace, 'training_data')
iris_dataset = Dataset.Tabular.from_delimited_files(DataPath(datastore, 'iris.csv'))

cats_dogs_dataset = Dataset.File.from_files(
    paths='https://download.microsoft.com/download/3/E/1/3E1C3F21-ECDB-4869-8368-6DEBA77B919F/kagglecatsanddogs_3367a.zip',
    archive_options=ArchiveOptions(archive_type=ArchiveType.ZIP, entry_glob='**/*.jpg')
)
```

各種のオプションとさまざまなソースからのデータセットの作成、それらの登録と Azure Machine Learning UI での確認、データ サイズがコンピューティング能力とどのように相互作用するかの理解、およびそれらのバージョン管理についての詳細は、「[Azure Machine Learning データセットを作成する](how-to-create-register-datasets.md)」を参照してください。 

### <a name="pass-datasets-to-your-script"></a>データセットをスクリプトに渡す

データセットのパスをスクリプトに渡すには、`Dataset` オブジェクトの `as_named_input()` メソッドを使用します。 生成された `DatasetConsumptionConfig` オブジェクトを引数としてスクリプトに渡すか、またはパイプライン スクリプトへの `inputs` 引数を使用して、`Run.get_context().input_datasets[]` を使ってデータセットを取得することができます。

名前付き入力を作成したら、アクセス モード (`as_mount()` または `as_download()`) を選択できます。 スクリプトがデータセット内のすべてのファイルを処理し、コンピューティング リソースのディスクがデータセットに対して十分な大きさである場合は、ダウンロード アクセス モードを選択することをお勧めします。 ダウンロード アクセス モードを使用すると、実行時のデータ ストリーミングのオーバーヘッドを回避できます。 スクリプトがデータセットのサブセットにアクセスする場合、またはそれがコンピューティングに対して大きすぎる場合は、マウント アクセス モードを使用します。 詳細については、「[マウントとダウンロード](https://docs.microsoft.com/azure/machine-learning/how-to-train-with-datasets#mount-vs-download)」をご覧ください

データセットをパイプライン ステップに渡すには、次の手順を実行します。

1. `TabularDataset.as_named_inputs()` または `FileDataset.as_named_input()` (末尾に ’s’ はありません) を使用して `DatasetConsumptionConfig` オブジェクトを作成します
1. `as_mount()` または `as_download()` を使用して、アクセス モードを設定します
1. `arguments` または `inputs` 引数のいずれかを使用して、データセットをパイプライン ステップに渡します

次のスニペットは、`PythonScriptStep` コンストラクター内でこれらのステップを組み合わせた一般的なパターンを示しています。 

```python

train_step = PythonScriptStep(
    name="train_data",
    script_name="train.py",
    compute_target=cluster,
    inputs=[iris_dataset.as_named_inputs('iris').as_mount()]
)
```

また、`random_split()` や `take_sample()` などのメソッドを使用して、複数の入力を作成したり、パイプライン ステップに渡されるデータの量を減らしたりすることもできます。

```python
seed = 42 # PRNG seed
smaller_dataset = iris_dataset.take_sample(0.1, seed=seed) # 10%
train, test = smaller_dataset.random_split(percentage=0.8, seed=seed)

train_step = PythonScriptStep(
    name="train_data",
    script_name="train.py",
    compute_target=cluster,
    inputs=[train.as_named_inputs('train').as_download(), test.as_named_inputs('test').as_download()]
)
```

### <a name="access-datasets-within-your-script"></a>スクリプト内でデータセットにアクセスする

パイプライン ステップ スクリプトへの名前付き入力は、`Run` オブジェクト内でディクショナリとして使用できます。 `Run.get_context()` を使用してアクティブな `Run` オブジェクトを取得し、`input_datasets` を使用して名前付き入力のディクショナリを取得します。 `inputs` 引数ではなく `arguments` 引数を使用して `DatasetConsumptionConfig` オブジェクトを渡した場合は、`ArgParser` コードを使用してデータにアクセスします。 次のスニペットでは、両方の手法が示されています。

```python
# In pipeline definition script:
# Code for demonstration only: It would be very confusing to split datasets between `arguments` and `inputs`
train_step = PythonScriptStep(
    name="train_data",
    script_name="train.py",
    compute_target=cluster,
    arguments=['--training-folder', train.as_named_inputs('train').as_download()]
    inputs=[test.as_named_inputs('test').as_download()]
)

# In pipeline script
parser = argparse.ArgumentParser()
parser.add_argument('--training-folder', type=str, dest='train_folder', help='training data folder mounting point')
args = parser.parse_args()
training_data_folder = args.train_folder

testing_data_folder = Run.get_context().input_datasets['test']
```

渡される値は、データセット ファイルのパスになります。

登録済みの `Dataset` に直接アクセスすることもできます。 登録済みのデータセットは永続的であり、ワークスペース全体で共有されるため、それらを直接取得できます。

```python
run = Run.get_context()
ws = run.experiment.workspace
ds = Dataset.get_by_name(workspace=ws, name='mnist_opendataset')
```

## <a name="use-pipelinedata-for-intermediate-data"></a>中間データに `PipelineData` を使用する

`Dataset` オブジェクトは永続データを表しますが、パイプラインのステップから出力される一時的なデータには [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) オブジェクトが使用されます。 `PipelineData` オブジェクトの有効期間は 1 つのパイプライン ステップより長くなるため、それらをパイプライン定義スクリプトで定義します。 `PipelineData` オブジェクトを作成するときは、名前と、データを格納するデータストアを指定する必要があります。 `arguments` と `outputs` の "_両方_" の引数を使用して、`PipelineData` オブジェクトを `PythonScriptStep` に渡します。

```python
default_datastore = workspace.get_default_datastore()
dataprep_output = PipelineData("clean_data", datastore=default_datastore)

dataprep_step = PythonScriptStep(
    name="prep_data",
    script_name="dataprep.py",
    compute_target=cluster,
    arguments=["--output-path", dataprep_output]
    inputs=[Dataset.get_by_name(workspace, 'raw_data')],
    outputs=[dataprep_output]
)
```

即時アップロードを提供するアクセス モードを使用して、`PipelineData` オブジェクトを作成することもできます。 その場合は、`PipelineData` を作成するときに、`upload_mode` を `"upload"` に設定し、`output_path_on_compute` 引数を使用して、データの書き込み先のパスを指定します。

```python
PipelineData("clean_data", datastore=def_blob_store, output_mode="upload", output_path_on_compute="clean_data_output/")
```

### <a name="use-pipelinedata-as-outputs-of-a-training-step"></a>トレーニング ステップの出力として `PipelineData` を使用する

パイプラインの `PythonScriptStep` 内で、プログラムの引数を使用して、使用可能な出力パスを取得できます。 このステップが最初のものであり、出力データを初期化する場合は、指定されたパスにディレクトリを作成する必要があります。 その後、`PipelineData` に含める任意のファイルを書き込むことができます。

```python
parser = argparse.ArgumentParser()
parser.add_argument('--output_path', dest='output_path', required=True)
args = parser.parse_args()

# Make directory for file
os.makedirs(os.path.dirname(args.output_path), exist_ok=True)
with open(args.output_path, 'w') as f:
    f.write("Step 1's output")
```

`is_directory` 引数を `True` に設定して `PipelineData` を作成した場合、`os.makedirs()` の呼び出しを実行するだけで十分であり、その後、パスに任意のファイルを自由に書き込むことができます。 詳しくは、[PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) のリファレンス ドキュメントをご覧ください。

### <a name="read-pipelinedata-as-inputs-to-non-initial-steps"></a>最初以外のステップへの入力として `PipelineData` を読み取る

最初のパイプライン ステップで `PipelineData` パスにデータが書き込まれ、それがその最初のステップの出力になった場合、それを後のステップへの入力として使用できます。

```python
step1_output_data = PipelineData("processed_data", datastore=def_blob_store, output_mode="upload")

step1 = PythonScriptStep(
    name="generate_data",
    script_name="step1.py",
    runconfig = aml_run_config,
    arguments = ["--output_path", step1_output_data],
    inputs=[],
    outputs=[step1_output_data]
)

step2 = PythonScriptStep(
    name="read_pipeline_data",
    script_name="step2.py",
    compute_target=compute,
    runconfig = aml_run_config,
    arguments = ["--pd", step1_output_data],
    inputs=[step1_output_data]
)

pipeline = Pipeline(workspace=ws, steps=[step1, step2])
```

`PipelineData` 入力の値は、前の出力へのパスです。 前に示したように、最初のステップで 1 つのファイルを書き込んだ場合、それは次のように使用されます。 

```python
parser = argparse.ArgumentParser()
parser.add_argument('--pd', dest='pd', required=True)
args = parser.parse_args()

with open(args.pd) as f:
    print(f.read())
```

## <a name="convert-pipelinedata-objects-to-datasets"></a>`PipelineData` オブジェクトを `Dataset` に変換する

`PipelineData` を実行時間よりも長く使用できるようにする場合は、`as_dataset()` 関数を使用して、それを `Dataset` に変換します。 その後、`Dataset` を登録し、それをワークスペース内の第一級オブジェクトにすることができます。 `PipelineData` オブジェクトのパスは、パイプラインを実行するたびに異なるため、`PipelineData` オブジェクトから作成された `Dataset` を登録する場合は、`create_new_version` を `True` に設定することを強くお勧めします。

```python
step1_output_ds = step1_output_data.as_dataset()
step1_output_ds.register(name="processed_data", create_new_version=True)
```

## <a name="next-steps"></a>次のステップ

* [Azure Machine Learning データセットを作成する](how-to-create-register-datasets.md)
* [Azure Machine Learning SDK で機械学習パイプラインを作成して管理する](how-to-create-your-first-pipeline.md)
