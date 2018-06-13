---
title: ゾーン Windows VM の作成 - Azure PowerShell | Microsoft Docs
description: Azure PowerShell を使用して可用性ゾーン内に Windows 仮想マシンを作成する
services: virtual-machines-windows
documentationcenter: virtual-machines
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/27/2018
ms.author: danlep
ms.custom: ''
ms.openlocfilehash: 5f76b117b01090864e1bf33e986e8ec96f0bf376
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/19/2018
ms.locfileid: "31599936"
---
# <a name="create-a-windows-virtual-machine-in-an-availability-zone-with-powershell"></a>PowerShell を使用して可用性ゾーン内に Windows 仮想マシンを作成する

この記事では、Azure PowerShell を使用して、Windows Server 2016 を実行する Azure 仮想マシンを Azure 可用性ゾーン内に作成する方法を詳しく説明します。 [可用性ゾーン](../../availability-zones/az-overview.md)とは、1 つの Azure リージョン内で物理的に分離されたゾーンのことです。 可用性ゾーンは、データセンター全体に及ぶ珍しい障害や損失からアプリとデータを保護するために使用します。

可用性ゾーンを使用するには、[サポートされている Azure リージョン](../../availability-zones/az-overview.md#regions-that-support-availability-zones)に仮想マシンを作成します。

最新の Azure PowerShell モジュールがインストールされていることを確認してください。 インストールまたはアップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-azurerm-ps)に関するページを参照してください。

## <a name="log-in-to-azure"></a>Azure にログインする

`Connect-AzureRmAccount` コマンドで Azure サブスクリプションにログインし、画面上の指示に従います。

```powershell
Connect-AzureRmAccount
```

## <a name="check-vm-sku-availability"></a>提供されている VM SKU の確認
提供されている VM サイズ (SKU) は、リージョンやゾーンによって異なる場合があります。 可用性ゾーンの使用計画を立てやすくするために、提供されている VM SKU を Azure リージョンとゾーンごとに一覧表示することができます。 そうすることで適切な VM サイズを選び、必要な回復性をすべてのゾーンにわたって確保することができます。 さまざまな VM の種類とサイズについて詳しくは、[VM サイズの概要](sizes.md)に関するページを参照してください。

提供されている VM SKU は、[Get-AzureRmComputeResourceSku](/powershell/module/azurerm.compute/get-azurermcomputeresourcesku) コマンドで確認できます。 次の例では、*eastus2* リージョンで提供されている VM SKU を一覧表示しています。

```powershell
Get-AzureRmComputeResourceSku | where {$_.Locations.Contains("eastus2")};
```

次に示したのは、その出力例の抜粋です。これは各 VM サイズが提供されている可用性ゾーンを確認できます。

```powershell
ResourceType                Name  Location      Zones   [...]
------------                ----  --------      -----
virtualMachines  Standard_DS1_v2   eastus2  {1, 2, 3}
virtualMachines  Standard_DS2_v2   eastus2  {1, 2, 3}
[...]
virtualMachines     Standard_F1s   eastus2  {1, 2, 3}
virtualMachines     Standard_F2s   eastus2  {1, 2, 3}
[...]
virtualMachines  Standard_D2s_v3   eastus2  {1, 2, 3}
virtualMachines  Standard_D4s_v3   eastus2  {1, 2, 3}
[...]
virtualMachines   Standard_E2_v3   eastus2  {1, 2, 3}
virtualMachines   Standard_E4_v3   eastus2  {1, 2, 3}
```


## <a name="create-resource-group"></a>Create resource group

[New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) を使用して Azure リソース グループを作成します。 リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 この例では、*myResourceGroup* という名前のリソース グループが *eastus2* リージョンに作成されます。 

```powershell
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS2
```

## <a name="create-networking-resources"></a>ネットワーク リソースの作成

### <a name="create-a-virtual-network-subnet-and-a-public-ip-address"></a>仮想ネットワーク、サブネット、パブリック IP アドレスを作成する 
これらのリソースは、仮想マシンへのネットワーク接続を提供し、その仮想マシンをインターネットに接続するために使用されます。 IP アドレスを可用性ゾーン (この例では *2*) 内に作成します。 後の手順で、IP アドレスの作成に使用したのと同じゾーンに VM を作成します。

```powershell
# Create a subnet configuration
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzureRmVirtualNetwork -ResourceGroupName myResourceGroup -Location eastus2 `
    -Name myVNet -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig

