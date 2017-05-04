---
title: "チュートリアル - Azure VM における高可用性アプリケーションの作成 | Microsoft Docs"
description: "Azure で 3 台の Windows VM とロード バランサーを使用して、可用性が高く堅牢なアプリケーションを作成する方法について説明します。"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/30/2017
ms.author: davidmu
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 61fd0dc45cfa2d320713819be3db811be58ab77e
ms.lasthandoff: 04/27/2017

---

# <a name="build-a-load-balanced-highly-available-application-on-windows-virtual-machines-in-azure"></a>Azure の Windows 仮想マシンにおける負荷分散・高可用性アプリケーションの作成

このチュートリアルでは、メンテナンス イベントの発生時も正常な動作を維持できる高可用性アプリケーションを作成します。 このアプリでは、ロード バランサーと可用性セット、さらに 3 台の Windows 仮想マシン (VM) を使います。 このチュートリアルでは、IIS をインストールしますが、同じ高可用性コンポーネントとガイドラインでさまざまなアプリケーション フレームワークをデプロイできます。 

## <a name="step-1---azure-prerequisites"></a>手順 1 - Azure の前提条件

このチュートリアルに取り組む前に、最新の [Azure PowerShell](/powershell/azure/overview) モジュールがインストールされていることを確認してください。

最初に、Login-AzureRmAccount コマンドを使用して Azure サブスクリプションにログインし、画面上の指示に従います。

```powershell
Login-AzureRmAccount
```

Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 他の Azure リソースを作成する前に、[New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) を使用してリソース グループを作成する必要があります。 次の例では、`myResourceGroup` という名前のリソース グループを `westeurope` リージョンに作成します。 

```powershell
New-AzureRmResourceGroup -ResourceGroupName myResourceGroup -Location westeurope
```

## <a name="step-2---create-availability-set"></a>手順 2 - 可用性セットの作成

仮想マシンは、論理上の障害ドメインと更新ドメインにまたがって作成することができます。 それぞれの論理ドメインは、基盤となる Azure データセンターにおける特定のハードウェア領域を表します。 複数の VM を作成すると、それらの領域にまたがってコンピューティング リソースとストレージ リソースが分散されます。 ハードウェア コンポーネントをメンテナンスする必要がある場合でも、この分散によってアプリの可用性が保たれます。 こうした論理上の障害ドメインと更新ドメインは、可用性セットを通じて定義することができます。

可用性セットを作成するには、[New-AzureRmAvailabilitySet](/powershell/module/azurerm.compute/new-azurermavailabilityset) を使用します。 次の例では、`myAvailabilitySet` という名前の可用性セットを作成します。

```powershell
$availabilitySet = New-AzureRmAvailabilitySet `
  -ResourceGroupName myResourceGroup `
  -Name myAvailabilitySet `
  -Location westeurope `
  -Managed `
  -PlatformFaultDomainCount 3 `
  -PlatformUpdateDomainCount 2
```

## <a name="step-3---create-load-balancer"></a>手順 3 - ロード バランサーの作成

Azure のロード バランサーは、定義されている一連の VM に対し、ロード バランサー規則に従ってトラフィックを分散します。 正常性プローブが各 VM の特定のポートを監視し、稼働している VM にのみトラフィックを分散します。

### <a name="create-public-ip-address"></a>パブリック IP アドレスの作成

インターネット上のアプリにアクセスするには、パブリック IP アドレスをロード バランサーに割り当てます。 [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) を使用してパブリック IP アドレスを作成します。 `myPublicIP` という名前のパブリック IP アドレスを作成する例を次に示します。

```powershell
$pip = New-AzureRmPublicIpAddress `
  -ResourceGroupName myResourceGroup `
  -Location westeurope `
  -AllocationMethod Static `
  -Name myPublicIP
```

### <a name="create-load-balancer"></a>ロード バランサーの作成

[New-AzureRmLoadBalancerFrontendIpConfig](/powershell/module/azurerm.network/new-azurermloadbalancerfrontendipconfig) を使用して、フロントエンド IP アドレスを作成します。 次の例では、`myFrontEndPool` という名前のフロントエンド IP アドレスを作成します。 

