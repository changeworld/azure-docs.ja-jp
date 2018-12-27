---
title: 'チュートリアル: Azure Active Directory と Compliance ELF の統合 | Microsoft Docs'
description: Azure Active Directory と Compliance ELF の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 69c6efc3-54c7-49ec-b827-33177c09aa13
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2018
ms.author: jeedes
ms.openlocfilehash: e5a7bfc51bcd1931def202d701127de701afb595
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/14/2018
ms.locfileid: "39042862"
---
# <a name="tutorial-azure-active-directory-integration-with-compliance-elf"></a>チュートリアル: Azure Active Directory と Compliance ELF の統合

このチュートリアルでは、Compliance ELF と Azure Active Directory (Azure AD) を統合する方法について説明します。

Compliance ELF と Azure AD の統合には、次の利点があります。

- Compliance ELF にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが各自の Azure AD アカウントで Compliance ELF に自動的にサインオン (シングル サインオン) するように、設定が可能です。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Compliance ELF と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Compliance ELF でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Compliance ELF の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-compliance-elf-from-the-gallery"></a>ギャラリーからの Compliance ELF の追加
Azure AD への Compliance ELF の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Compliance ELF を追加する必要があります。

**ギャラリーから Compliance ELF を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

4. 検索ボックスに「**Compliance ELF**」と入力し、結果ウィンドウで **Compliance ELF** を選び、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果一覧の Compliance ELF](./media/complianceelf-tutorial/tutorial_complianceelf_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Compliance ELF で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Compliance ELF ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Compliance ELF の関連ユーザーの間で、リンク関係が確立されている必要があります。

Compliance ELF で、Azure AD の **[ユーザー名]** の値を **[Username]\(ユーザー名\)** の値として割り当ててリンク関係を確立します。

Compliance ELF で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Compliance ELF のテスト ユーザーの作成](#create-a-compliance-elf-test-user)** - Compliance ELF で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure ポータルで Azure AD のシングル サインオンを有効にして、Compliance ELF アプリケーションでシングル サインオンを構成します。

**Compliance ELF で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure ポータルの **Compliance ELF** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。

    ![[シングル サインオン] ダイアログ ボックス](./media/complianceelf-tutorial/tutorial_complianceelf_samlbase.png)

3. **[Compliance ELF のドメインと URL]** セクションで、**IDP** 開始モードでアプリケーションを構成する場合は、次の手順に従います。

    ![[Compliance ELF のドメインと URL] のシングル サインオン情報](./media/complianceelf-tutorial/tutorial_complianceelf_url.png)

    **[識別子]** ボックスに、`https://sso.cordium.com` のように URL を入力します。

4. アプリケーションを **SP** 開始モードで構成する場合は、**[詳細な URL 設定の表示]** チェックボックスをオンにして次の手順を実行します。

    ![[Compliance ELF のドメインと URL] のシングル サインオン](./media/complianceelf-tutorial/tutorial_complianceelf_url1.png)

    **[サインオン URL]** ボックスに、`https://<subdomain>.complianceelf.com` のパターンを使用して URL を入力します。
    
    > [!NOTE] 
    > これは実際の値ではありません。 この値は実際のサインオン URL に変更します。 この値を取得するには、[Compliance ELF サポート チーム](mailto:support@complianceelf.com)に問い合わせてください。

5. **[SAML 署名証明書]** セクションで、コピー ボタンをクリックして **[App Federation Metadata Url]\(アプリケーション フェデレーション メタデータ URL\)** をコピーし、メモ帳に貼り付けます。
    
    ![Configure single sign-on](./media/complianceelf-tutorial/tutorial_metadataurl.png)
     
6. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/complianceelf-tutorial/tutorial_general_400.png)

7. **Compliance ELF** 側でシングル サインオンを構成するには、**[App Federation Metadata Url]\(アプリケーション フェデレーション メタデータ URL\)** を [Compliance ELF サポート チーム](mailto:support@complianceelf.com)に送信する必要があります。 サポート チームはこれを設定して、SAML SSO 接続が両方の側で正しく設定されるようにします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/complianceelf-tutorial/create_aaduser_01.png)

2. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/complianceelf-tutorial/create_aaduser_02.png)

3. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/complianceelf-tutorial/create_aaduser_03.png)

4. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/complianceelf-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。
  
### <a name="create-a-compliance-elf-test-user"></a>Compliance ELF テスト ユーザーを作成する

Compliance ELF で Britta Simon というユーザーを作成します。 [Compliance ELF サポート チーム](mailto:support@complianceelf.com)と協力して、Compliance ELF プラットフォームにユーザーを追加します。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Compliance ELF へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**Compliance ELF に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[Compliance ELF]** を選択します。

    ![アプリケーションの一覧の [Compliance ELF] リンク](./media/complianceelf-tutorial/tutorial_complianceelf_app.png)  

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Compliance ELF] タイルをクリックすると、自動的に Compliance ELF アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/complianceelf-tutorial/tutorial_general_01.png
[2]: ./media/complianceelf-tutorial/tutorial_general_02.png
[3]: ./media/complianceelf-tutorial/tutorial_general_03.png
[4]: ./media/complianceelf-tutorial/tutorial_general_04.png

[100]: ./media/complianceelf-tutorial/tutorial_general_100.png

[200]: ./media/complianceelf-tutorial/tutorial_general_200.png
[201]: ./media/complianceelf-tutorial/tutorial_general_201.png
[202]: ./media/complianceelf-tutorial/tutorial_general_202.png
[203]: ./media/complianceelf-tutorial/tutorial_general_203.png

