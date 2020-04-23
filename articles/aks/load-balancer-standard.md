---
title: Standard SKU ロード バランサーの使用
titleSuffix: Azure Kubernetes Service
description: Standard SKU でロード バランサーを使用して、Azure Kubernetes Service (AKS) でサービスを公開する方法について説明します。
services: container-service
author: zr-msft
ms.topic: article
ms.date: 09/27/2019
ms.author: zarhoads
ms.openlocfilehash: c1d2c0e48394fbde1b595ae4b405d84f437dc5e4
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81392813"
---
# <a name="use-a-standard-sku-load-balancer-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) で Standard SKU ロード バランサーを使用する

Azure Kubernetes Service (AKS) で `LoadBalancer` 型の Kubernetes サービス経由でのアプリケーションへのアクセスを提供するために、Azure Load Balancer を使用できます。 AKS で実行されるロード バランサーは、内部または外部ロード バランサーとして使用できます。 内部ロード バランサーは、AKS クラスターと同じ仮想ネットワークで実行されているアプリケーションに対してのみ Kubernetes サービスへのアクセスを可能にします。 外部ロード バランサーは、1 つ以上のイングレス用のパブリック IP を受け取り、そのパブリック IP を使用して外部から Kubernetes サービスにアクセスできるようにします。

Azure Load Balancer は、*Basic* と *Standard* の 2 つの SKU で使用できます。 AKS クラスターを作成する場合、既定では *Standard* SKU が使用されます。 *Standard* SKU ロード バランサーを使用すると、より大きいバックエンド プール サイズ、Availability Zones など、追加のフィーチャーや機能が提供されます。 使用するロード バランサーを選択する前に、*Standard* と *Basic* ロード バランサーの違いを理解しておくことが重要です。 AKS クラスターを作成した後は、そのクラスターのロード バランサー SKU は変更できません。 *Basic* と *Standard* SKU の詳細については、「[Azure Load Balancer の SKU の比較][azure-lb-comparison]」をご覧ください。

この記事は、Kubernetes および Azure Load Balancer の基本的な概念を理解していることを前提としています。 詳細については、「[Azure Kubernetes Services (AKS) における Kubernetes の中心概念][kubernetes-concepts]」と「[Azure Load Balancer の概要][azure-lb]」を参照してください。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合、この記事では、Azure CLI バージョン 2.0.81 以降を実行していることが要件となります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール][install-azure-cli]に関するページを参照してください。

## <a name="before-you-begin"></a>開始する前に

この記事では、*Standard* SKU Azure Load Balancer を持つ AKS クラスターがあることを前提としています。 AKS クラスターが必要な場合は、[Azure CLI を使用した場合][aks-quickstart-cli]または [Azure portal を使用した場合][aks-quickstart-portal]の AKS のクイックスタートを参照してください。

既存のサブネットまたはリソース グループを使用する場合、AKS クラスターのサービス プリンシパルにはネットワーク リソースを管理するアクセス許可も必要です。 一般に、委任されたリソースのサービス プリンシパルには*ネットワーク共同作成者*ロールを割り当てます。 サービス プリンシパルの代わりに、システム割り当てのマネージド ID もアクセス許可に使用できます。 詳細については、[マネージド ID の使用](use-managed-identity.md)に関するページを参照してください。 アクセス許可の詳細については、[他の Azure リソースへの AKS アクセスの委任][aks-sp]に関する記事を参照してください。

### <a name="moving-from-a-basic-sku-load-balancer-to-standard-sku"></a>Basic SKU から Standard SKU にロード バランサーを移行する

既存のクラスターに Basic SKU ロード バランサーが与えられている場合、Standard SKU ロード バランサーでクラスターを使用する目的で移行するとき、動作に重大な違いがあることにご留意ください。

たとえば、`load-balancer-sku` 型のクラスターをクラスターの作成時にのみ定義できる場合、一般的に blue/green のデプロイでクラスターを移行します。 しかしながら、*Basic SKU* ロード バランサーでは、*Standard SKU* IP アドレスが必須の *Standard SKU* ロード バランサーとの間に互換性がない *Basic SKU* IP アドレスが使用されます。 クラスターを移行してロード バランサー SKU をアップグレードするとき、IP アドレス SKU に互換性がある新しい IP アドレスが必須となります。

