---
title: Azure Kubernetes Service (AKS) クラスターのスケールダウン モードを使用する (プレビュー)
titleSuffix: Azure Kubernetes Service
description: Azure Kubernetes Service (AKS) でスケールダウン モードを使用する方法について説明します。
services: container-service
ms.topic: article
ms.date: 09/01/2021
ms.author: qpetraroia
author: qpetraroia
ms.openlocfilehash: 6bd1f4c70d9c427d7f6487040453e7ecae199717
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124800120"
---
# <a name="use-scale-down-mode-to-deletedeallocate-nodes-in-azure-kubernetes-service-aks-preview"></a>スケールダウン モードを使用して、Azure Kubernetes Service (AKS) のノードを削除したり割り当て解除を行う (プレビュー)

既定では、手動またはクラスター オートスケーラーによって実行されるスケールアップ操作では、新しいノードの割り当てとプロビジョニングを行う必要があり、スケールダウン操作ではノードが削除されます。 スケールダウン モードを使用すると、スケールダウン時に Azure Kubernetes Service (AKS) クラスター内のノードを削除するか、割り当てを解除するかを決めることができます。 

Azure VM が `Stopped` (割り当て解除) 状態の間は、VM のコンピューティング リソースに対する課金は行われません。 ただし、VM に接続されている OS やデータ ストレージ ディスクに対する課金については、変わらず行われます。 これは、コンテナー イメージがそれらのノードで保持されることも意味します。 詳細については、「[Azure Virtual Machines の状態と課金][state-billing-azure-vm]」を参照してください。 この動作により、デプロイにはキャッシュされたイメージが利用されるので、操作速度が速くなります。 スケールダウン モードを使用すると、ノードを事前にプロビジョニングしたり、コンテナー イメージを事前にプルする必要がなくなるため、コンピューティング コストを削減できます。

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="before-you-begin"></a>開始する前に

> [!WARNING]
> 割り当て解除された VM を保持するには、スケールダウン モードを Deallocate に設定する必要があります。 これには、IaaS API (Virtual Machine Scale Set API) を使用して割り当てが解除された VM が含まれます。 スケールダウン モードを Delete に設定すると、割り当て解除された VM が削除されます。

この記事は、AKS クラスターがすでに存在していることを前提としています。 AKS クラスターが必要な場合は、[Azure CLI を使用した場合][aks-quickstart-cli]または [Azure portal を使用した場合][aks-quickstart-portal]の AKS のクイックスタートを参照してください。

### <a name="limitations"></a>制限事項

- [エフェメラル OS][ephemeral-os] ディスクはサポートされていません。 クラスターまたはノード プールを作成する場合は、`--node-osdisk-type Managed` を使用してマネージド OS ディスクを指定するようにしてください。
- [スポット ノード プール][spot-node-pool]はサポートされていません。

### <a name="install-aks-preview-cli-extension"></a>aks-preview CLI 拡張機能をインストールする

*aks-preview* Azure CLI 拡張機能バージョン 0.5.30 以降も必要です。 *aks-preview* Azure CLI 拡張機能は、[az extension add][az-extension-add] コマンドを使用してインストールします。 または、[az extension update][az-extension-update] コマンドを使用すると、使用可能な更新プログラムをインストールできます。

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-the-aks-scaledownmodepreview-preview-feature"></a>`AKS-ScaleDownModePreview` プレビュー機能を登録する

この機能を使用するには、サブスクリプションで `AKS-ScaleDownModePreview` 機能フラグも有効にする必要があります。

`AKS-ScaleDownModePreview` 機能フラグは、次の例のとおり、[az feature register][az-feature-register] コマンドを使用して登録します。

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "AKS-ScaleDownModePreview"
```

状態が *[登録済み]* と表示されるまでに数分かかります。 登録の状態は、[az feature list][az-feature-list] コマンドで確認できます。

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-ScaleDownModePreview')].{Name:name,State:properties.state}"
```

準備ができたら、[az provider register][az-provider-register] コマンドを使用して、*Microsoft.ContainerService* リソース プロバイダーの登録を更新します。

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="using-scale-down-mode-to-deallocate-nodes-on-scale-down"></a>スケールダウン モードを使用してスケールダウン時にノードを割り当て解除する

`--scale-down-mode Deallocate` を設定すると、クラスターやノード プールのスケールダウン中にノードの割り当てが解除されます。 すべての割り当て解除されたノードが停止されます。 クラスターやノード プールをスケールアップする必要がある場合は、新しいノードがプロビジョニングされる前に、割り当て解除されたノードが最初に起動されます。

この例では、20 ノードの新しいノード プールを作成し、スケールダウン時には `--scale-down-mode Deallocate` でノードの割り当てを解除するように指定します。

```azurecli-interactive
az aks nodepool add --node-count 20 --scale-down-mode Deallocate --node-osdisk-type Managed --max-pods 10 --name nodepool2 --cluster-name myAKSCluster --resource-group myResourceGroup
```

ノード プールをスケーリングし、ノード数を 5 に変更することで、15 個のノードの割り当てが解除されます。

```azurecli-interactive
az aks nodepool scale --node-count 5 --name nodepool2 --cluster-name myAKSCluster --resource-group myResourceGroup
```

### <a name="deleting-previously-deallocated-nodes"></a>以前に割り当て解除されたノードを削除する

割り当て解除したノードを削除するには、`--scale-down-mode Delete` を設定してスケールダウン モードを `Delete` に変更します。 これで、15 個のノードが削除されます。

```azurecli-interactive
az aks nodepool update --scale-down-mode Delete --name nodepool2 --cluster-name myAKSCluster --resource-group myResourceGroup
```

> [!NOTE]
> スケールダウン モードを `Deallocate` から `Delete` に変更してから `Deallocate` に戻ると、ノード プールは `Deallocate` スケールダウン モードに維持されたまま、割り当てが解除されたすべてのノードが削除されます。

## <a name="using-scale-down-mode-to-delete-nodes-on-scale-down"></a>スケールダウン モードを使用してスケールダウン時にノードを削除する

スケールダウン モードを使用しない AKS の既定の動作では、クラスターをスケールダウンするときにノードが削除されます。 スケールダウン モードを使用すると、`--scale-down-mode Delete` を設定することでこれを明示的に実現することができます。

この例では、新しいノード プールを作成し、スケールダウン時には `--scale-down-mode Delete` でノードを削除するように指定します。 スケーリング操作は、クラスター オートスケーラーを介して処理されます。

```azurecli-interactive
az aks nodepool add --enable-cluster-autoscaler --min-count 1 --max-count 10 --max-pods 10 --node-osdisk-type Managed --scale-down-mode Delete --name nodepool3 --cluster-name myAKSCluster --resource-group myResourceGroup
```

## <a name="next-steps"></a>次のステップ

- AKS クラスターのアップグレードに関する詳細については、「[AKS クラスターのアップグレード][aks-upgrade]」を参照してください
- クラスター オートスケーラーの詳細については、[AKS でのアプリケーションの需要に合わせてクラスターを自動的にスケーリングする][cluster-autoscaler]方法に関するページを参照してください

<!-- LINKS - Internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-aks-install-cli]: /cli/azure/aks#az_aks_install_cli
[az-provider-register]: /cli/azure/provider#az_provider_register
[aks-upgrade]: upgrade-cluster.md
[cluster-autoscaler]: cluster-autoscaler.md
[ephemeral-os]: cluster-configuration.md#ephemeral-os
[state-billing-azure-vm]: ../virtual-machines/states-billing.md
[spot-node-pool]: spot-node-pool.md