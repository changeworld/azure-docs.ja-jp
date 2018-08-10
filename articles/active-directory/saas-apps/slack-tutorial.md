---
title: 'チュートリアル: Azure Active Directory と Slack の統合 | Microsoft Docs'
description: Azure Active Directory とSlack の間にシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: ffc5e73f-6c38-4bbb-876a-a7dd269d4e1c
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: jeedes
ms.openlocfilehash: 8f79926d0d4729c6ad939bc604e9eb885dbe9f03
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39421259"
---
# <a name="tutorial-azure-active-directory-integration-with-slack"></a>チュートリアル: Azure Active Directory と Slack の統合

このチュートリアルでは、Slack と Azure Active Directory (Azure AD) を統合する方法について説明します。

Slack と Azure AD の統合には、次の利点があります。

- Slack にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Slack にサインオンする機能 (シングル サインオン) を有効にできます。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Slack と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Slack でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Slack の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-slack-from-the-gallery"></a>ギャラリーからの Slack の追加
Azure AD への Slack の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Slack を追加する必要があります。

**ギャラリーから Slack を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[アプリケーション]][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[アプリケーション]][3]

1. 検索ボックスに「**Slack**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/slack-tutorial/tutorial_slack_search.png)

1. 結果ウィンドウで **[Slack]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/slack-tutorial/tutorial_slack_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Slack で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Slack ユーザーが Azure AD で認識されている必要があります。 つまり、Azure AD ユーザーと Slack の関連ユーザーの間でリンク関係が確立されている必要があるということです。

Slack で、Azure AD の **[ユーザー名]** の値を **[Username]** の値として割り当ててリンク関係を確立します。

