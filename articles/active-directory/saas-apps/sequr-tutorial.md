---
title: 'チュートリアル: Azure Active Directory と Genea Access Control の統合 | Microsoft Docs'
description: Azure Active Directory と Genea Access Control の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/17/2021
ms.author: jeedes
ms.openlocfilehash: 82c05f77781abdaea3b2c84aa1071656c206439a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104669865"
---
# <a name="tutorial-azure-active-directory-integration-with-genea-access-control"></a>チュートリアル: Azure Active Directory と Genea Access Control の統合

このチュートリアルでは、Genea Access Control と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Genea Access Control を統合すると、次のことができます。

* Genea Access Control にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Genea Access Control に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

Azure AD と Genea Access Control の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます
* Genea Access Control のシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Genea Access Control では、**SP Initiated SSO と IDP Initiated SSO** がサポートされます。
> [!NOTE]
> このアプリケーションの識別子は固定文字列値であるため、1 つのテナントで構成できるインスタンスは 1 つだけです。


## <a name="adding-genea-access-control-from-the-gallery"></a>ギャラリーからの Genea Access Control の追加

Azure AD への Genea Access Control の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Genea Access Control を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに、「**Genea Access Control**」と入力します。
1. 結果のパネルから **[Genea Access Control]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-genea-access-control"></a>Genea Access Control の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Genea Access Control に対する Azure AD SSO を構成してテストします。 SSO が機能するために、Azure AD ユーザーと Genea Access Control の関連ユーザーの間で、リンク関係を確立する必要があります。

Genea Access Control に対して Azure AD SSO を構成してテストするには、次の手順を行います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Genea Access Control の SSO の構成](#configure-genea-access-control-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Genea Access Control のテスト ユーザーの作成](#create-genea-access-control-test-user)** - Genea Access Control で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Genea Access Control** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次の手順を実行します。

    **[識別子]** ボックスに、`https://login.sequr.io` という URL を入力します。

5. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    a. **[サインオン URL]** テキスト ボックスに、URL として「`https://login.sequr.io`」と入力します。

    b. **[リレー状態]** テキストボックスに、この値を取得します。これについてはチュートリアルの後半で説明しています。

6. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションからの **証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

7. **[Genea Access Control のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

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

このセクションでは、B.Simon に Genea Access Control へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Genea Access Control]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。
## <a name="configure-genea-access-control-sso"></a>Genea Access Control の SSO の構成

1. 別の Web ブラウザー ウィンドウで、Genea Access Control 企業サイトに管理者としてサインインします。

1. 左側のナビゲーション パネルから、 **[統合]** をクリックします。

    ![ナビゲーション パネルから [統合] が選択された画面のスクリーンショット。](./media/sequr-tutorial/configure-1.png)

1. **[シングル サインオン]** セクションまで下方向にスクロールして、 **[管理]** をクリックします。

    ![[管理] ボタンが選択されている [シングル サインオン] セクションのスクリーンショット。](./media/sequr-tutorial/configure-2.png)

1. **[シングル サインオンの管理]** セクションで、次の手順を実行します。

    ![[シングル サインオンの管理] セクションのスクリーンショット。ここで、説明されている値を入力できます。](./media/sequr-tutorial/configure-3.png)

    a. **[ID プロバイダー のシングル サインオン URL]** テキストボックスに、Azure portal からコピーした **ログイン URL** の値を貼り付けます。

    b. Azure Portal からダウンロードした **証明書** ファイルをドラッグ アンド ドロップするか、証明書のコンテンツを手動で入力します。

    c. 構成を保存した後に、[リレー状態] の値が生成されます。 **[リレー状態]** の値をコピーして、Azure portal の **[基本的な SAML 構成]** セクションの **[リレー状態]** テキスト ボックスに貼り付けます。

    d. **[保存]** をクリックします。

### <a name="create-genea-access-control-test-user"></a>Genea Access Control のテスト ユーザーの作成

このセクションでは、Genea Access Control で Britta Simon というユーザーを作成します。 [Genea Access Control クライアント サポート チーム](mailto:support@sequr.io)と連携して、Genea Access Control プラットフォームにユーザーを追加してください。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

#### <a name="sp-initiated"></a>SP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Genea Access Control のサインオン URL にリダイレクトされます。  

* Genea Access Control のサインオン URL に直接移動し、そこからログイン フローを開始します。

#### <a name="idp-initiated"></a>IDP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックすると、SSO を設定した Genea Access Control に自動的にサインインされます 

また、Microsoft マイ アプリを使用して、任意のモードでアプリケーションをテストすることもできます。 マイ アプリで Genea Access Control タイルをクリックすると、SP モードで構成されている場合は、ログイン フローを開始するためのアプリケーション サインオン ページにリダイレクトされます。IDP モードで構成されている場合は、SSO を設定した Genea Access Control に自動的にサインインされます。 マイ アプリの詳細については、[マイ アプリの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Genea Access Control を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)をご覧ください。