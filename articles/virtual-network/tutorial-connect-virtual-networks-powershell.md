---
title: 仮想ネットワーク ピアリングを使用して仮想ネットワークを接続する - PowerShell | Microsoft Docs
description: この記事では、Azure PowerShell を使って仮想ネットワーク ピアリングで仮想ネットワークを接続する方法を説明します。
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I want to connect two virtual networks so that virtual machines in one virtual network can communicate with virtual machines in the other virtual network.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/13/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: 3b4a67a06d628040d155a0fe2d78beb2eee25090
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2018
ms.locfileid: "38477764"
---
# <a name="connect-virtual-networks-with-virtual-network-peering-using-powershell"></a>PowerShell を使用して仮想ネットワーク ピアリングで仮想ネットワークを接続する

仮想ネットワーク ピアリングを使用して、仮想ネットワークを相互に接続できます。 仮想ネットワークをピアリングすると、それぞれの仮想ネットワークに存在するリソースが、あたかも同じ仮想ネットワーク内に存在するかのような待ち時間と帯域幅で相互に通信できます。 この記事では、次のことについて説明します:

* 2 つの仮想ネットワークを作成する
* 仮想ネットワーク ピアリングを使用して 2 つの仮想ネットワークを接続する
* 各仮想ネットワークに仮想マシン (VM) を展開する
* VM 間の通信

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

PowerShell をインストールしてローカルで使用する場合、この記事では Azure PowerShell モジュール バージョン 5.4.1 以降が必要になります。 インストールされているバージョンを確認するには、` Get-Module -ListAvailable AzureRM` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-azurerm-ps)に関するページを参照してください。 PowerShell をローカルで実行している場合、`Connect-AzureRmAccount` を実行して Azure との接続を作成することも必要です。 

## <a name="create-virtual-networks"></a>仮想ネットワークを作成する

仮想ネットワークを作成する前に、仮想ネットワークのリソース グループと、この記事で作成された他のすべてのリソースを作成する必要があります。 [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) を使用してリソース グループを作成します。 次の例では、*myResourceGroup* という名前のリソース グループを *eastus* に作成します。

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

[New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) を使用して仮想ネットワークを作成します。 次の例では、アドレス プレフィックスが *10.0.0.0/16* の、*myVirtualNetwork1* という名前の仮想ネットワークを作成します。

```azurepowershell-interactive
$virtualNetwork1 = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork1 `
  -AddressPrefix 10.0.0.0/16
```

[New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) を使用してサブネット構成を作成します。 次の例では、アドレス プレフィックスが 10.0.0.0/24 のサブネット構成を作成します。

```azurepowershell-interactive
$subnetConfig = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name Subnet1 `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork1
```

[Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/Set-AzureRmVirtualNetwork) を使って、仮想ネットワークにサブネット構成を書き込みます。これにより、サブネットが作成されます。

```azurepowershell-interactive
$virtualNetwork1 | Set-AzureRmVirtualNetwork
```

アドレス プレフィックスが 10.1.0.0/16 で 1 つのサブネットを持つ仮想ネットワークを作成します。

```azurepowershell-interactive
# Create the virtual network.
$virtualNetwork2 = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork2 `
  -AddressPrefix 10.1.0.0/16

# Create the subnet configuration.
$subnetConfig = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name Subnet1 `
  -AddressPrefix 10.1.0.0/24 `
  -VirtualNetwork $virtualNetwork2

# Write the subnet configuration to the virtual network.
$virtualNetwork2 | Set-AzureRmVirtualNetwork
```

## <a name="peer-virtual-networks"></a>仮想ネットワークをピアリングする

[Add-AzureRmVirtualNetworkPeering](/powershell/module/azurerm.network/add-azurermvirtualnetworkpeering) を使用してピアリングを作成します。 次の例では、*myVirtualNetwork1* を *myVirtualNetwork2* にピアリングします。

```azurepowershell-interactive
Add-AzureRmVirtualNetworkPeering `
  -Name myVirtualNetwork1-myVirtualNetwork2 `
  -VirtualNetwork $virtualNetwork1 `
  -RemoteVirtualNetworkId $virtualNetwork2.Id
```

前のコマンドの実行後に返された出力では、**PeeringState** が *Initiated* です。 このピアリングは、*myVirtualNetwork2* から *myVirtualNetwork1* へのピアリングを作成するまで *Initiated* 状態のままです。 *myVirtualNetwork2* から *myVirtualNetwork1* へのピアリングを作成します。 

```azurepowershell-interactive
Add-AzureRmVirtualNetworkPeering `
  -Name myVirtualNetwork2-myVirtualNetwork1 `
  -VirtualNetwork $virtualNetwork2 `
  -RemoteVirtualNetworkId $virtualNetwork1.Id
