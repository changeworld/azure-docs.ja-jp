---
title: 'チュートリアル: Azure Active Directory と StatusPage の統合 | Microsoft Docs'
description: Azure Active Directory と StatusPage の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: f6ee8bb3-df43-4c0d-bf84-89f18deac4b9
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: jeedes
ms.openlocfilehash: e79eb2473760fd1eb7ccc3816ac73cce7c801f3e
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39445366"
---
# <a name="tutorial-azure-active-directory-integration-with-statuspage"></a>チュートリアル: Azure Active Directory と StatusPage の統合

このチュートリアルでは、StatusPage と Azure Active Directory (Azure AD) を統合する方法について説明します。

StatusPage と Azure AD の統合には、次の利点があります。

- StatusPage にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に StatusPage にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

StatusPage と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- StatusPage でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの StatusPage の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-statuspage-from-the-gallery"></a>ギャラリーからの StatusPage の追加
Azure AD への StatusPage の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に StatusPage を追加する必要があります。

**ギャラリーから StatusPage を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[アプリケーション]][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[アプリケーション]][3]

1. 検索ボックスに、「 **StatusPage**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/statuspage-tutorial/tutorial_statuspage_search.png)

1. 結果ウィンドウで **[StatusPage]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/statuspage-tutorial/tutorial_statuspage_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、StatusPage で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する StatusPage ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと StatusPage の関連ユーザーの間で、リンク関係が確立されている必要があります。

StatusPage で、Azure AD の **[ユーザー名]** の値を **[Username]** の値として割り当ててリンク関係を確立します。

StatusPage で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[StatusPage のテスト ユーザーの作成](#creating-a-statuspage-test-user)** - StatusPage で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクします。
1. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、StatusPage アプリケーションでシングル サインオンを構成します。

**StatusPage で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **StatusPage** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![Configure single sign-on][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![Configure single sign-on](./media/statuspage-tutorial/tutorial_statuspage_samlbase.png)

1. **[StatusPage のドメインと URL]** セクションで、次の手順を実行します。

    ![Configure single sign-on](./media/statuspage-tutorial/tutorial_statuspage_url.png)

    a. **[識別子]** ボックスに、次のパターンで URL を入力します。
    | |
    |--|
    | `https://<subdomain>.statuspagestaging.com/` |
    | `https://<subdomain>.statuspage.io/` |

    b. **[応答 URL]** ボックスに、次のパターンを使用して URL を入力します。 
    | |
    |--|
    | `https://<subdomain>.statuspagestaging.com/sso/saml/consume` |
    | `https://<subdomain>.statuspage.io/sso/saml/consume` |

    > [!NOTE]
    > シングル サインオンを構成するために必要なメタデータは、StatusPage サポート チーム ( [SupportTeam@statuspage.io](mailto:SupportTeam@statuspage.io)) に連絡して入手してください。 
    >
    >a. メタデータから発行者の値をコピーし、**[識別子]** ボックスに貼り付けます。
    >
    >b. メタデータから応答 URL をコピーし、**[応答 URL]** ボックスに貼り付けます。

1. **[SAML 署名証明書]** セクションで、**[Certificate (Base64) (証明書 (Base64)) ]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![Configure single sign-on](./media/statuspage-tutorial/tutorial_statuspage_certificate.png) 

1. **[保存]** ボタンをクリックします。

    ![Configure single sign-on](./media/statuspage-tutorial/tutorial_general_400.png)

1. **[StatusPage 構成]** セクションで、**[StatusPage を構成する]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから **SAML シングル サインオン サービスの URL** をコピーします。

    ![Configure single sign-on](./media/statuspage-tutorial/tutorial_statuspage_configure.png) 

1. 別の Web ブラウザー ウィンドウで、管理者として StatusPage 企業サイトにサインオンします。

1. メイン ツール バーで、 **[Manage Account]** をクリックします。
   
    ![Configure single sign-on](./media/statuspage-tutorial/tutorial_statuspage_06.png) 

1. **[Single Sign-on]** タブをクリックします。 
   
    ![Configure single sign-on](./media/statuspage-tutorial/tutorial_statuspage_07.png) 

1. [SSO Setup] ページで、次の手順に従います。
   
    ![Configure single sign-on](./media/statuspage-tutorial/tutorial_statuspage_08.png) 

    ![Configure single sign-on](./media/statuspage-tutorial/tutorial_statuspage_09.png) 
 
    a. **[SSO ターゲット URL]** ボックスに、Azure Portal からコピーした **SAML シングル サインオン サービス URL** の値を貼り付けます。

    b. ダウンロードした証明書をメモ帳で開き、その内容をコピーして、**[Certificate]** ボックスに貼り付けます。 

    c. **[構成の保存]** をクリックします。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関するページを参照してください。
> 

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/statuspage-tutorial/create_aaduser_01.png) 

1. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/statuspage-tutorial/create_aaduser_02.png) 

1. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/statuspage-tutorial/create_aaduser_03.png) 

1. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/statuspage-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **Create** をクリックしてください。
 
### <a name="creating-a-statuspage-test-user"></a>StatusPage のテスト ユーザーの作成

このセクションの目的は、StatusPage で Britta Simon というユーザーを作成することです。

StatusPage では、ジャストインタイム プロビジョニングがサポートされています。 この機能は、「 [Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)」で既に有効にしています。

**StatusPage で Britta Simon というユーザーを作成するには、次の手順に従います。**

1. StatusPage 企業サイトに管理者としてサインオンします。

1. 上部のメニューで **[Manage Account]** をクリックします。

    ![Configure single sign-on](./media/statuspage-tutorial/tutorial_statuspage_06.png)

1. **[チーム メンバー]** タブをクリックします。 
   
    ![Azure AD のテスト ユーザーの作成](./media/statuspage-tutorial/tutorial_statuspage_10.png) 

1. **[チーム メンバーの追加]** をクリックします。 
   
    ![Azure AD のテスト ユーザーの作成](./media/statuspage-tutorial/tutorial_statuspage_11.png) 

1. プロビジョニングする有効なユーザーの**電子メール アドレス**、**名**、**姓**を、対応するテキスト ボックスに入力します。 
   
    ![Azure AD のテスト ユーザーの作成](./media/statuspage-tutorial/tutorial_statuspage_12.png) 

1. **[Role]** で **[Client Administrator]** を選択します。

1. **[アカウントの作成]** をクリックします。

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に StatusPage へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Britta Simon を StatusPage に割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[StatusPage]** を選択します。

    ![Configure single sign-on](./media/statuspage-tutorial/tutorial_statuspage_app.png) 

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションの目的は、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストすることです。

アクセス パネルで [StatusPage] タイルをクリックすると、自動的に StatusPage アプリケーションにサインオンします。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/statuspage-tutorial/tutorial_general_01.png
[2]: ./media/statuspage-tutorial/tutorial_general_02.png
[3]: ./media/statuspage-tutorial/tutorial_general_03.png
[4]: ./media/statuspage-tutorial/tutorial_general_04.png

[100]: ./media/statuspage-tutorial/tutorial_general_100.png

[200]: ./media/statuspage-tutorial/tutorial_general_200.png
[201]: ./media/statuspage-tutorial/tutorial_general_201.png
[202]: ./media/statuspage-tutorial/tutorial_general_202.png
[203]: ./media/statuspage-tutorial/tutorial_general_203.png

