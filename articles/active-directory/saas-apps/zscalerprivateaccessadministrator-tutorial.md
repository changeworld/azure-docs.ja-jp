---
title: 'チュートリアル: Azure Active Directory と Zscaler Private Access Administrator の統合 | Microsoft Docs'
description: Azure Active Directory と Zscaler Private Access Administrator の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: c87392a7-e7fe-4cdc-a8e6-afe1ed975172
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2018
ms.author: jeedes
ms.openlocfilehash: 61b469ba5f64a52b87843432dfe60fe1d83ffec2
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39428385"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-private-access-administrator"></a>チュートリアル: Azure Active Directory と Zscaler Private Access Administrator の統合

このチュートリアルでは、Zscaler Private Access Administrator と Azure Active Directory (Azure AD) を統合する方法について説明します。

Zscaler Private Access Administrator と Azure AD の統合には、次の利点があります。

- Zscaler Private Access Administrator にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Zscaler Private Access Administrator にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Zscaler Private Access Administrator と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Zscaler Private Access Administrator でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Zscaler Private Access Administrator の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-zscaler-private-access-administrator-from-the-gallery"></a>ギャラリーからの Zscaler Private Access Administrator の追加
Azure AD への Zscaler Private Access Administrator の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Zscaler Private Access Administrator を追加する必要があります。

**ギャラリーから Zscaler Private Access Administrator を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

1. 検索ボックスに「**Zscaler Private Access Administrator**」と入力し、結果パネルから **[Zscaler Private Access Administrator]** を選択してから **[追加]** ボタンをクリックしてアプリケーションを追加します。

    ![結果一覧の Zscaler Private Access Administrator](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Zscaler Private Access Administrator で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Zscaler Private Access Administrator ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Zscaler Private Access Administrator の関連ユーザーの間で、リンク関係が確立されている必要があります。

Zscaler Private Access Administrator で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[Zscaler Private Access Administrator のテスト ユーザーの作成](#create-a-zscaler-private-access-administrator-test-user)** - Zscaler Private Access Administrator で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にし、Zscaler Private Access Administrator アプリケーションでシングル サインオンを構成します。

**Zscaler Private Access Administrator で Azure AD シングル サインオンを構成するには、次の手順を実行します。**

1. Azure Portal の **Zscaler Private Access Administrator** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_samlbase.png)

1. **[Zscaler Private Access Administrator Domain and URLs]\(Zscaler Private Access Administrator のドメインと URL\)** セクションで、**IDP** 開始モードでアプリケーションを構成する場合は、次の手順に従います。

    ![Zscaler Private Access Administrator のドメインと URL のシングル サインオン情報](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_url.png)

    a. **[識別子]** ボックスに、`https://<subdomain>.private.zscaler.com/auth/metadata` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://<subdomain>.private.zscaler.com/auth/sso` のパターンを使用して URL を入力します。

    c. **[詳細な URL 設定の表示]** をクリックします。

    d. **[RelayState]** ボックスに、値 `idpadminsso` を入力します。

1.  **SP** 開始モードでアプリケーションを構成する場合は、次の手順を実行します。

    **[サインオン URL]** ボックスに、`https://<subdomain>.private.zscaler.com/auth/sso` のパターンを使用して URL を入力します。

    > [!NOTE] 
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL でこれらの値を更新します。 これらの値を取得するには、[Zscaler Private Access Administrator サポート チーム](https://help.zscaler.com/zpa-submit-ticket)に連絡してください。
 
1. **[SAML 署名証明書]** セクションで、**[Metadata XML (メタデータ XML)]** をクリックし、コンピューターにメタデータ ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_certificate.png) 

1. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/zscalerprivateaccessadministrator-tutorial/tutorial_general_400.png)

1. 別の Web ブラウザー ウィンドウで、Zscaler Private Access Administrator 企業サイトに管理者としてログインします。

1. 上部の **[Administration]** をクリックして **[AUTHENTICATION]** セクションに移動し、**[IdP Configuration]** をクリックします。

    ![Zscaler Private Access Administrator の管理](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_admin.png)

1. 右上隅の **[Add IdP Configuration]** をクリックします。 

    ![Zscaler Private Access Administrator の addidp](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_addpidp.png)

