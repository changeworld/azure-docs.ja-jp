---
title: 'チュートリアル: Azure Active Directory と Salesforce Sandbox の統合 | Microsoft Docs'
description: Azure Active Directory と Salesforce Sandbox の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: ee54c39e-ce20-42a4-8531-da7b5f40f57c
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: jeedes
ms.openlocfilehash: 2e050b363db7ab1d226c5aa6fffefb17c218d377
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39424294"
---
# <a name="tutorial-azure-active-directory-integration-with-salesforce-sandbox"></a>チュートリアル: Azure Active Directory と Salesforce Sandbox の統合

このチュートリアルでは、Salesforce Sandbox と Azure Active Directory (Azure AD) を統合する方法について説明します。

Salesforce Sandbox と Azure AD の統合には、次の利点があります。

- Salesforce Sandbox にアクセスできる Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Salesforce Sandbox にサインオン (シングル サインオン) されるようにすることができます。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Azure AD と Salesforce Sandbox の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Salesforce Sandbox でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Salesforce Sandbox の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-salesforce-sandbox-from-the-gallery"></a>ギャラリーからの Salesforce Sandbox の追加
Azure AD への Salesforce Sandbox の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Salesforce Sandbox を追加する必要があります。

**ギャラリーから Salesforce Sandbox を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

1. 検索ボックスに「**Salesforce Sandbox**」と入力し、結果パネルで **[Salesforce Sandbox]** を選択し、**[追加]** ボタンをクリックして、アプリケーションを追加します。

    ![結果リスト内の Salesforce Sandbox](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Salesforce Sandbox で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Salesforce Sandbox ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Salesforce Sandbox の関連ユーザーの間で、リンク関係が確立されている必要があります。

Salesforce Sandbox で、Azure AD の **ユーザー名**の値を**ユーザー名**の値として割り当ててリンク関係を確立します。

Salesforce Sandbox で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[Salesforce Sandbox テスト ユーザーの作成](#create-a-salesforce-sandbox-test-user)** - Salesforce Sandbox で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、Salesforce Sandbox アプリケーションでシングル サインオンを構成します。

**Salesforce Sandbox で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Salesforce Sandbox** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_samlbase.png)

1. **[Salesforce Sandbox のドメインと URL]** セクションで、次の手順を実行します。

    ![Salesforce Sandbox Domain のドメインと URL のシングル サインオン情報](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_url.png)

    a. **[サインオン URL]** ボックスに、`https://<instancename>--Sandbox.<entityid>.my.salesforce.com` のパターンを使用して値を入力します。

    b. **[識別子]** ボックスに、`https://<instancename>--Sandbox.<entityid>.my.salesforce.com` の形式で値を入力します。
    
    > [!NOTE] 
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新してください。 これらの値を取得するには、[Salesforce クライアント サポート チーム](https://help.salesforce.com/support)に問い合わせてください。

1. **[SAML 署名証明書]** セクションで、**[証明書]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_certificate.png) 

1. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/salesforce-sandbox-tutorial/tutorial_general_400.png)

1. **[Salesforce Sandbox 構成]** セクションで、**[Salesforce Sandbox を構成する]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから、**SAML エンティティ ID と SAML シングル サインオン サービス URL** をコピーします。

    ![Configure single sign-on](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_configure.png) 

1. ブラウザーで新しいタブを開き、Salesforce Sandbox の管理者アカウントにログインします。

1. ページの右上隅の**設定アイコン**の下の **[Setup]\(セットアップ\)** をクリックします。

    ![Configure single sign-on](./media/salesforce-sandbox-tutorial/configure1.png)

1. ナビゲーション ウィンドウの **[SETTINGS]\(設定\)** まで下へスクロールし、**[Identity]\(ID\)** をクリックして、関連セクションを展開します。 次に、**[シングル サインオンの設定]** をクリックします。

    ![Configure single sign-on](./media/salesforce-sandbox-tutorial/sf-admin-sso.png)

1. **[SAML 有効]** を選択し、**[保存]** をクリックします。

    ![Configure single sign-on](./media/salesforce-sandbox-tutorial/sf-enable-saml.png)

1. SAML のシングル サインオン設定を構成するには、 **[新規]** をクリックします。

    ![Configure single sign-on](./media/salesforce-sandbox-tutorial/sf-admin-sso-new.png)

