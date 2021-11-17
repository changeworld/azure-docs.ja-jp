---
title: 'チュートリアル: Azure AD SSO と ContractSafe Saml2 SSO の統合'
description: Azure Active Directory と ContractSafe Saml2 SSO の間のシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/18/2021
ms.author: jeedes
ms.openlocfilehash: 15f57b89e024fda9bbdc9af320c09e9cc370cce5
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131477461"
---
# <a name="tutorial-integrate-azure-ad-sso-with-contractsafe-saml2-sso"></a>チュートリアル: Azure AD SSO と ContractSafe Saml2 SSO を統合する

このチュートリアルでは、ContractSafe Saml2 SSO と Azure Active Directory (Azure AD) を統合する方法について説明します。 ContractSafe Saml2 SSO と Azure AD を統合すると、次のことができます。

* ContractSafe Saml2 SSO にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して ContractSafe Saml2 SSO に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

## <a name="prerequisites"></a>前提条件

開始するには、以下が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* SSO が有効な ContractSafe Saml2 SSO サブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* ContractSafe Saml2 SSO では、**IDP** Initiated SSO がサポートされます。

## <a name="add-contractsafe-saml2-sso-from-the-gallery"></a>ギャラリーからの ContractSafe Saml2 SSO の追加

Azure AD への ContractSafe Saml2 SSO の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に ContractSafe Saml2 SSO を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**ContractSafe Saml2 SSO**」と入力します。
1. 結果のパネルから **ContractSafe Saml2 SSO** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-contractsafe-saml2-sso"></a>ContractSafe Saml2 SSO の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、ContractSafe Saml2 SSO に対する Azure AD SSO を構成してテストします。 SSO を機能させるために、Azure AD ユーザーと ContractSafe Saml2 SSO の関連ユーザーの間で、リンク関係を確立する必要があります。

ContractSafe Saml2 SSO に対して Azure AD SSO を構成してテストするには、次の手順を実行します。

1. [Azure AD SSO を構成](#configure-azure-ad-sso)して、ユーザーがこの機能を使用できるようにします。
   1. [Azure AD のテスト ユーザーを作成](#create-an-azure-ad-test-user)し、**B.Simon** のアカウントを使用して Azure AD SSO をテストします。
   1. [Azure AD テスト ユーザーを割り当て](#assign-the-azure-ad-test-user)て、**B.Simon** が Azure AD SSO を使用できるようにします。
1. [ContractSafe Saml2 SSO の構成](#configure-contractsafe-saml2-sso) - アプリケーション側で SSO 設定を構成します。
   1. [ContractSafe Saml2 SSO のテスト ユーザーの作成](#create-a-contractsafe-saml2-sso-test-user) - ContractSafe Saml2 SSO で **B.Simon** に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. [SSO をテスト](#test-sso)して、構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **ContractSafe Saml2 SSO** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンを選択して設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

   a. **[識別子]** ボックスに、次の形式で URL を入力します。`https://app.contractsafe.com/saml2_auth/<UNIQUEID>/acs/`

   b. **[応答 URL]** ボックスに、次の形式で URL を入力します。`https://app.contractsafe.com/saml2_auth/<UNIQUEID>/acs/`

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子と応答 URL でこれらの値を更新します。 この値を取得するには、[ContractSafe Saml2 SSO クライアント サポート チーム](mailto:support@contractsafe.com)にお問い合わせください。 また、Azure portal の **[基本的な SAML 構成]** セクションに示されている形式を参照することもできます。

1. ContractSafe Saml2 SSO では、特定の形式の SAML アサーションを使用するため、カスタム属性マッピングを SAML トークン属性の構成に追加する必要があります。 次のスクリーンショットには、既定の属性一覧が示されています。

    ![一般的な既定の属性](common/default-attributes.png)

1. 既定の属性に加えて、ContractSafe Saml2 SSO アプリケーションでは、その他にいくつかの属性が SAML 応答で返されることが想定されています。 これらの属性は値が事前に設定されますが、要件に従ってそれらの値を確認することができます。 以下に示したのは、追加の属性の一覧です。

    | 名前 | ソース属性|
    | ---------------| --------------- |
    | emailname | user.userprincipalname |
    | email | user.onpremisesuserprincipalname |

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[フェデレーション メタデータ XML]** を見つけます。 **[ダウンロード]** を選択して証明書をダウンロードし、コンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

1. **[ContractSafe Saml2 SSO のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションでは、Azure portal 内で **B.Simon** というテスト ユーザーを作成します。

1. Azure portal の左ペインで、**[Azure Active Directory]** を選択します。 **[ユーザー]** を選択し、 **[すべてのユーザー]** を選択します。
1. 画面の上部にある **[新しいユーザー]** を選択します。
1. **[ユーザー]** プロパティで、以下の手順を実行します。
   1. **[名前]** フィールドに「`B.Simon`」と入力します。  
   1. **[ユーザー名]** フィールドに、`username@companydomain.extension` 形式でメール アドレスを入力します。 たとえば `B.Simon@contoso.com` です。
   1. **[パスワードを表示]** チェック ボックスをオンにし、 **[パスワード]** ボックスに表示された値を書き留めます。
   1. **［作成］** を選択します

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、**B.Simon** に ContractSafe Saml2 SSO へのアクセスを許可することで、このユーザーが Azure SSO を使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[ContractSafe Saml2 SSO]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログ ボックスで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログ ボックスで、 **[ユーザー]** 一覧から **[B.Simon]** を選択します。 その後、画面の下部にある **[選択]** ボタンを選択します。
1. SAML アサーション内にロール値が必要な場合、 **[ロールの選択]** ダイアログ ボックスで、一覧からユーザーに適したロールを選択します。 次に、画面の下部にある **[選択]** ボタンを選択します。
1. **[割り当ての追加]** ダイアログ ボックスで、 **[割り当て]** ボタンを選択します。

## <a name="configure-contractsafe-saml2-sso"></a>ContractSafe Saml2 SSO の構成

**ContractSafe Saml2 SSO** 側で SSO を構成するには、ダウンロードした **フェデレーション メタデータ XML** と Azure portal からコピーした適切な URL を [ContractSafe Saml2 SSO サポート チーム](mailto:support@contractsafe.com)に送信する必要があります。 サポート チームの下で、SAML SSO 接続が両方の側で適切に設定されます。

### <a name="create-a-contractsafe-saml2-sso-test-user"></a>ContractSafe Saml2 SSO のテスト ユーザーの作成

ContractSafe Saml2 SSO で B.Simon というユーザーを作成します。 [ContractSafe Saml2 SSO サポートチーム](mailto:support@contractsafe.com)と連携して、ContractSafe Saml2 SSO プラットフォームにユーザーを追加します。 SSO を使用する前に、ユーザーを作成し、有効化する必要があります。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。

* Azure portal で [このアプリケーションをテストします] をクリックすると、SSO を設定した ContractSafe Saml2 SSO に自動的にサインインします。

* Microsoft マイ アプリを使用することができます。 マイ アプリ上で [ContractSafe Saml2 SSO] タイルをクリックすると、SSO を設定した ContractSafe Saml2 SSO に自動的にサインインします。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

ContractSafe Saml2 SSO を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-aad)をご覧ください。