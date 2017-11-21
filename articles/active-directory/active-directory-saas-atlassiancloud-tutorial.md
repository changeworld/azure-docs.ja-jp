---
title: "チュートリアル: Azure Active Directory と Atlassian Cloud の統合 | Microsoft Docs"
description: "Azure Active Directory と Atlassian Cloud の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 729b8eb6-efc4-47fb-9f34-8998ca2c9545
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/06/2017
ms.author: jeedes
ms.openlocfilehash: 25a7694cefde344b34d3a46535bf09209b306593
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/07/2017
---
# <a name="tutorial-azure-active-directory-integration-with-atlassian-cloud"></a>チュートリアル: Azure Active Directory と Atlassian Cloud の統合

このチュートリアルでは、Atlassian Cloud と Azure Active Directory (Azure AD) を統合する方法について説明します。

Atlassian Cloud と Azure AD の統合には、次の利点があります。

- Azure AD で、Atlassian Cloud にアクセスできる ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Atlassian Cloud にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Atlassian Cloud と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Atlassian Cloud 製品の SAML シングル サインオンを有効にするには、Identity Manager をセットアップする必要があります。 詳細については、「[Identity Manager]( https://www.atlassian.com/enterprise/cloud/identity-manager)」を参照してください

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Atlassian Cloud の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-atlassian-cloud-from-the-gallery"></a>ギャラリーからの Atlassian Cloud の追加
Azure AD への Atlassian Cloud の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Atlassian Cloud を追加する必要があります。

**ギャラリーから Atlassian Cloud を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

4. 検索ボックスに「**Atlassian Cloud**」と入力し、結果ウィンドウで **[Atlassian Cloud]** を選択し、**[追加]** ボタンをクリックして、アプリケーションを追加します。

    ![結果リスト内の Atlassian Cloud](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Atlassian Cloud で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Atlassian Cloud ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Atlassian Cloud の関連ユーザーの間で、リンク関係が確立されている必要があります。

Atlassian Cloud で、Azure AD の**ユーザー名**の値を **Username** の値として割り当ててリンク関係を確立します。

Atlassian Cloud での Azure AD シングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Atlassian Cloud のテスト ユーザーの作成](#create-an-atlassian-cloud-test-user)** - Atlassian Cloud で、ユーザーの Azure AD 表現にリンクされた Britta Simon の対応するユーザーを作成します。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、Atlassian Cloud アプリケーションでシングル サインオンを構成します。

**Atlassian Cloud との Azure AD シングル サインオンを構成するには、次の手順を実行します。**

1. Azure Portal の **Atlassian Cloud** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_samlbase.png)

3. **[Atlassian Cloud のドメインと URL]** セクションで、**IDP 開始モード**でアプリケーションを構成する場合は、次の手順を実行します。

    ![Atlassian Cloud のドメインと URL のシングル サインオン情報](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_url_new.png)
    
    a. **[識別子]** ボックスに次の URL を入力します。`https://id.atlassian.com/login`
    
    b. **[応答 URL]** ボックスに、URL として「`https://id.atlassian.com/login/saml/acs`」と入力します。

    c. **[リレー状態]** ボックスに、`https://<instancename>.atlassian.net` のパターンで URL を入力します。

4. アプリケーションを **SP** 開始モードで構成する場合は、**[詳細な URL 設定の表示]** チェックボックスをオンにして次の手順を実行します。

    ![Atlassian Cloud のドメインと URL のシングル サインオン情報](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_url1.png)

    **[サインオン URL]** テキストボックスに、`https://<instancename>.atlassian.net` のパターンを使用して URL を入力します。

    > [!NOTE] 
    > これらは実際の値ではありません。 これらの値は、このチュートリアルで後述する Atlassian Cloud SAML 構成画面から取得できます。

5. **[SAML 署名証明書]** セクションで、**[証明書 (Base64)]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_certificate.png) 

6. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_400.png)

7. **[Atlassian Cloud 構成]** セクションで、**[Atlassian Cloud の構成]** をクリックして **[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから、**SAML エンティティ ID と SAML シングル サインオン サービス URL** をコピーします。

    ![Atlassian Cloud 構成](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_configure.png) 

8. アプリケーションの SSO を構成するには、管理者権限で Atlassian ポータルにログインします。

9. **[Atlassian Site Administration]** (Atlassian サイト管理)  > **[Organizations & Security]** (組織とセキュリティ) に移動します。 組織をまだ作成していない場合、作成し、名前を付けます。 次に、左側のナビゲーションで、**[ドメイン]** をクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_06.png)

10. ドメインを確認する方法 (**DNS** または **HTTPS**) を選択します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_17.png)

11. DNS の確認で、**[ドメイン]** ページの **[DNS]** タブを選択し、次の手順を実行します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_18.png)

    a. **[コピー]** をクリックしてTXT レコードの値をコピーします。

    b. DNS から新しいレコードを追加するための設定ページを見つけます

    c. 新しいレコードを追加するオプションを選択して、**[ドメイン]** ページからコピーした値を **[値]** フィールドに貼り付けます。 お使いの DNS では、それを **[応答]** や **[説明]** と呼んでいることもあります。

    d. DNS レコードには次のフィールドも含まれることがあります。
    
    * **レコードの種類**: 「**TXT**」と入力します
    * **名前/ホスト/エイリアス**: 既定 (@ または空) のままにします
    * **Time to live (TTL)**: 「**86400**」と入力します
    
    e.  レコードを保存します。

