---
title: 'VPN Gateway: P2S VPN 接続用の Azure AD テナント:Azure AD 認証'
description: P2S VPN を使用して VNet に接続する場合には、Azure AD 認証を使用できます
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 12/27/2019
ms.author: alzam
ms.openlocfilehash: 1f7cf97e38bf201679593819cce814249f9625b0
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/14/2020
ms.locfileid: "75930419"
---
# <a name="create-an-azure-active-directory-tenant-for-p2s-openvpn-protocol-connections"></a>P2S OpenVPN プロトコル接続用の Azure Active Directory テナントを作成する

VNet に接続する際には、証明書ベースの認証か、 RADIUS 認証を使用できます。 ただし、Open VPN プロトコルを使用する場合は、Azure Active Directory 認証を使用することもできます。 この記事では、P2S Open VPN 認証用の Azure AD テナントを設定する方法について説明します。

> [!NOTE]
> Azure AD 認証は、OpenVPN® プロトコル接続でのみサポートされています。
>

## <a name="tenant"></a>1.Azure AD テナントを作成する

[新しいテナントの作成](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)に関する記事の手順に従って、Azure AD テナントを作成します。

* 組織名
* 初期ドメイン名

例:

   ![新しい Azure AD テナント](./media/openvpn-create-azure-ad-tenant/newtenant.png)

## <a name="users"></a>2.Azure AD テナント ユーザーを作成する

次に、2 つのユーザー アカウントを作成します。 グローバル管理者アカウントを 1 つ、マスター ユーザー アカウントを 1 つ作成します。 マスター ユーザー アカウントは、マスター埋め込みアカウント (サービス アカウント) として使用されます。 Azure AD テナント ユーザー アカウントを作成する際には、作成するユーザーの種類に応じてディレクトリ ロールを調整します。

[この記事](../active-directory/fundamentals/add-users-azure-active-directory.md)の手順を使用して、Azure AD テナントに対して少なくとも 2 人のユーザーを作成します。 作成するアカウントの種類 (下記) に応じて、**ディレクトリ ロール**を変更してください。

* 全体管理者
* User

## <a name="enable-authentication"></a>3.VPN ゲートウェイでの Azure AD 認証を有効にする

1. 認証に使用するディレクトリのディレクトリ ID を特定します。 これは、[Active Directory] ページの [プロパティ] セクションに表示されます。

    ![ディレクトリ ID](./media/openvpn-create-azure-ad-tenant/directory-id.png)

2. ディレクトリ ID をコピーします。

3. **全体管理者**ロールを割り当てられたユーザーとして、Azure portal にサインインします。

4. 次に、管理者の同意を与えます。 デプロイの場所に関連する URL をコピーし、ブラウザーのアドレス バーに貼り付けます。

    パブリック

    ```
    https://login.microsoftonline.com/common/oauth2/authorize?client_id=41b23e61-6c1e-4545-b367-cd054e0ed4b4&response_type=code&redirect_uri=https://portal.azure.com&nonce=1234&prompt=admin_consent
    ````

    Azure Government

    ```
    https://login-us.microsoftonline.com/common/oauth2/authorize?client_id=51bb15d4-3a4f-4ebf-9dca-40096fe32426&response_type=code&redirect_uri=https://portal.azure.us&nonce=1234&prompt=admin_consent
    ````

    Microsoft Cloud Germany

    ```
    https://login-us.microsoftonline.de/common/oauth2/authorize?client_id=538ee9e6-310a-468d-afef-ea97365856a9&response_type=code&redirect_uri=https://portal.microsoftazure.de&nonce=1234&prompt=admin_consent
    ````

    Azure China 21Vianet

    ```
    https://https://login.chinacloudapi.cn/common/oauth2/authorize?client_id=49f817b6-84ae-4cc0-928c-73f27289b3aa&response_type=code&redirect_uri=https://portal.azure.cn&nonce=1234&prompt=admin_consent
    ```

5. メッセージが表示されたら、**全体管理者**アカウントを選択します。

    ![ディレクトリ ID](./media/openvpn-create-azure-ad-tenant/pick.png)

6. メッセージが表示されたら、 **[同意する]** を選択します。

    ![Accept](./media/openvpn-create-azure-ad-tenant/accept.jpg)

7. Azure AD の **[エンタープライズ アプリケーション]** に、**Azure VPN** が表示されます。

    ![Azure VPN](./media/openvpn-create-azure-ad-tenant/azurevpn.png)

8. 「[Azure へのポイント対サイト接続に Azure AD 認証を構成する](virtual-wan-point-to-site-azure-ad.md)」の手順に従って、ユーザー VPN の Azure AD 認証を構成し、それを仮想ハブに割り当てます

## <a name="next-steps"></a>次のステップ

仮想ネットワークに接続するには、VPN クライアント プロファイルを作成して構成し、仮想ハブに関連付ける必要があります。 「[Azure へのポイント対サイト接続に Azure AD 認証を構成する](virtual-wan-point-to-site-azure-ad.md)」を参照してください。
