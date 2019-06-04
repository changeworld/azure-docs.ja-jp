---
title: インクルード ファイル
description: インクルード ファイル
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 05/22/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 211935aac56dff8d6e524706c416c126b1a0c3b8
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/23/2019
ms.locfileid: "66159378"
---
|**SKU**   | **S2S/VNet 間<br>トンネル** | **P2S<br> SSTP 接続** | **P2S<br> IKEv2/OpenVPN 接続** | **合計<br>スループット ベンチマーク** | **BGP** |
|---       | ---        | ---       | ---            | ---       | --- |
|**Basic** | 最大 10    | 最大 128  | サポートされていません  | 100 Mbps  | サポートされていません|
|**VpnGw1**| 最大 30*   | 最大 128  | 最大 250       | 650 Mbps  | サポートされています |
|**VpnGw2**| 最大 30*   | 最大 128  | 最大 500       | 1 Gbps    | サポートされています |
|**VpnGw3**| 最大 30*   | 最大 128  | 最大 1,000      | 1.25 Gbps | サポートされています |


(*) 30 個を超える S2S VPN トンネルが必要な場合は、[Virtual WAN](../articles/virtual-wan/virtual-wan-about.md) を使用してください。

* 合計スループット ベンチマークは、1 つのゲートウェイから集計された複数のトンネルの測定値に基づいています。 VPN ゲートウェイの合計スループット ベンチマークは、S2S と P2S を組み合わせたものです。 **多数のポイント対サイト接続がある場合、スループットの制限が原因でサイト間接続に悪影響が及ぶ可能性があります。** 合計スループット ベンチマークは、インターネット トラフィックの状況とアプリケーションの動作に依存するため、保証されたスループットではありません。

* これらの接続の制限は別々になっています。 たとえば、VpnGw1 SKU では 128 の SSTP 接続が利用できると共に 250 の IKEv2 接続を利用できます。

* 料金情報については、[価格](https://azure.microsoft.com/pricing/details/vpn-gateway)に関するページをご覧ください。

* SLA (サービス レベル アグリーメント) 情報は [SLA](https://azure.microsoft.com/support/legal/sla/vpn-gateway/) のページで確認できます。

* VpnGw1、VpnGw2、および VpnGw3 は、Resource Manager デプロイ モデルを使用する VPN ゲートウェイでのみサポートされます。

* Basic SKU はレガシ SKU とみなされます。 Basic SKU には一定の機能制限があります。 Basic SKU を使用するゲートウェイのサイズを変更し、新しいゲートウェイ SKU のいずれかにすることはできません。その代わり、新しい SKU に変更する必要があります。 Basic SKU を使用する前に、必要としている機能がサポートされていることを確認してください。
