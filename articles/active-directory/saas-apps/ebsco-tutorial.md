---
title: 'チュートリアル: Azure Active Directory と EBSCO の統合 | Microsoft Docs'
description: Azure Active Directory と EBSCO の間にシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 144f7f65-69e9-4016-a151-fe1104fd6ba8
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2018
ms.author: jeedes
ms.openlocfilehash: 5ecb0e87d45cc01b65c91ee4c5c9d29806999269
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39431683"
---
# <a name="tutorial-azure-active-directory-integration-with-ebsco"></a>チュートリアル: Azure Active Directory と EBSCO の統合

このチュートリアルでは、EBSCO と Azure Active Directory (Azure AD) を統合する方法について説明します。

EBSCO と Azure AD の統合には、次の利点があります。

- EBSCO にアクセスできる Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に EBSCO にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

EBSCO と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- EBSCO でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの EBSCO の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-ebsco-from-the-gallery"></a>ギャラリーからの EBSCO の追加
Azure AD への EBSCO の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に EBSCO を追加する必要があります。

**ギャラリーから EBSCO を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

1. 検索ボックスに「**EBSCO**」と入力し、結果パネルで **[EBSCO]** を選び、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果一覧の EBSCO](./media/ebsco-tutorial/tutorial_ebsco_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、EBSCO で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する EBSCO ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと EBSCO の関連ユーザーの間で、リンク関係が確立されている必要があります。

EBSCO で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[EBSCO のテスト ユーザーの作成](#create-an-ebsco-test-user)** - EBSCOhost ユーザーのプロビジョニング/パーソナル化を自動化できます。 EBSCO では、Just-In-Time ユーザー プロビジョニングがサポートされています。
1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、EBSCO アプリケーションでシングル サインオンを構成します。

**EBSCO で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **EBSCO** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/ebsco-tutorial/tutorial_ebsco_samlbase.png)

1. **[EBSCO Domain and URLs]\(EBSCO のドメインと URL\)** セクションで、**IDP 開始モード**でアプリケーションを構成する場合は、次の手順に従います。

    ![EBSCO のドメインと URL のシングル サインオン情報](./media/ebsco-tutorial/tutorial_ebsco_url.png)

    **[識別子]** ボックスに次の URL を入力します。`pingsso.ebscohost.com`

1. アプリケーションを **SP** 開始モードで構成する場合は、**[詳細な URL 設定の表示]** チェックボックスをオンにして次の手順を実行します。

    ![EBSCO のドメインと URL のシングル サインオン情報](./media/ebsco-tutorial/tutorial_ebsco_url1.png)

    **[サインオン URL]** ボックスに、`http://search.ebscohost.com/login.aspx?authtype=sso&custid=<unique EBSCO customer ID>&profile=<profile ID>` のパターンを使用して URL を入力します。
     
    > [!NOTE] 
    > サインオン URL は実際の値ではありません。 実際のサインオン URL でこの値を更新してください。 この値を取得するには、[EBSCO クライアント サポート チーム](mailto:sso@ebsco.com)に問い合わせてください。 

    o   **一意の要素:**  

    o   **[Custid]** = 一意の EBSCO カスタマー ID を入力 

    o   **[プロファイル]** = クライアントはリンクを調整して、ユーザーを (EBSCO から購入したものに基づいて) 特定のプロファイルに転送できます。 特定のプロファイル ID を入力できます。 メインの ID は eds (EBSCO Discovery Service) と ehost (EBSOCOhost データベース) です。 同じことに関する手順が[こちら](https://help.ebsco.com/interfaces/EBSCOhost/EBSCOhost_FAQs/How_do_I_set_up_direct_links_to_EBSCOhost_profiles_and_or_databases#profile)にあります。

1. EBSCO アプリケーションは、特定の形式で構成された SAML アサーションを受け入れます。 このアプリケーションには、次の要求を構成します。 この属性の値は、アプリケーション統合ページの **[User Attributer]** セクションで管理できます。 次のスクリーンショットはその例です。
    
    ![Configure single sign-on](./media/ebsco-tutorial/tutorial_ebsco_attribute.png)

    > [!Note]
    > **名前**属性は必須であり、EBSCO アプリケーションの**ユーザー識別子**と共にマッピングされます。 これは既定で追加されるため、手動で追加する必要はありません。
    
1. **[シングル サインオン]** ダイアログの **[ユーザー属性]** セクションで、上の図に示すように SAML トークン属性を構成し、次の手順を実行します。
    
    | 属性名 | 属性値 |
    | ---------------| --------------- |    
    | FirstName   | User.givenname |
    | LastName   | User.surname |
    | 電子メール   | User.mail |

    a. **[属性の追加]** をクリックして **[属性の追加]** ダイアログを開きます。

    ![Configure single sign-on](./media/ebsco-tutorial/tutorial_officespace_04.png)

    ![Configure single sign-on](./media/ebsco-tutorial/tutorial_attribute_05.png)
    
    b. **[名前]** ボックスに、その行に対して表示される属性名を入力します。
    
    c. **[値]** 一覧から、その行に対して表示される値を入力します。
    
    d. **[OK]** をクリックします。

1. **[SAML 署名証明書]** セクションで、**[Metadata XML (メタデータ XML)]** をクリックし、コンピューターにメタデータ ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/ebsco-tutorial/tutorial_ebsco_certificate.png) 

1. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/ebsco-tutorial/tutorial_general_400.png)
    
