---
title: チュートリアル:Azure Active Directory と Infinite Campus の統合 | Microsoft Docs
description: Azure Active Directory と Infinite Campus の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 3995b544-e751-4e0f-ab8b-c9a3862da6ba
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/30/2018
ms.author: jeedes
ms.openlocfilehash: 9f4adbacf2749e8c8ff2da8f331a007e8dcaaea3
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2018
ms.locfileid: "53099955"
---
# <a name="tutorial-azure-active-directory-integration-with-infinite-campus"></a>チュートリアル:Azure Active Directory と Infinite Campus の統合

このチュートリアルでは、Infinite Campus と Azure Active Directory (Azure AD) を統合する方法について説明します。

Infinite Campus と Azure AD の統合には、次の利点があります。

- Infinite Campus にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Infinite Campus にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Infinite Campus と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Infinite Campus シングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。
- 最低でも、構成を完了するためには Azure Active Directory の管理者である必要があります。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Infinite Campus の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-infinite-campus-from-the-gallery"></a>ギャラリーからの Infinite Campus の追加

Azure AD への Infinite Campus の統合を構成するには、管理対象の SaaS アプリの一覧にギャラリーから Infinite Campus を追加する必要があります。

**ギャラリーから Infinite Campus を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

4. 検索ボックスに「**Infinite Campus**」と入力し、結果パネルで **Infinite Campus** を選び、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果一覧での Infinite Campus](./media/infinitecampus-tutorial/tutorial_infinitecampus_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Infinite Campus で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Infinite Campus ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Infinite Campus の関連ユーザーの間で、リンク関係が確立されている必要があります。

Infinite Campus で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Infinite Campus テスト ユーザーの作成](#creating-a-infinite-campus-test-user)** - Infinite Campus で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal で Azure AD のシングル サインオンを有効にして、Infinite Campus アプリケーションでシングル サインオンを構成します。

**Infinite Campus で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure portal の **Infinite Campus** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

2. **[シングル サインオン方式の選択]** ダイアログで、**[SAML]** モードの **[選択]** をクリックして、シングル サインオンを有効にします。

    ![Configure single sign-on](common/tutorial_general_301.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、**[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![Configure single sign-on](common/editconfigure.png)

5. **[基本的な SAML 構成]** セクションで、**サービス プロバイダー メタデータ ファイル** (ステップ **11.c** にスキップ) がある場合は、次の手順に従います。

    a. **[メタデータ ファイルをアップロードします]** をクリックします。

        ![image](common/b9_saml.png)

    b. **フォルダー ロゴ**をクリックしてメタデータ ファイルを選択し、**[アップロード]** をクリックします。

    ![image](common/b9(1)_saml.png)

    c. メタデータ ファイルが正常にアップロードされると、次に示すように、**識別子**と**応答 URL** の値が、**[基本的な SAML 構成]** セクションのテキスト ボックスに自動的に設定されます。

    ![image](./media/infinitecampus-tutorial/tutorial_infinitecampus_url.png)

    d. **[サインオン URL]** ボックスに、次のパターン (ホスティング モデルでは、ドメインが異なります) を使用して URL を入力します。 `https://<DOMAIN>.infinitecampus.com/campus/SSO/<DISTRICTNAME>/SIS`

    > [!NOTE]
    > Infinite Campus SSO サービス プロバイダー構成ページで、**サービス プロバイダー メタデータ ファイル**を取得します。これについては後で説明します。 Infinite Campus で新しい SAML サービス プロバイダー構成を使用して開始する場合は、**手順 11** に進んで、サービス プロバイダー メタデータ ファイルのエクスポートを完了します。

6. **サービス プロバイダー メタデータ ファイル**がない場合は、次の手順に従います (ドメインはホスティング モデルによって異なることに注意してください)。

    a. **[サインオン URL]** ボックスに、`https://<DOMAIN>.infinitecampus.com/campus/SSO/<DISTRICTNAME>/SIS` のパターンを使用して URL を入力します。

    b. **[識別子]** ボックスに、`https://<DOMAIN>.infinitecampus.com/campus/<DISTRICTNAME>` の形式で URL を入力します。

    c. **[応答 URL]** ボックスに、`https://<DOMAIN>.infinitecampus.com/campus/SSO/<DISTRICTNAME>` のパターンを使用して URL を入力します。

    ![[Infinite Campus のドメインと URL] のシングル サインオン情報](./media/infinitecampus-tutorial/tutorial_infinitecampus_url1.png)

6. **[SAML 署名証明書]** ページの **[SAML 署名証明書]** セクションで、コピー **アイコン**をクリックして **[アプリのフェデレーション メタデータ URL]** をコピーし、メモ帳に貼り付けます。

    ![証明書のダウンロードのリンク](./media/infinitecampus-tutorial/tutorial_infinitecampus_certificate.png) 

7. **[無限キャンパス セットアップ]** セクションで、Azure メタデータ ファイルまたは URL のアップロードまたは利用する際に以下の値を使って検証します。

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

    ![Infinite Campus の構成](common/configuresection.png)

8. 別の Web ブラウザー ウィンドウで、セキュリティ管理者として Infinite Campus にログインします。

9. メニューの左側にある **[System Administration]\(システム管理\)** をクリックします。

    ![管理](./media/infinitecampus-tutorial/tutorial_infinitecampus_admin.png)

10. **[User Security]\(ユーザー セキュリティ\)** > **[SAML Management]\(SAML 管理\)** > **[SSO Service Provider Configuration]\(SSO サービス プロバイダーの構成\)** に移動します。

    ![SAML](./media/infinitecampus-tutorial/tutorial_infinitecampus_saml.png)

11. **[SSO Service Provider Configuration]\(SSO サービス プロバイダーの構成\)** ページで、次の手順に従います。

    ![SSO](./media/infinitecampus-tutorial/tutorial_infinitecampus_sso.png)

    a. **[Enable SAML Single Sign On]\(SAML シングル サインオンを有効にする\)** チェック ボックスをオンにします。
    
    b. **[Select an option to retrieve Identity Provider (IDP) server data]\(ID プロバイダー (IDP) サーバー データ取得のオプションを選択する\)** セクションで、**[Metadata URL]\(メタデータ URL\)** を選択し、**アプリのフェデレーション メタデータ URL** をボックスに貼り付けて、**[Sync]\(同期\)** をクリックします。

    c. **[Service Provider Metadata]\(サービス プロバイダー メタデータ\)** リンクをクリックして**サービス プロバイダー メタデータ ファイル**をコンピューターに保存し、それを Azure portal の **[基本的な SAML 構成]** セクションにアップロードして、**[識別子]** と **[応答 URL]** の値を自動的に設定します (アップロードと値の自動設定についてはステップ 4、手動入力についてはステップ 5 を参照)。

    d. **[Sync]\(同期\)** をクリックすると、**[SSO Service Provider Configuration]\(SSO サービス プロバイダーの構成\)** ページに値が自動的に設定されます。

    e. **[Save]** をクリックします。

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure portal で Britta Simon という_単一の_テスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、**[Azure Active Directory]**、**[ユーザー]**、**[すべてのユーザー]** の順に選択します。

    ![Azure AD ユーザーの作成][100]

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![Azure AD のテスト ユーザーの作成](common/create_aaduser_01.png) 

3. [ユーザーのプロパティ] で、次の手順を実行します。

    ![Azure AD のテスト ユーザーの作成](common/create_aaduser_02.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    b. **[ユーザー名]** フィールドに「**brittasimon@yourcompanydomain.extension**」と入力します  
    たとえば、BrittaSimon@contoso.com のように指定します。

    c. **[プロパティ]** を選択し、**[パスワードを表示]** チェック ボックスをオンにして、[パスワード] ボックスに表示された値を書き留めます。

    d. **作成**を選択します。

### <a name="creating-a-infinite-campus-test-user"></a>Infinite Campus テスト ユーザーの作成

Infinite Campus はデモグラフィック中心のアーキテクチャです。 Infinite Campus プラットフォームでのユーザーの追加については、[Infinite Campus サポート チーム](mailto:sales@infinitecampus.com)に問い合わせてください。

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Infinite Campus へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、**[すべてのアプリケーション]** を選択します。

    ![ユーザーの割り当て][201]

2. アプリケーションの一覧で **[Infinite Campus]** を選択します。

    ![Configure single sign-on](./media/infinitecampus-tutorial/tutorial_infinitecampus_app.png) 

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202]

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. **[割り当ての追加]** ダイアログで、**[割り当て]** ボタンを選択します。

### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで Infinite Campus のタイルをクリックすると、自動的に Infinite Campus アプリケーションにサインオンします。 Azure AD を管理しているものとおなじブラウザで無限キャンパスにログインしている場合、テスト ユーザーとしてログインしていることを確認してください。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関するページを参照してください。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: common/tutorial_general_01.png
[2]: common/tutorial_general_02.png
[3]: common/tutorial_general_03.png
[4]: common/tutorial_general_04.png

[100]: common/tutorial_general_100.png

[201]: common/tutorial_general_201.png
[202]: common/tutorial_general_202.png
[203]: common/tutorial_general_203.png
