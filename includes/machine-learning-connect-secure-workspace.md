---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 08/26/2021
ms.author: larryfr
ms.openlocfilehash: 966232f893ef404c3c4d6e5d3bc4929f74d7bd62
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130215141"
---
VNet の背後で保護されているワークスペースに接続するには、次の方法のいずれかを使用します。

* [Azure VPN ゲートウェイ](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md) - オンプレミスのネットワークをプライベート接続を介して VNet に接続します。 接続は、パブリック インターネットを介して行われます。 使用できる VPN ゲートウェイには、次の 2 種類があります。

    * [ポイント対サイト](../articles/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md): 各クライアント コンピューターは、VPN クライアントを使用して VNet に接続します。
    * [サイト間](../articles/vpn-gateway/tutorial-site-to-site-portal.md): VPN デバイスにより、VNet がオンプレミス ネットワークに接続されます。

* [ExpressRoute](https://azure.microsoft.com/services/expressroute/) - オンプレミスのネットワークをプライベート接続を介してクラウドに接続します。 接続は、接続プロバイダーを使用して行われます。
* [Azure Bastion](../articles/bastion/bastion-overview.md) - このシナリオでは、VNet 内に Azure 仮想マシン (ジャンプ ボックスと呼ばれることもある) を作成します。 次に、Azure Bastion を使用して VM に接続します。 Bastion を使用すると、ローカル Web ブラウザーから RDP または SSH セッションを使用して VM に接続できます。 続いて、開発環境としてジャンプ ボックスを使用します。 これは VNet 内にあるため、ワークスペースに直接アクセスできます。 ジャンプ ボックスの使用例については、[チュートリアル: セキュリティで保護されたワークスペースの作成](../articles/machine-learning/tutorial-create-secure-workspace.md)に関するページを参照してください。

> [!IMPORTANT]
> __VPN ゲートウェイ__ または __ExpressRoute__ を使用する場合は、オンプレミスのリソースと VNet 内のリソース間での名前解決の仕組みを計画する必要があります。 詳細については、[カスタム DNS サーバーの使用](../articles/machine-learning/how-to-custom-dns.md)に関する記事を参照してください。