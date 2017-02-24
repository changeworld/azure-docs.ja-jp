---
title: "チュートリアル: Azure Active Directory と Boomi の統合 | Microsoft Docs"
description: "Azure Active Directory と Boomi の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
editor: na
ms.assetid: 8e05afa9-2eda-4975-a0cc-6d408065860f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/02/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 6a60db808388bf1fdf9441518920f2eb4e5fcc4d
ms.openlocfilehash: bafbfb710d8cdb370d21d9299c447a0dfed3c468


---
# <a name="tutorial-azure-active-directory-integration-with-boomi"></a>チュートリアル: Azure Active Directory と Boomi の統合

このチュートリアルでは、Boomi と Azure Active Directory (Azure AD) を統合する方法について説明します。

Boomi と Azure AD の統合には、次の利点があります。

- Boomi にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Boomi にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure クラシック ポータル) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

Boomi と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Boomi でのシングル サインオンが有効なサブスクリプション


> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。


このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。


## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Boomi の追加
2. Azure AD シングル サインオンの構成とテスト


## <a name="adding-boomi-from-the-gallery"></a>ギャラリーからの Boomi の追加
Azure AD への Boomi の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Boomi を追加する必要があります。

**ギャラリーから Boomi を追加するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。 

    ![Active Directory][1]

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

    ![[アプリケーション]][2]

4. ページの下部にある **[追加]** をクリックします。

    ![アプリケーション][3]

5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。

    ![アプリケーション][4]

6. 検索ボックスに、「**Boomi**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_01.png)

7. 結果ウィンドウで **[Boomi]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_02.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Boomi で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Boomi ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Boomi の関連ユーザーの間で、リンク関係が確立されている必要があります。

このリンク関係を確立するには、Azure AD の **[ユーザー名]** の値を Boomi の **[Username]** の値として割り当てます。

Boomi で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Boomi のテスト ユーザーの作成](#creating-a-boomi-test-user)** - Boomi で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションの目的は、Azure クラシック ポータルで Azure AD のシングル サインオンを有効にすることと、Boomi アプリケーションでシングル サインオンを構成することです。

Boomi アプリケーションでは特定の形式の SAML アサーションが要求されるため、ユーザーのフェデレーション ID で NameIdentifier 属性値を設定する必要があります。 既定では、Azure AD は NameIdentifier 属性に UserPrincipalName を使用します。 ただし、正常に統合するには、Boomi でこの値がユーザーのフェデレーション ID と一致するように調整する必要があります。 これは、以下のスクリーン ショットに示すように **[属性]** タブから変更できます。 統合は、適切なマッピングが完了した後にのみ機能します。

![Configure Single Sign-On](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_51.png)

**Boomi で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. クラシック ポータルの **Boomi** アプリケーション統合ページで、**[シングル サインオンの構成]** をクリックして、**[シングル サインオンの構成]** ダイアログを開きます。

    ![Configure Single Sign-On][6] 

2. **[ユーザーの Boomi へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
 
    ![Configure Single Sign-On](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_03.png)

3. **[アプリケーション設定の構成]** ダイアログ ページで、次の手順に従います。

    ![[シングル サインオンの構成]](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_04.png)

    a.[サインオン URL] ボックスに、ユーザーが Tidemark アプリケーションへのサインオンに使用する URL を入力します。 **[応答 URL]** ボックスに、`https://platform.boomi.com/sso/<account name>/saml` のパターンを使用して URL を入力します。

    b. **[次へ]**をクリックします。

    > [!NOTE] 
    > これは実際の値ではないので注意してください。 この値は実際の応答 URL で更新する必要があります。 この値を取得するには、Boomi サポート チームに問い合わせてください。

4. **[Boomi でのシングル サインオンの構成]** ページで、**[証明書のダウンロード]** をクリックし、コンピューターにファイルを保存します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_05.png) 

    > [!NOTE]
    > 応答の NameID 要求値が Boomi システムで構成されているフェデレーション ID と一致する必要があることに注意してください。 そのため、Boomi サポートチームと連携して、組織内の適切なユーザー ID をフェデレーション ID としてマッピングしてください。 既定では、Azure AD によって NameIdentifier が UPN 値として設定されます。 これは、以下のスクリーン ショットに示すように [属性] タブから変更できます。 統合は、適切なマッピングが完了した後にのみ機能します。 
    
    ![Configure Single Sign-On](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_51.png)

5. 別の Web ブラウザー ウィンドウで、Boomi 企業サイトに管理者としてログインします。 

6. **[会社名]**、**[セットアップ]** の順に進みます。

7. **[SSO オプション]** タブをクリックして以下の手順に従います。

    ![アプリ側でのシングル サインオンの構成](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_06.png)

    a.この問題では、ターゲット (またはクラス) ラベルは "tip_amount" です。 **[Enable SAML Single Sign-On](SAML シングル サインオンの有効化)** のチェックボックスをオンにします。

    b. **[インポート]** をクリックして、Azure AD からダウンロードした証明書を **ID プロバーダー証明書** にアップロードします。
    
    c. **[ID プロバイダーのログイン URL]** ボックスに、Azure AD アプリケーションの構成ウィザードの **[リモート ログイン URL]** の値を入力します。

    d. **[Federation Id Location]** で、**[Federation Id is in NameID element of the Subject]** オプション ボタンを選択します。 

    e. **[保存]** ボタンをクリックします。

8. クラシック ポータルで、シングル サインオンの構成確認を選択し、 **[次へ]**をクリックします。

    ![Azure AD のシングル サインオン][10]

9. **[シングル サインオンの確認]** ページで、**[完了]** をクリックします。  
  
    ![Azure AD のシングル サインオン][11]


### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、クラシック ポータルで Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][20]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-boomi-tutorial/create_aaduser_09.png) 

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. 上部のメニューで **[ユーザー]**をクリックして、ユーザーの一覧を表示します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-boomi-tutorial/create_aaduser_03.png) 

