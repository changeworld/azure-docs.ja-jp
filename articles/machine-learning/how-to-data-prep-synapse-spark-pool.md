---
title: Apache Spark プールを使用したデータ ラングリング (プレビュー)
titleSuffix: Azure Machine Learning
description: Azure Synapse Analytics と Azure Machine Learning のデータ ラングリング用に Apache Spark プールをアタッチして起動する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: mldata
ms.topic: how-to
ms.author: nibaccam
author: nibaccam
ms.reviewer: nibaccam
ms.date: 10/21/2021
ms.custom: devx-track-python, data4ml, synapse-azureml, contperf-fy21q4
ms.openlocfilehash: 711521554e30b2905184150fa631ce92390534dd
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131564387"
---
# <a name="data-wrangling-with-apache-spark-pools-preview"></a>Apache Spark プールを使用したデータ ラングリング (プレビュー) 

この記事では 、[Azure Machine Learning Python SDK](/python/api/overview/azure/ml/) を使用して Jupyter Notebook で [Azure Synapse Analytics](../synapse-analytics/overview-what-is.md) を利用した専用 Synapse セッション内で、データ ラングリング タスクを対話的に実行する方法について説明します。 

Azure Machine Learning パイプラインを使用する場合は、「[機械学習パイプライン内で (Azure Synapse Analytics で実行される) Apache Spark を使用する方法 (プレビュー)](how-to-use-synapsesparkstep.md)」を参照してください。

Synapse ワークスペースで Azure Synapse Analytics を使用する方法のガイダンスについては、[Azure Synapse Analytics の概要シリーズ](../synapse-analytics/get-started.md)に関するページを参照してください。

