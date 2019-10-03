---
title: Azure PowerShell を使用して負荷分散規則とアウトバウンド規則を構成する
titlesuffix: Azure Load Balancer
description: この記事では、Azure PowerShell を使用して Standard Load Balancer の負荷分散規則とアウトバウンド規則を構成する方法について説明します。
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: article
ms.date: 09/24/2019
ms.author: allensu
ms.openlocfilehash: 6e85d31e40e35b9d796fc66394a6eb446c7302ad
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/25/2019
ms.locfileid: "71263058"
---
# <a name="configure-load-balancing-and-outbound-rules-in-standard-load-balancer-using-azure-powershell"></a>Azure PowerShell を使用して Standard Load Balancer の負荷分散規則とアウトバウンド規則を構成する

この記事では、Azure PowerShell を使用して Standard Load Balancer のアウトバウンド規則を構成する方法について説明します。  

作業が完了すると、ロード バランサー リソースには 2 つのフロントエンドとそれらに関連付けられた規則 (1 つは受信用、もう 1 つは送信用) が含まれます。  各フロントエンドはパブリック IP アドレスへの参照を持ちます。このシナリオでは、受信と送信に異なるパブリック IP アドレスを使用します。   負荷分散規則では受信負荷分散のみを提供し、アウトバウンド規則では VM に提供される送信 NAT を制御します。  この記事では、受信用と送信用の 2 つの異なるバックエンド プールを使用して、機能を示し、このシナリオに柔軟性を持たせます。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="connect-to-azure-account"></a>Azure アカウントへの接続
[Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) コマンドで Azure サブスクリプションにサインインし、画面上の指示に従います。
    
```azurepowershell-interactive
Connect-AzAccount
```
## <a name="create-resource-group"></a>リソース グループの作成

[New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0) を使用して Azure リソース グループを作成します。 Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。

次の例では、*myresourcegroupoutbound* という名前のリソース グループを *eastus2* に作成します。

```azurepowershell-interactive
New-AzResourceGroup -Name myresourcegroupoutbound -Location eastus
```
## <a name="create-virtual-network"></a>Create virtual network
[New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork?view=azps-2.6.0) と [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig?view=azps-2.6.0) を使用して、*myresourcegroupoutbound* に *mysubnetoutbound* という名前のサブネットがある *myvnetoutbound* という名前の仮想ネットワークを作成します。

```azurepowershell-interactive
$subnet = New-AzVirtualNetworkSubnetConfig -Name mysubnetoutbound -AddressPrefix "192.168.0.0/24"

New-AzVirtualNetwork -Name myvnetoutbound -ResourceGroupName myresourcegroupoutbound -Location eastus -AddressPrefix "192.168.0.0/16" -Subnet $subnet
```

## <a name="create-inbound-public-ip-address"></a>受信パブリック IP アドレスの作成 

インターネット上の Web アプリにアクセスするには、ロード バランサーのパブリック IP アドレスが必要です。 Standard Load Balancer では、Standard パブリック IP アドレスのみがサポートされています。 [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=azps-2.6.0) を使用して、*myresourcegroupoutbound* に *mypublicipinbound* という名前の Standard パブリック IP アドレスを作成します。

```azurepowershell-interactive
$pubIPin = New-AzPublicIpAddress -ResourceGroupName myresourcegroupoutbound -Name mypublicipinbound -AllocationMethod Static -Sku Standard -Location eastus
```

## <a name="create-outbound-public-ip-address"></a>送信パブリック IP アドレスの作成 

[New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=azps-2.6.0) を使用して、ロード バランサーのフロントエンド送信構成の Standard IP アドレスを作成します。

```azurepowershell-interactive
$pubIPout = New-AzPublicIpAddress -ResourceGroupName myresourcegroupoutbound -Name mypublicipoutbound -AllocationMethod Static -Sku Standard -Location eastus
```

## <a name="create-azure-load-balancer"></a>Azure Load Balancer を作成する

