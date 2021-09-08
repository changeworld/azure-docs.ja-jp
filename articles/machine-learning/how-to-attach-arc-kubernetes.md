---
title: Azure Arc 対応機械学習 (プレビュー)
description: Azure Machine Learning で機械学習モデルをトレーニングする Azure Arc 対応 Kubernetes クラスターを構成する
titleSuffix: Azure Machine Learning
author: luisquintanilla
ms.author: luquinta
ms.service: machine-learning
ms.subservice: core
ms.date: 06/18/2021
ms.topic: how-to
ms.openlocfilehash: 81d4d17b4efdc1dd7d59cb566f8a9ef2e6742aac
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2021
ms.locfileid: "122966768"
---
# <a name="configure-azure-arc-enabled-machine-learning-preview"></a>Azure Arc 対応機械学習を構成する (プレビュー)

トレーニング用に Azure Arc 対応機械学習を構成する方法について説明します。

## <a name="what-is-azure-arc-enabled-machine-learning"></a>Azure Arc 対応機械学習とは

Azure Arc を使用することで、ユーザーはオンプレミス、マルチクラウド、エッジを問わず、あらゆる Kubernetes 環境で Azure サービスを実行することができます。

Azure Arc 対応機械学習を使用すると、Azure Arc 対応 Kubernetes クラスターを構成し、Azure Machine Learning で機械学習モデルをトレーニングおよび管理することができます。

Azure Arc 対応機械学習では、次のトレーニング シナリオがサポートされています。

* CLI (v2) を使用したモデルのトレーニング
  * 分散トレーニング
  * ハイパーパラメーターのスイープ
* Azure Machine Learning Python SDK を使用したモデルのトレーニング
  * ハイパーパラメーターの調整
