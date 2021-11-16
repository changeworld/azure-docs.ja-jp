---
author: cherylmc
ms.author: cherylmc
ms.date: 10/15/2021
ms.service: virtual-wan
ms.topic: include
ms.openlocfilehash: 5cac86b599e7e52cbc6018de6d97392bf28e4608
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131521329"
---
1. 作成した仮想 WAN を開きます。

1. 仮想 WAN に関連付けられている仮想ハブを選択してハブ ページを開きます。

1. ゲートウェイの種類の以下の順序に従って、すべてのゲートウェイ エンティティを削除します。 この処理は、完了までに 30 分ほどかかる場合があります。

    **VPN:**  
   1. VPN サイトの切断  
   1. VPN 接続を削除します  
   1. VPN ゲートウェイの削除  

    **ExpressRoute:**  
   1. ExpressRoute 接続を削除する  
   1. ExpressRoute ゲートウェイを削除する  

1. ハブはこの時点で削除することも、後でリソース グループを削除するときに削除することもできます。

1. 仮想 WAN に関連付けられているすべてのハブについて、この手順を繰り返します。

1. Azure portal でリソース グループに移動します。

1. **[リソース グループの削除]** を選択します。 これでハブと仮想 WAN を含め、リソース グループの内容がすべて削除されます。