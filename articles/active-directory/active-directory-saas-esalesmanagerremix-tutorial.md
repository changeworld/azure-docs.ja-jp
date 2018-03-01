---
title: "チュートリアル: Azure Active Directory と E Sales Manager Remix の統合 | Microsoft Docs"
description: "Azure Active Directory と E Sales Manager Remix の間のシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 89b5022c-0d5b-4103-9877-ddd32b6e1c02
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/21/2018
ms.author: jeedes
ms.openlocfilehash: 56c2860b6aeac9fdc66f2b1fdd1ed9126bf77d3f
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/22/2018
---
# <a name="tutorial-azure-active-directory-integration-with-e-sales-manager-remix"></a>チュートリアル: Azure Active Directory と E Sales Manager Remix の統合

このチュートリアルでは、E Sales Manager Remix と Azure Active Directory (Azure AD) を統合する方法について説明します。

E Sales Manager Remix と Azure AD の統合には、次の利点があります。

- E Sales Manager Remix にアクセスするユーザーを Azure AD 上でコントロールできます。
- ユーザーが自分の Azure AD アカウントで自動的に E Sales Manager Remix にサインオン (シングル サインオン) できるように、設定が可能です。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

E Sales Manager Remix と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- E Sales Manager Remix でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの E Sales Manager Remix の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-e-sales-manager-remix-from-the-gallery"></a>ギャラリーからの E Sales Manager Remix の追加
Azure AD への E Sales Manager Remix の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に E Sales Manager Remix を追加する必要があります。

**ギャラリーから E Sales Manager Remix を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

4. 検索ボックスに「**E Sales Manager Remix**」と入力し、結果パネルで **[E Sales Manager Remix]** を選び、**[追加]** ボタンをクリックして、アプリケーションを追加します。

    ![結果一覧の E Sales Manager Remix](./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、E Sales Manager Remix で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する E Sales Manager Remix ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと E Sales Manager Remix の関連ユーザーの間で、リンク関係が確立されている必要があります。

E Sales Manager Remix で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[E Sales Manager Remix のテスト ユーザーの作成](#create-an-e-sales-manager-remix-test-user)** - E Sales Manager Remix で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、E Sales Manager Remix アプリケーションでシングル サインオンを構成します。

**E Sales Manager Remix で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **E Sales Manager Remix** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_samlbase.png)

3. **[E Sales Manager Remix のドメインと URL]** セクションで、次の手順に従います。

    ![[E Sales Manager Remix のドメインと URL] のシングル サインオン情報](./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_url.png)

    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが RightScale アプリケーションへのサインオンに使用する URL を入力します。 **[サインオン URL]** ボックスに、`https://<Server-Based-URL>/<sub-domain>/esales-pc` のパターンを使用して URL を入力します。

    b. **[識別子]** ボックスに、`https://<Server-Based-URL>/<sub-domain>/` の形式で URL を入力します。

    c. **[識別子]** の値をメモ帳にコピーします。 識別子の値は、このチュートリアルで後ほど使用します。
    
    > [!NOTE] 
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新してください。 これらの値を取得するには、[E Sales Manager Remix クライアント サポート チーム](mailto:esupport@softbrain.co.jp)に問い合わせてください。

4. **[SAML 署名証明書]** セクションで、**[証明書 (Base64)]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_certificate.png) 

5. **[その他のすべてのユーザー属性を表示および編集する]** を選択し、**[emailaddress]** 属性をクリックします。
    
    ![E Sales Manager Remix 構成](./media/active-directory-saas-esalesmanagerremix-tutorial/configure1.png)

6. テキスト ボックスから要求の**名前空間**と**名前**の値をコピーします。 次のパターンを使用して値を生成します - `<Namespace>/<Name>`。 この値は、このチュートリアルで後ほど使用します。

    ![E Sales Manager Remix 構成](./media/active-directory-saas-esalesmanagerremix-tutorial/configure2.png)

7. **[E Sales Manager Remix 構成]** セクションで、**[E Sales Manager Remix を構成する]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから**サインアウト URL と SAML シングル サインオン サービス URL** をコピーします。

    ![E Sales Manager Remix 構成](./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_configure.png) 

8. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_400.png)

9. E Sales Manager Remix アプリケーションに管理者としてサインオンします。

10. 右上のメニューから **[To Administrator Menu]\(管理者メニューに移動)** を選択します。

    ![E Sales Manager Remix 構成](./media/active-directory-saas-esalesmanagerremix-tutorial/configure4.png)

