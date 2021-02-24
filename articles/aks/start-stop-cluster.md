---
title: Azure Kubernetes Service (AKS) の起動と停止
description: Azure Kubernetes Service (AKS) クラスターを起動または開始する方法について説明します。
services: container-service
ms.topic: article
ms.date: 09/24/2020
author: palma21
ms.openlocfilehash: 026da4237fe14726766b265e55930346293c71df
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/21/2021
ms.locfileid: "98662899"
---
# <a name="stop-and-start-an-azure-kubernetes-service-aks-cluster-preview"></a>Azure Kubernetes Service (AKS) クラスターの起動と開始 (プレビュー)

AKS ワークロードは、継続的に実行する必要がない場合があります。たとえば、営業時間中にのみ使用される開発クラスターなどです。 そのため、Azure Kubernetes Service (AKS) クラスターがアイドル状態になり、システム コンポーネントしか実行されない場合があります。 [すべての `User` ノード プールを 0 にスケーリングする](scale-cluster.md#scale-user-node-pools-to-0)ことで、クラスターの占有領域を削減できますが、クラスターの実行中にシステム コンポーネントを実行するには、[`System` プール](use-system-pools.md)が必要です。 これらの期間中にコストをさらに最適化するために、クラスターを完全にオフにする (停止する) ことができます。 この操作により、コントロール プレーンとエージェント ノードが完全に停止し、再起動時に保存されているすべてのオブジェクトとクラスターの状態を保持しながら、すべてのコンピューティング コストを節約することができます。 また、週末の後に中断したところからすぐに再開することや、バッチ ジョブの実行中にのみクラスターを実行することができるようになります。

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="before-you-begin"></a>開始する前に

この記事は、AKS クラスターがすでに存在していることを前提としています。 AKS クラスターが必要な場合は、[Azure CLI を使用した場合][aks-quickstart-cli]または [Azure portal を使用した場合][aks-quickstart-portal]の AKS のクイックスタートを参照してください。


### <a name="limitations"></a>制限事項

クラスターの開始および停止機能を使用する場合、次の制限事項が適用されます。

- この機能は、Virtual Machine Scale Sets でサポートされているクラスターでのみサポートされています。
- 停止した AKS クラスターのクラスターの状態は、最大 12 か月間保持されます。 クラスターの停止期間が 12 か月間を超えた場合、クラスターの状態を回復することはできません。 詳細については、[AKS のポリシーのサポート](support-policies.md)に関するページを参照してください。
- プレビュー期間中は、クラスターを停止する前に、クラスター オートスケーラー (CA) を停止する必要があります。
- 停止された AKS クラスターの起動または削除のみを行うことができます。 スケールやアップグレードなどの操作を実行するには、まずクラスターを起動します。

### <a name="install-the-aks-preview-azure-cli"></a>`aks-preview` Azure CLI をインストールする 

*aks-preview* Azure CLI 拡張機能バージョン 0.4.64 以降も必要です。 *aks-preview* Azure CLI 拡張機能は、[az extension add][az-extension-add] コマンドを使用してインストールします。 または、[az extension update][az-extension-update] コマンドを使用すると、使用可能な更新プログラムをインストールできます。

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
``` 

### <a name="register-the-startstoppreview-preview-feature"></a>`StartStopPreview` プレビュー機能を登録する

クラスターの起動または停止機能を使用するには、サブスクリプションで `StartStopPreview` 機能フラグを有効にする必要があります。

`StartStopPreview` 機能フラグは、次の例のとおり、[az feature register][az-feature-register] コマンドを使用して登録します。

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "StartStopPreview"
```

状態が *[登録済み]* と表示されるまでに数分かかります。 登録の状態は、[az feature list][az-feature-list] コマンドで確認できます。

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/StartStopPreview')].{Name:name,State:properties.state}"
```

準備ができたら、[az provider register][az-provider-register] コマンドを使用して、*Microsoft.ContainerService* リソース プロバイダーの登録を更新します。

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="stop-an-aks-cluster"></a>AKS クラスターを停止する

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

> [!IMPORTANT]
> [ポッド中断バジェット](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/)を使用している場合、ドレイン プロセスの完了に時間がかかるため、停止操作に時間がかかる可能性があります。


## <a name="start-an-aks-cluster"></a>AKS クラスターを起動する

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


## <a name="next-steps"></a>次の手順

- `User` プールを 0 にスケーリングする方法については、[`User` プールの 0 へのスケーリング](scale-cluster.md#scale-user-node-pools-to-0)に関する記事を参照してください。
- スポット インスタンスを使用してコストを節約する方法については、[スポット ノード プールを AKS に追加する](spot-node-pool.md)方法に関するページを参照してください。
- AKS サポート ポリシーの詳細については、[AKS のサポート ポリシー](support-policies.md)に関するページを参照してください。

<!-- LINKS - external -->

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli&preserve-view=true
[az-extension-add]: /cli/azure/extension?view=azure-cli-latest#az-extension-add&preserve-view=true
[az-extension-update]: /cli/azure/extension?view=azure-cli-latest#az-extension-update&preserve-view=true
[az-feature-register]: /cli/azure/feature?view=azure-cli-latest#az-feature-register&preserve-view=true
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest#az-feature-list&preserve-view=true
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest#az-provider-register&preserve-view=true
[az-aks-show]: /cli/azure/aks?view=azure-cli-latest#az_aks_show
