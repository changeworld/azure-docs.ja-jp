---
title: P2S VPN ゲートウェイで OpenVPN を有効にする方法
titleSuffix: Azure VPN Gateway
description: ポイント対サイト構成用に VPN ゲートウェイで OpenVPN プロトコルを有効にする方法について説明します。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 02/05/2021
ms.author: cherylmc
ms.openlocfilehash: 056e9a44009f90be23d66c5da005902ccc8ebebf
ms.sourcegitcommit: a5dd9799fa93c175b4644c9fe1509e9f97506cc6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2021
ms.locfileid: "108205433"
---
# <a name="configure-openvpn-for-point-to-site-vpn-gateways"></a>ポイント対サイト VPN Gateway 用に OpenVPN を構成する

この記事では、Azure VPN Gateway で **OpenVPN® プロトコル** を構成する方法について説明します。 ポータルまたは PowerShell の手順のいずれかを使用できます。

## <a name="prerequisites"></a>前提条件

* この記事では、稼働中のポイント対サイト環境が既にあることを前提としています。 ない場合は、次のいずれかの方法を使用して作成します。

  * [ポータル - ポイント対サイトを作成する](vpn-gateway-howto-point-to-site-resource-manager-portal.md)

  * [PowerShell - ポイント対サイトを作成する](vpn-gateway-howto-point-to-site-rm-ps.md)

* VPN ゲートウェイで Basic SKU が使用されていないことを確認します。 Basic SKU は OpenVPN ではサポートされていません。

## <a name="portal"></a>ポータル

1. ポータルで、**仮想ネットワーク ゲートウェイの [ポイント対サイトの構成]** に移動します。
1. **[トンネルの種類]** については、ドロップダウンから **[OpenVPN (SSL)]** を選択します。

   :::image type="content" source="./media/vpn-gateway-howto-openvpn/portal.png" alt-text="ドロップダウンから [OpenVPN (SSL)] を選択":::
1. 変更を保存し、**次のステップ** に進みます。

## <a name="powershell"></a>PowerShell

1. 次の例を使用して、ゲートウェイで OpenVPN を有効にします。

   ```azurepowershell-interactive
   $gw = Get-AzVirtualNetworkGateway -ResourceGroupName $rgname -name $name
   Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientProtocol OpenVPN
   ```
1. **次のステップ** に進みます。

## <a name="next-steps"></a>次のステップ

OpenVPN 用にクライアントを構成する方法については、[OpenVPN クライアントの構成](vpn-gateway-howto-openvpn-clients.md)に関するページを参照してください。

**"OpenVPN" は OpenVPN Inc. の商標です。**
