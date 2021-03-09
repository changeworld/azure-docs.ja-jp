---
title: BGP の状態とメトリックの表示
titleSuffix: Azure VPN Gateway
description: トラブルシューティングのために、BGP 関連の重要な情報を表示できます。
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.devlang: powershell
ms.topic: sample
ms.date: 02/22/2021
ms.author: alzam
ms.openlocfilehash: 097568dddd5c8568d4523cdeb05ab0c889c31670
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101742869"
---
# <a name="view-bgp-metrics-and-status-using-powershell"></a>PowerShell を使用して BGP のメトリックと状態を表示する

すべての BGP ピアと状態を表示するには、**Get-AzVirtualNetworkGatewayBGPPeerStatus** を使用します

[!INCLUDE [VPN Gateway PowerShell instructions](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayBgpPeerStatus -ResourceGroupName resourceGroup -VirtualNetworkGatewayName gatewayName

Asn               : 65515
ConnectedDuration : 9.01:04:53.5768637
LocalAddress      : 10.1.0.254
MessagesReceived  : 14893
MessagesSent      : 14900
Neighbor          : 10.0.0.254
RoutesReceived    : 1
State             : Connected
```

BGP を通じてゲートウェイで学習されたすべてのルートを表示するには、**Get-AzVirtualNetworkGatewayLearnedRoute** を使用します。

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayLearnedRoute -ResourceGroupName resourceGroup -VirtualNetworkGatewayname gatewayName

AsPath       :
LocalAddress : 10.1.0.254
Network      : 10.1.0.0/16
NextHop      :
Origin       : Network
SourcePeer   : 10.1.0.254
Weight       : 32768

AsPath       :
LocalAddress : 10.1.0.254
Network      : 10.0.0.254/32
NextHop      :
Origin       : Network
SourcePeer   : 10.1.0.254
Weight       : 32768

AsPath       : 65515
LocalAddress : 10.1.0.254
Network      : 10.0.0.0/16
NextHop      : 10.0.0.254
Origin       : EBgp
SourcePeer   : 10.0.0.254
Weight       : 32768
```

BGP を通じてゲートウェイからピアにアドバタイズされているすべてのルートを表示するには、**Get-AzVirtualNetworkGatewayAdvertisedRoute** を使用します。

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayAdvertisedRoute -VirtualNetworkGatewayName gatewayName -ResourceGroupName resourceGroupName -Peer 10.0.0.254
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

作成したリソースが不要になったら、[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) コマンドを使用してリソース グループを削除します。 コマンドによって、リソース グループと含まれるすべてのリソースが削除されます。

```azurepowershell-interactive
Remove-AzResourceGroup -Name ResourceGroupName
```

## <a name="next-steps"></a>次のステップ

Azure PowerShell モジュールの詳細については、[Azure PowerShell のドキュメント](/powershell/azure/)をご覧ください。
