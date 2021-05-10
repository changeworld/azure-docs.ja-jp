---
title: Azure Virtual WAN 用に OpenVPN クライアントを構成する
description: この記事では、Azure Virtual WAN 向け OpenVPN クライアントを構成するときに役立ちます。 Windows、Mac、iOS、Linux のクライアント構成手順が含まれます。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 04/27/2021
ms.author: cherylmc
ms.openlocfilehash: 91b4a9a69912f7a5980348cc2b9a874673fa0eb2
ms.sourcegitcommit: 62e800ec1306c45e2d8310c40da5873f7945c657
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2021
ms.locfileid: "108163393"
---
# <a name="configure-an-openvpn-client-for-azure-virtual-wan"></a>Azure Virtual WAN 用に OpenVPN クライアントを構成する

この記事では、**OpenVPN &reg; プロトコル** クライアントの構成方法について説明します。 Windows 10 用の Azure VPN クライアントを使用して、OpenVPN プロトコル経由で接続することもできます。 詳しい手順については、[こちら](openvpn-azure-ad-client.md)をご覧ください

## <a name="before-you-begin"></a>開始する前に

ユーザー VPN (ポイント対サイト) 構成を作成します。 [トンネルの種類] では、[OpenVPN] を必ず選択してください。 手順については、[Azure Virtual WAN 用の P2S 構成の作成](virtual-wan-point-to-site-portal.md#p2sconfig)に関する記事を参照してください。

[!INCLUDE [configuration steps](../../includes/vpn-gateway-vwan-config-openvpn-clients.md)]

## <a name="next-steps"></a>次のステップ

ユーザー VPN (ポイント対サイト) の詳細については、[ユーザー VPN 接続の作成](virtual-wan-point-to-site-portal.md)に関する記事を参照してください。

**"OpenVPN" は OpenVPN Inc. の商標です。**
