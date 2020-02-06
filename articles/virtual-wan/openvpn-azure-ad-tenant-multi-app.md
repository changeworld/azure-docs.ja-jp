---
title: 'VPN Gateway: さまざまなユーザー グループの Azure AD テナント: Azure AD 認証'
description: P2S VPN を使用して VNet に接続する場合には、Azure AD 認証を使用できます
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 1/10/2020
ms.author: alzam
ms.openlocfilehash: 90244b9dcf30c2ef01d4e57c9d8e35fa1d71f434
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/04/2020
ms.locfileid: "76985647"
---
# <a name="create-an-azure-active-directory-tenant-for-p2s-openvpn-protocol-connections"></a>P2S OpenVPN プロトコル接続用の Azure Active Directory テナントを作成する

VNet に接続する際には、証明書ベースの認証か、 RADIUS 認証を使用できます。 ただし、Open VPN プロトコルを使用する場合は、Azure Active Directory 認証を使用することもできます。 一連のユーザーがそれぞれ異なる VPN ゲートウェイに接続できるようにしたい場合、複数のアプリを AD に登録し、それらを異なる VPN ゲートウェイにリンクさせる方法が考えられます。 この記事では、P2S OpenVPN 認証用の Azure AD テナントを設定し、ユーザーとグループにそれぞれ異なるアクセスを許可するように、複数のアプリを作成して Azure AD に登録する方法について説明します。

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

## <a name="enable-authentication"></a>4.さまざまなユーザーやグループ用に追加のアプリケーションを登録する

1. Azure Active Directory で **[アプリの登録]** をクリックし、 **[新しい登録]** を選択します。

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app1.png)

2. **[アプリケーションの登録]** ブレードに **[名前]** を入力し、目的の **[サポートされているアカウントの種類]** を選択して、 **[登録]** をクリックします。

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app2.png)

3. 新しいアプリが登録されたら、アプリ ブレードにある **[API の公開]** をクリックします。

4. **[+ スコープの追加]** をクリックします。
5. **[アプリケーション ID URI]** は既定値のままにし、 **[保存して続行]** をクリックします。

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app3.png)
6. 必須フィールドに入力し、 **[状態]** を **[有効]** にします。 **[スコープの追加]** をクリックします。

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app4.png)
7. **[API の公開]** をクリックし、 **[+ クライアント アプリケーションの追加]** をクリックします。  **[クライアント ID]** には、クラウドに基づいて次の値を入力します。
    -   Azure **Public** の場合、「**41b23e61-6c1e-4545-b367-cd054e0ed4b4**」と入力します
    -   Azure **Government** の場合、「**51bb15d4-3a4f-4ebf-9dca-40096fe32426**」と入力します
    -   Azure **Germany** の場合、「**538ee9e6-310a-468d-afef-ea97365856a9**」と入力します
    -   Azure **China 21Vianet** の場合、「**49f817b6-84ae-4cc0-928c-73f27289b3aa**」と入力します


8. **[アプリケーションの追加]** をクリックします

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app5.png)
9. **[概要]** ページから **[アプリケーション (クライアント) ID]** をコピーします。 VPN ゲートウェイを構成するために必要です。

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app6.png)

10. このセクション (4) の手順を繰り返し、セキュリティ要件に必要な数だけアプリケーションを作成します。 各アプリケーションが VPN ゲートウェイに関連付けられ、さまざまなユーザーのグループを指定できます。 1 つのゲートウェイに関連付けられるアプリケーションは 1 つだけです。

## <a name="enable-authentication"></a>5.アプリケーションへのユーザーの割り当て

1. Azure AD の **[エンタープライズ アプリケーション]** で、新しく登録したアプリケーションを選択し、 **[プロパティ]** をクリックします。 **[ユーザーの割り当てが必要ですか?]** を **[はい]** に設定します。 **[保存]**

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/user2.png)

2. アプリ ページで **[ユーザーとグループ]** をクリックし、 **[ユーザーの追加]** をクリックします。

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/user3.png)
3. **[割り当ての追加]** で **[ユーザーとグループ]** をクリックします。 この VPN アプリケーションにアクセスできるようにするユーザーを選択します。 **[選択]** をクリックします。

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/user4.png)

## <a name="site"></a>6.新しい P2S 構成を作成する

P2S 構成には、リモート クライアントを接続するためのパラメーターが定義されています。

1. 次の変数を設定します。実際の環境に合わせて値は置き換えてください。

   ```powershell
   $aadAudience = "00000000-abcd-abcd-abcd-999999999999"
   $aadIssuer = "https://sts.windows.net/00000000-abcd-abcd-abcd-999999999999/"
   $aadTenant = "https://login.microsoftonline.com/00000000-abcd-abcd-abcd-999999999999"    
   ```

2. 次のコマンドを実行して構成を作成します。

   ```powershell
   $aadConfig = New-AzVpnServerConfiguration -ResourceGroupName <ResourceGroup> -Name newAADConfig -VpnProtocol OpenVPN -VpnAuthenticationType AAD -AadTenant $aadTenant -AadIssuer $aadIssuer -AadAudience $aadAudience -Location westcentralus
   ```

> [!NOTE]
> VPN ゲートウェイへのアクセスがすべてのユーザーに与えられるため、上のコマンドでは Azure VPN クライアントのアプリケーション ID を使用しないでください。 登録したアプリケーションの ID を使用します。

## <a name="hub"></a>7.ハブの割り当てを編集する

