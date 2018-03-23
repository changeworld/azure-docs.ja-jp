---
title: 仮想ネットワーク ピアリングを使用して仮想ネットワークを接続する - PowerShell | Microsoft Docs
description: 仮想ネットワーク ピアリングを使用して仮想ネットワークを接続する方法について説明します。
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: ''
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/06/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: c7b3fa2b566ab02e7fb4a03055db83f1545895e8
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/08/2018
---
# <a name="connect-virtual-networks-with-virtual-network-peering-using-powershell"></a>PowerShell を使用して仮想ネットワーク ピアリングで仮想ネットワークを接続する

仮想ネットワーク ピアリングを使用して、仮想ネットワークを相互に接続できます。 仮想ネットワークをピアリングすると、両方の仮想ネットワークに存在するリソースが、同じ仮想ネットワーク内に存在する場合と同じ待ち時間と帯域幅で相互に通信できます。 この記事では、2 つの仮想ネットワークの作成とピアリングについて説明します。 学習内容は次のとおりです。

> [!div class="checklist"]
> * 2 つの仮想ネットワークを作成する
> * 仮想ネットワーク間のピアリングを作成する
> * ピアリングをテストする

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

PowerShell をインストールしてローカルで使用する場合、この記事では Azure PowerShell モジュール バージョン 3.6 以降が必要になります。 インストールされているバージョンを確認するには、` Get-Module -ListAvailable AzureRM` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-azurerm-ps)に関するページを参照してください。 PowerShell をローカルで実行している場合、`Login-AzureRmAccount` を実行して Azure との接続を作成することも必要です。 

## <a name="create-virtual-networks"></a>仮想ネットワークを作成する

仮想ネットワークを作成する前に、仮想ネットワークのリソース グループと、この記事で作成された他のすべてのリソースのリソース グループを作成する必要があります。 [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) を使用してリソース グループを作成します。 次の例では、*myResourceGroup* という名前のリソース グループを *eastus* に作成します。

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

*myVirtualNetwork2* 仮想ネットワークのアドレス プレフィックスは、*myVirtualNetwork1* 仮想ネットワークのアドレス プレフィックスとは重複しません。 アドレス プレフィックスが重複する仮想ネットワークをピアリングすることはできません。

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

ピアリングは 2 つの仮想ネットワークの間で行われますが、推移的ではありません。 このため、たとえば、*myVirtualNetwork2* を *myVirtualNetwork3* とピアリングしたい場合は、仮想ネットワーク *myVirtualNetwork2* と *myVirtualNetwork3* の間に追加ピアリングを作成する必要があります。 *myVirtualNetwork1* が *myVirtualNetwork2* とピアリングされていても、*myVirtualNetwork1* が *myVirtualNetwork3* ともピアリングされている場合は、*myVirtualNetwork1* のリソースがアクセスできるのは *myVirtualNetwork3* のリソースだけです。 

