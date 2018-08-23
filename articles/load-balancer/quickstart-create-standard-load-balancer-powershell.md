---
title: 'クイック スタート: Standard Load Balancer を作成する - Azure PowerShell | Microsoft Docs'
description: このクイック スタートでは、PowerShell を使用して Standard Load Balancer を作成する方法について説明します
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
tags: azure-resource-manager
Customer intent: I want to create a Standard Load balancer so that I can load balance internet traffic to VMs.
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/22/2018
ms.author: kumud
ms:custom: mvc
ms.openlocfilehash: 67d514fe6315604016dc10b7dfc8154c3919f914
ms.sourcegitcommit: a62cbb539c056fe9fcd5108d0b63487bd149d5c3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/22/2018
ms.locfileid: "42616545"
---
# <a name="get-started"></a>クイック スタート: Azure PowerShell を使用して Standard Load Balancer を作成する
このクイック スタートでは、Azure PowerShell を使用して Standard Load Balancer を作成する方法について説明します ロード バランサーをテストするには、Windows Server を実行する 2 つの仮想マシン (VM) をデプロイし、VM 間で Web アプリの負荷を分散します。 Standard Load Balancer の詳細については、[Standard Load Balancer の概要](load-balancer-standard-overview.md)に関するページを参照してください。

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

PowerShell をインストールしてローカルで使用する場合、この記事では Azure PowerShell モジュール バージョン 5.4.1 以降が必要になります。 インストールされているバージョンを確認するには、`Get-Module -ListAvailable AzureRM` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-azurerm-ps)に関するページを参照してください。 PowerShell をローカルで実行している場合、`Login-AzureRmAccount` を実行して Azure との接続を作成することも必要です。 

## <a name="create-a-resource-group"></a>リソース グループの作成

ロード バランサーを作成する前に、[New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) を使用してリソース グループを作成する必要があります。 次の例では、*myResourceGroupLB* という名前のリソース グループを *EastUS* に作成します。

```azurepowershell-interactive
New-AzureRmResourceGroup `
  -ResourceGroupName "myResourceGroupLB" `
  -Location "EastUS"
```
## <a name="create-a-public-ip-address"></a>パブリック IP アドレスの作成
インターネット上のアプリにアクセスするには、ロード バランサーのパブリック IP アドレスが必要です。 [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) を使用してパブリック IP アドレスを作成します。 次の例では、*myPublicIP* という名前のパブリック IP アドレスを *myResourceGroupLB* リソース グループに作成します。

```azurepowershell-interactive
$publicIP = New-AzureRmPublicIpAddress `
-Name "myPublicIP" `
-ResourceGroupName "myResourceGroupLB" `
-Location "EastUS" `
-Sku "Standard" `
-AllocationMethod "Static"
  
```
## <a name="create-standard-load-balancer"></a>Standard Load Balancer を作成する
 このセクションでは、ロード バランサーのフロントエンド IP とバックエンド アドレス プールを構成してから、Basic Load Balancer を作成します。
 
### <a name="create-frontend-ip"></a>フロントエンド IP を作成する
[New-AzureRmLoadBalancerFrontendIpConfig](/powershell/module/azurerm.network/new-azurermloadbalancerfrontendipconfig) を使用して、フロントエンド IP を作成します。 次の例では、*myFrontEnd* という名前のフロントエンド IP 構成を作成し、*myPublicIP* アドレスにアタッチします。 

```azurepowershell-interactive
$frontendIP = New-AzureRmLoadBalancerFrontendIpConfig `
  -Name "myFrontEnd" `
  -PublicIpAddress $publicIP
