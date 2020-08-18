---
title: クイック スタート:パブリック ロード バランサーを作成する - Azure PowerShell
titleSuffix: Azure Load Balancer
description: このクイックスタートでは、Azure PowerShell を使用してロード バランサーを作成する方法について説明します
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a load balancer so that I can load balance internet traffic to VMs.
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/06/2020
ms.author: allensu
ms:custom: seodec18
ms.openlocfilehash: bdacd752ab549d0caed4d3579ac8d0c3b2606477
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/07/2020
ms.locfileid: "87925704"
---
# <a name="quickstart-create-a-public-load-balancer-to-load-balance-vms-using-azure-powershell"></a>クイック スタート:Azure PowerShell を使用して VM の負荷を分散するパブリック ロード バランサーを作成する

Azure PowerShell を使用してパブリック ロード バランサーと 3 つの仮想マシンを作成することにより、Azure Load Balancer の使用を開始します。

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- ローカルにインストールされた Azure PowerShell または Azure Cloud Shell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

PowerShell をインストールしてローカルで使用する場合、この記事では Azure PowerShell モジュール バージョン 5.4.1 以降が必要になります。 インストールされているバージョンを確認するには、`Get-Module -ListAvailable Az` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-Az-ps)に関するページを参照してください。 PowerShell をローカルで実行している場合、`Connect-AzAccount` を実行して Azure との接続を作成することも必要です。

## <a name="create-a-resource-group"></a>リソース グループを作成する

Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) を使用して、次のようにリソース グループを作成します。

* 名前は **myResourceGroupLB** にします。
* 場所は **eastus** にします。

```azurepowershell-interactive
## Variables for the command ##
$rg = 'MyResourceGroupLB'
$loc = 'eastus'

New-AzResourceGroup -Name $rg -Location $loc
```
---

# <a name="option-1-default-create-a-public-load-balancer-standard-sku"></a>[オプション 1 (既定): パブリック ロード バランサーを作成する (Standard SKU)](#tab/option-1-create-load-balancer-standard)

>[!NOTE]
>運用環境のワークロードには、Standard SKU ロード バランサーをお勧めします。 SKU の詳細については、「 **[Azure Load Balancer の SKU](skus.md)** 」を参照してください。

## <a name="create-a-public-ip-address"></a>パブリック IP アドレスの作成

インターネット上の Web アプリにアクセスするには、ロード バランサーのパブリック IP アドレスが必要です。 

[New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) を使用して、以下のことを行います。

* **myPublicIP** という名前の Standard ゾーン冗長パブリック IP アドレスを作成します。
* **myResourceGroupLB** 内に作成します。

```azurepowershell-interactive
## Variables for the command ##
$rg = 'MyResourceGroupLB'
$loc = 'eastus'
$pubIP = 'myPublicIP'
$sku = 'Standard'
$all = 'static'

$publicIp = 
New-AzPublicIpAddress -ResourceGroupName $rg -Name $pubIP -Location $loc -AllocationMethod $all -SKU $sku
```

ゾーンのパブリック IP アドレスをゾーン 1 に作成するには、次のコマンドを使用します。

```azurepowershell-interactive
## Variables for the command ##
$rg = 'MyResourceGroupLB'
$loc = 'eastus'
$pubIP = 'myPublicIP'
$sku = 'Standard'
$all = 'static'

$publicIp = 
New-AzPublicIpAddress -ResourceGroupName $rg -Name $pubIP -Location $loc -AllocationMethod $all -SKU $sku -zone 1
```

## <a name="create-standard-load-balancer"></a>Standard ロード バランサーを作成する

このセクションでは、ロード バランサーの以下のコンポーネントを作成および構成する方法について説明します。

  * ロード バランサーの着信ネットワーク トラフィックを受け取るフロントエンド IP プール。
  * フロントエンド プールから負荷分散されたネットワーク トラフィックが送信されるバックエンド IP プール。
  * バックエンド VM インスタンスの正常性を判断する正常性プローブ。
  * VM に対するトラフィックの分散方法を定義するロード バランサー規則。

### <a name="create-frontend-ip"></a>フロントエンド IP を作成する

[New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig) を使用して、次のようにフロントエンド IP を作成します。

* 名前は **myFrontEnd** にします。
* パブリック IP **myPublicIP** に接続します。

```azurepowershell-interactive
## Variables for the commands ##
$fe = 'myFrontEnd'
$rg = 'MyResourceGroupLB'
$loc = 'eastus'
$pubIP = 'myPublicIP'

$publicIp = 
Get-AzPublicIpAddress -Name $pubIP -ResourceGroupName $rg

$feip = 
New-AzLoadBalancerFrontendIpConfig -Name $fe -PublicIpAddress $publicIp
```

### <a name="configure-back-end-address-pool"></a>バックエンド アドレス プールを構成する

[New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) を使用して、次のようにバックエンド アドレス プールを作成します。 

* 名前は **myBackEndPool** にします。
* 残りの手順では、VM をこのバックエンド プールにアタッチします。

```azurepowershell-interactive
## Variable for the command ##
$be = 'myBackEndPool'

$bepool = 
New-AzLoadBalancerBackendAddressPoolConfig -Name $be
```

### <a name="create-the-health-probe"></a>正常性プローブを作成する

正常性プローブは、すべての仮想マシン インスタンスを調べて、ネットワーク トラ	フィックを送信できるかどうかを確認します。 

プローブ チェックが失敗した仮想マシンは、ロード バランサーから削除されます。 障害が解決されると、仮想マシンがロード バランサーに再び追加されます。

[Add-AzLoadBalancerProbeConfig](/powershell/module/az.network/add-azloadbalancerprobeconfig) を使用して、次のように正常性プローブを作成します。

* 仮想マシンの正常性を監視します。
* 名前は **myHealthProbe** にします。
* プロトコルは **TCP** にします。
* **ポート 80** を監視します。

```azurepowershell-interactive
## Variables for the command ##
$hp = 'myHealthProbe'
$pro = 'http'
$port = '80'
$int = '360'
$cnt = '5'

$probe = 
New-AzLoadBalancerProbeConfig -Name $hp -Protocol $pro -Port $port -RequestPath / -IntervalInSeconds $int -ProbeCount $cnt
```

### <a name="create-the-load-balancer-rule"></a>ロード バランサー規則を作成する

ロード バランサー規則は、以下のものを定義します。

* 着信トラフィック用のフロントエンド IP 構成。
* トラフィックを受信するためのバックエンド IP プール。
* 必要な発信元ポートと宛先ポート。 

[Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig) を使用して、次のようにロード バランサー規則を作成します。 

