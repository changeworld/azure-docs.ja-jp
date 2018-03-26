---
title: 複数のサブネットを含んだ Azure 仮想ネットワークを作成する - PowerShell | Microsoft Docs
description: PowerShell を使用して、複数のサブネットを含む仮想ネットワークを作成する方法について説明します。
services: virtual-network
documentationcenter: ''
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: ''
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/01/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: c90bdc9381bf98e5c1457e8e28f74105227d8f8d
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/13/2018
---
# <a name="create-a-virtual-network-with-multiple-subnets-using-powershell"></a>PowerShell を使用して複数のサブネットを含む仮想ネットワークを作成する

仮想ネットワークでは、複数の種類の Azure リソースが、インターネットと通信したり、プライベートに相互通信したりできます。 仮想ネットワークに複数のサブネットを作成すると、ネットワークをセグメント化して、サブネット間のトラフィックのフローをフィルター処理または制御できます。 この記事では、次の方法について説明します。

> [!div class="checklist"]
> * 仮想ネットワークの作成
> * サブネットの作成
> * 仮想マシン間のネットワーク通信のテスト

Azure サブスクリプションがない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

PowerShell をインストールしてローカルで使用する場合、このチュートリアルでは Azure PowerShell モジュール バージョン 3.6 以降が必要になります。 インストールされているバージョンを確認するには、` Get-Module -ListAvailable AzureRM` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-azurerm-ps)に関するページを参照してください。 PowerShell をローカルで実行している場合、`Login-AzureRmAccount` を実行して Azure との接続を作成することも必要です。 

## <a name="create-a-virtual-network"></a>仮想ネットワークの作成

[New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) を使用してリソース グループを作成します。 次の例では、*myResourceGroup* という名前のリソース グループを *EastUS* に作成します。

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

[New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) を使用して仮想ネットワークを作成します。 次の例では、アドレス プレフィックスが *10.0.0.0/16* の、*myVirtualNetwork* という名前の仮想ネットワークを作成します。

```azurepowershell-interactive
$virtualNetwork = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

**AddressPrefix** は、CIDR 表記で指定されます。 指定したアドレス プレフィックスには、IP アドレス 10.0.0.0 ～ 10.0.255.254 が含まれます。

## <a name="create-a-subnet"></a>サブネットの作成

サブネットを作成するには、まずサブネット構成を作成し、次にそのサブネット構成を使用して仮想ネットワークを更新します。 [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) を使用してサブネット構成を作成します。 次の例では、*パブリック* サブネット用と*プライベート* サブネット用の 2 つのサブネット構成を作成します。

```azurepowershell-interactive
$subnetConfigPublic = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name Public `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork

$subnetConfigPrivate = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name Private `
  -AddressPrefix 10.0.1.0/24 `
  -VirtualNetwork $virtualNetwork
```

サブネットに指定する **AddressPrefix** は、仮想ネットワーク用に定義されたアドレス プレフィックスの範囲内である必要があります。 Azure DHCP では、サブネットのアドレス プレフィックスから、サブネットにデプロイされるリソースに IP アドレスが割り当てられます。 Azure では、**パブリック** サブネットにデプロイされるリソースに、アドレス 10.0.0.4 ～ 10.0.0.254 を割り当てるのみです。なぜなら、各サブネットの最初の 4 つのアドレス (この例では、サブネットのアドレス 10.0.0.0 ～ 10.0.0.3) と最後のアドレス (この例では、サブネットのアドレス 10.0.0.255) は、Azure によって予約されているためです。

[Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/Set-AzureRmVirtualNetwork) を使用して、仮想ネットワークにサブネット構成を書き込みます。これにより、仮想ネットワークにサブネットが作成されます。

```azurepowershell-interactive
$virtualNetwork | Set-AzureRmVirtualNetwork
```

