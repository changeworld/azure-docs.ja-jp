---
title: インクルード ファイル
description: インクルード ファイル
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/12/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 8087025810214f3edbb74e628698eb69558f3500
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/14/2019
ms.locfileid: "74085255"
---
仮想ネットワーク ゲートウェイを作成する場合、使用するゲートウェイの SKU を指定する必要があります。 ワークロード、スループット、機能、および SLA の種類に基づいて、要件を満たす SKU を選択します。 Azure Availability Zones における仮想ネットワーク ゲートウェイの SKU については、[Azure Availability Zones でのゲートウェイの SKU](../articles/vpn-gateway/about-zone-redundant-vnet-gateways.md) に関するページを参照してください。

###  <a name="benchmark"></a>各ゲートウェイ SKU のトンネル数、接続数、およびスループット

[!INCLUDE [Aggregated throughput by SKU](./vpn-gateway-table-gwtype-aggtput-include.md)]

> [!NOTE]
> VpnGw SKU (VpnGw1、VpnGw1AZ、VpnGw2、VpnGw2AZ、VpnGw3、VpnGw3AZ、VpnGw4、VpnGw4AZ、VpnGw5、および VpnGw5AZ) は、Resource Manager デプロイ モデルでのみサポートされています。 クラシック仮想ネットワークには、引き続き以前の (従来の) SKU をご利用ください。
>  * 従来のゲートウェイ SKU (Basic、Standard、HighPerformance) の使用については、「[仮想ネットワーク ゲートウェイ SKU (従来の SKU) の使用](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md)」を参照してください。
>  * ExpressRoute ゲートウェイ SKU については、「[ExpressRoute 用の仮想ネットワーク ゲートウェイについて](../articles/expressroute/expressroute-about-virtual-network-gateways.md)」を参照してください。
>

###  <a name="feature"></a>各ゲートウェイ SKU の機能セット

新しい VPN ゲートウェイ SKU では、ゲートウェイで提供される機能セットが効率化されています。

| **SKU**| **機能**|
| ---    | ---         |
|**Basic** (\*\*)   | **ルートベースの VPN**: S2S/接続用に 10 個のトンネル。RADIUS 認証なし (P2S)、IKEv2 なし (P2S)<br>**ポリシーベースの VPN**: (IKEv1): 1 つの S2S/接続トンネル、P2S なし|
| **Basic を除くすべての Generation1 および Generation2 SKU** | **ルートベース VPN**: 最大 30 トンネル ( * )、P2S、BGP、アクティブ/アクティブ、カスタム IPsec/IKE ポリシー、ExpressRoute/VPN 共存 |
|        |             |

( * ) "PolicyBasedTrafficSelectors" を構成することによって、ルートベースの VPN ゲートウェイを、オンプレミスにある複数のポリシーベース ファイアウォール デバイスに接続することができます。 詳細については、[PowerShell を使って複数のオンプレミス ポリシーベース VPN デバイスに VPN ゲートウェイを接続する方法](../articles/vpn-gateway/vpn-gateway-connect-multiple-policybased-rm-ps.md)に関するページを参照してください。

(\*\*) Basic SKU はレガシ SKU とみなされます。 Basic SKU には一定の機能制限があります。 Basic SKU を使用するゲートウェイのサイズを変更し、新しいゲートウェイ SKU のいずれかにすることはできず、その代わり、新しい SKU に変更する必要があります。この場合、VPN ゲートウェイの削除と再作成が必要です。

###  <a name="workloads"></a>ゲートウェイの SKU - 運用環境と開発テスト環境のワークロード

SLA と機能セットに違いがあるため、運用環境と開発テスト環境には以下の SKU をお勧めします。

| **ワークロード**                       | **SKU**               |
| ---                                | ---                    |
| **運用環境での重要なワークロード** | Basic を除くすべての Generation1 および Generation2 SKU |
| **開発テストまたは概念実証**   | Basic (\*\*)                 |
|                                    |                        |

(\*\*) Basic SKU はレガシ SKU とみなされ、機能に制限があります。 Basic SKU を使用する前に、必要としている機能がサポートされていることを確認してください。

古い SKU (レガシ) を使用している場合、運用環境で推奨される SKU は、Standard SKU と HighPerformance SKU です。 古い SKU の情報や指示事項については、[ゲートウェイ SKU (レガシ)](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md) に関するページを参照してください。
