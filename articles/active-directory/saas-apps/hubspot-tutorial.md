---
title: 'チュートリアル: Azure AD SSO と HubSpot の統合'
description: Azure Active Directory と HubSpot の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/07/2021
ms.author: jeedes
ms.openlocfilehash: 54ca75be0467b2b08c3195dcba1849c42910dd2c
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132291295"
---
# <a name="tutorial-azure-ad-sso-integration-with-hubspot"></a>チュートリアル: Azure AD SSO と HubSpot の統合

このチュートリアルでは、HubSpot と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と HubSpot を統合すると、次のことができます。

* HubSpot にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して HubSpot に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

HubSpot と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD サブスクリプションをお持ちでない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/)を作成してください。
* シングル サインオンが有効な HubSpot のサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストし、HubSpot を Azure AD と統合します。

HubSpot では、次の機能がサポートされています。

* **SP Initiated シングル サインオン**。
* **IDP Initiated シングル サインオン**。

## <a name="add-hubspot-from-the-gallery"></a>ギャラリーから HubSpot を追加する

Azure AD への HubSpot の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に HubSpot を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**HubSpot**」と入力します。
1. 結果のパネルから **[HubSpot]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-hubspot"></a>HubSpot の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、HubSpot 用に Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと HubSpot の関連ユーザーとの間にリンク関係を確立する必要があります。

