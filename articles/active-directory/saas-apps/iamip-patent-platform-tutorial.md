---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と IamIP Patent Platform の統合 | Microsoft Docs
description: Azure Active Directory と IamIP Patent Platform の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/28/2021
ms.author: jeedes
ms.openlocfilehash: 5de5587e7f517d28207bf041b655591736f84a57
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124833150"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-iamip-patent-platform"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と IamIP Patent Platform の統合

このチュートリアルでは、IamIP Patent Platform と Azure Active Directory (Azure AD) を統合する方法について説明します。 IamIP Patent Platform と Azure AD を統合すると、次のことができます。

* IamIP Patent Platform にアクセスできるユーザーを Azure AD で制御する。
* ユーザーが自分の Azure AD アカウントを使用して IamIP Patent Platform に自動的にサインインできるようにする。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* シングル サインオン (SSO) が有効な IamIP Patent Platform サブスクリプション。

## <a name="tutorial-description"></a>チュートリアルの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* IamIP Patent Platform では、SP-Initiated SSO と IDP-Initiated SSO がサポートされます。

## <a name="add-iamip-patent-platform-from-the-gallery"></a>ギャラリーからの IamIP Patent Platform の追加

Azure AD への IamIP Patent Platform の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に IamIP Patent Platform を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左ウィンドウで、 **[Azure Active Directory]** を選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに、「**IamIP Patent Platform**」と入力します。
1. 結果のパネルから **[IamIP Patent Platform]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-iamip-patent-platform"></a>IamIP Patent Platform の Azure AD SSO の構成とテスト

B.Simon というテスト ユーザーを使用して、IamIP Patent Platform に対する Azure AD SSO を構成してテストします。 SSO を機能させるために、Azure AD ユーザーとそれに対応する IamIP Patent Platform 内のユーザーとの間にリンク関係を確立する必要があります。

IamIP Patent Platform 用に Azure AD SSO を構成してテストするには、次の手順を行います。

1. **[Azure AD SSO を構成](#configure-azure-ad-sso)** して、ユーザーがこの機能を使用できるようにします。
    1. Azure AD のシングル サインオンをテストするための **[Azure AD テスト ユーザーを作成](#create-an-azure-ad-test-user)** します。
    1. **[テスト ユーザーにアクセス権を付与](#grant-access-to-the-test-user)** して、Azure AD シングル サインオンをユーザーが使用できるようにします。

1. アプリケーション側で **[IamIP Patent Platform SSO を構成](#configure-iamip-patent-platform-sso)** します。
    1. Azure AD のユーザーに対応するユーザーとして、 **[IamIP Patent Platform のテスト ユーザーを作成](#create-iamip-patent-platform-test-user)** します。

1. **[SSO をテスト](#test-sso)** して、構成が正しく機能することを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **IamIP Patent Platform** アプリケーション統合ページの **[管理]** セクションで、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の鉛筆ボタンを選択して設定を編集します。

   ![基本的な SAML 構成の鉛筆ボタン](common/edit-urls.png)

1. サービス プロバイダー メタデータ ファイルがあり、なおかつ IDP-Initiated モードで SSO を構成したい場合は、 **[基本的な SAML 構成]** セクションで次の手順を実行します。

    a. **[メタデータ ファイルをアップロードする]** を選択します。

    ![メタデータ ファイルをアップロードする](common/upload-metadata.png)

    b. フォルダー ボタンを選択し、メタデータ ファイルを選択して、 **[アップロード]** を選択します。

    ![フォルダー ボタンとアップロード ボタン](common/browse-upload-metadata.png)

    c. メタデータ ファイルがアップロードされると、 **[基本的な SAML 構成]** セクションの **[識別子]** と **[応答 URL]** の値が自動的に設定されます。

    > [!Note]
    > **[識別子]** と **[応答 URL]** の値が自動的に設定されない場合は、要件に応じて手動で値を入力してください。

1. SP-Initiated モードでアプリケーションを構成したい場合は、 **[追加の URL を設定します]** を選択し、次の手順を実行します。

    **[サインオン URL]** ボックスに、URL として「`https://patents.iamip.com/login-user`」を入力します。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (未加工)]** の **[ダウンロード]** リンクを選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロード リンク](common/certificateraw.png)

1. **[IamIP Patent Platform のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

    ![構成 URL のコピー](common/idp-intiated.png))

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションでは、Azure portal で B. Simon というテスト ユーザーを作成します。

1. Azure portal の左ペインで、 **[Azure Active Directory]** を選択します。 **[ユーザー]** を選択し、 **[すべてのユーザー]** を選択します。
1. 画面の上部にある **[新しいユーザー]** を選択します。
1. **[ユーザー]** プロパティで、次の手順を実行します。
   1. **[名前]** ボックスに「**B.Simon**」と入力します。  
   1. **[ユーザー名]** ボックスに「\<username>@\<companydomain>.\<extension>」と入力します。 たとえば、「 `B.Simon@contoso.com` 」のように入力します。
   1. **[パスワードを表示]** をオンにし、 **[パスワード]** ボックスに表示された値を書き留めます。
   1. **［作成］** を選択します

### <a name="grant-access-to-the-test-user"></a>テスト ユーザーへのアクセス権の付与

このセクションでは、B.Simon に IamIP Patent Platform へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[IamIP Patent Platform]** を選択します。
1. アプリの概要ページの **[管理]** セクションで、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログ ボックスで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログ ボックスで、**ユーザー** の一覧で **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーションにロール値が必要な場合は、 **[ロールの選択]** ダイアログ ボックスでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログ ボックスで、 **[割り当て]** ボタンを選択します。

## <a name="configure-iamip-patent-platform-sso"></a>IamIP Patent Platform SSO の構成

IamIP Patent Platform 側でシングル サインオンを構成するには、ダウンロードした未加工の証明書と Azure portal からコピーした適切な URL を [IamIP Patent Platform サポート チーム](mailto:info@iamip.com)に送信する必要があります。 SAML SSO 接続が両側で正しく構成されます。

### <a name="create-iamip-patent-platform-test-user"></a>IamIP Patent Platform のテスト ユーザーの作成

[IamIP Patent Platform サポート チーム](mailto:info@iamip.com)と連携し、B.Simon という名前のユーザーを IamIP Patent Platform に追加します。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

#### <a name="sp-initiated"></a>SP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる IamIP Patent Platform のサインオン URL にリダイレクトされます。  

* IamIP Patent Platform のサインオン URL に直接移動し、そこからログイン フローを開始します。

#### <a name="idp-initiated"></a>IDP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックすると、SSO を設定した IamIP Patent Platform に自動的にサインインされます。 

また、Microsoft マイ アプリを使用して、任意のモードでアプリケーションをテストすることもできます。 マイ アプリで [IamIP Patent Platform] タイルをクリックすると、SP モードで構成されている場合は、ログイン フローを開始するためのアプリケーション サインオン ページにリダイレクトされます。IDP モードで構成されている場合は、SSO を設定した IamIP Patent Platform に自動的にサインインされます。 マイ アプリの詳細については、[マイ アプリの概要](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

IamIP Patent Platform を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-aad)をご覧ください。