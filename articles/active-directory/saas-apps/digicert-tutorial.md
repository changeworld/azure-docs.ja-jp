---
title: 'チュートリアル: Azure Active Directory と DigiCert の統合 | Microsoft Docs'
description: Azure Active Directory と DigiCert の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 646f3129-aa67-4875-9073-1d0b6a3173d9
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/09/2018
ms.author: jeedes
ms.openlocfilehash: f37ac37d80562a402d6891ffaa2a687e04c3a8c0
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39434882"
---
# <a name="tutorial-azure-active-directory-integration-with-digicert"></a>チュートリアル: Azure Active Directory と DigiCert の統合

このチュートリアルでは、DigiCert と Azure Active Directory (Azure AD) を統合する方法について説明します。

DigiCert と Azure AD の統合には、次の利点があります。

- DigiCert にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に DigiCert にサインオン (シングル サインオン) できるように、設定が可能です。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

DigiCert と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- DigiCert でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの DigiCert の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-digicert-from-the-gallery"></a>ギャラリーからの DigiCert の追加
Azure AD への DigiCert の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に DigiCert を追加する必要があります。

**ギャラリーから DigiCert を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[アプリケーション]][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[アプリケーション]][3]

1. 検索ボックスに、「**DigiCert**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/digicert-tutorial/tutorial_digicert_search.png)

1. 結果ウィンドウで **[DigiCert]** を選択し、**[追加]** をクリックしてアプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/digicert-tutorial/tutorial_digicert_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、DigiCert で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する DigiCert ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと DigiCert の関連ユーザーの間で、リンク関係が確立されている必要があります。

DigiCert で、Azure AD の **[ユーザー名]** の値を **[Username]** の値として割り当ててリンク関係を確立します。

DigiCert で Azure AD のシングル サインオンを構成してテストするには、次の手順を完了する必要があります。

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[DigiCert テスト ユーザーの作成](#creating-a-digicert-test-user)** - DigiCert で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、DigiCert アプリケーションでシングル サインオンを構成します。

**DigiCert で Azure AD シングル サインオンを構成するには、次の手順を実行します。**

1. Azure Portal の **DigiCert** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![Configure single sign-on][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![Configure single sign-on](./media/digicert-tutorial/tutorial_digicert_samlbase.png)

1. **[DigiCert のドメインと URL]** セクションで、次の手順に従います。

    ![Configure single sign-on](./media/digicert-tutorial/tutorial_digicert_url.png)
    
    **[識別子]** ボックスに次の URL を入力します。`https://www.digicert.com/sso`

1. DigiCert アプリケーションは、特定の形式で構成された SAML アサーションを受け入れます。 このアプリケーションには、次の要求を構成します。 この属性の値は、アプリケーション統合ページの **[User Attributer]** セクションで管理できます。 次のスクリーンショットは、この構成の例を示しています。 

    ![Configure single sign-on](./media/digicert-tutorial/tutorial_digicert_attributes.png)
    
1. **[シングル サインオン]** ダイアログの **[ユーザー属性]** セクションで、図に示すように SAML トークン属性を構成し、次の手順を実行します。
    
    | 属性名 | 属性値 |
    | ------------------- | -------------------- |    
    | company | < companycode > |
    | digicertrole | CanAccessCertCentral |

    > [!Note]
    > 属性 **company** の値は実数ではありません。 この値を実際の企業コードで更新します。 **company** 属性の値を取得するには、[DigiCert サポート チーム](mailto:support@digicert.com)に問い合わせてください。

    a. **[属性の追加]** をクリックして **[属性の追加]** ダイアログを開きます。

    ![Configure single sign-on](./media/digicert-tutorial/tutorial_attribute_04.png)

    ![Configure single sign-on](./media/digicert-tutorial/tutorial_attribute_05.png)

    b. **[名前]** ボックスに、その行に対して表示される属性名を入力します。

    c. **[値]** 一覧から、その行に対して表示される値を入力します。
    
    d. **[OK]** をクリックします。 

1. **[SAML 署名証明書]** セクションで、**[Metadata XML (メタデータ XML)]** をクリックし、コンピューターにメタデータ ファイルを保存します。

    ![Configure single sign-on](./media/digicert-tutorial/tutorial_digicert_certificate.png) 

1. **[保存]** ボタンをクリックします。

    ![Configure single sign-on](./media/digicert-tutorial/tutorial_general_400.png)

1. **DigiCert** 側にシングル サインオンを構成するには、ダウンロードした**メタデータ XML** を [DigiCert サポート チーム](mailto:support@digicert.com)に送信する必要があります。 サポート チームはこれを設定して、SAML SSO 接続が両方の側で正しく設定されるようにします。

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/digicert-tutorial/create_aaduser_01.png) 

1. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/digicert-tutorial/create_aaduser_02.png) 

1. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/digicert-tutorial/create_aaduser_03.png) 

1. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/digicert-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **Create** をクリックしてください。
 
### <a name="creating-a-digicert-test-user"></a>DigiCert テスト ユーザーの作成

このセクションでは、DigiCert で Britta Simon というユーザーを作成します。 [DigiCert サポート チーム](mailto:support@digicert.com)と連携して DigiCert でユーザーを作成してください。

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に DigiCert へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**DigiCert に Britta Simon を割り当てるには、次の手順を実行します。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[DigiCert]** を選択します。

    ![Configure single sign-on](./media/digicert-tutorial/tutorial_digicert_app.png) 

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [DigiCert] タイルをクリックすると、自動的に DigiCert アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/digicert-tutorial/tutorial_general_01.png
[2]: ./media/digicert-tutorial/tutorial_general_02.png
[3]: ./media/digicert-tutorial/tutorial_general_03.png
[4]: ./media/digicert-tutorial/tutorial_general_04.png

[100]: ./media/digicert-tutorial/tutorial_general_100.png

[200]: ./media/digicert-tutorial/tutorial_general_200.png
[201]: ./media/digicert-tutorial/tutorial_general_201.png
[202]: ./media/digicert-tutorial/tutorial_general_202.png
[203]: ./media/digicert-tutorial/tutorial_general_203.png