1. 仮想 WAN の下にある **[ハブ]** ブレードに移動します。
2. VPN サーバーの構成を関連付けるハブを選択し、省略記号 (...) をクリックします。

   ![新しいサイト](media/virtual-wan-point-to-site-azure-ad/p2s4.jpg)
3. **[仮想ハブを編集する]** をクリックします。
4. **[ポイント対サイト ゲートウェイを含める]** チェック ボックスをオンにし、必要な **[ゲートウェイ スケール ユニット]** を選択します。

   ![新しいサイト](media/virtual-wan-point-to-site-azure-ad/p2s2.jpg)
5. VPN クライアントの IP アドレスの割り当て元となる **[アドレス プール]** を入力します。
6. **[Confirm]\(確認\)** をクリックします。
7. この操作は、完了するまで最大 30 分かかることがあります。

## <a name="device"></a>8.VPN プロファイルをダウンロードする

VPN プロファイルを使用してクライアントを構成します。

1. 仮想 WAN のページで、 **[User VPN configurations]\(ユーザー VPN の構成\)** をクリックします。
2. ページの上部にある **[Download user VPN config]\(ユーザー VPN 構成をダウンロードする\)** をクリックします。
3. ファイルの作成が完了したら、リンクをクリックしてファイルをダウンロードできます。
4. プロファイル ファイルを使用して VPN クライアントを構成します。

4. ダウンロードした zip ファイルを解凍します。

5. 解凍された "AzureVPN" フォルダーを参照します。

6. "azurevpnconfig.xml" ファイルの場所をメモしておきます。 azurevpnconfig.xml には VPN 接続の設定が含まれています。このファイルは Azure VPN クライアント アプリケーションに直接インポートできます。 このファイルは、接続する必要があるすべてのユーザーに、電子メールやその他の方法で配布することもできます。 ユーザーが正常に接続するには、有効な Azure AD 資格情報が必要になります。
## <a name="configure-user-vpn-clients"></a>ユーザー VPN クライアントを構成する

接続するには、Azure VPN Client (プレビュー) をダウンロードし、前の手順でダウンロードした VPN クライアント プロファイルを、VNet に接続する各コンピューターでインポートします。

> [!NOTE]
> Azure AD 認証は、OpenVPN® プロトコル接続でのみサポートされています。
>

#### <a name="to-download-the-azure-vpn-client"></a>Azure VPN Client をダウンロードするには

この[リンク](https://www.microsoft.com/p/azure-vpn-client-preview/9np355qt2sqb?rtc=1&activetab=pivot:overviewtab)を使用して、Azure VPN Client (プレビュー) をダウンロードします。

#### <a name="import"></a>クライアント プロファイルをインポートするには

1. ページ上で、 **[インポート]** を選択します。

    ![import](./media/virtual-wan-point-to-site-azure-ad/import/import1.jpg)

2. プロファイル xml ファイルを参照し、選択します。 ファイルが選択された状態で、 **[開く]** を選択します。

    ![import](./media/virtual-wan-point-to-site-azure-ad/import/import2.jpg)

3. プロファイルの名前を指定し、 **[保存]** を選択します。

    ![import](./media/virtual-wan-point-to-site-azure-ad/import/import3.jpg)

4. **[接続]** を選択して VPN に接続します。

    ![import](./media/virtual-wan-point-to-site-azure-ad/import/import4.jpg)

5. 接続されると、アイコンが緑色に変わり、 **[接続済み]** と表示されます。

    ![import](./media/virtual-wan-point-to-site-azure-ad/import/import5.jpg)

#### <a name="delete"></a>クライアント プロファイルを削除するには

1. 削除するクライアント プロファイルの横にある省略記号 (...) を選択します。 **[削除]** を選択します。

    ![delete](./media/virtual-wan-point-to-site-azure-ad/delete/delete1.jpg)

2. **[削除]** を選択して削除します。

    ![delete](./media/virtual-wan-point-to-site-azure-ad/delete/delete2.jpg)

#### <a name="diagnose"></a>接続の問題を診断する

1. 接続の問題を診断するには、**診断**ツールを使用します。 診断する VPN 接続の横にある省略記号 (...) を選択して、メニューを表示します。 次に、 **[診断]** を選択します。

    ![診断](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose1.jpg)

2. **[接続プロパティ]** ページで、 **[Run Diagnosis]\(診断の実行\)** を選択します。

    ![診断](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose2.jpg)

3. 自分の資格情報でサインインします。

    ![診断](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose3.jpg)

4. 診断結果を確認します。

    ![診断](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose4.jpg)

## <a name="viewwan"></a>仮想 WAN を表示する

1. 仮想 WAN に移動します。
2. [概要] ページのマップ上の各ポイントは、ハブを表します。 任意のポイントにカーソルを置くと、ハブの正常性の概要が表示されます。
3. ハブと接続のセクションでは、ハブの状態、サイト、リージョン、VPN 接続の状態、および入出力バイト数を表示できます。

## <a name="viewhealth"></a>リソースの正常性を表示する

1. WAN に移動します。
2. WAN のページの **[サポート + トラブルシューティング]** セクションで、 **[正常性]** をクリックしてリソースを表示します。


## <a name="cleanup"></a>リソースをクリーンアップする

これらのリソースが不要になったら、[Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) を使用して、リソース グループとその中のすべてのリソースを削除できます。 "myResourceGroup" をリソース グループの名前に置き換えて、次の PowerShell コマンドを実行します。

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>次のステップ

Virtual WAN の詳細については、[Virtual WAN の概要](virtual-wan-about.md)に関するページを参照してください。