運用仮想ネットワークをピアリングする前に、[ピアリングの概要](virtual-network-peering-overview.md)、[ピアリングの管理](virtual-network-manage-peering.md)、および[仮想ネットワークの制限](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)について十分に理解しておくことをお勧めします。 この記事は、同じサブスクリプションや同じ場所にある 2 つの仮想ネットワーク間のピアリングについて説明していますが、[異なるリージョン](#register)や[異なる Azure サブスクリプション](create-peering-different-subscriptions.md#powershell)の仮想ネットワークをピアリングすることもできます。 [ハブとスポークのネットワーク設計](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering)をピアリングで作成することもできます。

## <a name="test-peering"></a>ピアリングをテストする

異なる仮想ネットワーク内にある仮想マシン間のピアリングを使用したネットワーク通信をテストするには、仮想マシンを各サブネットにデプロイし、仮想マシン間で通信します。 

### <a name="create-virtual-machines"></a>仮想マシンを作成する

各仮想ネットワークに仮想マシンを作成して、後の手順でそれらの間の通信を検証できるようにします。

[New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) を使って、仮想マシンを作成します。 次の例では、*myVirtualNetwork1* 仮想ネットワークで *myVm1* という名前の仮想マシンを作成します。 `-AsJob` オプションを使用すると、仮想マシンはバックグラウンドで作成されるため、次の手順に進むことができます。 メッセージが表示されたら、仮想マシンへのログインに使用するユーザー名とパスワードを入力します。

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

*myVirtualNetwork1* の *Subnet1* で使用できる最初の IP アドレスが 10.0.0.4 であるため、Azure により 10.0.0.4 が仮想マシンのプライベート IP アドレスとして自動的に割り当てられます。 

*myVirtualNetwork2* 仮想ネットワークに仮想マシンを作成します。

```azurepowershell-interactive
New-AzureRmVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork2" `
  -SubnetName "Subnet1" `
  -ImageName "Win2016Datacenter" `
  -Name "myVm2"
```

仮想マシンの作成には、数分かかります。 返される出力には表示されませんが、*myVirtualNetwork2* の *Subnet1* で使用できる最初の IP アドレスが 10.1.0.4 であるため、Azure では仮想マシンのプライベート IP アドレスとして 10.1.0.4 が自動的に割り当てられます。 

Azure で仮想マシンが作成され、PowerShell に出力が返されるまで、以降の手順に進まないでください。

### <a name="test-virtual-machine-communication"></a>仮想マシンの通信をテストする

インターネットから仮想マシンのパブリック IP アドレスに接続できます。 [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) を使用して、仮想マシンのパブリック IP アドレスを返します。 次の例では、*myVm1* 仮想マシンのパブリック IP アドレスを返しています。

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVm1 `
  -ResourceGroupName myResourceGroup | Select IpAddress
```

次のコマンドを使用して、お使いのローカル コンピューターから、*myVm1* 仮想マシンでリモート デスクトップ セッションを作成します。 `<publicIpAddress>` を前のコマンドで返された IP アドレスに置き換えます。

```
mstsc /v:<publicIpAddress>
```

リモート デスクトップ プロトコル (.rdp) ファイルが作成され、お使いのコンピューターにダウンロードされて開かれます。 ユーザー名とパスワードを入力し (仮想マシンの作成時に入力した資格情報を指定するために、**[その他]**、**[別のアカウントを使用する]** を選択することが必要な場合があります)、**[OK]** をクリックします。 サインイン処理中に証明書の警告が表示される場合があります。 **[はい]** または **[続行]** をクリックして接続処理を続行します。

コマンド プロンプトから、Windows ファイアウォール経由での ping を有効にし、後の手順でこの仮想マシンを *myVm2* から ping できるようにします。

```
netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
```

この記事ではテスト目的で ping を使用していますが、運用環境のデプロイでは Windows ファイアウォールで ICMP を許可することは推奨されません。

*myVm2* 仮想マシンに接続するには、*myVm1* 仮想マシン上でコマンド プロンプトから以下のコマンドを入力します。

```
mstsc /v:10.1.0.4
```

*myVm1* で ping を有効にしたため、*myVm2* 仮想マシン上でコマンド プロンプトから IP アドレスで ping できるようになりました。

```
ping 10.0.0.4
```

4 つの応答を受信します。 仮想マシンの IP アドレスではなく名前 (*myVm1*) で ping を実行すると、ping に失敗します。これは *myVm1* が不明なホスト名であるためです。 Azure の既定の名前解決は、同じ仮想ネットワークの仮想マシン間で機能しますが、異なる仮想ネットワークの仮想マシン間では機能しません。 仮想ネットワーク間で名前を解決するには、[独自の DNS サーバーをデプロイ](virtual-networks-name-resolution-for-vms-and-role-instances.md)するか、[Azure DNS プライベート ドメイン](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)を使用する必要があります。

*myVm1* と *myVm2* の両方への RDP セッションを切断します。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

必要なくなったら、[Remove-AzureRmResourcegroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) を使用して、リソース グループとその中のすべてのリソースを削除します。

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

**<a name="register"></a>グローバル仮想ネットワーク ピアリング プレビューへの登録**

同一リージョン内の仮想ネットワーク ピアリングの機能は一般公開されています。 異なるリージョン内の複数の仮想ネットワークのピアリングは、現在プレビュー段階です。 使用可能なリージョンについては、[仮想ネットワークの更新情報](https://azure.microsoft.com/updates/?product=virtual-network)を参照してください。 リージョン間で仮想ネットワークをピアリングするには、まず、プレビューに登録する必要があります。その場合、(ピアリングする各仮想ネットワークがあるサブスクリプション内で) 次の手順を実行します。

1. 次のコマンドを入力して、ピアリングする各仮想ネットワークがあるサブスクリプションをプレビューに登録します。

    ```powershell-interactive
    Register-AzureRmProviderFeature `
      -FeatureName AllowGlobalVnetPeering `
      -ProviderNamespace Microsoft.Network
    
    Register-AzureRmResourceProvider `
      -ProviderNamespace Microsoft.Network
    ```
2. 次のコマンドを入力して、プレビューに登録されていることを確認します。

    ```powershell-interactive    
    Get-AzureRmProviderFeature `
      -FeatureName AllowGlobalVnetPeering `
      -ProviderNamespace Microsoft.Network
    ```

    上記のコマンドを入力した後に受信する **RegistrationState** 出力が、両方のサブスクリプションに対して **Registered** になる前に、リージョンが異なる仮想ネットワークをピアリングしようとすると、失敗します。

## <a name="next-steps"></a>次の手順

この記事では、仮想ネットワーク ピアリングを使用して、2 つの仮想ネットワークを接続する方法を学習しました。 VPN 経由で[ご自身のコンピューターを仮想ネットワークに接続](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)し、仮想ネットワーク、またはピアリングされた仮想ネットワークのリソースを操作できます。

再利用可能なスクリプトのスクリプト サンプルに進み、仮想ネットワークの記事で説明する多くのタスクを完了します。

> [!div class="nextstepaction"]
> [仮想ネットワーク スクリプトのサンプル](../networking/powershell-samples.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
