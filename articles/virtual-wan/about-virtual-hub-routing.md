---
title: 仮想ハブのルーティングについて
titleSuffix: Azure Virtual WAN
description: この記事では、仮想ハブのルーティングについて説明します。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: 368440976558730bd7bb9600a0bf45c56d1ee300
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2020
ms.locfileid: "86147397"
---
# <a name="about-virtual-hub-routing"></a>仮想ハブのルーティングについて

仮想ハブのルーティング機能は、Border Gateway Protocol (BGP) を使用してゲートウェイ間のすべてのルーティングを管理するルーターによって提供されます。 仮想ハブには、サイト間 VPN ゲートウェイ、ExpressRoute ゲートウェイ、ポイント対サイト ゲートウェイ、Azure Firewall などの複数のゲートウェイを含めることができます。 また、このルーターは、仮想ハブに接続する仮想ネットワーク間の転送接続を提供し、最大 50 Gbps の合計スループットをサポートできます。 これらのルーティング機能は、Standard Virtual WAN のお客様に適用されます。

ルーティングを構成する方法については、「[仮想ハブのルーティングを構成する方法](how-to-virtual-hub-routing.md)」を参照してください。

## <a name="routing-concepts"></a><a name="concepts"></a>ルーティングの概念

次のセクションでは、仮想ハブのルーティングでの主要な概念について説明します。

> [!NOTE]
> 仮想ネットワーク接続におけるハブ ルート テーブル、関連付け、伝達、静的ルートというこれら新しい概念の一部はまだ展開中である場合があり、8 月 3 日の週に完了する予定です。
>

### <a name="hub-route-table"></a><a name="hub-route"></a>ハブ ルート テーブル

仮想ハブ ルート テーブルには、1 つ以上のルートを含めることができます。 ルートには、その名前、ラベル、宛先の種類、宛先プレフィックスの一覧、およびルーティングされるパケットの次ホップ情報が含まれます。 通常、**接続**は、ルート テーブルに関連付けられたり伝達したりするルーティング構成を持ちます。

### <a name="connection"></a><a name="connection"></a>接続

接続は、ルーティング構成を持つ Resource Manager のリソースです。 4 種類の接続は次のとおりです。

* **VPN 接続**:VPN サイトを仮想ハブ VPN ゲートウェイに接続します。
* **ExpressRoute 接続**: ExpressRoute 回線を仮想ハブの ExpressRoute ゲートウェイに接続します。
* **P2S 構成接続**: ユーザー VPN (ポイント対サイト) 構成を仮想ハブのユーザー VPN (ポイント対サイト) ゲートウェイに接続します。
* **ハブ仮想ネットワーク接続**: 仮想ネットワークを仮想ハブに接続します。

仮想ネットワーク接続のルーティング構成は、セットアップ中に設定できます。 既定では、すべての接続は既定のルート テーブルに関連付けられ、伝達します。

### <a name="association"></a><a name="association"></a>関連付け

各接続は、1 つのルート テーブルに関連付けられています。 ルート テーブルに接続を関連付けることにより、ルート テーブルにルートとして示されている宛先にトラフィックを送信できます。 接続のルーティング構成には、関連付けられているルート テーブルが表示されます。  複数の接続を同じルート テーブルに関連付けることができます。 すべての VPN、ExpressRoute、およびユーザー VPN 接続は、同じ (既定の) ルート テーブルに関連付けられます。

既定では、すべての接続は、仮想ハブ内の**既定のルート テーブル**に関連付けられます。 各仮想ハブには独自の既定のルート テーブルがあり、これを編集して静的ルートを追加することができます。 静的に追加されたルートは、同じプレフィックスに対する動的に学習されたルートより優先されます。

:::image type="content" source="./media/about-virtual-hub-routing/concepts-association.png" alt-text="関連付け":::

### <a name="propagation"></a><a name="propagation"></a>伝達

接続は、ルート テーブルにルートを動的に伝達します。 VPN 接続、ExpressRoute 接続、または P2S 構成接続では、ルートは、BGP を使用して仮想ハブからオンプレミス ルーターに伝達されます。 ルートは、1 つまたは複数のルート テーブルに伝達できます。

各仮想ハブでは、**None ルート テーブル**も使用できます。 None ルート テーブルに伝達するということは、接続から伝達する必要があるルートがないことを意味します。 VPN、ExpressRoute、およびユーザー VPN 接続は、ルートを同じルート テーブルのセットに伝達します。

:::image type="content" source="./media/about-virtual-hub-routing/concepts-propagation.png" alt-text="伝達":::

### <a name="configuring-static-routes-in-a-virtual-network-connection"></a><a name="static"></a>仮想ネットワーク接続での静的ルートの構成

静的ルートを構成すると、仮想ハブに接続されたスポーク VNet にプロビジョニングされているネットワーク仮想アプライアンス (NVA) のものである可能性がある次ホップ IP 経由でトラフィックを誘導するメカニズムが提供されます。 静的ルートは、ルート名、宛先プレフィックスの一覧、および次ホップ IP で構成されます。

## <a name="route-tables-in-basic-and-standard-virtual-wans-prior-to-the-feature-set-of-association-and-propagation"></a><a name="route"></a>関連付けと伝達の機能セットの前の Basic および Standard Virtual WAN 内のルート テーブル

ルート テーブルで、関連付けと伝達のための機能が利用できるようになりました。 既存のルート テーブルは、これらの機能を持たないルート テーブルです。 ハブ ルーティングに既存のルートがあり、新しい機能を使用する場合は、次を考慮してください。

* **仮想ハブに既存のルートがある Standard Virtual WAN のお客様**:

新しいルート テーブルの機能を使用するには、Azure でのロールアウトが完了する 8 月 3 日の週までお待ちください。 Azure portal のハブの [ルーティング] セクションにルートが既にある場合、まずそれらを削除してから、(Azure portal のハブの [ルート テーブル] セクションにある) 新しいルート テーブルを作成する必要があります。

* **仮想ハブに既存のルートがある Basic Virtual WAN のお客様**:新しいルート テーブルの機能を使用するには、Azure でのロールアウトが完了する 8 月 3 日の週までお待ちください。 Azure portal のハブの [ルーティング] セクションにルートが既にある場合、まずそれらを削除してから、お使いの Basic Virtual WAN を Standard Virtual WAN に**アップグレード**します。 「[Virtual WAN を Basic から Standard にアップグレードする](upgrade-virtual-wan.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

ルーティングを構成する方法については、「[仮想ハブのルーティングを構成する方法](how-to-virtual-hub-routing.md)」を参照してください。

Virtual WAN の詳細については、[FAQ](virtual-wan-faq.md) を参照してください。
