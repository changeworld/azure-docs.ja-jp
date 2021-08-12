---
title: Azure Virtual WAN 用に OpenVPN クライアントを構成する
description: Azure Virtual WAN 用に OpenVPN クライアントを構成する方法について説明します。 このアーティクルには、Windows、Mac、iOS、Linux のクライアント構成手順が含まれます。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 04/27/2021
ms.author: cherylmc
ms.openlocfilehash: 83c70aca81eaa888186807d43fff5a7bbaccb700
ms.sourcegitcommit: e1d5abd7b8ded7ff649a7e9a2c1a7b70fdc72440
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/27/2021
ms.locfileid: "110579620"
---
# <a name="configure-an-openvpn-client-for-azure-virtual-wan"></a>Azure Virtual WAN 用に OpenVPN クライアントを構成する

この記事では、**OpenVPN &reg; プロトコル** クライアントの構成方法について説明します。 Windows 10 用の Azure VPN クライアントを使用して、OpenVPN プロトコル経由で接続することもできます。 詳細については、「[Configure a VPN client for P2S OpenVPN connections](openvpn-azure-ad-client.md) (P2S OpenVPN 接続用に VPN クライアントを構成する)」を参照してください。

## <a name="before-you-begin"></a>開始する前に

ユーザー VPN (ポイント対サイト) 構成を作成します。 [トンネルの種類] では、[OpenVPN] を必ず選択してください。 手順については、[Azure Virtual WAN 用の P2S 構成の作成](virtual-wan-point-to-site-portal.md#p2sconfig)に関する記事を参照してください。

[!INCLUDE [configuration steps](../../includes/vpn-gateway-vwan-config-openvpn-clients.md)]

## <a name="next-steps"></a>次のステップ

ユーザー VPN (ポイント対サイト) の詳細については、[ユーザー VPN 接続の作成](virtual-wan-point-to-site-portal.md)に関する記事を参照してください。

**"OpenVPN" は OpenVPN Inc. の商標です。**
