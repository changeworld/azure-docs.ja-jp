---
title: Azure Kubernetes Service (AKS) とアップタイム SLA
description: Azure Kubernetes Service (AKS) API サーバーのオプションのアップタイム SLA オファリングについて説明します。
services: container-service
ms.topic: conceptual
ms.date: 01/08/2021
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 77bdfb4ed75b9287b911a6b0d2742c235b37e297
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/17/2021
ms.locfileid: "122325071"
---
# <a name="azure-kubernetes-service-aks-uptime-sla"></a>Azure Kubernetes Service (AKS) のアップタイム SLA

アップタイム SLA は、利用料金に応じて AKS クラスターの SLA を向上させるサービス レベルです。 アップタイム SLA があるクラスターは、AKS REST API の Paid サービス レベルとも見なされ、コントロール プレーン リソースの増量に加えて、クラスターの負荷に合わせて自動的にスケーリングされます。 アップタイム SLA は、[可用性ゾーン][availability-zones]を使用するクラスターに対しては Kubernetes API サーバー エンドポイントの 99.95% の可用性を保証し、可用性ゾーンを使用しないクラスターに対しては 99.9% の可用性を保証します。 AKS は、更新ドメインおよび障害ドメイン全体でマスター ノード レプリカを使用して、SLA 要件が満たされるようにします。

AKS では、コントロール プレーン コンポーネントの可用性を確保するために、実稼働ワークロードでアップタイム SLA の使用を推奨しています。 対照的に、Free サービス レベルのクラスターは、レプリカが少なく、コントロール プレーンのリソースが限られており、実稼働ワークロードには適していません。

お客様は今まで通り、サービス レベル目標 (SLO) が 99.5% の無料クラスターを個数無制限で作成し、優先 SLO を選択することができます。 

> [!IMPORTANT]
> エグレス ロックダウンを使用するクラスターの場合、[エグレス トラフィックの制限](limit-egress-traffic.md)に関するページを参照して、適切なポートを開きます。

## <a name="region-availability"></a>利用可能なリージョン

* アップタイム SLA は、[AKS がサポートされている](https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service)パブリック リージョンおよび Azure Government リージョンで利用できます。
* アップタイム SLA は、AKS がサポートされているすべてのパブリック リージョン内の[プライベート AKS クラスター][private-clusters]に対して使用できます。

## <a name="sla-terms-and-conditions"></a>SLA の使用条件

アップタイム SLA は有料の機能であり、クラスターごとに有効化されます。 アップタイム SLA の価格は、個々のクラスターのサイズではなく、個別のクラスターの数によって決まります。 詳細については、[アップタイム SLA の価格の詳細](https://azure.microsoft.com/pricing/details/kubernetes-service/)に関するページを参照してください。

## <a name="before-you-begin"></a>開始する前に

* [Azure CLI](/cli/azure/install-azure-cli) バージョン 2.8.0 以降をインストールします

## <a name="creating-a-new-cluster-with-uptime-sla"></a>アップタイム SLA を使用した新しいクラスターの作成

アップタイム SLA を使用して新しいクラスターを作成するには、Azure CLI を使用します。

次の例では、*myResourceGroup* という名前のリソース グループを *eastus* に作成します。

```azurecli-interactive
# Create a resource group
az group create --name myResourceGroup --location eastus
```

AKS クラスターを作成するには、[`az aks create`][az-aks-create] コマンドを使用します。 次の例では、*myAKSCluster* という名前のクラスターを 1 つのノードで作成します。 この操作は、完了するまでに数分かかります。

```azurecli-interactive
# Create an AKS cluster with uptime SLA
az aks create --resource-group myResourceGroup --name myAKSCluster --uptime-sla --node-count 1
```

数分後、コマンドが完了し、クラスターに関する情報が JSON 形式で返されます。 次の JSON スニペットは SKU の有料レベルを示し、クラスターでアップタイム SLA が有効になっていることを示します。

```output
  },
  "sku": {
    "name": "Basic",
    "tier": "Paid"
  },
```

## <a name="modify-an-existing-cluster-to-use-uptime-sla"></a>既存のクラスターを変更してアップタイム SLA を使用する

必要に応じて、既存のクラスターを更新してアップタイム SLA を使用することもできます。

前の手順で AKS クラスターを作成した場合は、リソース グループを削除します。

```azurecli-interactive
# Delete the existing cluster by deleting the resource group 
az group delete --name myResourceGroup --yes --no-wait
```

新しいリソース グループを作成します。

```azurecli-interactive
# Create a resource group
az group create --name myResourceGroup --location eastus
```

新しいクラスターを作成します。アップタイム SLA は使用しません。

```azurecli-interactive
# Create a new cluster without uptime SLA
az aks create --resource-group myResourceGroup --name myAKSCluster--node-count 1
```

[`az aks update`][az-aks-update] コマンドを使用して、既存のクラスターを更新します。

```azurecli-interactive
# Update an existing cluster to use Uptime SLA
az aks update --resource-group myResourceGroup --name myAKSCluster --uptime-sla
```

次の JSON スニペットは SKU の有料レベルを示し、クラスターでアップタイム SLA が有効になっていることを示します。

```output
  },
  "sku": {
    "name": "Basic",
    "tier": "Paid"
  },
```

## <a name="opt-out-of-uptime-sla"></a>アップタイム SLA のオプト アウト

クラスターを更新して Free レベルに変更し、アップタイム SLA からオプト アウトできます。

```azurecli-interactive
# Update an existing cluster to opt out of Uptime SLA
 az aks update --resource-group myResourceGroup --name myAKSCluster --no-uptime-sla
```

## <a name="clean-up"></a>クリーンアップ

課金されないようにするために、作成したすべてのリソースをクリーンアップします。 クラスターを削除するには、[`az group delete`][az-group-delete] コマンドを使用して AKS リソース グループを削除します。

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>次のステップ

[Availability Zones][availability-zones] を使用し、AKS クラスター ワークロードで高可用性を上げます。

[エグレス トラフィックを制限する](limit-egress-traffic.md)ようにクラスターを構成します。

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service

<!-- LINKS - Internal -->
[vm-skus]: ../virtual-machines/sizes.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[faq]: ./faq.md
[availability-zones]: ./availability-zones.md
[az-aks-create]: /cli/azure/aks?#az_aks_create
[limit-egress-traffic]: ./limit-egress-traffic.md
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-aks-update]: /cli/azure/aks#az_aks_update
[az-group-delete]: /cli/azure/group#az_group_delete
[private-clusters]: private-clusters.md
