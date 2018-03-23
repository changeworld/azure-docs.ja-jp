---
title: 高速ネットワークを使った Azure 仮想マシンの作成 | Microsoft Docs
description: 高速ネットワークを使った Linux 仮想マシンの作成方法について説明します。
services: virtual-network
documentationcenter: ''
author: jdial
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 01/04/2018
ms.author: jimdial
ms.openlocfilehash: c0017b8759a1f01b010172be562ed869d1d51a25
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/08/2018
---
# <a name="create-a-windows-virtual-machine-with-accelerated-networking"></a>高速ネットワークを使った Windows 仮想マシンの作成

> [!IMPORTANT] 
> 仮想マシンは、高速ネットワークを有効にして作成する必要があります。 この機能は、既存の仮想マシンでは有効にできません。 高速ネットワークを有効にするには、次の手順に従ってください。
>   1. 仮想マシンの削除
>   2. 高速ネットワークを有効にして仮想マシンを再作成する
>

このチュートリアルでは、高速ネットワークを使った Windows 仮想マシン (VM) の作成方法について説明します。 高速ネットワークによって、VM との間でシングル ルート I/O 仮想化 (SR-IOV) が可能になり、ネットワークのパフォーマンスが大幅に向上します。 この高パフォーマンスのパスによってデータパスからホストがバイパスされ、サポートされる VM タイプの最も要件の厳しいネットワーク ワークロードで使用した場合でも、待ち時間、ジッター、CPU 使用率が軽減されます。 次の図は、2 台の VM 間の通信を、高速ネットワークを使う場合と使わない場合とで比較したものです。

![比較](./media/create-vm-accelerated-networking/accelerated-networking.png)

