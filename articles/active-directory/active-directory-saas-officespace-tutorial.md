---
title: "チュートリアル: Azure Active Directory と OfficeSpace Software の統合 | Microsoft Docs"
description: "Azure Active Directory と OfficeSpace Software の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 95d8413f-db98-4e2c-8097-9142ef1af823
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 01e2bcf3fa32546a7952ddc919f99b46a74755a2
ms.openlocfilehash: e0933b1a7e19fc70f9c5e81225b296412837385e


---
# <a name="tutorial-azure-active-directory-integration-with-officespace-software"></a>チュートリアル: Azure Active Directory と OfficeSpace Software の統合

このチュートリアルでは、OfficeSpace Software と Azure Active Directory (Azure AD) を統合する方法について説明します。

OfficeSpace Software と Azure AD の統合には、次の利点があります。

- OfficeSpace Software にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に OfficeSpace Software にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure クラシック ポータル) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

OfficeSpace Software と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- シングル サインオン対応の OfficeSpace Software サブスクリプション


> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。


このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から&1; か月の評価版を入手できます。


## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の&2; つの要素で構成されています。

1. ギャラリーからの OfficeSpace Software の追加
2. Azure AD シングル サインオンの構成とテスト


## <a name="adding-officespace-software-from-the-gallery"></a>ギャラリーからの OfficeSpace Software の追加
Azure AD への OfficeSpace Software の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に OfficeSpace Software を追加する必要があります。

**ギャラリーから OfficeSpace Software を追加するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。 

    ![Active Directory][1]

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

    ![[アプリケーション]][2]

4. ページの下部にある **[追加]** をクリックします。

    ![アプリケーション][3]

5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。

    ![アプリケーション][4]

6. 検索ボックスに、「**OfficeSpace Software**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_001.png)

7. 結果ウィンドウで **[OfficeSpace Software]** を選び、**[完了]** をクリックしてアプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_0001.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、OfficeSpace Software で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する OfficeSpace Software ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと OfficeSpace Software の関連ユーザーの間で、リンク関係が確立されている必要があります。

このリンク関係を確立するには、Azure AD の **[ユーザー名]** の値を OfficeSpace Software の **[Username (ユーザー名)]** の値として割り当てます。

OfficeSpace Software で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[OfficeSpace Software のテスト ユーザーの作成](#creating-an-officespace-software-test-user)** - OfficeSpace Software で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションの目的は、Azure クラシック ポータルで Azure AD のシングル サインオンを有効にすることと、OfficeSpace Software アプリケーションでシングル サインオンを構成することです。

OfficeSpace Software アプリケーションは、特定の形式で構成された SAML アサーションを受け入れます。 このアプリケーションには、次の要求を構成してください。 この属性の値は、アプリケーションの**[属性]**タブから管理できます。 次のスクリーンショットはその例です。 

![[シングル サインオンの構成]](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_01.png)

**OfficeSpace Software で Azure AD のシングル サインオンを構成するには、次の手順に従います。**

1. Azure クラシック ポータルの **OfficeSpace Software** アプリケーション統合ページで、上部のメニューから **[属性]** をクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_02.png)

2. **[Saml トークン属性]** ダイアログで、以下の表の各行について、次の手順を実行します。
    
    | 属性名 | 属性値 |
    | --- | --- |    
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier | User.mail |
    | email | User.mail |
    | name | user.displayname |
    | first_name | User.givenname |
    | last_name | User.surname |

    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが Yardi eLearning アプリケーションへのサインオンに使用する URL を入力します。 **[ユーザー属性の追加]** をクリックして、**[ユーザー属性の追加]** ダイアログを開きます。

    ![[シングル サインオンの構成]](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_03.png)
    
    b. **[属性名]** ボックスに、その行に対して表示される属性名を入力します。
    
    c. **[属性値]** リストで、当該行に対して示されている属性値を入力します。
    
    d. ページの下部にある **[完了]**

3. 上部のメニューで **[クイック スタート]**をクリックします。

    ![Configure Single Sign-On](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_04.png) 

4. クラシック ポータルの **OfficeSpace Software** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。

    ![[シングル サインオンの構成]](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_05.png)

5. **[ユーザーの OfficeSpace Software へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
 
    ![[シングル サインオンの構成]](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_06.png)

6. **[アプリケーション設定の構成]** ダイアログ ページで、次の手順に従います。

    ![[シングル サインオンの構成]](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_07.png)

    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが Yardi eLearning アプリケーションへのサインオンに使用する URL を入力します。 **[サインオン URL]** ボックスに、`https://<company name>.officespace.com/users/sign_in/saml` のパターンを使用して URL を入力します。

    b. **[次へ]**をクリックします。

    > [!NOTE] 
    > これは実際の値ではないので注意してください。 この値は実際のサインオン URL で更新する必要があります。 この値の取得については、[OfficeSpace Software サポート チーム](emaiLto:support@officespacesoftware.com)にお問い合わせください。

7. **[OfficeSpace Software でのシングル サインオンの構成]** ページで、**[証明書のダウンロード]** をクリックし、コンピューターにファイルを保存します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_08.png) 

