---
title: 'チュートリアル: Azure Active Directory と dmarcian の統合 | Microsoft Docs'
description: Azure Active Directory と dmarcian の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: a04b9383-3a60-4d54-9412-123daaddff3b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/21/2018
ms.author: jeedes
ms.openlocfilehash: 0f8878505280371bf6046c1d1f0d7fc1275dd496
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "47039889"
---
# <a name="tutorial-azure-active-directory-integration-with-dmarcian"></a>チュートリアル: Azure Active Directory と dmarcian の統合

このチュートリアルでは、dmarcian と Azure Active Directory (Azure AD) を統合する方法について説明します。

dmarcian と Azure AD の統合には、次の利点があります。

- dmarcian にアクセスできる Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に dmarcian にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

dmarcian と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- dmarcian のシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの dmarcian の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-dmarcian-from-the-gallery"></a>ギャラリーからの dmarcian の追加
Azure AD への dmarcian の統合を構成するに、ギャラリーから管理対象 SaaS アプリの一覧に dmarcian を追加する必要があります。

**ギャラリーから dmarcian を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![エンタープライズ アプリケーション][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

4. 検索ボックスに「**dmarcian**」と入力し、結果パネルから **[dmarcian]** を選択してから、**[追加]** ボタンをクリックしてアプリケーションを追加します。

    ![結果一覧の dmarcian](./media/dmarcian-tutorial/tutorial_dmarcian_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、dmarcian で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する dmarcian ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと dmarcian の関連ユーザーの間で、リンク関係が確立されている必要があります。

dmarcian で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[dmarcian のテスト ユーザーの作成](#create-a-dmarcian-test-user)** - dmarcian で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、dmarcian アプリケーションでシングル サインオンを構成します。

**dmarcian で Azure AD のシングル サインオンを構成するには、次の手順を実行します。**

1. Azure Portal の **dmarcian** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/dmarcian-tutorial/tutorial_dmarcian_samlbase.png)

3. **[dmarcian Domain and URLs] (dmarcian のドメインと URL)** セクションで、**IDP** 開始モードでアプリケーションを構成する場合は、次の手順を実行します。

    ![[dmarcian Domain and URLs] (dmarcian のドメインと URL) のシングル サインオン情報](./media/dmarcian-tutorial/tutorial_dmarcian_url.png)

    a. **[識別子]** ボックスに、次のパターンで URL を入力します。
    | |
    | -- |
    | `https://us.dmarcian.com/sso/saml/<ACCOUNT_ID>/sp.xml` |
    | `https://dmarcian-eu.com/sso/saml/<ACCOUNT_ID>/sp.xml` |
    | `https://dmarcian-ap.com/sso/saml/<ACCOUNT_ID>/sp.xml` |

    b. **[応答 URL]** ボックスに、次のパターンを使用して URL を入力します。
    | |
    |--|
    | `https://us.dmarcian.com/login/<ACCOUNT_ID>/handle/` |
    | `https://dmarcian-eu.com/login/<ACCOUNT_ID>/handle/` |
    | `https://dmarcian-ap.com/login/<ACCOUNT_ID>/handle/` |

4. アプリケーションを **SP** 開始モードで構成する場合は、**[詳細な URL 設定の表示]** チェックボックスをオンにして次の手順を実行します。

    ![[dmarcian Domain and URLs] (dmarcian のドメインと URL) のシングル サインオン情報](./media/dmarcian-tutorial/tutorial_dmarcian_url1.png)

    **[サインオン URL]** ボックスに、次の形式で URL を入力します。
    | |
    |--|
    | `https://us.dmarcian.com/login/<ACCOUNT_ID>` |
    | `https://dmarcian-eu.com/login/<ACCOUNT_ID>` |
    | `https://dmarciam-ap.com/login/<ACCOUNT_ID>` |
     
    > [!NOTE] 
    > これらは実際の値ではありません。 これらの値は、実際の識別子、応答 URL、およびサインオン URL に更新します。実際の値については、このチュートリアルの後の方で説明します。 

5. **[SAML 署名証明書]** セクションで、コピー ボタンをクリックして **[App Federation Metadata Url]\(アプリケーション フェデレーション メタデータ URL\)** をコピーし、メモ帳に貼り付けます。

    ![証明書のダウンロードのリンク](./media/dmarcian-tutorial/tutorial_dmarcian_certificate.png) 

6. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/dmarcian-tutorial/tutorial_general_400.png)
    
7. 別の Web ブラウザー ウィンドウで、セキュリティ管理者として dmarcian にサインインします。

8. 右上隅にある **[プロファイル]** をクリックし、**[ユーザー設定]** に移動します。

    ![[ユーザー設定] ](./media/dmarcian-tutorial/tutorial_dmarcian_pref.png)

9. 下へスクロールして **[シングル サインオン]** セクションをクリックしてから、**[構成]** をクリックします。

    ![シングル ](./media/dmarcian-tutorial/tutorial_dmarcian_sso.png)

10. **[SAML シングル サインオン]** ページで、**[状態]** を **[有効]** に設定し、次の手順を実行します。

    ![認証 ](./media/dmarcian-tutorial/tutorial_dmarcian_auth.png)

    * **[Add dmarcian to your Identity Provider] (ID プロバイダーへの dmarcian の追加)** セクションで、**[コピー]** をクリックしてインスタンスの **[Assertion Consumer Service URL]** をコピーし、それを Azure Portal の **[dmarcian Domain and URLs] (dmarcian のドメインと URL)** セクションにある **[応答 URL]** テキスト ボックスに貼り付けます。

    * **[Add dmarcian to your Identity Provider] (ID プロバイダーへの dmarcian の追加)** セクションで、**[コピー]** をクリックしてインスタンスの **[エンティティ ID]** をコピーし、それを Azure Portal の **[dmarcian Domain and URLs] (dmarcian のドメインと URL)** セクションにある **[識別子]** テキスト ボックスに貼り付けます。

    * **[Set up Authentication] (認証の設定)** セクションの **[Identity Provider Metadata] (ID プロバイダーのメタデータ)** テキスト ボックスに、Azure Portal からコピーした **[アプリのフェデレーション メタデータ URL]** を貼り付けます。

    * **[Set up Authentication] (認証の設定)** セクションの **[Attribute Statements] (属性ステートメント)** テキスト ボックスに、URL `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` を貼り付けます。

    * **[Set up Login URL] (ログイン URL の設定)** セクションで、インスタンスの **[ログイン URL]** をコピーし、それを Azure Portal の **[dmarcian Domain and URLs] (dmarcian のドメインと URL)** セクションにある **[サインオン URL]** テキスト ボックスに貼り付けます。

        > [!Note]
        > **[ログイン URL]** は、組織に応じて変更できます。

    * **[Save]** をクリックします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/dmarcian-tutorial/create_aaduser_01.png)

2. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/dmarcian-tutorial/create_aaduser_02.png)

3. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/dmarcian-tutorial/create_aaduser_03.png)

4. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/dmarcian-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。
 
### <a name="create-a-dmarcian-test-user"></a>dmarcian テスト ユーザーを作成する

Azure AD ユーザーが dmarcian にサインインできるようにするには、ユーザーを dmarcian にプロビジョニングする必要があります。 dmarcian では、プロビジョニングは手動のタスクです。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. セキュリティ管理者として dmarcian にサインインします。

2. 右上隅にある **[プロファイル]** をクリックし、**[ユーザーの管理]** に移動します。

    ![ユーザー ](./media/dmarcian-tutorial/tutorial_dmarcian_user.png)

3. **[SSO Users] (SSO ユーザー)** セクションの右側で、**[新しいユーザーの追加]** をクリックします。

    ![ユーザーの追加 ](./media/dmarcian-tutorial/tutorial_dmarcian_addnewuser.png)

4. **[新しいユーザーの追加]** ポップアップで、次の手順を実行します。

    ![新しいユーザー ](./media/dmarcian-tutorial/tutorial_dmarcian_save.png)

    a. **[New User Email] (新しいユーザーの電子メール)** テキスト ボックスに、ユーザーの電子メール (**brittasimon@contoso.com** など) を入力します。

    b. そのユーザーに管理者権限を付与する場合は、**[Make User an Admin] (ユーザーを管理者にする)** を選択します。

    c. **[ユーザーの追加]** をクリックします。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に dmarcian へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**dmarcian に Britta Simon を割り当てるには、次の手順を実行します。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[dmarcian]** を選択します。

    ![アプリケーションの一覧の [dmarcian] リンク](./media/dmarcian-tutorial/tutorial_dmarcian_app.png)  

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで dmarcian のタイルをクリックすると、自動的に dmarcian アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/dmarcian-tutorial/tutorial_general_01.png
[2]: ./media/dmarcian-tutorial/tutorial_general_02.png
[3]: ./media/dmarcian-tutorial/tutorial_general_03.png
[4]: ./media/dmarcian-tutorial/tutorial_general_04.png

[100]: ./media/dmarcian-tutorial/tutorial_general_100.png

[200]: ./media/dmarcian-tutorial/tutorial_general_200.png
[201]: ./media/dmarcian-tutorial/tutorial_general_201.png
[202]: ./media/dmarcian-tutorial/tutorial_general_202.png
[203]: ./media/dmarcian-tutorial/tutorial_general_203.png

