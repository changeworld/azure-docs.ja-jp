---
title: 'チュートリアル: Azure Active Directory と Vidyard の統合 | Microsoft Docs'
description: Azure Active Directory と Vidyard の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: bed7df23-6e13-4e7c-b4cc-53ed4804664d
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/22/2018
ms.author: jeedes
ms.openlocfilehash: 871942db15d6a3cff45584e33b2191e21d2281a0
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39426457"
---
# <a name="tutorial-azure-active-directory-integration-with-vidyard"></a>チュートリアル: Azure Active Directory と Vidyard の統合

このチュートリアルでは、Vidyard と Azure Active Directory (Azure AD) を統合する方法について説明します。

Vidyard と Azure AD の統合には、次の利点があります。

- Vidyard にアクセスできる Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで Vidyard に自動的にサインオン (シングル サインオン) できるように設定できます。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Vidyard と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Vidyard でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Vidyard の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-vidyard-from-the-gallery"></a>ギャラリーからの Vidyard の追加
Azure AD への Vidyard の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Vidyard を追加する必要があります。

**ギャラリーから Vidyard を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

1. 検索ボックスに「**Vidyard**」と入力し、結果パネルで **Vidyard** を選び、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果リストの Vidyard](./media/vidyard-tutorial/tutorial_vidyard_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Vidyard で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Vidyard ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Vidyard の関連ユーザーの間で、リンク関係が確立されている必要があります。

Vidyard で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[Vidyard テスト ユーザーの作成](#create-a-vidyard-test-user)** - Vidyard で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal で Azure AD のシングル サインオンを有効にして、Vidyard アプリケーションでシングル サインオンを構成します。

**Vidyard で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure portal の **Vidyard** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/vidyard-tutorial/tutorial_vidyard_samlbase.png)

1. アプリケーションを **IDP** 開始モードで構成する場合は、**[Vidyard のドメインと URL]** セクションで次の手順を実行します。

    ![[Vidyard のドメインと URL] のシングル サインオン情報](./media/vidyard-tutorial/tutorial_vidyard_url2.png)

    a. **[識別子]** ボックスに、`https://secure.vidyard.com/sso/saml/<unique id>/metadata` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://secure.vidyard.com/sso/saml/<unique id>/consume` のパターンを使用して URL を入力します。

1. アプリケーションを **SP** 開始モードで構成する場合は、**[詳細な URL 設定の表示]** チェックボックスをオンにして次の手順を実行します。

    ![[Vidyard のドメインと URL] のシングル サインオン情報](./media/vidyard-tutorial/tutorial_vidyard_url1.png)

    **[サインオン URL]** ボックスに、`https://secure.vidyard.com/sso/saml/<unique id>/login` のパターンを使用して URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL に値を置き換えます。実際の値については後で説明します

1. **[SAML 署名証明書]** セクションで、**[証明書 (Base64)]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/vidyard-tutorial/tutorial_vidyard_certificate.png) 

1. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/vidyard-tutorial/tutorial_general_400.png)

1. **[Vidyard 構成]** セクションで、**[Vidyard の構成]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから **SAML シングル サインオン サービスの URL** をコピーします。

    ![Vidyard 構成](./media/vidyard-tutorial/tutorial_vidyard_configure.png)

1. 別の Web ブラウザーのウィンドウで、管理者として Vidyard Software 企業サイトにログインします。

1. Vidyard ダッシュボードから、**[Group]\(グループ\)** > **[Security]\(セキュリティ\)** を選択します

    ![Vidyard 構成](./media/vidyard-tutorial/configure1.png)

1. **[New Profile]\(新しいプロファイル\)** タブをクリックします。

    ![Vidyard 構成](./media/vidyard-tutorial/configure2.png)