1. **EBSCO** 側にシングル サインオンを構成するには、ダウンロードした**メタデータ XML** を [EBSCO サポート チーム](mailto:sso@ebsco.com)に送信する必要があります。 サポート チームはこれを設定して、SAML SSO 接続が両方の側で正しく設定されるようにします。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関するページを参照してください。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/ebsco-tutorial/create_aaduser_01.png)

1. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/ebsco-tutorial/create_aaduser_02.png)

1. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/ebsco-tutorial/create_aaduser_03.png)

1. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/ebsco-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。
 
### <a name="create-an-ebsco-test-user"></a>EBSCO テスト ユーザーの作成

EBSCO の場合、ユーザー プロビジョニングは自動的に行われます。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

Azure AD によって必要なデータが EBSCO アプリケーションに渡されます。 EBSCO のユーザー プロビジョニングは自動であるか、1 回限りのフォームが要求されます。 これは、個人設定が保存された、多数の既存の EBSCOhost アカウントをクライアントが持っているかどうかによって異なります。 実装中、[EBSCO サポート チーム](mailto:sso@ebsco.com)についても同じことが考えられます。 どちらの場合でも、テスト前にクライアントが EBSCOhost アカウントを作成する必要はありません。

   >[!Note]
   >EBSCOhost ユーザーのプロビジョニング/パーソナル化を自動化できます。 Just-In-Time ユーザー プロビジョニングについて、[EBSCO サポート チーム](mailto:sso@ebsco.com)にお問い合わせください。 
 
### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に EBSCO へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**EBSCO に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[EBSCO]** を選択します。

    ![アプリケーションの一覧の [EBSCO] リンク](./media/ebsco-tutorial/tutorial_ebsco_app.png)  

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

1. アクセス パネルで EBSCO のタイルをクリックすると、EBSCO アプリケーションに自動的にサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。

1. アプリケーションにログインしたら、右上隅の **[サインイン]** ボタンをクリックします。

    ![アプリケーションの一覧の EBSCO サインイン](./media/ebsco-tutorial/tutorial_ebsco_signin.png)
 
1. 組織ログインまたは SAML ログインを、**[Link your existing MyEBSCOhost account to your institution account now]\(今すぐ既存の MyEBSCOhost アカウントを組織アカウントにリンクする\)** または **[Create a new MyEBSCOhost account and link it to your institution account]\(新しい MyEBSCOhost アカウントを作成して組織アカウントにリンクする\)** に関連付けることを求める 1 回限りのプロンプトが表示されます。 アカウントは、EBSCOhost アプリケーションのパーソナル化に使用されます。 **[新しいアカウントの作成]** オプションを選択すると、次のスクリーンショットのように、SAML 応答の値が事前入力されたパーソナル化用のフォームが表示されます。 **[続行]** をクリックしてこの選択を保存します。
    
     ![アプリケーションの一覧の EBSCO ユーザー](./media/ebsco-tutorial/tutorial_ebsco_user.png)

1. 以上の設定を完了したら、Cookie/キャッシュをクリアして再びログインします。 これで再び手動でサインインする必要はなくなり、パーソナル化の設定は保持されます。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/ebsco-tutorial/tutorial_general_01.png
[2]: ./media/ebsco-tutorial/tutorial_general_02.png
[3]: ./media/ebsco-tutorial/tutorial_general_03.png
[4]: ./media/ebsco-tutorial/tutorial_general_04.png

[100]: ./media/ebsco-tutorial/tutorial_general_100.png

[200]: ./media/ebsco-tutorial/tutorial_general_200.png
[201]: ./media/ebsco-tutorial/tutorial_general_201.png
[202]: ./media/ebsco-tutorial/tutorial_general_202.png
[203]: ./media/ebsco-tutorial/tutorial_general_203.png

