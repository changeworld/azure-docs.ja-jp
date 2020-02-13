---
title: VPN ユーザー用の MFA を有効にする：Azure AD 認証
description: VPN ユーザー用の多要素認証を有効にします。
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: alzam
ms.openlocfilehash: 771dea2d9ae2979bc71880368ed3a9a538e8a803
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/02/2020
ms.locfileid: "76964724"
---
# <a name="enable-azure-multi-factor-authentication-mfa-for-vpn-users"></a>VPN ユーザーの Azure 多要素認証 (MFA)を有効にします。

アクセスを許可する前に、ユーザーに認証の第 2 要素を求めるようにする場合は、ユーザーごとに Azure Multi-Factor Authentication (MFA) を構成するか、さらに細かく制御するために[条件付きアクセス](../active-directory/conditional-access/overview.md)を使用した Multi-Factor Authentication (MFA) を活用することができます。 ユーザーごとの Multi-Factor Authentication の構成は、追加コストなしで有効にすることができます。しかし、ユーザーごとに MFA を有効にする場合、ユーザーは、Azure AD テナントに関連付けられているすべてのアプリケーションに対して第 2 要素認証を行うように求められます。 条件付きアクセスを使用すると、第 2 要素がどのように求められるかをより細かく制御でき、VPN にのみ MFA を割り当てて、Azure AD テナントに関連付けられている他のアプリケーションには割り当てないようにすることができます。

## <a name="enableauth"></a>認証を有効にする

1. **[Azure Active Directory] > [エンタープライズ アプリケーション] > [すべてのアプリケーション]** へ移動します。
2. **[エンタープライズ アプリケーション - すべてのアプリケーション]** ページで、 **[Azure VPN]** を選択します。

   ![ディレクトリ ID](../../includes/media/vpn-gateway-vwan-openvpn-azure-ad-mfa/user1.jpg)

## <a name="enablesign"></a> サインインの設定を構成する

**[Azure VPN のプロパティ]** ページで、サインインの設定を構成します。

1. **ユーザーのサインインを有効にしますか？** で、**はい**を選択します。 これにより、AD テナントのすべてのユーザーが，VPN に正常に接続できるようになります。
2. **ユーザー割り当てが必用ですか？** で、Azure VPN に対するアクセス許可を持つユーザーのみにサインインを制限する場合は、**はい**を選択します。
3. 変更を保存します。

   ![アクセス許可](./media/openvpn-azure-ad-mfa/user2.jpg)

## <a name="option-1---enable-multi-factor-authentication-mfa-via-conditional-access"></a>オプション 1 - 条件付きアクセスを使用して Multi-Factor Authentication (MFA) を有効にする

条件付きアクセスを使用すると、アプリケーションごとにきめ細かなアクセス制御を行うことができます。  条件付きアクセスを利用するには、条件付きアクセス規則の対象となるユーザーに対して Azure AD Premium 1 以上のライセンスが適用されている必要があることに注意してください。

1. **[エンタープライズ アプリケーション - すべてのアプリケーション]** ページで、 **[Azure VPN]** を選択し、 **[条件付きアクセス]** を選択して、 **[新しいポリシー]** をクリックします。
2. [ユーザーとグループ] の *[含める]* タブで、 **[ユーザーとグループの選択]** をオンにし、 **[ユーザーとグループ]** をオンにして、MFA の対象となるユーザーのグループまたはセットを選択します。  **[Done]** をクリックします。
![割り当て](../../includes/media/vpn-gateway-vwan-openvpn-azure-ad-mfa/mfa-ca-assignments.png)
3. **[許可]** で、 **[アクセス権の付与]** をオンにし、 **[多要素認証が必要です]** をオンにし、 **[選択したコントロールすべてが必要]** をオンにして、 **[選択]** ボタンをクリックします。
![アクセス権の付与 - MFA](../../includes/media/vpn-gateway-vwan-openvpn-azure-ad-mfa/mfa-ca-grant-mfa.png)
4. **[ポリシーを有効にする]** の下の **[オン]** を選択して、 **[作成]** ボタンをクリックします。
![ポリシーを有効にする](../../includes/media/vpn-gateway-vwan-openvpn-azure-ad-mfa/mfa-ca-enable-policy.png)

## <a name="option-2---enable-multi-factor-authentication-mfa-per-user"></a>オプション 2 - ユーザーごとに Multi-Factor Authentication (MFA) を有効にする

[!INCLUDE [MFA steps](../../includes/vpn-gateway-vwan-openvpn-azure-ad-mfa.md)]

## <a name="next-steps"></a>次のステップ

仮想ネットワークに接続するには、VPN クライアント プロファイルを作成し、構成する必要があります。 「[VPN クライアントを P2S VPN 接続用に構成する](openvpn-azure-ad-client.md)」を参照してください。
