---
title: 'チュートリアル: Azure Active Directory と BlueJeans の統合 | Microsoft Docs'
description: Azure Active Directory と BlueJeans の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: dfc634fd-1b55-4ba8-94a8-b8288429b6a9
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2018
ms.author: jeedes
ms.openlocfilehash: 2ec94217a8df2efaa23eb3cc2c9d5a80e8037615
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39425989"
---
# <a name="tutorial-azure-active-directory-integration-with-bluejeans"></a>チュートリアル: Azure Active Directory と BlueJeans の統合

このチュートリアルでは、BlueJeans と Azure Active Directory (Azure AD) を統合する方法について説明します。

BlueJeans と Azure AD の統合には、次の利点があります。

- BlueJeans にアクセスする Azure AD ユーザーを制御できます
- ユーザーが自分の Azure AD アカウントで自動的に BlueJeans にサインオン (シングル サインオン) できるようにします
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

BlueJeans と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- BlueJeans でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの BlueJeans の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-bluejeans-from-the-gallery"></a>ギャラリーからの BlueJeans の追加
Azure AD への BlueJeans の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に BlueJeans を追加する必要があります。

**ギャラリーから BlueJeans を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Active Directory][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[アプリケーション]][2]

1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[アプリケーション]][3]

1. 検索ボックスに、「**BlueJeans**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/bluejeans-tutorial/tutorial_bluejeans_search.png)

1. 結果ウィンドウで **[BlueJeans]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/bluejeans-tutorial/tutorial_bluejeans_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、BlueJeans で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する BlueJeans ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと BlueJeans の関連ユーザーの間で、リンク関係が確立されている必要があります。

BlueJeans で、Azure AD の **[ユーザー名]** の値を **[Username]** の値として割り当ててリンク関係を確立します。

