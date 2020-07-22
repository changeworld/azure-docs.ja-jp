---
title: 'シナリオ: 任意間'
titleSuffix: Azure Virtual WAN
description: ルーティングのシナリオ - 任意間
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: ecc2b3cf236cb2a78fd595189649e7f6b176d709
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85567668"
---
# <a name="scenario-any-to-any"></a>シナリオ:任意の環境間

Virtual WAN で仮想ハブのルーティングを使用する場合、多くのシナリオを利用できます。 任意間シナリオでは、すべてのスポークが別のスポークに接続できます。 複数のハブが存在する場合、Standard Virtual WAN では、ハブツーハブ ルーティング (ハブ間ルーティングとも呼ばれます) が既定で有効になります。 

このシナリオでは、VPN、ExpressRoute、およびユーザー VPN 接続が、同じルート テーブルに関連付けられます。 すべての VPN、ExpressRoute、およびユーザー VPN 接続では、同じルート テーブルのセットにルートが伝達されます。 仮想ハブのルーティングの詳細については、「[仮想ハブのルーティングについて](about-virtual-hub-routing.md)」を参照してください。

## <a name="scenario-architecture"></a><a name="architecture"></a>シナリオのアーキテクチャ

**図 1** では、すべての VNet とブランチ (VPN、ExpressRoute、P2S) を相互に接続できます。 仮想ハブでは、接続は次のように機能します。

* VPN 接続では、VPN サイトが VPN ゲートウェイに接続されます。
* 仮想ネットワーク接続では、仮想ネットワークが仮想ハブに接続されます。 仮想ハブのルーターでは、VNet 間の転送機能が提供されます。
* ExpressRoute 接続では、ExpressRoute 回線が ExpressRoute ゲートウェイに接続されます。

接続のルーティング構成を **[なし]** またはカスタム ルートテーブルに設定しない限り、これらの接続は (既定では作成時に) 既定のルート テーブルに関連付けられます。 また、これらの接続では、既定では、既定のルート テーブルにルートが伝達されます。 これにより、すべてのスポーク (VNet、VPN、ER、P2S) が相互に接続できる任意間シナリオが有効になります。

**図 1**

:::image type="content" source="./media/routing-scenarios/any-any/figure-1.png" alt-text="図 1":::

## <a name="scenario-workflow"></a><a name="workflow"></a>シナリオのワークフロー

このシナリオは、Standard Virtual WAN では既定で有効になっています。 WAN 構成でブランチ間の設定が無効になっている場合、ブランチ スポーク間の接続は許可されません。 Virtual WAN では、VPN/ExpressRoute/ユーザー VPN はブランチ スポークとみなされます。

## <a name="next-steps"></a>次のステップ

* Virtual WAN の詳細については、[FAQ](virtual-wan-faq.md) を参照してください。
* 仮想ハブのルーティングの詳細については、「[仮想ハブのルーティングについて](about-virtual-hub-routing.md)」を参照してください。
