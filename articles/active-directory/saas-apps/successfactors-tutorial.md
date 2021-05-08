---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と SuccessFactors の統合 | Microsoft Docs
description: Azure Active Directory と SuccessFactors の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/26/2020
ms.author: jeedes
ms.openlocfilehash: a903eb6000cdd5212ad358cae4952e27a4719070
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98725263"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-successfactors"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と SuccessFactors の統合

このチュートリアルでは、SuccessFactors と Azure Active Directory (Azure AD) を統合する方法について説明します。 SuccessFactors を Azure AD と統合すると、次のことができます。

* SuccessFactors にアクセスする Azure AD ユーザーを制御する。
* ユーザーが自分の Azure AD アカウントを使用して SuccessFactors に自動的にサインインできるようにする。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。


## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* SuccessFactors でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* SuccessFactors では、**SP** Initiated SSO がサポートされます。

## <a name="adding-successfactors-from-the-gallery"></a>ギャラリーからの SuccessFactors の追加

Azure AD への SuccessFactors の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に SuccessFactors を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**SuccessFactors**」と入力します。
1. 結果パネルで **[SuccessFactors]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。


## <a name="configure-and-test-azure-ad-sso-for-successfactors"></a>SuccessFactors の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、SuccessFactors に対する Azure AD SSO を構成してテストします。 SSO を機能させるために、Azure AD ユーザーと SuccessFactors の関連ユーザーとの間にリンク関係を確立する必要があります。

