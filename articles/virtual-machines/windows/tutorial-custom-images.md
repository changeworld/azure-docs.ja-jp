---
title: "Azure PowerShell を使用したカスタム VM イメージの作成 | Microsoft Docs"
description: "チュートリアル - Azure PowerShell を使用してカスタム VM イメージを作成します。"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/02/2017
ms.author: cynthn
ms.translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: ab19073735816ebb32a9840ec03b31b358ccb565
ms.contentlocale: ja-jp
ms.lasthandoff: 05/03/2017

---

# <a name="create-a-custom-image-of-an-azure-vm-using-powershell"></a>PowerShell を使用した Azure VM のカスタム イメージの作成

このチュートリアルでは、Azure 仮想マシンの独自のカスタム イメージを定義する方法を学習します。 カスタム イメージを使用すると、既に構成しているイメージを使用して VM を作成できます。 カスタム イメージは、バイナリとアプリケーションの事前読み込み、アプリケーションの構成、VM データ ディスクの定義、その他の OS 構成をブートストラップするために使用できます。 カスタム イメージを作成すると、カスタマイズした VM に加えて、接続されているすべてのディスクがイメージ内に含まれます。

このチュートリアルの手順は、最新バージョンの [Azure PowerShell](/powershell/azure/overview) モジュールを使用して行うことができます。

## <a name="before-you-begin"></a>開始する前に

以下の手順では、既存の VM を取得し、新しい VM インスタンスの作成に使用できる再利用可能なカスタム イメージに変換する方法について詳しく説明します。

このチュートリアルの例を完了するには、既存の仮想マシンが必要です。 必要に応じて、この[サンプル スクリプト](../scripts/virtual-machines-windows-powershell-sample-create-vm.md)で仮想マシンを作成できます。 このチュートリアルを実行するときは、リソース グループと VM の名前を適宜置き換えてください。

## <a name="prepare-vm"></a>VM の準備

仮想マシンのイメージを作成するには、VM を一般化して割り当てを解除し、ソース VM を Azure で一般化済みとマークすることで、VM を準備する必要があります。

### <a name="generalize-the-windows-vm-using-sysprep"></a>Sysprep を使用して Windows VM を一般化する

