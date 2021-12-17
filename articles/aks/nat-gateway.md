---
title: 管理された NAT Gateway (プレビュー)
description: マネージド NAT 統合を使用して AKS クラスターを作成する方法について説明します
services: container-service
ms.topic: article
ms.date: 10/26/2021
ms.author: juda
ms.openlocfilehash: 87edce54391661fe986365ad45d72bfeee41c761
ms.sourcegitcommit: c434baa76153142256d17c3c51f04d902e29a92e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2021
ms.locfileid: "132179604"
---
# <a name="managed-nat-gateway-preview"></a>管理された NAT Gateway (プレビュー)

AKS のお客様は Azure Load Balancer 経由でエグレス トラフィックをルーティングできますが、送信フローの可能なトラフィックの量に制限があります。 

Azure NAT Gateway では、IP アドレスあたり最大で 64,000 個の送信 UDP および TCP トラフィック フローが許容され、最大で 16 個の IP アドレスを使用できます。

この記事では、エグレス トラフィックにマネージド NAT Gateway を使用して AKS クラスターを作成する方法について説明します。

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="before-you-begin"></a>開始する前に

マネージド NAT Gateway を使用するには、次のものが必要です。

* 最新バージョンの Azure CLI
* `aks-preview` 拡張機能バージョン 0.5.31 以降
* Kubernetes バージョン 1.20.x 以降


### <a name="register-the-aks-natgatewaypreview-feature-flag"></a>`AKS-NATGatewayPreview` 機能フラグを登録する

NAT Gateway 機能を使用するには、サブスクリプションで `AKS-NATGatewayPreview` 機能フラグを有効にする必要があります。 

```azurecli
az feature register --namespace "Microsoft.ContainerService" --name "AKS-NATGatewayPreview"
```
[az feature list][az-feature-list] コマンドを使用して登録状態を確認できます。

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-NATGatewayPreview')].{Name:name,State:properties.state}"
```

準備ができたら、[az provider register][az-provider-register] コマンドを使用して、*Microsoft.ContainerService* リソース プロバイダーの登録を更新します。

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```


## <a name="create-an-aks-cluster-with-a-managed-nat-gateway"></a>マネージド NAT Gateway を使用して AKS クラスターを作成する
新しいマネージド NAT Gateway を使用して AKS クラスターを作成するには、`az aks create` の実行時に `--outbound-type managedNATGateway`、`--nat-gateway-managed-outbound-ip-count`、`--nat-gateway-idle-timeout` を使用します。 次の例では、*myresourcegroup* リソース グループを作成し、マネージド NAT Gateway、2 つの送信 IP、30 秒のアイドル タイムアウトを使用して、*myresourcegroup* に *natcluster* AKS クラスターを作成します。


```azurecli-interactive
az group create --name myresourcegroup --location southcentralus
```

```azurecli-interactive
az aks create \
    --resource-group myresourcegroup \
    --name natcluster \
    --node-count 3 \
    --outbound-type managedNATGateway \ 
    --nat-gateway-managed-outbound-ip-count 2 \
    --nat-gateway-idle-timeout 30
```

> [!IMPORTANT]
> 送信 IP アドレスが指定されていない場合、既定値は 1 です。

### <a name="update-the-number-of-outbound-ip-addresses"></a>送信 IP アドレスの数を更新する
送信 IP アドレスまたはアイドル タイムアウトを更新するには、`az aks update` の実行時に `--nat-gateway-managed-outbound-ip-count` または `--nat-gateway-idle-timeout` を使用します。 次に例を示します。

```azurecli-interactive
az aks update \ 
    --resource-group myresourcegroup \
    --name natcluster\
    --nat-gateway-managed-outbound-ip-count 5
```

## <a name="create-an-aks-cluster-with-a-user-assigned-nat-gateway"></a>ユーザー割り当て NAT Gateway を使用して AKS クラスターを作成する
ユーザー割り当て NAT Gateway を使用して AKS クラスターを作成するには、`az aks create` を実行するときに `--outbound-type userAssignedNATGateway` を使用します。 この構成には、([Kubenet][byo-vnet-kubenet] または [Azure CNI][byo-vnet-azure-cni] を使用して) 自前で用意したネットワークが必要であり、サブネットで NAT Gateway があらかじめ構成されている必要があります。 以下のコマンドを使用すると、このシナリオに必要なリソースが作成されます。 変数に保存された値が `az aks create` コマンドでもそのまま使用できるよう、すべてを同じセッションで実行するようにしてください。

1. リソース グループを作成します。
    ```azurecli-interactive
    az group create --name myresourcegroup \
        --location southcentralus
    ```

2. ネットワークのアクセス許可のためのマネージド ID を作成し、後で使用できるように ID を `$IDENTITY_ID` に保存します。
    ```azurecli-interactive
    IDENTITY_ID=$(az identity create \
        --resource-group myresourcegroup \
        --name natclusterid \
        --location southcentralus \
        --query id \
        --output tsv)
    ```

3. NAT ゲートウェイのパブリック IP を作成します。
    ```azurecli-interactive
    az network public-ip create \
        --resource-group myresourcegroup \
        --name mynatgatewaypip \
        --location southcentralus \
        --sku standard
    ```

4. NAT ゲートウェイを作成します。
    ```azurecli-interactive
    az network nat gateway create \
        --resource-group myresourcegroup \
        --name mynatgateway \
        --location southcentralus \
        --public-ip-addresses mynatgatewaypip
    ```

5. 仮想ネットワークを作成します。
    ```azurecli-interactive
    az network vnet create \
        --resource-group myresourcegroup \
        --name myvnet \
        --location southcentralus \
        --address-prefixes 172.16.0.0/20 
    ```

6. NAT ゲートウェイを使用して仮想ネットワークにサブネットを作成し、後で使用できるように ID を `$SUBNET_ID` に保存します。
    ```azurecli-interactive
    SUBNET_ID=$(az network vnet subnet create \
        --resource-group myresourcegroup \
        --vnet-name myvnet \
        --name natcluster \
        --address-prefixes 172.16.0.0/22 \
        --nat-gateway mynatgateway \
        --query id \
        --output tsv)
    ```

7. NAT ゲートウェイとマネージド ID があるサブネットを使用して AKS クラスターを作成します。
    ```azurecli-interactive
    az aks create \
        --resource-group myresourcegroup \
        --name natcluster \
        --location southcentralus \
        --network-plugin azure \
        --vnet-subnet-id $SUBNET_ID \
        --outbound-type userAssignedNATGateway \
        --enable-managed-identity \
        --assign-identity $IDENTITY_ID
    ```

## <a name="next-steps"></a>次の手順
- NAT Gateway の詳細については、[Azure NAT Gateway][nat-docs] に関する記事を参照してください。

<!-- LINKS - internal -->


<!-- LINKS - external-->
[nat-docs]: ../virtual-network/nat-gateway/nat-overview.md
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[byo-vnet-azure-cni]: configure-azure-cni.md
[byo-vnet-kubenet]: configure-kubenet.md