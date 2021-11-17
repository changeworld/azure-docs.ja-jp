---
title: 'チュートリアル: Azure Active Directory シングル サインオン (SSO) と Tulip の統合 | Microsoft Docs'
description: Azure Active Directory と Tulip の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/30/2021
ms.author: jeedes
ms.openlocfilehash: 78922b738e470084c0218be829a02d57b85527e7
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132332803"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-tulip"></a>チュートリアル: Azure Active Directory シングル サインオン (SSO) と Tulip の統合

このチュートリアルでは、Tulip と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Tulip を統合すると、次のことが可能になります。

* Tulip にアクセスできるユーザーを Azure AD で制御する。
* ユーザーが自分の Azure AD アカウントを使用して Tulip に自動的にサインインできるようにする。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Tulip でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。


* Tulip では、**IDP** によって開始される SSO がサポートされます。

## <a name="adding-tulip-from-the-gallery"></a>ギャラリーからの Tulip の追加

Azure AD への Tulip の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Tulip を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Tulip**」と入力します。
1. 結果のパネルから **[Tulip]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。


## <a name="configure-and-test-azure-ad-sso-for-tulip"></a>Tulip の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Tulip に対する Azure AD SSO を構成してテストします。 SSO を機能させるためには、Azure AD ユーザーと Tulip の関連ユーザーとの間にリンク関係を確立する必要があります。

Tulip に対して Azure AD SSO を構成してテストするには、次のステップを実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Tulip SSO の構成](#configure-tulip-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Tulip のテスト ユーザーの作成](#create-tulip-test-user)** - Tulip で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Tulip** アプリケーション統合ページで、 **[管理]** セクションを探して、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、**サービス プロバイダー メタデータ ファイル** がある場合は、次の手順に従います。

    a. **[メタデータ ファイルをアップロードします]** をクリックします。

    ![image1](common/upload-metadata.png)

    b. **フォルダー ロゴ** をクリックしてメタデータ ファイルを選択し、 **[アップロード]** をクリックします。

    ![Image2](common/browse-upload-metadata.png)

    c. メタデータ ファイルが正常にアップロードされると、**識別子** と **応答 URL** の値が、[基本的な SAML 構成] セクションに自動的に設定されます。

    ![image3](common/idp-intiated.png)

    > [!Note]
    > **識別子** と **返信 URL** の値が自動的に設定されない場合は、要件に応じて手動で値を入力してください。

1. Tulip アプリケーションは、特定の形式の SAML アサーションを使用するため、カスタム属性のマッピングを SAML トークンの属性の構成に追加する必要があります。 次のスクリーンショットには、既定の属性一覧が示されています。

    ![image](common/default-attributes.png)

1. 上記に加えて、Tulip では、いくつかの追加の属性が SAML 応答で返されることも想定されています。それらを次に示します。 これらの属性も値が事前に設定されますが、要件に従ってそれらの値を確認することができます。
    
    | 名前 |ソース属性|
    | -------------- | --------- |
    | displayName | user.displayname |
    | emailAddress |User.mail |
    | badgeID | user.employeeid |
    | groups |user.groups |


1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** を見つけて、 **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

1. **[Tulip のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に Tulip へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Tulip]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-tulip-sso"></a>Tulip SSO を構成する

1. Tulip の Web サイトに管理者としてログインします。

1. **[設定]**  ->  **[SAML]** に移動し、下のページの以下のステップを実行します。

    ![Tulip の構成のスクリーンショット。](./media/tulip-tutorial/configuration.png)

    a。 **SAML ログインを有効にします**。 

    b. **[メタデータ xml ファイル]** をクリックして **サービス プロバイダー メタデータ ファイル** をダウンロードし、このファイルを使用して Azure portal の **[基本 SAML 構成]** セクションにアップロードします。

    c. **[SSO ログイン URL]** テキストボックスに、Azure portal からコピーした **ログイン URL** の値を貼り付けます。

    d. **[SSO ログアウト URL]** テキストボックスに、Azure portal からコピーした **ログアウト URL** の値を貼り付けます。

    e. Azure portal からダウンロードした **証明書 (Base64)** をメモ帳で開き、その内容を **[証明書]** テキストボックスに貼り付けます。

    f. **[属性マッピング]** で、
    
     * **[Name 属性]** の値に **displayName** を指定します。

     * **[Email 属性]** の値に **emailAdress** を指定します。

     * **[Badge 属性]** の値に **badgeID** を指定します。

     * **[Role 属性]** の値に **groups** を指定します。

    g. **[ロール マッピング]** で、グループの ObjectID をアカウント オーナーにマップします。

    h. **[Save SAML Configuration]\(SAML 構成の保存\)** をクリックします。

### <a name="create-tulip-test-user"></a>Tulip テスト ユーザーを作成する

このセクションでは、Tulip で Britta Simon というユーザーを作成します。 [Tulip サポート チーム](mailto:support@tulip.co)と連携し、Tulip プラットフォームにユーザーを追加してください。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。

* Azure portal で [このアプリケーションをテストします] をクリックすると、SSO を設定した Tulip に自動的にサインインされます

* Microsoft マイ アプリを使用することができます。 マイ アプリで [Tulip] タイルをクリックすると、SSO を設定した Tulip に自動的にサインインされます。 マイ アプリの詳細については、[マイ アプリの概要](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Tulip を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を適用する方法をご覧ください](/cloud-app-security/proxy-deployment-any-app)。
