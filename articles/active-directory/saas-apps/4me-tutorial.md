---
title: 'チュートリアル: Azure Active Directory と 4me の統合 | Microsoft Docs'
description: Azure Active Directory と 4me の間にシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 983eecc6-41f8-49b7-b7f6-dcf833dde121
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/03/2018
ms.author: jeedes
ms.openlocfilehash: c9134ceebca696ed2b3376a69e26c2ea06f4f0f6
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/03/2018
ms.locfileid: "39507123"
---
# <a name="tutorial-azure-active-directory-integration-with-4me"></a>チュートリアル: Azure Active Directory と 4me の統合

このチュートリアルでは、4me と Azure Active Directory (Azure AD) を統合する方法について説明します。

4me と Azure AD の統合には、次の利点があります。

- 4me にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで 4me に自動的にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

4me と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- 4me でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの 4me の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-4me-from-the-gallery"></a>ギャラリーからの 4me の追加
Azure AD への 4me の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に 4me を追加する必要があります。

**ギャラリーから 4me を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

4. 検索ボックスに「**4me**」と入力し、結果パネルで **4me** を選び、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果一覧の 4me](./media/4me-tutorial/tutorial_4me_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、4me で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する 4me ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと 4me の関連ユーザーの間で、リンク関係が確立されている必要があります。

4me で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[4me テスト ユーザーの作成](#create-a-4me-test-user)** - Azure AD の Britta Simon にリンクさせるために、対応するユーザーを 4me で作成します。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal で Azure AD のシングル サインオンを有効にして、4me アプリケーションでシングル サインオンを構成します。

**4me で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure portal の **4me** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/4me-tutorial/tutorial_4me_samlbase.png)

3. **[4me のドメインと URL]** セクションで、次の手順を行います。

    ![[4me のドメインと URL] のシングル サインオン情報](./media/4me-tutorial/tutorial_4me_url.png)

    a. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。

    | 環境| URL|
    |---|---|
    | 運用 | `https://<SUBDOMAIN>.4me.com`|
    | QA| `https://<SUBDOMAIN>.4me.qa`|
  
    b. **[識別子]** ボックスに、次の形式で URL を入力します。
    
    | 環境| URL|
    |---|---|
    | 運用 | `https://<SUBDOMAIN>.4me.com`|
    | QA| `https://<SUBDOMAIN>.4me.qa`|

    > [!NOTE] 
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新してください。 この値を取得するには、[4me クライアント サポート チーム](mailto:support@4me.com)にお問い合わせください。 
 
4. 4me アプリケーションは、特定の形式で構成された SAML アサーションを受け入れます。 このアプリケーションには、次の要求を構成します。 これらの属性の値は、アプリケーション統合ページの **[ユーザー属性]** セクションで管理できます。 次のスクリーンショットはその例です。
    
    ![Configure single sign-on](./media/4me-tutorial/tutorial_4me_attribute.png)

5. **[シングル サインオン]** ダイアログの **[ユーザー属性]** セクションで、上の図に示すように SAML トークン属性を構成し、次の手順を実行します。
    
    | 属性名 | 属性値 |
    | ---------------| --------------- |    
    | first_name | User.givenname |
    | last_name | User.surname |

    a. **[属性の追加]** をクリックして **[属性の追加]** ダイアログを開きます。

    ![Configure single sign-on](./media/4me-tutorial/tutorial_attribute_04.png)

    ![Configure single sign-on](./media/4me-tutorial/tutorial_attribute_05.png)
    
    b. **[名前]** ボックスに、その行に対して表示される属性名を入力します。
    
    c. **[値]** 一覧から、その行に対して表示される値を入力します。

    d. **[名前空間]** は空白のままにします。
    
    d. **[OK]** をクリックします。

6. **[SAML 署名証明書]** セクションで、お使いのコンピューターの **[拇印]** の値をコピーします。

    ![証明書のダウンロードのリンク](./media/4me-tutorial/tutorial_4me_certificate.png) 

7. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/4me-tutorial/tutorial_general_400.png)

8. **[4me 構成]** セクションで、**[4me の構成]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから**サインアウト URL と SAML シングル サインオン サービス URL** をコピーします。

    ![4me の構成](./media/4me-tutorial/tutorial_4me_configure.png) 

9. 別の Web ブラウザー ウィンドウで、4me に管理者としてログインします。

10. 左上の **[Settings]\(設定\)** ロゴをクリックし、左側のバーの **[Single Sign-On]\(シングル サインオン\)** をクリックします。

    ![4me の設定](./media/4me-tutorial/tutorial_4me_settings.png)

11. **[シングル サインオン]** ページで、次の手順を実行します。

    ![4me のシングル サインオン](./media/4me-tutorial/tutorial_4me_singlesignon.png)

    a. **[Enabled]\(有効\)** オプションを選択します。

    b. **[Remote logout URL]\(リモート ログアウト URL\)** ボックスに、Azure portal からコピーした**サインアウト URL** の値を貼り付けます。

    c. **[SAML]** セクションの **[SAML SSO URL]** ボックスに、Azure portal からコピーした **SAML シングル サインオン サービス URL** の値を貼り付けます。

    d. **[Certificate fingerprint]\(証明書フィンガープリント\)** ボックスに、Azure portal からコピーした**拇印**の値をコロンで区切って貼り付けます (AA:BB:CC:DD:EE:FF:GG:HH:II)。

    e. **[Save]** をクリックします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/4me-tutorial/create_aaduser_01.png)

2. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/4me-tutorial/create_aaduser_02.png)

3. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/4me-tutorial/create_aaduser_03.png)

4. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/4me-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。
 
### <a name="create-a-4me-test-user"></a>4me テスト ユーザーの作成

このセクションの目的は、4me で Britta Simon というユーザーを作成することです。 4me では、Just-In-Time プロビジョニングがサポートされています。この設定は、既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 存在しない 4me ユーザーにアクセスしようとすると、新しいユーザーが自動的に作成されます。

>[!Note]
>ユーザーを手動で作成する必要がある場合は、[4me サポート チーム](mailto:support@4me.com)にお問い合わせください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に 4me へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**4me に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[4me]** を選択します。

    ![アプリケーションの一覧の 4me のリンク](./media/4me-tutorial/tutorial_4me_app.png)  

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで 4me のタイルをクリックすると、4me アプリケーションに自動的にサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/4me-tutorial/tutorial_general_01.png
[2]: ./media/4me-tutorial/tutorial_general_02.png
[3]: ./media/4me-tutorial/tutorial_general_03.png
[4]: ./media/4me-tutorial/tutorial_general_04.png

[100]: ./media/4me-tutorial/tutorial_general_100.png

[200]: ./media/4me-tutorial/tutorial_general_200.png
[201]: ./media/4me-tutorial/tutorial_general_201.png
[202]: ./media/4me-tutorial/tutorial_general_202.png
[203]: ./media/4me-tutorial/tutorial_general_203.png

