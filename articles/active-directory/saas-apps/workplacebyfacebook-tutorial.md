---
title: 'チュートリアル: Azure Active Directory と Workplace by Facebook の統合 | Microsoft Docs'
description: Azure Active Directory と Workplace by Facebook の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 30f2ee64-95d3-44ef-b832-8a0a27e2967c
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/30/2018
ms.author: jeedes
ms.openlocfilehash: 1f83dd64c7f6773ddb8956e6ebbc37b8c55aacec
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39423873"
---
# <a name="tutorial-azure-active-directory-integration-with-workplace-by-facebook"></a>チュートリアル: Azure Active Directory と Workplace by Facebook の統合

このチュートリアルでは、Workplace by Facebook と Azure Active Directory (Azure AD) を統合する方法について説明します。

Workplace by Facebook と Azure AD の統合には、次の利点があります。

- Workplace by Facebook にアクセスする Azure AD ユーザーを制御できます
- ユーザーが自分の Azure AD アカウントで自動的に Workplace by Facebook にサインオン (シングル サインオン) できるようにします
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Azure AD と Workplace by Facebook の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Workplace by Facebook でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

> [!NOTE]
> Facebook には、Workplace Standard (無料) と Workplace Premium (有料) の 2 つの製品があります。 すべての Workplace Premium テナントは、他のコストやライセンスを必要とせずに、SCIM と SSO の統合を構成できます。 SSO と SCIM は、Workplace Standard インスタンスでは利用できません。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Workplace by Facebook の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-workplace-by-facebook-from-the-gallery"></a>ギャラリーからの Workplace by Facebook の追加
Azure AD への Workplace by Facebook の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Workplace by Facebook を追加する必要があります。

**ギャラリーから Workplace by Facebook を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[アプリケーション]][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[アプリケーション]][3]

1. 検索ボックスに、「**Workplace by Facebook**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/workplacebyfacebook-tutorial/tutorial_workplacebyfacebook_search.png)

1. 結果ウィンドウで **[Workplace by Facebook]** を選択し、**[追加]** をクリックしてアプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/workplacebyfacebook-tutorial/tutorial_workplacebyfacebook_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Workplace by Facebook で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Workplace by Facebook ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Workplace by Facebook の関連ユーザーの間で、リンク関係が確立されている必要があります。

このリンク関係は、Azure AD の **[ユーザー名]** の値を、Workplace by Facebook の **[Username]\(ユーザー名\)** の値として割り当てることで確立されます。

