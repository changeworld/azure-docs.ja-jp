---
ms.author: cherylmc
author: cherylmc
ms.date: 08/19/2021
ms.service: virtual-wan
ms.topic: include
ms.openlocfilehash: 6dea9948a85278c236c704562d194f18c962683b
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/24/2021
ms.locfileid: "122778534"
---
1. ポータルの **[リソースの検索]** バーで、検索ボックスに「**Virtual WAN**」と入力し、**Enter** キーを押します。

1. 検索結果から、 **[Virtual WAN]** を選択します。 [Virtual WAN] ページで **[+ 作成]** を選択し、 **[WAN の作成]** ページを開きます。

1. **[WAN の作成]** ページの **[基本]** タブで、フィールドに入力します。 例の値を変更して、お使いの環境に適用します。

   :::image type="content" source="./media/virtual-wan-create-vwan-include/basics.png" alt-text="スクリーンショットには、[基本] タブが選択された [WAN の作成] ペインが表示されています。":::

   * **サブスクリプション**: 使用するサブスクリプションを選択します。
   * **リソース グループ**: 新規作成するか、または既存のものを使用します。
   * **リソース グループの場所**: ドロップダウンからリソースの場所を選択します。 WAN はグローバルなリソースであり、特定のリージョンに存在するものではありません。 ただし、作成した WAN リソースを管理および検索するために、リージョンを選択する必要があります。
   * **名前**: 仮想 WAN に付ける名前を入力します。
   * **種類**: Basic または Standard。 **[Standard]** を選択します。 [Basic] を選択した場合には、Basic 仮想 WAN は、Basic ハブのみを含むことができます。 Basic ハブは、サイト間接続にのみ使用できます。

1. ページの下部でフィールドへの入力を完了したら、 **[確認と作成]** を選択します。

1. 検証に合格したら、 **[作成]** をクリックして仮想 WAN を作成します。
