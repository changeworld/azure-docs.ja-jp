---
title: ネットワーク トラフィックをルーティングする - PowerShell | Microsoft Docs
description: PowerShell を使用してルート テーブルでネットワーク トラフィックをルーティングする方法を説明します。
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/05/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: f91b143c75a82aa6760796770b3ae4d0e4ec53dd
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/08/2018
---
# <a name="route-network-traffic-with-a-route-table-using-powershell"></a>PowerShell を使用してルート テーブルでネットワーク トラフィックをルーティングする

既定では、仮想ネットワーク内のすべてのサブネット間でトラフィックが自動的にルーティングされます。 Azure の既定のルーティングは、独自のルートを作成して上書きすることができます。 カスタム ルートを作成する機能は、たとえば、サブネット間でファイアウォールを越えてトラフィックをルーティングしたい場合に便利です。 この記事では、次の方法について説明します。

> [!div class="checklist"]
> * ルート テーブルの作成
> * ルートの作成
> * 仮想ネットワーク サブネットへのルート テーブルの関連付け
> * ルーティングのテスト
> * ルーティングのトラブルシューティング

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

PowerShell をインストールしてローカルで使用する場合、この記事では Azure PowerShell モジュール バージョン 5.4.1 以降が必要になります。 インストールされているバージョンを確認するには、`Get-Module -ListAvailable AzureRM` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-azurerm-ps)に関するページを参照してください。 PowerShell をローカルで実行している場合、`Login-AzureRmAccount` を実行して Azure との接続を作成することも必要です。 

## <a name="create-a-route-table"></a>ルート テーブルの作成

既定では、仮想ネットワークのすべてのサブネット間でトラフィックがルーティングされます。 Azure の既定のルートの詳細については、「[システム ルート](virtual-networks-udr-overview.md)」を参照してください。 Azure の既定のルーティングを上書きするには、ルートを含むルート テーブルを作成し、そのルート テーブルを仮想ネットワーク サブネットに関連付けます。

ルート テーブルを作成する前に、[New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) を使用してリソース グループを作成します。 次の例では、この記事で作成されるすべてのリソースに対して、*myResourceGroup* という名前のリソース グループを作成します。 

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

[New-AzureRmRouteTable](/powershell/module/azurerm.network/new-azurermroutetable) を使用してルート テーブルを作成します。 次の例では、*myRouteTablePublic* という名前のルート テーブルを作成します。

```azurepowershell-interactive
$routeTablePublic = New-AzureRmRouteTable `
  -Name 'myRouteTablePublic' `
  -ResourceGroupName myResourceGroup `
  -location EastUS
```

## <a name="create-a-route"></a>ルートの作成

ルート テーブルには、0 個以上のルートが含まれます。 [Get-AzureRmRouteTable](/powershell/module/azurerm.network/get-azurermroutetable) を使用してルート テーブル オブジェクトを取得してから、[Add-AzureRmRouteConfig](/powershell/module/azurerm.network/add-azurermrouteconfig) でルートを作成します。その後、[Set-AzureRmRouteTable](/powershell/module/azurerm.network/set-azurermroutetable) を使ってルート構成をルート テーブルに書き込みます。 

```azurepowershell-interactive
Get-AzureRmRouteTable `
  -ResourceGroupName "myResourceGroup" `
  -Name "myRouteTablePublic" `
  | Add-AzureRmRouteConfig `
  -Name "ToPrivateSubnet" `
  -AddressPrefix 10.0.1.0/24 `
  -NextHopType "VirtualAppliance" `
  -NextHopIpAddress 10.0.2.4 `
 | Set-AzureRmRouteTable
