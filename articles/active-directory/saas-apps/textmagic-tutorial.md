---
title: 'チュートリアル: Azure Active Directory と TextMagic の統合 | Microsoft Docs'
description: Azure Active Directory と TextMagic の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 3e5b49d2-7096-46bc-a9ce-90e09177ba28
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2017
ms.author: jeedes
ms.openlocfilehash: b8ffd732221604d55c65d4623de89f716bba49eb
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39427460"
---
# <a name="tutorial-azure-active-directory-integration-with-textmagic"></a>チュートリアル: Azure Active Directory と TextMagic の統合

このチュートリアルでは、TextMagic と Azure Active Directory (Azure AD) を統合する方法について説明します。

TextMagic と Azure AD の統合には、次の利点があります。

- TextMagic にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に TextMagic にサインオン (シングル サインオン) できるように、設定が可能です。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

TextMagic と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- TextMagic でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーから TextMagic を追加する
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-textmagic-from-the-gallery"></a>ギャラリーから TextMagic を追加する
Azure AD への TextMagic の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に TextMagic を追加する必要があります。

**ギャラリーから TextMagic を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

1. 検索ボックスに「**TextMagic**」と入力し、結果ウィンドウで **TextMagic** を選び、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果一覧の TextMagic](./media/textmagic-tutorial/tutorial_textmagic_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、TextMagic で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する TextMagic ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと TextMagic の関連ユーザーの間で、リンク関係が確立されている必要があります。

TextMagic で、Azure AD の **[ユーザー名]** の値を **[Username]\(ユーザー名\)** の値として割り当ててリンク関係を確立します。

TextMagic で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[TextMagic のテスト ユーザーの作成](#create-a-textmagic-test-user)** - Azure AD の Britta Simon にリンクさせるために、対応するユーザーを TextMagic で作成します。
1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、TextMagic アプリケーションでシングル サインオンを構成します。

**TextMagic で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **TextMagic** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/textmagic-tutorial/tutorial_textmagic_samlbase.png)

1. **[TextMagic のドメインと URL]** セクションで、**IDP** 開始モードでアプリケーションを構成する場合は、次の手順に従います。

    ![[TextMagic のドメインと URL] のシングル サインオン情報](./media/textmagic-tutorial/tutorial_textmagic_url.png)

    **[識別子]** ボックスに次の URL を入力します。`https://my.textmagic.com/saml/metadata`

1. アプリケーションを **SP** 開始モードで構成する場合は、**[詳細な URL 設定の表示]** チェックボックスをオンにして次の手順を実行します。

    ![[TextMagic のドメインと URL] のシングル サインオン情報](./media/textmagic-tutorial/url1.png)

    **[サインオン URL]** ボックスに、「`https://my.textmagic.com/login/sso`」と入力します。


1. **[SAML 署名証明書]** セクションで、**[Certificate (Base64) (証明書 (Base64)) ]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/textmagic-tutorial/tutorial_textmagic_certificate.png) 

1. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/textmagic-tutorial/tutorial_general_400.png)
    
1. **[TextMagic の構成]** セクションで、**[TextMagic の構成]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから、**サインアウト URL、SAML エンティティ ID、SAML シングル サインオン サービス URL** をコピーします。

    ![TextMagic の構成](./media/textmagic-tutorial/tutorial_textmagic_configure.png) 

1. 別の Web ブラウザーのウィンドウで、TextMagic 企業サイトに管理者としてログインします。

1. ユーザー名で **[Account settings]\(アカウント設定\)** を選択します。

    ![TextMagic の構成](./media/textmagic-tutorial/config1.png) 
1. **[Single Sign-On (SSO)]\(シングル サインオン (SSO)\)** タブをクリックし、次のフィールドを入力します。  
    
    ![TextMagic の構成](./media/textmagic-tutorial/config2.png)

    a. **[Identity Provider Entity ID]\(ID プロバイダー エンティティ ID\)** ボックスに、Azure Portal からコピーした **SAML エンティティ ID** の値を貼り付けます。

    b. **[Identity provider SSO URL]\(ID プロバイダーの SSO の URL\)** ボックスに、Azure Portal からコピーした**シングル サインオン サービス URL** の値を貼り付けます。

    c. **[Identity provider SLO URL]\(ID プロバイダーの SLO の URL\)** ボックスに、Azure Portal からコピーした**サインアウト URL** の値を貼り付けます。

    d. Azure Portal からダウンロードした **base-64 でエンコードされた証明書**をメモ帳で開き、その内容をクリップボードにコピーしてから、それを **[Public x509 certificate]\(パブリック x509 証明書\)** ボックスに貼り付けます。

    e. **[Save]** をクリックします。


> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関するページを参照してください。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/textmagic-tutorial/create_aaduser_01.png)

1. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/textmagic-tutorial/create_aaduser_02.png)

1. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/textmagic-tutorial/create_aaduser_03.png)

1. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/textmagic-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。
 
### <a name="create-a-textmagic-test-user"></a>TextMagic テスト ユーザーを作成する

アプリケーションでは、ジャストインタイムのユーザー プロビジョニングがサポートされ、認証後にユーザーがアプリケーションに自動的に作成されます。 初めてシステムにログインしてサブアカウントを有効にしたら、情報を入力する必要があります。
このセクションでは、ユーザー側で必要な操作はありません。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に TextMagic へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**TextMagic に Britta Simon を割り当てるには、次の手順を実行します。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **TextMagic** を選択します。

    ![アプリケーションの一覧の TextMagic のリンク](./media/textmagic-tutorial/tutorial_textmagic_app.png)  

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで TextMagic のタイルをクリックすると、TextMagic アプリケーションに自動的にサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/textmagic-tutorial/tutorial_general_01.png
[2]: ./media/textmagic-tutorial/tutorial_general_02.png
[3]: ./media/textmagic-tutorial/tutorial_general_03.png
[4]: ./media/textmagic-tutorial/tutorial_general_04.png

[100]: ./media/textmagic-tutorial/tutorial_general_100.png

[200]: ./media/textmagic-tutorial/tutorial_general_200.png
[201]: ./media/textmagic-tutorial/tutorial_general_201.png
[202]: ./media/textmagic-tutorial/tutorial_general_202.png
[203]: ./media/textmagic-tutorial/tutorial_general_203.png

