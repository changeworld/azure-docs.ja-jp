---
title: PowerShell を使用した Azure スポット仮想マシンのデプロイ
description: Azure PowerShell を使用して Azure スポット仮想マシンをデプロイし、コストを節約する方法について説明します。
author: cynthn
ms.service: virtual-machines
ms.subservice: spot
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 06/26/2020
ms.author: cynthn
ms.reviewer: jagaveer
ms.openlocfilehash: 33172004ac4361de51b92389fbf56bd699f7124f
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102096447"
---
# <a name="deploy-azure-spot-virtual-machines-using-azure-powershell"></a>Azure PowerShell を使用して Azure スポット仮想マシンをデプロイする


[Azure スポット仮想マシン](../spot-vms.md)を使用すると、非常に低コストで未使用の容量を利用できます。 Azure で容量の回復が必要になると常に、Azure インフラストラクチャによって Azure スポット仮想マシンが削除されます。 したがって、Azure スポット仮想マシンは、バッチ処理ジョブ、開発/テスト環境、大規模なコンピューティング ワークロードなど、中断してもかまわないワークロードに最適です。

Azure スポット仮想マシンの価格は、リージョンと SKU に基づいて変動します。 詳細については、[Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) と [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) の VM 価格を参照してください。 最大価格の設定の詳細については、[Azure スポット仮想マシンの価格](../spot-vms.md#pricing)に関するページを参照してください。

VM に対して、1 時間あたりに支払うことができる最大価格を設定するオプションがあります。 Azure スポット仮想マシンの最大価格は、小数点以下最大 5 桁を使用して米ドル (USD) で設定できます。 たとえば、`0.98765` の値は、1 時間あたり $0.98765 米ドルの最大価格になります。 最大価格を `-1` に設定した場合、VM は価格に基づいて削除されません。 VM の価格は、使用可能な容量とクォータがある限り、現在のスポットの価格または標準 VM の価格のいずれか低い方になります。


## <a name="create-the-vm"></a>VM の作成

構成を作成するための [New-AzVmConfig](/powershell/module/az.compute/new-azvmconfig) を使用してスポット VM を作成します。 `-Priority Spot` を追加し、`-MaxPrice` を次のいずれかに設定します。
- `-1`。この場合、VM は価格に基づいて削除されません。
- 最大 5 桁の金額 (ドル)。 たとえば、`-MaxPrice .98765` は、スポット VM の価格が 1 時間あたり $0.98765 になると VM の割り当てが解除されることを意味します。


この例では、価格に基づいて割り当てを解除されないスポット VM を作成します (Azure で容量の回復が必要な場合のみ)。 この VM の割り当てを解除するように削除ポリシーが設定されているため、後で再起動することができます。 VM が削除されるときに VM と基になるディスクを削除する場合は、`New-AzVMConfig` で `-EvictionPolicy` を `Delete` に設定します。


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

# Create a virtual machine configuration and set this to be an Azure Spot Virtual Machine

$vmConfig = New-AzVMConfig -VMName $vmName -VMSize Standard_D1 -Priority "Spot" -MaxPrice -1 -EvictionPolicy Deallocate | `
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

## <a name="simulate-an-eviction"></a>削除をシミュレートする

Azure スポット仮想マシンの[削除をシミュレート](/rest/api/compute/virtualmachines/simulateeviction)して、突然の削除に対するアプリケーションの応答をテストすることができます。 

次の情報をお客様の情報に置き換えてください。 

- `subscriptionId`
- `resourceGroupName`
- `vmName`


```rest
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}/simulateEviction?api-version=2020-06-01
```

`Response Code: 204` は、シミュレートされた削除が成功したことを意味します。 

## <a name="next-steps"></a>次のステップ

[Azure CLI](../linux/spot-cli.md)、[ポータル](../spot-portal.md)、または [テンプレート](../linux/spot-template.md)を使用して Azure スポット仮想マシンを作成することもできます。

Azure スポット仮想マシンの価格については、[Azure 小売価格 API](/rest/api/cost-management/retail-prices/azure-retail-prices) を使用して現在の価格情報を照会してください。 `meterName` と `skuName` の両方に `Spot` が含まれています。

エラーが発生した場合は、[エラー コード](../error-codes-spot.md)を参照してください。
