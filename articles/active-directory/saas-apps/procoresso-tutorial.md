---
title: 'チュートリアル: Azure Active Directory と Procore SSO の統合 | Microsoft Docs'
description: Azure Active Directory と Procore SSO の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/21/2021
ms.author: jeedes
ms.openlocfilehash: d7e6678db28f6edc2dfcaf36a656a309418ead8f
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132341903"
---
# <a name="tutorial-azure-active-directory-integration-with-procore-sso"></a>チュートリアル: Azure Active Directory と Procore SSO の統合

このチュートリアルでは、Procore SSO と Azure Active Directory (Azure AD) を統合する方法について説明します。 Procore SSO と Azure AD を統合すると、次のことができるようになります。

* Procore SSO にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Procore SSO に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

Procore SSO と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Procore SSO シングル サインオン対応のサブスクリプション。

> [!NOTE]
> この統合は、Azure AD 米国政府クラウド環境から利用することもできます。 このアプリケーションは、Azure AD 米国政府クラウドのアプリケーション ギャラリーにあります。パブリック クラウドの場合と同じように構成してください。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Procore SSO では、**IDP** Initiated SSO がサポートされています。

## <a name="add-procore-sso-from-the-gallery"></a>ギャラリーからの Procore SSO の追加

Azure AD への Procore SSO の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Procore SSO を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Procore SSO**」と入力します。
1. 結果のパネルから **[Procore SSO]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-procore-sso"></a>Procore SSO のための Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Procore SSO を使用した Azure AD SSO を構成してテストします。 SSO を機能させるために、Azure AD ユーザーと Procore SSO の関連ユーザーの間にリンク関係を確立する必要があります。

Procore SSO を使用した Azure AD SSO を構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Procore SSO の構成](#configure-procore-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Procore SSO のテスト ユーザーの作成](#create-procore-sso-test-user)** - Procore SSO 内で B.Simon に対応するユーザーを作成し、Azure AD のこのユーザーにリンクします。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Procore SSO** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. アプリは Azure と事前に統合済みであるため、 **[基本的な SAML 構成]** セクションで実行が必要な手順はありません。

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして、要件のとおりに指定したオプションから **フェデレーション メタデータ XML** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

6. **[Procore SSO のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

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

このセクションでは、B.Simon に Procore SSO へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Procore SSO]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-procore-sso"></a>Procore SSO の構成 

1. **Procore SSO** 側のシングル サインオンを構成するために、Procore の企業サイトに管理者としてサインインします。

2. [TOOLBOX] \(ツールボックス) ドロップ ダウンから **[Admin] \(管理)** をクリックして、SSO 設定ページを開きます。

    ![[Directory]\(ディレクトリ\) が選択された Procore の企業サイトを示すスクリーンショット。](./media/procoresso-tutorial/admin.png)

3. 以下の説明に従って、各ボックスに値を貼り付けます。

    ![[Add a Person]\(ユーザーの追加\) ダイアログ ボックスを示すスクリーンショット。](./media/procoresso-tutorial/setting.png)   

    a. **[Single Sign On Issuer URL]\(シングル サインオン発行者 URL\)** テキスト ボックスに、Azure portal からコピーした **Azure AD ID** の値を貼り付けます。

    b. **[SAML Sign On Target URL]\(SAML サインオン ターゲット URL\)** ボックスに、Azure portal からコピーした **ログイン URL** の値を貼り付けます。

    c. 次に、先ほど Azure portal からダウンロードした **フェデレーション メタデータの XML** を開いて、**X509Certificate** という名前のタグ内の証明書をコピーします。 コピーした値を、 **[Single Sign On x509 Certificate] \(シングル サインオン x509 証明書)** ボックスに貼り付けます。

4. **[Save Changes] \(変更を保存)** をクリックします。

5. 上記の設定後、Procore へのログインで経由する **ドメイン名** (例: **contoso.com**) を [Procore のサポート チーム](https://support.procore.com/)に送信して、このドメインのフェデレーション SSO を有効化してもらう必要があります。

### <a name="create-procore-sso-test-user"></a>Procore SSO のテスト ユーザーを作成する

以下の手順に従って、Procore SSO の側で Procore テスト ユーザーを作成します。

1. Procore 企業サイトに管理者としてサインインします。    

2. [TOOLBOX] \(ツールボックス) ドロップ ダウンで **[Directory] \(ディレクトリ)** をクリックして、企業のディレクトリ ページを開きます。

    ![ツールボックスから [Directory]\(ディレクトリ\) が選択された Procore の企業サイトを示すスクリーンショット。](./media/procoresso-tutorial/directory.png)

3. **[Add a Person] \(ユーザーの追加)** オプションをクリックしてフォームを開き、次の手順を行います。

    ![ユーザー情報を入力できる [Add a person to Boylan Construction]\(Boylan Construction にユーザーを追加する\) を示すスクリーンショット。](./media/procoresso-tutorial/user.png)

    a. **[First Name] \(名)** テキストボックスに、ユーザーの名を入力します (この例では **Britta**).

    b. **[Last Name] \(姓)** テキストボックスに、ユーザーの姓を入力します (この例では **Simon**).

    c. **[Email Address] \(メール アドレス)** ボックスに、ユーザーのメール アドレスを入力します (この例では BrittaSimon@contoso.com)。

    d. **[Permission Template] \(アクセス許可テンプレート)** で **[Apply Permission Template Later] \(アクセス許可テンプレートは後で適用する)** を選択します。

    e. **Create** をクリックしてください。

4. 新しく追加された連絡先の詳細を確認して更新します。

    ![ユーザー設定を確認できる編集ページを示すスクリーンショット。](./media/procoresso-tutorial/details.png)

5. メールによる招待状が必要な場合は **[Save and Send Invitiation]\(保存して招待状を送信\)** を、直接保存する場合は **[Save]\(保存\)** をクリックして、ユーザー登録を完了します。
    
    ![招待状を保存して送信できる [Current Project Settings]\(現在のプロジェクトの設定\) を示すスクリーンショット。](./media/procoresso-tutorial/save.png)

## <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。

* Azure portal で [このアプリケーションをテストします] をクリックすると、SSO を設定した Procore SSO に自動的にサインインします。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [Procore SSO] タイルをクリックすると、SSO を設定した Procore SSO に自動的にサインインします。 マイ アプリの詳細については、[マイ アプリの概要](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Procore SSO を構成した後、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を適用する方法をご覧ください](/cloud-app-security/proxy-deployment-aad)。
