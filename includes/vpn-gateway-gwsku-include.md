---
title: インクルード ファイル
description: インクルード ファイル
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 605533f25b36a92a660301d28aa63cb2ecdd44f4
ms.sourcegitcommit: 11321f26df5fb047dac5d15e0435fce6c4fde663
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/06/2018
ms.locfileid: "37909986"
---
仮想ネットワーク ゲートウェイを作成する場合、使用するゲートウェイの SKU を指定する必要があります。 ワークロード、スループット、機能、および SLA の種類に基づいて、要件を満たす SKU を選択します。

###  <a name="benchmark"></a>各ゲートウェイ SKU のトンネル数、接続数、およびスループット

[!INCLUDE [Aggregated throughput by SKU](./vpn-gateway-table-gwtype-aggtput-include.md)]

[!INCLUDE [classic SKU](./vpn-gateway-classic-sku-support-include.md)]

###  <a name="feature"></a>各ゲートウェイ SKU の機能セット

新しい VPN ゲートウェイ SKU では、ゲートウェイで提供される機能セットが効率化されています。

| **SKU**| **機能**|
| ---    | ---         |
|**Basic** (\*\*)   | **ルート ベースの VPN**: 10 個のトンネルと P2S、RADIUS 認証なし (P2S)、IKEv2 なし (P2S)<br>**ポリシーベース VPN**: (IKEv1): 1 トンネル。P2S なし|
| **VpnGw1、VpnGw2、および VpnGw3** | **ルートベース VPN**: 最大 30 トンネル ( * )、P2S、BGP、アクティブ/アクティブ、カスタム IPsec/IKE ポリシー、ExpressRoute/VPN 共存 |
|        |             |

( * ) "PolicyBasedTrafficSelectors" を構成することによって、ルートベースの VPN ゲートウェイ (VpnGw1、VpnGw2、VpnGw3) を、オンプレミスにある複数のポリシーベース ファイアウォール デバイスに接続することができます。 詳細については、[PowerShell を使って複数のオンプレミス ポリシーベース VPN デバイスに VPN ゲートウェイを接続する方法](../articles/vpn-gateway/vpn-gateway-connect-multiple-policybased-rm-ps.md)に関するページを参照してください。

(\*\*) Basic SKU はレガシ SKU とみなされます。 Basic SKU には一定の機能制限があります。 Basic SKU を使用するゲートウェイのサイズを変更し、新しいゲートウェイ SKU のいずれかにすることはできず、その代わり、新しい SKU に変更する必要があります。この場合、VPN ゲートウェイの削除と再作成が必要です。

###  <a name="workloads"></a>ゲートウェイの SKU - 運用環境と開発テスト環境のワークロード

SLA と機能セットに違いがあるため、運用環境と開発テスト環境には以下の SKU をお勧めします。

| **ワークロード**                       | **SKU**               |
| ---                                | ---                    |
| **運用環境での重要なワークロード** | VpnGw1、VpnGw2、VpnGw3 |
| **開発テストまたは概念実証**   | Basic (\*\*)                 |
|                                    |                        |

(\*\*) Basic SKU はレガシ SKU とみなされ、機能に制限があります。 Basic SKU を使用する前に、必要としている機能がサポートされていることを確認してください。

古い SKU (レガシ) を使用している場合、運用環境で推奨される SKU は、Standard SKU と HighPerformance SKU です。 古い SKU の情報や指示事項については、[ゲートウェイ SKU (レガシ)](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md) に関するページを参照してください。