* 名前は **myHTTPRule** にします
* フロントエンド プール **myFrontEnd** で**ポート 80** をリッスンします。
* **ポート 80** を使用して、負荷分散されたネットワーク トラフィックをバックエンド アドレス プール **myBackEndPool** に送信します。 
* 正常性プローブ **myHealthProbe** を使用します。
* プロトコルは **TCP** にします。

```azurepowershell-interactive
## Variables for the command ##
$lbr = 'myHTTPRule'
$pro = 'tcp'
$port = '80'

## $feip and $bePool are the variables from previous steps. ##

$rule = 
New-AzLoadBalancerRuleConfig -Name $lbr -Protocol $pro -Probe $probe -FrontendPort $port -BackendPort $port -FrontendIpConfiguration $feip -BackendAddressPool $bePool -DisableOutboundSNAT
```

### <a name="create-load-balancer-resource"></a>ロード バランサーのリソースを作成する

[New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer) を使用して、次のようにパブリック ロード バランサーを作成します。

* 名前は **myLoadBalancer** にします
* 場所は **eastus** にします。
* リソース グループは **myResourceGroupLB** にします。

```azurepowershell-interactive
## Variables for the command ##
$lbn = 'myLoadBalancer'
$rg = 'myResourceGroupLB'
$loc = 'eastus'
$sku = 'Standard'

## $feip, $bepool, $probe, $rule are variables with configuration information from previous steps. ##

$lb = 
New-AzLoadBalancer -ResourceGroupName $rg -Name $lbn -SKU $sku -Location $loc -FrontendIpConfiguration $feip -BackendAddressPool $bepool -Probe $probe -LoadBalancingRule $rule
```

## <a name="configure-virtual-network"></a>仮想ネットワークを構成する

VM をデプロイしてロード バランサーをテストする前に、サポート用の仮想ネットワーク リソースを作成します。

### <a name="create-a-virtual-network-and-azure-bastion-host"></a>仮想ネットワークと Azure Bastion ホストを作成する

[New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) を使用して、次のように仮想ネットワークを作成します。

* 名前は **myVNet** にします。
* リソース グループは **myResourceGroupLB** にします。
* サブネットの名前は **myBackendSubnet** にします。
* 仮想ネットワークは **10.0.0.0/16** にします。
* サブネットは **10.0.0.0/24** にします。
* Bastion サブネット **10.0.1.0/24**

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroupLB'
$loc = 'eastus'
$sub = 'myBackendSubnet'
$bsub = 'AzureBastionSubnet'
$spfx = '10.0.0.0/24'
$bpfx = '10.0.1.0/24'
$vnm = 'myVNet'
$vpfx = '10.0.0.0/16'


## Create backend subnet config ##
$subnetConfig = 
New-AzVirtualNetworkSubnetConfig -Name $sub -AddressPrefix $spfx

## Create Bastion subnet config ##
$bassubnetConfig =
New-AzVirtualNetworkSubnetConfig -name $bsub -AddressPrefix $bpfx

## Create the virtual network ##
$vnet = 
New-AzVirtualNetwork -ResourceGroupName $rg -Location $loc -Name $vnm -AddressPrefix $vpfx -Subnet $subnetConfig,$bassubnetConfig
```
[New-AzBastion](/powershell/module/az.network/new-azbastion) で、次の要塞ホストを作成します。

* 名前: **myBastionHost**
* パブリック IP: **myBastionIP**

```azurepowershell-interactive
$rg = 'myResourceGroupLB'
$loc = 'eastus'
$bas = 'myBastionHost'
$basip = 'myBastionIP'
$all = 'Static'
$sku 'Standard'

## Create public IP address for Bastion host ##
$baspubip = 
New-AzPublicIPAddress -ResourceGroupName $rg -Name $basip -Location $loc -AllocationMethod $all -Sku $sku

## Create the bastion host using the $vnet variable from previous step ##
New-AzBastion -ResourceGroupName $rg -Name $bas -PublicIpAddress $baspubip -VirtualNetwork $vnet
```
要塞ホストが仮想ネットワークにデプロイされるまでに数分かかります。

### <a name="create-network-security-group"></a>ネットワーク セキュリティ グループの作成
ネットワーク セキュリティ グループを作成して、仮想ネットワークへの受信接続を定義します。

#### <a name="create-a-network-security-group-rule-for-port-80"></a>ポート 80 のネットワーク セキュリティ グループ規則を作成する
[New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) を使用して、次のようにネットワーク セキュリティ グループの規則を作成します。

* 名前は **myNSGRuleHTTP** にします。
* 説明は **Allow HTTP** にします。
* アクセスは **Allow** にします。
* プロトコルは **TCP** にします。
* 方向は **Inbound** にします。
* 優先度は **2000** にします。
* 発信元は **Internet** にします。
* 発信元ポート範囲は **(*)** にします。
* 宛先アドレス プレフィックスは **(*)** にします。
* 宛先は**ポート 80** にします。

```azurepowershell-interactive
## Variables for command ##
$rnm = 'myNSGRuleHTTP'
$des = 'Allow HTTP'
$acc = 'Allow'
$pro = 'Tcp'
$dir = 'Inbound'
$pri = '2000'
$spfx = 'Internet'
$spr = '*'
$dpfx = '*'
$dpr = '80'

$rule1 = 
New-AzNetworkSecurityRuleConfig -Name $rnm -Description $des -Access $acc -Protocol $pro -Direction $dir -Priority $pri -SourceAddressPrefix $spfx -SourcePortRange $spr -DestinationAddressPrefix $dpfx -DestinationPortRange $dpr
```

#### <a name="create-a-network-security-group"></a>ネットワーク セキュリティ グループの作成

[New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) を使用して、次のようにネットワーク セキュリティ グループを作成します。

* 名前は **myNSG** にします。
* リソース グループは **myResourceGroupLB** にします。
* 場所は **eastus** にします。
* 前の手順で作成したセキュリティ規則を変数に格納します。

```azurepowershell
## Variables for command ##
$rg = 'myResourceGroupLB'
$loc = 'eastus'
$nmn = 'myNSG'

## $rule1 contains configuration information from the previous steps. ##
$nsg = 
New-AzNetworkSecurityGroup -ResourceGroupName $rg -Location $loc -Name $nmn -SecurityRules $rule1
```

### <a name="create-network-interfaces"></a>ネットワーク インターフェイスの作成

[New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) を使用して、次のように 3 つのネットワーク インターフェイスを作成します。

#### <a name="vm-1"></a>VM 1

* 名前は **myNicVM1** にします。
* リソース グループは **myResourceGroupLB** にします。
* 場所は **eastus** にします。
* 仮想ネットワークは **myVNet** にします。
* サブネットは **myBackendSubnet** にします。
* ネットワーク セキュリティ グループは **myNSG** にします。
* **myBackEndPool** のロード バランサー **myLoadBalancer** に接続します。

```azurepowershell-interactive
## Variables for command ##
$rg = 'myResourceGroupLB'
$loc = 'eastus'
$nic1 = 'myNicVM1'
$vnt = 'myVNet'
$lb = 'myLoadBalancer'
$ngn = 'myNSG'

