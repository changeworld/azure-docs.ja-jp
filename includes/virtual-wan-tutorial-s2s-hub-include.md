---
ms.author: cherylmc
author: cherylmc
ms.date: 08/17/2021
ms.service: virtual-wan
ms.topic: include
ms.openlocfilehash: c0fdadad76480880ac930e1f472def26f8f1e904
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/17/2021
ms.locfileid: "122321916"
---
1. 作成した仮想 WAN を見つけます。 仮想 WAN ページの **[接続]** セクションで、 **[ハブ]** を選択します。
1. **[ハブ]** ページで、 **[+ 新しいハブ]** を選択して、 **[仮想ハブを作成する]** ページを開きます。

   :::image type="content" source="./media/virtual-wan-tutorial-hub-include/basics.png" alt-text="スクリーンショットには、[基本] タブが選択された [仮想ハブの作成] ペインが表示されています。" border="false":::
1. **[仮想ハブを作成する]** ページの **[基本]** タブで、次のフィールドを入力します。

   * **リージョン**: この設定は、以前は [場所] でした。 これは、仮想ハブを作成するリージョンです。
   * **名前**: 仮想ハブに付ける名前。
   * **ハブ プライベート アドレス空間**: ハブを作成するための最小アドレス空間は /24 です。 /25 から /32 の範囲で使用すると、作成中にエラーが発生します。 仮想ハブ内のサービスのサブネット アドレス空間を明示的に計画する必要はありません。 Azure Virtual WAN はマネージド サービスであるため、異なるゲートウェイまたはサービス (VPN ゲートウェイ、ExpressRoute ゲートウェイ、ユーザー VPN またはポイント対サイト ゲートウェイ、ファイアウォール、ルーティングなど) に対して、適切なサブネットが仮想ハブに作成されます。
