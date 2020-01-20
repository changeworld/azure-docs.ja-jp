---
title: 'VPN Gateway: さまざまなユーザー グループの Azure AD テナント:Azure AD 認証'
description: P2S VPN を使用して VNet に接続する場合には、Azure AD 認証を使用できます
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 12/20/2019
ms.author: alzam
ms.openlocfilehash: 73a47b20010be0b4eadec790dd8fb254f912ff2a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/25/2019
ms.locfileid: "75474121"
---
# <a name="create-an-azure-active-directory-tenant-for-p2s-openvpn-protocol-connections"></a>P2S OpenVPN プロトコル接続用の Azure Active Directory テナントを作成する

VNet に接続する際には、証明書ベースの認証か、 RADIUS 認証を使用できます。 ただし、Open VPN プロトコルを使用する場合は、Azure Active Directory 認証を使用することもできます。 さまざまなユーザー セットでさまざまな VPN ゲートウェイに接続できるようにする場合、複数のアプリを AD に登録し、それらを異なる VPN ゲートウェイにリンクできます。 この記事は、P2S OpenVPN 認証用の Azure AD テナントを設定し、さまざまなユーザーとグループにさまざまなアクセスを許可するように、複数のアプリを作成し、Azure AD に登録する際に役立ちます。

> [!NOTE]
> Azure AD 認証は、OpenVPN® プロトコル接続でのみサポートされています。
>

## <a name="tenant"></a>1.Azure AD テナントを作成する

[新しいテナントの作成](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)に関する記事の手順に従って、Azure AD テナントを作成します。

* 組織名
* 初期ドメイン名

例:

   ![新しい Azure AD テナント](./media/openvpn-azure-ad-tenant-multi-app/newtenant.png)

## <a name="users"></a>2.Azure AD テナント ユーザーを作成する

次に、2 つのユーザー アカウントを作成します。 グローバル管理者アカウントを 1 つ、マスター ユーザー アカウントを 1 つ作成します。 マスター ユーザー アカウントは、マスター埋め込みアカウント (サービス アカウント) として使用されます。 Azure AD テナント ユーザー アカウントを作成する際には、作成するユーザーの種類に応じてディレクトリ ロールを調整します。

[この記事](../active-directory/fundamentals/add-users-azure-active-directory.md)の手順を使用して、Azure AD テナントに対して少なくとも 2 人のユーザーを作成します。 作成するアカウントの種類 (下記) に応じて、**ディレクトリ ロール**を変更してください。

* 全体管理者
* User

## <a name="enable-authentication"></a>3.Azure AD テナントで Azure VPN クライアントを登録する

1. 認証に使用するディレクトリのディレクトリ ID を特定します。 これは、[Active Directory] ページの [プロパティ] セクションに表示されます。

    ![ディレクトリ ID](./media/openvpn-azure-ad-tenant-multi-app/directory-id.png)

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

    ![ディレクトリ ID](./media/openvpn-azure-ad-tenant-multi-app/pick.png)

6. メッセージが表示されたら、 **[同意する]** を選択します。

    ![Accept](./media/openvpn-azure-ad-tenant-multi-app/accept.jpg)

7. Azure AD の **[エンタープライズ アプリケーション]** に、**Azure VPN** が表示されます。

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/azurevpn.png)

## <a name="enable-authentication"></a>4.さまざまなユーザーやグループに追加のアプリケーションを登録する

1. お使いの Azure Active Directory で **[アプリの登録]** を選択し、 **[新しい登録]** を選択します。

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app1.png)

2. **[アプリケーションの登録]** ブレードに **[名前]** を入力し、目的の **[サポートされているアカウントの種類]** を選択し、 **[登録]** をクリックします。

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app2.png)

3. 新しいアプリが登録されたら、アプリ ブレードの下にある **[API の公開]** をクリックします。

