---
title: Azure Virtual WAN のカスタム IPsec ポリシーを構成する:ポータル | Microsoft Docs
description: ポータルを使用して Azure Virtual WAN のカスタム IPsec ポリシーを構成する方法について説明します。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: cherylmc
ms.openlocfilehash: f37d7f3bfac37253339aab3493fb2c444900e099
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "73511180"
---
# <a name="configure-a-custom-ipsec-policy-for-virtual-wan-using-the-portal"></a>ポータルを使用して Virtual WAN のカスタム IPsec ポリシーを構成する

Azure portal で Virtual WAN のカスタム IPsec ポリシーを構成できます。 カスタム ポリシーは、両側 (オンプレミスと Azure VPN ゲートウェイ) で、IKE フェーズ 1 および IKE フェーズ 2 に同じ設定を使用する場合に便利です。

## <a name="working-with-custom-policies"></a>カスタム ポリシーの使用

[!INCLUDE [IPsec](../../includes/virtual-wan-ipsec-custom-include.md)]

## <a name="configure-a-policy"></a>ポリシーを構成する

1. **仮想ハブを見つけます**。 ブラウザーから [Azure ポータル](https://aka.ms/azurevirtualwanpreviewfeatures) に移動し、Azure アカウントでサインインします。 サイトの仮想ハブを見つけます。
2. **VPN サイトを選択します**。 ハブ ページで、カスタム ポリシーを設定する VPN サイトを選択します。

   ![select](./media/virtual-wan-custom-ipsec-portal/locate.png)
3. **VPN 接続を編集します**。 **コンテキスト メニュー** ( **...** ) から、 **[VPN 接続を編集する]** を選択します。

   ![編集](./media/virtual-wan-custom-ipsec-portal/contextmenu.png)
4. **設定を構成します**。 **[VPN 接続を編集する]** ページで、設定を構成 ます。 **[Save]\(保存\)** を選択して設定を保存します。

   ![構成して保存する](./media/virtual-wan-custom-ipsec-portal/edit.png)

## <a name="next-steps"></a>次のステップ

Virtual WAN の詳細については、[Virtual WAN の概要](virtual-wan-about.md)に関するページを参照してください。