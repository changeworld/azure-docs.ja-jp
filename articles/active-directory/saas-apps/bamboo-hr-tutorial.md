---
title: 'チュートリアル: Azure Active Directory と BambooHR の統合 | Microsoft Docs'
description: Azure Active Directory と BambooHR の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: f826b5d2-9c64-47df-bbbf-0adf9eb0fa71
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/18/2018
ms.author: jeedes
ms.openlocfilehash: dc6664321588d383b4656199c3e8ea79159ca850
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39437680"
---
# <a name="tutorial-azure-active-directory-integration-with-bamboohr"></a>チュートリアル: Azure Active Directory と BambooHR の統合

このチュートリアルでは、BambooHR と Azure Active Directory (Azure AD) を統合する方法について説明します。

BambooHR と Azure AD の統合には、次の利点があります。

- Azure AD で誰が BambooHR にアクセスできるかを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に BambooHR にシングル サインオン (SSO) できるようにします
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Azure AD と BambooHR の統合を構成するには、次の項目が必要です。

- Azure AD サブスクリプション
- BambooHR SSO が有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従います。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月間無料の試用版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 

このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの BambooHR の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="add-bamboohr-from-the-gallery"></a>ギャラリーからの BambooHR の追加
BambooHR の Azure AD への統合を構成するには、次の手順に従って、BambooHR をギャラリーから管理対象 SaaS アプリの一覧に追加します。

1. [Azure Portal](https://portal.azure.com) の左側のウィンドウで、**[Azure Active Directory]** を選択します。 

    ![Azure Active Directory のボタン][1]

1. **[エンタープライズ アプリケーション]** > **[すべてのアプリケーション]** の順に選択します。

    ![[エンタープライズ アプリケーション] ウィンドウ][2]
    
1. アプリケーションを追加するには、**[新しいアプリケーション]** を選択します。

    ![[新しいアプリケーション] ボタン][3]

1. 検索ボックスに「**BambooHR**」と入力します。 結果リストで **[BambooHR]** を選択し、**[追加]** を選択します。

    ![結果一覧の BambooHR](./media/bamboo-hr-tutorial/tutorial_bamboohr_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーを使用して、BambooHR で Azure AD SSO を構成し、テストします。

SSO を機能させるには、Azure AD ユーザーに対応する BambooHR ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと BambooHR の関連ユーザーとの間で、リンク関係を確立する必要があるということです。

BambooHR のリンク関係を確立するには、Azure AD の**ユーザー名**の値を BambooHR の**ユーザー名**の値として割り当てます。

BambooHR で Azure AD SSO を構成してテストするには、次からの 5 つのセクションで構成要素を完了します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、次の手順で Azure Portal で Azure AD SSO を有効にし、BambooHR アプリケーションに SSO を構成します。

1. Azure Portal の **[BambooHR]** アプリケーション統合ページで、**[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク][4]

1. **[シングル サインオン]** ウィンドウの **[モード]** ボックスの一覧から **[SAML ベースのサインオン]** を選択します。
 
    ![[シングル サインオン] ウィンドウ](./media/bamboo-hr-tutorial/tutorial_bamboohr_samlbase.png)

1. **[BambooHR のドメインと URL]** で、次の手順を実行します。

    ![[BambooHR のドメインと URL] セクション](./media/bamboo-hr-tutorial/tutorial_bamboohr_url.png)

    a. **[サインオン URL]** ボックスに、`https://<company>.bamboohr.com` 形式で URL を入力します。

    b. **[識別子]** ボックスに「`BambooHR-SAML`」と入力します。

    > [!NOTE] 
    > **サインオン URL** は実際の値ではありません。 実際のサインオン URL で更新してください。 値を取得するには、[BambooHR クライアント サポート チーム](https://www.bamboohr.com/contact.php)に問い合わせてください。 
 
1. **[SAML 署名証明書]** で、**[証明書 (Base64)]** を選択し、コンピューターに証明書ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/bamboo-hr-tutorial/tutorial_bamboohr_certificate.png) 

1. **[保存]** を選択します。

    ![[保存] ボタン](./media/bamboo-hr-tutorial/tutorial_general_400.png)

1. **[BambooHR Configuration]\(BambooHR 構成\)** で、**[Configure BambooHR]\(BambooHR の構成\)** を選択して **[Configure sign-on]\(サインオンの構成\)** ウィンドウを開きます。 **[クイック リファレンス]** セクションで **SAML シングル サインオン サービス URL** をコピーします。この値が後で必要になります。

    ![BambooHR の構成](./media/bamboo-hr-tutorial/tutorial_bamboohr_configure.png) 

1. 新しいウィンドウで、BambooHR ｌ企業サイトに管理者としてサインインします。

1. ホーム ページで、次の操作を行います。
   
    ![BambooHR シングル サインオン ページ](./media/bamboo-hr-tutorial/ic796691.png "シングル サインオン")   

    a. **[アプリ]** を選択します。
   
    b. **[アプリ]** ウィンドウの **[シングル サインオン]** を選択します。
   
    c. **[SAML シングル サインオン]** を選択します。

1. **[SAML シングル サインオン]** ウィンドウで、次の手順を実行します。
   
    ![[SAML シングル サインオン] ウィンドウ](./media/bamboo-hr-tutorial/IC796692.png "[SAML シングル サインオン]")
   
    a. **[SSO Login URL]\(SSO ログイン URL\)** ボックスに、手順 6. で Azure Portal からコピーした **SAML シングル サインオン サービス URL** を貼り付けます。
      
    b. Azure Portal からダウンロードした Base-64 でエンコードされた証明書をメモ帳で開き、その内容をコピーして **[X.509 証明書]** ボックスに貼り付けます。
   
    c. **[保存]** を選択します。

> [!TIP]
> アプリの設定中に、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できます。 **[Active Directory]** > **[エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブを選択し、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)をご覧ください。
> 

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD テスト ユーザー Britta Simon の作成][100]

Azure AD でテスト ユーザーを作成するには、次の手順に従います。

1. Azure Portal の左側のウィンドウで、**[Azure Active Directory]** を選択します。

    ![Azure Active Directory のボタン](./media/bamboo-hr-tutorial/create_aaduser_01.png)

1. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** を選びます。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/bamboo-hr-tutorial/create_aaduser_02.png)

