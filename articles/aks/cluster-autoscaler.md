---
title: Azure Kubernetes Service (AKS) でのクラスター オートスケーラーの使用
description: Azure Kubernetes Service (AKS) クラスターでのアプリケーションの需要を満たすように、クラスター オートスケーラーを使用してクラスターを自動的にスケーリングする方法について説明します。
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 01/29/2019
ms.author: iainfou
ms.openlocfilehash: 6b2302e69c9412170b55df4bfd8c1df5a9f75ef3
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/31/2019
ms.locfileid: "55479135"
---
# <a name="automatically-scale-a-cluster-to-meet-application-demands-on-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) でのアプリケーションの需要を満たすようにクラスターを自動的にスケーリング

Azure Kubernetes Service (AKS) のアプリケーションの需要に対応するには、ワークロードを実行するノードの数を調整する必要が生じる場合があります。 クラスター オートスケーラー コンポーネントは、リソース制約のためにスケジュールできないクラスター内のポッドを監視できます。 問題が検出されると、アプリケーションの需要を満たすためにノードの数が増やされます。 また、実行ポッドの不足について定期的にノードがチェックされ、必要に応じてノードの数が減らされます。 AKS クラスター内のノードの数を自動的に増減するこの機能を使用すると、効率的で、コスト効率の高いクラスターを実行できます。

この記事では、AKS クラスターでクラスター オートスケーラーを有効にして管理する方法について説明します。

> [!IMPORTANT]
> 現在、この機能はプレビュー段階にあります。 プレビュー版は、[追加使用条件][terms-of-use]に同意することを条件に使用できます。 この機能の一部の側面は、一般公開 (GA) 前に変更される可能性があります。

## <a name="before-you-begin"></a>開始する前に

この記事では、Azure CLI バージョン 2.0.55 以降を実行していることが要件です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール][azure-cli-install]に関するページを参照してください。

クラスター オートスケーラーをサポートする AKS クラスターは、仮想マシン スケール セットを使用し、かつ Kubernetes バージョン *1.12.4* 以降を実行している必要があります。 このスケール セット サポートはプレビュー段階です。 スケール セットを使用するクラスターをオプトインして作成するには、以下の例に示すように、[az extension add][az-extension-add] コマンドを使用して *aks-preview* Azure CLI 拡張機能をインストールします。

```azurecli-interactive
az extension add --name aks-preview
```

> [!NOTE]
> *aks-preview* 拡張機能をインストールすると、作成するすべての AKS クラスターでスケール セット プレビュー デプロイ モデルが使用されます。 通常のフルサポートのクラスターをオプトアウトして作成するには、`az extension remove --name aks-preview` を使用して拡張機能を削除します。

## <a name="about-the-cluster-autoscaler"></a>クラスター オートスケーラーについて

平日と夜間、または週末など、アプリケーションの変則的な需要に対応するために、多くの場合、クラスターには自動的にスケーリングする方法が必要になります。 AKS クラスターは、次の 2 つの方法のいずれかでスケーリングできます。

* **クラスター オートスケーラー**は、リソース制約のためにノードでスケジュールできないポッドを監視します。 状況に応じて、クラスターはノードの数を自動的に増やします。
* **ポッドの水平オートスケーラー**は、Kubernetes クラスターのメトリック サーバーを使用して、ポッドのリソースの需要をモニターします。 サービスに必要なリソース量が増えると、その需要を満たすためにポッドの数が自動的に増やされます。

![クラスター オートスケーラーとポッドの水平オートスケーラーは、必要なアプリケーション需要に対応するために、多くの場合、連携して機能します。](media/autoscaler/cluster-autoscaler.png)

また、ポッドの水平オートスケーラーとクラスター オートスケーラーは、必要に応じてポッドとノードの数を減らすこともできます。 クラスター オートスケーラーは、一定期間未使用の容量があった場合、ノードの数を減らします。 クラスター オートスケーラーによって削除されるノード上のポッドは、クラスター内の他の場所で安全にスケジュールされます。 クラスター オートスケーラーは、以下の状況のように、ポッドが移動できない場合はスケールダウンできないことがあります。

* ポッドが直接作成されており、デプロイやレプリカ セットなどのコントローラー オブジェクトによってサポートされていない。
* Pod Disruption Budget (PDB) の制限が非常に厳しく、ポッドの数が特定のしきい値を下回ることが許可されていない。
* ポッドが、別のノードでスケジュールされた場合に適用できないノード セレクターまたはアンチ アフィニティーを使用している。

クラスター オートスケーラーでスケールダウンを行えない場合がある状況について詳しくは、「[What types of pods can prevent the cluster autoscaler from removing a node?][autoscaler-scaledown]」を参照してください。

クラスター オートスケーラーは、スケール イベント間の時間間隔やリソースしきい値などに対して開始パラメーターを使用します。 これらのパラメーターは、Azure プラットフォームによって定義されており、現在公開されていないため、調整することはできません。 クラスター オートスケーラーが使用するパラメーターについて詳しくは、「[What are the cluster autoscaler parameters?][autoscaler-parameters]」を参照してください。

