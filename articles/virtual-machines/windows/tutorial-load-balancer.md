---
title: "Azure で Windows 仮想マシンの負荷分散を行う方法 | Microsoft Docs"
description: "Azure Load Balancer を使用して、可用性が高く堅牢なアプリケーションを 3 つの Windows VM にまたがって作成する方法について説明します。"
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
ms.date: 04/17/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 2c33e75a7d2cb28f8dc6b314e663a530b7b7fdb4
ms.openlocfilehash: e9a91a9a933104e4f885b009490ec35468398662
ms.lasthandoff: 04/21/2017

---

# <a name="how-to-load-balance-windows-virtual-machines-in-azure-to-create-a-highly-available-application"></a>Azure の Windows 仮想マシンを負荷分散して高可用性アプリケーションを作成する方法
このチュートリアルでは、トラフィックを分散し高可用性を提供する、Azure Load Balancer のさまざまなコンポーネントについて説明します。 ロード バランサーの動作を確認するには、3 つの Windows 仮想マシン (VM) 上で動作する単純な IIS Web サイトを構築します。

このチュートリアルの手順は、最新バージョンの [Azure PowerShell](/powershell/azureps-cmdlets-docs/) モジュールを使用して行うことができます。


## <a name="azure-load-balancer-overview"></a>Azure Load Balancer の概要
Azure Load Balancer は、着信トラフィックを正常な VM に分散することで高可用性を実現する第 4 層 (TCP、UDP) のロード バランサーです。 ロード バランサーの正常性プローブは、各 VM の特定のポートを監視し、稼働している VM にのみトラフィックを分散します。

1 つまたは複数のパブリック IP アドレスが含まれるフロントエンド IP 構成を定義します。 このフロントエンド IP 構成によって、ロード バランサーとアプリケーションにインターネット経由でアクセスできるようになります。 

仮想マシンは、仮想ネットワーク インターフェイス カード (NIC) を使用してロード バランサーに接続されます。 トラフィックを VM に分散するには、バックエンド アドレス プールに、ロード バランサーに接続される仮想 NIC の IP アドレスを含めます。

トラフィックのフローを制御するには、VM にマップする特定のポートとプロトコルについて、ロード バランサー規則を定義します。


## <a name="create-azure-load-balancer"></a>Azure Load Balancer を作成する
このセクションでは、ロード バランサーの各コンポーネントを作成および設定する方法について説明します。 ロード バランサーを作成する前に、[New-AzureRmResourceGroup](/powershell/resourcemanager/AzureRM.Resources/new-azurermresourcegroup) を使用してリソース グループを作成します。 次の例では、`myResourceGroupLoadBalancer` という名前のリソース グループを `westus` の場所に作成します。

```powershell
New-AzureRmResourceGroup `
  -ResourceGroupName myResourceGroupLoadBalancer `
  -Location westus
```

### <a name="create-a-public-ip-address"></a>パブリック IP アドレスの作成
インターネット上のアプリにアクセスするには、ロード バランサーのパブリック IP アドレスが必要です。 [New-AzureRmPublicIpAddress](/powershell/resourcemanager/azurerm.network/new-azurermpublicipaddress) を使用してパブリック IP アドレスを作成します。 `myResourceGroupLoadBalancer` リソース グループに `myPublicIP` という名前のパブリック IP アドレスを作成する例を次に示します。

```powershell
$publicIP = New-AzureRmPublicIpAddress `
  -ResourceGroupName myResourceGroupLoadBalancer `
  -Location westus `
  -AllocationMethod Static `
  -Name myPublicIP
```

### <a name="create-a-load-balancer"></a>ロード バランサーの作成
[New-AzureRmLoadBalancerFrontendIpConfig](/powershell/resourcemanager/azurerm.network/new-azurermloadbalancerfrontendipconfig) を使用して、フロントエンド IP アドレスを作成します。 次の例では、`myFrontEndPool` という名前のフロントエンド IP アドレスを作成します。 

```powershell
$frontendIP = New-AzureRmLoadBalancerFrontendIpConfig `
  -Name myFrontEndPool `
  -PublicIpAddress $publicIP
```

[New-AzureRmLoadBalancerBackendAddressPoolConfig](/powershell/resourcemanager/azurerm.network/new-azurermloadbalancerbackendaddresspoolconfig) を使用して、バックエンド アドレス プールを作成します。 次の例では、`myBackEndPool` という名前のバックエンド アドレス プールを作成します。

