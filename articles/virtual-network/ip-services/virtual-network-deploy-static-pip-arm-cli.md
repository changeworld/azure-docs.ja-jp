---
title: 静的パブリック IP アドレスを持つ VM を作成する - Azure CLI
titlesuffix: Azure Virtual Network
description: Azure CLI を使用して静的パブリック IP アドレスを持つ仮想マシン (VM) を作成します。 静的パブリック IP アドレスは、変更されないアドレスです。
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 10/01/2021
ms.custom: template-how-to
ms.openlocfilehash: 6ea60d6b2784972f2a4e3210ad815c4823a27a19
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2021
ms.locfileid: "129367780"
---
# <a name="create-a-virtual-machine-with-a-static-public-ip-address-using-the-azure-cli"></a>Azure CLI を使用して静的パブリック IP アドレスを持つ仮想マシンを作成する

この記事では、静的パブリック IP アドレスを持つ VM を作成します。 パブリック IP アドレスを使用して、インターネットから仮想マシンへの通信を行うことができます。 動的アドレスの代わりに静的パブリック IP アドレスを割り当てることで、アドレスの変更がないことが保証されます。 

パブリック IP アドレスには、[わずかながら料金が発生](https://azure.microsoft.com/pricing/details/ip-addresses)します。 サブスクリプションごとに使用できるパブリック IP アドレスの数には[制限](../../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)があります。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- このチュートリアルには、Azure CLI のバージョン 2.0.28 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。

## <a name="create-a-resource-group"></a>リソース グループを作成する

Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。

[az group create](/cli/azure/group#az_group_create) を使用して、**myResourceGroup** という名前のリソース グループを場所 **eastus2** に作成します。

```azurecli-interactive
  az group create \
    --name myResourceGroup \
    --location eastus2
```

## <a name="create-a-public-ip-address"></a>パブリック IP アドレスの作成

標準のパブリック IPv4 アドレスを作成するには、[az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) を使用します。

次のコマンドでは、**myPublicIP** という名前のゾーン冗長パブリック IP アドレスを **myResourceGroup** に作成します。

```azurecli-interactive
az network public-ip create \
    --resource-group myResourceGroup \
    --name myPublicIP \
    --version IPv4 \
    --sku Standard \
    --zone 1 2 3
```
## <a name="create-a-virtual-machine"></a>仮想マシンの作成

[az vm create](/cli/azure/vm#az_vm_create) を使用して仮想マシンを作成します。 

次のコマンドでは、Windows Server の仮想マシンを作成します。 前に作成したパブリック IP アドレスの名前を **`-PublicIPAddressName`** パラメーターに入力します。 メッセージが表示されたら、仮想マシンの資格情報として使用するユーザー名とパスワードを入力します。

```azurecli-interactive
  az vm create \
    --name myVM \
    --resource-group TutorVMRoutePref-rg \
    --public-ip-address myPublicIP \
    --size Standard_A2 \
    --image MicrosoftWindowsServer:WindowsServer:2019-Datacenter:latest \
    --admin-username azureuser
```

パブリック IP の SKU の詳細については、[パブリック IP アドレスの SKU](public-ip-addresses.md#sku) に関するページを参照してください。 仮想マシンは Azure Load Balancer のバックエンド プールに追加できます。 パブリック IP アドレスの SKU は、ロード バランサーのパブリック IP の SKU に一致する必要があります。 詳細については、「 [Azure ロード バランサー](../../load-balancer/skus.md)」を参照してください。

[az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show) を使用して、割り当てられたパブリック IP アドレスを表示し、それが静的アドレスとして作成されたことを確認します。

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroup \
    --name myPublicIP \
    --query [ipAddress,publicIpAllocationMethod,sku] \
    --output table
```

> [!WARNING]
> 仮想マシンのオペレーティング システム内の IP アドレス設定は変更しないでください。 オペレーティング システムは、Azure のパブリック IP アドレスを認識しません。 プライベート IP アドレスの設定をオペレーティング システムに追加できますが、必要でない限り、この操作は行わないことをお勧めします。必要な場合は、[オペレーティング システムへのプライベート IP アドレスの追加](virtual-network-network-interface-addresses.md#private)に関する記事を読んだ後で行うことをお勧めします。

[!INCLUDE [ephemeral-ip-note.md](../../../includes/ephemeral-ip-note.md)]

## <a name="clean-up-resources"></a>リソースをクリーンアップする

不要になったら、[az group delete](/cli/azure/group#az_group_delete) を使用して、リソース グループとそのグループに含まれているすべてのリソースを削除できます。

```azurecli-interactive
  az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>次のステップ

- Azure の[パブリック IP アドレス](public-ip-addresses.md#public-ip-addresses)について詳しく学習する。
- すべての[パブリック IP アドレスの設定](virtual-network-public-ip-address.md#create-a-public-ip-address)について詳しく学習する。
- [プライベート IP アドレス](private-ip-addresses.md)と Azure 仮想マシンへの[静的プライベート IP アドレス](virtual-network-network-interface-addresses.md#add-ip-addresses)の割り当ての詳細を確認します。
- [Linux](../../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) と [Windows](../../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) の仮想マシンの作成の詳細を確認します。