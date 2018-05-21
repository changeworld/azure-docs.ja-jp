---
title: Azure Stack で PowerShell を使用して Linux 仮想マシンを作成する | Microsoft Docs
description: Azure Stack で PowerShell を使用して Linux 仮想マシンを作成します。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 03EE5929-4F05-47D7-B246-EA93D6FC47CD
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 04/24/2018
ms.author: mabrigg
ms.custom: mvc
ms.openlocfilehash: 1e2dbc6020dd317e96c4116811f8e3bf87680bfb
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/04/2018
---
# <a name="quickstart-create-a-linux-server-virtual-machine-by-using-powershell-in-azure-stack"></a>クイック スタート: Azure Stack で PowerShell を使用して Linux サーバー仮想マシンを作成する

*適用先: Azure Stack 統合システムと Azure Stack 開発キット*

Azure Stack の PowerShell を使用して、Ubuntu Server 16.04 LTS 仮想マシンを作成できます。 この記事の手順に従って仮想マシンを作成し、使用します。  この記事では、以下のことを実行する手順も示します。

* リモート クライアントを使用して仮想マシンに接続します。
* NGINX Web サーバーをインストールし、既定のホーム ページを表示します。
* 使用されていないリソースをクリーンアップします。

## <a name="prerequisites"></a>前提条件

* **Azure Stack Marketplace 内の Linux イメージ**

   Azure Stack Marketplace には、既定では Linux イメージが含まれていません。 必要な **Ubuntu Server 16.04 LTS** イメージを提供する Azure Stack オペレーターを取得します。 オペレーターは、「[Azure から Azure Stack に Marketplace の項目をダウンロードする](../azure-stack-download-azure-marketplace-item.md)」という記事に記載されている手順を使用できます。

* リソースを作成して管理するため、Azure Stack には Azure PowerShell の特定のバージョンが必要です。 Azure Stack 用に構成された PowerShell がない場合は、[開発キット](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop)または Windows ベースの外部クライアント ([VPN 経由で接続](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn)している場合) にサインインし、PowerShell の[インストール](azure-stack-powershell-install.md)と[構成](azure-stack-powershell-configure-user.md)の手順に従います。

* Windows ユーザー プロファイルの .ssh ディレクトリに保存された id_rsa.pub という名前の SSH 公開キー。 SSH キーの作成の詳細については、[Windows での SSH キーの作成](../../virtual-machines/linux/ssh-from-windows.md)に関するページを参照してください。

## <a name="create-a-resource-group"></a>リソース グループの作成

リソース グループは、Azure Stack リソースのデプロイと管理を行うことができる論理コンテナーです。 開発キットまたは Azure Stack 統合システムから、次のコード ブロックを実行してリソース グループを作成します。 このドキュメントではすべての変数に値を割り当てていますが、これらの値をそのまま使用することも、新しい値を割り当てることもできます。

```powershell
# Create variables to store the location and resource group names.
$location = "local"
$ResourceGroupName = "myResourceGroup"

New-AzureRmResourceGroup `
  -Name $ResourceGroupName `
  -Location $location
```

## <a name="create-storage-resources"></a>ストレージ リソースの作成

ストレージ アカウントを作成してから、Ubuntu Server 16.04 LTS イメージのストレージ コンテナーを作成します。

```powershell
# Create variables to store the storage account name and the storage account SKU information
$StorageAccountName = "mystorageaccount"
$SkuName = "Standard_LRS"

# Create a new storage account
$StorageAccount = New-AzureRMStorageAccount `
  -Location $location `
  -ResourceGroupName $ResourceGroupName `
  -Type $SkuName `
  -Name $StorageAccountName

Set-AzureRmCurrentStorageAccount `
  -StorageAccountName $storageAccountName `
  -ResourceGroupName $resourceGroupName

# Create a storage container to store the virtual machine image
$containerName = 'osdisks'
$container = New-AzureStorageContainer `
  -Name $containerName `
  -Permission Blob
```

## <a name="create-networking-resources"></a>ネットワーク リソースの作成

仮想ネットワーク、サブネット、パブリック IP アドレスを作成します。 これらのリソースは、仮想マシンにネットワーク接続を提供するために使用されます。

```powershell
# Create a subnet configuration
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
  -Name mySubnet `
  -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzureRmVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -Name MyVnet `
  -AddressPrefix 192.168.0.0/16 `
  -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$pip = New-AzureRmPublicIpAddress `
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -AllocationMethod Static `
  -IdleTimeoutInMinutes 4 `
  -Name "mypublicdns$(Get-Random)"

```

### <a name="create-a-network-security-group-and-a-network-security-group-rule"></a>ネットワーク セキュリティ グループとネットワーク セキュリティ グループの規則を作成する

ネットワーク セキュリティ グループは、受信規則と送信規則を使用して仮想マシンを保護します。 ポート 3389 に、受信リモート デスクトップ接続を許可する受信規則を作成し、ポート 80 に、受信 Web トラフィックを許可する受信規則を作成します。

```powershell
# Create an inbound network security group rule for port 22
$nsgRuleSSH = New-AzureRmNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleSSH  -Protocol Tcp `
-Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 22 -Access Allow

