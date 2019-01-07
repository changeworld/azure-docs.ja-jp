---
title: ML パイプラインの作成、実行、追跡
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning SDK for Python で機械学習パイプラインを作成して実行します。  パイプラインは、データの準備、モデルのトレーニング、モデルのデプロイ、推論などの機械学習 (ML) フェーズを結び付けるワークフローの作成と管理に使用されます。
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: sgilley
ms.author: sanpil
author: sanpil
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 8478b6760921f4641cd214b1ff19cae9757b6d7e
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/12/2018
ms.locfileid: "53269044"
---
# <a name="create-and-run-a-machine-learning-pipeline-using-azure-machine-learning-sdk"></a>Azure Machine Learning SDK を使用して機械学習パイプラインを作成および実行する

この記事では、[Azure Machine Learning SDK](https://aka.ms/aml-sdk) を使用して[機械学習パイプライン](concept-ml-pipelines.md)を作成、公開、実行、追跡する方法について説明します。  パイプラインは、機械学習のさまざまなフェーズを結び付けるワークフローを作成して管理するのに役立ちます。 データの準備やモデルのトレーニングなど、パイプラインの各フェーズには、1 つまたは複数のステップを含めることができます。

作成したパイプラインは、Azure Machine Learning service [ワークスペース](how-to-manage-workspace.md)のメンバーであれば見ることができます。 

パイプラインでは、そのパイプラインに関連付けられている中間および最終データの計算と格納に、リモート コンピューティング先が使用されます。  パイプラインでは、サポートされている [Azure ストレージ](https://docs.microsoft.com/azure/storage/)の場所のデータを読み取ったり書き込んだりすることができます。

>[!Note]
>Azure サブスクリプションをお持ちでない場合は、開始する前に無料アカウントを作成してください。 [無料版または有料版の Azure Machine Learning service](http://aka.ms/AMLFree) を今日からお試しいただけます。

## <a name="prerequisites"></a>前提条件

* Azure Machine Learning SDK をインストールするための[開発環境を構成](how-to-configure-environment.md)します。

* すべてのパイプライン リソースを保持するための [Azure Machine Learning ワークスペース](how-to-configure-environment.md#workspace)を作成します。 

 ```python
 ws = Workspace.create(
     name = '<workspace-name>',
     subscription_id = '<subscription-id>',
     resource_group = '<resource-group>',
     location = '<workspace_region>',
     exist_ok = True)
 ```

## <a name="set-up-machine-learning-resources"></a>機械学習リソースをセットアップする

パイプラインの実行に必要なリソースを作成します。

* パイプラインの手順で必要なデータへのアクセスに使用されるデータストアを設定します。

* データストアに存在するデータまたはデータストアでアクセス可能なデータを指し示すように、`DataReference` オブジェクトを構成します。

* パイプラインの手順が実行される[コンピューティング先](concept-azure-machine-learning-architecture.md#compute-target)を設定します。

### <a name="set-up-a-datastore"></a>データストアをセットアップする
データストアには、パイプラインでアクセスするデータが格納されます。  各ワークスペースに既定のデータストアがあります。 データストアを追加登録できます。 

ワークスペースを作成すると、[Azure ファイル ストレージ](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)と [BLOB ストレージ](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction)が既定でワークスペースに関連付けられます。  Azure ファイル ストレージがワークスペースの "既定のデータストア" ですが、BLOB ストレージをデータストアとして使用することもできます。  [Azure ストレージ オブジェクト](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks)に関するページをご覧ください。 

```python
# Default datastore (Azure file storage)
def_data_store = ws.get_default_datastore() 

# The above call is equivalent to this 
def_data_store = Datastore(ws, "workspacefilestore")

# Get blob storage associated with the workspace
def_blob_store = Datastore(ws, "workspaceblobstore")
```

パイプラインからアクセスできるように、データ ファイルまたはディレクトリをデータストアにアップロードします。  この例では、BLOB ストレージ バージョンのデータストアを使用します。

```python
def_blob_store.upload_files(
    ["./data/20news.pkl"],
    target_path="20newsgroups", 
    overwrite=True)
```

パイプラインは、1 つまたは複数のステップで構成されます。  ステップは、コンピューティング先で実行される単位です。  ステップでは、データ ソースが使用されて、"中間" データが生成される場合があります。 ステップでは、モデルなどのデータ、モデル ファイルと依存ファイルを含むディレクトリ、一時データが作成されることがあります。  このデータは、パイプラインの後続の他のステップで使用できます。

### <a name="configure-data-reference"></a>データ参照を構成する

パイプラインでステップへの入力として参照できるデータ ソースを作成しました。 パイプライン内のデータ ソースは [DataReference](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference) オブジェクトによって表されます。 `DataReference` オブジェクトでは、データストアに存在するデータまたはデータストアからアクセス可能なデータが指し示されています。

```python
blob_input_data = DataReference(
    datastore=def_blob_store,
    data_reference_name="test_data",
    path_on_datastore="20newsgroups/20news.pkl")
```

中間データ (またはステップの出力) は、[PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) オブジェクトによって表されます。 `output_data1` は、ステップの出力として生成され、後続の 1 つまたは複数のステップの入力として使われます。  `PipelineData` では、ステップの間にデータの依存関係が導入され、パイプライン内に暗黙的な実行順序が作成されます。

```python
output_data1 = PipelineData(
    "output_data1",
    datastore=def_blob_store,
    output_name="output_data1")
```

### <a name="set-up-compute"></a>コンピューティングをセットアップする

Azure Machine Learning でのコンピューティング (またはコンピューティング先) とは、機械学習パイプラインで計算ステップを実行するマシンまたはクラスターのことです。 たとえば、ステップを実行するための Azure Machine Learning コンピューティングを作成できます。

```python
compute_name = "aml-compute"
 if compute_name in ws.compute_targets:
    compute_target = ws.compute_targets[compute_name]
    if compute_target and type(compute_target) is AmlCompute:
        print('Found compute target: ' + compute_name)
else:
    print('Creating a new compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size = vm_size, # NC6 is GPU-enabled
                                                                min_nodes = 1, 
                                                                max_nodes = 4)
     # create the compute target
    compute_target = ComputeTarget.create(ws, compute_name, provisioning_config)
    
    # Can poll for a minimum number of nodes and for a specific timeout. 
    # If no min node count is provided it will use the scale settings for the cluster
    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
    
     # For a more detailed view of current cluster status, use the 'status' property    
    print(compute_target.status.serialize())
```

## <a name="construct-your-pipeline-steps"></a>パイプラインのステップを構築する

パイプラインのステップを定義する準備が整いました。 Azure Machine Learning SDK では、多くの組み込みステップを使用できます。 最も基本的なステップは、指定したコンピューティング先で Python スクリプトを実行する `PythonScriptStep` です。

```python
trainStep = PythonScriptStep(
    script_name="train.py",
    arguments=["--input", blob_input_data, "--output", processed_data1],
    inputs=[blob_input_data],
    outputs=[processed_data1],
    compute_target=compute_target,
    source_directory=project_folder
)
```

ステップを定義した後は、それらのステップの一部または全部を使用してパイプラインを作成します。

>[!NOTE]
>ステップを定義するとき、またはパイプラインを構築するときに、ファイルまたはデータが Azure Machine Learning service にアップロードされることはありません。

```python
# list of steps to run
compareModels = [trainStep, extractStep, compareStep]

# Build the pipeline
pipeline1 = Pipeline(workspace=ws, steps=[compareModels])
```

## <a name="submit-the-pipeline"></a>パイプラインを送信する

パイプラインを送信すると、各ステップで依存関係がチェックされ、ソース ディレクトリとして指定されているフォルダーのスナップショットが Azure Machine Learning service にアップロードされます。  ソース ディレクトリを指定していない場合は、現在のローカル ディレクトリがアップロードされます。

```python
# Submit the pipeline to be run
pipeline_run1 = Experiment(ws, 'Compare_Models_Exp').submit(pipeline1)
```

初めてパイプラインを実行すると、次のようになります。

* ワークスペースに関連付けられた BLOB ストレージからコンピューティング先に、プロジェクトのスナップショットがダウンロードされます。
* パイプラインの各ステップに対応した Docker イメージがビルドされます。
* 各ステップの Docker イメージが、コンテナー レジストリからコンピューティング先にダウンロードされます。
* `DataReference` オブジェクトがステップで指定されている場合は、データ ストアがマウントされます。 マウントがサポートされていない場合は、代わりにデータがコンピューティング先にコピーされます。
* ステップの定義で指定されているコンピューティング先で、ステップが実行されます。 
* ログ、stdout と stderr、メトリック、ステップによって指定されている出力などの成果物が作成されます。 その後、これらの成果物がアップロードされて、ユーザーの既定のデータ ストアに保持されます。

![パイプラインとして実験を実行する](./media/how-to-create-your-first-pipeline/run_an_experiment_as_a_pipeline.png)

## <a name="publish-a-pipeline"></a>パイプラインを発行する

後で異なる入力を使用して実行するために、パイプラインを発行することができます。 パラメーターを受け入れるように既に発行されているパイプラインの REST エンドポイントの場合、発行する前にパイプラインをパラメーター化する必要があります。 

1. パイプライン パラメーターを作成するには、既定の値で [PipelineParameter](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.pipelineparameter?view=azure-ml-py) オブジェクトを使用します。

 ```python
 pipeline_param = PipelineParameter(
     name="pipeline_arg", 
     default_value=10)
 ```

2. 次のように、パイプラインのいずれかのステップにパラメーターとしてこの `PipelineParameter` オブジェクトを追加します。

 ```python
 compareStep = PythonScriptStep(
     script_name="compare.py",
     arguments=["--comp_data1", comp_data1, "--comp_data2", comp_data2, "--output_data", out_data3, "--param1", pipeline_param],
     inputs=[ comp_data1, comp_data2],
     outputs=[out_data3],    
     target=compute_target, 
     source_directory=project_folder)
 ```

3. 呼び出されるときにパラメーターを受け取るこのパイプラインを発行します。

```python
published_pipeline1 = pipeline1.publish(
    name="My_Published_Pipeline", 
    description="My Published Pipeline Description")
```

## <a name="run-a-published-pipeline"></a>発行されたパイプラインを実行する

発行されたすべてのパイプラインには、Python ではないクライアントなどの外部システムからパイプラインの実行を呼び出すための REST エンドポイントがあります。 このエンドポイントでは、バッチ スコアリングと再トレーニングのシナリオでの "管理された再現性" のための手段が提供されています。

前のパイプラインの実行を呼び出すには、「[AzureCliAuthentication class](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.azurecliauthentication?view=azure-ml-py)」(AzureCliAuthentication クラス) で説明されているように、Azure Active Directory 認証ヘッダー トークンが必要です

```python
response = requests.post(published_pipeline1.endpoint, 
    headers=aad_token, 
    json={"ExperimentName": "My_Pipeline",
        "ParameterAssignments": {"pipeline_arg": 20}})
```
## <a name="view-results"></a>結果の表示

すべてのパイプラインとその実行の詳細の一覧を表示します。
1. [Azure Portal](https://portal.azure.com/) にサインインします。  

1. [ワークスペースを表示](how-to-manage-workspace.md#view-a-workspace)して、パイプラインの一覧を検索します。
 ![機械学習パイプラインの一覧](./media/how-to-create-your-first-pipeline/list_of_pipelines.png)
 
1. 実行結果を表示する特定のパイプラインを選択します。

## <a name="next-steps"></a>次の手順
- [GitHub 上のこれらの Jupyter notebook](https://aka.ms/aml-pipeline-readme) を使用して、機械学習パイプラインをさらに調べます。
- [azureml-pipelines-core](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) パッケージおよび [azureml-pipelines-steps](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py) パッケージの SDK リファレンス ヘルプを読みます。

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]
