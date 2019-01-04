---
title: チュートリアル:Azure Active Directory と G Suite の統合 | Microsoft Docs
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
ms.date: 11/30/2018
ms.author: jeedes
ms.openlocfilehash: bcea7848c7331ecd326f0ccb6ab9f543ce972205
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/04/2018
ms.locfileid: "52834691"
---
# <a name="tutorial-azure-active-directory-integration-with-g-suite"></a>チュートリアル:Azure Active Directory と G Suite の統合

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
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。 このドキュメントは、新しいユーザー シングル サインオン エクスペリエンスを使用して作成されました。 まだ古いものを使用している場合、セットアップは異なります。 G-Suite アプリケーションのシングル サインオン設定で、新しいエクスペリエンスを有効にすることができます。 **[Azure AD, Enterprise applications]\(Azure AD Enterprise アプリケーション\)** に移動し、**[G Suite]** を選択し、**[Single Sign-on]\(シングル サインオン\)** を選択し、**[Try out our new experience]\(新しいエクスペリエンスを試す\)** をクリックします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="frequently-asked-questions"></a>よく寄せられる質問

1. **Q:この統合では、Google Cloud Platform と Azure AD の SSO 統合はサポートされていますか。**

    A:はい。 Google Cloud Platform と Google Apps は同じ認証プラットフォームを共有します。 そのため、GCP の統合を実行するには、Google Apps で SSO を構成する必要があります。

