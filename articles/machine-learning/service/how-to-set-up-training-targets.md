---
title: モデル トレーニング用のコンピューティング ターゲット
titleSuffix: Azure Machine Learning service
description: 機械学習モデル トレーニング用のトレーニング環境 (コンピューティング ターゲット) を構成します。 トレーニング環境を簡単に切り替えることができます。 ローカルでトレーニングを開始し、スケール アウトする必要がある場合は、クラウド ベースのコンピューティング ターゲットに切り替えます。 Databricks
services: machine-learning
author: heatherbshapiro
ms.author: hshapiro
ms.reviewer: larryfr
manager: cgronlun
ms.service: machine-learning
ms.component: core
ms.topic: article
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 664d56daf3e70e2e5699d0c07331c466c60e06c5
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/13/2018
ms.locfileid: "53338864"
---
# <a name="set-up-compute-targets-for-model-training"></a>モデル トレーニング用のコンピューティング ターゲットを設定する

Azure Machine Learning service では、さまざまなコンピューティング リソースでモデルをトレーニングできます。 __コンピューティング ターゲット__と呼ばれるこれらのコンピューティング リソースはローカルまたはクラウドに存在させることができます。 このドキュメントでは、サポートされているコンピューティング ターゲットとその使用方法について学びます。

コンピューティング ターゲットは、トレーニング スクリプトが実行される場所、または、モデルが Web サービスとしてデプロイされるときにホストされる場所であるリソースです。 Azure Machine Learning SDK、Azure portal、または Azure CLI を使用してコンピューティング ターゲットを作成および管理できます。 別のサービス (たとえば、HDInsight クラスター) によって作成されたコンピューティング ターゲットがある場合、それらを Azure Machine Learning service ワークスペースに接続して使用できます。

Azure Machine Learning がサポートするコンピューティング ターゲットは 3 つのカテゴリに大別されます:

* __ローカル__:開発/実験環境として使用するローカル コンピューター、またはクラウドベースの VM。 

* __マネージド コンピューティング__:Azure Machine Learning コンピューティングは、Azure Machine Learning service によって管理されるコンピューティング製品です。 トレーニング、テスト、バッチ推論用のシングルノードまたはマルチノードのコンピューティングを簡単に作成できます。

* __接続型コンピューティング__:独自の Azure クラウド コンピューティングを用意して、それを Azure Machine Learning に接続することもできます。 サポートされているコンピューティングの種類とそれらの使用方法については、以下で説明します。


## <a name="supported-compute-targets"></a>サポートされているコンピューティング ターゲット

Azure Machine Learning service では、さまざまなコンピューティング ターゲットをさまざまな形でサポートします。 典型的なモデル開発ライフサイクルは、少量のデータを用いた開発と実験から始まります。 この段階では、ローカル環境を使用することをお勧めします。 たとえば、ローカル コンピューターやクラウドベースの VM などです。 より大規模なデータ セットにトレーニングをスケールアップする、または分散トレーニングを実行する段階で、Azure Machine Learning コンピューティングを使用して、実行を送信するたびに自動スケーリングするシングルノードまたはマルチノード クラスターを作成することをお勧めします。 独自のコンピューティング リソースを接続することもできますが、以下で説明するように、シナリオによってサポートが異なる場合があります:

