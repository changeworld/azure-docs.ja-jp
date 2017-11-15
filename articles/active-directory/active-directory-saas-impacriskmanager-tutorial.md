---
title: "チュートリアル: Azure Active Directory と IMPAC Risk Manager の統合 | Microsoft Docs"
description: "Azure Active Directory と IMPAC Risk Manager の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 4d77390e-898c-4258-a562-a1181dfe2880
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: jeedes
ms.openlocfilehash: 0d3f17b86caaf781ab8825b047853f2949828e9e
ms.sourcegitcommit: 6a6e14fdd9388333d3ededc02b1fb2fb3f8d56e5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/07/2017
---
# <a name="tutorial-azure-active-directory-integration-with-impac-risk-manager"></a>チュートリアル: Azure Active Directory と IMPAC Risk Manager の統合

このチュートリアルでは、IMPAC Risk Manager と Azure Active Directory (Azure AD) を統合する方法について説明します。

IMPAC Risk Manager と Azure AD の統合には、次の利点があります。

- IMPAC Risk Manager にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に IMPAC Risk Manager にサインオン (シングル サインオン) できるように、設定が可能です。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

IMPAC Risk Manager と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- IMPAC Risk Manager でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの IMPAC Risk Manager の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-impac-risk-manager-from-the-gallery"></a>ギャラリーからの IMPAC Risk Manager の追加
Azure AD への IMPAC Risk Manager の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に IMPAC Risk Manager を追加する必要があります。

**ギャラリーから IMPAC Risk Manager を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

4. 検索ボックスに「**IMPAC Risk Manager**」と入力し、結果ウィンドウで **[IMPAC Risk Manager]** を選び、**[追加]** ボタンをクリックしてアプリケーションを追加します。

    ![結果一覧の IMPAC Risk Manager](./media/active-directory-saas-impacriskmanager-tutorial/tutorial_impacriskmanager_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、IMPAC Risk Manager で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する IMPAC Risk Manager ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと IMPAC Risk Manager の関連ユーザーの間で、リンク関係が確立されている必要があります。

IMPAC Risk Manager で、Azure AD の **[ユーザー名]** の値を **[Username]** の値として割り当ててリンク関係を確立します。

IMPAC Risk Manager で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[IMPAC Risk Manager のテスト ユーザーの作成](#create-a-impac-risk-manager-test-user)** - IMPAC Risk Manager で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、IMPAC Risk Manager アプリケーションでシングル サインオンを構成します。

**IMPAC Risk Manager で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **IMPAC Risk Manager** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/active-directory-saas-impacriskmanager-tutorial/tutorial_impacriskmanager_samlbase.png)

3. **[IMPAC Risk Manager のドメインと URL]** セクションで、IDP 開始モードでアプリケーションを構成する場合は、次の手順に従います。

    ![[IMPAC Risk Manager のドメインと URL] のシングル サインオン情報](./media/active-directory-saas-impacriskmanager-tutorial/tutorial_impacriskmanager_url_new.png)

    a. **[識別子]** ボックスに、IMPAC から提供された値を入力します。

    b. **[応答 URL]** ボックスに、次のパターンを使用して URL を入力します。
    | 環境 | URL パターン |
    | ---------------|--------------- |    
    | 実稼動用 |`https://www.riskmanager.co.nz/DotNet/SSOv2/AssertionConsumerService.aspx?client=<ClientSuffix>`|
    | ステージングおよびトレーニング用  |`https://staging.riskmanager.co.nz/DotNet/SSOv2/AssertionConsumerService.aspx?client=<ClientSuffix>`|
    | 開発用  |`https://dev.riskmanager.co.nz/DotNet/SSOv2/AssertionConsumerService.aspx?client=<ClientSuffix>`|
    | QA 用 |`https://QA.riskmanager.co.nz/DotNet/SSOv2/AssertionConsumerService.aspx?client=<ClientSuffix>`|
    | テスト用 |`https://test.riskmanager.co.nz/DotNet/SSOv2/AssertionConsumerService.aspx?client=<ClientSuffix>`|

4. アプリケーションを **SP** 開始モードで構成する場合は、**[詳細な URL 設定の表示]** チェックボックスをオンにして次の手順を実行します。

    ![[IMPAC Risk Manager のドメインと URL] のシングル サインオン情報](./media/active-directory-saas-impacriskmanager-tutorial/tutorial_impacriskmanager_url1_new.png)

    **[サインオン URL]** ボックスに、次の形式で URL を入力します。
    | 環境 | URL パターン |
    | ---------------|--------------- |    
    | 実稼動用 |`https://www.riskmanager.co.nz/SSOv2/<ClientSuffix>`|
    | ステージングおよびトレーニング用  |`https://staging.riskmanager.co.nz/SSOv2/<ClientSuffix>`|
    | 開発用  |`https://dev.riskmanager.co.nz/SSOv2/<ClientSuffix>`|
    | QA 用 |`https://QA.riskmanager.co.nz/SSOv2/<ClientSuffix>`|
    | テスト用 |`https://test.riskmanager.co.nz/SSOv2/<ClientSuffix>`|

    > [!NOTE] 
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL でこれらの値を更新します。 これらの値を取得するには、[IMPAC Risk Manager クライアント サポート チーム](mailto:rmsupport@Impac.co.nz)に問い合わせてください。

5. **[SAML 署名証明書]** セクションで、**[証明書 (Base64)]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/active-directory-saas-impacriskmanager-tutorial/tutorial_impacriskmanager_certificate.png) 

6. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/active-directory-saas-impacriskmanager-tutorial/tutorial_general_400.png)
    
7. **[IMPAC Risk Manager 構成]** セクションで、**[IMPAC Risk Manager を構成する]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから、**SAML シングル サインオン サービス URL、SAML エンティティ ID**、**サインアウト URL** をコピーします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-impacriskmanager-tutorial/tutorial_impacriskmanager_configure.png)

8. **IMPAC Risk Manager** 側にシングル サインオンを構成するには、ダウンロードされた**証明書 (Base64)**、**サインアウト URL、SAML エンティティ ID**、および **SAML シングル サインオン サービス URL** を [IMPAC Risk Manager サポート チーム](mailto:rmsupport@Impac.co.nz)に送信する必要があります。 サポート チームはこれを設定して、SAML SSO 接続が両方の側で正しく設定されるようにします。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関する記事をご覧ください。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/active-directory-saas-impacriskmanager-tutorial/create_aaduser_01.png)

2. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/active-directory-saas-impacriskmanager-tutorial/create_aaduser_02.png)

3. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/active-directory-saas-impacriskmanager-tutorial/create_aaduser_03.png)

4. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/active-directory-saas-impacriskmanager-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。
 
### <a name="create-a-impac-risk-manager-test-user"></a>IMPAC Risk Manager のテスト ユーザーを作成する

このセクションでは、IMPAC Risk Manager で Britta Simon というユーザーを作成します。 [IMPAC Risk Manager サポート チーム](mailto:rmsupport@Impac.co.nz)と連携し、IMPAC Risk Manager プラットフォームにユーザーを追加してください。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。 

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に IMPAC Risk Manager へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**IMPAC Risk Manager に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[IMPAC Risk Manager]** を選択します。

    ![アプリケーションの一覧の [IMPAC Risk Manager] リンク](./media/active-directory-saas-impacriskmanager-tutorial/tutorial_impacriskmanager_app.png)  

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [IMPAC Risk Manager] タイルをクリックすると、自動的に IMPAC Risk Manager アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-impacriskmanager-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-impacriskmanager-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-impacriskmanager-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-impacriskmanager-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-impacriskmanager-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-impacriskmanager-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-impacriskmanager-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-impacriskmanager-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-impacriskmanager-tutorial/tutorial_general_203.png

