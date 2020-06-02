---
title: Azure Kubernetes Service (AKS) でシステム ノード プールを使用する
description: Azure Kubernetes Service (AKS) でシステム ノード プールを作成および管理する方法について学習します
services: container-service
ms.topic: article
ms.date: 04/28/2020
ms.openlocfilehash: 85cc699d6ef8c632663775e91f2b5cad6ca7a7b6
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/12/2020
ms.locfileid: "83125249"
---
# <a name="manage-system-node-pools-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) でシステム ノード プールを管理する

Azure Kubernetes Service (AKS) で同じ構成のノードは、*ノード プール*にグループ化できます。 ノード プールには、お使いのアプリケーションを実行する基になる VM が含まれます。 お使いの AKS クラスターには、システム ノード プールとユーザー ノードプールの 2 つの異なるノード プールのモードがあります。 システム ノード プールは、CoreDNS や tunnelfront などの重要なシステム ポッドをホストするという主要な目的を果たします。 ユーザー ノード プールは、アプリケーション ポッドをホストするという主要な目的を果たします。 ただし、AKS クラスター内のプールを 1 つだけにする場合は、システム ノード プールでアプリケーション ポッドをスケジュールすることができます。 各 AKS クラスターには、少なくとも 1 つのノードを含むシステム ノード プールが、少なくとも 1 つ含まれている必要があります。 

> [!Important]
> 運用環境内のお使いの AKS クラスターで 1 つのシステム ノード プールを実行する場合、そのノード プールには少なくとも 3 つのノードを使用することをお勧めします。

## <a name="before-you-begin"></a>開始する前に

* Azure CLI バージョン 2.3.1 以降がインストールされて構成されている必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール][install-azure-cli]に関するページを参照してください。

## <a name="limitations"></a>制限事項

システム ノード プールをサポートする AKS クラスターを作成および管理する場合には、次の制限があります。

* 「[Azure Kubernetes Service (AKS) のクォータ、仮想マシンのサイズの制限、およびリージョンの可用性][quotas-skus-regions]」を参照してください。
* AKS クラスターは、VM の種類として仮想マシン スケール セットを使用して構築する必要があります。
* ノード プールの名前は、小文字の英数字のみを含めることができ、小文字で始める必要があります。 Linux のノード プールの長さは、1 から 12 文字の範囲内である必要があります。 Windows のノード プールの長さは、1 から 6 文字の範囲内である必要があります。
* ノード プール モードを設定するには、2020-03-01 以上の API バージョンを使用する必要があります。
* ノード プールのモードは必須プロパティであり、ARM テンプレートまたは API 呼び出しを直接行う場合は、明示的に設定する必要があります。

## <a name="system-and-user-node-pools"></a>システムおよびユーザー ノード プール

システム ノード プールのノードには、それぞれ **kubernetes.azure.com/mode: system** というラベルが付いています。 すべての AKS クラスターには、少なくとも 1 つのシステム ノード プールが含まれています。 システム ノード プールには、次の制限があります。

* システム プールの osType は、Linux である必要があります。
* ユーザー ノード プールの osType には、Linux または Windows が可能です。
* システム プールには少なくとも 1 つのノードを含める必要があり、ユーザー ノード プールには 0 以上のノードを含めることができます。
* システム ノード プールには、少なくとも 2 つの vCPU と 4 GB のメモリのがある VM SKU が必要です。
* システム ノード プールでは、[ポッドの最小値と最大値の式][maximum-pods]に関する説明のとおり、少なくともポッドを 30 サポートしている必要があります。
* スポット ノード プールには、ユーザー ノード プールが必要です。

ノード プールには、次の操作を実行できます。

* 別のシステム ノード プールが AKS クラスター内にあり、それが代わりをする場合に、システム ノード プールをユーザー ノード プールに変更する。
* ユーザー ノード プールをシステム ノード プールに変更する。
* ユーザー ノード プールを削除する。
* 別のシステム ノード プールが AKS クラスター内にあり、それが代わりをする場合に、システム ノード プールを削除できる。
* AKS クラスターには、少なくとも 1 つのシステム ノード プールが必要であり、複数のシステム ノード プールが存在する場合がある。
* 既存のノード プールで変更できない各種の設定を変更したい場合は、新しいノード プールを作成して既存のものと置き換えることができる。 たとえば、新しい maxPods 設定で新しいノード プールを追加し、古いノード プールを削除する。

## <a name="create-a-new-aks-cluster-with-a-system-node-pool"></a>システム ノード プールを含む新しい AKS クラスターを作成する

新しい AKS クラスターを作成すると、1 つのノードを含むシステム ノード プールが自動作成されます。 最初のノード プールのモード型は、既定で system になります。 az aks nodepool add を使用して作成した新しいノード プールは、モード パラメーターを明示的に指定しない限り、ユーザーノード プールになります。

次の例では、*myResourceGroup* という名前のリソース グループを*米国東部*リージョンに作成します。

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

