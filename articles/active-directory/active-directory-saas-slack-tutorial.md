---
title: "チュートリアル: Azure Active Directory と Slack の統合 | Microsoft Docs"
description: "Azure Active Directory で Slack を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: c7a0b761-75b7-4e6b-9980-71d645643a78
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/21/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 4d5144c6a690c2338dec45b27dcb308328b6fecb
ms.openlocfilehash: 37440a8ba397c4dc227a448dfa574cebd14be49c


---

# <a name="tutorial-azure-active-directory-integration-with-slack"></a>チュートリアル: Azure Active Directory と Slack の統合

このチュートリアルの目的は、Slack と Azure Active Directory (Azure AD) を統合する方法を説明することです。

Slack と Azure AD の統合には、次の利点があります。

- Slack にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Slack にサインオンする機能 (シングル サインオン) を有効にできます。
- 1 つの中央サイト (Azure クラシック ポータル) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

Slack と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Slack でのシングル サインオンが有効なサブスクリプション


> [!NOTE]
>  このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。


このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。


## <a name="scenario-description"></a>シナリオの説明
このチュートリアルの目的は、テスト環境で Azure AD のシングル サインオンをテストできるようにすることです。

このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Slack の追加
2. Azure AD シングル サインオンの構成とテスト


## <a name="adding-slack-from-the-gallery"></a>ギャラリーからの Slack の追加
Azure AD への Slack の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Slack を追加する必要があります。

**ギャラリーから Slack を追加するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。 

    ![Active Directory][1]

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
    
    ![[アプリケーション]][2]

4. ページの下部にある **[追加]** をクリックします。
    
    ![アプリケーション][3]

5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。

    ![アプリケーション][4]

6. 検索ボックスに「**Slack**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-slack-tutorial/tutorial_slack_01.png)

7. 結果ウィンドウで **[Slack]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。

    ![ギャラリーでアプリを選択する](./media/active-directory-saas-slack-tutorial/tutorial_slack_0001.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションの目的は、"Britta Simon" というテスト ユーザーを基に、Slack で Azure AD のシングル サインオンを構成し、テストする方法について説明することです。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Slack ユーザーが Azure AD で認識されている必要があります。 つまり、Azure AD ユーザーと Slack の関連ユーザーの間でリンク関係が確立されている必要があるということです。

このリンク関係を確立するには、Azure AD の **[ユーザー名]** の値を Slack の **[Username]** の値に割り当てます。

Slack で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Slack のテスト ユーザーの作成](#creating-a-slack-test-user)** - Slack で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションの目的は、Azure クラシック ポータルで Azure AD のシングル サインオンを有効にすることと、Slack アプリケーションでシングル サインオンを構成することです。

Slack アプリケーションは、特定の形式で構成された SAML アサーションを受け入れます。 このアプリケーションには、次の要求を構成してください。 この属性の値は、アプリケーションの**[属性]**タブから管理できます。 次のスクリーンショットはその例です。 

![Configure Single Sign-On](./media/active-directory-saas-slack-tutorial/tutorial_slack_09.png)

**Slack で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure クラシック ポータルの **Slack** アプリケーション統合ページで、上部のメニューの **[属性]** をクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-slack-tutorial/tutorial_slack_03.png)

2. **[Saml トークン属性]** ダイアログで、以下の表の各行について、次の手順を実行します。

    | 属性名 | 属性値 |
    | --- | --- |    
    | User.Email | user.userprincipalname |
    | first_name | User.givenname |
    | last_name | User.surname |
    | User.Username | extractmailprefix([userprincipalname]) |

    a.[サインオン URL] ボックスに、ユーザーが Tidemark アプリケーションへのサインオンに使用する URL を入力します。 **[ユーザー属性の追加]** をクリックして、**[ユーザー属性の追加]** ダイアログを開きます。

    ![[シングル サインオンの構成]](./media/active-directory-saas-slack-tutorial/tutorial_slack_04.png)
    
    b. **[属性名]** ボックスに、その行に対して表示される属性名を入力します。
    
    c. **[属性値]** リストで、当該行に対して示されている属性値を入力します。
    
    d. ページの下部にある **[完了]**

3. 上部のメニューで **[クイック スタート]**をクリックします。

    ![Configure Single Sign-On](./media/active-directory-saas-slack-tutorial/tutorial_slack_05.png) 

4. **[ユーザーの Slack へのアクセスを設定してください]** ページで、**[Azure AD Single Sign-On] (Azure AD のシングル サインオン)** を選択し、**[次へ]** をクリックします。
    
    ![[シングル サインオンの構成]](./media/active-directory-saas-slack-tutorial/tutorial_slack_06.png)

5. **[アプリケーション設定の構成]** ダイアログ ページで、次の手順を実行し、**[次へ]** をクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-slack-tutorial/tutorial_slack_07.png)

    a.[サインオン URL] ボックスに、ユーザーが Tidemark アプリケーションへのサインオンに使用する URL を入力します。 **[サインオン URL]** ボックスに、`https://<company name>.slack.com` のパターンを使用して URL を入力します。

    b. ページの下部にある **[次へ]**」を参照してください。

    > [!NOTE] 
    > この値は実際のサインオン URL で更新する必要があることに注意してください。 この値を取得するには、Slack サポート チームに問い合わせてください。

6. **[Slack でのシングル サインオンの構成]** ページで、**[証明書のダウンロード]** をクリックし、コンピューターにファイルを保存します。

    ![Configure Single Sign-On](./media/active-directory-saas-slack-tutorial/tutorial_slack_08.png)

7.  別の Web ブラウザー ウィンドウで、Slacka 企業サイトに管理者としてログインします。

