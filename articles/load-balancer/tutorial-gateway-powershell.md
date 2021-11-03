---
title: 'チュートリアル: ゲートウェイ ロード バランサーを作成する - Azure PowerShell'
titleSuffix: Azure Load Balancer
description: このチュートリアルでは、Azure PowerShell を使用して、ゲートウェイ ロード バランサーを作成する方法を学習します。
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: tutorial
ms.date: 11/02/2021
ms.custom: template-tutorial, ignite-fall-2021
ms.openlocfilehash: 97b8b428d71da312c7cfc0ed9da057003ac68ee7
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131092176"
---
# <a name="tutorial-create-a-gateway-load-balancer-using-azure-powershell"></a>チュートリアル: Azure PowerShell を使用してゲートウェイ ロード バランサーを作成する

Azure Load Balancer は、Standard、Basic、および Gateway SKU で構成されます。 Azure Gateway Load Balancer は、ネットワーク仮想アプライアンス (NVA) の透過的な挿入に使用されます。 ハイ パフォーマンスと NVA の高いスケーラビリティを必要とするシナリオには、Azure Gateway Load Balancer を使用します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * プレビュー機能を登録します。
> * 仮想ネットワークを作成します。
> * ネットワーク セキュリティ グループを作成します。
> * ゲートウェイ ロード バランサーを作成します。
> * ロード バランサー フロントエンドをゲートウェイ ロード バランサーにチェーンします。

> [!IMPORTANT]
> Gateway Azure Load Balancer は、現在パブリック プレビューの段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションを持つ Azure アカウント。[アカウントを無料で作成する](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 既存のパブリック Standard SKU Azure Load Balancer。 ロード バランサーの作成の詳細については、 **[Azure PowerShell を使用したパブリック ロード バランサーの作成](quickstart-load-balancer-standard-public-powershell.md)** に関する記事を参照してください。
    - このチュートリアルの目的のため、例の既存のロード バランサーの名前は **myLoadBalancer** です。
- ローカルにインストールされた Azure PowerShell または Azure Cloud Shell

PowerShell をインストールしてローカルで使用する場合、この記事では Azure PowerShell モジュール バージョン 5.4.1 以降が必要になります。 インストールされているバージョンを確認するには、`Get-Module -ListAvailable Az` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-Az-ps)に関するページを参照してください。 PowerShell をローカルで実行している場合、`Connect-AzAccount` を実行して Azure との接続を作成することも必要です。

## <a name="register-preview-feature"></a>プレビュー機能を登録する

ゲートウェイ ロード バランサーのパブリック プレビューの一環として、プロバイダーを Azure サブスクリプションに登録する必要があります。

次のように [Register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) を使用して、**AllowGatewayLoadBalancer** プロバイダー機能を登録します。

```azurepowershell-interactive
Register-AzProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowGatewayLoadBalancer

```

次のように [Register-AzResourceProvider](/powershell/module/az.resources/register-azresourceprovider) を使用して、**Microsoft.Network** リソース プロバイダーを登録します。

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Network

```

## <a name="create-a-resource-group"></a>リソース グループを作成する

Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) を使用して、次のようにリソース グループを作成します。

```azurepowershell-interactive
New-AzResourceGroup -Name 'TutorGwLB-rg' -Location 'eastus'

```

## <a name="create-virtual-network"></a>Create virtual network

ゲートウェイ ロード バランサーのバックエンド プールにあるリソースには、仮想ネットワークが必要です。 [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) を使用して、仮想ネットワークを作成します。 [New-AzBastion](/powershell/module/az.network/new-azbastion) を使用して、仮想ネットワーク内のリソースを安全に管理するための bastion ホストをデプロイします。

```azurepowershell-interactive
## Create backend subnet config ##
$subnet = @{
    Name = 'myBackendSubnet'
    AddressPrefix = '10.1.0.0/24'
}
$subnetConfig = New-AzVirtualNetworkSubnetConfig @subnet 

## Create Azure Bastion subnet. ##
$bastsubnet = @{
    Name = 'AzureBastionSubnet' 
    AddressPrefix = '10.1.1.0/24'
}
$bastsubnetConfig = New-AzVirtualNetworkSubnetConfig @bastsubnet