運用で使用するために Azure 仮想ネットワークとサブネットをデプロイする前に、アドレス空間に関する[考慮事項](manage-virtual-network.md#create-a-virtual-network)や[仮想ネットワークの制限](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)に十分精通しておくことをお勧めします。 リソースがサブネットにデプロイされた後に、仮想ネットワークやサブネットの変更 (アドレス範囲の変更など) を行うと、サブネットにデプロイされている既存の Azure リソースを再デプロイする必要が生じる場合があります。

## <a name="test-network-communication"></a>ネットワーク通信をテストする

仮想ネットワークでは、インターネットとの通信、および仮想ネットワーク間のプライベート通信のために、複数の種類の Azure リソースを使用できます。 仮想ネットワークにデプロイできるリソースの種類の 1 つは、仮想マシンです。 仮想ネットワークに 2 つの仮想マシンを作成して、後の手順でそれらの間のネットワーク通信と、インターネットとのネットワーク通信を検証できるようにします。 

### <a name="create-virtual-machines"></a>仮想マシンを作成する

[New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) を使って、仮想マシンを作成します。 次の例では、*myVirtualNetwork* 仮想ネットワークの*パブリック* サブネットに、*myVmWeb* という名前の仮想マシンを作成します。 `-AsJob` オプションを使用すると、仮想マシンはバックグラウンドで作成されるため、次の手順に進むことができます。 メッセージが表示されたら、仮想マシンへのログインに使用するユーザー名とパスワードを入力します。

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

*パブリック* サブネットで使用できる最初の IP アドレスが 10.0.0.4 であるため、Azure では仮想マシンのプライベート IP アドレスとして 10.0.0.4 が自動的に割り当てられます。 

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

仮想マシンの作成には、数分かかります。 返される出力には表示されませんが、*myVirtualNetwork* の*プライベート* サブネットで使用できる最初の IP アドレスが 10.0.1.4 であるため、Azure では仮想マシンのプライベート IP アドレスとして 10.0.1.4 が自動的に割り当てられます。 

仮想マシンが作成され、PowerShell から出力が返されるまでは、残りの手順を続行しないでください。

この記事で作成した仮想マシンは、1 つの[ネットワーク インターフェイス](virtual-network-network-interface.md)を備えています。このネットワーク インターフェイスには、1 つの IP アドレスが動的に割り当てられます。 VM をデプロイした後に、[複数のパブリックおよびプライベート IP アドレスを追加したり、IP アドレスの割り当て方法を静的に変更](virtual-network-network-interface-addresses.md#add-ip-addresses)したりできます。 仮想マシンの作成時に選択した [VM サイズ](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)でサポートされている最大数まで、[ネットワーク インターフェイスを追加](virtual-network-network-interface-vm.md#vm-add-nic)できます。 また、VM に対して[シングル ルート I/O 仮想化 (SR-IOV)](create-vm-accelerated-networking-powershell.md) を有効にすることもできます (ただし、作成した VM の VM サイズがこの機能に対応している場合に限ります)。

### <a name="communicate-between-virtual-machines-and-with-the-internet"></a>仮想マシン間およびインターネットとの通信を行う

インターネットから仮想マシンのパブリック IP アドレスに接続できます。 *myVmMgmt* 仮想マシンの作成時に、*myVmMgmt* という名前のパブリック IP アドレスも作成され、仮想マシンに割り当てられています。 仮想マシンにパブリック IP アドレスを割り当てることは必須ではありませんが、Azure では、作成した各仮想マシンにパブリック IP アドレスが既定で割り当てられます。 インターネットから仮想マシンに通信するには、仮想マシンにパブリック IP アドレスが割り当てられている必要があります。 仮想マシンにパブリック IP アドレスが割り当てられているかどうかにかかわらず、すべての仮想マシンがインターネットとの送信方向の通信を行うことができます。 Azure での送信方向のインターネット接続について詳しくは、「[Azure の送信接続](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json)」をご覧ください。 

[Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) を使用して、仮想マシンのパブリック IP アドレスを返します。 次の例では、*myVmMgmt* 仮想マシンのパブリック IP アドレスを返します。

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVmMgmt `
  -ResourceGroupName myResourceGroup | Select IpAddress
```

次のコマンドを使用して、ローカル コンピューターから、*myVmMgmt* 仮想マシンを使用してリモート デスクトップ セッションを作成します。 `<publicIpAddress>` を前のコマンドで返された IP アドレスに置き換えます。

```
mstsc /v:<publicIpAddress>
```

リモート デスクトップ プロトコル (.rdp) ファイルが作成され、お使いのコンピューターにダウンロードされて開かれます。 仮想マシンの作成時に指定したユーザー名とパスワードを入力し (仮想マシンの作成時に入力した資格情報を指定するために、**[More choices]\(その他の選択肢\)**、**[Use a different account]\(別のアカウントを使用する\)** を選択することが必要な場合があります)、**[OK]** をクリックします。 サインイン処理中に証明書の警告が表示される場合があります。 **[はい]** または **[続行]** をクリックして接続処理を続行します。 

後の手順で、*myVmWeb* 仮想マシンから *myVmMgmt* 仮想マシンと通信するために ping を使用します。 ping は ICMP を使用しますが、既定では ICMP は Windows ファイアウォールで拒否されます。 コマンド プロンプトから次のコマンドを入力して、Windows ファイアウォールで ICMP を有効にします。

```
netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
```

この記事では ping を使用していますが、運用環境のデプロイでは Windows ファイアウォールで ICMP を許可することは推奨されません。

セキュリティ上の理由から、仮想ネットワーク内でリモートで接続できる仮想マシンの数を制限するのが一般的です。 このチュートリアルでは、*myVmMgmt* 仮想マシンは仮想ネットワークで *myVmWeb* 仮想マシンを管理するために使用されます。 次のコマンドを使用して、*myVmMgmt* 仮想マシンから *myVmWeb* 仮想マシンにリモート デスクトップ接続します。

``` 
mstsc /v:myVmWeb
```

*myVmWeb* 仮想マシンから *myVmMgmt* 仮想マシンに通信するには、コマンド プロンプトで次のコマンドを入力します。

```
ping myvmmgmt
```

次の出力例のような出力が返されます。
    
```
Pinging myvmmgmt.dar5p44cif3ulfq00wxznl3i3f.bx.internal.cloudapp.net [10.0.1.4] with 32 bytes of data:
Reply from 10.0.1.4: bytes=32 time<1ms TTL=128
Reply from 10.0.1.4: bytes=32 time<1ms TTL=128
Reply from 10.0.1.4: bytes=32 time<1ms TTL=128
Reply from 10.0.1.4: bytes=32 time<1ms TTL=128
    
Ping statistics for 10.0.1.4:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 0ms, Average = 0ms
```
      
*myVmMgmt* 仮想マシンのアドレスは 10.0.1.4 であることがわかります。 10.0.1.4 は、前の手順で *myVmMgmt* 仮想マシンをデプロイした*プライベート* サブネットのアドレス範囲内にある、最初の利用可能な IP アドレスでした。  この仮想マシンの完全修飾ドメイン名は、*myvmmgmt.dar5p44cif3ulfq00wxznl3i3f.bx.internal.cloudapp.net* です。 ドメイン名の *dar5p44cif3ulfq00wxznl3i3f* の部分は仮想マシンと異なりますが、残りの部分は同じです。 既定では、すべての Azure 仮想マシンは既定の Azure DNS サービスを使用します。 仮想ネットワーク内のすべての仮想マシンは、Azure の既定の DNS サービスを使用して、同じ仮想ネットワーク内にある他のすべての仮想マシンの名前を解決できます。 Azure の既定の DNS サービスを使用する代わりに、独自の DNS サーバーや、Azure DNS サービスのプライベート ドメイン機能を使用することもできます。 詳細については、「[独自の DNS サーバーを使用する名前解決](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server)」または「[プライベート ドメインに Azure DNS を使用する](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)」をご覧ください。

*myVmWeb* 仮想マシンに Windows Server 用インターネット インフォメーション サービス (IIS) をインストールするには、PowerShell セッションから次のコマンドを入力します。

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

IIS のインストールが完了したら、*myVmWeb* のリモート デスクトップ セッションを切断します。*myVmMgmt* のリモート デスクトップ セッションは保持されます。 Web ブラウザーを開き、http://myvmweb に移動します。 IIS のウェルカム ページが表示されます。

*myVmMgmt* のリモート デスクトップ セッションを切断します。

Azure によって *myVmWeb* 仮想マシンに割り当てられたパブリック アドレスを取得します。

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVmWeb `
  -ResourceGroupName myResourceGroup | Select IpAddress
```

お使いのコンピューターで、*myVmWeb* 仮想マシンのパブリック IP アドレスに移動します。 お使いのコンピューターから IIS のウェルカム ページを表示する試みが失敗します。 この試みが失敗するのは、仮想マシンがデプロイされたときに、Azure が既定で各仮想マシンのネットワーク セキュリティ グループを作成したためです。 

ネットワーク セキュリティ グループには、ポートと IP アドレスによって受信/送信ネットワーク トラフィックを許可または拒否するセキュリティ規則が含まれています。 Azure が作成した既定のネットワーク セキュリティ グループでは、同じ仮想ネットワーク内のリソース間の、すべてのポート経由の通信が許可されます。 Windows 仮想マシンの既定のネットワーク セキュリティ グループでは、TCP ポート 3389 (RDP) を除くすべてのポート経由の、すべてのインターネットからの受信トラフィックが拒否されます。 その結果、既定では、Web サーバーに対してポート 3389 を開きたくない場合でも、インターネットから *myVmWeb* 仮想マシンに直接 RDP することもできます。 Web 閲覧はポート 80 経由で通信するため、インターネットからの通信は失敗します。なぜなら、既定のネットワーク セキュリティ グループにはポート 80 経由のトラフィックを許可するルールがないからです。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

必要なくなったら、[Remove-AzureRmResourcegroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) を使用して、リソース グループとその中のすべてのリソースを削除します。

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>次の手順

このチュートリアルでは、複数のサブネットを含んだ仮想ネットワークをデプロイする方法を学習しました。 また、Windows 仮想マシンを作成したときに Azure がその仮想マシンに接続するネットワーク インターフェイスを作成し、ポート 3389 経由のインターネットからのトラフィックのみを許可するネットワーク セキュリティ グループを作成することを学習しました。 次のチュートリアルに進み、個別の仮想マシンではなく、サブネットへのネットワーク トラフィックをフィルター処理する方法を学習しましょう。

> [!div class="nextstepaction"]
> [サブネットへのネットワーク トラフィックをフィルター処理する](./virtual-networks-create-nsg-arm-ps.md)