## Command to get virtual network configuration. ##
$vnet = 
Get-AzVirtualNetwork -Name $vnt -ResourceGroupName $rg

## Command to get load balancer configuration
$bepool = 
Get-AzLoadBalancer -Name $lb -ResourceGroupName $rg | Get-AzLoadBalancerBackendAddressPoolConfig

## Command to get network security group configuration ##
$nsg = 
Get-AzNetworkSecurityGroup -Name $ngn -ResourceGroupName $rg

## Command to create network interface for VM1 ##
$nicVM1 = 
New-AzNetworkInterface -ResourceGroupName $rg -Location $loc -Name $nic1 -LoadBalancerBackendAddressPool $bepool -NetworkSecurityGroup $nsg -Subnet $vnet.Subnets[0]
```

#### <a name="vm-2"></a>VM 2

* 名前は **myNicVM2** にします。
* リソース グループは **myResourceGroupLB** にします。
* 場所は **eastus** にします。
* 仮想ネットワークは **myVNet** にします。
* サブネットは **myBackendSubnet** にします。
* ネットワーク セキュリティ グループは **myNSG** にします。
* **myBackEndPool** のロード バランサー **myLoadBalancer** に接続します。

```azurepowershell-interactive
## Variables for command ##
$rg = 'myResourceGroupLB'
$loc = 'eastus'
$nic2 = 'myNicVM2'
$vnt = 'myVNet'
$lb = 'myLoadBalancer'
$ngn = 'myNSG'

## Command to get virtual network configuration. ##
$vnet = 
Get-AzVirtualNetwork -Name $vnt -ResourceGroupName $rg

## Command to get load balancer configuration
$bepool = 
Get-AzLoadBalancer -Name $lb -ResourceGroupName $rg | Get-AzLoadBalancerBackendAddressPoolConfig

## Command to get network security group configuration ##
$nsg = 
Get-AzNetworkSecurityGroup -Name $ngn -ResourceGroupName $rg

## Command to create network interface for VM2 ##
$nicVM2 = 
New-AzNetworkInterface -ResourceGroupName $rg -Location $loc -Name $nic2 -LoadBalancerBackendAddressPool $bepool -NetworkSecurityGroup $nsg -Subnet $vnet.Subnets[0]
```

#### <a name="vm-3"></a>VM 3

* 名前は **myNicVM3** にします。
* リソース グループは **myResourceGroupLB** にします。
* 場所は **eastus** にします。
* 仮想ネットワークは **myVNet** にします。
* サブネットは **myBackendSubnet** にします。
* ネットワーク セキュリティ グループは **myNSG** にします。
* **myBackEndPool** のロード バランサー **myLoadBalancer** に接続します。

```azurepowershell-interactive
## Variables for command ##
$rg = 'myResourceGroupLB'
$loc = 'eastus'
$nic3 = 'myNicVM3'
$vnt = 'myVNet'
$lb = 'myLoadBalancer'
$ngn = 'myNSG'

## Command to get virtual network configuration. ##
$vnet = 
Get-AzVirtualNetwork -Name $vnt -ResourceGroupName $rg

## Command to get load balancer configuration
$bepool = 
Get-AzLoadBalancer -Name $lb -ResourceGroupName $rg | Get-AzLoadBalancerBackendAddressPoolConfig

## Command to get network security group configuration ##
$nsg = 
Get-AzNetworkSecurityGroup -Name $ngn -ResourceGroupName $rg

## Command to create network interface for VM3 ##
$nicVM3 = 
New-AzNetworkInterface -ResourceGroupName $rg -Location $loc -Name $nic3 -LoadBalancerBackendAddressPool $bepool -NetworkSecurityGroup $nsg -Subnet $vnet.Subnets[0]
```
### <a name="create-virtual-machines"></a>仮想マシンを作成する

次のように、[Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) を使用して VM の管理者のユーザー名とパスワードを設定します。

```azurepowershell
$cred = Get-Credential
```

以下を使用して、仮想マシンを作成します。

* [New-AzVM](/powershell/module/az.compute/new-azvm)
* [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig)
* [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem)
* [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage)
* [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface)


#### <a name="vm1"></a>VM1

* 名前は **myVM1** にします。
* リソース グループは **myResourceGroupLB** にします。
* ネットワーク インターフェイス **myNicVM1** に接続します。
* ロード バランサー **myLoadBalancer** に接続します。
* ゾーンは **Zone 1** にします。
* 場所は **eastus** にします。

```azurepowershell-interactive
## Variables used for command. ##
$rg = 'myResourceGroupLB'
$vm = 'myVM1'
$siz = 'Standard_DS1_v2'
$pub = 'MicrosoftWindowsServer'
$off = 'WindowsServer'
$sku = '2019-Datacenter'
$ver = 'latest'
$zn = '1'
$loc = 'eastus'

## Create a virtual machine configuration. $cred and $nicVM1 are variables with configuration from the previous steps. ##

$vmConfig = 
New-AzVMConfig -VMName $vm -VMSize $siz | Set-AzVMOperatingSystem -Windows -ComputerName $vm -Credential $cred | Set-AzVMSourceImage -PublisherName $pub -Offer WindowsServer -Skus $sku -Version $ver | Add-AzVMNetworkInterface -Id $nicVM1.Id

## Create the virtual machine ##
New-AzVM -ResourceGroupName $rg -Zone $zn -Location $loc -VM $vmConfig
```


#### <a name="vm2"></a>VM2

* 名前は **myVM2** にします。
* リソース グループは **myResourceGroupLB** にします。
* ネットワーク インターフェイス **myNicVM2** に接続します。
* ロード バランサー **myLoadBalancer** に接続します。
* ゾーンは **Zone 2** にします。
* 場所は **eastus** にします。

```azurepowershell-interactive
## Variables used for command. ##
$rg = 'myResourceGroupLB'
$vm = 'myVM2'
$siz = 'Standard_DS1_v2'
$pub = 'MicrosoftWindowsServer'
$off = 'WindowsServer'
$sku = '2019-Datacenter'
$ver = 'latest'
$zn = '2'
$loc = 'eastus'

## Create a virtual machine configuration. $cred and $nicVM2 are variables with configuration from the previous steps. ##

$vmConfig = 
New-AzVMConfig -VMName $vm -VMSize $siz | Set-AzVMOperatingSystem -Windows -ComputerName $vm -Credential $cred | Set-AzVMSourceImage -PublisherName $pub -Offer WindowsServer -Skus $sku -Version $ver | Add-AzVMNetworkInterface -Id $nicVM2.Id