|コンピューティング ターゲット| GPU アクセラレーション | 自動化されたハイパーパラメーターのチューニング | 自動化された機械学習 | パイプライン親和性|
|----|:----:|:----:|:----:|:----:|
|[ローカル コンピューター](#local)| 可能性あり | &nbsp; | ✓ | &nbsp; |
|[Azure Machine Learning コンピューティング](#amlcompute)| ✓ | ✓ | ✓ | ✓ |
|[リモート VM](#vm) | ✓ | ✓ | ✓ | ✓ |
|[Azure Databricks](#databricks)| &nbsp; | &nbsp; | ✓ | ✓[*](#pipeline-only) |
|[Azure Data Lake Analytics](#adla)| &nbsp; | &nbsp; | &nbsp; | ✓[*](#pipeline-only) |
|[Azure HDInsight](#hdinsight)| &nbsp; | &nbsp; | &nbsp; | ✓ |

> [!IMPORTANT]
> <a id="pipeline-only"></a>__*__ パイプラインで使用できるのは、Azure Databricks と Azure Data Lake Analytics __のみ__です。 パイプラインの詳細については、「[パイプラインと Azure Machine Learning](concept-ml-pipelines.md)」のドキュメントを参照してください。

> [!IMPORTANT]
> Azure Machine Learning コンピューティングは、ワークスペース内から作成する必要があります。 既存のインスタンスをワークスペースに接続することはできません。
>
> その他のコンピューティング ターゲットは、Azure Machine Learning の外部で作成してからワークスペースに接続する必要があります。

> [!NOTE]
> モデルをトレーニングするとき、一部のコンピューティング ターゲットは Docker コンテナー イメージに依存します。 GPU ベース イメージは Microsoft Azure サービスのみで使用する必要があります。 モデル トレーニングの場合、これらのサービスは次のとおりです:
>
> * Azure Machine Learning コンピューティング
> * Azure Kubernetes Service
> * Data Science Virtual Machine。

## <a name="workflow"></a>ワークフロー

Azure Machine Learning でモデルを開発およびデプロイするワークフローは次の手順に従います。

1. Python で機械学習トレーニング スクリプトを開発します。
1. コンピューティング ターゲットを作成して構成するか、既存のコンピューティング ターゲットを接続します。
1. コンピューティング ターゲットにトレーニング スクリプトを送信します。
1. 結果を検査して最適なモデルを見つけます。
1. モデル レジストリにモデルを登録します。
1. モデルをデプロイします。

> [!IMPORTANT]
> トレーニング スクリプトは特定のコンピューティング ターゲットに関連付けられていません。 最初ローカル コンピューターでトレーニングし、トレーニング スクリプトを書き直すことなくコンピューティング ターゲットを切り替えることができます。

> [!TIP]
> コンピューティング ターゲットをワークスペースに関連付けるたびに、マネージド コンピューティングを作成するか、または既存のコンピューティングを接続することによって、コンピューティングに名前を付ける必要があります。 これは 2 ～ 16 文字の長さである必要があります。

あるコンピューティング ターゲットを別のコンピューティング ターゲットに切り替えるには、[実行構成](concept-azure-machine-learning-architecture.md#run-configuration)を作成する必要があります。 実行構成は、コンピューティング ターゲットでスクリプトを実行する方法を定義します。

## <a name="training-scripts"></a>トレーニング スクリプト

トレーニング実行を開始すると、トレーニング スクリプトを含むディレクトリのスナップショットが作成され、コンピューティング ターゲットに送信されます。 詳細については、[スナップショット](concept-azure-machine-learning-architecture.md#snapshot)を参照してください。

## <a id="local"></a>ローカル コンピューター

ローカルでトレーニングするときは、SDK を使用してトレーニング操作を送信します。 ユーザー管理環境またはシステム管理環境を使用してトレーニングできます。

### <a name="user-managed-environment"></a>ユーザー管理環境

ユーザー管理環境では、スクリプトを実行する Python 環境で必要なすべてのパッケージが使用できることをユーザー自身が確認する必要があります。 次のコード スニペットは、ユーザー管理環境でのトレーニング構成例です。

```python
from azureml.core.runconfig import RunConfiguration

# Editing a run configuration property on-fly.
run_config_user_managed = RunConfiguration()

run_config_user_managed.environment.python.user_managed_dependencies = True

# You can choose a specific Python environment by pointing to a Python path 
#run_config.environment.python.interpreter_path = '/home/ninghai/miniconda3/envs/sdk2/bin/python'
```

  
### <a name="system-managed-environment"></a>システム管理環境

システム管理環境では、conda を使用して依存関係を管理します。 conda によって、依存関係のリストを含む`conda_dependencies.yml` という名前のファイルが作成されます。 その後、ユーザーは新しい conda 環境の構築をシステムに指示し、そこでスクリプトを実行できます。 システム管理環境は、`conda_dependencies.yml` ファイルが変更されていない限り、後で再利用できます。 

新しい環境の初期セットアップは、必要な依存関係のサイズに応じて完了までに数分かかります。 次のコード スニペットでは、scikit-learn に依存するシステム管理環境が作成されます。

```python
from azureml.core.runconfig import RunConfiguration
from azureml.core.conda_dependencies import CondaDependencies

run_config_system_managed = RunConfiguration()

run_config_system_managed.environment.python.user_managed_dependencies = False
run_config_system_managed.auto_prepare_environment = True

# Specify conda dependencies with scikit-learn

run_config_system_managed.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'])
```

## <a id="amlcompute"></a>Azure Machine Learning コンピューティング

Azure Machine Learning コンピューティングは、ユーザーがシングルノードからマルチノードのコンピューティングを簡単に作成できる、マネージド コンピューティング インフラストラクチャです。 __ワークスペース リージョン内__に作成され、ワークスペース内の他のユーザーと共有できるリソースです。 ジョブが送信されると自動的にスケールアップし、Azure Virtual Network に配置できます。 モデルの依存関係を Docker コンテナーにパッケージ化する__コンテナー化環境__で実行されます。

Azure Machine Learning コンピューティングを使用して、クラウド内の CPU または GPU コンピューティング ノードのクラスター全体にトレーニング プロセスを分散させることができます。 GPU を含む VM サイズの詳細については、「[GPU 最適化済み仮想マシンのサイズ](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu)」ドキュメントを参照してください。

> [!NOTE]
> Azure Machine Learning コンピューティングには、割り当て可能なコア数などの既定の制限があります。 詳細については、「[Azure リソースのクォータの管理と要求](https://docs.microsoft.com/azure/machine-learning/service/how-to-manage-quotas)」ドキュメントを参照してください。

Azure Machine Learning コンピューティングは、実行をスケジュールするときにオンデマンドで、または永続的なリソースとして作成できます。

### <a name="run-based-creation"></a>実行ベースの作成

Azure Machine Learning コンピューティングを実行時にコンピューティング ターゲットとして作成できます。 この場合、コンピューティングは実行用に自動的に作成され、実行構成で指定する max_node を上限にスケールアップし、実行完了後は__自動的に削除__されます。

> [!IMPORTANT]
> Azure Machine Learning コンピューティングの実行ベースの作成は現在、プレビュー状態です。 ハイパーパラメーター チューニングまたは自動化された機械学習を使用している場合は、実行ベースの作成を使用しないでください。 ハイパーパラメーター チューニングまたは自動化された機械学習を使用する必要がある場合は、実行を送信する前に Azure Machine Learning コンピューティングを作成します。

```python
from azureml.core.compute import ComputeTarget, AmlCompute

#Let us first list the supported VM families for Azure Machine Learning Compute
AmlCompute.supported_vmsizes()

from azureml.core.runconfig import RunConfiguration

# create a new runconfig object
run_config = RunConfiguration()

# signal that you want to use AmlCompute to execute script.
run_config.target = "amlcompute"

# AmlCompute will be created in the same region as workspace. Set vm size for AmlCompute from the list returned above
run_config.amlcompute.vm_size = 'STANDARD_D2_V2'

```

### <a name="persistent-compute-basic"></a>永続的なコンピューティング (基本)

永続的な Azure Machine Learning コンピューティングは、複数のジョブにわたって再利用できます。 ワークスペース内の他のユーザーと共有でき、ジョブ間で保持されます。

永続的な Azure Machine Learning コンピューティング リソースを作成するには、`vm_size` および `max_nodes` パラメーターを指定します。 以後、Azure Machine Learning は残りのパラメーターにスマート既定値を使用します。  たとえば、未使用時は 0 ノードまで自動的にスケールダウンしたり、ジョブを実行するために必要に応じて専用 VM を作成したりするように、コンピューティングが設定されます。 

* **vm_size**:Azure Machine Learning コンピューティングによって作成されるノードの VM ファミリ。
* **max_nodes**:Azure Machine Learning コンピューティングでジョブを実行中に自動スケーリングする最大ノード数。

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# Choose a name for your CPU cluster
cpu_cluster_name = "cpucluster"

# Verify that cluster does not exist already
try:
    cpu_cluster = ComputeTarget(workspace=ws, name=cpu_cluster_name)
    print('Found existing cluster, use it.')
except ComputeTargetException:
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                           max_nodes=4)
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)

cpu_cluster.wait_for_completion(show_output=True)

```

### <a name="persistent-compute-advanced"></a>永続的なコンピューティング (詳細)

Azure Machine Learning コンピューティングの作成時には、いくつかの詳細プロパティも設定できます。  これらのプロパティを使用すると、永続的なクラスターを固定サイズで、またはサブスクリプションの既存の Azure Virtual Network 内に作成できます。

`vm_size` と `max_nodes` に加えて、次のプロパティを使用できます:

* **min_nodes**:Azure Machine Learning コンピューティングでジョブを実行中にダウンスケールする最小ノード数 (既定値は 0 ノード)。
* **vm_priority**:Azure Machine Learning コンピューティングの作成時に、"dedicated" (専用) VM (既定) と "lowpriority" (低優先度) VM のどちらかを選択します。 低優先度 VM は Azure の余剰容量を使用するため低コストですが、実行が割り込まれるリスクがあります。
* **idle_seconds_before_scaledown**:実行が完了してから min_node までの自動スケーリングを待機するアイドル時間 (既定値は 120 秒)。
* **vnet_resourcegroup_name**:__既存__の仮想ネットワークのリソース グループ。 Azure Machine Learning コンピューティングはこの仮想ネットワーク内に作成されます。
* **vnet_name**:仮想ネットワークの名前。 仮想ネットワークは Azure Machine Learning ワークスペースと同じリージョンになければなりません。
* **subnet_name**:仮想ネットワーク内のサブネットの名前。 Azure Machine Learning コンピューティング リソースには、このサブネット範囲の IP アドレスが割り当てられます。

> [!TIP]
> 永続的な Azure Machine Learning コンピューティング リソースを作成するときに、min_nodes や max_nodes など、そのプロパティを更新することもできます。 その `update()` 関数を呼び出すだけです。

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# Choose a name for your CPU cluster
cpu_cluster_name = "cpucluster"

# Verify that cluster does not exist already
try:
    cpu_cluster = ComputeTarget(workspace=ws, name=cpu_cluster_name)
    print('Found existing cluster, use it.')
except ComputeTargetException:
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                           vm_priority='lowpriority',
                                                           min_nodes=2,
                                                           max_nodes=4,
                                                           idle_seconds_before_scaledown='300',
                                                           vnet_resourcegroup_name='<my-resource-group>',
                                                           vnet_name='<my-vnet-name>',
                                                           subnet_name='<my-subnet-name>')
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)

cpu_cluster.wait_for_completion(show_output=True)

```


## <a id="vm"></a>リモート VM

Azure Machine Learning では、独自のコンピューティング リソースを用意してワークスペースに接続することもサポートされています。 任意のリモート VM もそのようなリソースの一種ですが、Azure Machine Learning service からアクセスできることが条件です。 Azure VM でも、組織内またはオンプレミスのリモート サーバーでもかまいません。 具体的には、IP アドレスと資格情報 (ユーザー名/パスワードまたは SSH キー) があれば、任意のアクセス可能な VM をリモート実行に使用できます。
システムで構築済みの conda 環境、既存の Python 環境、または Docker コンテナーを使用できます。 Docker コンテナーを使用した実行には、VM で Docker Engine が動作していることが必要です。 この機能は、ローカル コンピューターよりも柔軟性がある、クラウドベースの開発/実験環境が必要な場合に特に役立ちます。

> [!TIP]
> このシナリオ向けに選択する Azure VM としては、Data Science Virtual Machine の使用をお勧めします。 これは Azure における事前構成済みのデータ サイエンスおよび AI 開発環境であり、ML 開発のライフサイクル全体に対応したツールとフレームワークが厳選されています。 Azure Machine Learning で Data Science Virtual Machine を使用する方法については、[開発環境の構成](https://docs.microsoft.com/azure/machine-learning/service/how-to-configure-environment#dsvm)に関するドキュメントを参照してください。

> [!WARNING]
> Azure Machine Learning は、Ubuntu を実行する仮想マシンのみサポートします。 仮想マシンを作成したり、既存の仮想マシンを選択するときは、Ubuntu を使用するマシンを選択する必要があります。

次の手順では、SDK を使用してトレーニング ターゲットとして Data Science Virtual Machine (DSVM) を構成します。

1. コンピューティング ターゲットとして既存の仮想マシンを接続するには、仮想マシンの完全修飾ドメイン名、ログイン名、およびパスワードを入力する必要があります。  例では、```<fqdn>``` を VM のパブリックの完全修飾ドメイン名、またはパブリック IP アドレスに置き換えます。 ```<username>``` と ```<password>``` を VM の SSH ユーザーとパスワードに置き換えます。

    ```python
    from azureml.core.compute import RemoteCompute, ComputeTarget
    
    # Create compute config.
    attach_config = RemoteCompute.attach_configuration(address = "ipaddress",
                                                       ssh_port=22,
                                                       username='<username>',
                                                       password="<password>")
    # If using SSH instead of a password, use this:
    #                                                  ssh_port=22,
    #                                                   username='<username>',
    #                                                   password=None,
    #                                                   private_key_file="path-to-file",
    #                                                   private_key_passphrase="passphrase")

    # Attach the compute
    compute = ComputeTarget.attach(ws, "attach-dsvm", attach_config)

    compute.wait_for_completion(show_output=True)
    ```

1. DSVM コンピューティング ターゲット用の構成を作成します。 Docker と conda は、DSVM でトレーニング環境を作成および構成するために使用されます:

    ```python
    from azureml.core.runconfig import RunConfiguration
    from azureml.core.conda_dependencies import CondaDependencies

    # Load the "cpu-dsvm.runconfig" file (created by the above attach operation) in memory
    run_config = RunConfiguration(framework = "python")

    # Set compute target to the Linux DSVM
    run_config.target = compute_target_name

    # Use Docker in the remote VM
    run_config.environment.docker.enabled = True

    # Use CPU base image
    # If you want to use GPU in DSVM, you must also use GPU base Docker image azureml.core.runconfig.DEFAULT_GPU_IMAGE
    run_config.environment.docker.base_image = azureml.core.runconfig.DEFAULT_CPU_IMAGE
    print('Base Docker image is:', run_config.environment.docker.base_image)

    # Ask system to provision a new one based on the conda_dependencies.yml file
    run_config.environment.python.user_managed_dependencies = False

    # Prepare the Docker and conda environment automatically when used the first time.
    run_config.prepare_environment = True

    # specify CondaDependencies obj
    run_config.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'])

    ```

## <a id="databricks"></a>Azure Databricks

Azure Databricks は、Azure クラウド内の Apache Spark ベースの環境です。 これは、Azure Machine Learning パイプラインでモデルをトレーニングする際に、コンピューティング ターゲットとして使用できます。

> [!IMPORTANT]
> Azure Databricks コンピューティング ターゲットは、Machine Learning パイプラインでのみ使用できます。
>
> モデルのトレーニングに使用する前に、Azure Databricks ワークスペースを作成する必要があります。 これらのリソースを作成するには、[Azure Databricks での Spark ジョブの実行](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal)に関するドキュメントを参照してください。

コンピューティング ターゲットとして Azure Databricks に接続するには、Azure Machine Learning SDK を使用し、次の情報を提供する必要があります。

* __コンピューティング名__:このコンピューティング リソースに割り当てる名前。
* __Databricks ワークスペース名__:Azure Databricks ワークスペースの名前。
* __アクセス トークン__:Azure Databricks に対する認証に使用するアクセス トークン。 アクセス トークンを生成するには、[認証](https://docs.azuredatabricks.net/api/latest/authentication.html)に関するドキュメントを参照してください。

次のコードは、コンピューティング ターゲットとして Azure Databricks に接続する方法を示しています。

```python
databricks_compute_name = os.environ.get("AML_DATABRICKS_COMPUTE_NAME", "<databricks_compute_name>")
databricks_workspace_name = os.environ.get("AML_DATABRICKS_WORKSPACE", "<databricks_workspace_name>")
databricks_resource_group = os.environ.get("AML_DATABRICKS_RESOURCE_GROUP", "<databricks_resource_group>")
databricks_access_token = os.environ.get("AML_DATABRICKS_ACCESS_TOKEN", "<databricks_access_token>")

try:
    databricks_compute = ComputeTarget(workspace=ws, name=databricks_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('databricks_compute_name {}'.format(databricks_compute_name))
    print('databricks_workspace_name {}'.format(databricks_workspace_name))
    print('databricks_access_token {}'.format(databricks_access_token))

    # Create attach config
    attach_config = DatabricksCompute.attach_configuration(resource_group = databricks_resource_group,
                                                           workspace_name = databricks_workspace_name,
                                                           access_token = databricks_access_token)
    databricks_compute = ComputeTarget.attach(
             ws,
             databricks_compute_name,
             attach_config
         )
    
    databricks_compute.wait_for_completion(True)
```

## <a id="adla"></a>Azure Data Lake Analytics

Azure Data Lake Analytics は、Azure クラウド内のビッグ データ分析プラットフォームです。 これは、Azure Machine Learning パイプラインでモデルをトレーニングする際に、コンピューティング ターゲットとして使用できます。

> [!IMPORTANT]
> Azure Data Lake Analytics コンピューティング ターゲットは、Machine Learning パイプラインでのみ使用できます。
>
> モデルのトレーニングに使用する前に、Azure Data Lake Analytics ワークスペースを作成する必要があります。 このリソースを作成するには、「[Azure Data Lake Analytics の使用を開始する](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-get-started-portal)」を参照してください。

コンピューティング ターゲットとして Data Lake Analytics に接続するには、Azure Machine Learning SDK を使用し、次の情報を提供する必要があります。

* __コンピューティング名__:このコンピューティング リソースに割り当てる名前。
* __リソース グループ__:Data Lake Analytics アカウントを含むリソース グループ。
* __アカウント名__:Data Lake Analytics アカウント名です。

次のコードは、コンピューティング ターゲットとして Data Lake Analytics に接続する方法を示しています。

```python
adla_compute_name = os.environ.get("AML_ADLA_COMPUTE_NAME", "<adla_compute_name>")
adla_resource_group = os.environ.get("AML_ADLA_RESOURCE_GROUP", "<adla_resource_group>")
adla_account_name = os.environ.get("AML_ADLA_ACCOUNT_NAME", "<adla_account_name>")

try:
    adla_compute = ComputeTarget(workspace=ws, name=adla_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('adla_compute_name {}'.format(adla_compute_name))
    print('adla_resource_id {}'.format(adla_resource_group))
    print('adla_account_name {}'.format(adla_account_name))
    # create attach config
    attach_config = AdlaCompute.attach_configuration(resource_group = adla_resource_group,
                                                     account_name = adla_account_name)
    # Attach ADLA
    adla_compute = ComputeTarget.attach(
             ws,
             adla_compute_name,
             attach_config
         )
    
    adla_compute.wait_for_completion(True)
```

> [!TIP]
> Azure Machine Learning パイプラインは、Data Lake Analytics アカウントの既定のデータ ストアに格納されたデータのみ使用できます。 使用する必要があるデータが既定以外のストアにある場合は、[`DataTransferStep`](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py) を使用して、トレーニングの前にデータをコピーできます。

## <a id="hdinsight"></a>Azure HDInsight 

HDInsight は、ビッグ データ分析のための一般的なプラットフォームです。 モデルのトレーニングに使用できる Apache Spark を提供します。

> [!IMPORTANT]
> モデルのトレーニングに使用する前に、HDInsight クラスターを作成する必要があります。 HDInsight クラスターで Spark を作成するには、「[HDInsight で Spark クラスターを作成する」](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-jupyter-spark-sql)のドキュメントを参照してください。
>
> クラスターを作成するとき、SSH ユーザー名とパスワードを指定する必要があります。 コンピューティング ターゲットとして HDInsight を使用するときに必要になるので、これらの値をメモしておいてください。
>
> クラスターが作成されると、その完全修飾ドメイン名 (FQDN) は `<clustername>.azurehdinsight.net` になります。ここで、`<clustername>` はクラスターに付けた名前です。 コンピューティング ターゲットとしてこれを使用するには、このアドレス (またはクラスターのパブリック IP アドレス) が必要です。

コンピューティング ターゲットとして HDInsight を構成するには、HDInsight クラスターの完全修飾ドメイン名、クラスターのログイン名、およびパスワードを入力する必要があります。 次の例では、SDK を使用してクラスターをワークスペースに接続します。 この例では、`<fqdn>` をクラスターのパブリックの完全修飾ドメイン名またはパブリック IP アドレスに置き換えます。 `<username>` および `<password>` を SSH ユーザーおよびクラスターのパスワードに置き換えます。

> [!NOTE]
> クラスターの FQDN を検索するには、Azure Portal にアクセスし、HDInsight クラスターを選択します。 __[概要]__ セクションで、FQDN は __URL__ エントリの一部です。 値の先頭から `https://` を削除します。
>
> ![URL エントリが強調表示されている HDInsight クラスターの概要のスクリーン ショット](./media/how-to-set-up-training-targets/hdinsight-overview.png)

```python
from azureml.core.compute import HDInsightCompute, ComputeTarget

try:
    # Attaches a HDInsight cluster as a compute target.
    attach_config = HDInsightCompute.attach_configuration(address = "fqdn-or-ipaddress",
                                                          ssh_port = 22,
                                                          username = "username",
                                                          password = None, #if using ssh key
                                                          private_key_file = "path-to-key-file",
                                                          private_key_phrase = "key-phrase")
    compute = ComputeTarget.attach(ws, "myhdi", attach_config)
except UserErrorException as e:
    print("Caught = {}".format(e.message))
    print("Compute config already attached.")

# Configure HDInsight run
# load the runconfig object from the "myhdi.runconfig" file generated by the attach operaton above.
run_config = RunConfiguration.load(project_object = project, run_config_name = 'myhdi')

# ask system to prepare the conda environment automatically when used for the first time
run_config.auto_prepare_environment = True
```

## <a name="submit-training-run"></a>トレーニングの実行の送信

トレーニングの実行を送信するには、2 つの方法があります。

* `ScriptRunConfig` オブジェクトを送信する。
* `Pipeline` オブジェクトを送信する。

> [!IMPORTANT]
> Azure Databricks および Azure Datalake Analytics のコンピューティング ターゲットは、パイプラインでのみ使用できます。
> ローカルのコンピューティング ターゲットは、パイプラインでは使用できません。

### <a name="submit-using-scriptrunconfig"></a>`ScriptRunConfig` を使用して送信する

`ScriptRunConfig` を使用してトレーニングの実行を送信するコード パターンは、コンピューティング ターゲットに関係なく同じです。

* コンピューティング ターゲットの実行構成を使用して `ScriptRunConfig` オブジェクトを作成します。
* 実行を送信します。
* 実行が完了するのを待ちます。

次の例では、このドキュメントで作成済みのシステム管理のローカル コンピューティング ターゲットの構成を使用します。

```python
src = ScriptRunConfig(source_directory = script_folder, script = 'train.py', run_config = run_config_system_managed)
run = exp.submit(src)
run.wait_for_completion(show_output = True)
```


### <a name="submit-using-a-pipeline"></a>パイプラインを使用して送信する

パイプラインを使用してトレーニングの実行を送信するコード パターンは、コンピューティング ターゲットに関係なく同じです。

* コンピューティング リソースに対するパイプラインに 1 つのステップを追加します。
* パイプラインを使用して実行を送信します。
* 実行が完了するのを待ちます。

次の例では、このドキュメントで作成済みの Azure Databricks コンピューティング ターゲットを使用します。

```python
dbStep = DatabricksStep(
    name="databricksmodule",
    inputs=[step_1_input],
    outputs=[step_1_output],
    num_workers=1,
    notebook_path=notebook_path,
    notebook_params={'myparam': 'testparam'},
    run_name='demo run name',
    databricks_compute=databricks_compute,
    allow_reuse=False
)
# list of steps to run
steps = [dbStep]
pipeline = Pipeline(workspace=ws, steps=steps)
pipeline_run = Experiment(ws, 'Demo_experiment').submit(pipeline)
pipeline_run.wait_for_completion()
```

機械学習パイプラインの詳細については、「[パイプラインと Azure Machine Learning](concept-ml-pipelines.md)」のドキュメントを参照してください。

パイプラインを使用してトレーニングを示す Jupyter Notebook の例については、[https://github.com/Azure/MachineLearningNotebooks/tree/master/pipeline](https://github.com/Azure/MachineLearningNotebooks/tree/master/pipeline) を参照してください。

## <a name="view-and-set-up-compute-using-the-azure-portal"></a>Azure Portal を使用した計算の表示および設定

Azure Portal からワークスペースに関連付けられたコンピューティング ターゲットを表示できます。 一覧を取得するには、次の手順に従います。

1. [Azure Portal](https://portal.azure.com) にアクセスし、ワークスペースに移動します。
2. __[アプリケーション]__ セクションの __[計算]__ リンクをクリックします。

    ![[計算] タブを表示する](./media/how-to-set-up-training-targets/azure-machine-learning-service-workspace.png)

### <a name="create-a-compute-target"></a>コンピューティング ターゲットを作成する

上記の手順に従ってコンピューティング ターゲットの一覧を表示し、次の手順に従ってコンピューティング ターゲットを作成します。

1. __+__ 記号をクリックし、コンピューティング ターゲットを追加します。

    ![計算の追加 ](./media/how-to-set-up-training-targets/add-compute-target.png)

1. コンピューティング ターゲットの名前を入力する
1. __[トレーニング]__ に使用するコンピューティングの種類として **[Machine Learning コンピューティング]** を選択します

    > [!IMPORTANT]
    > トレーニング用の Azure Machine Learning コンピューティングはマネージド コンピューティングとして作成することしかできません

1. 必須フォーム、特に、VM ファミリや、コンピューティングのスピンアップに使用する最大ノード数を入力します 
1. __[作成]__
1. 一覧からコンピューティング ターゲットを選択することによって、作成操作の状態を表示できます

    ![コンピューティングの一覧を表示する](./media/how-to-set-up-training-targets/View_list.png)

1. コンピューティング ターゲットの詳細が表示されます。

    ![詳細を表示する](./media/how-to-set-up-training-targets/compute-target-details.png)

1. 上述のように、これらのターゲットに対して実行を送信できるようになりました


### <a name="reuse-existing-compute-in-your-workspace"></a>ワークスペース内の既存の計算の再利用

上記の手順に従ってコンピューティング ターゲットの一覧を表示し、次の手順に従ってコンピューティング ターゲットを再利用します。

1. **+** 記号をクリックし、コンピューティング ターゲットを追加します
2. コンピューティング ターゲットの名前を入力する
3. __トレーニング__に接続する計算の種類を選択します

    > [!IMPORTANT]
    > ポータルを使用してすべてのコンピューティングの種類を接続できるわけではありません。
    > 現在トレーニング用に接続できる種類は次のとおりです。
    > 
    > * リモート VM
    > * Databricks
    > * Data Lake Analytics
    > * HDInsight

1. 必須フォームに入力する

    > [!NOTE]
    > パスワードよりも安全な SSH キーを使用することをお勧めします。 パスワードはブルート フォース攻撃に対して脆弱ですが、SSH キーは暗号署名を使用しています。 Azure Virtual Machines で使用するための SSH キーを作成する方法の詳細については、次のドキュメントを参照してください。
    >
    > * [Linux または macOS で SSH キーを作成して使用する]( https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys)
    > * [Windows で SSH キーを作成して使用する]( https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows)

1. 接続を選択する
1. 一覧からコンピューティング ターゲットを選択することによって、接続操作の状態を表示できます
1. 上述のように、これらのターゲットに対して実行を送信できるようになりました

## <a name="examples"></a>例
次の場所にあるノートブックを参照してください:
* [how-to-use-azureml/training](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)

* [tutorials/img-classification-part1-training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>次の手順

* [Azure Machine Learning SDK リファレンス](https://aka.ms/aml-sdk)
* [チュートリアル:モデルをトレーニングする](tutorial-train-models-with-aml.md)
* [モデルをデプロイする場所](how-to-deploy-and-where.md)
* [Azure Machine Learning サービスで機械学習パイプラインの構築](concept-ml-pipelines.md)