これらの 2 つのオートスケーラーは連携して機能し、両方のオートスケーラーが 1 つのクラスターにデプロイされることがよくあります。 この 2 つを組み合わせて使用する場合、ポッドの水平オートスケーラーの主な役割は、アプリケーションの需要を満たすために必要な数のポッドを実行することになります。 クラスター オートスケーラーの主な役割は、スケジュールされたポッドをサポートするために必要な数のノードを実行することになります。

> [!NOTE]
> クラスター オートスケーラーを使用する場合、手動スケーリングは無効になります。 必要なノード数は、クラスター オートスケーラーによって決定されるようにします。 クラスターを手動でスケーリングする場合は、[クラスター オートスケーラーを無効化](#disable-the-cluster-autoscaler)します。

## <a name="create-an-aks-cluster-and-enable-the-cluster-autoscaler"></a>AKS クラスターの作成とクラスター オートスケーラーの有効化

AKS クラスターを作成する必要がある場合は、[az aks create][az-aks-create] コマンドを使用します。 前述の「[開始する前に](#before-you-begin)」セクションで概説されている必須の最小バージョン番号以上の *--kubernetes-version* を指定します。 クラスター オートスケーラーを有効にして構成するには、*--enable-cluster-autoscaler* パラメーターを使用し、ノードの *--min-count* と *--max-count* を指定します。

以下の例は、仮想マシン スケール セットとクラスター オートスケーラーを有効にして AKS クラスターを作成し、最小 *1* つ、最大 *3* つのノードを使用します。

```azurecli-interactive
# First create a resource group
az group create --name myResourceGroup --location canadaeast

# Now create the AKS cluster and enable the cluster autoscaler
az aks create \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --kubernetes-version 1.12.4 \
  --node-count 1 \
  --enable-vmss \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

このクラスターを作成して、クラスター オートスケーラーの設定を構成するには数分かかります。

### <a name="enable-the-cluster-autoscaler-on-an-existing-aks-cluster"></a>既存の AKS クラスターでのクラスター オートスケーラーの有効化

前述の「[開始する前に](#before-you-begin)」セクションで概説されている要件を満たす既存の AKS クラスターでクラスター オートスケーラーを有効にすることができます。 [az aks update][az-aks-update] コマンドを使用して、*--enable-cluster-autoscaler* を選択し、ノードの *--min-count* と *--max-count* を指定します。 以下の例は、最小 *1* つ、最大 *3* つのノードを使用する既存のクラスターでクラスター オートスケーラーを有効にします。

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

最小ノード数がクラスター内の既存ノード数を超えている場合は、追加ノードを作成するために数分かかります。

## <a name="change-the-cluster-autoscaler-settings"></a>クラスター オートスケーラーの設定の変更

既存の AKS クラスターを作成または更新する前述のステップで、クラスター オートスケーラーの最小ノード数は *1* に設定され、最大ノード数は *3* に設定されました。 アプリケーション需要の変化に応じて、クラスター オートスケーラーのノード数の調整が必要になる場合があります。

ノード数を変更するには、[az aks update][az-aks-update] コマンドを使用して、最小値と最大値を指定します。 以下の例は、*--min-count* を *1* に、*--max-count* を *5* に設定します。

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --update-cluster-autoscaler \
  --min-count 1 \
  --max-count 5
```

> [!NOTE]
> プレビュー中は、現在クラスターに設定されている数より多い最小ノード数を設定することはできません。 例えば、現在、最小数が *1* に設定されている場合、最小数を *3* に更新することはできません。

アプリケーションとサービスのパフォーマンスをモニターして、必要なパフォーマンスに一致するようにクラスター オートスケーラーのノード数を調整してください。

## <a name="disable-the-cluster-autoscaler"></a>クラスター オートスケーラーの無効化

クラスター オートスケーラーを今後使用しない場合は、[az aks update][az-aks-update] コマンドを使用して無効にできます。 クラスター オートスケーラーが無効になってもノードは削除されません。

クラスター オートスケーラーを削除するには、以下の例に示すように *--disable-cluster-autoscaler* パラメーターを指定します。

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --disable-cluster-autoscaler
```

[az aks scale][az-aks-scale] コマンドを使用して、クラスターを手動でスケーリングできます。 ポッドの水平オートスケーラーを使用する場合、その機能はクラスター オートスケーラーを無効にしても維持されますが、ノード リソースがすべて使用中になると、ポッドをスケジュールできなくなる場合があります。

## <a name="next-steps"></a>次の手順

この記事では、AKS ノードの数を自動的にスケーリングする方法について説明します。 また、ポッドの水平オートスケーラーを使用して、アプリケーションを実行するポッドの数を自動的に調整することもできます。 ポッドの水平オートスケーラーの使用手順については、「[AKS でのアプリケーションのスケーリング][aks-scale-apps]」を参照してください。

<!-- LINKS - internal -->
[aks-upgrade]: upgrade-cluster.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-extension-add]: /cli/azure/extension#az-extension-add
[aks-scale-apps]: tutorial-kubernetes-scale.md
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-scale]: /cli/azure/aks#az-aks-scale

<!-- LINKS - external -->
[az-aks-update]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[autoscaler-scaledown]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-types-of-pods-can-prevent-ca-from-removing-a-node
[autoscaler-parameters]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-are-the-parameters-to-ca
