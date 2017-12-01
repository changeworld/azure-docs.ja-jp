---
title: "チュートリアル: Azure Active Directory と Google Apps の統合 | Microsoft Docs"
description: "Azure Active Directory と Google Apps の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 38a6ca75-7fd0-4cdc-9b9f-fae080c5a016
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/16/2017
ms.author: jeedes
ms.openlocfilehash: 1d92e673a948dd139ff2d4a24f2e602180be43c5
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/18/2017
---
# <a name="tutorial-azure-active-directory-integration-with-google-apps"></a>チュートリアル: Azure Active Directory と Google Apps の統合

このチュートリアルでは、Google Apps と Azure Active Directory (Azure AD) を統合する方法について説明します。

Google Apps と Azure AD の統合には、次の利点があります。

- Google Apps にアクセスする Azure AD ユーザーを制御できます。
- ユーザーは自分の Azure AD アカウントで Google Apps に自動的にサインオン (シングル サインオン) できます。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Google Apps と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Google Apps でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="video-tutorial"></a>ビデオ チュートリアル
2 分間で Google Apps へのシングル サインオンを有効にする方法:

> [!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Enable-single-sign-on-to-Google-Apps-in-2-minutes-with-Azure-AD/player]

## <a name="frequently-asked-questions"></a>よく寄せられる質問
1. **Q: Chromebook とその他の Chrome デバイスは、Azure AD シングル サインオンと互換性がありますか。**
   
    A: はい。ユーザーは Azure AD の資格情報を使用して、Chromebook デバイスにサインインすることができます。 ユーザーに資格情報の入力を求めるメッセージが 2 回表示される場合がある理由については、[Google Apps のこちらのサポート記事](https://support.google.com/chrome/a/answer/6060880)をご覧ください。

2. **Q: シングル サインオンを有効にした場合、ユーザーは Google Classroom、GMail、Google Drive、YouTube などの Google 製品にサインインするために Azure AD 資格情報を使用できますか。**
   
    A: はい。[Google アプリ](https://support.google.com/a/answer/182442?hl=en&ref_topic=1227583)ごとに、組織で有効にするか無効にするかを選択します。

3. **Q: Google Apps ユーザーの一部だけに対して、シングル サインオンを有効にできますか。**
   
    A: いいえ。シングル サインオンを有効にすると、直ちにすべての Google Apps ユーザーが Azure AD 資格情報での認証を要求されるようになります。 Google Apps は複数の ID プロバイダーをサポートしていないため、Google Apps 環境の ID プロバイダーは Azure AD か Google であり、同時に両方を設定することはできません。

4. **Q: Windows でサインインしたユーザーは、パスワードの入力を求められることなく、自動的に Google Apps に認証されますか。**
   
    A: このシナリオを有効にするには、2 つのオプションがあります。 まず、ユーザーは [Azure Active Directory 参加](active-directory-azureadjoin-overview.md)を通じて Windows 10 デバイスにサインインできます。 また、ユーザーは、 [Active Directory フェデレーション サービス (AD FS)](active-directory-aadconnect-user-signin.md) デプロイを通じて Azure AD へのシングル サインオンが有効になっているオンプレミスの Active Directory にドメイン参加している Windows デバイスにサインインすることもできます。 どちらのオプションでも、以下のチュートリアルの手順に従って、Azure AD と Google Apps との間のシングル サインオンを有効にする必要があります。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Google Apps の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-google-apps-from-the-gallery"></a>ギャラリーからの Google Apps の追加
Azure AD への Google Apps の統合を構成するには、管理対象の SaaS アプリの一覧にギャラリーから Google Apps を追加する必要があります。

**ギャラリーから Google Apps を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

4. 検索ボックスに「**Google Apps**」と入力し、結果ウィンドウで **[Google Apps]** を選び、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果一覧の Google Apps](./media/active-directory-saas-googleapps-tutorial/tutorial_googleapps_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Google Apps で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Google Apps ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Google Apps の関連ユーザーの間で、リンク関係が確立されている必要があります。

Google Apps で、Azure AD の **[ユーザー名]** の値を **[Username]\(ユーザー名\)** の値として割り当ててリンク関係を確立します。

Google Apps で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Google Apps テスト ユーザーの作成](#create-a-google-apps-test-user)** - Google Apps で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、Google Apps アプリケーションでシングル サインオンを構成します。

**Google Apps で Azure AD シングル サインオンを構成するには、次の手順を実行します。**

1. Azure Portal の **Google Apps** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/active-directory-saas-googleapps-tutorial/tutorial_googleapps_samlbase.png)

3. **[Google Apps Domain and URLs]\(Google Apps のドメインと URL\)** セクションで、次の手順を実行します。

    ![[Google Apps のドメインと URL] のシングル サインオン情報](./media/active-directory-saas-googleapps-tutorial/tutorial_googleapps_url.png)

    a. **[サインオン URL]** ボックスに、`https://mail.google.com/a/<yourdomain.com>` のパターンを使用して URL を入力します。

    b. **[識別子]** ボックスに、次のパターンで URL を入力します。

    | |
    |--|
    | `http://google.com/a/<yourdomain.com>`|
    | `http://google.com`|    
    | `google.com/<yourdomain.com>`|
    | `google.com`|

    > [!NOTE] 
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新してください。 これらの値を取得するには、[Google Apps クライアント サポート チーム](https://www.google.com/contact/)に問い合わせてください。 

4. **[SAML 署名証明書]** セクションで、**[証明書]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/active-directory-saas-googleapps-tutorial/tutorial_googleapps_certificate.png) 

5. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/active-directory-saas-googleapps-tutorial/tutorial_general_400.png)