1. **[すべてのユーザー]** ウィンドウの上部にある **[追加]** を選択します。

    ![[追加] ボタン](./media/bamboo-hr-tutorial/create_aaduser_03.png)

1. **[ユーザー]** ウィンドウで、次の手順を実行します。

    ![[ユーザー] ウィンドウ](./media/bamboo-hr-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **作成**を選択します。
 
### <a name="create-a-bamboohr-test-user"></a>BambooHR テスト ユーザーの作成

Azure AD ユーザーで BambooHR にサインインできるようにするには、次の手順に従い、それらのユーザーを BambooHR に手動で設定します。

1. **BambooHR** サイトに管理者としてサインインします。

1. 上部のツールバーで **[Settings]\(設定\)** を選択します。
   
    ![[Settings]\(設定\) ボタン](./media/bamboo-hr-tutorial/IC796694.png "[Settings]\(設定\)")

1. **[概要]** を選択します。

1. 左側のウィンドウで **[Security]\(セキュリティ\)** > **[Users]\(ユーザー\)** を選択します。

1. 設定しようとしている有効な Azure AD アカウントのユーザー名、パスワード、メール アドレスを入力します。

1. **[保存]** を選択します。
        
>[!NOTE]
>Azure AD ユーザー アカウントの設定は、BambooHR のユーザー アカウント作成ツールや API を使って行うこともできます。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

BambooHR へのアクセスを許可することで、ユーザーである Britta Simon が Azure SSO を使用できるようにします。

![ユーザー ロールを割り当てる][200] 

BambooHR に Britta Simon というユーザーを割り当てるには、次の手順に従います。

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** > **[すべてのアプリケーション]** の順に選択します。

    ![ユーザーの割り当て][201] 

1. **[エンタープライズ アプリケーション]** の一覧で **[BambooHR]** を選択します。

    ![[エンタープライズ アプリケーション] の一覧の [BambooHR] リンク](./media/bamboo-hr-tutorial/tutorial_bamboohr_app.png)  

1. 左側のウィンドウで **[ユーザーとグループ]** を選択します。

    ![[ユーザーとグループ] リンク][202]

1. **[追加]** ボタンを選択し、**[割り当ての追加]** ウィンドウで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

1. **[ユーザーとグループ]** ウィンドウの **[ユーザー]** 一覧で、**Britta Simon** を選択します。

1. **[Select]\(選択\)** ボタンをクリックします。

1. **[割り当ての追加]** ウィンドウで **[割り当て]** ボタンを選択します。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

アクセス パネルを使用して Azure AD SSO の構成をテストします。

アクセス パネルの **[BambooHR]** タイルを選択すると、BambooHR アプリケーションに自動的にサインインされます。

アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory の統合に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/bamboo-hr-tutorial/tutorial_general_01.png
[2]: ./media/bamboo-hr-tutorial/tutorial_general_02.png
[3]: ./media/bamboo-hr-tutorial/tutorial_general_03.png
[4]: ./media/bamboo-hr-tutorial/tutorial_general_04.png

[100]: ./media/bamboo-hr-tutorial/tutorial_general_100.png

[200]: ./media/bamboo-hr-tutorial/tutorial_general_200.png
[201]: ./media/bamboo-hr-tutorial/tutorial_general_201.png
[202]: ./media/bamboo-hr-tutorial/tutorial_general_202.png
[203]: ./media/bamboo-hr-tutorial/tutorial_general_203.png