```powershell
$backendPool = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name myBackEndPool
```

次に、[New-AzureRmLoadBalancer](/powershell/resourcemanager/azurerm.network/new-azurermloadbalancer) を使用して、ロード バランサーを作成します。 `myPublicIP` アドレスを使用して `myLoadBalancer` という名前のロード バランサーを作成する例を次に示します。

```powershell
$lb = New-AzureRmLoadBalancer `
  -ResourceGroupName myResourceGroupLoadBalancer `
  -Name myLoadBalancer `
  -Location westus `
  -FrontendIpConfiguration $frontendIP `
  -BackendAddressPool $backendPool
```

### <a name="create-a-health-probe"></a>正常性プローブの作成
ロード バランサーでアプリの状態を監視するには、正常性プローブを使用します。 正常性プローブは、ロード バランサーのローテーションに含める VM を、正常性チェックへの応答に基づいて動的に追加したり削除したりする働きをします。 既定では、15 秒間隔のチェックが 2 回連続でエラーになった VM はロード バランサーのローテーションから外されます。 正常性プローブは、プロトコルに基づいて作成するか、またはアプリの特定の正常性チェック ページに基づいて作成します。 

次の例では、TCP プローブを作成します。 よりきめ細やかな正常性チェックを行う場合は、カスタムの HTTP プローブを作成することもできます。 カスタム HTTP プローブを使用する場合は、`healthcheck.aspx` などの正常性チェック ページを作成する必要があります。 対象となるホストをロード バランサーのローテーションに維持するには、プローブが **HTTP 200 OK** 応答を返す必要があります。

TCP 正常性プローブを作成するには、[Add-AzureRmLoadBalancerProbeConfig](/powershell/resourcemanager/azurerm.network/add-azurermloadbalancerprobeconfig) を使用します。 次の例では、個々の VM を監視する `myHealthProbe` という名前の正常性プローブを作成しています。

```powershell
Add-AzureRmLoadBalancerProbeConfig -Name myHealthProbe `
  -LoadBalancer $lb `
  -Protocol tcp `
  -Port 80 `
  -IntervalInSeconds 15 `
  -ProbeCount 2
```

### <a name="create-a-load-balancer-rule"></a>ロード バランサー規則の作成
ロード バランサー規則の目的は、一連の VM に対するトラフィックの分散方法を定義することです。 着信トラフィック用のフロントエンド IP 構成と、トラフィックを受信するためのバックエンド IP プールを、必要な発信元ポートと宛先ポートと共に定義します。 正常な VM のみでトラフィックを受信するには、使用する正常性プローブの定義も行います。

[Add-AzureRmLoadBalancerRuleConfig](/powershell/resourcemanager/AzureRM.Network/add-azurermloadbalancerruleconfig) を使用して、ロード バランサー規則を作成します。 次の例では、`myLoadBalancerRule` という名前のロード バランサー規則を、ポート `80` に作成します。

```powershell
Add-AzureRmLoadBalancerRuleConfig -Name myLoadBalancerRule `
  -LoadBalancer $lb `
  -FrontendIpConfiguration $lb.FrontendIpConfigurations[0] `
  -BackendAddressPool $lb.BackendAddressPools[0] `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80
```

[Set-AzureRmLoadBalancer](/powershell/resourcemanager/azurerm.network/set-azurermloadbalancer) を使用して、ロード バランサーを更新します。

```powershell
Set-AzureRmLoadBalancer -LoadBalancer $lb
```


## <a name="configure-virtual-network"></a>仮想ネットワークを構成する
一部の VM をデプロイしてバランサーをテストする前に、関連する仮想ネットワーク リソースを作成します。 仮想ネットワークの詳細については、[Azure 仮想ネットワークの管理](tutorial-virtual-network.md)に関するチュートリアルを参照してください。

### <a name="create-network-resources"></a>ネットワーク リソースを作成する
[New-AzureRmVirtualNetwork](/powershell/resourcemanager/azurerm.network/new-azurermvirtualnetwork) を使用して仮想ネットワークを作成します。 次の例では、`mySubnet` に `myVnet` という名前の仮想ネットワークを作成します。

```powershell
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
  -Name mySubnet `
  -AddressPrefix 192.168.1.0/24