6. **[Google Apps Configuration]\(Google Apps 構成\)** セクションで、**[Configure Google Apps]\(Google Apps を構成する\)** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから**サインアウト URL、SAML シングル サインオン サービス URL、パスワードの URL の変更**をコピーします。

    ![Google Apps の構成](./media/active-directory-saas-googleapps-tutorial/tutorial_googleapps_configure.png) 

7. ブラウザーで新しいタブを開き、管理者アカウントを使用して、 [Google Apps の管理コンソール](http://admin.google.com/) にサインインします。

8. **[セキュリティ]**をクリックします。 このリンクが表示されていない場合、画面下部の **[その他の設定]** に隠れていることがあります。
   
    ![Click Security.][10]

9. **[セキュリティ]** ページで、**[シングル サインオン (SSO) の設定]** をクリックします。
   
    ![Click SSO.][11]

10. 次の構成の変更を実行します。
   
    ![Configure SSO][12]
   
    a. **[Setup SSO with third-party identity provider]\(サード パーティの ID プロバイダーで SSO を設定する\)** を選択します。

    b. Google Apps の **[サインイン ページの URL]** フィールドに、Azure Portal からコピーした **[シングル サインオン サービス URL]** の値を貼り付けます。

    c. Google Apps の **[サインアウト ページの URL]** フィールドに、Azure Portal からコピーした **[サインアウト URL]** の値を貼り付けます。 

    d. Google Apps の **[パスワードの URL の変更]** フィールドに、Azure Portal からコピーした **[パスワードの URL の変更]** の値を貼り付けます。 

    e. Google Apps の **[検証証明書]** に、Azure Portal からダウンロードした証明書をアップロードします。

    f.SAML 属性の属性名またはスキーマ リファレンスを入力します。 **[変更を保存]**をクリックします。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関する記事をご覧ください。
> 

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/active-directory-saas-googleapps-tutorial/create_aaduser_01.png)

2. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/active-directory-saas-googleapps-tutorial/create_aaduser_02.png)

3. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/active-directory-saas-googleapps-tutorial/create_aaduser_03.png)

4. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/active-directory-saas-googleapps-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。
 
### <a name="create-a-google-apps-test-user"></a>Google Apps テスト ユーザーの作成

このセクションの目的は、Google Apps ソフトウェアで Britta Simon というユーザーを作成することです。 Google Apps では、自動プロビジョニングがサポートされており、これは既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 ユーザーがまだ Google Apps ソフトウェアに存在しない場合は、Google Apps ソフトウェアにアクセスしようとしたときに新しいユーザーが作成されます。

>[!NOTE] 
>ユーザーを手動で作成する必要がある場合は、[Google サポート チーム](https://www.google.com/contact/)にお問い合わせください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Google Apps へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**Google Apps に Britta Simon を割り当てるには、次の手順を実行します。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[Google Apps]** を選択します。

    ![アプリケーションの一覧の Google Apps リンク](./media/active-directory-saas-googleapps-tutorial/tutorial_googleapps_app.png)  

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで Google Apps のタイルをクリックすると、Google Apps アプリケーションに自動的にサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-googleapps-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-googleapps-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-googleapps-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-googleapps-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-googleapps-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-googleapps-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-googleapps-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-googleapps-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-googleapps-tutorial/tutorial_general_203.png
[10]: ./media/active-directory-saas-googleapps-tutorial/gapps-security.png
[11]: ./media/active-directory-saas-googleapps-tutorial/security-gapps.png
[12]: ./media/active-directory-saas-googleapps-tutorial/gapps-sso-config.png

