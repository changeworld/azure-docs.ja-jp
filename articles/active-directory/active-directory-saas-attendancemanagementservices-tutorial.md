---
title: 'チュートリアル: Azure Active Directory と Attendance Management Services の統合 | Microsoft Docs'
description: Azure Active Directory と Attendance Management Services の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 1f56e612-728b-4203-a545-a81dc5efda00
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/13/2018
ms.author: jeedes
ms.openlocfilehash: abf882d7b06083080ad5cb3c2a20390a76a48139
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-attendance-management-services"></a>チュートリアル: Azure Active Directory と Attendance Management Services の統合

このチュートリアルでは、Attendance Management Services と Azure Active Directory (Azure AD) を統合する方法について説明します。

Attendance Management Services と Azure AD の統合には、次の利点があります。

- Attendance Management Services にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Attendance Management Services にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Attendance Management Services と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Attendance Management Services でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーから Attendance Management Services を追加する
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-attendance-management-services-from-the-gallery"></a>ギャラリーから Attendance Management Services を追加する
Azure AD への Attendance Management Services の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Attendance Management Services を追加する必要があります。

**ギャラリーから Attendance Management Services を追加するには、次の手順を実行します:** 

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

4. 検索ボックスに「**Attendance Management Services**」と入力して結果パネルから **[Attendance Management Services]** を選択し、**[追加]** をクリックしてアプリケーションを追加します。

    ![結果リストの Attendance Management Services](./media/active-directory-saas-attendancemanagementservices-tutorial/tutorial_attendancemanagementservices_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Attendance Management Services で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Attendance Management Services ユーザーが Azure AD で認識される必要があります。 言い換えると、Azure AD ユーザーと Attendance Management Services の関連ユーザーの間で、リンク関係が確立されている必要があります。

Attendance Management Services で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Attendance Management Services テスト ユーザーの作成](#create-an-attendance-management-service-test-user)** - Attendance Management Services で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Microsoft Azure Portal で Azure AD のシングル サインオンを有効にして、Attendance Management Services アプリケーションでシングル サインオンを構成します。

**Attendance Management Services との Azure AD シングル サインオンを構成するには、次の手順を実行します。**

1. Azure Portal の **Attendance Management Services** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/active-directory-saas-attendancemanagementservices-tutorial/tutorial_attendancemanagementservices_samlbase.png)

3. **[Attendance Management Services のドメインと URL]** セクションで、以下の手順を実行します。

    ![[Attendance Management Services のドメインと URL] のシングル サインオン情報](./media/active-directory-saas-attendancemanagementservices-tutorial/tutorial_attendancemanagementservices_url.png)

    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが RightScale アプリケーションへのサインオンに使用する URL を入力します。 **[サインオン URL]** ボックスに、`https://id.obc.jp/<tenant information >/` のパターンを使用して URL を入力します。

    b. **[識別子]** ボックスに、`https://id.obc.jp/<tenant information >/` の形式で URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新してください。 これらの値を取得するには、[Attendance Management Services クライアント サポート チーム](http://www.obcnet.jp/)に問い合わせてください。

4. **[SAML 署名証明書]** セクションで、**[証明書 (Base64)]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/active-directory-saas-attendancemanagementservices-tutorial/tutorial_attendancemanagementservices_certificate.png) 

5. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/active-directory-saas-attendancemanagementservices-tutorial/tutorial_general_400.png)

6. **[Attendance Management Services の構成]** セクションで、**[Attendance Management Services の構成]** をクリックして **[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから **SAML エンティティ ID と SAML シングル サインオン サービス URL** をコピーします。

    ![Attendance Management Services の構成](./media/active-directory-saas-attendancemanagementservices-tutorial/tutorial_attendancemanagementservices_configure.png) 

7. 別の Web ブラウザーのウィンドウで、管理者として Attendance Management Services 企業サイトにサインオンします。

8. **[セキュリティ管理] セクション**の **[SAML 認証]** をクリックします。

    ![Attendance Management Services の構成](./media/active-directory-saas-attendancemanagementservices-tutorial/user1.png)

9. 次の手順に従います。

    ![Attendance Management Services の構成](./media/active-directory-saas-attendancemanagementservices-tutorial/user2.png)

    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが RightScale アプリケーションへのサインオンに使用する URL を入力します。 **[SAML 認証を利用する]** を選択します。

    b. **[識別子]** テキストボックスに、Azure Portal からコピーした **SAML エンティティ ID** の値を貼り付けます。 

    c. **[認証エンドポイント URL]** ボックスに、Azure Portal からコピーした **SAML シングル サインオン サービス URL** の値を貼り付けます。

    d. **[ファイル選択]** ボタンをクリックして、Azure AD からダウンロードした証明書をアップロードします。

    e. **[パスワード認証を無効する]** を選択します。

    f. **[登録]** をクリックします

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。 **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関するページを参照してください。
> 

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/active-directory-saas-attendancemanagementservices-tutorial/create_aaduser_01.png)

2. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/active-directory-saas-attendancemanagementservices-tutorial/create_aaduser_02.png)

3. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/active-directory-saas-attendancemanagementservices-tutorial/create_aaduser_03.png)

4. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/active-directory-saas-attendancemanagementservices-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。
 
### <a name="create-an-attendance-management-services-test-user"></a>Attendance Management Services テスト ユーザーの作成

Azure AD ユーザーが Attendance Management Services にログインできるようにするには、ユーザーが Attendance Management Services にプロビジョニングされる必要があります。 Attendance Management Services の場合、プロビジョニングは手動のタスクです。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. 管理者として Attendance Management Services 企業サイトにログインします。

2. **[セキュリティ管理] セクション**の **[利用者管理]** をクリックします。

    ![従業員の追加](./media/active-directory-saas-attendancemanagementservices-tutorial/user5.png)

3. **[新規登録]** をクリックします。

    ![従業員の追加](./media/active-directory-saas-attendancemanagementservices-tutorial/user3.png)

4. **[OBCiD 情報]** セクションで、次の手順を実行します。

    ![従業員の追加](./media/active-directory-saas-attendancemanagementservices-tutorial/user4.png)

    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが RightScale アプリケーションへのサインオンに使用する URL を入力します。 **[OBCiD]** テキスト ボックスに、ユーザーの電子メール (**BrittaSimon@contoso.com** など) を入力します。

    b. **[Password]\(パスワード\)** ボックスに、ユーザーのパスワードを入力します。

    c. **[登録]** をクリックします


### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Attendance Management Services へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**Attendance Management Services に Britta Simon を 割り当てるには、次の手順を実行します。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で、**[Attendance Management Services]** を選択します。

    ![アプリケーション一覧の [Attendance Management Services] リンク](./media/active-directory-saas-attendancemanagementservices-tutorial/tutorial_attendancemanagementservices_app.png)  

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Attendance Management Services] タイルをクリックすると、自動的に Attendance Management Services アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-attendancemanagementservices-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-attendancemanagementservices-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-attendancemanagementservices-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-attendancemanagementservices-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-attendancemanagementservices-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-attendancemanagementservices-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-attendancemanagementservices-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-attendancemanagementservices-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-attendancemanagementservices-tutorial/tutorial_general_203.png

