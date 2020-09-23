---
title: トレーニングとデプロイのコンピューティングを作成する (Python)
titleSuffix: Azure Machine Learning
description: Azure Machine Learning Python SDK を使用し、機械学習のためにトレーニングおよびデプロイのコンピューティング リソース (コンピューティング先) を作成します。
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 07/08/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperfq1
ms.openlocfilehash: ac440db4c1dbddd317743e2d681a62251624d9bd
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/22/2020
ms.locfileid: "90898134"
---
# <a name="create-compute-targets-for-model-training-and-deployment-with-python-sdk"></a>Python SDK を使用してモデルのトレーニングとデプロイのためのコンピューティング先を作成する

この記事では、Azure Machine Learning Python SDK を使用してコンピューティング ターゲットを作成し、管理します。 コンピューティング先は次を利用して作成し、管理することもできます。
* [Azure Machine Learning studio](how-to-create-attach-compute-studio.md) 
* Azure Machine Learning 用 [CLI 拡張機能](reference-azure-machine-learning-cli.md#resource-management)
* Azure Machine Learning 用 [VS Code 拡張機能](how-to-manage-resources-vscode.md#compute-clusters)


## <a name="prerequisites"></a>前提条件

* Azure サブスクリプションをお持ちでない場合は、開始する前に無料アカウントを作成してください。 [無料版または有料版の Azure Machine Learning](https://aka.ms/AMLFree) を試してください
* [Azure Machine Learning SDK for Python](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true)
* [Azure Machine Learning ワークスペース](how-to-manage-workspace.md)

## <a name="limitations"></a>制限事項

* ワークスペースから**同じコンピューティングに対して複数のアタッチメントを同時に作成することは避けてください**。 たとえば、2 つの異なる名前を使用して 1 つの Azure Kubernetes Service クラスターをワークスペースにアタッチすることが該当します。 アタッチを繰り返すたびに、先行する既存のアタッチメントが切断されます。

    TLS またはその他のクラスター構成設定を変更するためなど、コンピューティング先を再アタッチする場合は、既存のアタッチメントを先に削除する必要があります。

* このドキュメントに記載されている一部のシナリオは __プレビュー__としてマークされています。 プレビュー段階の機能はサービス レベル アグリーメントなしで提供されており、運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="whats-a-compute-target"></a>コンピューティング先とは何か

Azure Machine Learning では、さまざまなリソースまたは環境でご利用のモデルをトレーニングでき、それらを総称して[__コンピューティング先__](concept-azure-machine-learning-architecture.md#compute-targets)と呼びます。 コンピューティング先は、ローカル マシンでも、Azure Machine Learning コンピューティング、Azure HDInsight、リモート仮想マシンなどのクラウド リソースでもかまいません。  [モデルをデプロイする場所と方法](how-to-deploy-and-where.md)に関するページで説明されているように、モデルのデプロイ用のコンピューティング先を作成することもできます。

## <a name="compute-targets-for-training"></a>モデル トレーニング用のコンピューティング先

Azure Machine Learning では、異なるコンピューティング先に対してさまざまなサポートが提供されています。 典型的なモデル開発ライフサイクルは、少量のデータを用いた開発と実験から始まります。 この段階では、ローカル環境を使用することをお勧めします。 たとえば、ローカル コンピューターやクラウドベースの VM などです。 より大規模なデータ セットにトレーニングをスケールアップする、または分散トレーニングを実行する段階で、Azure Machine Learning コンピューティングを使用して、実行を送信するたびに自動スケーリングするシングルノードまたはマルチノード クラスターを作成することをお勧めします。 独自のコンピューティング リソースを接続することもできますが、以下で説明するように、シナリオによってサポートが異なる場合があります:

[!INCLUDE [aml-compute-target-train](../../includes/aml-compute-target-train.md)]

> [!NOTE]
> Azure Machine Learning コンピューティング クラスターは、永続的なリソースとして作成するか、または実行を要求するときに動的に作成できます。 実行ベースの作成では、トレーニングの実行の完了後にコンピューティング ターゲットが削除されるため、この方法で作成されたコンピューティング ターゲットは再利用できません。

以下のセクションでは、次のコンピューティング先を構成する方法を示します。

* [ローカル コンピューター](#local)
* [Azure Machine Learning コンピューティング クラスター](#amlcompute)
* [Azure Machine Learning コンピューティング インスタンス](#instance)
* [リモート仮想マシン](#vm)
* [Azure HDInsight](#hdinsight)

## <a name="compute-targets-for-inference"></a>推論のコンピューティング先

推論を実行すると、Azure Machine Learning によって、モデルおよびそれを使用するために必要な関連リソースをホストする Docker コンテナーが作成されます。 このコンテナーはその後、次のいずれかのデプロイ シナリオで使用されます。

* リアルタイムの推論に使用される __Web サービス__ として。 Web サービスのデプロイでは、次のいずれかのコンピューティング先が使用されます。

    * [ローカル コンピューター](#local)
    * [Azure Machine Learning コンピューティング インスタンス](#instance)
    * [Azure Container Instances](#aci)
    * [Azure Kubernetes Services](how-to-create-attach-kubernetes.md)
    * Azure Functions (プレビュー)。 Azure Functions へのデプロイでは、Azure Machine Learning のみに依存して Docker コンテナーが構築されます。 そこから、Azure Functions を使用してデプロイされます。 詳細については、「[Azure Functions に機械学習モデルをデプロイする (プレビュー)](how-to-deploy-functions.md)」を参照してください。

* データのバッチを定期的に処理するために使用される__バッチ推論__エンドポイントとして。 バッチ推論では、[Azure Machine Learning コンピューティング クラスター](#amlcompute)が使用されます。

* __IoT デバイス__に (プレビュー)。 IoT デバイスへのデプロイでは、Azure Machine Learning のみに依存して Docker コンテナーが構築されます。 そこから、Azure IoT Edge を使用してデプロイされます。 詳細については、[IoT Edge モジュールとしてのデプロイ (プレビュー)](/azure/iot-edge/tutorial-deploy-machine-learning) に関する記事を参照してください。

## <a name="local-computer"></a><a id="local"></a>ローカル コンピューター

**トレーニング**のためにローカル コンピューターを使用する場合は、コンピューティング先を作成する必要はありません。  ローカル コンピューターから[トレーニング実行を送信する](how-to-set-up-training-targets.md)だけで十分です。

ローカル コンピューターを**推論**に使用する場合は、Docker がインストールされている必要があります。 デプロイを実行するには、[LocalWebservice.deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py#deploy-configuration-port-none-) を使用して、Web サービスが使用するポートを定義します。 次に、「[Azure Machine Learning を使用してモデルをデプロイする](how-to-deploy-and-where.md)」の説明に従って通常のデプロイ プロセスを使用します。

## <a name="azure-machine-learning-compute-cluster"></a><a id="amlcompute"></a>Azure Machine Learning コンピューティング クラスター

Azure Machine Learning コンピューティング クラスターは、シングルノードまたはマルチノードのコンピューティングを簡単に作成できるマネージド コンピューティング インフラストラクチャです。 コンピューティングは、リソースとしてワークスペース リージョン内に作成され、ワークスペース内の他のユーザーと共有できます。 コンピューティングはジョブが送信されると自動的にスケールアップされ、Azure 仮想ネットワークに配置できます。 コンピューティングはコンテナー化環境で実行され、モデルの依存関係が [Docker コンテナー](https://www.docker.com/why-docker)にパッケージ化されます。

Azure Machine Learning コンピューティングを使用して、クラウド内の CPU または GPU コンピューティング ノードのクラスター全体にトレーニングまたはバッチ推論のプロセスを分散させることができます。 GPU を含む VM サイズの詳細については、「[GPU 最適化済み仮想マシンのサイズ](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu)」を参照してください。 

Azure Machine Learning コンピューティングには、割り当て可能なコア数などの既定の制限があります。 詳細については、「[Azure リソースのクォータの管理と要求](how-to-manage-quotas.md)」を参照してください。

> [!TIP]
> 必要なコア数に十分に対応するクォータを備えている限り、クラスターは一般に、最大で 100 ノードまでスケールアップすることができます。 既定では、たとえば、MPI ジョブをサポートするために、クラスターは、そのノード間でノード間通信を有効にした状態でセットアップされます。 ただし、[サポート チケットを作成](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)し、ご利用のサブスクリプション、ワークスペース、またはノード間通信を無効にする特定のクラスターをリストに登録することを要求するだけで、ご利用のクラスターを数千のノードにスケーリングすることができます。 

Azure Machine Learning コンピューティングは、複数回の実行で再利用できます。 コンピューティングは、ワークスペース内の他のユーザーと共有することができ、複数回の実行の間で保持されます。この場合、送信された実行の回数およびクラスター上で設定された max_nodes に基づいてノードは自動的にスケールアップまたはスケールダウンされます。 min_nodes 設定では、使用可能な最小ノード数が制御されます。

[!INCLUDE [min-nodes-note](../../includes/machine-learning-min-nodes.md)]

1. **作成してアタッチする**:Python で永続的な Azure Machine Learning コンピューティング リソースを作成するには、**vm_size** および **max_nodes** プロパティを指定します。 その後、Azure Machine Learning では他のプロパティに対してスマート既定値が使用されます。 コンピューティングは、使用されていないときは、0 ノードまで自動的にスケールダウンされます。   必要に応じて、ジョブ実行専用の VM が作成されます。
    
    * **vm_size**:Azure Machine Learning コンピューティングによって作成されるノードの VM ファミリ。
    * **max_nodes**:Azure Machine Learning コンピューティングでジョブを実行中に自動スケールアップする最大ノード数。
    
   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=cpu_cluster)]

   Azure Machine Learning コンピューティングを作成するときに、いくつかの詳細プロパティも設定できます。 これらのプロパティを使用すると、永続的なクラスターを固定サイズで、またはサブスクリプションの既存の Azure 仮想ネットワーク内に作成できます。  詳しくは、「[AmlCompute クラス](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py
    )」をご覧ください。

    または、[Azure Machine Learning Studio](how-to-create-attach-compute-studio.md#portal-create) で、永続的な Azure Machine Learning コンピューティング リソースを作成してアタッチすることもできます。

これでコンピューティングをアタッチしたので、次のステップは、[トレーニング実行を送信](how-to-set-up-training-targets.md)または[バッチ推論の実行](how-to-use-parallel-run-step.md)のいずれかになります。

 ### <a name="lower-your-compute-cluster-cost"></a><a id="low-pri-vm"></a>コンピューティング クラスターのコストを削減する

[優先順位の低い VM](concept-plan-manage-cost.md#low-pri-vm) を使用して、一部または全部のワークロードを実行することもできます。 これらの VM では、可用性が保証されず、使用中に割り込まれる可能性があります。 割り込まれたジョブは、再開されるのではなく、最初から開始し直されます。 

優先順位の低い VM を指定するには、次のいずれかの方法を使用します。
    
* VM を作成するときに、スタジオで **[低優先度]** を選択します。
    
* Python SDK では、プロビジョニングの構成で `vm_priority` 属性を設定します。  
    
    ```python
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                                vm_priority='lowpriority',
                                                                max_nodes=4)
    ```
    
* CLI を使用して、`vm-priority` を設定します。
    
    ```azurecli-interactive
    az ml computetarget create amlcompute --name lowpriocluster --vm-size Standard_NC6 --max-nodes 5 --vm-priority lowpriority
    ```

 ### <a name="set-up-managed-identity"></a><a id="managed-identity"></a> マネージド ID を設定する

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-intro.md)]

プロビジョニング構成でマネージド ID を構成します。  
    
* システム割り当てマネージド ID:
    ```python
    # configure cluster with a system-assigned managed identity
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                            max_nodes=5,
                                                            identity_type="SystemAssigned",
                                                            )
    ```

* ユーザー割り当てマネージド ID: 

    ```python
    # configure cluster with a user-assigned managed identity
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                            max_nodes=5,
                                                            identity_type="UserAssigned",
                                                            identity_id=['/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'])

    cpu_cluster_name = "cpu-cluster"
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)
    ```

既存のコンピューティング クラスターにマネージド ID を追加します。  
    
* システム割り当てマネージド ID:

    ```python
    # add a system-assigned managed identity
    cpu_cluster.add_identity(identity_type="SystemAssigned")
    ````

* ユーザー割り当てマネージド ID:

    ```python
    # add a user-assigned managed identity
    cpu_cluster.add_identity(identity_type="UserAssigned", 
                                identity_id=['/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'])
    ```

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-note.md)]

#### <a name="managed-identity-usage"></a>マネージド ID の使用

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-default.md)]


## <a name="azure-machine-learning-compute-instance"></a><a id="instance"></a>Azure Machine Learning コンピューティング インスタンス

[Azure Machine Learning コンピューティング インスタンス ](concept-compute-instance.md) は、単一の VM を簡単に作成できるマネージド コンピューティング インフラストラクチャです。 コンピューティングはワークスペース リージョン内に作成されますが、コンピューティング クラスターとは異なり、1 つのインスタンスをワークスペース内の他のユーザーと共有することはできません。 さらに、インスタンスは自動的にスケールダウンされません。  継続的な料金を防止するには、リソースを停止する必要があります。

コンピューティング インスタンスは複数のジョブを並列に実行でき、ジョブ キューを備えています。 

コンピューティング インスタンスは、企業で SSH ポートを開かなくても、[仮想ネットワーク環境](how-to-enable-virtual-network.md#compute-instance)でジョブを安全に実行できます。 ジョブはコンテナー化された環境で実行され、モデルの依存関係が Docker コンテナーにパッケージ化されます。 

1. **作成してアタッチする**: 
    
    ```python
    import datetime
    import time
    
    from azureml.core.compute import ComputeTarget, ComputeInstance
    from azureml.core.compute_target import ComputeTargetException
    
    # Choose a name for your instance
    # Compute instance name should be unique across the azure region
    compute_name = "ci{}".format(ws._workspace_id)[:10]
    
    # Verify that instance does not exist already
    try:
        instance = ComputeInstance(workspace=ws, name=compute_name)
        print('Found existing instance, use it.')
    except ComputeTargetException:
        compute_config = ComputeInstance.provisioning_configuration(
            vm_size='STANDARD_D3_V2',
            ssh_public_access=False,
            # vnet_resourcegroup_name='<my-resource-group>',
            # vnet_name='<my-vnet-name>',
            # subnet_name='default',
            # admin_user_ssh_public_key='<my-sshkey>'
        )
        instance = ComputeInstance.create(ws, compute_name, compute_config)
        instance.wait_for_completion(show_output=True)
    ```

これでコンピューティングをアタッチし、実行を構成したので、次のステップは、[トレーニング実行の送信](how-to-set-up-training-targets.md)または[推論のためのモデルのデプロイ](how-to-deploy-local-container-notebook-vm.md)のいずれかになります。

## <a name="azure-container-instance"></a><a id="aci"></a>Azure Container Instances

Azure Container Instances (ACI) は、モデルのデプロイ時に動的に作成されます。 他の方法では、ACI を作成したり、ワークスペースにアタッチしたりすることはできません。 詳細については、[Azure Container Instances へのモデルのデプロイ](how-to-deploy-azure-container-instance.md)に関するページを参照してください。

## <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

Azure Kubernetes Service (AKS) を Azure Machine Learning と組み合わせて使用すると、さまざまな構成オプションが使用できます。 詳細については、[Azure Kubernetes Service を作成してアタッチする方法](how-to-create-attach-kubernetes.md)に関するページを参照してください。

## <a name="remote-virtual-machines"></a><a id="vm"></a>リモート仮想マシン

Azure Machine Learning では、独自のコンピューティング リソースを用意してワークスペースに接続することもサポートされています。 任意のリモート VM もそのようなリソースの一種ですが、Azure Machine Learning からアクセスできることが条件です。 リソースは、Azure VM でも、組織内またはオンプレミスのリモート サーバーでもかまいません。 具体的には、IP アドレスと資格情報 (ユーザー名とパスワードまたは SSH キー) があれば、任意のアクセス可能な VM をリモート実行に使用できます。

システムで構築済みの conda 環境、既存の Python 環境、または Docker コンテナーを使用できます。 Docker コンテナーで実行するには、Docker エンジンを VM で実行する必要があります。 この機能は、ローカル コンピューターよりも柔軟性がある、クラウドベースの開発/実験環境が必要な場合に特に役立ちます。

このシナリオ向けに選択する Azure VM としては、Azure Data Science 仮想マシン (DSVM) を使用します。 この VM は、Azure での事前構成済みのデータ サイエンスおよび AI 開発環境です。 その VM では、完全なライフサイクルの機械学習開発用に精選されたツールとフレームワークが提供されます。 Azure Machine Learning での DSVM の使用方法について詳しくは、[開発環境の構成](https://docs.microsoft.com/azure/machine-learning/how-to-configure-environment#dsvm)に関する記事をご覧ください。

1. **作成**:モデルのトレーニングに使用する DSVM を事前に作成します。 このリソースの作成については、「[Linux (Ubuntu) データ サイエンス仮想マシンのプロビジョニング](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro)」をご覧ください。

    > [!WARNING]
    > Azure Machine Learning では、**Ubuntu** を実行する仮想マシンのみがサポートされます。 VM を作成するとき、または既存の VM を選択するときは、Ubuntu を使用する VM を選択する必要があります。
    > 
    > さらに Azure Machine Learning では、仮想マシンに__パブリック IP アドレス__が必要です。

1. **アタッチする**:コンピューティング ターゲットとして既存の仮想マシンを接続するには、仮想マシンのリソース ID、ユーザー名、およびパスワードを入力する必要があります。 VM のリソース ID は、次の文字列形式を使用して、サブスクリプション ID、リソース グループ名、VM 名から作成できます: `/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Compute/virtualMachines/<vm_name>`

 
   ```python
   from azureml.core.compute import RemoteCompute, ComputeTarget

   # Create the compute config 
   compute_target_name = "attach-dsvm"
   
   attach_config = RemoteCompute.attach_configuration(resource_id='<resource_id>',
                                                   ssh_port=22,
                                                   username='<username>',
                                                   password="<password>")

   # Attach the compute
   compute = ComputeTarget.attach(ws, compute_target_name, attach_config)

   compute.wait_for_completion(show_output=True)
   ```

   または、[Azure Machine Learning Studio を使用して](how-to-create-attach-compute-studio.md#attached-compute)、DSVM をワークスペースにアタッチすることもできます。

    > [!WARNING]
    > ワークスペースから同じ DSVM に対して複数のアタッチメントを同時に作成することは避けてください。 アタッチを繰り返すたびに、先行する既存のアタッチメントが切断されます。

1. **構成する**:DSVM コンピューティング先用の実行構成を作成します。 Docker と conda は、DSVM でトレーニング環境を作成および構成するために使用されます。

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/dsvm.py?name=run_dsvm)]


コンピューティングをアタッチし、実行を構成したので、次のステップでは[トレーニング実行を送信](how-to-set-up-training-targets.md)します。

## <a name="azure-hdinsight"></a><a id="hdinsight"></a>Azure HDInsight 

Azure HDInsight は、ビッグ データ分析のための一般的なプラットフォームです。 そのプラットフォームでは、モデルのトレーニングに使用できる Apache Spark が提供されます。

1. **作成**:モデルのトレーニングに使用する HDInsight クラスターを、事前に作成します。 HDInsight クラスターで Spark を作成するには、[HDInsight での Spark クラスターの作成](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-jupyter-spark-sql)に関する記事をご覧ください。 

    > [!WARNING]
    > Azure Machine Learning では、HDInsight クラスターに__パブリック IP アドレス__が必要です。

    クラスターを作成するとき、SSH ユーザー名とパスワードを指定する必要があります。 コンピューティング先として HDInsight を使用するときに必要になるので、これらの値をメモしておいてください。
    
    クラスターが作成された後、ホスト名 \<clustername>-ssh.azurehdinsight.net でそれに接続します。\<clustername> はクラスターに指定した名前です。 

1. **アタッチする**:コンピューティング先として HDInsight クラスターをアタッチするには、HDInsight クラスターのリソース ID、ユーザー名、およびパスワードを指定する必要があります。 HDInsight クラスターのリソース ID は、次の文字列形式を使用して、サブスクリプション ID、リソース グループ名、HDInsight クラスター名から作成できます: `/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.HDInsight/clusters/<cluster_name>`

    ```python
   from azureml.core.compute import ComputeTarget, HDInsightCompute
   from azureml.exceptions import ComputeTargetException

   try:
    # if you want to connect using SSH key instead of username/password you can provide parameters private_key_file and private_key_passphrase

    attach_config = HDInsightCompute.attach_configuration(resource_id='<resource_id>',
                                                          ssh_port=22, 
                                                          username='<ssh-username>', 
                                                          password='<ssh-pwd>')
    hdi_compute = ComputeTarget.attach(workspace=ws, 
                                       name='myhdi', 
                                       attach_configuration=attach_config)

   except ComputeTargetException as e:
    print("Caught = {}".format(e.message))

   hdi_compute.wait_for_completion(show_output=True)
   ```

   または、[Azure Machine Learning Studio を使用して](how-to-create-attach-compute-studio.md#attached-compute)、HDInsight クラスターをワークスペースにアタッチすることもできます。

    > [!WARNING]
    > ワークスペースから同じ HDInsight に対して複数のアタッチメントを同時に作成することは避けてください。 アタッチを繰り返すたびに、先行する既存のアタッチメントが切断されます。

1. **構成する**:HDI コンピューティング先用の実行構成を作成します。 

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/hdi.py?name=run_hdi)]


コンピューティングをアタッチし、実行を構成したので、次のステップでは[トレーニング実行を送信](how-to-set-up-training-targets.md)します。

## <a name="azure-batch"></a><a id="azbatch"></a>Azure Batch 

Azure Batch は、大規模な並列コンピューティングやハイパフォーマンス コンピューティング (HPC) のアプリケーションをクラウドで効率的に実行するために使用されます。 AzureBatchStep を Azure Machine Learning Pipeline で使用して、マシンの Azure Batch プールにジョブを送信できます。

コンピューティング ターゲットとして Azure Batch に接続するには、Azure Machine Learning SDK を使用し、次の情報を提供する必要があります。

-    **Azure Batch のコンピューティング名**:ワークスペース内のコンピューティングに使用されるフレンドリ名
-    **Azure Batch アカウント名**:Azure Batch アカウントの名前
-    **リソース グループ**:Azure Batch アカウントを含むリソース グループ。

次のコードは、コンピューティング ターゲットとして Azure Batch に接続する方法を示しています。

```python
from azureml.core.compute import ComputeTarget, BatchCompute
from azureml.exceptions import ComputeTargetException

# Name to associate with new compute in workspace
batch_compute_name = 'mybatchcompute'

# Batch account details needed to attach as compute to workspace
batch_account_name = "<batch_account_name>"  # Name of the Batch account
# Name of the resource group which contains this account
batch_resource_group = "<batch_resource_group>"

try:
    # check if the compute is already attached
    batch_compute = BatchCompute(ws, batch_compute_name)
except ComputeTargetException:
    print('Attaching Batch compute...')
    provisioning_config = BatchCompute.attach_configuration(
        resource_group=batch_resource_group, account_name=batch_account_name)
    batch_compute = ComputeTarget.attach(
        ws, batch_compute_name, provisioning_config)
    batch_compute.wait_for_completion()
    print("Provisioning state:{}".format(batch_compute.provisioning_state))
    print("Provisioning errors:{}".format(batch_compute.provisioning_errors))

print("Using Batch compute:{}".format(batch_compute.cluster_resource_id))
```

> [!WARNING]
> ワークスペースから同じ Azure Batch に対して複数のアタッチメントを同時に作成することは避けてください。 アタッチを繰り返すたびに、先行する既存のアタッチメントが切断されます。

### <a name="azure-databricks"></a><a id="databricks"></a>Azure Databricks

Azure Databricks は、Azure クラウド内の Apache Spark ベースの環境です。 これは、Azure Machine Learning パイプラインでコンピューティング先として使用できます。

使用する前に、Azure Databricks ワークスペースを作成します。 ワークスペース リソースを作成するには、[Azure Databricks での Spark ジョブの実行](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal)に関するドキュメントを参照してください。

コンピューティング先として Azure Databricks をアタッチするには、次の情報を指定します。

* __Databricks コンピューティング名__:このコンピューティング リソースに割り当てる名前。
* __Databricks ワークスペース名__:Azure Databricks ワークスペースの名前。
* __Databricks アクセス トークン__:Azure Databricks に対する認証に使用するアクセス トークン。 アクセス トークンを生成するには、[認証](https://docs.azuredatabricks.net/dev-tools/api/latest/authentication.html)に関するドキュメントを参照してください。

次のコードでは、Azure Machine Learning SDK を使用してコンピューティング先として Azure Databricks をアタッチする方法を示します (__Databricks ワークスペースは、AML ワークスペースと同じサブスクリプション内に存在する必要があります__)。

```python
import os
from azureml.core.compute import ComputeTarget, DatabricksCompute
from azureml.exceptions import ComputeTargetException

databricks_compute_name = os.environ.get(
    "AML_DATABRICKS_COMPUTE_NAME", "<databricks_compute_name>")
databricks_workspace_name = os.environ.get(
    "AML_DATABRICKS_WORKSPACE", "<databricks_workspace_name>")
databricks_resource_group = os.environ.get(
    "AML_DATABRICKS_RESOURCE_GROUP", "<databricks_resource_group>")
databricks_access_token = os.environ.get(
    "AML_DATABRICKS_ACCESS_TOKEN", "<databricks_access_token>")

try:
    databricks_compute = ComputeTarget(
        workspace=ws, name=databricks_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('databricks_compute_name {}'.format(databricks_compute_name))
    print('databricks_workspace_name {}'.format(databricks_workspace_name))
    print('databricks_access_token {}'.format(databricks_access_token))

    # Create attach config
    attach_config = DatabricksCompute.attach_configuration(resource_group=databricks_resource_group,
                                                           workspace_name=databricks_workspace_name,
                                                           access_token=databricks_access_token)
    databricks_compute = ComputeTarget.attach(
        ws,
        databricks_compute_name,
        attach_config
    )

    databricks_compute.wait_for_completion(True)
```

詳細な例については、GitHub の[サンプル ノートブック](https://aka.ms/pl-databricks)を参照してください。

> [!WARNING]
> ワークスペースから同じ Azure Databricks に対して複数のアタッチメントを同時に作成することは避けてください。 アタッチを繰り返すたびに、先行する既存のアタッチメントが切断されます。

### <a name="azure-data-lake-analytics"></a><a id="adla"></a>Azure Data Lake Analytics

Azure Data Lake Analytics は、Azure クラウド内のビッグ データ分析プラットフォームです。 これは、Azure Machine Learning パイプラインでコンピューティング先として使用できます。

使用する前に、Azure Data Lake Analytics アカウントを作成します。 このリソースを作成するには、「[Azure Data Lake Analytics の使用を開始する](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-get-started-portal)」を参照してください。

コンピューティング ターゲットとして Data Lake Analytics に接続するには、Azure Machine Learning SDK を使用し、次の情報を提供する必要があります。

* __コンピューティング名__:このコンピューティング リソースに割り当てる名前。
* __リソース グループ__:Data Lake Analytics アカウントを含むリソース グループ。
* __アカウント名__:Data Lake Analytics アカウント名です。

次のコードは、コンピューティング ターゲットとして Data Lake Analytics に接続する方法を示しています。

```python
import os
from azureml.core.compute import ComputeTarget, AdlaCompute
from azureml.exceptions import ComputeTargetException


adla_compute_name = os.environ.get(
    "AML_ADLA_COMPUTE_NAME", "<adla_compute_name>")
adla_resource_group = os.environ.get(
    "AML_ADLA_RESOURCE_GROUP", "<adla_resource_group>")
adla_account_name = os.environ.get(
    "AML_ADLA_ACCOUNT_NAME", "<adla_account_name>")

try:
    adla_compute = ComputeTarget(workspace=ws, name=adla_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('adla_compute_name {}'.format(adla_compute_name))
    print('adla_resource_id {}'.format(adla_resource_group))
    print('adla_account_name {}'.format(adla_account_name))
    # create attach config
    attach_config = AdlaCompute.attach_configuration(resource_group=adla_resource_group,
                                                     account_name=adla_account_name)
    # Attach ADLA
    adla_compute = ComputeTarget.attach(
        ws,
        adla_compute_name,
        attach_config
    )

    adla_compute.wait_for_completion(True)
```

詳細な例については、GitHub の[サンプル ノートブック](https://aka.ms/pl-adla)を参照してください。

> [!WARNING]
> ワークスペースから同じ ADLA に対して複数のアタッチメントを同時に作成することは避けてください。 アタッチを繰り返すたびに、先行する既存のアタッチメントが切断されます。

> [!TIP]
> Azure Machine Learning パイプラインは、Data Lake Analytics アカウントの既定のデータ ストアに格納されたデータのみ使用できます。 使用する必要があるデータが既定以外のストアにある場合は、[`DataTransferStep`](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py&preserve-view=true) を使用して、トレーニングの前にデータをコピーできます。

## <a name="notebook-examples"></a>ノートブックの例

さまざまなコンピューティング先を使用したトレーニングの例については、以下のノートブックをご覧ください。
* [how-to-use-azureml/training](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)
* [tutorials/img-classification-part1-training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/image-classification-mnist-data/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>次のステップ

* コンピューティング リソースを使用し、[トレーニング実行を送信](how-to-set-up-training-targets.md)します。
* [チュートリアル:モデルのトレーニング](tutorial-train-models-with-aml.md)に関する記事では、マネージド コンピューティング先を使用してモデルをトレーニングします。
* より優れたモデルを構築するために、[ハイパーパラメーター](how-to-tune-hyperparameters.md)を効率的に調整する方法を学習します。
* モデルのトレーニングが済んだら、[モデルをデプロイする方法と場所](how-to-deploy-and-where.md)を確認します。
* [Azure Machine Learning と Azure Virtual Network を使用する](how-to-enable-virtual-network.md)
