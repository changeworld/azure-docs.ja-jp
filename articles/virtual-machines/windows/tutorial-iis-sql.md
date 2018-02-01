---
title: "Azure SQL、IIS、および .NET スタックを実行する VM を Azure で作成する | Microsoft Docs"
description: "チュートリアル - Windows 仮想マシンに Azure SQL、IIS、および .NET スタックをインストールします。"
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
ms.date: 10/24/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 6533ab205e07243e2f757ea0a66028e1d140c52b
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/01/2018
---
# <a name="install-a-sql92iis92net-stack-in-azure"></a>Azure SQL、IIS、および .NET スタックを Azure にインストールします。

このチュートリアルでは、Azure PowerShell を使用して Azure SQL、IIS、および .NET スタックをインストールします。 このスタックは、1 つは IIS および .NET を、もう 1 つは SQL Server を使用して Windows Server 2016 を実行する 2 つの VM で構成されています。

> [!div class="checklist"]
> * New-AzVM を使用して VM を作成する
> * VM に IIS と .NET Core SDK をインストールする
> * SQL Server を実行する VM を作成する
> * SQL Server 拡張機能をインストールする

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

PowerShell をインストールしてローカルで使用する場合、このチュートリアルでは Azure PowerShell モジュール バージョン 5.1.1 以降が必要になります。 バージョンを確認するには、` Get-Module -ListAvailable AzureRM` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-azurerm-ps)に関するページを参照してください。 PowerShell をローカルで実行している場合、`Login-AzureRmAccount` を実行して Azure との接続を作成することも必要です。

## <a name="create-a-iis-vm"></a>IIS の仮想マシンを作成する 

この例では、PowerShell Cloud Shell の [New-AzVM](https://www.powershellgallery.com/packages/AzureRM.Compute.Experiments) コマンドレットを使用して、Windows Server 2016 仮想マシンを作成し、IIS と .NET Framework をインストールします。 IIS と Azure SQL VM はリソース グループと仮想ネットワークを共有するため、それらの名前となる変数を作成します。

コード ブロックの右上にある **[試してみる]** ボタンをクリックすると、このウィンドウで Cloud Shell を起動できます。 コマンド プロンプト で仮想マシンの資格情報を入力するよう求められます。

```azurepowershell-interactive
$vmName = "IISVM$(Get-Random)"
$vNetName = "myIISSQLvNet"
$resourceGroup = "myIISSQLGroup"
New-AzureRMVm -Name $vmName -ResourceGroupName $resourceGroup -VirtualNetworkName $vNetName 
```

カスタム スクリプト拡張機能を使用して、IIS および .NET framework をインストールします。

```azurepowershell-interactive

Set-AzureRmVMExtension -ResourceGroupName $resourceGroup `
    -ExtensionName IIS `
    -VMName $vmName `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.4 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server,Web-Asp-Net45,NET-Framework-Features"}' `
    -Location EastUS
```

## <a name="azure-sql-vm"></a>Azure SQL 仮想マシン

SQL Server の構成済みの Azure Marketplace イメージを使用して Azure SQL VM を作成します。 まず VM を作成し、VM に SQL Server 拡張機能をインストールします。 


```azurepowershell-interactive
# Create user object. You get a pop-up prompting you to enter the credentials for the VM.
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create a subnet configuration
$vNet = Get-AzureRmVirtualNetwork -Name $vNetName -ResourceGroupName $resourceGroup
Add-AzureRmVirtualNetworkSubnetConfig -Name mySQLSubnet -VirtualNetwork $vNet -AddressPrefix "192.168.2.0/24"
Set-AzureRmVirtualNetwork -VirtualNetwork $vNet


# Create a public IP address and specify a DNS name
$pip = New-AzureRmPublicIpAddress -ResourceGroupName $resourceGroup -Location eastus `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4

# Create an inbound network security group rule for port 3389
$nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389 -Access Allow


# Create a network security group
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location eastus `
  -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP

# Create a virtual network card and associate with public IP address and NSG
$nic = New-AzureRmNetworkInterface -Name mySQLNic -ResourceGroupName $resourceGroup -Location eastus `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration
$vmConfig = New-AzureRmVMConfig -VMName mySQLVM -VMSize Standard_D1 | `
Set-AzureRmVMOperatingSystem -Windows -ComputerName mySQLVM -Credential $cred | `
Set-AzureRmVMSourceImage -PublisherName MicrosoftSQLServer -Offer SQL2014SP2-WS2012R2 -Skus Enterprise -Version latest | `
Add-AzureRmVMNetworkInterface -Id $nic.Id

# Create the VM
New-AzureRmVM -ResourceGroupName $resourceGroup -Location eastus -VM $vmConfig
```

[Set-AzureRmVMSqlServerExtension](/powershell/module/azurerm.compute/set-azurermvmsqlserverextension) を使用して [ SQL Server 拡張機能](/sql/virtual-machines-windows-sql-server-agent-extension.md) を Azure SQL 仮想マシンに追加します。

```azurepowershell-interactive
Set-AzureRmVMSqlServerExtension -ResourceGroupName $resourceGroup -VMName mySQLVM -name "SQLExtension"
```

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Azure PowerShell を使用して Azure SQL、IIS、および .NET スタックをインストールしました。 以下の方法について学習しました。

> [!div class="checklist"]
> * New-AzVM を使用して VM を作成する
> * VM に IIS と .NET Core SDK をインストールする
> * SQL Server を実行する VM を作成する
> * SQL Server 拡張機能をインストールする

SSL 証明書を使用して IIS Web サーバーをセキュリティ保護する方法については、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [SSL 証明書を使用してセキュリティ保護された IIS Web サーバー](tutorial-secure-web-server.md)

