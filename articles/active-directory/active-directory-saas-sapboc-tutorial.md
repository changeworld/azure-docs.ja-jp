---
title: "チュートリアル: Azure Active Directory と SAP Business Object Cloud の統合 | Microsoft Docs"
description: "Azure Active Directory と SAP Business Object Cloud の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 6c5e44f0-4e52-463f-b879-834d80a55cdf
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: jeedes
ms.translationtype: Human Translation
ms.sourcegitcommit: ef1e603ea7759af76db595d95171cdbe1c995598
ms.openlocfilehash: 85723eeb9c030dc51bdad47ea8e0996ff7868336
ms.contentlocale: ja-jp
ms.lasthandoff: 06/16/2017

---
# <a name="tutorial-azure-active-directory-integration-with-sap-business-object-cloud"></a>チュートリアル: Azure Active Directory と SAP Business Object Cloud の統合

このチュートリアルでは、SAP Business Object Cloud と Azure Active Directory (Azure AD) を統合する方法について説明します。

SAP Business Object Cloud と Azure AD の統合には、次の利点があります。

- SAP Business Object Cloud にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に SAP Business Object Cloud にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Azure AD と SAP Business Object Cloud の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- SAP Business Object Cloud でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーから SAP Business Object Cloud を追加する
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-sap-business-object-cloud-from-the-gallery"></a>ギャラリーから SAP Business Object Cloud を追加する
Azure AD への SAP Business Object Cloud の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に SAP Business Object Cloud を追加する必要があります。

**ギャラリーから SAP Business Object Cloud を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![アプリケーション][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![アプリケーション][3]

4. 検索ボックスに、「**SAP Business Object Cloud**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-sapboc-tutorial/tutorial_sapboc_search.png)

5. 結果ウィンドウで **[SAP Business Object Cloud]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-sapboc-tutorial/tutorial_sapboc_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、SAP Business Object Cloud で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する SAP Business Object Cloud ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと SAP Business Object Cloud の関連ユーザーの間で、リンク関係が確立されている必要があります。

SAP Business Object Cloud で、Azure AD の **[ユーザー名]** の値を **[ユーザー名]** の値として割り当ててリンク関係を確立します。

SAP Business Object Cloud で Azure AD のシングル サインオンを構成してテストするには、次の手順を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[SAP Business Object Cloud のテスト ユーザーの作成](#creating-an-sap-business-object-cloud-test-user)** - SAP Business Object Cloud で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、SAP Business Object Cloud アプリケーションでシングル サインオンを構成します。

**SAP Business Object Cloud で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **SAP Business Object Cloud** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![[シングル サインオンの構成]][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオンの構成]](./media/active-directory-saas-sapboc-tutorial/tutorial_sapboc_samlbase.png)

3. **[SAP Business Object Cloud のドメインと URL]** セクションで、次の手順に従います。

    ![[シングル サインオンの構成]](./media/active-directory-saas-sapboc-tutorial/tutorial_sapboc_url.png)

    a. **[サインオン URL]** ボックスに、`https://<sub-domain>.projectorca.cloud/` のパターンを使用して URL を入力します。

    b. **[識別子]** ボックスに、`<sub-domain>.projectorca.cloud` の形式で URL を入力します。

    > [!NOTE] 
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新してください。 サインオン URL を取得するには、[SAP Business Object Cloud クライアント サポート チーム](https://www.sap.com/product/analytics/cloud-analytics.support.html)に問い合わせてください。SAP Business Object Cloud メタデータをダウンロードするための識別子は、このチュートリアルで後述する管理コンソールから取得できます。 
 
4. **[SAML 署名証明書]** セクションで、**[Metadata XML (メタデータ XML)]** をクリックし、コンピューターにメタデータ ファイルを保存します。

    ![Configure Single Sign-On](./media/active-directory-saas-sapboc-tutorial/tutorial_sapboc_certificate.png) 

5. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-sapboc-tutorial/tutorial_general_400.png)

6. 別の Web ブラウザーのウィンドウで、SAP Business Object Cloud 企業サイトに管理者としてログインします。

7. **[Menu]\(メニュー\) > [System]\(システム\) > [Administration]\(管理\)** の順に選択します。
    
    ![[シングル サインオンの構成]](./media/active-directory-saas-sapboc-tutorial/config1.png)    
8. **[Security]\(セキュリティ\)** タブの **[Edit]\(編集\)** (ペンのアイコン) をクリックします。
    
    ![[シングル サインオンの構成]](./media/active-directory-saas-sapboc-tutorial/config2.png)    