1. **[SAML Configuration]\(SAML の構成\)** セクションで、次の手順に従います。

    ![Vidyard 構成](./media/vidyard-tutorial/configure3.png)

    a. **[Profile Name]\(プロファイル名\)** ボックスに一般的なプロファイル名を入力してください。

    b. **[SSO User Login Page]\(SSO ユーザー ログイン ページ\)** の値をコピーして、Azure portal の **[Vidyard のドメインと URL]** セクションにある **[サインオン URL]** ボックスに貼り付けます。

    c. **[ACS URL]** の値をコピーして、Azure portal の **[Vidyard のドメインと URL]** セクションにある **[応答 URL]** ボックスに貼り付けます。

    d. **[Issuer/Metadata URL]\(発行者/メタデータ URL\)** の値をコピーして、Azure portal の **[Vidyard のドメインと URL]** セクションにある **[識別子]** ボックスに貼り付けます。

    e. Azure portal からダウンロードした証明書ファイルをメモ帳で開き、**[X.509 Certificate]\(X.509 証明書\)** ボックスに貼り付けます。

    f. **[SAML Endpoint URL]\(SAML エンドポイント URL\)** ボックスに、Azure portal からコピーした **SAML シングル サインオン サービス URL** の値を貼り付けます。

    g. **[Confirm]\(確認\)** をクリックします。

1. [Single Sign On]\(シングル サインオン\) タブで、既存のプロファイルの横にある **[Assign]\(割り当て\)** を選択します

    ![Vidyard 構成](./media/vidyard-tutorial/configure4.png)

    > [!NOTE]
    > SSO プロファイルを作成したら、ユーザーが Azure 経由でアクセスする必要のある任意のグループに SSO プロファイルを割り当てます。 ユーザーが割り当てられたグループ内に存在しない場合、Vidyard は自動的にユーザー アカウントを作成し、そのロールをリアルタイムで割り当てます。

1. **[Groups Available to Assign]\(割り当て可能なグループ\)** に表示されている、対象の組織グループを選択します。

    ![Vidyard 構成](./media/vidyard-tutorial/configure5.png)

1. **[Groups Currently Assigned]\(現在割り当てられているグループ\)** に、割り当てられているグループが表示されます。 組織に従ってグループのロールを選択し、**[Confirm]\(確認\)** をクリックします。

    ![Vidyard 構成](./media/vidyard-tutorial/configure6.png)

    > [!NOTE]
    > 詳細については、[このドキュメント](https://knowledge.vidyard.com/saml-single-sign-on-authentication/saml-based-single-sign-on-sso-in-vidyard)を参照してください。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/vidyard-tutorial/create_aaduser_01.png)

1. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/vidyard-tutorial/create_aaduser_02.png)

1. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/vidyard-tutorial/create_aaduser_03.png)

1. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/vidyard-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。
 
### <a name="create-a-vidyard-test-user"></a>Vidyard テスト ユーザーの作成

このセクションの目的は、Vidyard で Britta Simon というユーザーを作成することです。 Vidyard では、Just-In-Time プロビジョニングがサポートされています。この設定は、既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 ユーザーがまだ存在しない場合、Vidyard にアクセスしようとすると、新しいユーザーが作成されます。
>[!Note]
>ユーザーを手動で作成する必要がある場合は、[Vidyard サポート チーム](mailto:support@vidyard.com)にお問い合わせください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Vidyard へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**Vidyard に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[Vidyard]** を選択します。

    ![アプリケーションの一覧の Vidyard のリンク](./media/vidyard-tutorial/tutorial_vidyard_app.png)  

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Vidyard] タイルをクリックすると、自動的に Vidyard アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/vidyard-tutorial/tutorial_general_01.png
[2]: ./media/vidyard-tutorial/tutorial_general_02.png
[3]: ./media/vidyard-tutorial/tutorial_general_03.png
[4]: ./media/vidyard-tutorial/tutorial_general_04.png

[100]: ./media/vidyard-tutorial/tutorial_general_100.png

[200]: ./media/vidyard-tutorial/tutorial_general_200.png
[201]: ./media/vidyard-tutorial/tutorial_general_201.png
[202]: ./media/vidyard-tutorial/tutorial_general_202.png
[203]: ./media/vidyard-tutorial/tutorial_general_203.png

