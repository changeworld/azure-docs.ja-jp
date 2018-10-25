---
title: 'チュートリアル: Azure Active Directory と Zendesk の統合 | Microsoft Docs'
description: Azure Active Directory と Zendesk の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 9d7c91e5-78f5-4016-862f-0f3242b00680
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/03/2018
ms.author: jeedes
ms.openlocfilehash: 37d20eabfc8fb883cda346dc8b55a17b8b959218
ms.sourcegitcommit: f58fc4748053a50c34a56314cf99ec56f33fd616
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/04/2018
ms.locfileid: "48268176"
---
# <a name="tutorial-azure-active-directory-integration-with-zendesk"></a>チュートリアル: Azure Active Directory と Zendesk の統合

このチュートリアルでは、Zendesk と Azure Active Directory (Azure AD) を統合する方法について説明します。

Zendesk と Azure AD の統合には、次の利点があります。

- Zendesk にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的にZendesk にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Zendesk と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Zendesk でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。
このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Zendesk の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-zendesk-from-the-gallery"></a>ギャラリーからの Zendesk の追加

Azure AD への Zendesk の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Zendesk を追加する必要があります。

**ギャラリーから Zendesk を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![image](./media/zendesk-tutorial/selectazuread.png)

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![image](./media/zendesk-tutorial/a_select_app.png)
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![image](./media/zendesk-tutorial/a_new_app.png)

