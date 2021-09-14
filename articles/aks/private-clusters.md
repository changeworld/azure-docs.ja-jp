---
title: プライベート Azure Kubernetes Service クラスターを作成する
description: プライベート Azure Kubernetes Service (AKS) クラスターの作成方法について説明します
services: container-service
ms.topic: article
ms.date: 8/30/2021
ms.openlocfilehash: 69366f82c746d1d436d437e2892b010331ecf967
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/03/2021
ms.locfileid: "123429030"
---
# <a name="create-a-private-azure-kubernetes-service-cluster"></a>プライベート Azure Kubernetes Service クラスターを作成する

プライベート クラスターでは、コントロール プレーンまたは API サーバーに、「[RFC1918 - Address Allocation for Private Internet](https://tools.ietf.org/html/rfc1918)」 (RFC1918 - プライベート インターネットでのアドレス割り当て) で定義されている内部 IP アドレスがあります。 プライベート クラスターを使用することにより、API サーバーとノード プールの間のネットワーク トラフィックがプライベート ネットワークにのみ保持されるようにすることができます。

コントロールプレーンまたは API サーバーは、Azure Kubernetes Service (AKS) マネージドの Azure サブスクリプションの中にあります。 カスタマーのクラスターまたはノードプールは、カスタマーのサブスクリプションの中にあります。 サーバーとクラスターまたはノードプールは、API サーバー仮想ネットワーク内の [Azure Private Link サービス][private-link-service] と、カスタマーの AKS クラスターのサブネットで公開されているプライベート エンドポイントを介して相互に通信できます。

## <a name="region-availability"></a>利用可能なリージョン

プライベート クラスターは、[AKS がサポートされている](https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service)パブリック リージョン、Azure Government リージョン、および Azure China 21Vianet リージョンで使用できます。

> [!NOTE]
> Azure Government サイトはサポートされていますが、Private Link のサポートがないため、US Gov テキサスは現在サポートされていません。

## <a name="prerequisites"></a>前提条件

* Azure CLI バージョン 2.2.0 以降
* Private Link サービスは、Standard Azure Load Balancer でのみサポートされています。 Basic Azure Load Balancer はサポートされていません。  
* カスタム DNS サーバーを使用するには、カスタム DNS サーバーにアップストリーム DNS サーバーとして Azure DNS IP 168.63.129.16 を追加します。

## <a name="create-a-private-aks-cluster"></a>プライベート AKS クラスターを作成する

### <a name="create-a-resource-group"></a>リソース グループを作成する

リソース グループを作成するか、AKS クラスターの既存のリソース グループを使用します。

```azurecli-interactive
az group create -l westus -n MyResourceGroup
```

### <a name="default-basic-networking"></a>既定の基本ネットワーク 

```azurecli-interactive
az aks create -n <private-cluster-name> -g <private-cluster-resource-group> --load-balancer-sku standard --enable-private-cluster  
```
`--enable-private-cluster`は、プライベート クラスターに必須のフラグです 

### <a name="advanced-networking"></a>[高度] ネットワーク  

```azurecli-interactive
az aks create \
    --resource-group <private-cluster-resource-group> \
    --name <private-cluster-name> \
    --load-balancer-sku standard \
    --enable-private-cluster \
    --network-plugin azure \
    --vnet-subnet-id <subnet-id> \
    --docker-bridge-address 172.17.0.1/16 \
    --dns-service-ip 10.2.0.10 \
    --service-cidr 10.2.0.0/24 
```
`--enable-private-cluster`は、プライベート クラスターに必須のフラグです 

> [!NOTE]
> Docker ブリッジ アドレス CIDR (172.17.0.1/16) がサブネット CIDR と競合する場合は、Docker ブリッジ アドレスを適切に変更します。

## <a name="configure-private-dns-zone"></a>プライベート DNS ゾーンを構成する 

次のパラメーターを使用してプライベート DNS ゾーンを構成できます。

- "system"。既定値でもあります。 --private-dns-zone 引数を省略すると、AKS によって、ノード リソース グループにプライベート DNS ゾーンが作成されます。
- "none"。既定でパブリック DNS に設定されます。これは、AKS によってプライベート DNS ゾーンが作成されないことを意味します。  
- "CUSTOM_PRIVATE_DNS_ZONE_RESOURCE_ID"。Azure グローバル クラウド用に `privatelink.<region>.azmk8s.io` の形式でプライベート DNS ゾーンを作成する必要があります。 そのプライベート DNS ゾーンのリソース ID は、後で必要になります。  さらに、少なくとも `private dns zone contributor` および `vnet contributor` ロールを持つユーザー割り当て ID またはサービス プリンシパルも必要になります。
  - プライベート DNS ゾーンが AKS クラスターとは異なるサブスクリプションにある場合は、両方のサブスクリプションで Microsoft.ContainerServices を登録する必要があります。
  - "fqdn-subdomain" は、サブドメインの機能を `privatelink.<region>.azmk8s.io` に提供するためにのみ、"CUSTOM_PRIVATE_DNS_ZONE_RESOURCE_ID" と共に使用できます。

### <a name="create-a-private-aks-cluster-with-private-dns-zone"></a>プライベート DNS ゾーンがあるプライベート AKS クラスターを作成する

```azurecli-interactive
az aks create -n <private-cluster-name> -g <private-cluster-resource-group> --load-balancer-sku standard --enable-private-cluster --enable-managed-identity --assign-identity <ResourceId> --private-dns-zone [system|none]
```

### <a name="create-a-private-aks-cluster-with-a-custom-private-dns-zone"></a>カスタム プライベート DNS ゾーンがあるプライベート AKS クラスターを作成する

```azurecli-interactive
az aks create -n <private-cluster-name> -g <private-cluster-resource-group> --load-balancer-sku standard --enable-private-cluster --enable-managed-identity --assign-identity <ResourceId> --private-dns-zone <custom private dns zone ResourceId> --fqdn-subdomain <subdomain-name>
```

## <a name="create-a-private-aks-cluster-with-a-public-dns-address"></a>パブリック DNS アドレスがあるプライベート AKS クラスターを作成する

前提条件:

* aks-preview 拡張機能バージョン 0.5.29 以降を備えた Azure CLI。
* ARM または REST API を使用する場合、AKS API バージョンは 2021-05-01 以降である必要があります。

パブリック DNS オプションを利用すると、プライベート クラスターのルーティング オプションを簡略化できます。  

![パブリック DNS](https://user-images.githubusercontent.com/50749048/124776520-82629600-df0d-11eb-8f6b-71c473b6bd01.png)

1. プライベート AKS クラスターをプロビジョニングするときに `--enable-public-fqdn` を指定すると、AKS は、その FQDN の追加の A レコードを Azure パブリック DNS に作成します。 エージェント ノードにより引き続きプライベート DNS ゾーンの A レコードが使用され、API サーバーとの通信のためにプライベート エンドポイントのプライベート IP アドレスが解決されます。

2. `--enable-public-fqdn` と `--private-dns-zone none` の両方を使用した場合、クラスターはパブリック FQDN のみを持ちます。 このオプションを使用する場合、API サーバーの FQDN の名前解決にプライベート DNS ゾーンが作成または使用されることはありません。 API の IP は引き続きプライベートであり、パブリックにルーティングできません。

```azurecli-interactive
az aks create -n <private-cluster-name> -g <private-cluster-resource-group> --load-balancer-sku standard --enable-private-cluster --enable-managed-identity --assign-identity <ResourceId> --private-dns-zone <private-dns-zone-mode> --enable-public-fqdn
```

## <a name="options-for-connecting-to-the-private-cluster"></a>プライベート クラスターに接続するための選択肢

API サーバー エンドポイントには、パブリック IP アドレスがありません。 API サーバーを管理するには、AKS クラスターの Azure Virtual Network (VNet) にアクセスできる VM を使用する必要があります。 プライベート クラスターへのネットワーク接続を確立するには、いくつかの選択肢があります。

* AKS クラスターと同じ Azure Virtual Network (VNet) に VM を作成します。
* 別のネットワーク内の VM を使用し、[仮想ネットワーク ピアリング][virtual-network-peering]を設定します。  このオプションの詳細については、以下のセクションを参照してください。
* [Express Route または VPN][express-route-or-VPN] 接続を使用します。
* [AKS 実行コマンド機能](#aks-run-command-preview)を使用します。

AKS クラスターと同じ VNET に VM を作成するのが最も簡単な方法です。  Express Route と VPN にはコストがかかり、ネットワークがさらに複雑になります。  仮想ネットワーク ピアリングを利用する場合、重複する範囲がないようにネットワーク CIDR 範囲を計画する必要があります。

### <a name="aks-run-command-preview"></a>AKS 実行コマンド (プレビュー)

現在、プライベート クラスターにアクセスする必要がある場合は、クラスター仮想ネットワークまたはピアリングされたネットワークかクライアント コンピューターの中で行う必要があります。 この場合、通常はお使いのコンピューターを VPN または Express Route 経由でクラスター仮想ネットワーク、またはクラスター仮想ネットワーク内に作成されるジャンプボックスに接続する必要があります。 AKS 実行コマンドを使用すると、AKS クラスター内で AKS API を介してコマンドをリモートで呼び出すことができます。 この機能により、リモート ラップトップから、たとえばプライベート クラスターに対する Just-In-Time コマンドを実行できるようにする API が提供されます。 これが大きな支援となって、クライアント コンピューターがクラスターのプライベート ネットワーク上になく、その場合でも同じ RBAC コントロールとプライベート API サーバーを保持して適用する場合に、プライベート クラスターへの迅速な Just-In-Time アクセスを実現できます。

### <a name="register-the-runcommandpreview-preview-feature"></a>`RunCommandPreview` プレビュー機能を登録する

新しい実行コマンド API を使用するには、サブスクリプションで `RunCommandPreview` 機能フラグを有効にする必要があります。

`RunCommandPreview` 機能フラグは、次の例のとおり、[az feature register][az-feature-register] コマンドを使用して登録します。

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "RunCommandPreview"
```

状態が *[登録済み]* と表示されるまでに数分かかります。 登録の状態は、[az feature list][az-feature-list] コマンドで確認できます。

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/RunCommandPreview')].{Name:name,State:properties.state}"
```

準備ができたら、[az provider register][az-provider-register] コマンドを使用して、*Microsoft.ContainerService* リソース プロバイダーの登録を更新します。

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

### <a name="use-aks-run-command"></a>AKS 実行コマンドを使用する

単純なコマンド

```azurecli-interactive
az aks command invoke -g <resourceGroup> -n <clusterName> -c "kubectl get pods -n kube-system"
```

特定のファイルをアタッチしてマニフェストをデプロイする

```azurecli-interactive
az aks command invoke -g <resourceGroup> -n <clusterName> -c "kubectl apply -f deployment.yaml -n default" -f deployment.yaml
```

フォルダー全体をアタッチしてマニフェストをデプロイする

```azurecli-interactive
az aks command invoke -g <resourceGroup> -n <clusterName> -c "kubectl apply -f deployment.yaml -n default" -f .
```

Helm のインストールを実行し、特定の値のマニフェストを渡す

```azurecli-interactive
az aks command invoke -g <resourceGroup> -n <clusterName> -c "helm repo add bitnami https://charts.bitnami.com/bitnami && helm repo update && helm install my-release -f values.yaml bitnami/nginx" -f values.yaml
```

## <a name="virtual-network-peering"></a>仮想ネットワーク ピアリング

前述のように、仮想ネットワーク ピアリングはプライベート クラスターにアクセスする方法の 1 つです。 仮想ネットワーク ピアリングを使用するには、仮想ネットワークとプライベート DNS ゾーンの間にリンクを設定する必要があります。
    
1. Azure portal でノード リソース グループに移動します。  
2. プライベート DNS ゾーンを選択します。   
3. 左側のウィンドウで、 **[仮想ネットワーク]** リンクを選択します。  
4. 新しいリンクを作成して、VM の仮想ネットワークをプライベート DNS ゾーンに追加します。 DNS ゾーンリンクが利用可能になるまで数分かかります。  
5. Azure portal で、クラスターの仮想ネットワークが含まれるリソース グループに移動します。  
6. 右側のウィンドウで、仮想ネットワークを選択します。 仮想ネットワーク名は *aks-vnet-\** のフォームになっています。  
7. 左側のウィンドウで、 **[ピアリング]** を選択します。  
8. **[追加]** を選択して VM の仮想ネットワークを追加し、次にピアリングを作成します。  
9. VM のある仮想ネットワークに移動して **[ピアリング]** を選択、AKS 仮想ネットワークを選択し、次にピアリングを作成します。 AKS 仮想ネットワークのアドレス範囲と VM の仮想ネットワークが競合している場合、ピアリングは失敗します。 詳細については、「[仮想ネットワーク ピアリング][virtual-network-peering]」を参照してください。

## <a name="hub-and-spoke-with-custom-dns"></a>カスタム DNS を使用したハブとスポーク

[ハブとスポークのアーキテクチャ](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)は、Azure でネットワークをデプロイするためによく使用されます。 これらのデプロイの多くでは、オンプレミスと Azure ベースの DNS 解決が可能となるよう、スポーク VNet の DNS 設定は中央の DNS フォワーダーを参照するように構成されます。 このようなネットワーク環境に AKS クラスターをデプロイする場合、いくつかの特別な考慮事項を考慮する必要があります。

![プライベート クラスターのハブとスポーク](media/private-clusters/aks-private-hub-spoke.png)

1. 既定では、プライベート クラスターがプロビジョニングされると、プライベート エンドポイント (1) とプライベート DNS ゾーン (2) がクラスター マネージド リソース グループに作成されます。 クラスターによりプライベート ゾーンの A レコードが使用され、API サーバーとの通信のためにプライベート エンドポイントの IP が解決されます。

2. プライベート DNS ゾーンは、クラスター ノードが接続されている VNet にのみリンクされます (3)。 つまり、プライベート エンドポイントは、そのリンクされた VNet 内のホストによってのみ解決できます。 VNet にカスタム DNS が一切構成されていないシナリオ (既定) では、これは問題なく機能します。リンクされているためにプライベート DNS ゾーン内のレコードを解決できる DNS の 168.63.129.16 が、ホストによって指定されるためです。

3. クラスターを含む VNet にカスタム DNS 設定があるシナリオでは (4)、プライベート DNS ゾーンがカスタム DNS リゾルバーを含む VNet にリンクされていない限り (5)、クラスターのデプロイは失敗します。 このリンクは、クラスターのプロビジョニング中にプライベート ゾーンを作成した後に手動で、またはイベントベースのデプロイ メカニズム (Azure Event Grid や Azure Functions など) を使用したゾーンの作成の検出時に自動で、作成することができます。

> [!NOTE]
> [kubernet で独自のルート テーブル](./configure-kubenet.md#bring-your-own-subnet-and-route-table-with-kubenet)を使用し、プライベート クラスターで独自の DNS を使用する場合、クラスターの作成は失敗します。 作成を成功させるためには、クラスターの作成に失敗した後、ノード リソース グループの [RouteTable](./configure-kubenet.md#bring-your-own-subnet-and-route-table-with-kubenet) をサブネットに関連付ける必要があります。

## <a name="limitations"></a>制限事項 
* AKS-RunCommand コマンドは、AKS マネージド AAD と Private Link が有効になっているクラスターに対しては機能しません。
* 承認済み IP 範囲は、プライベート API サーバー エンドポイントには適用できません。パブリック API サーバーにのみ適用されます
* [Azure Private Link サービスの制限事項][private-link-service]は、プライベート クラスターに適用されます。
* Azure DevOps Microsoft でホストするエージェントとプライベート クラスターの組み合わせはサポートされていません。 [セルフホステッド エージェント](/azure/devops/pipelines/agents/agents?tabs=browser)を使用することを検討してください。 
* Azure Container Registry をプライベート AKS で使用できるようにする必要があるカスタマーは、Container Registry 仮想ネットワークをエージェントクラスターの仮想ネットワークとピアリングしてください。
* 既存の AKS クラスターからプライベートクラスターへの変換はサポートされていません
* カスタマーのサブネット内でプライベート エンドポイントを削除または変更すると、クラスターが機能しなくなります。 

<!-- LINKS - internal -->
[az-provider-register]: /cli/azure/provider#az_provider_register
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[private-link-service]: ../private-link/private-link-service-overview.md#limitations
[virtual-network-peering]: ../virtual-network/virtual-network-peering-overview.md
[azure-bastion]: ../bastion/tutorial-create-host-portal.md
[express-route-or-vpn]: ../expressroute/expressroute-about-virtual-network-gateways.md
[devops-agents]: /azure/devops/pipelines/agents/agents
[availability-zones]: availability-zones.md