9. 認証方法として **[SAML Single Sign-On (SSO)]\(SAML シングル サインオン (SSO)\)** を選択します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-sapboc-tutorial/config3.png)  

10. **[Download]\(ダウンロード\)** をクリックして Service Provider メタデータをダウンロードします。 ファイルから **entityID** 値を取得して、Azure Portal の **[SAP Business Object Cloud のドメインと URL]** セクションにある **[識別子]** ボックスに貼り付けます。

    ![[シングル サインオンの構成]](./media/active-directory-saas-sapboc-tutorial/config4.png)  

11. **[Upload Identity Provider metadata]\(ID プロバイダーのメタデータのアップロード\)** の手順で、**[Upload...]\(アップロード...\)** ボタンをクリックして、Azure Portal からダウンロードしたメタデータ ファイルをアップロードします。 

    ![[シングル サインオンの構成]](./media/active-directory-saas-sapboc-tutorial/config5.png)

12. **[User Attribute]\(ユーザー属性\)** で、実装に使用する適切なユーザー属性を一覧から選択して ID プロバイダーにマップします。 [Custom SAML Mapping]\(カスタム SAML マッピング\) オプションを使用して、ユーザー ページにカスタム属性を入力するか、ユーザー属性として [Email]\(電子メール\) または [USER ID]\(ユーザー ID\) を選択します。 ここでは、 Azure Portal の **[ユーザー属性]** セクションにある userprincipalname 属性を使用してユーザー ID 要求をマップしたので、**[Email]\(電子メール\)** を選択しています。これにより、一意のユーザーの電子メールが提供され、成功したすべての SAML 応答で SAP Business Object Cloud アプリケーションに送信されます。

    ![[シングル サインオンの構成]](./media/active-directory-saas-sapboc-tutorial/config6.png)

13. **[Login Credential (Email)]\(ログイン資格情報 (電子メール)\)** ボックスに電子メール アドレスを入力し、**[Verify Account]\(アカウントの確認\)** ボタンをクリックして、システムがアカウントにログイン資格情報を追加できるようにします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-sapboc-tutorial/config7.png)

14. **[Save]\(保存\)** アイコンをクリックします。

    ![[保存]](./media/active-directory-saas-sapboc-tutorial/save.png)

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関する記事をご覧ください。
> 

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-sapboc-tutorial/create_aaduser_01.png) 

2. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-sapboc-tutorial/create_aaduser_02.png) 

3. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-sapboc-tutorial/create_aaduser_03.png) 

4. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-sapboc-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. ページの下部にある **[Create]**」を参照してください。
 
### <a name="creating-an-sap-business-object-cloud-test-user"></a>SAP Business Object Cloud のテスト ユーザーの作成

Azure AD ユーザーが SAP Business Object Cloud にログインできるようにするには、そのユーザーを SAP Business Object Cloud にプロビジョニングする必要があります。  
SAP Business Object Cloud の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. SAP Business Object Cloud 企業サイトに管理者としてログインします。

2. **[Menu]\(メニュー\) > [Security]\(セキュリティ\) > [Users]\(ユーザー\)** の順に選択します。

    ![従業員の追加](./media/active-directory-saas-sapboc-tutorial/user1.png)

3. **[Users]\(ユーザー\)** ページで、**[+]** をクリックして新しいユーザーの詳細を追加し、次の手順を実行します。

    ![[ユーザーの招待]](./media/active-directory-saas-sapboc-tutorial/user4.png)

    a. **[USER ID]\(ユーザー ID\)** ボックスに、ユーザー ID を入力します (この例では Britta)。

    b. **[FIRST NAME]\(名\)** ボックスに、ユーザーの名を入力します (この例では Britta)。

    c. **[LAST NAME]\(姓\)** ボックスに、ユーザーの姓を入力します (この例では Simon)。

    d. **[DISPLAY NAME]\(表示名\)** ボックスに、ユーザーの氏名を入力します (この例では Britta Simon)。

    e. **[E-MAIL]\(電子メール\)** ボックスに、ユーザーの電子メール アドレスを入力します (この例では brittasimon@contoso.com)。

    f.SAML 属性の属性名またはスキーマ リファレンスを入力します。 ユーザーに割り当てる適切なロールを選択します。

      ![役割](./media/active-directory-saas-sapboc-tutorial/user3.png)

    g. **[Save]\(保存\)** アイコンをクリックします。 


### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に SAP Business Object Cloud へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**SAP Business Object Cloud に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[SAP Business Object Cloud]** を選択します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-sapboc-tutorial/tutorial_sapboc_app.png) 

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [SAP Business Object Cloud] タイルをクリックすると、自動的に SAP Business Object Cloud アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_203.png


