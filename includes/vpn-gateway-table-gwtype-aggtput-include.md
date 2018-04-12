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
ms.openlocfilehash: c9457e51858d4a073d8baffdd435c8100d95d566
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/05/2018
---
|**SKU**   | **S2S/VNet 間<br>トンネル** | **P2S<br>接続** | **合計<br>スループット ベンチマーク** |
|---       | ---                             | ---                    | ---                         |
|**VpnGw1**| 最大 30                         | 最大 128               | 650 Mbps                    |
|**VpnGw2**| 最大 30                         | 最大 128               | 1 Gbps                      |
|**VpnGw3**| 最大 30                         | 最大 128               | 1.25 Gbps                   |
|**Basic** | 最大 10                         | 最大 128               | 100 Mbps                    | 
|          |                                 |                        |                             | 

- 合計スループット ベンチマークは、1 つのゲートウェイから集計された複数のトンネルの測定値に基づいています。 インターネットのトラフィックの状況とアプリケーションの動作に依存するため、これは保証されたスループットではありません。

- 料金情報については、[価格](https://azure.microsoft.com/pricing/details/vpn-gateway)に関するページをご覧ください。

- SLA (サービス レベル アグリーメント) 情報は [SLA](https://azure.microsoft.com/support/legal/sla/vpn-gateway/) のページで確認できます。

- VpnGw1、VpnGw2、および VpnGw3 は、Resource Manager デプロイメント モデルを使用する VPN ゲートウェイでのみサポートされます。