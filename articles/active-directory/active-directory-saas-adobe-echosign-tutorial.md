---
title: 'チュートリアル: Azure Active Directory と Adobe Sign の統合 | Microsoft Docs'
description: Azure Active Directory と Adobe Sign の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: f9385723-8fe7-4340-8afb-1508dac3e92b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2018
ms.author: jeedes
ms.openlocfilehash: c3b7e7178ef68475f331edf058ca0f23661af3ea
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/20/2018
ms.locfileid: "34338875"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-sign"></a>チュートリアル: Azure Active Directory と Adobe Sign の統合

このチュートリアルでは、Adobe Sign と Azure Active Directory (Azure AD) を統合する方法について説明します。

Adobe Sign と Azure AD の統合には、次の利点があります。

- Adobe Sign にアクセスする Azure AD ユーザーを制御できます
- ユーザーが自分の Azure AD アカウントで自動的に Adobe Sign にサインオン (シングル サインオン) できるようにします
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Adobe Sign と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Adobe Sign でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Adobe Sign の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-adobe-sign-from-the-gallery"></a>ギャラリーからの Adobe Sign の追加
Azure AD への Adobe Sign の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Adobe Sign を追加する必要があります。

**ギャラリーから Adobe Sign を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[アプリケーション]][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[アプリケーション]][3]

4. [検索] ボックスに、「**Adobe Sign**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-adobe-echosign-tutorial/tutorial_adobesign_search.png)

5. 結果ウィンドウで **[Adobe Sign]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-adobe-echosign-tutorial/tutorial_adobesign_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Adobe Sign で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Adobe Sign ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Adobe Sign の関連ユーザーの間で、リンク関係が確立されている必要があります。

Adobe Sign で、Azure AD の **[ユーザー名]** の値を **[Username]** の値として割り当ててリンク関係を確立します。

