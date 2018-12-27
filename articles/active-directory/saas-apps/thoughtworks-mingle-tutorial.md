---
title: 'チュートリアル: Azure Active Directory と Thoughtworks Mingle の統合 | Microsoft Docs'
description: Azure Active Directory と Thoughtworks Mingle の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 69d859d9-b7f7-4c42-bc8c-8036138be586
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: jeedes
ms.openlocfilehash: a685b5702aa9f74f3e0abf2a06774a30ac0d996f
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39436994"
---
# <a name="tutorial-azure-active-directory-integration-with-thoughtworks-mingle"></a>チュートリアル: Azure Active Directory と Thoughtworks Mingle の統合

このチュートリアルでは、Thoughtworks Mingle と Azure Active Directory (Azure AD) を統合する方法について説明します。

Thoughtworks Mingle と Azure AD の統合には、次の利点があります。

- Thoughtworks Mingle にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで Thoughtworks Mingle に自動サインオン (シングル サインオン) できるようになります。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Azure AD と Thoughtworks Mingle の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Thoughtworks Mingle でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Thoughtworks Mingle の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-thoughtworks-mingle-from-the-gallery"></a>ギャラリーからの Thoughtworks Mingle の追加
Azure AD への Thoughtworks Mingle の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Thoughtworks Mingle を追加する必要があります。

**ギャラリーから Thoughtworks Mingle を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

1. 検索ボックスに「**Thoughtworks Mingle**」と入力し、結果ウィンドウで **[Thoughtworks Mingle]** を選び、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果一覧の Thoughtworks Mingle](./media/thoughtworks-mingle-tutorial/tutorial_thoughtworksmingle_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Thoughtworks Mingle で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Thoughtworks Mingle ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Thoughtworks Mingle の関連ユーザーの間で、リンク関係が確立されている必要があります。

Thoughtworks Mingle で、Azure AD の **[ユーザー名]** の値を **[Username]** の値として割り当ててリンク関係を確立します。

Thoughtworks Mingle で Azure AD のシングル サインオンを構成してテストするには、次の手順を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[Thoughtworks Mingle テスト ユーザーの作成](#create-a-thoughtworks-mingle-test-user)** - Thoughtworks Mingle で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、Thoughtworks Mingle アプリケーションでシングル サインオンを構成します。

**Thoughtworks Mingle で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Thoughtworks Mingle** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![Configure single sign-on][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/thoughtworks-mingle-tutorial/tutorial_thoughtworksmingle_samlbase.png)

1. **[Thoughtworks Mingle のドメインと URL]** セクションで、次の手順を実行します。

    ![[Thoughtworks Mingle のドメインと URL] のシングル サインオン情報](./media/thoughtworks-mingle-tutorial/tutorial_thoughtworksmingle_url.png)

    **[サインオン URL]** ボックスに、`https://<companyname>.mingle.thoughtworks.com` のパターンを使用して URL を入力します。

    > [!NOTE] 
    > この値は実際のものではありません。 実際のサインオン URL でこの値を更新してください。 この値を取得するには、[Thoughtworks Mingle クライアント サポート チーム](https://support.thoughtworks.com/hc/categories/201743486-Mingle-Community-Support)に問い合わせてください。 
 
1. **[SAML 署名証明書]** セクションで、**[Metadata XML (メタデータ XML)]** をクリックし、コンピューターにメタデータ ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/thoughtworks-mingle-tutorial/tutorial_thoughtworksmingle_certificate.png) 

1. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/thoughtworks-mingle-tutorial/tutorial_general_400.png)

1. **Thoughtworks Mingle** 企業サイトに管理者としてログインします。

1. **[Admin]** タブをクリックし、**[SSO Config]** をクリックします。
   
    ![[Admin] タブ](./media/thoughtworks-mingle-tutorial/ic785157.png "SSO Config")

1. **[SSO Config]** セクションで、次の手順に従います。
   
    ![SSO Config](./media/thoughtworks-mingle-tutorial/ic785158.png "SSO Config")
    
    a. メタデータ ファイルをアップロードするには、 **[Choose file]** をクリックします。 

    b. **[変更を保存]** をクリックします。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関するページを参照してください。
> 

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](./media/thoughtworks-mingle-tutorial/create_aaduser_01.png) 

1. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/thoughtworks-mingle-tutorial/create_aaduser_02.png) 

1. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![[追加] ボタン](./media/thoughtworks-mingle-tutorial/create_aaduser_03.png) 

1. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![[ユーザー] ダイアログ ボックス](./media/thoughtworks-mingle-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **Create** をクリックしてください。
 
### <a name="create-a-thoughtworks-mingle-test-user"></a>Thoughtworks Mingle テスト ユーザーの作成

Azure AD ユーザーがサインインできるようにするには、Azure Active Directory ユーザー名を使用して、Azure AD ユーザーを Thoughtworks Mingle アプリケーションにプロビジョニングする必要があります。 Thoughtworks Mingle の場合、プロビジョニングは手動で行います。

**ユーザー プロビジョニングを構成するには、次の手順に従います。**

1. Thoughtworks Mingle 企業サイトに管理者としてログインします。

1. **[Profile]** をクリックします。
   
    ![最初のプロジェクト](./media/thoughtworks-mingle-tutorial/ic785160.png "最初のプロジェクト")

1. **[Admin]** タブをクリックし、**[Users]** をクリックします。
   
    ![ユーザー](./media/thoughtworks-mingle-tutorial/ic785161.png "Users")

1. **[新しいユーザー]** をクリックします。
   
    ![New User](./media/thoughtworks-mingle-tutorial/ic785162.png "New User")

1. **[New User]** ダイアログ ページで、次の手順に従います。
   
    ![[New User] ダイアログ](./media/thoughtworks-mingle-tutorial/ic785163.png "New User")  
 
    a. プロビジョニングする有効な Azure AD アカウントの**サインイン名**、**表示名**、**パスワードの選択**、**パスワードの確認**を該当するボックスに入力します。 

    b. **[User type]** として、**[Full user]** を選択します。

    c. **[Create This Profile]** をクリックします。

>[!NOTE]
>Thoughtworks Mingle から提供されている他の Thoughtworks Mingle ユーザー アカウント作成ツールまたは API を使用して、AAD ユーザー アカウントをプロビジョニングできます。
> 

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Thoughtworks Mingle へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**Britta Simon を Thoughtworks Mingle に割り当てるには、次の手順を実行します。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で、**[Thoughtworks Mingle]** を選択します。

    ![アプリケーションの一覧の Thoughtworks Mingle リンク](./media/thoughtworks-mingle-tutorial/tutorial_thoughtworksmingle_app.png) 

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202] 

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションの目的は、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストすることです。

アクセス パネルで [Thoughtworks Mingle] タイルをクリックすると、自動的に Thoughtworks Mingle アプリケーションにサインオンします。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/thoughtworks-mingle-tutorial/tutorial_general_01.png
[2]: ./media/thoughtworks-mingle-tutorial/tutorial_general_02.png
[3]: ./media/thoughtworks-mingle-tutorial/tutorial_general_03.png
[4]: ./media/thoughtworks-mingle-tutorial/tutorial_general_04.png

[100]: ./media/thoughtworks-mingle-tutorial/tutorial_general_100.png

[200]: ./media/thoughtworks-mingle-tutorial/tutorial_general_200.png
[201]: ./media/thoughtworks-mingle-tutorial/tutorial_general_201.png
[202]: ./media/thoughtworks-mingle-tutorial/tutorial_general_202.png
[203]: ./media/thoughtworks-mingle-tutorial/tutorial_general_203.png