```

このルートでは、10.0.1.0/24 アドレス プレフィックス宛のすべてのトラフィックが、IP アドレス 10.0.2.4 のネットワーク仮想アプライアンス経由でリダイレクトされます。 ネットワーク仮想アプライアンスと指定されたアドレス プレフィックスを持つサブネットは、以降の手順で作成されます。 サブネット間でトラフィックが直接ルーティングされる Azure の既定のルーティングは上書きされます。 それぞれのルートで次ホップの種類が指定されます。 次ホップの種類は、Azure に対してトラフィックのルーティング方法を指示します。 この例では、次ホップの種類は *VirtualAppliance* です。 Azure で使用できるすべての次ホップの種類の詳細については、[次ホップの種類](virtual-networks-udr-overview.md#custom-routes)に関するページをご覧ください。

## <a name="associate-a-route-table-to-a-subnet"></a>サブネットへのルート テーブルの関連付け

ルート テーブルをサブネットに関連付ける前に、仮想ネットワークとサブネットを作成する必要があります。 [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) を使用して仮想ネットワークを作成します。 次の例では、アドレス プレフィックスが *10.0.0.0/16* の、*myVirtualNetwork* という名前の仮想ネットワークを作成します。

```azurepowershell-interactive
$virtualNetwork = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

[New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) を使用して 3 つのサブネット構成を作成することで、3 つのサブネットを作成します。 次の例では、"*パブリック*"、"*プライベート*"、および *DMZ* サブネット用の 3 つのサブネット構成を作成します。

```azurepowershell-interactive
$subnetConfigPublic = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name Public `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork

$subnetConfigPrivate = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name Private `
  -AddressPrefix 10.0.1.0/24 `
  -VirtualNetwork $virtualNetwork

$subnetConfigDmz = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name DMZ `
  -AddressPrefix 10.0.2.0/24 `
  -VirtualNetwork $virtualNetwork
```

アドレス プレフィックスは、仮想ネットワーク用に定義されたアドレス プレフィックスの範囲内にある必要があります。 サブネットのアドレス プレフィックスは互いに重複することはできません。

[Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/Set-AzureRmVirtualNetwork) を使用して、仮想ネットワークにサブネット構成を書き込みます。これにより、仮想ネットワークにサブネットが作成されます。

```azurepowershell-interactive
$virtualNetwork | Set-AzureRmVirtualNetwork
```

1 つのルート テーブルを 0 個以上のサブネットに関連付けることができます。 サブネットには、0 個または 1 個のルート テーブルを関連付けることができます。 サブネットからの送信トラフィックは、Azure の既定のルート、およびサブネットに関連付けるルート テーブルに追加した任意のカスタム ルートに基づいてルーティングされます。 [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig) を使用して *myRouteTablePublic* ルート テーブルを "*パブリック*" サブネットに関連付け、[Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork) を使用してサブネット構成を仮想ネットワークに書き込みます。

```azurepowershell-interactive
Set-AzureRmVirtualNetworkSubnetConfig `
  -VirtualNetwork $virtualNetwork `
  -Name 'Public' `
  -AddressPrefix 10.0.0.0/24 `
  -RouteTable $routeTablePublic | `
Set-AzureRmVirtualNetwork
```

運用で使用するためにルート テーブルをデプロイする前に、[Azure でのルーティング](virtual-networks-udr-overview.md)と [Azure の制限](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)について十分に理解しておくことをお勧めします。

## <a name="test-routing"></a>ルーティングをテストする

ルーティングをテストするために、仮想マシンを作成します。この仮想マシンは、前の手順で作成したルートが経由するネットワーク仮想アプライアンスとして機能します。 ネットワーク仮想アプライアンスを作成したら、仮想マシンを "*パブリック*" サブネットおよび "*プライベート*" サブネットにデプロイします。 その後、"*パブリック*" サブネットから "*プライベート*" サブネットへのトラフィックを、ネットワーク仮想アプライアンスを経由するようにルーティングします。

### <a name="create-a-network-virtual-appliance"></a>ネットワーク仮想アプライアンスを作成する

ネットワーク アプリケーションを実行する仮想マシンは、ネットワーク仮想アプライアンスと呼ばれることがよくあります。 ネットワーク仮想アプライアンスは、通常、ネットワーク アプリケーションで構成されているロジックに基づいて、ネットワーク トラフィックを受信し、何らかのアクションを実行してから、ネットワーク トラフィックを転送または破棄します。 