>[!IMPORTANT]
> Azure Machine Learning と Azure Synapse Analytics の統合はプレビュー段階です。 この記事で紹介している機能には、[試験的](/python/api/overview/azure/ml/#stable-vs-experimental)なプレビュー機能を含んだ `azureml-synapse` パッケージが採用されており、それらは随時変更される可能性があります。

## <a name="azure-machine-learning-and-azure-synapse-analytics-integration"></a>Azure Machine Learning と Azure Synapse Analytics の統合

Azure Synapse Analytics と Azure Machine Learning の統合 (プレビュー) によって、Azure Synapse によってサポートされる Apache Spark プールをアタッチし、インタラクティブなデータ探索とデータ準備を行うことができます。 この統合により、大規模なデータ ラングリングを行うための専用のコンピューティングが得られ、機械学習モデルのトレーニングにも使用する Python ノートブック内からそのすべてを利用することができます。

## <a name="prerequisites"></a>前提条件

* [Azure Machine Learning Python SDK がインストールされている](/python/api/overview/azure/ml/install)こと。 

* [Azure Machine Learning ワークスペースを作成](how-to-manage-workspace.md?tabs=python)します。

* [Azure portal で Azure Synapse Analytics ワークスペースを作成する](../synapse-analytics/quickstart-create-workspace.md)。

* [Azure portal、Web ツール、または Synapse Studio を使用して Apache Spark プールを作成します](../synapse-analytics/quickstart-create-apache-spark-pool-portal.md)。

* [開発環境を構成](how-to-configure-environment.md)して Azure Machine Learning SDK をインストールするか、SDK が既にインストールされている [Azure Machine Learning コンピューティング インスタンス](concept-compute-instance.md#create)を使用します。 

* 次のコードを使用して、`azureml-synapse` パッケージ (プレビュー) をインストールします。

  ```python
  pip install azureml-synapse
  ```

* [Azure Machine Learning Python SDK](how-to-link-synapse-ml-workspaces.md#link-sdk) または [Azure Machine Learning スタジオ](how-to-link-synapse-ml-workspaces.md#link-studio)を使用して Azure Machine Learning ワークスペースと Azure Synapse Analytics ワークスペースをリンクさせます

* [Synapse Spark プール](how-to-link-synapse-ml-workspaces.md#attach-synapse-spark-pool-as-a-compute)をコンピューティング先としてアタッチします。

## <a name="launch-synapse-spark-pool-for-data-wrangling-tasks"></a>データ ラングリング タスク用に Synapse Spark プールを起動する

Apache Spark プールを使用したデータ準備を開始するには、Apache Spark Synapse のコンピューティング名を指定します。 この名前は、 **[アタッチされたコンピューティング]** タブの下の Azure Machine Learning スタジオ を使用して確認できます。 

![get attached compute name](media/how-to-data-prep-synapse-spark-pool/attached-compute.png)

> [!IMPORTANT]
> 引き続き Apache Spark プールを使用するためには、データ ラングリング タスクで使用するコンピューティング リソースを `%synapse` (1 行のコードの場合) および `%%synapse` (複数行の場合) で指定する必要があります。 [%synapse マジック コマンドの詳細については、こちらをご覧ください](/python/api/azureml-synapse/azureml.synapse.magics.remotesynapsemagics(class))。

```python
%synapse start -c SynapseSparkPoolAlias
```

セッションの開始後、セッションのメタデータを確認できます。

```python
%synapse meta
```

Apache Spark セッション中に使用する [Azure Machine Learning 環境](concept-environments.md)を指定できます。 この環境に指定された Conda 依存関係のみが有効になります。 Docker イメージはサポートされません。

>[!WARNING]
>  環境 Conda 依存関係に指定された Python 依存関係は、Apache Spark プールではサポートされません。 現在サポートされるのは、固定の Python バージョンのみです。 スクリプトに `sys.version_info` を追加して Python バージョンを確認してください。

次のコードでは、セッションの開始前に `azureml-core` バージョン 1.20.0 および `numpy` バージョン 1.17.0 をインストールする環境 (`myenv`) を作成します。 その後、Apache Spark セッションの `start` ステートメントにこの環境を含めることができます。

```python

from azureml.core import Workspace, Environment

# creates environment with numpy and azureml-core dependencies
ws = Workspace.from_config()
env = Environment(name="myenv")
env.python.conda_dependencies.add_pip_package("azureml-core==1.20.0")
env.python.conda_dependencies.add_conda_package("numpy==1.17.0")
env.register(workspace=ws)
```

Apache Spark プールとカスタム環境を使用したデータ準備を開始するには、Apache Spark プールの名前と Apache Spark セッション中に使用する環境を指定します。 さらに、サブスクリプション ID、機械学習ワークスペースのリソース グループ、機械学習ワークスペースの名前を指定することもできます。

>[!IMPORTANT]
> リンクされた Synapse ワークスペースで [[セッション レベルのパッケージを許可する]](../synapse-analytics/spark/apache-spark-manage-python-packages.md#session-scoped-packages) が有効になっている必要があります。
>
>![セッション レベルのパッケージを有効にする](media/how-to-data-prep-synapse-spark-pool/enable-session-level-package.png)

```python
%synapse start -c SynapseSparkPoolAlias -e myenv -s AzureMLworkspaceSubscriptionID -r AzureMLworkspaceResourceGroupName -w AzureMLworkspaceName
```

## <a name="load-data-from-storage"></a>ストレージからデータを読み込む

Apache Spark セッションが開始されたら、準備するデータを読み取ります。 データの読み込みは、Azure Blob Storage および Azure Data Lake Storage Generation 1 および 2 でサポートされます。

これらのストレージ サービスからデータを読み込むには、2 とおりの方法があります。 

* 対応する Hadoop Distributed Files System (HDFS) パスを使用して、ストレージから直接データを読み込みます。

* 既存の [Azure Machine Learning データセット](how-to-create-register-datasets.md)からデータを読み取ります。

これらのストレージ サービスにアクセスするには、**ストレージ BLOB データ閲覧者** のアクセス許可が必要です。 これらのストレージ サービスにデータを書き戻す予定がある場合は、**ストレージ BLOB データ共同作成者** のアクセス許可が必要となります。 [詳細については、ストレージのアクセス許可とロールに関するページを参照](../storage/blobs/assign-azure-role-data-access.md)してください。

### <a name="load-data-with-hadoop-distributed-files-system-hdfs-path"></a>Hadoop Distributed Files System (HDFS) パスを使用してデータを読み込む

対応する HDFS パスを使用して、ストレージからのデータの読み込みと読み取りを行うには、データ アクセスの認証資格情報を手元に用意しておく必要があります。 これらの資格情報は、ストレージの種類によって異なります。  

次のコードは、Shared Access Signature (SAS) トークンまたはアクセス キーを使用して、**Azure Blob Storage** から Spark データフレームにデータを読み取る方法を示しています。 

```python
%%synapse

# setup access key or SAS token
sc._jsc.hadoopConfiguration().set("fs.azure.account.key.<storage account name>.blob.core.windows.net", "<access key>")
sc._jsc.hadoopConfiguration().set("fs.azure.sas.<container name>.<storage account name>.blob.core.windows.net", "<sas token>")

# read from blob 
df = spark.read.option("header", "true").csv("wasbs://demo@dprepdata.blob.core.windows.net/Titanic.csv")
```

次のコードは、サービス プリンシパルの資格情報を使用して、**Azure Data Lake Storage Generation 1 (ADLS Gen 1)** からデータを読み取る方法を示しています。 

```python
%%synapse

# setup service principal which has access of the data
sc._jsc.hadoopConfiguration().set("fs.adl.account.<storage account name>.oauth2.access.token.provider.type","ClientCredential")

sc._jsc.hadoopConfiguration().set("fs.adl.account.<storage account name>.oauth2.client.id", "<client id>")

sc._jsc.hadoopConfiguration().set("fs.adl.account.<storage account name>.oauth2.credential", "<client secret>")

sc._jsc.hadoopConfiguration().set("fs.adl.account.<storage account name>.oauth2.refresh.url",
"https://login.microsoftonline.com/<tenant id>/oauth2/token")

df = spark.read.csv("adl://<storage account name>.azuredatalakestore.net/<path>")

```

次のコードは、サービス プリンシパルの資格情報を使用して、**Azure Data Lake Storage Generation 2 (ADLS Gen 2)** からデータを読み取る方法を示しています。 

```python
%%synapse

# setup service principal which has access of the data
sc._jsc.hadoopConfiguration().set("fs.azure.account.auth.type.<storage account name>.dfs.core.windows.net","OAuth")
sc._jsc.hadoopConfiguration().set("fs.azure.account.oauth.provider.type.<storage account name>.dfs.core.windows.net", "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider")
sc._jsc.hadoopConfiguration().set("fs.azure.account.oauth2.client.id.<storage account name>.dfs.core.windows.net", "<client id>")
sc._jsc.hadoopConfiguration().set("fs.azure.account.oauth2.client.secret.<storage account name>.dfs.core.windows.net", "<client secret>")
sc._jsc.hadoopConfiguration().set("fs.azure.account.oauth2.client.endpoint.<storage account name>.dfs.core.windows.net",
"https://login.microsoftonline.com/<tenant id>/oauth2/token")

df = spark.read.csv("abfss://<container name>@<storage account>.dfs.core.windows.net/<path>")

```

### <a name="read-in-data-from-registered-datasets"></a>登録済みデータセットからデータを読み取る

ワークスペースで既存の登録済みデータセットを取得し、それを Spark データフレームに変換することによってデータ準備を行うこともできます。

次の例では、ワークスペースに対して認証し、Blob Storage 内のファイルを参照する登録済みの TabularDataset (`blob_dset`) を取得し、それを Spark データフレームに変換します。 Spark データフレームにデータセットを変換する際は、`pyspark` のデータ探索ライブラリおよびデータ準備ライブラリを活用できます。  

``` python
%%synapse

from azureml.core import Workspace, Dataset

subscription_id = "<enter your subscription ID>"
resource_group = "<enter your resource group>"
workspace_name = "<enter your workspace name>"

ws = Workspace(workspace_name = workspace_name,
               subscription_id = subscription_id,
               resource_group = resource_group)

dset = Dataset.get_by_name(ws, "blob_dset")
spark_df = dset.to_spark_dataframe()
```

## <a name="perform-data-wrangling-tasks"></a>データ ラングリング タスクを実行する

データを取得して調査した後、データ ラングリング タスクを実行することができます。

次のコードは、前セクションで見た HDFS の例を応用し、**Survivor** 列に基づいて Spark データフレーム (`df`) のデータをフィルター選択し、そのリストを **Age** でグループ化します。

```python
%%synapse

from pyspark.sql.functions import col, desc

df.filter(col('Survived') == 1).groupBy('Age').count().orderBy(desc('count')).show(10)

df.show()

```

## <a name="save-data-to-storage-and-stop-spark-session"></a>データをストレージに保存して Spark セッションを停止する

データ探索とデータ準備が完了したら、後で使用できるよう準備済みのデータを Azure 上のストレージ アカウントに保存します。

次の例では、準備済みのデータを Azure Blob Storage に書き戻し、`training_data` ディレクトリにある元の `Titanic.csv` ファイルを上書きします。 ストレージへの書き戻しを行うには、**ストレージ BLOB データ共同作成者** のアクセス許可が必要となります。 [詳細については、ストレージのアクセス許可とロールに関するページを参照](../storage/blobs/assign-azure-role-data-access.md)してください。

```python
%% synapse

df.write.format("csv").mode("overwrite").save("wasbs://demo@dprepdata.blob.core.windows.net/training_data/Titanic.csv")
```

データ準備が完了し、準備済みのデータをストレージに保存したら、次のコマンドを使用して Apache Spark プールの使用を停止します。

```python
%synapse stop
```

## <a name="create-dataset-to-represent-prepared-data"></a>準備済みのデータを表すデータセットを作成する

準備済みのデータをモデルのトレーニングに利用する際は、[Azure Machine Learning データストア](how-to-access-data.md)にストレージを接続し、[Azure Machine Learning データセット](how-to-create-register-datasets.md)で使用するファイルを指定します。

以下のコード例は、次の処理を行います。

* 準備済みのデータを保存したストレージ サービスに接続するデータストアが作成済みであることを前提としています。  
* その既存のデータストア (`mydatastore`) を、get() メソッドでワークスペース (`ws`) から取得します。
* `mydatastore` の `training_data` ディレクトリに格納されている準備済みのデータ ファイルを参照する [FileDataset](how-to-create-register-datasets.md#filedataset) (`train_ds`) を作成します。  
* 変数 `input1` を作成します。後でこの変数を使用して、コンピューティング先からトレーニング タスクで利用できる `train_ds` データセットのデータ ファイルを作成できます。

```python
from azureml.core import Datastore, Dataset

datastore = Datastore.get(ws, datastore_name='mydatastore')

datastore_paths = [(datastore, '/training_data/')]
train_ds = Dataset.File.from_files(path=datastore_paths, validate=True)
input1 = train_ds.as_mount()

```

## <a name="use-a-scriptrunconfig-to-submit-an-experiment-run-to-a-synapse-spark-pool"></a>`ScriptRunConfig` を使用して Synapse Spark プールに実験の実行を送信する

データ ラングリング タスクを自動化して作成する準備ができたら、[ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig) オブジェクトを使用して、[アタッチした Synapse Spark プール](how-to-link-synapse-ml-workspaces.md#attach-a-pool-with-the-python-sdk)に実験の実行を送信できます。  

同様に、Azure Machine Learning パイプラインがある場合は、[SynapseSparkStep を使用して、パイプラインのデータ準備手順のコンピューティング先](how-to-use-synapsesparkstep.md)として Synapse Spark プールを指定できます。

データを Synapse Spark プールで使用できるようにする方法は、データセットの種類によって異なります。 

* FileDataset の場合は、[`as_hdfs()`](/python/api/azureml-core/azureml.data.filedataset#as-hdfs--) メソッドを使用できます。 実行が送信されると、データセットは Hadoop 分散ファイル システム (HFDS) として Synapse Spark プールで使用できるようになります。 
* [TabularDataset](how-to-create-register-datasets.md#tabulardataset) の場合は、[`as_named_input()`](/python/api/azureml-core/azureml.data.abstract_dataset.abstractdataset#as-named-input-name-) メソッドを使用できます。 

次のコードによって、以下の処理が実行されます。 

* 前のコード例で作成した FileDataset `train_ds` から変数 `input2` を作成します。
* HDFSOutputDatasetConfiguration クラスを使用して変数 `output` を作成します。 実行が完了すると、このクラスによって、実行の出力をデータセット `test` としてデータストア `mydatastore` に保存できるようになります。 [Azure Machine Learning] ワークスペースでは、`test` データセットは `registered_dataset` という名前で登録されます。 
* Synapse Spark プールでの実行のために、実行で使用される設定を構成します。 
* 以下のために、ScriptRunConfig パラメーターを定義します。 
  * 実行のために `dataprep.py` を使用します。 
  * 入力として使用するデータと、それを Synapse Spark プールで使用できるようにする方法を指定します。
  * 出力データを格納する場所 `output` を指定します。  

```Python
from azureml.core import Dataset, HDFSOutputDatasetConfig
from azureml.core.environment import CondaDependencies
from azureml.core import RunConfiguration
from azureml.core import ScriptRunConfig 
from azureml.core import Experiment

input2 = train_ds.as_hdfs()
output = HDFSOutputDatasetConfig(destination=(datastore, "test").register_on_complete(name="registered_dataset")

run_config = RunConfiguration(framework="pyspark")
run_config.target = synapse_compute_name

run_config.spark.configuration["spark.driver.memory"] = "1g" 
run_config.spark.configuration["spark.driver.cores"] = 2 
run_config.spark.configuration["spark.executor.memory"] = "1g" 
run_config.spark.configuration["spark.executor.cores"] = 1 
run_config.spark.configuration["spark.executor.instances"] = 1 

conda_dep = CondaDependencies()
conda_dep.add_pip_package("azureml-core==1.20.0")

run_config.environment.python.conda_dependencies = conda_dep

script_run_config = ScriptRunConfig(source_directory = './code',
                                    script= 'dataprep.py',
                                    arguments = ["--file_input", input2,
                                                 "--output_dir", output],
                                    run_config = run_config)
```

`ScriptRunConfig` オブジェクトが設定されたら、実行を送信できます。

```python
from azureml.core import Experiment 

exp = Experiment(workspace=ws, name="synapse-spark") 
run = exp.submit(config=script_run_config) 
run
```

この例で使用した `dataprep.py` スクリプトのような、その他の詳細については、[サンプルの Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-synapse/spark_job_on_synapse_spark_pool.ipynb) のページを参照してください。

データの準備が完了すると、データをトレーニング ジョブの入力として使用できるようになります。 前述のコード例では、トレーニング ジョブの入力データとして `registered_dataset` を指定しています。 

## <a name="example-notebooks"></a>サンプルの Notebook

サンプルの Notebook で、Azure Synapse Analytics と Azure Machine Learning の統合機能の詳細な概念とデモをご覧ください。
* [Azure Machine Learning ワークスペース内の Notebook から対話型の Spark セッションを実行します](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-synapse/spark_session_on_synapse_spark_pool.ipynb)。
* [コンピューティング先として Synapse Spark プールを使用して Azure Machine Learning 実験の実行を送信します](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-synapse/spark_job_on_synapse_spark_pool.ipynb)。

## <a name="next-steps"></a>次のステップ

* [モデルをトレーニングします](how-to-set-up-training-targets.md)。
* [Azure Machine Learning データセットをトレーニングします](how-to-train-with-datasets.md)。
