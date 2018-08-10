---
title: 'チュートリアル: Azure Active Directory と RedBrick Health の統合 | Microsoft Docs'
description: Azure Active Directory と RedBrick Health の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 26290c65-9aa3-42ab-8ba5-901b14dc8e73
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/22/2018
ms.author: jeedes
ms.openlocfilehash: 11452dfb94a5a1c8cf4734b1ef21a44cccafbea1
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39430510"
---
# <a name="tutorial-azure-active-directory-integration-with-redbrick-health"></a>チュートリアル: Azure Active Directory と RedBrick Health の統合

このチュートリアルでは、RedBrick Health と Azure Active Directory (Azure AD) を統合する方法について説明します。

RedBrick Health と Azure AD の統合には、次の利点があります。

- RedBrick Health にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に RedBrick Health にサインオン (シングル サインオン) できるように、設定が可能です。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

RedBrick Health と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- RedBrick Health でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの RedBrick Health の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-redbrick-health-from-the-gallery"></a>ギャラリーからの RedBrick Health の追加
Azure AD への RedBrick Health の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に RedBrick Health を追加する必要があります。

**ギャラリーから RedBrick Health を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

1. 検索ボックスに「**RedBrick Health**」と入力し、結果ウィンドウで **RedBrick Health** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果一覧での RedBrick Health](./media/redbrickhealth-tutorial/tutorial_redbrickhealth_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、RedBrick Health で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する RedBrick Health ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと RedBrick Health の関連ユーザーの間で、リンク関係が確立されている必要があります。

RedBrick Health で、Azure AD の **[ユーザー名]** の値を **[Username]** の値として割り当ててリンク関係を確立します。

RedBrick Health で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[RedBrick Health のテスト ユーザーの作成](#create-a-redbrick-health-test-user)** - RedBrick Health で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、RedBrick Health アプリケーションでシングル サインオンを構成します。

**RedBrick Health との Azure AD シングル サインオンを構成するには、次の手順を実行します。**

1. Azure Portal の **RedBrick Health** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/redbrickhealth-tutorial/tutorial_redbrickhealth_samlbase.png)

1. **[RedBrick Health のドメインと URL]** セクションで、次の手順に従います。

    ![[RedBrick Health のドメインと URL] のシングル サインオン情報](./media/redbrickhealth-tutorial/tutorial_redbrickhealth_url.png)

    a. **[識別子]** ボックスに次の URL を入力します。`http://www.redbrickhealth.com`
    
    b. **[応答 URL]** ボックスに、次の URL を入力します。`https://sso-intg.redbrickhealth.com/sp/ACS.saml2`
    
    運用環境: `https://sso.redbrickhealth.com/sp/ACS.saml2`

    c. **[詳細な URL 設定の表示]** をクリックします。
    
    ![[RedBrick Health のドメインと URL] のシングル サインオン情報](./media/redbrickhealth-tutorial/tutorial_redbrickhealth_url1.png)

    d. **[リレー状態]** ボックスに、`https://api-sso2.redbricktest.com/identity/sso/nbound?target=https://vanity9-sso2.redbrickdev.com/portal&connection=<companyname>conn1` のパターンで URL を入力します。
    
    > [!NOTE] 
    > リレー状態の値は実際のものではありません。 実際のリレー状態でこの値を更新します。 この値を取得するには、[RedBrick Health サポート チーム](https://home.redbrickhealth.com/contact/)に問い合わせてください。

1. RedBrick Health アプリケーションでは、特定の形式の SAML アサーションが求められます。そのため、カスタム属性マッピングを SAML トークン属性構成に追加する必要があります。 これらの要求はお客様に固有であり、お客様の要件によって異なります。 次のオプションの要求は、アプリケーションで構成できる単なる例です。 これらの属性の値は、アプリケーション統合ページの **[ユーザー属性]** セクションで管理できます。

    ![Configure single sign-on](./media/redbrickhealth-tutorial/attribute.png)

1. **[シングル サインオン]** ダイアログの **[ユーザー属性]** セクションで、上の図に示すように SAML トークン属性を構成し、次の手順を実行します。

    | 属性名 | 属性値 |
    | ---------------| ----------------|
    | プリンシパル名 | ********** |
    | [クライアント ID] | ********** |
    | 参加者の ID | ********** |
    
    > [!NOTE]
    > これらの値はあくまで参考のためのものです。 組織の要件に従って属性を定義する必要があります。 必要な要求について詳しくは、[RedBrick Health のサポート チーム](https://home.redbrickhealth.com/contact/)にお問い合わせください。
    
    a. **[属性の追加]** をクリックして **[属性の追加]** ダイアログを開きます。
    
    ![Configure single sign-on](./media/redbrickhealth-tutorial/tutorial_attribute_04.png)
    
    ![Configure single sign-on](./media/redbrickhealth-tutorial/tutorial_attribute_05.png)
    
    b. **[名前]** ボックスに、その行に対して表示される属性名を入力します。
    
    c. **[値]** 一覧から、その行に対して表示される値を入力します。

    d. **[名前空間]** は空白のままにします。
    
    e. **[OK]** をクリックします。

1. **[SAML 署名証明書]** セクションで、**[証明書 (Base64)]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/redbrickhealth-tutorial/tutorial_redbrickhealth_certificate.png) 

1. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/redbrickhealth-tutorial/tutorial_general_400.png)

1. **[RedBrick Health 構成]** セクションで、**[RedBrick Health の構成]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス] セクション**から **SAML エンティティ ID** をコピーします。

    ![RedBrick Health の構成](./media/redbrickhealth-tutorial/tutorial_redbrickhealth_configure.png) 

1. **RedBrick Health** 側でシングル サインオンを構成するには、ダウンロードされた**証明書 (Base64)** と **SAML エンティティ ID** を [RedBrick Health サポート チーム](https://home.redbrickhealth.com/contact/)に送る必要があります。 サポート チームはこれを設定して、SAML SSO 接続が両方の側で正しく設定されるようにします。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関するページを参照してください。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/redbrickhealth-tutorial/create_aaduser_01.png)

1. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/redbrickhealth-tutorial/create_aaduser_02.png)

1. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/redbrickhealth-tutorial/create_aaduser_03.png)

1. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/redbrickhealth-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。
  
### <a name="create-a-redbrick-health-test-user"></a>RedBrick Health のテスト ユーザーを作成する

このセクションでは、RedBrick Health で Britta Simon というユーザーを作成します。 [RedBrick Health サポート チーム](https://home.redbrickhealth.com/contact/)と連携し、RedBrick Health プラットフォームにユーザーを追加してください。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。 

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に RedBrick Health へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**Britta Simon を RedBrick Health に割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[RedBrick Health]** を選択します。

    ![アプリケーションの一覧の RedBrick Health のリンク](./media/redbrickhealth-tutorial/tutorial_redbrickhealth_app.png)  

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [RedBrick Health] タイルをクリックすると、自動的に RedBrick Health アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/redbrickhealth-tutorial/tutorial_general_01.png
[2]: ./media/redbrickhealth-tutorial/tutorial_general_02.png
[3]: ./media/redbrickhealth-tutorial/tutorial_general_03.png
[4]: ./media/redbrickhealth-tutorial/tutorial_general_04.png

[100]: ./media/redbrickhealth-tutorial/tutorial_general_100.png

[200]: ./media/redbrickhealth-tutorial/tutorial_general_200.png
[201]: ./media/redbrickhealth-tutorial/tutorial_general_201.png
[202]: ./media/redbrickhealth-tutorial/tutorial_general_202.png
[203]: ./media/redbrickhealth-tutorial/tutorial_general_203.png

