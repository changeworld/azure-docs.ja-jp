---
title: 'チュートリアル: Azure Active Directory と Yodeck の統合 | Microsoft Docs'
description: Azure Active Directory と Yodeck の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/02/2021
ms.author: jeedes
ms.openlocfilehash: 56f02ba6231570988257d24e6f8ee60aa1beedf5
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132319956"
---
# <a name="tutorial-azure-active-directory-integration-with-yodeck"></a>チュートリアル: Azure Active Directory と Yodeck の統合

このチュートリアルでは、Yodeck と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Yodeck を統合すると、次のことができます。

* Yodeck にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Yodeck に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

Yodeck と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Yodeck でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Yodeck では、**SP** と **IDP** Initiated SSO がサポートされます。

> [!NOTE]
> このアプリケーションの識別子は固定文字列値であるため、1 つのテナントで構成できるインスタンスは 1 つだけです。

## <a name="add-yodeck-from-the-gallery"></a>ギャラリーからの Yodeck の追加

Azure AD への Yodeck の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Yodeck を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに、 **[Yodeck]** と入力します。
1. 結果のパネルから **[Yodeck]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-yodeck"></a>Yodeck の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Yodeck で Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと Yodeck の関連ユーザーとの間にリンク関係を確立する必要があります。

Yodeck で Azure AD SSO を構成してテストするには、次の手順に従います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Yodeck SSO の構成](#configure-yodeck-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Yodeck のテスト ユーザーの作成](#create-yodeck-test-user)** - Yodeck で B.Simon に対応するユーザーを作成し、Azure AD のこのユーザーにリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Yodeck** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次の手順を実行します。

    **[識別子]** ボックスに、`https://app.yodeck.com/api/v1/account/metadata/` という URL を入力します。

5. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    **[サインオン URL]** テキスト ボックスに、URL として「`https://app.yodeck.com/login`」と入力します。

6. **Set up Single Sign-On with SAML\(SAML でのシングルサインオンの設定** ページの **SAML 署名証明書** セクションで、コピー ボタンをクリックして **App Federation Metadata Url\(アプリのフェデレーション メタデータ URL)** をコピーして、コンピューターに保存します。

    ![証明書のダウンロードのリンク](common/copy-metadataurl.png)

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

このセクションでは、B.Simon に Yodeck へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Yodeck]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-yodeck-sso"></a>Yodeck SSO の構成

1. **Yodeck** 内での構成を自動化するには、 **[拡張機能のインストール]** をクリックして **My Apps Secure Sign-in ブラウザー拡張機能** をインストールする必要があります。

    ![[拡張機能のインストール] ボタンを示すスクリーンショット。](./media/target-process-tutorial/install_extension.png)

1. ブラウザーに拡張機能を追加した後、 **[Yodeck のセットアップ]** をクリックすると、Yodeck アプリケーションに移動します。 そこから、管理者の資格情報を入力して Yodeck にサインインします。 ブラウザー拡張機能によりアプリケーションが自動的に構成され、手順 3 から 5 が自動化されます。

    ![セットアップの構成](common/setup-sso.png)

    **アプリケーションを手動で構成する場合は、次の手順を実行します。**

1. 別の Web ブラウザー ウィンドウで、Yodeck 企業サイトに管理者としてサインインします。

1. ページの右上隅にある **[ユーザー設定]** オプションをクリックし、**[アカウント設定]** を選択します。

    ![ユーザーに対して [アカウント設定] が選択されていることを示すスクリーンショット。](./media/yodeck-tutorial/account.png)

1. **[SAML]** をクリックし、次の手順を実行します。

    ![[SAML] タブを示すスクリーンショット。ここで以下の手順を行います。](./media/yodeck-tutorial/configure.png)

    a. **[URL からインポートする]** を選択します。

    b. **[URL]** テキスト ボックスに、Azure Portal からコピーした **[アプリのフェデレーション メタデータ URL]** 値を貼り付け、**[インポート]** をクリックします。
    
    c. **[アプリのフェデレーション メタデータ URL]** をインポートすると、残りのフィールドが自動的に設定されます。

    d. **[保存]** をクリックします。

### <a name="create-yodeck-test-user"></a>Yodeck のテスト ユーザーの作成

Azure AD ユーザーが Yodeck にサインインできるようにするには、ユーザーを Yodeck にプロビジョニングする必要があります。 Yodeck の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. Yodeck 企業サイトに管理者としてサインインします。

2. ページの右上隅にある [ユーザー設定] オプションをクリックし、[ユーザー] を選択します。

    ![ユーザーに対して選択された [Users]\(ユーザー\) を示すスクリーンショット。](./media/yodeck-tutorial/user.png)

3. **[+User]** をクリックして **[ユーザーの詳細]** タブを開きます。

    ![[Users]\(ユーザー\) ボタンを示すスクリーンショット。](./media/yodeck-tutorial/user-details.png)

4. [**User Details**] ダイアログ ページで、以下の手順を実行します。

    ![[User Details]\(ユーザーの詳細\) タブを示すスクリーンショット。ここで以下の手順を行います。](./media/yodeck-tutorial/user-page.png)

    a. **[名]** ボックスに、ユーザーの名を入力します (この例では **Britta**)。

    b. **[Last Name]\(姓\)** ボックスに、ユーザーの姓を入力します (この例では **Simon**)。

    c. **[Email]\(メール\)** ボックスに、ユーザーのメール アドレス (brittasimon@contoso.com など) を入力します。

    d. 組織の要件に従って、適切な **[アカウントのアクセス許可]** オプションを選択します。
    
    e. **[保存]** をクリックします。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

#### <a name="sp-initiated"></a>SP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Yodeck のサインオン URL にリダイレクトされます。  

* Yodeck のサインオン URL に直接移動し、そこからログイン フローを開始します。

#### <a name="idp-initiated"></a>IDP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックすると、SSO を設定した Yodeck に自動的にサインインされます。 

また、Microsoft マイ アプリを使用して、任意のモードでアプリケーションをテストすることもできます。 マイ アプリで [Yodeck] タイルをクリックすると、SP モードで構成されている場合は、ログイン フローを開始するためのアプリケーション サインオン ページにリダイレクトされます。IDP モードで構成されている場合は、SSO を設定した Yodeck に自動的にサインインされます。 マイ アプリの詳細については、[マイ アプリの概要](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Yodeck を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を適用する方法をご覧ください](/cloud-app-security/proxy-deployment-aad)。
