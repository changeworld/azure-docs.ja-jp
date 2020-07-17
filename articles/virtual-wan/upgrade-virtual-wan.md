---
title: Azure Virtual WAN を Basic から Standard にアップグレードする - Azure Portal | Microsoft Docs
description: より多くの機能を使用するために、Virtual WAN の種類をアップグレードできます。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: cherylmc
ms.openlocfilehash: 83fd5bafb5496908403c50dc0e000fd33a836c95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "73511200"
---
# <a name="upgrade-a-virtual-wan-from-basic-to-standard"></a>Virtual WAN を Basic から Standard にアップグレードする

この記事は、Basic WAN を Standard WAN にアップグレードする際に役立ちます。 "Basic" WAN タイプでは、その内部にあるすべてのハブが Basic SKU ハブとして作成されます。 Basic ハブでは、サイト間 VPN 機能のみに限定されます。 "Standard" WAN タイプでは、その内部にあるすべてのハブが Standard SKU ハブとして作成されます。 Standard ハブを使用すると、ExpressRoute、ユーザー (ポイント対サイト) VPN、完全なメッシュ ハブ、Azure ハブを介した VNet 間の転送を有効にすることができます。

次の表では、各 WAN の種類で使用できる構成を示します。

[!INCLUDE [Basic and Standard SKUs](../../includes/virtual-wan-standard-basic-include.md)]

## <a name="to-change-the-virtual-wan-type"></a>Virtual WAN の種類を変更するには

1. Virtual WAN のページで **[構成]** を選択し、[構成] ページを開きます。

   ![Virtual WAN のダイアグラム](./media/upgrade-virtual-wan/1.png)
2. Virtual WAN の種類として、ドロップダウンから **Standard** を選択します。

   ![Virtual WAN のダイアグラム](./media/upgrade-virtual-wan/2.png)
3. Standard Virtual WAN にアップグレードすると、Basic Virtual WAN に戻すことができなくなることに注意してください。 アップグレードする場合は、 **[確認]** を選択します。

   ![Virtual WAN のダイアグラム](./media/upgrade-virtual-wan/4.png)
4. 変更が保存されると、Virtual WAN ページは次の例のようになります。

   ![Virtual WAN のダイアグラム](./media/upgrade-virtual-wan/5.png)

## <a name="next-steps"></a>次のステップ

Virtual WAN の詳細については、[Virtual WAN の概要](virtual-wan-about.md)に関するページを参照してください。