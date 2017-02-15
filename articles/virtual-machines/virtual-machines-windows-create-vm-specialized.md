---
title: "Windows VM のコピーを作成する | Microsoft Docs"
description: "Resource Manager デプロイメント モデルで、Windows を実行する特殊な Azure VM のコピーを作成する方法について説明します。"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 3b7d3cd5-e3d7-4041-a2a7-0290447458ea
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 09/21/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: a779f084e0ad6de71ad3e2de86a2fb85738b8fe6


---
# <a name="create-a-vm-from-a-specialized-vhd"></a>特殊化した VHD から VM を作成する
Powershell を使用して、OS ディスクとして特殊化された VHD をアタッチすることにより、新しい VM を作成します。 特殊化された VHD では、ユーザーアカウント、アプリケーション、その他のステート データが元の VM から保持されます。 

一般化された VHD から VM を作成する場合は、「[Create a VM from a generalized VHD image ](virtual-machines-windows-create-vm-generalized.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」 (一般化した VHD イメージからの VM の作成) をご覧ください。

## <a name="create-the-subnet-and-vnet"></a>サブネットと vNet の作成
[仮想ネットワーク](../virtual-network/virtual-networks-overview.md)の vNet とサブネットを作成します。

1. サブネットを作成します。 この例では、**mySubNet** という名前のサブネットをリソース グループ **myResourceGroup** に作成し、サブネットのアドレス プレフィックスを **10.0.0.0/24** に設定します。
   
    ```powershell
    $rgName = "myResourceGroup"
    $subnetName = "mySubNet"
    $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```
2. vNet を作成します。 この例では、仮想ネットワーク名を **myVnetName**、場所を **米国西部**、仮想ネットワークのアドレス プレフィックスを **10.0.0.0/16** に設定します。 
   
    ```powershell
    $location = "West US"
    $vnetName = "myVnetName"
    $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
        -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    

## <a name="create-a-public-ip-address-and-nic"></a>パブリック IP アドレスと NIC の作成
仮想ネットワークでの仮想マシンとの通信を有効にするには、 [パブリック IP アドレス](../virtual-network/virtual-network-ip-addresses-overview-arm.md) とネットワーク インターフェイスが必要です。

1. パブリック IP を作成します。 この例では、パブリック IP アドレス名を **myIP** に設定しています。
   
    ```powershell
    $ipName = "myIP"
    $pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location `
        -AllocationMethod Dynamic
    ```       
2. NIC を作成します。 この例では、NIC 名を **myNicName** に設定しています。
   
    ```powershell
    $nicName = "myNicName"
    $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $location `
        -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
    ```

## <a name="create-the-network-security-group-and-an-rdp-rule"></a>ネットワーク セキュリティ グループと RDP 規則の作成
RDP を使用して VM にログインできるようにするには、ポート 3389 で RDP アクセスを許可するセキュリティ規則が必要です。 新しい VM の VHD は既存の特殊化された VM から作成されたため、VM が作成された後は、RDP を使用してログオンするアクセス許可が付与されていたソースの仮想マシンから既存のアカウントを使用できます。

この例では、NSG の名前を **myNsg** に設定し、RDP 規則の名前を **myRdpRule** に設定します。

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389

$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
```

エンドポイントと NSG の規則について詳しくは、[PowerShell を使用した Azure の VM へのポートの開放](virtual-machines-windows-nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)に関するページをご覧ください。

## <a name="create-the-vm-configuration"></a>VM 構成の作成
コピーした VHD を OS VHD としてアタッチする VM 構成を設定します。

```powershell
# Set the URI for the VHD that you want to use. In this example, the VHD file named "myOsDisk.vhd" is kept 
# in a storage account named "myStorageAccount" in a container named "myContainer".
$osDiskUri = "https://myStorageAccount.blob.core.windows.net/myContainer/myOsDisk.vhd"

# Set the VM name and size. This example sets the VM name to "myVM" and the VM size to "Standard_A2".
$vmName = "myVM"
$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A2"

# Add the NIC
$vm = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id

# Add the OS disk by using the URL of the copied OS VHD. In this example, when the OS disk is created, the 
# term "osDisk" is appened to the VM name to create the OS disk name. This example also specifies that this 
# Windows-based VHD should be attached to the VM as the OS disk.
$osDiskName = $vmName + "osDisk"
$vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption attach -Windows
```


VM にアタッチする必要があるデータ ディスクがある場合は、次も追加してください。 

```powershell
# Optional: Add data disks by using the URLs of the copied data VHDs at the appropriate Logical Unit 
# Number (Lun).
$dataDiskName = $vmName + "dataDisk"
$vm = Add-AzureRmVMDataDisk -VM $vm -Name $dataDiskName -VhdUri $dataDiskUri -Lun 0 -CreateOption attach
```

データとオペレーティング システム ディスクの URL は、 `https://StorageAccountName.blob.core.windows.net/BlobContainerName/DiskName.vhd`のようになります。 ポータルでこれを見つけるには、ターゲット ストレージ コンテナーを参照し、コピーしたオペレーティング システムまたはデータ VHD をクリックして、URL の内容をコピーします。

## <a name="create-the-vm"></a>VM の作成
先ほど作成した構成を使用して VM を作成します。

```powershell
#Create the new VM
New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm
```

このコマンドが成功した場合は、次のような出力が表示されます。

```powershell
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK   

```

## <a name="verify-that-the-vm-was-created"></a>VM 作成の確認
新しく作成された VM は、[Azure Portal](https://portal.azure.com)の **[参照]** の >  **[仮想マシン]**、または次の PowerShell コマンドで確認できます。

```powershell
$vmList = Get-AzureRmVM -ResourceGroupName $rgName
$vmList.Name
```

## <a name="next-steps"></a>次のステップ
新しい仮想マシンにサインインするには、 [ポータル](https://portal.azure.com)で VM を参照し、 **[接続]**をクリックして、リモート デスクトップ RDP ファイルを開きます。 元の仮想マシンのアカウント資格情報を使用して、新しい仮想マシンにサインインします。 詳しくは、「[Windows が実行されている Azure 仮想マシンに接続してログオンする方法](virtual-machines-windows-connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」をご覧ください。




<!--HONumber=Nov16_HO3-->


