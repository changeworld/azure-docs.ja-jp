---
title: "Azure で Windows VM をカスタマイズする | Microsoft Docs"
description: "Azure でカスタム スクリプト拡張機能と Key Vault を使用して Windows VM をカスタマイズする方法を説明します。"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 04/27/2017
ms.author: iainfou
ms.translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: 54920f3b6665ce5d74bf8025d44d5e16bd8a54b4
ms.contentlocale: ja-jp
ms.lasthandoff: 05/03/2017

---

# <a name="how-to-customize-a-windows-virtual-machine-in-azure"></a>Azure で Windows 仮想マシンをカスタマイズする方法
仮想マシン (VM) を迅速かつ一貫した方法で構成するには、一般的に、何らかの形で自動化することが望ましいです。 Windows VM をカスタマイズする一般的なアプローチとして、[Windows のカスタム スクリプト拡張機能](extensions-customscript.md)を使用する方法があります。 このチュートリアルでは、カスタム スクリプト拡張機能を使用して、IIS をインストールして構成し、基本的な Web サイトを実行できるようにする方法について説明します。

このチュートリアルの手順は、最新バージョンの [Azure PowerShell](/powershell/azure/overview) モジュールを使用して行うことができます。


## <a name="custom-script-extension-overview"></a>カスタム スクリプト拡張機能の概要
カスタム スクリプト拡張機能は、Azure VM でスクリプトをダウンロードし、実行します。 この拡張機能は、デプロイ後の構成、ソフトウェアのインストール、その他の構成や管理タスクに役立ちます。 スクリプトは、Azure ストレージや GitHub からダウンロードできます。また、拡張機能の実行時に Azure Portal に提供することもできます。

カスタム スクリプト拡張機能は Azure Resource Manager テンプレートと統合されており、Azure CLI、PowerShell、Azure Portal、または Azure 仮想マシン REST API を使用して実行することもできます。

カスタム スクリプト拡張機能は、Windows VM と Linux VM の両方で使用することができます。


## <a name="create-virtual-machine"></a>仮想マシンの作成
VM を作成する前に、[New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) を使用してリソース グループを作成します。 次の例では、*myResourceGroupAutomate* という名前のリソース グループを場所 *westus* に作成します。

```powershell
New-AzureRmResourceGroup -ResourceGroupName myResourceGroupAutomate -Location westus
```

次のように、[Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) を使用して VM の管理者のユーザー名とパスワードを設定します。

```powershell
$cred = Get-Credential
```

[New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) を使用して VM を作成できるようになりました。 次の例では、必要な仮想ネットワーク コンポーネント、OS の構成を作成し、*myVM* という名前の VM を作成します。

```powershell
# Create a subnet configuration
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
    -Name mySubnet `
    -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzureRmVirtualNetwork `
    -ResourceGroupName myResourceGroupAutomate `
    -Location westus `
    -Name myVnet `
    -AddressPrefix 192.168.0.0/16 `
    -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$publicIP = New-AzureRmPublicIpAddress `
    -ResourceGroupName myResourceGroupAutomate `
    -Location westus `
    -AllocationMethod Static `
    -IdleTimeoutInMinutes 4 `
    -Name "myPublicIP"

# Create an inbound network security group rule for port 3389
$nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig `
    -Name myNetworkSecurityGroupRuleRDP  `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 1000 `
    -SourceAddressPrefix * `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 3389 `
    -Access Allow

# Create an inbound network security group rule for port 80
$nsgRuleWeb = New-AzureRmNetworkSecurityRuleConfig `
    -Name myNetworkSecurityGroupRuleWWW  `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 1001 `
    -SourceAddressPrefix * `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 80 `
    -Access Allow

# Create a network security group
$nsg = New-AzureRmNetworkSecurityGroup `
    -ResourceGroupName myResourceGroupAutomate `
    -Location westus `
    -Name myNetworkSecurityGroup `
    -SecurityRules $nsgRuleRDP,$nsgRuleWeb

# Create a virtual network card and associate with public IP address and NSG
$nic = New-AzureRmNetworkInterface `
    -Name myNic `
    -ResourceGroupName myResourceGroupAutomate `
    -Location westus `
    -SubnetId $vnet.Subnets[0].Id `
    -PublicIpAddressId $publicIP.Id `
    -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration
$vmConfig = New-AzureRmVMConfig -VMName myVM -VMSize Standard_DS2 | `
Set-AzureRmVMOperatingSystem -Windows -ComputerName myVM -Credential $cred | `
Set-AzureRmVMSourceImage -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer -Skus 2016-Datacenter -Version latest | `
Add-AzureRmVMNetworkInterface -Id $nic.Id

New-AzureRmVM -ResourceGroupName myResourceGroupAutomate -Location westus -VM $vmConfig
```

リソースと VM が作成されるまで数分かかります。


## <a name="automate-iis-install"></a>IIS のインストールを自動化する
[Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) を使用して、カスタム スクリプト拡張機能をインストールします。 この拡張機能によって `powershell Add-WindowsFeature Web-Server` が実行され、IIS Web サーバーがインストールされます。次に、VM のホスト名を表示するように *Default.htm* ページが更新されます。

```powershell
Set-AzureRmVMExtension -ResourceGroupName myResourceGroupAutomate `
    -ExtensionName IIS `
    -VMName myVM `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.4 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
    -Location westus
```


## <a name="test-web-site"></a>Web サイトをテストする
ロード バランサーのパブリック IP アドレスを取得するには、[Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) を使用します。 次の例では、先ほど作成した *myPublicIP* の IP アドレスを取得しています。

```powershell
Get-AzureRmPublicIPAddress `
    -ResourceGroupName myResourceGroupAutomate `
    -Name myPublicIP | select IpAddress
```

このパブリック IP アドレスを Web ブラウザーに入力できます。 次の例のように、Web サイトが表示され、ロード バランサーによって負荷分散されたトラフィックの宛先となった VM のホスト名が表示されます。

![実行中の IIS Web サイト](./media/tutorial-automate-vm-deployment/running-iis-website.png)


## <a name="next-steps"></a>次のステップ

このチュートリアルでは、VM を初回起動時にカスタマイズする方法を説明しました。 次のチュートリアルに進み、カスタムの VM イメージを作成する方法を学習してください。

[カスタム VM イメージを作成する](./tutorial-custom-images.md)