Slack で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[Slack のテスト ユーザーの作成](#creating-a-slack-test-user)** - Slack で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクします。
1. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、Slack アプリケーションにシングル サインオンを構成します。

**Slack で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Slack** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![Configure single sign-on][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![Configure single sign-on](./media/slack-tutorial/tutorial_slack_samlbase.png)

1. **[Slack のドメインと URL]** セクションで、次の手順を実行します。

    ![Configure single sign-on](./media/slack-tutorial/tutorial_slack_url.png)

    a. **[サインオン URL]** ボックスに、`https://<companyname>.slack.com` のパターンを使用して URL を入力します。

    b. **[識別子]** ボックスに次の URL を入力します。`https://slack.com`

    > [!NOTE] 
    > この値は実際のものではありません。 この値は実際のサインオン URL で更新する必要があります。 この値を取得するには、[Slack サポート チーム](https://slack.com/help/contact)にお問い合わせください。
     
1. Slack アプリケーションは、特定の形式で構成された SAML アサーションを受け入れます。 このアプリケーションには、次の要求を構成します。 この属性の値は、アプリケーション統合ページの **[User Attributer]** セクションで管理できます。 次のスクリーンショットはその例です。
    
    ![Configure single sign-on](./media/slack-tutorial/tutorial_slack_attribute.png)

    > [!NOTE] 
    > 割り当てられた**メール アドレス**が Office 365 ライセンスのものではないユーザーがいる場合、**User.Email** 要求は SAML トークンに表示されません。 その場合は、**user.userprincipalname** を **User.Email** 属性の値として使って、代わりに**一意識別子**としてマップすることをお勧めします。

1. **[シングル サインオン]** ダイアログの **[ユーザー属性]** セクションで、**[ユーザー識別子]** として **[user.mai]l** を選択し、以下の表に示す行ごとに、次の手順を実行します。
    
    | 属性名 | 属性値 |
    | --- | --- |
    | first_name | User.givenname |
    | last_name | User.surname |
    | User.Email | User.mail |  
    | User.Username | user.userprincipalname |

    a. **[属性]** をクリックして **[属性の編集]** ダイアログ ボックスを開き、次の手順を実行します。

    ![Configure single sign-on](./media/slack-tutorial/tutorial_slack_attribute1.png)

    a. **[名前]** ボックスに、その行に対して表示される属性名を入力します。

    b. **[値]** 一覧から、その行に対して表示される属性値を選択します。

    c. **[名前空間]** は空白のままにします。

    d. **[OK]**

1. **[SAML 署名証明書]** セクションで、**[Certificate (Base64) (証明書 (Base64)) ]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![Configure single sign-on](./media/slack-tutorial/tutorial_slack_certificate.png)

1. **[保存]** ボタンをクリックします。

    ![Configure single sign-on](./media/slack-tutorial/tutorial_general_400.png)

1. **[Slack Configuration]** (Slack 構成) セクションで、**[Configure Slack]** (Slack を構成する) をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから **SAML エンティティ ID と SAML シングル サインオン サービス URL** をコピーします。

    ![Configure single sign-on](./media/slack-tutorial/tutorial_slack_configure.png)

1. 別の Web ブラウザー ウィンドウで、Slack 企業サイトに管理者としてログインします。

1. **[Microsoft Azure AD]**、**[Team Settings]** の順に選択します。

     ![アプリ側でのシングル サインオンの構成](./media/slack-tutorial/tutorial_slack_001.png)

1. **[Team Settings]** セクションで、**[Authentication]** タブをクリックし、**[Change Settings]** をクリックします。

    ![アプリ側でのシングル サインオンの構成](./media/slack-tutorial/tutorial_slack_002.png)

1. **[SAML Authentication Settings]** ダイアログで、次の手順を実行します。

    ![アプリ側でのシングル サインオンの構成](./media/slack-tutorial/tutorial_slack_003.png)

    a.  **[SAML 2.0 エンドポイント (HTTP)]** ボックスに、Azure Portal からコピーした **SAML シングル サインオン サービス URL** の値を貼り付けます。

    b.  **[ID プロバイダーの発行者]** ボックスに、Azure Portal からコピーした **SAML エンティティ ID** の値を貼り付けます。

    c.  ダウンロードした証明書をメモ帳で開き、その内容をクリップボードにコピーし、**[Public Certificate]** ボックスに貼り付けます。

    d. 必要に応じて、Slack チームに上記の 3 つの設定を構成します。 設定の詳細については、**Slack の SSO 構成ガイド**をこちらから参照してください。 `https://get.slack.help/hc/articles/220403548-Guide-to-single-sign-on-with-Slack%60`

    e.  **[Save Configuration]** をクリックします。

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/slack-tutorial/create_aaduser_01.png) 

1. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/slack-tutorial/create_aaduser_02.png) 

1. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。

    ![Azure AD のテスト ユーザーの作成](./media/slack-tutorial/create_aaduser_03.png)

1. **[ユーザー]** ダイアログ ページで、次の手順を実行します。

    ![Azure AD のテスト ユーザーの作成](./media/slack-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **Create** をクリックしてください。

### <a name="creating-a-slack-test-user"></a>Slack テスト ユーザーの作成

このセクションの目的は、Slack で Britta Simon というユーザーを作成することです。 Slack では、Just-In-Time プロビジョニングがサポートされています。この設定は、既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 存在しない Slack ユーザーにアクセスしようとすると、新しいユーザーが自動的に作成されます。 Slack はまた、 自動ユーザー プロビジョニングをサポートしています。自動ユーザー プロビジョニングの構成方法について詳しくは、[こちら](slack-provisioning-tutorial.md)をご覧ください。

> [!NOTE]
> ユーザーを手動で作成する必要がある場合は、[Slack のサポート チーム](https://slack.com/help/contact)にお問い合わせください。

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Slack へのアクセスを許可することで、Britta Simon が Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200]

**Slack に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[Slack]** を選択します。

    ![Configure single sign-on](./media/slack-tutorial/tutorial_slack_app.png) 

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Slack] タイルをクリックすると、Slack アプリケーションに自動的にサインオンします。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)
* [[ユーザー プロビジョニングの構成]](slack-provisioning-tutorial.md)


<!--Image references-->

[1]: ./media/slack-tutorial/tutorial_general_01.png
[2]: ./media/slack-tutorial/tutorial_general_02.png
[3]: ./media/slack-tutorial/tutorial_general_03.png
[4]: ./media/slack-tutorial/tutorial_general_04.png

[100]: ./media/slack-tutorial/tutorial_general_100.png

[200]: ./media/slack-tutorial/tutorial_general_200.png
[201]: ./media/slack-tutorial/tutorial_general_201.png
[202]: ./media/slack-tutorial/tutorial_general_202.png
[203]: ./media/slack-tutorial/tutorial_general_203.png
