---
title: "Windows Server 向け Azure Hybrid Use Benefit | Microsoft Docs"
description: "Windows Server のソフトウェア アシュアランスの特典を最大限利用してオンプレミスのライセンスを Azure で使用する方法について説明します。"
services: virtual-machines-windows
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: 332583b6-15a3-4efb-80c3-9082587828b0
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/13/2016
ms.author: georgem
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: 5c40b318be2503fe2ec05e9f2a5a09c46b88a0dc


---
# <a name="azure-hybrid-use-benefit-for-windows-server"></a>Windows Server 向け Azure Hybrid Use Benefit
Windows Server をソフトウェア アシュアランス付きで使用しているお客様は、オンプレミスの Windows Server ライセンスを Azure でも使用して、Windows Server VM を Azure で低コストで実行できます。 Azure Hybrid Use Benefit では、Windows Server VM を Azure で実行でき、基本コンピューティング料金のみが請求されます。 詳細については、 [Azure Hybrid Use Benefit のライセンスに関するページ](https://azure.microsoft.com/pricing/hybrid-use-benefit/)を参照してください。 この記事では、このライセンスの特典を利用して Windows Server VM を Azure にデプロイする方法について説明します。

> [!NOTE]
> Azure Hybrid Use Benefit を利用して Windows Server VM をデプロイするために Azure Marketplace イメージを使用することはできません。 ご使用の VM を基本コンピューティング料金の割引対象として正しく登録するには、ご使用の VM を PowerShell または Resource Manager テンプレートのいずれかを使用してデプロイする必要があります。
>
>

## <a name="pre-requisites"></a>前提条件
Windows Server VM 向けの Azure Hybrid Use Benefit を Azure で利用するには、次の 2 つの前提条件があります。

* Azure PowerShell モジュールがインストールされている
* Windows Server VHD が Azure Storage にアップロードされている

### <a name="install-azure-powershell"></a>Azure PowerShell をインストールするには
[最新の Azure PowerShell がインストールおよび構成](../powershell-install-configure.md)されていることを確認します。 Resource Manager テンプレートを使用して VM をデプロイする場合でも、Windows Server VHD をアップロードする (次の手順を参照) ために Azure PowerShell をインストールする必要があります。

### <a name="upload-a-windows-server-vhd"></a>Windows Server VHD をアップロードする
Windows Server VM を Azure にデプロイするには、先に Windows Server の基本ビルドを含む VHD を作成する必要があります。 この VHD は、Sysprep を使用して適切に準備した後、Azure にアップロードする必要があります。 [VHD 要件と Sysprep プロセスの詳細](virtual-machines-windows-upload-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)や「[Sysprep Support for Server Role (サーバー ロールに対する Sysprep サポート)](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)」を参照してください。 Sysprep を実行する前に、VM をバックアップします。 VHD が準備できたら、次のように `Add-AzureRmVhd` コマンドレットを使用して、その VHD を Azure Storage アカウントにアップロードします。

```powershell
Add-AzureRmVhd -ResourceGroupName "myResourceGroup" -LocalFilePath "C:\Path\To\myvhd.vhd" `
    -Destination "https://mystorageaccount.blob.core.windows.net/vhds/myvhd.vhd"
```

> [!NOTE]
> Microsoft SQL Server、SharePoint Server、Dynamics も、ソフトウェア アシュアランス ライセンスを利用できます。 ただし、Windows Server イメージはあくまでご自身で用意する必要があります。アプリケーション コンポーネントをインストールし、適宜ライセンス キーを指定したうえで、ディスク イメージを Azure にアップロードします。 「[SysPrep を使用した SQL Server のインストールに関する注意点](https://msdn.microsoft.com/library/ee210754.aspx)」や「[Build a SharePoint Server 2016 Reference Image (Sysprep) (SharePoint Server 2016 リファレンス イメージを構築する (Sysprep))](http://social.technet.microsoft.com/wiki/contents/articles/33789.build-a-sharepoint-server-2016-reference-image-sysprep.aspx)」など、アプリケーションを含めて Sysprep を実行するための適切なドキュメントを参照してください。
>
>

[VHD を Azure にアップロードするプロセス](virtual-machines-windows-upload-image.md#upload-the-vhd-to-your-storage-account)の詳細を確認できます。

> [!TIP]
> この記事では Windows Server VM のデプロイを中心に説明しています。 Windows Client VM も同じ方法でデプロイできます。 以下の例で、`Server` を `Client` に適宜置き換えます。
>
>

## <a name="deploy-a-vm-via-powershell-quick-start"></a>PowerShell クイック スタートを使用して VM をデプロイする
Windows Server VM を PowerShell を使用してデプロイするときに、追加パラメーター `-LicenseType`を指定できます。 VHD を Azure にアップロードした後、新しい VM を `New-AzureRmVM` を使用して作成し、ライセンスの種類を次のように指定します。

```powershell
New-AzureRmVM -ResourceGroupName "myResourceGroup" -Location "West US" -VM $vm -LicenseType "Windows_Server"
```

この後の [PowerShell を使用した VM の Azure への詳細なデプロイ手順](virtual-machines-windows-hybrid-use-benefit-licensing.md#detailed-powershell-walkthrough)を参照するか、[Resource Manager と PowerShell を使用して Windows VM を作成する](virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)ための別の手順に関する詳細なガイドを参照できます。

## <a name="deploy-a-vm-via-resource-manager"></a>Resource Manager を使用して VM をデプロイする
Resource Manager テンプレート内に、追加パラメーター `licenseType` を指定できます。 [Azure Resource Manager テンプレートの作成](../resource-group-authoring-templates.md)で詳細を確認できます。 VHD を Azure にアップロードした後、コンピューティング プロバイダーの一部としてライセンスの種類を含めるように Resource Manager テンプレートを編集し、テンプレートを通常どおりデプロイします。

```json
"properties": {  
   "licenseType": "Windows_Server",
   "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
   },
```

## <a name="verify-your-vm-is-utilizing-the-licensing-benefit"></a>VM がライセンスの特典を利用していることを確認する
VM を PowerShell または Resource Manager のいずれかのデプロイ方法によってデプロイした後、次のように `Get-AzureRmVM` を使用してライセンスの種類を確認します。

```powershell
Get-AzureRmVM -ResourceGroup "myResourceGroup" -Name "myVM"
```

次のように出力されます。

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Server
```

これを Azure Hybrid Use Benefit ライセンスを使用せずにデプロイされた次の VM (Azure Gallery から直接デプロイされた VM など) と比べてください。

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              :
```

## <a name="detailed-powershell-walkthrough"></a>PowerShell の詳細な手順
次の PowerShell の詳細な手順は、VM の完全なデプロイを示しています。 「 [Resource Manager と PowerShell を使用して Windows VM を作成する](virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」で、実際のコマンドレットと作成される別のコンポーネントを確認できます。 リソース グループ、ストレージ アカウント、および仮想ネットワークを作成した後、VM を定義し、最後に VM を作成します。

最初に、資格情報を取得し、場所とリソース グループ名を設定します。

```powershell
$cred = Get-Credential
$location = "West US"
$resourceGroupName = "myResourceGroup"
```

パブリック IP を作成します。

```powershell
$publicIPName = "myPublicIP"
$publicIP = New-AzureRmPublicIpAddress -Name $publicIPName -ResourceGroupName $resourceGroupName `
    -Location $location -AllocationMethod "Dynamic"
```

サブネット、NIC、および VNET を定義します。

```powershell
$subnetName = "mySubnet"
$nicName = "myNIC"
$vnetName = "myVnet"
$subnetconfig = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/8
$vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $resourceGroupName -Location $location `
    -AddressPrefix 10.0.0.0/8 -Subnet $subnetconfig
$nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $resourceGroupName -Location $location `
    -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $publicIP.Id
```

VM に名前を付け、VM 構成を作成します。

```powershell
$vmName = "myVM"
$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A1"
```

OS を定義します。

```powershell
$computerName = "myVM"
$vm = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $computerName -Credential $cred `
    -ProvisionVMAgent -EnableAutoUpdate
```

NIC を VM に追加します。

```powershell
$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
```

使用するストレージ アカウントを定義します。

```powershell
$storageAcc = Get-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -AccountName mystorageaccount
```

適切に準備され、使用する VM に接続される VHD をアップロードします。

```powershell
$osDiskName = "licensing.vhd"
$osDiskUri = '{0}vhds/{1}{2}.vhd' -f $storageAcc.PrimaryEndpoints.Blob.ToString(), $vmName.ToLower(), $osDiskName
$urlOfUploadedImageVhd = "https://mystorageaccount.blob.core.windows.net/vhd/myvhd.vhd"
$vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption FromImage `
    -SourceImageUri $urlOfUploadedImageVhd -Windows
```

最後に、VM を作成し、Azure Hybrid Use Benefit を利用するライセンスの種類を定義します。

```powershell
New-AzureRmVM -ResourceGroupName $resourceGroupName -Location $location -VM $vm -LicenseType "Windows_Server"
```

## <a name="next-steps"></a>次のステップ
[Azure Hybrid Use Benefit](https://azure.microsoft.com/pricing/hybrid-use-benefit/)について確認します。

[Resource Manager テンプレートの使用方法](../azure-resource-manager/resource-group-overview.md)の詳細を参照します。



<!--HONumber=Nov16_HO3-->


