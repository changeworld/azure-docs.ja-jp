---
title: 'チュートリアル: Azure Active Directory と Trisotech Digital Enterprise Server の統合 | Microsoft Docs'
description: Azure Active Directory と Trisotech Digital Enterprise Server の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 6d54d20c-eca1-4fa6-b56a-4c3ed0593db0
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2018
ms.author: jeedes
ms.openlocfilehash: e36a4be3a95b67c040855171d4b167e495a22496
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39439628"
---
# <a name="tutorial-azure-active-directory-integration-with-trisotech-digital-enterprise-server"></a>チュートリアル: Azure Active Directory と Trisotech Digital Enterprise Server の統合

このチュートリアルでは、Trisotech Digital Enterprise Server と Azure Active Directory (Azure AD) を統合する方法について説明します。

Trisotech Digital Enterprise Server と Azure AD の統合には、次の利点があります。

- Trisotech Digital Enterprise Server にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが各自の Azure AD アカウントで Trisotech Digital Enterprise Server に自動的にサインオン (シングル サインオン) するように設定できます。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Azure AD と Trisotech Digital Enterprise Server の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Trisotech Digital Enterprise Server でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Trisotech Digital Enterprise Server の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-trisotech-digital-enterprise-server-from-the-gallery"></a>ギャラリーからの Trisotech Digital Enterprise Server の追加
Azure AD への Trisotech Digital Enterprise Server の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Trisotech Digital Enterprise Server を追加する必要があります。

**ギャラリーから Trisotech Digital Enterprise Server を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

1. 検索ボックスに「**Trisotech Digital Enterprise Server**」と入力し、結果パネルで **[Trisotech Digital Enterprise Server]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果リスト内の Trisotech Digital Enterprise Server](./media/trisotechdigitalenterpriseserver-tutorial/tutorial_trisotechdigitalenterpriseserver_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Trisotech Digital Enterprise Server で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Trisotech Digital Enterprise Server ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Trisotech Digital Enterprise Server の関連ユーザーの間で、リンク関係が確立されている必要があります。

Trisotech Digital Enterprise Server で Azure AD のシングル サインオンを構成してテストするには、次の手順を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[Trisotech Digital Enterprise Server のテスト ユーザーの作成](#create-a-trisotech-digital-enterprise-server-test-user)** - Trisotech Digital Enterprise Server で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にし、Trisotech Digital Enterprise Server アプリケーションでシングル サインオンを構成します。

**Trisotech Digital Enterprise Server で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Trisotech Digital Enterprise Server** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/trisotechdigitalenterpriseserver-tutorial/tutorial_trisotechdigitalenterpriseserver_samlbase.png)

1. **[Trisotech Digital Enterprise Server のドメインと URL]** セクションで、次の手順を実行します。

    ![[Trisotech Digital Enterprise Server のドメインと URL] のシングル サインオン情報](./media/trisotechdigitalenterpriseserver-tutorial/tutorial_trisotechdigitalenterpriseserver_url.png)

    a. **[サインオン URL]** ボックスに、`https://<companyname>.trisotech.com` のパターンを使用して URL を入力します。

    b. **[識別子]** ボックスに、`https://<companyname>.trisotech.com` の形式で URL を入力します。

    > [!NOTE] 
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新してください。 これらの値を取得するには、[Trisotech Digital Enterprise Server サポート チーム](mailto:support@trisotech.com)にご連絡ください。

1. **[SAML 署名証明書]** セクションで、コピー ボタンをクリックして **[App Federation Metadata Url]\(アプリケーション フェデレーション メタデータ URL\)** をコピーし、メモ帳に貼り付けます。 

    ![証明書のダウンロードのリンク](./media/trisotechdigitalenterpriseserver-tutorial/tutorial_trisotechdigitalenterpriseserver_certificate.png)

1. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/trisotechdigitalenterpriseserver-tutorial/tutorial_general_400.png)