```

### <a name="configure-backend-address-pool"></a>バックエンド アドレス プールを構成する

[New-AzureRmLoadBalancerBackendAddressPoolConfig](/powershell/module/azurerm.network/new-azurermloadbalancerbackendaddresspoolconfig) を使用して、バックエンド アドレス プールを作成します。 残りの手順では、VM をこのバックエンド プールにアタッチします。 次の例では、*myBackEndPool* という名前のバックエンド アドレス プールを作成します。

```azurepowershell-interactive
$backendPool = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name "myBackEndPool"
```
### <a name="create-a-health-probe"></a>正常性プローブの作成
ロード バランサーでアプリの状態を監視するには、正常性プローブを使用します。 正常性プローブは、ロード バランサーのローテーションに含める VM を、正常性チェックへの応答に基づいて動的に追加したり削除したりする働きをします。 既定では、15 秒間隔のチェックが 2 回連続でエラーになった VM はロード バランサーのローテーションから外されます。 正常性プローブは、プロトコルに基づいて作成するか、またはアプリの特定の正常性チェック ページに基づいて作成します。 

次の例では、TCP プローブを作成します。 よりきめ細やかな正常性チェックを行う場合は、カスタムの HTTP プローブを作成することもできます。 カスタム HTTP プローブを使用する場合は、*healthcheck.aspx* などの正常性チェック ページを作成する必要があります。 対象となるホストをロード バランサーのローテーションに維持するには、プローブが **HTTP 200 OK** 応答を返す必要があります。

TCP 正常性プローブを作成するには、[Add-AzureRmLoadBalancerProbeConfig](/powershell/module/azurerm.network/add-azurermloadbalancerprobeconfig) を使用します。 次の例では、*HTTP* ポート *80* で各 VM を監視する *myHealthProbe* という名前の正常性プローブを作成します。

```azurepowershell-interactive
$probe = New-AzureRmLoadBalancerProbeConfig `
  -Name "myHealthProbe" `
  -RequestPath healthcheck2.aspx `
  -Protocol http `
  -Port 80 `
  -IntervalInSeconds 16 `
  -ProbeCount 2
  ```

### <a name="create-a-load-balancer-rule"></a>ロード バランサー規則の作成
ロード バランサー規則の目的は、一連の VM に対するトラフィックの分散方法を定義することです。 着信トラフィック用のフロントエンド IP 構成と、トラフィックを受信するためのバックエンド IP プールを、必要な発信元ポートと宛先ポートと共に定義します。 正常な VM のみでトラフィックを受信するには、使用する正常性プローブの定義も行います。

[Add-AzureRmLoadBalancerRuleConfig](/powershell/module/azurerm.network/add-azurermloadbalancerruleconfig) を使用して、ロード バランサー規則を作成します。 次の例では、*myLoadBalancerRule* という名前のロード バランサー規則を作成し、*TCP* ポート *80* でトラフィックを負荷分散します。

```azurepowershell-interactive
$lbrule = New-AzureRmLoadBalancerRuleConfig `
  -Name "myLoadBalancerRule" `
  -FrontendIpConfiguration $frontendIP `
  -BackendAddressPool $backendPool `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80 `
  -Probe $probe
```

### <a name="create-the-nat-rules"></a>NAT 規則を作成する

[Add-AzureRmLoadBalancerRuleConfig](/powershell/module/azurerm.network/new-azurermloadbalancerinboundnatruleconfig) を使用して、NAT 規則を作成します。 次の例では、*myLoadBalancerRDP1* および *myLoadBalancerRDP2* という名前の NAT 規則を作成し、ポート 4221 および 4222 を使用するバックエンド サーバーへの RDP 接続を許可します。

```azurepowershell-interactive
$natrule1 = New-AzureRmLoadBalancerInboundNatRuleConfig `
-Name 'myLoadBalancerRDP1' `
-FrontendIpConfiguration $frontendIP `
-Protocol tcp `
-FrontendPort 4221 `
-BackendPort 3389

$natrule2 = New-AzureRmLoadBalancerInboundNatRuleConfig `
-Name 'myLoadBalancerRDP2' `
-FrontendIpConfiguration $frontendIP `
-Protocol tcp `
-FrontendPort 4222 `
-BackendPort 3389
```

### <a name="create-load-balancer"></a>ロード バランサーの作成

[New-AzureRmLoadBalancer](/powershell/module/azurerm.network/new-azurermloadbalancer) を使用して、Standard Load Balancer を作成します。 次の例では、前述の手順で作成したフロントエンド IP の構成、バックエンド プール、正常性プローブ、負荷分散規則、および NAT 規則を使用して、myLoadBalancer という名前のパブリック Basic Load Balancer を作成します。

