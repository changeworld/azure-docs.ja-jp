---
title: Azure VPN Gateway:P2S VPN クライアント プロファイルについて
description: この記事を利用して、VPN クライアント プロファイルに必要な情報を確認できます。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 09/03/2020
ms.author: cherylmc
ms.openlocfilehash: 6a09767a7992a5f902adea6f99e937f3fc6fa7fa
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/22/2020
ms.locfileid: "90985937"
---
# <a name="about-p2s-vpn-client-profiles"></a>P2S VPN クライアント プロファイルについて

ダウンロードしたプロファイル ファイルには、VPN 接続の構成に必要な情報が含まれています。 この記事は、VPN クライアント プロファイルに必要な情報を取得して理解するのに役立ちます。

[!INCLUDE [client profiles](../../includes/vpn-gateway-vwan-vpn-profile-download.md)]

* **OpenVPN フォルダー**には、*ovpn* プロファイルが含まれています。このプロファイルは、キーと証明書を含めるために変更する必要があります。 詳細については、「[Azure VPN Gateway 用に OpenVPN クライアントを構成する](vpn-gateway-howto-openvpn-clients.md#windows)」をご覧ください。 VPN ゲートウェイで Azure AD 認証が選択されている場合、このフォルダーは ZIP ファイルに存在しません。 代わりに、AzureVPN フォルダーに移動して、azurevpnconfig.xml を検索します。

## <a name="next-steps"></a>次のステップ

ポイント対サイトの詳細については、[ポイント対サイト](point-to-site-about.md)に関する記事をご覧ください。
