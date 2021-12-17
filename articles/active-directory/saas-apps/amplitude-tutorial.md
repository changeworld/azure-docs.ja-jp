---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と Amplitude の統合 | Microsoft Docs
description: Azure Active Directory と Amplitude の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/16/2021
ms.author: jeedes
ms.openlocfilehash: 780e94b678a6155c0c5f6e0bc93cc584a764af40
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132324424"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-amplitude"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Amplitude の統合

このチュートリアルでは、Amplitude と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Amplitude を統合すると、次のことができます。

* Amplitude にアクセスする Azure AD ユーザーを管理する。
* ユーザーが自分の Azure AD アカウントを使用して Amplitude に自動的にサインインできるようにします。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Amplitude でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Amplitude では、**SP Initiated SSO と IDP Initiated SSO** がサポートされます。
* Amplitude では、**Just-In-Time** ユーザー プロビジョニングがサポートされます。

> [!NOTE]
> このアプリケーションの識別子は固定文字列値であるため、1 つのテナントで構成できるインスタンスは 1 つだけです。

## <a name="add-amplitude-from-the-gallery"></a>ギャラリーからの Amplitude の追加

Azure AD への Amplitude の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Amplitude を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Amplitude**」と入力します。
1. 結果のパネルから **[Amplitude]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-amplitude"></a>Amplitude の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Amplitude に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと Amplitude の関連ユーザーとの間にリンク関係を確立する必要があります。

Amplitude に対して Azure AD SSO を構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Amplitude SSO の構成](#configure-amplitude-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Amplitude テスト ユーザーの作成](#create-amplitude-test-user)** - Amplitude で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Amplitude** アプリケーション統合ページで、 **[管理]** セクションを探して、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次の手順を実行します。

    a. **[識別子]** ボックスに、`https://amplitude.com/saml/sso/metadata` という URL を入力します。

    b. **[応答 URL]** ボックスに、`https://analytics.amplitude.com/saml/sso/<UNIQUE_ID>` のパターンを使用して URL を入力します

    > [!NOTE]
    > 応答 URL 値は、実際の値ではありません。 応答 URL はこのチュートリアルの後半で取得します。

1. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    **[サインオン URL]** テキスト ボックスに、URL として「`https://analytics.amplitude.com/sso`」と入力します。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[フェデレーション メタデータ XML]** を探して **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

1. **[Amplitude のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B. Simon に Amplitude へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Amplitude]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-amplitude-sso"></a>Amplitude SSO の構成

1. Amplitude 内での構成を自動化するには、 **[拡張機能のインストール]** をクリックして **My Apps Secure Sign-in ブラウザー拡張機能** をインストールする必要があります。

    ![マイ アプリの拡張機能](common/install-myappssecure-extension.png)

1. ブラウザーに拡張機能を追加した後、 **[Amplitude のセットアップ]** をクリックすると、Amplitude アプリケーションに移動します。 そこから、管理者の資格情報を入力して Amplitude にサインインします。 ブラウザー拡張機能によりアプリケーションが自動的に構成され、手順 3 ～ 6 が自動化されます。

    ![セットアップの構成](common/setup-sso.png)

1. Amplitude を手動でセットアップする場合は、新しい Web ブラウザー ウィンドウを開き、管理者として Amplitude 企業サイトにサインインして、次の手順を実行します。

1. 左側のナビゲーション バーから **[Plan Admin]\(プラン管理者\)** をクリックします。

    ![このスクリーンショットは、[Plan Admin]\(プラン管理者\) が選択された状態の Amplitude メニューを示しています。](./media/amplitude-tutorial/plan-tab.png)

1. **[SSO Integration]\(SSO の統合\)** から **[Microsoft Azure Active Directory Metadata]\(Microsoft Azure Active Directory のメタデータ\)** を選択します。

    ![このスクリーンショットは、[Microsoft Azure Active Directory Metadata]\(Microsoft Azure Active Directory のメタデータ\) リンクがコールアウトされた状態の [Plan Admin]\(プラン管理者\) ペインを示しています。](./media/amplitude-tutorial/metadata.png)

1. **[Setup Single Sign-On]\(シングル サインオンの設定\)** セクションで、次の手順を実行します。

    ![このスクリーンショットは、この手順で説明されている値が表示された状態の [Setup Single Sign-On]\(シングル サインオンの設定\) セクションを示しています。](./media/amplitude-tutorial/configuration.png)

    a. Azure Portal からダウンロードした **メタデータ XML** をメモ帳で開き、内容を **[Microsoft Azure Active Directory Metadata]\(Microsoft Azure Active Directory のメタデータ\)** テキストボックスに貼り付けます。

    b. **[Reply URL (ACS)]\(応答 URL (ACS)\)** の値をコピーして Azure portal 上の **[基本的な SAML 構成]** にある **[応答 URL]** テキスト ボックスに貼り付けます。

    c. **[保存]**

### <a name="create-amplitude-test-user"></a>Amplitude のテスト ユーザーの作成

このセクションでは、B.Simon というユーザーを Amplitude に作成します。 Amplitude では、Just-In-Time ユーザー プロビジョニングがサポートされ、既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 Amplitude にユーザーがまだ存在していない場合は、認証後に新しく作成されます。

> [!Note]
> ユーザーを手動で作成する必要がある場合は、[Amplitude サポート チーム](https://amplitude.zendesk.com)にお問い合わせください。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

#### <a name="sp-initiated"></a>SP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Amplitude のサインオン URL にリダイレクトされます。  

* Amplitude のサインオン URL に直接移動し、そこからログイン フローを開始します。

#### <a name="idp-initiated"></a>IDP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックすると、SSO を設定した Amplitude に自動的にサインインします。 

また、Microsoft マイ アプリを使用して、任意のモードでアプリケーションをテストすることもできます。 マイ アプリで [Amplitude] タイルをクリックすると、SP モードで構成されている場合は、ログイン フローを開始するためのアプリケーション サインオン ページにリダイレクトされます。IDP モードで構成されている場合は、SSO を設定した Amplitude に自動的にサインインします。 マイ アプリの詳細については、[マイ アプリの概要](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Amplitude を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を適用する方法をご覧ください](/cloud-app-security/proxy-deployment-aad)。
