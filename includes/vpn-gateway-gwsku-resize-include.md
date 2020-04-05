---
title: インクルード ファイル
description: インクルード ファイル
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/04/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 4842c55b2b1fd23f4d6b7996ccf02e7141504836
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "73495745"
---
`Resize-AzVirtualNetworkGateway` PowerShell コマンドレットを使用して、Generation1 または Generation2 SKU をアップグレードまたはダウングレードできます (Basic SKU を除くすべての VpnGw SKU のサイズを変更できます)。 Basic ゲートウェイ SKU を使用している場合は、[代わりに以下の手順を使用](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md#resize)して、ゲートウェイのサイズを変更します。

次の PowerShell サンプルでは、ゲートウェイ SKU のサイズを VpnGw2 に変更しています。

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
Resize-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku VpnGw2
```

Azure Portal でゲートウェイのサイズを変更することもできます。その場合は、ご利用の仮想ネットワーク ゲートウェイの **[構成]** ページに移動して、ドロップダウンから別の SKU を選択します。
