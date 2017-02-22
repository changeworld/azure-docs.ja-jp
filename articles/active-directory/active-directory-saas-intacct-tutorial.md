---
title: "チュートリアル: Azure Active Directory と Intacct の統合 | Microsoft Docs"
description: "Azure Active Directory と Intacct の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 970bca01-6480-4a9f-ad99-3e4103b5ea88
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 3a5048300def133c6077179955d9d254b383e73b
ms.openlocfilehash: 7f4795e0622425016a7a1635f9e5d8c7a2e8255c


---
# <a name="tutorial-azure-active-directory-integration-with-intacct"></a>チュートリアル: Azure Active Directory と Intacct の統合

このチュートリアルでは、Intacct と Azure Active Directory (Azure AD) を統合する方法について説明します。

Intacct と Azure AD の統合には、次の利点があります。

- Intacct にアクセスする Azure AD ユーザーを制御できます
- ユーザーが自分の Azure AD アカウントで自動的に Intacct にサインオン (シングル サインオン) できるようにします
- 1 つの中央サイト (Azure 管理ポータル) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

Intacct と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Intacct でのシングル サインオンが有効なサブスクリプション


> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。


このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から&1; か月の評価版を入手できます。


## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の&2; つの要素で構成されています。

1. ギャラリーからの Intacct の追加
2. Azure AD シングル サインオンの構成とテスト


## <a name="adding-intacct-from-the-gallery"></a>ギャラリーからの Intacct の追加
Azure AD への Intacct の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Intacct を追加する必要があります。

**ギャラリーから Intacct を追加するには、次の手順に従います。**

1. **[Azure 管理ポータル](https://portal.azure.com)**の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![アプリケーション][2]
    
3. ダイアログの上部にある **[追加]** をクリックします。

    ![アプリケーション][3]

4. 検索ボックスに、「**Intacct**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-intacct-tutorial/tutorial_intacct_000.png)

5. 結果ウィンドウで **[Intacct]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-intacct-tutorial/tutorial_intacct_0001.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Intacct で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Intacct ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Intacct の関連ユーザーの間で、リンク関係が確立されている必要があります。

このリンク関係を確立するには、Azure AD の **[ユーザー名]** の値を Intacct の **[Username (ユーザー名)]** の値として割り当てます。

Intacct で Azure AD のシングル サインオンを構成してテストするには、次の要素を完了する必要があります。

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Intacct テスト ユーザーの作成](#creating-an-intacct-test-user)** - Intacct で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Microsoft Azure 管理ポータルで Azure AD のシングル サインオンを有効にし、Intacct アプリケーションでシングル サインオンを構成します。

**Intacct で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Microsoft Azure 管理ポータルの **Intacct** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![Configure Single Sign-On][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオンの構成]](./media/active-directory-saas-intacct-tutorial/tutorial_intacct_01.png)

3. **[Intacct のドメインと URL]** セクションで、次の手順を実行します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-intacct-tutorial/tutorial_intacct_02.png)

    a. **[識別子]** ボックスに、「`https://saml.intacct.com`」と入力します。

    b. **[応答 URL]** ボックスに、`https://<company name>.intacct.com/ia/acct/sso_response.phtml` のパターンを使用して URL を入力します。

    > [!NOTE] 
    > これは実際の値ではないので注意してください。 これらの値は、実際の識別子と応答 URL で更新する必要があります。 ここでは、識別子に一意の URL を使用することをお勧めします。 これらの値を取得するには、[Intacct サポート チーム](mailto:info@intacct.com)に連絡してください。 

4. **[SAML 署名証明書**] セクションで、**[新しい証明書の作成]** をクリックします。

    ![Configure Single Sign-On](./media/active-directory-saas-intacct-tutorial/tutorial_intacct_03.png)     

5. **[新しい証明書の作成]**ダイアログで、カレンダー アイコンをクリックし、**期限日**を選択します。 **[保存]** をクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-intacct-tutorial/tutorial_general_300.png)

6. **[SAML 署名証明書]** セクションで、**[Make new certificate active (新しい証明書を有効にする)]** をクリックし、**[保存]** をクリックします。

    ![Configure Single Sign-On](./media/active-directory-saas-intacct-tutorial/tutorial_intacct_04.png)

7. ポップアップ表示される **[Rollover certificate (ロール オーバー証明書)]** ウィンドウで、**[OK]** をクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-intacct-tutorial/tutorial_general_400.png)

8. **[SAML 署名証明書]** セクションで、**[Certificate (Base64) (証明書 (Base64)) ]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![Configure Single Sign-On](./media/active-directory-saas-intacct-tutorial/tutorial_intacct_05.png) 