```powershell
$frontendIP = New-AzureRmLoadBalancerFrontendIpConfig -Name myFrontEndPool -PublicIpAddress $pip
```

[New-AzureRmLoadBalancerBackendAddressPoolConfig](/powershell/module/azurerm.network/new-azurermloadbalancerbackendaddresspoolconfig) を使用して、バックエンド アドレス プールを作成します。 次の例では、`myBackEndPool` という名前のバックエンド アドレス プールを作成します。

```powershell
$backendPool = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name myBackEndPool
```

次に、[New-AzureRmLoadBalancer](/powershell/module/azurerm.network/new-azurermloadbalancer) を使用して、ロード バランサーを作成します。 `myPublicIP` アドレスを使用して `myLoadBalancer` という名前のロード バランサーを作成する例を次に示します。

```powershell
$lb = New-AzureRmLoadBalancer `
  -ResourceGroupName myResourceGroup `
  -Name myLoadBalancer `
  -Location westeurope `
  -FrontendIpConfiguration $frontendIP `
  -BackendAddressPool $backendPool
```

### <a name="create-health-probe"></a>正常性プローブの作成

ロード バランサーでアプリの状態を監視するには、正常性プローブを使用します。 正常性プローブは、ロード バランサーのローテーションに含める VM を、正常性チェックへの応答に基づいて動的に追加したり削除したりする働きをします。 既定では、15 秒間隔のチェックが 2 回連続でエラーになった VM はロード バランサーのローテーションから外されます。

[Add-AzureRmLoadBalancerProbeConfig](/powershell/module/azurerm.network/add-azurermloadbalancerprobeconfig) を使用して、正常性プローブを作成します。 次の例では、個々の VM を監視する `myHealthProbe` という名前の正常性プローブを作成しています。

```powershell
Add-AzureRmLoadBalancerProbeConfig -Name myHealthProbe `
  -LoadBalancer $lb `
  -Protocol tcp `
  -Port 80 `
  -IntervalInSeconds 15 `
  -ProbeCount 2
```

### <a name="create-load-balancer-rule"></a>ロード バランサー規則の作成

ロード バランサー規則の目的は、一連の VM に対するトラフィックの分散方法を定義することです。

[Add-AzureRmLoadBalancerRuleConfig](/powershell/module/azurerm.network/add-azurermloadbalancerruleconfig) を使用して、ロード バランサー規則を作成します。 次の例では、`myLoadBalancerRule` という名前のロード バランサー規則を、ポート `80` に作成します。

```powershell
Add-AzureRmLoadBalancerRuleConfig -Name myLoadBalancerRule `
  -LoadBalancer $lb `
  -FrontendIpConfiguration $lb.FrontendIpConfigurations[0] `
  -BackendAddressPool $lb.BackendAddressPools[0] `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80
```

[Set-AzureRmLoadBalancer](/powershell/module/azurerm.network/set-azurermloadbalancer) を使用して、ロード バランサーを更新します。

```powershell
Set-AzureRmLoadBalancer -LoadBalancer $lb
```

## <a name="step-4---configure-networking"></a>手順 4 - ネットワークの構成

それぞれの VM には、仮想ネットワーク インターフェイス カード (NIC) が少なくとも 1 つ備わっていて、そこから仮想ネットワークに接続されています。 この仮想ネットワークは、定義されているアクセス規則に基づいてトラフィックをフィルタリングするようにセキュリティで保護されています。

### <a name="create-virtual-network"></a>Create virtual network

最初に、[New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) を使用してサブネットを構成します。 次の例では、`mySubnet` という名前のサブネットを作成します。

```powershell
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24
```

対象の VM にネットワーク接続を提供するには、[New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) を使用して仮想ネットワークを作成します。 次の例では、`mySubnet` に `myVnet` という名前の仮想ネットワークを作成します。

```powershell
$vnet = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location westeurope `
  -Name myVnet `
  -AddressPrefix 192.168.0.0/16 `
  -Subnet $subnetConfig