1. [SAML シングル サインオンの設定] セクションで、次の手順に従います。

    ![Configure single sign-on](./media/salesforce-sandbox-tutorial/sf-saml-config.png)

    a. **[名前]** テキストボックスに、構成の名前を入力します (例: *SPSSOWAAD_Test*)。 

    b. **[発行者]** フィールドに、Azure Portal からコピーした **SAML エンティティ ID** の値を貼り付けます

    c. これがディレクトリに追加する最初の Salesforce Sandbox インスタンスの場合は、**[エンティティ ID]** テキストボックスに「`https://<instancename>--Sandbox.<entityid>.my.salesforce.com`」と入力します。 Salesforce Sandbox のインスタンスを既に追加している場合は、**[Entity Id (エンティティ ID)]** に次の形式の**サインオン URL** を入力します。`https://<instancename>--Sandbox.<entityid>.my.salesforce.com`

    d. **ID プロバイダー証明書**をアップロードするには、**[Choose File]\(ファイルの選択\)** をクリックし、Azure Portal からダウンロードした証明書ファイルを参照して、選択します。

    e. **SAML ID タイプ**として、次のオプションのいずれかを選択します。

      * SAML アサーション内でユーザーの Salesforce ユーザー名が渡される場合は、**[Assertion contains the User's Salesforce username]\(アサーションにユーザーの Salesforce ユーザー名が含まれています\)** を選択します

      * SAML アサーション内でユーザー オブジェクトのフェデレーション ID が渡される場合は、**[Assertion contains the Federation ID from the User object]\(アサーションにユーザー オブジェクトのフェデレーション ID が含まれています\)** を選択します

      * SAML アサーション内でユーザー オブジェクトのユーザー ID が渡される場合は、**[Assertion contains the Use ID from the User object]\(アサーションにユーザー オブジェクトのユーザー ID が含まれています\)** を選択します

    f. **[SAML Identity Location (SAML ID の場所)]** で、**[Identity is in the NameIdentifier element of the Subject statement (ID は Subject ステートメントの NameIdentifier 要素にあります)]** を選択します。

    g. **[サービス プロバイダーが開始した要求のバインド]** で **[HTTP POST]** を選択します。

    h. **[ID プロバイダーのログイン URL]** ボックスに、Azure Portal からコピーした**シングル サインオン サービス URL** の値を貼り付けます。

    i. SFDC は SAML ログアウトをサポートしていません。  回避策として、`https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0` を **[ID プロバイダーのログアウト URL]** テキストボックスに貼り付けます。

    j. **[Save]** をクリックします。

### <a name="enable-your-domain"></a>ドメインの有効化

このセクションでは、ドメインが既に作成されていることを前提としています。  詳細については、「[ドメイン名の定義](https://help.salesforce.com/HTViewHelpDoc?id=domain_name_define.htm&language=en_US)」をご覧ください。

**ドメインを有効にするには、次の手順に従います。**

1. Salesforce の左側のナビゲーション ウィンドウで、**[Company Settings]\(会社の設定\)** をクリックして関連するセクションを展開し、**[My Domain]\(マイ ドメイン\)** をクリックします。

    ![Configure single sign-on](./media/salesforce-sandbox-tutorial/sf-my-domain.png)

   >[!NOTE]
   >ドメインが正しく構成されていることを確認します。

1. **[認証の構成]** セクションで、**[編集]** をクリックし、**[認証サービス]** として、前のセクションの SAML シングル サインオンの設定の名前を選択して、最後に **[保存]** をクリックします。

   ![Configure single sign-on](./media/salesforce-sandbox-tutorial/sf-edit-auth-config.png)

ドメインを構成するとすぐに、ユーザーはドメインの URL を使用して Salesforce Sandbox にログインできるようになります。

URL の値を取得するには、前のセクションで作成した SSO プロファイルをクリックします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/salesforce-sandbox-tutorial/create_aaduser_01.png)

1. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/salesforce-sandbox-tutorial/create_aaduser_02.png)

1. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/salesforce-sandbox-tutorial/create_aaduser_03.png)

1. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/salesforce-sandbox-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。

### <a name="create-a-salesforce-sandbox-test-user"></a>Salesforce Sandbox のテスト ユーザーの作成

このセクションでは、Britta Simon というユーザーを Salesforce Sandbox に作成します。 Salesforce Sandbox では、Just-In-Time プロビジョニングがサポートされています。この設定は既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 ユーザーがまだ Salesforce Sandbox に存在しない場合は、Salesforce Sandbox にアクセスしようとしたときに新しいユーザーが作成されます。 Salesforce Sandbox は、自動ユーザー プロビジョニングもサポートしています。自動ユーザー プロビジョニングの構成方法の詳細については、[こちら](salesforce-sandbox-provisioning-tutorial.md)を参照してください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Salesforce Sandbox へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200]

**Salesforce Sandbox に Britta Simon を割り当てるには、次の手順を実行します。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[Salesforce Sandbox]** を選択します。

    ![アプリケーション リストの [Salesforce Sandbox] リンク](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_app.png)  

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Salesforce Sandbox] タイルをクリックすると、自動的に Salesforce Sandbox アプリケーションにサインオンするはずです。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)
* [[ユーザー プロビジョニングの構成]](salesforce-sandbox-provisioning-tutorial.md)


<!--Image references-->

[1]: ./media/salesforce-sandbox-tutorial/tutorial_general_01.png
[2]: ./media/salesforce-sandbox-tutorial/tutorial_general_02.png
[3]: ./media/salesforce-sandbox-tutorial/tutorial_general_03.png
[4]: ./media/salesforce-sandbox-tutorial/tutorial_general_04.png

[100]: ./media/salesforce-sandbox-tutorial/tutorial_general_100.png

[200]: ./media/salesforce-sandbox-tutorial/tutorial_general_200.png
[201]: ./media/salesforce-sandbox-tutorial/tutorial_general_201.png
[202]: ./media/salesforce-sandbox-tutorial/tutorial_general_202.png
[203]: ./media/salesforce-sandbox-tutorial/tutorial_general_203.png
