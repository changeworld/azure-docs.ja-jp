---
author: cherylmc
ms.author: cherylmc
ms.date: 06/29/2021
ms.service: bastion
ms.topic: include
ms.openlocfilehash: f0585ca5bc2a76cb7f969f8a599ee98152b872cf
ms.sourcegitcommit: 98308c4b775a049a4a035ccf60c8b163f86f04ca
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/30/2021
ms.locfileid: "113108239"
---
Azure Bastion を使用して VM に接続するときは、VM のパブリック IP アドレスは必要ありません。 パブリック IP アドレスを他に使用していない場合は、VM との関連付けを解除できます。 パブリック IP アドレスの VM との関連付けを解除するには、次の手順に従います。

1. 仮想マシンに移動し、 **[ネットワーク]** を選択します。 **[NIC パブリック IP]** を選択して、パブリック IP アドレス ページを開きます。

   :::image type="content" source="./media/bastion-remove-ip/networking.png" alt-text="[ネットワーク] ページのスクリーンショット。":::

1. VM の **[パブリック IP アドレス]** ページで **[関連付け解除]** を選択します。

   :::image type="content" source="./media/bastion-remove-ip/disassociate.png" alt-text="VM のパブリック IP アドレスのスクリーンショット。":::

1. **[はい]** を選択して、ネットワーク インターフェイスから IP アドレスの関連付けを解除します。

   :::image type="content" source="./media/bastion-remove-ip/disassociate-yes.png" alt-text="パブリック IP アドレスの関連付けを解除する画面のスクリーンショット。":::

1. IP アドレスの関連付けを解除した後、パブリック IP アドレス リソースを削除できます。 パブリック IP アドレス リソースを削除するには、リソース グループに移動し、削除したい IP アドレス リソースを探します。 次に、 **[削除]** を選択してリソースを削除します。

   :::image type="content" source="./media/bastion-remove-ip/delete-resource.png" alt-text="パブリック IP アドレス リソースを削除する画面のスクリーンショット。":::