---
title: "チュートリアル: Azure Active Directory と Slack の統合 | Microsoft Docs"
description: "Azure Active Directory とSlack の間にシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: ffc5e73f-6c38-4bbb-876a-a7dd269d4e1c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 14972d3e1212fb0cf6653bd4a693470425294d2c
ms.openlocfilehash: c975231ea18c7c1853d9b20fc49542c10ef9abcc


---
# <a name="tutorial-azure-active-directory-integration-with-slack"></a>チュートリアル: Azure Active Directory と Slack の統合

このチュートリアルでは、Slack と Azure Active Directory (Azure AD) を統合する方法について説明します。

Slack と Azure AD の統合には、次の利点があります。

- Slack にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Slack にサインオンする機能 (シングル サインオン) を有効にできます。
- 1 つの中央サイト (Azure 管理ポータル) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

Slack と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Slack でのシングル サインオンが有効なサブスクリプション


> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。


このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から&1; か月の評価版を入手できます。


## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の&2; つの要素で構成されています。

1. ギャラリーからの Slack の追加
2. Azure AD シングル サインオンの構成とテスト


## <a name="adding-slack-from-the-gallery"></a>ギャラリーからの Slack の追加
Azure AD への Slack の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Slack を追加する必要があります。

**ギャラリーから Slack を追加するには、次の手順に従います。**

1. **[Azure 管理ポータル](https://portal.azure.com)**の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![アプリケーション][2]
    
3. ダイアログの上部にある **[追加]** をクリックします。

    ![アプリケーション][3]

4. 検索ボックスに「**Slack**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-slack-tutorial/tutorial_slack_000.png)

5. 結果ウィンドウで **[Slack]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-slack-tutorial/tutorial_slack_0001.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Slack で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Slack ユーザーが Azure AD で認識されている必要があります。 つまり、Azure AD ユーザーと Slack の関連ユーザーの間でリンク関係が確立されている必要があるということです。

このリンク関係を確立するには、Azure AD の **[ユーザー名]** の値を Slack の **[Username]** の値に割り当てます。

Slack で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Slack のテスト ユーザーの作成](#creating-a-slack-test-user)** - Slack で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Microsoft Azure 管理ポータルで Azure AD のシングル サインオンを有効にして、Slack アプリケーションにシングル サインオンを構成します。

**Slack で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Microsoft Azure 管理ポータルの **Slack** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![[シングル サインオンの構成]][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオンの構成]](./media/active-directory-saas-slack-tutorial/tutorial_slack_01.png)

3. **[Slack のドメインと URL]** セクションで、次の手順を実行します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-slack-tutorial/tutorial_slack_02.png)

    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが Yardi eLearning アプリケーションへのサインオンに使用する URL を入力します。 **[サインオン URL]** ボックスに、`https://<company name>.slack.com` のパターンを使用して URL を入力します。

    b. **[識別子]** ボックスに、「`https://slack.com`」と入力します。

    > [!NOTE] 
    > これは実際の値ではないので注意してください。 実際のサインオン URL と識別子でこれらの値を更新する必要があります。 ここでは、識別子に一意の URL を使用することをお勧めします。 この値を取得するには、[Slack サポート チーム](https://slack.com/help/contact)にお問い合わせください。 

4. Slack アプリケーションは、特定の形式で構成された SAML アサーションを受け入れます。 このアプリケーションには、次の要求を構成してください。 この属性の値は、アプリケーション統合ページの **[User Attributer]** セクションで管理できます。 次のスクリーンショットはその例です。
    
    ![Configure Single Sign-On](./media/active-directory-saas-slack-tutorial/tutorial_slack_03.png)

5. **[シングル サインオン]** ダイアログの **[ユーザー属性]** セクションで、**[ユーザー識別子]** として **[user.mai]l** を選択し、以下の表に示す行ごとに、次の手順を実行します。
    
    | 属性名 | 属性値 |
    | --- | --- |    
    | User.Email | user.userprincipalname |
    | first_name | User.givenname |
    | last_name | User.surname |
    | User.Username | extractmailprefix([userprincipalname]) |

    a. **[属性の追加]** をクリックして **[属性の追加]** ダイアログを開きます。

    ![[シングル サインオンの構成]](./media/active-directory-saas-slack-tutorial/tutorial_slack_04.png)

    ![Configure Single Sign-On](./media/active-directory-saas-slack-tutorial/tutorial_slack_05.png)
    
    b. **[名前]** ボックスに、その行に対して表示される属性名を入力します。
    
    c. **[値]** 一覧から、その行に対して表示される値を入力します。
    
    d. **[OK]**

6. **[SAML 署名証明書**] セクションで、**[新しい証明書の作成]** をクリックします。

    ![Configure Single Sign-On](./media/active-directory-saas-slack-tutorial/tutorial_slack_06.png)     

7. **[新しい証明書の作成]**ダイアログで、カレンダー アイコンをクリックし、**期限日**を選択します。 **[保存]** をクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-slack-tutorial/tutorial_general_300.png)

