---
title: 静的プライベート IP アドレスを持つ VM を作成する - Azure CLI
description: Azure CLI を使用して静的プライベート IP アドレスを持つ仮想マシンを作成する方法を説明します。
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 10/01/2021
ms.custom: template-how-to
ms.openlocfilehash: 1129b4cf06a2de01438f2c83500eee84240183fc
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2021
ms.locfileid: "129705270"
---
# <a name="create-a-virtual-machine-with-a-static-private-ip-address-using-the-azure-cli"></a>Azure CLI を使用して静的プライベート IP アドレスを持つ仮想マシンを作成する

仮想マシン (VM) には、指定された範囲のプライベート IP アドレスが自動的に割り当てられます。 この範囲は、VM がデプロイされているサブネットに基づいています。 VM では、その VM が削除されるまでこのアドレスが保持されます。 Azure は、次に VM に作成されているサブネットから使用可能なプライベート IP アドレスを自動的に割り当てます。 サブネットで特定の IP アドレスが必要な場合は、VM に静的 IP アドレスを割り当てます。

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

## <a name="create-a-virtual-machine"></a>仮想マシンを作成する

[az vm create](/cli/azure/vm#az_vm_create) を使用して仮想マシンを作成します。 

次のコマンドでは、Windows Server の仮想マシンを作成します。 メッセージが表示されたら、仮想マシンの資格情報として使用するユーザー名とパスワードを入力します。

```azurecli-interactive
  az vm create \
    --name myVM \
    --resource-group myResourceGroup \
    --public-ip-address myPublicIP \
    --public-ip-sku Standard \
    --size Standard_A2 \
    --image MicrosoftWindowsServer:WindowsServer:2019-Datacenter:latest \
    --admin-username azureuser
```

## <a name="change-private-ip-address-to-static"></a>プライベート IP アドレスを静的に変更する

このセクションでは、以前に作成した仮想マシンのプライベート IP アドレスを "**動的**" から "**静的**" に変更します。 

[az network nic ip-config update](/cli/azure/network/nic/ip-config#az_network_nic_ip_config_update) を使用して、ネットワーク インターフェイスの構成を更新します。

次のコマンドでは、仮想マシンのプライベート IP アドレスを静的に変更します。

```azurecli-interactive
  az network nic ip-config update \
    --name ipconfigmyVM \
    --resource-group myResourceGroup \
    --nic-name myVMVMNic \
    --private-ip-address 10.0.0.4
```

> [!WARNING]
> プライベート IP アドレスの設定をオペレーティング システムに追加できますが、この操作は、[オペレーティング システムへのプライベート IP アドレスの追加](virtual-network-network-interface-addresses.md#private)に関する記事を読んだ後に行うことをお勧めします。

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
