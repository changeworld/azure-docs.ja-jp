---
ms.author: cherylmc
author: cherylmc
ms.date: 07/30/2021
ms.service: virtual-wan
ms.topic: include
ms.openlocfilehash: 06b4d9f2830bfd9599aee30c68235d3078eb32fa
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121734683"
---
**OpenVPN**

* [Azure Virtual WAN 用に OpenVPN クライアントを構成する](../articles/virtual-wan/howto-openvpn-clients.md)
* [Azure AD の認証 - Windows 10](../articles/virtual-wan/openvpn-azure-ad-client.md)
* [Azure AD の認証 - macOS](../articles/virtual-wan/openvpn-azure-ad-client-mac.md)

**IKEv2**

1. Windows コンピューターのアーキテクチャに対応する VPN クライアント構成ファイルを選択します。 64 ビットのプロセッサ アーキテクチャの場合は、"VpnClientSetupAmd64" インストーラー パッケージを選択します。 32 ビットのプロセッサ アーキテクチャの場合は、"VpnClientSetupX86" インストーラー パッケージを選択します。

1. パッケージをダブルクリックしてインストールします。 SmartScreen ポップアップが表示された場合は、 **[詳細]** 、 **[実行]** の順に選択します。

1. クライアント コンピューターで **[ネットワークの設定]** に移動し、 **[VPN]** を選択します。 VPN 接続により、その接続先の仮想ネットワークの名前が表示されます。

1. 接続を試行する前に、クライアント コンピューターにクライアント証明書をインストール済みであることを確認します。 ネイティブ Azure 証明書の認証タイプを使用する場合、認証にはクライアント証明書が必要です。 証明書の生成の詳細については、「[証明書の生成](../articles/virtual-wan/certificates-point-to-site.md)」をご覧ください。 クライアント証明書のインストール方法については、[クライアント証明書のインストール](../articles/vpn-gateway/point-to-site-how-to-vpn-client-install-azure-cert.md)に関するページをご覧ください。
