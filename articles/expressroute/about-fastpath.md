---
title: Azure ExpressRoute FastPath について
description: ゲートウェイをバイパスしてネットワーク トラフィックを送信するための Azure ExpressRoute FastPath について説明します
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 08/10/2021
ms.author: duau
ms.custom: references_regions
ms.openlocfilehash: 1fcdeaaee2ebafbfd8e97577ed87a5ef0e545cd5
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130226397"
---
# <a name="about-expressroute-fastpath"></a>ExpressRoute FastPath について

ExpressRoute 仮想ネットワーク ゲートウェイの目的は、ネットワーク ルートを交換し、ネットワーク トラフィックをルーティングすることです。 FastPath の目的は、お使いのオンプレミス ネットワークと仮想ネットワークの間のデータ パスのパフォーマンスを向上させることです。 FastPath が有効になっていると、ゲートウェイはバイパスされ、ネットワーク トラフィックが仮想ネットワーク内の仮想マシンに直接送信されます。

## <a name="requirements"></a>必要条件

### <a name="circuits"></a>回線

FastPath はすべての ExpressRoute 回線で使用できます。

### <a name="gateways"></a>ゲートウェイ

FastPath を使うときも、仮想ネットワーク ゲートウェイとオンプレミス ネットワークの間でルートを交換する場合は、仮想ネットワーク ゲートウェイを作成する必要があります。 パフォーマンス情報やゲートウェイ SKU など、仮想ネットワーク ゲートウェイと ExpressRoute の詳細については、[ExpressRoute の仮想ネットワーク ゲートウェイ](expressroute-about-virtual-network-gateways.md)に関するページを参照してください。

FastPath を構成するには、仮想ネットワーク ゲートウェイを次のいずれかにする必要があります。

* Ultra Performance
* ErGw3AZ

## <a name="limitations"></a>制限事項

FastPath ではほとんどの構成がサポートされていますが、次の機能はサポートされていません。

* ゲートウェイ サブネット上の UDR: この UDR は、FastPath がオンプレミス ネットワークから Azure 仮想ネットワーク内の仮想マシンに直接送信するネットワーク トラフィックには影響しません。 

* Basic Load Balancer: 仮想ネットワークに Basic の内部ロード バランサーをデプロイする場合、または仮想ネットワークにデプロイする Azure PaaS サービスで Basic の内部ロード バランサーを使用する場合、オンプレミスのネットワークから Basic Load Balancer でホストされている仮想 IP へのネットワーク トラフィックが仮想ネットワーク ゲートウェイに送信されます。 この問題を解決するには、Basic Load Balancer から [Standard Load Balancer](../load-balancer/load-balancer-overview.md) にアップグレードします。

* Private Link: オンプレミス ネットワークから仮想ネットワークの[プライベート エンドポイント](../private-link/private-link-overview.md)に接続する場合、接続は仮想ネットワーク ゲートウェイを通過します。

## <a name="public-preview"></a>パブリック プレビュー

以下の FastPath 機能はパブリック プレビュー段階です。

**VNet ピアリング** - ExpressRoute に接続されているものとピアリングされた仮想ネットワークにデプロイされている VM に、FastPath によってトラフィックが直接送信され、ExpressRoute の仮想ネットワーク ゲートウェイはバイパスされます。

[ExpressRoute の FastPath 機能に登録する方法](expressroute-howto-linkvnet-arm.md#enroll-in-expressroute-fastpath-features-preview)に関する記事を参照してください。

すべてのリージョンで利用できます。
 
## <a name="next-steps"></a>次のステップ

FastPath を有効にする方法については、[ExpressRoute への仮想ネットワークのリンク](expressroute-howto-linkvnet-arm.md#configure-expressroute-fastpath)に関するページを参照してください。