## Create the virtual machine ##
New-AzVM -ResourceGroupName $rg -Zone $zn -Location $loc -VM $vmConfig
```

#### <a name="vm3"></a>VM3

* 名前は **myVM3** にします。
* リソース グループは **myResourceGroupLB** にします。
* ネットワーク インターフェイス **myNicVM3** に接続します。
* ロード バランサー **myLoadBalancer** に接続します。
* ゾーンは **Zone 3** にします。
* 場所は **eastus** にします。

```azurepowershell-interactive
## Variables used for command. ##
$rg = 'myResourceGroupLB'
$vm = 'myVM3'
$siz = 'Standard_DS1_v2'
$pub = 'MicrosoftWindowsServer'
$off = 'WindowsServer'
$sku = '2019-Datacenter'
$ver = 'latest'
$zn = '3'
$loc = 'eastus'

## Create a virtual machine configuration. $cred and $nicVM3 are variables with configuration from the previous steps. ##

$vmConfig = 
New-AzVMConfig -VMName $vm -VMSize $siz | Set-AzVMOperatingSystem -Windows -ComputerName $vm -Credential $cred | Set-AzVMSourceImage -PublisherName $pub -Offer WindowsServer -Skus $sku -Version $ver | Add-AzVMNetworkInterface -Id $nicVM3.Id

## Create the virtual machine ##
New-AzVM -ResourceGroupName $rg -Zone $zn -Location $loc -VM $vmConfig
```

## <a name="create-outbound-rule-configuration"></a>アウトバウンド規則構成の作成
ロード バランサーのアウトバウンド規則では、バックエンド プール内の VM 用にアウトバウンド送信元ネットワーク アドレス変換 (SNAT) を構成します。 

アウトバウンド接続の詳細については、「[Azure のアウトバウンド接続](load-balancer-outbound-connections.md)」を参照してください。

### <a name="create-outbound-public-ip-address"></a>送信パブリック IP アドレスの作成

[New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) を使用して、以下のことを行います。

* **myPublicIPOutbound** という名前の Standard ゾーン冗長パブリック IP アドレスを作成します。
* **myResourceGroupLB** 内に作成します。

```azurepowershell-interactive
## Variables for the command ##
$rg = 'MyResourceGroupLB'
$loc = 'eastus'
$pubIP = 'myPublicIPOutbound'
$sku = 'Standard'
$all = 'static'

$publicIp = 
New-AzPublicIpAddress -ResourceGroupName $rg -Name $pubIP -Location $loc -AllocationMethod $all -SKU $sku
```

ゾーンのパブリック IP アドレスをゾーン 1 に作成するには、次のコマンドを使用します。

```azurepowershell-interactive
## Variables for the command ##
$rg = 'MyResourceGroupLB'
$loc = 'eastus'
$pubIP = 'myPublicIPOutbound'
$sku = 'Standard'
$all = 'static'

$publicIp = 
New-AzPublicIpAddress -ResourceGroupName $rg -Name $pubIP -Location $loc -AllocationMethod $all -SKU $sku -zone 1
```
### <a name="create-outbound-frontend-ip-configuration"></a>アウトバウンド フロントエンド IP 構成を作成する

[Add-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/add-azloadbalancerfrontendipconfig) を使用して、次のように新しいフロントエンド IP 構成を作成します。

* 名前は **myFrontEndOutbound** にします。
* パブリック IP アドレス **myPublicIPOutbound** に関連付けます。

```azurepowershell-interactive
## Variables for the command ##
$fen = 'myFrontEndOutbound'

## Get the load balancer configuration  and apply the frontend config##
Get-AzLoadBalancer -Name $lbn -ResourceGroupName $rg | Add-AzLoadBalancerFrontendIPConfig -Name $fen -PublicIpAddress $publicIP | Set-AzLoadBalancer
```

### <a name="create-outbound-pool"></a>送信プールを作成する

[Add-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/add-azloadbalancerbackendaddresspoolconfig) を使用して、新しいアウトバウンド プールを作成します。 

[Set-AzLoadBalancer](/powershell/module/az.network/set-azloadbalancer) を使用して、次のようにプールおよびフロントエンド IP アドレスをロード バランサーに適用します。

* 名前は **myBackEndPoolOutbound** にします。

```azurepowershell-interactive
## Variables for the command ##
$ben = 'myBackEndPoolOutbound'
$lbn = 'myLoadBalancer'
$rg = 'myResourceGroupLB'

## Get the load balancer configuration and create the outbound backend address pool##
Get-AzLoadBalancer -Name $lbn -ResourceGroupName $rg | Add-AzLoadBalancerBackendAddressPoolConfig -Name $ben | Set-AzLoadBalancer
```
### <a name="create-outbound-rule-and-apply-to-load-balancer"></a>アウトバウンド規則を作成し、ロード バランサーに適用する

[Add-AzLoadBalancerOutboundRuleConfig](/powershell/module/az.network/new-azloadbalanceroutboundruleconfig) を使用して、次のようにアウトバウンド バックエンド プールの新しいアウトバウンド規則を作成します。 

[Set-AzLoadBalancer](/powershell/module/az.network/set-azloadbalancer) を使用して、次のように規則をロード バランサーに適用します。

* 名前は **myOutboundRule** にします。
* ロード バランサー **myLoadBalancer** に関連付けます。
* フロントエンド **myFrontEndOutbound** に関連付けます。
* プロトコルは **All** にします。
* アイドル タイムアウトは **15** にします。
* アウトバウンド ポートは **10000** にします。
* バックエンド プール **myBackEndPoolOutbound** に関連付けます。
* リソース グループは **myResourceGroupLB** にします。

```azurepowershell-interactive
## Variables for the commands ##
$rg = 'myResourceGroupLB'
$lbn = 'myLoadBalancer'
$brn = 'myOutboundRule'
$pro = 'All'
$idl = '15'
$por = '10000'

## Get the load balancer configuration ##
$lb = 
Get-AzLoadBalancer -Name $lbn -ResourceGroupName $rg 

