---
ms.author: cherylmc
author: cherylmc
ms.date: 08/19/2021
ms.service: virtual-wan
ms.topic: include
ms.openlocfilehash: f5d641a1d017f40081b311e9b9b610b7fcc7c13c
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/24/2021
ms.locfileid: "122820895"
---
1. 作成した仮想 WAN を探します。 [仮想 WAN] ページの **[接続]** セクションで、 **[ハブ]** を選択します。 **[新しいハブ]** をクリックして **[仮想ハブを作成する]** ページを開きます。

   :::image type="content" source="media/virtual-wan-empty-hub/new-hub.jpg" alt-text="[Hubs configuration]\(ハブの構成\) ダイアログ ボックスを示すスクリーンショット。[新しいハブ] が選択されています。":::

1. **[仮想ハブを作成する]** ページで、フィールドに入力します。

   :::image type="content" source="media/virtual-wan-tutorial-er-hub/create-hub.png" alt-text="スクリーンショットには、値を入力できる [基本] タブが表示されています。":::

   * **[リージョン]** : 仮想ハブをデプロイするリージョンを選択します。
   * **[名前]** : 仮想ハブに付ける名前。
   * **[ハブ プライベート アドレス空間]** : CIDR 表記のハブのアドレス範囲。

1. **[ExpressRoute] タブ** を選択します。 **[はい]** をクリックして設定を表示し、フィールドに入力します。 ゲートウェイ スケール ユニットについては、[よくあるご質問](../articles/virtual-wan/virtual-wan-faq.md#what-are-virtual-wan-gateway-scale-units)に関する記事を参照してください。

   :::image type="content" source="media/virtual-wan-tutorial-er-hub/expressroute.png" alt-text="スクリーンショットには、値を入力できる [ExpressRoute] タブが表示されています。":::

1. **[確認と作成]** を選択して検証します。
1. **[作成]** を選択し、ExpressRoute ゲートウェイを使用してハブを作成します。 ハブは完了までに 30 分程度かかる場合があります。 30 分後に **[更新]** を実行して、 **[ハブ]** ページでハブを確認します。 **[リソースに移動]** を選択してリソースに移動します。