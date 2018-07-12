---
title: クイック スタート - Azure PowerShell を使用して Linux VM を作成する | Microsoft Docs
description: このクイック スタートでは、Azure PowerShell を使用して Linux 仮想マシンを作成する方法について説明します
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/24/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 45b1597d3b61a9386fc015b5a7272d948fa5772b
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2018
ms.locfileid: "37930948"
---
# <a name="quickstart-create-a-linux-virtual-machine-in-azure-with-powershell"></a>クイック スタート: PowerShell を使用して Azure に Linux 仮想マシンを作成する

Azure PowerShell モジュールは、PowerShell コマンド ラインやスクリプトで Azure リソースを作成および管理するために使用します。 このクイック スタートでは、Azure PowerShell モジュールを使って、Ubuntu を実行する Linux 仮想マシン (VM) を Azure に展開する方法を示します。 次に、VM の動作を確認するために、VM に SSH 接続し、NGINX Web サーバーをインストールします。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

PowerShell をインストールしてローカルで使用する場合、このチュートリアルでは Azure PowerShell モジュール バージョン 5.7.0 以降が必要になります。 バージョンを確認するには、`Get-Module -ListAvailable AzureRM` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-azurerm-ps)に関するページを参照してください。 PowerShell をローカルで実行している場合、`Connect-AzureRmAccount` を実行して Azure との接続を作成することも必要です。

最後に、*id_rsa.pub* という名前の公開 SSH キーを Windows ユーザー プロファイルの *.ssh* ディレクトリに格納しておく必要があります。 SSH キーを作成および使用する方法の詳細については、[Azure 用の SSH キーの作成](ssh-from-windows.md)に関するページを参照してください。

## <a name="create-a-resource-group"></a>リソース グループの作成

[New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) を使用して Azure リソース グループを作成します。 リソース グループとは、Azure リソースの展開と管理に使用する論理コンテナーです。

```azurepowershell-interactive
New-AzureRmResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

## <a name="create-virtual-network-resources"></a>仮想ネットワークのリソースを作成する

仮想ネットワーク、サブネット、パブリック IP アドレスを作成します。 これらのリソースは、VM へのネットワーク接続を提供し、VM をインターネットに接続するために使われます。

```azurepowershell-interactive
# Create a subnet configuration
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name "mySubnet" -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzureRmVirtualNetwork -ResourceGroupName "myResourceGroup" -Location "EastUS" `
-Name "myVNET" -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$pip = New-AzureRmPublicIpAddress -ResourceGroupName "myResourceGroup" -Location "EastUS" `
-AllocationMethod Static -IdleTimeoutInMinutes 4 -Name "mypublicdns$(Get-Random)"
```

Azure ネットワーク セキュリティ グループとトラフィック規則を作成します。 ネットワーク セキュリティ グループは、受信規則と送信規則を使用して VM をセキュリティで保護します。 次の例では、TCP ポート 22 に対して SSH 接続を許可する受信規則を作成します。 受信 Web トラフィックを許可するため、TCP ポート 80 の受信規則も作成します。

```azurepowershell-interactive
# Create an inbound network security group rule for port 22
$nsgRuleSSH = New-AzureRmNetworkSecurityRuleConfig -Name "myNetworkSecurityGroupRuleSSH"  -Protocol "Tcp" `
-Direction "Inbound" -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 22 -Access "Allow"

# Create an inbound network security group rule for port 80
$nsgRuleWeb = New-AzureRmNetworkSecurityRuleConfig -Name "myNetworkSecurityGroupRuleWWW"  -Protocol "Tcp" `
-Direction "Inbound" -Priority 1001 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 80 -Access "Allow"

# Create a network security group
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName "myResourceGroup" -Location "EastUS" `
-Name "myNetworkSecurityGroup" -SecurityRules $nsgRuleSSH,$nsgRuleWeb
```

[New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) を使用して、仮想ネットワーク インターフェイス カード (NIC) を作成します。 仮想 NIC は、VM をサブネット、ネットワーク セキュリティ グループ、パブリック IP アドレスに接続します。

