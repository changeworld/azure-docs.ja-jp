---
title: 静的プライベート IP アドレスを持つ VM を作成する - Azure PowerShell
description: Azure PowerShell を使用して静的プライベート IP アドレスを持つ仮想マシンを作成する方法を説明します。
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 10/01/2021
ms.custom: template-how-to
ms.openlocfilehash: a98fab6f07e2616e8f3732f28446c24e9051bf09
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2021
ms.locfileid: "129367674"
---
# <a name="create-a-virtual-machine-with-a-static-private-ip-address-using-azure-powershell"></a>Azure PowerShell を使用して静的プライベート IP アドレスを持つ仮想マシンを作成する

仮想マシン (VM) には、指定された範囲のプライベート IP アドレスが自動的に割り当てられます。 この範囲は、VM がデプロイされているサブネットに基づいています。 VM では、その VM が削除されるまでこのアドレスが保持されます。 Azure は、次に VM に作成されているサブネットから使用可能なプライベート IP アドレスを自動的に割り当てます。 サブネットで特定の IP アドレスが必要な場合は、VM に静的 IP アドレスを割り当てます。

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- ローカルにインストールされた Azure PowerShell または Azure Cloud Shell

PowerShell をインストールしてローカルで使用する場合、この記事では Azure PowerShell モジュール バージョン 5.4.1 以降が必要になります。 インストールされているバージョンを確認するには、`Get-Module -ListAvailable Az` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-Az-ps)に関するページを参照してください。 PowerShell をローカルで実行している場合、`Connect-AzAccount` を実行して Azure との接続を作成することも必要です。

## <a name="create-a-resource-group"></a>リソース グループを作成する

Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) を使用して **myResourceGroup** という名前のリソース グループを **eastus2** に作成します。

```azurepowershell-interactive
$rg =@{
    Name = 'myResourceGroup'
    Location = 'eastus2'
}
New-AzResourceGroup @rg

```

## <a name="create-a-virtual-machine"></a>仮想マシンの作成

[New-AzVM](/powershell/module/az.compute/new-azvm) を使って、仮想マシンを作成します。 

次のコマンドでは、Windows Server の仮想マシンを作成します。 メッセージが表示されたら、仮想マシンの資格情報として使用するユーザー名とパスワードを入力します。

```azurepowershell-interactive
## Create virtual machine. ##
$vm = @{
    ResourceGroupName = 'myResourceGroup'
    Location = 'East US 2'
    Name = 'myVM'
    PublicIpAddressName = 'myPublicIP'
}
New-AzVM @vm
```

## <a name="change-private-ip-address-to-static"></a>プライベート IP アドレスを静的に変更する

このセクションでは、以前に作成した仮想マシンのプライベート IP アドレスを "**動的**" から "**静的**" に変更します。 

[Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) を使用して、仮想ネットワーク構成を変数に配置します。 [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) を使用して、サブネット構成を変数に配置します。 [Get-AzNetworkInterface](/powershell/module/az.network/get-aznetworkinterface) を使用してネットワーク インターフェイスの構成を取得し、変数に配置します。 [Set-AzNetworkInterfaceIpConfig](/powershell/module/az.network/set-aznetworkinterfaceipconfig) を使用して、ネットワーク インターフェイスの構成を設定します。 最後に、[Set-AzNetworkInterface](/powershell/module/az.network/set-aznetworkinterface) を使用して、仮想マシンの構成を設定します。

次のコマンドでは、仮想マシンのプライベート IP アドレスを静的に変更します。

```azurepowershell-interactive
## Place virtual network configuration into a variable. ##
$net = @{
    Name = 'myVM'
    ResourceGroupName = 'myResourceGroup'
}
$vnet = Get-AzVirtualNetwork @net

## Place subnet configuration into a variable. ##
$sub = @{
    Name = 'myVM'
    VirtualNetwork = $vnet
}
$subnet = Get-AzVirtualNetworkSubnetConfig @sub

## Get name of network interface and place into a variable ##
$int1 = @{
    Name = 'myVM'
    ResourceGroupName = 'myResourceGroup'
}
$vm = Get-AzVM @int1

## Place network interface configuration into a variable. ##
$nic = Get-AzNetworkInterface -ResourceId $vm.NetworkProfile.NetworkInterfaces.Id

## Set interface configuration. ##
$config =@{
    Name = 'myVM'
    PrivateIpAddress = '192.168.1.4'
    Subnet = $subnet
}
$nic | Set-AzNetworkInterfaceIpConfig @config -Primary

## Save interface configuration. ##
$nic | Set-AzNetworkInterface
```

> [!WARNING]
> プライベート IP アドレスの設定をオペレーティング システムに追加できますが、この操作は、[オペレーティング システムへのプライベート IP アドレスの追加](virtual-network-network-interface-addresses.md#private)に関する記事を読んだ後に行うことをお勧めします。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

必要なくなったら、[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) を使用して、リソース グループとその中のすべてのリソースを削除できます。

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>次のステップ

- Azure の[パブリック IP アドレス](public-ip-addresses.md#public-ip-addresses)について詳しく学習する。
- すべての[パブリック IP アドレスの設定](virtual-network-public-ip-address.md#create-a-public-ip-address)について詳しく学習する。
- [プライベート IP アドレス](private-ip-addresses.md)と Azure 仮想マシンへの[静的プライベート IP アドレス](virtual-network-network-interface-addresses.md#add-ip-addresses)の割り当ての詳細を確認します。
- [Linux](../../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) と [Windows](../../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) の仮想マシンの作成の詳細を確認します。