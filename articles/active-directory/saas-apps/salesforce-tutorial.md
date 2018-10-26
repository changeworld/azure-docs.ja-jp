---
title: 'チュートリアル: Azure Active Directory と Salesforce の統合 | Microsoft Docs'
description: Azure Active Directory と Salesforce の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: d2d7d420-dc91-41b8-a6b3-59579e043b35
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2018
ms.author: jeedes
ms.openlocfilehash: 36f1bd9c11c8932968a3501ef22fdb7153411256
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/08/2018
ms.locfileid: "48867563"
---
# <a name="tutorial-azure-active-directory-integration-with-salesforce"></a>チュートリアル: Azure Active Directory と Salesforce の統合

このチュートリアルでは、Salesforce と Azure Active Directory (Azure AD) を統合する方法について説明します。

Salesforce と Azure AD の統合には、次の利点があります。

- Salesforce にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで Salesforce に自動的にサインオン (シングル サインオン) できるように設定できます。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Azure AD と Salesforce の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Salesforce でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Salesforce の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-salesforce-from-the-gallery"></a>ギャラリーからの Salesforce の追加

Azure AD への Salesforce の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Salesforce を追加する必要があります。

**ギャラリーから Salesforce を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

4. 検索ボックスに「**Salesforce**」と入力し、結果パネルで **[Salesforce]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果一覧の Salesforce](./media/salesforce-tutorial/tutorial_salesforce_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Salesforce で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Salesforce ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Salesforce の関連ユーザーの間で、リンク関係が確立されている必要があります。

Salesforce で、Azure AD の **[ユーザー名]** の値を **[Username]\(ユーザー名\)** の値として割り当ててリンク関係を確立します。

Salesforce で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Salesforce テスト ユーザーの作成](#create-a-salesforce-test-user)** - Salesforce で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、Salesforce アプリケーションでシングル サインオンを構成します。

**Salesforce で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Salesforce** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

2. 画面上部の **[シングル サインオン モードの変更]** をクリックして、**[SAML]** モードを選択します。

    ![シングル サインオン構成のリンク](./media/salesforce-tutorial/tutorial_general_300.png)

3. **[シングル サインオン方式の選択]** ダイアログで、**[SAML]** モードの **[選択]** をクリックして、シングル サインオンを有効にします。

    ![シングル サインオン構成のリンク](./media/salesforce-tutorial/tutorial_general_301.png)

4. **[SAML でシングル サインオンをセットアップします]** ページで、**[編集]** ボタンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。
   
    ![シングル サインオン構成のリンク](./media/salesforce-tutorial/tutorial_general_302.png)

5. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    ![[Salesforce のドメインと URL] のシングル サインオン情報](./media/salesforce-tutorial/tutorial_salesforce_url.png)

    a. **[サインオン URL]** ボックスに、次のパターンを使用して値を入力します。

    エンタープライズ アカウント: `https://<subdomain>.my.salesforce.com`

    開発者アカウント: `https://<subdomain>-dev-ed.my.salesforce.com`

    b. **[識別子]** ボックスに、次の形式で値を入力します。

    エンタープライズ アカウント: `https://<subdomain>.my.salesforce.com`

    開発者アカウント: `https://<subdomain>-dev-ed.my.salesforce.com`

    > [!NOTE]
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新してください。 これらの値を取得するには、[Salesforce クライアント サポート チーム](https://help.salesforce.com/support)に問い合わせてください。

6. **[SAML 署名証明書]** セクションで、**[ダウンロード]** をクリックして**フェデレーション メタデータの XML** をダウンロードしてから、コンピューターに XML ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/salesforce-tutorial/tutorial_salesforce_certificate.png) 

7. ブラウザーで新しいタブを開き、Salesforce の管理者アカウントにログインします。

8. ページの右上隅の**設定アイコン**の下の **[Setup]\(セットアップ\)** をクリックします。

    ![Configure single sign-on](./media/salesforce-tutorial/configure1.png)

9. ナビゲーション ウィンドウの **[SETTINGS]\(設定\)** まで下へスクロールし、**[Identity]\(ID\)** をクリックして、関連セクションを展開します。 次に、**[シングル サインオンの設定]** をクリックします。

    ![Configure single sign-on](./media/salesforce-tutorial/sf-admin-sso.png)

10. **[シングル サインオンの設定]** ページで、**[編集]** ボタンをクリックします。

    ![Configure single sign-on](./media/salesforce-tutorial/sf-admin-sso-edit.png)

    > [!NOTE]
    > Salesforce アカウント用のシングル サインオン設定を有効にできない場合は、必要に応じて、[Salesforce クライアント サポート チーム](https://help.salesforce.com/support)にお問い合わせください。

11. **[SAML 有効]** を選択し、**[保存]** をクリックします。

      ![Configure single sign-on](./media/salesforce-tutorial/sf-enable-saml.png)

12. SAML のシングル サインオン設定を構成するには、**[New from Metadata File]\(メタデータ ファイルから新規)** をクリックします。

    ![Configure single sign-on](./media/salesforce-tutorial/sf-admin-sso-new.png)

13. **[ファイルの選択]** をクリックして、Azure portal からダウンロードしたメタデータ XML ファイルをアップロードし、**[作成]** をクリックします。

    ![Configure single sign-on](./media/salesforce-tutorial/xmlchoose.png)

14. **[SAML シングル サインオンの設定]** ページでは、フィールドは自動的に入力されます。[保存] をクリックします。

    ![Configure single sign-on](./media/salesforce-tutorial/salesforcexml.png)

15. Salesforce の左側のナビゲーション ウィンドウで、**[Company Settings]\(会社の設定\)** をクリックして関連するセクションを展開し、**[My Domain]\(マイ ドメイン\)** をクリックします。

    ![Configure single sign-on](./media/salesforce-tutorial/sf-my-domain.png)

16. **[認証の構成]** セクションまで下へスクロールし、**[編集]** ボタンをクリックします。

    ![Configure single sign-on](./media/salesforce-tutorial/sf-edit-auth-config.png)

17. **[Authentication Configuration]\(認証の構成\)** セクションで、SAML SSO 構成の **[Authentication Service]\(認証サービス\)** として **[AzureSSO]** をオンにし、**[Save]\(保存\)** をクリックします。

    ![Configure single sign-on](./media/salesforce-tutorial/sf-auth-config.png)

    > [!NOTE]
    > 複数の認証サービスを選択した場合、ユーザーが Salesforce 環境へのシングル サインオンを開始すると、サインインに使用する認証サービスを選択するよう要求されます。 このメッセージが表示されないようにするには、**その他すべての認証サービスをオフのままに**しておいてください。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、**[Azure Active Directory]**、**[ユーザー]**、**[すべてのユーザー]** の順に選択します。

    ![Azure AD ユーザーの作成][100]

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![Azure AD のテスト ユーザーの作成](./media/salesforce-tutorial/create_aaduser_01.png) 

3. [ユーザーのプロパティ] で、次の手順を実行します。

    ![Azure AD のテスト ユーザーの作成](./media/salesforce-tutorial/create_aaduser_02.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    b. **[ユーザー名]** フィールドに、**brittasimon@yourcompanydomain.extension** と入力します  
    たとえば、BrittaSimon@contoso.com のように指定します。

    c. **[プロパティ]** を選択し、**[パスワードを表示]** チェック ボックスをオンにして、[パスワード] ボックスに表示された値を書き留めます。

    d. **作成**を選択します。

### <a name="create-a-salesforce-test-user"></a>Salesforce テスト ユーザーの作成

このセクションでは、Britta Simon というユーザーを Salesforce に作成します。 Salesforce では、Just-In-Time プロビジョニングがサポートされています。この設定は既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 ユーザーがまだ Salesforce に存在しない場合は、Salesforce にアクセスしようとしたときに新しいユーザーが作成されます。 Salesforce は、自動ユーザー プロビジョニングもまたサポートしています。自動ユーザー プロビジョニングの構成方法について詳しくは、[こちら](salesforce-provisioning-tutorial.md)をご覧ください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Salesforce へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200]

**Salesforce に Britta Simon を割り当てるには、次の手順を実行します。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201]

2. アプリケーションの一覧で **[Salesforce]** を選択します。

    ![アプリケーションの一覧の Salesforce リンク](./media/salesforce-tutorial/tutorial_salesforce_app.png)

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

4. **[ユーザーの追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Salesforce] タイルをクリックすると、Salesforce アプリケーションに自動的にサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)
* [[ユーザー プロビジョニングの構成]](salesforce-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/salesforce-tutorial/tutorial_general_01.png
[2]: ./media/salesforce-tutorial/tutorial_general_02.png
[3]: ./media/salesforce-tutorial/tutorial_general_03.png
[4]: ./media/salesforce-tutorial/tutorial_general_04.png

[100]: ./media/salesforce-tutorial/tutorial_general_100.png

[200]: ./media/salesforce-tutorial/tutorial_general_200.png
[201]: ./media/salesforce-tutorial/tutorial_general_201.png
[202]: ./media/salesforce-tutorial/tutorial_general_202.png
[203]: ./media/salesforce-tutorial/tutorial_general_203.png