クラスターの移行方法に関して他に注意すべき事項については、[移行時の考慮事項に関するドキュメント](aks-migration.md)を参照してください。移行時に考慮すべき重要なトピックが一覧表示されています。 以下の制限事項も、AKS で Standard SKU ロード バランサーを使用するときに注意すべき、動作上の重要な違いです。

### <a name="limitations"></a>制限事項

*Standard* SKU を使用するロード バランサーをサポートする AKS クラスターを作成し、管理する場合、次の制限が適用されます。

* AKS クラスターからのエグレス トラフィックを許可するために、パブリック IP または IP プレフィックスが少なくとも 1 つ必要です。 パブリック IP または IP プレフィックスは、コントロール プレーンとエージェント ノードの間の接続を維持するためと、前のバージョンの AKS との互換性を維持するためにも必要です。 *Standard* SKU ロード バランサーでパブリック IP または IP プレフィックスを指定するための次のオプションがあります:
    * 独自のパブリック IP を指定する。
    * 独自のパブリック IP プレフィックスを指定する。
    * 最大 100 までの数字を指定し、AKS クラスターとして作成された同じリソース グループでそれだけの数の *Standard* SKU パブリック IP を作成することを AKS クラスターに許可します。名前は通常、*MC_* で始まります。 AKS により、パブリック IP が *Standard* SKU ロード バランサーに割り当てられます。 既定では、パブリック IP、パブリック IP プレフィックス、または IP の数が指定されていない場合、同じリソース グループでパブリック IP が 1 つ自動的に作成されます。 また、パブリック アドレスを許可する必要があり、IP 作成を禁止する Azure Policy は作成しないようにする必要があります。
* ロード バランサーに対して *Standard* SKU を使用する場合、Kubernetes バージョン *1.13 以降*を使用する必要があります。
* ロード バランサー SKU は、AKS クラスターの作成時にのみ定義できます。 AKS クラスターが作成された後にロード バランサー SKU を変更することはできません。
* 1 つのクラスターで使用できるロード バランサー SKU (Basic または Standard) の種類は 1 つのみです。
* *Standard* SKU ロード バランサーでは、*Standard* SKU IP アドレスのみがサポートされています。

## <a name="use-the-standard-sku-load-balancer"></a>*Standard* SKU のロード バランサーを使用する

AKS クラスターを作成すると、既定では、そのクラスターでサービスを実行するときに、*Standard* SKU ロード バランサーが使用されます。 たとえば、[Azure CLI を使用したクイックスタート][aks-quickstart-cli]では、*Standard* SKU ロード バランサーを使用してサンプル アプリケーションをデプロイしています。

> [!IMPORTANT]
> パブリック IP アドレスを回避するには、ユーザー定義ルート (UDR) をカスタマイズします。 AKS クラスターの送信の種類を UDR に指定すると、AKS で作成された Azure ロード バランサーの IP プロビジョニングとバックエンド プールの設定をスキップできます。 [クラスターの `outboundType` を 'userDefinedRouting' に設定する](egress-outboundtype.md)方法に関する記事を参照してください。

## <a name="configure-the-load-balancer-to-be-internal"></a>ロード バランサーを内部として構成する

また、ロード バランサーを内部にして、パブリック IP を公開しないように構成することもできます。 ロード バランサーを内部として構成するには、*LoadBalancer* サービスにアノテーションとして `service.beta.kubernetes.io/azure-load-balancer-internal: "true"` を追加します。 サンプルの yaml マニフェストと内部ロード バランサーの詳細について、[こちら][internal-lb-yaml]で確認できます。

## <a name="scale-the-number-of-managed-public-ips"></a>管理対象のパブリック IP の数をスケーリングする

既定で作成される管理対象の送信パブリック IP と共に *Standard* SKU ロード バランサーを使用するとき、*load-balancer-managed-ip-count* パラメーターを使用し、管理対象の送信パブリック IP の数を拡大縮小できます。

