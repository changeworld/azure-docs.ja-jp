---
title: Azure Kubernetes Service (AKS) の起動と停止
description: Azure Kubernetes Service (AKS) クラスターを起動または開始する方法について説明します。
services: container-service
ms.topic: article
ms.date: 08/09/2021
author: palma21
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 8a19cc69d14e32a20819618efcf60594aef34be9
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2021
ms.locfileid: "132371982"
---
# <a name="stop-and-start-an-azure-kubernetes-service-aks-cluster"></a>Azure Kubernetes Service (AKS) クラスターの停止と起動

AKS ワークロードは、継続的に実行する必要がない場合があります。たとえば、営業時間中にのみ使用される開発クラスターなどです。 そのため、Azure Kubernetes Service (AKS) クラスターがアイドル状態になり、システム コンポーネントしか実行されない場合があります。 [すべての `User` ノード プールを 0 にスケーリングする](scale-cluster.md#scale-user-node-pools-to-0)ことで、クラスターの占有領域を削減できますが、クラスターの実行中にシステム コンポーネントを実行するには、[`System` プール](use-system-pools.md)が必要です。
これらの期間中にコストをさらに最適化するために、クラスターを完全にオフにする (停止する) ことができます。 この操作によってコントロール プレーンとエージェント ノードが完全に停止されます。再起動時のためにすべてのオブジェクト (スタンドアロン ポッドを除く) とクラスターの状態が保持され、あらゆるコンピューティング コストを節約できます。 また、週末の後に中断したところからすぐに再開することや、バッチ ジョブの実行中にのみクラスターを実行することができるようになります。

## <a name="before-you-begin"></a>開始する前に

この記事は、AKS クラスターがすでに存在していることを前提としています。 AKS クラスターが必要であれば、[Azure CLI を使用した場合][aks-quickstart-cli]、[Azure PowerShell を使用した場合][kubernetes-walkthrough-powershell]、または [Azure portal を使用した場合][aks-quickstart-portal]の AKS クイックスタートを参照してください。

### <a name="limitations"></a>制限事項

クラスターの開始および停止機能を使用する場合、次の制限事項が適用されます。

- この機能は、Virtual Machine Scale Sets でサポートされているクラスターでのみサポートされています。
- 停止した AKS クラスターのクラスターの状態は、最大 12 か月間保持されます。 クラスターの停止期間が 12 か月間を超えた場合、クラスターの状態を回復することはできません。 詳細については、[AKS のポリシーのサポート](support-policies.md)に関するページを参照してください。
- 停止された AKS クラスターの起動または削除のみを行うことができます。 スケールやアップグレードなどの操作を実行するには、まずクラスターを起動します。
- プライベート クラスターにリンクされている、ユーザーがプロビジョニングした PrivateEndpoints は、停止した AKS クラスターを開始するときに削除して再作成する必要があります。
- 停止プロセスはすべてのノードをドレインするため、スタンドアロン ポッド (Deployment、StatefulSet、DaemonSet、Job などによって管理されていないポッド) はすべて削除されます。

## <a name="stop-an-aks-cluster"></a>AKS クラスターを停止する

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

`az aks stop` コマンドを使用して、実行中の AKS クラスターのノードとコントロール プレーンを停止できます。 次の例では、*myAKSCluster* という名前のクラスターを停止します。

```azurecli-interactive
az aks stop --name myAKSCluster --resource-group myResourceGroup
```

[az aks show][az-aks-show] コマンドを使用し、次の出力のように `powerState` が `Stopped` と表示されることを確認することで、クラスターがいつ停止したかを確認できます。

```json
{
[...]
  "nodeResourceGroup": "MC_myResourceGroup_myAKSCluster_westus2",
  "powerState":{
    "code":"Stopped"
  },
  "privateFqdn": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
[...]
}
```

`provisioningState` に `Stopping` が表示されている場合は、クラスターがまだ完全に停止していないことを意味します。

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

[Stop-AzAksCluster][stop-azakscluster] コマンドレットを使用し、実行中の AKS クラスターのノードとコントロール プレーンを停止できます。 次の例では、*myAKSCluster* という名前のクラスターを停止します。

```azurepowershell-interactive
Stop-AzAksCluster -Name myAKSCluster -ResourceGroupName myResourceGroup
```

[Get-AzAksCluster][get-azakscluster] コマンドレットを使用し、次の出力のように、`ProvisioningState` に `Stopped` が表示されていることを確認することでクラスターの停止を確認します。

```Output
ProvisioningState       : Stopped
MaxAgentPools           : 100
KubernetesVersion       : 1.20.7
...
```

`ProvisioningState` に `Stopping` が表示されている場合は、クラスターがまだ完全に停止していないことを意味します。

---

> [!IMPORTANT]
> [ポッド中断バジェット](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/)を使用している場合、ドレイン プロセスの完了に時間がかかるため、停止操作に時間がかかる可能性があります。

## <a name="start-an-aks-cluster"></a>AKS クラスターを起動する

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

`az aks start` コマンドを使用して、停止した AKS クラスターのノードとコントロール プレーンを起動できます。 クラスターは、以前のコントロール プレーンの状態とエージェント ノード数で再起動されます。
次の例では、*myAKSCluster* という名前のクラスターを起動します。

```azurecli-interactive
az aks start --name myAKSCluster --resource-group myResourceGroup
```

[az aks show][az-aks-show] コマンドを使用し、次の出力のように `powerState` が `Running` と表示されることを確認することで、クラスターがいつ起動したかを確認できます。

```json
{
[...]
  "nodeResourceGroup": "MC_myResourceGroup_myAKSCluster_westus2",
  "powerState":{
    "code":"Running"
  },
  "privateFqdn": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
[...]
}
```

`provisioningState` に `Starting` が表示されている場合は、クラスターがまだ完全に起動していないことを意味します。

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

[Start-AzAksCluster][start-azakscluster] コマンドレットを使用し、停止した AKS クラスターのノードとコントロール プレーンを開始できます。 クラスターは、以前のコントロール プレーンの状態とエージェント ノード数で再起動されます。
次の例では、*myAKSCluster* という名前のクラスターを起動します。

```azurepowershell-interactive
Start-AzAksCluster -Name myAKSCluster -ResourceGroupName myResourceGroup
```

[Get-AzAksCluster][get-azakscluster] コマンドレットを使用し、次の出力のように、`ProvisioningState` に `Succeeded` が表示されていることを確認することでクラスターの起動時間を確認できます。

```Output
ProvisioningState       : Succeeded
MaxAgentPools           : 100
KubernetesVersion       : 1.20.7
...
```

`ProvisioningState` に `Starting` が表示されている場合は、クラスターがまだ完全に起動していないことを意味します。

---

> [!NOTE]
> クラスター バックアップを開始するとき、次の動作が想定されます。
>
> * API サーバーの IP アドレスは異なっている場合があります。
> * クラスター オートスケーラーを使用している場合、クラスターのバックアップを開始するとき、現在のノード数が、設定した最小と最大の範囲値の間にないことがあります。 クラスターは、ワークロードを実行するために必要なノード数で開始します。これは、オートスケーラー設定の影響を受けません。 クラスターでスケーリング操作を実行すると、最小値と最大値は現在のノード数に影響し、クラスターはいずれはその必要範囲に入り、クラスターを停止するまでそこにとどまります。

## <a name="next-steps"></a>次の手順

- `User` プールを 0 にスケーリングする方法については、[`User` プールの 0 へのスケーリング](scale-cluster.md#scale-user-node-pools-to-0)に関する記事を参照してください。
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
