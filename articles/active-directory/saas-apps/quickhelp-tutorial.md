---
title: 'チュートリアル: Azure Active Directory と QuickHelp の統合 | Microsoft Docs'
description: Azure Active Directory と QuickHelp の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/15/2021
ms.author: jeedes
ms.openlocfilehash: a6d9efffcfdf2df8ef52e34a220857f1ab06b104
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132299849"
---
# <a name="tutorial-azure-active-directory-integration-with-quickhelp"></a>チュートリアル: Azure Active Directory と QuickHelp の統合

このチュートリアルでは、QuickHelp と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と QuickHelp を統合すると、次のことが可能になります。

* QuickHelp にアクセスできるユーザーを Azure AD で制御します。
* ユーザーが自分の Azure AD アカウントを使用して QuickHelp に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* QuickHelp でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* QuickHelp では、**SP** によって開始される SSO がサポートされます。

* QuickHelp では、**Just In Time** ユーザー プロビジョニングがサポートされます。

> [!NOTE]
> このアプリケーションの識別子は固定文字列値であるため、1 つのテナントで構成できるインスタンスは 1 つだけです。

## <a name="add-quickhelp-from-the-gallery"></a>ギャラリーからの QuickHelp の追加

Azure AD への QuickHelp の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に QuickHelp を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**QuickHelp**」と入力します。
1. 結果のパネルから **[QuickHelp]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-quickhelp"></a>QuickHelp の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、QuickHelp に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと QuickHelp の関連ユーザーとの間にリンク関係を確立する必要があります。

QuickHelp に Azure AD SSO を構成してテストするには、次の手順を行います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[QuickHelp SSO の構成](#configure-quickhelp-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[QuickHelp のテスト ユーザーの作成](#create-quickhelp-test-user)** - QuickHelp で B.Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **QuickHelp** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    a. **[識別子 (エンティティ ID)]** ボックスに `https://auth.quickhelp.com` という URL を入力します。

    b. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。`https://quickhelp.com/<ROUTE_URL>`

    > [!NOTE]
    > サインオン URL は実際の値ではありません。 実際のサインオン URL でこの値を更新してください。 組織の QuickHelp 管理者や BrainStorm Client Success マネージャーに値を問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして、要件のとおりに指定したオプションから **フェデレーション メタデータ XML** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

6. **[QuickHelp のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

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

このセクションでは、B.Simon に QuickHelp へのアクセスを許可して、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[QuickHelp]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-quickhelp-sso"></a>QuickHelp SSO の構成

1. QuickHelp 企業サイトに管理者としてサインインします。

2. 上部のメニューで **[Admin]** をクリックします。
   
    ![Brainstorm の [管理] メニュー項目のスクリーンショット](./media/quickhelp-tutorial/admin.png "[管理] メニュー")

3. **[QuickHelp の管理者]** メニューの **[設定]** をクリックします。
   
    ![[QuickHelp の管理] メニューの [設定] が選択されている画面のスクリーンショット](./media/quickhelp-tutorial/menu.png "設定")

4. **[認証設定]** をクリックします。

5. **[認証設定]** ページで、次の手順を実行します。
   
    ![[認証設定] ページのスクリーンショット](./media/quickhelp-tutorial/authenticate.png "認証設定")
   
    a. **SSO 型** として **WSFederation** を選びます。
   
    b. ダウンロードした Azure メタデータ ファイルをアップロードするには、 **[参照]** をクリックし、目的のファイルに移動し、 **[メタデータのアップロード]** をクリックします。
   
    c. **[電子メール]** ボックスに「`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`」と入力します。
   
    d. **[名]** ボックスに「`type http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`」と入力します。
   
    e. **[姓]** ボックスに「`type http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`」と入力します。
   
    f. **[操作バー]** で、 **[保存]** をクリックします。

### <a name="create-quickhelp-test-user"></a>QuickHelp のテスト ユーザーの作成

このセクションでは、Britta Simon というユーザーを QuickHelp に作成します。 QuickHelp では、Just-In-Time ユーザー プロビジョニングがサポートされており、既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 QuickHelp にユーザーがまだ存在していない場合は、認証後に新しく作成されます。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる QuickHelp のサインオン URL にリダイレクトされます。 

* QuickHelp のサインオン URL に直接移動し、そこからログイン フローを開始します。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [QuickHelp] タイルをクリックすると、QuickHelp のサインオン URL にリダイレクトされます。 マイ アプリの詳細については、[マイ アプリの概要](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

QuickHelp を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を適用する方法をご覧ください](/cloud-app-security/proxy-deployment-aad)。