特に重要な点は、Sysprep がすべての個人アカウント情報を削除して、マシンをイメージとして使用できるように準備することです。 Sysprep の詳細については、「 [Sysprep の使用方法: 紹介](http://technet.microsoft.com/library/bb457073.aspx)」を参照してください。


1. 仮想マシンへの接続
2. 管理者としてコマンド プロンプト ウィンドウを開きます。 ディレクトリを *%windir%\system32\sysprep* に変更し、*sysprep.exe* を実行します。
3. **[システム準備ツール]** ダイアログ ボックスで *[システムの OOBE (Out-of-Box Experience) に入る]* を選択し、*[一般化する]* チェック ボックスがオンになっていることを確認します。
4. **[シャットダウン オプション]** の *[シャットダウン]* を選択し、**[OK]** をクリックします。
5. Sysprep は完了時に仮想マシンをシャットダウンします。 **VM は再起動しないでください**。

### <a name="deallocate-and-mark-the-vm-as-generalized"></a>VM の割り当てを解除して VM を汎用としてマークする

イメージを作成するには、VM の割り当てを解除し、Azure で VM を一般化済みとしてマークする必要があります。

[Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm) を使用して VM の割り当てを解除します。

```powershell
Stop-AzureRmVM -ResourceGroupName myResourceGroupImages -Name myVM -Force
```

[Set-AzureRmVm](/powershell/module/azurerm.compute/set-azurermvm) を使用して、仮想マシンの状態を `-Generalized` に設定します。 
   
```powershell
Set-AzureRmVM -ResourceGroupName myResourceGroupImages -Name myVM -Generalized
```


## <a name="create-the-image"></a>イメージの作成

[New-AzureRmImageConfig](/powershell/module/azurerm.compute/new-azurermimageconfig) と [New-AzureRmImage](/powershell/module/azurerm.compute/new-azurermimage) を使用して、VM のイメージを作成できるようになりました。 次の例では、*myVM* という名前の VM から *myImage* という名前のイメージを作成します。

仮想マシンを取得します。 

```powershell
$vm = Get-AzureRmVM -Name myVM -ResourceGroupName myResourceGroupImages
```

イメージの構成を作成します。

```powershell
$image = New-AzureRmImageConfig -Location EastUS -SourceVirtualMachineId $vm.ID 
```

イメージを作成します。

```powershell
New-AzureRmImage -Image $image -ImageName myImage -ResourceGroupName myResourceGroupImages
```    

 
## <a name="create-vms-from-the-image"></a>イメージからの VM の作成

イメージが用意できたので、イメージから新しい VM を 1 つ以上作成できます。 カスタム イメージからの VM の作成は、Marketplace イメージを使用した VM の作成によく似ています。 Marketplace イメージを使用する際は、イメージ、イメージ プロバイダー、プラン、SKU、バージョンに関する情報が必要です。 カスタム イメージを使用する場合は、カスタム イメージ リソースの ID だけを指定する必要があります。 

次のスクリプトでは、*$image* 変数を作成し、[Get-AzureRmImage](/powershell/module/azurerm.compute/get-azurermimage) を使用してカスタム イメージに関する情報を格納した後、[Set-AzureRmVMSourceImage](/powershell/module/azurerm.compute/set-azurermvmsourceimage) を使用し、先ほど作成した *$image* 変数を使用して ID を指定します。 

このスクリプトは、場所 "*米国西部*" にある *myResourceGroupFromImage* という名前の新しいリソース グループに、カスタム イメージから *myVMfromImage* という名前の VM を作成します。


```powershell
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

New-AzureRmResourceGroup -Name myResourceGroupFromImage -Location EastUS

$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
    -Name mySubnet `
    -AddressPrefix 192.168.1.0/24

$vnet = New-AzureRmVirtualNetwork `
    -ResourceGroupName myResourceGroupFromImage `
    -Location EastUS `
    -Name MYvNET `
    -AddressPrefix 192.168.0.0/16 `
    -Subnet $subnetConfig

$pip = New-AzureRmPublicIpAddress `
    -ResourceGroupName myResourceGroupFromImage `
    -Location EastUS `
    -Name "mypublicdns$(Get-Random)" `
    -AllocationMethod Static `
    -IdleTimeoutInMinutes 4

  $nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig `
    -Name myNetworkSecurityGroupRuleRDP `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 1000 `
    -SourceAddressPrefix * `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 3389 `
    -Access Allow

  $nsg = New-AzureRmNetworkSecurityGroup `
    -ResourceGroupName myResourceGroupFromImage `
    -Location EastUS `
    -Name myNetworkSecurityGroup `
    -SecurityRules $nsgRuleRDP

$nic = New-AzureRmNetworkInterface `
    -Name myNic `
    -ResourceGroupName myResourceGroupFromImage `
    -Location EastUS `
    -SubnetId $vnet.Subnets[0].Id `
    -PublicIpAddressId $pip.Id `
    -NetworkSecurityGroupId $nsg.Id

$vmConfig = New-AzureRmVMConfig `
    -VMName myVMfromImage `
    -VMSize Standard_D1 | Set-AzureRmVMOperatingSystem -Windows `
        -ComputerName myComputer `
        -Credential $cred 

# Here is where we create a variable to store information about the image 
$image = Get-AzureRmImage `
    -ImageName myImage `
    -ResourceGroupName myResourceGroupImages

# Here is where we specify that we want to create the VM from and image and provide the image ID
$vmConfig = Set-AzureRmVMSourceImage -VM $vmConfig -Id $image.Id

$vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id

New-AzureRmVM `
    -ResourceGroupName myResourceGroupFromImage `
    -Location EastUS `
    -VM $vmConfig
```

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、カスタム VM カスタム イメージを作成する方法を説明しました。 次のチュートリアルに進み、仮想マシンの高可用性について学習してください。

[高可用性 VM の作成](tutorial-availability-sets.md)




