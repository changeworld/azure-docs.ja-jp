---
title: 静的パブリック IP アドレスを持つ VM を作成する - Azure PowerShell
titlesuffix: Azure Virtual Network
description: Azure PowerShell を使用して静的パブリック IP アドレスを持つ仮想マシン (VM) を作成します。 静的パブリック IP アドレスは、変更されないアドレスです。
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 10/01/2021
ms.custom: template-how-to
ms.openlocfilehash: 60c9d4827a83475b56abcc769bb1fa7f68467a24
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2021
ms.locfileid: "129367778"
---
# <a name="create-a-virtual-machine-with-a-static-public-ip-address-using-azure-powershell"></a>Azure PowerShell を使用して静的パブリック IP アドレスを持つ仮想マシンを作成する

この記事では、静的パブリック IP アドレスで VM を作成します。 パブリック IP アドレスを使用すると、インターネットから仮想マシンに通信できます。 動的アドレスの代わりに静的パブリック IP アドレスを割り当てることで、アドレスの変更がないことが保証されます。 

パブリック IP アドレスには、[わずかながら料金が発生](https://azure.microsoft.com/pricing/details/ip-addresses)します。 サブスクリプションごとに使用できるパブリック IP アドレスの数には[制限](../../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)があります。

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

## <a name="create-a-public-ip-address"></a>パブリック IP アドレスの作成

[New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) を使用して、標準のパブリック IPv4 アドレスを作成します。

次のコマンドでは、**myPublicIP** という名前のゾーン冗長パブリック IP アドレスを **myResourceGroup** に作成します。

```azurepowershell-interactive
## Create IP. ##
$ip = @{
    Name = 'myPublicIP'
    ResourceGroupName = 'myResourceGroup'
    Location = 'eastus2'
    Sku = 'Standard'
    AllocationMethod = 'Static'
    IpAddressVersion = 'IPv4'
    Zone = 1,2,3   
}
New-AzPublicIpAddress @ip
```
## <a name="create-a-virtual-machine"></a>仮想マシンの作成

[New-AzVM](/powershell/module/az.Compute/new-azvm) を使用して、仮想マシンを作成します。 

次のコマンドでは、Windows Server の仮想マシンを作成します。 前に作成したパブリック IP アドレスの名前を **`-PublicIPAddressName`** パラメーターに入力します。 メッセージが表示されたら、仮想マシンの資格情報として使用するユーザー名とパスワードを入力します。

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

パブリック IP の SKU の詳細については、[パブリック IP アドレス SKU](public-ip-addresses.md#sku) に関するページを参照してください。 仮想マシンは Azure Load Balancer のバックエンド プールに追加できます。 パブリック IP アドレスの SKU は、ロード バランサーのパブリック IP の SKU に一致する必要があります。 詳細については、「 [Azure ロード バランサー](../../load-balancer/skus.md)」を参照してください。

[Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) を使用して、割り当てられたパブリック IP アドレスを表示し、それが静的アドレスとして作成されたことを確認します。

```azurepowershell-interactive
## Retrieve public IP address settings. ##
$ip = @{
    Name = 'myPublicIP'
    ResourceGroupName = 'myResourceGroup'
}
Get-AzPublicIpAddress @ip | Select "IpAddress","PublicIpAllocationMethod" | Format-Table

```

> [!WARNING]
> 仮想マシンのオペレーティング システム内の IP アドレス設定は変更しないでください。 オペレーティング システムは、Azure のパブリック IP アドレスを認識しません。 プライベート IP アドレスの設定をオペレーティング システムに追加できますが、必要でない限り、この操作は行わないことをお勧めします。必要な場合は、[オペレーティング システムへのプライベート IP アドレスの追加](virtual-network-network-interface-addresses.md#private)に関する記事を読んだ後で行うことをお勧めします。

[!INCLUDE [ephemeral-ip-note.md](../../../includes/ephemeral-ip-note.md)]

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