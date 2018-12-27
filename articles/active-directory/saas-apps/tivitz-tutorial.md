---
title: 'チュートリアル: Azure Active Directory と TiViTz の統合 | Microsoft Docs'
description: Azure Active Directory と TiViTz の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: b97ed88f-7888-4185-be22-41d1f62cbbf1
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: jeedes
ms.openlocfilehash: 9b997b198c526aec5dfc64c33969008988c1abff
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39422462"
---
# <a name="tutorial-azure-active-directory-integration-with-tivitz"></a>チュートリアル: Azure Active Directory と TiViTz の統合

このチュートリアルでは、TiViTz と Azure Active Directory (Azure AD) を統合する方法について説明します。

TiViTz と Azure AD の統合には、次の利点があります。

- TiViTz にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に TiViTz にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Azure AD と TiViTz の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- TiViTz でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの TiViTz の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-tivitz-from-the-gallery"></a>ギャラリーからの TiViTz の追加
Azure AD への TiViTz の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に TiViTz を追加する必要があります。

**ギャラリーから TiViTz を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[アプリケーション]][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[アプリケーション]][3]

1. 検索ボックスに、「**TiViTz**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/tivitz-tutorial/tutorial_tivitz_search.png)

1. 結果ウィンドウで **[TiViTz]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/tivitz-tutorial/tutorial_tivitz_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、TiViTz で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する TiViTz ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと TiViTz の関連ユーザーの間で、リンク関係が確立されている必要があります。

TiViTz で、Azure AD での **[ユーザー名]** の値を **[Username]\(ユーザー名\)** の値として割り当ててリンク関係を確立します。

TiViTz で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[TiViTz のテスト ユーザーの作成](#creating-a-tivitz-test-user)** - TiViTz で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、TiViTz アプリケーションでシングル サインオンを構成します。

**TiViTz で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **TiViTz** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![Configure single sign-on][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![Configure single sign-on](./media/tivitz-tutorial/tutorial_tivitz_samlbase.png)

1. **[TiViTz のドメインと URL]** セクションで、次の手順を実行します。

    ![Configure single sign-on](./media/tivitz-tutorial/tutorial_tivitz_url.png)

    a. **[サインオン URL]** ボックスに、`https://<companyname>.o365.tivitz.com/` のパターンを使用して URL を入力します。

    b. **[識別子]** ボックスに、`https://<companyname>.o365.tivitz.com/` の形式で URL を入力します。

    > [!NOTE] 
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新してください。 この値を取得するには、[TiViTz クライアント サポート チーム](mailto:info@tivitz.com)にお問い合わせください。 

1. **[SAML 署名証明書]** セクションで、**[Metadata XML (メタデータ XML)]** をクリックし、コンピューターにメタデータ ファイルを保存します。

    ![Configure single sign-on](./media/tivitz-tutorial/tutorial_tivitz_certificate.png) 

1. **[保存]** ボタンをクリックします。

    ![Configure single sign-on](./media/tivitz-tutorial/tutorial_general_400.png)

1. **TiViTz** 側にシングル サインオンを構成するには、ダウンロードした**メタデータ XML** を [TiViTz サポート チーム](mailto:info@tivitz.com)に送信する必要があります。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関するページを参照してください。
> 

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/tivitz-tutorial/create_aaduser_01.png) 

1. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/tivitz-tutorial/create_aaduser_02.png) 

1. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/tivitz-tutorial/create_aaduser_03.png) 

1. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/tivitz-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **Create** をクリックしてください。
 
### <a name="creating-a-tivitz-test-user"></a>TiViTz のテスト ユーザーの作成

このセクションの目的は、TiViTz で Britta Simon というユーザーを作成することです。 TiViTz では、Just-In-Time プロビジョニングがサポートされています。この設定は、既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 存在しない TiViTz ユーザーにアクセスしようとすると、新しいユーザーが自動的に作成されます。

>[!NOTE]
>ユーザーを手動で作成する必要がある場合は、[TiViTz のサポート チーム](mailto:info@tivitz.com)にお問い合わせください。

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に TiViTz へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Britta Simon を TiViTz に割り当てるには、次の手順を実行します。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[TiViTz]** を選択します。

    ![Configure single sign-on](./media/tivitz-tutorial/tutorial_tivitz_app.png) 

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [TiViTz] タイルをクリックすると、TiViTz アプリケーションに自動的にサインオンします。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/tivitz-tutorial/tutorial_general_01.png
[2]: ./media/tivitz-tutorial/tutorial_general_02.png
[3]: ./media/tivitz-tutorial/tutorial_general_03.png
[4]: ./media/tivitz-tutorial/tutorial_general_04.png

[100]: ./media/tivitz-tutorial/tutorial_general_100.png

[200]: ./media/tivitz-tutorial/tutorial_general_200.png
[201]: ./media/tivitz-tutorial/tutorial_general_201.png
[202]: ./media/tivitz-tutorial/tutorial_general_202.png
[203]: ./media/tivitz-tutorial/tutorial_general_203.png

