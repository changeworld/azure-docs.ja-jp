---
title: インクルード ファイル
description: インクルード ファイル
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: c06e69dd9d1997500589659e936dc25ee01ed145
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/20/2018
---
現在の SKU (VpnGw1、VpnGw2、VPNGW3) について、ゲートウェイ SKU のサイズを変更して、より強力なものにアップグレードする場合は、`Resize-AzureRmVirtualNetworkGateway` PowerShell コマンドレットを使用できます。 このコマンドレットを使用してゲートウェイ SKU のサイズをダウングレードすることもできます。 Basic ゲートウェイ SKU を使用している場合は、[代わりに以下の手順を使用](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md#resize)して、ゲートウェイのサイズを変更します。

次の PowerShell サンプルでは、ゲートウェイ SKU のサイズを VpnGw2 に変更しています。

```powershell
$gw = Get-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
Resize-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku VpnGw2
```

Azure Portal でゲートウェイのサイズを変更することもできます。その場合は、ご利用の仮想ネットワーク ゲートウェイの **[構成]** ページに移動して、ドロップダウンから別の SKU を選択します。