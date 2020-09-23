---
title: Azure VPN Gateway:P2S VPN クライアント プロファイルについて
description: これは、クライアント プロファイル ファイルを操作する際に役立ちます。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 09/03/2020
ms.author: cherylmc
ms.openlocfilehash: 326abaa123ede1fbe371c69fcbed47a310b54511
ms.sourcegitcommit: ac5cbef0706d9910a76e4c0841fdac3ef8ed2e82
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/03/2020
ms.locfileid: "89424839"
---
# <a name="about-p2s-vpn-client-profiles"></a>P2S VPN クライアント プロファイルについて

ダウンロードしたプロファイル ファイルには、VPN 接続の構成に必要な情報が含まれています。 この記事は、VPN クライアント プロファイルに必要な情報を取得して理解するのに役立ちます。

[!INCLUDE [client profiles](../../includes/vpn-gateway-vwan-vpn-profile-download.md)]

* **OpenVPN フォルダー**には、*ovpn* プロファイルが含まれています。このプロファイルは、キーと証明書を含めるために変更する必要があります。 詳細については、「[Azure VPN Gateway 用に OpenVPN クライアントを構成する](vpn-gateway-howto-openvpn-clients.md#windows)」をご覧ください。 VPN ゲートウェイで Azure AD 認証が選択されている場合、このフォルダーは ZIP ファイルに存在しません。 代わりに、AzureVPN フォルダーに移動して、azurevpnconfig.xml を検索します。

## <a name="next-steps"></a>次のステップ

ポイント対サイトの詳細については、[ポイント対サイト](point-to-site-about.md)に関する記事をご覧ください。
