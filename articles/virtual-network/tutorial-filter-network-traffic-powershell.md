---
title: ネットワーク トラフィックをフィルター処理する - Azure PowerShell | Microsoft Docs
description: この記事では、ネットワーク セキュリティ グループと PowerShell を使用して、サブネットに対するネットワーク トラフィックをフィルター処理する方法について説明します。
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I want to filter network traffic to virtual machines that perform similar functions, such as web servers.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/30/2018
ms.author: jdial
ms.custom: mvc
ms.openlocfilehash: a37ed044275abfca23c83e7d17012b9afdc78f6c
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2018
ms.locfileid: "37906458"
---
# <a name="filter-network-traffic-with-a-network-security-group-using-powershell"></a>ネットワーク セキュリティ グループと PowerShell を使用してネットワーク トラフィックをフィルター処理する

ネットワーク セキュリティ グループを使用して、仮想ネットワーク サブネットとの間で送受信されるネットワーク トラフィックをフィルター処理できます。 ネットワーク セキュリティ グループには、IP アドレス、ポート、およびプロトコルでネットワーク トラフィックをフィルター処理するセキュリティ規則が含まれています。 セキュリティ規則は、サブネットに展開されたリソースに適用されます。 この記事では、次のことについて説明します:

* ネットワーク セキュリティ グループと規則を作成する
* 仮想ネットワークを作成し、ネットワーク セキュリティ グループをサブネットに関連付ける
* 仮想マシン (VM) をサブネットに展開する
* トラフィック フィルターをテストする

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

PowerShell をインストールしてローカルで使用する場合、この記事では Azure PowerShell モジュール バージョン 6.2.1 以降が必要になります。 インストールされているバージョンを確認するには、`Get-Module -ListAvailable AzureRM` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-azurerm-ps)に関するページを参照してください。 PowerShell をローカルで実行している場合、`Connect-AzureRmAccount` を実行して Azure との接続を作成することも必要です。

## <a name="create-a-network-security-group"></a>ネットワーク セキュリティ グループの作成

ネットワーク セキュリティ グループにはセキュリティ規則が含まれています。 セキュリティ規則には、送信元と送信先が指定されています。 送信元と送信先にはアプリケーション セキュリティ グループを指定できます。

### <a name="create-application-security-groups"></a>アプリケーション セキュリティ グループを作成する

まず [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) を使用して、この記事で作成したすべてのリソースのリソース グループを作成します。 次の例では、*eastus* の場所にリソース グループを作成します。 


```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

[New-AzureRmApplicationSecurityGroup](/powershell/module/azurerm.network/new-azurermapplicationsecuritygroup) を使用してアプリケーション セキュリティ グループを作成します。 アプリケーション セキュリティ グループを使用すると、同様のポート フィルター処理要件を持つサーバーをグループ化できます。 次の例では、2 つのアプリケーション セキュリティ グループを作成します。

```azurepowershell-interactive
$webAsg = New-AzureRmApplicationSecurityGroup `
  -ResourceGroupName myResourceGroup `
  -Name myAsgWebServers `
  -Location eastus

$mgmtAsg = New-AzureRmApplicationSecurityGroup `
  -ResourceGroupName myResourceGroup `
  -Name myAsgMgmtServers `
  -Location eastus
```

### <a name="create-security-rules"></a>セキュリティ規則を作成する

[New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig) を使用してセキュリティ規則を作成します。 次の例では、ポート 80 および 443 を介してインターネットから *myWebServers* アプリケーション セキュリティ グループに対して送信されるトラフィックを許可する規則を作成します。

```azurepowershell-interactive
$webRule = New-AzureRmNetworkSecurityRuleConfig `
  -Name "Allow-Web-All" `
  -Access Allow `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 100 `
  -SourceAddressPrefix Internet `
  -SourcePortRange * `
  -DestinationApplicationSecurityGroupId $webAsg.id `
  -DestinationPortRange 80,443

The following example creates a rule that allows traffic inbound from the internet to the *myMgmtServers* application security group over port 3389:

$mgmtRule = New-AzureRmNetworkSecurityRuleConfig `
  -Name "Allow-RDP-All" `
  -Access Allow `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 110 `
  -SourceAddressPrefix Internet `
  -SourcePortRange * `
  -DestinationApplicationSecurityGroupId $mgmtAsg.id `
  -DestinationPortRange 3389
