---
title: Kubernetes on Azure のチュートリアル - クラスターのアップグレード
description: この Azure Kubernetes Service (AKS) のチュートリアルでは、既存の AKS クラスターを最新の使用可能な Kubernetes バージョンにアップグレードする方法を学習します。
services: container-service
ms.topic: tutorial
ms.date: 05/24/2021
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 4751081d628f3ea1bff411b5e391377e8b771939
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/29/2021
ms.locfileid: "110697750"
---
# <a name="tutorial-upgrade-kubernetes-in-azure-kubernetes-service-aks"></a>チュートリアル:Azure Kubernetes Service (AKS) での Kubernetes のアップグレード

アプリケーションとクラスターのライフサイクルの一環として、使用可能な最新バージョンの Kubernetes にアップグレードし、新しい機能を使用することができます。 Azure Kubernetes Service (AKS) クラスターは、Azure CLI を使用してアップグレードできます。

このチュートリアル (7 部構成の第 7 部) では、Kubernetes クラスターがアップグレードされます。 学習内容は次のとおりです。

> [!div class="checklist"]
> * 現在の使用可能な Kubernetes バージョンを識別する
> * Kubernetes ノードをアップグレードする
> * 正常なアップグレードを検証する

## <a name="before-you-begin"></a>開始する前に

これまでのチュートリアルでは、アプリケーションをコンテナー イメージにパッケージ化しました。 このイメージを Azure Container Registry にアップロードし、AKS クラスターを作成しました。 その後、AKS クラスターにアプリケーションをデプロイしました。 これらの手順を完了しておらず、順番に進めたい場合は、[チュートリアル 1 - コンテナー イメージを作成する][aks-tutorial-prepare-app]に関するページから開始してください。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

このチュートリアルでは、Azure CLI バージョン 2.0.53 以降を実行している必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール][azure-cli-install]に関するページを参照してください。

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

このチュートリアルでは、Azure PowerShell バージョン 5.9.0 以降を実行している必要があります。 バージョンを確認するには、`Get-InstalledModule -Name Az` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure PowerShell ][azure-powershell-install]のインストールに関するページをご覧ください。

---

## <a name="get-available-cluster-versions"></a>使用可能なクラスターのバージョンを取得する

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

クラスターをアップグレードする前に、[az aks get-upgrades][] コマンドを使用して、アップグレードで利用できる Kubernetes のリリースを確認します。

```azurecli
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster
```

次の例では、現在のバージョンが *1.18.10* であることがわかります。また、 *[Upgrades]* の下に、利用可能なバージョンが示されています。

```json
{
  "agentPoolProfiles": null,
  "controlPlaneProfile": {
    "kubernetesVersion": "1.18.10",
    ...
    "upgrades": [
      {
        "isPreview": null,
        "kubernetesVersion": "1.19.1"
      },
      {
        "isPreview": null,
        "kubernetesVersion": "1.19.3"
      }
    ]
  },
  ...
}
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

クラスターをアップグレードする前に、[Get-AzAksCluster][get-azakscluster] コマンドレットを使用して、実行している Kubernetes のバージョンと、それが存在するリージョンを特定します。

```azurepowershell
Get-AzAksCluster -ResourceGroupName myResourceGroup -Name myAKSCluster |
  Select-Object -Property Name, KubernetesVersion, Location
```

次の例では、現在のバージョンは *1.19.9* です。

```output
Name         KubernetesVersion Location
----         ----------------- --------
myAKSCluster 1.19.9            eastus
```

[Get-AzAksVersion][get-azaksversion] コマンドレットを使用して、AKS クラスターが存在するリージョンで使用できる Kubernetes アップグレード リリースを確認します。

```azurepowershell
Get-AzAksVersion -Location eastus | Where-Object OrchestratorVersion -gt 1.19.9
```

使用可能なバージョンは *OrchestratorVersion* の下に表示されます。

```output
OrchestratorType    : Kubernetes
OrchestratorVersion : 1.20.2
DefaultProperty     :
IsPreview           :
Upgrades            : {Microsoft.Azure.Commands.Aks.Models.PSOrchestratorProfile}