Workplace by Facebook で Azure AD のシングル サインオンを構成してテストするには、次の一連の作業を完了させる必要があります。

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[再認証の頻度の構成](#configuring-reauthentication-frequency)** - Workplace で SAML チェックを要求するよう構成します。
1. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[Workplace by Facebook テスト ユーザーの作成](#creating-a-workplace-by-facebook-test-user)** - Workplace by Facebook で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、Workplace by Facebook アプリケーションでシングル サインオンを構成します。

**Workplace by Facebook で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Workplace by Facebook** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![Configure single sign-on][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![Configure single sign-on](./media/workplacebyfacebook-tutorial/tutorial_workplacebyfacebook_samlbase.png)

1. **[Workplace by Facebook のドメインと URL]** セクションで、次の手順を実行します。

    ![Configure single sign-on](./media/workplacebyfacebook-tutorial/tutorial_workplacebyfacebook_url.png)

    a. **[サインオン URL]** ボックスに、`https://<instancename>.facebook.com` のパターンを使用して URL を入力します。

    b. **[識別子]** ボックスに、`https://www.facebook.com/company/<instanceID>` の形式で URL を入力します。

    > [!NOTE] 
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新してください。 Workplace コミュニティの適切な値については、Workplace Company Dashboard の認証ページを参照してください。 

1. **[SAML 署名証明書]** セクションで、**[Certificate (Base64) (証明書 (Base64)) ]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![Configure single sign-on](./media/workplacebyfacebook-tutorial/tutorial_workplacebyfacebook_certificate.png) 

1. **[保存]** ボタンをクリックします。

    ![Configure single sign-on](./media/workplacebyfacebook-tutorial/tutorial_general_400.png)

1. **[Workplace by Facebook 構成]** セクションで、**[Workplace by Facebook の構成する]** をクリックして **[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから、**サインアウト URL、SAML エンティティ ID、SAML シングル サインオン サービス URL** をコピーします。

    ![Configure single sign-on](./media/workplacebyfacebook-tutorial/config.png) 

1. 別の Web ブラウザーのウィンドウで、Workplace by Facebook 企業サイトに管理者としてログインします。
  
   > [!NOTE] 
   > SAML 認証プロセスの一環として、Azure AD にパラメーターを渡すために Workplace が最大サイズ 2.5 KBのクエリ文字列を使用する可能性があります。

1. **[Company Dashboard]\(会社のダッシュボード\)** で、**[Authentication]\(認証\)** タブに移動します。

1. **[SAML Authentication]\(SAML 認証\)** で、ドロップダウン リストの一覧から **[SSO Only]\(SSO のみ\)** を選択します。

1. Azure Portal の **[Workplace by Facebook の構成]** セクションからコピーした値を対応するフィールドに入力します。

    *   **[SAML URL]** ボックスに、Azure Portal からコピーした**シングル サインオン サービス URL** の値を貼り付けます。
    *   **[SAML Issuer URL]\(SAML 発行者のURL\)** ボックスに、Azure Portal からコピーした **SAML エンティティ ID** の値を貼り付けます。
    *   **[SAML Logout redirect]\(SAML ログアウト リダイレクト\)** (オプション) に、Azure Portal からコピーした**サインアウト URL** の値を貼り付けます。
    *   Azure Portal からダウンロードした **Base-64 でエンコードされた証明書**をメモ帳で開き、その内容をクリップボードにコピーしてから、それを **[SAML Certificate]\(SAML 証明書\)** ボックスに貼り付けます。

1. **[SAML Configuration]\(SAML の構成\)** セクションに一覧表示されている [Audience URL]、[Recipient URL]、[ACS (Assertion Consumer Service) URL] の入力が必要になる場合もあります。

1. セクションの下部にスクロールし、**[Test SSO]\(SSO のテスト\)** をクリックします。 これにより、Azure AD ログイン ページで表示されるポップアップ ウィンドウが表示されます。 通常どおり資格情報を入力して認証を行います。 

    **トラブルシューティング:** Azure AD から返された電子メール アドレスが、ログインに使用した Workplace アカウントと同じであることを確認してください。

1. テストが正常に完了したら、ページの下部にスクロールして **[保存]** をクリックします。

1. Workplace を使用しているすべてのユーザーに、認証用の Azure AD ログイン ページが表示されるようになりました。

1. **SAML ログアウト リダイレクト (オプション)** - 

    必要に応じて、Azure AD のログアウト ページの指定に使用される SAML ログアウト URL を構成できます。 この設定が有効に構成されている場合は、ユーザーに対して Workplace ログアウト ページが表示されなくなります。 代わりに、SAML ログアウト リダイレクトの設定に追加された URL にユーザーがリダイレクトされるようになります。

### <a name="configuring-reauthentication-frequency"></a>再認証の頻度の構成

SAML チェックの要求を毎日、3 日ごと、1 週間ごと、2 週間ごと、1 か月ごとに行う、または行わないように、Workplace を構成できます。

> [!NOTE] 
>モバイル アプリケーションで設定できる SAML チェック頻度の最小値は 1 週間です。

また、[Require SAML authentication for all users now]\(すべてのユーザーに SAML 認証を要求する\) ボタンを使用して、すべてのユーザーに SAML の再設定を強制できます。


### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/workplacebyfacebook-tutorial/create_aaduser_01.png) 

1. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/workplacebyfacebook-tutorial/create_aaduser_02.png) 

1. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/workplacebyfacebook-tutorial/create_aaduser_03.png) 

1. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/workplacebyfacebook-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **Create** をクリックしてください。
 
### <a name="creating-a-workplace-by-facebook-test-user"></a>Workplace by Facebook テスト ユーザーの作成

このセクションでは、Britta Simon というユーザーを Workplace by Facebook に作成します。 Workplace by Facebook では、Just-In-Time プロビジョニングがサポートされています。この設定は既定で有効になっています。

このセクションでは、ユーザー側で必要な操作はありません。 Workplace by Facebook にユーザーが 1 人もいない場合は、Workplace by Facebook にアクセスしようとしたときに新しいユーザーが作成されます。

>[!Note]
>ユーザーを手動で作成する必要がある場合は、[Workplace by Facebook クライアント サポート チーム](https://workplace.fb.com/faq/)にお問い合わせください。

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Workplace by Facebook へのアクセスを許可することで、Britta Simon が Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Britta Simon を Workplace by Facebook に割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[Workplace by Facebook]** を選択します。

    ![Configure single sign-on](./media/workplacebyfacebook-tutorial/tutorial_workplacebyfacebook_app.png) 

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。


## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)
* [[ユーザー プロビジョニングの構成]](workplacebyfacebook-provisioning-tutorial.md)



<!--Image references-->

[1]: ./media/workplacebyfacebook-tutorial/tutorial_general_01.png
[2]: ./media/workplacebyfacebook-tutorial/tutorial_general_02.png
[3]: ./media/workplacebyfacebook-tutorial/tutorial_general_03.png
[4]: ./media/workplacebyfacebook-tutorial/tutorial_general_04.png

[100]: ./media/workplacebyfacebook-tutorial/tutorial_general_100.png

[200]: ./media/workplacebyfacebook-tutorial/tutorial_general_200.png
[201]: ./media/workplacebyfacebook-tutorial/tutorial_general_201.png
[202]: ./media/workplacebyfacebook-tutorial/tutorial_general_202.png
[203]: ./media/workplacebyfacebook-tutorial/tutorial_general_203.png
