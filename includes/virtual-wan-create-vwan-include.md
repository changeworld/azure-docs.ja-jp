---
title: ファイルを含める
description: インクルード ファイル
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 07/09/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 62d466e81309765540bcbd52714733b97d241ebc
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2020
ms.locfileid: "93354133"
---
ブラウザーから Azure portal に移動し、Azure アカウントでサインインします。

1. [仮想 WAN] ページに移動します。 ポータルで **+ [リソースの作成]** をクリックします。 検索ボックスに「**Virtual WAN**」と入力し、**Enter** キーを押します。
1. 結果から **[Virtual WAN]** を選択します。 [Virtual WAN] (仮想 WAN) ページで、 **[作成]** を選択して [WAN の作成] ページを開きます。
1. **[WAN の作成]** ページの **[基本]** タブで、次のフィールドに入力します。

   :::image type="content" source="./media/virtual-wan-create-vwan-include/basics.png" alt-text="スクリーンショットには、[基本] タブが選択された [WAN の作成] ペインが表示されています。":::

   * **[サブスクリプション]** - 使用するサブスクリプションを選択します。
   * **[リソース グループ]** - 新規作成するか、または既存のものを使用します。
   * **[リソース グループの場所]** - ドロップダウンからリソースの場所を選択します。 WAN はグローバルなリソースであり、特定のリージョンに存在するものではありません。 ただし、作成した WAN リソースを管理および検索するために、リージョンを選択する必要があります。
   * **[名前]** - WAN に付ける名前を入力します。
   * **[種類]** - Basic または Standard。 **[Standard]** を選択します。 Basic VWAN を選択する場合、Basic VWAN に含めることができるのは Basic ハブだけであり、接続の種類がサイト間に制限されることを理解しておいてください。
1. フィールドへの入力を完了したら、 **[確認および作成]** を選択します。
1. 検証に合格したら、 **[作成]** を選択して仮想 WAN を作成します。
