---
title: "チュートリアル: Azure Active Directory と HR2day by Merces の統合 | Microsoft Docs"
description: "Azure Active Directory と HR2day by Merces の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 853d08c9-27b1-48d4-b8e7-3705140eb67f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/24/2017
ms.author: jeedes
ms.translationtype: Human Translation
ms.sourcegitcommit: 138f04f8e9f0a9a4f71e43e73593b03386e7e5a9
ms.openlocfilehash: 456ef3620c1d7bb6ab2bf09b094f977e46cf2ed6
ms.contentlocale: ja-jp
ms.lasthandoff: 06/29/2017


---
# <a name="tutorial-azure-active-directory-integration-with-hr2day-by-merces"></a>チュートリアル: Azure Active Directory と HR2day by Merces の統合

このチュートリアルでは、HR2day by Merces と Azure Active Directory (Azure AD) を統合する方法について説明します。

HR2day by Merces と Azure AD の統合には、次の利点があります。

- HR2day by Merces にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に HR2day by Merces にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

HR2day by Merces と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- HR2day by Merces でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの HR2day by Merces の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-hr2day-by-merces-from-the-gallery"></a>ギャラリーからの HR2day by Merces の追加
Azure AD への HR2day by Merces の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に HR2day by Merces を追加する必要があります。

**ギャラリーから HR2day by Merces を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![アプリケーション][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![アプリケーション][3]

4. [検索] ボックスに、「 **HR2day by Merces**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2daybymerces_search.png)

5. 結果ウィンドウで **[HR2day by Merces]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2daybymerces_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、HR2day by Merces で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する HR2day by Merces ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと HR2day by Merces の関連ユーザーの間で、リンク関係が確立されている必要があります。

HR2day by Merces で、Azure AD の **[ユーザー名]** の値を **[Username]** の値として割り当ててリンク関係を確立します。

HR2day by Merces で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[HR2day by Merces のテスト ユーザーの作成](#creating-an-hr2day-by-merces-test-user)** - HR2day by Merces で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、HR2day by Merces アプリケーションでシングル サインオンを構成します。

**HR2day by Merces で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **HR2day by Merces** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![[シングル サインオンの構成]][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオンの構成]](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2daybymerces_samlbase.png)

3. **[HR2day by Merces Domain and URLs]\(HR2day by Merces のドメインと URL\)** セクションで、次の手順を実行します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2daybymerces_url.png)

    a. **[サインオン URL]** ボックスに、`https://<tenantname>.force.com/<instancename>` のパターンを使用して URL を入力します。

    b. **[識別子]** ボックスに、`https://hr2day.force.com/<companyname>` の形式で URL を入力します。

    > [!NOTE] 
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新してください。 これらの値を取得するには、[HR2day by Merces クライアント サポート チーム](mailto:servicedesk@merces.nl)に連絡してください。 
 


4. **[SAML 署名証明書]** セクションで、**[証明書 (Base64)]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2daybymerces_certificate.png) 

5. このセクションでは、ユーザーが SAML プロトコルに基づくフェデレーションを使用して、Azure AD でのユーザーのアカウントで HR2day by Merces に対する認証を行うことができるようにする方法を説明します。

    HR2day by Merces アプリケーションでは、特定の形式の SAML アサーションを使用するため、カスタム属性マッピングを SAML トークン属性の構成に追加する必要があります。 次のスクリーンショットはその例です。 

    ![[シングル サインオンの構成]](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2day_00.png)
    
    > [!NOTE] 
    SAML アサーションを構成する前に、[HR2day by Merces クライアント サポート チーム](mailto:servicedesk@merces.nl)に連絡し、テナントの一意識別子属性の値を請求する必要があります。 次のセクションの手順を完了するには、この値が必要です。  

6. **[シングル サインオン]** ダイアログの **[ユーザー属性]** セクションで、図に示すように SAML トークン属性を構成し、次の手順を実行します。
    
      | 属性名    |   属性値 |  
    | ------------------- | -------------------- |    
    | ATTR_LOGINCLAIM | join([mail],"102938475Z","@" |
    
      a. **[属性の追加]** をクリックして **[属性の追加]** ダイアログを開きます。

    ![[シングル サインオンの構成]](./media/active-directory-saas-hr2day-tutorial/tutorial_attribute_04.png)

    ![Configure Single Sign-On](./media/active-directory-saas-hr2day-tutorial/tutorial_attribute_05.png)

    b. **[名前]** ボックスに、「**“ATTR_LOGINCLAIM”**」と入力します。

    c. **[値]** の一覧で、**[Join()]** を選択します。

    d. **[文字列 1]** の一覧で、**[user.mail]** を選択します。

    e. **[文字列 2]** の一覧で、HR2day チームから提供された**一意識別子**を入力します。

    f.SAML 属性の属性名またはスキーマ リファレンスを入力します。 **[区切り記号]** テキストボックスに「**@**」を入力します。
    
    g. **[OK]**をクリックします。

7. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-hr2day-tutorial/tutorial_general_400.png)

8. **[HR2day by Merces Configuration]\(HR2day by Merces 構成\)** セクションで、**[Configure HR2day by Merces]\(HR2day by Merces を構成\)** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから、**サインアウト URL、SAML エンティティ ID、SAML シングル サインオン サービス URL** をコピーします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2daybymerces_configure.png) 

9. お使いのアプリケーションで SSO を利用できるように構成するには、ダウンロードした**証明書 (Base64)** ファイルを電子メールに添付して、[HR2day by Merces クライアント サポート チーム](mailTo:servicedesk@merces.nl)に連絡してください。 **サインアウト URL、SAML エンティティ ID、SAML シングル サインオン サービス URL** もお知らせください。サポートチームが SSO 統合で設定します。

    > [!NOTE]
    > Merces チームに、この統合ではエンティティ ID を **https://hr2day.force.com/INSTANCENAME** というパターンで設定する必要があることをお伝えください。
    > 
    > 

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関する記事をご覧ください。
> 

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-hr2day-tutorial/create_aaduser_01.png) 

2. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-hr2day-tutorial/create_aaduser_02.png) 

3. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-hr2day-tutorial/create_aaduser_03.png) 

4. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-hr2day-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. ページの下部にある **[Create]**」を参照してください。
 
### <a name="creating-an-hr2day-by-merces-test-user"></a>HR2day by Merces テスト ユーザーの作成

このセクションの目的は、HR2day by Merces で Britta Simon というユーザーを作成することです。 [HR2day by Merces クライアント サポート チーム](mailto:servicedesk@merces.nl)と連携し、HR2day by Merces アカウントにユーザーを追加してください。 

> [!NOTE]
> ユーザーを手動で作成する必要がある場合は、[HR2day by Merces クライアント サポート チーム](mailto:servicedesk@merces.nl)にお問い合わせください。

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に HR2day by Merces へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**HR2day by Merces に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[HR2day by Merces]**を選択します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2daybymerces_app.png) 

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションの目的は、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストすることです。  

アクセス パネルで [HR2day by Merces] タイルをクリックすると、自動的に HR2day by Merces アプリケーションにサインオンします。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_203.png


