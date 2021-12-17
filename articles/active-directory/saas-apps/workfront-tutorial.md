---
title: 'チュートリアル: Azure Active Directory と Workfront の統合 | Microsoft Docs'
description: Azure Active Directory と Workfront の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/23/2021
ms.author: jeedes
ms.openlocfilehash: 5cf9ff5a847203369c3ade70bd22df4cdb9bc062
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132298425"
---
# <a name="tutorial-azure-active-directory-integration-with-workfront"></a>チュートリアル: Azure Active Directory と Workfront の統合

このチュートリアルでは、Workfront と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Workfront を統合すると、次のことができます。

* Workfront にアクセスできるユーザーを Azure AD で制御する。
* ユーザーが自分の Azure AD アカウントを使用して Workfront に自動的にサインインできるようにする。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

Workfront と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Workfront でのシングル サインオンが有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Workfront では、**SP** Initiated SSO がサポートされます。

## <a name="add-workfront-from-the-gallery"></a>ギャラリーからの Workfront の追加

Azure AD への Workfront の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Workfront を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに、「**Workfront**」と入力します。
1. 結果のパネルから **Workfront** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-workfront"></a>Workfront の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Workfront に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと Workfront の関連ユーザーとの間にリンク関係を確立する必要があります。

Workfront に対して Azure AD SSO を構成してテストするには、次の手順を行います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Workfront の SSO の構成](#configure-workfront-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Workfront テスト ユーザーの作成](#create-workfront-test-user)** - Workfront で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Workfront** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    a. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。`https://<companyname>.attask-ondemand.com`

    b. **[識別子 (エンティティ ID)]** ボックスに、次のパターンを使用して URL を入力します。`https://<companyname>.attasksandbox.com/SAML2`

    > [!NOTE]
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新します。 これらの値を取得するには、[Workfront クライアント サポート チーム](https://www.workfront.com/services-and-support)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションからの **証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

6. **[Workfront のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

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

このセクションでは、B. Simon に Workfront へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Workfront]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-workfront-sso"></a>Workfront の SSO の構成

1. Workfront 企業サイトに管理者としてサインオンします。

2. **[Single Sign On Configuration]** に移動します。

3. **[Single Sign-On (シングル サインオン)]** ダイアログ ボックスで、次の手順を実行します。
    
    ![Configure single sign-on](./media/workfront-tutorial/single-sign-on.png)
   
    a. **[Type]** で **[SAML 2.0]** を選択します。
   
    b. **サービス プロバイダー ID** を選択します。
   
    c. **[Login Portal URL]\(ログイン ポータル URL\)** ボックスに **ログイン URL** を貼り付けます。
   
    d. **[Sign-Out URL]\(サインアウト URL\)** ボックスに **ログアウト URL** を貼り付けます。
   
    e. **パスワード変更 URL** を **[Change Password URL]\(パスワード変更 URL\)** ボックスに貼り付けます。
   
    f. **[保存]** をクリックします。

### <a name="create-workfront-test-user"></a>Workfront テスト ユーザーの作成

このセクションの目的は、Workfront で Britta Simon というユーザーを作成することです。

**Workfront で Britta Simon というユーザーを作成するには、次の手順に従います。**

1. Workfront 企業サイトに管理者としてサインオンします。
 
2. 上部のメニューで **[People]** をクリックします。
 
3. **[New Person]** をクリックします。 

4. [New Person] ダイアログで、次の手順を実行します。
   
    ![Workfront テスト ユーザーを作成する](./media/workfront-tutorial/add-person.png)
   
    a. **[First Name]\(名\)** ボックスに「Britta」と入力します。
   
    b. **[Last Name]\(姓\)** ボックスに「Simon」と入力します。
   
    c. **[Email Address]** ボックスに、Britta Simon の Azure Active Directory の電子メール アドレスを入力します。
   
    d. **[Add Person]** をクリックします。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始する Workfront のサインオン URL にリダイレクトされます。 

* Workfront のサインオン URL に直接移動し、そこからログイン フローを開始します。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [Workfront] タイルをクリックすると、Workfront のサインオン URL にリダイレクトされます。 マイ アプリの詳細については、[マイ アプリの概要](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Workfront を構成したら、自分の組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を適用する方法をご覧ください](/cloud-app-security/proxy-deployment-any-app)。