OrchestratorType    : Kubernetes
OrchestratorVersion : 1.20.5
DefaultProperty     :
IsPreview           :
Upgrades            : {}
```

---

## <a name="upgrade-a-cluster"></a>クラスターのアップグレード

実行中のアプリケーションの中断を最小限に抑えるために、AKS ノードは慎重に切断およびドレインされます。 このプロセスでは、以下の手順が行われます。

1. Kubernetes スケジューラが、アップグレードされるノードに追加のポッドがスケジュールされないようにします。
1. ノードで実行中のポッドは、クラスター内の他のノードにスケジュールされます。
1. 最新の Kubernetes コンポーネントを実行するノードが作成されます。
1. 新しいノードの準備が整い、クラスターに参加すると、Kubernetes スケジューラによってそのノードでポッドの実行が開始されます。
1. 古いノードが削除され、クラスター内の次のノードが切断およびドレイン プロセスを開始します。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[az aks upgrade][] コマンドを使用して、AKS クラスターをアップグレードします。

```azurecli
az aks upgrade \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --kubernetes-version KUBERNETES_VERSION
```

> [!NOTE]
> 一度に 1 つのマイナー バージョンのみをアップグレードできます。 たとえば、*1.14.x* から *1.15.x* にアップグレードすることはできますが、*1.14.x* から *1.16.x* に直接アップグレードすることはできません。 *1.14.x* から *1.16.x* にアップグレードするには、まず *1.14.x* から *1.15.x* にアップグレードします。その後、*1.15.x* から *1.16.x* にもう一度アップグレードします。

次の出力例の抜粋には、*1.19.1* にアップグレードした結果が示されています。 *kubernetesVersion* が *1.19.1* としてレポートされていることがわかります。

```json
{
  "agentPoolProfiles": [
    {
      "count": 3,
      "maxPods": 110,
      "name": "nodepool1",
      "osType": "Linux",
      "storageProfile": "ManagedDisks",
      "vmSize": "Standard_DS1_v2",
    }
  ],
  "dnsPrefix": "myAKSClust-myResourceGroup-19da35",
  "enableRbac": false,
  "fqdn": "myaksclust-myresourcegroup-19da35-bd54a4be.hcp.eastus.azmk8s.io",
  "id": "/subscriptions/<Subscription ID>/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster",
  "kubernetesVersion": "1.19.1",
  "location": "eastus",
  "name": "myAKSCluster",
  "type": "Microsoft.ContainerService/ManagedClusters"
}
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

[Set-AzAksCluster][set-azakscluster] コマンドレットを使用して、AKS クラスターをアップグレードします。

```azurepowershell
Set-AzAksCluster -ResourceGroupName myResourceGroup -Name myAKSCluster -KubernetesVersion <KUBERNETES_VERSION>
```

> [!NOTE]
> 一度に 1 つのマイナー バージョンのみをアップグレードできます。 たとえば、*1.14.x* から *1.15.x* にアップグレードすることはできますが、*1.14.x* から *1.16.x* に直接アップグレードすることはできません。 *1.14.x* から *1.16.x* にアップグレードするには、まず *1.14.x* から *1.15.x* にアップグレードします。その後、*1.15.x* から *1.16.x* にもう一度アップグレードします。

次の出力例の抜粋には、*1.19.9* にアップグレードした結果が示されています。 *kubernetesVersion* が *1.20.2* としてレポートされていることがわかります。

```output
ProvisioningState       : Succeeded
MaxAgentPools           : 100
KubernetesVersion       : 1.20.2
PrivateFQDN             :
AgentPoolProfiles       : {default}
Name                    : myAKSCluster
Type                    : Microsoft.ContainerService/ManagedClusters
Location                : eastus
Tags                    : {}
```

---