1. 別の Web ブラウザー ウィンドウで、Trisotech Digital Enterprise Server 構成企業サイトに管理者としてログインします。

1. **メニュー アイコン**をクリックし、**[管理]** を選択します。

    ![Configure single sign-on](./media/trisotechdigitalenterpriseserver-tutorial/user1.png)

1. **[User Provider]\(ユーザー プロバイダー\)** を選択します。

    ![Configure single sign-on](./media/trisotechdigitalenterpriseserver-tutorial/user2.png)

1. **[User Provider Configurations]\(ユーザー プロバイダー構成\)** セクションで、次の手順を実行します。

    ![Configure single sign-on](./media/trisotechdigitalenterpriseserver-tutorial/user3.png)

    a. **[認証方法]** のドロップダウン リストから **[Secured Assertion Markup Language 2 (SAML 2)]** を選択します。

    b. **[メタデータ URL]** テキスト ボックスに、Azure Portal からコピーした **[アプリのフェデレーション メタデータ URL]** 値を貼り付けます。

    c. **[アプリケーション ID]** テキストボックスに、次のパターンを使って URL を入力します: `https://<companyname>.trisotech.com`。

    d. **[保存]**

    e. **[Allowed Domains (empty means everyone)]\(許可されているドメイン (空の場合はすべてのユーザー))\** テキストボックスにドメイン名を入力します。許可されているドメインに一致するユーザーのライセンスが自動的に割り当てられます

    f. **[保存]**

 ### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/trisotechdigitalenterpriseserver-tutorial/create_aaduser_01.png)

1. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/trisotechdigitalenterpriseserver-tutorial/create_aaduser_02.png)

1. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/trisotechdigitalenterpriseserver-tutorial/create_aaduser_03.png)

1. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/trisotechdigitalenterpriseserver-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。
 
### <a name="create-a-trisotech-digital-enterprise-server-test-user"></a>Trisotech Digital Enterprise Server テスト ユーザーの作成

このセクションの目的は、Trisotech Digital Enterprise Server で Britta Simon というユーザーを作成することです。 Trisotech Digital Enterprise Server では、Just-In-Time プロビジョニングがサポートされています。この設定は、既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 存在しない Trisotech Digital Enterprise Server ユーザーにアクセスしようとすると、新しいユーザーが自動的に作成されます。
>[!Note]
>ユーザーを手動で作成する必要がある場合は、[Trisotech Digital Enterprise Server サポート チーム](mailto:support@trisotech.com)にお問い合わせください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Trisotech Digital Enterprise Server へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**Britta Simon を Trisotech Digital Enterprise Server に割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[Trisotech Digital Enterprise Server]** を選択します。

    ![アプリケーションの一覧の Trisotech Digital Enterprise Server のリンク](./media/trisotechdigitalenterpriseserver-tutorial/tutorial_trisotechdigitalenterpriseserver_app.png)  

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Trisotech Digital Enterprise Server] タイルをクリックすると、Trisotech Digital Enterprise Server アプリケーションに自動的にサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/trisotechdigitalenterpriseserver-tutorial/tutorial_general_01.png
[2]: ./media/trisotechdigitalenterpriseserver-tutorial/tutorial_general_02.png
[3]: ./media/trisotechdigitalenterpriseserver-tutorial/tutorial_general_03.png
[4]: ./media/trisotechdigitalenterpriseserver-tutorial/tutorial_general_04.png

[100]: ./media/trisotechdigitalenterpriseserver-tutorial/tutorial_general_100.png

[200]: ./media/trisotechdigitalenterpriseserver-tutorial/tutorial_general_200.png
[201]: ./media/trisotechdigitalenterpriseserver-tutorial/tutorial_general_201.png
[202]: ./media/trisotechdigitalenterpriseserver-tutorial/tutorial_general_202.png
[203]: ./media/trisotechdigitalenterpriseserver-tutorial/tutorial_general_203.png

