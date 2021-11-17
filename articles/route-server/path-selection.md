---
title: Azure Route Server でのパスの選択
description: Azure Route Server でネットワーク仮想アプライアンスのパス選択を有効にする方法について説明します。
services: route-server
author: duongau
ms.service: route-server
ms.topic: conceptual
ms.date: 11/09/2021
ms.author: duau
ms.openlocfilehash: 011156427bfaed238679d4fc4b4e6d51e566ec6e
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132350018"
---
# <a name="path-selection-with-azure-route-server"></a>Azure Route Server でのパスの選択

Azure Route Server を使用すると、インターネット経由で SDWAN NVA を介してオンプレミス ネットワークにルーティングするようにトラフィックを制御できます。 この記事では、Azure Route Server でパスの選択を有効にする方法について説明します。これにより、SNWAN NVA を構成して、オンプレミスのネットワークと通信するときの[ルーティング設定](../virtual-network/ip-services/routing-preference-overview.md)を定義できます。

## <a name="how-does-it-work"></a>それはどのように機能するのでしょうか。

:::image type="content" source="./media/path-selection/routing-preference.png" alt-text="Azure Route Server とインターネット ルーティング IP を使用した SDWAN の図。":::

### <a name="cold-potato-routing"></a>コールド ポテト ルーティング

Azure Route Server と同じ仮想ネットワークにデプロイされた SDWAN NVA は Microsoft ネットワーク IP アドレスで構成されます。 オンプレミスへのトラフィック パスは、Microsoft グローバル ネットワークを使用し、宛先に最も近い Microsoft ネットワークから出ます。 オンプレミスからのルーティングでは、戻りパスでユーザーに最も近い Microsoft ネットワークに入ります。 このルーティング方法はパフォーマンスを最適にするため、コストにおいて可能な限り最善のエクスペリエンスを提供します。 

### <a name="hot-potato-routing"></a>ホット ポテト ルーティング

コストを最適化する方法として 2 つ目のルーティング方法は、インターネット IP を使用して SDWAN NVA を割り当てることで実現します。 トラフィックがオンプレミスにルーティングされると、サービスがホストされているのと同じリージョンにある Microsoft ネットワークを出ます。 その後、ISP のネットワークを使用してインターネット経由でルーティングされます。 オンプレミスからのルーティングは、ホストされているサービス リージョンに最も近い Microsoft ネットワークに入ります。 このルーティング方法は、大量のデータの転送などのタスクを完了する場合に全体価格が最善となります。

## <a name="how-to-enable-routing-preference"></a>ルーティング設定を有効にする方法

新しいパブリック IP アドレスを作成するときに、 *[ルーティングの優先順位]* として **[インターネット]** を選択します。 次に、パブリック IP アドレスを SDWAN NVA に割り当てます。

:::image type="content" source="./media/path-selection/internet-ip.png" alt-text="パブリック IP アドレスのインターネット ルーティング設定のスクリーンショット。":::

Microsoft では、Microsoft ネットワークとインターネットの両方を使用して接続ソリューションを実装し、環境に回復性の層をさらに追加することをお勧めします。

## <a name="next-steps"></a>次のステップ

- [Azure Route Server](route-server-faq.md) に関する詳細情報を確認します。
- [Azure Route Server を構成する](quickstart-configure-route-server-powershell.md)方法を確認します。