2. **Q:Chromebook とその他の Chrome デバイスは、Azure AD シングル サインオンと互換性がありますか。**
  
    A:はい。ユーザーは Azure AD の資格情報を使用して、Chromebook デバイスにサインインすることができます。 ユーザーに資格情報の入力を求めるメッセージが 2 回表示される場合がある理由については、[G Suite のこちらのサポート記事](https://support.google.com/chrome/a/answer/6060880)をご覧ください。

3. **Q:シングル サインオンを有効にした場合、ユーザーは Google Classroom、GMail、Google Drive、YouTube などの Google 製品にサインインするために Azure AD 資格情報を使用できますか。**

    A:はい。[G Suite](https://support.google.com/a/answer/182442?hl=en&ref_topic=1227583) ごとに、組織で有効にするか無効にするかを選択します。

4. **Q:一部の G Suite ユーザーに対してのみ、シングル サインオンを有効にすることはできますか。**

    A:いいえ。シングル サインオンを有効にすると、直ちにすべての G Suite ユーザーが Azure AD の資格情報での認証を要求されるようになります。 G Suite では複数の ID プロバイダーをサポートしていないため、G Suite 環境の ID プロバイダーは Azure AD か Google であり、同時に両方を設定することはできません。

5. **Q:Windows でサインインしたユーザーは、パスワードの入力を求められることなく、G Suite に対して自動的に認証されますか。**

    A:このシナリオを有効にするには、2 つのオプションがあります。 まず、ユーザーは [Azure Active Directory 参加](../device-management-introduction.md)を通じて Windows 10 デバイスにサインインできます。 また、ユーザーは、 [Active Directory フェデレーション サービス (AD FS)](../hybrid/plan-connect-user-signin.md) デプロイを通じて Azure AD へのシングル サインオンが有効になっているオンプレミスの Active Directory にドメイン参加している Windows デバイスにサインインすることもできます。 どちらのオプションでも、以下のチュートリアルの手順に従って、Azure AD と G Suite の間のシングル サインオンを有効にする必要があります。

6. **エラー:無効な電子メール アドレス**

    このセットアップでは、ユーザーがサインインできるにはメール属性が必要です。 この属性は手動では設定できません。

    メール属性は、有効な Exchange ライセンスを持つユーザーに自動的に設定されます。 メールが有効になっていないユーザーの場合、アプリケーションでアクセス権を付与するにはこの属性を取得する必要があるため、このエラーを受け取ります。

    管理者アカウントで portal.office.com に移動し、管理センターで課金とサブスクリプションをクリックし、Office 365 サブスクリプションを選択し、ユーザーへの割り当てをクリックし、右側のウィンドウでサブスクリプションを確認するユーザーを選択し、ライセンスの編集をクリックします。

    O365 ライセンスの割り当てが適用されるまで、数分かかる場合があります。 その後、user.mail 属性が自動的に設定されて、問題は解決します。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの G Suite の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-g-suite-from-the-gallery"></a>ギャラリーからの G Suite の追加

Azure AD への G Suite の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に G Suite を追加する必要があります。

**ギャラリーから G Suite を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

4. 検索ボックスに「**G Suite**」と入力し、結果ウィンドウで **[G Suite]** を選び、**[追加]** をクリックしてアプリケーションを追加します。

    ![結果一覧の G Suite](./media/google-apps-tutorial/tutorial_gsuite_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、G Suite で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する G Suite ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと G Suite の関連ユーザーの間で、リンク関係が確立されている必要があります。

G Suite で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[G Suite テスト ユーザーの作成](#creating-a-g-suite-test-user)** - Azure AD の Britta Simon にリンクさせるために、対応するユーザーを G Suite で作成します。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にし、G Suite アプリケーションでシングル サインオンを構成します。

**G Suite で Azure AD のシングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **G Suite** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

2. **[シングル サインオン方式の選択]** ダイアログで、**[SAML]** モードの **[選択]** をクリックして、シングル サインオンを有効にします。

    ![Configure single sign-on](common/tutorial_general_301.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、**[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![Configure single sign-on](common/editconfigure.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    ![[G Suite のドメインと URL] のシングル サインオン情報](./media/google-apps-tutorial/tutorial_gsuite_url.png)

    a. **[サインオン URL]** ボックスに、`https://www.google.com/a/<yourdomain.com>/ServiceLogin?continue=https://mail.google.com` のパターンを使用して URL を入力します。

    b. **[識別子]** ボックスに、次の形式で URL を入力します。
    | |
    |--|
    | `google.com/a/<yourdomain.com>` |
    | `google.com` |
    | `https://google.com` |
    | `https://google.com/a/<yourdomain.com>` |

    > [!NOTE]
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新してください。 これらの値を取得するには、[G Suite クライアント サポート チーム](https://www.google.com/contact/)に連絡してください。

5. G Suite アプリケーションは、特定の形式で構成された SAML アサーションを受け入れます。 このアプリケーションには、次の要求を構成します。 これらの属性の値は、アプリケーション統合ページの **[ユーザー属性]** セクションで管理できます。 **[SAML でシングル サインオンをセットアップします]** ページで、**[編集]** ボタンをクリックして **[ユーザー属性]** ダイアログを開きます。

    ![image](./media/google-apps-tutorial/i3-attribute.png)

6. **[ユーザー属性]** ダイアログの **[ユーザーの要求]** セクションで、上の図のように SAML トークン属性を構成し、次の手順を実行します。

    a. **[編集]** をクリックして **[ユーザー要求の管理]** ダイアログを開きます。

    ![image](./media/google-apps-tutorial/i2-attribute.png)

    ![image](./media/google-apps-tutorial/i4-attribute.png)

    b. **[ソース属性]** の一覧から、属性値を選択します。

    c. **[Save]** をクリックします。

5. **[SAML 署名証明書]** ページの **[SAML 署名証明書]** セクションで、**[ダウンロード]** をクリックして**証明書 (Base64)** をダウンロードし、証明書ファイルをコンピューターに保存します。

    ![証明書のダウンロードのリンク](./media/google-apps-tutorial/tutorial_gsuite_certificate.png) 

6. **[Set up G Suite]\(G Suite の設定)** セクションで、要件どおりの適切な URL をコピーします。

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

    ![G Suite 構成](common/configuresection.png)

9. ブラウザーで新しいタブを開き、管理者アカウントを使用して、[G Suite 管理コンソール](https://admin.google.com/)にサインインします。

10. **[セキュリティ]** をクリックします。 このリンクが表示されていない場合、画面下部の **[その他の設定]** に隠れていることがあります。

    ![Click Security.][10]

11. **[セキュリティ]** ページで、**[シングル サインオン (SSO) の設定]** をクリックします。

    ![Click SSO.][11]

12. 次の構成の変更を実行します。

    ![Configure SSO][12]

    a. **[Setup SSO with third-party identity provider]\(サード パーティの ID プロバイダーで SSO を設定する\)** を選択します。

    b. G Suite の **[サインイン ページの URL]** フィールドに、Azure portal からコピーした  **[ログイン URL]**  の値を貼り付けます。

    c. G Suite の **[サインアウト ページの URL]** フィールドに、Azure portal からコピーした  **[ログアウト URL]**  の値を貼り付けます。

    d. G Suite の **[パスワードの URL の変更]** フィールドに、Azure portal からコピーした  **[パスワードの URL の変更]**  の値を貼り付けます。

    e. G Suite の **[検証証明書]** に、Azure Portal からダウンロードした証明書をアップロードします。

    f. **[Use a domain specific issuer]\(ドメイン固有の発行者を使用する\)** をオンにします。

    g. **[変更を保存]** をクリックします。

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

### <a name="creating-a-g-suite-test-user"></a>G Suite テスト ユーザーの作成

このセクションの目的は、G Suite ソフトウェアで Britta Simon というユーザーを作成することです。 G Suite では、自動プロビジョニングがサポートされており、これは既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 G Suite ソフトウェアにユーザーがまだ存在しない場合は、G Suite ソフトウェアにアクセスしようとしたときに新しいユーザーが作成されます。

> [!NOTE]
> シングル サインオンをテストする前に Azure AD でのプロビジョニングが有効にされていない場合は、既に G Suite にユーザーが存在していることを確認してください。

> [!NOTE]
> ユーザーを手動で作成する必要がある場合は、[Google サポート チーム](https://www.google.com/contact/)にお問い合わせください。

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に G Suite へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、**[すべてのアプリケーション]** を選択します。

    ![ユーザーの割り当て][201]

2. アプリケーションの一覧で **[G Suite]** を選択します。

    ![Configure single sign-on](./media/google-apps-tutorial/tutorial_gsuite_app.png)

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202]

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. **[割り当ての追加]** ダイアログで、**[割り当て]** ボタンを選択します。

### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [G Suite] タイルをクリックすると、自動的に G Suite アプリケーションにサインオンします。
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
<!--Image references-->

[10]: ./media/google-apps-tutorial/gapps-security.png
[11]: ./media/google-apps-tutorial/security-gapps.png
[12]: ./media/google-apps-tutorial/gapps-sso-config.png