---
title: 'チュートリアル: Azure Active Directory と SAML 1.1 Token enabled LOB App の統合 | Microsoft Docs'
description: Azure Active Directory と SAML 1.1 Token enabled LOB App の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: ced1d88d-0e48-40d5-9aea-ef991cd9d270
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2018
ms.author: jeedes
ms.openlocfilehash: edabc09f820093d088ec0b8ed1222fb26c800bee
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39426430"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-11-token-enabled-lob-app"></a>チュートリアル: Azure Active Directory と SAML 1.1 Token enabled LOB App の統合

このチュートリアルでは、SAML 1.1 Token enabled LOB App と Azure Active Directory (Azure AD) を統合する方法について説明します。

SAML 1.1 Token enabled LOB App と Azure AD の統合には、次の利点があります。

- SAML 1.1 Token enabled LOB App にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで SAML 1.1 Token enabled LOB App に自動的にサインオン (シングル サインオン) できるように設定できます。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Azure AD と SAML 1.1 Token enabled LOB App の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- SAML 1.1 Token enabled LOB App でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの SAML 1.1 Token enabled LOB App の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-saml-11-token-enabled-lob-app-from-the-gallery"></a>ギャラリーからの SAML 1.1 Token enabled LOB App の追加
Azure AD への SAML 1.1 Token enabled LOB App の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に SAML 1.1 Token enabled LOB App を追加する必要があります。

**ギャラリーから SAML 1.1 Token enabled LOB App を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

1. [検索] ボックスに「**SAML 1.1 Token enabled LOB App**」と入力し、結果パネルで **SAML 1.1 Token enabled LOB App** を選び、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果リストの SAML 1.1 Token enabled LOB App](./media/saml-tutorial/tutorial_saml_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、SAML 1.1 Token enabled LOB App で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する SAML 1.1 Token enabled LOB App ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと SAML 1.1 Token enabled LOB App の関連ユーザーの間で、リンク関係が確立されている必要があります。

SAML 1.1 Token enabled LOB App で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[SAML 1.1 Token enabled LOB App テスト ユーザーの作成](#create-a-saml-11-token-enabled-lob-app-test-user)** - SAML 1.1 Token enabled LOB App で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal で Azure AD のシングル サインオンを有効にして、SAML 1.1 Token enabled LOB App アプリケーションでシングル サインオンを構成します。

**SAML 1.1 Token enabled LOB App で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure portal の **SAML 1.1 Token enabled LOB App** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/saml-tutorial/tutorial_saml_samlbase.png)

1. **[SAML 1.1 Token enabled LOB App のドメインと URL]** セクションで、次の手順に従います。

    ![[SAML 1.1 Token enabled LOB App のドメインと URL] のシングル サインオン情報](./media/saml-tutorial/tutorial_saml_url.png)

    a. **[サインオン URL]** ボックスに、`https://your-app-url` のパターンを使用して URL を入力します。

    b. **[識別子 (エンティティ ID)]** ボックスに、次のパターンで URL を入力します。`https://your-app-url`
     
    > [!NOTE] 
    > これらは実際の値ではありません。 これらの値をアプリケーション固有の URL に置き換えてください。  

1. **[SAML 署名証明書]** セクションで、**[Certificate (Base64) (証明書 (Base64)) ]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/saml-tutorial/tutorial_saml_certificate.png) 

1. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/saml-tutorial/tutorial_general_400.png)
    
1. **[SAML 1.1 Token enabled LOB App 構成]** セクションで、**[SAML 1.1 Token enabled LOB App の構成]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから、**サインアウト URL、SAML エンティティ ID、SAML シングル サインオン サービス URL** をコピーします。

    ![SAML 1.1 Token enabled LOB App 構成](./media/saml-tutorial/tutorial_saml_configure.png) 

1. **SAML 1.1 Token enabled LOB App** 側にシングル サインオンを構成するには、ダウンロードされた**証明書 (Base64)、サインアウト URL、SAML エンティティ ID、SAML シングル サインオン サービス URL** をアプリケーション サポート チームに送信する必要があります。 サポート チームはこれを設定して、SAML SSO 接続が両方の側で正しく設定されるようにします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/saml-tutorial/create_aaduser_01.png)

1. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/saml-tutorial/create_aaduser_02.png)

1. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/saml-tutorial/create_aaduser_03.png)

1. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/saml-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。
 
### <a name="create-a-saml-11-token-enabled-lob-app-test-user"></a>SAML 1.1 Token enabled LOB App テスト ユーザーの作成

このセクションでは、SAML 1.1 Token enabled LOB App で Britta Simon というユーザーを作成します。 アプリケーション サポート チームと協力してアプリケーション側にユーザーを作成します。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に SAML 1.1 Token enabled LOB App へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**SAML 1.1 Token enabled LOB App に Britta Simon を割り当てるには、次の手順を実行します。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[SAML 1.1 Token enabled LOB App]** を選択します。

    ![アプリケーションの一覧の [SAML 1.1 Token enabled LOB App] リンク](./media/saml-tutorial/tutorial_saml_app.png)  

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [SAML 1.1 Token enabled LOB App] タイルをクリックすると、自動的に SAML 1.1 Token enabled LOB App アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/saml-tutorial/tutorial_general_01.png
[2]: ./media/saml-tutorial/tutorial_general_02.png
[3]: ./media/saml-tutorial/tutorial_general_03.png
[4]: ./media/saml-tutorial/tutorial_general_04.png

[100]: ./media/saml-tutorial/tutorial_general_100.png

[200]: ./media/saml-tutorial/tutorial_general_200.png
[201]: ./media/saml-tutorial/tutorial_general_201.png
[202]: ./media/saml-tutorial/tutorial_general_202.png
[203]: ./media/saml-tutorial/tutorial_general_203.png
