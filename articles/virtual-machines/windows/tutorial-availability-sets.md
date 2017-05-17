---
title: "Azure の Windows VM 向け可用性セットのチュートリアル | Microsoft Docs"
description: "Azure の Windows VM 向け可用性セットについて説明します。"
documentationcenter: 
services: virtual-machines-windows
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 04/26/2017
ms.author: cynthn
ms.translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: 831c55939ad3673aa8e44f165e18e826f64b54cc
ms.contentlocale: ja-jp
ms.lasthandoff: 05/03/2017


---

# <a name="how-to-use-availability-sets"></a>可用性セットの使用方法

このチュートリアルでは、可用性セットと呼ばれる論理的なグループに仮想マシン (VM) を配置することで、その可用性を高める方法について説明します。 可用性セット内で VM を作成する場合、Azure プラットフォームにより、基になるインフラストラクチャ全体に VM が分散されます。 ハードウェア障害が発生したり、プラットフォームで計画的なメンテナンスが実施されたりした場合、可用性セットを使用していると、少なくとも 1 つの VM を必ず動作させることができます。

このチュートリアルの手順は、最新バージョンの [Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/) モジュールを使用して行うことができます。

## <a name="availability-set-overview"></a>可用性セットの概要

仮想マシンは、基になる Azure データセンターのハードウェアの論理的なグループにまたがって作成することができます。 複数の VM を作成すると、コンピューティング リソースとストレージ リソースは、サーバー、ネットワーク スイッチ、ストレージなどのハードウェアにまたがって分散されます。 万一ハードウェア コンポーネントにメンテナンスが必要な状況が生じても、この分散によってアプリの稼働率が保たれます。 可用性セットでは、この論理的なグループ化を定義できます。

可用性セットによって、VM の高可用性が実現します。 アプリケーションが障害やメンテナンス イベントを必ず許容するように設計することも必要になります。

## <a name="create-an-availability-set"></a>可用性セットの作成

可用性セットは、[New-AzureRmAvailabilitySet](/powershell/module/azurerm.compute/new-azurermavailabilityset) を使用して作成します。 この例では、*myResourceGroupAvailability* リソース グループ内の *myAvailabilitySet* という名前の可用性セットに対して、更新ドメインと障害ドメインの両方の数として *2* を設定します。


```powershell
New-AzureRmAvailabilitySet `
   -Location EastUS `
   -Name myAvailabilitySet `
   -ResourceGroupName myResourceGroupAvailability `
   -Managed `
   -PlatformFaultDomainCount 2 `
   -PlatformUpdateDomainCount 2
```

## <a name="create-vms-inside-an-availability-set"></a>可用性セット内の VM の作成

VM は、ハードウェア全体で適切に分散させるために、可用性セット内で作成する必要があります。 可用性セットを作成した後に、既存の VM を追加することはできません。 

1 つの場所にあるハードウェアは、複数の更新ドメインと障害ドメインに分割されます。 **更新ドメイン**は、VM と、同時に再起動できる基になる物理ハードウェアのグループです。 同じ**障害ドメイン**内の VM は、共通の電源とネットワーク スイッチだけでなく、共通のストレージも共有します。 

[New-AzureRMVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig) を使用して構成付きの VM を作成する場合には、パラメーター `-AvailabilitySetId` に可用性セットの ID を指定します。

今回は [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) を使って、可用性セットに VM を 2 台作成します。

```powershell
$availabilitySet = Get-AzureRmAvailabilitySet `
    -ResourceGroupName myResourceGroupAvailability `
    -Name myAvailabilitySet

$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
    -Name mySubnet `
    -AddressPrefix 192.168.1.0/24
$vnet = New-AzureRmVirtualNetwork `
    -ResourceGroupName myResourceGroupAvailability `
    -Location EastUS `
    -Name MYvNET `
    -AddressPrefix 192.168.0.0/16 `
    -Subnet $subnetConfig

for ($i=1; $i -le 2; $i++)
{
   $pip = New-AzureRmPublicIpAddress `
        -ResourceGroupName myResourceGroupAvailability `
        -Location EastUS `
        -Name "mypublicdns$(Get-Random)" `
        -AllocationMethod Static `
        -IdleTimeoutInMinutes 4

   $nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig `
        -Name myNetworkSecurityGroupRuleRDP$i `
        -Protocol Tcp `
        -Direction Inbound `
        -Priority 1000 `
        -SourceAddressPrefix * `
        -SourcePortRange * `
        -DestinationAddressPrefix * `
        -DestinationPortRange 3389 `
        -Access Allow

   $nsg = New-AzureRmNetworkSecurityGroup `
        -ResourceGroupName myResourceGroupAvailability `
        -Location EastUS `
        -Name myNetworkSecurityGroup$i `
        -SecurityRules $nsgRuleRDP

   $nic = New-AzureRmNetworkInterface `
        -Name myNic$i `
        -ResourceGroupName myResourceGroupAvailability `
        -Location EastUS `
        -SubnetId $vnet.Subnets[0].Id `
        -PublicIpAddressId $pip.Id `
        -NetworkSecurityGroupId $nsg.Id

   # Here is where we specify the availability set
   $vm = New-AzureRmVMConfig `
        -VMName myVM$i `
        -VMSize Standard_D1 `
        -AvailabilitySetId $availabilitySet.Id

   $vm = Set-AzureRmVMOperatingSystem `
        -VM $vm `
        -Windows -ComputerName myVM$i `   
        -Credential $cred `
        -ProvisionVMAgent `
        -EnableAutoUpdate
   $vm = Set-AzureRmVMSourceImage `
        -VM $vm `
        -PublisherName MicrosoftWindowsServer `
        -Offer WindowsServer `
        -Skus 2016-Datacenter `
        -Version latest
   $vm = Set-AzureRmVMOSDisk `
        -VM $vm `
        -Name myOsDisk$i `
        -DiskSizeInGB 128 `
        -CreateOption FromImage `
        -Caching ReadWrite
   $vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
   New-AzureRmVM `
        -ResourceGroupName myResourceGroupAvailability `
        -Location EastUS `
        -VM $vm
}

```

VM 2 台分の作成と構成が終わるまでには、数分かかります。 完了すると、基になるハードウェア全体に 2 台の仮想マシンが分散して配置されます。 

## <a name="check-for-available-vm-sizes"></a>使用可能な VM のサイズのチェック 

可用性セットには後で VM をさらに追加することができますが、そのハードウェアで使用可能な VM のサイズを把握しておく必要があります。 ハードウェア クラスターで可用性セットに使用可能なすべてのサイズを一覧表示するには、[Get-AzureRMVMSize](/powershell/module/azurerm.compute/get-azurermvmsize) を使用します。

```powershell
Get-AzureRmVMSize `
   -AvailabilitySetName myAvailabilitySet `
   -ResourceGroupName myResourceGroupAvailability  
```

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、仮想マシン スケール セットの作成方法を説明しました。 次のチュートリアルに進み、仮想マシンのスケール セットについて学習してください。

[VM スケール セットの作成](tutorial-create-vmss.md)