BlueJeans で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[BlueJeans のテスト ユーザーの作成](#creating-a-bluejeans-test-user)** - BlueJeans で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、BlueJeans アプリケーションでシングル サインオンを構成します。

**BlueJeans で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **BlueJeans** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![Configure single sign-on][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。

    ![Configure single sign-on](./media/bluejeans-tutorial/tutorial_bluejeans_samlbase.png)

1. **[BlueJeans のドメインと URL]** セクションで、次の手順に従います。

    ![Configure single sign-on](./media/bluejeans-tutorial/tutorial_bluejeans_url.png)

    a. **[サインオン URL]** ボックスに、`https://<companyname>.BlueJeans.com` のパターンを使用して URL を入力します。

    b. **[識別子]** ボックスに、`https://<companyname>.BlueJeans.com` の形式で URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新してください。 これらの値を取得するには、[BlueJeans クライアント サポート チーム](https://support.bluejeans.com/contact)に問い合わせください。

1. **[SAML 署名証明書]** セクションで、**[証明書 (Base64)]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![Configure single sign-on](./media/bluejeans-tutorial/tutorial_bluejeans_certificate.png) 

1. **[保存]** ボタンをクリックします。

    ![Configure single sign-on](./media/bluejeans-tutorial/tutorial_general_400.png)

1. **[BlueJeans 構成]** セクションで、**[BlueJeans の構成]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから**サインアウト URL、パスワードの変更 URL、および SAML シングル サインオン サービス URL** をコピーします。

    ![Configure single sign-on](./media/bluejeans-tutorial/tutorial_bluejeans_configure.png) 

1. 別の Web ブラウザー ウィンドウで、 **BlueJeans** 企業サイトに管理者としてログインします。

1. **[管理] \> [グループ設定] \> [セキュリティ]** の順にクリックします。

   ![管理](./media/bluejeans-tutorial/IC785868.png "Admin")

1. **[セキュリティ]** セクションで、次の手順を実行します。

   ![SAML シングル サインオン](./media/bluejeans-tutorial/IC785869.png "SAML Single Sign On")

   a. **[SAML シングル サインオン]** を選択します。

   b. **[自動プロビジョニングの有効化]** を選択します。

1. 次の手順を実行します。

    ![証明書パス](./media/bluejeans-tutorial/IC785870.png "Certificate Path")

    a. **[ファイルの選択]** をクリックし、ダウンロードした証明書をアップロードします。

    b. **SAML シングル サインオン サービス URL** を **[Login URL]** (ログイン URL) ボックスに貼り付けます。

    c. **パスワードの変更 URL** を **[Password Change URL]** (パスワード変更 URL) ボックスに貼り付けます。

    d. **サインアウト URL** を **[Logout URL]** (ログアウト URL) ボックスに貼り付けます。

1. 次の手順を実行します。

    ![変更を保存](./media/bluejeans-tutorial/IC785874.png "Save Changes")

    a. **[User id]** (ユーザー ID) ボックスに「`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`」と入力します。

    b. **[Email]** (電子メール) ボックスに「`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`」と入力します。

    c. **[変更を保存]** をクリックします。

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/bluejeans-tutorial/create_aaduser_01.png)

1. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。

    ![Azure AD のテスト ユーザーの作成](./media/bluejeans-tutorial/create_aaduser_02.png)

1. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。

    ![Azure AD のテスト ユーザーの作成](./media/bluejeans-tutorial/create_aaduser_03.png)

1. **[ユーザー]** ダイアログ ページで、次の手順を実行します。

    ![Azure AD のテスト ユーザーの作成](./media/bluejeans-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **Create** をクリックしてください。

### <a name="creating-a-bluejeans-test-user"></a>BlueJeans のテスト ユーザーの作成

このセクションの目的は、BlueJeans で Britta Simon というユーザーを作成することです。 BlueJeans では、自動ユーザー プロビジョニングがサポートされています。この設定は、既定で有効になっています。 自動ユーザー プロビジョニングの構成方法の詳細については、[こちら](bluejeans-provisioning-tutorial.md)で確認できます。

**ユーザーを手動で作成する必要がある場合は、次の手順を実行します:**

1. **BlueJeans** 企業サイトに管理者としてログインします。

1. **[管理] \> [ユーザーの管理] \> [ユーザーの追加]** の順にクリックします。

   ![管理](./media/bluejeans-tutorial/IC785877.png "Admin")

   >[!IMPORTANT]
   >**[ユーザーの追加]** タブは、**[セキュリティ]** タブの **[自動プロビジョニングの有効化]** がオフになっている場合にのみ使用できます。 

1. **[ユーザーの追加]** セクションで、次の手順を実行します。

    ![ユーザーの追加](./media/bluejeans-tutorial/IC785886.png "Add User")

    a. 対応するテキスト ボックスに、プロビジョニングする有効な AAD アカウントの **BlueJeans ユーザー名**、**メール アドレス**、**BlueJeans ミーティング ID**、**モデレーター パスコード**、**フル ネーム**、**会社**を入力します。

    b. **[ユーザーの追加]** をクリックします。

>[!NOTE]
>BlueJeans から提供されている他の BlueJeans ユーザー アカウント作成ツールまたは API を使用して、AAD ユーザー アカウントをプロビジョニングできます。

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、BlueJeans へのアクセスを許可することで、Britta Simon が Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200]

**BlueJeans に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201]

1. アプリケーションの一覧で **[BlueJeans]** を選択します。

    ![Configure single sign-on](./media/bluejeans-tutorial/tutorial_bluejeans_app.png)

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202]

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。

### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [BlueJeans] タイルをクリックすると、BlueJeans アプリケーションのログイン ページが表示されます。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)
* [[ユーザー プロビジョニングの構成]](bluejeans-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/bluejeans-tutorial/tutorial_general_01.png
[2]: ./media/bluejeans-tutorial/tutorial_general_02.png
[3]: ./media/bluejeans-tutorial/tutorial_general_03.png
[4]: ./media/bluejeans-tutorial/tutorial_general_04.png

[100]: ./media/bluejeans-tutorial/tutorial_general_100.png

[200]: ./media/bluejeans-tutorial/tutorial_general_200.png
[201]: ./media/bluejeans-tutorial/tutorial_general_201.png
[202]: ./media/bluejeans-tutorial/tutorial_general_202.png
[203]: ./media/bluejeans-tutorial/tutorial_general_203.png