既存のクラスターを更新するには、次のコマンドを実行します。 このパラメーターをクラスター作成時に設定し、複数の管理対象送信パブリック IP を与えることもできます。

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-managed-outbound-ip-count 2
```

上の例では、*myResourceGroup* の *myAKSCluster* クラスターに対して、管理対象の送信パブリック IP の数が *2* に設定されます。 

また、*load-balancer-managed-ip-count* パラメーターを使用して、`--load-balancer-managed-outbound-ip-count` パラメーターを追加し、それを希望する値に設定するときに、クラスターの作成時に管理対象の送信パブリック IP の初期数を設定することもできます。 管理対象の送信パブリック IP の既定数は 1 です。

## <a name="provide-your-own-public-ips-or-prefixes-for-egress"></a>エグレスのために独自のパブリック IP またはプレフィックスを指定する

*Standard* SKU ロード バランサーの使用時、AKS クラスターによって、AKS クラスターに対して作成された同じリソース グループでパブリック IP が自動的に作成され、そのパブリック IP が *Standard* SKU ロード バランサーに割り当てられます。 あるいは、クラスター作成時に独自のパブリック IP を割り当てたり、既存のクラスターのロード バランサー プロパティを更新したりできます。

複数の IP アドレスまたはプレフィックスを導入することで、1 つのロード バランサー オブジェクトの背後で IP アドレスを定義するとき、複数のバックアップ サービスを定義できます。 特定のノードのエグレス エンドポイントは、そのノードが関連付けられているサービスに依存します。

> [!IMPORTANT]
> *Standard* SKU ロード バランサーと共に、エグレス用の *Standard* SKU パブリック IP を使用する必要があります。 [az network public-ip show][az-network-public-ip-show] コマンドを使用することで、パブリック IP の SKU を検証できます。
>
> ```azurecli-interactive
> az network public-ip show --resource-group myResourceGroup --name myPublicIP --query sku.name -o tsv
> ```

[az network public-ip show][az-network-public-ip-show] コマンドを使用すると、パブリック IP の ID が一覧表示されます。

```azurecli-interactive
az network public-ip show --resource-group myResourceGroup --name myPublicIP --query id -o tsv
```

上のコマンドでは、*myResourceGroup* リソース グループの *myPublicIP* パブリック IP の IP が表示されます。

*load-balancer-outbound-ips* パラメーターを指定して *az aks update* コマンドを使用すると、パブリック IP でクラスターが更新されます。

次の例では、*load-balancer-outbound-ips* パラメーターと共に前のコマンドからの ID が使用されます。

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2>
```

*Standard* SKU ロード バランサーと共に、エグレス用のパブリック IP プレフィックスを使用することもできます。 次の例では、[az network public-ip prefix show][az-network-public-ip-prefix-show] コマンドを使用し、パブリック IP プレフィックスの IP が一覧表示されます。

```azurecli-interactive
az network public-ip prefix show --resource-group myResourceGroup --name myPublicIPPrefix --query id -o tsv
```

上のコマンドでは、*myResourceGroup* リソース グループの *myPublicIPPrefix* パブリック IP プレフィックスの IP が表示されます。

次の例では、*load-balancer-outbound-ip-prefixes* パラメーターと前のコマンドからの ID が使用されています。

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ip-prefixes <publicIpPrefixId1>,<publicIpPrefixId2>
```

> [!IMPORTANT]
> パブリック IP と IP プレフィックスは、AKS クラスターと同じリージョンにあり、同じサブスクリプションに含まれている必要があります。 

### <a name="define-your-own-public-ip-or-prefixes-at-cluster-create-time"></a>クラスター作成時に独自のパブリック IP またはプレフィックスを定義する

エグレス エンドポイントをホワイトリストに記載するといったシナリオを支援する目的で、クラスター作成時、エグレス用に独自の IP アドレスや IP プレフィックスを導入することがあります。 クラスター作成手順に上のコードと同じパラメーターを追加し、クラスターのライフサイクルの開始時に独自のパブリック IP と IP プレフィックスを定義します。

*load-balancer-outbound-ips* パラメーターを指定して *az aks create* コマンドを使用し、開始時に自分のパブリック IP で新しいクラスターを作成します。

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --vm-set-type VirtualMachineScaleSets \
    --node-count 1 \
    --load-balancer-sku standard \
    --generate-ssh-keys \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2>
```

*load-balancer-outbound-ip-prefixes* パラメーターを指定して *az aks create* コマンドを使用し、開始時に自分のパブリック IP プレフィックスで新しいクラスターを作成します。

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --vm-set-type VirtualMachineScaleSets \
    --node-count 1 \
    --load-balancer-sku standard \
    --generate-ssh-keys \
    --load-balancer-outbound-ip-prefixes <publicIpPrefixId1>,<publicIpPrefixId2>