4. 検索ボックスに「**Zendesk**」と入力し、結果ウィンドウで **[Zendesk]** を選び、**[追加]** をクリックしてアプリケーションを追加します。

     ![image](./media/zendesk-tutorial/a_add_app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Zendesk で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Zendesk ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Zendesk の関連ユーザーの間で、リンク リレーションシップが確立されている必要があります。

Zendesk で、Azure AD の **[ユーザー名]** の値に **[Username]** の値を割り当て、リンク関係を確立します。

Zendesk で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Zendesk テスト ユーザーの作成](#create-a-zendesk-test-user)** - Zendesk で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にし、Zendesk アプリケーションでシングル サインオンを構成します。

**Zendesk で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. [Azure portal](https://portal.azure.com/) の **Zendesk** アプリケーション統合ページで、**[シングル サインオン]** を選択します。

    ![image](./media/zendesk-tutorial/b1_b2_select_sso.png)

2. 画面上部の **[シングル サインオン モードの変更]** をクリックして、**[SAML]** モードを選択します。

      ![image](./media/zendesk-tutorial/b1_b2_saml_ssso.png)

3. **[シングル サインオン方式の選択]** ダイアログで、**[SAML]** モードの **[選択]** をクリックして、シングル サインオンを有効にします。

    ![image](./media/zendesk-tutorial/b1_b2_saml_sso.png)

4. **[SAML でシングル サインオンをセットアップします]** ページで、**[編集]** ボタンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![image](./media/zendesk-tutorial/b1-domains_and_urlsedit.png)

5. **[基本的な SAML 構成]** セクションで、次の手順のようにします。

    a. **[サインオン URL]** テキスト ボックスに、`https://<subdomain>.zendesk.com` というパターンで URL を入力します。

    b. **[識別子]** ボックスに、`<subdomain>.zendesk.com` というパターンで URL を入力します。

    ![image](./media/zendesk-tutorial/b1-domains_and_urls.png)

    > [!NOTE] 
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新してください。 これらの値を取得するには、[Zendesk クライアント サポート チーム](https://support.zendesk.com/hc/articles/203663676-Using-SAML-for-single-sign-on-Professional-and-Enterprise)にお問い合わせください。

6. Zendesk は、特定の形式で構成された SAML アサーションを受け入れます。 必須の SAML 属性はありませんが、必要に応じて、アプリケーション統合ページの **[ユーザー属性]** セクションから属性を追加できます。 **[SAML でシングル サインオンをセットアップします]** ページで、**[編集]** ボタンをクリックして **[ユーザー属性]** ダイアログを開きます。

    ![image](./media/zendesk-tutorial/i4-attribute.png)

7. **[ユーザー属性]** ダイアログの **[ユーザーの要求]** セクションで、上の図のように SAML トークン属性を構成し、次の手順を実行します。

    a. **[新しい要求の追加]** をクリックして **[ユーザー要求の管理]** ダイアログを開きます。

    ![image](./media/zendesk-tutorial/i2-attribute.png)

    ![image](./media/zendesk-tutorial/i3-attribute.png)
    
    b. **[名前]** ボックスに、その行に対して表示される属性名を入力します。

    c. **[名前空間]** は空白のままにします。

    d. [ソース] として **[属性]** を選択します。
    
    e. **[ソース属性]** の一覧から、当該行に対して示されている属性値を入力します。
    
    f. **[OK]** をクリックします。

    g. **[Save]** をクリックします。

    > [!NOTE]
    > 既定では、Azure AD に含まれていない属性を追加するには拡張属性を使用します。 「[SAMLに設定できるユーザー属性](https://support.zendesk.com/hc/en-us/articles/203663676-Using-SAML-for-single-sign-on-Professional-and-Enterprise-)」をクリックすると、**Zendesk** が受け入れる SAML 属性の完全な一覧が表示されます。

8. [SAML 署名証明書] セクションの **[SAML 署名証明書]** セクションで、**[拇印]** をコピーしてコンピューターに保存します。

    ![image](./media/zendesk-tutorial/C3_certificate.png)

    a. 必要に応じて、**[署名オプション]** で適切なオプションを選択します。

    b. 必要に応じて、**[署名アルゴリズム]** で適切なオプションを選択します。

    c. **[保存]**

9. **[Set up Zendesk]\(Zendesk のセットアップ\)** セクションで、**[ステップ バイ ステップの手順を表示]** をクリックして **[サインオンの構成]** ウィンドウを開きます。 **クイック リファレンス セクション**から以下の URL をコピーします。

    URL は次のように表示されている場合があることに注意してください。

    a. SAML シングル サインオン サービス URL

    b. エンティティ ID

    c. サインアウト URL

    ![image](./media/zendesk-tutorial/d1_saml.png) 

10. Zendesk は自動と手動の 2 つの方法で構成できます。
  
11. Zendesk 内での構成を自動化するには、**[拡張機能のインストール]** をクリックして **[My Apps Secure Sign-in browser extension]\(My Apps Secure Sign-in ブラウザー拡張機能\)** をインストールする必要があります。

    ![image](./media/zendesk-tutorial/install_extension.png)

12. ブラウザーに拡張機能を追加した後、**[Set up Zendesk]\(Zendesk のセットアップ\)** をクリックすると、Zendesk アプリケーションに移動します。 そこから、管理者資格情報を提供して Zendesk にサインインします。 ブラウザー拡張機能によりアプリケーションが自動的に構成され、手順 13 が自動化されます。

     ![image](./media/zendesk-tutorial/d2_saml.png) 

13. Zendesk を手動でセットアップする場合は、新しい Web ブラウザー ウィンドウを開き、管理者として Zendesk 企業サイトにとログインして、次の手順のようにします。

    * **[Admin]** をクリックします。

    * 左側のナビゲーション ウィンドウで、**[設定]**、**[セキュリティ]** の順にクリックします。

    * **[Security]\(セキュリティ\)** ページで、次の手順に従います。

      ![Security (セキュリティ)](././media/zendesk-tutorial/ic773089.png "Security")

      ![シングル サインオン](././media/zendesk-tutorial/ic773090.png "シングル サインオン")

      a. **[Admin & Agents]\(管理者とエージェント\)** タブをクリックします。

      b. **[シングルサインオン (SSO) と SAML]** を選択し、**[SAML]** を選択します。

      c. **[SAML SSO URL]\(SAML SSO URL\)** ボックスに、Azure Portal からコピーした **SAML シングル サインオン サービス URL** の値を貼り付けます。

      d. **[Remote Logout URL]\(リモート ログアウト URL\)** ボックスに、Azure Portal からコピーした**サインアウト URL** の値を貼り付けます。

      e. **[Certificate Fingerprint] \(証明書のフィンガープリント)** テキスト ボックスに、Azure Portal からコピーした証明書の **THUMBPRINT** 値を貼り付けます。

      f. **[Save]** をクリックします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成 

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、**[Azure Active Directory]**、**[ユーザー]**、**[すべてのユーザー]** の順に選択します。

    ![image](./media/zendesk-tutorial/d_users_and_groups.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![image](./media/zendesk-tutorial/d_adduser.png)

3. [ユーザーのプロパティ] で、次の手順のようにします。

    ![image](./media/zendesk-tutorial/d_userproperties.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    b. **[ユーザー名]** フィールドに、**brittasimon@yourcompanydomain.extension** と入力します。  
    たとえば、BrittaSimon@contoso.com のように指定します。

    c. **[プロパティ]** を選択し、**[パスワードを表示]** チェック ボックスをオンにして、[パスワード] ボックスに表示された値を書き留めます。

    d. **作成**を選択します。

### <a name="create-a-zendesk-test-user"></a>Zendesk テスト ユーザーの作成

このセクションの目的は、ZendeskにBritta Simon というユーザーを作成することです。 Zendesk では、自動ユーザー プロビジョニングがサポートされています。この設定は、既定で有効になっています。 自動ユーザー プロビジョニングの構成方法について詳しくは、[こちら](Zendesk-provisioning-tutorial.md)をご覧ください。

**ユーザーを手動で作成する必要がある場合、次の手順を実行します。**

> [!NOTE]
> **エンドユーザー** アカウントは、サインインするときに自動的にプロビジョニングされます。 **エージェント**と**管理者**のアカウントは、サインインの前に **Zendesk** で手動でプロビジョニングする必要があります。

1. **Zendesk** テナントにログインします。

2. **[顧客リスト]** タブを選択します。

3. **[ユーザー]** タブを選択し、**[追加]** をクリックします。

    ![ユーザーの追加](././media/zendesk-tutorial/ic773632.png "ユーザーの追加")
4. プロビジョニングする既存の Azure AD アカウントの**名前**と**電子メール アドレス**を入力し、**[Save]\(保存\)** をクリックします。

    ![New user](././media/zendesk-tutorial/ic773633.png "New user")

> [!NOTE]
> Zendesk から提供されている他の Zendesk ユーザー アカウント作成ツールまたは API を使用して、AAD ユーザー アカウントをプロビジョニングできます。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Zendesk へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、**[すべてのアプリケーション]** を選択します。

    ![image](./media/zendesk-tutorial/d_all_applications.png)

2. アプリケーションの一覧で **[Zendesk]** を選択します。

    ![image](./media/zendesk-tutorial/d_all_zendeskapplications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![image](./media/zendesk-tutorial/d_leftpaneusers.png)

4. **[追加]** ボタンを選択し、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![image](./media/zendesk-tutorial/d_assign_user.png)

4. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **Britta Simon** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

5. **[割り当ての追加]** ダイアログで、**[割り当て]** ボタンを選択します。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Zendesk] タイルをクリックすると、自動的に Zendesk アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)
* [[ユーザー プロビジョニングの構成]](zendesk-provisioning-tutorial.md)