Adobe Sign で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Adobe Sign テスト ユーザーの作成](#creating-an-adobe-sign-test-user)** - Adobe Sign で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、Adobe Sign アプリケーションでシングル サインオンを構成します。

**Adobe Sign で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Adobe Sign** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![[Configure Single Sign-On]][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。

    ![[Configure Single Sign-On]](./media/active-directory-saas-adobe-echosign-tutorial/tutorial_adobesign_samlbase.png)

3. **[Adobe Sign のドメインと URL]** セクションで、次の手順を実行します。

    ![[Configure Single Sign-On]](./media/active-directory-saas-adobe-echosign-tutorial/tutorial_adobesign_url.png)

    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが RightScale アプリケーションへのサインオンに使用する URL を入力します。 **[サインオン URL]** ボックスに、`https://<companyname>.echosign.com/` のパターンを使用して URL を入力します。

    b. **[識別子]** ボックスに、`https://<companyname>.echosign.com` の形式で URL を入力します。

    > [!NOTE] 
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新してください。 これらの値を取得するには、[Adobe Sign クライアント サポート チーム](https://helpx.adobe.com/in/contact/support.html)に問い合わせください。

4. **[SAML 署名証明書]** セクションで、**[証明書 (Base64)]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![[Configure Single Sign-On]](./media/active-directory-saas-adobe-echosign-tutorial/tutorial_adobesign_certificate.png) 

5. **[保存]** ボタンをクリックします。

    ![[Configure Single Sign-On]](./media/active-directory-saas-adobe-echosign-tutorial/tutorial_general_400.png)

6. **[Adobe Sign Configuration (Adobe Sign 構成)]** セクションで、**[Configure Adobe Sign (Adobe Sign を構成する)]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから、**サインアウト URL、SAML エンティティ ID、SAML シングル サインオン サービス URL** をコピーします。

    ![[Configure Single Sign-On]](./media/active-directory-saas-adobe-echosign-tutorial/tutorial_adobesign_configure.png)

7. Adobe Sign で自分のドメインをホワイトリストに登録するには、構成する前に、[Adobe Sign クライアント サポート チーム](https://helpx.adobe.com/in/contact/support.html)に連絡する必要があります。 次の手順に従ってドメインを追加します。

    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが RightScale アプリケーションへのサインオンに使用する URL を入力します。 [Adobe Sign クライアント サポート チーム](https://helpx.adobe.com/in/contact/support.html)からランダムに生成されたトークンが送信されます。 ドメインの場合、トークンは **adobe-sign-verification= xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx** のようになります。

    b. DNS テキスト レコードで検証トークンを発行し、[Adobe Sign クライアント サポート チーム](https://helpx.adobe.com/in/contact/support.html)に通知する必要があります。
    
    > [!NOTE]
    > これには数日またはそれ以上かかる場合があります。 DNS への反映の遅延は、DNS で公開された値が 1 時間以上表示されない可能性があることを意味することに注意してください。 DNS テキスト レコードでこのトークンを発行する方法については、組織の IT 管理者が熟知しているものと想定しています。
    
    c. トークンが発行された後に、サポート チケットを通じて [Adobe Sign クライアント サポート チーム](https://helpx.adobe.com/in/contact/support.html)に通知すると、サポート チームがドメインを検証し、それをユーザーのアカウントに追加します。
    
    d. DNS レコードでトークンを発行するための一般的な手順

    * 自分のドメイン アカウントにログインする
    * DNS レコードを更新するためのページを検索する。 このページは、DNS 管理、ネーム サーバー管理、または詳細設定と呼ばれる場合があります。
    * 自分のドメインの TXT レコードを検索する。
    * Adobe から提供された完全なトークン値を使用して TXT レコードを追加する
    * 変更を保存します。

8. 別の Web ブラウザーのウィンドウで、管理者として Adobe Sign 企業サイトにログインします。

9. SAML メニューで、**[アカウント設定]** をクリックし、**[SAML 設定]** をクリックします。
   
    ![Account](./media/active-directory-saas-adobe-echosign-tutorial/ic789520.png "Account")

10. **[SAML 設定]** セクションで、次の手順に従います。
  
    ![SAML Settings](./media/active-directory-saas-adobe-echosign-tutorial/ic789521.png "SAML Settings")
   
    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが RightScale アプリケーションへのサインオンに使用する URL を入力します。 **[SAML モード]** として **[SAML Mandatory]** を選択します。
   
    b. **[Adobe Sign 資格情報を使用して、Adobe Sign アカウント管理者のログインを許可する]** を選択します。
   
    c. **[ユーザーの作成]** として、**[SAML を使用して認証されたユーザーを自動的に追加]** を選択します。

    d. Azure Portal からコピーした **SAML エンティティ ID** を **[Entity ID/Issuer URL]\(エンティティ ID/発行者の URL\)** ボックスに貼り付けます。
    
    e. Azure Portal からコピーした **SAML シングル サインオン サービス URL** を **[Login URL/SSO Endpoint]\(ログイン URL/SSO エンドポイント\)** テキストボックスに貼り付けます。
   
    f. Azure Portal からコピーした **サインアウト URL** を **[Logout URL/SLO Endpoint]\(ログアウト URL/SSO エンドポイント\)** テキストボックスに貼り付けます。

    g. ダウンロードした**証明書 (Base64)** ファイルをメモ帳で開き、その内容をクリップボードにコピーし、**[IdP Certificate]** (IdP 証明書) ボックスに貼り付けます。

    h. **[変更を保存]** をクリックします。

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-adobe-echosign-tutorial/create_aaduser_01.png) 

2. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-adobe-echosign-tutorial/create_aaduser_02.png) 

3. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-adobe-echosign-tutorial/create_aaduser_03.png) 

4. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-adobe-echosign-tutorial/create_aaduser_04.png) 

    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが RightScale アプリケーションへのサインオンに使用する URL を入力します。 **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **Create** をクリックしてください。
 
### <a name="creating-an-adobe-sign-test-user"></a>Adobe Sign のテスト ユーザーの作成

Azure AD ユーザーが Adobe Sign にログインできるようにするには、ユーザーを Adobe Sign にプロビジョニングする必要があります。 Adobe Sign の場合、プロビジョニングは手動で実行します。

>[!NOTE]
>他の Adobe Sign ユーザー アカウント作成ツールまたは Adobe Sign から提供されている API を使用して、AAD ユーザー アカウントをプロビジョニングできます。 

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. **Adobe Sign** 企業サイトに管理者としてログインします。

2. 上部のメニューで、**[アカウント]** をクリックしてから、左側のナビゲーション ウィンドウの **[ユーザーとグループ]** をクリックし、**[新しいユーザーの作成]** をクリックします。
   
    ![Account](./media/active-directory-saas-adobe-echosign-tutorial/ic789524.png "Account")
   
3. **[新しいユーザーの作成]** セクションで、次の手順に従います。
   
    ![Create User](./media/active-directory-saas-adobe-echosign-tutorial/ic789525.png "Create User")
   
    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが RightScale アプリケーションへのサインオンに使用する URL を入力します。 関連するテキスト ボックスに、プロビジョニングする有効な AAD アカウントの **[メール アドレス]**、**[名]**、**[姓]** を入力します。
   
    b. **[Create User]** をクリックします。

>[!NOTE]
>Azure Active Directory のアカウント所有者には、アカウントがアクティブになる前に、アカウント確認用のリンクを含む電子メールが送信されます。 

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Adobe Sign へのアクセスを許可することで、Britta Simon が Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Adobe Sign に Britta Simon を割り当てるには、次の手順を実行します。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[Adobe Sign]** を選択します。

    ![[Configure Single Sign-On]](./media/active-directory-saas-adobe-echosign-tutorial/tutorial_adobesign_app.png) 

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

アクセス パネルで Adobe Sign のタイルをクリックすると、自動的に Adobe Sign アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-adobe-echosign-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-adobe-echosign-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-adobe-echosign-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-adobe-echosign-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-adobe-echosign-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-adobe-echosign-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-adobe-echosign-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-adobe-echosign-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-adobe-echosign-tutorial/tutorial_general_203.png