```

## <a name="configure-outbound-ports-and-idle-timeout"></a>送信ポートとアイドル タイムアウトを構成する

> [!WARNING]
> 次のセクションは、大規模なネットワークの高度なシナリオや、既定の構成での SNAT 不足の問題を対象としています。 正常なクラスターを維持するには、*AllocatedOutboundPorts* または *IdleTimeoutInMinutes* を既定値から変更する前に、VM と IP アドレスの使用可能なクォータの正確なインベントリが必要です。
> 
> *AllocatedOutboundPorts* や *IdleTimeoutInMinutes* の値を変更すると、ロード バランサーに対するアウトバウンド規則の動作が大きく変化する可能性があります。 これらの値を更新する前に、「[Load Balancer のアウトバウンド規則][azure-lb-outbound-rules-overview]」、「[Load Balancer のアウトバウンド規則][azure-lb-outbound-rules]」、および「[Azure の Outbound connections][azure-lb-outbound-connections]」を確認し、変更の影響を完全に理解してください。

送信用に割り当てられたポートとアイドル タイムアウトは、[SNAT][azure-lb-outbound-connections] に使用されます。 既定の *Standard* SKU ロード バランサーでは、[送信ポートの数はバックエンドのプール サイズに基づいて自動的に割り当て][azure-lb-outbound-preallocatedports]られ、各ポートのアイドル タイムアウトは 30 分に設定されます。 これらの値を確認するには、[az network lb outbound-rule list][az-network-lb-outbound-rule-list] を使用して、ロード バランサーのアウトバウンド規則を表示します。

```azurecli-interactive
NODE_RG=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
az network lb outbound-rule list --resource-group $NODE_RG --lb-name kubernetes -o table
```

上記のコマンドを実行すると、次の例に示すように、ロード バランサーのアウトバウンド規則が一覧表示されます。

```console
AllocatedOutboundPorts    EnableTcpReset    IdleTimeoutInMinutes    Name             Protocol    ProvisioningState    ResourceGroup
------------------------  ----------------  ----------------------  ---------------  ----------  -------------------  -------------
0                         True              30                      aksOutboundRule  All         Succeeded            MC_myResourceGroup_myAKSCluster_eastus  
```

出力例では、*AllocatedOutboundPorts* と *IdleTimeoutInMinutes* の既定値が示されています。 *AllocatedOutboundPorts* の値が 0 の場合、送信ポートの数はバックエンドのプール サイズに基づいて自動的に割り当てられます。 たとえば、クラスターのノード数が 50 以下の場合、各ノードには 1,024 個のポートが割り当てられます。

上記の既定の構成では SNAT が不足すると思われる場合は、*allocatedOutboundPorts* または *IdleTimeoutInMinutes* の設定を変更することを検討してください。 IP アドレスを追加するごとに 64,000 個のポートをさらに割り当てることができますが、Azure Standard Load Balancer では、IP アドレスを追加しても、ノードあたりのポート数が自動的に増えることはありません。 これらの値は、*load-balancer-outbound-ports* および *load-balancer-idle-timeout* パラメーターを設定することによって変更できます。 次に例を示します。

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ports 0 \
    --load-balancer-idle-timeout 30
```

> [!IMPORTANT]
> 接続またはスケーリングの問題を回避するために *allocatedOutboundPorts* をカスタマイズする前に、[必要なクォータを計算する][calculate-required-quota]必要があります。 *allocatedOutboundPorts* に指定する値は、8 の倍数である必要もあります。

クラスターを作成するときに *load-balancer-outbound-ports* および *load-balancer-idle-timeout* パラメーターを使用することもできますが、*load-balancer-managed-outbound-ip-count*、*load-balancer-outbound-ips*、または *load-balancer-outbound-ip-prefixes* のいずれかも指定する必要があります。  次に例を示します。

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --vm-set-type VirtualMachineScaleSets \
    --node-count 1 \
    --load-balancer-sku standard \
    --generate-ssh-keys \
    --load-balancer-managed-outbound-ip-count 2 \
    --load-balancer-outbound-ports 0 \
    --load-balancer-idle-timeout 30
