---
title: 'チュートリアル: Azure Active Directory と FreshDesk の統合 | Microsoft Docs'
description: Azure Active Directory と FreshDesk の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: c2a3e5aa-7b5a-4fe4-9285-45dbe6e8efcc
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/02/2018
ms.author: jeedes
ms.openlocfilehash: b5968b83fc9beb481e2ad2c0cd44d2c284747fa1
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2018
ms.locfileid: "51010815"
---
# <a name="tutorial-azure-active-directory-integration-with-freshdesk"></a>チュートリアル: Azure Active Directory と FreshDesk の統合

このチュートリアルでは、FreshDesk と Azure Active Directory (Azure AD) を統合する方法について説明します。

FreshDesk と Azure AD の統合には、次の利点があります。

- FreshDesk にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで FreshDesk に自動的にサインオン (シングル サインオン) できるように、設定が可能です。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

FreshDesk と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- FreshDesk でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの FreshDesk の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-freshdesk-from-the-gallery"></a>ギャラリーからの FreshDesk の追加

Azure AD への FreshDesk の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に FreshDesk を追加する必要があります。

**ギャラリーから FreshDesk を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

4. 検索ボックスに「**FreshDesk**」と入力し、結果ウィンドウで **[FreshDesk]** を選び、**[追加]** をクリックしてアプリケーションを追加します。

    ![結果一覧の FreshDesk](./media/freshdesk-tutorial/tutorial_freshdesk_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、FreshDesk で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する FreshDesk ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと FreshDesk の関連ユーザーの間で、リンク関係が確立されている必要があります。

FreshDesk で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[FreshDesk のテスト ユーザーの作成](#creating-a-freshdesk-test-user)** - Azure AD の Britta Simon にリンクさせるために、対応するユーザーを FreshDesk で作成します。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal で Azure AD のシングル サインオンを有効にして、FreshDesk アプリケーションにシングル サインオンを構成します。

**FreshDesk で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure portal の **FreshDesk** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

2. **[シングル サインオン方式の選択]** ダイアログで、**[SAML]** モードの **[選択]** をクリックして、シングル サインオンを有効にします。

    ![Configure single sign-on](common/tutorial_general_301.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、**[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![Configure single sign-on](common/editconfigure.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    ![[FreshDesk のドメインと URL] のシングル サインオン情報](./media/freshdesk-tutorial/tutorial_freshdesk_url.png)

    a. **[サインオン URL]** ボックスに、`https://<tenant-name>.freshdesk.com` のパターン、または FreshDesk から示されたその他の値を使用して URL を入力します。

    b. **[Identifier (Entity ID)]** \(ID (エンティティ ID)\) ボックスに、`https://<tenant-name>.freshdesk.com` のパターン、または FreshDesk から示されたその他の値を使用して URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新してください。 これらの値を取得するには、[FreshDesk サポート チーム](https://freshdesk.com/helpdesk-software?utm_source=Google-AdWords&utm_medium=Search-IND-Brand&utm_campaign=Search-IND-Brand&utm_term=freshdesk&device=c&gclid=COSH2_LH7NICFVUDvAodBPgBZg)に問い合わせてください。

5. FreshDesk アプリケーションは、特定の形式の SAML アサーションを使用するため、カスタム属性のマッピングを SAML トークンの属性の構成に追加する必要があります。 次のスクリーンショットはその例です。 **[ユーザー識別子]** の既定値は **user.userprincipalname** ですが、**FreshDesk** では、これがユーザーのメール アドレスにマップされると想定されます。 そのため、一覧の **user.mail** 属性を使用するか、組織構成に基づいて適切な属性値を使用できます。

    ![image](./media/freshdesk-tutorial/i4-attribute.png)

6. **[ユーザー属性と要求]** ダイアログの **[ユーザーの要求]** セクションで、上の図のように SAML トークン属性を構成し、次の手順を実行します。
    
    a. **[アイコンの編集]** をクリックして **[ユーザー要求の管理]** ダイアログを開きます。

    ![image](./media/freshdesk-tutorial/i2-attribute.png)

    ![image](./media/freshdesk-tutorial/i3-attribute.png)

    b. **[ソース属性]** の一覧から、**[user.mail]** を選択します。

    c. **[Save]** をクリックします。

7. **[SAML 署名証明書]** ページの **[SAML 署名証明書]** セクションで、**[ダウンロード]** をクリックして**証明書 (Base64)** をダウンロードし、証明書ファイルをコンピューターに保存します。

    ![証明書のダウンロードのリンク](./media/freshdesk-tutorial/tutorial_freshdesk_certificate.png)

8. **コマンド プロンプト**を開き、次のコマンドを実行します。

    a. コマンド プロンプトに、値として `certutil.exe -dump FreshDesk.cer` を入力します。

    > [!NOTE]
    > ここでの **FreshDesk.cer** は、Azure portal からダウンロードした証明書です。

    b. **Cert ハッシュ (sha256)** 値をコピーし、メモ帳に貼り付けます。 

9. **[Set up FreshDesk]** \(FreshDesk の設定\) セクションで、要件に従って適切な URL をコピーします。

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

    ![FreshDesk の構成](common/configuresection.png)

10. 別の Web ブラウザー ウィンドウで、Freshdesk 企業サイトに管理者としてログインします。

11. **[設定]** アイコンを選択し、**[セキュリティ]** セクションで次の手順を実行します。

    ![Single Sign On](./media/freshdesk-tutorial/IC776770.png "Single Sign On")
  
    a. **[シングル サインオン (SSO)]** で **[オン]** を選択します。

    b. **[SAML SSO]** を選択します。

    c. **[SAML Login URL]** \(SAML ログイン URL\) テキストボックスに、Azure portal からコピーした**ログイン URL** の値を貼り付けます。

    d. **[ログアウト URL]** テキストボックスに、Azure portal からコピーした**ログアウト URL** の値を貼り付けます。

    e. **[セキュリティ証明書フィンガープリント]** テキストボックスに、先ほど得た **Cert ハッシュ (sha256)** の値を貼り付けます。
  
    f. **[Save]** をクリックします。

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、**[Azure Active Directory]**、**[ユーザー]**、**[すべてのユーザー]** の順に選択します。

    ![Azure AD ユーザーの作成][100]

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![Azure AD のテスト ユーザーの作成](common/create_aaduser_01.png) 

3. [ユーザーのプロパティ] で、次の手順を実行します。

    ![Azure AD のテスト ユーザーの作成](common/create_aaduser_02.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    b. **[ユーザー名]** フィールドに「**brittasimon@yourcompanydomain.extension**」と入力します  
    たとえば、BrittaSimon@contoso.com のように指定します。

    c. **[プロパティ]** を選択し、**[パスワードを表示]** チェック ボックスをオンにして、[パスワード] ボックスに表示された値を書き留めます。

    d. **作成**を選択します。

### <a name="creating-a-freshdesk-test-user"></a>FreshDesk テスト ユーザーの作成

Azure AD ユーザーが FreshDesk にログインできるようにするには、そのユーザーを FreshDesk にプロビジョニングする必要があります。  
FreshDesk の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. **Freshdesk** テナントにログインします。

2. 上部のメニューで **[Admin]** をクリックします。

    ![管理](./media/freshdesk-tutorial/IC776772.png "Admin")

3. **[全般設定]** タブで **[エージェント]** をクリックします。
  
    ![Agents](./media/freshdesk-tutorial/IC776773.png "Agents")

4. **[新しいエージェント]** をクリックします。

    ![New Agent](./media/freshdesk-tutorial/IC776774.png "New Agent")

5. [エージェント情報] ダイアログで、次の手順を実行します。

    ![Agent Information](./media/freshdesk-tutorial/IC776775.png "Agent Information")

    a. **[電子メール]** ボックスに、プロビジョニングする Azure AD アカウントの Azure AD 電子メール アドレスを入力します。

    b. **[フル ネーム]** ボックスに、プロビジョニングする Azure AD のアカウントの名前を入力します。

    c. **[タイトル]** ボックスに、プロビジョニングする Azure AD アカウントのタイトルを入力します。

    d. **[Save]** をクリックします。

    >[!NOTE]
    >Azure AD のアカウント所有者には、そのアカウントがアクティブになる前に、アカウント確認用のリンクを含む電子メールが送信されます。
    >
    >[!NOTE]
    >Freshdesk から提供されているその他の Freshdesk ユーザー アカウント作成ツールまたは API を使用して、AAD ユーザー アカウントを Freshdesk にプロビジョニングできます。

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に FreshDesk へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、**[すべてのアプリケーション]** を選択します。

    ![ユーザーの割り当て][201]

2. アプリケーションの一覧で **[FreshDesk]** を選択します。

    ![Configure single sign-on](./media/freshdesk-tutorial/tutorial_freshdesk_app.png)

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202]

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. **[割り当ての追加]** ダイアログで、**[割り当て]** ボタンを選択します。

### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで FreshDesk のタイルをクリックすると、FreshDesk アプリケーションに自動的にサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事をご覧ください。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: common/tutorial_general_01.png
[2]: common/tutorial_general_02.png
[3]: common/tutorial_general_03.png
[4]: common/tutorial_general_04.png

[100]: common/tutorial_general_100.png

[201]: common/tutorial_general_201.png
[202]: common/tutorial_general_202.png
[203]: common/tutorial_general_203.png
