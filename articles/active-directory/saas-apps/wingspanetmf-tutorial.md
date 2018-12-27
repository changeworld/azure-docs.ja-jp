---
title: 'チュートリアル: Azure Active Directory と Wingspan eTMF の統合 | Microsoft Docs'
description: Azure Active Directory と Wingspan eTMF の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: ace320d3-521c-449c-992f-feabe7538de7
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.author: jeedes
ms.openlocfilehash: d27d2c6a6c6bae2ebd13f78da308f32275993ec5
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39434066"
---
# <a name="tutorial-azure-active-directory-integration-with-wingspan-etmf"></a>チュートリアル: Azure Active Directory と Wingspan eTMF の統合

このチュートリアルでは、Wingspan eTMF と Azure Active Directory (Azure AD) を統合する方法について説明します。

Wingspan eTMF と Azure AD の統合には、次の利点があります。

- Wingspan eTMF にアクセスする Azure AD ユーザーを制御できます
- ユーザーが自分の Azure AD アカウントで自動的に Wingspan eTMF にサインオン (シングル サインオン) できるようにします
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Azure AD と Wingspan eTMF の統合を構成するには、次の項目が必要です。

- Azure AD サブスクリプション
- Wingspan eTMF でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Wingspan eTMF の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-wingspan-etmf-from-the-gallery"></a>ギャラリーからの Wingspan eTMF の追加
Azure AD への Wingspan eTMF の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Wingspan eTMF を追加する必要があります。

**ギャラリーから Wingspan eTMF を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[アプリケーション]][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[アプリケーション]][3]

1. 検索ボックスに、「**Wingspan eTMF**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/wingspanetmf-tutorial/tutorial_wingspanetmf_search.png)

1. 結果パネルで、**[Wingspan eTMF]** を選択し、**[追加]** ボタンをクリックしてアプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/wingspanetmf-tutorial/tutorial_wingspanetmf_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" という名前のテスト ユーザーに基づいて、Wingspan eTMF で Azure AD シングル サインオンを構成およびテストします。

シングル サインオンを機能させるには、Azure AD が、Azure AD のユーザーに対応する Wingspan eTMF のユーザーを認識している必要があります。 つまり、Azure AD ユーザーと Wingspan eTMF の関連するユーザーの間のリンク関係が確立されている必要があります。

このリンク関係は、Azure AD での **[ユーザー名]** の値を Wingspan eTMF での **[ユーザー名]** の値として割り当てることによって確立されます。

Wingspan eTMF で Azure AD シングル サインオンを構成およびテストするには、次の構成要素を完了する必要があります。

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[Wingspan eTMF テスト ユーザーの作成](#creating-a-wingspan-etmf-test-user)** - Azure AD のユーザー表現にリンクされた Wingspan eTMF での Britta Simon の対応するユーザーを作成します。
1. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD シングル サインオンを有効にし、Wingspan eTMF アプリケーションでシングル サインオンを構成します。

**Wingspan eTMF で Azure AD シングル サインオンを構成するには、次の手順を実行します。**

1. Azure Portal の **Wingspan eTMF** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![Configure single sign-on][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![Configure single sign-on](./media/wingspanetmf-tutorial/tutorial_wingspanetmf_samlbase.png)

1. **[Wingspan eTMF Domain and URLs] \(Wingspan eTMF のドメインと URL)** セクションで、次の手順を実行します。

    ![Configure single sign-on](./media/wingspanetmf-tutorial/tutorial_wingspanetmf_url11.png)

    a. **[サインオン URL]** ボックスに、`https://<customer name>.<instance name>.mywingspan.com/saml` のパターンを使用して URL を入力します。

    b. **[識別子]** ボックスに、`http://saml.<instance name>.wingspan.com/shibboleth` の形式で URL を入力します。

    c. **[応答 URL]** ボックスに、`https://<customer name>.<instance name>.mywingspan.com/` のパターンを使用して URL を入力します。
     
    > [!NOTE] 
    > これらは実際の値ではありません。 これらの値を、実際の顧客名とインスタンス名を含む実際のサインオン URL、識別子、および応答 URL で更新します。 これらの値を取得するには、[Wingspan eTMF クライアント サポート チーム](http://www.wingspan.com/contact-us/)に問い合わせてください。 

1. **[SAML 署名証明書]** セクションで、**[Metadata XML (メタデータ XML)]** をクリックし、コンピューターにメタデータ ファイルを保存します。

    ![Configure single sign-on](./media/wingspanetmf-tutorial/tutorial_wingspanetmf_certificate.png) 

1. **[保存]** ボタンをクリックします。

    ![Configure single sign-on](./media/wingspanetmf-tutorial/tutorial_general_400.png)

1. **[Wingspan eTMF]** 側でシングル サインオンを構成するには、ダウンロードされた**メタデータ XML** を [Wingspan eTMF サポート](http://www.wingspan.com/contact-us/)に送信する必要があります。 サポート チームはこれをセットアップして、SAML SSO 接続が両方の側で正しく設定されるようにします。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関するページを参照してください。
 

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/wingspanetmf-tutorial/create_aaduser_01.png) 

1. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/wingspanetmf-tutorial/create_aaduser_02.png) 

1. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/wingspanetmf-tutorial/create_aaduser_03.png) 

1. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/wingspanetmf-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **Create** をクリックしてください。
 
### <a name="creating-a-wingspan-etmf-test-user"></a>Wingspan eTMF テスト ユーザーの作成

このセクションでは、Wingspan eTMF の Britta Simon という名前のユーザーを作成します。 Wingspan eTMF アプリケーションでユーザーを追加するには、[Wingspan eTMF サポート](http://www.wingspan.com/contact-us/)と協力します。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Wingspan eTMF へのアクセス権を付与することによって、Britta Simon が Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Britta Simon を Wingspan eTMF に割り当てるには、次の手順を実行します。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[Wingspan eTMF]** を選択します。

    ![Configure single sign-on](./media/wingspanetmf-tutorial/tutorial_wingspanetmf_app.png) 

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。 

アクセス パネルの [Wingspan eTMF] タイルをクリックすると、[Organization sign on] \(組織のサイン オン) ページにリダイレクトされます。 ログインが成功すると、Wingspan eTMF アプリケーションにサイン オンします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/wingspanetmf-tutorial/tutorial_general_01.png
[2]: ./media/wingspanetmf-tutorial/tutorial_general_02.png
[3]: ./media/wingspanetmf-tutorial/tutorial_general_03.png
[4]: ./media/wingspanetmf-tutorial/tutorial_general_04.png

[100]: ./media/wingspanetmf-tutorial/tutorial_general_100.png

[200]: ./media/wingspanetmf-tutorial/tutorial_general_200.png
[201]: ./media/wingspanetmf-tutorial/tutorial_general_201.png
[202]: ./media/wingspanetmf-tutorial/tutorial_general_202.png
[203]: ./media/wingspanetmf-tutorial/tutorial_general_203.png

