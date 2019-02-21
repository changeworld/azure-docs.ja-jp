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
ms.openlocfilehash: 081352a23e6a0d8f9e2daa77eca1f8ac85172ff6
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/19/2019
ms.locfileid: "56418230"
---
現在の SKU (VpnGw1、VpnGw2、VPNGW3) について、ゲートウェイ SKU のサイズを変更して、より強力なものにアップグレードする場合は、`Resize-AzVirtualNetworkGateway` PowerShell コマンドレットを使用できます。 このコマンドレットを使用してゲートウェイ SKU のサイズをダウングレードすることもできます。 Basic ゲートウェイ SKU を使用している場合は、[代わりに以下の手順を使用](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md#resize)して、ゲートウェイのサイズを変更します。

次の PowerShell サンプルでは、ゲートウェイ SKU のサイズを VpnGw2 に変更しています。

```powershell
$gw = Get-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
Resize-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku VpnGw2
```

Azure Portal でゲートウェイのサイズを変更することもできます。その場合は、ご利用の仮想ネットワーク ゲートウェイの **[構成]** ページに移動して、ドロップダウンから別の SKU を選択します。