---
title: チュートリアル:Azure Active Directory と Egnyte の統合 | Microsoft Docs
description: Azure Active Directory と Egnyte の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 8c2101d4-1779-4b36-8464-5c1ff780da18
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2018
ms.author: jeedes
ms.openlocfilehash: e33fc71e0e43864d7d70495fc5056a8acaf4ad56
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2019
ms.locfileid: "55159014"
---
# <a name="tutorial-azure-active-directory-integration-with-egnyte"></a>チュートリアル:Azure Active Directory と Egnyte の統合

このチュートリアルでは、Egnyte と Azure Active Directory (Azure AD) を統合する方法について説明します。

Egnyte と Azure AD の統合には、次の利点があります。

- Egnyte にアクセスする Azure AD ユーザーを制御できます。
- ユーザーは自分の Azure AD アカウントで自動的に Egnyte にサインオン (シングル サインオン) できます。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Egnyte と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Egnyte でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Egnyte の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-egnyte-from-the-gallery"></a>ギャラリーからの Egnyte の追加

Azure AD への Egnyte の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Egnyte を追加する必要があります。

**ギャラリーから Egnyte を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

4. 検索ボックスに「**Egnyte**」と入力し、結果パネルで **[Egnyte]** を選び、**[追加]** ボタンをクリックして、アプリケーションを追加します。

    ![結果一覧の Egnyte](./media/egnyte-tutorial/tutorial_egnyte_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Egnyte で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Egnyte ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Egnyte の関連ユーザーの間で、リンク関係が確立されている必要があります。

Egnyte で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Egnyte テスト ユーザーの作成](#creating-an-egnyte-test-user)** - Azure AD でのユーザーにリンクされた、Egnyte での Britta Simon の対応するユーザーを作成します。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、Egnyte アプリケーションでシングル サインオンを構成します。

**Egnyte で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Egnyte** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

2. **[シングル サインオン方式の選択]** ダイアログで、**[SAML]** モードの **[選択]** をクリックして、シングル サインオンを有効にします。

    ![Configure single sign-on](common/tutorial_general_301.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、**[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![Configure single sign-on](common/editconfigure.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    ![Egnyte のドメインおよび URL のシングル サインオン情報](./media/egnyte-tutorial/tutorial_egnyte_url.png)

    **[サインオン URL]** ボックスに、`https://<companyname>.egnyte.com` のパターンを使用して URL を入力します。

    > [!NOTE] 
    > これは実際の値ではありません。 この値を実際のサインオン URL で更新してください。 この値を取得するには、[Egnyte サポート チーム](https://www.egnyte.com/corp/contact_egnyte.html)に問い合わせてください。 

5. **[SAML 署名証明書]** ページの **[SAML 署名証明書]** セクションで、**[ダウンロード]** をクリックして**証明書 (Base64)** をダウンロードし、証明書ファイルをコンピューターに保存します。

    ![証明書のダウンロードのリンク](./media/egnyte-tutorial/tutorial_egnyte_certificate.png) 

6. **[Set up Egnyte]\(Egnyte の設定\)** セクションで、要件に従う適切な URL をコピーします。

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

    ![Egnyte の構成](common/configuresection.png)

7. 別の Web ブラウザー ウィンドウで、Egnyte 企業サイトに管理者としてログインします。

8. **[設定]** をクリックします。
   
    ![設定](./media/egnyte-tutorial/ic787819.png "Settings")

9. メニューで **[設定]** をクリックします。

    ![設定](./media/egnyte-tutorial/ic787820.png "Settings")

10. **[構成]** タブをクリックし、**[セキュリティ]** をクリックします。

    ![Security (セキュリティ)](./media/egnyte-tutorial/ic787821.png "Security")

11. **[シングル サインオン認証]** セクションで、次の手順を実行します。

    ![Single Sign On Authentication](./media/egnyte-tutorial/ic787822.png "Single Sign On Authentication")   
    
    a. **[シングル サインオン認証]** として **[SAML 2.0]** を選択します。
   
    b. **[ID プロバイダー]** として **[AzureAD]** を選択します。
   
    c. Azure portal からコピーした**ログイン URL** を **[ID プロバイダーのログイン URL]** テキスト ボックス内に貼り付けます。
   
    d. Azure portal からコピーした **Azure AD 識別子**を **[Identity provider entity ID]\(ID プロバイダーのエンティティ ID\)** テキスト ボックスに貼り付けます。
      
    e. Azure Portal からダウンロードした base-64 でエンコードされた証明書をメモ帳で開き、その内容をクリップボードにコピーして **[Identity provider certificate]\(ID プロバイダー証明書\)** ボックスに貼り付けます。
   
    f. **[既定のユーザー マッピング]** として **[メール アドレス]** を選択します。
   
    g. **[ドメイン固有の発行者の値を使用]** として **[無効]** を選択します。
   
    h. **[Save]** をクリックします。

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

### <a name="creating-an-egnyte-test-user"></a>Egnyte テスト ユーザーの作成

Azure AD ユーザーが Egnyte にログインできるようにするには、そのユーザーを Egnyte にプロビジョニングする必要があります。 Egnyte の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. **Egnyte** 企業サイトに管理者としてログインします。

2. **[設定] \> [ユーザーとグループ]** の順にクリックします。

3. **[新しいユーザーの追加]** をクリックし、追加するユーザーの種類を選択します。
   
    ![ユーザー](./media/egnyte-tutorial/ic787824.png "Users")

4. **[New Power User]\(新規のパワー ユーザー\)** セクションで、次の手順を実行します。
    
    ![New Standard User](./media/egnyte-tutorial/ic787825.png "New Standard User")   

    a. **[Email]\(電子メール\)** ボックスに、ユーザーのメール アドレスを入力します (例: **Brittasimon@contoso.com**)。

    b. **[ユーザー名]** テキスト ボックスにユーザー名 (**Brittasimon** など) を入力します。

    c. **[Authentication Type]\(認証の種類\)** として **[シングル サインオン]** を選択します。
   
    d. **[Save]** をクリックします。
    
    >[!NOTE]
    >Azure Active Directory アカウント保有者に通知メールが届きます。
    >

>[!NOTE]
>Egnyte から提供されている他の Egnyte ユーザー アカウント作成ツールまたは API を使用して、AAD ユーザー アカウントをプロビジョニングできます。
> 

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Egnyte へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、**[すべてのアプリケーション]** を選択します。

    ![ユーザーの割り当て][201]

2. アプリケーションの一覧で **[Egnyte]** を選択します。

    ![Configure single sign-on](./media/egnyte-tutorial/tutorial_egnyte_app.png)

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202]

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. **[割り当ての追加]** ダイアログで、**[割り当て]** ボタンを選択します。

### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Egnyte] タイルをクリックすると、自動的に Egnyte アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関するページを参照してください。

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
