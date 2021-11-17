---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と HRworks Single Sign-On の統合 | Microsoft Docs
description: Azure Active Directory と HRworks Single Sign-On の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/29/2021
ms.author: jeedes
ms.openlocfilehash: 43e67da4ed97ea188a3cd227ac34f8f5628b8cf4
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132317587"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-hrworks-single-sign-on"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と HRworks Single Sign-On の統合

このチュートリアルでは、HRworks Single Sign-On と Azure Active Directory (Azure AD) を統合する方法について説明します。 HRworks Single Sign-On を Azure AD と統合すると、次のことができます。

* HRworks Single Sign-On にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントで HRworks Single Sign-On に自動的にサインインするように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* HRworks Single Sign-On でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* HRworks Single Sign-On では、**SP** Initiated SSO がサポートされます。

> [!NOTE]
> このアプリケーションの識別子は固定文字列値であるため、1 つのテナントで構成できるインスタンスは 1 つだけです。

## <a name="add-hrworks-single-sign-on-from-the-gallery"></a>ギャラリーからの HRworks Single Sign-On の追加

Azure AD への HRworks Single Sign-On の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に HRworks Single Sign-On を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**HRworks Single Sign-On**」と入力します。
1. 結果のパネルから **HRworks Single Sign-On** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-hrworks-single-sign-on"></a>HRworks Single Sign-on の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、HRworks Single Sign-On に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと HRworks Single Sign-On の関連ユーザーとの間にリンク関係を確立する必要があります。

HRworks Single Sign-on で Azure AD SSO を構成してテストするには、次の手順に従います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[HRworks Single Sign-On の SSO の構成](#configure-hrworks-single-sign-on-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[HRworks Single Sign-On のテスト ユーザーの作成](#create-hrworks-single-sign-on-test-user)** - HRworks Single Sign-On で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **HRworks Single Sign-On** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    **[サインオン URL]** ボックスに、`https://login.hrworks.de/?companyId=<COMPANY_ID>&directssologin=true` という形式で URL を入力します。

    > [!NOTE]
    > この値は実際のものではありません。 実際のサインオン URL でこの値を更新してください。 この値を取得するには、[HRworks Single Sign-On クライアント サポート チーム](https://www.hrworks.de/dienstleistungen/support/)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[フェデレーション メタデータ XML]** を探して **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

1. **[HRworks Single Sign-On のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に HRworks Single Sign-On へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[HRworks Single Sign-On]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-hrworks-single-sign-on-sso"></a>HRworks Single Sign-On の SSO の構成

1. HRworks Single Sign-On 内での構成を自動化するには、 **[拡張機能のインストール]** をクリックして **My Apps Secure Sign-in ブラウザー拡張機能** をインストールする必要があります。

    ![マイ アプリの拡張機能](common/install-myappssecure-extension.png)

1. ブラウザーに拡張機能を追加した後、 **[HRworks Single Sign-On のセットアップ]** をクリックすると、HRworks Single Sign-On アプリケーションに誘導されます。 そこから、管理者の資格情報を入力して HRworks Single Sign-On にサインインします。 ブラウザー拡張機能によりアプリケーションが自動的に構成され、手順 3 から 4 が自動化されます。

    ![セットアップの構成](common/setup-sso.png)

1. HRworks Single Sign-On を手動でセットアップする場合は、新しい Web ブラウザー ウィンドウを開き、管理者として HRworks Single Sign-On 企業サイトにサインインして、次の手順を実行します。

1. メニュー バーの左側で **[Administrator]\(管理者\)**  >  **[Basics]\(基本\)**  >  **[Security]\(セキュリティ\)**  >  **[Single Sign-on]\(シングル サインオン\)** の順にクリックし、以下の手順を実行します。

    ![Configure single sign-on](./media/hrworks-single-sign-on-tutorial/configure.png)

    a. **[Use Single Sign-on]\(シングル サインオンを使用する\)** をオンにします。

    b. **[Meta data input method]\(メタ データの入力方法\)** として **[XML Metadata]\(XML メタデータ\)** を選択します。

    c. **[Value for NameID]\(NameID の値\)** として **[Individual NameID identifier]\(個別の NameID 識別子\)** を選択します。

    d. Azure portal からダウンロードしたメタデータ XML をメモ帳で開き、その内容をコピーして **[Metadata]** テキスト ボックスに貼り付けます。

    e. **[保存]** をクリックします。

### <a name="create-hrworks-single-sign-on-test-user"></a>HRworks Single Sign-On のテスト ユーザーの作成

Azure AD ユーザーが HRworks Single Sign-On にサインインできるようにするには、ユーザーを HRworks Single Sign-On にプロビジョニングする必要があります。 HRworks Single Sign-On では、プロビジョニングは手動のタスクです。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. 管理者として HRworks Single Sign-On にサインインします。

1. メニュー バーの左側で、 **[Administrator]\(管理者\)**  >  **[Persons]\(人\)**  >  **[Persons]\(人\)**  >  **[New person]\(新しい人\)** の順にクリックします。

     ![[Persons]\(人\) および [New person]\(新しい人\) が選択されている HRworks ページを示すスクリーンショット。](./media/hrworks-single-sign-on-tutorial/persons.png)

1. ポップアップで、 **[Next]\(次へ\)** をクリックします。

    ![人に対して選択できる国の一覧を示すスクリーンショット。](./media/hrworks-single-sign-on-tutorial/new-person.png)

1. **[Create new person with country for legal terms]\(法的条項のための国を指定した新しい人の作成\)** ポップアップで、 **[First name]\(名\)** 、 **[Last name]\(姓\)** などのそれぞれの詳細を入力し、 **[Create]\(作成\)** をクリックします。

    ![人の名と姓を入力できるテキスト ボックスを示すスクリーンショット。](./media/hrworks-single-sign-on-tutorial/create-person.png)

## <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる HRworks Single Sign-On のサインオン URL にリダイレクトされます。 

* HRworks Single Sign-on のサインオン URL に直接移動し、そこからログイン フローを開始します。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [HRworks Single Sign-On] タイルをクリックすると、HRworks Single Sign-On のサインオン URL にリダイレクトされます。 マイ アプリの詳細については、[マイ アプリの概要](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

HRworks Single Sign-On を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-aad)をご覧ください。