```

この記事では、RDP (ポート 3389) が *myAsgMgmtServers* VM のインターネットに公開されています。 運用環境では、ポート 3389 をインターネットに公開せずに、[VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json) または[プライベート](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ネットワーク接続を使用して、管理する Azure リソースに接続することをお勧めします。

### <a name="create-a-network-security-group"></a>ネットワーク セキュリティ グループの作成

[New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup) を使用して、ネットワーク セキュリティ グループを作成します。 次の例では、*myNsg* という名前のネットワーク セキュリティ グループを作成します。 

```powershell-interactive
$nsg = New-AzureRmNetworkSecurityGroup `
  -ResourceGroupName myResourceGroup `
  -Location eastus `
  -Name myNsg `
  -SecurityRules $webRule,$mgmtRule
```

## <a name="create-a-virtual-network"></a>仮想ネットワークの作成

[New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) を使用して仮想ネットワークを作成します。 次の例では、*myVirtualNetwork* という名前の仮想ネットワークを作成します。

```azurepowershell-interactive
$virtualNetwork = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

[New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) を使用してサブネット構成を作成し、[Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork) を使用してサブネット構成を仮想ネットワークに書き込みます。 次の例では、*mySubnet* という名前のサブネットを仮想ネットワークに追加し、それに *myNsg* ネットワーク セキュリティ グループを関連付けます。

```azurepowershell-interactive
Add-AzureRmVirtualNetworkSubnetConfig `
  -Name mySubnet `
  -VirtualNetwork $virtualNetwork `
  -AddressPrefix "10.0.2.0/24" `
  -NetworkSecurityGroup $nsg
$virtualNetwork | Set-AzureRmVirtualNetwork
```

## <a name="create-virtual-machines"></a>仮想マシンを作成する

VM を作成する前に、[Get-AzureRmVirtualNetwork](/powershell/module/azurerm.network/get-azurermvirtualnetwork) を使用して、サブネットを持つ仮想ネットワーク オブジェクトを取得します。

```powershell-interactive
$virtualNetwork = Get-AzureRmVirtualNetwork `
 -Name myVirtualNetwork `
 -Resourcegroupname myResourceGroup
```
[New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) を使用して、各 VM のパブリック IP アドレスを作成します。

```powershell-interactive
$publicIpWeb = New-AzureRmPublicIpAddress `
  -AllocationMethod Dynamic `
  -ResourceGroupName myResourceGroup `
  -Location eastus `
  -Name myVmWeb

$publicIpMgmt = New-AzureRmPublicIpAddress `
  -AllocationMethod Dynamic `
  -ResourceGroupName myResourceGroup `
  -Location eastus `
  -Name myVmMgmt
```

[New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) で 2 つのネットワーク インターフェイスを作成し、パブリック IP アドレスをネットワーク インターフェイスに割り当てます。 次の例では、ネットワーク インターフェイスを作成し、それに *myVmWeb* パブリック IP アドレスを関連付けて、*myAsgWebServers* アプリケーション セキュリティ グループのメンバーにします。

```powershell-interactive
$webNic = New-AzureRmNetworkInterface `
  -Location eastus `
  -Name myVmWeb `
  -ResourceGroupName myResourceGroup `
  -SubnetId $virtualNetwork.Subnets[0].Id `
  -ApplicationSecurityGroupId $webAsg.Id `
  -PublicIpAddressId $publicIpWeb.Id
```

次の例では、ネットワーク インターフェイスを作成し、それに *myVmMgmt* パブリック IP アドレスを関連付けて、*myAsgMgmtServers* アプリケーション セキュリティ グループのメンバーにします。

```powershell-interactive
$mgmtNic = New-AzureRmNetworkInterface `
  -Location eastus `
  -Name myVmMgmt `
  -ResourceGroupName myResourceGroup `
  -SubnetId $virtualNetwork.Subnets[0].Id `
  -ApplicationSecurityGroupId $mgmtAsg.Id `
  -PublicIpAddressId $publicIpMgmt.Id
```

仮想ネットワークに 2 つの VM を作成して、後でトラフィックのフィルター処理を検証できるようにします。 

[New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig) を使用して VM 構成を作成し、[New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) を使用して VM を作成します。 次の例では、Web サーバーとして機能する VM を作成します。 `-AsJob` オプションを使用すると、VM はバックグラウンドで作成されるため、次の手順に進むことができます。 

```azurepowershell-interactive
# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

$webVmConfig = New-AzureRmVMConfig `
  -VMName myVmWeb `
  -VMSize Standard_DS1_V2 | `
Set-AzureRmVMOperatingSystem -Windows `
  -ComputerName myVmWeb `
  -Credential $cred | `
Set-AzureRmVMSourceImage `
  -PublisherName MicrosoftWindowsServer `
  -Offer WindowsServer `
  -Skus 2016-Datacenter `
  -Version latest | `
Add-AzureRmVMNetworkInterface `
  -Id $webNic.Id
New-AzureRmVM `
  -ResourceGroupName myResourceGroup `
  -Location eastus `
  -VM $webVmConfig `
  -AsJob
```

管理サーバーとして機能する VM を作成します。