```

### <a name="configure-network-security"></a>ネットワーク セキュリティの構成

Azure [ネットワーク セキュリティ グループ](../../virtual-network/virtual-networks-nsg.md) (NSG) は、仮想マシンの受信トラフィックと送信トラフィックを制御します。 特定のポートまたは特定のポート範囲に対するネットワーク トラフィックが、ネットワーク セキュリティ グループの規則によって許可または拒否されます。 また、これらの規則に送信元アドレス プレフィックスを含めれば、あらかじめ決められた送信元からのトラフィックにのみ仮想マシンとの通信を許可することができます。

Web トラフィックを目的のアプリに誘導するには、[New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig) を使用して、ネットワーク セキュリティ グループの規則を作成します。 次の例では、`myNetworkSecurityGroupRule` という名前のネットワーク セキュリティ グループの規則を作成しています。

```powershell
$nsgRule = New-AzureRmNetworkSecurityRuleConfig `
  -Name myNetworkSecurityGroupRule `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 1001 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 80 `
  -Access Allow
```

[New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup) を使用して、ネットワーク セキュリティ グループを作成します。 次の例では、`myNetworkSecurityGroup` という名前の NSG を作成します。

```powershell
$nsg = New-AzureRmNetworkSecurityGroup `
  -ResourceGroupName myResourceGroup `
  -Location westeurope `
  -Name myNetworkSecurityGroup `
  -SecurityRules $nsgRule
```

[Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig) を使用して、仮想ネットワークのサブネットにネットワーク セキュリティ グループを追加します。

```powershell
Set-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet `
  -Name mySubnet `
  -NetworkSecurityGroup $nsg `
  -AddressPrefix 192.168.1.0/24
```

[Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork) を使用して、仮想ネットワークを更新します。

```powershell
Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
```

### <a name="create-virtual-network-interface-cards"></a>仮想ネットワーク インターフェイス カードの作成

ロード バランサーは、VM そのものではなく仮想 NIC リソースと連動します。 仮想 NIC は、ロード バランサーに接続され、そのうえで VM に接続されます。

[New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) を使用して、仮想 NIC を作成します。 以下の例では、3 つの仮想 NIC を作成します  (以降の手順で、アプリ用に VM ごとに 1 つの仮想 NIC を作成します)。


```powershell
for ($i=1; $i -le 3; $i++)
{
   New-AzureRmNetworkInterface -ResourceGroupName myResourceGroup `
     -Name myNic$i `
     -Location westeurope `
     -Subnet $vnet.Subnets[0] `
     -LoadBalancerBackendAddressPool $lb.BackendAddressPools[0]
}

```

## <a name="step-5---create-virtual-machines"></a>手順 5 - 仮想マシンを作成する

基礎となるコンポーネントがすべて整ったら、アプリを実行するための高可用性 VM を作成します。 

[Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) を使用して、仮想マシンの管理者アカウントに必要なユーザー名とパスワードを設定します。

```powershell
$cred = Get-Credential
```

[New-AzureRmVMConfig](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.8.0/new-azurermvmconfig)[Set-AzureRmVMOperatingSystem](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.8.0/set-azurermvmoperatingsystem)、[Set-AzureRmVMSourceImage](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.8.0/set-azurermvmsourceimage)、[Set-AzureRmVMOSDisk](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.8.0/set-azurermvmosdisk)、[Add-AzureRmVMNetworkInterface](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.8.0/add-azurermvmnetworkinterface)、および [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm)を使用して、VM を作成します。 次の例では、3 台の VM を作成します。

```powershell
for ($i=1; $i -le 3; $i++)
{
   $vm = New-AzureRmVMConfig -VMName myVM$i -VMSize Standard_D1 -AvailabilitySetId $availabilitySet.Id
   $vm = Set-AzureRmVMOperatingSystem -VM $vm -Windows -ComputerName myVM$i -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
   $vm = Set-AzureRmVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2016-Datacenter -Version latest
   $vm = Set-AzureRmVMOSDisk -VM $vm -Name myOsDisk$i -StorageAccountType StandardLRS -DiskSizeInGB 128 -CreateOption FromImage -Caching ReadWrite
   $nic = Get-AzureRmNetworkInterface -ResourceGroupName myResourceGroup -Name myNic$i
   $vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
   New-AzureRmVM -ResourceGroupName myResourceGroup -Location westeurope -VM $vm
}