## Apply the outbound rule configuration to the load balancer. ##
$lb | Add-AzLoadBalancerOutBoundRuleConfig -Name $brn -FrontendIPConfiguration $lb.FrontendIpConfigurations[1] -BackendAddressPool $lb.BackendAddressPools[1] -Protocol $pro -IdleTimeoutInMinutes $idl -AllocatedOutboundPort $por | Set-AzLoadBalancer
```

### <a name="add-virtual-machines-to-outbound-pool"></a>仮想マシンをアウトバウンド プールに追加する

[Add-AzNetworkInterfaceIpConfig](/powershell/module/az.network/add-aznetworkinterfaceipconfig) を使用して、次のようにロード バランサーのアウトバウンド プールに仮想マシンのネットワーク インターフェイスを追加します。


#### <a name="vm1"></a>VM1
* バックエンド アドレス プールは **myBackEndPoolOutbound** にします。
* リソース グループは **myResourceGroupLB** にします。
* ネットワーク インターフェイス **myNicVM1** および **ipconfig1** に関連付けます。
* ロード バランサー **myLoadBalancer** に関連付けます。

```azurepowershell-interactive
## Variables for the commands ##
$rg = 'myResourceGroupLB'
$lbn = 'myLoadBalancer'
$bep = 'myBackEndPoolOutbound'
$nic1 = 'myNicVM1'
$ipc = 'ipconfig1'

## Get the load balancer configuration ##
$lb = 
Get-AzLoadBalancer -Name $lbn -ResourceGroupName $rg

## Get the network interface configuration ##
$nic = 
Get-AzNetworkInterface -Name $nic1 -ResourceGroupName $rg

## Apply the backend to the network interface ##
$nic | Set-AzNetworkInterfaceIpConfig -Name $ipc -LoadBalancerBackendAddressPoolId $lb.BackendAddressPools[0].id,$lb.BackendAddressPools[1].id | Set-AzNetworkInterface
```

#### <a name="vm2"></a>VM2
* バックエンド アドレス プールは **myBackEndPoolOutbound** にします。
* リソース グループは **myResourceGroupLB** にします。
* ネットワーク インターフェイス **myNicVM2** および **ipconfig1** に関連付けます。
* ロード バランサー **myLoadBalancer** に関連付けます。

```azurepowershell-interactive
## Variables for the commands ##
$rg = 'myResourceGroupLB'
$lbn = 'myLoadBalancer'
$bep = 'myBackEndPoolOutbound'
$nic2 = 'myNicVM2'
$ipc = 'ipconfig1'

## Get the load balancer configuration ##
$lb = 
Get-AzLoadBalancer -Name $lbn -ResourceGroupName $rg

## Get the network interface configuration ##
$nic = 
Get-AzNetworkInterface -Name $nic2 -ResourceGroupName $rg

## Apply the backend to the network interface ##
$nic | Set-AzNetworkInterfaceIpConfig -Name $ipc -LoadBalancerBackendAddressPoolId $lb.BackendAddressPools[0].id,$lb.BackendAddressPools[1].id | Set-AzNetworkInterface
```

#### <a name="vm3"></a>VM3
* バックエンド アドレス プールは **myBackEndPoolOutbound** にします。
* リソース グループは **myResourceGroupLB** にします。
* ネットワーク インターフェイス **myNicVM3** および **ipconfig1** に関連付けます。
* ロード バランサー **myLoadBalancer** に関連付けます。

```azurepowershell-interactive
## Variables for the commands ##
$rg = 'myResourceGroupLB'
$lbn = 'myLoadBalancer'
$bep = 'myBackEndPoolOutbound'
$nic3 = 'myNicVM3'
$ipc = 'ipconfig1'

## Get the load balancer configuration ##
$lb = 
Get-AzLoadBalancer -Name $lbn -ResourceGroupName $rg

## Get the network interface configuration ##
$nic = 
Get-AzNetworkInterface -Name $nic3 -ResourceGroupName $rg

## Apply the backend to the network interface ##
$nic | Set-AzNetworkInterfaceIpConfig -Name $ipc -LoadBalancerBackendAddressPoolId $lb.BackendAddressPools[0].id,$lb.BackendAddressPools[1].id | Set-AzNetworkInterface

```

# <a name="option-2-create-a-public-load-balancer-basic-sku"></a>[オプション 2: パブリック ロード バランサーを作成する (Basic SKU)](#tab/option-1-create-load-balancer-basic)

>[!NOTE]
>運用環境のワークロードには、Standard SKU ロード バランサーをお勧めします。 SKU の詳細については、「 **[Azure Load Balancer の SKU](skus.md)** 」を参照してください。

## <a name="create-a-public-ip-address"></a>パブリック IP アドレスの作成

インターネット上の Web アプリにアクセスするには、ロード バランサーのパブリック IP アドレスが必要です。 

[New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) を使用して、以下のことを行います。

* **myPublicIP** という名前の Standard ゾーン冗長パブリック IP アドレスを作成します。
* **myResourceGroupLB** 内に作成します。

```azurepowershell-interactive
## Variables for the command ##
$rg = 'MyResourceGroupLB'
$loc = 'eastus'
$pubIP = 'myPublicIP'
$sku = 'Basic'
$all = 'static'

$publicIp = 
New-AzPublicIpAddress -ResourceGroupName $rg -Name $pubIP -Location $loc -AllocationMethod $all -SKU $sku
```

## <a name="create-basic-load-balancer"></a>Basic ロード バランサーを作成する

このセクションでは、ロード バランサーの以下のコンポーネントを作成および構成する方法について説明します。

  * ロード バランサーの着信ネットワーク トラフィックを受け取るフロントエンド IP プール。
  * フロントエンド プールから負荷分散されたネットワーク トラフィックが送信されるバックエンド IP プール。
  * バックエンド VM インスタンスの正常性を判断する正常性プローブ。
  * VM に対するトラフィックの分散方法を定義するロード バランサー規則。

### <a name="create-frontend-ip"></a>フロントエンド IP を作成する

[New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig) を使用して、次のようにフロントエンド IP を作成します。

* 名前は **myFrontEnd** にします。
* パブリック IP **myPublicIP** に接続します。

```azurepowershell-interactive
## Variables for the commands ##
$fe = 'myFrontEnd'
$rg = 'MyResourceGroupLB'
$loc = 'eastus'
$pubIP = 'myPublicIP'

$publicIp = 
Get-AzPublicIpAddress -Name $pubIP -ResourceGroupName $rg

$feip = 
New-AzLoadBalancerFrontendIpConfig -Name $fe -PublicIpAddress $publicIp
```

### <a name="configure-back-end-address-pool"></a>バックエンド アドレス プールを構成する

[New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) を使用して、次のようにバックエンド アドレス プールを作成します。 

* 名前は **myBackEndPool** にします。
* 残りの手順では、VM をこのバックエンド プールにアタッチします。

```azurepowershell-interactive
## Variable for the command ##
$be = 'myBackEndPool'

