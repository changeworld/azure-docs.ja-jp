---
title: Spark プールを使用したデータ準備 (プレビュー)
titleSuffix: Azure Machine Learning
description: Azure Synapse と Azure Machine Learning のデータ準備用に Spark プールをアタッチする方法について説明します
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: nibaccam
author: nibaccam
ms.reviewer: nibaccam
ms.date: 03/02/2021
ms.custom: how-to, devx-track-python, data4ml
ms.openlocfilehash: 87e03b6aee122c5a26d4388ca8b570aa6cdf7b55
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/02/2021
ms.locfileid: "101660526"
---
# <a name="attach-synapse-spark-pools-for-data-preparation-with-azure-synapse-preview"></a>Azure Synapse のデータ準備用に Synapse Spark プールをアタッチする (プレビュー)

この記事では、[Azure Synapse](/synapse-analytics/overview-what-is.md) の Apache Spark プールをアタッチ、起動してデータ準備を行う方法について説明します。 

>[!IMPORTANT]
> Azure Machine Learning と Azure Synapse の統合はプレビュー段階です。 この記事で紹介している機能には、[試験的](/python/api/overview/azure/ml/?preserve-view=true&view=azure-ml-py#stable-vs-experimental)なプレビュー機能を含んだ `azureml-synapse` パッケージが採用されており、それらは随時変更される可能性があります。

## <a name="azure-machine-learning-and-azure-synapse-integration-preview"></a>Azure Machine Learning と Azure Synapse の統合 (プレビュー)

Azure Synapse と Azure Machine Learning の統合 (プレビュー) によって、Azure Synapse の Apache Spark プールをアタッチし、インタラクティブなデータ探索とデータ準備を行うことができます。 この統合により、大規模なデータ準備を行うための専用のコンピューティングが得られ、機械学習モデルのトレーニングにも使用する Python ノートブック内からそのすべてを利用することができます。

## <a name="prerequisites"></a>前提条件

* [Azure Machine Learning ワークスペースを作成](how-to-manage-workspace.md?tabs=python)します。

* [Azure portal で Synapse ワークスペースを作成](../synapse-analytics/quickstart-create-workspace.md)します。

* [Azure portal、Web ツール、Synapse Studio のいずれかを使用して Apache Spark プールを作成](../synapse-analytics/quickstart-create-apache-spark-pool-portal.md)します

* `azureml-synapse` パッケージ (プレビュー) を含む [Azure Machine Learning Python SDK をインストール](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py)します。 
    * これをご自身でインストールすることもできますが、互換性があるのは、SDK バージョン 1.20 以降のみです。 
        ```python
        pip install azureml-synapse
        ```

## <a name="link-machine-learning-workspace-and-synapse-assets"></a>Machine Learning ワークスペースと Synapse 資産をリンクさせる

データ準備用の Synapse Spark プールをアタッチする前に、Azure Machine Learning ワークスペースを Azure Synapse ワークスペースにリンクさせる必要があります。 

ML ワークスペースと Synapse ワークスペースのリンクは、[Python SDK](#link-sdk) または [Azure Machine Learning スタジオ](#link-studio)から行うことができます。 

> [!IMPORTANT]
> Synapse ワークスペースを正常にリンクさせるには、Synapse ワークスペースの **所有者** ロールが付与されている必要があります。 [Azure portal](https://ms.portal.azure.com/) でご自身のアクセス権を確認してください。
>
> Synapse ワークスペースの **所有者** 以外で、既にあるリンクされたサービスの利用を希望される方は、「[既にあるリンクされたサービスを取得する](#get-an-existing-linked-service)」を参照してください。


<a name="link-sdk"></a>
### <a name="link-workspaces-with-the-python-sdk"></a>Python SDK にワークスペースをリンクさせる

以下のコードでは、次の目的に [`LinkedService`](/python/api/azureml-core/azureml.core.linked_service.linkedservice?preserve-view=true&view=azure-ml-py) クラスと [`SynapseWorkspaceLinkedServiceConfiguration`](/python/api/azureml-core/azureml.core.linked_service.synapseworkspacelinkedserviceconfiguration?preserve-view=true&view=azure-ml-py) クラスを使用しています。 

* Machine Learning ワークスペース (`ws`) を Azure Synapse ワークスペースにリンクさせる。 
* リンクされたサービスとして Azure Machine Learning に Synapse ワークスペースを登録する。

``` python
import datetime  
from azureml.core import Workspace, LinkedService, SynapseWorkspaceLinkedServiceConfiguration

# Azure Machine Learning workspace
ws = Workspace.from_config()

#link configuration 
synapse_link_config = SynapseWorkspaceLinkedServiceConfiguration(
    subscription_id=ws.subscription_id,
    resource_group= 'your resource group',
    name='mySynapseWorkspaceName')

# Link workspaces and register Synapse workspace in Azure Machine Learning
linked_service = LinkedService.register(workspace = ws,              
                                            name = 'synapselink1',    
                                            linked_service_config = synapse_link_config)
```
> [!IMPORTANT] 
> リンクされたサービスごとにマネージド ID (`system_assigned_identity_principal_id`) が作成されます。 このマネージド ID には、Synapse セッションを開始する前に、Synapse ワークスペースの **Synapse Apache Spark 管理者** ロールが付与されている必要があります。 [Synapse Studio のマネージド ID に Synapse Apache Spark 管理者ロールを割り当てます](../synapse-analytics/security/how-to-manage-synapse-rbac-role-assignments.md)。
>
> 特定のリンクされたサービスの `system_assigned_identity_principal_id` を見つけるには、`LinkedService.get('<your-mlworkspace-name>', '<linked-service-name>')` を使用します。

<a name="link-studio"></a>
### <a name="link-workspaces-via-studio"></a>スタジオを使用してワークスペースをリンクさせる

Azure Machine Learning スタジオを使用して Machine Learning ワークスペースと Synapse ワークスペースをリンクさせるには、次の手順に従います。 

1. [Azure Machine Learning スタジオ](https://ml.azure.com/)にサインインします。
1. 左ペインの **[管理]** セクションにある **[リンクされたサービス]** を選択します。
1. **[Add integration]\(統合の追加\)** を選択します。
1. **[Link workspace]\(ワークスペースのリンク\)** フォームの各フィールドの内容を入力します。

   |フィールド| 説明    
   |---|---
   |名前| リンクされたサービスの名前を入力します。 この特定のリンクされたサービスを参照する際に、この名前を使用します。
   |サブスクリプション名 | Machine Learning ワークスペースに関連付けられているサブスクリプションの名前を選択します。 
   |Synapse ワークスペース | リンク先の Synapse ワークスペースを選択します。 
   
1. **[次へ]** を選択して **[Select Spark pools (optional)]\(Spark プールの選択 (省略可)\)** フォームを開きます。 このフォームで、ワークスペースにアタッチする Synapse Spark プールを選択します。

1. **[次へ]** を選択して **[Review]\(レビュー\)** フォームを開き、選択内容を確認します。 
1. **[作成]** を選択して、リンクされたサービスの作成プロセスを完了します。

## <a name="get-an-existing-linked-service"></a>既にあるリンクされたサービスを取得する

既にあるリンクされたサービスを取得して使用するためには、Synapse ワークスペースに対する **ユーザーまたは共同作成者** のアクセス許可が必要です。

この例では、[`get()`](/python/api/azureml-core/azureml.core.linkedservice?preserve-view=true&view=azure-ml-py#get-workspace--name-) メソッドを使用して、既にあるリンクされたサービス (`synapselink1`) をワークスペース (`ws`) から取得します。
```python
linked_service = LinkedService.get(ws, 'synapselink1')
```

### <a name="manage-linked-services"></a>リンクされたサービスの管理

ワークスペースのリンクを解除するには、`unregister()` メソッドを使用します

``` python
linked_service.unregister()
```

Machine Learning ワークスペースに関連付けられているリンクされたサービスをすべて表示します。 

```python
LinkedService.list(ws)
```
 
## <a name="attach-synapse-spark-pool-as-a-compute"></a>Synapse Spark プールをコンピューティングとしてアタッチする

ワークスペースがリンクされたら、データ準備タスクの専用のコンピューティング リソースとして Synapse Spark プールをアタッチします。 

Synapse Spark プールは、次の手段でアタッチできます。
* Azure Machine Learning Studio
* [Azure Resource Manager (ARM) テンプレート](https://github.com/Azure/azure-quickstart-templates/blob/master/101-machine-learning-linkedservice-create/azuredeploy.json)
* Python SDK 

スタジオを使用して Synapse Spark プールをアタッチするには、次の手順に従います。 

1. [Azure Machine Learning スタジオ](https://ml.azure.com/)にサインインします。
1. 左ペインの **[管理]** セクションにある **[リンクされたサービス]** を選択します。
1. Synapse ワークスペースを選択します。
1. 左上の **[Attached Spark pools]\(アタッチされた Spark プール\)** を選択します。 
1. **[接続]** を選択します。 
1. Synapse Spark プールを一覧から選択し、名前を入力します。  
    1. コンピューティングにアタッチできる使用可能な Synapse Spark プールが、この一覧によって認識されます。 
    1. 新しい Synapse Spark プールを作成するには、[Synapse Studio を使用して Apache Spark プールを作成する](../synapse-analytics/quickstart-create-apache-spark-pool-portal.md)方法に関するページを参照してください。
1. **[Attach selected]\(選択されたアタッチ\)** を選択します。 


**Python SDK** を使用して Synapse Spark プールをアタッチすることもできます。 

以下のコードは、次の処理を実行します。 
1. 次の情報を使用して SynapseCompute を構成します。

   1. 前の手順で作成または取得した LinkedService (`linked_service`)。 
   1. アタッチするコンピューティング先の種類 (`SynapseSpark`)
   1. Synapse Spark プールの名前。 これは、Synapse ワークスペースにある既存の Apache Spark プールと一致している必要があります。
   
1. 次の情報を渡して機械学習の ComputeTarget を作成します。 
   1. 使用する Machine Learning ワークスペース (`ws`)
   1. Machine Learning ワークスペース内でコンピューティングを参照する際の名前。 
   1. SynapseCompute を構成する際に指定した attach_configuration。
       1. ComputeTarget.attach() の呼び出しは非同期であるため、このサンプルは、呼び出しが完了するまでブロック状態になります。

```python
from azureml.core.compute import SynapseCompute, ComputeTarget

attach_config = SynapseCompute.attach_configuration(linked_service, #Linked synapse workspace alias
                                                    type='SynapseSpark', #Type of assets to attach
                                                    pool_name="<Synapse Spark pool name>") #Name of Synapse spark pool 

synapse_compute = ComputeTarget.attach(workspace= ws,                
                                       name='<Synapse Spark pool alias in Azure ML>', 
                                       attach_configuration=attach_config
                                      )

synapse_compute.wait_for_completion()
```

Synapse Spark プールがアタッチされていることを確認します。

```python
ws.compute_targets['Synapse Spark pool alias']
```

## <a name="launch-synapse-spark-pool-for-data-preparation-tasks"></a>データ準備タスク用に Synapse Spark プールを起動する

Synapse セッション中に使用する [Azure Machine Learning 環境](concept-environments.md)を指定できます。 この環境に指定された Conda 依存関係のみが有効になります。 Docker イメージはサポートされません。

>[!WARNING]
>  環境 Conda 依存関係に指定された Python 依存関係は、Synapse Spark プールではサポートされません。 現在サポートされるのは、固定の Python バージョンのみです。 スクリプトに `sys.version_info` を追加して Python バージョンを確認してください。

次のコードでは、セッションの開始前に `azureml-core` バージョン 1.20.0 および `numpy` バージョン 1.17.0 をインストールする環境 (`myenv`) を作成します。 その後、Synapse セッションの `start` ステートメントにこの環境を含めることができます。

```python

from azureml.core import Workspace, Environment

# creates environment with numpy and azureml-core dependencies
ws = Workspace.from_config()
env = Environment(name="myenv")
env.python.conda_dependencies.add_pip_package("azureml-core==1.20.0")
env.python.conda_dependencies.add_conda_package("numpy==1.17.0")
env.register(workspace=ws)
```

Synapse Spark プールを使用したデータ準備を開始するには、Synapse Spark プールの名前を指定し、サブスクリプション ID、Machine Learning ワークスペースのリソース グループ、Machine Learning ワークスペースの名前、Synapse セッション中に使用する環境を入力します。 

> [!IMPORTANT]
> 引き続き Synapse Spark プールを使用するためには、データ準備タスクで使用するコンピューティング リソースを `%synapse` (１ 行のコードの場合) および `%%synapse` (複数行から成るコードの場合) で指定する必要があります。 

```python
%synapse start -c SynapseSparkPoolAlias -s AzureMLworkspaceSubscriptionID -r AzureMLworkspaceResourceGroupName -w AzureMLworkspaceName -e myenv
```

セッションの開始後、セッションのメタデータを確認できます。

```python
%synapse meta
```

## <a name="load-data-from-storage"></a>ストレージからデータを読み込む

Synapse Spark セッションが開始されたら、準備するデータを読み取ります。 データの読み込みは、Azure Blob Storage および Azure Data Lake Storage Generation 1 および 2 でサポートされます。

これらのストレージ サービスからデータを読み込むには、2 とおりの方法があります。 

* 対応する Hadoop Distributed Files System (HDFS) パスを使用して、ストレージから直接データを読み込みます。

* 既存の [Azure Machine Learning データセット](how-to-create-register-datasets.md)からデータを読み取ります。

これらのストレージ サービスにアクセスするには、**ストレージ BLOB データ閲覧者** のアクセス許可が必要です。 これらのストレージ サービスにデータを書き戻す予定がある場合は、**ストレージ BLOB データ共同作成者** のアクセス許可が必要となります。 [詳細については、ストレージのアクセス許可とロールに関するページを参照](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues)してください。

### <a name="load-data-with-hadoop-distributed-files-system-hdfs-path"></a>Hadoop Distributed Files System (HDFS) パスを使用してデータを読み込む

対応する HDFS パスを使用して、ストレージからのデータの読み込みと読み取りを行うには、データ アクセスの認証資格情報を手元に用意しておく必要があります。 これらの資格情報は、ストレージの種類によって異なります。  

次のコードは、Shared Access Signature (SAS) トークンまたはアクセス キーを使用して、**Azure Blob Storage** から Spark データフレームにデータを読み取る方法を示しています。 

```python
%%synapse

# setup access key or SAS token
sc._jsc.hadoopConfiguration().set("fs.azure.account.key.<storage account name>.blob.core.windows.net", "<access key>")
sc._jsc.hadoopConfiguration().set("fs.azure.sas.<container name>.<storage account name>.blob.core.windows.net", "sas token")

# read from blob 
df = spark.read.option("header", "true").csv("wasbs://demo@dprepdata.blob.core.windows.net/Titanic.csv")
```

次のコードは、サービス プリンシパルの資格情報を使用して、**Azure Data Lake Storage Generation 1 (ADLS Gen 1)** からデータを読み取る方法を示しています。 

```python

# setup service principal which has access of the data
sc._jsc.hadoopConfiguration().set("fs.adl.account.<storage account name>.oauth2.access.token.provider.type","ClientCredential")

sc._jsc.hadoopConfiguration().set("fs.adl.account.<storage account name>.oauth2.client.id", "<client id>")

sc._jsc.hadoopConfiguration().set("fs.adl.account.<storage account name>.oauth2.credential", "<client secret>")

sc._jsc.hadoopConfiguration().set("fs.adl.account.<storage account name>.oauth2.refresh.url",
https://login.microsoftonline.com/<tenant id>/oauth2/token)

df = spark.read.csv("adl://<storage account name>.azuredatalakestore.net/<path>")

```

次のコードは、サービス プリンシパルの資格情報を使用して、**Azure Data Lake Storage Generation 2 (ADLS Gen 2)** からデータを読み取る方法を示しています。 

```python
# setup service principal which has access of the data
sc._jsc.hadoopConfiguration().set("fs.azure.account.auth.type.<storage account name>.dfs.core.windows.net","OAuth")
sc._jsc.hadoopConfiguration().set("fs.azure.account.oauth.provider.type.<storage account name>.dfs.core.windows.net", "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider")
sc._jsc.hadoopConfiguration().set("fs.azure.account.oauth2.client.id.<storage account name>.dfs.core.windows.net", "<client id>")
sc._jsc.hadoopConfiguration().set("fs.azure.account.oauth2.client.secret.<storage account name>.dfs.core.windows.net", "<client secret>")
sc._jsc.hadoopConfiguration().set("fs.azure.account.oauth2.client.endpoint.<storage account name>.dfs.core.windows.net",
https://login.microsoftonline.com/<tenant id>/oauth2/token)


df = spark.read.csv("abfss://<container name>@<storage account>.dfs.core.windows.net/<path>")

```

### <a name="read-in-data-from-registered-datasets"></a>登録済みデータセットからデータを読み取る

ワークスペースで既存の登録済みデータセットを取得し、それを Spark データフレームに変換することによってデータ準備を行うこともできます。  

次の例では、Blob Storage 内のファイルを参照する登録済みの TabularDataset (`blob_dset`) を取得し、それを Spark データフレームに変換します。 Spark データフレームにデータセットを変換する際は、`pyspark` のデータ探索ライブラリおよびデータ準備ライブラリを活用できます。  

``` python

%%synapse
from azureml.core import Workspace, Dataset

dset = Dataset.get_by_name(ws, "blob_dset")
spark_df = dset.to_spark_dataframe()
```

## <a name="perform-data-preparation-tasks"></a>データ準備タスクを実行する

データを取得して調査した後、データ準備タスクを実行することができます。

次のコードは、前セクションで見た HDFS の例を応用し、**Survivor** 列に基づいて Spark データフレーム (`df`) のデータをフィルター選択し、そのリストを **Age** でグループ化します。

```python
%%synapse
from pyspark.sql.functions import col, desc

df.filter(col('Survived') == 1).groupBy('Age').count().orderBy(desc('count')).show(10)

df.show()

```

## <a name="save-data-to-storage-and-stop-spark-session"></a>データをストレージに保存して Spark セッションを停止する

データ探索とデータ準備が完了したら、後で使用できるよう準備済みのデータを Azure 上のストレージ アカウントに保存します。

次の例では、準備済みのデータを Azure Blob Storage に書き戻し、`training_data` ディレクトリにある元の `Titanic.csv` ファイルを上書きします。 ストレージへの書き戻しを行うには、**ストレージ BLOB データ共同作成者** のアクセス許可が必要となります。 [詳細については、ストレージのアクセス許可とロールに関するページを参照](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues)してください。

```python
%% synapse

df.write.format("csv").mode("overwrite").save("wasbs://demo@dprepdata.blob.core.windows.net/training_data/Titanic.csv")
```

データ準備が完了し、準備済みのデータをストレージに保存したら、次のコマンドを使用して Synapse Spark プールの使用を停止します。

```python
%synapse stop
```

## <a name="create-dataset-to-represent-prepared-data"></a>準備済みのデータを表すデータセットを作成する

準備済みのデータをモデルのトレーニングに利用する際は、[Azure Machine Learning データストア](how-to-access-data.md)にストレージを接続し、[Azure Machine Learning データセット](how-to-create-register-datasets.md)で使用するファイルを指定します。

以下のコード例は、次の処理を行います。

* 準備済みのデータを保存したストレージ サービスに接続するデータストアが作成済みであることを前提としています。  
* その既存のデータストア (`mydatastore`) を、get() メソッドでワークスペース (`ws`) から取得します。
* `mydatastore` の `training_data` ディレクトリに格納されている準備済みのデータ ファイルを参照する [FileDataset](how-to-create-register-datasets.md#filedataset) (`train_ds`) を作成します。  
* 変数 `input1` を作成します。後でこの変数を使用して、コンピューティング先から利用できる `train_ds` データセットのデータ ファイルを作成できます。

```python
from azureml.core import Datastore, Dataset

datastore = Datastore.get(ws, datastore_name='mydatastore')

datastore_paths = [(datastore, '/training_data/')]
train_ds = Dataset.File.from_files(path=datastore_paths, validate=True)
input1 = train_ds.as_mount()

```

## <a name="example-notebook"></a>ノートブックの例

単一のノートブックから Azure Synapse と Azure Machine Learning を使用してデータ準備とモデルのトレーニングを実行する方法を詳しく示したコード例については、こちらの[エンド ツー エンドのノートブック](../synapse-analytics/overview-what-is.md)を参照してください。

## <a name="next-steps"></a>次のステップ

* [モデルをトレーニングします](how-to-set-up-training-targets.md)。
* [Azure Machine Learning データセットをトレーニングする](how-to-train-with-datasets.md)
* [Azure Machine Learning データセットを作成する](how-to-create-register-datasets.md)。