#### <a name="create-a-network-interface"></a>ネットワーク インターフェイスの作成

前の手順では、次ホップの種類としてネットワーク仮想アプライアンスを指定したルートを作成しました。 ネットワーク アプリケーションを実行する仮想マシンは、ネットワーク仮想アプライアンスと呼ばれることがよくあります。 運用環境でデプロイするネットワーク仮想アプライアンスは、ほとんどの場合、構成済みの仮想マシンです。 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?search=network%20virtual%20appliance&page=1) では、いくつかのネットワーク仮想アプライアンスを入手できます。 この記事では、基本的な仮想マシンを作成します。

仮想マシンには 1 つ以上のネットワーク インターフェイスがアタッチされており、これによって仮想マシンはネットワークと通信することができます。 ネットワーク インターフェイスが、自身に送信されてきたネットワーク トラフィックのうち、自身の IP アドレス宛てではないものを転送できるように、このネットワーク インターフェイスに対して IP 転送を有効にする必要があります。 ネットワーク インターフェイスを作成する前に、[Get-AzureRmVirtualNetwork](/powershell/module/azurerm.network/get-azurermvirtualnetwork) を使用して仮想ネットワーク ID を取得し、次に [Get-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/get-azurermvirtualnetworksubnetconfig) を使用してサブネット ID を取得する必要があります。 [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) を使用して、IP 転送が有効な *DMZ* サブネットにネットワーク インターフェイスを作成します。

```azurepowershell-interactive
# Retrieve the virtual network object into a variable.
$virtualNetwork=Get-AzureRmVirtualNetwork `
  -Name myVirtualNetwork `
  -ResourceGroupName myResourceGroup

# Retrieve the subnet configuration into a variable.
$subnetConfigDmz = Get-AzureRmVirtualNetworkSubnetConfig `
  -Name DMZ `
  -VirtualNetwork $virtualNetwork

# Create the network interface.
$nic = New-AzureRmNetworkInterface `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name 'myVmNva' `
  -SubnetId $subnetConfigDmz.Id `
  -EnableIPForwarding
```

#### <a name="create-a-virtual-machine"></a>仮想マシンの作成

仮想マシンを作成して、既存のネットワーク インターフェイスをその仮想マシンにアタッチするには、[New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig) を使用して、まず仮想マシン構成を作成しておく必要があります。 構成には、前の手順で作成したネットワーク インターフェイスを含めます。 ユーザー名とパスワードの入力が求められたら、仮想マシンへのログインに使用するユーザー名とパスワードを選択します。 

```azurepowershell-interactive
# Create a credential object.
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create a virtual machine configuration.
$vmConfig = New-AzureRmVMConfig `
  -VMName 'myVmNva' `
  -VMSize Standard_DS2 | `
  Set-AzureRmVMOperatingSystem -Windows `
    -ComputerName 'myVmNva' `
    -Credential $cred | `
  Set-AzureRmVMSourceImage `
    -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer `
    -Skus 2016-Datacenter `
    -Version latest | `
  Add-AzureRmVMNetworkInterface -Id $nic.Id
```

[New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) を使用して、仮想マシン構成を使用する仮想マシンを作成します。 次の例では、*myVmNva* という名前の仮想マシンを作成します。 

```azurepowershell-interactive
$vmNva = New-AzureRmVM `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -VM $vmConfig `
  -AsJob
