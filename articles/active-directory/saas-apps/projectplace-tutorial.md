---
title: チュートリアル:Azure Active Directory と Projectplace の統合 | Microsoft Docs
description: このチュートリアルでは、Azure Active Directory と Projectplace の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 298059ca-b652-4577-916a-c31393d53d7a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/26/2019
ms.author: jeedes
ms.openlocfilehash: 17fbc6bc4f022a15c34c5ca7b9465be392cdc639
ms.sourcegitcommit: 179918af242d52664d3274370c6fdaec6c783eb6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/13/2019
ms.locfileid: "65560624"
---
# <a name="tutorial-azure-active-directory-integration-with-projectplace"></a>チュートリアル:Azure Active Directory と Projectplace の統合

このチュートリアルでは、Projectplace と Azure Active Directory (Azure AD) を統合する方法について説明します。

この統合には、次の利点があります。

* Azure AD を使用して、Projectplace にアクセスするユーザーを管理できます。
* ユーザーが自分の Azure AD アカウントを使用して Projectplace に自動的にサインイン (シングル サインオン) するように設定できます。
* 1 つの中央サイト (Azure ポータル) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Azure AD と Projectplace の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD 環境がない場合は、[1 か月の試用版](https://azure.microsoft.com/pricing/free-trial/)サブスクリプションにサインアップできます。
* シングル サインオンが有効な Projectplace サブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Projectplace では、SP によって開始される SSO がサポートされます

## <a name="add-projectplace-from-the-gallery"></a>ギャラリーからの Projectplace の追加

Azure AD への Projectplace の統合をセットアップするには、ギャラリーからマネージド SaaS アプリの一覧に Projectplace を追加する必要があります。

1. [Azure portal](https://portal.azure.com) の左側のウィンドウで、 **[Azure Active Directory]** を選択します。

    ![[Azure Active Directory] を選択します。](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]**  >  **[すべてのアプリケーション]** の順に移動します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. アプリケーションを追加するには、ウィンドウの上部の **[新しいアプリケーション]** を選択します。

    ![[新しいアプリケーション] を選択する](common/add-new-app.png)

4. 検索ボックスに、「**Projectplace**」と入力します。 検索結果で **[Projectplace]** を選択し、 **[追加]** を選択します。

     ![[検索結果]](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、Britta Simon という名前のテスト ユーザーを使用して、Projectplace で Azure AD のシングル サインオンを構成およびテストします。
シングル サインオンを有効にするには、Azure AD ユーザーと Projectplace の対応するユーザーの間で、関係が確立されている必要があります。

Projectplace で Azure AD のシングル サインオンを構成およびテストするには、以下の手順を完了する必要があります。

1. **[Azure AD のシングル サインオンを構成](#configure-azure-ad-single-sign-on)** して、この機能をユーザーに対して有効にします。
2. アプリケーション側で **[Projectplace シングル サインオンを構成](#configure-projectplace-single-sign-on)** します。
3. Azure AD のシングル サインオンをテストするための **[Azure AD テスト ユーザーを作成](#create-an-azure-ad-test-user)** します。
4. **[Azure AD テスト ユーザーを割り当て](#assign-the-azure-ad-test-user)** て、Azure AD のシングル サインオンをそのユーザーに対して有効にします。
5. ユーザーの Azure AD 表現にリンクされた **[Projectplace テスト ユーザーを作成](#create-a-projectplace-test-user)** します。
6. **[シングル サインオンをテスト](#test-single-sign-on)** して、この構成が機能することを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal で Azure AD のシングル サインオンを有効にします。

Projectplace で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **Projectplace** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![[シングル サインオン] の選択](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログ ボックスで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン方式の選択](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、**編集**アイコンを選択して **[基本的な SAML 構成]** ダイアログ ボックスを開きます。

    ![[編集] アイコン](common/edit-urls.png)

4. **[基本的な SAML 構成]** ダイアログ ボックスの **[サインオン URL]** ボックスに、次のパターンで URL を入力します。

    `https://<company>.projectplace.com`

   ![[基本的な SAML 構成] ダイアログ ボックス](common/sp-signonurl.png)
    > [!NOTE]
    > この値は、プレースホルダーです。 実際のサインオン URL を使用する必要があります。 この値を取得するには、[Projectplace サポート チーム](https://success.planview.com/Projectplace/Support)に問い合わせてください。 また、Azure portal の **[基本的な SAML 構成]** ダイアログ ボックスに示されているパターンを参照することもできます。

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、実際の要件に従って、 **[フェデレーション メタデータ XML]** の横にある **[ダウンロード]** リンクを選択し、証明書を自分のコンピューターに保存します。

    ![証明書のダウンロード リンク](common/metadataxml.png)

6. **[Projectplace のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

    ![構成 URL をコピーする](common/copy-configuration-urls.png)

    1. **ログイン URL**。

    1. **Azure AD 識別子**。

    1. **ログアウト URL**。

### <a name="configure-projectplace-single-sign-on"></a>Projectplace シングル サインオンの構成

**Projectplace** 側でシングル サインオンを構成するには、ダウンロードした**フェデレーション メタデータ XML** と Azure portal からコピーした URL を [Projectplace サポート チーム](https://success.planview.com/Projectplace/Support)に送信する必要があります。 このチームは、SAML SSO 接続が両方の側で正しく設定されていることを確認します。

>[!NOTE]
>シングル サインオンの構成は、[Projectplace サポート チーム](https://success.planview.com/Projectplace/Support)が実行する必要があります。 構成が完了すると直ちに、通知が届きます。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションでは、Azure portal で Britta Simon という名前のテスト ユーザーを作成します。

1. Azure portal で、左側のウィンドウの **[Azure Active Directory]** を選択し、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。

    ![[すべてのユーザー] の選択](common/users.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![[新しいユーザー] の選択](common/new-user.png)

3. **[ユーザー]** ダイアログ ボックスで、次の手順を実行します。

    ![[ユーザー] ダイアログ ボックス](common/user-properties.png)

    1. **[名前]** ボックスに「**BrittaSimon**」と入力します。
  
    1. **[ユーザー名]** ボックスに、「**BrittaSimon@\<yourcompanydomain>.\<extension>** 」と入力します。 (例: BrittaSimon@contoso.com)。

    1. **[パスワードを表示]** を選択し、 **[パスワード]** ボックスの値を書き留めます。

    1. **作成** を選択します。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Projectplace へのアクセスを許可することで、このユーザーが Azure AD シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** 、 **[Projectplace]** の順に選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で、 **[Projectplace]** を選択します。

    ![アプリケーションの一覧](common/all-applications.png)

3. 左側のウィンドウで **[ユーザーとグループ]** を選択します。

    ![[ユーザーとグループ] の選択](common/users-groups-blade.png)

4. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログ ボックスで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] を選択する](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログ ボックスで、ユーザーの一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内にロール値が必要な場合、 **[ロールの選択]** ダイアログ ボックスで、一覧からユーザーに適したロールを選択します。 画面の下部にある **[選択]** ボタンをクリックします。

7. **[割り当ての追加]** ダイアログ ボックスで **[割り当て]** を選びます。

### <a name="create-a-projectplace-test-user"></a>Projectplace のテスト ユーザーの作成

Azure AD ユーザーが Projectplace にサインインできるようにするには、それらを Projectplace に追加する必要があります。 それらを手動で追加する必要があります。

ユーザー アカウントを作成するには、以下の手順に従います。

1. **Projectplace** 企業サイトに管理者としてサインインします。

2. **[People]\(ユーザー\)** に移動し、 **[Members]\(メンバー\)** を選択します。
   
    ![[People]\(ユーザー\) に移動し、[Members]\(メンバー\) を選択する](./media/projectplace-tutorial/ic790228.png "People")

3. **[Add Member]\(メンバーの追加\)** を選択します。
   
    ![[Add Member]\(メンバーの追加\) を選択する](./media/projectplace-tutorial/ic790232.png "Add Members")

4. **[Add Member]\(メンバーの追加\)** セクションで、次の手順に従います。
   
    ![[Add Member]\(メンバーの追加\) セクション](./media/projectplace-tutorial/ic790233.png "New Members")
   
    1. **[New Members]\(新しいメンバー\)** ボックスに、追加する有効な Azure AD アカウントの電子メール アドレスを入力します。
   
    1. **[送信]** を選択します。

   Azure AD のアカウント所有者には、そのアカウントがアクティブになる前に、アカウント確認用のリンクを含むメールが送信されます。

>[!NOTE]
>Projectplace から提供されている他の任意のユーザー アカウント作成ツールまたは API を使用して、Azure AD ユーザー アカウントを追加することもできます。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

ここで、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストする必要があります。

アクセス パネルで [Projectplace] タイルを選択すると、SSO を設定した Projectplace インスタンスに自動的にサインインします。 詳細については、「[マイ アプリ ポータルでアプリにアクセスして使用する](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)」を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリケーションと Azure Active Directory との統合に関するチュートリアル](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
