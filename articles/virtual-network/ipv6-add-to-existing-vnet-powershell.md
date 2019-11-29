---
title: Azure 仮想ネットワーク内の IPv4 アプリケーションを IPv6 にアップグレードする - PowerShell
titlesuffix: Azure Virtual Network
description: この記事では、Azure PowerShell を使用して Azure 仮想ネットワーク内の既存のアプリケーションに IPv6 アドレスをデプロイする方法について示します。
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/21/2019
ms.author: kumud
ms.openlocfilehash: 907a6de2ff89ddd3c2cb5bdab67e1deb984141dc
ms.sourcegitcommit: c4700ac4ddbb0ecc2f10a6119a4631b13c6f946a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/27/2019
ms.locfileid: "72965232"
---
# <a name="upgrade-an-ipv4-application-to-ipv6-in-azure-virtual-network---powershell-preview"></a>Azure 仮想ネットワーク内の IPv4 アプリケーションを IPv6 にアップグレードする - PowerShell (プレビュー)

この記事では、Standard Load Balancer とパブリック IP を使用して、Azure 仮想ネットワーク内の既存の IPv4 アプリケーションに IPv6 接続を追加する方法について説明します。 インプレース アップグレードには次のものが含まれます。
- 仮想ネットワークとサブネットの IPv6 アドレス空間
- IPv4 と IPV6 の両方のフロントエンド構成の Standard Load Balancer
- IPv4 + IPv6 の両方が構成された NIC を使用する VM
- ロード バランサーがインターネットに直接接続する IPv6 接続を持つ IPv のパブリック IP

> [!Important]
> 現在、Azure Virtual Network の IPv6 サポートは、パブリック プレビュー段階です。 このプレビュー版はサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」をご覧ください。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

PowerShell をローカルにインストールして使用することを選択する場合、この記事では Azure PowerShell モジュール バージョン 6.9.0 以降が必要になります。 インストールされているバージョンを確認するには、`Get-Module -ListAvailable Az` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-Az-ps)に関するページを参照してください。 PowerShell をローカルで実行している場合、`Connect-AzAccount` を実行して Azure との接続を作成することも必要です。

## <a name="prerequisites"></a>前提条件

### <a name="register-the-service"></a>サービスを登録する

Azure でデュアル スタック アプリケーションをデプロイする前に、次の Azure PowerShell を使用して、このプレビュー機能に対してサブスクリプションを構成する必要があります。

次のように登録します。
```azurepowershell
Register-AzProviderFeature -FeatureName AllowIPv6VirtualNetwork -ProviderNamespace Microsoft.Network
Register-AzProviderFeature -FeatureName AllowIPv6CAOnStandardLB -ProviderNamespace Microsoft.Network
```
機能の登録が完了するまで、最長で 30 分かかります。 次の Azure PowerShell コマンドを実行することで、登録状態を確認できます。次のように登録を確認します。
```azurepowershell
Get-AzProviderFeature -FeatureName AllowIPv6VirtualNetwork -ProviderNamespace Microsoft.Network
Get-AzProviderFeature -FeatureName AllowIPv6CAOnStandardLB -ProviderNamespace Microsoft.Network
```
登録が完了した後、次のコマンドを実行します。

```azurepowershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Network
```

### <a name="create-a-standard-load-balancer"></a>Standard Load Balancer を作成する
この記事では、[クイック スタート: Standard Load Balancer を作成する - Azure PowerShell](../load-balancer/quickstart-create-standard-load-balancer-powershell.md) に関するページに説明されているように、Standard Load Balancer をデプロイしたことを前提としています。

## <a name="retrieve-the-resource-group"></a>リソース グループの取得

デュアル スタック仮想ネットワークを作成する前に、[Get-AzResourceGroup](/powershell/module/az.resources/get-azresourcegroup) でリソース グループを取得する必要があります。

```azurepowershell
 $rg = Get-AzResourceGroup  -ResourceGroupName "myResourceGroupSLB"
```

## <a name="create-an-ipv6-ip-addresses"></a>IPv6 IP アドレスの作成

Standard Load Balancer の [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) を使用して、パブリック IPv6 アドレスを作成します。 次の例では、*PublicIP_v6* という名前の IPv6 パブリック IP アドレスを *myResourceGroupSLB* リソース グループに作成します。

```azurepowershell
  
  $PublicIP_v6 = New-AzPublicIpAddress `
  -Name "PublicIP_v6" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Sku Standard  `
  -AllocationMethod Static `
  -IpAddressVersion IPv6
```

## <a name="configure-load-balancer-frontend"></a>ロード バランサー フロントエンドの構成

既存のロード バランサー構成を取得し、次のように [Add-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/Add-AzLoadBalancerFrontendIpConfig) を使用して新しい IPv6 IP アドレスを追加します。

```azurepowershell
# Retrieve the load balancer configuration
$lb = Get-AzLoadBalancer -ResourceGroupName $rg.ResourceGroupName -Name "MyLoadBalancer"
# Add IPv6 components to the local copy of the load balancer configuration
$lb | Add-AzLoadBalancerFrontendIpConfig `
  -Name "dsLbFrontEnd_v6" `
  -PublicIpAddress $PublicIP_v6
