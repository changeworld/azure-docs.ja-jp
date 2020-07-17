---
title: シナリオ:共有サービスの VNet へのルート
titleSuffix: Azure Virtual WAN
description: ルーティングのシナリオ - すべての VNet とブランチがアクセスする必要がある、ワークロードを持つ共有サービス VNet にアクセスするためのルートを設定します。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: 64bb4c85399f811c0ab7ff84b297b64734efc491
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85567900"
---
# <a name="scenario-route-to-shared-services-vnets"></a>シナリオ:共有サービスの VNet へのルート

Virtual WAN の仮想ハブ ルーティングを使用する場合、多くのシナリオを利用できます。 このシナリオの目標は、すべての VNet とブランチ (VPN/ER/P2S) がアクセスする必要がある、ワークロードを持つ**共有サービス** VNet にアクセスするためのルートを設定することです。 仮想ハブ ルーティングの詳細については、「[仮想ハブのルーティングについて](about-virtual-hub-routing.md)」を参照してください。

## <a name="scenario-workflow"></a><a name="workflow"></a>シナリオのワークフロー

このシナリオを構成するには、次の手順を考慮してください。

1. **共有サービス** VNet を特定します。
2. カスタム ルート テーブルを作成します。 この例では、このルート テーブルを **RT_SHARED** と呼んでいます。 ルート テーブルを作成する手順については、「[仮想ハブのルーティングを構成する方法](how-to-virtual-hub-routing.md)」を参照してください。 ガイドラインとして、次の値を使用します。

   * **関連付け**
     * **共有サービス VNet 以外の Vnet *では、分離する Vnet を選択します。*** これで、これらのすべての Vnet (共有サービス VNet を除く) が、RT_SHARED ルート テーブルのルートに基づいて宛先に到達することができます。

   * **伝達**
      * **ブランチ**では、既に選択している他のルート テーブルに加えて、ルートをこのルート テーブルに伝達します。 この手順により、RT_SHARED ルート テーブルは、すべてのブランチ接続 (VPN/ER/ユーザー VPN) からのルートを取得します。
      * **Vnet** では、**共有サービス VNet** を選択します。 この手順により、RT_SHARED ルート テーブルは、共有サービス VNet 接続からのルートを取得します。

     これで、ルート構成が次の図に示すように変更されます。

   :::image type="content" source="./media/routing-scenarios/shared-service-vnet/shared-services.png" alt-text="共有サービス VNet":::

## <a name="next-steps"></a>次のステップ

* Virtual WAN の詳細については、[FAQ](virtual-wan-faq.md) を参照してください。
* 仮想ハブ ルーティングの詳細については、「[仮想ハブのルーティングについて](about-virtual-hub-routing.md)」を参照してください。
