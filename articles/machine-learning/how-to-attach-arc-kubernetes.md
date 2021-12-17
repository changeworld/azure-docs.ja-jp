---
title: Azure Arc 対応機械学習 (プレビュー)
description: Azure Machine Learning で機械学習モデルのトレーニングと推論を行う Azure Kubernetes Service と Azure Arc 対応 Kubernetes クラスターを構成する
titleSuffix: Azure Machine Learning
author: luisquintanilla
ms.author: luquinta
ms.service: machine-learning
ms.subservice: core
ms.date: 11/17/2021
ms.topic: how-to
ms.custom: ignite-fall-2021
ms.openlocfilehash: 5ae6b6c636e1713d9f423b00301759ffb469b5f0
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132723432"
---
# <a name="configure-kubernetes-clusters-for-machine-learning-preview"></a>機械学習のための Kubernetes クラスターを構成する (プレビュー)

機械学習ワークロードのトレーニングと推論に使用する Azure Kubernetes Service (AKS) と Azure Arc 対応 Kubernetes クラスターを構成する方法について説明します。

## <a name="what-is-azure-arc-enabled-machine-learning"></a>Azure Arc 対応機械学習とは

Azure Arc を使用することで、ユーザーはオンプレミス、マルチクラウド、エッジを問わず、あらゆる Kubernetes 環境で Azure サービスを実行することができます。

Azure Arc 対応機械学習を使用すると、Azure Kubernetes Service または Azure Arc 対応 Kubernetes クラスターを構成して、Azure Machine Learning で機械学習モデルをトレーニング、推論、管理することができます。

## <a name="machine-learning-on-azure-kubernetes-service"></a>Azure Kubernetes Service での Machine Learning

Azure Machine Learning のトレーニングと推論ワークロードに Azure Kubernetes Service クラスターを使用する場合、それらを Azure Arc に接続する必要はありません。

