---
title: 'チュートリアル: Azure Active Directory と QPrism の統合 | Microsoft Docs'
description: Azure Active Directory と QPrism の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 72ab75ba-132b-4f83-a34b-d28b81b6d7bc
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/23/2018
ms.author: jeedes
ms.openlocfilehash: ddf22491d7531daecf4448e62e8594c3326d7b77
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39420276"
---
# <a name="tutorial-azure-active-directory-integration-with-qprism"></a>チュートリアル: Azure Active Directory と QPrism の統合

このチュートリアルでは、QPrism と Azure Active Directory (Azure AD) を統合する方法について説明します。

QPrism と Azure AD の統合には、次の利点があります。

- QPrism にアクセスするユーザーを Azure AD で制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に QPrism にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure ポータル) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

QPrism と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- QPrism でのシングル サインオンが有効なサブスクリプション

このチュートリアルの手順をテストするには、次の推奨事項に従います。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの QPrism の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="add-qprism-from-the-gallery"></a>ギャラリーから QPrism を追加する
Azure AD への QPrism の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に QPrism を追加する必要があります。

**ギャラリーから QPrism を追加するには**

1. [Azure Portal](https://portal.azure.com) の左側のウィンドウで、**[Azure Active Directory]** を選択します。 

    ![Azure Active Directory のボタン][1]

1. **[エンタープライズ アプリケーション]** > **[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
1. 新しいアプリケーションを追加するには、ダイアログ ボックスの上部の **[新しいアプリケーション]** を選択します。

    ![[新しいアプリケーション] ボタン][3]

1. 検索ボックスに「**QPrism**」と入力し、結果パネルから **[QPrism]** を選択します。 **[追加]** をクリックして、アプリケーションを追加します。

    ![結果一覧の QPrism](./media/qprism-tutorial/tutorial_qprism_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、QPrism で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する QPrism ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと QPrism の関連ユーザーの間にリンク関係が存在する必要があります。

この関係を確立するには、QPrism で、Azure AD の **[ユーザー名]** の値を **[Username]\(ユーザー名\)** の値として割り当てます。

Clarizen で Azure AD のシングル サインオンを構成してテストするには、次の手順を完了します。

1. [Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)- ユーザーがこの機能を使用できるようにします。
1. [Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)- Britta Simon で Azure AD のシングル サインオンをテストします。
1. [QPrism テスト ユーザーの作成](#create-a-qprism-test-user)- QPrism で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. [Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)- Britta Simon が Azure AD シングル サインオンを使用できるようにします。
1. [シングル サインオンのテスト](#test-single-sign-on) - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にし、QPrism アプリケーションでシングル サインオンを構成します。

1. Azure Portal の **QPrism** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

1. **[シングル サインオン]** ダイアログ ボックスで、**[モード]** として **[SAML ベースのサインオン]** を選択して、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/qprism-tutorial/tutorial_qprism_samlbase.png)

1. **[QPrism のドメインと URL]** セクションで、次の手順を実行します。

    ![[QPrism のドメインと URL] のシングル サインオン情報](./media/qprism-tutorial/tutorial_qprism_url.png)

    a. **[サインオン URL]** ボックスに、`https://<customer domain>.qmyzone.com/login` という形式で URL を入力します。

    b. **[識別子]** ボックスに、`https://<customer domain>.qmyzone.com/metadata.php` という形式で URL を入力します。
         
    > [!NOTE] 
    > これらは実際の値ではありません。 これらの値を実際の識別子とサインオン URL で更新してください。 これらの値を取得するには、[QPrism クライアント サポート チーム](mailto:qsupport-ce@quatrro.com)にお問い合わせください。 

1. **[SAML 署名証明書]** セクションで、コピー ボタンをクリックして **[App Federation Metadata Url]\(アプリケーション フェデレーション メタデータ URL\)** をコピーし、メモ帳に貼り付けます。

     ![証明書のダウンロードのリンク](./media/qprism-tutorial/tutorial_qprism_certificate.png)

1. **[保存]** を選択します。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/qprism-tutorial/tutorial_general_400.png)
    
1. **QPrism** 側でシングル サインオンを構成するには、**アプリのフェデレーション メタデータ URL** を [QPrism サポート チーム](mailto:qsupport-ce@quatrro.com)に送信する必要があります。 サポート チームはこれを設定して、SAML SSO 接続が両方の側で正しく設定されるようにします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD にテスト ユーザーを作成するには**

1. Azure Portal の左側のウィンドウで、**[Azure Active Directory]** を選択します。

    ![Azure Active Directory のボタン](./media/qprism-tutorial/create_aaduser_01.png)

1. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/qprism-tutorial/create_aaduser_02.png)

1. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** を選択します。

    ![[追加] ボタン](./media/qprism-tutorial/create_aaduser_03.png)

1. **[ユーザー]** ダイアログ ボックスで、次の手順を実行します。

    ![[ユーザー] ダイアログ ボックス](./media/qprism-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **作成**を選択します。
 
### <a name="create-a-qprism-test-user"></a>QPrism テスト ユーザーの作成

このセクションでは、QPrism で Britta Simon というユーザーを作成します。 [QPrism サポート チーム](mailto:qsupport-ce@quatrro.com)と連携して、QPrism プラットフォームにユーザーを追加してください。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。 

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に QPrism へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**Britta Simon を QPrism に割り当てるには**

1. Azure ポータルで、アプリケーション ビューを開き、ディレクトリ ビューに移動します。 **[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** を選択します。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[QPrism]** を選択します。

    ![アプリケーションの一覧の QPrism のリンク](./media/qprism-tutorial/tutorial_qprism_app.png)  

1. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク][202]

1. **[追加]** を選択します。 **[割り当ての追加]** で **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

1. **[ユーザーとグループ]** ダイアログ ボックスで、**[ユーザー]** の一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログ ボックスで、**[選択]** を選択します。

1. **[割り当ての追加]** で **[割り当て]** を選択します。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [QPrism] タイルをクリックすると、QPrism アプリケーションに自動的にサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/qprism-tutorial/tutorial_general_01.png
[2]: ./media/qprism-tutorial/tutorial_general_02.png
[3]: ./media/qprism-tutorial/tutorial_general_03.png
[4]: ./media/qprism-tutorial/tutorial_general_04.png

[100]: ./media/qprism-tutorial/tutorial_general_100.png

[200]: ./media/qprism-tutorial/tutorial_general_200.png
[201]: ./media/qprism-tutorial/tutorial_general_201.png
[202]: ./media/qprism-tutorial/tutorial_general_202.png
[203]: ./media/qprism-tutorial/tutorial_general_203.png

