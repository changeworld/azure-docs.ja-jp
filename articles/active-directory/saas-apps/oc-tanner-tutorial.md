---
title: 'チュートリアル: Azure Active Directory と O.C. の統合 Tanner - AppreciateHub の統合 | Microsoft Docs'
description: Azure Active Directory と O.C. の間でシングル サインオンを構成する方法について説明します。 Tanner - AppreciateHub で Britta Simon というユーザーを作成することです。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/07/2021
ms.author: jeedes
ms.openlocfilehash: 24c3258ec45f48d80d47463d018a2e29eec2651e
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132287668"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-oc-tanner---appreciatehub"></a>チュートリアル: Azure Active Directory シングル サインオン (SSO) と O.C. Tanner - AppreciateHub の統合 Azure AD で制御できます。

このチュートリアルの目的は、O.C.  Tanner - AppreciateHub と Azure Active Directory (Azure AD) を統合する方法を示すことです。 O.C. Tanner - AppreciateHub と Azure AD を統合すると、次のことができます。

* O.C. Tanner - AppreciateHub にアクセスできるユーザーを Azure AD で制御できます。 Tanner - AppreciateHub で Britta Simon というユーザーを作成することです。
* ユーザーが自分の Azure AD アカウントを使用して O.C. Tanner - AppreciateHub に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* O.C. Tanner - AppreciateHub でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* O.C. Tanner - AppreciateHub では、**IDP** Initiated SSO がサポートされます。

## <a name="add-oc-tanner---appreciatehub-from-the-gallery"></a>ギャラリーからの O.C. Tanner - AppreciateHub の追加

Azure AD への O.C.  Tanner - AppreciateHub の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に O.C.  Tanner - AppreciateHub を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**O.C. Tanner - AppreciateHub**」と入力します。
1. 結果のパネルから **[O.C. Tanner - AppreciateHub]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-oc-tanner---appreciatehub"></a>O.C. の Azure AD SSO の構成とテスト Azure AD で制御できます。

O.C. Tanner - AppreciateHub で Azure AD SSO を構成し、テストします。**B.Simon** というテスト ユーザーを使用します。 SSO が機能するために、Azure AD ユーザーと O.C. Tanner - AppreciateHub の関連ユーザーとの間にリンク関係を確立する必要があります。 Tanner - AppreciateHub で Britta Simon というユーザーを作成することです。

O.C. Tanner - AppreciateHub で構成するには、次の手順に従います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[O.C. Tanner - AppreciateHub の SSO の構成](#configure-oc-tanner---appreciatehub-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[O.C. Tanner - AppreciateHub のテスト ユーザーの作成](#create-oc-tanner---appreciatehub-test-user)** - O.C. Tanner - AppreciateHub で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **O.C. Tanner - AppreciateHub** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. アプリは Azure と事前に統合済みであるため、 **[基本的な SAML 構成]** セクションで実行が必要な手順はありません。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして、要件のとおりに指定したオプションから **フェデレーション メタデータ XML** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

1. **[O.C. Tanner - AppreciateHub の設定]** セクションで、要件に従って適切な URL をコピーします。

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

このセクションでは、B.Simon に O.C. Tanner - AppreciateHub へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。 Tanner - AppreciateHub で Britta Simon というユーザーを作成することです。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[O.C. Tanner - AppreciateHub]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-oc-tanner---appreciatehub-sso"></a>O.C.  Tanner - AppreciateHub の SSO

**O.C. Tanner - AppreciateHub** 側でシングル サインオンを構成するには、ダウンロードした **フェデレーション メタデータ XML** と Azure portal からコピーした適切な URL を [O.C. Tanner - AppreciateHub サポート チーム](mailto:sso@octanner.com)に送信する必要があります。 サポート チームはこれを設定して、SAML SSO 接続が両方の側で正しく設定されるようにします。

### <a name="create-oc-tanner---appreciatehub-test-user"></a>O.C.  Tanner - AppreciateHub テスト ユーザーの作成

このセクションの目的は、O.C.  Tanner - AppreciateHub で Britta Simon というユーザーを作成することです。

**O.C. Tanner - AppreciateHub で Britta Simon というユーザーを作成するには、次の手順に従います。**

[O.C. Tanner - AppreciateHub サポート チーム](mailto:sso@octanner.com)に、Azure AD 内のユーザー名 Britta Simon と同じ値の nameID 属性を持つユーザーを作成することを依頼します。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。

* Azure portal で [このアプリケーションをテストします] をクリックすると、SSO を設定した O.C. Tanner - AppreciateHub に自動的にサインインされます。

* Microsoft マイ アプリを使用することができます。 アクセス パネルで [O.C.  Tanner - AppreciateHub タイルをクリックすると、SSO を設定した O.C. Tanner - AppreciateHub に自動的にサインインされます。 マイ アプリの詳細については、[マイ アプリの概要](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

O.C. Tanner - AppreciateHub を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を適用する方法をご覧ください](/cloud-app-security/proxy-deployment-any-app)。
