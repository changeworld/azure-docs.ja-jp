---
title: P2S VPN ゲートウェイで OpenVPN を有効にする方法
titleSuffix: Azure VPN Gateway
description: ポイント対サイト構成用に VPN ゲートウェイで OpenVPN プロトコルを有効にする方法について説明します。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 07/28/2021
ms.author: cherylmc
ms.openlocfilehash: ab7826a89dc879c1bad62e8c56415047d164c6f9
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121729493"
---
# <a name="configure-openvpn-for-point-to-site-vpn-gateways"></a>ポイント対サイト VPN Gateway 用に OpenVPN を構成する

この記事では、Azure VPN Gateway で **OpenVPN® プロトコル** を構成する方法について説明します。 ポータルまたは PowerShell の手順のいずれかを使用できます。

## <a name="prerequisites"></a>前提条件

* この記事では、稼働中のポイント対サイト環境が既にあることを前提としています。 ない場合は、次のいずれかの方法を使用して作成します。 ゲートウェイを作成するときは、**Basic** SKU で OpenVPN トンネルの種類がサポートされていないことに注意してください。

  * [ポータル - ポイント対サイトを作成する](vpn-gateway-howto-point-to-site-resource-manager-portal.md)

  * [PowerShell - ポイント対サイトを作成する](vpn-gateway-howto-point-to-site-rm-ps.md)

* VPN ゲートウェイが既にある場合は、それで **Basic** SKU が使用されていないことを確認してください。 Basic SKU は OpenVPN ではサポートされていません。 SKU の詳細については、[VPN Gateway の構成設定](vpn-gateway-about-vpn-gateway-settings.md)に関する記事を参照してください。 Basic SKU のサイズを変更するには、[レガシ ゲートウェイのサイズ変更](vpn-gateway-about-skus-legacy.md#resource-manager)に関する記事を参照してください。 

## <a name="portal"></a>ポータル

1. ポータルで、**仮想ネットワーク ゲートウェイの [ポイント対サイトの構成]** に移動します。
1. **[トンネルの種類]** については、ドロップダウンから **[OpenVPN (SSL)]** を選択します。

   :::image type="content" source="./media/vpn-gateway-howto-openvpn/portal.png" alt-text="ドロップダウンから [OpenVPN (SSL)] を選択":::
1. 変更を保存し、**次のステップ** に進みます。

## <a name="powershell"></a>PowerShell

1. 次の例を使用してゲートウェイで OpenVPN を有効にし、必要に応じて値を調整します。

   ```azurepowershell-interactive
   $gw = Get-AzVirtualNetworkGateway -ResourceGroupName TestRG1 -name VNet1GW
   Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientProtocol OpenVPN
   ```
1. **次のステップ** に進みます。

## <a name="next-steps"></a>次のステップ

OpenVPN 用にクライアントを構成する方法については、[OpenVPN クライアントの構成](vpn-gateway-howto-openvpn-clients.md)に関するページを参照してください。

**"OpenVPN" は OpenVPN Inc. の商標です。**
