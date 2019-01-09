---
title: チュートリアル:Azure Active Directory と TextMagic の統合 | Microsoft Docs
description: Azure Active Directory と TextMagic の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 3e5b49d2-7096-46bc-a9ce-90e09177ba28
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2018
ms.author: jeedes
ms.openlocfilehash: 5ab193d908063230946ebb2bb6320ab50bf14971
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/07/2018
ms.locfileid: "53014890"
---
# <a name="tutorial-azure-active-directory-integration-with-textmagic"></a>チュートリアル:Azure Active Directory と TextMagic の統合

このチュートリアルでは、TextMagic と Azure Active Directory (Azure AD) を統合する方法について説明します。

TextMagic と Azure AD の統合には、次の利点があります。

- TextMagic にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に TextMagic にサインオン (シングル サインオン) できるように、設定が可能です。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

TextMagic と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- TextMagic でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーから TextMagic を追加する
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-textmagic-from-the-gallery"></a>ギャラリーから TextMagic を追加する

Azure AD への TextMagic の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に TextMagic を追加する必要があります。

**ギャラリーから TextMagic を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

4. 検索ボックスに「**TextMagic**」と入力し、結果ウィンドウで **TextMagic** を選び、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果一覧の TextMagic](./media/textmagic-tutorial/tutorial_textmagic_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、TextMagic で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する TextMagic ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと TextMagic の関連ユーザーの間で、リンク関係が確立されている必要があります。

TextMagic で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[TextMagic のテスト ユーザーの作成](#creating-a-textmagic-test-user)** - Azure AD の Britta Simon にリンクさせるために、対応するユーザーを TextMagic で作成します。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、TextMagic アプリケーションでシングル サインオンを構成します。

**TextMagic で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **TextMagic** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

2. **[シングル サインオン方式の選択]** ダイアログで、**[SAML]** モードの **[選択]** をクリックして、シングル サインオンを有効にします。

    ![Configure single sign-on](common/tutorial_general_301.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、**[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![Configure single sign-on](common/editconfigure.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    ![[TextMagic のドメインと URL] のシングル サインオン情報](./media/textmagic-tutorial/tutorial_textmagic_url.png)

    **[識別子]** ボックスに次の URL を入力します。`https://my.textmagic.com/saml/metadata`

5. TextMagic アプリケーションは、特定の形式で構成された SAML アサーションを受け入れます。 このアプリケーションには、次の要求を構成します。 これらの属性の値は、アプリケーション統合ページの **[ユーザー属性と要求]** セクションで管理できます。 **[SAML でシングル サインオンをセットアップします]** ページで、**[編集]** ボタンをクリックして **[ユーザー属性と要求]** ダイアログを開きます。

    ![image](./media/textmagic-tutorial/i4-attribute.png)

6. **[ユーザー属性と要求]** ダイアログの **[ユーザーの要求]** セクションで、上の図のように SAML トークン属性を構成し、次の手順を実行します。

    | Name  | ソース属性  | 名前空間 |
    | --------------- | --------------- | --------------- |
    | company | user.companyname | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | firstName               | User.givenname |  http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | lastName            | User.surname |  http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | phone               | user.telephonenumber |  http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    
    a. **[編集] アイコン**をクリックして、**[名前識別子の値]** を **user.userprinicipalname** から **user.mail** に編集します。

    ![TextMagic 属性](./media/textmagic-tutorial/tutorial_textmagic_email.png)

    b. **[新しい要求の追加]** をクリックして **[ユーザー要求の管理]** ダイアログを開きます。

    ![image](./common/new_save_attribute.png)

    ![image](./common/new_attribute_details.png)

    c. **[名前]** ボックスに、その行に対して表示される属性名を入力します。

    d. **[名前空間]** 値を入力します。

    e. [ソース] として **[属性]** を選択します。

    f. **[ソース属性]** の一覧から、その行に表示される属性値を入力します。

    g. **[OK]** をクリックします。

    h. **[Save]** をクリックします。 

7. **[SAML 署名証明書]** ページの **[SAML 署名証明書]** セクションで、**[ダウンロード]** をクリックして**証明書 (Base64)** をダウンロードし、証明書ファイルをコンピューターに保存します。

    ![証明書のダウンロードのリンク](./media/textmagic-tutorial/tutorial_textmagic_certificate.png) 

8. **[TextMagic の設定]** セクションで、要件に従う適切な URL をコピーします。

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

    ![TextMagic の構成](common/configuresection.png)

9. 別の Web ブラウザーのウィンドウで、TextMagic 企業サイトに管理者としてログインします。

10. ユーザー名で **[Account settings]\(アカウント設定\)** を選択します。

    ![TextMagic の構成](./media/textmagic-tutorial/config1.png)

11. **[Single Sign-On (SSO)]\(シングル サインオン (SSO)\)** タブをクリックし、次のフィールドを入力します。  
    
    ![TextMagic の構成](./media/textmagic-tutorial/config2.png)

    a.  **[Identity Provider Entity ID:]\(ID プロバイダー エンティティ ID\)**  ボックスに、Azure portal からコピーした  **Azure AD 識別子**の値を貼り付けます。

    b.  **[Identity provider SSO URL:]\(ID プロバイダー SSO URL\)**  ボックスに、Azure portal からコピーした **ログイン URL** の値を貼り付けます。

    c.  **[Identity provider SLO URL:]\(ID プロバイダー SLO URL\)**  ボックスに、Azure portal からコピーした **ログアウト URL** の値を貼り付けます。

    d. Azure portal からダウンロードした **base-64 でエンコードされた証明書**をメモ帳で開き、その内容をクリップボードにコピーしてから、それを  **[Public x509 certificate:]\(パブリック x509 証明書:\)**  ボックスに貼り付けます。

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、**[Azure Active Directory]**、**[ユーザー]**、**[すべてのユーザー]** の順に選択します。

    ![Azure AD ユーザーの作成][100]

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![Azure AD のテスト ユーザーの作成](common/create_aaduser_01.png) 

3. [ユーザーのプロパティ] で、次の手順を実行します。

    ![Azure AD のテスト ユーザーの作成](common/create_aaduser_02.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    b. **[ユーザー名]** フィールドに「**brittasimon@yourcompanydomain.extension**」と入力します  
    たとえば、BrittaSimon@contoso.com のように指定します。

    c. **[プロパティ]** を選択し、**[パスワードを表示]** チェック ボックスをオンにして、[パスワード] ボックスに表示された値を書き留めます。

    d. **作成**を選択します。
  
### <a name="creating-a-textmagic-test-user"></a>TextMagic テスト ユーザーを作成する

アプリケーションでは、**Just-In-Time のユーザー プロビジョニング**がサポートされ、認証後にユーザーがアプリケーションに自動的に作成されます。 初めてシステムにログインしてサブアカウントを有効にしたら、情報を入力する必要があります。
このセクションでは、ユーザー側で必要な操作はありません。

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に TextMagic へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、**[すべてのアプリケーション]** を選択します。

    ![ユーザーの割り当て][201]

2. アプリケーションの一覧で **TextMagic** を選択します。

    ![Configure single sign-on](./media/textmagic-tutorial/tutorial_textmagic_app.png) 

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202]

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. **[割り当ての追加]** ダイアログで、**[割り当て]** ボタンを選択します。

### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで TextMagic のタイルをクリックすると、TextMagic アプリケーションに自動的にサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関するページを参照してください。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: common/tutorial_general_01.png
[2]: common/tutorial_general_02.png
[3]: common/tutorial_general_03.png
[4]: common/tutorial_general_04.png

[100]: common/tutorial_general_100.png

[201]: common/tutorial_general_201.png
[202]: common/tutorial_general_202.png
[203]: common/tutorial_general_203.png
