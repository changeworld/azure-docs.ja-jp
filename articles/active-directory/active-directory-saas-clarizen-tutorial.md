---
title: "チュートリアル: Azure Active Directory と Clarizen の統合 | Microsoft Docs"
description: "Azure Active Directory と Clarizen の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 149c47fc79c834c948584588326c4dd7240b7ccf
ms.openlocfilehash: c94ba8b4a0508146df7c6abff9b7a757f8fde575


---
# <a name="tutorial-azure-active-directory-integration-with-clarizen"></a>チュートリアル: Azure Active Directory と Clarizen の統合

このチュートリアルでは、Clarizen と Azure Active Directory (Azure AD) を統合する方法について説明します。

Clarizen と Azure AD の統合には、次の利点があります。

- Clarizen にアクセスする Azure AD ユーザーを制御できます
- ユーザーが自分の Azure AD アカウントで自動的に Clarizen にサインオン (シングル サインオン) できるようにします
- 1 つの中央サイト (Azure 管理ポータル) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

Clarizen と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Clarizen でのシングル サインオンが有効なサブスクリプション


> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。


このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から&1; か月の評価版を入手できます。


## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の&2; つの要素で構成されています。

1. ギャラリーからの Clarizen の追加
2. Azure AD シングル サインオンの構成とテスト


## <a name="adding-clarizen-from-the-gallery"></a>ギャラリーからの Clarizen の追加
Azure AD への Clarizen の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Clarizen を追加する必要があります。

**ギャラリーから Clarizen を追加するには、次の手順に従います。**

1. **[Azure 管理ポータル](https://portal.azure.com)**の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![アプリケーション][2]
    
3. ダイアログの上部にある **[追加]** をクリックします。

    ![アプリケーション][3]

4. 検索ボックスに、「**Clarizen**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_000.png)

5. 結果ウィンドウで **[Clarizen]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_0001.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Clarizen で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Clarizen ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Clarizen の関連ユーザーの間で、リンク関係が確立されている必要があります。

このリンク関係を確立するには、Azure AD の **[ユーザー名]** の値を Clarizen の **[Username (ユーザー名)]** の値として割り当てます。

Clarizen で Azure AD のシングル サインオンを構成してテストするには、次の要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Clarizen テスト ユーザーの作成](#creating-a-clarizen-test-user)** - Clarizen で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Microsoft Azure 管理ポータルで Azure AD のシングル サインオンを有効にし、Clarizen アプリケーションでシングル サインオンを構成します。

**Clarizen で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Microsoft Azure 管理ポータルの **Clarizen** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![Configure Single Sign-On][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![Configure Single Sign-On](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_01.png)

3. **[Clarizen のドメインと URL]** セクションで、次の手順を実行します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_02.png)

    a. **[識別子]** ボックスに、値として「`Clarizen`」と入力します。

    b. **[応答 URL]** ボックスに、`https://<company name>.clarizen.com/Clarizen/Pages/Integrations/SAML/SamlResponse.aspx` のパターンを使用して URL を入力します。

    > [!NOTE] 
    > これは実際の値ではないので注意してください。 これらの値は、実際の識別子と応答 URL で更新する必要があります。 ここでは、識別子に一意の文字列値を使用することをお勧めします。 これらの値を取得するには、[Clarizen サポート チーム](https://success.clarizen.com/hc/en-us/requests/new)に連絡してください。 

4. **[SAML 署名証明書**] セクションで、**[新しい証明書の作成]** をクリックします。

    ![Configure Single Sign-On](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_03.png)   

5. **[新しい証明書の作成]**ダイアログで、カレンダー アイコンをクリックし、**期限日**を選択します。 **[保存]** をクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-clarizen-tutorial/tutorial_general_300.png)

6. **[SAML 署名証明書]** セクションで、**[Make new certificate active (新しい証明書を有効にする)]** をクリックし、**[保存]** をクリックします。

    ![Configure Single Sign-On](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_04.png)

7. ポップアップ表示される **[Rollover certificate (ロール オーバー証明書)]** ウィンドウで、**[OK]** をクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-clarizen-tutorial/tutorial_general_400.png)

