---
title: インクルード ファイル
description: インクルード ファイル
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/14/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 410570302eec418f1e4bcb75d6413936a96b5171
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "77471542"
---
条件付きアクセスを使用すると、アプリケーションごとにきめ細かなアクセス制御を行うことができます。 条件付きアクセスを利用するには、条件付きアクセス規則の対象となるユーザーに対して Azure AD Premium 1 以上のライセンスが適用されている必要があります。

1. **[Enterprise Applications - すべてのアプリケーション]** ページに移動し、**Azure VPN** をクリックします。

   - **[条件付きアクセス]** をクリックします。
   - **[新しいポリシー]** をクリックして **[新規]** ウィンドウを開きます。
2. **新規**ペインで、**割り当て -> ユーザーとグループ** に移動します。 **ユーザーとグループ ->** **含める** タブで次の操作を行います。

   - **[ユーザーとグループの選択]** をクリックします。
   - **[ユーザーとグループ]** をクリックします。
   - **[Select]** をクリックして、MFA の影響を受けるグループまたはユーザーのセットを選択します。
   - **[Done]** をクリックします。

   ![代入](./media/vpn-gateway-vwan-openvpn-azure-ad-mfa/mfa-ca-assignments.png)
3. **新規** ペインで、**アクセスの制御 -> 付与** ペインに移動します。

   - **[アクセス権の付与]** をクリックします。
   - **[多要素認証が必要です]** をクリックします。
   - **[選択したコントロールすべてが必要]** をクリックします。
   - **[選択]** をクリックします。
   
   ![アクセス権の付与 - MFA](./media/vpn-gateway-vwan-openvpn-azure-ad-mfa/mfa-ca-grant-mfa.png)
4. **ポリシーを有効化する** セクション:

   - **[ON]** を選択します。
   - **Create** をクリックしてください。

   ![ポリシーを有効化する](./media/vpn-gateway-vwan-openvpn-azure-ad-mfa/mfa-ca-enable-policy.png)