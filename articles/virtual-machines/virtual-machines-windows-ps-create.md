---
title: "PowerShell を使用して Azure VM を作成する | Microsoft Docs"
description: "Azure PowerShell と Azure Resource Manager を使用すると、Windows Server を実行する VM を簡単に作成できます。"
services: virtual-machines-windows
documentationcenter: 
author: davidmu1
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 14fe9ca9-e228-4d3b-a5d8-3101e9478f6e
ms.service: virtual-machines-windows
ms.topic: get-started-article
ms.date: 02/14/2017
ms.author: davidmu
translationtype: Human Translation
ms.sourcegitcommit: c1262b7708918cbdc8ce35f3e65a47a04797f195
ms.openlocfilehash: 8a67352a65e755f2177fb4870f34c41440bf90a2

---

# <a name="create-a-windows-vm-using-resource-manager-and-powershell"></a>Resource Manager と PowerShell を使用して Windows VM を作成する

この記事では、Windows Server を実行する Azure 仮想マシンとそれに必要なリソースを [Resource Manager](../azure-resource-manager/resource-group-overview.md) と PowerShell を使用してすばやく作成する方法を紹介します。 仮想マシンを作成するには、この記事のすべての手順を実施する必要があります。所要時間は約 30 分です。 コマンド内のサンプルのパラメーター値は、ご自分の環境に適した名前に置き換えてください。

## <a name="step-1-install-azure-powershell"></a>手順 1: Azure PowerShell をインストールする

最新バージョンの Azure PowerShell をインストールし、サブスクリプションを選択して、ご利用のアカウントにサインインする方法については、「[Azure PowerShell のインストールおよび構成方法](/powershell/azureps-cmdlets-docs)」を参照してください。

## <a name="step-2-create-a-resource-group"></a>手順 2: リソース グループを作成する

すべてのリソースがリソース グループに含まれている必要があるため、最初に作成しましょう。  

1. リソースを作成できる場所の一覧を取得します。
   
    ```powershell
    Get-AzureRmLocation | sort Location | Select Location
    ```

2. リソースの場所を設定します。 次のコマンドにより、場所が **centralus** に設定されます。
   
    ```powershell
    $location = "centralus"
    ```

3. リソース グループを作成します。 次のコマンドにより、設定した場所に **myResourceGroup** という名前のリソース グループが作成されます。
   
    ```powershell
    $myResourceGroup = "myResourceGroup"
    New-AzureRmResourceGroup -Name $myResourceGroup -Location $location
    ```

## <a name="step-3-optional-create-a-storage-account"></a>手順 3: ストレージ アカウントを作成する

仮想マシンを作成する際に、[Azure Managed Disks](../storage/storage-managed-disks-overview.md) を使用するか、非管理対象ディスクを使用するかを選択できるようになりました。 非管理対象ディスクの使用を選択した場合は、作成する仮想マシンによって使用される仮想ハード ディスクを格納するための[ストレージ アカウント](../storage/storage-introduction.md)を作成する必要があります。 Managed Disks の使用を選択した場合は、ストレージ アカウントは必要ありません。 ストレージ アカウント名の長さは 3 ～ 24 文字で、数字と小文字のみを使用できます。

1. ストレージ アカウント名の一意性をテストします。 次のコマンドにより、**myStorageAccount** という名前がテストされます。
   
    ```powershell
    $myStorageAccountName = "mystorageaccount"
    Get-AzureRmStorageAccountNameAvailability $myStorageAccountName
    ```
   
    このコマンドで **True**が返された場合は、提案した名前は Azure 内で一意です。 

2. 次に、ストレージ アカウントを作成します。
   
    ```powershell    
    $myStorageAccount = New-AzureRmStorageAccount -ResourceGroupName $myResourceGroup `
        -Name $myStorageAccountName -SkuName "Standard_LRS" -Kind "Storage" -Location $location
    ```

## <a name="step-4-create-a-virtual-network"></a>手順 4: 仮想ネットワークを作成する

すべての仮想マシンは [仮想ネットワーク](../virtual-network/virtual-networks-overview.md)に属します。

1. 仮想ネットワークのサブネットを作成します。 次のコマンドを実行すると、10.0.0.0/24 というアドレス プレフィックスを持つ **mySubnet** というサブネットが作成されます。
   
    ```powershell
    $mySubnet = New-AzureRmVirtualNetworkSubnetConfig -Name "mySubnet" -AddressPrefix 10.0.0.0/24
    ```

2. 次に、仮想ネットワークを作成します。 次のコマンドを実行すると、作成したサブネットと **10.0.0.0/16** というアドレス プレフィックスを使用して、**myVnet** という仮想ネットワークが作成されます。
   
    ```powershell
    $myVnet = New-AzureRmVirtualNetwork -Name "myVnet" -ResourceGroupName $myResourceGroup `
        -Location $location -AddressPrefix 10.0.0.0/16 -Subnet $mySubnet
    ```

## <a name="step-5-create-a-public-ip-address-and-network-interface"></a>手順 5: パブリック IP アドレスとネットワーク インターフェイスを作成する

仮想ネットワークでの仮想マシンとの通信を有効にするには、 [パブリック IP アドレス](../virtual-network/virtual-network-ip-addresses-overview-arm.md) とネットワーク インターフェイスが必要です。