$vnet = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroupLoadBalancer `
  -Location westus `
  -Name myVnet `
  -AddressPrefix 192.168.0.0/16 `
  -Subnet $subnetConfig
```

[New-AzureRmNetworkSecurityRuleConfig](/powershell/resourcemanager/azurerm.network/new-azurermnetworksecurityruleconfig) を使用してネットワーク セキュリティ グループの規則を作成し、[New-AzureRmNetworkSecurityGroup](/powershell/resourcemanager/azurerm.network/new-azurermnetworksecuritygroup) を使用してネットワーク セキュリティ グループを作成します。 [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/resourcemanager/azurerm.network/set-azurermvirtualnetworksubnetconfig) を使用してネットワーク セキュリティ グループをサブネットに追加し、[Set-AzureRmVirtualNetwork](/powershell/resourcemanager/azurerm.network/set-azurermvirtualnetwork) を使用して仮想ネットワークを更新します。 

次の例では、`myNetworkSecurityGroup` という名前のネットワーク セキュリティ グループの規則を作成し、これを `mySubnet` に適用します。

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
$nsg = New-AzureRmNetworkSecurityGroup `
  -ResourceGroupName myResourceGroupLoadBalancer `
  -Location westus `
  -Name myNetworkSecurityGroup `
  -SecurityRules $nsgRule
Set-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet `
  -Name mySubnet `
  -NetworkSecurityGroup $nsg `
  -AddressPrefix 192.168.1.0/24
Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
```

仮想 NIC は、[New-AzureRmNetworkInterface](/powershell/resourcemanager/azurerm.network/new-azurermnetworkinterface) を使用して作成します。 以下の例では、3 つの仮想 NIC を作成します  (以降の手順では、アプリ用に作成する VM ごとに仮想 NIC を 1 つ)。 いつでも追加の仮想 NIC と VM を作成してロード バランサーに追加することができます。

```powershell
for ($i=1; $i -le 3; $i++)
{
   New-AzureRmNetworkInterface -ResourceGroupName myResourceGroupLoadBalancer `
     -Name myNic$i `
     -Location westus `
     -Subnet $vnet.Subnets[0] `
     -LoadBalancerBackendAddressPool $lb.BackendAddressPools[0]
}
```

## <a name="create-virtual-machines"></a>仮想マシンを作成する
アプリの高可用性を高めるには、可用性セットに VM を配置します。

可用性セットを作成するには、[New-AzureRmAvailabilitySet](/powershell/resourcemanager/azurerm.compute/new-azurermavailabilityset) を使用します。 次の例では、`myAvailabilitySet` という名前の可用性セットを作成します。

```powershell
$availabilitySet = New-AzureRmAvailabilitySet `
  -ResourceGroupName myResourceGroupLoadBalancer `
  -Name myAvailabilitySet `
  -Location westus `
  -Managed `
  -PlatformFaultDomainCount 3 `
  -PlatformUpdateDomainCount 2
