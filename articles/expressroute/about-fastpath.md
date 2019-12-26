---
title: Azure ExpressRoute FastPath について
description: ゲートウェイをバイパスしてネットワーク トラフィックを送信するための Azure ExpressRoute FastPath について説明します
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/05/2019
ms.author: cherylmc
ms.openlocfilehash: 6ff1dac312bcb4df1e1afc9679df09fc8a2b28ff
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2019
ms.locfileid: "74896889"
---
# <a name="about-expressroute-fastpath"></a>ExpressRoute FastPath について

ExpressRoute 仮想ネットワーク ゲートウェイの目的は、ネットワーク ルートを交換し、ネットワーク トラフィックをルーティングすることです。 FastPath の目的は、お使いのオンプレミス ネットワークと仮想ネットワークの間のデータ パスのパフォーマンスを向上させることです。 FastPath が有効になっていると、ゲートウェイはバイパスされ、ネットワーク トラフィックが仮想ネットワーク内の仮想マシンに直接送信されます。

## <a name="requirements"></a>必要条件

### <a name="circuits"></a>回線

FastPath はすべての ExpressRoute 回線で使用できます。

### <a name="gateways"></a>ゲートウェイ

FastPath を使うときも、仮想ネットワーク ゲートウェイとオンプレミス ネットワークの間でルートを交換する場合は、仮想ネットワーク ゲートウェイを作成する必要があります。 仮想ネットワーク ゲートウェイと ExpressRoute の詳細については、[ExpressRoute の仮想ネットワーク ゲートウェイ](expressroute-about-virtual-network-gateways.md)に関するページを参照してください。

仮想ネットワーク ゲートウェイは、次のいずれかである必要があります。

* Ultra Performance
* ErGw3AZ

## <a name="supported-features"></a>サポートされる機能

FastPath ではほとんどの構成がサポートされていますが、次の機能はサポートされていません。

* ゲートウェイ サブネットの UDR:お使いの仮想ネットワークのゲートウェイ サブネットに UDR を適用した場合、ご自身のオンプレミス ネットワークからのネットワーク トラフィックは、引き続き仮想ネットワーク ゲートウェイに送信されます。

* VNet ピアリング:他の仮想ネットワークが、ExpressRoute に接続されているものとピアリングされている場合、ご自身のオンプレミス ネットワークから他の仮想ネットワーク (いわゆる "スポーク" VNet) へのネットワーク トラフィックは、引き続き仮想ネットワーク ゲートウェイに送信されます。 回避策として、すべての仮想ネットワークを ExpressRoute 回線に直接接続します。

* Basic Load Balancer:仮想ネットワークに Basic の内部ロード バランサーをデプロイする場合、または仮想ネットワークにデプロイする Azure PaaS サービスで Basic の内部ロード バランサーを使用する場合、オンプレミスのネットワークから Basic Load Balancer でホストされている仮想 IP へのネットワーク トラフィックが仮想ネットワーク ゲートウェイに送信されます。 この問題を解決するには、Basic Load Balancer から [Standard Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) にアップグレードします。

* Private Link:オンプレミス ネットワークから仮想ネットワークの[プライベート エンドポイント](../private-link/private-link-overview.md)に接続する場合、接続は仮想ネットワーク ゲートウェイを通過します。
 
## <a name="next-steps"></a>次の手順

FastPath を有効にする方法については、[ExpressRoute への仮想ネットワークのリンク](expressroute-howto-linkvnet-arm.md#configure-expressroute-fastpath)に関するページを参照してください。
