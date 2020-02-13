---
title: プライベート Azure Kubernetes Service クラスターを作成する
description: プライベート Azure Kubernetes Service (AKS) クラスターの作成方法について説明します
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 1/24/2020
ms.author: mlearned
ms.openlocfilehash: 934dfdb43d6d2e4ccc346b728f0ac4f5febea327
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/01/2020
ms.locfileid: "76932591"
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
* 米国西部
* 米国西部 2
* 米国東部 2
* カナダ中部
* 北ヨーロッパ
* 西ヨーロッパ
* オーストラリア東部

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

## <a name="connect-to-the-private-cluster"></a>プライベート クラスターに接続する
API サーバー エンドポイントには、パブリック IP アドレスがありません。 そのため、仮想ネットワーク内に Azure 仮想マシン (VM) を作成し、API サーバーに接続する必要があります。 そのためには、次の手順を実行します。

1. クラスターに接続するための資格情報を取得します。

   ```azurecli-interactive
   az aks get-credentials --name MyManagedCluster --resource-group MyResourceGroup
   ```

1. 以下のいずれかを実行します。
   * AKS クラスターと同じ仮想ネットワーク内に VM を作成します。  
   * 別の仮想ネットワーク内に VM を作成し、この仮想ネットワークを AKS クラスター仮想ネットワークとピアリングします。

     別の仮想ネットワーク内に VM を作成する場合には、この仮想ネットワークとプライベート DNS ゾーンの間にリンクを設定します。 そのためには次を行います。
    
     a. Azure portal の MC_ * リソース グループに移動します。  
     b. プライベート DNS ゾーンを選択します。   
     c. 左側のウィンドウで、 **[仮想ネットワーク]** リンクを選択します。  
     d. 新しいリンクを作成して、VM の仮想ネットワークをプライベート DNS ゾーンに追加します。 DNS ゾーンリンクが利用可能になるまで数分かかります。  
     e. Azure portal の MC_ * リソースグループに戻ります。  
     f. 右側のウィンドウで、仮想ネットワークを選択します。 仮想ネットワーク名は *aks-vnet-\** のフォームになっています。  
     g. 左側のウィンドウで、 **[ピアリング]** を選択します。  
     h. **[追加]** を選択して VM の仮想ネットワークを追加し、次にピアリングを作成します。  
     i. VM のある仮想ネットワークに移動して **[ピアリング]** を選択、AKS 仮想ネットワークを選択し、次にピアリングを作成します。 AKS 仮想ネットワークのアドレス範囲と VM の仮想ネットワークが競合している場合、ピアリングは失敗します。 詳細については、「[仮想ネットワーク ピアリング][virtual-network-peering]」を参照してください。

1. Secure Shell (SSH) 経由で VM にアクセスします。
1. Kubectl ツールをインストールし、Kubectl コマンドを実行します。


## <a name="dependencies"></a>依存関係  
* Private Link サービスは、Standard Azure Load Balancer でのみサポートされています。 Basic Azure Load Balancer はサポートされていません。  
* カスタム DNS サーバーを使用するには、この IP 168.63.129.16 に転送する DNS を備えた AD サーバーをデプロイします。

## <a name="limitations"></a>制限事項 
* 現在、Availability Zones は米国東部 2 と米国西部 2 のリージョンでのみサポートされています。
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
[private-link-service]: https://docs.microsoft.com/azure/private-link/private-link-service-overview
[virtual-network-peering]: ../virtual-network/virtual-network-peering-overview.md