HubSpot 用に Azure AD SSO を構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[HubSpot SSO の構成](#configure-hubspot-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[HubSpot のテスト ユーザーの作成](#create-hubspot-test-user)** - HubSpot で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

1. Azure portal の **HubSpot** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** ウィンドウで、**IDP 開始モード** を構成するために、次の手順を実行します。

    1. **[識別子]** ボックスに、https:\//api.hubspot.com/login-api/v1/saml/login?portalId=\<CUSTOMER ID\> というパターンの URL を入力します。

    1. **[応答 URL]** ボックスに、https:\//api.hubspot.com/login-api/v1/saml/acs?portalId=\<CUSTOMER ID\> というパターンの URL を入力します。

    > [!NOTE]
    > URL の書式を設定するには、Azure portal の **[基本的な SAML 構成]** ウィンドウに示されているパターンを参照することもできます。

1. *SP-initiated* モードでアプリケーションを構成するには:

    1. **[追加の URL を設定します]** を選択します。

    1. **[サインオン URL]** ボックスに、「**https:\//app.hubspot.com/login**」と入力します。

1. **[SAML でシングル サインオンをセットアップします]** ウィンドウの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** の横にある **[ダウンロード]** を選択します。 要件に基づいてダウンロード オプションを選択します。 お使いのコンピューターに証明書ファイルを保存します。

    ![証明書 (Base64) ダウンロード オプション](common/certificatebase64.png)

1. **[HubSpot のセットアップ]** セクションで、要件に基づいて次の URL をコピーします。

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

このセクションでは、B.Simon に HubSpot へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[HubSpot]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-hubspot-sso"></a>HubSpot SSO の構成

1. ブラウザーで新しいタブを開き、HubSpot の管理者アカウントにサインインします。

1. ページの右上隅にある **[Settings]\(設定\)** アイコンを選択します。

    ![HubSpot の [Settings]\(設定\) アイコン](./media/hubspot-tutorial/icon.png)

1. **[Account Defaults]\(アカウントの既定値\)** を選択します。

    ![HubSpot の [Account Defaults]\(アカウントの既定値\) オプション](./media/hubspot-tutorial/account.png)

1. **[Security]\(セキュリティ\)** セクションまで下にスクロールし、 **[Set up]\(セットアップ\)** を選択します。

    ![HubSpot の [Set up]\(セットアップ\) オプション](./media/hubspot-tutorial/security.png)

1. **[Set up single sign-on]\(シングル サインオンの設定\)** セクションで、次の手順を実行します。

    1. **[Audience URl (Service Provider Entity ID)]\(オーディエンス URI (サービス プロバイダー エンティティ ID)\)** ボックスで、 **[Copy]\(コピー\)** を選択して値をコピーします。 Azure portal で、 **[基本的な SAML 構成]** ウィンドウの **[識別子]** ボックスに値を貼り付けます。

    1. **[Sign on URL, ACS, Recipient, or Redirect]\(サインオン URL、ACS、受信者、またはリダイレクト\)** ボックスで、 **[Copy]\(コピー\)** を選択して値をコピーします。 Azure portal で、 **[基本的な SAML 構成]** ウィンドウの **[応答 URL]** ボックスに値を貼り付けます。

    1. HubSpot の **[Identity Provider Identifier or Issuer URL]\(ID プロバイダーの識別子または発行者 URL\)** ボックスに、Azure portal でコピーした **[Azure AD 識別子]** の値を貼り付けます。

    1. HubSpot の **[Identity Provider Single Sign-On URL]\(ID プロバイダーのシングル サインオン URL\)** ボックスに、Azure portal でコピーした **[ログイン URL]** の値を貼り付けます。

    1. Windows のメモ帳で、ダウンロードした **証明書 (Base64)** ファイルを開きます。 ファイルの内容を選択してコピーします。 次に、HubSpot でそれを **[X.509 Certificate]\(X.509 証明書\)** ボックスに貼り付けます。

    1. **[認証]** を選択します。

        ![HubSpot の [Set up single sign-on]\(シングル サインオンの設定\) セクション](./media/hubspot-tutorial/certificate.png)

### <a name="create-hubspot-test-user"></a>HubSpot のテスト ユーザーの作成

Azure AD ユーザーが HubSpot にサインインできるようにするには、そのユーザーが HubSpot でプロビジョニングされている必要があります。 HubSpot では、プロビジョニングは手動で行います。

HubSpot でユーザー アカウントをプロビジョニングするには

1. HubSpot 企業サイトに管理者としてサインインします。

1. ページの右上隅にある **[Settings]\(設定\)** アイコンを選択します。

    ![HubSpot の [Settings]\(設定\) アイコン](./media/hubspot-tutorial/icon.png)

1. **[Users & Teams]\(ユーザーとチーム\)** を選択します。

    ![HubSpot の [Users & Teams]\(ユーザーとチーム\) オプション](./media/hubspot-tutorial/users.png)

1. **[Create user]\(ユーザーの作成\)** を選択します。

    ![HubSpot の [Create user]\(ユーザーの作成\) オプション](./media/hubspot-tutorial/teams.png)

1. **[Add email addess(es)]\(メール アドレスの追加\)** ボックスにユーザーのメール アドレスを brittasimon\@contoso.com という形式で入力し、 **[Next]\(次へ\)** をクリックします。

    ![HubSpot の [Create users]\(ユーザーの作成\) セクションの [Add email addess(es)]\(メール アドレスの追加\) ボックス](./media/hubspot-tutorial/add-user.png)

1. **[Create users]\(ユーザーの作成\)** セクションで、各タブを選択します。各タブで、ユーザーに関連するオプションとアクセス許可を設定します。 次に、 **[次へ]** を選択します。

    ![HubSpot の [Create users]\(ユーザーの作成\) セクションのタブ](./media/hubspot-tutorial/create-user.png)

1. ユーザーに招待を送信するには、 **[Send]\(送信\)** を選択します。

    ![HubSpot の [Send]\(送信\) オプション](./media/hubspot-tutorial/invitation.png)

    > [!NOTE]
    > ユーザーが招待を受け入れると、ユーザーはアクティブ化されます。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

#### <a name="sp-initiated"></a>SP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる HubSpot のサインオン URL にリダイレクトされます。  

* HubSpot のサインオン URL に直接移動し、そこからログイン フローを開始します。

#### <a name="idp-initiated"></a>IDP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックすると、SSO を設定した HubSpot に自動的にサインインされます。 

また、Microsoft マイ アプリを使用して、任意のモードでアプリケーションをテストすることもできます。 マイ アプリで [HubSpot] タイルをクリックすると、SP モードで構成されている場合は、ログイン フローを開始するためのアプリケーション サインオン ページにリダイレクトされます。IDP モードで構成されている場合は、SSO を設定した HubSpot に自動的にサインインされます。 マイ アプリの詳細については、[マイ アプリの概要](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

HubSpot を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を適用する方法をご覧ください](/cloud-app-security/proxy-deployment-aad)。
