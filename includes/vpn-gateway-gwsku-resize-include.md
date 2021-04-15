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
ms.openlocfilehash: a3ae2a876d6a3772d941fec0b8a1ea3f537e60c3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96010800"
---
`Resize-AzVirtualNetworkGateway` PowerShell コマンドレットを使用して、Generation1 または Generation2 SKU をアップグレードまたはダウングレードできます (Basic SKU を除くすべての VpnGw SKU のサイズを変更できます)。 Basic ゲートウェイ SKU を使用している場合は、[代わりに以下の手順を使用](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md#resize)して、ゲートウェイのサイズを変更します。

次の PowerShell サンプルでは、ゲートウェイ SKU のサイズを VpnGw2 に変更しています。

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
Resize-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku VpnGw2
```