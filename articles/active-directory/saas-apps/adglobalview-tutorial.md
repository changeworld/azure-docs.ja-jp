---
title: 'チュートリアル: Azure Active Directory シングル サインオン (SSO) と ADP Globalview (非推奨) の統合 | Microsoft Docs'
description: Azure Active Directory と ADP Globalview (非推奨) の間でシングル サインオンを構成する方法について説明します。
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
ms.openlocfilehash: 08b1294436ead372234104008a48ca23e56de389
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104589312"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-adp-globalview-deprecated"></a>チュートリアル: Azure Active Directory シングル サインオン (SSO) と ADP Globalview (非推奨) の統合

このチュートリアルでは、ADP Globalview (非推奨) と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と ADP Globalview (非推奨) を統合すると、次のことができます。

* ADP Globalview (非推奨) にアクセスするユーザーを Azure AD で制御する。
* ユーザーが Azure AD アカウントを使用して ADP Globalview (非推奨) に自動的にサインインできるようにする。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* ADP Globalview (非推奨) のシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* ADP Globalview (非推奨) では、**IDP**-initiated SSO がサポートされています。

## <a name="adding-adp-globalview-deprecated-from-the-gallery"></a>ギャラリーからの ADP Globalview (非推奨) の追加

ADP Globalview (非推奨) の Azure AD への統合を構成するには、ADP Globalview (非推奨) をギャラリーからマネージド SaaS アプリの一覧に追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**ADP Globalview (非推奨)** 」と入力します。
1. 結果のパネルから **[ADP Globalview (非推奨)]** を選択し、このアプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-adp-globalview-deprecated"></a>ADP Globalview (非推奨) に対する Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、ADP Globalview (非推奨) に対する Azure AD SSO を構成してテストします。 SSO を機能させるためには、Azure AD ユーザーと ADP Globalview (非推奨) の関連ユーザーとの間にリンク関係を確立する必要があります。

ADP Globalview (非推奨) に対する Azure AD SSO を構成してテストするには、次の手順を行います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[ADP Globalview (非推奨) SSO の構成](#configure-adp-globalview-deprecated-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[ADP Globalview (非推奨) テスト ユーザーの作成](#create-adp-globalview-deprecated-test-user)** - ADP Globalview (非推奨) で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **ADP Globalview (非推奨)** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次のフィールドの値を入力します。

    
    **[識別子]** テキスト ボックスに、次のいずれかのパターンを使用して URL を入力します。

    | 識別子 |
    | ----------- |
    | `https://<subdomain>.globalview.adp.com/federate` |
    | `https://<subdomain>.globalview.adp.com/federate2` |
    |

    > [!NOTE]
    > これは実際の値ではありません。 この値を実際の識別子で更新してください。 この値を取得するには、[ADP Globalview (非推奨) クライアント サポート チーム](https://www.adp.com/contact-us/overview.aspx)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** を見つけて、 **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

1. **[ADP Globalview t (非推奨) のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、ADP Globalview (非推奨) へのアクセスを許可することで、B.Simon が Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で、 **[ADP Globalview　(非推奨)]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-adp-globalview-deprecated-sso"></a>ADP Globalview (非推奨) SSO の構成

**ADP Globalview (非推奨)** 側でシングル サインオンを構成するには、ダウンロードした **証明書 (Base64)** と Azure portal からコピーした適切な URL を [ADP Globalview (非推奨) サポート チーム](https://www.adp.com/contact-us/overview.aspx)に送信する必要があります。 サポート チームはこれを設定して、SAML SSO 接続が両方の側で正しく設定されるようにします。

### <a name="create-adp-globalview-deprecated-test-user"></a>ADP Globalview (非推奨) テスト ユーザーの作成

このセクションでは、ADP Globalview (非推奨) で B.Simon というユーザーを作成します。 [ADP GlobalView (非推奨) サポート チーム](https://www.adp.com/contact-us/overview.aspx)と協力し、ADP GlobalView (非推奨) プラットフォームにユーザーを追加してください。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。

* Azure portal で [このアプリケーションをテストします] をクリックすると、SSO を設定した ADP Globalview (非推奨) に自動的にサインインします

* Microsoft マイ アプリを使用することができます。 マイ アプリで [ADP Globalview (非推奨)] タイルをクリックすると、SSO を設定した ADP Globalview (非推奨) に自動的にサインインします。 マイ アプリの詳細については、[マイ アプリの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関するページを参照してください。


## <a name="next-steps"></a>次のステップ

ADP Globalview (非推奨) を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)をご覧ください。