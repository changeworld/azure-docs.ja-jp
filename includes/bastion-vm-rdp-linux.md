---
author: cherylmc
ms.author: cherylmc
ms.date: 08/30/2021
ms.service: virtual-wan
ms.topic: include
ms.openlocfilehash: c52c00dd8fa834775a01162e2506f1821b0dcdae
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/19/2021
ms.locfileid: "130173350"
---
1. [Azure portal](https://portal.azure.com) で、接続先の仮想マシンに移動します。 **[概要]** ページで **[接続]** を選択し、ドロップダウンから **[要塞]** を選択します。

   :::image type="content" source="./media/bastion-vm-rdp/connect.png" alt-text="[接続] のスクリーンショット。" lightbox="./media/bastion-vm-rdp/connect.png":::

1. [Bastion] を選択してから **[Bastion を使用する]** を選択します。 Bastion を仮想ネットワーク用にプロビジョニングしていない場合は、[Bastion の構成](../articles/bastion/quickstart-host-portal.md)に関する記事をご覧ください。

   :::image type="content" source="./media/bastion-vm-rdp/select-use-bastion.png" alt-text="[Bastion を使用する] を示すスクリーンショット。":::

1. **[Azure Bastion を使用して接続する]** ページで、 **[接続の設定]** セクションを展開し、 **[RDP]** を選択します。 標準の RDP ポート (3389) とは異なる受信ポートを使用する予定の場合は、 **[ポート]** を入力します。

   :::image type="content" source="./media/bastion-connect-vm-rdp-linux/connection-settings.png" alt-text="ポートを示すスクリーンショット。" lightbox="./media/bastion-connect-vm-rdp-linux/connection-settings.png":::

1. **[ユーザー名]** と **[パスワード]** を入力し、 **[接続]** を選択して VM に接続します。

   :::image type="content" source="./media/bastion-connect-vm-rdp-linux/username-password.png" alt-text="[接続] をクリックしているスクリーンショット。" lightbox="./media/bastion-connect-vm-rdp-linux/username-password.png":::

1. Bastion を使用したこの仮想マシンへの RDP 接続は、ポート 443 と Bastion サービスを使用して (HTML5 を介して) Azure portal で直接開きます。 

   VM に接続しているとき、キーボード ショートカット キーを使用すると、ローカル コンピューターのショートカット キーとは同じ動作にならないことがあることに注意してください。 たとえば、Windows クライアント コンピューターから Windows VM に接続すると、"CTRL + ALT + END" は "CTRL + ALT + Delete" のキーボード ショートカットになります。 Mac クライアント コンピューターから Windows VM に接続すると、キーボード ショートカットは "Fn + CTRL + ALT + Backspace" になります。