8. **[SAML 署名証明書]** セクションで、**[Certificate (Base64) (証明書 (Base64)) ]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_05.png) 

9. **[Clarizen 構成]** セクションで、**[Clarizen の構成]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。

    ![Configure Single Sign-On](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_06.png) 

    ![Configure Single Sign-On](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_07.png)

10. 別の Web ブラウザー ウィンドウで、Clarizen 企業サイトに管理者としてログインします。

11. 自分のユーザー名をクリックし、 **[設定]**をクリックします。

    ![設定](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_001.png "Settings")

12. **[グローバル設定]** タブをクリックし、**[フェデレーション認証]** の横にある **[編集]** をクリックします。

    ![グローバル設定](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_002.png "グローバル設定")

13. **[フェデレーション認証]** ダイアログで、次の手順を実行します。

    ![フェデレーション認証](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_003.png "フェデレーション認証")

    a. **[Enable Federated Authentication (フェデレーション認証を有効にする)]** をオンにします。

    b. **[アップロード]** をクリックして、ダウンロードした証明書をアップロードします。

    c. **[Sign-in URL (サインイン URL)]** ボックスに、Azure AD アプリケーション構成ウィンドウの **[SAML Single Sign-on Service URL (SAML シングル サインオン サービス URL)]** の値を入力します。

    d. **[Sign-out URL (サインアウト URL)]** ボックスに、Azure AD アプリケーション構成ウィンドウの **[Sign-Out URL (サインアウト URL)]** の値を入力します。

    e. **[POST を使用]**を選択します。

    f. **[保存]**をクリックします。

  

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure 管理ポータルで Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure 管理ポータル**の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-clarizen-tutorial/create_aaduser_01.png) 

2. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-clarizen-tutorial/create_aaduser_02.png) 

3. ダイアログの上部にある **[追加]** をクリックして **[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-clarizen-tutorial/create_aaduser_03.png) 

4. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-clarizen-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **[作成]**をクリックします。 



### <a name="creating-a-clarizen-test-user"></a>Clarizen のテスト ユーザーの作成

Azure AD ユーザーが Clarizen にログインできるようにするには、そのユーザーを Clarizen にプロビジョニングする必要があります。  
Clarizen の場合、プロビジョニングは手動で行います。

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>ユーザー アカウントをプロビジョニングするには、次の手順に従います。

1. Clarizen 企業サイトに管理者としてログインします。

2. **[ユーザー]**をクリックします。

    ![ユーザー](./media/active-directory-saas-clarizen-tutorial/create_aaduser_001.png "People")

3. **[ユーザーの招待]**をクリックします。

    ![ユーザーの招待](./media/active-directory-saas-clarizen-tutorial/create_aaduser_002.png "ユーザーの招待")

4. **[Invite People (ユーザーの招待)]** ダイアログ ページで、次の手順を実行します。

    ![ユーザーの招待](./media/active-directory-saas-clarizen-tutorial/create_aaduser_003.png "ユーザーの招待")

    a. **[Email (電子メール)]** ボックスに、Britta Simon アカウントの電子メール アドレスを入力します。
    
    b. **[招待]**をクリックします。

    > [!NOTE]
    > Azure Active Directory アカウント所有者がメールを受信し、リンクに従ってアカウントを確認するとそのアカウントがアクティブになります。


### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Clarizen へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Clarizen に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure 管理ポータルでアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[Clarizen]** を選択します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_50.png) 

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** をクリックします。
    


### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Clarizen] タイルをクリックすると、Clarizen アプリケーションに自動的にサインオンします。


## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-clarizen-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-clarizen-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-clarizen-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-clarizen-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-clarizen-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-clarizen-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-clarizen-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-clarizen-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-clarizen-tutorial/tutorial_general_203.png


<!--HONumber=Feb17_HO1-->


