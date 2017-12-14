---
title: "Azure で管理イメージから VM を作成する | Microsoft Docs"
description: "Resource Manager デプロイメント モデルで Azure PowerShell または Azure Portal を使って、一般化した管理イメージから仮想マシンを作成します。"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 12/01/2017
ms.author: cynthn
ms.openlocfilehash: d8986c71fd0300af385750af898d620e6d3e1f24
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/07/2017
---
# <a name="create-a-vm-from-a-managed-image"></a>管理イメージから VM を作成する

PowerShell または Azure Portal を使って、管理 VM イメージから複数の VM を作成できます。 管理 VM イメージには、OS や データ ディスクなど、VM の作成に必要な情報が含まれています。 OS ディスクやデータ ディスクなど、イメージを構成する VHD は、管理ディスクとして保管されます。 

新しい VM の作成に使用する[管理 VM イメージを作成](capture-image-resource.md)しておく必要があります。 

## <a name="use-the-portal"></a>ポータルの使用

1. [Azure Portal](https://portal.azure.com)を開きます。
2. 左側のメニューで、**[すべてのリソース]** を選びます。 **[種類]** でリソースを並べ替えて、イメージを簡単に見つけることができます。
3. 一覧から使うイメージを選びます。 イメージの **[概要]** ページが開きます。
4. メニューから **[+ VM の作成]** をクリックします。
5. 仮想マシンの情報を入力します。 ここに入力したユーザー名とパスワードが、仮想マシンへのログインに使用されます。 完了したら、**[OK]** をクリックします。 既存のリソース グループに新しい VM を作成するか、**[新規作成]** を選んで VM を格納する新しいリソース グループを作成することができます。
6. VM のサイズを選択します。 その他のサイズも表示するには、**[すべて表示]** を選択するか、**[Supported disk type (サポートされているディスクの種類)]** フィルターを変更します。 
7. **[設定]** で必要な変更を行い、**[OK]** をクリックします。 
8. 概要ページでは、イメージ名が **[プライベート イメージ]** の一覧に表示されます。 **[OK]** をクリックして、仮想マシンのデプロイを開始します。


## <a name="use-powershell"></a>PowerShell の使用

### <a name="prerequisites"></a>前提条件

AzureRM.Compute および AzureRM.Network の PowerShell モジュールが最新バージョンであることを確認してください。 管理者として PowerShell プロンプトを開き、次のコマンドを実行してこれらをインストールします。

```azurepowershell-interactive
Install-Module AzureRM.Compute,AzureRM.Network
```
詳細については、[Azure PowerShell のバージョン管理に関するページ](/powershell/azure/overview)をご覧ください。



### <a name="collect-information-about-the-image"></a>イメージに関する情報の収集

まず、イメージに関する基本的な情報を収集し、イメージの変数を作成する必要があります。 この例では、**West Central US** という場所のリソース グループ **myResourceGroup** にある **myImage** という名前の管理対象 VM イメージを使用します。 

```azurepowershell-interactive
$rgName = "myResourceGroup"
$location = "West Central US"
$imageName = "myImage"
$image = Get-AzureRMImage -ImageName $imageName -ResourceGroupName $rgName
```

### <a name="create-a-virtual-network"></a>仮想ネットワークの作成
[仮想ネットワーク](../../virtual-network/virtual-networks-overview.md)の vNet とサブネットを作成します。

サブネットを作成します。 次の例では、**10.0.0.0/24** というアドレス プレフィックスを持つ **mySubnet** という名前のサブネットを作成します。  
   
```azurepowershell-interactive
$subnetName = "mySubnet"
$singleSubnet = New-AzureRmVirtualNetworkSubnetConfig `
    -Name $subnetName -AddressPrefix 10.0.0.0/24
```

仮想ネットワークを作成する。 次の例では、**10.0.0.0/16** というアドレス プレフィックスを持つ **myVnet** という名前の仮想ネットワークを作成します。  
   
```azurepowershell-interactive
$vnetName = "myVnet"
$vnet = New-AzureRmVirtualNetwork `
    -Name $vnetName `
    -ResourceGroupName $rgName `
    -Location $location `
    -AddressPrefix 10.0.0.0/16 `
    -Subnet $singleSubnet
```    

### <a name="create-a-public-ip-address-and-network-interface"></a>パブリック IP アドレスとネットワーク インターフェイスの作成

仮想ネットワークでの仮想マシンとの通信を有効にするには、 [パブリック IP アドレス](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) とネットワーク インターフェイスが必要です。

パブリック IP アドレスを作成します。 この例では、**myPip** という名前のパブリック IP アドレスを作成します。 
   
```azurepowershell-interactive
$ipName = "myPip"
$pip = New-AzureRmPublicIpAddress `
    -Name $ipName `
    -ResourceGroupName $rgName `
    -Location $location `
    -AllocationMethod Dynamic
```
       
NIC を作成します。 この例では、**myNic** という名前の NIC を作成します。 
   
```azurepowershell-interactive
$nicName = "myNic"
$nic = New-AzureRmNetworkInterface `
    -Name $nicName `
    -ResourceGroupName $rgName `
    -Location $location `
    -SubnetId $vnet.Subnets[0].Id `
    -PublicIpAddressId $pip.Id
```

### <a name="create-the-network-security-group-and-an-rdp-rule"></a>ネットワーク セキュリティ グループと RDP 規則の作成

RDP を使用して VM にログインできるようにするには、ポート 3389 に対する RDP アクセスを許可するネットワーク セキュリティの規則 (NSG) が必要です。 

この例では、**myNsg** という名前の NSG を作成します。この NSG には、ポート 3389 経由の RDP トラフィックを許可する **myRdpRule** という名前の規則が含まれています。 NSG の詳細については、[PowerShell を使用した Azure の VM へのポートの開放](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)に関するページを参照してください。

```azurepowershell-interactive
$nsgName = "myNsg"
$ruleName = "myRdpRule"
$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name $ruleName -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389

$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
```


### <a name="create-a-variable-for-the-virtual-network"></a>仮想ネットワーク用の変数の作成

変数を作成して、仮想ネットワークの作成を完了します。 

```azurepowershell-interactive
$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName $rgName -Name $vnetName

```

### <a name="get-the-credentials-for-the-vm"></a>VM の資格情報の取得

次のコマンドレットを実行するとウィンドウが開きます。そこで、VM へのリモート アクセスでローカル管理者アカウントとして使用する、新しいユーザー名とパスワードを入力します。 

```azurepowershell-interactive
$cred = Get-Credential
```

### <a name="set-variables-for-the-vm-name-computer-name-and-the-size-of-the-vm"></a>VM 名、コンピューター名、および VM のサイズの変数の設定

VM 名とコンピューター名の変数を作成します。 この例では、VM 名として **myVM** を、コンピューター名として **myComputer** を設定します。

```azurepowershell-interactive
$vmName = "myVM"
$computerName = "myComputer"
```

仮想マシンのサイズを設定します。 次の例では、サイズが **Standard_DS1_v2** に設定された VM を作成します。 詳細については、[VM のサイズ](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/)に関するドキュメントをご覧ください。

```azurepowershell-interactive
$vmSize = "Standard_DS1_v2"
```

VM の名前とサイズを VM 構成に追加します。

```azurepowershell-interactive
$vm = New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize
```

### <a name="set-the-vm-image-as-source-image-for-the-new-vm"></a>新しい VM のソース イメージとしての VM イメージの設定

管理対象 VM イメージの ID を使用してソース イメージを設定します。

```azurepowershell-interactive
$vm = Set-AzureRmVMSourceImage -VM $vm -Id $image.Id
```

### <a name="set-the-os-configuration-and-add-the-nic"></a>OS 構成の設定と NIC の追加

ストレージの種類 (PremiumLRS または StandardLRS) と OS ディスクのサイズを入力します。 この例では、アカウントの種類を **PremiumLRS** に、ディスク サイズを **128 GB**に、ディスク キャッシュを **ReadWrite** に設定します。

```azurepowershell-interactive
$vm = Set-AzureRmVMOSDisk -VM $vm  `
    -StorageAccountType PremiumLRS `
    -DiskSizeInGB 128 `
    -CreateOption FromImage `
    -Caching ReadWrite

$vm = Set-AzureRmVMOperatingSystem -VM $vm -Windows -ComputerName $computerName `
-Credential $cred -ProvisionVMAgent -EnableAutoUpdate

$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
```

### <a name="create-the-vm"></a>VM の作成

先ほど作成し **$vm** 変数に格納した構成を使用して、新しい VM を作成します。

```azurepowershell-interactive
New-AzureRmVM -VM $vm -ResourceGroupName $rgName -Location $location
```

### <a name="verify-that-the-vm-was-created"></a>VM 作成の確認
完了したら、[Azure Portal](https://portal.azure.com) で **[参照]** > **[仮想マシン]** にアクセスするか、次の PowerShell コマンドを使用して、新しく作成された VM を確認します。

```azurepowershell-interactive
$vmList = Get-AzureRmVM -ResourceGroupName $rgName
$vmList.Name
```

## <a name="next-steps"></a>次のステップ
Azure PowerShell を使用して新しい仮想マシンを管理するには、「[Azure PowerShell モジュールを使用して Windows VM を作成および管理する](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」を参照してください。

