---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 07/15/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: a1abf76ee8e6c44eaa61d9de38b163f230327edd
ms.sourcegitcommit: 47ac63339ca645096bd3a1ac96b5192852fc7fb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2021
ms.locfileid: "114362312"
---
1. 仮想 WAN のページで、左側のウィンドウの **[ハブ]** を選択します。 **[ハブ]** ページで **[+ 新しいハブ]** を選択します。

   :::image type="content" source="media/virtual-wan-p2s-hub/new-hub.png" alt-text="新しいハブのスクリーンショット。":::

1. **[仮想ハブを作成する]** ページで、次のフィールドに入力します。

   * **[リージョン]** - 仮想ハブをデプロイするリージョンを選択します。
   * **[名前]** - 仮想ハブの呼び出しに使う名前を入力します。
   * **[ハブ プライベート アドレス空間]** - CIDR 表記のハブのアドレス範囲。

   :::image type="content" source="media/virtual-wan-p2s-hub/create-hub.png" alt-text="仮想ハブ作成のスクリーンショット。":::

1. **[ポイント対サイト]** タブで、次のフィールドを入力します。

   * **[ゲートウェイ スケール ユニット]** - ユーザー VPN ゲートウェイの合計容量を表します。
   * **[ポイント対サイト構成]** - 前の手順で作成した構成です。
   * **[クライアント アドレス プール]** - リモート ユーザーに使用されます。
   * **カスタム DNS サーバーの IP**
   * **ルーティング設定** - 適切な [ルーティング設定] を選択します。 Azure のルーティング優先設定を使用すると、Azure とインターネットにトラフィックをルーティングする方法を選択できます。 Microsoft ネットワーク経由または ISP ネットワーク (パブリック インターネット) 経由のどちらでトラフィックをルーティングするかを選択できます。 これらのオプションはそれぞれコールド ポテト ルーティングおよびホット ポテト ルーティングとも呼ばれます。 Virtual WAN 内のパブリック IP アドレスは、選択したルーティング オプションに基づいてサービスによって割り当てられます。 Microsoft ネットワークまたは ISP によるルーティング設定の詳細については、[ルーティングの基本設定](../articles/virtual-network/routing-preference-overview.md)に関する記事を参照してください。

   :::image type="content" source="media/virtual-wan-p2s-hub/create-point-to-site.png" alt-text="ポイント対サイトが選択された仮想ハブ構成のスクリーンショット。":::

1. **[Review + create]\(レビュー + 作成\)** を選択します。
1. **[検証に成功しました]** ページで **[作成]** を選択します。