* 機械学習パイプラインをビルドして使用する
* 送信プロキシ サーバーを使用したオンプレミスでのモデルのトレーニング
* NFS データストアを使用したオンプレミスでのモデルのトレーニング

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free)してください。
* Azure Arc 対応 Kubernetes クラスター。 詳細については、[既存の Kubernetes クラスターを Azure Arc に接続する方法に関するクイックスタート ガイド](../azure-arc/kubernetes/quickstart-connect-cluster.md)を参照してください。
* [Azure Arc 対応 Kubernetes クラスターの拡張機能の前提条件](../azure-arc/kubernetes/extensions.md#prerequisites)を満たしていること。
  * Azure CLI バージョン >= 2.24.0
  * Azure CLI k8s 拡張機能のバージョン >= 0.4.3
* Azure Machine Learning ワークスペース。 まだお持ちでない場合は、開始する前に[ワークスペースを作成](how-to-manage-workspace.md?tabs=python)してください。
  * Azure Machine Learning Python SDK のバージョン >= 1.30

## <a name="deploy-azure-machine-learning-extension"></a>Azure Machine Learning 拡張機能の展開

Azure Arc 対応 Kubernetes には、Azure Policy 定義、監視、機械学習など、さまざまなエージェントをインストールできるクラスター拡張機能があります。 Azure Machine Learning では、Kubernetes クラスターに Azure Machine Learning エージェントを展開するために、*Microsoft.AzureML.Kubernetes* クラスター拡張機能を使用する必要があります。 Azure Machine Learning 拡張機能をインストールしたら、クラスターを Azure Machine Learning ワークスペースに接続し、トレーニングに使用できます。

`k8s-extension` Azure CLI 拡張機能を使用して、Azure Arc 対応 Kubernetes クラスターに Azure Machine Learning 拡張機能を展開します。

1. Azure にログインする
    
    ```azurecli
    az login
    az account set --subscription <your-subscription-id>
    ```

1. Azure Machine Learning 拡張機能の展開

    ```azurecli
    az k8s-extension create --name amlarc-compute --extension-type Microsoft.AzureML.Kubernetes --configuration-settings enableTraining=True  --cluster-type connectedClusters --cluster-name <your-connected-cluster-name> --resource-group <resource-group> --scope cluster
    ```

    >[!IMPORTANT]
    > トレーニングのために Azure Arc 対応クラスターを有効にするには、`enableTraining` を **[True]** に設定する必要があります。 このコマンドを実行すると、Azure Service Bus と Azure Relay リソースが、Arc クラスターと同じリソース グループの下に作成されます。 これらのリソースは、クラスターとの通信に使用されます。 これらを変更すると、トレーニングのコンピューティング先として使用されている接続されたクラスターが切断されます。

    モデル トレーニング用の Azure Machine Learning 拡張機能を展開する際には、次の設定を構成することもできます。

    |構成設定のキー名  |説明  |
    |--|--|
    | ```enableTraining``` | 既定値は `False` です。 機械学習モデルをトレーニングするための拡張機能インスタンスを作成するには、`True` に設定します。  |
    |```logAnalyticsWS```  | 既定値は `False` です。 Azure Machine Learning 拡張機能は、Azure LogAnalytics ワークスペースと統合されています。 LogAnalytics ワークスペースを使用してログの表示と分析機能を利用できるようにするには、`True` に設定します。 LogAnalytics ワークスペースのコストが適用される場合があります。   |
    |```installNvidiaDevicePlugin```  | 既定値は `True` です。 Nvidia GPU ハードウェアでトレーニングを行うには、Nvidia デバイス プラグインが必要です。 Azure Machine Learning 拡張機能を使用すると、Kubernetes クラスターに GPU ハードウェアがあるかどうかに関係なく、Azure Machine Learning インスタンスの作成時に Nvidia デバイス プラグインが既定でインストールされます。 トレーニングに GPU を使用する予定がない場合、または Nvidia デバイス プラグインが既にインストールされている場合は、`False` に設定します。  |
    |```installBlobfuseSysctl```  | "enableTraining=True" の場合、規定値は `True` です。 トレーニングを行うには、Blobfuse 1.3.7 が必要です。 拡張機能のインスタンスを作成すると、Azure Machine Learning によって既定で Blobfuse がインストールされます。 Kubernetes クラスターに Blobfuse 1.37 が既にインストールされている場合は、この構成設定を `False` に設定します。   |
    |```installBlobfuseFlexvol```  | "enableTraining=True" の場合、規定値は `True` です。 トレーニングを行うには、Blobfuse Flexvolume が必要です。 Azure Machine Learning を使用すると、既定で Blobfuse Flexvolume が既定のパスにインストールされます。 Kubernetes クラスターに Blobfuse Flexvolume が既にインストールされている場合は、この構成設定を `False` に設定します。   |
    |```volumePluginDir```  | Blobfuse Flexvolume をインストールするホスト パスです。 "enableTraining=True" の場合にのみ適用されます。 既定では、Azure Machine Learning によって Blobfuse Flexvolumeblobfuse は */etc/kubernetes/volumeplugins* の既定のパスの下にインストールされます。 カスタムのインストール場所を指定するには、この構成設定を指定してください。'''   |

    > [!WARNING]
    > クラスターに Nvidia デバイスプラグイン、Blobfuse、Blobfuse Flexvolume が既にインストールされている場合、これらを再インストールすると拡張機能のインストール エラーが発生する可能性があります。 インストール エラーを回避するには、`installNvidiaDevicePlugin`、`installBlobfuseSysctl`、および `installBlobfuseFlexvol` を `False` に設定してください。

1. AzureML 拡張機能の展開を確認する

    ```azurecli
    az k8s-extension show --name amlarc-compute --cluster-type connectedClusters --cluster-name <your-connected-cluster-name> --resource-group <resource-group>
    ```

    応答から `"extensionType": "amlarc-compute"` と `"installState": "Installed"` を見つけます。 最初の数分間は、`"installState": "Pending"` と表示されることがあります。

    `installState` が **Installed** と表示されたら、kubeconfig ファイルが目的のクラスターを指している状態で、お使いのマシン上で次のコマンドを実行して、*azureml* 名前空間の下のすべてのポッドの状態が *Running* になっていることを確認します。

   ```bash
    kubectl get pods -n azureml
   ```

## <a name="attach-arc-cluster-studio"></a>Arc クラスターの接続 (studio)

Azure Arc 対応 Kubernetes クラスターを接続すると、ワークスペースでトレーニング用に使用できるようになります。

1. [Azure Machine Learning Studio](https://ml.azure.com) に移動します。
1. **[管理]** の下にある、 **[コンピューティング]** を選択します。
1. **[Attached computes]\(接続されたコンピューティング\)** タブを開きます。
1. **[+New]\(新規\) > [Kubernetes (preview)]\(Kubernetes (プレビュー)\)** の順に選択します

   ![Kubernetes クラスターの接続](./media/how-to-attach-arc-kubernetes/attach-kubernetes-cluster.png)

1. コンピューティング名を入力し、ドロップダウンから Azure Arc 対応 Kubernetes クラスターを選択します。

   ![Kubernetes クラスターを構成する](./media/how-to-attach-arc-kubernetes/configure-kubernetes-cluster.png)

1. (省略可能) 高度なシナリオでは、構成ファイルを参照してアップロードします。

   ![構成ファイルをアップロードする](./media/how-to-attach-arc-kubernetes/upload-configuration-file.png)

1. **[接続]** を選択します

    [Attached computes]\(接続されたコンピューティング\) タブで、クラスターの初期状態が *[作成中]* になります。 クラスターが正常に接続されると、状態は *[成功]* に変わります。 それ以外の場合、状態は *[失敗]* に変わります。

    ![リソースをプロビジョニングする](./media/how-to-attach-arc-kubernetes/provision-resources.png)

### <a name="advanced-attach-scenario"></a>高度な接続シナリオ

JSON 構成ファイルを使用して、Azure Arc 対応 Kubernetes クラスターで高度なコンピューティング先の機能を構成します。

構成ファイルの例を次に示します。

```json
{
   "namespace": "amlarc-testing",
   "defaultInstanceType": "gpu_instance",
   "instanceTypes": {
      "gpu_instance": {
         "nodeSelector": {
            "accelerator": "nvidia-tesla-k80"
         },
         "resources": {
            "requests": {
               "cpu": "2",
               "memory": "16Gi",
               "nvidia.com/gpu": "1"
            },
            "limits": {
               "cpu": "2",
               "memory": "16Gi",
               "nvidia.com/gpu": "1"
            }
         }
      },
      "big_cpu_sku": {
         "nodeSelector": {
            "VMSizes": "VM-64vCPU-256GB"
         },
         "resources": {
            "requests": {
               "cpu": "4",
               "memory": "16Gi",
               "nvidia.com/gpu": "0"
            },
            "limits": {
               "cpu": "4",
               "memory": "16Gi",
               "nvidia.com/gpu": "0"
            }
         }
      }
   }
}
```

次のカスタム コンピューティング先プロパティは、構成ファイルを使用して構成できます。

* `namespace` - 既定では、`default` 名前空間になります。 これは、ジョブとポッドが実行される名前空間です。 既定以外の名前空間を設定する場合は、その名前空間が既に存在している必要があることに注意してください。 名前空間を作成するには、クラスター管理者特権が必要です。

* `defaultInstanceType` - トレーニング ジョブが既定で実行されるインスタンスの種類。 `instanceTypes` を指定した場合は `defaultInstanceType` が必須です。 `defaultInstanceType` の値は、`instanceTypes` プロパティで定義されている値のいずれかである必要があります。

    > [!IMPORTANT]
    > 現時点では、コンピューターのターゲット名を使用したジョブの送信のみがサポートされています。 したがって、構成は常に既定で defaultInstanceType に設定されます。

* `instanceTypes` - トレーニング ジョブに使用されるインスタンスの種類の一覧。 インスタンスの種類は、それぞれ `nodeSelector` と `resources requests/limits` プロパティによって定義されます。

  * `nodeSelector` - クラスター内のノードを識別するために使用される 1 つ以上のノード ラベル。 クラスター ノードのラベルを作成するには、クラスター管理者特権が必要です。 このプロパティを指定すると、トレーニング ジョブは指定されたノード ラベルを持つノード上で実行されるようにスケジュールされます。 `nodeSelector` を使用することで、ワークロードの配置をトレーニングするためにノードのサブセットを対象にできます。 これは、クラスターに異なる SKU がある場合や、CPU ノードや GPU ノードなどのさまざまな種類のノードがある場合に役立ちます。 たとえば、すべての GPU ノードのノード ラベルを作成し、GPU ノード プールの `instanceType` を定義できます。 これにより、トレーニング ジョブをスケジュールするときに GPU ノード プールが排他的に対象になります。 

  * `resources requests/limits` - リソース要求を指定し、実行するトレーニング ジョブ ポッドを制限します。 既定値は 1 つの CPU と 4 GB のメモリです。

    >[!IMPORTANT]
    > 既定では、クラスター リソースは 1 つの CPU と 4 GB のメモリで展開されます。 クラスターが下位のリソースで構成されている場合、ジョブの実行は失敗します。 ジョブの完了を確実にするために、トレーニング ジョブのニーズに応じて、常にリソースの要求や制限を指定することをお勧めします。 既定の構成ファイルの例を次に示します。
    >
    > ```json
    > {
    >    "namespace": "default",
    >    "defaultInstanceType": "defaultInstanceType",
    >    "instanceTypes": {
    >       "defaultInstanceType": {
    >          "nodeSelector": null,
    >          "resources": {
    >             "requests": {
    >                "cpu": "1",
    >                "memory": "4Gi",
    >                "nvidia.com/gpu": "0"
    >             },
    >             "limits": {
    >                "cpu": "1",
    >                "memory": "4Gi",
    >                "nvidia.com/gpu": "0"
    >             }
    >          }
    >       }
    >    }
    > }
    > ```

## <a name="attach-arc-cluster-python-sdk"></a>Arc クラスターの接続 (Python SDK)

次の Python コードは、Azure Arc 対応 Kubernetes クラスターを接続し、それをトレーニングのコンピューティング先として使用する方法を示しています。

```python
from azureml.core.compute import KubernetesCompute
from azureml.core.compute import ComputeTarget
import os

ws = Workspace.from_config()

# choose a name for your Azure Arc-enabled Kubernetes compute
amlarc_compute_name = os.environ.get("AML_COMPUTE_CLUSTER_NAME", "amlarc-compute")

# resource ID for your Azure Arc-enabled Kubernetes cluster
resource_id = "/subscriptions/123/resourceGroups/rg/providers/Microsoft.Kubernetes/connectedClusters/amlarc-cluster"

if amlarc_compute_name in ws.compute_targets:
    amlarc_compute = ws.compute_targets[amlarc_compute_name]
    if amlarc_compute and type(amlarc_compute) is KubernetesCompute:
        print("found compute target: " + amlarc_compute_name)
else:
    print("creating new compute target...")

    amlarc_attach_configuration = KubernetesCompute.attach_configuration(resource_id) 
    amlarc_compute = ComputeTarget.attach(ws, amlarc_compute_name, amlarc_attach_configuration)

 
    amlarc_compute.wait_for_completion(show_output=True)
    
     # For a more detailed view of current KubernetesCompute status, use get_status()
    print(amlarc_compute.get_status().serialize())
```

### <a name="advanced-attach-scenario"></a>高度な接続シナリオ

次のコードは、名前空間、nodeSelector、リソースの要求や制限など、高度なコンピューティング先プロパティを構成する方法を示しています。

```python
from azureml.core.compute import KubernetesCompute
from azureml.core.compute import ComputeTarget
import os

ws = Workspace.from_config()

# choose a name for your Azure Arc-enabled Kubernetes compute
amlarc_compute_name = os.environ.get("AML_COMPUTE_CLUSTER_NAME", "amlarc-compute")

# resource ID for your Azure Arc-enabled Kubernetes cluster
resource_id = "/subscriptions/123/resourceGroups/rg/providers/Microsoft.Kubernetes/connectedClusters/amlarc-cluster"

if amlarc_compute_name in ws.compute_targets:
   amlarc_compute = ws.compute_targets[amlarc_compute_name]
   if amlarc_compute and type(amlarc_compute) is KubernetesCompute:
      print("found compute target: " + amlarc_compute_name)
else:
   print("creating new compute target...")
   ns = "amlarc-testing"
    
   instance_types = {
      "gpu_instance": {
         "nodeSelector": {
            "accelerator": "nvidia-tesla-k80"
         },
         "resources": {
            "requests": {
               "cpu": "2",
               "memory": "16Gi",
               "nvidia.com/gpu": "1"
            },
            "limits": {
               "cpu": "2",
               "memory": "16Gi",
               "nvidia.com/gpu": "1"
            }
        }
      },
      "big_cpu_sku": {
         "nodeSelector": {
            "VMSizes": "VM-64vCPU-256GB"
         }
      }
   }

   amlarc_attach_configuration = KubernetesCompute.attach_configuration(resource_id = resource_id, namespace = ns, default_instance_type="gpu_instance", instance_types = instance_types)
 
   amlarc_compute = ComputeTarget.attach(ws, amlarc_compute_name, amlarc_attach_configuration)

 
   amlarc_compute.wait_for_completion(show_output=True)
    
   # For a more detailed view of current KubernetesCompute status, use get_status()
   print(amlarc_compute.get_status().serialize())
```

## <a name="next-steps"></a>次のステップ

- [CLI (v2) を使用したモデルのトレーニング](how-to-train-cli.md)
- [トレーニングの実行を構成して送信する](how-to-set-up-training-targets.md)
- [ハイパーパラメーターを調整する](how-to-tune-hyperparameters.md)
- [Scikit-learn を使用してモデルをトレーニングする](how-to-train-scikit-learn.md)
- [TensorFlow モデルをトレーニングする](how-to-train-tensorflow.md)
- [PyTorch モデルのトレーニング](how-to-train-pytorch.md)
- [Azure Machine Learning パイプラインを使用してトレーニングする](how-to-create-machine-learning-pipelines.md)
- [送信プロキシ サーバーを使用したオンプレミスでのモデルのトレーニング](../azure-arc/kubernetes/quickstart-connect-cluster.md#4a-connect-using-an-outbound-proxy-server)
