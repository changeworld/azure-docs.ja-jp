---
title: 'Azure ExpressRoute: Azure portal を使用して回線のピアリングをリセットする'
description: Azure portal を使用して Azure ExpressRoute 回線のピアリングを無効および有効にする方法について説明します。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 11/30/2020
ms.author: duau
ms.openlocfilehash: d4d6b0b0cce4f5304f7c5790ef2bda05633be52f
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/03/2020
ms.locfileid: "96581313"
---
# <a name="reset-expressroute-circuit-peerings-use-the-azure-portal"></a>Azure portal を使用して ExpressRoute 回線のピアリングをリセットする

この記事では、Azure portal を使用して ExpressRoute 回線のピアリングを無効および有効にする方法について説明します。 ピアリングを無効にすると、ExpressRoute 回線のプライマリとセカンダリ接続の両方の BGP セッションがシャットダウンされます。 Microsoft へのこのピアリング経由の接続が失われます。 ピアリングを有効にすると、ExpressRoute 回線のプライマリとセカンダリ接続の両方の BGP セッションが起動されます。 Microsoft へのこのピアリング経由の接続が回復します。 ExpressRoute 回線上の Microsoft ピアリングと Azure プライベート ピアリングを独立に有効および無効にすることができます。 ExpressRoute 回線上のピアリングを初めて構成すると、そのピアリングは既定で有効になります。

ExpressRoute ピアリングをリセットすると役立つ可能性のあるシナリオがいくつかの存在します。
* ディザスター リカバリーの設計と実装をテストします。 たとえば、2 つの ExpressRoute 回線があるとします。 1 つの回線のピアリングを無効にして、ネットワーク トラフィックをもう一方の回線に強制的にフェールオーバーするようにできます。
* ExpressRoute 回線の Azure プライベート ピアリングまたは Microsoft ピアリングで BFD (Bidirectional Forwarding Detection) を有効にします。 BFD は、Azure プライベート ピアリングでは ExpressRoute 回線が 2018 年 8 月 1 日より後に作成された場合、また Microsoft ピアリングでは ExpressRoute 回線が 2020 年 1 月 10 日より後に作成された場合、既定で有効です。 If your ExpressRoute circuit gets created after January 10 2020. 回線がその前に作成された場合、BFD は有効になっていません。 ピアリングを無効にし、再度有効にすることによって、BFD を有効にすることができます。 

### <a name="sign-in-to-the-azure-portal"></a>Azure portal にサインインする

ブラウザーから [Azure ポータル](https://portal.azure.com) に移動し、Azure アカウントでサインインします。

## <a name="reset-a-peering"></a>ピアリングをリセットする

1. ピアリング構成の変更を行う回線を選択します。

    :::image type="content" source="./media/expressroute-howto-reset-peering-portal/expressroute-circuit-list.png" alt-text="ExpressRoute 回線の一覧":::

1. 有効または無効にするピアリング構成を選択します。

    :::image type="content" source="./media/expressroute-howto-reset-peering-portal/expressroute-circuit.png" alt-text="ExpressRoute 回線の概要":::

1. **[Enable Peering]\(ピアリングを有効にする\)** チェックボックスをオフにし、 **[保存]** を選択してピアリング構成を無効にします。

    :::image type="content" source="./media/expressroute-howto-reset-peering-portal/disable-peering.png" alt-text="プライベート ピアリングを無効にする":::

1. ピアリングを再び有効にするには、 **[Enable Peering]\(ピアリングを有効にする\)** チェックボックスをオンにし、 **[保存]** を選択します。

## <a name="next-steps"></a>次のステップ
ExpressRoute の問題をトラブルシューティングするためにサポートが必要な場合は、次の記事を確認してください。
* [ExpressRoute 接続の確認](expressroute-troubleshooting-expressroute-overview.md)
* [ネットワーク パフォーマンスのトラブルシューティング](expressroute-troubleshooting-network-performance.md)