SuccessFactors に対する Azure AD SSO を構成してテストするには、次の手順を行います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
2. **[SuccessFactors SSO の構成](#configure-successfactors-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[SuccessFactors のテスト ユーザーの作成](#create-successfactors-test-user)** - SuccessFactors で B.Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
3. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **SuccessFactors** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    a. **[サインオン URL]** ボックスに、次のいずれかの形式で URL を入力します。

    - `https://<companyname>.successfactors.com/<companyname>`
    - `https://<companyname>.sapsf.com/<companyname>`
    - `https://<companyname>.successfactors.eu/<companyname>`
    - `https://<companyname>.sapsf.eu`

    b. **[識別子]** ボックスに、次のいずれかのパターンで URL を入力します。

    - `https://www.successfactors.com/<companyname>`
    - `https://www.successfactors.com`
    - `https://<companyname>.successfactors.eu`
    - `https://www.successfactors.eu/<companyname>`
    - `https://<companyname>.sapsf.com`
    - `https://hcm4preview.sapsf.com/<companyname>`
    - `https://<companyname>.sapsf.eu`
    - `https://www.successfactors.cn`
    - `https://www.successfactors.cn/<companyname>`

    c. **[応答 URL]** ボックスに、次のいずれかの形式で URL を入力します。

    - `https://<companyname>.successfactors.com/<companyname>`
    - `https://<companyname>.successfactors.com`
    - `https://<companyname>.sapsf.com/<companyname>`
    - `https://<companyname>.sapsf.com`
    - `https://<companyname>.successfactors.eu/<companyname>`
    - `https://<companyname>.successfactors.eu`
    - `https://<companyname>.sapsf.eu`
    - `https://<companyname>.sapsf.eu/<companyname>`
    - `https://<companyname>.sapsf.cn`
    - `https://<companyname>.sapsf.cn/<companyname>`

    > [!NOTE]
    > これらは実際の値ではありません。 実際のサインオン URL、識別子、および応答 URL で値を更新します。 これらの値を取得するには、[SuccessFactors クライアント サポート チーム](https://www.sap.com/support.html)に問い合わせてください。

4. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** を見つけて、 **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

6. **[SuccessFactors のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションでは、Azure portal 内で B.Simon というテスト ユーザーを作成します。

1. Azure portal の左側のウィンドウから、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。
1. 画面の上部にある **[新しいユーザー]** を選択します。
1. **[ユーザー]** プロパティで、以下の手順を実行します。
    1. **[名前]** フィールドに「`B.Simon`」と入力します。  
    1. **[ユーザー名]** フィールドに「username@companydomain.extension」と入力します。 たとえば、「 `B.Simon@contoso.com` 」のように入力します。
    1. **[パスワードを表示]** チェック ボックスをオンにし、 **[パスワード]** ボックスに表示された値を書き留めます。
    1. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、B.Simon に SuccessFactors へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[SuccessFactors]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-successfactors-sso"></a>SuccessFactors SSO の構成

1. 別の Web ブラウザー ウィンドウで、**SuccessFactors 管理者ポータル** に管理者としてログインします。

2. **[アプリケーション セキュリティ]** で **[Single Sign On Feature (シングル サインオン機能)]** に移動します。

3. **[Reset Token (リセット トークン)]** に任意の値を入力し、 **[Save Token (トークンの保存)]** をクリックして、SAML SSO を有効にします。

    ![[Application Security]\(アプリケーション セキュリティ\) タブのスクリーンショット。トークンを入力するための [Single Sign On Features]\(シングル サインオン機能\) が強調表示されています。][11]

    > [!NOTE]
    > この値は、オン/オフのスイッチとして使用されます。 任意の値を保存すると、SAML SSO はオンになります。 値が空白のまま保存すると、SAML SSO はオフになります。

4. 次のスクリーンショットの画面に移動して、次の操作を実行します。

    ![[For SAML-based S S O]\(SAML ベースの S S O 用\) ペインのスクリーンショット。ここで、説明されている値を入力できます。][12]
  
    a. **[SAML v2 SSO]** オプションをクリックします。
  
    b. **[SAML Asserting Party Name]\(SAML アサーティング パーティ名\)** を設定します (例: SAML 発行者 + 会社名)。

    c. **[発行者の URL]** テキストボックスに、Azure portal からコピーした、**Azure AD ID** の値を貼り付けます。

    d. **[Require Mandatory Signature]\(必須の署名が必要\)** として **[アサーション]** を選択します。

    e. **[Enable SAML Flag (SAML フラグを有効にする)]** で **[Enabled (有効にする)]** を選択します。

    f. **[Login Request Signature(SF Generated/SP/RP) (ログイン要求署名 (SF 生成/SP/RP))]** で **[No (いいえ)]** を選択します。

    g. **[SAML Profile (SAML プロファイル)]** で **[Browser/Post Profile (Browser/Post プロファイル)]** を選択します。

    h. **[Enforce Certificate Valid Period (証明書の有効期間を適用する)]** で **[No (いいえ)]** を選択します。

    i. Azure Portal からダウンロードした証明書ファイルのコンテンツをコピーし、 **[SAML Verifying Certificate]\(SAML で確認する証明書\)** ボックスに貼り付けます。

    > [!NOTE] 
    > この証明書には、証明書の開始タグおよび終了タグが必要です。

5. [SAML V2] に移動して、次の手順に従います。

    ![[SAML v2 S P initiated logout]\(SAML v2 S P initiated ログアウト\) ペインのスクリーンショット。ここで、説明されている値を入力できます。][13]

    a. **[Support SP-initiated Global Logout (SP によって開始されたグローバル ログアウトのサポート)]** で **[Yes (はい)]** を選択します。

    b. **[Global Logout Service URL (LogoutRequest destination)]\(グローバル ログアウト サービスの URL (LogoutRequest の送信先)\)** ボックスに、Azure Portal からコピーした **サインアウト URL** の値を貼り付けます。

    c. **[Require sp must encrypt all NameID element (すべての NameID 要素で SP での暗号化を要求)]** で **[No (いいえ)]** を選択します。

    d. **[NameID Format (NameID の形式)]** で **[unspecified (未指定)]** を選択します。

    e. **[Enable sp initiated login (AuthnRequest) (SP によって開始されたログインを有効にする (AuthnRequest))]** で **[Yes (はい)]** を選択します。

    f. Azure portal からコピーした **[ログイン URL]** の値を、 **[Send request as Company-Wide issuer]\(会社全体の発行者として要求を送信する\)** ボックスに貼り付けます。

6. 大文字と小文字を区別しないログイン ユーザー名を作成する場合、次の手順を実行します。

    ![Configure single sign-on][29]

    a. 下部の **[Company Settings (会社設定)]** に移動します。

    b. **[Enable Non-Case-Sensitive Username]\(大文字と小文字を区別しないユーザー名\)** の横のチェック ボックスをオンにします。

    c. **[保存]** をクリックします。

    > [!NOTE]
    > これを有効にしようとすると、重複する SAML ログイン名が作成されるかどうかがシステムによって確認されます。 たとえば、顧客が User1 および user1 というユーザー名を持っている場合などです。 大文字と小文字の区別をしないと、これらの重複が発生します。 システムによってエラー メッセージが表示され、機能は有効になりません。 顧客はユーザー名の一方を、違うスペルになるように変更する必要があります。

### <a name="create-successfactors-test-user"></a>SuccessFactors のテスト ユーザーの作成

Azure AD ユーザーが SuccessFactors にサインインできるようにするには、そのユーザーを SuccessFactors にプロビジョニングする必要があります。 SuccessFactors の場合、プロビジョニングは手動で行います。

SuccessFactors でユーザーを作成するには、 [SuccessFactors のサポート チーム](https://www.sap.com/support.html)に連絡する必要があります。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる SuccessFactors のサインオン URL にリダイレクトされます。 

* SuccessFactors のサインオン URL に直接移動し、そこからログイン フローを開始します。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [SuccessFactors] タイルをクリックすると、SuccessFactors のサインオン URL にリダイレクトされます。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。


## <a name="next-steps"></a>次のステップ

SuccessFactors を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を適用する方法](/cloud-app-security/proxy-deployment-aad)をご覧ください。

<!--Image references-->

[11]: ./media/successfactors-tutorial/tutorial_successfactors_07.png
[12]: ./media/successfactors-tutorial/tutorial_successfactors_08.png
[13]: ./media/successfactors-tutorial/tutorial_successfactors_09.png
[29]: ./media/successfactors-tutorial/tutorial_successfactors_10.png