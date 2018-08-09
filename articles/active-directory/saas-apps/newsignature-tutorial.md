---
title: 'チュートリアル: Azure Active Directory と Cloud Management Portal for Microsoft Azure の統合 | Microsoft Docs'
description: Azure Active Directory と Cloud Management Portal for Microsoft Azure の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 4ea9f47c-25ca-42b0-a878-9e7aa6f34973
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: jeedes
ms.openlocfilehash: 0cd14a308758701e207e0b1ee6d3591c4b0347bd
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39427637"
---
# <a name="tutorial-azure-active-directory-integration-with-cloud-management-portal-for-microsoft-azure"></a>チュートリアル: Azure Active Directory と Cloud Management Portal for Microsoft Azure の統合

このチュートリアルでは、Cloud Management Portal for Microsoft Azure と Azure Active Directory (Azure AD) を統合する方法を説明します。

Cloud Management Portal for Microsoft Azure と Azure AD の統合には、次の利点があります。

- Cloud Management Portal for Microsoft Azure にアクセスできるユーザーを Azure AD で制御できます
- ユーザーが自分の Azure AD アカウントで自動的に Cloud Management Portal for Microsoft Azure にサインオンできるようにします (シングル サインオン)。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Cloud Management Portal for Microsoft Azure と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Cloud Management Portal for Microsoft Azure でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Cloud Management Portal for Microsoft Azure の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-cloud-management-portal-for-microsoft-azure-from-the-gallery"></a>ギャラリーからの Cloud Management Portal for Microsoft Azure の追加
Azure AD への Cloud Management Portal for Microsoft Azure の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Cloud Management Portal for Microsoft Azure を追加する必要があります。

**ギャラリーから Cloud Management Portal for Microsoft Azure を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[アプリケーション]][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[アプリケーション]][3]

1. 検索ボックスに、「 **Cloud Management Portal for Microsoft Azure**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/newsignature-tutorial/tutorial_newsignature_search.png)

1. 結果ウィンドウで **Cloud Management Portal for Microsoft Azure** を選択し、**[追加]** ボタンをクリックしてアプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/newsignature-tutorial/tutorial_newsignature_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーを使用して、Cloud Management Portal for Microsoft Azure との Azure AD シングル サインオンを構成してテストする方法を説明します。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Cloud Management Portal for Microsoft Azure ユーザーが Azure AD で認識される必要があります。 言い換えると、Azure AD ユーザーと Cloud Management Portal for Microsoft Azure の関連ユーザーの間で、リンク関係が確立されている必要があります。

Cloud Management Portal for Microsoft Azure で、Azure AD の **[ユーザー名]** の値を **[Username]** の値として割り当ててリンク関係を確立します。

