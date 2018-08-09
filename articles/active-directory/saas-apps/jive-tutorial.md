---
title: 'チュートリアル: Azure Active Directory と Jive の統合 | Microsoft Docs'
description: Azure Active Directory と Jive の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 9fc5659a-c116-4a1b-a601-333325a26b46
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2018
ms.author: jeedes
ms.openlocfilehash: cebcfb4614d1f685697bed6914f80237e175fb7b
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39436558"
---
# <a name="tutorial-azure-active-directory-integration-with-jive"></a>チュートリアル: Azure Active Directory と Jive の統合

このチュートリアルでは、Jive と Azure Active Directory (Azure AD) を統合する方法について説明します。

Jive と Azure AD の統合には、次の利点があります。

- Jive にアクセスできる Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Jive にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Jive と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Jive でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。
このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Jive の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-jive-from-the-gallery"></a>ギャラリーからの Jive の追加
Azure AD への Jive の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Jive を追加する必要があります。

**ギャラリーから Jive を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[アプリケーション]][2]

1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[アプリケーション]][3]

1. 検索ボックスに、「 **Jive**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/jive-tutorial/tutorial_jive_search.png)

1. 結果ウィンドウで **[Jive]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/jive-tutorial/tutorial_jive_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Jive で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Jive ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Jive 内の対応しているユーザーの間で、リンク関係が確立されている必要があります。

このリンク関係を確立するには、Azure AD の **[ユーザー名]** の値を Jive の **[Username]** の値として割り当てます。

Jive で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[Jive テスト ユーザーの作成](#creating-a-jive-test-user)** - Azure AD の Britta Simon にリンクさせるために、対応するユーザーを Jive で作成します。
1. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にし、Jive アプリケーションでシングル サインオンを構成します。

**Jive で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Jive** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![Configure single sign-on][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。

    ![Configure single sign-on](./media/jive-tutorial/tutorial_jive_samlbase.png)

1. **[Jive のドメインと URL]** セクションで、次の手順を実行します。

    ![Configure single sign-on](./media/jive-tutorial/tutorial_jive_url.png)

    a. **[サインオン URL]** ボックスに、`https://<instance name>.jivecustom.com` のパターンを使用して URL を入力します。

    b. **[識別子]** ボックスに、`https://<instance name>.jiveon.com` の形式で URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新してください。 これらの値を取得するには、[Jive クライアント サポート チーム](https://www.jivesoftware.com/services-support/)に連絡してください。

1. **[SAML 署名証明書]** セクションで、**[メタデータ XML]** をクリックし、コンピューターに XML ファイルを保存します。

    ![Configure single sign-on](./media/jive-tutorial/tutorial_jive_certificate.png)

1. **[保存]** ボタンをクリックします。

    ![Configure single sign-on](./media/jive-tutorial/tutorial_general_400.png)

1. **Jive** 側でシングル サインオンを構成するには、管理者として Jive テナントにサインオンします。

1. 上部のメニューで、**[SAML]** をクリックします。

    ![アプリ側でのシングル サインオンの構成](./media/jive-tutorial/tutorial_jive_002.png)

    a. **[General]\(全般\)** タブで **[Enabled]\(有効\)** を選択します。b. **[Save all saml settings (すべての SAML 設定を保存)]** ボタンをクリックします。

1. **[Idp Metadata (IDP メタデータ)]** タブに移動します。

    ![アプリ側でのシングル サインオンの構成](./media/jive-tutorial/tutorial_jive_003.png)

    a. ダウンロードしたメタデータ XML ファイルの内容をコピーし、**[Identity Provider (IDP) Metadata (ID プロバイダー (IDP) のメタデータ)]** ボックスに貼り付けます。

    b. **[Save all saml settings (すべての SAML 設定を保存)]** ボタンをクリックします。

1. **[User Attribute Mapping (ユーザー属性のマッピング)]** タブに移動します。

    ![アプリ側でのシングル サインオンの構成](./media/jive-tutorial/tutorial_jive_004.png)

    a. **[Email (電子メール)]** ボックスに、**mail** 値の属性名をコピーして貼り付けます。

    b. **[First Name (名)]** ボックスに、**givenname** 値の属性名をコピーして貼り付けます。

    c. **[Last Name (姓)]** ボックスに、**surname** 値の属性名をコピーして貼り付けます。

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/jive-tutorial/create_aaduser_01.png) 

1. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。

    ![Azure AD のテスト ユーザーの作成](./media/jive-tutorial/create_aaduser_02.png)

1. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。

    ![Azure AD のテスト ユーザーの作成](./media/jive-tutorial/create_aaduser_03.png) 

1. **[ユーザー]** ダイアログ ページで、次の手順を実行します。

    ![Azure AD のテスト ユーザーの作成](./media/jive-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **Create** をクリックしてください。

### <a name="creating-a-jive-test-user"></a>Jive テスト ユーザーの作成

このセクションの目的は、Jive で Britta Simon というユーザーを作成することです。 Jive では、自動ユーザー プロビジョニングがサポートされています。この設定は、既定で有効になっています。 自動ユーザー プロビジョニングの構成方法の詳細については、[こちら](jive-provisioning-tutorial.md)で確認できます。

ユーザーを手動で作成する必要がある場合、[Jive クライアント サポート チーム](https://www.jivesoftware.com/services-support/)と連携し、Jive プラットフォームにユーザーを追加してください。

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Jive へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようします。

![ユーザーの割り当て][200] 

**Jive に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[Jive]** を選択します。

    ![Configure single sign-on](./media/jive-tutorial/tutorial_jive_app.png) 

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Jive] タイルをクリックすると、自動的に Jive アプリケーションにサインオンします。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)
* [[ユーザー プロビジョニングの構成]](jive-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/jive-tutorial/tutorial_general_01.png
[2]: ./media/jive-tutorial/tutorial_general_02.png
[3]: ./media/jive-tutorial/tutorial_general_03.png
[4]: ./media/jive-tutorial/tutorial_general_04.png

[100]: ./media/jive-tutorial/tutorial_general_100.png

[200]: ./media/jive-tutorial/tutorial_general_200.png
[201]: ./media/jive-tutorial/tutorial_general_201.png
[202]: ./media/jive-tutorial/tutorial_general_202.png
[203]: ./media/jive-tutorial/tutorial_general_203.png