```azurepowershell-interactive
$lb = New-AzureRmLoadBalancer `
-ResourceGroupName 'myResourceGroupLB' `
-Name 'MyLoadBalancer' `
-Location 'eastus' `
-FrontendIpConfiguration $frontendIP `
-BackendAddressPool $backendPool `
-Probe $probe `
-LoadBalancingRule $lbrule `
-InboundNatRule $natrule1,$natrule2 `
-sku Standard
```

## <a name="create-network-resources"></a>ネットワーク リソースを作成する
一部の VM を展開してバランサーをテストする前に、サポート ネットワーク リソース (仮想ネットワークと仮想 NIC) を作成する必要があります。 

### <a name="create-a-virtual-network"></a>仮想ネットワークの作成
[New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) を使用して仮想ネットワークを作成します。 次の例では、*mySubnet* と共に *myVnet* という名前の仮想ネットワークを作成します。

```azurepowershell-interactive
# Create subnet config
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
  -Name "mySubnet" `
  -AddressPrefix 10.0.2.0/24

# Create the virtual network
$vnet = New-AzureRmVirtualNetwork `
  -ResourceGroupName "myResourceGroupLB" `
  -Location "EastUS" `
  -Name "myVnet" `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $subnetConfig
```
### <a name="create-network-security-group"></a>ネットワーク セキュリティ グループの作成
ネットワーク セキュリティ グループを作成して、仮想ネットワークへの受信接続を定義します。

#### <a name="create-a-network-security-group-rule-for-port-3389"></a>ポート 3389 のネットワーク セキュリティ グループの規則を作成する
[New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig) を使用してポート 3389 経由の RDP 接続を許可するネットワーク セキュリティ グループ規則を作成します。

```azurepowershell-interactive

$rule1 = New-AzureRmNetworkSecurityRuleConfig `
-Name 'myNetworkSecurityGroupRuleRDP' `
-Description 'Allow RDP' `
-Access Allow `
-Protocol Tcp `
-Direction Inbound `
-Priority 1000 `
-SourceAddressPrefix Internet `
-SourcePortRange * `
-DestinationAddressPrefix * `
-DestinationPortRange 3389
```

#### <a name="create-a-network-security-group-rule-for-port-80"></a>ポート 80 のネットワーク セキュリティ グループ規則を作成する
[New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig) を使用してポート 80 経由の着信接続を許可するネットワーク セキュリティ グループ規則を作成します。

```azurepowershell-interactive
$rule2 = New-AzureRmNetworkSecurityRuleConfig `
-Name 'myNetworkSecurityGroupRuleHTTP' `
-Description 'Allow HTTP' `
-Access Allow `
-Protocol Tcp `
-Direction Inbound `
-Priority 2000 `
-SourceAddressPrefix Internet `
-SourcePortRange * `
-DestinationAddressPrefix * `
-DestinationPortRange 80
```
#### <a name="create-a-network-security-group"></a>ネットワーク セキュリティ グループの作成

[New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup) を使用して、ネットワーク セキュリティ グループを作成します。

```azurepowershell-interactive
$nsg = New-AzureRmNetworkSecurityGroup`
-ResourceGroupName 'myResourceGroupLB' `
-Location 'EastUS' `
-Name 'myNetworkSecurityGroup'`
-SecurityRules $rule1,$rule2
```

###<a name="create-nics"></a>NIC の作成
[New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) を使用して仮想 NIC を作成します。 次の例では、2 つの仮想 NIC を作成します  (以降の手順では、アプリ用に作成する VM ごとに仮想 NIC を 1 つ)。 いつでも追加の仮想 NIC と VM を作成してロード バランサーに追加することができます。

```azurepowershell-interactive
# Create NIC for VM1
$nicVM1 = New-AzureRmNetworkInterface `
-ResourceGroupName 'myResourceGroupLB' `
-Location 'EastUS' `
-Name 'MyNic1' `
-LoadBalancerBackendAddressPool $backendPool `
-NetworkSecurityGroup $nsg `
-LoadBalancerInboundNatRule $natrule1 `
-Subnet $vnet.Subnets[0]

# Create NIC for VM2
$nicVM2 = New-AzureRmNetworkInterface `
-ResourceGroupName 'myResourceGroupLB' `
-Location 'EastUS' `
-Name 'MyNic2' `
-LoadBalancerBackendAddressPool $backendPool `
-NetworkSecurityGroup $nsg `
-LoadBalancerInboundNatRule $natrule2 `
-Subnet $vnet.Subnets[0]

```

### <a name="create-virtual-machines"></a>仮想マシンを作成する
アプリの高可用性を高めるには、可用性セットに VM を配置します。

可用性セットを作成するには、[New-AzureRmAvailabilitySet](/powershell/module/azurerm.compute/new-azurermavailabilityset) を使用します。 次の例では、*myAvailabilitySet* という名前の可用性セットを作成します。