```

前のコマンドの実行後に返された出力では、**PeeringState** が *Connected* です。 Azure によって、*myVirtualNetwork1-myVirtualNetwork2* ピアリングのピアリング状態も *Connected* に変更されました。 [Get-AzureRmVirtualNetworkPeering](/powershell/module/azurerm.network/get-azurermvirtualnetworkpeering) を使用して、*myVirtualNetwork1-myVirtualNetwork2* ピアリングのピアリング状態が *Connected* に変更されたことを確認します。

```azurepowershell-interactive
Get-AzureRmVirtualNetworkPeering `
  -ResourceGroupName myResourceGroup `
  -VirtualNetworkName myVirtualNetwork1 `
  | Select PeeringState
```

両方の仮想ネットワークのピアリングの **PeeringState** が *Connected* になるまで、一方の仮想ネットワークのリソースともう一方の仮想ネットワークのリソースは通信できません。 

## <a name="create-virtual-machines"></a>仮想マシンを作成する

後で仮想ネットワーク間で通信できるように、各仮想ネットワーク内に VM を作成します。

### <a name="create-the-first-vm"></a>最初の VM を作成する

[New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) を使用して VM を作成します。 次の例では、*myVirtualNetwork1* 仮想ネットワークで *myVm1* という名前の VM を作成します。 `-AsJob` オプションを使用すると、VM はバックグラウンドで作成されるため、次の手順に進むことができます。 メッセージが表示されたら、VM へのログインに使用するユーザー名とパスワードを入力します。

```azurepowershell-interactive
New-AzureRmVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork1" `
  -SubnetName "Subnet1" `
  -ImageName "Win2016Datacenter" `
  -Name "myVm1" `
  -AsJob
```

### <a name="create-the-second-vm"></a>2 つ目の VM を作成する

```azurepowershell-interactive
New-AzureRmVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork2" `
  -SubnetName "Subnet1" `
  -ImageName "Win2016Datacenter" `
  -Name "myVm2"
```

VM の作成には数分かかります。 Azure で VM が作成され、PowerShell に出力が返されるまで、以降の手順に進まないでください。

## <a name="communicate-between-vms"></a>VM 間の通信

インターネットから VM のパブリック IP アドレスに接続できます。 [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) を使用して、VM のパブリック IP アドレスを返します。 次の例では、*myVm1* VM のパブリック IP アドレスを返しています。

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVm1 `
  -ResourceGroupName myResourceGroup | Select IpAddress
```

次のコマンドを使用して、お使いのローカル コンピューターから、*myVm1* VM でリモート デスクトップ セッションを作成します。 `<publicIpAddress>` を前のコマンドで返された IP アドレスに置き換えます。

```
mstsc /v:<publicIpAddress>
```

リモート デスクトップ プロトコル (.rdp) ファイルが作成され、お使いのコンピューターにダウンロードされて開かれます。 ユーザー名とパスワードを入力し (VM の作成時に入力した資格情報を指定するために、必要に応じて **[その他]**、**[別のアカウントを使う]** を選択することもあります)、**[OK]** をクリックします。 サインイン処理中に証明書の警告が表示される場合があります。 **[はい]** または **[続行]** をクリックして接続処理を続行します。

*myVm1* VM では、Windows ファイアウォールを介してインターネット制御メッセージ プロトコル (ICMP) を有効にして、後で PowerShell を使用して*myVm2* からこの VM に ping を実行できるようにします。

```powershell
New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
```

この記事では、VM 間の通信に ping を使用していますが、運用環境のデプロイでは Windows ファイアウォールで ICMP を許可することは推奨されません。

*myVm2* VM に接続するには、*myVm1* VM 上でコマンド プロンプトから以下のコマンドを入力します。

```
mstsc /v:10.1.0.4
```

*myVm1* で ping を有効にしたため、*myVm2* VM 上でコマンド プロンプトから IP アドレスで ping できるようになりました。

```
ping 10.0.0.4
```

4 つの応答を受信します。 *myVm1* と *myVm2* の両方への RDP セッションを切断します。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

必要なくなったら、[Remove-AzureRmResourcegroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) を使用して、リソース グループとその中のすべてのリソースを削除します。

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>次の手順

この記事では、仮想ネットワーク ピアリングで同じ Azure リージョン内の 2 つのネットワークを接続する方法を説明しました。 異なる[サポートされるリージョン](virtual-network-manage-peering.md#cross-region)内および[異なる Azure サブスクリプション](create-peering-different-subscriptions.md#powershell)内の仮想ネットワークをピアリングすることも、ピアリングを使って[ハブとスポーク ネットワーク設計](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering)を作成することもできます。 仮想ネットワーク ピアリングについて詳しくは、[仮想ネットワーク ピアリングの概要](virtual-network-peering-overview.md)および[仮想ネットワーク ピアリングの管理](virtual-network-manage-peering.md)に関するページをご覧ください。

[ユーザーのコンピューターを VPN 経由で仮想ネットワークに接続](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json)し、仮想ネットワーク、またはピアリングされた仮想ネットワークのリソースを操作できます。 仮想ネットワークの記事で説明する多くのタスクを完了するための再利用可能なスクリプトについては、[スクリプト サンプル](powershell-samples.md)をご覧ください。
