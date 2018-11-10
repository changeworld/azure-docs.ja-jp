---
title: Azure PowerShell を使用してハイブリッド ネットワークに Azure Firewall をデプロイして構成する
description: このチュートリアルでは、Azure portal を使用して Azure Firewall をデプロイおよび構成する方法を学習します。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.date: 10/27/2018
ms.author: victorh
ms.openlocfilehash: 3c225e6fbfb13c04d650b8e6b72ee18d23139a8e
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/26/2018
ms.locfileid: "50158960"
---
# <a name="tutorial-deploy-and-configure-azure-firewall-in-a-hybrid-network-using-azure-powershell"></a>チュートリアル: Azure PowerShell を使用してハイブリッド ネットワークに Azure Firewall をデプロイして構成する

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * ネットワーク環境を設定する
> * ファイアウォールを構成してデプロイする
> * ルートを作成する
> * 仮想マシンの作成
> * ファイアウォールをテストする

このチュートリアルでは、次の 3 つの Vnet を作成します。
- **VNet-Hub** - この VNet にはファイアウォールがあります。
- **VNet-Spoke** - スポーク VNet は Azure 上のワークロードを表します。
- **VNet-Onprem** - OnPrem VNet はオンプレミス ネットワークを表します。 実際のデプロイでは、VPN 接続または Express Route 接続のいずれかで接続できます。 わかりやすくするため、このチュートリアルでは VPN ゲートウェイ接続を使用し、Azure に配置された VNet がオンプレミス ネットワークを表すために使用されます。

![ハイブリッド ネットワークでのファイアウォール](media/tutorial-hybrid-ps/hybrid-network-firewall.png)

## <a name="key-requirements"></a>主な要件

このシナリオが正しく機能するために重要な要件が 3 つあります。

- スポーク サブネット上のユーザー定義ルートが、Azure Firewall IP アドレスを既定のゲートウェイとして指すこと。 このルート テーブルでは BGP ルート伝達を**無効**にする必要があります。
- ハブ ゲートウェイ サブネット上のユーザー定義ルートが、スポーク ネットワークへの次のホップとしてファイアウォール IP アドレスを指すこと。
- Azure Firewall サブネット上にユーザー定義ルートは必要ありません。BGP からルートを学習するためです。
- VNet-Hub を VNet-Spoke にピアリングするときは **AllowGatewayTransit** を設定し、VNet-Spoke を VNet-Hub にピアリングするときは **UseRemoteGateways** を設定してください。