4. **[+ スコープの追加]** をクリックします。
5. **[アプリケーション ID URI]** は既定のまま残し、 **[保存して続行]** をクリックします。

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app3.png)
6. 必須フィールドに入力し、 **[状態]** を確実に **[有効]** にします。 **[スコープの追加]** をクリックします。

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app4.png)
7. **[API の公開]** をクリックし、 **[+ クライアント アプリケーションの追加]** をクリックします。  **[クライアント ID]** には、クラウドに基づいて次の値を入力します。
    -   Azure **Public** の場合、「**41b23e61-6c1e-4545-b367-cd054e0ed4b4**」と入力します
    -   Azure **Government** の場合、「**51bb15d4-3a4f-4ebf-9dca-40096fe32426**」と入力します
    -   Azure **Germany** の場合、「**538ee9e6-310a-468d-afef-ea97365856a9**」と入力します
    -   Azure **China 21Vianet** の場合、「**49f817b6-84ae-4cc0-928c-73f27289b3aa**」と入力します


8. **[アプリケーションの追加]** をクリックします

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app5.png)
9. **[概要]** ページから **[Application (client) ID]\(アプリケーション (クライアント) ID\)** をコピーします。 VPN ゲートウェイを構成するために必要です。

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app6.png)

10. このセクション (4) の手順を繰り返し、セキュリティ要件に必要な数だけアプリケーションを作成します。 各アプリケーションが VPN ゲートウェイに関連付けられ、さまざまなユーザー セットを指定できます。 1 つのゲートウェイに関連付けられるアプリケーションは 1 つだけです。

## <a name="enable-authentication"></a>5.アプリケーションへのユーザーの割り当て

1. Azure AD の **[エンタープライズ アプリケーション]** の下で、新しく登録したアプリケーションを選択し、 **[プロパティ]** をクリックします。 **[ユーザーの割り当てが必要ですか?]** を確実に **[はい]** に設定します。 **[保存]**

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/user2.png)

2. アプリ ページで **[ユーザーとグループ]** をクリックし、 **[ユーザーの追加]** をクリックします。

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/user3.png)
3. **[割り当ての追加]** で **[ユーザーとグループ]** をクリックします。 この VPN アプリケーションにアクセスできるようにするユーザーを選択します。 **[選択]** をクリックします。

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/user4.png)

## <a name="enable-authentication"></a>6.VPN ゲートウェイでの Azure AD 認証を有効にする

1. 次のコマンドを実行して、VPN ゲートウェイでの Azure AD 認証を有効にします。コマンドは実際の環境に合わせて変更してください。

    ```azurepowershell-interactive
    $gw = Get-AzVirtualNetworkGateway -Name <name of VPN gateway> -ResourceGroupName <Resource group>
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientRootCertificates @()
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -AadTenantUri "https://login.microsoftonline.com/<your Directory ID>" -AadAudienceId "application ID from previous section" -AadIssuerUri "https://sts.windows.net/<your Directory ID>/" -VpnClientAddressPool 192.168.0.0/24
    ```
> [!NOTE]
> VPN ゲートウェイへのアクセスがすべてのユーザーに与えられるため、上のコマンドでは Azure VPN クライアントのアプリケーション ID を使用しないでください。 登録したアプリケーションの ID を使用します。

2. 次のコマンドを実行してプロファイルを作成し、ダウンロードします。 -ResourcGroupName と -Name の値は実際の値に変更してください。

    ```azurepowershell-interactive
    $profile = New-AzVpnClientConfiguration -Name <name of VPN gateway> -ResourceGroupName <Resource group> -AuthenticationMethod "EapTls"
    $PROFILE.VpnProfileSASUrl
    ```

3. コマンドを実行すると、次のような結果が表示されます。 結果の URL をブラウザーにコピーして、プロファイルの ZIP ファイルをダウンロードします。

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/profile.png)

4. ダウンロードした zip ファイルを解凍します。

5. 解凍された "AzureVPN" フォルダーを参照します。

6. "azurevpnconfig.xml" ファイルの場所をメモしておきます。 azurevpnconfig.xml には VPN 接続の設定が含まれています。このファイルは Azure VPN クライアント アプリケーションに直接インポートできます。 このファイルは、接続する必要があるすべてのユーザーに、電子メールやその他の方法で配布することもできます。 ユーザーが正常に接続するには、有効な Azure AD 資格情報が必要になります。

## <a name="next-steps"></a>次のステップ

仮想ネットワークに接続するには、VPN クライアント プロファイルを作成し、構成する必要があります。 「[VPN クライアントを P2S VPN 接続用に構成する](openvpn-azure-ad-client.md)」を参照してください。
