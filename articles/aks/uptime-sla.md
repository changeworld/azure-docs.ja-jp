---
title: Azure Kubernetes Service (AKS) とアップタイム SLA
description: Azure Kubernetes Service (AKS) API サーバーのオプションのアップタイム SLA オファリングについて説明します。
services: container-service
ms.topic: conceptual
ms.date: 05/19/2020
ms.custom: references_regions
ms.openlocfilehash: 2df0ad675f03b25363ab0f5b13dceb762a657ed7
ms.sourcegitcommit: d118ad4fb2b66c759b70d4d8a18e6368760da3ad
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/02/2020
ms.locfileid: "84299555"
---
# <a name="azure-kubernetes-service-aks-uptime-sla"></a>Azure Kubernetes Service (AKS) のアップタイム SLA

アップタイム SLA は、クラスターに対して利用料金に基づく高い SLA を実現するためのオプションの機能です。 アップタイム SLA は、[可用性ゾーン][availability-zones]を使用するクラスターに対しては Kubernetes API サーバー エンドポイントの 99.95% の可用性を保証し、可用性ゾーンを使用しないクラスターに対しては 99.9% の可用性を保証します。 AKS は、更新ドメインおよび障害ドメイン全体でマスター ノード レプリカを使用して、SLA 要件が満たされるようにします。

コンプライアンス要件を満たす SLA が必要なお客様や、SLA をエンドユーザーに拡張する必要があるお客様は、この機能を有効にする必要があります。 アップタイム SLA の向上によるメリットが得られる重要なワークロードを持つお客様にもメリットがあります。 可用性ゾーンでアップタイム SLA 機能を使用すると、Kubernetes API サーバーのアップタイムの可用性を高めることができます。  

お客様は今まで通り、サービス レベル目標 (SLO) が 99.5% の無制限の無料クラスターを作成することができ、必要に応じて、優先の SLO または SLA アップタイムを選択できます。

> [!Important]
> エグレス ロックダウンを使用するクラスターの場合、[エグレス トラフィックの制限](limit-egress-traffic.md)に関するページを参照して、適切なポートを開きます。

## <a name="sla-terms-and-conditions"></a>SLA の使用条件

アップタイム SLA は有料の機能であり、クラスターごとに有効化されます。 アップタイム SLA の価格は、個々のクラスターのサイズではなく、個別のクラスターの数によって決まります。 詳細については、[アップタイム SLA の価格の詳細](https://azure.microsoft.com/pricing/details/kubernetes-service/)に関するページを参照してください。

## <a name="before-you-begin"></a>開始する前に

* Azure CLI バージョン 2.7.0 以降

## <a name="creating-a-cluster-with-uptime-sla"></a>アップタイム SLA を使用したクラスターの作成

アップタイム SLA を使用して新しいクラスターを作成するには、Azure CLI を使用します。

次の例では、*myResourceGroup* という名前のリソース グループを *eastus* に作成します。

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```
AKS クラスターを作成するには、[az aks create][az-aks-create] コマンドを使用します。 次の例では、*myAKSCluster* という名前のクラスターを 1 つのノードで作成します。 コンテナーの Azure Monitor は、 *--enable-addons monitoring* パラメーターを使用して有効にすることもできます。  この操作は、完了するまでに数分かかります。

```azurecli-interactive
az aks create --resource-group myResourceGroup --name myAKSCluster --uptime-sla --node-count 1 --enable-addons monitoring --generate-ssh-keys
```
数分後、コマンドが完了し、クラスターに関する情報が JSON 形式で返されます。 次の JSON スニペットは SKU の有料レベルを示し、クラスターでアップタイム SLA が有効になっていることを示します。

```output
  },
  "sku": {
    "name": "Basic",
    "tier": "Paid"
  },
```

## <a name="limitations"></a>制限事項

* 現時点では、アップタイム SLA を有効にするために既存のクラスターとして変換することはできません。
* 現時点では、アップタイム SLA を有効にして作成した後で AKS クラスターからアップタイム SLA を解除する方法はありません。  
* プライベート クラスターは現時点ではサポートされていません。

## <a name="next-steps"></a>次のステップ

[Availability Zones][availability-zones] を使用し、AKS クラスター ワークロードで高可用性を上げます。
[エグレス トラフィックを制限する](limit-egress-traffic.md)ようにクラスターを構成します。

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service

<!-- LINKS - Internal -->
[vm-skus]: ../virtual-machines/linux/sizes.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[faq]: ./faq.md
[availability-zones]: ./availability-zones.md
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[limit-egress-traffic]: ./limit-egress-traffic.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
