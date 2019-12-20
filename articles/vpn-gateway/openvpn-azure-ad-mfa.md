---
title: 'VPN ユーザーの MFA を有効にする: Azure AD 認証'
description: VPN ユーザーの多要素認証を有効にします
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: alzam
ms.openlocfilehash: 7f05b850a0d886ac0df5c542de647f91fe62eb05
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/22/2019
ms.locfileid: "74382203"
---
# <a name="enable-azure-multi-factor-authentication-mfa-for-vpn-users"></a>VPN ユーザーの多要素認証 (MFA) を有効にする

ユーザーにアクセス許可を付与する前に 2 つ目の認証要素の入力を求める場合は、Azure AD テナントに対して Azure Multi-Factor Authentication (MFA) を構成できます。 この記事の手順は、2 段階認証の要件を有効にするために役立ちます。

## <a name="prereq"></a>前提条件

この構成の前提条件は、[テナントの構成](openvpn-azure-ad-tenant.md)に関する記事の手順に従って構成された Azure AD テナントです。

## <a name="mfa"></a>MFA ページを開く

1. Azure ポータルにサインインします。
2. **[Azure Active Directory]、[すべてのユーザー]** の順に移動します。
3. **[多要素認証]** を選択して、[多要素認証] ページを開きます。

   ![サインイン](./media/openvpn-azure-ad-mfa/mfa1.jpg)

## <a name="users"></a>ユーザーを選択する

1. **[多要素認証]** ページで、MFA を有効にするユーザーを選択します。
2. **[有効化]** を選択します。

   ![選択](./media/openvpn-azure-ad-mfa/mfa2.jpg)

## <a name="enableauth"></a>認証を有効にする

1. **[Azure Active Directory]、[エンタープライズ アプリケーション]、[すべてのアプリケーション]** の順に移動します。
2. **[エンタープライズ アプリケーション - すべてのアプリケーション]** ページで、 **[Azure VPN]** を選択します。

   ![ディレクトリ ID](./media/openvpn-azure-ad-mfa/user1.jpg)

## <a name="enablesign"></a>サインイン設定を構成する

**[Azure VPN - プロパティ]** ページで、サインイン設定を構成します。

1. **[ユーザーのサインインが有効になっていますか?]** を **[はい]** に設定します。 これにより、AD テナントのすべてのユーザーが VPN に正常に接続できるようになります。
2. サインインを Azure VPN にアクセスできるユーザーだけに制限する場合は、 **[ユーザーの割り当てが必要ですか?]** を **[はい]** に設定します。
3. 変更を保存します。

   ![アクセス許可](./media/openvpn-azure-ad-mfa/user2.jpg)

## <a name="next-steps"></a>次の手順

仮想ネットワークに接続するには、VPN クライアント プロファイルを作成して構成する必要があります。 「[VPN クライアントを P2S VPN 接続用に構成する](openvpn-azure-ad-client.md)」を参照してください。