1. **[Add IdP Configuration]** ページで、次の手順に従います。
 
    ![Zscaler Private Access Administrator の idpselect](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_idpselect.png)

    a. **[IdP Metadata File Upload]** フィールドの **[Select File]** をクリックして、Azure AD からダウンロードしたメタデータ ファイルをアップロードします。

    b. Azure AD から **IdP メタデータ** が読み取られ、以下に示すようにすべてのフィールド情報が設定されます。

    ![Zscaler Private Access Administrator の idpconfig](./media/zscalerprivateaccessadministrator-tutorial/idpconfig.png)

    c. **[Administrator]** として **[Single Sign On]** を選択します。

    d. **[Domains]** フィールドから自分のドメインを選択します。
    
    e. **[Save]** をクリックします。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関するページを参照してください。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/zscalerprivateaccessadministrator-tutorial/create_aaduser_01.png)

1. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/zscalerprivateaccessadministrator-tutorial/create_aaduser_02.png)

1. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/zscalerprivateaccessadministrator-tutorial/create_aaduser_03.png)

1. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/zscalerprivateaccessadministrator-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。
  
### <a name="create-a-zscaler-private-access-administrator-test-user"></a>Zscaler Private Access Administrator のテスト ユーザーの作成

Azure AD ユーザーが Zscaler Private Access Administrator にログインできるようにするには、ユーザーを Zscaler Private Access Administrator にプロビジョニングする必要があります。 Zscaler Private Access Administrator の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. 管理者として Zscaler Private Access Administrator の会社サイトにログインします。

1. 上部の **[Administration]** をクリックして **[AUTHENTICATION]** セクションに移動し、**[IdP Configuration]** をクリックします。

    ![Zscaler Private Access Administrator の管理](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_admin.png)

1. メニューの左側にある **[Administrators]** をクリックします。

    ![Zscaler Private Access Administrator の管理者](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_adminstrator.png)

1. 右上隅の **[Add Administrator]** をクリックします。

    ![Zscaler Private Access Administrator の管理者の追加](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_addadmin.png)

1. **[Add Administrator]** ページで、次の手順に従います。

    ![Zscaler Private Access Administrator のユーザー管理](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_useradmin.png)

    a. **[Username]** ボックスに、ユーザーの電子メール (**BrittaSimon@contoso.com** など) を入力します。

    b. **[Password]** ボックスに、ユーザーのパスワードを入力します。

    c. **[Confirm Password]** ボックスに、パスワードを入力します。

    d. **[Role]** に **[Zscaler Private Access Administrator]** を選択します。

    e. **[電子メール]** テキスト ボックスに、ユーザーの電子メール (**BrittaSimon@contoso.com** など) を入力します。

    f. **[Phone]** ボックスに、電話番号を入力します。

    g. **[Timezone]** ボックスで、タイム ゾーンを選択します。

    h. **[Save]** をクリックします。  

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Zscaler Private Access Administrator へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**Zscaler Private Access Administrator に Britta Simon を割り当てるには、次の手順を実行します。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で、**[Zscaler Private Access Administrator]** を選択します。

    ![アプリケーションの一覧の Zscaler Private Access Administrator リンク](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_app.png)  

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで Zscaler Private Access Administrator のタイルをクリックすると、自動的に Zscaler Private Access Administrator アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/zscalerprivateaccessadministrator-tutorial/tutorial_general_01.png
[2]: ./media/zscalerprivateaccessadministrator-tutorial/tutorial_general_02.png
[3]: ./media/zscalerprivateaccessadministrator-tutorial/tutorial_general_03.png
[4]: ./media/zscalerprivateaccessadministrator-tutorial/tutorial_general_04.png

[100]: ./media/zscalerprivateaccessadministrator-tutorial/tutorial_general_100.png

[200]: ./media/zscalerprivateaccessadministrator-tutorial/tutorial_general_200.png
[201]: ./media/zscalerprivateaccessadministrator-tutorial/tutorial_general_201.png
[202]: ./media/zscalerprivateaccessadministrator-tutorial/tutorial_general_202.png
[203]: ./media/zscalerprivateaccessadministrator-tutorial/tutorial_general_203.png

