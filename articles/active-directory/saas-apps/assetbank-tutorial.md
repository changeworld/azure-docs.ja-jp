---
title: 'チュートリアル: Azure Active Directory と Asset Bank の統合 | Microsoft Docs'
description: Azure Active Directory と Asset Bank の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 3006ad6e-8831-41cd-94aa-7e7ae770ce7b
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/09/2017
ms.author: jeedes
ms.openlocfilehash: 9127ef7605227293a10b21c2111396e8c7939b82
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39437279"
---
# <a name="tutorial-azure-active-directory-integration-with-asset-bank"></a>チュートリアル: Azure Active Directory と Asset Bank の統合

このチュートリアルでは、Asset Bank と Azure Active Directory (Azure AD) を統合する方法について説明します。

Asset Bank と Azure AD の統合には、次の利点があります。

- Asset Bank にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Asset Bank にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Asset Bank と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Asset Bank でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーから Asset Bank を追加する
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-asset-bank-from-the-gallery"></a>ギャラリーから Asset Bank を追加する
Azure AD への Asset Bank の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Asset Bank を追加する必要があります。

**ギャラリーから Asset Bank を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[アプリケーション]][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[アプリケーション]][3]

1. [検索] ボックスに、「 **Asset Bank**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/assetbank-tutorial/tutorial_assetbank_search.png)

1. 結果ウィンドウで **[Asset Bank]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/assetbank-tutorial/tutorial_assetbank_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" という名前のテスト ユーザーに基づいて、Asset Bank で Azure AD のシングル サインオンを構成およびテストします。

シングル サインオンを機能させるには、Azure AD が Azure AD のユーザーに対応する Asset Bank のユーザーを認識している必要があります。 言い換えると、Azure AD ユーザーと Asset Bank の関連ユーザーの間で、リンク関係が確立されている必要があります。

Asset Bank で、Azure AD での **[ユーザー名]** の値を **[ユーザー名]** の値として割り当てることによってリンク関係を確立します。

Asset Bank で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[Asset Bank テスト ユーザーの作成](#creating-an-asset-bank-test-user)** - Azure AD でのユーザーにリンクされた、Asset Bank での Britta Simon の対応するユーザーを作成します。
1. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にし、Asset Bank アプリケーションでシングル サインオンを構成します。

**Asset Bank で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **[Asset Bank]** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![Configure single sign-on][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![Configure single sign-on](./media/assetbank-tutorial/tutorial_assetbank_samlbase.png)

1. **[Asset Bank Domain and URLs] \(Asset Bank のドメインと URL)** セクションで、次の手順を実行します。

    ![Configure single sign-on](./media/assetbank-tutorial/tutorial_assetbank_url.png)

    a. **[サインオン URL]** ボックスに、`https://<companyname>.assetbank-server.com` のパターンを使用して URL を入力します。

    b. **[識別子]** ボックスに、`https://<companyname>.assetbank-server.com/shibboleth` の形式で URL を入力します。

    > [!NOTE] 
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新してください。 これらの値を取得するには、[Asset Bank クライアント サポート チーム](mailto:support@assetbank.co.uk)に問い合わせてください。 
 
1. **[SAML 署名証明書]** セクションで、**[Metadata XML (メタデータ XML)]** をクリックし、コンピューターにメタデータ ファイルを保存します。

    ![Configure single sign-on](./media/assetbank-tutorial/tutorial_assetbank_certificate.png) 

1. **[保存]** ボタンをクリックします。

    ![Configure single sign-on](./media/assetbank-tutorial/tutorial_general_400.png)

1. **Asset Bank** 側にシングル サインオンを構成するには、ダウンロードされた**メタデータ XML** を [Asset Bank サポート チーム](mailto:support@assetbank.co.uk)に送信する必要があります。 


> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関するページを参照してください。
 
### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/assetbank-tutorial/create_aaduser_01.png) 

1. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/assetbank-tutorial/create_aaduser_02.png) 

1. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/assetbank-tutorial/create_aaduser_03.png) 

1. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/assetbank-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **Create** をクリックしてください。
 
### <a name="creating-an-asset-bank-test-user"></a>Asset Bank テスト ユーザーの作成

このセクションの目的は、Asset Bank で Britta Simon というユーザーを作成することです。 Asset Bank では、Just-In-Time プロビジョニングがサポートされています。この設定は、既定で有効になっています。

このセクションでは、ユーザー側で必要な操作はありません。 Asset Bank にアクセスしようとすると、ユーザーがまだ存在しない場合は新しいユーザーが作成されます。 

>[!NOTE]
>ユーザーを手動で作成する必要がある場合は、[Asset Bank サポート チーム](mailto:support@assetbank.co.uk)に問い合わせる必要があります。

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Asset Bank へのアクセス権を付与することによって、Britta Simon が Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Asset Bank に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[Asset Bank]** を選択します。

    ![Configure single sign-on](./media/assetbank-tutorial/tutorial_assetbank_app.png) 

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションの目的は、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストすることです。

アクセス パネルで [Asset Bank] タイルをクリックすると、自動的に Asset Bank アプリケーションにサインオンします。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/assetbank-tutorial/tutorial_general_01.png
[2]: ./media/assetbank-tutorial/tutorial_general_02.png
[3]: ./media/assetbank-tutorial/tutorial_general_03.png
[4]: ./media/assetbank-tutorial/tutorial_general_04.png

[100]: ./media/assetbank-tutorial/tutorial_general_100.png

[200]: ./media/assetbank-tutorial/tutorial_general_200.png
[201]: ./media/assetbank-tutorial/tutorial_general_201.png
[202]: ./media/assetbank-tutorial/tutorial_general_202.png
[203]: ./media/assetbank-tutorial/tutorial_general_203.png

