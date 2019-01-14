---
title: モデル トレーニング用のコンピューティング ターゲット
titleSuffix: Azure Machine Learning service
description: 機械学習モデル トレーニング用のトレーニング環境 (コンピューティング ターゲット) を構成します。 トレーニング環境を簡単に切り替えることができます。 ローカルでトレーニングを開始します。 スケール アウトする必要がある場合は、クラウド ベースのコンピューティング先に切り替えます。
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
ms.openlocfilehash: 9037f6d7602f186bc30e55acbc050280bca134ee
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/27/2018
ms.locfileid: "53794466"
---
# <a name="set-up-compute-targets-for-model-training"></a>モデル トレーニング用のコンピューティング ターゲットを設定する

Azure Machine Learning service では、さまざまなコンピューティング リソースでモデルをトレーニングできます。 __コンピューティング ターゲット__と呼ばれるこれらのコンピューティング リソースはローカルまたはクラウドに存在させることができます。 この記事では、サポートされているコンピューティング先とその使用方法について学びます。

コンピューティング先は、トレーニング スクリプトを実行または Web サービスとしてモデルがデプロイされたときにモデルをホストするリソースです。 Azure Machine Learning SDK、Azure portal、または Azure CLI を使用してコンピューティング先を作成および管理できます。 別のサービス (たとえば、Azure HDInsight クラスター) によって作成されたコンピューティング先がある場合、それらのターゲットを Azure Machine Learning service ワークスペースに接続して使用できます。

Azure Machine Learning がサポートするコンピューティング ターゲットは 3 つのカテゴリに大別されます:

* __ローカル__:開発および実験環境として使用するローカル コンピューター、またはクラウドベースの仮想マシン (VM)。 
* __マネージド コンピューティング__:Azure Machine Learning コンピューティングは、Azure Machine Learning service によって管理されるコンピューティング製品です。 そのオファリングでは、トレーニング、テスト、バッチ推論用のシングルノードまたはマルチノードのコンピューティングを簡単に作成できます。
* __接続型コンピューティング__:独自の Azure クラウド コンピューティングを用意して、それを Azure Machine Learning に接続することもできます。 サポートされているコンピューティングの種類とそれらの使用方法については、以下のセクションで詳しく説明します。


## <a name="supported-compute-targets"></a>サポートされているコンピューティング ターゲット

Azure Machine Learning service では、さまざまなコンピューティング ターゲットをさまざまな形でサポートします。 典型的なモデル開発ライフサイクルは、少量のデータを用いた開発と実験から始まります。 このステージでは、ローカル コンピューターなどのローカル環境またはクラウド ベースの VM を使用することをお勧めします。 より大規模なデータ セットにトレーニングをスケールアップする、または分散トレーニングを実行する段階で、Azure Machine Learning コンピューティング環境を使用して、実行を送信するたびに自動スケーリングするシングルノードまたはマルチノード クラスターを作成します。 独自のコンピューティング リソースを接続することもできますが、以下の表で説明するように、シナリオによってサポートが異なる場合があります。

