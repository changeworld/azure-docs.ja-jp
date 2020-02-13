---
title: PowerShell を使用した Azure スポット VM のデプロイ
description: Azure PowerShell を使用してスポット VM をデプロイし、コストを節約する方法について学びます。
services: virtual-machines-windows
author: cynthn
manager: gwallace
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: article
ms.date: 02/11/2020
ms.author: cynthn
ms.openlocfilehash: 17186d1d7b50ea872dc47eca8c2c4491787d2a38
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/12/2020
ms.locfileid: "77158946"
---
# <a name="preview-deploy-spot-vms-using-azure-powershell"></a>プレビュー:Azure PowerShell を使用してスポット VM をデプロイする


[スポット VM](spot-vms.md) を使うと、非常に低コストで未使用の容量を利用できます。 Azure で容量の回復が必要になると常に、Azure インフラストラクチャはスポット VM を削除します。 したがって、スポット VM は、バッチ処理ジョブ、開発/テスト環境、大規模なコンピューティング ワークロードなど、中断してもかまわないワークロードに最適です。

スポット VM の価格は、リージョンと SKU に基づいて変化します。 詳細については、[Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) と [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) の VM 価格を参照してください。 最大価格の設定の詳細については、[スポット VM の価格](spot-vms.md#pricing)に関するページを参照してください。

VM に対して、1 時間あたりに支払うことができる最大価格を設定するオプションがあります。 スポット VM の最大価格は、小数点以下最大 5 桁を使用して米ドル (USD) で設定できます。 たとえば、`0.98765` の値は、1 時間あたり $0.98765 米ドルの最大価格になります。 最大価格を `-1` に設定した場合、VM は価格に基づいて削除されません。 VM の価格は、使用可能な容量とクォータがある限り、現在のスポットの価格または標準 VM の価格のいずれか低い方になります。

> [!IMPORTANT]
> スポット インスタンスは現在、パブリック プレビューの段階にあります。
> このプレビュー バージョンは運用環境のワークロードにはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。
>



## <a name="create-the-vm"></a>VM の作成

構成を作成するための [New-AzVmConfig](/powershell/module/az.compute/new-azvmconfig) を使用してスポット VM を作成します。 `-Priority Spot` を追加し、`-MaxPrice` を次のいずれかに設定します。
- `-1`。この場合、VM は価格に基づいて削除されません。
- 最大 5 桁の金額 (ドル)。 たとえば、`-MaxPrice .98765` は、スポット VM の価格が 1 時間あたり $0.98765 になると VM の割り当てが解除されることを意味します。


この例では、価格に基づいて割り当てを解除されないスポット VM を作成します (Azure で容量の回復が必要な場合のみ)。

```azurepowershell-interactive
$resourceGroup = "mySpotRG"
$location = "eastus"
$vmName = "mySpotVM"
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."
New-AzResourceGroup -Name $resourceGroup -Location $location
$subnetConfig = New-AzVirtualNetworkSubnetConfig `
   -Name mySubnet -AddressPrefix 192.168.1.0/24
$vnet = New-AzVirtualNetwork -ResourceGroupName $resourceGroup `
   -Location $location -Name MYvNET -AddressPrefix 192.168.0.0/16 `
   -Subnet $subnetConfig
$pip = New-AzPublicIpAddress -ResourceGroupName $resourceGroup -Location $location `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389 -Access Allow
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location $location `
  -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP
$nic = New-AzNetworkInterface -Name myNic -ResourceGroupName $resourceGroup -Location $location `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration and set this to be a Spot VM

$vmConfig = New-AzVMConfig -VMName $vmName -VMSize Standard_D1 -Priority "Spot" -MaxPrice -1| `
Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred | `
Set-AzVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2016-Datacenter -Version latest | `
Add-AzVMNetworkInterface -Id $nic.Id

New-AzVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig
```

VM が作成されたら、クエリを実行して、リソース グループ内のすべての VM の最大価格を確認できます。

```azurepowershell-interactive
Get-AzVM -ResourceGroupName $resourceGroup | `
   Select-Object Name,@{Name="maxPrice"; Expression={$_.BillingProfile.MaxPrice}}
```

## <a name="next-steps"></a>次のステップ

[Azure CLI](../linux/spot-cli.md) か[テンプレート](../linux/spot-template.md)を使用してスポット VM を作成することもできます。

エラーが発生した場合は、[エラー コード](../error-codes-spot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)を参照してください。