$bepool = 
New-AzLoadBalancerBackendAddressPoolConfig -Name $be
```

### <a name="create-the-health-probe"></a>正常性プローブを作成する

正常性プローブは、すべての仮想マシン インスタンスを調べて、ネットワーク トラ	フィックを送信できるかどうかを確認します。 

プローブ チェックが失敗した仮想マシンは、ロード バランサーから削除されます。 障害が解決されると、仮想マシンがロード バランサーに再び追加されます。

[Add-AzLoadBalancerProbeConfig](/powershell/module/az.network/add-azloadbalancerprobeconfig) を使用して、次のように正常性プローブを作成します。

* 仮想マシンの正常性を監視します。
* 名前は **myHealthProbe** にします。
* プロトコルは **TCP** にします。
* **ポート 80** を監視します。

```azurepowershell-interactive
## Variables for the command ##
$hp = 'myHealthProbe'
$pro = 'http'
$port = '80'
$int = '360'
$cnt = '5'

$probe = 
New-AzLoadBalancerProbeConfig -Name $hp -Protocol $pro -Port $port -RequestPath / -IntervalInSeconds $int -ProbeCount $cnt
```

### <a name="create-the-load-balancer-rule"></a>ロード バランサー規則を作成する

ロード バランサー規則は、以下のものを定義します。

* 着信トラフィック用のフロントエンド IP 構成。
* トラフィックを受信するためのバックエンド IP プール。
* 必要な発信元ポートと宛先ポート。 

[Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig) を使用して、次のようにロード バランサー規則を作成します。 

* 名前は **myHTTPRule** にします
* フロントエンド プール **myFrontEnd** で**ポート 80** をリッスンします。
* **ポート 80** を使用して、負荷分散されたネットワーク トラフィックをバックエンド アドレス プール **myBackEndPool** に送信します。 
* 正常性プローブ **myHealthProbe** を使用します。
* プロトコルは **TCP** にします。
* フロントエンド IP アドレスを使用して、アウトバウンドの送信元ネットワーク アドレス変換 (SNAT) を有効にします。


```azurepowershell-interactive
## Variables for the command ##
$lbr = 'myHTTPRule'
$pro = 'tcp'
$port = '80'

## $feip and $bePool are the variables from previous steps. ##

$rule = 
New-AzLoadBalancerRuleConfig -Name $lbr -Protocol $pro -Probe $probe -FrontendPort $port -BackendPort $port -FrontendIpConfiguration $feip -BackendAddressPool $bePool
```

### <a name="create-load-balancer-resource"></a>ロード バランサーのリソースを作成する

[New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer) を使用して、次のようにパブリック ロード バランサーを作成します。

* 名前は **myLoadBalancer** にします
* 場所は **eastus** にします。
* リソース グループは **myResourceGroupLB** にします。

```azurepowershell-interactive
## Variables for the command ##
$lbn = 'myLoadBalancer'
$rg = 'myResourceGroupLB'
$loc = 'eastus'
$sku = 'Basic'

## $feip, $bepool, $probe, $rule are variables with configuration information from previous steps. ##

$lb = 
New-AzLoadBalancer -ResourceGroupName $rg -Name $lbn -SKU $sku -Location $loc -FrontendIpConfiguration $feip -BackendAddressPool $bepool -Probe $probe -LoadBalancingRule $rule
```

## <a name="configure-virtual-network"></a>仮想ネットワークを構成する

VM をデプロイしてロード バランサーをテストする前に、サポート用の仮想ネットワーク リソースを作成します。

### <a name="create-a-virtual-network-and-azure-bastion-host"></a>仮想ネットワークと Azure Bastion ホストを作成する

[New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) を使用して、次のように仮想ネットワークを作成します。

* 名前は **myVNet** にします。
* リソース グループは **myResourceGroupLB** にします。
* サブネットの名前は **myBackendSubnet** にします。
* 仮想ネットワークは **10.0.0.0/16** にします。
* サブネットは **10.0.0.0/24** にします。
* Bastion サブネット **10.0.1.0/24**

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroupLB'
$loc = 'eastus'
$sub = 'myBackendSubnet'
$bsub = 'AzureBastionSubnet'
$spfx = '10.0.0.0/24'
$bpfx = '10.0.1.0/24'
$vnm = 'myVNet'
$vpfx = '10.0.0.0/16'


## Create backend subnet config ##
$subnetConfig = 
New-AzVirtualNetworkSubnetConfig -Name $sub -AddressPrefix $spfx

## Create Bastion subnet config ##
$bassubnetConfig =
New-AzVirtualNetworkSubnetConfig -name $bsub -AddressPrefix $bpfx

## Create the virtual network ##
$vnet = 
New-AzVirtualNetwork -ResourceGroupName $rg -Location $loc -Name $vnm -AddressPrefix $vpfx -Subnet $subnetConfig,$bassubnetConfig
```
[New-AzBastion](/powershell/module/az.network/new-azbastion) で、次の要塞ホストを作成します。

* 名前: **myBastionHost**
* パブリック IP: **myBastionIP**

```azurepowershell-interactive
$rg = 'myResourceGroupLB'
$loc = 'eastus'
$bas = 'myBastionHost'
$basip = 'myBastionIP'

## Create public IP address for Bastion host ##
$basip = 
New-AzPublicIPAddress -ResourceGroupName $rg -Location $loc

## Create the bastion host using the $vnet variable from previous step ##
New-AzBastion -ResourceGroupName $rg -Name $bas -PublicIpAddress $basip -VirtualNetwork $vnet
```

### <a name="create-network-security-group"></a>ネットワーク セキュリティ グループの作成
ネットワーク セキュリティ グループを作成して、仮想ネットワークへの受信接続を定義します。

#### <a name="create-a-network-security-group-rule-for-port-80"></a>ポート 80 のネットワーク セキュリティ グループ規則を作成する
[New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) を使用して、次のようにネットワーク セキュリティ グループの規則を作成します。

* 名前は **myNSGRuleHTTP** にします。
* 説明は **Allow HTTP** にします。
* アクセスは **Allow** にします。
* プロトコルは **TCP** にします。
* 方向は **Inbound** にします。
* 優先度は **2000** にします。
* 発信元は **Internet** にします。
* 発信元ポート範囲は **(*)** にします。
* 宛先アドレス プレフィックスは **(*)** にします。
* 宛先は**ポート 80** にします。

```azurepowershell-interactive
## Variables for command ##
$rnm = 'myNSGRuleHTTP'
$des = 'Allow HTTP'
$acc = 'Allow'
$pro = 'Tcp'
$dir = 'Inbound'
$pri = '2000'
$spfx = 'Internet'
$spr = '*'
$dpfx = '*'
$dpr = '80'

$rule2 = 
New-AzNetworkSecurityRuleConfig -Name $rnm -Description $des -Access $acc -Protocol $pro -Direction $dir -Priority $pri -SourceAddressPrefix $spfx -SourcePortRange $spr -DestinationAddressPrefix $dpfx -DestinationPortRange $dpr
```