12. 組織の管理の **[ドメイン] ページ**に戻り、**[ドメインの確認]** ボタンをクリックします。 ポップアップでドメイン名を入力し、**[ドメインの確認]** ボタンをクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_19.png)
    
    > [!NOTE]
    > TXT レコードの変更が有効になるまで最大 72 時間かかることがあり、ドメインの確認が成功したかどうかはすぐにわかりません。 これらの手順を実行したらすぐに **[ドメイン]** ページで確認の状態をチェックしてください。 更新された状態が **[VERIFIED]** (確認済み) で次の画面が表示されます。

    ![[シングル サインオンの構成]](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_20.png)

13. HTTPS の確認で、**[ドメイン]** ページの **[HTTPS]** タブを選択し、次の手順を実行します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_21.png)

    a.  **[ファイルのダウンロード]** をクリックし、HTML ファイルをダウンロードします。

    b.  ドメインのルート ディレクトリに HTML ファイルをアップロードします。

14. 組織の管理の **[ドメイン]** ページに戻り、**[ドメインの確認]** ボタンをクリックします。 ポップアップで**ドメイン名**を入力し、**[ドメインの確認]** ボタンをクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_22.png)

15. 確認プロセスで、ルート ディレクトリにアップロードしたファイルが見つかると、ドメインの状態が**確認済み**に更新されます。

    ![[シングル サインオンの構成]](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_23.png)

    > [!NOTE]
    > 詳細については、[Atlassian のドメインの確認ドキュメント](https://confluence.atlassian.com/cloud/domain-verification-873871234.html)を参照してください

16. 左側のナビゲーション バーで、**[SAML single sign-on]** (SAML シングル サインオン) をクリックします。 まだ Atlassian の Identity Manager にサブスクライブしていない場合は、サブスクライブします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_11.png)

17. **[Add SAML configuration]** (SAML 構成の追加) ダイアログ ボックスで、次のように ID プロバイダー設定を追加します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_12.png)

    a. **[ID プロバイダー エンティティ ID]** テキスト ボックスに、Azure Portal からコピーした **SAML エンティティ ID** の値を貼り付けます。

    b. **[ID プロバイダー シングル サインオン URL]** テキスト ボックスに、Azure Portal からコピーした **SAML シングル サインオン サービス URL** の値を貼り付けます。

    c. ダウンロードした証明書をメモ帳で開き、Begin 行と End 行以外の値をコピーし、**[公開 X509 証明書]** ボックスに貼り付けます。
    
    d. **[構成の保存]** をクリックして変更を保存します。
     
18. Azure AD 設定を更新し、正しい URL が設定されていることを確認します。
  
    ![[シングル サインオンの構成]](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_13.png)

    a. SAML 画面から **[SP Identity ID]** の値をコピーし、Azure ポータルの **[Atlassian Cloud のドメインと URL]** セクションの **[識別子]** ボックスに貼り付けます。
    
    b. SAML 画面から **[SP Assertion Consumer Service URL]** の値をコピーし、Azure ポータルの **[Atlassian Cloud のドメインと URL]** セクションの **[応答 URL]** ボックスに貼り付けます。
    
    c. サインオン URL は、Atlassian Cloud のテナント URL です。 
    
19. Azure Portal で、**[保存]** をクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_400.png)

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関する記事をご覧ください。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_01.png)

2. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_02.png)

3. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_03.png)

4. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。
  
### <a name="create-an-atlassian-cloud-test-user"></a>Atlassian Cloud のテスト ユーザーの作成

Azure AD ユーザーが Atlassian Cloud にログインできるようにするには、そのユーザーを Atlassian Cloud にプロビジョニングする必要があります。 Atlassian Cloud の場合、プロビジョニングは手動で実行します。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. Atlassian ポータルの [サイト管理] セクションで、**[ユーザー]** ボタンをクリックします。

    ![Atlassian Cloud ユーザーの作成](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_14.png) 

2. **[ユーザーの招待]** ボタンをクリックして、Atlassian Cloud にユーザーを作成します。

    ![Atlassian Cloud ユーザーの作成](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_15.png) 

3. ユーザーの**電子メール アドレス**を入力し、アプリケーションへのアクセスを割り当てます。 

    ![Atlassian Cloud ユーザーの作成](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_16.png)
 
4. **[ユーザーの招待]** ボタンをクリックします。これで、招待状が電子メールでユーザーに送信され、招待状を受け取ったユーザーがシステムでアクティブになります。 

>[!NOTE] 
>[ユーザー] セクションの **[一括作成]** ボタンをクリックして、ユーザーを作成することもできます。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Atlassian Cloud へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**Atlassian Cloud に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[Atlassian Cloud]** を選択します。

    ![[アプリケーション] リストの [Atlassian Cloud] リンク](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_app.png)  

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Atlassian Cloud] タイルをクリックすると、自動的に Atlassian Cloud アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_203.png

