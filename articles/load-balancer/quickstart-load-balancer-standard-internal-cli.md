---
title: クイック スタート:内部ロード バランサーを作成する - Azure CLI
titleSuffix: Azure Load Balancer
description: このクイックスタートでは、Azure CLI を使用して内部ロード バランサーを作成する方法について説明します
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a load balancer so that I can load balance internal traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/19/2020
ms.author: allensu
ms.custom: mvc, devx-track-js, devx-track-azurecli
ms.openlocfilehash: 15060a367bba2d50d7054730321f7f20d4c25e46
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/06/2021
ms.locfileid: "97916679"
---
# <a name="quickstart-create-an-internal-load-balancer-to-load-balance-vms-using-azure-cli"></a>クイック スタート:Azure CLI を使用して VM の負荷を分散する内部ロード バランサーを作成する

Azure CLI を使用して内部ロード バランサーと 3 つの仮想マシンを作成することにより、Azure Load Balancer の使用を開始します。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)] 

- このクイックスタートには、Azure CLI のバージョン 2.0.28 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。

## <a name="create-a-resource-group"></a>リソース グループを作成する

Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。

[az group create](/cli/azure/group#az_group_create) を使用して、次のようにリソース グループを作成します。

* 名前は **CreateIntLBQS-rg** にします。 
* 場所は **eastus** にします。

```azurecli-interactive
  az group create \
    --name CreateIntLBQS-rg \
    --location eastus

```
---

# <a name="standard-sku"></a>[**Standard SKU**](#tab/option-1-create-load-balancer-standard)

>[!NOTE]
>運用環境のワークロードには、Standard SKU ロード バランサーをお勧めします。 SKU の詳細については、「 **[Azure Load Balancer の SKU](skus.md)** 」を参照してください。

:::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/resources-diagram-internal.png" alt-text="クイックスタートで作成される標準的なロード バランサー リソース。" border="false":::

## <a name="configure-virtual-network---standard"></a>仮想ネットワークを構成する - Standard

VM をデプロイしてロード バランサーをデプロイする前に、サポート用の仮想ネットワーク リソースを作成します。

### <a name="create-a-virtual-network"></a>仮想ネットワークの作成

[az network vnet create](/cli/azure/network/vnet#az-network-vnet-create) を使用して、次のように仮想ネットワークを作成します。

* 名前は **myVNet** にします。
* アドレス プレフィックスは **10.1.0.0/16** にします。
* サブネットの名前は **myBackendSubnet** にします。
* サブネット プレフィックスは **10.1.0.0/24** にします。
* 作成先のリソース グループは **CreateIntLBQS-rg** とします。
* 場所は **eastus** にします。

```azurecli-interactive
  az network vnet create \
    --resource-group CreateIntLBQS-rg \
    --location eastus \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.1.0.0/24
```

### <a name="create-a-public-ip-address"></a>パブリック IP アドレスの作成

[az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create) を使用して、bastion ホストのパブリック IP アドレスを作成します。

* **myBastionIP** という名前の Standard ゾーン冗長パブリック IP アドレスを作成します。
* 作成先のリソース グループは **CreateIntLBQS-rg** とします。

```azurecli-interactive
az network public-ip create \
    --resource-group CreateIntLBQS-rg  \
    --name myBastionIP \
    --sku Standard
```
### <a name="create-a-bastion-subnet"></a>bastion サブネットを作成する

[az network vnet subnet create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) を使用して、bastion サブネットを作成します。

* 名前は **AzureBastionSubnet** にします。
* アドレス プレフィックスは **10.1.1.0/24** にします。
* 仮想ネットワークは **myVNet** にします。
* 作成先のリソース グループは **CreateIntLBQS-rg** とします。

```azurecli-interactive
az network vnet subnet create \
    --resource-group CreateIntLBQS-rg  \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.1.1.0/24
```

### <a name="create-bastion-host"></a>bastion ホストを作成する

[az network bastion create](/cli/azure/network/bastion#az-network-bastion-create) を使用して、bastion ホストを作成します。

* 名前: **myBastionHost**
* 作成先のリソース グループは **CreateIntLBQS-rg** とします。
* パブリック IP **myBastionIP** に関連付けます。
* 仮想ネットワーク **myVNet** に関連付けます。
* 場所は **eastus** にします。

```azurecli-interactive
az network bastion create \
    --resource-group CreateIntLBQS-rg  \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus
```

Azure Bastion ホストがデプロイされるまでに数分かかる場合があります。


### <a name="create-a-network-security-group"></a>ネットワーク セキュリティ グループの作成

Standard ロード バランサーの場合、バックエンドが扱う VM には、ネットワーク セキュリティ グループに属しているネットワーク インターフェイスが必要です。 

[az network nsg create](/cli/azure/network/nsg#az-network-nsg-create) を使用して、次のようにネットワーク セキュリティ グループを作成します。

* 名前は **myNSG** にします。
* 作成先のリソース グループは **CreateIntLBQS-rg** とします。

```azurecli-interactive
  az network nsg create \
    --resource-group CreateIntLBQS-rg \
    --name myNSG
```

### <a name="create-a-network-security-group-rule"></a>ネットワーク セキュリティ グループ規則を作成する

[az network nsg rule create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create) を使用して、次のようにネットワーク セキュリティ グループの規則を作成します。

* 名前は **myNSGRuleHTTP** にします。
* ネットワーク セキュリティ グループは、前の手順で作成した **myNSG** にします。
* 作成先のリソース グループは **CreateIntLBQS-rg** とします。
* プロトコルは **(*)** にします。
* 方向は **Inbound** にします。
* 送信元は **(*)** にします。
* 送信先は **(*)** にします。
* 送信先ポートは **ポート 80** にします。
* アクセスは **Allow** にします。
* 優先度は **200** にします。

```azurecli-interactive
  az network nsg rule create \
    --resource-group CreateIntLBQS-rg \
    --nsg-name myNSG \
    --name myNSGRuleHTTP \
    --protocol '*' \
    --direction inbound \
    --source-address-prefix '*' \
    --source-port-range '*' \
    --destination-address-prefix '*' \
    --destination-port-range 80 \
    --access allow \
    --priority 200
```

## <a name="create-backend-servers---standard"></a>バックエンド サーバーを作成する - Standard

このセクションでは、以下を作成します。

* 仮想マシンに使用する 3 つのネットワーク インターフェイス。
* ロード バランサーのバックエンド サーバーとして使用する 3 つの仮想マシン。

### <a name="create-network-interfaces-for-the-virtual-machines"></a>仮想マシンのネットワーク インターフェイスを作成する

[az network nic create](/cli/azure/network/nic#az-network-nic-create) を使用して、次のように 3 つのネットワーク インターフェイスを作成します。

* それぞれ名前は **myNicVM1**、**myNicVM2**、**myNicVM3** とします。
* 作成先のリソース グループは **CreateIntLBQS-rg** とします。
* 仮想ネットワークは **myVNet** にします。
* サブネットは **myBackendSubnet** にします。
* ネットワーク セキュリティ グループは **myNSG** にします。

```azurecli-interactive
  array=(myNicVM1 myNicVM2 myNicVM3)
  for vmnic in "${array[@]}"
  do
    az network nic create \
        --resource-group CreateIntLBQS-rg \
        --name $vmnic \
        --vnet-name myVNet \
        --subnet myBackEndSubnet \
        --network-security-group myNSG
  done
```

### <a name="create-virtual-machines"></a>仮想マシンを作成する

[az vm create](/cli/azure/vm#az-vm-create) を使用して、次のように仮想マシンを作成します。

* それぞれの名前は **myVM1**、 **myVM2**、 **myVM3** とします。
* 作成先のリソース グループは **CreateIntLBQS-rg** とします。
* ネットワーク インターフェイス **myNicVM1**、**myNicVM2**、および **myNicVM3** に接続します。
* 仮想マシン イメージは **win2019datacenter** にします。
* **ゾーン 1**、**ゾーン 2**、および **ゾーン 3** 内です。

```azurecli-interactive
  array=(1 2 3)
  for n in "${array[@]}"
  do
    az vm create \
    --resource-group CreateIntLBQS-rg \
    --name myVM$n \
    --nics myNicVM$n \
    --image win2019datacenter \
    --admin-username azureuser \
    --zone $n \
    --no-wait
  done
```

VM がデプロイされるまでに、数分かかる場合があります。

## <a name="create-standard-load-balancer"></a>Standard ロード バランサーを作成する

このセクションでは、ロード バランサーの以下のコンポーネントを作成および構成する方法について説明します。

  * ロード バランサーの着信ネットワーク トラフィックを受け取るフロントエンド IP プール。
  * フロントエンド プールから負荷分散されたネットワーク トラフィックが送信されるバックエンド IP プール。
  * バックエンド VM インスタンスの正常性を判断する正常性プローブ。
  * VM に対するトラフィックの分散方法を定義するロード バランサー規則。

### <a name="create-the-load-balancer-resource"></a>ロード バランサーのリソースを作成する

[az network lb create](/cli/azure/network/lb#az-network-lb-create) を使用して、次のようにパブリック ロード バランサーを作成します。

* 名前は **myLoadBalancer** にします。
* フロントエンド プールの名前は **myFrontEnd** にします。
* バックエンド プールの名前は **myBackEndPool** にします。
* 仮想ネットワーク **myVNet** に関連付けます。
* バックエンド サブネット **myBackendSubnet** に関連付けます。

```azurecli-interactive
  az network lb create \
    --resource-group CreateIntLBQS-rg \
    --name myLoadBalancer \
    --sku Standard \
    --vnet-name myVnet \
    --subnet myBackendSubnet \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool
```

### <a name="create-the-health-probe"></a>正常性プローブを作成する

正常性プローブは、すべての仮想マシン インスタンスを調べて、ネットワーク トラ	フィックを送信できるかどうかを確認します。 

プローブ チェックが失敗した仮想マシンは、ロード バランサーから削除されます。 障害が解決されると、仮想マシンがロード バランサーに再び追加されます。

[az network lb probe create](/cli/azure/network/lb/probe#az-network-lb-probe-create) を使用して、次のように正常性プローブを作成します。

* 仮想マシンの正常性を監視します。
* 名前は **myHealthProbe** にします。
* プロトコルは **TCP** にします。
* **ポート 80** を監視します。

```azurecli-interactive
  az network lb probe create \
    --resource-group CreateIntLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80
```

### <a name="create-the-load-balancer-rule"></a>ロード バランサー規則を作成する

ロード バランサー規則は、以下のものを定義します。

* 着信トラフィック用のフロントエンド IP 構成。
* トラフィックを受信するためのバックエンド IP プール。
* 必要な発信元ポートと宛先ポート。 

[az network lb rule create](/cli/azure/network/lb/rule#az-network-lb-rule-create) を使用して、次のようにロード バランサー規則を作成します。

* 名前は **myHTTPRule** にします
* フロントエンド プール **myFrontEnd** で **ポート 80** をリッスンします。
* **ポート 80** を使用して、負荷分散されたネットワーク トラフィックをバックエンド アドレス プール **myBackEndPool** に送信します。 
* 正常性プローブ **myHealthProbe** を使用します。
* プロトコルは **TCP** にします。
* アイドル タイムアウトは **15 分** とします。
* TCP リセットを有効にします。

```azurecli-interactive
  az network lb rule create \
    --resource-group CreateIntLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe \
    --disable-outbound-snat true \
    --idle-timeout 15 \
    --enable-tcp-reset true
```
>[!NOTE]
>バックエンド プール内の仮想マシンは、この構成ではアウトバウンド インターネット接続を持ちません。 </br> アウトバウンド接続の提供の詳細については、以下を参照してください。 </br> **[Azure の送信接続](load-balancer-outbound-connections.md)**</br> 接続を提供するためのオプション: </br> **[送信専用のロード バランサーの構成](egress-only.md)** </br> **[Virtual Network NAT とは](../virtual-network/nat-overview.md)**

### <a name="add-virtual-machines-to-load-balancer-backend-pool"></a>仮想マシンをロード バランサー バックエンド プールに追加する

[az network nic ip-config address-pool add](/cli/azure/network/nic/ip-config/address-pool#az-network-nic-ip-config-address-pool-add) を使用して、次のように仮想マシンをバックエンド プールに追加します。

* バックエンド アドレス プールは **myBackEndPool** にします。
* 作成先のリソース グループは **CreateIntLBQS-rg** とします。
* ネットワーク インターフェイス **myNicVM1**、**myNicVM2**、および **myNicVM3** に関連付けます。
* ロード バランサー **myLoadBalancer** に関連付けます。

```azurecli-interactive
  array=(VM1 VM2 VM3)
  for vm in "${array[@]}"
  do
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNic$vm \
   --resource-group CreateIntLBQS-rg \
   --lb-name myLoadBalancer
  done

```

# <a name="basic-sku"></a>[**Basic SKU**](#tab/option-1-create-load-balancer-basic)

>[!NOTE]
>運用環境のワークロードには、Standard SKU ロード バランサーをお勧めします。 SKU の詳細については、「 **[Azure Load Balancer の SKU](skus.md)** 」を参照してください。

:::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/resources-diagram-internal-basic.png" alt-text="クイックスタートで作成される基本的なロード バランサー リソース。" border="false":::

## <a name="configure-virtual-network---basic"></a>仮想ネットワークを構成する - Basic

VM をデプロイしてロード バランサーをデプロイする前に、サポート用の仮想ネットワーク リソースを作成します。

### <a name="create-a-virtual-network"></a>仮想ネットワークの作成

[az network vnet create](/cli/azure/network/vnet#az-network-vnet-createt) を使用して、次のように仮想ネットワークを作成します。

* 名前は **myVNet** にします。
* アドレス プレフィックスは **10.1.0.0/16** にします。
* サブネットの名前は **myBackendSubnet** にします。
* サブネット プレフィックスは **10.1.0.0/24** にします。
* 作成先のリソース グループは **CreateIntLBQS-rg** とします。
* 場所は **eastus** にします。

```azurecli-interactive
  az network vnet create \
    --resource-group CreateIntLBQS-rg \
    --location eastus \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.1.0.0/24
```

### <a name="create-a-public-ip-address"></a>パブリック IP アドレスの作成

[az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create) を使用して、bastion ホストのパブリック IP アドレスを作成します。

* **myBastionIP** という名前の Standard ゾーン冗長パブリック IP アドレスを作成します。
* 作成先のリソース グループは **CreateIntLBQS-rg** とします。

```azurecli-interactive
az network public-ip create \
    --resource-group CreateIntLBQS-rg \
    --name myBastionIP \
    --sku Standard
```
### <a name="create-a-bastion-subnet"></a>bastion サブネットを作成する

[az network vnet subnet create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) を使用して、bastion サブネットを作成します。

* 名前は **AzureBastionSubnet** にします。
* アドレス プレフィックスは **10.1.1.0/24** にします。
* 仮想ネットワークは **myVNet** にします。
* 作成先のリソース グループは **CreateIntLBQS-rg** とします。

```azurecli-interactive
az network vnet subnet create \
    --resource-group CreateIntLBQS-rg \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.1.1.0/24
```

### <a name="create-bastion-host"></a>bastion ホストを作成する

[az network bastion create](/cli/azure/network/bastion#az-network-bastion-create) を使用して、bastion ホストを作成します。

* 名前: **myBastionHost**
* 作成先のリソース グループは **CreateIntLBQS-rg** とします。
* パブリック IP **myBastionIP** に関連付けます。
* 仮想ネットワーク **myVNet** に関連付けます。
* 場所は **eastus** にします。

```azurecli-interactive
az network bastion create \
    --resource-group CreateIntLBQS-rg \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus
```

Azure Bastion ホストがデプロイされるまでに数分かかる場合があります。

### <a name="create-a-network-security-group"></a>ネットワーク セキュリティ グループの作成

Standard ロード バランサーの場合、バックエンドが扱う VM には、ネットワーク セキュリティ グループに属しているネットワーク インターフェイスが必要です。 

[az network nsg create](/cli/azure/network/nsg#az-network-nsg-create) を使用して、次のようにネットワーク セキュリティ グループを作成します。

* 名前は **myNSG** にします。
* 作成先のリソース グループは **CreateIntLBQS-rg** とします。

```azurecli-interactive
  az network nsg create \
    --resource-group CreateIntLBQS-rg \
    --name myNSG
```

### <a name="create-a-network-security-group-rule"></a>ネットワーク セキュリティ グループ規則を作成する

[az network nsg rule create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create) を使用して、次のようにネットワーク セキュリティ グループの規則を作成します。

* 名前は **myNSGRuleHTTP** にします。
* ネットワーク セキュリティ グループは、前の手順で作成した **myNSG** にします。
* 作成先のリソース グループは **CreateIntLBQS-rg** とします。
* プロトコルは **(*)** にします。
* 方向は **Inbound** にします。
* 送信元は **(*)** にします。
* 送信先は **(*)** にします。
* 送信先ポートは **ポート 80** にします。
* アクセスは **Allow** にします。
* 優先度は **200** にします。

```azurecli-interactive
  az network nsg rule create \
    --resource-group CreateIntLBQS-rg \
    --nsg-name myNSG \
    --name myNSGRuleHTTP \
    --protocol '*' \
    --direction inbound \
    --source-address-prefix '*' \
    --source-port-range '*' \
    --destination-address-prefix '*' \
    --destination-port-range 80 \
    --access allow \
    --priority 200
```

## <a name="create-backend-servers---basic"></a>バックエンド サーバーを作成する - Basic

このセクションでは、以下を作成します。

* 仮想マシンに使用する 3 つのネットワーク インターフェイス。
* 仮想マシンの可用性セット
* ロード バランサーのバックエンド サーバーとして使用する 3 つの仮想マシン。

### <a name="create-network-interfaces-for-the-virtual-machines"></a>仮想マシンのネットワーク インターフェイスを作成する

[az network nic create](/cli/azure/network/nic#az-network-nic-create) を使用して、次のように 3 つのネットワーク インターフェイスを作成します。

* それぞれ名前は **myNicVM1**、**myNicVM2**、**myNicVM3** とします。
* 作成先のリソース グループは **CreateIntLBQS-rg** とします。
* 仮想ネットワークは **myVNet** にします。
* サブネットは **myBackendSubnet** にします。
* ネットワーク セキュリティ グループは **myNSG** にします。

```azurecli-interactive
  array=(myNicVM1 myNicVM2 myNicVM3)
  for vmnic in "${array[@]}"
  do
    az network nic create \
        --resource-group CreateIntLBQS-rg \
        --name $vmnic \
        --vnet-name myVNet \
        --subnet myBackEndSubnet \
        --network-security-group myNSG
  done
```

### <a name="create-availability-set-for-virtual-machines"></a>仮想マシンの可用性セットを作成する

[az vm availability-set create](/cli/azure/vm/availability-set#az-vm-availability-set-create) を使用して、次のように可用性セットを作成します。

* 名前は **myAvailabilitySet** にします。
* 作成先のリソース グループは **CreateIntLBQS-rg** とします。
* 場所は **eastus** にします。

```azurecli-interactive
  az vm availability-set create \
    --name myAvailabilitySet \
    --resource-group CreateIntLBQS-rg \
    --location eastus 
    
```

### <a name="create-virtual-machines"></a>仮想マシンを作成する

[az vm create](/cli/azure/vm#az-vm-create) を使用して、次のように仮想マシンを作成します。

* それぞれの名前は **myVM1**、 **myVM2**、 **myVM3** とします。
* 作成先のリソース グループは **CreateIntLBQS-rg** とします。
* ネットワーク インターフェイス **myNicVM1**、**myNicVM2**、および **myNicVM3** に接続します。
* 仮想マシン イメージは **win2019datacenter** にします。
* **myAvailabilitySet** 内です。


```azurecli-interactive
  array=(1 2 3)
  for n in "${array[@]}"
  do
    az vm create \
    --resource-group CreateIntLBQS-rg \
    --name myVM$n \
    --nics myNicVM$n \
    --image win2019datacenter \
    --admin-username azureuser \
    --availability-set myAvailabilitySet \
    --no-wait
  done
```
VM がデプロイされるまでに、数分かかる場合があります。

## <a name="create-basic-load-balancer"></a>Basic ロード バランサーを作成する

このセクションでは、ロード バランサーの以下のコンポーネントを作成および構成する方法について説明します。

  * ロード バランサーの着信ネットワーク トラフィックを受け取るフロントエンド IP プール。
  * フロントエンド プールから負荷分散されたネットワーク トラフィックが送信されるバックエンド IP プール。
  * バックエンド VM インスタンスの正常性を判断する正常性プローブ。
  * VM に対するトラフィックの分散方法を定義するロード バランサー規則。

### <a name="create-the-load-balancer-resource"></a>ロード バランサーのリソースを作成する

[az network lb create](/cli/azure/network/lb#az-network-lb-create) を使用して、次のようにパブリック ロード バランサーを作成します。

* 名前は **myLoadBalancer** にします。
* フロントエンド プールの名前は **myFrontEnd** にします。
* バックエンド プールの名前は **myBackEndPool** にします。
* 仮想ネットワーク **myVNet** に関連付けます。
* バックエンド サブネット **myBackendSubnet** に関連付けます。

```azurecli-interactive
  az network lb create \
    --resource-group CreateIntLBQS-rg \
    --name myLoadBalancer \
    --sku Basic \
    --vnet-name myVNet \
    --subnet myBackendSubnet \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool
```

### <a name="create-the-health-probe"></a>正常性プローブを作成する

正常性プローブは、すべての仮想マシン インスタンスを調べて、ネットワーク トラ	フィックを送信できるかどうかを確認します。 

プローブ チェックが失敗した仮想マシンは、ロード バランサーから削除されます。 障害が解決されると、仮想マシンがロード バランサーに再び追加されます。

[az network lb probe create](/cli/azure/network/lb/probe#az-network-lb-probe-create) を使用して、次のように正常性プローブを作成します。

* 仮想マシンの正常性を監視します。
* 名前は **myHealthProbe** にします。
* プロトコルは **TCP** にします。
* **ポート 80** を監視します。

```azurecli-interactive
  az network lb probe create \
    --resource-group CreateIntLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80
```

### <a name="create-the-load-balancer-rule"></a>ロード バランサー規則を作成する

ロード バランサー規則は、以下のものを定義します。

* 着信トラフィック用のフロントエンド IP 構成。
* トラフィックを受信するためのバックエンド IP プール。
* 必要な発信元ポートと宛先ポート。 

[az network lb rule create](/cli/azure/network/lb/rule#az-network-lb-rule-create) を使用して、次のようにロード バランサー規則を作成します。

* 名前は **myHTTPRule** にします
* フロントエンド プール **myFrontEnd** で **ポート 80** をリッスンします。
* **ポート 80** を使用して、負荷分散されたネットワーク トラフィックをバックエンド アドレス プール **myBackEndPool** に送信します。 
* 正常性プローブ **myHealthProbe** を使用します。
* プロトコルは **TCP** にします。
* アイドル タイムアウトは **15 分** とします。

```azurecli-interactive
  az network lb rule create \
    --resource-group CreateIntLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe \
    --idle-timeout 15 
```
### <a name="add-virtual-machines-to-load-balancer-backend-pool"></a>仮想マシンをロード バランサー バックエンド プールに追加する

[az network nic ip-config address-pool add](/cli/azure/network/nic/ip-config/address-pool#az-network-nic-ip-config-address-pool-add) を使用して、次のように仮想マシンをバックエンド プールに追加します。

* バックエンド アドレス プールは **myBackEndPool** にします。
* 作成先のリソース グループは **CreateIntLBQS-rg** とします。
* ネットワーク インターフェイス **myNicVM1**、**myNicVM2**、および **myNicVM3** に関連付けます。
* ロード バランサー **myLoadBalancer** に関連付けます。

```azurecli-interactive
  array=(VM1 VM2 VM3)
  for vm in "${array[@]}"
  do
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNic$vm \
   --resource-group CreateIntLBQS-rg \
   --lb-name myLoadBalancer
  done

```
---

## <a name="test-the-load-balancer"></a>ロード バランサーをテストする

### <a name="create-test-virtual-machine"></a>テスト用の仮想マシンを作成する

[az network nic create](/cli/azure/network/nic#az-network-nic-create) を使用して、ネットワーク インターフェイスを作成します。

* 名前は **myNicTestVM** にします。
* 作成先のリソース グループは **CreateIntLBQS-rg** とします。
* 仮想ネットワークは **myVNet** にします。
* サブネットは **myBackendSubnet** にします。
* ネットワーク セキュリティ グループは **myNSG** にします。

```azurecli-interactive
  az network nic create \
    --resource-group CreateIntLBQS-rg \
    --name myNicTestVM \
    --vnet-name myVNet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```
[az vm create](/cli/azure/vm#az-vm-create) を使用して、仮想マシンを作成します。

* 名前は **myTestVM** にします。
* 作成先のリソース グループは **CreateIntLBQS-rg** とします。
* ネットワーク インターフェイス **myNicTestVM** に接続します。
* 仮想マシン イメージは **Win2019Datacenter** にします。

```azurecli-interactive
  az vm create \
    --resource-group CreateIntLBQS-rg \
    --name myTestVM \
    --nics myNicTestVM \
    --image Win2019Datacenter \
    --admin-username azureuser \
    --no-wait
```
仮想マシンがデプロイされるまでに、数分かかる場合があります。

## <a name="install-iis"></a>IIS のインストール

仮想マシンに IIS をインストールし、既定の Web サイトをコンピューター名に設定するには、[az vm extension set](/cli/azure/vm/extension#az_vm_extension_set) を使用します。

```azurecli-interactive
  array=(myVM1 myVM2 myVM3)
    for vm in "${array[@]}"
    do
     az vm extension set \
       --publisher Microsoft.Compute \
       --version 1.8 \
       --name CustomScriptExtension \
       --vm-name $vm \
       --resource-group CreateIntLBQS-rg \
       --settings '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}'
  done

```

### <a name="test"></a>テスト

1. Azure portal に[サインイン](https://portal.azure.com)します。

2. **[概要]** 画面で、ロード バランサーのプライベート IP アドレスを見つけます。 左側のメニューで **[すべてのサービス]** を選択し、 **[すべてのリソース]** を選択して、**myLoadBalancer** を選択します。

3. **myLoadBalancer** の **[概要]** で、 **[プライベート IP アドレス]** の横にあるアドレスを書き留めるか、コピーしておきます。

4. 左側のメニューで **[すべてのサービス]** を選択し、 **[すべてのリソース]** を選択して、リソースの一覧から **CreateIntLBQS-rg** リソース グループにある **myTestVM** を選択します。

5. **[概要]** ページで **[接続]** 、 **[要塞]** の順に選択します。

6. VM 作成時に入力したユーザー名とパスワードを入力します。

7. **myTestVM** で **Internet Explorer** 開きます。

8. 前の手順の IP アドレスをブラウザーのアドレス バーに入力します。 IIS Web サーバーの既定のページがブラウザーに表示されます。

    :::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/load-balancer-test.png" alt-text="標準の内部ロード バランサーを作成する" border="true":::
   
ロード バランサーが 3 つの VM すべてにトラフィックを分散していることを確認するには、各 VM の IIS Web サーバーの既定のページをカスタマイズした後、クライアント マシンで Web ブラウザーを強制的に最新の情報に更新します。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

必要がなくなったら、[az group delete](/cli/azure/group#az-group-delete) コマンドを使用して、リソース グループ、ロード バランサー、およびすべての関連リソースを削除します。

```azurecli-interactive
  az group delete \
    --name CreateIntLBQS-rg
```

## <a name="next-steps"></a>次のステップ

このクイック スタートでは次のようにします。

* Standard またはパブリック ロード バランサーを作成しました
* 仮想マシンを接続しました。 
* ロード バランサーのトラフィック規則と正常性プローブを構成しました。
* ロード バランサーをテストしました。

Azure Load Balancer についてさらに学習するには、次の記事に進んでください。
> [!div class="nextstepaction"]
> [Azure Load Balancer の概要](load-balancer-overview.md)