4. 下部にあるツール バーで **[ユーザーの追加]** をクリックして、**[ユーザーの追加]** ダイアログ ボックスを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-boomi-tutorial/create_aaduser_04.png) 

5. **[このユーザーに関する情報の入力]** ダイアログ ページで、次の手順に従います。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-boomi-tutorial/create_aaduser_05.png) 

    a.[サインオン URL] ボックスに、ユーザーが Tidemark アプリケーションへのサインオンに使用する URL を入力します。 [ユーザーの種類] として [組織内の新しいユーザー] を選択します。

    b. [ユーザー名] **ボックス**に「**BrittaSimon**」と入力します。

    c. **[次へ]**をクリックします。

6.  **[ユーザー プロファイル]** ダイアログ ページで、次の手順に従います。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-boomi-tutorial/create_aaduser_06.png) 

    a.[サインオン URL] ボックスに、ユーザーが Tidemark アプリケーションへのサインオンに使用する URL を入力します。 **[名]** ボックスに「**Britta**」と入力します。  

    b. **[姓]** ボックスに「**Simon**」と入力します。

    c. **[表示名]** ボックスに「**Britta Simon**」と入力します。

    d. **[ロール]** 一覧で **[ユーザー]** を選択します。

    e. **[次へ]**をクリックします。

7. **[一時パスワードの取得]** ダイアログ ページで、**[作成]** をクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-boomi-tutorial/create_aaduser_07.png) 

8. **[一時パスワードの取得]** ダイアログ ページで、次の手順に従います。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-boomi-tutorial/create_aaduser_08.png) 

    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが Yardi eLearning アプリケーションへのサインオンに使用する URL を入力します。 **[新しいパスワード]** の値を書き留めます。

    b. ページの下部にある **[完了]**」を参照してください。   



### <a name="creating-a-boomi-test-user"></a>Boomi のテスト ユーザーの作成

Azure AD ユーザーが Boomi にログインできるようにするには、ユーザーを Boomi にプロビジョニングする必要があります。 Boomi の場合、プロビジョニングは手動で行います。

####<a name="to-provision-a-user-account-perform-the-following-steps"></a>ユーザー アカウントをプロビジョニングするには、次の手順を実行します。

1. Boomi 企業サイトに管理者としてログインします。

2. **[ユーザー管理]**、**[ユーザー]** の順に進みます。

    ![Users](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_001.png "Users")

3. **+** アイコンをクリックすると **[Add/Maintain User Roles](ユーザーの役割の追加と管理)** ダイアログが開きます。

    ![Users](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_002.png "Users")

    ![Users](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_003.png "Users")

4. ユーザーの**電子メール アドレス**を入力します。

5. ユーザーの**名**、**姓**を入力します。

6. ユーザーの**フェデレーション ID** を入力します。 各ユーザーには、アカウント内のユーザーを一意に識別するフェデレーション ID が必要です。 

7. **標準ユーザー**の役割をユーザーに割り当てます。 シングル サインオンのアクセスのほかに、Boomi AtomSphere の通常のアクセスも付与することになるため、管理者の役割は割り当てないでください。

8. **[OK]**をクリックします。

    > [!NOTE]
    > ユーザー パスワードは ID プロバイダーを通じて管理されるため、AtomSphere アカウントのログインに使用するパスワードを含む、「ようこそ」の通知メールはユーザーに送信されません。 Boomi から提供されている他の Boomi ユーザー アカウント作成ツールまたは API を使用して、AAD ユーザー アカウントをプロビジョニングできます。 

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Boomi へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Britta Simon を Boomi に割り当てるには、次の手順に従います。**

1. クラシック ポータルでアプリケーション ビューを開くために、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[Boomi]** を選択します。

    ![Configure Single Sign-On](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_50.png) 

3. 上部のメニューで **[ユーザー]**をクリックします。

    ![ユーザーの割り当て][203] 

4. ユーザーの一覧で **[Britta Simon]**を選択します。

5. 下部にあるツール バーで **[割り当て]**をクリックします。
    
    ![ユーザーの割り当て][205]



### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Boomi] タイルをクリックすると、自動的に Boomi アプリケーションにサインオンします。


## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_205.png



<!--HONumber=Dec16_HO1-->