Cloud Management Portal for Microsoft Azure での Azure AD シングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[Cloud Management Portal for Microsoft Azure テスト ユーザーの作成](#creating-a-cloud-management-portal-for-microsoft-azure-test-user)** - Azure AD の Britta Simon にリンクさせるために、対応するユーザーを Cloud Management Portal for Microsoft Azure で作成します。
1. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、Cloud Management Portal for Microsoft Azure アプリケーションでシングル サインオンを構成します。

**Cloud Management Portal for Microsoft Azure との Azure AD シングル サインオンを構成するには、次の手順を実行します。**

1. Azure Portal の **Cloud Management Portal for Microsoft Azure** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![Configure single sign-on][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![Configure single sign-on](./media/newsignature-tutorial/tutorial_newsignature_samlbase.png)

1. **[Cloud Management Portal for Microsoft Azure のドメインと URL]** セクションで、次の手順を実行します。

    ![Configure single sign-on](./media/newsignature-tutorial/tutorial_newsignature_url.png)

    a. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。 
    
    | |
    |--|
    | `https://portal.newsignature.com/<instancename>` |   
    | `https://portal.igcm.com/<instancename>` |
    
    b. **[識別子]** ボックスに、次のパターンで URL を入力します。 
    
    | |
    |--|
    | `https://<subdomain>.igcm.com` |
    | `https://<subdomain>.newsignature.com` |

    c. **[応答 URL]** ボックスに、次の形式で URL を入力します。 
    
    | |
    |--|
    | `https://<subdomain>.igcm.com/<instancename>` |
    | `https://<subdomain>.newsignature.com` |
    | `https://<subdomain>.newsignature.com/<instancename>` |

    > [!NOTE] 
    > これらは実際の値ではありません。 実際のサインオン URL、識別子、および応答 URL で値を更新します。 [Cloud Management Portal for Microsoft Azure クライアント サポート チーム](mailto:jczernuszka@newsignature.com)に問い合わせて値を取得します。 
 
1. **[SAML 署名証明書]** セクションで、**[証明書 (Base64)]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![Configure single sign-on](./media/newsignature-tutorial/tutorial_newsignature_certificate.png) 

1. **[保存]** ボタンをクリックします。

    ![Configure single sign-on](./media/newsignature-tutorial/tutorial_general_400.png)

1. **[Cloud Management Portal for Microsoft Azure 構成]** セクションで **[Cloud Management Portal for Microsoft Azure の構成]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから、**サインアウト URL、SAML エンティティ ID、SAML シングル サインオン サービス URL** をコピーします。

    ![Configure single sign-on](./media/newsignature-tutorial/tutorial_newsignature_configure.png) 

1. **Cloud Management Portal for Microsoft Azure** 側でシングル サインオンを構成するには、ダウンロードした**証明書**、**サインアウト URL**、**SAML シングル サインオン サービス URL**、**SAML エンティティ ID** を [Cloud Management Portal for Microsoft Azure サポート チーム](mailto:jczernuszka@newsignature.com)に送信する必要があります。 サポート チームはこれを設定して、SAML SSO 接続が両方の側で正しく設定されるようにします。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関するページを参照してください。

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/newsignature-tutorial/create_aaduser_01.png) 

1. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/newsignature-tutorial/create_aaduser_02.png) 

1. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/newsignature-tutorial/create_aaduser_03.png) 

1. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/newsignature-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **Create** をクリックしてください。
 
### <a name="creating-a-cloud-management-portal-for-microsoft-azure-test-user"></a>Cloud Management Portal for Microsoft Azure テスト ユーザーの作成

このセクションの目的は、Cloud Management Portal for Microsoft Azure で Britta Simon というユーザーを作成することです。 [Cloud Management Portal for Microsoft Azure サポート チーム](mailto:jczernuszka@newsignature.com)と協力して、Cloud Management Portal for Microsoft Azure システムにユーザーを追加してください。


### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Cloud Management Portal for Microsoft Azure へのアクセスを許可することで、このユーザーが Azure のシングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Cloud Management Portal for Microsoft Azure に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **Cloud Management Portal for Microsoft Azure**を選択します。

    ![Configure single sign-on](./media/newsignature-tutorial/tutorial_newsignature_app.png) 

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションの目的は、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストすることです。
アクセス パネルで Cloud Management Portal for Microsoft Azure のタイルをクリックすると、自動的に Cloud Management Portal for Microsoft Azure アプリケーションにサインオンします。

アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/newsignature-tutorial/tutorial_general_01.png
[2]: ./media/newsignature-tutorial/tutorial_general_02.png
[3]: ./media/newsignature-tutorial/tutorial_general_03.png
[4]: ./media/newsignature-tutorial/tutorial_general_04.png

[100]: ./media/newsignature-tutorial/tutorial_general_100.png

[200]: ./media/newsignature-tutorial/tutorial_general_200.png
[201]: ./media/newsignature-tutorial/tutorial_general_201.png
[202]: ./media/newsignature-tutorial/tutorial_general_202.png
[203]: ./media/newsignature-tutorial/tutorial_general_203.png

