---
title: Azure Kubernetes Service (AKS) での API サーバーの許可された IP 範囲
description: Azure Kubernetes Service (AKS) で API サーバーへのアクセスのための IP アドレス範囲を使用してクラスターをセキュリティで保護する方法について説明します
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/06/2019
ms.author: mlearned
ms.openlocfilehash: 8418499cc3e094162ac7483aaa6c71e74db95ae1
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2019
ms.locfileid: "73472965"
---
# <a name="secure-access-to-the-api-server-using-authorized-ip-address-ranges-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) で許可された IP アドレス範囲を使用して API サーバーへのアクセスをセキュリティで保護する

Kubernetes では、API サーバーは、リソースの作成やノードの数のスケーリングなどのクラスター内のアクションを実行するための要求を受信します。 API サーバーは、クラスターを操作したり管理したりするための中心的な方法です。 クラスターのセキュリティを向上させ、攻撃を最小限に抑えるには、API サーバーを限られた一連の IP アドレス範囲からのみアクセス可能にする必要があります。

この記事では、API サーバーで許可された IP アドレス範囲を使用して、コントロール プレーンにアクセスできる IP アドレスと CIDR を制限する方法を示します。

> [!IMPORTANT]
> 新しいクラスターでは、API サーバーで許可された IP アドレス範囲は *Standard* SKU ロード バランサーでのみサポートされます。 *Basic* SKU ロード バランサーと API サーバーで許可された IP アドレス範囲が構成された既存のクラスターは、引き続き機能します。 それらの既存のクラスターもアップグレードできるため、引き続き機能します。

## <a name="before-you-begin"></a>開始する前に