#Update the running load balancer with the new frontend
$lb | Set-AzLoadBalancer
```

## <a name="configure-load-balancer-backend-pool"></a>ロード バランサーのバックエンド プールの構成

ロード バランサー構成のローカル コピーにバックエンド プールを作成し、次のように新しいバックエンド プール構成を使用して実行中のロード バランサーを更新します。

```azurepowershell
$lb | Add-AzLoadBalancerBackendAddressPoolConfig -Name "LbBackEndPool_v6"
# Update the running load balancer with the new backend pool
$lb | Set-AzLoadBalancer
```

## <a name="configure-load-balancer-rules"></a>ロード バランサー規則の構成
既存のロード バランサー フロントエンドとバックエンド プールの構成を取得し、[Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/Add-AzLoadBalancerRuleConfig) を使用して新しい負荷分散規則を追加します。

```azurepowershell
# Retrieve the updated (live) versions of the frontend and backend pool
$frontendIPv6 = Get-AzLoadBalancerFrontendIpConfig -Name "dsLbFrontEnd_v6" -LoadBalancer $lb
$backendPoolv6 = Get-AzLoadBalancerBackendAddressPoolConfig -Name "LbBackEndPool_v6" -LoadBalancer $lb
# Create new LB rule with the frontend and backend
$lb | Add-AzLoadBalancerRuleConfig `
  -Name "dsLBrule_v6" `
  -FrontendIpConfiguration $frontendIPv6 `
  -BackendAddressPool $backendPoolv6 `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80
#Finalize all the load balancer updates on the running load balancer
$lb | Set-AzLoadBalancer
```
## <a name="add-ipv6-address-ranges"></a>IPv6 アドレス範囲の追加

次のように、VM をホストする仮想ネットワークとサブネットに IPv6 アドレス範囲を追加します。

```azurepowershell
#Add IPv6 ranges to the VNET and subnet
#Retreive the VNET object
$vnet = Get-AzVirtualNetwork  -ResourceGroupName $rg.ResourceGroupName -Name "myVnet" 
#Add IPv6 prefix to the VNET
$vnet.addressspace.addressprefixes.add("ace:cab:deca::/48")
#Update the running VNET
$vnet |  Set-AzVirtualNetwork

#Retrieve the subnet object from the local copy of the VNET
$subnet= $vnet.subnets[0]
#Add IPv6 prefix to the Subnet (subnet of the VNET prefix, of course)
$subnet.addressprefix.add("ace:cab:deca::/64")
#Update the running VNET with the new subnet configuration
$vnet |  Set-AzVirtualNetwork

```
## <a name="add-ipv6-configuration-to-nic"></a>NIC への IPv6 構成の追加

次のように [Add-AzNetworkInterfaceIpConfig](/powershell/module/az.network/Add-AzNetworkInterfaceIpConfig) を使用して、すべての VM NIC を IPv6 アドレスで構成します。

```azurepowershell

#Retrieve the NIC objects
$NIC_1 = Get-AzNetworkInterface -Name "myNic1" -ResourceGroupName $rg.ResourceGroupName
$NIC_2 = Get-AzNetworkInterface -Name "myNic2" -ResourceGroupName $rg.ResourceGroupName
$NIC_3 = Get-AzNetworkInterface -Name "myNic3" -ResourceGroupName $rg.ResourceGroupName
#Add an IPv6 IPconfig to NIC_1 and update the NIC on the running VM
$NIC_1 | Add-AzNetworkInterfaceIpConfig -Name MyIPv6Config -Subnet $vnet.Subnets[0]  -PrivateIpAddressVersion IPv6 -LoadBalancerBackendAddressPool $backendPoolv6 
$NIC_1 | Set-AzNetworkInterface
#Add an IPv6 IPconfig to NIC_2 and update the NIC on the running VM
$NIC_2 | Add-AzNetworkInterfaceIpConfig -Name MyIPv6Config -Subnet $vnet.Subnets[0]  -PrivateIpAddressVersion IPv6 -LoadBalancerBackendAddressPool $backendPoolv6 
$NIC_2 | Set-AzNetworkInterface
#Add an IPv6 IPconfig to NIC_3 and update the NIC on the running VM
$NIC_3 | Add-AzNetworkInterfaceIpConfig -Name MyIPv6Config -Subnet $vnet.Subnets[0]  -PrivateIpAddressVersion IPv6 -LoadBalancerBackendAddressPool $backendPoolv6 
$NIC_3 | Set-AzNetworkInterface

```

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>Azure portal で IPv6 デュアル スタック仮想ネットワークを表示する
次のようにして、Azure portal で IPv6 デュアル スタック仮想ネットワークを表示することができます。
1. ポータルの検索バーに、「*myVnet*」と入力します。
2. 検索結果に **myVnet** が表示されたら、それを選択します。 これにより、*myVNet* という名前のデュアル スタック仮想ネットワークの **[概要]** ページが起動します。 デュアル スタック仮想ネットワークには、*mySubnet* という名前のデュアル スタック サブネットにある、IPv4 と IPv6 の両方の構成を持つ 3 つの NIC が表示されます。

  ![Azure の IPv6 デュアル スタック仮想ネットワーク](./media/ipv6-add-to-existing-vnet-powershell/ipv6-dual-stack-vnet.png)

> [!NOTE]
> このプレビュー リリースの場合、Azure 仮想ネットワークの IPv6 は、Azure portal で読み取り専用で使用できます。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

必要がなくなったら、[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) コマンドを使用して、リソース グループ、VM、およびすべての関連リソースを削除できます。

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyAzureResourceGroupSLB
```

## <a name="next-steps"></a>次の手順

この記事では、IPv4 フロントエンド IP 構成を持つ既存の Standard Load Balancer をデュアル スタック (IPv4 および IPv6) 構成に更新しました。 また、IPv6 構成をバックエンド プール内の VM の NIC と、それらをホストする仮想ネットワークに追加しました。 Azure 仮想ネットワークでの IPv6 サポートの詳細については、[Azure Virtual Network の IPv6 の概要](ipv6-overview.md)に関するページを参照してください