8. 別の Web ブラウザーのウィンドウで、管理者として OfficeSpace Software テナントにログインします。

9. **[ADMIN (管理)]** に移動して、**[Connectors (コネクタ)]**をクリックします。

    ![アプリ側でのシングル サインオンの構成](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_002.png)

10. **[SAML 承認]**をクリックします。

    ![アプリ側でのシングル サインオンの構成](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_003.png)

11. **[SAML 承認]** セクションで、次の手順に従います。

    ![アプリ側でのシングル サインオンの構成](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_004.png)

    a.この問題では、ターゲット (またはクラス) ラベルは "tip_amount" です。 **[Logout provider url (ログアウト プロバイダー URL)]** ボックスに、Azure AD アプリケーションの構成ウィザードの **[リモート ログイン URL]** の値を入力します。

    b. **[Client idp target url (クライアント IdP ターゲット URL)]** ボックスに、Azure AD アプリケーションの構成ウィザードの **[リモート ログアウト URL]** の値を入力します。

    c. ダウンロードした証明書から **[拇印]** の値をコピーして、**[Client idp cert fingerprint (クライアント IdP 証明書の指紋)]** ボックスに貼り付けます。 

    d. **[設定の保存]**をクリックします。

    > [!NOTE]
    > 詳細については、「 [How to retrieve a certificate's thumbprint value (証明書のサムプリント値を取得する方法)](http://youtu.be/YKQF266SAxI)

12. クラシック ポータルで、シングル サインオンの構成確認を選択し、**[次へ]**をクリックします。

    ![Azure AD のシングル サインオン][10]

13. **[シングル サインオンの確認]** ページで、**[完了]** をクリックします。  
  
    ![Azure AD のシングル サインオン][11]


### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、クラシック ポータルで Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][20]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-officespace-tutorial/create_aaduser_09.png) 

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. 上部のメニューで **[ユーザー]**をクリックして、ユーザーの一覧を表示します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-officespace-tutorial/create_aaduser_03.png) 

4. 下部にあるツール バーで **[ユーザーの追加]** をクリックして、**[ユーザーの追加]** ダイアログ ボックスを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-officespace-tutorial/create_aaduser_04.png) 

5. **[このユーザーに関する情報の入力]** ダイアログ ページで、次の手順に従います。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-officespace-tutorial/create_aaduser_05.png) 

    a.[サインオン URL] ボックスに、ユーザーが Tidemark アプリケーションへのサインオンに使用する URL を入力します。 [ユーザーの種類] として [組織内の新しいユーザー] を選択します。

    b. [ユーザー名] **ボックス**に「**BrittaSimon**」と入力します。

    c. **[次へ]**をクリックします。

6.  **[ユーザー プロファイル]** ダイアログ ページで、次の手順に従います。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-officespace-tutorial/create_aaduser_06.png) 

    a.[サインオン URL] ボックスに、ユーザーが Tidemark アプリケーションへのサインオンに使用する URL を入力します。 **[名]** ボックスに「**Britta**」と入力します。  

    b. **[姓]** ボックスに「**Simon**」と入力します。

    c. **[表示名]** ボックスに「**Britta Simon**」と入力します。

    d. **[ロール]** 一覧で **[ユーザー]** を選択します。

    e. **[次へ]**をクリックします。

7. **[一時パスワードの取得]** ダイアログ ページで、**[作成]** をクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-officespace-tutorial/create_aaduser_07.png) 

8. **[一時パスワードの取得]** ダイアログ ページで、次の手順に従います。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-officespace-tutorial/create_aaduser_08.png) 

    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが Yardi eLearning アプリケーションへのサインオンに使用する URL を入力します。 **[新しいパスワード]** の値を書き留めます。

    b. ページの下部にある **[完了]**」を参照してください。   



### <a name="creating-an-officespace-software-test-user"></a>OfficeSpace Software のテスト ユーザーの作成

このセクションの目的は、OfficeSpace Software で Britta Simon というユーザーを作成することです。 OfficeSpace Software では、ジャスト イン タイム プロビジョニングがサポートされています。この設定は、既定で有効になっています。

このセクションでは、ユーザー側で必要な操作はありません。 ユーザーが存在しない場合は、OfficeSpace Software へのアクセスを試みたときに、新しいユーザーが自動的に作成されます。

> [!NOTE]
> ユーザーを手動で作成する必要がある場合は、[OfficeSpace Software サポート チーム](emaiLto:support@officespacesoftware.com)にお問い合わせください。


### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に OfficeSpace Software へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**OfficeSpace Software に Britta Simon を割り当てるには、次の手順に従います。**

1. クラシック ポータルでアプリケーション ビューを開くために、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で、**[OfficeSpace Software]** を選択します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_50.png) 

3. 上部のメニューで **[ユーザー]**をクリックします。

    ![ユーザーの割り当て][203] 

4. ユーザーの一覧で **[Britta Simon]**を選択します。

5. 下部にあるツール バーで **[割り当て]**をクリックします。
    
    ![ユーザーの割り当て][205]



### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [OfficeSpace Software] タイルをクリックすると、自動的に OfficeSpace Software アプリケーションにサインオンします。


## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_205.png



<!--HONumber=Dec16_HO2-->


