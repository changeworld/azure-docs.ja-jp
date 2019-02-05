---
title: チュートリアル:Azure Active Directory と AnswerHub の統合 | Microsoft Docs
description: Azure Active Directory と AnswerHub の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 818b91d7-01df-4b36-9706-f167c710a73c
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.openlocfilehash: 41a1eef4ff417890114addcac00e2df3e49dc529
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/31/2019
ms.locfileid: "55453265"
---
# <a name="tutorial-azure-active-directory-integration-with-answerhub"></a>チュートリアル:Azure Active Directory と AnswerHub の統合

このチュートリアルでは、AnswerHub と Azure Active Directory (Azure AD) を統合する方法について説明します。
AnswerHub と Azure AD の統合には、次の利点があります。

* AnswerHub にアクセスするユーザーを Azure AD で管理できます。
* ユーザーが自分の Azure AD アカウントを使用して AnswerHub に自動的にサインイン (シングル サインオン) するように設定できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Azure AD と AnswerHub の統合を構成するには、次の項目が必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* AnswerHub でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* AnswerHub では、**SP** Initiated SSO がサポートされます

## <a name="adding-answerhub-from-the-gallery"></a>ギャラリーからの AnswerHub の追加

AnswerHub の Azure AD への統合を構成するには、AnswerHub をギャラリーから管理対象 SaaS アプリの一覧に追加する必要があります。

**ギャラリーから AnswerHub を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**AnswerHub**」と入力し、結果パネルで **[AnswerHub]** を選択し、**[追加]** ボタンをクリックして、アプリケーションを追加します。

     ![結果一覧の AnswerHub](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** という名前のテスト ユーザーに基づいて、AnswerHub で Azure AD のシングル サインオンを構成およびテストします。
シングル サインオンを機能させるには、Azure AD ユーザーと AnswerHub 内の関連ユーザーとの間にリンク関係が確立されている必要があります。

AnswerHub で Azure AD のシングル サインオンを構成およびテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[AnswerHub シングル サインオンの構成](#configure-answerhub-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[AnswerHub のテスト ユーザーの作成](#create-answerhub-test-user)** - AnswerHub で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

AnswerHub で Azure AD のシングル サインオンを構成するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com/) の **[AnswerHub]** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、**[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、**[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    ![[AnswerHub のドメインと URL] のシングル サインオン情報](common/sp-identifier.png)

    a. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。`https://<company>.answerhub.com`

    b. **[識別子 (エンティティ ID)]** ボックスに、次のパターンを使用して URL を入力します。`https://<company>.answerhub.com`

    > [!NOTE]
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新します。 これらの値を取得するには、[AnswerHub クライアント サポート チーム](mailto:success@answerhub.com)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、**[ダウンロード]** をクリックして要件のとおりに指定したオプションからの**証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

6. **[AnswerHub のセットアップ]** セクションで、要件どおりの適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-answerhub-single-sign-on"></a>AnswerHub のシングル サインオンを構成する

1. 別の Web ブラウザーのウィンドウで、管理者として AnswerHub 企業サイトにログインします。

    > [!NOTE]
    > AnswerHub の構成について不明点がある場合は、[AnswerHub サポート チーム](mailto:success@answerhub.com.)にお問い合わせください。

2. **[Administration]** に移動します。

3. **[User and Group]** タブをクリックします。

4. 左側のナビゲーション ウィンドウで、**[Social Settings]** セクションの **[SAML Setup]** をクリックします。

5. **[IDP Config]** タブをクリックします。

6. **[IDP Config]** タブで、次の手順を実行します。

    ![SAML のセットアップ](./media/answerhub-tutorial/ic785172.png "SAML Setup")  
  
    a. **[IDP Login URL]\(IDP ログイン URL\)** ボックスに、Azure portal からコピーした **[ログイン URL]** を貼り付けます。
  
    b. **[IDP Logout URL]\(IDP ログアウト URL\)** ボックスに、Azure portal からコピーした **[ログアウト URL]** 値を貼り付けます。

    c. **[IDP Name Identifier Format] \(IDP 名前識別子形式)** テキスト ボックスに、Azure Portal の **[ユーザー属性]** セクションで選択したのと同じユーザー識別子の値を入力します。
  
    d. **[Keys and Certificates]** をクリックします。

7. **[Keys and Certificates]\(キーと証明書\)** タブで、次の手順を実行します。

    ![Keys and Certificates (キーと証明書)](./media/answerhub-tutorial/ic785173.png "Keys and Certificates")  

    a. Azure Portal からダウンロードした Base-64 でエンコードされた証明書をメモ帳で開き、その内容をクリップボードにコピーしてから、それを **[IDP Public Key (x509 Format)] \(IDP 公開キー (x509 形式))** テキスト ボックスに貼り付けます。
  
    b. **[Save]** をクリックします。

8. **[IDP Config]** タブで、**[Save]** をクリックします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、**[Azure Active Directory]**、**[ユーザー]**、**[すべてのユーザー]** の順に選択します。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](common/users.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![[新しいユーザー] ボタン](common/new-user.png)

3. [ユーザーのプロパティ] で、次の手順を実行します。

    ![[ユーザー] ダイアログ ボックス](common/user-properties.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    b. **[ユーザー名]** フィールドに「**brittasimon@yourcompanydomain.extension**」と入力します。  
    たとえば、BrittaSimon@contoso.com のように指定します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、[パスワード] ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、AnswerHub へのアクセス権を付与することによって、Britta Simon が Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、**[すべてのアプリケーション]**、**[AnswerHub]** の順に選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で、**[AnswerHub]** を選択します。

    ![アプリケーションの一覧の AnswerHub のリンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、**[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、**[割り当て]** ボタンをクリックします。

### <a name="create-answerhub-test-user"></a>AnswerHub のテスト ユーザーの作成

Azure AD ユーザーが AnswerHub にログインできるようにするには、そのユーザーを AnswerHub にプロビジョニングする必要があります。 AnswerHub の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. **AnswerHub** 企業サイトに管理者としてログインします。

2. **[Administration]** に移動します。

3. **[Users & Groups]** タブをクリックします。

4. 左側のナビゲーション ウィンドウで、**[Manage Users]\(ユーザーの管理\)** セクションの **[Create or import users]\(ユーザーの作成またはインポート\)** をクリックし、**[Users & Groups]\(ユーザーとグループ\)** をクリックします。

   ![Users & Groups (ユーザーとグループ)](./media/answerhub-tutorial/ic785175.png "Users & Groups")

5. プロビジョニングする有効な Azure Active Directory ユーザー アカウントの**電子メール アドレス**、**ユーザー名**、**パスワード**を対応するボックスに入力し、**[Save]** をクリックします。

> [!NOTE]
> 他の AnswerHub ユーザー アカウントの作成ツールまたは AnswerHub から提供されている API を使用して、AAD ユーザー アカウントをプロビジョニングできます。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [AnswerHub] タイルをクリックすると、SSO を設定した AnswerHub に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory でのアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

