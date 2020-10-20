---
title: インクルード ファイル
description: インクルード ファイル
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: include
ms.date: 10/12/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: b079ede0845de3794507691bc3c252930e2a6604
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/13/2020
ms.locfileid: "91977177"
---
1. [Azure Portal](https://portal.azure.com)を開きます。 接続先の仮想マシンに移動し、 **[接続]** を選択します。 ドロップダウンから **[Bastion]** を選択します。

   :::image type="content" source="./media/bastion-vm-rdp/connect-vm.png" alt-text="[Bastion] を選択する" border="false":::

1. ドロップダウンから [Bastion] を選択すると、RDP、SSH、Bastion の 3 つのタブがあるサイド バーが表示されます。 Bastion が仮想ネットワーク用にプロビジョニングされていたため、[Bastion] タブが既定でアクティブになっています。 **[Bastion を使用する]** を選択します。

   :::image type="content" source="./media/bastion-vm-rdp/use-bastion.png" alt-text="[Bastion] を選択する" border="false":::

1. **[Azure Bastion を使用して接続する]** ページで、仮想マシン スケール セットのユーザー名とパスワードを入力し、 **[接続]** を選択します。

   :::image type="content" source="./media/bastion-vm-rdp/host.png" alt-text="[Bastion] を選択する" border="false":::

1. Bastion を使用したこの仮想マシンへの RDP 接続は、ポート 443 と Bastion サービスを使用して (HTML5 を介して) Azure portal で直接開きます。

   :::image type="content" source="./media/bastion-vm-rdp/connection.png" alt-text="[Bastion] を選択する" border="false":::