## Create the virtual network ##
$net = @{
    Name = 'myVNet'
    ResourceGroupName = 'TutorGwLB-rg'
    Location = 'eastus'
    AddressPrefix = '10.1.0.0/16'
    Subnet = $subnetConfig,$bastsubnetConfig
}
$vnet = New-AzVirtualNetwork @net

## Create public IP address for bastion host. ##
$ip = @{
    Name = 'myBastionIP'
    ResourceGroupName = 'TutorGwLB-rg'
    Location = 'eastus'
    Sku = 'Standard'
    AllocationMethod = 'Static'
}
$publicip = New-AzPublicIpAddress @ip

## Create bastion host ##
$bastion = @{
    ResourceGroupName = 'TutorGwLB-rg'
    Name = 'myBastion'
    PublicIpAddress = $publicip
    VirtualNetwork = $vnet
}
New-AzBastion @bastion -AsJob

```

## <a name="create-nsg"></a>Create NSG

次の例を使用して、ネットワーク セキュリティ グループを作成します。 前に作成した仮想ネットワーク内のネットワーク トラフィックに、必要な NSG ルールを構成します。

[New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) を使用して、NSG のルールを作成します。 [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) を使用して NSG を作成します。

```azurepowershell-interactive
## Create rule for network security group and place in variable. ##
$nsgrule1 = @{
    Name = 'myNSGRule-AllowAll'
    Description = 'Allow all'
    Protocol = '*'
    SourcePortRange = '*'
    DestinationPortRange = '*'
    SourceAddressPrefix = '0.0.0.0/0'
    DestinationAddressPrefix = '0.0.0.0/0'
    Access = 'Allow'
    Priority = '100'
    Direction = 'Inbound'
}
$rule1 = New-AzNetworkSecurityRuleConfig @nsgrule1

$nsgrule2 = @{
    Name = 'myNSGRule-AllowAll-TCP-Out'
    Description = 'Allow all TCP Out'
    Protocol = 'TCP'
    SourcePortRange = '*'
    DestinationPortRange = '*'
    SourceAddressPrefix = '0.0.0.0/0'
    DestinationAddressPrefix = '0.0.0.0/0'
    Access = 'Allow'
    Priority = '100'
    Direction = 'Outbound'
}
$rule2 = New-AzNetworkSecurityRuleConfig @nsgrule2

## Create network security group ##
$nsg = @{
    Name = 'myNSG'
    ResourceGroupName = 'TutorGwLB-rg'
    Location = 'eastus'
    SecurityRules = $rule1,$rule2
}
New-AzNetworkSecurityGroup @nsg
```

## <a name="create-gateway-load-balancer"></a>ゲートウェイ ロード バランサーを作成する

このセクションでは、構成を作成し、ゲートウェイ ロード バランサーをデプロイします。 [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig) を使用して、ロード バランサーのフロントエンド IP 構成を作成します。 

[New-AzLoadBalancerTunnelInterface](/powershell/module/az.network/new-azloadbalancerfrontendipconfig) を使用して、ロード バランサーの 2 つのトンネル インターフェイスを作成します。 

NVA では [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) を使用して、バックエンド プールを作成します。 

ロード バランサー内のバックエンド インスタンスの正常性を監視するには、正常性プローブが必要です。 [New-AzLoadBalancerProbeConfig](/powershell/module/az.network/new-azloadbalancerprobeconfig) を使用して正常性プローブを作成します。

バックエンド インスタンス宛てのトラフィックは、負荷分散規則を使用してルーティングされます。 [New-AzLoadBalancerRuleConfig](/powershell/module/az.network/new-azloadbalancerruleconfig) を使用して負荷分散規則を作成します。

ロード バランサーを作成してデプロイするには、[New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer) を使用します。

```azurepowershell-interactive
## Place virtual network configuration in a variable for later use. ##
$net = @{
    Name = 'myVNet'
    ResourceGroupName = 'TutorGwLB-rg'
}
$vnet = Get-AzVirtualNetwork @net

## Create load balancer frontend configuration and place in variable. ## 
$fe = @{
    Name = 'myFrontend'
    SubnetId = $vnet.subnets[0].id
}
$feip = New-AzLoadBalancerFrontendIpConfig @fe

## Create backend address pool configuration and place in variable. ## 
$int1 = @{
    Type = 'Internal'
    Protocol = 'Vxlan'
    Identifier = '800'
    Port = '10800'
}
$tunnelInterface1 = New-AzLoadBalancerBackendAddressPoolTunnelInterfaceConfig @int1