## <a name="validate-an-upgrade"></a>アップグレードを検証する

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[az aks show][] コマンドを次のように使用して、アップグレードが成功したことを確認します。

```azurecli
az aks show --resource-group myResourceGroup --name myAKSCluster --output table
```

次の出力例は、AKS クラスターで *KubernetesVersion 1.19.1* が実行されていることを示しています。

```output
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ----------------------------------------------------------------
myAKSCluster  eastus      myResourceGroup  1.19.1               Succeeded            myaksclust-myresourcegroup-19da35-bd54a4be.hcp.eastus.azmk8s.io
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

[Get-AzAksCluster][get-azakscluster] コマンドレットを次のように使用して、アップグレードが成功したことを確認します。

```azurepowershell
Get-AzAksCluster -ResourceGroupName myResourceGroup -Name myAKSCluster |
  Select-Object -Property Name, Location, KubernetesVersion, ProvisioningState
```

次の出力例は、AKS クラスターで *KubernetesVersion 1.20.2* が実行されていることを示しています。

```output
Name         Location KubernetesVersion ProvisioningState
----         -------- ----------------- -----------------
myAKSCluster eastus   1.20.2            Succeeded
```

---

## <a name="delete-the-cluster"></a>クラスターを削除する

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

このチュートリアルはシリーズの最後の部分なので、AKS クラスターを削除することをお勧めします。 Kubernetes ノードは Azure 仮想マシン (VM) で実行され、クラスターを使用しない場合でも引き続き料金が発生するためです。 [az group delete][az-group-delete] コマンドを使用して、リソース グループ、コンテナー サービス、およびすべての関連リソースを削除します。

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```
### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

このチュートリアルはシリーズの最後の部分なので、AKS クラスターを削除することをお勧めします。 Kubernetes ノードは Azure 仮想マシン (VM) で実行され、クラスターを使用しない場合でも引き続き料金が発生するためです。 [Remove-AzResourceGroup][remove-azresourcegroup] コマンドレットを使用して、リソース グループ、コンテナー サービス、すべての関連リソースを削除します。

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

---

> [!NOTE]
> クラスターを削除したとき、AKS クラスターで使用される Azure Active Directory サービス プリンシパルは削除されません。 サービス プリンシパルを削除する手順については、[AKS のサービス プリンシパルに関する考慮事項と削除][sp-delete]に関するページを参照してください。 マネージド ID を使用した場合、その ID はプラットフォームによって管理され、ユーザーがシークレットをプロビジョニングしたりローテーションしたりする必要はありません。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、AKS クラスター内の Kubernetes をアップグレードしました。 以下の方法を学習しました。

> [!div class="checklist"]
> * 現在の使用可能な Kubernetes バージョンを識別する
> * Kubernetes ノードをアップグレードする
> * 正常なアップグレードを検証する

AKS の詳細については、[AKS の概要][aks-intro]に関するページを参照してください。 AKS を使用した完全なソリューションを作成するうえでのガイダンスについては、[AKS ソリューション ガイダンス][aks-solution-guidance]を参照してください。

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-intro]: ./intro-kubernetes.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[az aks show]: /cli/azure/aks#az_aks_show
[az aks get-upgrades]: /cli/azure/aks#az_aks_get_upgrades
[az aks upgrade]: /cli/azure/aks#az_aks_upgrade
[azure-cli-install]: /cli/azure/install-azure-cli
[az-group-delete]: /cli/azure/group#az_group_delete
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[aks-solution-guidance]: /azure/architecture/reference-architectures/containers/aks-start-here?WT.mc_id=AKSDOCSPAGE
[azure-powershell-install]: /powershell/azure/install-az-ps
[get-azakscluster]: /powershell/module/az.aks/get-azakscluster
[get-azaksversion]: /powershell/module/az.aks/get-azaksversion
[set-azakscluster]: /powershell/module/az.aks/set-azakscluster
[remove-azresourcegroup]: /powershell/module/az.resources/remove-azresourcegroup