AKS クラスターを作成するには、[az aks create][az-aks-create] コマンドを使用します。 次の例では、*myAKSCluster* という名前の 1 つのノードを含む 1 つのシステム プールがあるクラスターを作成します。 ご自分の運用環境のワークロードでは、少なくともノードが 3 つあるシステム ノード プールを使用していることを確認してください。 この操作が完了するまでに数分かかる場合があります。

```azurecli-interactive
az aks create -g myResourceGroup --name myAKSCluster --node-count 1 --generate-ssh-keys
```

## <a name="add-a-system-node-pool-to-an-existing-aks-cluster"></a>既存の AKS クラスターにシステム ノード プールを追加する

既存の AKS クラスターには、システム ノード プールを 1 つ以上追加できます。 次のコマンドでは、モード型が system のノード プールを既定のノード数 3 で追加します。

```azurecli-interactive
az aks nodepool add -g myResourceGroup --cluster-name myAKSCluster -n mynodepool --mode system
```
## <a name="show-details-for-your-node-pool"></a>お使いのノード プールの詳細を表示する

お使いのノード プールの詳細を確認するには、次のコマンドを使用します。  

```azurecli-interactive
az aks nodepool show -g myResourceGroup --cluster-name myAKSCluster -n mynodepool
```

システム ノード プールには **System** 型のモードが定義され、ユーザー ノード プールには **User** 型のモードが定義されています。

```output
{
  "agentPoolType": "VirtualMachineScaleSets",
  "availabilityZones": null,
  "count": 3,
  "enableAutoScaling": null,
  "enableNodePublicIp": false,
  "id": "/subscriptions/666d66d8-1e43-4136-be25-f25bb5de5883/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/agentPools/mynodepool",
  "maxCount": null,
  "maxPods": 110,
  "minCount": null,
  "mode": "System",
  "name": "mynodepool",
  "nodeLabels": {},
  "nodeTaints": null,
  "orchestratorVersion": "1.15.10",
  "osDiskSizeGb": 100,
  "osType": "Linux",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "scaleSetEvictionPolicy": null,
  "scaleSetPriority": null,
  "spotMaxPrice": null,
  "tags": null,
  "type": "Microsoft.ContainerService/managedClusters/agentPools",
  "vmSize": "Standard_DS2_v2",
  "vnetSubnetId": null
}
```

## <a name="update-system-and-user-node-pools"></a>システムおよびユーザー ノード プールを更新する

システムおよびユーザー ノード プールのいずれもモードを変更できます。 システム ノード プールをユーザー プールに変更できるのは、AKS クラスターに別のシステム ノード プールが既に存在する場合のみです。

このコマンドは、システム ノード プールをユーザー ノード プールに変更します。

```azurecli-interactive
az aks nodepool update -g myResourceGroup --cluster-name myAKSCluster -n mynodepool --mode user
```

このコマンドは、ユーザー ノード プールをシステム ノード プールに変更します。

```azurecli-interactive
az aks nodepool update -g myResourceGroup --cluster-name myAKSCluster -n mynodepool --mode system
```

## <a name="delete-a-system-node-pool"></a>システムノードプールを削除する

> [!Note]
> API バージョン 2020-03-02 より前で AKS クラスターでシステム ノード プールを使用する場合、新しいシステム ノード プールを追加してから、元の既定のノード プールを削除してください。

以前は、AKS クラスターの最初の既定ノード プールであったシステム ノード プールは削除できませんでした。 現在は、お使いのクラスターから任意のノード プールを柔軟に削除できるようになっています。 AKS クラスターにはシステム ノード プールが少なくとも 1 つ必要であるため、お使いの AKS クラスターには、その 1 つを削除する前に、少なくとも 2 つのシステム ノード プールが必要です。

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster -n mynodepool
```

## <a name="next-steps"></a>次のステップ

この記事では、AKS クラスターでシステム ノード プールを作成および管理する方法を学習しました。 複数のノード プールの使用方法の詳細については、[複数のノード プールの使用][use-multiple-node-pools]に関するページを参照してください。

<!-- EXTERNAL LINKS -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-taint]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#taint
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubernetes-labels]: https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/
[kubernetes-label-syntax]: https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/#syntax-and-character-set

<!-- INTERNAL LINKS -->
[aks-windows]: windows-container-cli.md
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-nodepool-add]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-add
[az-aks-nodepool-list]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-list
[az-aks-nodepool-update]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-update
[az-aks-nodepool-upgrade]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-upgrade
[az-aks-nodepool-scale]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-scale
[az-aks-nodepool-delete]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-delete
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[gpu-cluster]: gpu-cluster.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[quotas-skus-regions]: quotas-skus-regions.md
[supported-versions]: supported-kubernetes-versions.md
[tag-limitation]: ../azure-resource-manager/resource-group-using-tags.md
[taints-tolerations]: operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations
[vm-sizes]: ../virtual-machines/linux/sizes.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[maximum-pods]: configure-azure-cni.md#maximum-pods-per-node