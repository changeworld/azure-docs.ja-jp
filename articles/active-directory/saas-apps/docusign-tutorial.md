---
title: チュートリアル:Azure Active Directory と DocuSign の統合 | Microsoft Docs
description: Azure Active Directory と DocuSign の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: a691288b-84c1-40fb-84bd-5b06878865f0
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/19/2018
ms.author: jeedes
ms.openlocfilehash: 6b2b5f72c9520498d4834bbbfaf6c56656a807e7
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/07/2018
ms.locfileid: "53015221"
---
# <a name="tutorial-azure-active-directory-integration-with-docusign"></a>チュートリアル:Azure Active Directory と DocuSign の統合

このチュートリアルでは、DocuSign と Azure Active Directory (Azure AD) を統合する方法について説明します。

DocuSign と Azure AD の統合には、次の利点があります。

- DocuSign にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に DocuSign にサインオン (シングル サインオン) できるように設定することができます。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Azure AD と DocuSign の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- DocuSign でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの DocuSign の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-docusign-from-the-gallery"></a>ギャラリーからの DocuSign の追加

Azure AD への DocuSign の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に DocuSign を追加する必要があります。

**ギャラリーから DocuSign を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

4. 検索ボックスに「**DocuSign**」と入力し、結果ウィンドウで **DocuSign** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果一覧の DocuSign](./media/docusign-tutorial/tutorial_docusign_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、DocuSign で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する DocuSign ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと DocuSign の関連ユーザーの間で、リンク関係が確立されている必要があります。

DocuSign で Azure AD のシングル サインオンを構成してテストするには、次の手順を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[DocuSign テスト ユーザーの作成](#creating-a-docusign-test-user)** - DocuSign で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、DocuSign アプリケーションでシングル サインオンを構成します。

**DocuSign で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **DocuSign** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

2. **[シングル サインオン方式の選択]** ダイアログで、**[SAML]** モードの **[選択]** をクリックして、シングル サインオンを有効にします。

    ![Configure single sign-on](common/tutorial_general_301.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、**[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![Configure single sign-on](common/editconfigure.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    ![[DocuSign のドメインと URL] のシングル サインオン情報](./media/docusign-tutorial/tutorial_docusign_url.png)

    a. **[サインオン URL]** ボックスに、`https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2/login/sp/<IDPID>` のパターンを使用して URL を入力します。

    b. **[識別子]** ボックスに、`https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2` の形式で URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 実際のサインオン URL と識別子に値を置き換えます。実際の値については、このチュートリアルの **[View SAML 2.0 Endpoints]\(SAML 2.0 エンドポイントの表示\)** に関するセクションで説明します。

5. **[SAML 署名証明書]** ページの **[SAML 署名証明書]** セクションで、**[ダウンロード]** をクリックして**証明書 (Base64)** をダウンロードし、証明書ファイルをコンピューターに保存します。

    ![証明書のダウンロードのリンク](./media/docusign-tutorial/tutorial_docusign_certificate.png) 

6. **[DocuSign の設定]** セクションで、要件に従って適切な URL をコピーします。

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

    ![DocuSign の構成](common/configuresection.png)

7. 別の Web ブラウザー ウィンドウで、**DocuSign 管理者ポータル**に管理者としてログインします。

8. ページの右上にあるプロファイル **ロゴ**をクリックし、**[Go to Admin]\(管理に移動\)** をクリックします。
  
    ![シングル サインオンの構成][51]

9. ドメインのソリューション ページで **[Domains]\(ドメイン\)** をクリックします。

    ![シングル サインオンの構成][50]

10. **[Domains]\(ドメイン\)** セクションの **[CLAIM DOMAIN]\(ドメインの要求\)** をクリックします。

    ![シングル サインオンの構成][52]

11. **[Claim a domain]\(ドメインの要求\)** ダイアログの **[Domain Name]\(ドメイン名\)** ボックスに、所属する会社のドメインを入力してから、**[CLAIM]\(要求\)** をクリックします。 ドメインを確認し、状態がアクティブであることを確かめてください。

    ![シングル サインオンの構成][53]

12. ドメインのソリューション ページで **[Identity Providers]\(ID プロバイダー\)** をクリックします。
  
    ![シングル サインオンの構成][54]

13. **[Identity Provider]\(ID プロバイダー\)** セクションで、**[ADD IDENTITY PROVIDER]\(ID プロバイダー\)** をクリックします。 

    ![シングル サインオンの構成][55]

14. **[Identity Provider Settings (ID プロバイダーの設定)]** ページで、次の手順を実行します。

    ![シングル サインオンの構成][56]

    a. **[Name (名前)]** ボックスに、構成の一意の名前を入力します。 スペースは使用しないでください。

    b. **[ID プロバイダーの発行者]** ボックスに、Azure portal からコピーした **Azure AD 識別子**の値を貼り付けます。

    c. **[Identity Provider Login URL]\(ID プロバイダーのログイン URL\)** テキスト ボックスに、Azure portal からコピーした**ログイン URL** の値を貼り付けます。

    d. **[Identity Provider Logout URL]\(ID プロバイダーのログアウト URL\)** テキスト ボックスに、Azure portal からコピーした**ログアウト URL** の値を貼り付けます。

    e. **[Sign AuthN request]\(AuthN 要求に署名する\)** を選択します。

    f. **[Send AuthN request by (認証要求の送信方法)]** として、**[POST]** を選択します。

    g. **[Send logout request by]\(ログアウト要求の送信方法\)** として、**[GET]** を選択します。

    h. **[Custom Attribute Mapping]\(カスタム属性のマッピング\)** セクションの **[ADD NEW MAPPING]\(新しいマッピングの追加\)** をクリックします。

    ![シングル サインオンの構成][62]

    i. Azure AD の要求とマッピングするフィールドを選択します。 この例では、**emailaddress** 要求は **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress** の値とマップされます。 これは、Azure AD の電子メール要求の既定の要求名です。**[SAVE]\(保存\)** をクリックします。

    ![シングル サインオンの構成][57]

    > [!NOTE]
    > Azure AD のユーザーを DocuSign のユーザー マッピングにマッピングする際は、適切な**ユーザー識別子**を使用してください。 適切なフィールドを選択し、組織の設定に基づく適切な値を入力してください。

    j. **[Identity Provider Certificate]\(ID プロバイダー証明書\)** セクションで **[ADD CERTIFICATE]\(証明書の追加\)** をクリックし、Azure AD ポータルからダウンロードした証明書をアップロードし、**[SAVE]\(保存\)** をクリックします。

    ![シングル サインオンの構成][58]

    k. **[Identity Providers]\(ID プロバイダー\)** セクションで、**[ACTIONS]\(アクション\)** をクリックし、**[Endpoints]\(エンドポイント\)** をクリックします。

    ![シングル サインオンの構成][59]

    l. **DocuSign 管理者ポータル**の **[View SAML 2.0 Endpoints]\(SAML 2.0 エンドポイントの表示\)** セクションで、次の手順を実行します。

    ![シングル サインオンの構成][60]

    * **[Service Provider Issuer URL]\(サービス プロバイダー発行者 URL\)** をコピーし、Azure portal の **[DocuSign のドメインと URL]** セクションの **[識別子]** ボックスに貼り付けます。

    * **[Service Provider Login URL]\(サービス プロバイダーのログイン URL\)** をコピーし、Azure portal の **[DocuSign のドメインと URL]** セクションの **[サインオン URL]** ボックスに貼り付けます。

    * **[閉じる]** をクリックします。

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

### <a name="creating-a-docusign-test-user"></a>DocuSign テスト ユーザーの作成

このセクションの目的は、DocuSign で Britta Simon というユーザーを作成することです。 DocuSign では、Just-In-Time プロビジョニングがサポートされています。この設定は、既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 存在しない DocuSign ユーザーにアクセスしようとすると、新しいユーザーが自動的に作成されます。
>[!Note]
>ユーザーを手動で作成する必要がある場合は、 [DocuSign サポート チーム](https://support.docusign.com/)にお問い合わせください。

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に DocuSign へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、**[すべてのアプリケーション]** を選択します。

    ![ユーザーの割り当て][201]

2. アプリケーションの一覧で **[DocuSign]** を選択します。

    ![Configure single sign-on](./media/docusign-tutorial/tutorial_docusign_app.png)

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202]

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. **[割り当ての追加]** ダイアログで、**[割り当て]** ボタンを選択します。

### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [DocuSign] タイルをクリックすると、自動的に DocuSign アプリケーションにサインオンします。
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
[50]: ./media/docusign-tutorial/tutorial_docusign_18.png
[51]: ./media/docusign-tutorial/tutorial_docusign_21.png
[52]: ./media/docusign-tutorial/tutorial_docusign_22.png
[53]: ./media/docusign-tutorial/tutorial_docusign_23.png
[54]: ./media/docusign-tutorial/tutorial_docusign_19.png
[55]: ./media/docusign-tutorial/tutorial_docusign_20.png
[56]: ./media/docusign-tutorial/tutorial_docusign_24.png
[57]: ./media/docusign-tutorial/tutorial_docusign_25.png
[58]: ./media/docusign-tutorial/tutorial_docusign_26.png
[59]: ./media/docusign-tutorial/tutorial_docusign_27.png
[60]: ./media/docusign-tutorial/tutorial_docusign_28.png
[61]: ./media/docusign-tutorial/tutorial_docusign_29.png
[62]: ./media/docusign-tutorial/tutorial_docusign_30.png
