---
title: 'チュートリアル: Azure Active Directory と Vtiger CRM (SAML) の統合 | Microsoft Docs'
description: Azure Active Directory と Vtiger CRM (SAML) の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/31/2021
ms.author: jeedes
ms.openlocfilehash: b108bbd77676c172f97b1ec224e1380af6f5f030
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132298492"
---
# <a name="tutorial-integrate-vtiger-crm-saml-with-azure-active-directory"></a>チュートリアル: Vtiger CRM (SAML) と Azure Active Directory の統合

このチュートリアルでは、Vtiger CRM (SAML) と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Vtiger CRM (SAML) を統合すると、次のことができます。

* Vtiger CRM (SAML) にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Vtiger CRM (SAML) に自動的にサインインできるようになります。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Vtiger CRM (SAML) でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。 

* Vtiger CRM (SAML) では、**SP** Initiated SSO がサポートされます。
* Vtiger CRM (SAML) では、**Just-In-Time** ユーザー プロビジョニングがサポートされます。

## <a name="add-vtiger-crm-saml-from-the-gallery"></a>ギャラリーからの Vtiger CRM (SAML) の追加

Azure AD への Vtiger CRM (SAML) の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Vtiger CRM (SAML) を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Vtiger CRM (SAML)**」と入力します。
1. 結果のパネルから **[Vtiger CRM (SAML)]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-vtiger-crm-saml"></a>Vtiger CRM (SAML) の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Vtiger CRM (SAML) に対する Azure AD SSO を構成してテストします。 SSO を機能させるためには、Azure AD ユーザーと Vtiger CRM (SAML) の関連ユーザーとの間にリンク関係を確立する必要があります。

Vtiger CRM (SAML) に対する Azure AD SSO を構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Vtiger CRM (SAML) SSO の構成](#configure-vtiger-crm-saml-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Vtiger CRM (SAML) テスト ユーザーの作成](#create-vtiger-crm-saml-test-user)** - Vtiger CRM (SAML) で B.Simon に対応するユーザーを作成し、Azure AD のこのユーザーにリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Vtiger CRM (SAML)** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** ページで、次の手順を実行します。

   a. **[識別子 (エンティティ ID)]** ボックスに、次のパターンを使用して URL を入力します。`https://<CUSTOMER_INSTANCE>.od1.vtiger.com/sso/saml?acs`

    b. **[サインオン URL]** テキスト ボックスに、次のいずれかのパターンを使用して URL を入力します。

   | サインオン URL |
   |---|
   |`https://<CUSTOMER_INSTANCE>.od1.vtiger.com`|
   |`https://<CUSTOMER_INSTANCE>.od2.vtiger.com`|
   |`https://<CUSTOMER_INSTANCE>.od1.vtiger.ws`|
   |

    > [!NOTE]
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新します。 これらの値を取得するには、[Vtiger CRM (SAML) Client サポート チーム](mailto:support@vtiger.com)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** を見つけて、 **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

   ![証明書のダウンロードのリンク](common/certificatebase64.png)

1. **[Set up Vtiger CRM (SAML)]\(Vtiger CRM (SAML) のセットアップ\)** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に Vtiger CRM (SAML) へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧から **[Vtiger CRM (SAML)]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-vtiger-crm-saml-sso"></a>Vtiger CRM (SAML) の SSO の構成

**Vtiger CRM (SAML)** 側でシングル サインオンを構成するには、ダウンロードした **証明書 (Base64)** と Azure portal からコピーした適切な URL を [Vtiger CRM (SAML) サポート チーム](mailto:support@vtiger.com)に送信する必要があります。 サポート チームはこれを設定して、SAML SSO 接続が両方の側で正しく設定されるようにします。

### <a name="create-vtiger-crm-saml-test-user"></a>Vtiger CRM (SAML) テスト ユーザーの作成

このセクションでは、Britta Simon というユーザーを Vtiger CRM (SAML) に作成します。 Vtiger CRM (SAML) では、Just-In-Time ユーザー プロビジョニングがサポートされており、既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 Vtiger CRM (SAML) にユーザーがまだ存在していない場合は、認証後に新しく作成されます。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Vtiger CRM (SAML) のサインオン URL にリダイレクトされます。 

* Vtiger CRM (SAML) のサインオン URL に直接移動し、そこからログイン フローを開始します。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [Vtiger CRM (SAML)] タイルをクリックすると、Vtiger CRM (SAML) のサインオン URL にリダイレクトされます。 マイ アプリの詳細については、[マイ アプリの概要](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Vtiger CRM (SAML) を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を適用する方法をご覧ください](/cloud-app-security/proxy-deployment-aad)。
