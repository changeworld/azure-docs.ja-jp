---
title: プライベート Azure Kubernetes Service クラスター
description: プライベート Azure Kubernetes Service (AKS) クラスターの作成方法について説明します
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 12/10/2019
ms.author: mlearned
ms.openlocfilehash: 8af0f998df2a92e51078a2e23806cca07ff08ca3
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/25/2019
ms.locfileid: "75475017"
---
# <a name="public-preview---private-azure-kubernetes-service-cluster"></a>パブリック プレビュー - プライベート Azure Kubernetes Service クラスター

プライベート クラスターでは、コントロール プレーン/API サーバーは [RFC1918](https://tools.ietf.org/html/rfc1918) で定義された内部 IP アドレスを持ちます。  プライベート クラスターを使用することにより、API サーバーとノード プールの間のネットワーク トラフィックがプライベート ネットワークにのみ保持されるようにすることができます。

コントロール プレーン/API サーバー (AKS マネージド Azure サブスクリプションに含まれる) と顧客のクラスター/ノードプール (顧客サブスクリプションに含まれる) の間の通信は、API サーバー VNET の [Private Link サービス][private-link-service] と顧客の AKS クラスターのサブネットで公開されているプライベート エンドポイントを介して相互に通信できます。

> [!IMPORTANT]
> AKS のプレビュー機能は、セルフサービスのオプトインです。 プレビューは、"現状有姿のまま" および "利用可能な限度" で提供され、サービス レベル契約および限定保証から除外されるものとします。 AKS プレビューは、カスタマー サポートによってベスト エフォートで部分的にカバーされます。 そのため、これらの機能は、運用環境での使用を意図していません。 詳細については、次のサポートに関する記事を参照してください。
>
> * [AKS のサポート ポリシー](support-policies.md)
> * [Azure サポートに関する FAQ](faq.md)

## <a name="before-you-begin"></a>開始する前に

* Azure CLI バージョン 2.0.77 以降および aks-preview 0.4.18 拡張機能が必要です

## <a name="current-supported-regions"></a>現在サポートされているリージョン
* 米国西部
* 米国西部 2
* 米国東部 2
* カナダ中部
* 北ヨーロッパ
* 西ヨーロッパ
* オーストラリア東部

## <a name="install-latest-aks-cli-preview-extension"></a>最新の AKS CLI プレビュー拡張機能をインストールする

プライベート クラスターを使用するには、*aks-preview* CLI 拡張機能のバージョン 0.4.18 以降が必要です。 [az extension add][az-extension-add] コマンドを使用して *aks-preview* Azure CLI 拡張機能をインストールし、[az extension update][az-extension-update] コマンドを使用して使用可能な更新プログラムがあるかどうかを確認します。

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```
> [!CAUTION]
> サブスクリプションで機能を登録する場合、現時点ではその機能を登録解除することはできません。 一部のプレビュー機能を有効にした後、すべての AKS クラスターに対して既定値が使用され、サブスクリプション内に作成されます。 運用サブスクリプションではプレビュー機能を有効にしないでください。 プレビュー機能をテストし、フィードバックを集めるには、別のサブスクリプションを使用してください。

```azurecli-interactive
az feature register --name AKSPrivateLinkPreview --namespace Microsoft.ContainerService
```

状態が "*登録済み*" と表示されるまでに数分かかることがあります。 [az feature list][az-feature-list] コマンドを使用して登録状態を確認できます。

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKSPrivateLinkPreview')].{Name:name,State:properties.state}"
```

状態が登録されたら、[az provider register][az-provider-register] コマンドを使用して *Microsoft.ContainerService* リソース プロバイダーの登録を最新の情報に更新します。

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
az provider register --namespace Microsoft.Network
```
## <a name="create-a-private-aks-cluster"></a>プライベート AKS クラスターを作成する

#### <a name="default-basic-networking"></a>既定の基本的なネットワーク 

```azurecli-interactive
az aks create -n <private-cluster-name> -g <private-cluster-resource-group> --load-balancer-sku standard --enable-private-cluster  
```
--enable-private-cluster は、プライベート クラスターに必須のフラグです 

#### <a name="advanced-networking"></a>高度なネットワーク  

```azurecli-interactive
az aks create \ 
    --resource-group <private-cluster-resource-group>\ 
    --name <private-cluster-name> \ 
    --load-balancer-sku standard
    --enable-private-cluster 
    --network-plugin azure \ 
    --vnet-subnet-id <subnet-id> \ 
    --docker-bridge-address 172.17.0.1/16 \ 
    --dns-service-ip 10.2.0.10 \ 
    --service-cidr 10.2.0.0/24 \ 
```
--enable-private-cluster は、プライベート クラスターに必須のフラグです 

## <a name="steps-to-connect-to-the-private-cluster"></a>プライベート クラスターに接続するための手順
この API サーバー エンドポイントには、パブリック IP アドレスが指定されていません。 したがって、ユーザーは、仮想ネットワーク内に Azure 仮想マシンを作成し、API サーバーに接続する必要があります。 次の手順を実行します

* 資格情報を取得してクラスターに接続します

   ```azurecli-interactive
   az aks get-credentials --name MyManagedCluster --resource-group MyResourceGroup
   ```
* AKS クラスターと同じ VNET に VM を作成するか、別の VNET に VM を作成して、この VNET と AKS クラスター VNET をピアリングします
* 別の VNET に VM を作成する場合は、この VNET とプライベート DNS ゾーン間のリンクを設定する必要があります
    * ポータルで、MC_* リソース グループに移動します 
    * プライベート DNS ゾーンをクリックします 
    * 左ペインで仮想ネットワーク リンクを選択します
    * 新しいリンクを作成して、VM の VNET をプライベート DNS ゾーンに追加します *(DNS ゾーン リンクが使用可能になるまで数分かかります)*
* VM に SSH 接続します
* Kubectl ツールをインストールして、kubectl コマンドを実行します

## <a name="dependencies"></a>依存関係  
* Standard LB のみ - 基本のロード バランサーはサポートしていません  

## <a name="limitations"></a>制限事項 
* 同じ [Azure Private Link サービスの制限事項][private-link-service]がプライベート クラスターに適用されます。Azure プライベート エンドポイントと仮想ネットワーク サービス エンドポイントは、同一の VNET では現在サポートされていません
* プライベート Azure VNET 内のプライベート ACI インスタンスをスピンすることは、プライベート クラスター内の仮想ノードではサポートされていません
* プライベート クラスターですぐに使用できる Azure DevOps 統合はサポートされていません
* ACR を有効にしてプライベート AKS を操作する必要がある場合は、その ACR の VNET をエージェント クラスター VNET とピアリングする必要があります
* Azure Dev Spaces は現在サポートされていません
* 既存の AKS クラスターからプライベート クラスターへの変換はサポートされていません  
* 顧客のサブネット内でプライベート エンドポイントを削除または変更すると、クラスターが機能しなくなります 
* コンテナー用 Azure Monitor のライブ データは現在サポートされていません
* 独自の DNS を使用することは現在サポートされていません


<!-- LINKS - internal -->
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest#az-provider-register
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest#az-feature-list
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[private-link-service]: https://docs.microsoft.com/azure/private-link/private-link-service-overview