1. パブリック IP アドレスを作成します。 次のコマンドを実行すると、**Dynamic** という割り当て方法で **myPublicIp** というパブリック IP アドレスが作成されます。
   
    ```powershell
    $myPublicIp = New-AzureRmPublicIpAddress -Name "myPublicIp" -ResourceGroupName $myResourceGroup `
        -Location $location -AllocationMethod Dynamic
    ```
2. ネットワーク インターフェイスを作成します。 次のコマンドを実行すると、**myNIC** というネットワーク インターフェイスが作成されます。
   
    ```powershell
    $myNIC = New-AzureRmNetworkInterface -Name "myNIC" -ResourceGroupName $myResourceGroup `
        -Location $location -SubnetId $myVnet.Subnets[0].Id -PublicIpAddressId $myPublicIp.Id
    ```

## <a name="step-6-create-a-virtual-machine"></a>手順 6: 仮想マシンを作成する

すべての準備ができたので、仮想マシンを作成します。 仮想マシンは、[Marketplace イメージ](virtual-machines-windows-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)、[カスタムの一般化された (sysprep された) イメージ](virtual-machines-windows-create-vm-generalized.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)、または[カスタムの特殊化された (sysprep されていない) イメージ](virtual-machines-windows-create-vm-specialized.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)を使用して作成できます。 この例では、Marketplace からの Windows Server イメージを使用します。 

1. 次のコマンドを実行して、仮想マシンの管理者アカウントの名前とパスワードを設定します。

    ```powershell
    $cred = Get-Credential -Message "Type the name and password of the local administrator account."
    ```
   
    パスワードは、12 ～ 123 文字で指定する必要があります。また、少なくとも 1 つの小文字、1 つの大文字、1 つの数字、1 つの特殊文字を含める必要があります。

2. 仮想マシンの構成オブジェクトを作成します。 次のコマンドを実行すると、VM の名前とサイズを定義する **myVmConfig** という構成オブジェクトが作成されます。
   
    ```powershell
    $myVM = New-AzureRmVMConfig -VMName "myVM" -VMSize "Standard_DS1_v2"
    ```
   
    仮想マシンに使用できるサイズの一覧は、「 [Azure の仮想マシンのサイズ](virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 」をご覧ください。

3. VM のオペレーティング システム設定を構成します。 次のコマンドを実行すると、VM のコンピューター名、オペレーティング システムの種類、アカウントの資格情報が設定されます。
   
    ```powershell
    $myVM = Set-AzureRmVMOperatingSystem -VM $myVM -Windows -ComputerName "myVM" -Credential $cred `
        -ProvisionVMAgent -EnableAutoUpdate
    ```

4. VM のプロビジョニングに使用するイメージを定義します。 次のコマンドを実行すると、VM に使用する Windows Server イメージが定義されます。 

    ```powershell
    $myVM = Set-AzureRmVMSourceImage -VM $myVM -PublisherName "MicrosoftWindowsServer" `
        -Offer "WindowsServer" -Skus "2012-R2-Datacenter" -Version "latest"
    ```

5. 作成したネットワーク インターフェイスを構成に追加します。
   
    ```powershell
    $myVM = Add-AzureRmVMNetworkInterface -VM $myVM -Id $myNIC.Id
    ```

6. 非管理対象ディスクを使用する場合は、次のコマンドを実行して、VM ハード ディスクの場所と名前を定義します。それ以外の場合は、この手順をスキップします。 非管理対象ディスク用の仮想ハード ディスク ファイルはコンテナーに格納されます。 次のコマンドにより、作成したストレージ アカウントの **vhds/myOsDisk1.vhd** という名前のコンテナーにディスクが作成されます。
   
    ```powershell
    $blobPath = "vhds/myOsDisk1.vhd"
    $osDiskUri = $myStorageAccount.PrimaryEndpoints.Blob.ToString() + $blobPath
    ```

7. オペレーティング システム ディスクの情報を VM 構成に追加します。 次のコマンドにより、**myOsDisk1** という名前のディスクが作成されます。
   
    Managed Disks を使用する場合は、次のコマンドを実行して、構成のオペレーティング システム ディスクを設定します。

    ```powershell
    $myVM = Set-AzureRmVMOSDisk -VM $myVM -Name "myOsDisk1" -StorageAccountType PremiumLRS -DiskSizeInGB 128 -CreateOption FromImage -Caching ReadWrite
    ```

    非管理対象ディスクを使用する場合は、次のコマンドを実行して、構成のオペレーティング システム ディスクを設定します。

    ```powershell
    $myVM = Set-AzureRmVMOSDisk -VM $myVM -Name "myOsDisk1" -VhdUri $osDiskUri -CreateOption fromImage
    ```

8. 最後に、仮想マシンを作成します。
   
    ```powershell
    New-AzureRmVM -ResourceGroupName $myResourceGroup -Location $location -VM $myVM
    ```

## <a name="next-steps"></a>次のステップ

* デプロイに問題がある場合は、次の手順として、「[Troubleshoot common Azure deployment errors with Azure Resource Manager](../azure-resource-manager/resource-manager-common-deployment-errors.md)」(Azure Resource Manager を使用した Azure のデプロイで発生する一般的なエラーのトラブルシューティング) を参照してください。
* 「[Resource Manager と PowerShell を使用した Azure Virtual Machines の管理](virtual-machines-windows-ps-manage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」で、作成した仮想マシンを管理する方法を確認します。
* テンプレートを使用して仮想マシンを作成する方法については、「 [Resource Manager テンプレートで Windows 仮想マシンを作成する](virtual-machines-windows-ps-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)




<!--HONumber=Feb17_HO3-->


