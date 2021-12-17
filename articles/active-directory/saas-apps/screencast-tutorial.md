---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と Screencast-O-Matic の統合 | Microsoft Docs
description: Azure Active Directory と Screencast-O-Matic の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/31/2021
ms.author: jeedes
ms.openlocfilehash: c1c64606600679e49ffc63c97f58a4a9a7764687
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132279635"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-screencast-o-matic"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Screencast-O-Matic の統合

このチュートリアルでは、Screencast-O-Matic と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Screencast-O-Matic を統合すると、次のことができます。

* Screencast-O-Matic にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Screencast-O-Matic に自動的にサインイン (シングル サインオン) できるようにすることが可能です。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Screencast-O-Matic でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Screencast-O-Matic では、**SP** Initiated SSO がサポートされます。
* Screencast-O-Matic では、**Just-In-Time** ユーザー プロビジョニングがサポートされます。

> [!NOTE]
> このアプリケーションの識別子は固定文字列値であるため、1 つのテナントで構成できるインスタンスは 1 つだけです。

## <a name="add-screencast-o-matic-from-the-gallery"></a>ギャラリーからの Screencast-O-Matic の追加

Azure AD への Screencast-O-Matic の統合を構成するには、ギャラリーからマネージド SaaS アプリのリストに Screencast-O-Matic を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに、「**Screencast-O-Matic**」と入力します。
1. 結果パネルで **[Screencast-O-Matic]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-screencast-o-matic"></a>Screencast-O-Matic のための Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Screencast-O-Matic に対する Azure AD SSO を構成してテストします。 SSO を機能させるために、Azure AD ユーザーと Screencast-O-Matic の関連ユーザーとの間にリンク関係を確立する必要があります。

Screencast-O-Matic 用に Azure AD SSO を構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Screencast-O-Matic SSO の構成](#configure-screencast-o-matic-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Screencast-O-Matic のテスト ユーザーの作成](#create-screencast-o-matic-test-user)** - Screencast-O-Matic で B.Simon に対応するユーザーを作成し、Azure AD のこのユーザーにリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Screencast-O-Matic** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    **[サインオン URL]** ボックスに、`https://screencast-o-matic.com/<InstanceName>` という形式で URL を入力します。

    > [!NOTE]
    > この値は実際のものではありません。 実際のサインオン URL でこの値を更新してください。 この値を取得する場合は、[Screencast-O-Matic クライアント サポート チーム](mailto:support@screencast-o-matic.com)にお問い合わせください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[フェデレーション メタデータ XML]** を探して **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

1. **[Set up Screencast-O-Matic]\(Screencast-O-Matic の設定\)** セクションで、要件に基づく適切な URL をコピーします。

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

このセクションでは、B.Simon に Screencast-O-Matic へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Screencast-O-Matic]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-screencast-o-matic-sso"></a>Screencast-O-Matic SSO の構成

1. Screencast-O-Matic 内での構成を自動化するには、 **[拡張機能のインストール]** をクリックして **My Apps Secure Sign-in ブラウザー拡張機能** をインストールする必要があります。

    ![マイ アプリの拡張機能](common/install-myappssecure-extension.png)

1. ブラウザーに拡張機能を追加した後、 **[Screencast-O-Matic のセットアップ]** をクリックすると、Screencast-O-Matic アプリケーションに誘導されます。 そこから、管理者の資格情報を入力して Screencast-O-Matic にサインインします。 ブラウザー拡張機能により、アプリケーションが自動的に構成され、手順 3. から 10. が自動化されます。

    ![セットアップの構成](common/setup-sso.png)

1. Screencast-O-Matic を手動でセットアップする場合は、新しい Web ブラウザー ウィンドウを開き、管理者として Screencast-O-Matic 企業サイトにサインインして、次の手順を実行します。

1. **[サブスクリプション]** をクリックします。

    ![[サブスクリプション] を示すスクリーンショット。](./media/screencast-tutorial/subscribe.png)

1. **[Access page]\(アクセス ページ\)** セクションで、 **[Setup]\(セットアップ\)** をクリックします。

    ![[Setup]\(セットアップ\) ボタンが選択されている [Access page]\(アクセス ページ\) セクションを示すスクリーンショット。](./media/screencast-tutorial/setup.png)

1. **[Setup Access Page]\(アクセス ページのセットアップ\)** で、次の手順を実行します。

1. **[Access URL]\(アクセス URL\)** セクションで、指定されたテキスト ボックスにインスタンス名を入力します。

    ![インスタンス名のテキストボックスが強調表示されている [Access URL]\(アクセス URL\) セクションを示すスクリーンショット。](./media/screencast-tutorial/access-page.png)

1. **[SAML User Restriction (optional)]\(SAML ユーザーの制限 (省略可能)\)** セクションで、 **[Require Domain User]\(ドメイン ユーザーが必要\)** を選択します。

1. **[Upload IDP Metadata XML File]\(IDP メタデータ XML ファイルのアップロード\)** で、 **[ファイルの選択]** をクリックして、Azure portal からダウンロードしたメタデータをアップロードします。

1. **[OK]** をクリックします。

    ![アクセスを示すスクリーンショット。](./media/screencast-tutorial/metadata.png)

### <a name="create-screencast-o-matic-test-user"></a>Screencast-O-Matic のテスト ユーザーの作成

このセクションでは、Britta Simon というユーザーを Screencast-O-Matic に作成します。 Screencast-O-Matic では、Just-In-Time ユーザー プロビジョニングがサポートされています。この設定は既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 Screencast-O-Matic にユーザーがまだ存在していない場合は、認証後に新しく作成されます。 ユーザーを手動で作成する必要がある場合は、[Screencast-O-Matic クライアント サポート チーム](mailto:support@screencast-o-matic.com)にお問い合わせください。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Screencast-O-Matic のサインオン URL にリダイレクトされます。 

* Screencast-O-Matic のサインオン URL に直接移動し、そこからログイン フローを開始します。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [Screencast-O-Matic] タイルをクリックすると、Screencast-O-Matic のサインオン URL にリダイレクトされます。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Screencast-O-Matic を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-aad)をご覧ください。
