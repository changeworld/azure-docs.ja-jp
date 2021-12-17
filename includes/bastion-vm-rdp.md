---
title: インクルード ファイル
description: インクルード ファイル
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: include
ms.date: 08/27/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 4561159b143f7cbaa9908aabb48e8a0dd08578be
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128627161"
---
1. [Azure portal](https://portal.azure.com) で、接続先の仮想マシンに移動します。 **[概要]** ページで **[接続]** を選択し、ドロップダウンから **[要塞]** を選択します。

   :::image type="content" source="./media/bastion-vm-rdp/connect.png" alt-text="Connect のスクリーンショット。" lightbox="./media/bastion-vm-rdp/connect.png":::

1. ドロップダウンから [Bastion] を選択すると、RDP、SSH、Bastion の 3 つのタブがあるサイド バーが表示されます。 Bastion が仮想ネットワーク用にプロビジョニングされていたため、[Bastion] タブが既定でアクティブになっています。 **[Bastion を使用する]** を選択します。

   :::image type="content" source="./media/bastion-vm-rdp/select-use-bastion.png" alt-text="[Bastion を使用する] の選択のスクリーンショット。":::

1. **[Azure Bastion を使用して接続する]** ページで、仮想マシン スケール セットのユーザー名とパスワードを入力し、 **[接続]** を選択します。

   :::image type="content" source="./media/bastion-vm-rdp/connect-vm-host.png" alt-text="[接続] ボタンのスクリーンショット。":::

1. Bastion を使用したこの仮想マシンへの RDP 接続は、ポート 443 と Bastion サービスを使用して (HTML5 を介して) Azure portal で直接開きます。 

   * 接続したとき、VM のデスクトップの外観は、サンプルのスクリーンショットとは違うものになることがあります。 
   * VM に接続しているとき、キーボード ショートカット キーを使用すると、ローカル コンピューターのショートカット キーとは同じ動作にならないことがあります。 たとえば、Windows クライアントから Windows VM に接続しているとき、CTRL + ALT + END は、ローカル コンピューターで CTRL + ALT + Delete のキーボード ショートカットになります。 Windows VM に接続しているとき、Mac からこれを行うには、キーボード ショートカットは Fn + CTRL + ALT + Backspace になります。

   :::image type="content" source="./media/bastion-vm-rdp/connection.png" alt-text="ポート 443 を使用している Connect のスクリーンショット。":::
