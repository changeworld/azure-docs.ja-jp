---
title: P2S VPN ゲートウェイ用に OpenVPN クライアントを構成する方法
titleSuffix: Azure VPN Gateway
description: Azure VPN Gateway 用に OpenVPN クライアントを構成する方法について説明します。 この記事は、Windows、Linux、iOS、および Mac クライアントを構成する際に役立ちます。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 07/27/2021
ms.author: cherylmc
ms.openlocfilehash: 8c9ddff536c74182e1c13d51dde2900f07fb7470
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121729513"
---
# <a name="configure-openvpn-clients-for-azure-vpn-gateway"></a>Azure VPN Gateway 用に OpenVPN クライアントを構成する

この記事では、**OpenVPN &reg; プロトコル** クライアントの構成方法について説明します。

## <a name="before-you-begin"></a>開始する前に

VPN ゲートウェイ用に OpenVPN を構成する手順を完了していることを確認します。 詳細については、[Azure VPN Gateway 用に OpenVPN を構成する方法](vpn-gateway-howto-openvpn.md)に関するページを参照してください。

## <a name="vpn-client-configuration-files"></a>VPN クライアント構成ファイル

VPN クライアント構成ファイルは、ポータルから、または PowerShell を使用して生成およびダウンロードできます。 どちらの方法でも、同じ zip ファイルが返されます。 そのファイルを解凍して、OpenVPN フォルダーを表示します。

:::image type="content" source="./media/howto-openvpn-clients/download.png" alt-text="[VPN クライアントのダウンロード] が強調表示されているスクリーンショット。" :::

[!INCLUDE [configuration steps](../../includes/vpn-gateway-vwan-config-openvpn-clients.md)]

## <a name="next-steps"></a>次のステップ

VPN クライアントが別の VNet のリソースにアクセスできるようにするには、[VNet 間](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)に関する記事に記載されている手順に従って、VNet 間接続を設定します。 ゲートウェイと接続で BGP を有効にします。そうしないと、トラフィックは流れません。

**"OpenVPN" は OpenVPN Inc. の商標です。**