9. **[Intacct 構成]** セクションで、**[Intacct の構成]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。

    ![Configure Single Sign-On](./media/active-directory-saas-intacct-tutorial/tutorial_intacct_06.png) 

    ![Configure Single Sign-On](./media/active-directory-saas-intacct-tutorial/tutorial_intacct_07.png)

10. 別の Web ブラウザー ウィンドウで、Intacct 企業サイトに管理者としてログインします。

11. **[会社]** タブをクリックし、**[会社情報]** をクリックします。

    ![アプリ側でのシングル サインオンの構成](./media/active-directory-saas-intacct-tutorial/tutorial_intacct_001.png)

12. **[セキュリティ]** タブをクリックし、**[編集]** をクリックします。

    ![アプリ側でのシングル サインオンの構成](./media/active-directory-saas-intacct-tutorial/tutorial_intacct_002.png)

13. **[シングル サインオン (SSO)]** セクションで、次の手順を実行します。

    ![アプリ側でのシングル サインオンの構成](./media/active-directory-saas-intacct-tutorial/tutorial_intacct_003.png)

    a. **[シングル サインオンを有効にする]**を選択します。

    b. **[ID プロバイダーの種類]** として **[SAML 2.0]** を選択します。

    c. **[Issuer URL (発行者 URL)]** ボックスに、Azure AD アプリケーション構成ウィンドウの **[SAML Entity ID (SAML エンティティ ID)]** の値を入力します。

    d. **[Login URL (ログイン URL)]** ボックスに、Azure AD アプリケーション構成ウィンドウの **[SAML Single Sign-on Service URL (SAML シングル サインオン サービス URL)]** の値を入力します。

    e. ダウンロードした証明書をメモ帳で開き、その内容をクリップボードにコピーし、**[Certificate (証明書)]** テキスト ボックスに貼り付けます。

    f. **[保存]**をクリックします。

  

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure 管理ポータルで Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure 管理ポータル**の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-intacct-tutorial/create_aaduser_01.png) 

2. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-intacct-tutorial/create_aaduser_02.png) 

3. ダイアログの上部にある **[追加]** をクリックして **[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-intacct-tutorial/create_aaduser_03.png) 

4. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-intacct-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **[作成]**をクリックします。 



### <a name="creating-an-intacct-test-user"></a>Intacct テスト ユーザーの作成

Azure AD ユーザーが Intacct にログインできるようにするには、そのユーザーを Intacct にプロビジョニングする必要があります。  
Intacct の場合、プロビジョニングは手動で行います。

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>ユーザー アカウントをプロビジョニングするには、次の手順に従います。

1. Intacct 企業サイトに管理者としてログインします。

2. **[会社]** タブをクリックし、**[ユーザー]** をクリックします。

    ![ユーザー プロビジョニング](./media/active-directory-saas-intacct-tutorial/create_aaduser_001.png)

3. **[Add (追加)]** タブをクリックします。

    ![ユーザー プロビジョニング](./media/active-directory-saas-intacct-tutorial/create_aaduser_002.png)

4. **[ユーザー情報]** セクションで、次の手順を実行します。

    ![ユーザー プロビジョニング](./media/active-directory-saas-intacct-tutorial/create_aaduser_003.png)

    a. **[User ID (ユーザー ID)]** ボックスに、Britta Simon アカウントのユーザー ID を入力します。

    b. **[姓]** ボックスに「**Simon**」と入力します。

    c. **[名]** ボックスに「**Britta**」と入力します。  

    d. **[Email address (電子メール アドレス)]** ボックスに、Britta Simon アカウントの電子メール アドレスを入力します。

    e. プロビジョニングする Britta Simon アカウントの**管理者特権**を選択します。

    f. **[Title (タイトル)]** ボックスに、Britta Simon アカウントのタイトルを入力します。

    g. **[Phone (電話)]** ボックスに、Britta Simon アカウントの電話番号を入力します。
    
    h. **[保存]**をクリックします。

    > [!NOTE]
    > Azure Active Directory アカウント所有者がメールを受信し、リンクに従ってアカウントを確認するとそのアカウントがアクティブになります。 Intacct から提供されている他の Intacct ユーザー アカウント作成ツールまたは API を使用して、AAD ユーザー アカウントをプロビジョニングできます。


### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Intacct へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Intacct に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure 管理ポータルでアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[Intacct]** を選択します。

    ![Configure Single Sign-On](./media/active-directory-saas-intacct-tutorial/tutorial_intacct_50.png) 

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** をクリックします。
    


### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで Intacct のタイルをクリックすると、自動的に Intacct アプリケーションにサインオンします。


## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-intacct-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-intacct-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-intacct-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-intacct-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-intacct-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-intacct-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-intacct-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-intacct-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-intacct-tutorial/tutorial_general_203.png


<!--HONumber=Feb17_HO1-->