#### <a name="create-a-network-security-group"></a>ネットワーク セキュリティ グループの作成

[New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) を使用して、次のようにネットワーク セキュリティ グループを作成します。

* 名前は **myNSG** にします。
* リソース グループは **myResourceGroupLB** にします。
* 場所は **eastus** にします。
* 前の手順で作成したセキュリティ規則を変数に格納します。

```azurepowershell
## Variables for command ##
$rg = 'myResourceGroupLB'
$loc = 'eastus'
$nmn = 'myNSG'

## $rule1 and $rule2 are variables with configuration information from the previous steps. ##
$nsg = 
New-AzNetworkSecurityGroup -ResourceGroupName $rg -Location $loc -Name $nmn -SecurityRules $rule1,$rule2
```

### <a name="create-network-interfaces"></a>ネットワーク インターフェイスの作成

[New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) を使用して、次のように 3 つのネットワーク インターフェイスを作成します。

#### <a name="vm-1"></a>VM 1

* 名前は **myNicVM1** にします。
* リソース グループは **myResourceGroupLB** にします。
* 場所は **eastus** にします。
* 仮想ネットワークは **myVNet** にします。
* サブネットは **myBackendSubnet** にします。
* ネットワーク セキュリティ グループは **myNSG** にします。
* **myBackEndPool** のロード バランサー **myLoadBalancer** に接続します。

```azurepowershell-interactive
## Variables for command ##
$rg = 'myResourceGroupLB'
$loc = 'eastus'
$nic1 = 'myNicVM1'
$vnt = 'myVNet'
$lb = 'myLoadBalancer'
$ngn = 'myNSG'

## Command to get virtual network configuration. ##
$vnet = 
Get-AzVirtualNetwork -Name $vnt -ResourceGroupName $rg

## Command to get load balancer configuration
$bepool = 
Get-AzLoadBalancer -Name $lb -ResourceGroupName $rg | Get-AzLoadBalancerBackendAddressPoolConfig

## Command to get network security group configuration ##
$nsg = 
Get-AzNetworkSecurityGroup -Name $ngn -ResourceGroupName $rg

## Command to create network interface for VM1 ##
$nicVM1 = 
New-AzNetworkInterface -ResourceGroupName $rg -Location $loc -Name $nic1 -LoadBalancerBackendAddressPool $bepool -NetworkSecurityGroup $nsg -Subnet $vnet.Subnets[0]
```

#### <a name="vm-2"></a>VM 2

* 名前は **myNicVM2** にします。
* リソース グループは **myResourceGroupLB** にします。
* 場所は **eastus** にします。
* 仮想ネットワークは **myVNet** にします。
* サブネットは **myBackendSubnet** にします。
* ネットワーク セキュリティ グループは **myNSG** にします。
* **myBackEndPool** のロード バランサー **myLoadBalancer** に接続します。

```azurepowershell-interactive
## Variables for command ##
$rg = 'myResourceGroupLB'
$loc = 'eastus'
$nic2 = 'myNicVM2'
$vnt = 'myVNet'
$lb = 'myLoadBalancer'
$ngn = 'myNSG'

## Command to get virtual network configuration. ##
$vnet = 
Get-AzVirtualNetwork -Name $vnt -ResourceGroupName $rg

## Command to get load balancer configuration
$bepool = 
Get-AzLoadBalancer -Name $lb -ResourceGroupName $rg | Get-AzLoadBalancerBackendAddressPoolConfig

## Command to get network security group configuration ##
$nsg = 
Get-AzNetworkSecurityGroup -Name $ngn -ResourceGroupName $rg

## Command to create network interface for VM2 ##
$nicVM2 = 
New-AzNetworkInterface -ResourceGroupName $rg -Location $loc -Name $nic2 -LoadBalancerBackendAddressPool $bepool -NetworkSecurityGroup $nsg -Subnet $vnet.Subnets[0]
```

#### <a name="vm-3"></a>VM 3

* 名前は **myNicVM3** にします。
* リソース グループは **myResourceGroupLB** にします。
* 場所は **eastus** にします。
* 仮想ネットワークは **myVNet** にします。
* サブネットは **myBackendSubnet** にします。
* ネットワーク セキュリティ グループは **myNSG** にします。
* **myBackEndPool** のロード バランサー **myLoadBalancer** に接続します。

```azurepowershell-interactive
## Variables for command ##
$rg = 'myResourceGroupLB'
$loc = 'eastus'
$nic3 = 'myNicVM3'
$vnt = 'myVNet'
$lb = 'myLoadBalancer'
$ngn = 'myNSG'

## Command to get virtual network configuration. ##
$vnet = 
Get-AzVirtualNetwork -Name $vnt -ResourceGroupName $rg

## Command to get load balancer configuration
$bepool = 
Get-AzLoadBalancer -Name $lb -ResourceGroupName $rg | Get-AzLoadBalancerBackendAddressPoolConfig

## Command to get network security group configuration ##
$nsg = 
Get-AzNetworkSecurityGroup -Name $ngn -ResourceGroupName $rg

## Command to create network interface for VM3 ##
$nicVM3 = 
New-AzNetworkInterface -ResourceGroupName $rg -Location $loc -Name $nic3 -LoadBalancerBackendAddressPool $bepool -NetworkSecurityGroup $nsg -Subnet $vnet.Subnets[0]
```

### <a name="create-availability-set-for-virtual-machines"></a>仮想マシンの可用性セットを作成する

[New-AzAvailabilitySet](/powershell/module/az.compute/new-azvm) を使用して、次のように仮想マシンの可用性セットを作成します。

* 名前は **myAvSet** にします。
* リソース グループは **myResourceGroupLB** にします。
* 場所は **eastus** にします。

```azurepowershell-interactive
## Variables used for the command. ##
$rg = 'myResourceGroupLB'
$avs = 'myAvSet'
$loc = 'eastus'

New-AzAvailabilitySet -ResourceGroupName $rg -Name $avs -Location $loc
```

### <a name="create-virtual-machines"></a>仮想マシンを作成する

次のように、[Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) を使用して VM の管理者のユーザー名とパスワードを設定します。

```azurepowershell
$cred = Get-Credential
```

以下を使用して、仮想マシンを作成します。

* [New-AzVM](/powershell/module/az.compute/new-azvm)
* [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig)
* [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem)
* [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage)
* [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface)


#### <a name="vm1"></a>VM1

* 名前は **myVM1** にします。
* リソース グループは **myResourceGroupLB** にします。
* ネットワーク インターフェイス **myNicVM1** に接続します。
* ロード バランサー **myLoadBalancer** に接続します。
* 場所は **eastus** にします。
* 可用性セットは **myAvSet** にします。

