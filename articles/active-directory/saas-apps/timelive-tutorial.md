---
title: 'チュートリアル: Azure Active Directory と TimeLive の統合 | Microsoft Docs'
description: Azure Active Directory と TimeLive の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 34123629-4ad5-465c-a4c1-8299f857e720
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2017
ms.author: jeedes
ms.openlocfilehash: 26e70abc336b7cb13342873784aa2cb4048a47d9
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39437323"
---
# <a name="tutorial-azure-active-directory-integration-with-timelive"></a>チュートリアル: Azure Active Directory と TimeLive の統合

このチュートリアルでは、TimeLive と Azure Active Directory (Azure AD) を統合する方法について説明します。

TimeLive と Azure AD の統合には、次の利点があります。

- TimeLive にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に TimeLive にサインオン (シングル サインオン) できるように、設定が可能です。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

TimeLive と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- TimeLive でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの TimeLive の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-timelive-from-the-gallery"></a>ギャラリーからの TimeLive の追加
Azure AD への TimeLive の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に TimeLive を追加する必要があります。

**ギャラリーから TimeLive を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

1. 検索ボックスに「**TimeLive**」と入力し、結果パネルで **TimeLive** を選び、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果一覧の TimeLive](./media/timelive-tutorial/tutorial_timelive_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、TimeLive で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する TimeLive ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと TimeLive の関連ユーザーの間で、リンク関係が確立されている必要があります。

TimeLive で、Azure AD の **[ユーザー名]** の値を **[Username]** の値として割り当ててリンク関係を確立します。

TimeLive で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[TimeLive テスト ユーザーの作成](#create-a-timelive-test-user)** - Azure AD の Britta Simon にリンクさせるために、対応するユーザーを TimeLive で作成します。
1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、TimeLive アプリケーションでシングル サインオンを構成します。

**TimeLive で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **TimeLive** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/timelive-tutorial/tutorial_timelive_samlbase.png)

1. **[TimeLive のドメインと URL]** セクションで、次の手順を実行します。

    ![[TimeLive のドメインと URL] のシングル サインオン情報](./media/timelive-tutorial/tutorial_timelive_url.png)

    a. **[サインオン URL]** ボックスに、`https://domainname.livetecs.com/` のパターンを使用して URL を入力します。

    b. **[識別子]** ボックスに、`https://domainname.livetecs.com/` の形式で URL を入力します。

    > [!NOTE] 
    > これらは実際の値ではありません。 実際の識別子とサインオン URL でこれらの値を更新してください。 これらの値を取得するには、[TimeLive クライアント サポート チーム](mailto:support@livetecs.com)に連絡してください。 

1. **[SAML 署名証明書]** セクションで、**[証明書 (Base64)]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/timelive-tutorial/tutorial_timelive_certificate.png) 

1. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/timelive-tutorial/tutorial_general_400.png)
    
1. **[TimeLive 構成]** セクションで、**[TimeLive の構成]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから**サインアウト URL と SAML シングル サインオン サービス URL** をコピーします。

    ![TimeLive 構成](./media/timelive-tutorial/tutorial_timelive_configure.png)

1. 別の Web ブラウザーのウィンドウで、TimeLive 企業サイトに管理者としてログインします。

1. **[Admin Options]\(管理オプション\)** の **[Preferences]\(基本設定\)** を選びます。

    ![TimeLive 構成](./media/timelive-tutorial/configure1.png)

1. **[Application Preference]\(アプリケーション基本設定\)** セクションで、次の手順に従います。
    
    ![TimeLive 構成](./media/timelive-tutorial/configure2.png)

    a. **[セキュリティ]** タブを選びます。

    b. **[Enable Single Sign On (SSO)]\(シングル サインオン (SSO) を有効にする\)** チェック ボックスをオンにします。

    c. **[Sign in using Single Sign-On (SSO) with]\(サインインにシングル サインオン (SSO) を使用するもの\)** という見出しのドロップダウン メニューで **[SAML]** を選びます。

    d. **[SAML SSO URL]** に、Azure Portal からコピーした **SAML シングル サインオン サービス URL** の値を貼り付けます。

    e. **[Remote logout URL]\(リモート ログアウト URL\)** に、Azure Portal からコピーした**サインアウト URL** の値を貼り付けます。

    f. Azure Portal からダウンロードした **Base-64 でエンコードされた証明書**をメモ帳で開き、その内容をコピーして **[X.509 証明書]** ボックスに貼り付けます。

    g. **[Update]** をクリックします。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関するページを参照してください。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/timelive-tutorial/create_aaduser_01.png)

1. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/timelive-tutorial/create_aaduser_02.png)

1. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/timelive-tutorial/create_aaduser_03.png)

1. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/timelive-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。
 
### <a name="create-a-timelive-test-user"></a>TimeLive テスト ユーザーを作成する

このセクションの目的は、TimeLive で Britta Simon というユーザーを作成することです。 TimeLive では、Just-In-Time プロビジョニングがサポートされています。この設定は、既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 存在しない TimeLive ユーザーにアクセスしようとすると、新しいユーザーが自動的に作成されます。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に TimeLive へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**TimeLive に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **TimeLive** を選択します。

    ![アプリケーションの一覧の TimeLive のリンク](./media/timelive-tutorial/tutorial_timelive_app.png)  

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで TimeLive のタイルをクリックすると、TimeLive アプリケーションに自動的にサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/timelive-tutorial/tutorial_general_01.png
[2]: ./media/timelive-tutorial/tutorial_general_02.png
[3]: ./media/timelive-tutorial/tutorial_general_03.png
[4]: ./media/timelive-tutorial/tutorial_general_04.png

[100]: ./media/timelive-tutorial/tutorial_general_100.png

[200]: ./media/timelive-tutorial/tutorial_general_200.png
[201]: ./media/timelive-tutorial/tutorial_general_201.png
[202]: ./media/timelive-tutorial/tutorial_general_202.png
[203]: ./media/timelive-tutorial/tutorial_general_203.png

