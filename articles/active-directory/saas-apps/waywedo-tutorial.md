---
title: 'チュートリアル: Azure Active Directory と Way We Do の統合 | Microsoft Docs'
description: Azure Active Directory と Way We Do の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 84fc4f36-ecd1-42c6-8a70-cb0f3dc15655
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2018
ms.author: jeedes
ms.openlocfilehash: bc415ec7c577e221a1ab5af585dff5b4fc9ab7dc
ms.sourcegitcommit: c2c64fc9c24a1f7bd7c6c91be4ba9d64b1543231
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/26/2018
ms.locfileid: "39259594"
---
# <a name="tutorial-azure-active-directory-integration-with-way-we-do"></a>チュートリアル: Azure Active Directory と Way We Do の統合

このチュートリアルでは、Way We Do と Azure Active Directory (Azure AD) を統合する方法について説明します。

Way We Do と Azure AD の統合には、次の利点があります。

- Way We Do にアクセスできる Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Way We Do にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Way We Do と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Way We Do シングル サインオンに対応したサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Way We Do の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-way-we-do-from-the-gallery"></a>ギャラリーからの Way We Do の追加
Azure AD への Way We Do の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Way We Do を追加する必要があります。

**ギャラリーから Way We Do を追加するには、次の手順を行います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

4. 検索ボックスに「**Way We Do**」と入力し、結果パネルで **[Way We Do]** を選び、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果一覧の Way We Do](./media/waywedo-tutorial/tutorial_waywedo_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Way We Do で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Way We Do ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Way We Do の関連ユーザーの間で、リンク関係が確立されている必要があります。

Way We Do で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Way We Do テスト ユーザーの作成](#create-a-way-we-do-test-user)** - Azure AD の Britta Simon にリンクさせるために、対応するユーザーを Way We Do で作成します。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、Way We Do アプリケーションでシングル サインオンを構成します。

**Way We Do で Azure AD シングル サインオンを構成するには、次の手順を行います。**

1. Azure Portal の **Way We Do** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/waywedo-tutorial/tutorial_waywedo_samlbase.png)

3. **[Way We Do のドメインと URL]** セクションで、次の手順を行います。

    ![[Way We Do のドメインと URL] のシングル サインオン情報](./media/waywedo-tutorial/tutorial_waywedo_url.png)

    a. **[サインオン URL]** ボックスに、`https://<SUBDOMAIN>.waywedo.com/Authentication/ExternalSignIn` のパターンを使用して URL を入力します。

    b. **[識別子]** ボックスに、`https://<SUBDOMAIN>.waywedo.com` の形式で URL を入力します。

    > [!NOTE] 
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新してください。 この値を取得するには、[Way We Do クライアント サポート チーム](mailto:support@waywedo.com)にお問い合わせください。 
 
4. **[SAML 署名証明書]** セクションで、**[Certificate (Raw) (証明書 (Raw))]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/waywedo-tutorial/tutorial_waywedo_certificate.png) 

5. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/waywedo-tutorial/tutorial_general_400.png)

6. **[Way We Do Configuration]\(Way We Do 構成\)** セクションで、**[Configure Way We Do]\(Way We Do の構成\)** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから、**SAML エンティティ ID と SAML シングル サインオン サービス URL** をコピーします。

    ![Way We Do の構成](./media/waywedo-tutorial/tutorial_waywedo_configure.png) 

7. 異なる Web ブラウザー ウィンドウで、セキュリティ管理者として Way We Do にログインします。

8. Way We Do の任意のページで右上隅にある**人アイコン**をクリックし、ドロップダウン メニューで **[Account]\(アカウント\)** をクリックします。

    ![Way We Do アカウント](./media/waywedo-tutorial/tutorial_waywedo_account.png) 

9. **メニュー アイコン**をクリックしてプッシュ ナビゲーション メニューを開き、**[Single Sign On]\(シングル サイン オン\)** をクリックします。

    ![Way We Do シングル](./media/waywedo-tutorial/tutorial_waywedo_single.png)

10. **[Single sign-on setup]\(シングル サイン オンの設定\)** ページで、次の手順を行います。

    ![Way We Do 保存](./media/waywedo-tutorial/tutorial_waywedo_save.png)

    a. **[Turn on single sign-on]\(シングル サイン オンの有効化\)** トグルをクリックして、**[Yes]\(はい\)** に切り替えて、シングル サインオンを有効にします。

    b. **[Single sign-on name]\(シングル サイン オン名\)** テキストボックスに、自分の名前を入力します。

    c. **[Entity ID]\(エンティティ ID\)** ボックスに、Azure Portal からコピーした **SAML エンティティ ID** の値を貼り付けます。

    d. **[SAML SSO URL]** ボックスに、Azure Portal からコピーした **SAML シングル サインオン サービス URL** の値を貼り付けます。

    e. **[Certificate]\(証明書\)** の横にある**選択**ボタンをクリックして、証明書をアップロードします。

    f. **オプション設定** -
    
    * Enable Passwords (パスワードを有効にする) - このオプションを有効にすると、ユーザーがシングル サインオンのみを使用できるように通常のパスワードが Way We Do に対して機能します。

    * Enable Auto-provisioning (自動プロビジョニングを有効にする) - このオプションを有効にすると、サインオンに使用されている電子メール アドレスが Way We Do 内のユーザー リストと自動的に比較されます。 電子メール アドレスが Way We Do 内のアクティブ ユーザーと一致しない場合は、サインインするユーザーに対して新しいユーザー アカウントが自動的に追加され、不足している情報があれば要求されます。

      > [!NOTE]
      > シングル サインオンを介して追加されたユーザーは、一般ユーザーとして追加され、システムでのロールは割り当てられません。 管理者は編集者または管理者としてセキュリティ ロールにアクセスし、それを変更することができると共に、1 つまたは複数の Org Chart ロールを割り当てることもできます。 

    g. **[Save]\(保存\)** をクリックして設定を保持します。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/waywedo-tutorial/create_aaduser_01.png)

2. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/waywedo-tutorial/create_aaduser_02.png)

3. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/waywedo-tutorial/create_aaduser_03.png)

4. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/waywedo-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。
 
### <a name="create-a-way-we-do-test-user"></a>Way We Do テスト ユーザーを作成する

このセクションの目的は、Way We Do で Britta Simon というユーザーを作成することです。 Way We Do では、Just-In-Time プロビジョニングがサポートされています。この設定は、既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 存在しない Way We Do ユーザーにアクセスしようとすると、新しいユーザーが作成されます。

> [!Note]
> ユーザーを手動で作成する必要がある場合は、[Way We Do クライアント サポート チーム](mailto:support@waywedo.com)にお問い合わせください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Way We Do へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**Way We Do に Britta Simon を割り当てるには、次の手順を行います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[Way We Do]** を選択します。

    ![アプリケーションの一覧の [Way We Do] リンク](./media/waywedo-tutorial/tutorial_waywedo_app.png)  

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Way We Do] タイルをクリックすると、自動的に Way We Do アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/waywedo-tutorial/tutorial_general_01.png
[2]: ./media/waywedo-tutorial/tutorial_general_02.png
[3]: ./media/waywedo-tutorial/tutorial_general_03.png
[4]: ./media/waywedo-tutorial/tutorial_general_04.png

[100]: ./media/waywedo-tutorial/tutorial_general_100.png

[200]: ./media/waywedo-tutorial/tutorial_general_200.png
[201]: ./media/waywedo-tutorial/tutorial_general_201.png
[202]: ./media/waywedo-tutorial/tutorial_general_202.png
[203]: ./media/waywedo-tutorial/tutorial_general_203.png

