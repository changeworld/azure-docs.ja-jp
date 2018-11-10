---
title: インクルード ファイル
description: インクルード ファイル
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/29/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 03af5efcd4a37203a82db503f8bc602b33de734d
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/29/2018
ms.locfileid: "50226617"
---
|**SKU**   | **S2S/VNet 間<br>トンネル** | **P2S<br> SSTP 接続** | **P2S<br> IKEv2 接続** | **合計<br>スループット ベンチマーク** |
|---       | ---        | ---       | ---            | ---       |
|**VpnGw1**| 最大 30*   | 最大 128  | 最大 250       | 650 Mbps  |
|**VpnGw2**| 最大 30*   | 最大 128  | 最大 500       | 1 Gbps    |
|**VpnGw3**| 最大 30*   | 最大 128  | 最大 1,000      | 1.25 Gbps |
|**Basic** | 最大 10    | 最大 128  | サポートされていません  | 100 Mbps  | 

(*) 30 個を超える S2S VPN トンネルが必要な場合は、[Virtual WAN](../articles/virtual-wan/virtual-wan-about.md) を使用してください。

* VPN ゲートウェイの合計スループット ベンチマークは、S2S と P2S を組み合わせたものです。 **多数のポイント対サイト接続がある場合、スループットの制限が原因でサイト間接続に悪影響が及ぶ可能性があります。** 合計スループット ベンチマークは、1 つのゲートウェイから集計された複数のトンネルの測定値に基づいています。 インターネットのトラフィックの状況とアプリケーションの動作に依存するため、これは保証されたスループットではありません。

* これらの接続の制限は別々になっています。 たとえば、VpnGw1 SKU では 128 の SSTP 接続が利用できると共に 250 の IKEv2 接続を利用できます。

* 料金情報については、[価格](https://azure.microsoft.com/pricing/details/vpn-gateway)に関するページをご覧ください。

* SLA (サービス レベル アグリーメント) 情報は [SLA](https://azure.microsoft.com/support/legal/sla/vpn-gateway/) のページで確認できます。

* VpnGw1、VpnGw2、および VpnGw3 は、Resource Manager デプロイ モデルを使用する VPN ゲートウェイでのみサポートされます。
