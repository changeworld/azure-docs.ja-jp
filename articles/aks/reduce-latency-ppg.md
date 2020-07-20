---
title: 近接配置グループを使用して Azure Kubernetes Service (AKS) クラスターの待機時間を短縮する
description: 近接配置グループを使用して、AKS クラスターのワークロードの待機時間を短縮する方法を説明します。
services: container-service
manager: gwallace
ms.topic: article
ms.date: 06/22/2020
ms.openlocfilehash: 1bcdfb4bb3c910feeac0521308e1e7d733fbd959
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2020
ms.locfileid: "86244074"
---
# <a name="reduce-latency-with-proximity-placement-groups-preview"></a>近接配置グループを使用して待機時間を短縮する (プレビュー)

> [!Note]
> AKS で近接配置グループを使用すると、コロケーションはエージェント ノードにのみ適用されます。 ノード間、および対応するホストされたポッド間の待機時間が短縮されます。 コロケーションは、クラスターのコントロール プレーンの配置には影響しません。

Azure にアプリケーションをデプロイするときに、複数のリージョンまたは可用性ゾーンに仮想マシン (VM) インスタンスを分散すると、ネットワーク待機時間が発生し、アプリケーションの全体的なパフォーマンスに影響を及ぼす可能性があります。 近接配置グループは、Azure コンピューティング リソースが互いに物理的に近くに配置されるようにするために使用される論理的なグループ化です。 ゲーム、エンジニアリング シミュレーション、高頻度取引 (HFT) などの一部のアプリケーションでは、短い待機時間と、短時間で完了するタスクが必要です。 これらのようなハイ パフォーマンス コンピューティング (HPC) シナリオでは、クラスターのノード プールに[近接配置グループ](../virtual-machines/linux/co-location.md#proximity-placement-groups)を使用することを検討します。

## <a name="limitations"></a>制限事項

* 近接配置グループは、1 つの可用性ゾーンに分散します。
* 現在、仮想マシン可用性セットを使用する AKS クラスターはサポートされていません。
* 近接配置グループを使用するように既存のノード プールを変更することはできません。

> [!IMPORTANT]
> AKS のプレビュー機能は、セルフサービスのオプトイン単位で利用できます。 プレビューは、"現状有姿のまま" および "利用可能な限度" で提供され、サービス レベル契約および限定保証から除外されるものとします。 AKS プレビューは、ベストエフォート ベースでカスタマー サポートによって部分的にカバーされます。 そのため、これらの機能は、運用環境での使用を意図していません。 詳細については、次のサポート記事を参照してください。
>
> - [AKS のサポート ポリシー](support-policies.md)
> - [Azure サポートに関する FAQ](faq.md)

## <a name="before-you-begin"></a>開始する前に

次のリソースがインストールされている必要があります。

- aks-preview 0.4.53 拡張機能

### <a name="set-up-the-preview-feature-for-proximity-placement-groups"></a>近接配置グループのプレビュー機能を設定する

> [!IMPORTANT]
> AKS で近接配置グループを使用すると、コロケーションはエージェント ノードにのみ適用されます。 ノード間、および対応するホストされたポッド間の待機時間が短縮されます。 コロケーションは、クラスターのコントロール プレーンの配置には影響しません。

```azurecli-interactive
# register the preview feature
az feature register --namespace "Microsoft.ContainerService" --name "ProximityPlacementGroupPreview"
```

登録には数分かかることがあります。 次のコマンドを使用して、機能が登録されていることを確認します。

```azurecli-interactive
# Verify the feature is registered:
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/ProximityPlacementGroupPreview')].{Name:name,State:properties.state}"
```

プレビューの間、近接配置グループを使用するには *aks-preview* CLI 拡張機能が必要です。 [az extension add][az-extension-add] コマンドを使用した後、[az extension update][az-extension-update] コマンドを使用して、使用可能な更新プログラムがあるかどうかを確認します。

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```
## <a name="node-pools-and-proximity-placement-groups"></a>ノード プールと近接配置グループ

近接配置グループでデプロイする最初のリソースは、特定のデータ センターに接続します。 同じ近接配置グループでデプロイされた他のリソースは、同じデータ センター内に併置されます。 近接配置グループを使用するリソースをすべて停止 (割り当て解除) または削除すると、接続は解除されます。

* 多くのノード プールを、1 つの近接配置グループに関連付けることができます。
* 1 つのノード プールは、1 つの近接配置グループとだけ関連付けることができます。

## <a name="create-a-new-aks-cluster-with-a-proximity-placement-group"></a>近接配置グループを使用して新しい AKS クラスターを作成する

次の例では、[az group create][az-group-create] コマンドを使用して、*myResourceGroup* という名前のリソース グループを *centralus* リージョンに作成しています。 次いで、*myAKSCluster* という名前の AKS クラスターを [az aks create][az-aks-create] コマンドを使用して作成しています。 

高速ネットワークを使用すると、仮想マシンのネットワーク パフォーマンスが大幅に向上します。 近接配置グループと高速ネットワークを併用するのが理想的です。 既定では、AKS により[サポートされている仮想マシン インスタンス](../virtual-network/create-vm-accelerated-networking-cli.md?toc=/azure/virtual-machines/linux/toc.json#limitations-and-constraints)で高速ネットワークが使用されます。これには、2 つ以上の vCPU を持つほとんどの Azure 仮想マシンが含まれます。

近接配置グループを使用して新しい AKS クラスターを作成します。

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location centralus
```
次のコマンドを実行し、返された ID を格納します。

```azurecli-interactive
# Create proximity placement group
az ppg create -n myPPG -g myResourceGroup -l centralus -t standard
```

このコマンドでは、後の CLI コマンドに必要な *id* 値を含む出力が生成されます。

```output
{
  "availabilitySets": null,
  "colocationStatus": null,
  "id": "/subscriptions/yourSubscriptionID/resourceGroups/myResourceGroup/providers/Microsoft.Compute/proximityPlacementGroups/myPPG",
  "location": "centralus",
  "name": "myPPG",
  "proximityPlacementGroupType": "Standard",
  "resourceGroup": "myResourceGroup",
  "tags": {},
  "type": "Microsoft.Compute/proximityPlacementGroups",
  "virtualMachineScaleSets": null,
  "virtualMachines": null
}
```

次のコマンドの *myPPGResourceID* の値には、近接配置グループのリソース ID を使用します。

```azurecli-interactive
# Create an AKS cluster that uses a proximity placement group for the initial node pool
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --ppg myPPGResourceID
```

## <a name="add-a-proximity-placement-group-to-an-existing-cluster"></a>既存のクラスターに近接配置グループを追加する

新しいノード プールを作成することにより、既存のクラスターに近接配置グループを追加できます。 その後、必要に応じて、既存のワークロードを新しいノード プールに移行してから、元のノード プールを削除することができます。

前に作成したのと同じ近接配置グループを使用します。これにより、AKS クラスター内の両方のノード プールのエージェント ノードが、物理的に同じデータ センターに配置されます。

前に作成した近接配置グループのリソース ID を使用し、[`az aks nodepool add`][az-aks-nodepool-add] コマンドで新しいノード プールを追加します。

```azurecli-interactive
# Add a new node pool that uses a proximity placement group, use a --node-count = 1 for testing
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 1 \
    --ppg myPPGResourceID
```

## <a name="clean-up"></a>クリーンアップ

クラスターを削除するには、[`az group delete`][az-group-delete] コマンドを使用して AKS リソース グループを削除します。

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>次のステップ

* [近接配置グループ][proximity-placement-groups]についてさらに学習します。

<!-- LINKS - Internal -->
[azure-ad-rbac]: azure-ad-rbac.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-upgrades]: /cli/azure/aks#az-aks-get-upgrades
[az-aks-upgrade]: /cli/azure/aks#az-aks-upgrade
[az-aks-show]: /cli/azure/aks#az-aks-show
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[proximity-placement-groups]: ../virtual-machines/linux/co-location.md#proximity-placement-groups
[az-aks-create]: /cli/azure/aks#az-aks-create
[system-pool]: ./use-system-pools.md
[az-aks-nodepool-add]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-add
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
