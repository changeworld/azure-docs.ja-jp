---
title: プライベート Azure Kubernetes Service クラスターを作成する
description: プライベート Azure Kubernetes Service (AKS) クラスターの作成方法について説明します
services: container-service
ms.topic: article
ms.date: 2/21/2020
ms.openlocfilehash: 0a05bd15fff97d4f0020f6ce82ee90a2fe995edf
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/09/2020
ms.locfileid: "78944199"
---
# <a name="create-a-private-azure-kubernetes-service-cluster-preview"></a>プライベート Azure Kubernetes Service クラスターを作成する (プレビュー)

プライベートクラスターにおいては、コントロールプレーンまたは API サーバーは「[プライベートインターネット向けの RFC1918 アドレス割り当て](https://tools.ietf.org/html/rfc1918)」で定義される内部 IP アドレスを持っています。 プライベート クラスターを使用することで、API サーバーとノード プールの間のネットワーク トラフィックがプライベート ネットワークにのみ保持されるようにすることができます。

コントロールプレーンまたは API サーバーは、Azure Kubernetes Service (AKS) マネージドの Azure サブスクリプションの中にあります。 カスタマーのクラスターまたはノードプールは、カスタマーのサブスクリプションの中にあります。 サーバーとクラスターまたはノードプールは、API サーバー仮想ネットワーク内の [Azure Private Link サービス][private-link-service] と、カスタマーの AKS クラスターのサブネットで公開されているプライベート エンドポイントを介して相互に通信できます。

> [!IMPORTANT]
> AKS のプレビュー機能はセルフサービスであり、オプトイン ベースでオファーされます。 プレビューは、「*現状のまま*」、 「*利用可能な限度で*」提供されており、サービス レベル契約 (SLA) および限定保証からは除外されています。 AKS プレビューは、*ベストエフォート* ベースでカスタマー サポートによって部分的にカバーされます。 したがって、これらのフィーチャーはプロダクション環境での使用を目的としたものではありません。 詳細については、次のサポート記事を参照してください。
>
> * [AKS のサポート ポリシー](support-policies.md)
> * [Azure サポートに関する FAQ](faq.md)

## <a name="prerequisites"></a>前提条件

* Azure CLI バージョン 2.0.77 以降、および Azure CLI AKS Preview 拡張機能バージョン 0.4.18

## <a name="currently-supported-regions"></a>現在サポートされているリージョン

* オーストラリア東部
* オーストラリア南東部
* ブラジル南部
* カナダ中部
* カナダ東部
* 米国中部
* 東アジア
* 米国東部
* 米国東部 2
* 米国東部 2 EUAP
* フランス中部
* ドイツ北部
* 東日本
* 西日本
* 韓国中部
* 韓国南部
* 米国中北部
* 北ヨーロッパ
* 北ヨーロッパ
* 米国中南部
* 英国南部
* 西ヨーロッパ
* 米国西部
* 米国西部 2
* 米国東部 2

## <a name="currently-supported-availability-zones"></a>現在サポートされている Availability Zones

* 米国中部
* 米国東部
* 米国東部 2
* フランス中部
* 東日本
* 北ヨーロッパ
* 東南アジア
* 英国南部
* 西ヨーロッパ
* 米国西部 2

## <a name="install-the-latest-azure-cli-aks-preview-extension"></a>最新の Azure CLI AKS Preview 拡張機能をインストールする

プライベートクラスターを使用するには、Azure CLI AKS Preview 拡張機能バージョン 0.4.18 以降が必要です。 [az extension add][az-extension-add] コマンドを使用して Azure CLI AKS Preview 拡張機能をインストールした上で、次の [az extension update][az-extension-update] コマンドを使用して、利用可能な更新プログラムがあるかどうかを確認します。

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```
> [!CAUTION]
> サブスクリプションで機能を登録する場合、現時点ではその機能を登録解除することはできません。 一部のプレビュー機能を有効にすると、その後はサブスクリプションで作成されたすべての AKS クラスターに対して既定の設定を使用できます。 運用サブスクリプションではプレビュー機能を有効にしないでください。 プレビュー機能をテストし、フィードバックを集めるには、別のサブスクリプションを使用してください。

```azurecli-interactive
az feature register --name AKSPrivateLinkPreview --namespace Microsoft.ContainerService
```

登録状態が「*登録済み*」と表示されるまでに数分かかることがあります。 登録状態を確認するには、次の [az feature list][az-feature-list] コマンドを使用します。

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKSPrivateLinkPreview')].{Name:name,State:properties.state}"
```

状態が登録されたら、次の [az provider register][az-provider-register] コマンドを使用して *Microsoft.ContainerService* リソース プロバイダーの登録を最新の情報に更新します。

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
az provider register --namespace Microsoft.Network
```
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
*--enable-private-cluster*は、プライベート クラスターに必須のフラグです 

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
*--enable-private-cluster*は、プライベート クラスターに必須のフラグです 

> [!NOTE]
> Docker ブリッジ アドレス CIDR (172.17.0.1/16) がサブネット CIDR と競合する場合は、Docker ブリッジ アドレスを適切に変更します。

## <a name="options-for-connecting-to-the-private-cluster"></a>プライベート クラスターに接続するための選択肢

API サーバー エンドポイントには、パブリック IP アドレスがありません。 API サーバーを管理するには、AKS クラスターの Azure Virtual Network (VNet) にアクセスできる VM を使用する必要があります。 プライベート クラスターへのネットワーク接続を確立するには、いくつかの選択肢があります。

* AKS クラスターと同じ Azure Virtual Network (VNet) に VM を作成します。
* 別のネットワーク内の VM を使用し、[仮想ネットワーク ピアリング][virtual-network-peering]を設定します。  このオプションの詳細については、以下のセクションを参照してください。
* [Express Route または VPN][express-route-or-VPN] 接続を使用します。

AKS クラスターと同じ VNET に VM を作成するのが最も簡単な方法です。  Express Route と VPN にはコストがかかり、ネットワークがさらに複雑になります。  仮想ネットワーク ピアリングを利用する場合、重複する範囲がないようにネットワーク CIDR 範囲を計画する必要があります。

## <a name="virtual-network-peering"></a>仮想ネットワーク ピアリング

前述のように、VNet ピアリングはプライベート クラスターにアクセスする方法の 1 つです。 VNet ピアリングを使用するには、仮想ネットワークとプライベート DNS ゾーンの間にリンクを設定する必要があります。
    
1. Azure portal の MC_ * リソース グループに移動します。  
2. プライベート DNS ゾーンを選択します。   
3. 左側のウィンドウで、 **[仮想ネットワーク]** リンクを選択します。  
4. 新しいリンクを作成して、VM の仮想ネットワークをプライベート DNS ゾーンに追加します。 DNS ゾーンリンクが利用可能になるまで数分かかります。  
5. Azure portal の MC_ * リソースグループに戻ります。  
6. 右側のウィンドウで、仮想ネットワークを選択します。 仮想ネットワーク名は *aks-vnet-\** のフォームになっています。  
7. 左側のウィンドウで、 **[ピアリング]** を選択します。  
8. **[追加]** を選択して VM の仮想ネットワークを追加し、次にピアリングを作成します。  
9. VM のある仮想ネットワークに移動して **[ピアリング]** を選択、AKS 仮想ネットワークを選択し、次にピアリングを作成します。 AKS 仮想ネットワークのアドレス範囲と VM の仮想ネットワークが競合している場合、ピアリングは失敗します。 詳細については、「[仮想ネットワーク ピアリング][virtual-network-peering]」を参照してください。

## <a name="dependencies"></a>依存関係  
* Private Link サービスは、Standard Azure Load Balancer でのみサポートされています。 Basic Azure Load Balancer はサポートされていません。  
* カスタム DNS サーバーを使用するには、この IP 168.63.129.16 に転送する DNS を備えた AD サーバーをデプロイします。

## <a name="limitations"></a>制限事項 
* 承認済み IP 範囲は、プライベート API サーバー エンドポイントには適用できません。パブリック API サーバーにのみ適用されます
* Availability Zones は現在、特定のリージョンでサポートされています。このドキュメントの冒頭をご覧ください 
* [Azure Private Link サービスの制限事項][private-link-service]は、プライベートクラスター、Azure プライベート エンドポイント、および仮想ネットワークサービスエンドポイントに適用されます。これらは現在、同一の仮想ネットワークではサポートされていません。
* プライベート Azure 仮想ネットワーク内のプライベート Azure Container Instances (ACI) をスピンすることは、プライベート クラスター内の仮想ノードではサポートされていません
* プライベート クラスターですぐに使用できる Azure DevOps 統合はサポートされていません
* Azure Container Registry をプライベート AKS で使用できるようにする必要があるカスタマーは、Container Registry 仮想ネットワークをエージェントクラスターの仮想ネットワークとピアリングしてください。
* Azure Dev Spaces は現在サポートされていません
* 既存の AKS クラスターからプライベートクラスターへの変換はサポートされていません
* カスタマーのサブネット内でプライベート エンドポイントを削除または変更すると、クラスターが機能しなくなります。 
* コンテナー用 Azure Monitor の Live Data は現在サポートされていません。
* *独自の DNS の使用*は現在サポートされていません。


<!-- LINKS - internal -->
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest#az-provider-register
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest#az-feature-list
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[private-link-service]: /private-link/private-link-service-overview
[virtual-network-peering]: ../virtual-network/virtual-network-peering-overview.md
[azure-bastion]: ../bastion/bastion-create-host-portal.md
[express-route-or-vpn]: ../expressroute/expressroute-about-virtual-network-gateways.md

