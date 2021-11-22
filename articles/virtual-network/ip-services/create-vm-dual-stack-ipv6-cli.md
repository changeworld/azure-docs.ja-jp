---
title: デュアルスタック ネットワークと共に Azure 仮想マシンを作成する - Azure CLI
titleSuffix: Azure Virtual Network
description: この記事では、Azure CLI を使用して、Azure 内でデュアルスタック仮想ネットワークと共に仮想マシンを作成する方法について説明します。
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 11/11/2021
ms.custom: template-how-to
ms.openlocfilehash: ae8f7fef465b8ba42a058b43ca95fd2334f437c8
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2021
ms.locfileid: "132495257"
---
# <a name="create-an-azure-virtual-machine-with-a-dual-stack-network-using-the-azure-cli"></a>Azure CLI を使用して、デュアルスタック仮想ネットワークと共に Azure 仮想マシンを作成する

この記事では、Azure CLI を使用して Azure 内に仮想マシンを作成します。 デュアルスタック ネットワークと共に仮想マシンが手順の一部として作成されます。  完了すると、仮想マシンでは IPv4 と IPv6 の通信がサポートされます。  

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料で作成できます](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

- このチュートリアルには、Azure CLI のバージョン 2.0.28 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。

## <a name="create-a-resource-group"></a>リソース グループを作成する

Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。

[az group create](/cli/azure/group#az_group_create) を使用して、**myResourceGroup** という名前のリソース グループを場所 **eastus2** に作成します。

```azurecli-interactive
  az group create \
    --name myResourceGroup \
    --location eastus2
```

## <a name="create-a-virtual-network"></a>仮想ネットワークの作成

このセクションでは、仮想マシン用のデュアルスタック仮想ネットワークを作成します。

仮想ネットワークを作成するには、[az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) を使用します。

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroup \
    --location eastus2 \
    --name myVNet \
    --address-prefixes 10.0.0.0/16 2404:f800:8000:122::/63 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.0.0.0/24 2404:f800:8000:122::/64
```

## <a name="create-public-ip-addresses"></a>パブリック IP アドレスを作成する

このセクションでは、IPv4 と IPv6 の 2 つのパブリック IP アドレスを作成します。 

パブリック IP アドレスを作成するには、[az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) を使用します。

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroup \
    --name myPublicIP-Ipv4 \
    --sku Standard \
    --version IPv4

  az network public-ip create \
    --resource-group myResourceGroup \
    --name myPublicIP-Ipv6 \
    --sku Standard \
    --version IPv6

```
## <a name="create-a-network-security-group"></a>ネットワーク セキュリティ グループの作成

このセクションでは、仮想マシンと仮想ネットワークのネットワーク セキュリティ グループを作成します。

ネットワーク セキュリティ グループを作成するには、[az network nsg create](/cli/azure/network/nsg#az_network_nsg_create) を使用します。

```azurecli-interactive
  az network nsg create \
    --resource-group myResourceGroup \
    --name myNSG
```

### <a name="create-network-security-group-rules"></a>ネットワーク セキュリティ グループ規則を作成する

SSH に対してポート 22 上で仮想マシンへの接続を許可する規則を作成します。 追加の規則を作成すれば、アウトバウンド接続に対してすべてのポートを許可することができます。

ネットワーク セキュリティ グループの規則を作成するには、[az network nsg rule create](/cli/azure/network/nsg/rule#az_network_nsg_rule_create) を使用します。

```azurecli-interactive
  az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNSG \
    --name myNSGRuleSSH \
    --protocol '*' \
    --direction inbound \
    --source-address-prefix '*' \
    --source-port-range '*' \
    --destination-address-prefix '*' \
    --destination-port-range 22 \
    --access allow \
    --priority 200

  az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNSG \
    --name myNSGRuleAllOUT \
    --protocol '*' \
    --direction outbound \
    --source-address-prefix '*' \
    --source-port-range '*' \
    --destination-address-prefix '*' \
    --destination-port-range '*' \
    --access allow \
    --priority 200
```

## <a name="create-virtual-machine"></a>仮想マシンの作成

このセクションでは、仮想マシンとそのサポート リソースを作成します。

### <a name="create-network-interface"></a>ネットワーク インターフェイスを作成する

仮想マシンのネットワーク インターフェイスを作成するには、[az network nic create](/cli/azure/network/nic#az_network_nic_create)を使用します。 以前に作成したパブリック IP アドレスと NSG は、NIC に関連付けられています。 ネットワーク インターフェイスは、前に作成した仮想ネットワークに接続されています。

```azurecli-interactive
  az network nic create \
    --resource-group myResourceGroup \
    --name myNIC1 \
    --vnet-name myVNet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG \
    --public-ip-address myPublicIP-IPv4
```

### <a name="create-ipv6-ip-configuration"></a>IPv6 IP 構成を作成する

NIC に対して IPv6 構成を作成するには、[az network nic ip-config create](/cli/azure/network/nic/ip-config#az_network_nic_ip_config_create) を使用します。

```azurecli-interactive
  az network nic ip-config create \
    --resource-group myResourceGroup \
    --name myIPv6config \
    --nic-name myNIC1 \
    --private-ip-address-version IPv6 \
    --vnet-name myVNet \
    --subnet myBackendSubnet \
    --public-ip-address myPublicIP-IPv6
```

### <a name="create-vm"></a>VM を作成する

仮想マシンを作成するには、[az vm create](/cli/azure/vm#az_vm_create) を使用します。

```azurecli-interactive
  az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --nics myNIC1 \
    --image UbuntuLTS \
    --admin-username azureuser \
    --authentication-type ssh \
    --generate-ssh-keys
```

## <a name="test-ssh-connection"></a>SSH 接続をテストする

仮想マシンの IP アドレスを表示するには、[az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show) を使用します。

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroup \
    --name myPublicIP-IPv4 \
    --query ipAddress \
    --output tsv
```

```bash
user@Azure:~$ az network public-ip show \
>     --resource-group myResourceGroup \
>     --name myPublicIP-IPv4 \
>     --query ipAddress \
>     --output tsv
20.119.201.208
```

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroup \
    --name myPublicIP-IPv6 \
    --query ipAddress \
    --output tsv
```

```bash
user@Azure:~$ az network public-ip show \
>     --resource-group myResourceGroup \
>     --name myPublicIP-IPv6 \
>     --query ipAddress \
>     --output tsv
2603:1030:408:6::9d
```

次のコマンドを使用して、仮想マシンへの SSH 接続を開きます。 IP アドレスを自分の仮想マシンの IP アドレスに置き換えます。

```bash
  ssh azureuser@20.119.201.208
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

必要がなくなったら、[az group delete](/cli/azure/group#az_group_delete) コマンドを使用して、リソース グループ、仮想マシン、およびすべての関連リソースを削除します。

```azurecli-interactive
  az group delete \
    --name myResourceGroup
```

## <a name="next-steps"></a>次の手順

この記事では、Azure 仮想マシンと共にデュアルスタック ネットワークを作成する方法について説明しました。

Azure 内の IPv6 および IP アドレスの詳細については、以下の記事を参照してください。

- [Azure Virtual Network の IPv6 の概要。](ipv6-overview.md)

- [Azure Virtual Network IP サービスとは](ip-services-overview.md)