```azurepowershell-interactive
## Variables used for command. ##
$rg = 'myResourceGroupLB'
$vm = 'myVM1'
$siz = 'Standard_DS1_v2'
$pub = 'MicrosoftWindowsServer'
$off = 'WindowsServer'
$sku = '2019-Datacenter'
$ver = 'latest'
$loc = 'eastus'
$avs = 'myAvSet'

## Create a virtual machine configuration. $cred and $nicVM1 are variables with configuration from the previous steps. ##

$vmConfig = 
New-AzVMConfig -VMName $vm -VMSize $siz | Set-AzVMOperatingSystem -Windows -ComputerName $vm -Credential $cred | Set-AzVMSourceImage -PublisherName $pub -Offer WindowsServer -Skus $sku -Version $ver | Add-AzVMNetworkInterface -Id $nicVM1.Id

## Create the virtual machine ##
New-AzVM -ResourceGroupName $rg -Location $loc -VM $vmConfig -AvailabilitySetName $avs
```


#### <a name="vm2"></a>VM2

* 名前は **myVM2** にします。
* リソース グループは **myResourceGroupLB** にします。
* ネットワーク インターフェイス **myNicVM2** に接続します。
* ロード バランサー **myLoadBalancer** に接続します。
* 場所は **eastus** にします。
* 可用性セットは **myAvSet** にします。

```azurepowershell-interactive
## Variables used for command. ##
$rg = 'myResourceGroupLB'
$vm = 'myVM2'
$siz = 'Standard_DS1_v2'
$pub = 'MicrosoftWindowsServer'
$off = 'WindowsServer'
$sku = '2019-Datacenter'
$ver = 'latest'
$loc = 'eastus'
$avs = 'myAvSet'

## Create a virtual machine configuration. $cred and $nicVM2 are variables with configuration from the previous steps. ##

$vmConfig = 
New-AzVMConfig -VMName $vm -VMSize $siz | Set-AzVMOperatingSystem -Windows -ComputerName $vm -Credential $cred | Set-AzVMSourceImage -PublisherName $pub -Offer WindowsServer -Skus $sku -Version $ver | Add-AzVMNetworkInterface -Id $nicVM2.Id

## Create the virtual machine ##
New-AzVM -ResourceGroupName $rg -Location $loc -VM $vmConfig -AvailabilitySetName $avs
```

#### <a name="vm3"></a>VM3

* 名前は **myVM3** にします。
* リソース グループは **myResourceGroupLB** にします。
* ネットワーク インターフェイス **myNicVM3** に接続します。
* ロード バランサー **myLoadBalancer** に接続します。
* 場所は **eastus** にします。
* 可用性セットは **myAvSet** にします。

```azurepowershell-interactive
## Variables used for command. ##
$rg = 'myResourceGroupLB'
$vm = 'myVM3'
$siz = 'Standard_DS1_v2'
$pub = 'MicrosoftWindowsServer'
$off = 'WindowsServer'
$sku = '2019-Datacenter'
$ver = 'latest'
$loc = 'eastus'
$avs = 'myAvSet'

## Create a virtual machine configuration. $cred and $nicVM3 are variables with configuration from the previous steps. ##

$vmConfig = 
New-AzVMConfig -VMName $vm -VMSize $siz | Set-AzVMOperatingSystem -Windows -ComputerName $vm -Credential $cred | Set-AzVMSourceImage -PublisherName $pub -Offer WindowsServer -Skus $sku -Version $ver | Add-AzVMNetworkInterface -Id $nicVM3.Id

## Create the virtual machine ##
New-AzVM -ResourceGroupName $rg -Location $loc -VM $vmConfig -AvailabilitySetName $avs
```

3 つの VM を作成して構成するには数分かかります。

---

## <a name="install-iis"></a>IIS のインストール

1. Azure portal に[サインイン](https://portal.azure.com)します。

2. 左側のメニューで **[すべてのサービス]** 、 **[すべてのリソース]** の順に選択し、リソースの一覧で **[myResourceGroupLB]** リソース グループにある **[myVM1]** を選択します。

3. **[概要]** ページで **[接続]** 、 **[要塞]** の順に選択します。

4. VM 作成時に入力したユーザー名とパスワードを入力します。

5. **[接続]** を選択します。

6. サーバーのデスクトップで、 **[Windows 管理ツール]**  >  **[Windows PowerShell]** の順に移動します。

7. PowerShell ウィンドウで、次のコマンドを実行して以下の作業を行います。

    * IIS サーバーをインストールする
    * 既定の iisstart.htm ファイルを削除する
    * VM の名前が表示された新しい iisstart.htm ファイルを追加する。

   ```powershell
    
    # install IIS server role
     Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # remove default htm file
     Remove-Item  C:\inetpub\wwwroot\iisstart.htm
    
    # Add a new htm file that displays server name
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
   ```
8. **myVM1** との Bastion セッションを閉じます。

9. 手順 1. から 8. を繰り返して、IIS と更新済み iisstart.htm ファイルを **myVM2** と **myVM3** にインストールします。

## <a name="test-the-load-balancer"></a>ロード バランサーをテストする

1. Azure portal の **[概要]** 画面で、ロード バランサーのパブリック IP アドレスを見つけます。 左側のメニューで **[すべてのサービス]** 、 **[すべてのリソース]** 、 **[myPublicIP]** の順に選択します。

2. そのパブリック IP アドレスをコピーし、ブラウザーのアドレス バーに貼り付けます。 IIS Web サーバーの既定のページがブラウザーに表示されます。

   ![IIS Web サーバー](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

ロード バランサーが 3 つの VM すべてにトラフィックを分散していることを確認するには、各 VM の IIS Web サーバーの既定のページをカスタマイズした後、クライアント マシンで Web ブラウザーを強制的に最新の情報に更新します。


## <a name="clean-up-resources"></a>リソースをクリーンアップする

必要がなくなれば、[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) コマンドを使用して、リソース グループやロード バランサー、その他のリソースを削除できます。

```azurepowershell-interactive
## Variable for command. ##
$rg = 'myResourceGroupLB'

Remove-AzResourceGroup -Name $rg
```

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、

* Standard または Basic パブリック ロード バランサーを作成しました
* 仮想マシンを接続しました。 
* ロード バランサーのトラフィック規則と正常性プローブを構成しました。
* ロード バランサーをテストしました。

Azure Load Balancer の詳細については、「[Azure Load Balancer の概要](load-balancer-overview.md)」および「[Load Balancer に関してよく寄せられる質問](load-balancer-faqs.md)」を参照してください。

* [Load Balancer と可用性ゾーン](load-balancer-standard-availability-zones.md)について理解を深めます。
* Azure Bastion の詳細については、「[Azure Bastion とは](https://docs.microsoft.com/azure/bastion/bastion-overview)」を参照してください。

