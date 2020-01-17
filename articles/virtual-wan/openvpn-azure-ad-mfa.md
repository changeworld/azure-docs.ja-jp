---
title: VPN ユーザー用の MFA を有効にする：Azure AD 認証
description: VPN ユーザー用の多要素認証を有効にします。
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: alzam
ms.openlocfilehash: 9329ac33a771c829b8d67670bc07d9a353c58ff1
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/14/2020
ms.locfileid: "75930595"
---
# <a name="enable-azure-multi-factor-authentication-mfa-for-vpn-users"></a>VPN ユーザーの Azure 多要素認証 (MFA)を有効にします。

ユーザーにアクセスを許可する前に2つ目の認証要素の入力を求めるようにしたい場合には、Azure AD テナント用の Azure Multi-Factor Authentication (MFA) を構成できます。 ここに記載手順では、2 段階認証の要件を有効にする方法について説明します。

## <a name="prereq"></a>前提条件

この構成の前提条件は、[テナントの構成](openvpn-azure-ad-tenant.md)の手順を使用して構成された Azure AD テナントです。

## <a name="mfa"></a>MFA ページを開く

1. Azure portal にサインインします。
2. **[Azure Active Directory]、[ユーザー]** の順に移動します。
3. **[Multi-Factor Authentication]** を選択して、[多要素認証] ページを開きます。

   ![サインイン](./media/openvpn-azure-ad-mfa/mfa1.jpg)

## <a name="users"></a> ユーザーを選択します。

1. **[Multi-factor authentication]** のページで、MFA を有効にするユーザーを選択します。
2. **[有効化]** を選択します。

   ![選択](./media/openvpn-azure-ad-mfa/mfa2.jpg)

## <a name="enableauth"></a>認証を有効にする

1. **[Azure Active Directory] > [エンタープライズ アプリケーション] > [すべてのアプリケーション]** へ移動します。
2. **[エンタープライズ アプリケーション - すべてのアプリケーション]** ページで、 **[Azure VPN]** を選択します。

   ![ディレクトリ ID](./media/openvpn-azure-ad-mfa/user1.jpg)

## <a name="enablesign"></a> サインインの設定を構成する

**[Azure VPN のプロパティ]** ページで、サインインの設定を構成します。

1. **ユーザーのサインインを有効にしますか？** で、**はい**を選択します。 これにより、AD テナントのすべてのユーザーが，VPN に正常に接続できるようになります。
2. **ユーザー割り当てが必用ですか？** で、Azure VPN に対するアクセス許可を持つユーザーのみにサインインを制限する場合は、**はい**を選択します。
3. 変更を保存します。

   ![アクセス許可](./media/openvpn-azure-ad-mfa/user2.jpg)

## <a name="next-steps"></a>次のステップ

仮想ネットワークに接続するには、VPN クライアント プロファイルを作成し、構成する必要があります。 「[Azure へのポイント対サイト接続に Azure AD 認証を構成する](virtual-wan-point-to-site-azure-ad.md)」を参照してください。
