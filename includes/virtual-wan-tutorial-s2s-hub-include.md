---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 06/23/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: aacd6a780562476ea338b4c08795e14b6ba39760
ms.sourcegitcommit: 54d8b979b7de84aa979327bdf251daf9a3b72964
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/24/2021
ms.locfileid: "112579169"
---
1. 作成した仮想 WAN を見つけます。 仮想 WAN ページの **[接続]** セクションで、 **[ハブ]** を選択します。
1. **[ハブ]** ページで、 **[+ 新しいハブ]** を選択して、 **[仮想ハブを作成する]** ページを開きます。

   :::image type="content" source="./media/virtual-wan-tutorial-hub-include/basics.png" alt-text="スクリーンショットには、[基本] タブが選択された [仮想ハブの作成] ペインが表示されています。" border="false":::
1. **[仮想ハブを作成する]** ページの **[基本]** タブで、次のフィールドを入力します。

   * **[リージョン]** (旧称は [場所])
   * **名前**
   * **[ハブ プライベート アドレス空間]** - ハブを作成するための最小アドレス空間は /24 です。 /25 から /32 の範囲で使用すると、作成中にエラーが発生します。 仮想ハブ内のサービスのサブネット アドレス空間を明示的に計画する必要はありません。 Azure Virtual WAN はマネージド サービスであるため、異なるゲートウェイまたはサービス (VPN ゲートウェイ、ExpressRoute ゲートウェイ、ユーザー VPN またはポイント対サイト ゲートウェイ、ファイアウォール、ルーティングなど) に対して、適切なサブネットが仮想ハブに作成されます。