```azurepowershell-interactive
# Create a virtual network card and associate with public IP address and NSG
$nic = New-AzureRmNetworkInterface -Name "myNic" -ResourceGroupName "myResourceGroup" -Location "EastUS" `
-SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id
```

## <a name="create-a-virtual-machine"></a>仮想マシンの作成

仮想マシンの構成には、VM のイメージ、サイズ、認証オプションなど、VM の展開時に使われる設定が含まれています。 SSH の資格情報、OS の情報、VM のサイズを次のように定義します。

```azurepowershell-interactive
# Define a credential object
$securePassword = ConvertTo-SecureString ' ' -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ("azureuser", $securePassword)

# Create a virtual machine configuration
$vmConfig = New-AzureRmVMConfig -VMName "myVM" -VMSize "Standard_D1" | `
Set-AzureRmVMOperatingSystem -Linux -ComputerName "myVM" -Credential $cred -DisablePasswordAuthentication | `
Set-AzureRmVMSourceImage -PublisherName "Canonical" -Offer "UbuntuServer" -Skus "16.04-LTS" -Version "latest" | `
Add-AzureRmVMNetworkInterface -Id $nic.Id

# Configure SSH Keys
$sshPublicKey = Get-Content "$env:USERPROFILE\.ssh\id_rsa.pub"
Add-AzureRmVMSshPublicKey -VM $vmconfig -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"
```

これまでに定義した構成を組み合わせて、[New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) で仮想マシンを作成します。

```azurepowershell-interactive
New-AzureRmVM -ResourceGroupName "myResourceGroup" -Location eastus -VM $vmConfig
```

## <a name="connect-to-virtual-machine"></a>仮想マシンへの接続

展開が完了した後、SSH で VM に接続します。 VM の動作を確認するために、NGINX Web サーバーがインストールされます。

VM のパブリック IP アドレスを確認するには、[Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) を使います。

```azurepowershell-interactive
Get-AzureRmPublicIpAddress -ResourceGroupName "myResourceGroup" | Select "IpAddress"
```

VM に接続するには SSH クライアントを使います。 Web ブラウザーから Azure Cloud Shell を使うか、Windows を使っている場合は [Putty](ssh-from-windows.md) または [Windows Subsystem for Linux](/windows/wsl/install-win10) を使うことができます。 実際の VM のパブリック IP アドレスを指定してください。

```bash
ssh azureuser@IpAddress
```

メッセージが表示されたら、ログイン ユーザー名には *azureuser* を指定します。 SSH キーでパスフレーズが使われている場合は、求められたら入力する必要があります。

## <a name="install-web-server"></a>Web サーバーのインストール

VM の動作を確認するために、NGINX Web サーバーをインストールします。 パッケージ ソースを更新して最新の NGINX パッケージをインストールするには、SSH セッションから次のコマンドを実行します。

```bash
# update packages
sudo apt-get -y update

# install NGINX
sudo apt-get -y install nginx
```

完了したら、`exit` を使って SSH セッションを終了します

## <a name="view-the-web-server-in-action"></a>動作中の Web サーバーを表示する

NGINX をインストールし、VM のポート 80 をインターネットから開いたら、任意の Web ブラウザーを使用して NGINX の既定のようこそページを表示します。 前の手順で取得した VM のパブリック IP アドレスを使用します。 次の例は、既定の NGINX Web サイトを示しています。

![NGINX の既定のサイト](./media/quick-create-cli/nginx.png)

## <a name="clean-up-resources"></a>リソースのクリーンアップ

必要がなくなったら、[Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) コマンドレットを使って、リソース グループ、VM、およびすべての関連リソースを削除できます。

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>次の手順

このクイック スタートでは、単純な仮想マシンをデプロイし、ネットワーク セキュリティ グループと規則を作成し、基本的な Web サーバーをインストールしました。 Azure 仮想マシンの詳細については、Linux VM のチュートリアルを参照してください。

> [!div class="nextstepaction"]
> [Azure Linux 仮想マシンのチュートリアル](./tutorial-manage-vm.md)
