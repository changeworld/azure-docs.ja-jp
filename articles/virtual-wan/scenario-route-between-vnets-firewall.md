---
title: シナリオ:Virtual WAN 用の Azure Firewall のカスタム ルーティング
titleSuffix: Azure Virtual WAN
description: ルーティングのシナリオ - VNet 間ではトラフィックを直接ルーティングする一方、"VNet -> インターネットまたはブランチ" と "ブランチ対 VNet" のトラフィック フローには Azure Firewall を使用します
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: cdaa594a87d960688638591e6edd525aa3b048f4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85567940"
---
# <a name="scenario-azure-firewall---custom"></a>シナリオ:Azure Firewall - カスタム

Virtual WAN の仮想ハブ ルーティングを使用する場合、多くのシナリオを利用できます。 このシナリオでは、VNet 間ではトラフィックを直接ルーティングする一方、"VNet 対インターネットまたはブランチ" と "ブランチ対 VNet" のトラフィック フローには Azure Firewall を使用することを目標とします。 仮想ハブのルーティングの詳細については、「[仮想ハブのルーティングについて](about-virtual-hub-routing.md)」を参照してください。

## <a name="scenario-workflow"></a><a name="workflow"></a>シナリオのワークフロー

このシナリオでは、VNet 対インターネット、VNet 対ブランチ、またはブランチ対 VNet のトラフィックを Azure Firewall 経由でルーティングする一方、VNet 対 VNet のトラフィックについては直接ルーティングします。 Azure Firewall Manager を使用している場合、ルート設定は自動的に**既定のルート テーブル**に入力されます。 プライベート トラフィックは VNet とブランチに適用され、インターネット トラフィックは 0.0.0.0/0 に適用されます。

VPN、ExpressRoute、およびユーザー VPN 接続は、総称して "ブランチ" と呼ばれ、同じ (既定の) ルート テーブルに関連付けられます。 すべての VPN 接続、ExpressRoute 接続、ユーザー VPN 接続は、同じルート テーブルのセットにルートを伝達します。 このシナリオを構成するには、次の手順を考慮してください。

1. カスタム ルート テーブル **RT_VNET** を作成します。
1. VNet 対インターネットおよび VNet 対ブランチをアクティブにするルートを作成します (次ホップが Azure Firewall を指す 0.0.0.0/0)。 [Propagation]\(伝達\) セクションで、VNet が選択され、より具体的なルートが確保されていることを確認します。これにより、VNet 対 VNet の直接トラフィック フローが可能になります。

   * **[Association]\(関連付け\):** [VNet] を選択します。これは、このルート テーブルのルートに従って VNet が宛先に到達することを意味します。
   * **[Propagation]\(伝達\):** [VNet] を選択します。これは、VNet がこのルート テーブルに伝達されることを意味します。言い換えると、より具体的なルートがこのルート テーブルに伝達されるため、VNet 間の直接のトラフィック フローが確保されます。

1. VNet の集約された静的ルートを**既定のルート テーブル**に追加して、Azure Firewall を経由するブランチ対 VNet フローをアクティブ化します。 

   * ブランチが関連付けられ、既定のルート テーブルに伝達されることに注意してください。
   * ブランチは RT_VNET ルート テーブルに伝達されません。 これにより、Azure Firewall を経由する VNet 対ブランチのトラフィック フローが確保されます。

これにより、**図 1** に示すように、ルーティング構成が変更されます。

**図 1**

:::image type="content" source="./media/routing-scenarios/between-vnets-firewall/routing.png" alt-text="図 1":::

## <a name="next-steps"></a>次のステップ

* Virtual WAN の詳細については、[FAQ](virtual-wan-faq.md) を参照してください。
* 仮想ハブのルーティングの詳細については、「[仮想ハブのルーティングについて](about-virtual-hub-routing.md)」を参照してください。
* 仮想ハブ ルーティングの構成方法の詳細については、「[仮想ハブのルーティングを構成する方法](how-to-virtual-hub-routing.md)」を参照してください。