これらのルートの作成方法については、このチュートリアルの「[ルートを作成する](#create-routes)」セクションをご覧ください。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

## <a name="declare-the-variables"></a>変数を宣言する

以下の例では、このチュートリアルの値を使って変数を宣言します。 ほとんどの場合、値は実際のものに置き換える必要があります。 しかし、この手順を実行してこの種の構成に慣れたら、これらの変数を利用してもかまいません。 変数を必要に応じて変更したうえでコピーし、PowerShell コンソールに貼り付けます。

```azurepowershell
$RG1 = "FW-Hybrid-Test"
$Location1 = "East US"

# Variables for the firewall hub VNet

$VNetnameHub = "VNet-hub"
$SNnameHub = "AzureFirewallSubnet"
$VNetHubPrefix = "10.5.0.0/16"
$SNHubPrefix = "10.5.0.0/24"
$SNGWHubPrefix = "10.5.1.0/24"
$GWHubName = "GW-hub"
$GWHubpipName = "VNet-hub-GW-pip"
$GWIPconfNameHub = "GW-ipconf-hub"
$ConnectionNameHub = "hub-to-Onprem"

# Variables for the spoke VNet

$VnetNameSpoke = "VNet-Spoke"
$SNnameSpoke = "SN-Workload"
$VNetSpokePrefix = "10.6.0.0/16"
$SNSpokePrefix = "10.6.0.0/24"
$SNSpokeGWPrefix = "10.6.1.0/24"

# Variables for the OnPrem VNet

$VNetnameOnprem = "Vnet-Onprem"
$SNNameOnprem = "SN-Corp"
$VNetOnpremPrefix = "192.168.0.0/16"
$SNOnpremPrefix = "192.168.1.0/24"
$SNGWOnpremPrefix = "192.168.2.0/24"
$GWOnpremName = "GW-Onprem"
$GWIPconfNameOnprem = "GW-ipconf-Onprem"
$ConnectionNameOnprem = "Onprem-to-hub"
$GWOnprempipName = "VNet-Onprem-GW-pip"

$SNnameGW = "GatewaySubnet"
```

## <a name="create-a-resource-group"></a>リソース グループの作成

このチュートリアルで必要なすべてのリソースを含めるリソース グループを作成します。

```azurepowershell
  New-AzureRmResourceGroup -Name $RG1 -Location $Location1
  ```

## <a name="create-and-configure-the-firewall-hub-vnet"></a>ファイアウォール ハブ Vnet を作成して構成する

VNet に含めるサブネットを定義します。

```azurepowershell
$FWsub = New-AzureRmVirtualNetworkSubnetConfig -Name $SNnameHub -AddressPrefix $SNHubPrefix
$GWsub = New-AzureRmVirtualNetworkSubnetConfig -Name $SNnameGW -AddressPrefix $SNGWHubPrefix
```

ここで、ファイアウォール ハブ VNet を作成します。

```azurepowershell
$VNetHub = New-AzureRmVirtualNetwork -Name $VNetnameHub -ResourceGroupName $RG1 `
-Location $Location1 -AddressPrefix $VNetHubPrefix -Subnet $FWsub,$GWsub
```

VNet 用に作成する VPN ゲートウェイに割り当てるパブリック IP アドレスを要求します。 *AllocationMethod* が **Dynamic** であることに注意してください。 使用する IP アドレスを指定することはできません。 VPN アドレスはゲートウェイに動的に割り当てられます。 

  ```azurepowershell
  $gwpip1 = New-AzureRmPublicIpAddress -Name $GWHubpipName -ResourceGroupName $RG1 `
  -Location $Location1 -AllocationMethod Dynamic
```

## <a name="create-and-configure-the-spoke-vnet"></a>スポーク Vnet を作成して構成する

スポーク VNet に含めるサブネットを定義します。

```azurepowershell
$Spokesub = New-AzureRmVirtualNetworkSubnetConfig -Name $SNnameSpoke -AddressPrefix $SNSpokePrefix
$GWsubSpoke = New-AzureRmVirtualNetworkSubnetConfig -Name $SNnameGW -AddressPrefix $SNSpokeGWPrefix
```

スポーク VNet を作成します。

```azurepowershell
$VNetSpoke = New-AzureRmVirtualNetwork -Name $VnetNameSpoke -ResourceGroupName $RG1 `
-Location $Location1 -AddressPrefix $VNetSpokePrefix -Subnet $Spokesub,$GWsubSpoke
```

## <a name="create-and-configure-the-onprem-vnet"></a>OnPrem VNet を作成して構成する

VNet に含めるサブネットを定義します。

```azurepowershell
$Onpremsub = New-AzureRmVirtualNetworkSubnetConfig -Name $SNNameOnprem -AddressPrefix $SNOnpremPrefix
$GWOnpremsub = New-AzureRmVirtualNetworkSubnetConfig -Name $SNnameGW -AddressPrefix $SNGWOnpremPrefix
```

ここで、OnPrem VNet を作成します。

```azurepowershell
$VNetOnprem = New-AzureRmVirtualNetwork -Name $VNetnameOnprem -ResourceGroupName $RG1 `
-Location $Location1 -AddressPrefix $VNetOnpremPrefix -Subnet $Onpremsub,$GWOnpremsub
```

VNet 用に作成するゲートウェイに割り当てるパブリック IP アドレスを要求します。 *AllocationMethod* が **Dynamic** であることに注意してください。 使用する IP アドレスを指定することはできません。 IP アドレスはゲートウェイに動的に割り当てられます。 

  ```azurepowershell
  $gwOnprempip = New-AzureRmPublicIpAddress -Name $GWOnprempipName -ResourceGroupName $RG1 `
  -Location $Location1 -AllocationMethod Dynamic
```

## <a name="configure-and-deploy-the-firewall"></a>ファイアウォールを構成してデプロイする

ここで、ファイアウォールをハブ VNet にデプロイします。

```azurepowershell
# Get a Public IP for the firewall
$FWpip = New-AzureRmPublicIpAddress -Name "fw-pip" -ResourceGroupName $RG1 `
  -Location $Location1 -AllocationMethod Static -Sku Standard
# Create the firewall
$Azfw = New-AzureRmFirewall -Name AzFW01 -ResourceGroupName $RG1 -Location $Location1 -VirtualNetworkName $VNetnameHub -PublicIpName fw-pip

#Save the firewall private IP address for future use

$AzfwPrivateIP = $Azfw.IpConfigurations.privateipaddress
$AzfwPrivateIP

```

### <a name="configure-network-rules"></a>ネットワーク ルールを構成する

<!--- $Rule3 = New-AzureRmFirewallNetworkRule -Name "AllowPing" -Protocol ICMP -SourceAddress $SNOnpremPrefix `
   -DestinationAddress $VNetSpokePrefix -DestinationPort *--->

```azurepowershell
$Rule1 = New-AzureRmFirewallNetworkRule -Name "AllowWeb" -Protocol TCP -SourceAddress $SNOnpremPrefix `
   -DestinationAddress $VNetSpokePrefix -DestinationPort 80

$Rule2 = New-AzureRmFirewallNetworkRule -Name "AllowRDP" -Protocol TCP -SourceAddress $SNOnpremPrefix `
   -DestinationAddress $VNetSpokePrefix -DestinationPort 3389

$NetRuleCollection = New-AzureRmFirewallNetworkRuleCollection -Name RCNet01 -Priority 100 `
   -Rule $Rule1,$Rule2 -ActionType "Allow"
$Azfw.NetworkRuleCollections = $NetRuleCollection
Set-AzureRmFirewall -AzureFirewall $Azfw
```

## <a name="create-and-connect-the-vpn-gateways"></a>VPN ゲートウェイを作成して接続する

ハブと OnPrem VNet は、VPN ゲートウェイ経由で接続されます。

### <a name="create-a-vpn-gateway-for-the-hub-vnet"></a>ハブ VNet の VPN ゲートウェイを作成する

VPN ゲートウェイ構成を作成します。 VPN ゲートウェイの構成で、使用するサブネットとパブリック IP アドレスを定義します。

  ```azurepowershell
  $vnet1 = Get-AzureRmVirtualNetwork -Name $VNetnameHub -ResourceGroupName $RG1
  $subnet1 = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
  $gwipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfNameHub `
  -Subnet $subnet1 -PublicIpAddress $gwpip1
  ```

ここで、ハブ VNet の VPN ゲートウェイを作成します。 VNet 間構成では、RouteBased VpnType が必要です。 選択した VPN ゲートウェイ SKU によっては、VPN ゲートウェイの作成に 45 分以上かかる場合も少なくありません。

```azurepowershell
New-AzureRmVirtualNetworkGateway -Name $GWHubName -ResourceGroupName $RG1 `
-Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn `
-VpnType RouteBased -GatewaySku basic
```

### <a name="create-a-vpn-gateway-for-the-onprem-vnet"></a>OnPrem VNet の VPN ゲートウェイを作成する

VPN ゲートウェイ構成を作成します。 VPN ゲートウェイの構成で、使用するサブネットとパブリック IP アドレスを定義します。

  ```azurepowershell
$vnet2 = Get-AzureRmVirtualNetwork -Name $VNetnameOnprem -ResourceGroupName $RG1
$subnet2 = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
$gwipconf2 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfNameOnprem `
  -Subnet $subnet2 -PublicIpAddress $gwOnprempip
  ```

ここで、OnPrem VNet の VPN ゲートウェイを作成します。 VNet 間構成では、RouteBased VpnType が必要です。 選択した VPN ゲートウェイ SKU によっては、VPN ゲートウェイの作成に 45 分以上かかる場合も少なくありません。

```azurepowershell
New-AzureRmVirtualNetworkGateway -Name $GWOnpremName -ResourceGroupName $RG1 `
-Location $Location1 -IpConfigurations $gwipconf2 -GatewayType Vpn `
-VpnType RouteBased -GatewaySku basic
```

### <a name="create-the-vpn-connections"></a>VPN 接続を作成する

ここで、ハブと OnPrem ゲートウェイの間の VPN 接続を作成できます。

#### <a name="get-the-vpn-gateways"></a>VPN ゲートウェイを取得する

```azurepowershell
$vnetHubgw = Get-AzureRmVirtualNetworkGateway -Name $GWHubName -ResourceGroupName $RG1
$vnetOnpremgw = Get-AzureRmVirtualNetworkGateway -Name $GWOnpremName -ResourceGroupName $RG1
```

#### <a name="create-the-connections"></a>接続を作成する

この手順では、ハブ VNet から OnPrem VNet への接続を作成します。 この例では、参照される共有キーが表示されます。 共有キーには独自の値を使用することができます。 両方の接続の共有キーが一致することが重要です。 接続の作成完了までしばらくかかります。

```azurepowershell
New-AzureRmVirtualNetworkGatewayConnection -Name $ConnectionNameHub -ResourceGroupName $RG1 `
-VirtualNetworkGateway1 $vnetHubgw -VirtualNetworkGateway2 $vnetOnpremgw -Location $Location1 `
-ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
```
OnPrem からハブ VNet への接続を作成します。 この手順は前の手順と似ていますが、Vnet-Onprem から VNet-Hub への接続を作成する点が異なります。 共有キーが一致することを確認してください。 数分後に接続が確立されます。

  ```azurepowershell
  New-AzureRmVirtualNetworkGatewayConnection -Name $ConnectionNameOnprem -ResourceGroupName $RG1 `
  -VirtualNetworkGateway1 $vnetOnpremgw -VirtualNetworkGateway2 $vnetHubgw -Location $Location1 `
  -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
  ```

#### <a name="verify-the-connection"></a>接続を確認する

*Get-AzureRmVirtualNetworkGatewayConnection*コマンドレットを使用して、接続が成功したことを確認できます。*-Debug*は指定しても指定しなくてもかまいません。 次のコマンドレットを使用します。値は実際の値に置き換えてください。 プロンプトが表示されたら、**A** を選択して **All** を実行します。 この例では、テストする接続の名前が *-Name* で示されています。

```azurepowershell
Get-AzureRmVirtualNetworkGatewayConnection -Name $ConnectionNameHub -ResourceGroupName $RG1
```

コマンドレットの完了後、値を確認します。 以下の例では、接続状態は *Connected* と表示され、受信バイトと送信バイトを確認できます。

```
"connectionStatus": "Connected",
"ingressBytesTransferred": 33509044,
"egressBytesTransferred": 4142431
```

## <a name="peer-the-hub-and-spoke-vnets"></a>ハブとスポーク VNet をピアリングする

ここで、スポークとハブ VNet をピアリングします。

```azurepowershell
# Peer hub to spoke
Add-AzureRmVirtualNetworkPeering -Name HubtoSpoke -VirtualNetwork $VNetHub -RemoteVirtualNetworkId $VNetSpoke.Id -AllowGatewayTransit

# Peer spoke to hub
Add-AzureRmVirtualNetworkPeering -Name SpoketoHub -VirtualNetwork $VNetSpoke -RemoteVirtualNetworkId $VNetHub.Id -AllowForwardedTraffic -UseRemoteGateways
```

## <a name="create-routes"></a>ルートを作成する

次に、2 つのルートを作成します。

- ファイアウォール ハブ IP アドレスを介したハブ ゲートウェイ サブネットからスポーク サブネットへのルート
- ファイアウォール ハブ IP アドレスを介したスポーク サブネットからの既定ルート

> [!NOTE]
> Azure Firewall は、BGP を使用してオンプレミス ネットワークを学習します。 これには、インターネット トラフィックをオンプレミス ネットワーク経由で戻す既定のルートが含まれている場合があります。 代わりに、インターネット トラフィックをファイアウォールからインターネットに直接送信する場合は、次のホップ タイプ **インターネット**を伴う AzureFirewallSubnet にユーザー定義の既定のルート (0.0.0.0/0) を追加します。 オンプレミスの宛先トラフィックは、BGP で学習されたより多くの特定ルートを使用して、これまでと同じように、VPN/ExpressRoute ゲートウェイ経由で強制的にトンネリングされます。

```azurepowershell
#Create a route table
$routeTableHubSpoke = New-AzureRmRouteTable `
  -Name 'UDR-Hub-Spoke' `
  -ResourceGroupName $RG1 `
  -location $Location1

#Create a route
Get-AzureRmRouteTable `
  -ResourceGroupName $RG1 `
  -Name UDR-Hub-Spoke `
  | Add-AzureRmRouteConfig `
  -Name "ToSpoke" `
  -AddressPrefix $VNetSpokePrefix `
  -NextHopType "VirtualAppliance" `
  -NextHopIpAddress $AzfwPrivateIP `
 | Set-AzureRmRouteTable

#Associate the route table to the subnet

Set-AzureRmVirtualNetworkSubnetConfig `
  -VirtualNetwork $VNetHub `
  -Name $SNnameGW `
  -AddressPrefix $SNGWHubPrefix `
  -RouteTable $routeTableHubSpoke | `
Set-AzureRmVirtualNetwork

#Now create the default route

#Create a table, with BGP route propagation disabled
$routeTableSpokeDG = New-AzureRmRouteTable `
  -Name 'UDR-DG' `
  -ResourceGroupName $RG1 `
  -location $Location1 `
  -DisableBgpRoutePropagation

#Create a route
Get-AzureRmRouteTable `
  -ResourceGroupName $RG1 `
  -Name UDR-DG `
  | Add-AzureRmRouteConfig `
  -Name "ToSpoke" `
  -AddressPrefix 0.0.0.0/0 `
  -NextHopType "VirtualAppliance" `
  -NextHopIpAddress $AzfwPrivateIP `
 | Set-AzureRmRouteTable

#Associate the route table to the subnet

Set-AzureRmVirtualNetworkSubnetConfig `
  -VirtualNetwork $VNetSpoke `
  -Name $SNnameSpoke `
  -AddressPrefix $SNSpokePrefix `
  -RouteTable $routeTableSpokeDG | `
Set-AzureRmVirtualNetwork
```

## <a name="create-virtual-machines"></a>仮想マシンを作成する

次に、スポーク ワークロードと OnPrem の仮想マシンを作成し、適切なサブネットに配置します。

### <a name="create-the-workload-virtual-machine"></a>ワークロード仮想マシンを作成する

スポーク VNet に仮想マシンを作成し、IIS を実行し、パブリック IP アドレスは使用せず、ping を許可します。
プロンプトが表示されたら、仮想マシンの HTTPS ユーザー名とパスワードを入力します。

```azurepowershell
# Create an inbound network security group rule for ports 3389 and 80
$nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig -Name Allow-RDP  -Protocol Tcp `
  -Direction Inbound -Priority 200 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix $SNSpokePrefix -DestinationPortRange 3389 -Access Allow
$nsgRuleWeb = New-AzureRmNetworkSecurityRuleConfig -Name Allow-web  -Protocol Tcp `
  -Direction Inbound -Priority 202 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix $SNSpokePrefix -DestinationPortRange 80 -Access Allow

# Create a network security group
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $RG1 -Location $Location1 -Name NSG-Spoke02 -SecurityRules $nsgRuleRDP,$nsgRuleWeb

#Create the NIC
$NIC = New-AzureRmNetworkInterface -Name spoke-01 -ResourceGroupName $RG1 -Location $Location1 -SubnetId $VnetSpoke.Subnets[0].Id -NetworkSecurityGroupId $nsg.Id

#Define the virtual machine
$VirtualMachine = New-AzureRmVMConfig -VMName VM-Spoke-01 -VMSize "Standard_DS2"
$VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName Spoke-01 -ProvisionVMAgent -EnableAutoUpdate
$VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $NIC.Id
$VirtualMachine = Set-AzureRmVMSourceImage -VM $VirtualMachine -PublisherName 'MicrosoftWindowsServer' -Offer 'WindowsServer' -Skus '2016-Datacenter' -Version latest

#Create the virtual machine
New-AzureRmVM -ResourceGroupName $RG1 -Location $Location1 -VM $VirtualMachine -Verbose

#Install IIS on the VM
Set-AzureRmVMExtension `
    -ResourceGroupName $RG1 `
    -ExtensionName IIS `
    -VMName VM-Spoke-01 `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.4 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server"}' `
    -Location $Location1
```

<!---#Create a host firewall rule to allow ping in
Set-AzureRmVMExtension `
    -ResourceGroupName $RG1 `
    -ExtensionName IIS `
    -VMName VM-Spoke-01 `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.4 `
    -SettingString '{"commandToExecute":"powershell New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4"}' `
    -Location $Location1--->

### <a name="create-the-onprem-virtual-machine"></a>OnPrem 仮想マシンを作成する

これは単純な仮想マシンで、リモート デスクトップを使用してパブリック IP アドレスに接続できます。 そこから、ファイアウォールを介して OnPrem サーバーに接続できます。 プロンプトが表示されたら、仮想マシンの HTTPS ユーザー名とパスワードを入力します。

```azurepowershell
New-AzureRmVm `
    -ResourceGroupName $RG1 `
    -Name "VM-Onprem" `
    -Location $Location1 `
    -VirtualNetworkName $VNetnameOnprem `
    -SubnetName $SNNameOnprem `
    -OpenPorts 3389 `
    -Size "Standard_DS2"
```

## <a name="test-the-firewall"></a>ファイアウォールをテストする

まず、**VM-spoke-01** 仮想マシンのプライベート IP アドレスを取得してメモします。

```azurepowershell
$NIC.IpConfigurations.privateipaddress
```

1. Azure portal から、**VM-Onprem** 仮想マシンに接続します。
<!---2. Open a Windows PowerShell command prompt on **VM-Onprem**, and ping the private IP for **VM-spoke-01**.

   You should get a reply.--->
2. **VM-Onprem** 上で Web ブラウザーを開き、 http://\<VM-spoke-01 private IP\> にアクセスします。

   インターネット インフォメーション サービスの既定ページが表示されます。

3. **VM-Onprem** で、プライベート IP アドレスにある **VM-spoke-01** に対してリモート デスクトップを開きます。

   接続が成功すると、選択したユーザー名とパスワードを使用してサインインできるはずです。

これで、ファイアウォール ルールが動作していることを確認できました。

<!---- You can ping the server on the spoke VNet.--->
- スポーク VNet 上の Web サーバーを閲覧できます。
- RDP を使用してスポーク VNet 上のサーバーに接続できます。

次に、ファイアウォール ネットワーク ルール コレクションの動作を **Deny** に変更して、ファイアウォール ルールが想定どおりに動作することを確認します。 次のスクリプトを実行して、ルール コレクションの動作を **Deny** に変更します。

```azurepowershell
$rcNet = $azfw.GetNetworkRuleCollectionByName("RCNet01")
$rcNet.action.type = "Deny"

Set-AzureRmFirewall -AzureFirewall $azfw
```

ここで、テストを再実行します。 このとき、すべてが失敗するはずです。 既存のリモート デスクトップをすべて閉じてから、変更したルールをテストします。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

ファイアウォール リソースは、次のチュートリアルのために残しておいてもかまいませんが、不要であれば、**FW-Hybrid-Test** リソース グループを削除して、ファイアウォール関連のすべてのリソースを削除してください。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
> * ネットワーク環境を設定する
> * ファイアウォールを構成してデプロイする
> * ルートを作成する
> * 仮想マシンの作成
> * ファイアウォールをテストする

次に、Azure Firewall のログを監視することができます。

> [!div class="nextstepaction"]
> [チュートリアル: Azure Firewall のログを監視する](./tutorial-diagnostics.md)