```

*load-balancer-outbound-ports* および *load-balancer-idle-timeout* パラメーターをその既定値から変更すると、ロード バランサー プロファイルの動作に影響するので、クラスター全体が影響を受けます。

### <a name="required-quota-for-customizing-allocatedoutboundports"></a>allocatedOutboundPorts をカスタマイズするために必要なクォータ
ノードの VM の数と、割り当てられる必要がある送信ポートの数に基づいて、十分な送信 IP 容量が必要です。 送信 IP 容量が十分であることを確認するには、次の式を使用します。 
 
*outboundIPs* \* 64,000 \> *nodeVMs* \* *desiredAllocatedOutboundPorts*
 
たとえば、*nodeVMs* が 3 で、*desiredAllocatedOutboundPorts* が 50,000 の場合、必要な *outboundIPs* は 3 以上です。 必要最低限の量より多くの送信 IP 容量を組み込むことをお勧めします。 また、送信 IP 容量を計算するときは、クラスター オートスケーラーと、ノード プールのアップグレードの可能性を考慮する必要があります。 クラスター オートスケーラーについては、現在のノード数と最大ノード数を確認し、高い方の値を使用します。 アップグレードについては、アップグレードが可能なすべてのノード プールに対して追加のノード VM を考慮します。
 
*IdleTimeoutInMinutes* を既定の 30 分とは異なる値に設定する場合は、ワークロードで送信接続が必要な時間を考慮します。 また、AKS の外部で使用される *Standard* SKU ロード バランサーの既定のタイムアウト値が 4 分であることを考慮します。 特定の AKS ワークロードをより正確に反映するように *IdleTimeoutInMinutes* の値を設定すると、使用されなくなった接続と関連付けられることによる SNAT の枯渇を減らすのに役立ちます。

## <a name="restrict-access-to-specific-ip-ranges"></a>特定の IP 範囲へのアクセスを制限

既定では、ロード バランサーの仮想ネットワークに関連付けられているネットワーク セキュリティ グループ (NSG) には、すべての受信外部トラフィックを許可する規則があります。 この規則を更新して、受信トラフィックに特定の IP 範囲のみを許可することができます。 次のマニフェストでは *loadBalancerSourceRanges* を使用して、受信外部トラフィックの新しい IP 範囲を指定します。

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
  loadBalancerSourceRanges:
  - MY_EXTERNAL_IP_RANGE
```

上の例では、*MY_EXTERNAL_IP_RANGE* 範囲からの外部トラフィックのみを許可するように規則を更新します。 この方法を使用してロード バランサー サービスへのアクセスを制限する方法の詳細については、[Kubernetes のドキュメント][kubernetes-cloud-provider-firewall]を参照してください。

## <a name="next-steps"></a>次のステップ

[Kubernetes サービスのドキュメント][kubernetes-services]で Kubernetes サービスについて学習する。

<!-- LINKS - External -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubernetes-cloud-provider-firewall]: https://kubernetes.io/docs/tasks/access-application-cluster/configure-cloud-provider-firewall/#restrict-access-for-loadbalancer-service
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/
[aks-engine]: https://github.com/Azure/aks-engine

<!-- LINKS - Internal -->
[advanced-networking]: configure-azure-cni.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-sp]: kubernetes-service-principal.md#delegate-access-to-other-azure-resources
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-aks-install-cli]: /cli/azure/aks?view=azure-cli-latest#az-aks-install-cli
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-group-create]: /cli/azure/group#az-group-create
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-network-lb-outbound-rule-list]: /cli/azure/network/lb/outbound-rule?view=azure-cli-latest#az-network-lb-outbound-rule-list
[az-network-public-ip-show]: /cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-show
[az-network-public-ip-prefix-show]: /cli/azure/network/public-ip/prefix?view=azure-cli-latest#az-network-public-ip-prefix-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[azure-lb]: ../load-balancer/load-balancer-overview.md
[azure-lb-comparison]: ../load-balancer/concepts-limitations.md#skus
[azure-lb-outbound-rules]: ../load-balancer/load-balancer-outbound-rules-overview.md#snatports
[azure-lb-outbound-connections]: ../load-balancer/load-balancer-outbound-connections.md#snat
[azure-lb-outbound-preallocatedports]: ../load-balancer/load-balancer-outbound-connections.md#preallocatedports
[azure-lb-outbound-rules-overview]: ../load-balancer/load-balancer-outbound-rules-overview.md
[install-azure-cli]: /cli/azure/install-azure-cli
[internal-lb-yaml]: internal-lb.md#create-an-internal-load-balancer
[kubernetes-concepts]: concepts-clusters-workloads.md
[use-kubenet]: configure-kubenet.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[calculate-required-quota]: #required-quota-for-customizing-allocatedoutboundports
