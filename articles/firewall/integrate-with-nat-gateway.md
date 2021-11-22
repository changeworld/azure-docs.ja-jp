---
title: Azure NAT ゲートウェイを使用した SNAT ポートのスケーリング
description: Azure Firewall を NAT ゲートウェイと統合して、SNAT ポートを増やすことができます。
services: firewall
author: KumudD
ms.service: firewall
ms.topic: how-to
ms.date: 04/23/2021
ms.author: kumud
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 30d1da573bec205212658fa6aff5ac3b71ad2e50
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2021
ms.locfileid: "132399012"
---
# <a name="scale-snat-ports-with-azure-nat-gateway"></a>Azure NAT ゲートウェイを使用した SNAT ポートのスケーリング

Azure Firewall では、構成されているパブリック IP アドレスごとに 2048 個の SNAT ポートが提供され、最大で [250 個のパブリック IP アドレス](./deploy-multi-public-ip-powershell.md)を関連付けることができます。 アーキテクチャとトラフィック パターンによっては、この構成で 512,000 を超える SNAT ポートが必要になる場合があります。 たとえば、Microsoft 365 アプリと統合する大規模な [Windows Virtual Desktop デプロイ](./protect-azure-virtual-desktop.md)を保護するために、これを使用する場合などです。

多数のパブリック IP アドレスを使用する場合のもう 1 つの課題は、ダウンストリームの IP アドレス フィルター要件がある場合です。 Azure Firewall では、接続に使用されるソース パブリック IP アドレスがランダムに選択されるため、それに関連付けられているすべてのパブリック IP アドレスを許可する必要があります。 送信 SNAT ポートの要件を満たすために、[パブリック IP アドレス プレフィックス](../virtual-network/ip-services/public-ip-address-prefix.md)を使用し、250 個のパブリック IP アドレスを関連付ける必要がある場合でも、16 個のパブリック IP アドレス プレフィックスを作成して許可する必要があります。

送信 SNAT ポートをスケーリングするためのより適切なオプションは、[NAT ゲートウェイ リソース](../virtual-network/nat-gateway/nat-overview.md)を使用することです。 これにより、パブリック IP アドレスごとに 64,000 個の SNAT ポートが提供され、最大 16 個のパブリック IP アドレスがサポートされます。したがって、最大 1,024,000 個の送信 SNAT ポートを効果的に提供できます。

NAT ゲートウェイ リソースが Azure Firewall サブネットに関連付けられている場合、すべての送信インターネット トラフィックで、NAT ゲートウェイのパブリック IP アドレスが自動的に使用されます。 [ユーザー定義のルート](../virtual-network/tutorial-create-route-table-portal.md)を構成する必要はありません。 応答トラフィックでは、Azure Firewall のパブリック IP アドレスを使用してフロー対称が維持されます。 NAT ゲートウェイに複数の IP アドレスが関連付けられている場合は、IP アドレスがランダムに選択されます。 使用するアドレスを指定することはできません。

このアーキテクチャでは、二重 NAT はありません。 Azure Firewall インスタンスは、Azure Firewall のパブリック IP アドレスではなく、プライベート IP アドレスを使用して、NAT ゲートウェイにトラフィックを送信します。

> [!NOTE]
> 現時点では、[Azure Firewall を複数の可用性ゾーン](deploy-availability-zone-powershell.md)にデプロイしている場合、Azure Firewall で Azure NAT Gateway を使用することができません。

## <a name="associate-nat-gateway-with-azure-firewall-subnet---azure-powershell"></a>NAT ゲートウェイを Azure Firewall サブネットに関連付ける - Azure PowerShell

次の例では、Azure PowerShell を使用して、NAT ゲートウェイを作成し、Azure Firewall サブネットにアタッチしています。

```azurepowershell-interactive
# Create public IP addresses
New-AzPublicIpAddress -Name public-ip-1 -ResourceGroupName nat-rg -Sku Standard -AllocationMethod Static -Location 'South Central US'
New-AzPublicIpAddress -Name public-ip-2 -ResourceGroupName nat-rg -Sku Standard -AllocationMethod Static -Location 'South Central US'

# Create NAT gateway
$PublicIPAddress1 = Get-AzPublicIpAddress -Name public-ip-1 -ResourceGroupName nat-rg
$PublicIPAddress2 = Get-AzPublicIpAddress -Name public-ip-2 -ResourceGroupName nat-rg
New-AzNatGateway -Name firewall-nat -ResourceGroupName nat-rg -PublicIpAddress $PublicIPAddress1,$PublicIPAddress2 -Location 'South Central US' -Sku Standard

# Associate NAT gateway to subnet
$virtualNetwork = Get-AzVirtualNetwork -Name nat-vnet -ResourceGroupName nat-rg
$natGateway = Get-AzNatGateway -Name firewall-nat -ResourceGroupName nat-rg
$firewallSubnet = $virtualNetwork.subnets | Where-Object -Property Name -eq AzureFirewallSubnet
$firewallSubnet.NatGateway = $natGateway
$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="associate-nat-gateway-with-azure-firewall-subnet---azure-cli"></a>NAT ゲートウェイを Azure Firewall サブネットに関連付ける - Azure CLI

次の例では、Azure CLI を使用して、NAT ゲートウェイを作成し、Azure Firewall サブネットにアタッチしています。

```azurecli-interactive
# Create public IP addresses
az network public-ip create --name public-ip-1 --resource-group nat-rg --sku standard
az network public-ip create --name public-ip-2 --resource-group nat-rg --sku standard

# Create NAT gateway
az network nat gateway create --name firewall-nat --resource-group nat-rg --public-ip-addresses public-ip-1 public-ip-2

# Associate NAT gateway to subnet
az network vnet subnet update --name AzureFirewallSubnet --vnet-name nat-vnet --resource-group nat-rg --nat-gateway firewall-nat
```

## <a name="next-steps"></a>次のステップ

- [NAT ゲートウェイ リソースを使用した仮想ネットワークの設計](../virtual-network/nat-gateway/nat-gateway-resource.md)