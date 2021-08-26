---
author: cherylmc
ms.author: cherylmc
ms.date: 07/30/2021
ms.service: virtual-wan
ms.topic: include
ms.openlocfilehash: 21bda32ddb1c87bb07b6679499e648e0b2f7a809
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121734672"
---
1. 作成した仮想 WAN に移動します。 

1. 左側のメニューから、 **[ユーザー VPN 構成]** を選択します。

1. **[ユーザー VPN 構成]** ページで、 **[+ ユーザー VPN 構成の作成]** を選択します。

   :::image type="content" source="media/virtual-wan-p2s-configuration/user-vpn.png" alt-text="[ユーザー VPN 構成] ページのスクリーンショット":::

1. **[新しいユーザー VPN 構成を作成する]** ページの **[基本]** タブにある **[インスタンスの詳細]** で、VPN 構成に割り当てる **名前** を入力します。 

   :::image type="content" source="media/virtual-wan-p2s-configuration/custom.png" alt-text="カスタムへの IPsec スイッチのスクリーンショット":::

1. **[トンネルの種類]** で、目的のトンネルの種類をドロップダウンから選択します。 トンネルの種類のオプションは、 **[IKEv2 VPN]、[OpenVPN]** 、 **[OpenVpn and IkeV2]** です。 トンネルの種類ごとに、必要な設定が異なります。

   **要件とパラメーター**:

     **IKEv2 VPN**

     * **要件:** トンネルの種類に **[IKEv2]** を選択すると、認証方法を選択するように指示するメッセージが表示されます。 IKEv2 の場合、指定できる認証方法は 1 つのみです。 Azure 証明書、Azure Active Directory、または RADIUS ベースの認証を選択できます。

     * **IPsec カスタム パラメーター:** IKE フェーズ 1 および IKE フェーズ 2 のパラメーターをカスタマイズするには、IPsec スイッチを **[カスタム]** に切り替えてパラメーター値を選択します。 カスタマイズできるパラメーターの詳細については、[カスタムの IPsec](../articles/virtual-wan/point-to-site-ipsec.md) に関する記事を参照してください。

     **OpenVPN**

     * **要件:** トンネルの種類に **[OpenVPN]** を選択すると、認証メカニズムを選択するように指示するメッセージが表示されます。 トンネルの種類として [OpenVPN] が選択されている場合は、複数の認証方法を指定できます。 Azure 証明書、Azure Active Directory、または RADIUS ベースの認証の任意のサブセットを選択できます。 RADIUS ベースの認証の場合、セカンダリ RADIUS サーバーの IP アドレスとサーバー シークレットを指定できます。

1. 使用する **認証** 方法を構成します。 各認証方法は、 **[Azure 証明書]** 、 **[RADIUS 認証]** 、 **[Azure Active Directory]** のそれぞれ個別のタブにあります。 一部の認証方法は、特定のトンネルの種類でのみ使用できます。

   構成する認証方法のタブで、 **[はい]** を選択して、使用可能な構成設定を表示します。

   :::image type="content" source="media/virtual-wan-p2s-configuration/certificate-no.png" alt-text="[いいえ] 画面のスクリーンショット - [はい] が強調表示されています。":::

   * **例 - 証明書認証**

      :::image type="content" source="media/virtual-wan-p2s-configuration/certificate-authentication.png" alt-text="[はい] が選択されているスクリーンショット。":::

   * **例 - RADIUS 認証**

      :::image type="content" source="media/virtual-wan-p2s-configuration/radius-authentication.png" alt-text="RADIUS 認証ページのスクリーンショット。":::

   * **例 - Azure Active Directory 認証**

      :::image type="content" source="media/virtual-wan-p2s-configuration/azure-active-directory.png" alt-text="Azure Active Directory 認証ページ。":::

1. 設定の構成が完了したら、ページの下部にある **[確認と作成]** をクリックします。

1. **[作成]** をクリックしてユーザー VPN 構成を作成します。
