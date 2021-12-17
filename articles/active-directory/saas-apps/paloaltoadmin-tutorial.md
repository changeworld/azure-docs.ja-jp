---
title: 'チュートリアル: Azure AD SSO と Palo Alto Networks - Admin UI の統合 | Microsoft Docs'
description: Azure Active Directory と Palo Alto Networks - Admin UI の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/08/2021
ms.author: jeedes
ms.openlocfilehash: 7c7650434e6d55125bb4ebc6e53b62b23f280ecf
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132300203"
---
# <a name="tutorial-azure-ad-sso-integration-with-palo-alto-networks---admin-ui"></a>チュートリアル: Azure AD SSO と Palo Alto Networks - Admin UI の統合

このチュートリアルでは、Palo Alto Networks - Admin UI と Azure Active Directory (Azure AD) を統合する方法について説明します。 Palo Alto Networks - Admin UI を Azure AD と統合すると、次のことができます。

* Palo Alto Networks - Admin UI にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Palo Alto Networks - Admin UI に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Palo Alto Networks - Admin UI でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Palo Alto Networks - Admin UI では、**SP** Initiated SSO がサポートされます。
* Palo Alto Networks - Admin UI では、**Just-In-Time** ユーザー プロビジョニングがサポートされます。

## <a name="adding-palo-alto-networks---admin-ui-from-the-gallery"></a>ギャラリーからの Palo Alto Networks - Admin UI の追加

Azure AD への Palo Alto Networks - Admin UI の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Palo Alto Networks - Admin UI を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Palo Alto Networks - Admin UI**」と入力します。
1. 結果パネルで **[Palo Alto Networks - Admin UI]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-palo-alto-networks---admin-ui"></a>Palo Alto Networks - Admin UI のための Azure AD SSO の構成とテスト

このセクションでは、**B.Simon** というテスト ユーザーに基づいて、Palo Alto Networks - Admin UI で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと Palo Alto Networks - Admin UI 内の関連ユーザーとの間にリンク関係が確立されている必要があります。

