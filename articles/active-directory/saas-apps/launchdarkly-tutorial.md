---
title: 'チュートリアル: Azure Active Directory と LaunchDarkly の統合 | Microsoft Docs'
description: Azure Active Directory と LaunchDarkly の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 0e9cb37e-16bf-493b-bfc8-8d9840545a1e
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2018
ms.author: jeedes
ms.openlocfilehash: 54bf459f6acd7649f3ad1a546bef1d0429393161
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39420761"
---
# <a name="tutorial-azure-active-directory-integration-with-launchdarkly"></a>チュートリアル: Azure Active Directory と LaunchDarkly の統合

このチュートリアルでは、LaunchDarkly と Azure Active Directory (Azure AD) を統合する方法について説明します。

LaunchDarkly と Azure AD の統合には、次の利点があります。

- LaunchDarkly にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に LaunchDarkly にサインオン (シングル サインオン) できるようにすることが可能です。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

LaunchDarkly と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- LaunchDarkly でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの LaunchDarkly の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-launchdarkly-from-the-gallery"></a>ギャラリーからの LaunchDarkly の追加
Azure AD への LaunchDarkly の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に LaunchDarkly を追加する必要があります。

**ギャラリーから LaunchDarkly を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

1. 検索ボックスに「**LaunchDarkly**」と入力し、結果ウィンドウで **LaunchDarkly** を選び、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果一覧の LaunchDarkly](./media/launchdarkly-tutorial/tutorial_launchdarkly_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、LaunchDarkly で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する LaunchDarkly ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと LaunchDarkly の関連ユーザーの間で、リンク関係が確立されている必要があります。

LaunchDarkly で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[LaunchDarkly のテスト ユーザーの作成](#create-a-launchdarkly-test-user)** - LaunchDarkly で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、LaunchDarkly アプリケーションでシングル サインオンを構成します。

**LaunchDarkly で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **LaunchDarkly** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。

    ![[シングル サインオン] ダイアログ ボックス](./media/launchdarkly-tutorial/tutorial_launchdarkly_samlbase.png)

1. アプリケーションを **IDP** 開始モードで構成する場合は、**[LaunchDarkly のドメインと URL]** セクションで次の手順を実行します。

    ![[LaunchDarkly のドメインと URL] のシングル サインオン情報](./media/launchdarkly-tutorial/tutorial_launchdarkly_url.png)

    a. **[識別子 (エンティティ ID)]** ボックスに `app.launchdarkly.com` という URL を入力します。

    b. **[応答 URL]** ボックスに、`https://app.launchdarkly.com/trust/saml2/acs/<customers-unique-id>` のパターンを使用して URL を入力します。
    
    > [!NOTE]
    > 応答 URL 値は、実際の値ではありません。 実際の応答 URL に値を置き換えます。実際の値については後で説明します。

1. アプリケーションを **SP** 開始モードで構成する場合は、**[詳細な URL 設定の表示]** チェックボックスをオンにして次の手順を実行します。

    ![[LaunchDarkly のドメインと URL] のシングル サインオン情報](./media/launchdarkly-tutorial/tutorial_launchdarkly_url1.png)

    **[サインオン URL]** ボックスに、URL として「`https://app.launchdarkly.com`」を入力します。

1. **[SAML 署名証明書]** セクションで、**[Certificate (Base64) (証明書 (Base64)) ]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/launchdarkly-tutorial/tutorial_launchdarkly_certificate.png) 

1. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/launchdarkly-tutorial/tutorial_general_400.png)
    
1. **[LaunchDarkly 構成]** セクションで、**[LaunchDarkly の構成]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから**シングル サインオン サービス URL** をコピーします。

    ![LaunchDarkly 構成](./media/launchdarkly-tutorial/tutorial_launchdarkly_configure.png)

1. 別の Web ブラウザーのウィンドウで、LaunchDarkly 企業サイトに管理者としてログインします。

1. 左側のナビゲーション パネルから **[アカウント設定]** を選択します。

    ![LaunchDarkly 構成](./media/launchdarkly-tutorial/configure1.png)

1. **[セキュリティ]** タブをクリックします。

    ![LaunchDarkly 構成](./media/launchdarkly-tutorial/configure2.png)

1. **[ENABLE SSO]\(SSO を有効にする) **、 **[EDIT SAML CONFIGURATION]\(SAML 構成の編集)** の順にクリックします。

    ![LaunchDarkly 構成](./media/launchdarkly-tutorial/configure3.png)

1. **[Edit your SAML configuration]\(SAML の構成の編集) ** セクションで、次の手順を実行します。

    ![LaunchDarkly 構成](./media/launchdarkly-tutorial/configure4.png)

    a. インスタンスの **SAML コンシューマー サービス URL** をコピーし、Azure Portal で、**[LaunchDarkly ドメインと URL]** セクションの [応答 URL] ボックスに貼り付けます。

    b. **[サインオン URL]** テキストボックスに、Azure Portal からコピーした **シングル サインオン サービス URL** の値を貼り付けます。

    c. Azure Portal からダウンロードした証明書をメモ帳で開き、その内容をコピーして **[X.509 証明書]** ボックスに貼り付けます。または、 **[upload one]\(アップロードする)** をクリックして証明書を直接アップロードできます。

    d. **[保存]**

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/launchdarkly-tutorial/create_aaduser_01.png)

1. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/launchdarkly-tutorial/create_aaduser_02.png)

1. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/launchdarkly-tutorial/create_aaduser_03.png)

1. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/launchdarkly-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。

### <a name="create-a-launchdarkly-test-user"></a>LaunchDarkly テスト ユーザーを作成する

このセクションの目的は、LaunchDarkly で Britta Simon というユーザーを作成することです。 LaunchDarkly では、Just-In-Time プロビジョニングがサポートされています。この設定は、既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 LaunchDarkly にアクセスしようとすると、ユーザーがまだ存在しない場合は新しいユーザーが作成されます。
>[!Note]
>ユーザーを手動で作成する必要がある場合は、[LaunchDarkly クライアント サポート チーム](mailto:support@launchdarkly.com)にお問い合わせください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に LaunchDarkly へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**LaunchDarkly に Britta Simon を割り当てるには、次の手順を実行します。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[LaunchDarkly]** を選択します。

    ![アプリケーションの一覧の LaunchDarkly のリンク](./media/launchdarkly-tutorial/tutorial_launchdarkly_app.png)  

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [LaunchDarkly] タイルをクリックすると、LaunchDarkly アプリケーションに自動的にサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/launchdarkly-tutorial/tutorial_general_01.png
[2]: ./media/launchdarkly-tutorial/tutorial_general_02.png
[3]: ./media/launchdarkly-tutorial/tutorial_general_03.png
[4]: ./media/launchdarkly-tutorial/tutorial_general_04.png

[100]: ./media/launchdarkly-tutorial/tutorial_general_100.png

[200]: ./media/launchdarkly-tutorial/tutorial_general_200.png
[201]: ./media/launchdarkly-tutorial/tutorial_general_201.png
[202]: ./media/launchdarkly-tutorial/tutorial_general_202.png
[203]: ./media/launchdarkly-tutorial/tutorial_general_203.png

