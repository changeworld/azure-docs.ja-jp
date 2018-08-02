---
title: 'チュートリアル: Azure Active Directory と Skills Base の統合 | Microsoft Docs'
description: Azure Active Directory と Skills Base の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 237d90c4-8243-4f80-a305-b5ad9204159e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2018
ms.author: jeedes
ms.openlocfilehash: e11ba8ca9c4ad17b2ade909bb474ad2d1fcf4410
ms.sourcegitcommit: 30221e77dd199ffe0f2e86f6e762df5a32cdbe5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2018
ms.locfileid: "39205376"
---
# <a name="tutorial-azure-active-directory-integration-with-skills-base"></a>チュートリアル: Azure Active Directory と Skills Base の統合

このチュートリアルでは、Skills Base と Azure Active Directory (Azure AD) を統合する方法について説明します。

Skills Base と Azure AD の統合には、次の利点があります。

- Skills Base にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Skills Base にサインオン (シングル サインオン) できるように、設定が可能です。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Skills Base と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Skills Base でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Skills Base の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-skills-base-from-the-gallery"></a>ギャラリーからの Skills Base の追加
Azure AD への Skills Base の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Skills Base を追加する必要があります。

**ギャラリーから Skills Base を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

4. 検索ボックスに「**Skills Base**」と入力し、結果ウィンドウで **[Skills Base]** を選び、**[追加]** ボタンをクリックして、アプリケーションを追加します。

    ![結果一覧の Skills Base](./media/skillsbase-tutorial/tutorial_skillsbase_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Skills Base で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Skills Base ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Skills Base の関連ユーザーの間で、リンク関係が確立されている必要があります。

Skills Base で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Skills Base のテスト ユーザーの作成](#create-a-skills-base-test-user)** - Skills Base で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal で Azure AD のシングル サインオンを有効にして、Skills Base アプリケーションでシングル サインオンを構成します。

**Skills Base で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure portal の **Skills Base** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/skillsbase-tutorial/tutorial_skillsbase_samlbase.png)

3. **[Skills Base のドメインと URL]** セクションで、次の手順に従います。

    ![[Skills Base のドメインと URL] のシングル サインオン情報](./media/skillsbase-tutorial/tutorial_skillsbase_url.png)

    **[サインオン URL]** ボックスに、`https://app.skills-base.com/o/<customer-unique-key>` のパターンを使用して URL を入力します。

    > [!NOTE] 
    > Skills Base アプリケーションからサインオン URL を取得できます。 管理者としてログインし、[管理] > [設定] > [インスタンスの詳細] > [ショートカット] リンクに移動してください。 サインオン URL をコピーし、上のテキスト ボックスに貼り付けます。

4. **[SAML 署名証明書]** セクションで、**[Metadata XML (メタデータ XML)]** をクリックし、コンピューターにメタデータ ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/skillsbase-tutorial/tutorial_skillsbase_certificate.png) 

5. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/skillsbase-tutorial/tutorial_general_400.png)

6. 別の Web ブラウザー ウィンドウで、セキュリティ管理者として Skills Base にログインします。

7. メニュー左側の **[ADMIN]\(管理\)** で、**[Authentication]\(認証\)** をクリックします。

    ![管理者](./media/skillsbase-tutorial/tutorial_skillsbase_auth.png)

8. **[Authentication]\(認証\)** ページの [Single Sign-On]\(シングル サインオン\) で、**[SAML 2]** を選択します。

    ![シングル](./media/skillsbase-tutorial/tutorial_skillsbase_single.png)

9. **[Authentication]\(認証\)** ページで、次の手順を実行します。

    ![シングル](./media/skillsbase-tutorial/tutorial_skillsbase_save.png)

    a. **[Status]\(状態\)** オプションの横にある **[Update IdP metadata]\(IdP メタデータの更新\)** をクリックし、Azure Portal からダウンロードしたメタデータ XML のコンテンツを、指定のテキストボックスに貼り付けます。

    > [!Note]
    > Idp メタデータは、上のスクリーンショットに示すように、**メタデータ検証**ツールを使用して検証することもできます。

    b. **[Save]** をクリックします。
    
### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/skillsbase-tutorial/create_aaduser_01.png)

2. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/skillsbase-tutorial/create_aaduser_02.png)

3. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/skillsbase-tutorial/create_aaduser_03.png)

4. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/skillsbase-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。
 
### <a name="create-a-skills-base-test-user"></a>Skills Base のテスト ユーザーを作成する

このセクションの目的は、Skills Base で Britta Simon というユーザーを作成することです。 Skills Base では、Just-In-Time プロビジョニングがサポートされています。この設定は、既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 存在しない Skills Base ユーザーにアクセスしようとすると、新しいユーザーが自動的に作成されます。

>[!Note]
>ユーザーを手動で作成する必要がある場合は、[こちら](http://wiki.skills-base.net/index.php?title=Adding_people_and_enabling_them_to_log_in)の指示に従ってください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Skills Base へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**Skills Base に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[Skills Base]** を選択します。

    ![アプリケーションの一覧の [Skills Base] リンク](./media/skillsbase-tutorial/tutorial_skillsbase_app.png)  

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Skills Base] タイルをクリックすると、自動的に Skills Base アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/skillsbase-tutorial/tutorial_general_01.png
[2]: ./media/skillsbase-tutorial/tutorial_general_02.png
[3]: ./media/skillsbase-tutorial/tutorial_general_03.png
[4]: ./media/skillsbase-tutorial/tutorial_general_04.png

[100]: ./media/skillsbase-tutorial/tutorial_general_100.png

[200]: ./media/skillsbase-tutorial/tutorial_general_200.png
[201]: ./media/skillsbase-tutorial/tutorial_general_201.png
[202]: ./media/skillsbase-tutorial/tutorial_general_202.png
[203]: ./media/skillsbase-tutorial/tutorial_general_203.png