# Create an inbound network security group rule for port 80
$nsgRuleWeb = New-AzureRmNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleWWW  -Protocol Tcp `
-Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 80 -Access Allow

# Create a network security group
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName myResourceGroup -Location local `
-Name myNetworkSecurityGroup -SecurityRules $nsgRuleSSH,$nsgRuleWeb
```

### <a name="create-a-network-card-for-the-virtual-machine"></a>仮想マシン用のネットワーク カードを作成する

ネットワーク カードは、仮想マシンをサブネット、ネットワーク セキュリティ グループ、パブリック IP アドレスに接続します。

```powershell
# Create a virtual network card and associate it with public IP address and NSG
$nic = New-AzureRmNetworkInterface `
  -Name myNic `
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id `
  -NetworkSecurityGroupId $nsg.Id
```

## <a name="create-a-virtual-machine"></a>仮想マシンの作成

仮想マシンの構成を作成します。 この構成には、仮想マシンをデプロイするときに使用される設定が含まれています。 たとえば、ユーザー資格情報、サイズ、仮想マシン イメージなどです。

```powershell
# Define a credential object.
$UserName='demouser'
$securePassword = ConvertTo-SecureString ' ' -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ($UserName, $securePassword)

# Create the virtual machine configuration object
$VmName = "VirtualMachinelatest"
$VmSize = "Standard_D1"
$VirtualMachine = New-AzureRmVMConfig `
  -VMName $VmName `
  -VMSize $VmSize

$VirtualMachine = Set-AzureRmVMOperatingSystem `
  -VM $VirtualMachine `
  -Linux `
  -ComputerName "MainComputer" `
  -Credential $cred

$VirtualMachine = Set-AzureRmVMSourceImage `
  -VM $VirtualMachine `
  -PublisherName "Canonical" `
  -Offer "UbuntuServer" `
  -Skus "16.04-LTS" `
  -Version "latest"

$osDiskName = "OsDisk"
$osDiskUri = '{0}vhds/{1}-{2}.vhd' -f `
  $StorageAccount.PrimaryEndpoints.Blob.ToString(),`
  $vmName.ToLower(), `
  $osDiskName

# Sets the operating system disk properties on a virtual machine.
$VirtualMachine = Set-AzureRmVMOSDisk `
  -VM $VirtualMachine `
  -Name $osDiskName `
  -VhdUri $OsDiskUri `
  -CreateOption FromImage | `
  Add-AzureRmVMNetworkInterface -Id $nic.Id

# Configure SSH Keys
$sshPublicKey = Get-Content "$env:USERPROFILE\.ssh\id_rsa.pub"

# Adds the SSH Key to the virtual machine
Add-AzureRmVMSshPublicKey -VM $VirtualMachine `
 -KeyData $sshPublicKey `
 -Path "/home/azureuser/.ssh/authorized_keys"

# Create the virtual machine.
New-AzureRmVM `
  -ResourceGroupName $ResourceGroupName `
 -Location $location `
  -VM $VirtualMachine
```

## <a name="connect-to-the-virtual-machine"></a>仮想マシンへの接続

仮想マシンがデプロイされたら、仮想マシンの SSH 接続を構成します。 [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress?view=azurermps-4.3.1) コマンドを使用して、仮想マシンのパブリック IP アドレスを返します。

```powershell
Get-AzureRmPublicIpAddress -ResourceGroupName myResourceGroup | Select IpAddress
```

SSH がインストールされているクライアント システムから、次のコマンドを使用して仮想マシンに接続します。 Windows で作業している場合は、[Putty](http://www.putty.org/) を使用して接続を作成できます。

```
ssh <Public IP Address>
```

メッセージが表示されたら、ログイン ユーザーとして azureuser を入力します。 SSH キーを作成したときにパスフレーズを使用した場合は、そのパスフレーズを入力する必要があります。

## <a name="install-the-nginx-web-server"></a>NGINX Web サーバーのインストール

パッケージ リソースを更新し、最新の NGINX パッケージをインストールするため、次のスクリプトを実行します。

```bash
#!/bin/bash

# update package source
apt-get -y update

# install NGINX
apt-get -y install nginx
```

## <a name="view-the-nginx-welcome-page"></a>NGINX のようこそページの表示

NGINX がインストールされ、仮想マシン上のポート 80 が開かれたので、その仮想マシンのパブリック IP アドレスを使用して Web サーバーにアクセスできます。 Web ブラウザーを開き、```http://<public IP address>``` を参照します。

![NGINX Web サーバーのようこそページ](./media/azure-stack-quick-create-vm-linux-cli/nginx.png)

## <a name="clean-up-resources"></a>リソースのクリーンアップ

不要になったリソースをクリーンアップします。 [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup?view=azurermps-4.3.1) コマンドを使用して、これらのリソース グループを削除できます。 リソース グループとそのすべてのリソースを削除するには、次のコマンドを実行します。

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>次の手順

このクイック スタートでは、基本の Linux サーバー仮想マシンをデプロイしました。 Azure Stack 仮想マシンの詳細については、「[Azure Stack の仮想マシンに関する考慮事項](azure-stack-vm-considerations.md)」に進んでください。