$int2 = @{
    Type = 'External'
    Protocol = 'Vxlan'
    Identifier = '801'
    Port = '10801'
}
$tunnelInterface2 = New-AzLoadBalancerBackendAddressPoolTunnelInterfaceConfig @int2

$pool = @{
    Name = 'myBackendPool'
    TunnelInterface = $tunnelInterface1,$tunnelInterface2
}
$bepool = New-AzLoadBalancerBackendAddressPoolConfig @pool

## Create the health probe and place in variable. ## 
$probe = @{
    Name = 'myHealthProbe'
    Protocol = 'http'
    Port = '80'
    IntervalInSeconds = '360'
    ProbeCount = '5'
    RequestPath = '/'
}
$healthprobe = New-AzLoadBalancerProbeConfig @probe

## Create the load balancer rule and place in variable. ## 
$para = @{
    Name = 'myLBRule'
    Protocol = 'All'
    FrontendPort = '0'
    BackendPort = '0'
    FrontendIpConfiguration = $feip
    BackendAddressPool = $bepool
    Probe = $healthprobe
}
$rule = New-AzLoadBalancerRuleConfig @para

## Create the load balancer resource. ## 
$lb = @{
    ResourceGroupName = 'TutorGwLB-rg'
    Name = 'myLoadBalancer-gw'
    Location = 'eastus'
    Sku = 'Gateway'
    LoadBalancingRule = $rule
    FrontendIpConfiguration = $feip
    BackendAddressPool = $bepool
    Probe = $healthprobe
}
New-AzLoadBalancer @lb

```

## <a name="add-network-virtual-appliances-to-the-gateway-load-balancer-backend-pool"></a>ネットワーク仮想アプライアンスをバックエンド プールの Azure Gateway Load Balancer に追加する
Azure Marketplace を介して NVA をデプロイします。 デプロイが完了したら [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface) を使用してバックエンド プールに仮想マシンを追加します

## <a name="chain-load-balancer-frontend-to-gateway-load-balancer"></a>ロード バランサー フロントエンドをゲートウェイ ロード バランサーにチェーンします

この例では、Standard ロード バランサーのフロントエンドをゲートウェイ ロード バランサーにチェーンします。 

サブスクリプション内の既存のロード バランサーのフロントエンド IP に、フロントエンドを追加します。

[Set-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/set-azloadbalancerfrontendipconfig) を使用して、ゲートウェイ ロード バランサー フロントエンドを既存のロード バランサーにチェーンします。

```azurepowershell-interactive
## Place the gateway load balancer configuration into a variable. ##
$par1 = @{
    ResourceGroupName = 'TutorGwLB-rg'
    Name = 'myLoadBalancer-gw'
}
$gwlb = Get-AzLoadBalancer @par1

## Place the existing load balancer into a variable. ##
$par2 = @{
    ResourceGroupName = 'CreatePubLBQS-rg'
    Name = 'myLoadBalancer'
}
$lb = Get-AzLoadBalancer @par2

## Place the existing public IP for the existing load balancer into a variable.
$par3 = @{
    ResourceGroupName = 'CreatePubLBQS-rg'
    Name = 'myPublicIP'
}
$publicIP = Get-AzPublicIPAddress @par3

## Chain the gateway load balancer to your existing load balancer frontend. ##
$par4 = @{
    Name = 'myFrontEndIP'
    PublicIPAddress = $publicIP
    LoadBalancer = $lb
    GatewayLoadBalancerId = $gwlb.FrontendIpConfigurations.Id
}
$config = Set-AzLoadBalancerFrontendIpConfig @par4

$config | Set-AzLoadBalancer

```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

必要がなくなれば、[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) コマンドを使用して、リソース グループやロード バランサー、その他のリソースを削除できます。

```azurepowershell-interactive
Remove-AzResourceGroup -Name 'TutorGwLB-rg'
```

## <a name="next-steps"></a>次のステップ

Azure でネットワーク仮想アプライアンスを作成します。 

NVA を作成する場合は、このチュートリアルで作成したリソースを選択します。

* 仮想ネットワーク

* Subnet

* ネットワーク セキュリティ グループ

* ゲートウェイ ロード バランサー

次の記事に進んで、リージョン間 Azure Load Balancer の作成方法を学習してください。
> [!div class="nextstepaction"]
> [リージョン間のロード バランサー](tutorial-cross-region-powershell.md)
