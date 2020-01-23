---
title: VPN ユーザー用の MFA を有効にする：Azure AD 認証
description: VPN ユーザー用の多要素認証を有効にします。
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: alzam
ms.openlocfilehash: b22581d012b2c69081bc7b4eee093227c060b4c2
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/17/2020
ms.locfileid: "76169707"
---
# <a name="enable-azure-multi-factor-authentication-mfa-for-vpn-users"></a>VPN ユーザーの Azure 多要素認証 (MFA)を有効にします。

ユーザーにアクセスを許可する前に2つ目の認証要素の入力を求めるようにしたい場合には、Azure AD テナント用の Azure Multi-Factor Authentication (MFA) を構成できます。 ここに記載手順では、2 段階認証の要件を有効にする方法について説明します。

## <a name="prereq"></a>前提条件

この構成の前提条件は、[テナントの構成](openvpn-azure-ad-tenant.md)の手順を使用して構成された Azure AD テナントです。

[!INCLUDE [MFA steps](../../includes/vpn-gateway-vwan-openvpn-azure-ad-mfa.md)]

## <a name="enablesign"></a> サインインの設定を構成する

**[Azure VPN のプロパティ]** ページで、サインインの設定を構成します。

1. **ユーザーのサインインを有効にしますか？** で、**はい**を選択します。 これにより、AD テナントのすべてのユーザーが，VPN に正常に接続できるようになります。
2. **ユーザー割り当てが必用ですか？** で、Azure VPN に対するアクセス許可を持つユーザーのみにサインインを制限する場合は、**はい**を選択します。
3. 変更を保存します。

   ![アクセス許可](./media/openvpn-azure-ad-mfa/user2.jpg)

## <a name="next-steps"></a>次のステップ

仮想ネットワークに接続するには、VPN クライアント プロファイルを作成し、構成する必要があります。 「[VPN クライアントを P2S VPN 接続用に構成する](openvpn-azure-ad-client.md)」を参照してください。