|コンピューティング ターゲット| GPU アクセラレーション | 自動<br/> ハイパーパラメーター調整 | 自動</br> 機械学習 | パイプライン親和性|
|----|:----:|:----:|:----:|:----:|
|[ローカル コンピューター](#local)| 可能性あり | &nbsp; | ✓ | &nbsp; |
|[Azure Machine Learning コンピューティング](#amlcompute)| ✓ | ✓ | ✓ | ✓ |
|[リモート VM](#vm) | ✓ | ✓ | ✓ | ✓ |
|[Azure Databricks](#databricks)| &nbsp; | &nbsp; | ✓ | ✓[*](#pipeline-only) |
|[Azure Data Lake Analytics](#adla)| &nbsp; | &nbsp; | &nbsp; | ✓[*](#pipeline-only) |
|[Azure HDInsight](#hdinsight)| &nbsp; | &nbsp; | &nbsp; | ✓ |

> [!IMPORTANT]
> <a id="pipeline-only"></a>__*__ _パイプラインで使用できるのは、Azure Databricks と Azure Data Lake Analytics のみです。_<br/>
> パイプラインの詳細については、「[パイプラインと Azure Machine Learning](concept-ml-pipelines.md)」を参照してください。
>
> Azure Machine Learning コンピューティング環境は、ワークスペース内から作成する必要があります。 既存のインスタンスをワークスペースに接続することはできません。
>
> その他のコンピューティング ターゲットは、Azure Machine Learning の外部で作成してからワークスペースに接続する必要があります。
>
> モデルをトレーニングするとき、一部のコンピューティング先は Docker コンテナー イメージに依存します。 GPU ベース イメージは Microsoft Azure サービスのみで使用する必要があります。 モデル トレーニングの場合、サービスは次のとおりです。
> * Azure Machine Learning コンピューティング
> * Azure Kubernetes Service
> * Windows Data Science Virtual Machine (DSVM)

## <a name="workflow"></a>ワークフロー

Azure Machine Learning でモデルを開発およびデプロイするワークフローは次の手順に従います。

1. Python で機械学習トレーニング スクリプトを開発します。
1. コンピューティング先を作成して構成するか、既存のコンピューティング先を接続します。
1. コンピューティング ターゲットにトレーニング スクリプトを送信します。
1. 結果を検査して最適なモデルを見つけます。
1. モデル レジストリにモデルを登録します。
1. モデルをデプロイします。

> [!NOTE]
> トレーニング スクリプトは特定のコンピューティング ターゲットに関連付けられていません。 最初ローカル コンピューターでトレーニングし、トレーニング スクリプトを書き直すことなくコンピューティング ターゲットを切り替えることができます。
> 
> コンピューティング先をワークスペースに関連付けるたびに、マネージド コンピューティングを作成するか、または既存のコンピューティングを接続することによって、コンピューティングに名前を付けます。 名前は 2 ～ 16 文字で指定する必要があります

あるコンピューティング先を別のコンピューティング先に切り替えるには、[実行構成](concept-azure-machine-learning-architecture.md#run-configuration)が必要です。 実行構成は、コンピューティング ターゲットでスクリプトを実行する方法を定義します。

## <a name="training-scripts"></a>トレーニング スクリプト

トレーニング実行を開始すると、トレーニング スクリプトを含むディレクトリのスナップショットが作成され、コンピューティング先に送信されます。 詳細については、「[スナップショット](concept-azure-machine-learning-architecture.md#snapshot)」を参照してください。

## <a id="local"></a>ローカル コンピューター

ローカルでトレーニングするときは、SDK を使用してトレーニング操作を送信します。 ユーザー管理環境またはシステム管理環境を使用してトレーニングできます。

### <a name="user-managed-environment"></a>ユーザー管理環境

ユーザー管理環境では、スクリプトを実行する Python 環境で必要なすべてのパッケージが使用できることを確認します。 次のコード スニペットは、ユーザー管理環境でのトレーニング構成方法の例です。

```python
from azureml.core.runconfig import RunConfiguration

# Edit a run configuration property on the fly.
run_config_user_managed = RunConfiguration()

run_config_user_managed.environment.python.user_managed_dependencies = True

# Choose a specific Python environment by pointing to a Python path. For example:
# run_config.environment.python.interpreter_path = '/home/ninghai/miniconda3/envs/sdk2/bin/python'
```

  
### <a name="system-managed-environment"></a>システム管理環境

システム管理環境では、conda を使用して依存関係を管理します。 conda によって、依存関係のリストを含む **conda_dependencies.yml** という名前のファイルが作成されます。 その後、ユーザーは新しい conda 環境の構築をシステムに指示し、そこでスクリプトを実行できます。 システム管理環境は、conda_dependencies.yml ファイルが変更されていない限り、後で再利用できます。 

新しい環境の初期セットアップは、必要な依存関係のサイズに基づいて完了までに数分かかります。 次のコード スニペットでは、scikit-learn に依存するシステム管理環境が作成されます。

```python
from azureml.core.runconfig import RunConfiguration
from azureml.core.conda_dependencies import CondaDependencies

run_config_system_managed = RunConfiguration()

run_config_system_managed.environment.python.user_managed_dependencies = False
run_config_system_managed.auto_prepare_environment = True

# Specify the conda dependencies with scikit-learn

run_config_system_managed.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'])
```

## <a id="amlcompute"></a>Azure Machine Learning コンピューティング

Azure Machine Learning コンピューティングは、ユーザーがシングルノードまたはマルチノードのコンピューティングを簡単に作成できる、マネージド コンピューティング インフラストラクチャです。 コンピューティングは、リソースとしてワークスペース リージョン内に作成され、ワークスペース内の他のユーザーと共有できます。 コンピューティングはジョブが送信されると自動的にスケールアップされ、Azure 仮想ネットワークに配置できます。 コンピューティングは、コンテナー化環境で実行され、モデルの依存関係を Docker コンテナーにパッケージ化します。

Azure Machine Learning コンピューティングを使用して、クラウド内の CPU または GPU コンピューティング ノードのクラスター全体にトレーニング プロセスを分散させることができます。 GPU を含む VM サイズの詳細については、「[GPU 最適化済み仮想マシンのサイズ](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu)」を参照してください。

> [!NOTE]
> Azure Machine Learning コンピューティングには、割り当て可能なコア数などの既定の制限があります。 詳細については、「[Azure リソースのクォータの管理と要求](https://docs.microsoft.com/azure/machine-learning/service/how-to-manage-quotas)」を参照してください。

Azure Machine Learning コンピューティング環境は、実行をスケジュールするときにオンデマンドで、または永続的なリソースとして作成できます。

### <a name="run-based-creation"></a>実行ベースの作成

Azure Machine Learning コンピューティング環境を実行時にコンピューティング ターゲットとして作成できます。 コンピューティングは実行用に自動的に作成され、実行構成で指定する **max_node** を上限にスケールアップされます。コンピューティングは実行完了後に自動的に削除されます。

> [!IMPORTANT]
> Azure Machine Learning コンピューティング環境の実行ベースの作成は現在、プレビュー状態です。 自動化されたハイパーパラメーター チューニングまたは自動化された機械学習を使用する場合は、実行ベースの作成を使用しないでください。 ハイパーパラメーター チューニングまたは自動化された機械学習を使用するには、実行を送信する前に Azure Machine Learning コンピューティング環境を作成します。

```python
from azureml.core.compute import ComputeTarget, AmlCompute

# First, list the supported VM families for Azure Machine Learning Compute
AmlCompute.supported_vmsizes()

from azureml.core.runconfig import RunConfiguration

# Create a new runconfig object
run_config = RunConfiguration()

# Signal that you want to use AmlCompute to execute the script
run_config.target = "amlcompute"

# AmlCompute is created in the same region as your workspace
# Set the VM size for AmlCompute from the list of supported_vmsizes
run_config.amlcompute.vm_size = 'STANDARD_D2_V2'

```

### <a name="persistent-compute-basic"></a>永続的なコンピューティング:Basic

永続的な Azure Machine Learning コンピューティング環境は、複数のジョブにわたって再利用できます。 コンピューティングはワークスペース内の他のユーザーと共有でき、ジョブ間で保持されます。

永続的な Azure Machine Learning コンピューティング環境リソースを作成するには、**vm_size** および **max_nodes** プロパティを指定します。 その後、Azure Machine Learning では他のプロパティに対してスマート既定値が使用されます。 コンピューティングでは、未使用時は 0 ノードまで自動的にスケールダウンされ、ジョブを実行するために必要に応じて専用 VM が作成されます。 

* **vm_size**:Azure Machine Learning コンピューティングによって作成されるノードの VM ファミリ。
* **max_nodes**:Azure Machine Learning コンピューティングでジョブを実行中に自動スケールアップする最大ノード数。

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# Choose a name for your CPU cluster
cpu_cluster_name = "cpucluster"

# Verify that the cluster doesn't already exist
try:
    cpu_cluster = ComputeTarget(workspace=ws, name=cpu_cluster_name)
    print('Found existing cluster, use it.')
except ComputeTargetException:
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                           max_nodes=4)
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)

cpu_cluster.wait_for_completion(show_output=True)

```

### <a name="persistent-compute-advanced"></a>永続的なコンピューティング:詳細

Azure Machine Learning コンピューティング環境の作成時には、いくつかの詳細プロパティも設定できます。 これらのプロパティを使用すると、永続的なクラスターを固定サイズで、またはサブスクリプションの既存の Azure 仮想ネットワーク内に作成できます。

**vm_size** および **max_nodes** プロパティと共に、次のプロパティも使用できます。

* **min_nodes**:Azure Machine Learning コンピューティング環境でジョブを実行中にスケールダウンする最小ノード数。 既定の最小値は、ゼロ (0) ノードです。
* **vm_priority**:Azure Machine Learning コンピューティング環境リソースを作成するときに使用する VM の種類。 **dedicated** (既定値) または **lowpriority** を選択します。 低優先度 VM では Azure の余剰容量が使用されます。 これらの VM は低コストですが、これらの VM が使用されると実行が割り込まれる可能性があります。
* **idle_seconds_before_scaledown**:実行完了後、**min_nodes** の値まで自動スケールアップする前に、待機するアイドル時間数です。 既定のアイドル時間は 120 秒です。
* **vnet_resourcegroup_name**:__既存__の仮想ネットワークのリソース グループ。 Azure Machine Learning コンピューティング環境は、この仮想ネットワーク内に作成されます。
* **vnet_name**:仮想ネットワークの名前。 仮想ネットワークは Azure Machine Learning ワークスペースと同じリージョンになければなりません。
* **subnet_name**:仮想ネットワーク内のサブネットの名前。 Azure Machine Learning コンピューティング環境リソースには、このサブネット範囲の IP アドレスが割り当てられます。

> [!TIP]
> 永続的な Azure Machine Learning コンピューティング環境リソースを作成するときに、**min_nodes** や **max_nodes** の数などのプロパティを更新できます。 プロパティを更新するには、プロパティの `update()` 関数を呼び出します。

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# Choose a name for your CPU cluster
cpu_cluster_name = "cpucluster"

# Verify that the cluster doesn't already exist 
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

Azure Machine Learning では、独自のコンピューティング リソースを用意してワークスペースに接続することもサポートされています。 任意のリモート VM もそのようなリソースの一種ですが、Azure Machine Learning service からアクセスできることが条件です。 リソースは、Azure VM でも、組織内またはオンプレミスのリモート サーバーでもかまいません。 具体的には、IP アドレスと資格情報 (ユーザー名とパスワードまたは SSH キー) があれば、任意のアクセス可能な VM をリモート実行に使用できます。
システムで構築済みの conda 環境、既存の Python 環境、または Docker コンテナーを使用できます。 Docker コンテナーを使用して実行するときは、Docker エンジンを VM で実行する必要があります。 リモート VM 機能は、ローカル コンピューターよりも柔軟性がある、クラウドベースの開発および実験環境が必要な場合に特に役立ちます。

> [!TIP]
> このシナリオで選択する Azure VM としては、DSVM を使用します。 この VM は、Azure における事前構成済みのデータ サイエンスおよび AI 開発環境です。VM では、機械学習開発のライフサイクル全体に対応する厳選されたツールとフレームワークが提供されます。 Azure Machine Learning での DSVM の使用方法について詳しくは、[開発環境の構成](https://docs.microsoft.com/azure/machine-learning/service/how-to-configure-environment#dsvm)に関する記事をご覧ください。

> [!WARNING]
> Azure Machine Learning は、Ubuntu を実行する仮想マシンのみサポートします。 VM を作成するとき、または既存の VM を選択するときは、Ubuntu を使用する VM を選択する必要があります。

次の手順では、SDK を使用してトレーニング ターゲットとして DSVM を構成します。

1. コンピューティング ターゲットとして既存の仮想マシンを接続するには、仮想マシンの完全修飾ドメイン名 (FQDN)、ユーザー名、およびパスワードを入力する必要があります。 例では、\<fqdn> を VM のパブリック FQDN、またはパブリック IP アドレスに置き換えます。 \<username> と \<password> を、VM の SSH ユーザー名とパスワードで置き換えます。

    ```python
    from azureml.core.compute import RemoteCompute, ComputeTarget
    
    # Create the compute config
    attach_config = RemoteCompute.attach_configuration(address = "ipaddress",
                                                       ssh_port=22,
                                                       username='<username>',
                                                       password="<password>")

    # If you use SSH instead of a password, use this code:
    #                                                  ssh_port=22,
    #                                                  username='<username>',
    #                                                  password=None,
    #                                                  private_key_file="path-to-file",
    #                                                  private_key_passphrase="passphrase")

    # Attach the compute target
    compute = ComputeTarget.attach(ws, "attach-dsvm", attach_config)

    compute.wait_for_completion(show_output=True)
    ```

1. DSVM コンピューティング ターゲット用の構成を作成します。 Docker と conda は、DSVM でトレーニング環境を作成および構成するために使用されます。

    ```python
    from azureml.core.runconfig import RunConfiguration
    from azureml.core.conda_dependencies import CondaDependencies

    # Load into memory the cpu-dsvm.runconfig file created in the previous attach operation
    run_config = RunConfiguration(framework = "python")

    # Set the compute target to the Linux DSVM
    run_config.target = compute_target_name

    # Use Docker in the remote VM
    run_config.environment.docker.enabled = True

    # Use the CPU base image
    # To use GPU in DSVM, you must also use the GPU base Docker image "azureml.core.runconfig.DEFAULT_GPU_IMAGE"
    run_config.environment.docker.base_image = azureml.core.runconfig.DEFAULT_CPU_IMAGE
    print('Base Docker image is:', run_config.environment.docker.base_image)

    # Ask the system to provision a new conda environment based on the conda_dependencies.yml file
    run_config.environment.python.user_managed_dependencies = False

    # Prepare the Docker and conda environment automatically when they're used for the first time
    run_config.prepare_environment = True

    # Specify the CondaDependencies object
    run_config.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'])

    ```

## <a id="databricks"></a>Azure Databricks

Azure Databricks は、Azure クラウド内の Apache Spark ベースの環境です。 その環境は、Azure Machine Learning パイプラインでモデルをトレーニングする際に、コンピューティング ターゲットとして使用できます。

> [!IMPORTANT]
> Azure Databricks コンピューティング ターゲットは、Machine Learning パイプラインでのみ使用できます。
>
> モデルのトレーニングに使用する前に、Azure Databricks ワークスペースを作成する必要があります。 これらのリソースを作成するには、[Azure Databricks での Spark ジョブの実行](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal)に関する記事を参照してください。

コンピューティング ターゲットとして Azure Databricks に接続するには、Azure Machine Learning SDK を使用し、次の情報を提供する必要があります。

* __コンピューティング名__:このコンピューティング リソースに割り当てる名前。
* __Databricks ワークスペース名__:Azure Databricks ワークスペースの名前。
* __アクセス トークン__:Azure Databricks に対する認証に使用するアクセス トークン。 アクセス トークンを生成するには、[認証](https://docs.azuredatabricks.net/api/latest/authentication.html)に関する記事を参照してください。

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

    # Create the attach config
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

Azure Data Lake Analytics は、Azure クラウド内のビッグ データ分析プラットフォームです。 そのプラットフォームは、Azure Machine Learning パイプラインでモデルをトレーニングする際に、コンピューティング先として使用できます。

> [!IMPORTANT]
> Azure Data Lake Analytics コンピューティング ターゲットは、Machine Learning パイプラインでのみ使用できます。
>
> モデルのトレーニングに使用する前に、Azure Data Lake Analytics ワークスペースを作成する必要があります。 このリソースを作成するには、「[Azure Data Lake Analytics の使用を開始する](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-get-started-portal)」を参照してください。

コンピューティング ターゲットとして Data Lake Analytics に接続するには、Azure Machine Learning SDK を使用し、次の情報を提供する必要があります。

* __コンピューティング名__:このコンピューティング リソースに割り当てる名前。
* __[リソース グループ]__: Data Lake Analytics アカウントを含むリソース グループ。
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
    
    # Create the attach config
    attach_config = AdlaCompute.attach_configuration(resource_group = adla_resource_group,
                                                     account_name = adla_account_name)
    # Attach the ADLA
    adla_compute = ComputeTarget.attach(
             ws,
             adla_compute_name,
             attach_config
         )
    
    adla_compute.wait_for_completion(True)
```

> [!TIP]
> Azure Machine Learning パイプラインでは、Data Lake Analytics アカウントの既定のデータ ストアに格納されたデータのみ使用されます。 必要なデータが既定以外のストアにある場合は、[DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py) 操作を使用して、モデルのトレーニングの前にデータをコピーできます。

## <a id="hdinsight"></a>Azure HDInsight 

Azure HDInsight は、ビッグ データ分析のための一般的なプラットフォームです。 そのプラットフォームでは、モデルのトレーニングに使用できる Apache Spark が提供されます。

> [!IMPORTANT]
> モデルのトレーニングに使用する前に、HDInsight クラスターを作成する必要があります。 HDInsight クラスターで Spark を作成するには、[HDInsight での Spark クラスターの作成](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-jupyter-spark-sql)に関する記事をご覧ください。
>
> クラスターを作成するとき、SSH ユーザー名とパスワードを指定する必要があります。 コンピューティング先として HDInsight を使用するときに必要になるので、これらの値をメモしておいてください。
>
> 作成された後のクラスターの FQDN は \<clustername>.azurehdinsight.net です。\<clustername> は、ユーザーがクラスターに指定した名前です。 コンピューティング先としてクラスターを使用するには、FQDN アドレス (またはクラスターのパブリック IP アドレス) が必要です。

コンピューティング先として HDInsight を構成するには、HDInsight クラスターの FQDN、ユーザー名、およびパスワードを入力する必要があります。 次の例では、SDK を使用してクラスターをワークスペースに接続します。 例では、\<fqdn> をクラスターのパブリック FQDN、またはパブリック IP アドレスに置き換えます。 \<username> と \<password> を、クラスターの SSH ユーザー名とパスワードで置き換えます。

> [!NOTE]
> クラスターの FQDN を検索するには、Azure portal にアクセスし、HDInsight クラスターを選択します。 __[概要]__ の __[URL]__ エントリで FQDN を確認できます。 FQDN を取得するには、エントリの先頭から https:\// プレフィックスを削除します。

![Azure portal で HDInsight クラスターの FQDN を取得します。](./media/how-to-set-up-training-targets/hdinsight-overview.png)

```python
from azureml.core.compute import HDInsightCompute, ComputeTarget

try:
    # Attach an HDInsight cluster as a compute target
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

# Configure the HDInsight run
# Load the runconfig object from the myhdi.runconfig file generated in the previous attach operation
run_config = RunConfiguration.load(project_object = project, run_config_name = 'myhdi')

# Ask the system to prepare the conda environment automatically when it's used for the first time
run_config.auto_prepare_environment = True
```

## <a name="submit-training-runs"></a>トレーニングの実行の送信

トレーニングの実行を送信するには、2 つの方法があります。

* `ScriptRunConfig` オブジェクトを使用してトレーニングの実行を送信します。
* `Pipeline` オブジェクトを使用してトレーニングの実行を送信します。

> [!IMPORTANT]
> Azure Databricks および Azure Datalake Analytics のコンピューティング ターゲットは、パイプラインでのみ使用できます。
>
> ローカルのコンピューティング先は、パイプラインでは使用できません。

### <a name="scriptrunconfig-object"></a>ScriptRunConfig オブジェクト

`ScriptRunConfig` オブジェクトでトレーニングの実行を送信するためのコード パターンは、すべての種類のコンピューティング先について同じです。

1. コンピューティング先の実行構成を使用して `ScriptRunConfig` オブジェクトを作成します。
1. 実行を送信します。
1. 実行が完了するのを待ちます。

次の例では、前に作成したシステム管理のローカル コンピューティング先の構成を使用します。

```python
src = ScriptRunConfig(source_directory = script_folder, script = 'train.py', run_config = run_config_system_managed)
run = exp.submit(src)
run.wait_for_completion(show_output = True)
```


### <a name="pipeline-object"></a>Pipeline オブジェクト

`Pipeline` オブジェクトでトレーニングの実行を送信するためのコード パターンは、すべての種類のコンピューティング先について同じです。

1. コンピューティング リソースに対する `Pipeline` オブジェクトに 1 つのステップを追加します。
1. パイプラインを使用して実行を送信します。
1. 実行が完了するのを待ちます。

次の例では、作成済みの Azure Databricks コンピューティング先を使用します。

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

# List of steps to run
steps = [dbStep]
pipeline = Pipeline(workspace=ws, steps=steps)
pipeline_run = Experiment(ws, 'Demo_experiment').submit(pipeline)
pipeline_run.wait_for_completion()
```

機械学習パイプラインの詳細については、「[パイプラインと Azure Machine Learning](concept-ml-pipelines.md)」を参照してください。

パイプラインを使用してモデルをトレーニングする方法を示す Jupyter Notebook の例については、[https://github.com/Azure/MachineLearningNotebooks/tree/master/pipeline](https://github.com/Azure/MachineLearningNotebooks/tree/master/pipeline) を参照してください。

## <a name="access-computes-in-the-azure-portal"></a>Azure portal でコンピューティングにアクセスする

Azure portal でワークスペースに関連付けられたコンピューティング先にアクセスできます。 

### <a name="view-compute-targets"></a>コンピューティング先を表示する

ワークスペースのコンピューティング先を表示するには、次の手順を使用します。

1. [Azure portal](https://portal.azure.com) に移動し、ワークスペースを開きます。
1. __[アプリケーション]__ で __[Compute]__ を選択します。

    ![コンピューティング先を表示する](./media/how-to-set-up-training-targets/azure-machine-learning-service-workspace.png)

### <a name="create-a-compute-target"></a>コンピューティング ターゲットを作成する

コンピューティング先の一覧を表示するには、前の手順に従います。 その後、次の手順を使用してコンピューティング先を作成します。

1. プラス記号 (+) を選択して、コンピューティング先を追加します。

    ![コンピューティング先を追加する](./media/how-to-set-up-training-targets/add-compute-target.png)

1. コンピューティング ターゲットの名前を入力します。
1. __[トレーニング]__ に使用するコンピューティングの種類として **[Machine Learning コンピューティング]** を選択します。

    > [!IMPORTANT]
    > トレーニング用の Azure Machine Learning コンピューティング環境はマネージド コンピューティング リソースとして作成することしかできません。

1. フォームに入力します。 必須のプロパティの値を指定します。特に、コンピューティングの起動に使用する **[VM ファミリ]** と **[最大ノード数]** を指定します。 
1. __作成__を選択します。
1. 一覧からコンピューティング先を選択することによって、作成操作の状態を表示します。

    ![作成操作の状態を表示するコンピューティング先を選択する](./media/how-to-set-up-training-targets/View_list.png)

1. コンピューティング先の詳細が表示されます。

    ![コンピューティング先の詳細を表示する](./media/how-to-set-up-training-targets/compute-target-details.png)

前に説明したように、コンピューティング先に対して実行を送信できるようになります。


### <a name="reuse-existing-compute-targets"></a>既存のコンピューティング先を再利用する

コンピューティング先の一覧を表示するには、前に説明した手順に従います。 その後、次の手順を使用してコンピューティング先を再利用します。

1. プラス記号 (+) を選択して、コンピューティング先を追加します。
1. コンピューティング ターゲットの名前を入力します。
1. __トレーニング__に接続するコンピューティングの種類を選択します。

    > [!IMPORTANT]
    > Azure portal からすべてのコンピューティングの種類を接続できるわけではありません。
    > 現在トレーニング用に接続できるコンピューティングの種類は次のとおりです。
    >
    > * リモート VM
    > * Azure Databricks
    > * Azure Data Lake Analytics
    > * Azure HDInsight

1. フォームに入力し、必要なプロパティの値を指定します。

    > [!NOTE]
    > パスワードよりも安全な SSH キーを使用することをお勧めします。 パスワードはブルート フォース攻撃に対して脆弱です。 SSH キーは暗号署名に依存します。 Azure Virtual Machines で使用するための SSH キーを作成する方法の詳細については、次のドキュメントを参照してください。
    >
    > * [Linux または macOS で SSH キーを作成して使用する](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys)
    > * [Windows で SSH キーを作成して使用する](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows)

1. __[接続]__ を選択します。
1. 一覧からコンピューティング先を選択することによって、接続操作の状態を表示します。

前に説明したように、これらのコンピューティング先に対して実行を送信できるようになります。

## <a name="notebook-examples"></a>ノートブックの例

たとえば、次の場所にあるノートブックをご覧ください。

* [how-to-use-azureml/training](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)
* [tutorials/img-classification-part1-training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>次の手順

* [Azure Machine Learning SDK リファレンス](https://aka.ms/aml-sdk)
* [チュートリアル:モデルをトレーニングする](tutorial-train-models-with-aml.md)
* [モデルをデプロイする場所](how-to-deploy-and-where.md)
* [Azure Machine Learning サービスで機械学習パイプラインの構築](concept-ml-pipelines.md)
