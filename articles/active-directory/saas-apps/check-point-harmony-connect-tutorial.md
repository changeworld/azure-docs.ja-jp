---
title: 'チュートリアル: Azure Active Directory シングル サインオン (SSO) と Check Point Harmony Connect の統合 | Microsoft Docs'
description: Azure Active Directory と Check Point Harmony Connect の間にシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/04/2021
ms.author: jeedes
ms.openlocfilehash: b7ba48cb534ebd0ec2df6038b0b5df49ce2b0e88
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132311906"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-check-point-harmony-connect"></a>チュートリアル: Azure Active Directory シングル サインオン (SSO) と Check Point Harmony Connect の統合

このチュートリアルでは、Check Point Harmony Connect と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Check Point Harmony Connect を統合すると、次のことができます。

* Check Point Harmony Connect にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Check Point Harmony Connect に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Check Point Harmony Connect でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Check Point Harmony Connect では、**SP** Initiated SSO がサポートされます。
> [!NOTE]
> このアプリケーションの識別子は固定文字列値であるため、1 つのテナントで構成できるインスタンスは 1 つだけです。


## <a name="adding-check-point-harmony-connect-from-the-gallery"></a>ギャラリーからの Check Point Harmony Connect の追加

Azure AD への Check Point Harmony Connect の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Check Point Harmony Connect を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Check Point Harmony Connect**」と入力します。
1. 結果のパネルから **[Check Point Harmony Connect]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。


## <a name="configure-and-test-azure-ad-sso-for-check-point-harmony-connect"></a>Check Point Harmony Connect の Azure AD SSO の構成とテスト

**B. Simon** というテスト ユーザーを使用して、Check Point Harmony Connect に対する Azure AD SSO を構成してテストします。 SSO を機能させるためには、Azure AD ユーザーと Check Point Harmony Connect の関連ユーザーとの間にリンク関係を確立する必要があります。

Check Point Harmony Connect に対して Azure AD SSO を構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Check Point Harmony Connect の SSO の構成](#configure-check-point-harmony-connect-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Check Point Harmony Connect のテスト ユーザーの作成](#create-check-point-harmony-connect-test-user)** - Check Point Harmony Connect で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Check Point Harmony Connect** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次のフィールドの値を入力します。

    **[サインオン URL]** ボックスに、URL として「`https://cloudinfra-gw.portal.checkpoint.com/api/saml/sso`」を入力します。

1. Check Point Harmony Connect アプリケーションでは、特定の形式の SAML アサーションを使用するため、カスタム属性マッピングをご自分の SAML トークンの属性の構成に追加する必要があります。 次のスクリーンショットには、既定の属性一覧が示されています。

    ![image](common/default-attributes.png)

1. 上記に加えて、Check Point Harmony Connect では、いくつかの追加の属性が SAML 応答で返されることも想定されています。それらの属性を次に示します。 これらの属性も値が事前に設定されますが、要件に従ってそれらの値を確認することができます。
    
    | 名前 |  ソース属性|
    | ---------------- | --------- |
    | groups | user.groups |

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[フェデレーション メタデータ XML]** を探して **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

1. **[Check Point Harmony Connect のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に Check Point Harmony Connect へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Check Point Harmony Connect]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-check-point-harmony-connect-sso"></a>Check Point Harmony Connect SSO の構成

1. Check Point Harmony Connect の Web サイトに管理者としてログインします。

1. **[SETTINGS]\(設定\)** をクリックしてから **[Identity Provider]\(ID プロバイダー\)** に移動し、 **[CONNECT NOW]\(今すぐ接続\)** をクリックします。

    ![ID プロバイダーのスクリーンショット。](./media/check-point-harmony-connect-tutorial/identity-provider.png)

1. ID プロバイダーとして **[Microsoft Azure AD]** を選択し、 **[NEXT]\(次へ\)** をクリックします。

    ![ID プロバイダーの選択のスクリーンショット。](./media/check-point-harmony-connect-tutorial/select-identity-provider.png)

1. **[Verify Domain]\(ドメインの確認\)** ページで、自分の組織のドメインを入力し、生成された DNS レコードを TXT レコードとして DNS サーバーに入力して、 **[NEXT]\(次へ\)** をクリックします。

    ![ドメインの値のスクリーンショット。](./media/check-point-harmony-connect-tutorial/domain.png)

1. [Allow Connectivity]\(接続の許可\) ページで、次の手順を実行します。

    ![[Allow Connectivity]\(接続の許可\) ページのスクリーンショット。](./media/check-point-harmony-connect-tutorial/allow-connectivity.png)

    a. **[ENTITY ID]\(エンティティ ID\)** の値をコピーし、その値を Azure portal の **[基本的な SAML 構成]** セクションの **[識別子]** ボックスに貼り付けます。

    b. **[REPLY URL]\(応答 URL\)** の値をコピーし、Azure portal の **[基本的な SAML 構成]** セクションの **[応答 URL]** テキスト ボックスにこの値を貼り付けます。

    c. **[NEXT]\(次へ\)** をクリックします。

1. **[Configure Metadata]\(メタデータの構成\)** ページで、Azure portal からダウンロードした **フェデレーション メタデータ XML** をアップロードします。

1. **[CONFIRM IDENTITY PROVIDER]\(ID プロバイダーの確認\)** ページで、 **[Add]\(追加\)** をクリックして構成を完了します。

### <a name="create-check-point-harmony-connect-test-user"></a>Check Point Harmony Connect のテスト ユーザーの作成

1. Check Point Harmony Connect の Web サイトに管理者としてログインします。

1. **[Policy]\(ポリシー\)**  ->  **[Access Control]\(アクセス制御\)** の順に移動して **新しい規則** を作成し、 **[+]** をクリックして **[New User]\(新しいユーザー\)** を追加します。 

    ![新しいユーザーの作成のスクリーンショット。](./media/check-point-harmony-connect-tutorial/add-new-user.png)

1. **[ADD USER]\(ユーザーの追加\)** ウィンドウで、名前とユーザー名をそれぞれのテキスト ボックスに入力し、 **[ADD]\(追加\)** をクリックします。

    ![ユーザーの作成のスクリーンショット。](./media/check-point-harmony-connect-tutorial/add-user.png)

## <a name="test-sso"></a>SSO のテスト 

Check Point Harmony Connect をテストするには、その認証サービスに移動し、「**Azure AD のテスト ユーザーの作成**」セクションで作成したテスト アカウントを使用して認証を行います。

## <a name="next-steps"></a>次の手順

Check Point Harmony Connect を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。