```

3 台の VM をすべて作成して構成するには、数分かかります。 それぞれの VM でアプリがいつ実行されるかは、ロード バランサーの正常性プローブによって自動的に検出されます。 アプリが実行状態になると、ロード バランサー規則によってトラフィックの負荷分散が開始されます。

### <a name="install-the-app"></a>アプリのインストール 

Azure 仮想マシン拡張機能を使用して、アプリケーションのインストールやオペレーティング システムの構成などの仮想マシンの構成タスクを自動化することができます。 [Windows のカスタム スクリプト拡張機能](./../virtual-machines-windows-extensions-customscript.md)を使用して、仮想マシンで任意の PowerShell スクリプトを実行することができます。 スクリプトは Azure Storage やアクセス可能な任意の HTTP エンドポイントに格納するか、カスタム スクリプト拡張機能の構成に埋め込むことができます。 カスタム スクリプト拡張機能を使用する場合は、Azure VM エージェントがスクリプトの実行を管理します。

[Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) を使用して、カスタム スクリプト拡張機能をインストールします。 拡張機能は、`powershell Add-WindowsFeature Web-Server` を実行して IIS Web サーバーをインストールします。

```powershell
for ($i=1; $i -le 3; $i++)
{
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup `
     -ExtensionName IIS `
     -VMName myVM$i `
     -Publisher Microsoft.Compute `
     -ExtensionType CustomScriptExtension `
     -TypeHandlerVersion 1.4 `
     -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server"}' `
     -Location westeurope
}
```

### <a name="test-your-app"></a>アプリケーションをテストする

ロード バランサーのパブリック IP アドレスを取得するには、[Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) を使用します。 次の例では、先ほど作成した `myPublicIP` の IP アドレスを取得しています。

```powershell
Get-AzureRmPublicIPAddress -ResourceGroupName myResourceGroup -Name myPublicIP | select IpAddress
```

このパブリック IP アドレスを Web ブラウザーに入力します。 NSG 規則が適用され、既定の IIS Web サイトが表示されます。 

![IIS の既定のサイト](media/load-balanced-iis-tutorial/iis.png)

## <a name="step-6--management-tasks"></a>手順 6 – 管理タスク

アプリを実行している VM には、OS の更新プログラムをインストールするなどメンテナンスが必要になることもあります。 また、アプリのトラフィックが増大すれば、それに対処するために、新たに VM を追加しなければなりません。 このセクションでは、ロード バランサーから VM を除外したりロード バランサーに対して VM を追加したりする方法について説明します。 

### <a name="remove-a-vm-from-the-load-balancer"></a>ロード バランサーから VM を除外する

ネットワーク インターフェイス カードの LoadBalancerBackendAddressPools プロパティをリセットすることで、バックエンド アドレス プールから VM を削除します。

[Get-AzureRmNetworkInterface](/powershell/module/azurerm.network/get-azurermnetworkinterface) を使用して、ネットワーク インターフェイス カードを取得します。

```powershell
$nic = Get-AzureRmNetworkInterface -ResourceGroupName myResourceGroup -Name myNic2
``` 

ネットワーク インターフェイス カードの LoadBalancerBackendAddressPools プロパティを $null に設定します。

```powershell
$nic.Ipconfigurations[0].LoadBalancerBackendAddressPools=$null
```

ネットワーク インターフェイス カードを更新します。

```powershell
Set-AzureRmNetworkInterface -NetworkInterface $nic
```

### <a name="add-a-vm-to-the-load-balancer"></a>ロード バランサーに VM を追加する

VM のメンテナンスを実施した後や、処理能力の引き上げが必要となった場合は、ロード バランサーのバックエンド アドレス プールに VM を追加します。

ロード バランサーを取得します。

```powershell
$lb = Get-AzureRMLoadBalancer -ResourceGroupName myResourceGroup -Name myLoadBalancer 
```

ロード バランサーのバックエンド アドレス プールをネットワーク インターフェイス カードに追加します。

```powershell
$nic.IpConfigurations[0].LoadBalancerBackendAddressPools=$lb.BackendAddressPools[0]
```

ネットワーク インターフェイス カードを更新します。

```powershell
Set-AzureRmNetworkInterface -NetworkInterface $nic
```

## <a name="next-steps"></a>次のステップ

サンプル – [Azure 仮想マシンの PowerShell サンプル スクリプト](./../virtual-machines-windows-powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

