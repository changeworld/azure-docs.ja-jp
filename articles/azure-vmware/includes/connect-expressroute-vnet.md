---
title: ExpressRoute を仮想ネットワーク ゲートウェイに接続する
description: ExpressRoute を仮想ネットワーク ゲートウェイに接続する手順。
ms.topic: include
ms.date: 12/08/2020
ms.openlocfilehash: 115e8829723c25fb1644f3f5652fbace529a05cb
ms.sourcegitcommit: ad921e1cde8fb973f39c31d0b3f7f3c77495600f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/25/2021
ms.locfileid: "107945687"
---
<!-- Used in deploy-azure-vmware-solution.md and tutorial-configure-networking.md -->


1. ExpressRoute 承認キーを要求します。

   [!INCLUDE [request-authorization-key](request-authorization-key.md)]

1. 使用する予定の仮想ネットワーク ゲートウェイに移動し、 **[接続]**  >  **[+ 追加]** の順に選択します。

1. **[接続の追加]** ページで、各フィールドの値を指定し、 **[OK]** を選択します。 

   | フィールド | 値 |
   | --- | --- |
   | **名前**  | 接続名を入力します。  |
   | **接続の種類**  | **[ExpressRoute]** を選択します。  |
   | **承認の利用**  | このボックスが選択されていることを確認します。  |
   | **仮想ネットワーク ゲートウェイ** | 使用する仮想ネットワーク ゲートウェイ。  |
   | **承認キー**  | 前にコピーした承認キーを貼り付けます。 |
   | **ピア回線の URI**  | 前にコピーした ExpressRoute ID を貼り付けます。  |

   :::image type="content" source="../media/expressroute-global-reach/expressroute-add-connection.png" alt-text="ExpressRoute を仮想ネットワークゲートウェイに接続するための [接続の追加] ページのスクリーンショット。":::

ExpressRoute 回線と仮想ネットワークの間の接続が作成されます。

:::image type="content" source="../media/expressroute-global-reach/virtual-network-gateway-connections.png" alt-text="仮想ネットワーク ゲートウェイ接続のスクリーンショット。":::