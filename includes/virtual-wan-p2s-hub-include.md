---
ms.author: cherylmc
author: cherylmc
ms.date: 07/30/2021
ms.service: virtual-wan
ms.topic: include
ms.openlocfilehash: b5e80ecb8f5d0896abb4be61d60d2fbdbea77fa8
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121734700"
---
1. **仮想 WAN** のページで、左側のウィンドウの **[ハブ]** を選択します。 **[ハブ]** ページで **[+ 新しいハブ]** を選択します。

   :::image type="content" source="media/virtual-wan-p2s-hub/new-hub.png" alt-text="新しいハブのスクリーンショット。":::

1. **[仮想ハブを作成する]** ページの **[基本]** タブを表示します。

   :::image type="content" source="media/virtual-wan-p2s-hub/create-hub.png" alt-text="仮想ハブ作成のスクリーンショット。":::

1. **[基本]** タブで次の設定を構成します。

   * **[リージョン]** - 仮想ハブをデプロイするリージョンを選択します。
   * **[名前]** - 仮想ハブの呼び出しに使う名前を入力します。
   * **[ハブ プライベート アドレス空間]** - CIDR 表記のハブのアドレス範囲。

1. **[ポイント対サイト]** タブをクリックして、ポイント対サイトの構成ページを開きます。 ポイント対サイトの設定を表示するには、 **[はい]** をクリックします。

   :::image type="content" source="media/virtual-wan-p2s-hub/create-point-to-site.png" alt-text="ポイント対サイトが選択された仮想ハブ構成のスクリーンショット。":::

1. 次の設定を構成します。

   * **[ゲートウェイ スケール ユニット]** - ユーザー VPN ゲートウェイの合計容量を表します。 40 以上のゲートウェイ スケール ユニットを選択した場合は、それに応じてクライアント アドレス プールを計画します。 この設定がクライアント アドレス プールに与える影響の詳細については、[クライアント アドレス プールについて](../articles/virtual-wan/about-client-address-pools.md)の記事を参照してください。
   * **[ポイント対サイト構成]** - 前の手順で作成したユーザー VPN 構成を選択します。
   * **[ルーティングの優先順位]** - Azure のルーティング設定を使用すると、Azure とインターネットの間でトラフィックをルーティングする方法を選択できます。 Microsoft ネットワーク経由または ISP ネットワーク (パブリック インターネット) 経由のどちらでトラフィックをルーティングするかを選択できます。 これらのオプションはそれぞれコールド ポテト ルーティングおよびホット ポテト ルーティングとも呼ばれます。 Virtual WAN 内のパブリック IP アドレスは、選択したルーティング オプションに基づいてサービスによって割り当てられます。 Microsoft ネットワークまたは ISP によるルーティング設定の詳細については、[ルーティングの基本設定](../articles/virtual-network/routing-preference-overview.md)に関する記事を参照してください。
   * **[Client address pool]\(クライアント アドレス プール\)** - このアドレス プールから IP アドレスが VPN クライアントに自動的に割り当てられます。 詳細については、[クライアント アドレス プール](../articles/virtual-wan/about-client-address-pools.md)に関する記事を参照してください。
   * **[Custom DNS Servers]\(カスタム DNS サーバー\)** - クライアントが使用する DNS サーバーの IP アドレス。 最大で 5 つまで指定できます。

1. **[確認および作成]** を選択して設定を検証します。

1. 検証に合格した場合は、**[作成]** を選択します。 ハブの作成は、完了するまでに 30 分以上かかることがあります。