# Create a public IP address in an availability zone and specify a DNS name
$pip = New-AzureRmPublicIpAddress -ResourceGroupName myResourceGroup -Location eastus2 -Zone 2 `
    -AllocationMethod Static -IdleTimeoutInMinutes 4 -Name "mypublicdns$(Get-Random)"
```

### <a name="create-a-network-security-group-and-a-network-security-group-rule"></a>ネットワーク セキュリティ グループとネットワーク セキュリティ グループの規則を作成する 
ネットワーク セキュリティ グループは、受信規則と送信規則を使用して仮想マシンを保護します。 この場合、受信規則はポート 3389 に作成され、受信リモート デスクトップ接続を許可します。 さらにポート 80 に対して、受信 Web トラフィックを許可する受信規則を作成します。

```powershell
# Create an inbound network security group rule for port 3389
$nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
    -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
    -DestinationPortRange 3389 -Access Allow

# Create an inbound network security group rule for port 80
$nsgRuleWeb = New-AzureRmNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleWWW  -Protocol Tcp `
    -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
    -DestinationPortRange 80 -Access Allow

# Create a network security group
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName myResourceGroup -Location eastus2 `
    -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP,$nsgRuleWeb
```

### <a name="create-a-network-card-for-the-virtual-machine"></a>仮想マシン用のネットワーク カードを作成する 
[New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) を使用して仮想マシン用のネットワーク カードを作成します。 ネットワーク カードは、仮想マシンをサブネット、ネットワーク セキュリティ グループ、パブリック IP アドレスに接続します。

```powershell
# Create a virtual network card and associate with public IP address and NSG
$nic = New-AzureRmNetworkInterface -Name myNic -ResourceGroupName myResourceGroup -Location eastus2 `
    -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id
```

## <a name="create-virtual-machine"></a>仮想マシンの作成

仮想マシンの構成を作成します。 この構成には、仮想マシンのデプロイ時に使用される設定 (仮想マシンのイメージ、サイズ、認証構成など) が含まれています。 この例の *Standard_DS1_v2* サイズは、可用性ゾーンでサポートされています。 この構成では、IP アドレスを作成するときに設定した可用性ゾーンも指定します。 この手順の実行時に、資格情報の入力を求められます。 入力した値は、仮想マシンのユーザー名とパスワードとして構成されます。

```powershell
# Define a credential object
$cred = Get-Credential

# Create a virtual machine configuration
$vmConfig = New-AzureRmVMConfig -VMName myVM -VMSize Standard_DS1_v2 -Zone 2 | `
    Set-AzureRmVMOperatingSystem -Windows -ComputerName myVM -Credential $cred | `
    Set-AzureRmVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer `
    -Skus 2016-Datacenter -Version latest | Add-AzureRmVMNetworkInterface -Id $nic.Id
```

[New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) を使用して、仮想マシンを作成します。

```powershell
New-AzureRmVM -ResourceGroupName myResourceGroup -Location eastus2 -VM $vmConfig
```

## <a name="confirm-zone-for-managed-disk"></a>管理ディスクのゾーンの確認

VM と同じ可用性ゾーンに VM の IP アドレス リソースを作成しました。 VM の管理ディスク リソースは、同じ可用性ゾーンに作成されます。 このことは、[Get-AzureRmDisk](/powershell/module/azurerm.compute/get-azurermdisk) で確認できます。

```powershell
Get-AzureRmDisk -ResourceGroupName myResourceGroup
```

この出力は、管理ディスクが VM と同じ可用性ゾーン内にあることを示しています。

```powershell
ResourceGroupName  : myResourceGroup
AccountType        : PremiumLRS
OwnerId            : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.
                     Compute/virtualMachines/myVM
ManagedBy          : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx//resourceGroups/myResourceGroup/providers/Microsoft.
                     Compute/virtualMachines/myVM
Sku                : Microsoft.Azure.Management.Compute.Models.DiskSku
Zones              : {2}
TimeCreated        : 9/7/2017 6:57:26 PM
OsType             : Windows
CreationData       : Microsoft.Azure.Management.Compute.Models.CreationData
DiskSizeGB         : 127
EncryptionSettings :
ProvisioningState  : Succeeded
Id                 : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.
                     Compute/disks/myVM_OsDisk_1_bd921920bb0a4650becfc2d830000000
Name               : myVM_OsDisk_1_bd921920bb0a4650becfc2d830000000
Type               : Microsoft.Compute/disks
Location           : eastus2
Tags               : {}
```


## <a name="next-steps"></a>次の手順

この記事では、可用性ゾーン内に VM を作成する方法を説明しました。 Azure VM の[リージョンと可用性](regions-and-availability.md)の詳細を確認してください。