この記事では、[kubenet][kubenet] を使用しているクラスターで作業することを前提としています。  [Azure Container Networking Interface (CNI)][cni-networking] ベースのクラスターでは、アクセスをセキュリティで保護するために必要なルート テーブルがありません。  ルート テーブルを手動で作成する必要があります。  詳細については、[ルート テーブルの管理](https://docs.microsoft.com/azure/virtual-network/manage-route-table)に関するページをご覧ください。

API サーバーの許可された IP 範囲は、作成する新しい AKS クラスターに対してのみ機能します。 この記事では、Azure CLI を使用して AKS クラスターを作成する方法について説明します。

Azure CLI バージョン 2.0.76 以降がインストールされて構成されている必要があります。 バージョンを確認するには、 `az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「 [Azure CLI のインストール][install-azure-cli]」を参照してください。

## <a name="limitations"></a>制限事項

API サーバーの許可された IP 範囲を構成する場合は、次の制限が適用されます。

* API サーバーとの通信もブロックされるため、現在は Azure Dev Spaces を使用できません。

## <a name="overview-of-api-server-authorized-ip-ranges"></a>API サーバーの許可された IP 範囲の概要

Kubernetes API サーバーは、基になる Kubernetes API が公開される方法です。 このコンポーネントは、`kubectl` や Kubernetes ダッシュボードなど、管理ツールに対する操作を提供します。 AKS は、専用の API サーバーを含むシングルテナント クラスター マスターを提供します。 既定では、API サーバーにはパブリック IP アドレスが割り当てられるため、ロールベースのアクセス制御 (RBAC) を使用してアクセスを制御する必要があります。

通常はパブリックにアクセス可能な AKS コントロール プレーン/API サーバーへのアクセスをセキュリティで保護するには、許可された IP 範囲を有効にして使用することができます。 これらの許可された IP 範囲では、API サーバーと通信するための定義された IP アドレス範囲のみが許可されます。 これらの許可された IP 範囲に含まれていない IP アドレスから API サーバーへの要求はブロックされます。 その後、ユーザーやそのユーザーが要求するアクションを承認するには、引き続き RBAC を使用する必要があります。

API サーバーやその他のクラスター コンポーネントの詳細については、[AKS における Kubernetes の中心概念][concepts-clusters-workloads]に関するページを参照してください。

## <a name="create-an-aks-cluster"></a>AKS クラスターの作成

API サーバーの許可された IP 範囲は、新しい AKS クラスターに対してのみ機能します。 許可された IP 範囲をクラスター作成操作の一部として有効にすることはできません。 許可された IP 範囲をクラスター作成プロセスの一部として有効にしようとすると、その時点ではエグレス IP アドレスが定義されていないため、クラスター ノードはデプロイ中に API サーバーにアクセスできません。

最初に、[az aks create][az-aks-create] コマンドを使用してクラスターを作成します。 次の例では、*myResourceGroup* という名前のリソース グループ内に *myAKSCluster* という名前の単一ノード クラスターを作成します。

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location eastus

# Create an AKS cluster
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --generate-ssh-keys

# Get credentials to access the cluster
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="update-cluster-with-authorized-ip-ranges"></a>許可された IP 範囲を使用してクラスターを更新する

既定で、クラスターには [Standard SKU ロード バランサー][standard-sku-lb]が使用されます。これを使用して、送信ゲートウェイを構成できます。 [az network public-ip list][az-network-public-ip-list] を使用して、AKS クラスターのリソース グループを指定します。このリソース グループは、通常、*MC_* で始まります。 これにより、クラスターのパブリック IP が表示され、それを許可することができます。 [az aks update][az-aks-update] コマンドを使用し、 *--api-server-authorized-ip-ranges* パラメーターを指定して、クラスターの IP を許可します。 例:

```azurecli-interactive
RG=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
SLB_PublicIP=$(az network public-ip list --resource-group $RG --query [].ipAddress -o tsv)
az aks update --api-server-authorized-ip-ranges $SLB_PublicIP --resource-group myResourceGroup --name myAKSCluster
```

API サーバーの許可された IP 範囲を有効にするには、[az aks update][az-aks-update] コマンドを使用し、 *-api-server-authorized-ip-ranges* パラメーターを指定し、許可された IP アドレス範囲のリストを指定します。 これらの IP アドレス範囲は通常、オンプレミス ネットワークまたはパブリック IP によって使用されるアドレス範囲です。 CIDR 範囲を指定する場合は、その範囲内の最初の IP アドレスから始めます。 たとえば、*137.117.106.90/29* は有効な範囲ですが、範囲内の最初の IP アドレス (*137.117.106.88/29* など) を指定するようにしてください。

次の例では、*myResourceGroup* という名前のリソース グループ内の *myAKSCluster* という名前のクラスターで API サーバーの許可された IP 範囲を有効にします。 許可する IP アドレスの範囲は *172.0.0.0/16* (ポッド/ノードのアドレス範囲) と *168.10.0.0/18* (ServiceCidr) です。

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges 172.0.0.0/16,168.10.0.0/18
```

> [!NOTE]
> 次の範囲を許可リストに追加してください。
> - ファイアウォール パブリック IP アドレス
> - サービス CIDR
> - サブネットのアドレス範囲、ノード、ポッド
> - クラスターを管理するネットワークを表すあらゆる範囲

## <a name="disable-authorized-ip-ranges"></a>許可された IP 範囲を無効にする

許可された IP 範囲を無効にするには、[az aks update][az-aks-update] を使用し、空の範囲を指定して、API サーバーの許可された IP 範囲を無効にします。 例:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges ""
```

## <a name="next-steps"></a>次の手順

この記事では、API サーバーの許可された IP 範囲を有効にしました。 このアプローチは、セキュリティで保護された AKS クラスターをどのように実行できるかの一部です。

詳細については、[AKS でのアプリケーションとクラスターのセキュリティの概念][concepts-security]に関するページおよび [AKS でのクラスターのセキュリティとアップグレードのベスト プラクティス][operator-best-practices-cluster-security]に関するページを参照してください。

<!-- LINKS - external -->
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/extend-kubernetes/compute-storage-net/network-plugins/#kubenet

<!-- LINKS - internal -->
[az-aks-update]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-update
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-network-public-ip-list]: /cli/azure/network/public-ip#az-network-public-ip-list
[concepts-clusters-workloads]: concepts-clusters-workloads.md
[concepts-security]: concepts-security.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-cluster-security]: operator-best-practices-cluster-security.md
[standard-sku-lb]: load-balancer-standard.md