```azurepowershell-interactive
$availabilitySet = New-AzureRmAvailabilitySet `
  -ResourceGroupName "myResourceGroupLB" `
  -Name "myAvailabilitySet" `
  -Location "EastUS" `
  -Sku aligned `
  -PlatformFaultDomainCount 2 `
  -PlatformUpdateDomainCount 2
```

次のように、[Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) を使用して VM の管理者のユーザー名とパスワードを設定します。

```azurepowershell-interactive
$cred = Get-Credential
```

[New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) を使用して VM を作成できるようになりました。 次の例では、2 つの VM と必要な仮想ネットワーク コンポーネントがまだ存在しない場合にそれらを作成します。

```azurepowershell-interactive
for ($i=1; $i -le 2; $i++)
{
    New-AzureRmVm `
        -ResourceGroupName "myResourceGroupLB" `
        -Name "myVM$i" `
        -Location "East US" `
        -VirtualNetworkName "myVnet" `
        -SubnetName "mySubnet" `
        -SecurityGroupName "myNetworkSecurityGroup" `
        -OpenPorts 80 `
        -AvailabilitySetName "myAvailabilitySet" `
        -Credential $cred `
        -AsJob
}
```

`-AsJob` パラメーターにより、バックグラウンド タスクとして VM が作成されるため、PowerShell プロンプトが表示されます。 `Job` コマンドレットを使用して、バックグラウンド ジョブの詳細を表示できます。 2 つの VM を作成し、構成する処理には数分かかります。

### <a name="install-iis-with-custom-web-page"></a>カスタム Web ページで IIS をインストールする
 
次のようにカスタム Web ページを使用して両方のバックエンド VM に IIS をインストールします。

1. Load Balancer のパブリック IP アドレスを取得します。 `Get-AzureRmPublicIPAdress` を使用して、Load Balancer のパブリック IP アドレスを取得します。

  ```azurepowershell-interactive
    Get-AzureRmPublicIPAddress `
    -ResourceGroupName "myResourceGroupLB" `
    -Name "myPublicIP" | select IpAddress
  ```
2. 前の手順で取得したパブリック IP アドレスを使用して、VM1 へのリモート デスクトップ接続を作成します。 

  ```azurepowershell-interactive

      mstsc /v:PublicIpAddress:4221  
  
  ```
3. *VM1* の資格情報を入力して RDP セッションを開始します。
4. VM1 で Windows PowerShell を起動し、次のコマンドを使用して IIS サーバーのインストールと既定の htm ファイルの更新を行います。
    ```azurepowershell-interactive
    # Install IIS
      Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom htm file
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from host" + $env:computername)
    ```
5. *myVM1* との RDP 接続を閉じます。
6. `mstsc /v:PublicIpAddress:4222` コマンドを実行して *myVM2* との RDP 接続を作成し、*VM2* について手順 4 を繰り返します。

## <a name="test-load-balancer"></a>ロード バランサーをテストする
ロード バランサーのパブリック IP アドレスを取得するには、[Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) を使用します。 次の例では、先ほど作成した *myPublicIP* の IP アドレスを取得しています。

```azurepowershell-interactive
Get-AzureRmPublicIPAddress `
  -ResourceGroupName "myResourceGroupLB" `
  -Name "myPublicIP" | select IpAddress
```

このパブリック IP アドレスを Web ブラウザーに入力できます。 次の例のように、Web サイトが表示され、ロード バランサーによって負荷分散されたトラフィックの宛先となった VM のホスト名が表示されます。

![ロード バランサーをテストする](media/quickstart-create-basic-load-balancer-powershell/load-balancer-test.png)

アプリを実行している 2 つの VM すべての間で、ロード バランサーがトラフィックを負荷分散していることを確認するには、Web ブラウザーを強制的に最新の情報に更新します。 

## <a name="clean-up-resources"></a>リソースのクリーンアップ

必要がなくなったら、[Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) コマンドを使用して、リソース グループ、VM、およびすべての関連リソースを削除できます。

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroupLB
```

## <a name="next-steps"></a>次の手順

このクイック スタートでは、Basic Load Balancer を作成し、それに VM をアタッチして、ロード バランサー トラフィック規則と正常性プローブを構成してから、ロード バランサーをテストしました。 Azure Load Balancer についてさらに学習するには、Azure Load Balancer のチュートリアルに進みます。

> [!div class="nextstepaction"]
> [Azure Load Balancer のチュートリアル](tutorial-load-balancer-basic-internal-portal.md)
