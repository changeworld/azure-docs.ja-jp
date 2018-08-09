---
title: 'チュートリアル: Azure Active Directory と Clear Review の統合 | Microsoft Docs'
description: Azure Active Directory と Clear Review の間のシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 8264159a-11a2-4a8c-8285-4efea0adac8c
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/12/2018
ms.author: jeedes
ms.openlocfilehash: 604a557a91176c08a361ffd058adda63f53b30fc
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39433022"
---
# <a name="tutorial-azure-active-directory-integration-with-clear-review"></a>チュートリアル: Azure Active Directory と Clear Review の統合

このチュートリアルでは、Clear Review と Azure Active Directory (Azure AD) を統合する方法について説明します。

Clear Review と Azure AD の統合には、次の利点があります。

- Clear Review にアクセスするユーザーを Azure AD 上でコントロールできます。
- ユーザーが自分の Azure AD アカウントで自動的に Clear Review にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Azure AD と Clear Review の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Clear Review でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Clear Review の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-clear-review-from-the-gallery"></a>ギャラリーからの Clear Review の追加
Azure AD への Clear Review の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Clear Review を追加する必要があります。

**ギャラリーから Clear Review を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

1. 検索ボックスに「**Clear Review**」と入力し、結果パネルで **[Clear Review]** を選び、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果リストの Clear Review](./media/clearreview-tutorial/tutorial_clearreview_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Clear Review で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Clear Review ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Clear Review の関連ユーザーの間で、リンク関係が確立されている必要があります。

Clear Review で、Azure AD の **[ユーザー名]** の値を **[Username]** の値として割り当ててリンク関係を確立します。

Clear Review で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[Clear Review のテスト ユーザーの作成](#create-a-clear-review-test-user)** - Clear Review で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、Clear Review アプリケーションでシングル サインオンを構成します。

**Clear Review で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Clear Review** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/clearreview-tutorial/tutorial_clearreview_samlbase.png)

1. アプリケーションを **IdP 開始**モードで構成する場合は、**[Clear Review のドメインと URL]** セクションで次の手順を実行します。

    ![[Clear Review のドメインと URL] のシングル サインオン情報](./media/clearreview-tutorial/tutorial_clearreview_url.png)

    a. **[識別子]** ボックスに、`https://<customer name>.clearreview.com/sso/metadata/` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://<customer name>.clearreview.com/sso/acs/` のパターンを使用して URL を入力します。

1. アプリケーションを **SP** 開始モードで構成する場合は、**[詳細な URL 設定の表示]** チェックボックスをオンにして次の手順を実行します。

    ![[Clear Review のドメインと URL] のシングル サインオン情報](./media/clearreview-tutorial/tutorial_clearreview_url_sp.png)

    **[サインオン URL]** ボックスに、`https://<customer name>.clearreview.com` のパターンを使用して URL を入力します。

    > [!NOTE] 
    > これらは実際の値ではありません。 実際のサインオン URL、識別子、および応答 URL で値を更新します。 これらの値を取得するには、[Clear Review サポート チーム](https://clearreview.com/contact/)に連絡してください。

1. Clear Review アプリケーションでは、名前識別子の要求で一意のユーザー識別子の値が必要です。 ユーザー識別子の値を **user.mail** にマップする必要があります。

    ![属性セクション](./media/clearreview-tutorial/attribute.png)


1. **[SAML 署名証明書]** セクションで、**[Certificate (Base64) (証明書 (Base64)) ]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/clearreview-tutorial/tutorial_clearreview_certificate.png)

1. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/clearreview-tutorial/tutorial_general_400.png)

1. **[Clear Review 構成]** セクションで、**[Clear Review の構成]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから、**サインアウト URL、SAML エンティティ ID、SAML シングル サインオン サービス URL** をコピーします。

    ![Clear Review 構成](./media/clearreview-tutorial/tutorial_clearreview_configure.png) 

1. **Clear Review** 側でシングル サインオンを構成するために、**Clear Review** のポータルを管理者の資格情報で開きます。

1. 左側のナビゲーションから **[Admin]\(管理者\)** を選択します。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/clearreview-tutorial/tutorial_clearreview_app_admin1.png)

1. ページの下部にある **[Change]\(変更\)** を選択します。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/clearreview-tutorial/tutorial_clearreview_app_admin2.png)

1. **[Single Sign-On Settings]\(シングル サインオンの設定\)** ページで、次の手順を実行します。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/clearreview-tutorial/tutorial_clearreview_app_admin3.png)

    a. **[Issuer URL]\(発行者 URL\)** ボックスに、Azure Portal からコピーした **SAML エンティティ ID** の値を貼り付けます。

    b. **[SAML Endpoint]\(SAML エンドポイント\)** ボックスに、Azure Portal からコピーした **SAML シングル サインオン サービス URL** の値を貼り付けます。    

    c. **[SLO Endpoint]\(SLO エンドポイント\)** ボックスに、Azure Portal からコピーした**サインオン サービス URL** の値を貼り付けます。 

    d. ダウンロードした証明書をメモ帳で開き、その内容を **[X.509 Certificate]\(X.509 証明書\)** ボックスに貼り付けます。   

1. **[Save]** をクリックします。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関するページを参照してください。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/clearreview-tutorial/create_aaduser_01.png)

1. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/clearreview-tutorial/create_aaduser_02.png)

1. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/clearreview-tutorial/create_aaduser_03.png)

1. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/clearreview-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。
  
### <a name="create-a-clear-review-test-user"></a>Clear Review のテスト ユーザーの作成

このセクションでは、Clear Review で Britta Simon というユーザーを作成します。 Clear Review [サポート チーム](https://clearreview.com/contact/)と連携し、Clear Review プラットフォームにユーザーを追加してください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Clear Review へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**Britta Simon を Clear Review に割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[Clear Review]** を選択します。

    ![アプリケーションの一覧の Clear Review のリンク](./media/clearreview-tutorial/tutorial_clearreview_app.png)  

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Clear Review] タイルをクリックすると、自動的に Clear Review アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/clearreview-tutorial/tutorial_general_01.png
[2]: ./media/clearreview-tutorial/tutorial_general_02.png
[3]: ./media/clearreview-tutorial/tutorial_general_03.png
[4]: ./media/clearreview-tutorial/tutorial_general_04.png

[100]: ./media/clearreview-tutorial/tutorial_general_100.png

[200]: ./media/clearreview-tutorial/tutorial_general_200.png
[201]: ./media/clearreview-tutorial/tutorial_general_201.png
[202]: ./media/clearreview-tutorial/tutorial_general_202.png
[203]: ./media/clearreview-tutorial/tutorial_general_203.png