```azurepowershell-interactive
# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create the web server virtual machine configuration and virtual machine.
$mgmtVmConfig = New-AzureRmVMConfig `
  -VMName myVmMgmt `
  -VMSize Standard_DS1_V2 | `
Set-AzureRmVMOperatingSystem -Windows `
  -ComputerName myVmMgmt `
  -Credential $cred | `
Set-AzureRmVMSourceImage `
  -PublisherName MicrosoftWindowsServer `
  -Offer WindowsServer `
  -Skus 2016-Datacenter `
  -Version latest | `
Add-AzureRmVMNetworkInterface `
  -Id $mgmtNic.Id
New-AzureRmVM `
  -ResourceGroupName myResourceGroup `
  -Location eastus `
  -VM $mgmtVmConfig
```

仮想マシンの作成には、数分かかります。 Azure で VM の作成が完了するまで、次の手順に進まないでください。

## <a name="test-traffic-filters"></a>トラフィック フィルターをテストする

[Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) を使用して、VM のパブリック IP アドレスを返します。 次の例では、*myVmMgmt* VM のパブリック IP アドレスを返しています。

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVmMgmt `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

次のコマンドを使用して、お使いのローカル コンピューターから、*myVmMgmt* VM でリモート デスクトップ セッションを作成します。 `<publicIpAddress>` を前のコマンドで返された IP アドレスに置き換えます。

```
mstsc /v:<publicIpAddress>
```

ダウンロードされた RDP ファイルを開きます。 メッセージが表示されたら、**[Connect]** を選択します。

VM の作成時に指定したユーザー名とパスワードを入力し (VM の作成時に入力した資格情報を指定するために、**[その他]**、**[別のアカウントを使う]** の選択が必要になる場合があります)、**[OK]** を選択します。 サインイン処理中に証明書の警告が表示される場合があります。 **[はい]** を選択して、接続処理を続行します。 
   
インターネットから、*myVmMgmt* VM に接続されているネットワーク インターフェイスが含まれている *myAsgMgmtServers* アプリケーション セキュリティ グループに対する送信でポート 3389 が許可されているため、この接続は成功します。

PowerShell から次のコマンドを使用して、*myVmMgmt* VM から *myVmWeb* VM へのリモート デスクトップ接続を作成します。

``` 
mstsc /v:myvmWeb
```

各ネットワーク セキュリティ グループ内の既定のセキュリティ規則では、仮想ネットワーク内のすべての IP アドレス間で、すべてのポートを介したトラフィックが許可されるため、この接続は成功します。 *myAsgWebServers* のセキュリティ規則は、インターネットからのポート 3389 を介した受信を許可しないため、インターネットから *myVmWeb* VM へのリモート デスクトップ接続を作成することはできません。

PowerShell から次のコマンドを使用して、Microsoft IIS を *myVmWeb* VM にインストールします。

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

IIS のインストールが完了したら、*myVmWeb* VM から切断します。*myVmMgmt* VM のリモート デスクトップ接続は保持されます。 IIS のようこそ画面を表示するには、インターネット ブラウザーを開き、http://myVmWeb にアクセスします。

*myVmMgmt* VM から切断します。

コンピューターで PowerShell から次のコマンドを入力し、*myVmWeb* サーバーのパブリック IP アドレスを取得します。

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVmWeb `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

Azure の外部から *myVmWeb* Web サーバーにアクセスできることを確認するには、コンピューターでインターネット ブラウザーを開き、`http://<public-ip-address-from-previous-step>` にアクセスします。 インターネットから、*myVmWeb* VM に接続されているネットワーク インターフェイスが含まれている *myAsgWebServers* アプリケーション セキュリティ グループに対する送信でポート 80 が許可されているため、この接続は成功します。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

必要なくなったら、[Remove-AzureRmResourcegroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) を使用して、リソース グループとその中のすべてのリソースを削除できます。

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>次の手順

この記事では、ネットワーク セキュリティ グループを作成し、それを仮想ネットワーク サブネットに関連付けました。 ネットワーク セキュリティ グループについて詳しくは、[ネットワーク セキュリティ グループの概要](security-overview.md)と[ネットワーク セキュリティ グループの管理](manage-network-security-group.md)に関する記事を参照してください。

Azure の既定では、サブネット間でトラフィックがルーティングされます。 代わりに、たとえばファイアウォールとして機能する VM を介してサブネット間でトラフィックをルーティングすることもできます。 Azure の既定では、サブネット間でトラフィックがルーティングされます。 代わりに、たとえばファイアウォールとして機能する VM を介してサブネット間でトラフィックをルーティングすることもできます。 その方法については、[ルート テーブルの作成](tutorial-create-route-table-powershell.md)に関する記事を参照してください。