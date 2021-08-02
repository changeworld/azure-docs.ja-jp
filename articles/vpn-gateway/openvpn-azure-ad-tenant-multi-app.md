---
title: 'P2S OpenVPN プロトコル接続用の Azure AD テナントを作成する方法: Azure AD 認証'
titleSuffix: Azure VPN Gateway
description: P2S OpenVPN 認証用の Azure AD テナントを設定し、さまざまなユーザーとグループに異なるアクセスを許可するように複数のアプリを Azure AD に登録する方法について説明します。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 05/05/2021
ms.author: cherylmc
ms.openlocfilehash: f5b67a1843dd363c727122d77c4c9d574e90a4ab
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/06/2021
ms.locfileid: "108760483"
---
# <a name="create-an-active-directory-ad-tenant-for-p2s-openvpn-protocol-connections"></a>P2S OpenVPN プロトコル接続用の Active Directory (AD) テナントを作成する

ポイント対サイトを使用して VNet に接続する場合は、使用するプロトコルを選択できます。 使用するプロトコルによって、使用できる認証オプションが決まります。 OpenVPN プロトコルを使用する場合、Azure Active Directory 認証を使用できます。 一連のユーザーがそれぞれ異なる VPN ゲートウェイに接続できるようにしたい場合、複数のアプリを AD に登録し、それらを異なる VPN ゲートウェイにリンクさせる方法が考えられます。 この記事では、P2S OpenVPN 用の Azure AD テナントを設定し、ユーザーとグループにそれぞれ異なるアクセスを許可するように、複数のアプリを作成して Azure AD に登録する方法について説明します。 ポイント対サイト プロトコルと認証の詳細については、「[ポイント対サイト VPN について](point-to-site-about.md)」を参照してください。

[!INCLUDE [OpenVPN note](../../includes/vpn-gateway-openvpn-auth-include.md)]

[!INCLUDE [create](../../includes/openvpn-azure-ad-tenant-multi-app.md)]

## <a name="6-enable-authentication-on-the-gateway"></a><a name="enable-authentication"></a>6.ゲートウェイでの認証を有効にする

このステップでは、VPN ゲートウェイでの Azure AD 認証を有効にします。

1. VPN ゲートウェイで Azure AD 認証を有効にするには、 **[ポイント対サイトの構成]** に移動し、 **[トンネルの種類]** として **[OpenVPN (SSL)]** を選択します。 **[認証の種類]** として **[Azure Active Directory]** を選択し、 **[Azure Active Directory]** セクションに情報を入力します。

    ![Azure portal のビュー](./media/openvpn-azure-ad-tenant-multi-app/azure-ad-auth-portal.png)

    > [!NOTE]
    > Azure VPN クライアントのアプリケーション ID を使用しないでください。これにより、すべてのユーザーに VPN ゲートウェイへのアクセス権が付与されます。 登録したアプリケーションの ID を使用します。

2. **[VPN クライアントのダウンロード]** リンクをクリックして、プロファイルを作成してダウンロードします。

3. ダウンロードした zip ファイルを解凍します。

4. 解凍された "AzureVPN" フォルダーを参照します。

5. "azurevpnconfig.xml" ファイルの場所をメモしておきます。 azurevpnconfig.xml には VPN 接続の設定が含まれています。このファイルは Azure VPN クライアント アプリケーションに直接インポートできます。 このファイルは、接続する必要があるすべてのユーザーに、電子メールやその他の方法で配布することもできます。 ユーザーが正常に接続するには、有効な Azure AD 資格情報が必要になります。

## <a name="next-steps"></a>次のステップ

仮想ネットワークに接続するには、VPN クライアント プロファイルを作成し、構成する必要があります。 「[VPN クライアントを P2S VPN 接続用に構成する](openvpn-azure-ad-client.md)」を参照してください。