8.  **[Microsoft Azure AD]**、**[Team Settings]** の順に選択します。

    ![アプリ側でのシングル サインオンの構成](./media/active-directory-saas-slack-tutorial/tutorial_slack_001.png)

9.  **[Team Settings]** セクションで、**[Authentication]** タブをクリックし、**[Change Settings]** をクリックします。

    ![アプリ側でのシングル サインオンの構成](./media/active-directory-saas-slack-tutorial/tutorial_slack_002.png)

10. **[SAML Authentication Settings]** ダイアログで、次の手順を実行します。

    ![アプリ側でのシングル サインオンの構成](./media/active-directory-saas-slack-tutorial/tutorial_slack_003.png)

    a.[サインオン URL] ボックスに、ユーザーが Tidemark アプリケーションへのサインオンに使用する URL を入力します。  **[SAML 2.0 Endpoint (HTTP)]** ボックスに、Azure AD アプリケーション構成ウィザードの **[SAML SSO URL]** の値を入力します。

    b.  **[Identity Provider Issuer]** ボックスに、Azure AD アプリケーション構成ウィザードの **[発行者の URL]** の値を入力します。

    c.  ダウンロードした証明書をメモ帳で開き、その内容をクリップボードにコピーし、**[Public Certificate]** ボックスに貼り付けます。

    d.  **[Allow users to change their email address]**を選択解除します。

    e.  **[Allow users to choose their own username]**を選択します。

    f.  **[Authentication for your team must be used by]** で、**[It’s optional]** を選択します。

    g.  **[Save Configuration]**をクリックします。

11. クラシック ポータルで、シングル サインオンの構成確認を選択し、 **[次へ]**をクリックします。
    
    ![Azure AD のシングル サインオン][10]

12. **[シングル サインオンの確認]** ページで、**[完了]** をクリックします。  
    
    ![Azure AD のシングル サインオン][11]



### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、クラシック ポータルで Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][20]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-slack-tutorial/create_aaduser_09.png)

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. 上部のメニューで **[ユーザー]**をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-slack-tutorial/create_aaduser_03.png)

4. 下部にあるツール バーで **[ユーザーの追加]** をクリックして、**[ユーザーの追加]** ダイアログ ボックスを開きます。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-slack-tutorial/create_aaduser_04.png)

5. **[このユーザーに関する情報の入力]** ダイアログ ページで、次の手順に従います。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-slack-tutorial/create_aaduser_05.png)

    a.[サインオン URL] ボックスに、ユーザーが Tidemark アプリケーションへのサインオンに使用する URL を入力します。 [ユーザーの種類] として [組織内の新しいユーザー] を選択します。

    b. [ユーザー名] **ボックス**に「**BrittaSimon**」と入力します。

    c. **[次へ]**をクリックします。

6.  **[ユーザー プロファイル]** ダイアログ ページで、次の手順に従います。
    
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-slack-tutorial/create_aaduser_06.png)

    a.[サインオン URL] ボックスに、ユーザーが Tidemark アプリケーションへのサインオンに使用する URL を入力します。 **[名]** ボックスに「**Britta**」と入力します。  

    b. **[姓]** ボックスに「**Simon**」と入力します。

    c. **[表示名]** ボックスに「**Britta Simon**」と入力します。

    d. **[ロール]** 一覧で **[ユーザー]** を選択します。

    e. **[次へ]**をクリックします。

7. **[一時パスワードの取得]** ダイアログ ページで、**[作成]** をクリックします。
    
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-slack-tutorial/create_aaduser_07.png)

8. **[一時パスワードの取得]** ダイアログ ページで、次の手順に従います。
    
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-slack-tutorial/create_aaduser_08.png)

    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが Yardi eLearning アプリケーションへのサインオンに使用する URL を入力します。 **[新しいパスワード]** の値を書き留めます。

    b. ページの下部にある **[完了]**」を参照してください。   



### <a name="creating-a-slack-test-user"></a>Slack テスト ユーザーの作成

このセクションの目的は、Slack で Britta Simon というユーザーを作成することです。 Slack では、Just-In-Time プロビジョニングがサポートされています。この設定は、既定で有効になっています。

このセクションでは、ユーザー側で必要な操作はありません。 存在しない Slack ユーザーにアクセスしようとすると、新しいユーザーが自動的に作成されます。

> [!NOTE] 
> ユーザーを手動で作成する必要がある場合は、Slack のサポート チームにお問い合わせください。


### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションの目的は、Britta Simon に Slack へのアクセスを許可することで、このユーザーが Azure のシングル サインオンを使用できるようにすることです。
    
![ユーザーの割り当て][200]

**Slack に Britta Simon を割り当てるには、次の手順に従います。**

1. クラシック ポータルでアプリケーション ビューを開くために、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
    
    ![ユーザーの割り当て][201]

2. アプリケーションの一覧で **[Slack]** を選択します。
    
    ![Configure Single Sign-On](./media/active-directory-saas-slack-tutorial/tutorial_slack_50.png)

3. 上部のメニューで **[ユーザー]**をクリックします。
    
    ![ユーザーの割り当て][203]

4. ユーザーの一覧で **[Britta Simon]**を選択します。

5. 下部にあるツール バーで **[割り当て]**をクリックします。
    
    ![ユーザーの割り当て][205]

### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションの目的は、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストすることです。
 
アクセス パネルで [Slack] タイルをクリックすると、Slack アプリケーションに自動的にサインオンします。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-slack-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-slack-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-slack-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-slack-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-slack-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-slack-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-slack-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-slack-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-slack-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-slack-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-slack-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-slack-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-slack-tutorial/tutorial_general_205.png



<!--HONumber=Nov16_HO4-->


