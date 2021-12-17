---
title: チュートリアル:Azure Active Directory と Deputy の統合 | Microsoft Docs
description: Azure Active Directory と Deputy の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/11/2021
ms.author: jeedes
ms.openlocfilehash: 258c7b6e88129367c512af4b21c794351f31f243
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132280467"
---
# <a name="tutorial-azure-active-directory-integration-with-deputy"></a>チュートリアル:Azure Active Directory と Deputy の統合

このチュートリアルでは、Deputy を Azure Active Directory (Azure AD) と統合する方法について説明します。 Deputy を Azure AD と統合すると、次のことができます。

* Deputy にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Deputy に自動的にサインインできるようにします。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Deputy のシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Deputy により、**SP および IDP** Initiated SSO がサポートされます。
* Deputy により、**Just In Time** ユーザー プロビジョニングがサポートされます。

## <a name="add-deputy-from-the-gallery"></a>ギャラリーから Deputy を追加する

Azure AD への Deputy の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Deputy を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Deputy**」と入力します。
1. 結果のパネルから **Deputy** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-deputy"></a>Deputy に対して Azure AD SSO を構成し、テストする

**B.Simon** というテスト ユーザーを使用して、Deputy に Azure AD SSO を構成してテストします。 SSO を機能させるために、Azure AD ユーザーと Deputy の関連ユーザーとの間にリンク関係を確立する必要があります。