```

次のように、[Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) を使用して VM の管理者のユーザー名とパスワードを設定します。

```powershell
$cred = Get-Credential
```

[New-AzureRmVM](/powershell/resourcemanager/azurerm.compute/new-azurermvm) を使用して VM を作成できるようになりました。 次の例では、3 つの VM を作成します。

```powershell
for ($i=1; $i -le 3; $i++)
{
   $vm = New-AzureRmVMConfig -VMName myVM$i -VMSize Standard_D1 -AvailabilitySetId $availabilitySet.Id
   $vm = Set-AzureRmVMOperatingSystem -VM $vm -Windows -ComputerName myVM$i -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
   $vm = Set-AzureRmVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2016-Datacenter -Version latest
   $vm = Set-AzureRmVMOSDisk -VM $vm -Name myOsDisk$i -DiskSizeInGB 128 -CreateOption FromImage -Caching ReadWrite
   $nic = Get-AzureRmNetworkInterface -ResourceGroupName myResourceGroupLoadBalancer -Name myNic$i
   $vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
   New-AzureRmVM -ResourceGroupName myResourceGroupLoadBalancer -Location westus -VM $vm
}
```

3 台の VM をすべて作成して構成するには、数分かかります。

### <a name="install-iis-with-custom-script-extension"></a>カスタム スクリプト拡張機能を使用して IIS をインストールする
[Windows 仮想マシンをカスタマイズする方法](tutorial-automate-vm-deployment.md)に関する先行のチュートリアルで、Windows のカスタム スクリプト拡張機能を使用して VM のカスタマイズを自動化する方法が説明されています。 同じアプローチを使用して、VM に IIS をインストールして構成することができます。

[Set-AzureRmVMExtension](/powershell/resourcemanager/azurerm.compute/set-azurermvmextension) を使用して、カスタム スクリプト拡張機能をインストールします。 この拡張機能によって `powershell Add-WindowsFeature Web-Server` が実行され、IIS Web サーバーがインストールされます。次に、VM のホスト名を表示するように `Default.htm` ページが更新されます。

```powershell
for ($i=1; $i -le 3; $i++)
{
   Set-AzureRmVMExtension `
     -ResourceGroupName myResourceGroupLoadBalancer `
     -ExtensionName IIS `
     -VMName myVM$i `
     -Publisher Microsoft.Compute `
     -ExtensionType CustomScriptExtension `
     -TypeHandlerVersion 1.4 `
     -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
     -Location westus
}
```

## <a name="test-load-balancer"></a>ロード バランサーをテストする
ロード バランサーのパブリック IP アドレスを取得するには、[Get-AzureRmPublicIPAddress](/powershell/resourcemanager/azurerm.network/get-azurermpublicipaddress) を使用します。 次の例では、先ほど作成した `myPublicIP` の IP アドレスを取得しています。

```powershell
Get-AzureRmPublicIPAddress `
  -ResourceGroupName myResourceGroupLoadBalancer `
  -Name myPublicIP | select IpAddress
```

このパブリック IP アドレスを Web ブラウザーに入力できます。 次の例のように、Web サイトが表示され、ロード バランサーによって負荷分散されたトラフィックの宛先となった VM のホスト名が表示されます。

![実行中の IIS Web サイト](./media/tutorial-load-balancer/running-iis-website.png)

アプリを実行している 3 つの VM すべての間で、ロード バランサーがトラフィックを負荷分散していることを確認するには、Web ブラウザーを強制的に最新の情報に更新します。


## <a name="add-and-remove-vms"></a>仮想マシンの追加と削除
アプリを実行している VM には、OS の更新プログラムをインストールするなどメンテナンスが必要になることもあります。 また、アプリのトラフィックが増大すれば、それに対処するために、新たに VM を追加しなければなりません。 このセクションでは、ロード バランサーから VM を除外したりロード バランサーに対して VM を追加したりする方法について説明します。

### <a name="remove-a-vm-from-the-load-balancer"></a>ロード バランサーから VM を除外する
[Get-AzureRmNetworkInterface](/powershell/resourcemanager/azurerm.network/get-azurermnetworkinterface) を使用してネットワーク インターフェイス カードを取得し、仮想 NIC の `LoadBalancerBackendAddressPools` プロパティを `$null` に設定します。 最後に、仮想 NIC を更新します。

```powershell
$nic = Get-AzureRmNetworkInterface -ResourceGroupName myResourceGroupLoadBalancer -Name myNic2
$nic.Ipconfigurations[0].LoadBalancerBackendAddressPools=$null
Set-AzureRmNetworkInterface -NetworkInterface $nic
```

アプリを実行している残りの 2 つの VM の間で、ロード バランサーがトラフィックを負荷分散していることを確認するには、Web ブラウザーを強制的に最新の情報に更新します。 これで VM に対して、OS 更新プログラムのインストールや VM の再起動などのメンテナンスを行うことができます。

### <a name="add-a-vm-to-the-load-balancer"></a>ロード バランサーに VM を追加する
VM のメンテナンスを実施した後や、処理能力の引き上げが必要となった場合は、[Get-AzureRMLoadBalancer]() から仮想 NIC の `LoadBalancerBackendAddressPools` プロパティを `BackendAddressPool` に設定します。

ロード バランサーを取得します。

```powershell
$lb = Get-AzureRMLoadBalancer -ResourceGroupName myResourceGroupLoadBalancer -Name myLoadBalancer 
$nic.IpConfigurations[0].LoadBalancerBackendAddressPools=$lb.BackendAddressPools[0]
Set-AzureRmNetworkInterface -NetworkInterface $nic
```

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、負荷分散された IIS Web サイトの作成方法を説明しました。 次のチュートリアルに進み、VM ネットワークの管理方法を学習してください。

[Azure VM ネットワークの管理](./tutorial-virtual-network.md)