11. **[System settings]\(システム設定\)**>**[Cooperation with external system]\(外部システムと連携\)** を選択します

    ![E Sales Manager Remix 構成](./media/active-directory-saas-esalesmanagerremix-tutorial/configure5.png)
    
12. **[SAML]** を選択します

    ![E Sales Manager Remix 構成](./media/active-directory-saas-esalesmanagerremix-tutorial/configure6.png)

13. **[SAML authentication setting]\(SAML 認証設定\)** セクションで、次の手順に従います。

    ![E Sales Manager Remix 構成](./media/active-directory-saas-esalesmanagerremix-tutorial/configure3.png)
    
    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが RightScale アプリケーションへのサインオンに使用する URL を入力します。 **[PC version]\(PC 版\)** を選択します
    
    b. [Collaboration item]\(コラボレーション項目\) セクションから **[email]\(電子メール\)** を選択します。

    c. [Collaboration item]\(コラボレーション項目\) テキスト ボックスに、Azure Portal からコピーした次の**要求の値**を貼り付けます。例: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    d. **[Issuer (entity ID)]\(発行者 (エンティティ ID)\)** テキスト ボックスに、Azure Portal の **[E Sales Manager Remix のドメインと URL]** セクションからコピーした**識別子**の値を貼り付けます。

    e. Azure Portal からダウンロードした**証明書**をアップロードするには、**[File selection]\(ファイルの選択\)** を選択します。

    f. **[ID provider login URL]\(ID プロバイダーのログイン URL\)** テキスト ボックスに、Azure Portal からコピーした **SAML シングル サインオン サービス URL** を貼り付けます。

    g. **[Identity Provider Logout URL]\(ID プロバイダーのログアウト URL\)** テキスト ボックスに、Azure Portal からコピーした**サインアウト URL** の値を貼り付けます。

    h. **[Setting complete]\(設定完了\)** をクリックします

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関するページを参照してください。
> 

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/active-directory-saas-esalesmanagerremix-tutorial/create_aaduser_01.png)

2. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/active-directory-saas-esalesmanagerremix-tutorial/create_aaduser_02.png)

3. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/active-directory-saas-esalesmanagerremix-tutorial/create_aaduser_03.png)

4. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/active-directory-saas-esalesmanagerremix-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。
 
### <a name="create-an-e-sales-manager-remix-test-user"></a>E Sales Manager Remix のテスト ユーザーの作成

1. E Sales Manager Remix アプリケーションに管理者としてサインオンします。

2. 右上のメニューから **[To Administrator Menu]\(管理者メニューに移動)** を選択します。

    ![E Sales Manager Remix 構成](./media/active-directory-saas-esalesmanagerremix-tutorial/configure4.png)

3. **[Your company settings]\(会社の設定\)**>**[Maintenance of departments and employees]\(部署と従業員のメンテナンス\)** の順に選択し、**[Employees registered]\(登録済み従業員\)** を選択します。

    ![ユーザー](./media/active-directory-saas-esalesmanagerremix-tutorial/user1.png)

4. **[New employee registration]\(従業員の新規登録)** セクションで、次の手順に従います。
    
    ![ユーザー](./media/active-directory-saas-esalesmanagerremix-tutorial/user2.png)

    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが RightScale アプリケーションへのサインオンに使用する URL を入力します。 **[Employee Name]\(従業員名\)** テキスト ボックスに、Britta などのユーザー名を入力します。

    b. すべての必須フィールドにユーザー情報を入力します。
    
    c. SAML を有効にすると、管理者がログイン画面からログインできなくなるため、**[Admin Login]\(管理者ログイン\)** を選択して、管理者ログイン特権をユーザーに付与します

    d. **[Registration]\(登録\)** をクリックします

5. 以降、管理者としてログインするには、管理者権限が付与されたユーザーでログインし、右上のメニューで **[To Administrator Menu]\(管理者メニューに移動)** をクリックします。

    ![ユーザー](./media/active-directory-saas-esalesmanagerremix-tutorial/configure4.png)

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に E Sales Manager Remix へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**E Sales Manager Remix に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[E Sales Manager Remix]** を選択します。

    ![アプリケーションの一覧の [E Sales Manager Remix] リンク](./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_app.png)  

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [E Sales Manager Remix] タイルをクリックすると、自動的に E Sales Manager Remix アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_203.png