```

`-AsJob` オプションを使用すると、仮想マシンはバックグラウンドで作成されるため、次の手順に進むことができます。 メッセージが表示されたら、仮想マシンへのログインに使用するユーザー名とパスワードを入力します。 運用環境でデプロイするネットワーク仮想アプライアンスは、ほとんどの場合、構成済みの仮想マシンです。 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?search=network%20virtual%20appliance&page=1) では、いくつかのネットワーク仮想アプライアンスを入手できます。

*myVirtualNetwork* の *DMZ* サブネットで使用できる最初の IP アドレスが 10.0.2.4 であるため、Azure では仮想マシンのプライベート IP アドレスとして 10.0.2.4 が割り当てられます。

### <a name="create-virtual-machines"></a>仮想マシンを作成する

後の手順で、"*パブリック*" サブネットからのトラフィックがネットワーク仮想アプライアンス経由で "*プライベート*" サブネットにルーティングされていることを検証できるように、仮想ネットワークに 2 つの仮想マシンを作成します。 

[New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) を使用して、"*パブリック*" サブネット内に仮想マシンを作成します。 次の例では、*myVirtualNetwork* 仮想ネットワークの*パブリック* サブネットに、*myVmWeb* という名前の仮想マシンを作成します。 

```azurepowershell-interactive
New-AzureRmVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork" `
  -SubnetName "Public" `
  -ImageName "Win2016Datacenter" `
  -Name "myVmWeb" `
  -AsJob
```

*myVirtualNetwork* の "*パブリック*" サブネットで使用できる最初の IP アドレスが 10.0.1.4 であるため、Azure では仮想マシンのプライベート IP アドレスとして 10.0.0.4 が割り当てられます。

*プライベート* サブネットに仮想マシンを作成します。

```azurepowershell-interactive
New-AzureRmVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork" `
  -SubnetName "Private" `
  -ImageName "Win2016Datacenter" `
  -Name "myVmMgmt"
```

仮想マシンの作成には、数分かかります。 *myVirtualNetwork* の "*プライベート*" サブネットで使用できる最初の IP アドレスが 10.0.1.4 であるため、Azure では仮想マシンのプライベート IP アドレスとして 10.0.1.4 が割り当てられます。 

仮想マシンが作成され、PowerShell に出力が返されるまでは、次の手順に進まないでください。

### <a name="route-traffic-through-a-network-virtual-appliance"></a>ネットワーク仮想アプライアンス経由のトラフィックのルーティング

[Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) を使用して、*myVmWeb* 仮想マシンと *myVmMgmt* 仮想マシンへの Windows ファイアウォールを介した ICMP 受信を有効にし、後の手順で tracert を使って仮想マシン間の通信をテストできるようにします。

```powershell-interactive
Set-AzureRmVMExtension `
  -ResourceGroupName myResourceGroup `
  -VMName myVmWeb `
  -ExtensionName AllowICMP `
  -Publisher Microsoft.Compute `
  -ExtensionType CustomScriptExtension `
  -TypeHandlerVersion 1.8 `
  -SettingString '{"commandToExecute": "netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow"}' `
  -Location EastUS

Set-AzureRmVMExtension `
  -ResourceGroupName myResourceGroup `
  -VMName myVmMgmt `
  -ExtensionName AllowICMP `
  -Publisher Microsoft.Compute `
  -ExtensionType CustomScriptExtension `
  -TypeHandlerVersion 1.8 `
  -SettingString '{"commandToExecute": "netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow"}' `
  -Location EastUS
```

前のコマンドは、完了するまで数分かかる場合があります。 コマンドが完了し、PowerShell に出力が返されるまでは、次の手順に進まないでください。 この記事では tracert を使用してルーティングをテストしていますが、運用環境のデプロイでは Windows ファイアウォールで ICMP を許可することは推奨されません。

インターネットから仮想マシンのパブリック IP アドレスに接続します。 [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) を使用して、仮想マシンのパブリック IP アドレスを返します。 次の例では、*myVmMgmt* 仮想マシンのパブリック IP アドレスを返します。

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVmMgmt `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

次のコマンドを使用して、ローカル コンピューターから、*myVmMgmt* 仮想マシンを使用してリモート デスクトップ セッションを作成します。 `<publicIpAddress>` を前のコマンドで返された IP アドレスに置き換えます。

```
mstsc /v:<publicIpAddress>
```

