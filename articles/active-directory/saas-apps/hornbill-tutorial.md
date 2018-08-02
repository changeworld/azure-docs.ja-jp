---
title: 'チュートリアル: Azure Active Directory と Hornbill の統合 | Microsoft Docs'
description: Azure Active Directory と Hornbill の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 173061e4-ac1d-458f-bb9b-e9a2493aab0e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2018
ms.author: jeedes
ms.openlocfilehash: 30fdb55758d5fbac41452236ebaa9f96ab9bba6b
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/19/2018
ms.locfileid: "39163773"
---
# <a name="tutorial-azure-active-directory-integration-with-hornbill"></a>チュートリアル: Azure Active Directory と Hornbill の統合

このチュートリアルでは、Hornbill と Azure Active Directory (Azure AD) を統合する方法について説明します。

Hornbill と Azure AD の統合には、次の利点があります。

- Hornbill にアクセスできる Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Hornbill にサインオン (シングル サインオン) できるように設定できます。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Hornbill と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Hornbill シングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Hornbill の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-hornbill-from-the-gallery"></a>ギャラリーからの Hornbill の追加
Azure AD への Hornbill の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Hornbill を追加する必要があります。

**ギャラリーから Hornbill を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

4. 検索ボックスに「**Hornbill**」と入力し、結果パネルで **[Hornbill]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果リストの Hornbill](./media/hornbill-tutorial/tutorial_hornbill_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Hornbill で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Hornbill ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Hornbill の関連ユーザーの間で、リンク関係が確立されている必要があります。

Hornbill で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Hornbill のテスト ユーザーの作成](#create-a-hornbill-test-user)** - Hornbill で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal で Azure AD のシングル サインオンを有効にして、Hornbill アプリケーションでシングル サインオンを構成します。

**Hornbill で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure portal の **Hornbill** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/hornbill-tutorial/tutorial_hornbill_samlbase.png)

3. **[Hornbill のドメインと URL]** セクションで、次の手順に従います。

    ![[Hornbill のドメインと URL] のシングル サインオン情報](./media/hornbill-tutorial/tutorial_hornbill_url.png)

    a. **[サインオン URL]** ボックスに、`https://<SUBDOMAIN>.hornbill.com/<INSTANCE_NAME>/` のパターンを使用して URL を入力します。

    b. **[識別子]** ボックスに、`https://<SUBDOMAIN>.hornbill.com/<INSTANCE_NAME>/lib/saml/auth/simplesaml/module.php/saml/sp/metadata.php/saml` の形式で URL を入力します。

    > [!NOTE] 
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新してください。 これらの値を取得するには、[Hornbill クライアント サポート チーム](https://www.hornbill.com/support/?request/)にお問い合わせください。 

4. **[SAML 署名証明書]** セクションで、コピー ボタンをクリックして **[App Federation Metadata Url]\(アプリケーション フェデレーション メタデータ URL\)** をコピーし、メモ帳に貼り付けます。

    ![証明書のダウンロードのリンク](./media/hornbill-tutorial/tutorial_hornbill_certificate.png) 

5. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/hornbill-tutorial/tutorial_general_400.png)
 
6. 別の Web ブラウザー ウィンドウで、Hornbill にセキュリティ管理者としてログインします。

7. ホーム ページで **[System]\(システム\)** をクリックします。

    ![Hornbill システム](./media/hornbill-tutorial/tutorial_hornbill_system.png)

8. **[セキュリティ]** に移動します。

    ![Hornbill のセキュリティ](./media/hornbill-tutorial/tutorial_hornbill_security.png)

9. **[SSO Profiles]\(SSO プロファイル\)** をクリックします。

    ![Hornbill のシングル](./media/hornbill-tutorial/tutorial_hornbill_sso.png)

10. ページの右上の**追加のロゴ**をクリックします。

    ![Hornbill の追加](./media/hornbill-tutorial/tutorial_hornbill_addlogo.png)

11. **[Profile Details]\(プロファイルの詳細\)** バーで、**SAML メタデータのインポートのロゴ**をクリックします。

    ![Hornbill のロゴ](./media/hornbill-tutorial/tutorial_hornbill_logo.png)

12. ポップアップ ページの **[URL]** テキスト ボックスに、Azure portal からコピーした **[アプリのフェデレーション メタデータ URL]** を貼り付け、**[Process]\(処理\)** をクリックします。

    ![Hornbill の処理](./media/hornbill-tutorial/tutorial_hornbill_process.png)

13. [Process]\(処理\) をクリックすると、**[Profile Details]\(プロファイルの詳細\)** セクションに値が自動的に入力されます。

    ![Hornbill ページ 1](./media/hornbill-tutorial/tutorial_hornbill_ssopage.png)

    ![Hornbill ページ 2](./media/hornbill-tutorial/tutorial_hornbill_ssopage1.png)

    ![Hornbill ページ 3](./media/hornbill-tutorial/tutorial_hornbill_ssopage2.png)

14. **[変更を保存]** をクリックします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/hornbill-tutorial/create_aaduser_01.png)

2. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/hornbill-tutorial/create_aaduser_02.png)

3. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/hornbill-tutorial/create_aaduser_03.png)

4. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/hornbill-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。
 
### <a name="create-a-hornbill-test-user"></a>Hornbill テスト ユーザーの作成

このセクションの目的は、Hornbill で Britta Simon というユーザーを作成することです。 Hornbill では、Just-In-Time プロビジョニングがサポートされています。この設定は、既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 存在しない Hornbill ユーザーにアクセスしようとすると、新しいユーザーが作成されます。

> [!Note]
> ユーザーを手動で作成する必要がある場合は、[Hornbill クライアント サポート チーム](https://www.hornbill.com/support/?request/)にお問い合わせください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Hornbill へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**Hornbill に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[Hornbill]** を選択します。

    ![アプリケーションの一覧の Hornbill リンク](./media/hornbill-tutorial/tutorial_hornbill_app.png)  

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Hornbill] タイルをクリックすると、Hornbill アプリケーションに自動的にサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/hornbill-tutorial/tutorial_general_01.png
[2]: ./media/hornbill-tutorial/tutorial_general_02.png
[3]: ./media/hornbill-tutorial/tutorial_general_03.png
[4]: ./media/hornbill-tutorial/tutorial_general_04.png

[100]: ./media/hornbill-tutorial/tutorial_general_100.png

[200]: ./media/hornbill-tutorial/tutorial_general_200.png
[201]: ./media/hornbill-tutorial/tutorial_general_201.png
[202]: ./media/hornbill-tutorial/tutorial_general_202.png
[203]: ./media/hornbill-tutorial/tutorial_general_203.png