このセクションでは、ロード バランサーの以下のコンポーネントを作成および構成する方法について説明します。
  - ロード バランサーの受信ネットワーク トラフィックを受け取るフロントエンド IP。
  - フロントエンド IP から負荷分散されたネットワーク トラフィックが送信されるバックエンド プール。
  - 送信接続用のバックエンド プール。 
  - バックエンド VM インスタンスの正常性を判断する正常性プローブ。
  - VM へのトラフィックの分散方法を定義する、ロード バランサーの受信規則。
  - VM からのトラフィックの分散方法を定義する、ロード バランサーのアウトバウンド規則。

### <a name="create-inbound-frontend-ip"></a>受信フロントエンド IP の作成
[New-AzLoadBalancerFrontendIpConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerfrontendipconfig?view=azps-2.6.0) を使用して、ロード バランサーの送信フロントエンド IP 構成を作成します。これには、パブリック IP アドレス *mypublicipinbound* に関連付けられている *myfrontendinbound* という名前の受信フロントエンド IP 構成が含まれます。

```azurepowershell-interactive
$frontendIPin = New-AzLoadBalancerFrontendIPConfig -Name "myfrontendinbound" -PublicIpAddress $pubIPin
```
### <a name="create-outbound-frontend-ip"></a>送信フロントエンド IP の作成
[New-AzLoadBalancerFrontendIpConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerfrontendipconfig?view=azps-2.6.0) を使用して、ロード バランサーの送信フロントエンド IP 構成を作成します。これには、パブリック IP アドレス *mypublicipoutbound* に関連付けられている *myfrontendoutbound* という名前の送信フロントエンド IP 構成が含まれます。

```azurepowershell-interactive
$frontendIPout = New-AzLoadBalancerFrontendIPConfig -Name "myfrontendoutbound" -PublicIpAddress $pubIPout
```
### <a name="create-inbound-backend-pool"></a>受信バックエンド プールの作成
[New-AzLoadBalancerBackendAddressPoolConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig?view=azps-2.6.0) を使用して、Load Balancer 用に *bepoolinbound* という名前のバックエンド受信プールを作成します。

```azurepowershell-interactive
$bepoolin = New-AzLoadBalancerBackendAddressPoolConfig -Name bepoolinbound
``` 

### <a name="create-outbound-backend-pool"></a>送信バックエンド プールの作成
[New-AzLoadBalancerBackendAddressPoolConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig?view=azps-2.6.0) を使用して、*bepooloutbound* という名前の追加のバックエンド アドレス プールを作成し、VM のプールに対して送信接続を定義します。 送信プールを別に作成すると最大限の柔軟性が提供されますが、このステップを省略し、受信用の *bepoolinbound* だけを使用してもかまいません。  :

```azurepowershell-interactive
$bepoolout = New-AzLoadBalancerBackendAddressPoolConfig -Name bepooloutbound
``` 

### <a name="create-health-probe"></a>正常性プローブの作成

正常性プローブは、すべての仮想マシン インスタンスを確認し、ネットワーク トラフィックを送信できるかどうかを確認します。 プローブのチェックで失敗した仮想マシン インスタンスは、オンラインに戻り、プローブ チェックにより正常と判定されるまで、ロード バランサーから削除されます。 [New-AzLoadBalancerProbeConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerprobeconfig?view=azps-2.6.0) を使用して正常性プローブを作成し、仮想マシンの正常性を監視します。 

```azurepowershell-interactive
$probe = New-AzLoadBalancerProbeConfig -Name http -Protocol "http" -Port 80 -IntervalInSeconds 15 -ProbeCount 2 -RequestPath /
```
### <a name="create-load-balancing-rule"></a>負荷分散規則の作成

ロード バランサー規則では、必要な発信元ポートおよび宛先ポートと共に、受信トラフィック用のフロントエンド IP 構成と、トラフィックを受信するためのバックエンド プールを定義します。 [New-AzLoadBalancerRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerruleconfig?view=azps-2.6.0) を使用してロード バランサー規則 *myinboundlbrule* を作成します。この規則では、フロントエンド プール *myfrontendinbound* のポート 80 をリッスンし、同じポート 80 を使用して、負荷分散されたネットワーク トラフィックをバックエンド アドレス プール *bepoolinbound* に送信します。 