Palo Alto Networks - Admin UI で Azure AD シングル サインオンを構成し、テストするには、次の手順に従います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Palo Alto Networks - Admin UI の SSO の構成](#configure-palo-alto-networks---admin-ui-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Palo Alto Networks - Admin UI テスト ユーザーの作成](#create-palo-alto-networks---admin-ui-test-user)** - Palo Alto Networks - Admin UI で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクします。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Palo Alto Networks - Admin UI** アプリケーション統合ページで、 **[管理]** セクションを探し、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    a. **[識別子]** ボックスに、`https://<Customer Firewall FQDN>:443/SAML20/SP` という形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://<Customer Firewall FQDN>:443/SAML20/SP/ACS` という形式を使用して、Assertion Consumer Service (ACS) URL を入力します。

    c. **[サインオン URL]** ボックスに、`https://<Customer Firewall FQDN>/php/login.php` という形式で URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL でこれらの値を更新します。 これらの値を取得するには、[Palo Alto Networks - Admin UI クライアント サポート チーム](https://support.paloaltonetworks.com/support)に連絡してください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。
    >
    > **[識別子]** と **[応答 URL]** では、ポート 443 が必須となります。これらの値は Palo Alto Firewall にハードコーディングされています。 このポート番号を削除した場合、ログイン中にエラーが発生します。

    > **[識別子]** と **[応答 URL]** では、ポート 443 が必須となります。これらの値は Palo Alto Firewall にハードコーディングされています。 このポート番号を削除した場合、ログイン中にエラーが発生します。

1. Palo Alto Networks - Admin UI アプリケーションでは、特定の形式の SAML アサーションが求められます。そのため、カスタム属性マッピングを SAML トークン属性構成に追加する必要があります。 次のスクリーンショットには、既定の属性一覧が示されています。

    ![image](common/default-attributes.png)

   > [!NOTE]
   > 属性の値はサンプルです。*username* と *adminrole* には適切な値をマップしてください。 さらにもう 1 つ、省略可能な属性 *accessdomain* があります。この属性は、ファイアウォールで特定の仮想システムへの管理アクセスを制限する目的で使用します。

1. その他に、Palo Alto Networks - Admin UI アプリケーションでは、いくつかの属性が SAML 応答で返されることが想定されています。それらの属性を次に示します。 これらの属性も値が事前に設定されますが、要件に従ってそれらの値を確認することができます。

    | 名前 |  ソース属性|
    | --- | --- |
    | username | user.userprincipalname |
    | adminrole | customadmin |
    | | |

    > [!NOTE]
    > 上記で _adminrole_ として示されている **名前** の値は、「 **[Palo Alto Networks - Admin UI の SSO の構成](#configure-palo-alto-networks---admin-ui-sso)** 」セクションの手順 12 で構成される "_管理者ロールの属性_" と同じ値である必要があります。 上記で _customadmin_ として示されている **ソース属性** の値は、「 **[Palo Alto Networks - Admin UI の SSO の構成](#configure-palo-alto-networks---admin-ui-sso)** 」セクションの手順 9 で構成される "_管理者ロールのプロファイル名_" と同じ値である必要があります。  

    > [!NOTE]
    > これらの属性の詳細については、次の記事を参照してください。
    > * [Admin UI の 管理ロール プロファイル (adminrole)](https://www.paloaltonetworks.com/documentation/80/pan-os/pan-os/firewall-administration/manage-firewall-administrators/configure-an-admin-role-profile)
    > * [Admin UI の デバイス アクセス ドメイン (accessdomain)](https://docs.paloaltonetworks.com/pan-os/8-0/pan-os-web-interface-help/device/device-access-domain.html)

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして、要件のとおりに指定したオプションから **フェデレーション メタデータ XML** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

1. **[Palo Alto Networks - Admin UI のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションでは、Azure portal 内で B.Simon というテスト ユーザーを作成します。

1. Azure portal の左側のウィンドウから、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。
1. 画面の上部にある **[新しいユーザー]** を選択します。
1. **[ユーザー]** プロパティで、以下の手順を実行します。
   1. **[名前]** フィールドに「`B.Simon`」と入力します。  
   1. **[ユーザー名]** フィールドに「username@companydomain.extension」と入力します。 たとえば、「 `B.Simon@contoso.com` 」のように入力します。
   1. **[パスワードを表示]** チェック ボックスをオンにし、 **[パスワード]** ボックスに表示された値を書き留めます。
   1. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、B.Simon に Palo Alto Networks - Admin UI へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Palo Alto Networks - Admin UI]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-palo-alto-networks---admin-ui-sso"></a>Palo Alto Networks - Admin UI の SSO の構成

1. 新しいウィンドウで Palo Alto Networks Firewall Admin UI を管理者として開きます。

2. **[Device]\(デバイス\)** タブを選択します。

    ![[Device]\(デバイス\) タブを示すスクリーンショット。](./media/paloaltoadmin-tutorial/device.png)

3. 左側のウィンドウで **[SAML Identity Provider]\(SAML ID プロバイダー\)** を選択し、 **[Import]\(インポート\)** を選択してメタデータ ファイルをインポートします。

    ![メタデータ ファイルの [Import]\(インポート\) ボタンを示すスクリーンショット。](./media/paloaltoadmin-tutorial/admin.png)

4. **[SAML Identify Provider Server Profile Import]\(SAML ID プロバイダー サーバー プロファイル インポート\)** ウィンドウで、次の手順を実行します。

    ![[SAML Identify Provider Server Profile Import]\(SAML ID プロバイダー サーバー プロファイル インポート\) ウィンドウを示すスクリーンショット。](./media/paloaltoadmin-tutorial/profile.png)

    a. **[Profile Name]\(プロファイル名\)** ボックスに名前 (「**AzureAD Admin UI**」など) を入力します。

    b. **[Identity Provider Metadata]\(ID プロバイダー メタデータ\)** の下で **[Browse]\(参照\)** を選択して、前の手順で Azure Portal からダウンロードした metadata.xml ファイルを選択ます。

    c. **[Validate Identity Provider Certificate]\(ID プロバイダー証明書の検証\)** チェック ボックスをオフにします。

    d. **[OK]** を選択します。

    e. ファイアウォールの構成を確定するには **[Commit]\(コミット\)** を選択します。

5. 左側のウィンドウで、 **[SAML Identity Provider]\(SAML ID プロバイダー\)** を選択し、前の手順で作成した SAML ID プロバイダー プロファイル (たとえば、 **[AzureAD Admin UI]** ) を選択します。

    ![SAML ID プロバイダー プロファイルを示すスクリーンショット](./media/paloaltoadmin-tutorial/azure.png)

6. **[SAML Identify Provider Server Profile]\(SAML ID プロバイダー サーバー プロファイル\)** ウィンドウで、次の手順を実行します。

    ![[SAML Identity Provider Server Profile]\(SAML ID プロバイダー サーバー プロファイル\) ウィンドウを示すスクリーンショット。](./media/paloaltoadmin-tutorial/server.png)
  
    a. **[Identity Provider SLO URL]\(ID プロバイダー SLO URL\)** ボックスで、前の手順でインポートした SLO URL を `https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0` で置き換えます。
  
    b. **[OK]** を選択します。

7. Palo Alto Networks Firewall の Admin UI で、 **[Device]\(デバイス\)** をクリックし、 **[Admin Roles]\(管理者ロール\)** を選択します

8. **[追加]** ボタンを選びます。

9. **[Admin Role Profile]\(管理者ロール プロファイル\)** ウィンドウの **[名前]** ボックスに管理者ロールの名前 (たとえば **fwadmin**) を入力します。 この管理者ロール名は、ID プロバイダーから送信された SAML 管理者ロール属性名と一致する必要があります。 管理者ロールの名前と値は、Azure portal の **[ユーザー属性]** セクションで作成されています。

    ![Palo Alto Networks の管理者ロールの構成。](./media/paloaltoadmin-tutorial/role.png)
  
10. Firewall の Admin UI で、 **[Device]\(デバイス\)** をクリックし、 **[Authentication Profile]\(認証プロファイル\)** を選択します。

11. **[追加]** ボタンを選びます。

12. **[Authentication Profile]\(認証プロファイル\)** ウィンドウで、次の手順を実行します。 

    ![[Authentication Profile]\(認証プロファイル\) ウィンドウを示すスクリーンショット。](./media/paloaltoadmin-tutorial/authentication.png)

    a. **[Name]\(名前\)** ボックスに名前 (「**AzureSAML_Admin_AuthProfile**」など) を入力します。

    b. **[Type]\(種類\)** ドロップダウン リストで、 **[SAML]** を選択します。 

    c. **[IdP Server Profile]\(IdP サーバー プロファイル\)** ドロップダウン リストで適切な SAML ID プロバイダー サーバーのプロファイル ( **[AzureAD Admin UI]** など) を選択します。

    d. **[Enable Single Logout]\(シングル ログアウトを有効にする\)** チェック ボックスをオンにします

    e. **[Admin Role Attribute]\(管理者ロール属性\)** ボックスに属性名 (「**adminrole**」など) を入力します。

    f. **[Advanced]\(詳細設定\)** タブを選択してから、 **[Allow List]\(許可リスト\)** で **[Add]\(追加\)** を選択します。

    ![[Advanced]\(詳細設定\) タブの [Add]\(追加\) ボタンを示すスクリーンショット。](./media/paloaltoadmin-tutorial/allowlist.png)

    g. **[All]\(すべて\)** チェック ボックスをオンにするか、このプロファイルで認証できるユーザーとグループを選択します。  
    ユーザーが認証すると、ファイアウォールは関連するユーザー名またはグループをこの一覧のエントリと照合します。 エントリを追加しないと、ユーザーは認証できません。

    h. **[OK]** を選択します。

13. 管理者が Azure を使用して SAML SSO を使用できるようにするには、 **[Device]\(デバイス\)**  >  **[Setup]\(セットアップ\)** を選択します。 **[Setup]\(セットアップ\)** ウィンドウで **[Management]\(管理\)** タブを選択してから、 **[Authentication Settings]\(認証の設定\)** で **設定** ("歯車") ボタンを選択します。

    ![[Settings]\(設定\) ボタンを示すスクリーンショット。](./media/paloaltoadmin-tutorial/setup.png)

14. [Authentication Profile]\(認証プロファイル\) ウィンドウで作成した SAML 認証プロファイル (たとえば、**AzureSAML_Admin_AuthProfile**) を選択します。

    ![[Authentication Profile]\(認証プロファイル\) フィールドを示すスクリーンショット。](./media/paloaltoadmin-tutorial/settings.png)

15. **[OK]** を選択します。

16. 構成をコミットするには **[Commit]\(コミット\)** を選択します。

### <a name="create-palo-alto-networks---admin-ui-test-user"></a>Palo Alto Networks - Admin UI のテスト ユーザーの作成

Palo Alto Networks - Admin UI では、Just-In-Time ユーザー プロビジョニングがサポートされます。 ユーザーが既に存在していない場合、認証の成功後に自動的にシステム内に作成されます。 ユーザーを作成する操作は不要です。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Palo Alto Networks - Admin UI のサインオン URL にリダイレクトされます。 

* Palo Alto Networks - Admin UI のサインオン URL に直接移動し、そこからログイン フローを開始します。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [Palo Alto Networks - Admin UI] タイルをクリックすると、SSO を設定した Palo Alto Networks - Admin UI に自動的にサインインされます。 マイ アプリの詳細については、[マイ アプリの概要](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Palo Alto Networks - Admin UI を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を適用する方法をご覧ください](/cloud-app-security/proxy-deployment-any-app)。
