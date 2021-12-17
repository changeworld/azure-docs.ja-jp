---
title: 'チュートリアル: Azure Active Directory シングル サインオン (SSO) と True Office Learning - LIO の統合 | Microsoft Docs'
description: Azure Active Directory と True Office Learning - LIO の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/26/2021
ms.author: jeedes
ms.openlocfilehash: 66a9a5290c956b8fc254ec6308f62410c845dbbb
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132332879"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-true-office-learning---lio"></a>チュートリアル: Azure Active Directory シングル サインオン (SSO) と True Office Learning - LIO の統合

このチュートリアルでは、True Office Learning - LIO と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と True Office Learning - LIO を統合すると、次のことができます。

* True Office Learning - LIO にアクセスできるユーザーを Azure AD で制御します。
* ユーザーが自分の Azure AD アカウントを使用して True Office Learning - LIO に自動的にサインインできるようにします。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* True Office Learning - LIO でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* True Office Learning - LIO では、**SP** Initiated SSO がサポートされます。

## <a name="add-true-office-learning---lio-from-the-gallery"></a>ギャラリーから True Office Learning - LIO を追加する

Azure AD への True Office Learning - LIO の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に True Office Learning - LIO を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**True Office Learning - LIO**」と入力します。
1. 結果パネルから **[True Office Learning - LIO]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-true-office-learning---lio"></a>True Office Learning - LIO の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、True Office Learning - LIO に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと True Office Learning - LIO の関連ユーザーとの間にリンク関係を確立する必要があります。

True Office Learning - LIO に対して Azure AD SSO を構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[True Office Learning - LIO の SSO の構成](#configure-true-office-learning---lio-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[True Office Learning - LIO のテスト ユーザーの作成](#create-true-office-learning---lio-test-user)** - True Office Learning - LIO で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **True Office Learning - LIO** アプリケーション統合ページで、 **[管理]** セクションを探して、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    1. **[識別子 (エンティティ ID)]** ボックスに、次のパターンを使用して URL を入力します。`https://learn-sso.trueoffice.com/simplesaml/module.php/saml/sp/metadata.php/<CUSTOMER_NAME>-sp`

    1. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。`https://learn-sso.trueoffice.com/<CUSTOMER_NAME>`
    
        > [!NOTE]
        > これらは実際の値ではありません。 これらの値を実際の識別子とサインオン URL で更新してください。 これらの値を取得するには、[True Office Learning - LIO クライアント サポート チーム](mailto:service@trueoffice.com)にお問い合わせください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[フェデレーション メタデータ XML]** を探して **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

1. **[True Office Learning - LIO のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に True Office Learning - LIO へのアクセスを許可することで、Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[True Office Learning - LIO]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-true-office-learning---lio-sso"></a>True Office Learning - LIO の SSO の構成

構成に関する質問やメタデータのコピーを要求するには、[True Office Learning - LIO サポート チーム](mailto:service@trueoffice.com)にお問い合わせください。 リクエストには、次の情報を含めてください。
* 会社名。
* 会社 ID (わかっている場合)。
* 既存または新規の構成。

### <a name="create-true-office-learning---lio-test-user"></a>True Office Learning - LIO のテスト ユーザーの作成

このセクションでは、True Office Learning - LIO で Britta Simon というユーザーを作成します。 [True Office Learning - LIO サポート チーム](mailto:service@trueoffice.com)と連携して、True Office Learning - LIO プラットフォームにユーザーを追加してください。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、以下のオプションを使用して自分の Azure AD のシングル サインオン構成をテストします。

* Azure portal で、 **[このアプリケーションをテストします]** を選択します。 ログイン フローを開始できる True Office Learning - LIO のサインオン URL にリダイレクトされます。 
* True Office Learning - LIO のサインオン URL に直接移動し、そのサイトからのログイン フローを開始します。
* Microsoft マイ アプリを使用することができます。 マイ アプリで [True Office Learning - LIO] タイルを選択すると、True Office Learning - LIO のサインオン URL にリダイレクトされます。 マイ アプリの詳細については、[マイ アプリの概要](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

True Office Learning - LIO を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を適用する方法をご覧ください](/cloud-app-security/proxy-deployment-aad)。
