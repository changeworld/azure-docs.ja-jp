---
title: Azure VPN Gateway で OpenVPN を構成する方法:PowerShell
description: Azure VPN Gateway 用に OpenVPN を構成する手順
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 05/21/2019
ms.author: cherylmc
ms.openlocfilehash: de8d03467b5e44df1b9069c6db31d496785ff32e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "84983852"
---
# <a name="configure-openvpn-for-azure-point-to-site-vpn-gateway"></a>Azure ポイント対サイト VPN Gateway 用に OpenVPN を構成する

この記事では、Azure VPN Gateway で **OpenVPN® プロトコル**を構成する方法について説明します。 この記事では、稼働中のポイント対サイト環境が既にあることを前提としています。 ない場合は、手順 1 の指示に従ってポイント対サイト VPN を作成します。



## <a name="1-create-a-point-to-site-vpn"></a><a name="vnet"></a>1.ポイント対サイト VPN を作成する

機能しているポイント対サイト環境がまだない場合は、指示に従って作成します。 ネイティブの Azure 証明機関を使用してポイント対サイト VPN ゲートウェイを作成および構成する方法については、[ポイント対サイト VPN の作成](vpn-gateway-howto-point-to-site-resource-manager-portal.md)に関するページを参照してください。 

> [!IMPORTANT]
> Basic SKU は OpenVPN ではサポートされていません。

## <a name="2-enable-openvpn-on-the-gateway"></a><a name="enable"></a>2.ゲートウェイで OpenVPN を有効にする

ゲートウェイで OpenVPN を有効にします。 ゲートウェイがポイント対サイト (IKEv2 または SSTP) 用に既に構成されていることを確認してから、次のコマンドを実行してください。

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -ResourceGroupName $rgname -name $name
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientProtocol OpenVPN
```

## <a name="next-steps"></a>次のステップ

OpenVPN 用にクライアントを構成する方法については、[OpenVPN クライアントの構成](vpn-gateway-howto-openvpn-clients.md)に関するページを参照してください。

**"OpenVPN" は OpenVPN Inc. の商標です。**