Azure AD SSO と Deputy を構成してテストするには、次の手順を行います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Deputy の SSO の構成](#configure-deputy-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Deputy のテスト ユーザーの作成](#create-deputy-test-user)** - Deputy で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Deputy** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次の手順を実行します。

    a. **[識別子]** テキスト ボックスに、次のいずれかのパターンを使用して URL を入力します。

    ```http
    https://<subdomain>.<region>.au.deputy.com
    https://<subdomain>.<region>.ent-au.deputy.com
    https://<subdomain>.<region>.na.deputy.com
    https://<subdomain>.<region>.ent-na.deputy.com
    https://<subdomain>.<region>.eu.deputy.com
    https://<subdomain>.<region>.ent-eu.deputy.com
    https://<subdomain>.<region>.as.deputy.com
    https://<subdomain>.<region>.ent-as.deputy.com
    https://<subdomain>.<region>.la.deputy.com
    https://<subdomain>.<region>.ent-la.deputy.com
    https://<subdomain>.<region>.af.deputy.com
    https://<subdomain>.<region>.ent-af.deputy.com
    https://<subdomain>.<region>.an.deputy.com
    https://<subdomain>.<region>.ent-an.deputy.com
    https://<subdomain>.<region>.deputy.com
    ```

    b. **[応答 URL]** ボックスに、次のいずれかのパターンを使用して URL を入力します。
    
    ```http
    https://<subdomain>.<region>.au.deputy.com/exec/devapp/samlacs
    https://<subdomain>.<region>.ent-au.deputy.com/exec/devapp/samlacs
    https://<subdomain>.<region>.na.deputy.com/exec/devapp/samlacs
    https://<subdomain>.<region>.ent-na.deputy.com/exec/devapp/samlacs
    https://<subdomain>.<region>.eu.deputy.com/exec/devapp/samlacs
    https://<subdomain>.<region>.ent-eu.deputy.com/exec/devapp/samlacs
    https://<subdomain>.<region>.as.deputy.com/exec/devapp/samlacs.
    https://<subdomain>.<region>.ent-as.deputy.com/exec/devapp/samlacs
    https://<subdomain>.<region>.la.deputy.com/exec/devapp/samlacs
    https://<subdomain>.<region>.ent-la.deputy.com/exec/devapp/samlacs
    https://<subdomain>.<region>.af.deputy.com/exec/devapp/samlacs
    https://<subdomain>.<region>.ent-af.deputy.com/exec/devapp/samlacs
    https://<subdomain>.<region>.an.deputy.com/exec/devapp/samlacs
    https://<subdomain>.<region>.ent-an.deputy.com/exec/devapp/samlacs
    https://<subdomain>.<region>.deputy.com/exec/devapp/samlacs
    ```

5. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    **[サインオン URL]** ボックスに、`https://<your-subdomain>.<region>.deputy.com` という形式で URL を入力します。

    >[!NOTE]
    > Deputy リージョン サフィックスはオプションです。または次のいずれかを使用する必要があります。au | na | eu |as |la |af |an |ent-au |ent-na |ent-eu |ent-as | ent-la | ent-af | ent-an

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL でこれらの値を更新します。 これらの値を取得するには、[Deputy クライアント サポート チーム](https://www.deputy.com/call-centers-customer-support-scheduling-software)に連絡してください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. Deputy アプリケーションは特定の形式の SAML アサーションを使用するため、カスタム属性マッピングを SAML トークン属性の構成に追加する必要があります。 次のスクリーンショットには、既定の属性一覧が示されています。

    ![image](common/default-attributes.png)

1. その他に、Deputy アプリケーションでは、いくつかの属性が SAML 応答で返されることが想定されています。それらの属性を次に示します。 これらの属性も値が事前に設定されますが、要件に従ってそれらの値を確認することができます。
    
    | 名前 |  ソース属性|
    | -------------- | --------- |
    | 名 | User.givenname|
    | 姓 | User.surname |

6. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションからの **証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

7. **[Deputy のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

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

このセクションでは、B.Simon に Deputy へのアクセスを許可して、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Deputy]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-deputy-sso"></a>Deputy SSO の構成

1. Deputy アカウントに管理者としてログインします。

1. 右上隅にあるアカウントをクリックし、 **[Business settings]\(ビジネス設定\)** を選択します。

    ![[Business settings]\(ビジネス設定\) のスクリーンショット](./media/deputy-tutorial/business-settings.png)

1. 次に、 **[General]\(全般\)** タブの **[Single Sign-On settings]\(シングル サインオン設定\)** をクリックします。

    ![[Single Sign-On settings]\(シングル サインオン設定\) のスクリーンショット](./media/deputy-tutorial/general.png)

2. この **[Single Sign-On settings]\(シングル サインオン設定\)** ページで、次の手順を行います。

    ![Configure single sign-on](./media/deputy-tutorial/configuration.png)
    
    a. **[Enable Single Sign-On]\(シングル サインオンを有効にする\)** をクリックします。
   
    b. **[Identity provider login URL]\(ID プロバイダーのログイン URL\)** テキストボックスに、Azure portal からコピーした **ログイン URL** を貼り付けます。

    c. **[Identity Provider Issuer]\(ID プロバイダー発行者\)** テキストボックスに、Azure portal からコピーした **識別子 (エンティティ ID)** を貼り付けます。

    d. Azure portal からダウンロードした **証明書 (Base64)** をメモ帳で開き、その内容を **[X.509 certificate]\(X.509 証明書\)** テキストボックスに貼り付けます。

    e. SSO を使ってログインする場合は、 **[Single sign-on login required]\(シングル サインオン ログインが必要\)** を有効にします。
   
    f. **[Just-in-time provisioning]\(Just-In-Time プロビジョニング\)** を有効にし、 **[First name]\(名\)** と **[Last name]\(姓\)** フィールドに、`First name` と `Last name` のように、 **[User Attributes & Claims]\(ユーザー属性とクレーム\)** セクションで設定した属性の名前を入力します。

    g. **[変更の適用]** をクリックします。

### <a name="create-deputy-test-user"></a>Deputy のテスト ユーザーの作成

このセクションでは、Deputy で Britta Simon というユーザーを作成します。 Deputy では、Just-In-Time ユーザー プロビジョニングがサポートされています。これは既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 Deputy にユーザーがまだ存在していない場合は、認証後に新規に作成されます。

#### <a name="to-add-the-user-manually-perform-the-following-steps"></a>ユーザーを手動で追加するには、次の手順を行います。

1. Deputy 企業サイトに管理者としてログインします。

2. ナビゲーション ウィンドウの上部にある **[ユーザー]** をクリックします。

3. **[Add People]\(ユーザーの追加\)** ボタンをクリックし、 **[Add Single Person]\(1 人のユーザーの追加\)** をクリックします。
   
    ![ユーザーの追加](./media/deputy-tutorial/create-user-1.png "ユーザーの追加")

4. **[General]\(全般\)** タブで次の手順を行って、ユーザーを追加します。
   
    ![[New User]\(新しいユーザー\)](./media/deputy-tutorial/create-user-2.png "[新しいユーザー]")

    a. **[First name]\(名\)** および **[Last name]\(姓\)** テキストボックスに、**Britta** と **Simon** のように、フィールドに入力します。
   
    b. **[Work at]\(勤務先\)** ボックスに、勤務先の名前を入力します。
   
    c. **[保存]** ボタンをクリックします。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

#### <a name="sp-initiated"></a>SP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる TeamzSkill のサインオン URL にリダイレクトされます。  

* TeamzSkill のサインオン URL に直接移動し、そこからログイン フローを開始します。

#### <a name="idp-initiated"></a>IDP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックすると、SSO を設定した TeamzSkill に自動的にサインインされます 

また、Microsoft マイ アプリを使用して、任意のモードでアプリケーションをテストすることもできます。 マイ アプリで TeamzSkill タイルをクリックすると、SP モードで構成されている場合は、ログイン フローを開始するためのアプリケーション サインオン ページにリダイレクトされます。IDP モードで構成されている場合は、SSO を設定した TeamzSkill に自動的にサインインされます。 マイ アプリの詳細については、[マイ アプリの概要](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)に関するページを参照してください。


## <a name="next-steps"></a>次のステップ

Deputy を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。
