---
title: 'チュートリアル: Azure Active Directory と OneTrust Privacy Management Software の統合 | Microsoft Docs'
description: Azure Active Directory と OneTrust Privacy Management Software の間のシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 71c2b6d0-3d28-4130-a2c8-1e72ab3d5814
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: jeedes
ms.openlocfilehash: f8e06a4578d2f11331b87fdfb493e2bba4edb8cf
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39421692"
---
# <a name="tutorial-azure-active-directory-integration-with-onetrust-privacy-management-software"></a>チュートリアル: Azure Active Directory と OneTrust Privacy Management Software の統合

このチュートリアルでは、OneTrust Privacy Management Software と Azure Active Directory (Azure AD) を統合する方法について説明します。

OneTrust Privacy Management Software と Azure AD の統合には、次の利点があります。

- OneTrust Privacy Management Software にアクセスできるユーザーを Azure AD で制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に OneTrust Privacy Management Software にサインオン (シングル サインオン) できるように、設定が可能です。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

OneTrust Privacy Management Software と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- OneTrust Privacy Management Software でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの OneTrust Privacy Management Software の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-onetrust-privacy-management-software-from-the-gallery"></a>ギャラリーからの OneTrust Privacy Management Software の追加
Azure AD への OneTrust Privacy Management Software の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に OneTrust Privacy Management Software を追加する必要があります。

**ギャラリーから OneTrust Privacy Management Software を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

1. 検索ボックスに「**OneTrust Privacy Management Software**」と入力し、結果ウィンドウで **OneTrust Privacy Management Software** を選び、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果一覧の OneTrust Privacy Management Software](./media/onetrust-tutorial/tutorial_onetrust_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、OneTrust Privacy Management Software で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する OneTrust Privacy Management Software ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと OneTrust Privacy Management Software の関連ユーザーの間で、リンク関係が確立されている必要があります。

OneTrust Privacy Management Software で、Azure AD の **[ユーザー名]** の値を **[Username]** の値として割り当ててリンク関係を確立します。

OneTrust Privacy Management Software で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[OneTrust Privacy Management Software テスト ユーザーの作成](#create-a-onetrust-privacy-management-software-test-user)** - OneTrust Privacy Management Software で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Microsoft Portal で Azure AD のシングル サインオンを有効にして、OneTrust Privacy Management Software アプリケーションでシングル サインオンを構成します。

**OneTrust Privacy Management Software で Azure AD シングル サインオンを構成するには、次の手順を実行します。**

1. Azure Portal の **OneTrust Privacy Management Software** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/onetrust-tutorial/tutorial_onetrust_samlbase.png)

1. **[OneTrust Privacy Management Software のドメインと URL]** セクションで、**IDP** 開始モードでアプリケーションを構成する場合は、次の手順を実行します。

    ![[OneTrust Privacy Management Software のドメインと URL] のシングル サインオン情報](./media/onetrust-tutorial/tutorial_onetrust_url.png)

    a. **[識別子]** ボックスに次の URL を入力します。`https://www.onetrust.com/saml2`

    b. **[応答 URL]** ボックスに、`https://<subdomain>.onetrust.com/auth/consumerservice` のパターンを使用して URL を入力します。

1. アプリケーションを **SP** 開始モードで構成する場合は、**[詳細な URL 設定の表示]** チェックボックスをオンにして次の手順を実行します。

    ![[OneTrust Privacy Management Software のドメインと URL] のシングル サインオン情報](./media/onetrust-tutorial/tutorial_onetrust_url1.png)

    **[サインオン URL]** ボックスに、`https://<subdomain>.onetrust.com/auth/login` のパターンを使用して URL を入力します。
     
    > [!NOTE] 
    > これらは実際の値ではありません。 これらの値を、実際の応答 URL およびサインオン URL で更新してください。 これらの値を取得するには、[OneTrust Privacy Management Software クライアント サポート チーム](mailto:support@onetrust.com)に問い合わせてください。 

1. **[SAML 署名証明書]** セクションで、**[Metadata XML (メタデータ XML)]** をクリックし、コンピューターにメタデータ ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/onetrust-tutorial/tutorial_onetrust_certificate.png) 

1. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/onetrust-tutorial/tutorial_general_400.png)

1. **OneTrust Privacy Management Software** 側でシングル サインオンを構成するには、ダウンロードした**メタデータ XML** を [OneTrust Privacy Management Software サポート チーム](mailto:support@onetrust.com)に送信する必要があります。 サポート チームはこれを設定して、SAML SSO 接続が両方の側で正しく設定されるようにします。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関するページを参照してください。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/onetrust-tutorial/create_aaduser_01.png)

1. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/onetrust-tutorial/create_aaduser_02.png)

1. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/onetrust-tutorial/create_aaduser_03.png)

1. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/onetrust-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。
 
### <a name="create-a-onetrust-privacy-management-software-test-user"></a>OneTrust Privacy Management Software テスト ユーザーの作成

このセクションの目的は、OneTrust Privacy Management Software で Britta Simon というユーザーを作成することです。 OneTrust Privacy Management Software では、ジャスト イン タイム プロビジョニングがサポートされています。この設定は、既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 ユーザーが存在しない場合は、OneTrust Privacy Management Software へのアクセスを試みたときに、新しいユーザーが自動的に作成されます。

>[!Note]
>ユーザーを手動で作成する必要がある場合は、[OneTrust Privacy Management Software サポート チーム](mailto:support@onetrust.com)にお問い合わせください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に OneTrust Privacy Management Software へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**Britta Simon を OneTrust Privacy Management Software に割り当てるには、次の手順を実行します。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[OneTrust Privacy Management Software]** を選択します。

    ![アプリケーションの一覧の [OneTrust Privacy Management Software] リンク](./media/onetrust-tutorial/tutorial_onetrust_app.png)  

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [OneTrust Privacy Management Software] タイルをクリックすると、OneTrust Privacy Management Software アプリケーションに自動的にサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/onetrust-tutorial/tutorial_general_01.png
[2]: ./media/onetrust-tutorial/tutorial_general_02.png
[3]: ./media/onetrust-tutorial/tutorial_general_03.png
[4]: ./media/onetrust-tutorial/tutorial_general_04.png

[100]: ./media/onetrust-tutorial/tutorial_general_100.png

[200]: ./media/onetrust-tutorial/tutorial_general_200.png
[201]: ./media/onetrust-tutorial/tutorial_general_201.png
[202]: ./media/onetrust-tutorial/tutorial_general_202.png
[203]: ./media/onetrust-tutorial/tutorial_general_203.png

