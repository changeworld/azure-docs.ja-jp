---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 08/26/2021
ms.author: larryfr
ms.openlocfilehash: 3bd72c4930c510df03856ad31bbea51d21829c1e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128646112"
---
VNet の背後で保護されているワークスペースに接続するには、次の方法のいずれかを使用します。

* [Azure VPN ゲートウェイ](/azure/vpn-gateway/vpn-gateway-about-vpngateways) - オンプレミスのネットワークをプライベート接続を介して VNet に接続します。 接続は、パブリック インターネットを介して行われます。 使用できる VPN ゲートウェイには、次の 2 種類があります。

    * [ポイント対サイト](/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal): 各クライアント コンピューターは、VPN クライアントを使用して VNet に接続します。
    * [サイト間](/azure/vpn-gateway/tutorial-site-to-site-portal): VPN デバイスにより、VNet がオンプレミス ネットワークに接続されます。

* [ExpressRoute](https://azure.microsoft.com/services/expressroute/) - オンプレミスのネットワークをプライベート接続を介してクラウドに接続します。 接続は、接続プロバイダーを使用して行われます。
* [Azure Bastion](/azure/bastion/bastion-overview) - このシナリオでは、VNet 内に Azure 仮想マシン (ジャンプ ボックスと呼ばれることもある) を作成します。 次に、Azure Bastion を使用して VM に接続します。 Bastion を使用すると、ローカル Web ブラウザーから RDP または SSH セッションを使用して VM に接続できます。 続いて、開発環境としてジャンプ ボックスを使用します。 これは VNet 内にあるため、ワークスペースに直接アクセスできます。 ジャンプ ボックスの使用例については、[チュートリアル: セキュリティで保護されたワークスペースの作成](/azure/machine-learning/tutorial-create-secure-workspace)に関するページを参照してください。

> [!IMPORTANT]
> __VPN ゲートウェイ__ または __ExpressRoute__ を使用する場合は、オンプレミスのリソースと VNet 内のリソース間での名前解決の仕組みを計画する必要があります。 詳細については、[カスタム DNS サーバーの使用](/azure/machine-learning/how-to-custom-dns)に関する記事を参照してください。