インバウンドとアウトバウンドのネットワーク トラフィックを構成する必要があります。 詳細については、[ネットワークのインバウンド トラフィックおよびアウトバウンド トラフィックの構成 (AKS)](how-to-access-azureml-behind-firewall.md#azure-kubernetes-services-1) に関する記事を参照してください。

Azure Machine Learning の拡張機能を Azure Kubernetes Service クラスターにデプロイする方法については、「[Azure Machine Learning 拡張機能の展開](#deploy-azure-machine-learning-extension)」セクションを参照してください。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free)してください。
* Azure Arc 対応 Kubernetes クラスター。 詳細については、[既存の Kubernetes クラスターを Azure Arc に接続する方法に関するクイックスタート ガイド](../azure-arc/kubernetes/quickstart-connect-cluster.md)を参照してください。

    > [!NOTE]
    > AKS クラスターについて、それらを Azure Arc に接続するかどうかは **任意** です。

* [Azure Arc のネットワーク要件](../azure-arc/kubernetes/quickstart-connect-cluster.md?tabs=azure-cli#meet-network-requirements)を満たすこと

    > [!IMPORTANT]
    > 送信プロキシ サーバーまたはファイアウォールの内側で実行するクラスターには、追加のネットワーク構成が必要です。
    >
    > 詳細については、[ネットワークのインバウンド トラフィックおよびアウトバウンド トラフィックの構成 (Azure Arc 対応 Kubernetes)](how-to-access-azureml-behind-firewall.md#arc-kubernetes)に関する記事を参照してください。

* [Azure Arc 対応 Kubernetes クラスターの拡張機能の前提条件](../azure-arc/kubernetes/extensions.md#prerequisites)を満たしていること。
  * Azure CLI バージョン >= 2.24.0
  * Azure CLI k8s-extension 拡張機能のバージョン >= 1.0.0

* Azure Machine Learning ワークスペース。 まだお持ちでない場合は、開始する前に[ワークスペースを作成](how-to-manage-workspace.md?tabs=python)してください。
  * Azure Machine Learning Python SDK のバージョン >= 1.30
* Azure CLI を使用して Azure にログインする

    ```azurecli
    az login
    az account set --subscription <your-subscription-id>
    ```  

* **Azure RedHat OpenShift Service (ARO) および OpenShift Container Platform (OCP) のみ**

    * ARO または OCP Kubernetes クラスターが稼働している。 詳細については、[ARO Kubernetes クラスターの作成](../openshift/tutorial-create-cluster.md)に関する記事と [OCP Kubernetes クラスターの作成](https://docs.openshift.com/container-platform/4.6/installing/installing_platform_agnostic/installing-platform-agnostic.html)に関するページを参照してください
    * AzureML サービス アカウントへの特権アクセスを許可する。

        `oc edit scc privileged` を実行し、次を追加します 

        * ```system:serviceaccount:azure-arc:azure-arc-kube-aad-proxy-sa```
        * ```system:serviceaccount:azureml:{EXTENSION NAME}-kube-state-metrics``` **(注:** ```{EXTENSION NAME}``` **は、** ```az k8s-extension create --name``` **のステップで使用した拡張機能名と一致している必要があります)**
        * ```system:serviceaccount:azureml:cluster-status-reporter```
        * ```system:serviceaccount:azureml:prom-admission```
        * ```system:serviceaccount:azureml:default```
        * ```system:serviceaccount:azureml:prom-operator```
        * ```system:serviceaccount:azureml:csi-blob-node-sa```
        * ```system:serviceaccount:azureml:csi-blob-controller-sa```
        * ```system:serviceaccount:azureml:load-amlarc-selinux-policy-sa```
        * ```system:serviceaccount:azureml:azureml-fe```
        * ```system:serviceaccount:azureml:prom-prometheus```

## <a name="deploy-azure-machine-learning-extension"></a>Azure Machine Learning 拡張機能の展開

Azure Arc 対応 Kubernetes には、Azure Policy 定義、監視、機械学習など、さまざまなエージェントをインストールできるクラスター拡張機能があります。 Azure Machine Learning では、Kubernetes クラスターに Azure Machine Learning エージェントを展開するために、*Microsoft.AzureML.Kubernetes* クラスター拡張機能を使用する必要があります。 Azure Machine Learning 拡張機能をインストールしたら、クラスターを Azure Machine Learning ワークスペースに接続し、以下のシナリオ用に使用できます。

* [トレーニング](#training)
* [リアルタイム推論のみ](#inferencing)
* [トレーニングと推論](#training-inferencing)

> [!TIP]
> クラスターのみのトレーニングでは、Azure Machine Learning パイプラインの一環としてバッチ推論もサポートされます。

`k8s-extension` Azure CLI 拡張機能の [`create`](/cli/azure/k8s-extension?view=azure-cli-latest&preserve-view=true) コマンドを使用して、Azure Arc 対応 Kubernetes クラスターに Azure Machine Learning 拡張機能を展開します。

> [!IMPORTANT]
> Azure Machine Learning 拡張機能を AKS クラスターに展開するには、`--cluster-type` パラメーターを `managedClusters` に設定します。

Azure Machine Learning 拡張機能のさまざまな展開シナリオに使用できる構成設定の一覧を次に示します。

```--config``` または ```--config-protected``` を使用して、Azure Machine Learning 展開構成のキーと値のペアのリストを指定できます。

> [!TIP]
> Azure Machine Learning 拡張機能を ARO および OCP Kubernetes クラスターに展開するには、`openshift` パラメーターを `True` に設定します。

| 構成設定のキー名  | 説明  | トレーニング | 推論 | トレーニングと推論 |
|---|---|---|---|---|
| ```enableTraining``` | 既定値は `False` です。 機械学習モデルをトレーニングするための拡張機能インスタンスを作成するには、`True` に設定します。 |  **&check;** | 該当なし |  **&check;** |
|```logAnalyticsWS```  | 既定値は `False` です。 Azure Machine Learning 拡張機能は、Azure LogAnalytics ワークスペースと統合されています。 LogAnalytics ワークスペースを使用してログの表示と分析機能を利用できるようにするには、`True` に設定します。 LogAnalytics ワークスペースのコストが適用される場合があります。 | 省略可能 | オプション | 省略可能 |
|```installNvidiaDevicePlugin```  | 既定値は `True` です。 Nvidia GPU ハードウェアでトレーニングと推論を行うには、Nvidia デバイス プラグインが必要です。 Azure Machine Learning 拡張機能を使用すると、Kubernetes クラスターに GPU ハードウェアがあるかどうかに関係なく、Azure Machine Learning インスタンスの作成時に Nvidia デバイス プラグインが既定でインストールされます。 GPU を使用する予定がない場合、または Nvidia デバイス プラグインが既にインストールされている場合は、`False` に設定します。  | 省略可能 |オプション | 省略可能 |
| ```enableInference``` | 既定値は `False` です。  機械学習モデルを推論するための拡張機能インスタンスを作成するには、`True` に設定します。 | 該当なし | **&check;** |  **&check;** |
| ```allowInsecureConnections``` | 既定値は `False` です。 推論の HTTP エンドポイント サポートを含む Azure Machine Learning 拡張機能の展開で、```sslCertPemFile``` と ```sslKeyPemFile``` が指定されていない場合は、`True` に設定します。 | 該当なし | 省略可能 |  省略可能 |
| ```sslCertPemFile``` & ```ssKeyPMFile``` | SSL 証明書とキー ファイルへのパス (PEM でエンコード)。 推論の HTTPS エンドポイント サポートを含む AzureML 拡張機能の展開に必要です。 | 該当なし | 省略可能 |  省略可能 |
| ```privateEndpointNodeport``` | 既定値は `False` です。  NodePort を使った機械学習推論のプライベート エンドポイント サポートを含む Azure Machine Learning 拡張機能の展開では、`True` に設定します。 | 該当なし | 省略可能 |  省略可能 |
| ```privateEndpointILB``` | 既定値は `False` です。  serviceType 内部ロード バランサーを使った機械学習推論のプライベート エンドポイント サポートを含む Azure Machine Learning 拡張機能の展開では、`True` に設定します | 該当なし| 省略可能 |  省略可能 |
| ```inferenceLoadBalancerHA``` | 既定値は `True` です。 既定では、Azure Machine Learning 拡張機能により、高可用性を実現するために複数のイングレス コントローラー レプリカが展開されます。 クラスター リソースが限られている場合、または開発およびテスト用にのみ Azure Machine Learning 拡張機能を展開したい場合は、`False` に設定します。 高可用性のロードバランサーを使用しない場合、1 つのイングレス コントローラー レプリカのみが展開されます。 | 該当なし | 省略可能 |  省略可能 |
|```openshift``` | 既定値は `False` です。 ARO または OCP クラスターでの Azure Machine Learning 拡張機能の展開では、`True` に設定します。 展開プロセスでは、Azure Machine Learning services の操作が正常に機能するように、自動的にポリシー パッケージがコンパイルされ、各ノードにポリシー パッケージが読み込まれます。 | 省略可能 | オプション |  省略可能 |

> [!WARNING]
> クラスターに Nvidia デバイス プラグインが既にインストールされている場合、これらを再インストールすると拡張機能のインストール エラーが発生する可能性があります。 展開エラーを防ぐには、`installNvidiaDevicePlugin` を `False` に設定します。

### <a name="deploy-extension-for-training-workloads"></a>トレーニング ワークロード用の拡張機能を展開する <a id="training"></a>

次の Azure CLI コマンドを使用して、Azure Machine Learning 拡張機能を展開し、Kubernetes クラスターでトレーニング ワークロードを有効にします。

```azurecli
az k8s-extension create --name arcml-extension --extension-type Microsoft.AzureML.Kubernetes --config enableTraining=True --cluster-type connectedClusters --cluster-name <your-connected-cluster-name> --resource-group <resource-group> --scope cluster
```

### <a name="deploy-extension-for-real-time-inferencing-workloads"></a>リアルタイム推論ワークロード用の拡張機能を展開する <a id="inferencing"></a>

ネットワーク設定、Kubernetes 分散の種類、および Kubernetes クラスターがホストされている場所 (オンプレミスまたはクラウド) に応じて、次のいずれかのオプションを選んで Azure Machine Learning 拡張機能を展開し、Kubernetes クラスターで推論ワークロードを有効にします。

#### <a name="public-endpoints-support-with-public-load-balancer"></a>パブリック ロード バランサーを使用したパブリック エンドポイントのサポート

* **HTTPS**

    ```azurecli
    az k8s-extension create --name arcml-extension --extension-type Microsoft.AzureML.Kubernetes --cluster-type connectedClusters --cluster-name <your-connected-cluster-name> --config enableInference=True --config-protected sslCertPemFile=<path-to-the-SSL-cert-PEM-ile> sslKeyPemFile=<path-to-the-SSL-key-PEM-file> --resource-group <resource-group> --scope cluster
    ```

* **HTTP**

    > [!WARNING]
    > パブリック ロード バランサーを使用したパブリック HTTP エンドポイントのサポートは、リアルタイム推論シナリオ用に Azure Machine Learning 拡張機能を展開するための最も安全性の低い方法であるため、推奨 **されません**。

    ```azurecli
    az k8s-extension create --name arcml-extension --extension-type Microsoft.AzureML.Kubernetes --cluster-type connectedClusters --cluster-name <your-connected-cluster-name>  --configuration-settings enableInference=True allowInsecureConnections=True --resource-group <resource-group> --scope cluster
    ```

#### <a name="private-endpoints-support-with-internal-load-balancer"></a>内部ロード バランサーを使用したプライベート エンドポイントのサポート

* **HTTPS**

    ```azurecli
    az k8s-extension create --name amlarc-compute --extension-type Microsoft.AzureML.Kubernetes --cluster-type connectedClusters --cluster-name <your-connected-cluster-name> --config enableInference=True privateEndpointILB=True --config-protected sslCertPemFile=<path-to-the-SSL-cert-PEM-ile> sslKeyPemFile=<path-to-the-SSL-key-PEM-file> --resource-group <resource-group> --scope cluster
    ```

* **HTTP**

   ```azurecli
   az k8s-extension create --name arcml-extension --extension-type Microsoft.AzureML.Kubernetes --cluster-type connectedClusters --cluster-name <your-connected-cluster-name> --config enableInference=True privateEndpointILB=True allowInsecureConnections=True --resource-group <resource-group> --scope cluster
   ```

#### <a name="endpoints-support-with-nodeport"></a>NodePort を使用したエンドポイントのサポート

NodePort を使用すると、自由に独自の負荷分散ソリューションを設定したり、Kubernetes で完全にサポートされない環境を構成したり、1 つ以上のノードの IP を直接公開したりすることもできます。

NodePort サービスを使用して展開すると、スコアリング URL (または Swagger URL) はいずれかの Node IP (例: ```http://<NodeIP><NodePort>/<scoring_path>```) に置き換えられ、Node を使用できない場合でも変更されません。 ただし、任意の他の Node IP に置き換えられます。

* **HTTPS**

    ```azurecli
    az k8s-extension create --name arcml-extension --extension-type Microsoft.AzureML.Kubernetes --cluster-type connectedClusters --cluster-name <your-connected-cluster-name> --resource-group <resource-group> --scope cluster --config enableInference=True privateEndpointNodeport=True --config-protected sslCertPemFile=<path-to-the-SSL-cert-PEM-ile> sslKeyPemFile=<path-to-the-SSL-key-PEM-file>
    ```

* **HTTP**

   ```azurecli
   az k8s-extension create --name arcml-extension --extension-type Microsoft.AzureML.Kubernetes --cluster-type connectedClusters --cluster-name <your-connected-cluster-name> --config enableInference=True privateEndpointNodeport=True allowInsecureConnections=Ture --resource-group <resource-group> --scope cluster
   ```

### <a name="deploy-extension-for-training-and-inferencing-workloads"></a>トレーニング ワークロードと推論ワークロード用の拡張機能を展開する <a id="training-inferencing"></a>

次の Azure CLI コマンドを使用して Azure Machine Learning 拡張機能を展開し、Kubernetes クラスターで、クラスターのリアルタイム推論、バッチ推論、およびトレーニング ワークロードを有効にします。

```azurecli
az k8s-extension create --name arcml-extension --extension-type Microsoft.AzureML.Kubernetes --cluster-type connectedClusters --cluster-name <your-connected-cluster-name> --config enableTraining=True enableInference=True --config-protected sslCertPemFile=<path-to-the-SSL-cert-PEM-ile> sslKeyPemFile=<path-to-the-SSL-key-PEM-file>--resource-group <resource-group> --scope cluster
```

## <a name="resources-created-during-deployment"></a>展開中に作成されるリソース

Azure Machine Learning 拡張機能が展開されると、クラスターで実行するワークロードに応じて、Azure と Kubernetes クラスターに次のリソースが作成されます。

|リソース名  |リソースの種類 |トレーニング |推論 |トレーニングと推論|
|---|---|---|---|---|
|Azure ServiceBus|Azure リソース|**&check;**|**&check;**|**&check;**|
|Azure Relay|Azure リソース|**&check;**|**&check;**|**&check;**|
|{EXTENSION-NAME}|Azure リソース|**&check;**|**&check;**|**&check;**|
|aml-operator|Kubernetes deployment|**&check;**|該当なし|**&check;**|
|{EXTENSION-NAME}-kube-state-metrics|Kubernetes deployment|**&check;**|**&check;**|**&check;**|
|{EXTENSION-NAME}-prometheus-operator|Kubernetes deployment|**&check;**|**&check;**|**&check;**|
|amlarc-identity-controller|Kubernetes deployment|該当なし|**&check;**|**&check;**|
|amlarc-identity-proxy|Kubernetes deployment|該当なし|**&check;**|**&check;**|
|azureml-fe|Kubernetes deployment|該当なし|**&check;**|**&check;**|
|inference-operator-controller-manager|Kubernetes deployment|該当なし|**&check;**|**&check;**|
|metrics-controller-manager|Kubernetes deployment|**&check;**|**&check;**|**&check;**|
|relayserver|Kubernetes deployment|**&check;**|**&check;**|**&check;**|
|cluster-status-reporter|Kubernetes deployment|**&check;**|**&check;**|**&check;**|
|nfd-master|Kubernetes deployment|**&check;**|該当なし|**&check;**|
|gateway|Kubernetes deployment|**&check;**|**&check;**|**&check;**|
|csi-blob-controller|Kubernetes deployment|**&check;**|該当なし|**&check;**|
|csi-blob-node|Kubernetes daemonset|**&check;**|該当なし|**&check;**|
|fluent-bit|Kubernetes daemonset|**&check;**|**&check;**|**&check;**|
|k8s-host-device-plugin-daemonset|Kubernetes daemonset|**&check;**|**&check;**|**&check;**|
|nfd-worker|Kubernetes daemonset|**&check;**|該当なし|**&check;**|
|prometheus-prom-prometheus|Kubernetes statefulset|**&check;**|**&check;**|**&check;**|
|frameworkcontroller|Kubernetes statefulset|**&check;**|該当なし|**&check;**|

> [!IMPORTANT]
> Azure ServiceBus と Azure Relay のリソースは、Arc クラスター リソースと同じリソース グループの下にあります。 これらのリソースは Kubernetes クラスターと通信するために使われ、変更すると接続されたコンピューティング先が破損します。

> [!NOTE]
> **{EXTENSION-NAME}** は、```az k8s-extension create --name``` Azure CLI コマンドによって指定される拡張機能名です。

## <a name="verify-your-azureml-extension-deployment"></a>AzureML 拡張機能の展開を確認する

```azurecli
az k8s-extension show --name arcml-extension --cluster-type connectedClusters --cluster-name <your-connected-cluster-name> --resource-group <resource-group>
```

応答から `"extensionType": "arcml-extension"` と `"installState": "Installed"` を見つけます。 最初の数分間は、`"installState": "Pending"` と表示されることがあります。

`installState` が **Installed** と表示されたら、kubeconfig ファイルが目的のクラスターを指している状態で、お使いのマシン上で次のコマンドを実行して、*azureml* 名前空間の下のすべてのポッドの状態が *Running* になっていることを確認します。

```bash
kubectl get pods -n azureml
```

## <a name="attach-arc-cluster"></a>Arc クラスターの接続

### <a name="studio"></a><bpt id="p1">[</bpt>スタジオ<ept id="p1">](#tab/azure-studio)</ept>

Azure Arc 対応 Kubernetes クラスターを接続すると、ワークスペースでトレーニング用に使用できるようになります。

1. [Azure Machine Learning Studio](https://ml.azure.com) に移動します。
1. **[管理]** の下にある、 **[コンピューティング]** を選択します。
1. **[Attached computes]\(接続されたコンピューティング\)** タブを開きます。
1. **[+New]\(新規\) > [Kubernetes (preview)]\(Kubernetes (プレビュー)\)** の順に選択します

   ![Kubernetes クラスターの接続](./media/how-to-attach-arc-kubernetes/attach-kubernetes-cluster.png)

1. コンピューティング名を入力し、ドロップダウンから Azure Arc 対応 Kubernetes クラスターを選択します。

    * **(オプション)** Kubernetes の名前空間を入力します。デフォルトは `default` です。 すべての機械学習ワークロードは、クラスター内の指定された kubernetes 名前空間に送信されます。

    * **(省略可能)** システム割り当てまたはユーザー割り当てのマネージド ID を割り当てます。 マネージド ID により、開発者は資格情報を管理する必要がなくなります。 詳細については、[マネージド ID の概要](../active-directory/managed-identities-azure-resources/overview.md)を参照してください。

    ![Kubernetes クラスターを構成する](./media/how-to-attach-arc-kubernetes/configure-kubernetes-cluster-2.png)

1. **[接続]** を選択します

    [Attached computes]\(接続されたコンピューティング\) タブで、クラスターの初期状態が *[作成中]* になります。 クラスターが正常に接続されると、状態は *[成功]* に変わります。 それ以外の場合、状態は *[失敗]* に変わります。

    ![リソースをプロビジョニングする](./media/how-to-attach-arc-kubernetes/provision-resources.png)

### <a name="python-sdk"></a>[Python SDK](#tab/sdk)

Azure Machine Learning Python SDK を使い、[`attach_configuration`](/python/api/azureml-core/azureml.core.compute.kubernetescompute.kubernetescompute?view=azure-ml-py&preserve-view=true) メソッドを使って Azure Arc 対応 Kubernetes クラスターをコンピューティング先として接続できます。

次の Python コードは、Azure Arc 対応 Kubernetes クラスターを接続し、それをコンピューティング先として、マネージド ID を有効にして使用する方法を示しています。

マネージド ID により、開発者は資格情報を管理する必要がなくなります。 詳細については、[マネージド ID の概要](../active-directory/managed-identities-azure-resources/overview.md)を参照してください。

```python
from azureml.core.compute import KubernetesCompute
from azureml.core.compute import ComputeTarget
from azureml.core.workspace import Workspace
import os

ws = Workspace.from_config()

# Specify a name for your Kubernetes compute
amlarc_compute_name = "<COMPUTE_CLUSTER_NAME>"

# resource ID for the Kubernetes cluster and user-managed identity
resource_id = "/subscriptions/<sub ID>/resourceGroups/<RG>/providers/Microsoft.Kubernetes/connectedClusters/<cluster name>"

user_assigned_identity_resouce_id = ['subscriptions/<sub ID>/resourceGroups/<RG>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<identity name>']

ns = "default" 

if amlarc_compute_name in ws.compute_targets:
    amlarc_compute = ws.compute_targets[amlarc_compute_name]
    if amlarc_compute and type(amlarc_compute) is KubernetesCompute:
        print("found compute target: " + amlarc_compute_name)
else:
   print("creating new compute target...")


# assign user-assigned managed identity
amlarc_attach_configuration = KubernetesCompute.attach_configuration(resource_id = resource_id, namespace = ns,  identity_type ='UserAssigned',identity_ids = user_assigned_identity_resouce_id) 

# assign system-assigned managed identity
# amlarc_attach_configuration = KubernetesCompute.attach_configuration(resource_id = resource_id, namespace = ns,  identity_type ='SystemAssigned') 

amlarc_compute = ComputeTarget.attach(ws, amlarc_compute_name, amlarc_attach_configuration)
amlarc_compute.wait_for_completion(show_output=True)

# get detailed compute description containing managed identity principle ID, used for permission access. 
print(amlarc_compute.get_status().serialize())
```

`identity_type` パラメーターを使って、`SystemAssigned` または `UserAssigned` マネージド ID を有効にします。

### <a name="cli"></a>[CLI](#tab/cli)

Azure Machine Learning 2.0 CLI (プレビュー) を使って、AKS または Azure Arc 対応 Kubernetes クラスターを接続できます。

Azure Machine Learning CLI [`attach`](/cli/azure/ml/compute?view=azure-cli-latest&preserve-view=true) コマンドを使い、`--type` 引数を `kubernetes` に設定して、Azure Machine Learning 2.0 CLI を使って Kubernetes クラスターを接続します。

> [!NOTE]
> AKS または Azure Arc 対応 Kubernetes クラスターのコンピューティング接続サポートには、Azure CLI `ml` 拡張機能のバージョン 2.0.1a4 以上が必要です。 詳細については、「[CLI (v2) のインストールと設定](how-to-configure-cli.md)」を参照してください。

次のコマンドは、Azure Arc 対応 Kubernetes クラスターを接続し、それをコンピューティング先として、マネージド ID を有効にして使用する方法を示しています。

**AKS**

```azurecli
az ml compute attach --resource-group <resource-group-name> --workspace-name <workspace-name> --name amlarc-compute --resource-id "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Kubernetes/managedclusters/<cluster-name>" --type kubernetes --identity-type UserAssigned --user-assigned-identities "subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<identity-name>" --no-wait
```

**Azure Arc 対応 Kubernetes**

```azurecli
az ml compute attach --resource-group <resource-group-name> --workspace-name <workspace-name> --name amlarc-compute --resource-id "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Kubernetes/connectedClusters/<cluster-name>" --type kubernetes --user-assigned-identities "subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<identity-name>" --no-wait
```

`identity_type` 引数を使って、`SystemAssigned` または `UserAssigned` マネージド ID を有効にします。

> [!IMPORTANT]
> `--user-assigned-identities` は `UserAssigned` マネージド ID にのみ必要です。 コンマ区切りのユーザー マネージド ID のリストを指定することもできますが、クラスターを接続するときに使われるのは最初の ID のみです。

---

## <a name="next-steps"></a>次のステップ

- [トレーニング ワークロードと推論ワークロード用に異なるインスタンスの種類を作成および選択する](how-to-kubernetes-instance-type.md)
- [CLI (v2) を使用したモデルのトレーニング](how-to-train-cli.md)
- [トレーニングの実行を構成して送信する](how-to-set-up-training-targets.md)
- [ハイパーパラメーターを調整する](how-to-tune-hyperparameters.md)
- [Scikit-learn を使用してモデルをトレーニングする](how-to-train-scikit-learn.md)
- [TensorFlow モデルをトレーニングする](how-to-train-tensorflow.md)
- [PyTorch モデルのトレーニング](how-to-train-pytorch.md)
- [Azure Machine Learning パイプラインを使用してトレーニングする](how-to-create-machine-learning-pipelines.md)
- [送信プロキシ サーバーを使用したオンプレミスでのモデルのトレーニング](../azure-arc/kubernetes/quickstart-connect-cluster.md#4a-connect-using-an-outbound-proxy-server)