>[!NOTE]
>この負荷分散規則では、 **-DisableOutboundSNAT** パラメーターを指定するため、自動送信 (S)NAT が無効になります。 送信 NAT はアウトバウンド規則によってのみ提供されます。

```azurepowershell-interactive
$inboundRule = New-AzLoadBalancerRuleConfig -Name inboundlbrule -FrontendIPConfiguration $frontendIPin -BackendAddressPool $bepoolin -Probe $probe -Protocol "Tcp" -FrontendPort 80 -BackendPort 80 -IdleTimeoutInMinutes 15 -EnableFloatingIP -LoadDistribution SourceIP -DisableOutboundSNAT
```

### <a name="create-outbound-rule"></a>アウトバウンド規則の作成

アウトバウンド規則では、フロントエンド *myfrontendoutbound* で表されるフロントエンド パブリック IP を定義します。これは、この規則が適用されるすべての送信 NAT トラフィックとバックエンド プールに使用されます。  バックエンド プール *bepool* 内のすべての仮想マシン (NIC IP 構成) の送信ネットワーク変換用のアウトバウンド規則 *myoutboundrule* を作成します。  また、次のコマンドでは、送信アイドル タイムアウトを 4 分から 15 分に変更し、1024 個ではなく 10000 個の SNAT ポートを割り当てます。  詳細については、「[New-AzLoadBalancerOutboundRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalanceroutboundruleconfig?view=azps-2.7.0)」 (新しい AzLoadBalancerOutboundRuleConfig) を確認してください。

```azurepowershell-interactive
 $outboundRule = New-AzLoadBalancerOutBoundRuleConfig -Name outboundrule -FrontendIPConfiguration $frontendIPout -BackendAddressPool $bepoolout -Protocol All -IdleTimeoutInMinutes 15 -AllocatedOutboundPort 10000
```
送信用に別のプールを使用したくない場合は、前のコマンドのアドレス プール引数を変更して、代わりに *$bepoolin* を指定してもかまいません。  結果として得られる構成が柔軟で読みやすいものとなるように、別のプールの使用をお勧めします。

### <a name="create-load-balancer"></a>ロード バランサーの作成

[New-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancer?view=azps-2.6.0) を使用して、受信 IP アドレスを持つ *lb* という名前のロード バランサーを作成します。これには、受信フロントエンド IP 構成と、前の手順で作成したパブリック IP アドレス *mypublicipinbound* に関連付けられているバックエンド プール *bepoolinbound* が含まれます。

```azurepowershell-interactive
New-AzLoadBalancer -Name lb -Sku Standard -ResourceGroupName myresourcegroupoutbound -Location eastus -FrontendIpConfiguration $frontendIPin,$frontendIPout -BackendAddressPool $bepoolin,$bepoolout -Probe $probe -LoadBalancingRule $inboundrule -OutboundRule $outboundrule 
```

この時点で、[Add-AzNetworkInterfaceIpConfig](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest) を使用して、各 NIC リソースの IP 構成を更新することで、バックエンド プール *bepoolinbound* __および__ *bepooloutbound* への VM の追加を継続できます。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

必要がなくなれば、[Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.7.0) コマンドを使用して、リソース グループ、ロード バランサー、すべての関連リソースを削除できます。

```azurepowershell-interactive 
  Remove-AzResourceGroup -Name myresourcegroupoutbound
```

## <a name="next-steps"></a>次の手順
この記事では、Standard Load Balancer の作成、ロード バランサーの受信トラフィック規則の構成、バックエンド プール内の VM の正常性プローブの構成を行いました。 Azure Load Balancer の詳細については、Azure Load Balancer のチュートリアルに進みます。

> [!div class="nextstepaction"]
> [Azure Load Balancer のチュートリアル](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
