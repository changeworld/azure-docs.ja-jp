---
title: Azure Kubernetes Service (AKS)でのノード プールの起動と停止 (プレビュー)
description: AKS (Azure Kubernetes Service) でノード プールを起動または停止する方法について学習します。
services: container-service
ms.topic: article
ms.date: 10/25/2021
author: qpetraroia
ms.author: qpetraroia
ms.openlocfilehash: 4bf2eeda7b13d520bca769e9698e4ea5abebd584
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131092156"
---
# <a name="start-and-stop-an-azure-kubernetes-service-aks-node-pool-preview"></a>Azure Kubernetes Service (AKS) でのノード プールの開始と停止 (プレビュー)

AKS ワークロードは、特定のワークロードを実行するノード プールを含む開発クラスターなど、継続的に実行する必要はない場合があります。 コストを最適化するために、AKS クラスター内のノード プールを完全にオフにし (停止し)、コンピューティング コストを節約することができます。

## <a name="before-you-begin"></a>開始する前に

この記事は、AKS クラスターがすでに存在していることを前提としています。 AKS クラスターが必要であれば、[Azure CLI を使用した場合][aks-quickstart-cli]、[Azure PowerShell を使用した場合][kubernetes-walkthrough-powershell]、または [Azure portal を使用した場合][aks-quickstart-portal]の AKS クイックスタートを参照してください。

### <a name="install-aks-preview-cli-extension"></a>aks-preview CLI 拡張機能をインストールする

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

*aks-preview* Azure CLI 拡張機能も必要になります。 *aks-preview* Azure CLI 拡張機能は、[az extension add][az-extension-add] コマンドを使用してインストールします。 または、[az extension update][az-extension-update] コマンドを使用すると、使用可能な更新プログラムをインストールできます。

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-the-previewstartstopagentpool-preview-feature"></a>`PreviewStartStopAgentPool` プレビュー機能を登録する

この機能を使用するには、サブスクリプションで `PreviewStartStopAgentPool` 機能フラグも有効にする必要があります。

`PreviewStartStopAgentPool` 機能フラグは、次の例のとおり、[az feature register][az-feature-register] コマンドを使用して登録します。

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "PreviewStartStopAgentPool"
```

状態が *[登録済み]* と表示されるまでに数分かかります。 登録の状態は、[az feature list][az-feature-list] コマンドで確認できます。

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/PreviewStartStopAgentPool')].{Name:name,State:properties.state}"
```

準備ができたら、[az provider register][az-provider-register] コマンドを使用して、*Microsoft.ContainerService* リソース プロバイダーの登録を更新します。

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="stop-an-aks-node-pool"></a>AKS ノード プールを停止する

> [!IMPORTANT]
> ノード プールを開始/停止する場合は、次の動作が想定されます。
>
> * システム プールは停止できません。
> * スポット ノード プールはサポートされていません。
> * 停止したノード プールをアップグレードできます。
> * クラスターとノード プールが実行されている必要があります。

`az aks nodepool stop` を使用して、実行中の AKS ノード プールを停止します。 次の例は、*testnodepool* ノード プールを停止します。

```azurecli-interactive
az aks nodepool stop --nodepool-name testnodepool --resource-group myResourceGroup --cluster-name myAKSCluster
```

[az aks show][az-aks-show] コマンドを使用し、次の出力のように `powerState` が `Stopped` と表示されることを確認することで、ノード プールがいつ停止したかを確認できます。

```json
{
[...]
 "osType": "Linux",
    "podSubnetId": null,
    "powerState": {
        "code": "Stopped"
        },
    "provisioningState": "Succeeded",
    "proximityPlacementGroupId": null,
[...]
}
```

> [!NOTE]
> `provisioningState` に `Stopping` と表示されている場合、ノード プールはまだ完全に停止していません。

## <a name="start-a-stopped-aks-node-pool"></a>停止した AKS ノード プールを開始する

`az aks nodepool start` を使用して停止した AKS ノード プールを開始します。 次の例では、*testnodepool* という名前の停止したノード プールを開始します。

```azurecli-interactive
az aks nodepool start --nodepool-name testnodepool --resource-group myResourceGroup --cluster-name myAKSCluster
```

[az aks show][az-aks-show] を使用して `powerState` に `Running` が表示されているのを確認して、ノード プールが開始されていることを確認できます。 たとえば、次のように入力します。

```json
{
[...]
 "osType": "Linux",
    "podSubnetId": null,
    "powerState": {
        "code": "Running"
        },
    "provisioningState": "Succeeded",
    "proximityPlacementGroupId": null,
[...]
}
```

> [!NOTE]
> `provisioningState` に `Starting` と表示されている場合、ノード プールはまだ完全に開始していません。

---

## <a name="next-steps"></a>次の手順

- `User` プールを 0 にスケーリングする方法については、[`User` プールの 0 へのスケーリング](scale-cluster.md#scale-user-node-pools-to-0)に関する記事を参照してください。
- クラスターを停止する方法については、「[クラスターの開始または停止](start-stop-cluster.md)」を参照してください。
- スポット インスタンスを使用してコストを節約する方法については、[スポット ノード プールを AKS に追加する](spot-node-pool.md)方法に関するページを参照してください。
- AKS サポート ポリシーの詳細については、[AKS のサポート ポリシー](support-policies.md)に関するページを参照してください。

<!-- LINKS - external -->

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[az-aks-show]: /cli/azure/aks#az_aks_show
[kubernetes-walkthrough-powershell]: kubernetes-walkthrough-powershell.md
[stop-azakscluster]: /powershell/module/az.aks/stop-azakscluster
[get-azakscluster]: /powershell/module/az.aks/get-azakscluster
[start-azakscluster]: /powershell/module/az.aks/start-azakscluster