8. **[SAML 署名証明書]** セクションで、**[Make new certificate active (新しい証明書を有効にする)]** をクリックし、**[保存]** をクリックします。

    ![Configure Single Sign-On](./media/active-directory-saas-slack-tutorial/tutorial_slack_07.png)

9. ポップアップ表示される **[Rollover certificate (ロール オーバー証明書)]** ウィンドウで、**[OK]** をクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-slack-tutorial/tutorial_general_400.png)

10. **[SAML 署名証明書]** セクションで、**[Certificate (base64)]** (証明書 (base64)) をクリックし、コンピューターに証明書ファイルを保存します。

    ![Configure Single Sign-On](./media/active-directory-saas-slack-tutorial/tutorial_slack_08.png) 

11. **[Slack Configuration]** (Slack 構成) セクションで、**[Configure Slack]** (Slack を構成する) をクリックして、**[サインオンの構成]** ウィンドウを開きます。

    ![Configure Single Sign-On](./media/active-directory-saas-slack-tutorial/tutorial_slack_09.png) 

    ![Configure Single Sign-On](./media/active-directory-saas-slack-tutorial/tutorial_slack_10.png)

12.  別の Web ブラウザー ウィンドウで、Slacka 企業サイトに管理者としてログインします。

13.  **[Microsoft Azure AD]**、**[Team Settings]** の順に選択します。

    ![アプリ側でのシングル サインオンの構成](./media/active-directory-saas-slack-tutorial/tutorial_slack_001.png)

14.  **[Team Settings]** セクションで、**[Authentication]** タブをクリックし、**[Change Settings]** をクリックします。

    ![アプリ側でのシングル サインオンの構成](./media/active-directory-saas-slack-tutorial/tutorial_slack_002.png)

15. **[SAML Authentication Settings]** ダイアログで、次の手順を実行します。

    ![アプリ側でのシングル サインオンの構成](./media/active-directory-saas-slack-tutorial/tutorial_slack_003.png)

    a.  **[SAML 2.0 Endpoint (HTTP)]** (SAML 2.0 エンドポイント (HTTP)) テキスト ボックスで、Azure AD アプリ構成ウィンドウの **[SAML シングル サインオン サービス URL]** の値を入力します。

    b.  **[Identity Provider Issuer]** ボックスに、Azure AD アプリ構成ウィンドウから **[SAML Entity ID]** (SAML エンティティ ID) の値を入力します。

    c.  ダウンロードした証明書をメモ帳で開き、その内容をクリップボードにコピーし、**[Public Certificate]** ボックスに貼り付けます。

    d. 必要に応じて、Slack チームに上記の&3; つの設定を構成します。 設定の詳細については、**Slack の SSO 構成ガイド**をこちらから参照してください。 `https://get.slack.help/hc/en-us/articles/220403548-Guide-to-single-sign-on-with-Slack`

    e.  **[Save Configuration]**をクリックします。
     
    <!-- **[Allow users to change their email address]** (ユーザーにメール アドレスの変更を許可する) を選択解除します。

    e.  **[Allow users to choose their own username]** を選択します。

    f.  **[Authentication for your team must be used by]** で、**[It’s optional]** を選択します。 -->
  

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure 管理ポータルで Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure 管理ポータル**の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-slack-tutorial/create_aaduser_01.png) 

2. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-slack-tutorial/create_aaduser_02.png) 

3. ダイアログの上部にある **[追加]** をクリックして **[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-slack-tutorial/create_aaduser_03.png) 

4. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-slack-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **[作成]**をクリックします。 



### <a name="creating-a-slack-test-user"></a>Slack テスト ユーザーの作成

このセクションの目的は、Slack で Britta Simon というユーザーを作成することです。 Slack では、Just-In-Time プロビジョニングがサポートされています。この設定は、既定で有効になっています。

このセクションでは、ユーザー側で必要な操作はありません。 存在しない Slack ユーザーにアクセスしようとすると、新しいユーザーが自動的に作成されます。

> [!NOTE]
> ユーザーを手動で作成する必要がある場合は、[Slack のサポート チーム](https://slack.com/help/contact)にお問い合わせください。


### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Slack へのアクセスを許可することで、Britta Simonが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Slack に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure 管理ポータルでアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[Slack]** を選択します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-slack-tutorial/tutorial_slack_50.png) 

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** をクリックします。
    


### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Slack] タイルをクリックすると、Slack アプリケーションに自動的にサインオンします。


## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-slack-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-slack-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-slack-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-slack-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-slack-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-slack-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-slack-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-slack-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-slack-tutorial/tutorial_general_203.png


<!--HONumber=Feb17_HO1-->