リモート デスクトップ プロトコル (.rdp) ファイルが作成され、お使いのコンピューターにダウンロードされて開かれます。 仮想マシンの作成時に指定したユーザー名とパスワードを入力し (仮想マシンの作成時に入力した資格情報を指定するために、**[その他]**、**[別のアカウントを使う]** の選択が必要になる場合があります)、**[OK]** を選択します。 サインイン処理中に証明書の警告が表示される場合があります。 **[はい]** または **[続行]** をクリックして接続処理を続行します。 

[IP 転送の有効化](#enable-ip-forwarding)に関するセクションで、仮想マシンのネットワーク インターフェイスに対して Azure 内での IP 転送を有効にしました。 仮想マシン内では、オペレーティング システム、または仮想マシン内で実行中のアプリケーションも、ネットワーク トラフィックを転送できる必要があります。 運用環境にネットワーク仮想アプライアンスをデプロイすると、アプライアンスは、通常、トラフィックを転送する前に、フィルター処理、ログ記録などの機能を実行します。 ただし、この記事では、オペレーティング システムは、受信したすべてのトラフィックを単純に転送するものとします。 *myVmNva* のオペレーティング システム内での IP 転送を有効にします。

*myVmMgmt* 仮想マシンでコマンド プロンプトを使用して、*myVmNva* 仮想マシンにリモート デスクトップ接続します。

``` 
mstsc /v:myVmNva
```
    
*myVmNva* 仮想マシンのオペレーティング システム内で IP 転送を有効にするには、*myVmNva* 仮想マシンの PowerShell で次のコマンドを入力します。

```powershell
Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1
```
    
*myVmNva* 仮想マシンを再起動します。これによりリモート デスクトップ セッションが切断されますが、ユーザーが開いた *myVmMgmt* 仮想マシンに開いたリモート デスクトップ セッションはそのままです。

*myVmNva* 仮想マシンの再起動後、次のコマンドを使用して、*myVmMgmt* 仮想マシンから *myVmWeb* 仮想マシンへのネットワーク トラフィックのルーティングをテストします。

```
tracert myvmweb
```

応答は次の例のようになります。

```
Tracing route to myvmweb.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.0.4]
over a maximum of 30 hops:
    
1     1 ms     1 ms     1 ms  10.0.0.4
  
Trace complete.
```

トラフィックが *myVmMgmt* 仮想マシンから *myVmWeb* 仮想マシンに直接ルーティングされているのがわかります。 Azure の既定のルートでは、サブネット間でトラフィックが直接ルーティングされます。

次のコマンドを使用して、*myVmMgmt* 仮想マシンから *myVmWeb* 仮想マシンにリモート デスクトップ接続します。

``` 
mstsc /v:myVmWeb
```

*myVmWeb* 仮想マシンから *myVmMgmt* 仮想マシンへのネットワーク トラフィックのルーティングをテストするには、コマンド プロンプトから次のコマンドを入力します。

```
tracert myvmmgmt
```

応答は次の例のようになります。

```
Tracing route to myvmmgmt.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.1.4]
over a maximum of 30 hops:
        
1    <1 ms     *        1 ms  10.0.2.4
2     1 ms     1 ms     1 ms  10.0.1.4
        
Trace complete.
```

最初のホップが 10.0.2.4 であることがわかります。これは、ネットワーク仮想アプライアンスのプライベート IP アドレスです。 2 番目のホップは 10.0.1.4 です。これは、*myVmMgmt* 仮想マシンのプライベート IP アドレスです。 *myRouteTablePublic* ルート テーブルに追加され、"*パブリック*" サブネットに関連付けられているルートにより、Azure はトラフィックを "*プライベート*" サブネットに直接ルーティングするのではなく、NVA 経由でルーティングするようになります。

*myVmWeb* 仮想マシンと *myVmMgmt* 仮想マシンの両方へのリモート デスクトップ セッションを閉じます。

## <a name="troubleshoot-routing"></a>ルーティングのトラブルシューティング

前の手順で説明したように、Azure によって適用される既定のルートは、必要に応じて独自のルートで上書きすることができます。 場合によっては、トラフィックが意図したとおりにルーティングされないことがあります。 EastUS リージョンに Network Watcher がまだない場合は、[New-AzureRmNetworkWatcher](/powershell/module/azurerm.network/new-azurermnetworkwatcher) を使用して、そのリージョンで Network Watcher を有効にします。

```azurepowershell-interactive
# Enable network watcher for east region, if you don't already have a network watcher enabled for the region:
$nw = New-AzureRmNetworkWatcher `
 -Location eastus `
 -Name myNetworkWatcher_eastus `
 -ResourceGroupName myResourceGroup
```

[Get-AzureRmNetworkWatcherNextHop](/powershell/module/azurerm.network/get-azurermnetworkwatchernexthop) を使用して、2 つの仮想マシンの間でどのようにトラフィックがルーティングされているかを確認します。 たとえば、次のコマンドでは、*myVmWeb* (10.0.0.4) 仮想マシンから *myVmMgmt* (10.0.1.4) 仮想マシンへのトラフィック ルーティングをテストします。

```azurepowershell-interactive
$vmWeb = Get-AzureRmVM `
  -Name myVmWeb `
  -ResourceGroupName myResourceGroup

Get-AzureRmNetworkWatcherNextHop `
  -DestinationIPAddress 10.0.1.4 `
  -NetworkWatcherName myNetworkWatcher_eastus `
  -ResourceGroupName myResourceGroup `
  -SourceIPAddress 10.0.0.4 `
  -TargetVirtualMachineId $vmWeb.Id
```
少し待つと、次の出力が返されます。

```azurepowershell
NextHopIpAddress    NextHopType       RouteTableId
------------------  ---------------- ---------------------------------------------------------------------------------------------------------------------------
10.0.2.4            VirtualAppliance  /subscriptions/<Subscription-Id>/resourceGroups/myResourceGroup/providers/Microsoft.Network/routeTables/myRouteTablePublic
```

この出力により、*myVmWeb* から *myVmMgmt* へのトラフィックの次ホップの IP アドレスが 10.0.2.4 (*myVmNva* 仮想マシン) で、次ホップの種類が *VirtualAppliance* であること、また、ルーティングを発生させるルート テーブルが *myRouteTablePublic* であることがわかります。

各ネットワーク インターフェイスの有効なルートは、Azure の既定のルートと、ユーザーが定義した任意のルートの組み合わせとなります。 仮想マシンのネットワーク インターフェイスに対して有効なすべてのルートを表示するには、[Get-AzureRmEffectiveRouteTable](/powershell/module/azurerm.network/get-azurermeffectiveroutetable) を使用します。 たとえば、*myVmWeb* 仮想マシンの *myVmWeb* ネットワーク インターフェイスに対して有効なルートを表示するには、次のコマンドを入力します。

```azurepowershell-interactive
Get-AzureRmEffectiveRouteTable `
  -NetworkInterfaceName myVmWeb `
  -ResourceGroupName myResourceGroup `
  | Format-Table
```

すべての既定のルートと、前の手順で追加したルートが返されます。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

必要なくなったら、[Remove-AzureRmResourcegroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) を使用して、リソース グループとその中のすべてのリソースを削除します。

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>次の手順

この記事では、ルート テーブルを作成し、それをサブネットに関連付けました。 トラフィックをパブリック サブネットからプライベート サブネットにルーティングするネットワーク仮想アプライアンスを作成しました。 仮想ネットワーク内では多数の Azure リソースをデプロイできますが、一部の Azure PaaS サービスのリソースは仮想ネットワークにデプロイできなません。 ただし、一部の Azure PaaS サービスのリソースへのアクセスを、仮想ネットワーク サブネットからのトラフィックのみに制限できます。 Azure PaaS リソースへのネットワーク アクセスを制限する方法を確認するには、次の記事に進みます。

> [!div class="nextstepaction"]
> [PaaS リソースへのネットワーク アクセスを制限する](virtual-network-service-endpoints-configure.md#azure-powershell)