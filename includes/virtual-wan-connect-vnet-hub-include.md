---
author: cherylmc
ms.author: cherylmc
ms.date: 08/17/2021
ms.service: virtual-wan
ms.topic: include
ms.openlocfilehash: 563de3b6c57f38d63f2759c62fccc5a17d17dae9
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/24/2021
ms.locfileid: "122778519"
---
1. お使いの **Virtual WAN** に移動します。

1. **[仮想ネットワーク接続]** を選択します。

1. 仮想ネットワーク接続のページで、 **[+ 接続の追加]** を選択します。

   :::image type="content" source="./media/virtual-wan-connect-vnet-hub/add.png" alt-text="追加を示すスクリーンショット。":::

1. **[接続の追加]** ページで、必要な設定を構成します。 ルーティング設定の詳細については、「[ルーティングについて](../articles/virtual-wan/about-virtual-hub-routing.md)」を参照してください。
 
   :::image type="content" source="./media/virtual-wan-connect-vnet-hub/connection.png" alt-text="VNet 接続ページを示すスクリーンショット。":::

   * **接続名**: 接続に名前を付けます。
   * **ハブ**: この接続に関連付けるハブを選択します。
   * **サブスクリプション**:サブスクリプションを確認します。
   * **リソース グループ**: VNet が含まれるリソース グループ。
   * **仮想ネットワーク**: このハブに接続する仮想ネットワークを選択します。 既存の仮想ネットワーク ゲートウェイがない仮想ネットワークを選択してください。
   * **[なし] に伝達**: 既定では、これは **[いいえ]** に設定されています。 このスイッチを **[はい]** に変更すると、 **[ルート テーブルへ伝達]** および **[ラベルへ伝達]** の構成オプションが構成できなくなります。
   * **ルート テーブルを関連付ける**: 関連付けるルート テーブルを選択できます。
   * **静的ルート**: この設定を使用してネクスト ホップを指定できます。

1. 構成する設定が完了したら、 **[作成]** を選択して接続を作成します。