高速ネットワークを使用しない場合は、VM に出入りするすべてのネットワーク トラフィックがホストと仮想スイッチをスキャンする必要があります。 仮想スイッチでは、ネットワーク セキュリティ グループ、アクセス制御リスト、分離、その他のネットワーク仮想化サービスなど、すべてのポリシーが適用されます。 仮想スイッチの詳細については、[Hyper-V ネットワーク仮想化と仮想スイッチ](https://technet.microsoft.com/library/jj945275.aspx)に関する記事を参照してください。

高速ネットワークを使用した場合、ネットワーク トラフィックは、VM のネットワーク インターフェイス (NIC) に到達した後、VM に転送されます。 仮想スイッチによって適用されるすべてのネットワーク ポリシーはオフロードされ、ハードウェアで適用されるようになりました。 ハードウェアにポリシーを適用することによって、ホストに適用されるポリシーをすべて維持したまま、ホストや仮想スイッチをバイパスして、NIC からネットワーク トラフィックを直接 VM に転送できます。

高速ネットワークのメリットが得られるのは、高速ネットワークが有効になっている VM だけです。 最適な結果を得るには、同じ Azure 仮想ネットワーク (VNet) に接続された 2 台以上の VM でこの機能を有効にしておくことをお勧めします。 VNet の境界を越えて通信するときや、オンプレミスで接続する場合、全体的な待ち時間に対してこの機能がもたらす効果は限定的です。

## <a name="benefits"></a>メリット
* **待機時間の短縮 / 1 秒あたりのパケット数 (pps)の向上:** データパスから仮想スイッチを削除することで、ホストにおけるパケットのポリシー処理に必要な時間がなくなるため、VM 内で処理できるパケット数が増加します。
* **ジッターの削減:** 仮想スイッチの処理は、適用するポリシーの量と、処理を行う CPU のワークロードによって異なります。 ハードウェアへのポリシーの適用をオフロードすると、パケットが直接 VM に配信され、ホストと VM 間の通信とソフトウェアによる干渉やコンテキスト スイッチがなくなるため、そのばらつきはなくなります。
* **CPU 使用率の削減:** ホストの仮想スイッチをバイパスすることによって、ネットワーク トラフィックを処理するための CPU の使用率を軽減できます。

## <a name="supported-operating-systems"></a>サポートされているオペレーティング システム
Microsoft Windows Server 2012 R2 Datacenter と Windows Server 2016。

## <a name="supported-vm-instances"></a>サポートされている VM インスタンス
高速ネットワークは、4 つ以上の vCPU を持つ、コンピューティングに最適化された多くの汎用のインスタンス サイズでサポートされています。 ハイパースレッディングをサポートする D/DSv3 や E/ESv3 などのインスタンスでは、8 以上の vCPU を持つ VM インスタンスで高速ネットワークがサポートされています。 D/DSv2、D/DSv3、E/ESv3、F/Fs/Fsv2 および Ms/Mms のシリーズがサポートされています。

VM インスタンスの詳細については、[Windows VM のサイズ](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に関するページを参照してください。

## <a name="regions"></a>リージョン
すべてのパブリック Azure リージョンと Azure Government クラウドで使用できます。 

## <a name="limitations"></a>制限事項
この機能を使用する場合は、次の制限事項があります。

* **ネットワーク インターフェイスの作成:** 高速ネットワークは、新しい NIC でのみ有効にできます。 既存の NIC に対して有効にすることはできません。
* **VM の作成:** 高速ネットワークを有効にした NIC は、VM の作成時にのみ VM に接続できます。 既存の VM に NIC を接続することはできません。 VM を既存の可用性セットに追加する場合は、可用性セット内のすべての VM でも高速ネットワークが有効になっている必要があります。
* **Azure Resource Manager でのデプロイのみ:** 仮想マシン (クラシック) は高速ネットワークを使用してデプロイできません。

この記事では、高速ネットワークを使用した仮想マシンを、Azure PowerShell を使って作成する手順について説明しますが、[高速ネットワークを使用した仮想マシンは、Azure Portal を使って作成することもできます](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。 ポータルで、サポートされているオペレーティング システムと VM サイズで仮想マシンを作成する際に、**[設定]** の **[高速ネットワーク]** で **[有効]** を選択します。 仮想マシンが作成されたら、「[オペレーティング システムでドライバーがインストールされていることを確認する](#confirm-the-driver-is-installed-in-the-operating-system)」の手順を完了する必要があります。

## <a name="create-a-virtual-network"></a>仮想ネットワークの作成

[Azure PowerShell](/powershell/azure/install-azurerm-ps) バージョン 5.1.1 以降をインストールします。 現在インストールされているバージョンを見つけるには、`Get-Module -ListAvailable AzureRM` を実行します。 インストールまたはアップグレードする必要がある場合は、[PowerShell ギャラリー](https://www.powershellgallery.com/packages/AzureRM)から最新バージョンの AzureRM モジュールをインストールします。 PowerShell セッションで、[Add-AzureRmAccount](/powershell/module/AzureRM.Profile/Add-AzureRmAccount) を使用して Azure アカウントにログインします。

次の例では、パラメーター名を独自の値を置き換えます。 たとえば、*myResourceGroup*、*myNic*、*myVM* といったパラメーター名にします。

[New-AzureRmResourceGroup](/powershell/module/AzureRM.Resources/New-AzureRmResourceGroup) を使用してリソース グループを作成します。 次の例では、*myResourceGroup* という名前のリソース グループを場所 *centralus* に作成します。

```powershell
New-AzureRmResourceGroup -Name "myResourceGroup" -Location "centralus"
```

最初に、[New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/AzureRM.Network/New-AzureRmVirtualNetworkSubnetConfig) を使用してサブネット構成を作成します。 次の例では、*mySubnet* という名前のサブネットを作成します。

```powershell
$subnet = New-AzureRmVirtualNetworkSubnetConfig `
    -Name "mySubnet" `
    -AddressPrefix "192.168.1.0/24"
```

[New-AzureRmVirtualNetwork](/powershell/module/AzureRM.Network/New-AzureRmVirtualNetwork) を使用して、*mySubnet* サブネットを持つ仮想ネットワークを作成します。

```powershell
$vnet = New-AzureRmVirtualNetwork -ResourceGroupName "myResourceGroup" `
    -Location "centralus" `
    -Name "myVnet" `
    -AddressPrefix "192.168.0.0/16" `
    -Subnet $Subnet
```

## <a name="create-a-network-security-group"></a>ネットワーク セキュリティ グループの作成

最初に、[New-AzureRmNetworkSecurityRuleConfig](/powershell/module/AzureRM.Network/New-AzureRmNetworkSecurityRuleConfig) を使用して、ネットワーク セキュリティ グループの規則を作成します。

```powershell
$rdp = New-AzureRmNetworkSecurityRuleConfig `
    -Name 'Allow-RDP-All' `
    -Description 'Allow RDP' `
    -Access Allow `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 100 `
    -SourceAddressPrefix * `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 3389
```

[New-AzureRmNetworkSecurityGroup](/powershell/module/AzureRM.Network/New-AzureRmNetworkSecurityGroup) を使用してネットワーク セキュリティ グループを作成し、*Allow-RDP-All* セキュリティ規則を割り当てます。 *Allow-RDP-All* 規則に加え、ネットワーク セキュリティ グループには複数の既定の規則が含まれています。 ある既定の規則は、インターネットからのすべての着信アクセスを無効にします。そのため、仮想マシンが作成されたら、リモートでその仮想マシンに接続できるように、*Allow-RDP-All* 規則をネットワーク セキュリティ グループに割り当てます。

```powershell
$nsg = New-AzureRmNetworkSecurityGroup `
    -ResourceGroupName myResourceGroup `
    -Location centralus `
    -Name "myNsg" `
    -SecurityRules $rdp
```

[Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/AzureRM.Network/Set-AzureRmVirtualNetworkSubnetConfig) を使用して、ネットワーク セキュリティ グループを *mySubnet* サブネットに関連付けます。 ネットワーク セキュリティ グループ内の規則は、サブネットにデプロイされているすべてのリソースに対して有効です。

```powershell
Set-AzureRmVirtualNetworkSubnetConfig `
    -VirtualNetwork $vnet `
    -Name 'mySubnet' `
    -AddressPrefix "192.168.1.0/24" `
    -NetworkSecurityGroup $nsg
```

## <a name="create-a-network-interface-with-accelerated-networking"></a>高速ネットワークを使ったネットワーク インターフェイスの作成
[New-AzureRmPublicIpAddress](/powershell/module/AzureRM.Network/New-AzureRmPublicIpAddress) を使用してパブリック IP アドレスを作成します。 インターネットから仮想マシンにアクセスする計画がない場合、パブリック IP アドレスは不要ですが、この記事の手順を完了するには必要です。

```powershell
$publicIp = New-AzureRmPublicIpAddress `
    -ResourceGroupName myResourceGroup `
    -Name 'myPublicIp' `
    -location centralus `
    -AllocationMethod Dynamic
```

高速ネットワークを有効にし、[New-AzureRmNetworkInterface](/powershell/module/AzureRM.Network/New-AzureRmNetworkInterface) を使用してネットワーク インターフェイスを作成し、パブリック IP アドレスをこのネットワーク インターフェイスに割り当てます。 次の例では、*myVnet* 仮想ネットワークの *mySubnet* サブネット内に *myNic* という名前のネットワーク インターフェイスを作成し、*myPublicIp* パブリック IP アドレスを割り当てます。

```powershell
$nic = New-AzureRmNetworkInterface `
    -ResourceGroupName "myResourceGroup" `
    -Name "myNic" `
    -Location "centralus" `
    -SubnetId $vnet.Subnets[0].Id `
    -PublicIpAddressId $publicIp.Id `
    -EnableAcceleratedNetworking
```

## <a name="create-the-virtual-machine"></a>仮想マシンの作成

[Get-Credential](/powershell/module/microsoft.powershell.security/get-credential) を使用して、VM の資格情報を変数 `$cred` に設定します。

```powershell
$cred = Get-Credential
```

最初に、[New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig) を使用して VM を定義します。 次の例では、高速ネットワークをサポートする VM サイズ (*Standard_DS4_v2*) を使用して、*myVM* という VM を定義します。

```powershell
$vmConfig = New-AzureRmVMConfig -VMName "myVm" -VMSize "Standard_DS4_v2"
```

すべての VM のサイズと特性の一覧は、[Windows VM のサイズ](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に関するページを参照してください。

[Set-AzureRmVMOperatingSystem](/powershell/module/azurerm.compute/set-azurermvmoperatingsystem) と [Set-AzureRmVMSourceImage](/powershell/module/azurerm.compute/set-azurermvmsourceimage) を使用して、残りの VM 構成を作成します。 次の例では、Windows Server 2016 の VM を作成します。

```powershell
$vmConfig = Set-AzureRmVMOperatingSystem -VM $vmConfig `
    -Windows `
    -ComputerName "myVM" `
    -Credential $cred `
    -ProvisionVMAgent `
    -EnableAutoUpdate
$vmConfig = Set-AzureRmVMSourceImage -VM $vmConfig `
    -PublisherName "MicrosoftWindowsServer" `
    -Offer "WindowsServer" `
    -Skus "2016-Datacenter" `
    -Version "latest"
```

以前に作成したネットワーク インターフェイスを [Add-AzureRmVMNetworkInterface](/powershell/module/azurerm.compute/add-azurermvmnetworkinterface) にアタッチします。

```powershell
$vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id
```

最後に、[New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) を使用して VM を作成します。

```powershell
New-AzureRmVM -VM $vmConfig -ResourceGroupName "myResourceGroup" -Location "centralus"
```

## <a name="confirm-the-driver-is-installed-in-the-operating-system"></a>オペレーティング システムでドライバーがインストールされていることを確認する

Azure で VM を作成したら、VM に接続して、Windows でドライバーがインストールされていることを確認します。 

1. インターネット ブラウザーから Azure [Portal](https://portal.azure.com) を開いて、自分の Azure アカウントでサインインします。
2. Azure Portal の上部に "*リソースの検索*" というテキストが表示されたボックスがあります。そこに「*myVm*」と入力します。 検索結果に **myVm** が表示されたら、それをクリックします。 **[接続]** ボタンの下に **[作成中]** と表示されている場合、Azure による VM の作成がまだ完了していません。 概要の左上隅にある **[接続]** は必ず、**[接続]** ボタンの下の **[作成中]** が見えなくなってからクリックしてください。
3. 「[仮想マシンの作成](#create-the-virtual-machine)」で入力したユーザー名とパスワードを入力します。 Azure で Windows VM に接続したことがない場合は、「[仮想マシンへの接続](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#connect-to-virtual-machine)」を参照してください。
4. Windows の [スタート] ボタンを右クリックし、**[デバイス マネージャー]** をクリックします。 **[ネットワーク アダプター]** ノードを展開します。 **[Mellanox ConnectX-3 Virtual Function Ethernet Adapter]** が表示されていることを確認します (下図参照)。
   
    ![[デバイス マネージャー]](./media/create-vm-accelerated-networking/device-manager.png)

VM の高速ネットワークが有効になりました。
