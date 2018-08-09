---
title: 'チュートリアル: Azure Active Directory と G Suite の統合 | Microsoft Docs'
description: Azure Active Directory と G Suite の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 38a6ca75-7fd0-4cdc-9b9f-fae080c5a016
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: jeedes
ms.openlocfilehash: 8001f2d38ac80bb6c67419faa54bf834531f0332
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39439404"
---
# <a name="tutorial-azure-active-directory-integration-with-g-suite"></a>チュートリアル: Azure Active Directory と G Suite の統合

このチュートリアルでは、G Suite と Azure Active Directory (Azure AD) を統合する方法について説明します。

G Suite と Azure AD の統合には、次の利点があります。

- G Suite にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが Azure AD アカウントで自動的に G Suite にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

G Suite と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- G Suite でのシングル サインオンが有効なサブスクリプション
- Google Apps サブスクリプションまたは Google Cloud Platform サブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="frequently-asked-questions"></a>よく寄せられる質問
1.  **Q: この統合では、Google Cloud Platform と Azure AD の SSO 統合はサポートされていますか。**
    
    A: はい。 Google Cloud Platform と Google Apps は同じ認証プラットフォームを共有します。 そのため、GCP の統合を実行するには、Google Apps で SSO を構成する必要があります。


1. **Q: Chromebook とその他の Chrome デバイスは、Azure AD シングル サインオンと互換性がありますか。**
   
    A: はい。ユーザーは Azure AD の資格情報を使用して、Chromebook デバイスにサインインすることができます。 ユーザーに資格情報の入力を求めるメッセージが 2 回表示される場合がある理由については、[G Suite のこちらのサポート記事](https://support.google.com/chrome/a/answer/6060880)をご覧ください。

1. **Q: シングル サインオンを有効にした場合、ユーザーは Google Classroom、GMail、Google Drive、YouTube などの Google 製品にサインインするために Azure AD 資格情報を使用できますか。**
   
    A: はい。[G Suite](https://support.google.com/a/answer/182442?hl=en&ref_topic=1227583) ごとに、組織で有効にするか無効にするかを選択します。

1. **Q: 一部の G Suite ユーザーに対してのみ、シングル サインオンを有効にすることはできますか。**
   
    A: いいえ。シングル サインオンを有効にすると、直ちにすべての G Suite ユーザーが Azure AD の資格情報での認証を要求されるようになります。 G Suite では複数の ID プロバイダーをサポートしていないため、G Suite 環境の ID プロバイダーは Azure AD か Google であり、同時に両方を設定することはできません。

1. **Q: Windows でサインインしたユーザーは、パスワードの入力を求められることなく、G Suite に対して自動的に認証されますか。**
   
    A: このシナリオを有効にするには、2 つのオプションがあります。 まず、ユーザーは [Azure Active Directory 参加](../device-management-introduction.md)を通じて Windows 10 デバイスにサインインできます。 また、ユーザーは、 [Active Directory フェデレーション サービス (AD FS)](../connect/active-directory-aadconnect-user-signin.md) デプロイを通じて Azure AD へのシングル サインオンが有効になっているオンプレミスの Active Directory にドメイン参加している Windows デバイスにサインインすることもできます。 どちらのオプションでも、以下のチュートリアルの手順に従って、Azure AD と G Suite の間のシングル サインオンを有効にする必要があります。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの G Suite の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-g-suite-from-the-gallery"></a>ギャラリーからの G Suite の追加
Azure AD への G Suite の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に G Suite を追加する必要があります。

**ギャラリーから G Suite を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

1. 検索ボックスに「**G Suite**」と入力し、結果ウィンドウで **[G Suite]** を選び、**[追加]** をクリックしてアプリケーションを追加します。

    ![結果一覧の G Suite](./media/google-apps-tutorial/tutorial_googleapps_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、G Suite で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する G Suite ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと G Suite の関連ユーザーの間で、リンク関係が確立されている必要があります。

G Suite で、Azure AD の **[ユーザー名]** の値を **[Username]** の値として割り当ててリンク関係を確立します。

G Suite で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[G Suite テスト ユーザーの作成](#create-a-g-suite-test-user)** - Azure AD の Britta Simon にリンクさせるために、対応するユーザーを G Suite で作成します。
1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にし、G Suite アプリケーションでシングル サインオンを構成します。

**G Suite で Azure AD のシングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **G Suite** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。

    ![[シングル サインオン] ダイアログ ボックス](./media/google-apps-tutorial/tutorial_googleapps_samlbase.png)

1. **[G Suite のドメインと URL]** セクションで、**Gmail** を構成する場合は次の手順に従います。

    ![[G Suite のドメインと URL] のシングル サインオン情報](./media/google-apps-tutorial/tutorial_googleapps_urlgmail.png)

    a. **[サインオン URL]** ボックスに、`https://www.google.com/a/<yourdomain.com>/ServiceLogin?continue=https://mail.google.com` のパターンを使用して URL を入力します。

    b. **[識別子]** ボックスに、次の形式で URL を入力します。 
    | |
    |--|
    | `google.com/a/<yourdomain.com>` |
    | `google.com` |
    | `http://google.com` |
    | `http://google.com/a/<yourdomain.com>` |
 
    > [!NOTE] 
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新してください。 これらの値を取得するには、[G Suite クライアント サポート チーム](https://www.google.com/contact/)に連絡してください。

1. **[G Suite のドメインと URL]** セクションで、**Google Cloud Platform** を構成する場合は次の手順に従います。

    ![[G Suite のドメインと URL] のシングル サインオン情報](./media/google-apps-tutorial/tutorial_googleapps_url1.png)

    a. **[サインオン URL]** ボックスに、`https://www.google.com/a/<yourdomain.com>/ServiceLogin?continue=https://console.cloud.google.com ` のパターンを使用して URL を入力します。

    b. **[識別子]** ボックスに、次の形式で URL を入力します。 
    | |
    |--|
    | `google.com/a/<yourdomain.com>` |
    | `google.com` |
    | `http://google.com` |
    | `http://google.com/a/<yourdomain.com>` |
    
    > [!NOTE] 
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新してください。 これらの値を取得するには、[G Suite クライアント サポート チーム](https://www.google.com/contact/)に連絡してください。 

1. **[SAML 署名証明書]** セクションで、**[証明書]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/google-apps-tutorial/tutorial_googleapps_certificate.png) 

1. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/google-apps-tutorial/tutorial_general_400.png)

1. **[G Suite 構成]** セクションで、**[G Suite を構成する]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから**サインアウト URL、SAML シングル サインオン サービス URL、パスワードの URL の変更**をコピーします。

    ![G Suite 構成](./media/google-apps-tutorial/tutorial_googleapps_configure.png) 

1. ブラウザーで新しいタブを開き、管理者アカウントを使用して、[G Suite 管理コンソール](http://admin.google.com/)にサインインします。

1. **[セキュリティ]** をクリックします。 このリンクが表示されていない場合、画面下部の **[その他の設定]** に隠れていることがあります。
   
    ![Click Security.][10]

1. **[セキュリティ]** ページで、**[シングル サインオン (SSO) の設定]** をクリックします。
   
    ![Click SSO.][11]

1. 次の構成の変更を実行します。
   
    ![Configure SSO][12]
   
    a. **[Setup SSO with third-party identity provider]\(サード パーティの ID プロバイダーで SSO を設定する\)** を選択します。

    b. G Suite の **[サインイン ページの URL]** フィールドに、Azure Portal からコピーした **[シングル サインオン サービス URL]** の値を貼り付けます。

    c. G Suite の **[サインアウト ページの URL]** フィールドに、Azure Portal からコピーした **[サインアウト URL]** の値を貼り付けます。 

    d. G Suite の **[パスワードの URL の変更]** フィールドに、Azure Portal からコピーした **[パスワードの URL の変更]** の値を貼り付けます。 

    e. G Suite の **[検証証明書]** に、Azure Portal からダウンロードした証明書をアップロードします。

    f. **[Use a domain specific issuer]\(ドメイン固有の発行者を使用する\)** をオンにします。

    g. **[変更を保存]** をクリックします。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関するページを参照してください。
> 

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/google-apps-tutorial/create_aaduser_01.png)

1. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/google-apps-tutorial/create_aaduser_02.png)

1. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/google-apps-tutorial/create_aaduser_03.png)

1. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/google-apps-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。
 
### <a name="create-a-g-suite-test-user"></a>G Suite テスト ユーザーの作成

このセクションの目的は、G Suite ソフトウェアで Britta Simon というユーザーを作成することです。 G Suite では、自動プロビジョニングがサポートされており、これは既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 G Suite ソフトウェアにユーザーがまだ存在しない場合は、G Suite ソフトウェアにアクセスしようとしたときに新しいユーザーが作成されます。

>[!NOTE] 
>ユーザーを手動で作成する必要がある場合は、[Google サポート チーム](https://www.google.com/contact/)にお問い合わせください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に G Suite へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**G Suite に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[G Suite]** を選択します。

    ![アプリケーションの一覧の G Suite のリンク](./media/google-apps-tutorial/tutorial_googleapps_app.png)  

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [G Suite] タイルをクリックすると、自動的に G Suite アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/googleapps-tutorial/tutorial_general_01.png
[2]: ./media/googleapps-tutorial/tutorial_general_02.png
[3]: ./media/googleapps-tutorial/tutorial_general_03.png
[4]: ./media/googleapps-tutorial/tutorial_general_04.png

[100]: ./media/googleapps-tutorial/tutorial_general_100.png

[200]: ./media/googleapps-tutorial/tutorial_general_200.png
[201]: ./media/googleapps-tutorial/tutorial_general_201.png
[202]: ./media/googleapps-tutorial/tutorial_general_202.png
[203]: ./media/googleapps-tutorial/tutorial_general_203.png
[10]: ./media/googleapps-tutorial/gapps-security.png
[11]: ./media/googleapps-tutorial/security-gapps.png
[12]: ./media/googleapps-tutorial/gapps-sso-config.png

